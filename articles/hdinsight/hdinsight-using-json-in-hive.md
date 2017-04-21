<properties
   pageTitle="Analizar y proceso JSON en documentos con subárbol en HDInsight | Microsoft Azure"
   description="Obtenga información sobre cómo usar documentos JSON y analizarlos con subárbol en HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="rashimg"
   manager="mwinkle"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="06/22/2015"
   ms.author="rashimg"/>


# <a name="process-and-analyze-json-documents-using-hive-in-hdinsight"></a>Procesar y analizar documentos JSON con subárbol en HDInsight

Aprenda a procesar y analizar los archivos JSON con subárbol en HDInsight. El siguiente documento JSON se utilizará en el tutorial

    {
        "StudentId": "trgfg-5454-fdfdg-4346",
        "Grade": 7,
        "StudentDetails": [
            {
                "FirstName": "Peggy",
                "LastName": "Williams",
                "YearJoined": 2012
            }
        ],
        "StudentClassCollection": [
            {
                "ClassId": "89084343",
                "ClassParticipation": "Satisfied",
                "ClassParticipationRank": "High",
                "Score": 93,
                "PerformedActivity": false
            },
            {
                "ClassId": "78547522",
                "ClassParticipation": "NotSatisfied",
                "ClassParticipationRank": "None",
                "Score": 74,
                "PerformedActivity": false
            },
            {
                "ClassId": "78675563",
                "ClassParticipation": "Satisfied",
                "ClassParticipationRank": "Low",
                "Score": 83,
                "PerformedActivity": true
            }
        ]
    }

El archivo se encuentra en wasbs://processjson@hditutorialdata.blob.core.windows.net/. Para obtener más información sobre el uso de almacenamiento de blobs de Windows Azure con HDInsight, consulte [el almacenamiento de blobs de Azure de uso HDFS compatible con Hadoop en HDInsight](hdinsight-hadoop-use-blob-storage.md). Puede copiar el archivo en el contenedor predeterminado del clúster si lo desea.

En este tutorial, usará la consola de sección.  Para obtener instrucciones de abrir la consola de sección, vea [Usar subárbol con Hadoop en HDInsight con Escritorio remoto](hdinsight-hadoop-use-hive-remote-desktop.md).

##<a name="flatten-json-documents"></a>Acoplar documentos JSON

Los métodos enumerados en la siguiente sección requieren el documento JSON en una sola fila. Por lo tanto, debe acoplar el documento JSON en una cadena. Si ya se acopla el documento JSON, puede omitir este paso e ir directamente a la siguiente sección de datos JSON analizar.

    DROP TABLE IF EXISTS StudentsRaw;
    CREATE EXTERNAL TABLE StudentsRaw (textcol string) STORED AS TEXTFILE LOCATION "wasb://processjson@hditutorialdata.blob.core.windows.net/";

    DROP TABLE IF EXISTS StudentsOneLine;
    CREATE EXTERNAL TABLE StudentsOneLine
    (
      json_body string
    )
    STORED AS TEXTFILE LOCATION '/json/students';

    INSERT OVERWRITE TABLE StudentsOneLine
    SELECT CONCAT_WS(' ',COLLECT_LIST(textcol)) AS singlelineJSON
          FROM (SELECT INPUT__FILE__NAME,BLOCK__OFFSET__INSIDE__FILE, textcol FROM StudentsRaw DISTRIBUTE BY INPUT__FILE__NAME SORT BY BLOCK__OFFSET__INSIDE__FILE) x
          GROUP BY INPUT__FILE__NAME;

    SELECT * FROM StudentsOneLine

El archivo JSON sin formato se encuentra en **wasbs://processjson@hditutorialdata.blob.core.windows.net/**. La tabla subárbol *StudentsRaw* puntos al documento JSON no plana sin formato.

La tabla subárbol *StudentsOneLine* almacenará los datos en el sistema de archivos de HDInsight de forma predeterminada en la ruta de acceso */json/estudiantes /* .

La instrucción INSERT rellenar la tabla de StudentOneLine con los datos JSON planas.

La instrucción SELECT sólo devolverá la 1 fila.

Éste es el resultado de la instrucción SELECT:

![Acoplar del documento JSON.][image-hdi-hivejson-flatten]

##<a name="analyze-json-documents-in-hive"></a>Analizar JSON documentos en la sección

Sección ofrece tres mecanismos diferentes para ejecutar consultas en documentos JSON:

- Use el\_JSON\_objeto UDF (función definida por el usuario)
- usar la UDF JSON_TUPLE
- usar SerDe personalizado
- escribir que propietario UDF con Python o en otros idiomas. Consulte [este artículo] [ hdinsight-python] ejecución de su propio código Python con subárbol.

### <a name="use-the-getjsonobject-udf"></a>Use el\_JSON_OBJECT UDF
Sección proporciona que una UDF integrada denominada [obtener objeto json](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) que puede llevar a cabo JSON consultas durante el tiempo de ejecución. Este método acepta dos argumentos: el nombre de tabla y método que tiene el documento JSON plana y el campo JSON que necesita analizar. Veamos un ejemplo para ver cómo funciona este UDF.

Obtener el nombre y apellido de cada estudiante

    SELECT
      GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'),
      GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName')
    FROM StudentsOneLine;

Éste es el resultado cuando se ejecuta esta consulta en la ventana de la consola.

![get_json_object UDF][image-hdi-hivejson-getjsonobject]

Existen algunas limitaciones de la UDF get-json_object.

- Dado que cada campo de la consulta requiere un análisis de volver a la consulta, afecta al rendimiento.
- OBTENER\_JSON_OBJECT() devuelve la representación de cadena de una matriz. Para convertir una matriz de sección, debe usar expresiones regulares para reemplazar los corchetes ' [' y ']' y, a continuación, llame a también dividido para obtener la matriz.


Esta es la razón por la wiki de subárbol recomienda usar json_tuple.  

### <a name="use-the-jsontuple-udf"></a>Usar la UDF JSON_TUPLE

Otro UDF proporcionada por subárbol se denomina [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple) que funciona mejor que [get_ json _object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object). Este método toma un conjunto de claves y una cadena JSON y devuelve una tupla de valores con una función. La siguiente consulta devuelve el identificador de estudiante y la calidad del documento JSON:

    SELECT q1.StudentId, q1.Grade
      FROM StudentsOneLine jt
      LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
        AS StudentId, Grade;

El resultado de esta secuencia de comandos en la consola de sección:

![json_tuple UDF][image-hdi-hivejson-jsontuple]

JSON\_TUPLA usa la sintaxis de la [vista lateral](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) en la sección que permite json\_tupla para crear una tabla virtual mediante la aplicación de la función definido para cada fila de la tabla original.  Se vuelven difíciles de demasiado complejas JSONs por el uso de vista LATERAL repetido. Además, JSON_TUPLE no puede controlar JSONs anidadas.


###<a name="use-custom-serde"></a>Usar SerDe personalizado

SerDe es la mejor opción para analizar documentos JSON anidados, le permite definir el esquema JSON y utilizar el esquema para analizar los documentos. En este tutorial, usará uno de lo SerDe más popular que se ha desarrollado por [rcongiu](https://github.com/rcongiu).

**Para usar el SerDe personalizado:**

1. Instalar el [Kit de desarrollo de Java SE 7u55 JDK 1.7.0_55](http://www.oracle.com/technetwork/java/javase/downloads/java-archive-downloads-javase7-521261.html#jdk-7u55-oth-JPR). Elegir la versión de Windows X64 de la JDK si va a utilizar la implementación de HDInsight de Windows

    >[AZURE.WARNING] 1,8 JDK no funciona con esta SerDe.

    Una vez completada la instalación, agregue una nueva variable de entorno de usuario:

    1. Abrir **vista Configuración avanzada del sistema** desde la pantalla de Windows.
    2. Haga clic en **Variables de entorno**.  
    3. Agregar que una nueva variable de entorno **JAVA_HOME** es apuntando a **C:\Program Files\Java\jdk1.7.0_55** o donde está instalado el JDK.

    ![Configurar los valores de configuración correctos para JDK][image-hdi-hivejson-jdk]

2. Instalar [experto 3.3.1](http://mirror.olnevhost.net/pub/apache/maven/maven-3/3.3.1/binaries/apache-maven-3.3.1-bin.zip)

    Agregar la carpeta bin a la ruta, vaya a Control Panel--> Editar las Variables del sistema para las variables de Environment de cuenta. Captura de pantalla siguiente muestra cómo hacerlo.

    ![Configurar experto][image-hdi-hivejson-maven]

3. Duplicar el proyecto desde el sitio de github [SerDe JSON de sección](https://github.com/sheetaldolas/Hive-JSON-Serde/tree/master) . Puede hacerlo haciendo clic en el botón "Descargar" Zip", como se muestra en la siguiente captura de pantalla.

    ![Clonar el proyecto][image-hdi-hivejson-serde]

4: vaya a la carpeta donde ha descargado este y paquete de tipo "mvn". Esto debe crear los archivos jar es necesario que se puede copiar al clúster.

5: vaya a la carpeta de destino en la carpeta raíz que descargó el paquete. Cargar el archivo json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar en el nodo principal del clúster. Poner generalmente en la carpeta binaria del subárbol: C:\apps\dist\hive-0.13.0.2.1.11.0-2316\bin o algo similar.

6: en el símbolo de la sección, escriba "Agregar jar /path/to/json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar". Como en mi caso, el tarro está en la carpeta C:\apps\dist\hive-0.13.x\bin, puedo agregar directamente el tarro con el nombre tal y como se muestra a continuación:

    add jar json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar;

    ![Adding JAR to your project][image-hdi-hivejson-addjar]

Ahora está listo para usar el SerDe para ejecutar consultas en el documento JSON.

La instrucción siguiente crea una tabla con un esquema definido

    DROP TABLE json_table;
    CREATE EXTERNAL TABLE json_table (
      StudentId string,
      Grade int,
      StudentDetails array<struct<
          FirstName:string,
          LastName:string,
          YearJoined:int
          >
      >,
      StudentClassCollection array<struct<
          ClassId:string,
          ClassParticipation:string,
          ClassParticipationRank:string,
          Score:int,
          PerformedActivity:boolean
          >
      >
    ) ROW FORMAT SERDE 'org.openx.data.jsonserde.JsonSerDe'
    LOCATION '/json/students';

Para mostrar el nombre y el apellido del alumno

    SELECT StudentDetails.FirstName, StudentDetails.LastName FROM json_table;

Este es el resultado de la consola de sección.

![Consulta de SerDe 1][image-hdi-hivejson-serde_query1]

Para calcular la suma de la puntuación del documento JSON

    SELECT SUM(scores)
    FROM json_table jt
      lateral view explode(jt.StudentClassCollection.Score) collection as scores;

La consulta anterior usa [vista lateral de explosión](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) UDF para expandir la matriz de puntuaciones de modo que puede sumar.

Éste es el resultado de la consola de sección.

![Consulta de SerDe 2][image-hdi-hivejson-serde_query2]

Para buscar un estudiante determinado ha obtenido más de 80 puntos los asuntos que seleccione  
      JT. StudentClassCollection.ClassId de json_table jt lateral vista de explosión (JT. Colección de StudentClassCollection.Score) como puntuación puntuación donde > 80;

La consulta anterior devuelve una matriz de sección a diferencia de get\_json\_objeto que devuelve una cadena.

![Consulta SerDe 3][image-hdi-hivejson-serde_query3]

Si desea skil JSON con formato incorrecto, a continuación, como se explica en la [página wiki](https://github.com/sheetaldolas/Hive-JSON-Serde/tree/master) de este SerDe se puede conseguir escribiendo el siguiente código:  

    ALTER TABLE json_table SET SERDEPROPERTIES ( "ignore.malformed.json" = "true");




##<a name="summary"></a>Resumen
En conclusión, el tipo de operador JSON en subárbol que elija depende del escenario. Si tiene un documento JSON simple y solo tiene un campo para buscar en: puede elegir usa el subárbol UDF\_json\_objeto. Si tiene más de una claves para buscar puede usar json_tuple. Si tiene un documento anidado, debe usar la SerDe JSON.

Para ver otros artículos relacionados, vea

- [Usar la sección y HiveQL con Hadoop en HDInsight para analizar un archivo de ejemplo Apache log4j](hdinsight-use-hive.md)
- [Analizar datos de vuelo retraso usando subárbol en HDInsight](hdinsight-analyze-flight-delay-data.md)
- [Analizar datos de Twitter mediante subárbol en HDInsight](hdinsight-analyze-twitter-data.md)
- [Ejecutar un trabajo de Hadoop con DocumentDB y HDInsight](../documentdb/documentdb-run-hadoop-with-hdinsight.md)

[hdinsight-python]: hdinsight-python.md

[image-hdi-hivejson-flatten]: ./media/hdinsight-using-json-in-hive/flatten.png
[image-hdi-hivejson-getjsonobject]: ./media/hdinsight-using-json-in-hive/getjsonobject.png
[image-hdi-hivejson-jsontuple]: ./media/hdinsight-using-json-in-hive/jsontuple.png
[image-hdi-hivejson-jdk]: ./media/hdinsight-using-json-in-hive/jdk.png
[image-hdi-hivejson-maven]: ./media/hdinsight-using-json-in-hive/maven.png
[image-hdi-hivejson-serde]: ./media/hdinsight-using-json-in-hive/serde.png
[image-hdi-hivejson-addjar]: ./media/hdinsight-using-json-in-hive/addjar.png
[image-hdi-hivejson-serde_query1]: ./media/hdinsight-using-json-in-hive/serde_query1.png
[image-hdi-hivejson-serde_query2]: ./media/hdinsight-using-json-in-hive/serde_query2.png
[image-hdi-hivejson-serde_query3]: ./media/hdinsight-using-json-in-hive/serde_query3.png
[image-hdi-hivejson-serde_result]: ./media/hdinsight-using-json-in-hive/serde_result.png

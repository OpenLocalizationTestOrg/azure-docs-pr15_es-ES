<properties
   pageTitle="Usar Hadoop cerdo en HDInsight | Microsoft Azure"
   description="Aprenda a usar cerdo con Hadoop en HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/14/2016"
   ms.author="larryfr"/>

# <a name="use-pig-with-hadoop-on-hdinsight"></a>Usar cerdo con Hadoop en HDInsight

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

[Cerdo Apache](http://pig.apache.org/) es una plataforma para crear programas para Hadoop mediante un lenguaje de procedimientos conocido como *cerdo latino*. Cerdo es una alternativa a Java para crear soluciones *MapReduce* y se incluye con HDInsight de Azure.

En este artículo, aprenderá cómo puede usar cerdo con HDInsight.

##<a id="why"></a>¿Por qué usar cerdo?

Uno de los desafíos de procesamiento de datos con MapReduce en Hadoop implementa la lógica de procesamiento con solo un mapa y una función de reducir. Para procesos complejos, debe dividir procesamiento en varias operaciones de MapReduce que están conectadas a menudo para lograr el resultado deseado.

En lugar de obligar a usar solo mapa y reducir funciones, cerdo le permite definir los procesos como una serie de transformaciones que los datos atraviesan para generar el resultado deseado.

El idioma de cerdo latino le permite describir el flujo de datos de entrada sin formato, a través de una o más transformaciones, para generar el resultado deseado. Programas de cerdo latino siguen este modelo general:

- **Carga**: leer datos para manipular desde el sistema de archivos
- **Transformar**: manipular los datos
- **Descargar o almacén**: enviar datos a la pantalla o almacenarse para el procesamiento

Cerdo latino también es compatible con funciones definidas por el usuario (UDF), que permite invocar componentes externos que implementan lógica que es difícil de modelo de cerdo latino.

Para obtener más información acerca de cerdo latino, vea [cerdo latino referencia Manual 1](http://pig.apache.org/docs/r0.7.0/piglatin_ref1.html) y [2 de Manual de referencia de cerdo latino](http://pig.apache.org/docs/r0.7.0/piglatin_ref2.html).

Para obtener un ejemplo del uso de UDF con cerdo, consulte los siguientes documentos:

* [Usar DataFu con cerdo en HDInsight](hdinsight-hadoop-use-pig-datafu-udf.md) - DataFu es una colección de UDF útiles mantenidas Apache

* [Utilizar Python con cerdo y subárbol en HDInsight](hdinsight-python.md)

* [Usar C# con la sección y cerdo en HDInsight](hdinsight-hadoop-hive-pig-udf-dotnet-csharp.md)

##<a id="data"></a>Acerca de los datos de ejemplo

Este ejemplo usa un archivo de ejemplo *log4j* , que se almacena en **/example/data/sample.log** en el contenedor de almacenamiento de blobs de Windows. Cada registro dentro del archivo consta de una línea de campos que contiene un `[LOG LEVEL]` campo para mostrar el tipo y la gravedad, por ejemplo:

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

En el ejemplo anterior, el nivel de registro es el ERROR.

> [AZURE.NOTE] Puede también generar un archivo de log4j mediante la herramienta de registro de [Apache Log4j](http://en.wikipedia.org/wiki/Log4j) y, a continuación, cargar ese archivo en el blob. Para obtener instrucciones, vea [Cargar datos a HDInsight](hdinsight-upload-data.md) . Para obtener más información sobre el uso de blobs de Azure almacenamiento con HDInsight, consulte [Usar almacenamiento de blobs de Azure con HDInsight](hdinsight-hadoop-use-blob-storage.md).

Los datos de ejemplo se almacenan en el almacenamiento de blobs de Windows Azure, que HDInsight se usa como el sistema de archivo predeterminado para clústeres Hadoop. HDInsight puede tener acceso a los archivos almacenados en BLOB mediante el prefijo **wasb** . Por ejemplo, para obtener acceso al archivo sample.log, debería usar la siguiente sintaxis:

    wasbs:///example/data/sample.log

Dado que WASB es el valor predeterminado de almacenamiento de HDInsight, también puede acceder al archivo usando **/example/data/sample.log** de cerdo latino.

> [AZURE.NOTE] La sintaxis de la **wasbs: / / /**, se utiliza para acceder a los archivos almacenados en el contenedor de almacenamiento predeterminada para el clúster de HDInsight. Si especifica cuentas de almacenamiento adicional cuando aprovisionado el clúster y desea obtener acceso a los archivos almacenados en estas cuentas, puede acceder a los datos especificando la dirección contenedor almacenamiento y el nombre de la cuenta, por ejemplo: **wasbs://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log**.


##<a id="job"></a>Acerca del trabajo de ejemplo

La siguiente tarea cerdo latino carga el archivo de **sample.log** al almacenamiento predeterminado para el clúster de HDInsight. A continuación, realiza una serie de transformaciones que producen un recuento del número de veces que se ha producido cada nivel de registro en los datos de entrada. Los resultados se guardan en STDOUT.

    LOGS = LOAD 'wasbs:///example/data/sample.log';
    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
    FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
    GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
    FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
    RESULT = order FREQUENCIES by COUNT desc;
    DUMP RESULT;

La imagen siguiente muestra un desglose de lo que hace cada transformación a los datos.

![Representación gráfica de las transformaciones][image-hdi-pig-data-transformation]

##<a id="run"></a>Ejecutar el trabajo de cerdo latino

HDInsight puede ejecutar trabajos cerdo latino mediante una variedad de métodos. Utilice la siguiente tabla para decidir qué método es adecuado para usted y luego siga el vínculo para obtener un tutorial.

| **Use esta opción** si desea...                                   | .. .an shell **interactivo** | ... proceso **por lotes** | .. .with este **sistema operativo del clúster** | .. .de este **sistema operativo del cliente** |
|:--------------------------------------------------------------|:---------------------------:|:-----------------------:|:------------------------------------------|:-----------------------------------------|
| [SSH](hdinsight-hadoop-use-pig-ssh.md)                        |              ✔              |            ✔            | Linux                                     | Linux, Unix, Mac OS X o Windows        |
| [Doblez](hdinsight-hadoop-use-pig-curl.md)                      |           &nbsp;            |            ✔            | Linux o Windows                          | Linux, Unix, Mac OS X o Windows        |
| [.NET SDK para Hadoop](hdinsight-hadoop-use-pig-dotnet-sdk.md) |           &nbsp;            |            ✔            | Linux o Windows                          | Windows (por ahora)                        |
| [Windows PowerShell](hdinsight-hadoop-use-pig-powershell.md)  |           &nbsp;            |            ✔            | Linux o Windows                          | Windows                                  |
| [Escritorio remoto](hdinsight-hadoop-use-pig-remote-desktop.md)  |              ✔              |            ✔            | Windows                                   | Windows                                  |


## <a name="running-pig-jobs-on-azure-hdinsight-using-on-premises-sql-server-integration-services"></a>Ejecución de trabajos de cerdo en Azure HDInsight con local SQL Server Integration Services

También puede usar SQL Server Integration Services (SSIS) para ejecutar un trabajo de cerdo. El Feature Pack de Windows Azure para SSIS proporciona los siguientes componentes que funcionan con los trabajos de cerdo en HDInsight.


- [Tarea de cerdo de Azure HDInsight][pigtask]
- [Administrador de conexiones de suscripción de Azure][connectionmanager]


Más información sobre el Feature Pack de Azure para SSIS [aquí][ssispack].


##<a id="nextsteps"></a>Pasos siguientes

Ahora que ha aprendido a usar cerdo con HDInsight, use los siguientes vínculos para explorar otras formas de trabajar con HDInsight de Azure.

* [Cargar datos a HDInsight][hdinsight-upload-data]
* [Usar subárbol con HDInsight][hdinsight-use-hive]
* [Usar Sqoop con HDInsight](hdinsight-use-sqoop.md)
* [Usar Oozie con HDInsight](hdinsight-use-oozie.md)
* [Usar MapReduce trabajos con HDInsight][hdinsight-use-mapreduce]

[check]: ./media/hdinsight-use-pig/hdi.checkmark.png

[apachepig-home]: http://pig.apache.org/
[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
[curl]: http://curl.haxx.se/
[pigtask]: http://msdn.microsoft.com/library/mt146781(v=sql.120).aspx
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx

[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md#mapreduce-sdk

[Powershell-install-configure]: ../powershell-install-configure.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx

[image-hdi-log4j-sample]: ./media/hdinsight-use-pig/HDI.wholesamplefile.png
[image-hdi-pig-data-transformation]: ./media/hdinsight-use-pig/HDI.DataTransformation.gif
[image-hdi-pig-powershell]: ./media/hdinsight-use-pig/hdi.pig.powershell.png
[image-hdi-pig-architecture]: ./media/hdinsight-use-pig/HDI.Pig.Architecture.png

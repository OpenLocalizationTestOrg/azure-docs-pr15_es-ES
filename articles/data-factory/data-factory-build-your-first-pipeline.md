<properties
    pageTitle="Tutorial de fábrica de datos: primera canalización de datos | Microsoft Azure"
    description="Este tutorial generador de datos de Azure muestra cómo crear y programar un generador de datos que procesa datos usando secuencias de comandos de sección en un clúster de Hadoop."
    services="data-factory"
    keywords="datos de Azure fábrica tutorial hadoop clúster, hadoop subárbol"
    documentationCenter=""
    authors="spelluru"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="data-factory"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article" 
    ms.date="09/26/2016"
    ms.author="spelluru"/>

# <a name="tutorial-build-your-first-pipeline-to-process-data-using-hadoop-cluster"></a>Tutorial: Crear su primer canalización para procesar datos mediante clúster Hadoop 
> [AZURE.SELECTOR]
- [Información general y los requisitos previos](data-factory-build-your-first-pipeline.md)
- [Portal de Azure](data-factory-build-your-first-pipeline-using-editor.md)
- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Plantilla de administrador de recursos](data-factory-build-your-first-pipeline-using-arm.md)
- [API DE REST](data-factory-build-your-first-pipeline-using-rest-api.md)

En este tutorial, crear su primer generador de datos de Azure con una canalización de datos que procesa datos ejecutando el script de sección en un clúster de Azure HDInsight (Hadoop). 

> [AZURE.NOTE] En este artículo no proporciona una descripción conceptual del generador de datos de Azure. Para obtener información general del servicio, vea [Introducción al generador de datos de Azure](data-factory-introduction.md). Vea la [ruta de aprendizaje del generador de datos](https://azure.microsoft.com/documentation/learning-paths/data-factory/) para que una forma recomendada para desplazarse por el contenido para obtener información sobre el generador de datos.

## <a name="whats-covered-in-this-tutorial"></a>¿Qué está cubierto en este tutorial? 
**Generador de datos de Azure** permite crear el **movimiento** de datos y las tareas de **procesamiento** de datos como flujos de trabajo controlados por datos (también denominados canalizaciones de datos). Aprenda a crear su primera actividad canalización con un tratamiento de datos (o transformación de datos) de datos. Esta actividad utiliza un clúster de HDInsight Hadoop para transformar y analizar los registros de web de ejemplo.  

En este tutorial, realice los pasos siguientes:

1.  Crear un **Generador de datos**. Un generador de datos puede contener uno o más datos de tuberías que mover y proceso de datos. 
2.  Crear **servicios vinculados**. Crear un servicio vinculado para vincular un almacén de datos o un servicio de cálculo para el generador de datos. Un almacén de datos, como el almacenamiento de Azure contiene los datos de entrada y salida de las actividades de la canalización. Un servicio de cálculo como clúster HDInsight Hadoop procesos/transformaciones de datos.    
3.  Cree la entrada y salida **conjuntos de datos**. Un conjunto de datos de entrada representa la entrada de una actividad de la canalización y un conjunto de datos de salida representa el resultado de la actividad.
3.  Crear la **canalización**. Una canalización puede tener una o varias actividades (ejemplos: copiar actividad, HDInsight subárbol actividad). Este ejemplo usa la actividad de subárbol HDInsight que se ejecuta una secuencia de comandos de la sección en un clúster de HDInsight Hadoop. La secuencia de comandos primero crea una tabla que hace referencia a los datos de registro bruto web almacenados en el almacenamiento de blobs de Windows Azure y, a continuación, divide los datos sin formato por año y mes.

    Existen dos tipos de actividades compatibles con el generador de datos de Azure. Son: [las actividades de movimiento de datos](data-factory-data-movement-activities.md) y [las actividades de transformación de datos](data-factory-data-transformation-activities.md). No hay actividad de movimiento de un único datos, que es la actividad de copia. En este tutorial, no use la actividad de copia. Para obtener un tutorial sobre el uso de la actividad de copia, consulte [Tutorial: copiar datos de un Azure blob a SQL Azure](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). Actividad de HDInsight subárbol que use en este tutorial es una de las actividades de transformación de datos compatibles con el generador de datos.  
 
Esta es la **vista de diagrama** de la fábrica de datos de ejemplo creado en este tutorial. 

![Vista de diagrama de tutorial del generador de datos](./media/data-factory-build-your-first-pipeline/data-factory-tutorial-diagram-view.png)

En este tutorial, **inputdata** carpeta del contenedor de blobs de Windows Azure **adfgetstarted** contiene un archivo llamado input.log. Este archivo de registro tiene entradas de los tres meses: enero, febrero y marzo de 2016. Estas son las filas de ejemplo para cada mes en el archivo de entrada. 

    2016-01-01,02:01:09,SAMPLEWEBSITE,GET,/blogposts/mvc4/step2.png,X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,53175,871 
    2016-02-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
    2016-03-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871

Cuando el archivo se procesa la canalización con HDInsight subárbol actividad, la actividad se ejecuta una secuencia de comandos de la sección en el clúster HDInsight que particiones de entrada de datos por año y mes. La secuencia de comandos crea tres carpetas de salida que contienen un archivo con entradas de cada mes.  

    adfgetstarted/partitioneddata/year=2016/month=1/000000_0
    adfgetstarted/partitioneddata/year=2016/month=2/000000_0
    adfgetstarted/partitioneddata/year=2016/month=3/000000_0

Desde las líneas de ejemplo mostradas anteriormente, la primera que (con 2016-01-01) se escribe en el archivo 000000_0 el mes = 1 carpeta. Del mismo modo, el segundo se se escribe en el archivo el mes = 2 carpeta y la tercera uno se escribe en el archivo en el mes = 3 de la carpeta.  


## <a name="pre-requisites"></a>Requisitos previos
Antes de comenzar este tutorial, debe tener los siguientes requisitos previos:

1.  **Azure suscripción** : si no tiene una suscripción de Azure, puede crear una cuenta de prueba gratuita de dos minutos. Consulte el artículo de [Prueba gratuita](https://azure.microsoft.com/pricing/free-trial/) de cómo puede obtener una cuenta gratuita de prueba.

2.  **Almacenamiento de azure** : usar una cuenta de almacenamiento de Azure para almacenar los datos en este tutorial. Si no tiene una cuenta de almacenamiento de Azure, vea el artículo [crear una cuenta de almacenamiento](../storage/storage-create-storage-account.md#create-a-storage-account) . Después de crear la cuenta de almacenamiento, anote el **nombre de cuenta** y una **tecla de acceso**. Vea [la vista, copia y las teclas de acceso de almacenamiento regenerar](../storage/storage-create-storage-account.md#view-and-copy-storage-access-keys). 

### <a name="upload-files-to-azure-storage-for-the-tutorial"></a>Cargar archivos en el almacenamiento de Azure para el tutorial
Antes de iniciar el tutorial, necesita preparar su cuenta de almacenamiento de Azure con los archivos de ejemplo para el tutorial.

1. Cargar archivo de la sección consulta (HQL) a la carpeta de **script** del contenedor de blob **adfgetstarted** .
2. Cargar archivo de entrada a la carpeta **inputdata** del contenedor de blob **adfgetstarted** . 

#### <a name="create-hql-script-file"></a>Crear archivo de comandos HQL 

1. Inicie **el Bloc de notas** y pegue el siguiente script HQL. Esta secuencia de comandos de la sección crea dos tablas: **WebLogsRaw** y **WebLogsPartitioned**. Haga clic en **archivo** , en el menú y seleccione **Guardar como**. Cambiar a la carpeta **C:\adfgetstarted** en el disco duro. Seleccione * *todos los archivos (*.*) **para el** Guardar como tipo** campo. Escriba **partitionweblogs.hql** para el ****de nombre de archivo. Confirme que la **codificación de** campo en la parte inferior del cuadro de diálogo se establece en **ANSI**. Si no es así, establezca **ANSI **.  

        set hive.exec.dynamic.partition.mode=nonstrict;
        
        DROP TABLE IF EXISTS WebLogsRaw; 
        CREATE TABLE WebLogsRaw (
          date  date,
          time  string,
          ssitename string,
          csmethod  string,
          csuristem  string,
          csuriquery string,
          sport int,
          susername string,
          cipcsUserAgent string,
          csCookie string,
          csReferer string,
          cshost  string,
          scstatus  int,
          scsubstatus  int,
          scwin32status  int,
          scbytes int,
          csbytes int,
          timetaken int
        )
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        LINES TERMINATED BY '\n' 
        tblproperties ("skip.header.line.count"="2");
        
        LOAD DATA INPATH '${hiveconf:inputtable}' OVERWRITE INTO TABLE WebLogsRaw;
        
        DROP TABLE IF EXISTS WebLogsPartitioned ; 
        create external table WebLogsPartitioned (  
          date  date,
          time  string,
          ssitename string,
          csmethod  string,
          csuristem  string,
          csuriquery string,
          sport int,
          susername string,
          cipcsUserAgent string,
          csCookie string,
          csReferer string,
          cshost  string,
          scstatus  int,
          scsubstatus  int,
          scwin32status  int,
          scbytes int,
          csbytes int,
          timetaken int
        )
        partitioned by ( year int, month int)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
        STORED AS TEXTFILE 
        LOCATION '${hiveconf:partitionedtable}';
        
        INSERT INTO TABLE WebLogsPartitioned  PARTITION( year , month) 
        SELECT
          date,
          time,
          ssitename,
          csmethod,
          csuristem,
          csuriquery,
          sport,
          susername,
          cipcsUserAgent,
          csCookie,
          csReferer,
          cshost,
          scstatus,
          scsubstatus,
          scwin32status,
          scbytes,
          csbytes,
          timetaken,
          year(date),
          month(date)
        FROM WebLogsRaw

En tiempo de ejecución, la actividad de sección en la canalización de datos fábrica pasa valores el **inputtable** y **partitionedtable** parámetros como se muestra en el fragmento de código siguiente:  

        "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
        "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"

El **storageaccountname** es el nombre de su cuenta de almacenamiento de Azure.
 
#### <a name="create-a-sample-input-file"></a>Crear un archivo de entrada de ejemplo
Con el Bloc de notas, cree un archivo llamado **input.log** en la **c:\adfgetstarted** con el contenido siguiente: 

    #Software: Microsoft Internet Information Services 8.0
    #Fields: date time s-sitename cs-method cs-uri-stem cs-uri-query s-port cs-username c-ip cs(User-Agent) cs(Cookie) cs(Referer) cs-host sc-status sc-substatus sc-win32-status sc-bytes cs-bytes time-taken
    2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46
    2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32
    2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step4.png X-ARR-LOG-ID=4bea5b3d-8ac9-46c9-9b8c-ec3e9500cbea 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 72177 871 47
    2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step5.png X-ARR-LOG-ID=9b0c14b1-434d-495a-9b0d-46775194257b 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 37931 871 32
    2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step6.png X-ARR-LOG-ID=f99cff81-ec38-4a13-b2fe-21b10adca996 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 27146 871 47
    2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step1.png X-ARR-LOG-ID=af94d3b4-8e05-4871-82c4-638f866d4e83 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 66259 871 140
    2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47

#### <a name="upload-input-file-and-hql-file-to-your-azure-blob-storage"></a>Cargar el archivo de entrada y HQL en el almacenamiento de blobs de Windows Azure

Esta sección proporciona instrucciones sobre cómo usar la herramienta de **AzCopy** para copiar los archivos input.log y partitionweblogs.hql para el almacenamiento de blobs de Windows Azure. Puede usar cualquier otra herramienta de su elección (por ejemplo: [Microsoft Azure almacenamiento Explorer](http://storageexplorer.com/), [CloudXPlorer por ClumsyLeaf Software](http://clumsyleaf.com/products/cloudxplorer) para realizar esta tarea.   
     
1. Descargar la [versión más reciente de **AzCopy**](http://aka.ms/downloadazcopy)o la [última versión de vista previa](http://aka.ms/downloadazcopypr). Consulte el artículo [cómo usar AzCopy](../storage/storage-use-azcopy.md) para obtener instrucciones sobre el uso de la utilidad.
2. Vaya a la carpeta c:\adfgetstarted y ejecute el siguiente comando: 

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\AzCopy" /Source:. /Dest:https://<storageaccountname>.blob.core.windows.net/adfgetstarted/inputdata /DestKey:<storageaccesskey>  /Pattern:input.log

    Este comando carga el archivo **input.log** en la cuenta de almacenamiento (contenedor de**adfgetstarted** y **inputdata** carpeta). Reemplace **storageaccountname** con el nombre de su cuenta de almacenamiento y **storageaccesskey** con la tecla de acceso de almacenamiento.

    > [AZURE.NOTE] Este comando crea un contenedor denominado **adfgetstarted** en el almacenamiento de blobs de Windows Azure y copia el archivo de **input.log** de su unidad local a la carpeta **inputdata** en el contenedor. 
    
3. Después de que el archivo se ha cargado correctamente, verá el resultado similar al siguiente de AzCopy.
    
        Finished 1 of total 1 file(s).
        [2015/12/16 23:07:33] Transfer summary:
        -----------------
        Total files transferred: 1
        Transfer successfully:   1
        Transfer skipped:        0
        Transfer failed:         0
        Elapsed time:            00.00:00:01
5. Ejecute el comando siguiente para cargar el archivo **partitionweblogs.hql** en la carpeta de **script** del contenedor **adfgetstarted** . Esto es el comando: 
    
        AzCopy /Source:. /Dest:https://<storageaccountname>.blob.core.windows.net/adfgetstarted/script /DestKey:<storageaccesskey>  /Pattern:partitionweblogs.hql

Se han completado los requisitos previos. Puede crear un generador de datos con una de las siguientes maneras. Haga clic en una de las pestañas en la parte superior o en los siguientes vínculos para realizar el tutorial. 

- [Portal de Azure](data-factory-build-your-first-pipeline-using-editor.md)
- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Plantilla de administrador de recursos](data-factory-build-your-first-pipeline-using-arm.md)
- [API DE REST](data-factory-build-your-first-pipeline-using-rest-api.md)
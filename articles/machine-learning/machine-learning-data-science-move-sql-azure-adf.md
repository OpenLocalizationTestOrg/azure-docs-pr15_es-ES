<properties
    pageTitle="Mover los datos de un servidor local SQL Server a SQL Azure con el generador de datos de Azure | Azure"
    description="Configurar una canalización ADF que crea dos actividades de migración de datos que se mueven juntos datos diariamente entre las bases de datos local y en la nube."
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/14/2016"
    ms.author="bradsev" />


# <a name="move-data-from-an-on-premise-sql-server-to-sql-azure-with-azure-data-factory"></a>Mover datos de un servidor SQL local SQL Azure con el generador de datos de Azure

Este tema muestra cómo mover los datos de una base de datos local SQL Server a una base de datos de SQL Azure a través de almacenamiento de blobs de Windows Azure con el generador de datos de Azure (ADF).

Los siguientes vínculos de **menú** a temas que describen cómo recopilar datos en entornos de destino donde pueden almacenarse y procesarse durante el proceso de ciencias de datos de equipo de los datos.

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]


## <a name="intro"></a>Introducción: ¿Qué es ADF y cuándo se debe utilizar para migrar datos?

Generador de datos de Azure es un servicio de integración de datos en la nube totalmente administrado que organiza y automatiza el movimiento y la transformación de datos. El concepto clave en el modelo ADF es canalización. Una canalización es una agrupación lógica de actividades, que definen las acciones que puede realizar en los datos contenidos en los conjuntos de datos. Servicios vinculados se utilizan para definir la información necesaria para que el generador de datos para conectarse a los recursos de datos.

Con ADF, servicios de procesamiento de datos existentes se pueden crear en canalizaciones de datos que son altamente disponibles y administradas en la nube. Estas canalizaciones de datos pueden programarse para recopilar, preparar, transformar, analizar y publicar datos y ADF administra y organiza los datos complejos y dependencias de procesamiento. Soluciones pueden compilar e implementar en la nube, conectarse a un número creciente de local rápidamente y orígenes de datos en la nube.

Considere usar ADF:

- Cuando los datos deben migrarse continuamente en un escenario híbrido tiene acceso a recursos locales y de la nube 
- Cuando los datos se lleva a cabo o deben modificarse o tiene lógica de negocios agregada cuando se migran. 

ADF permite la programación y la supervisión de trabajos mediante sencillas secuencias de comandos JSON que administración el movimiento de datos de forma periódica. ADF también tiene otras funciones, como soporte para operaciones más complejas. Para obtener más información sobre ADF, consulte la documentación del [Generador de datos de Azure (ADF)](https://azure.microsoft.com/services/data-factory/).


## <a name="scenario"></a>El escenario

Se configura una canalización ADF que crea dos actividades de migración de datos. Juntos mover datos diariamente entre una base de datos SQL de local y una base de datos de SQL Azure en la nube. Las dos actividades son:

* copiar datos de una base de datos de SQL Server local a una cuenta de almacenamiento de blobs de Windows Azure
* Copie datos de la cuenta de almacenamiento de blobs de Windows Azure a una base de datos de SQL Azure.

>[AZURE.NOTE] Los pasos que se muestra a continuación se han adaptado desde el tutorial más detallado proporcionado por el equipo ADF: [mover datos entre orígenes locales y la nube con Data Management Gateway](../data-factory/data-factory-move-data-between-onprem-and-cloud.md) referencias a las secciones correspondientes de este tema se proporcionan cuando corresponda.


## <a name="prereqs"></a>Requisitos previos
En este tutorial se supone que:

* Una **suscripción de Azure**. Si no tiene una suscripción, puede registrarse para una [prueba gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Una **cuenta de almacenamiento de Azure**. Usar una cuenta de almacenamiento de Azure para almacenar los datos en este tutorial. Si no tiene una cuenta de almacenamiento de Azure, vea el artículo [crear una cuenta de almacenamiento](storage-create-storage-account.md#create-a-storage-account) . Después de crear la cuenta de almacenamiento, debe obtener la clave de cuenta que utiliza para acceder al almacenamiento. Vea [la vista, copia y las teclas de acceso de almacenamiento regenerar](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).
* Acceso a una **base de datos SQL Azure**. Si debe configurar una base de datos de SQL Azure, la tpoic [Introducción a la base de datos de SQL de Microsoft Azure](../sql-database/sql-database-get-started.md) proporciona información sobre cómo aprovisionar una nueva instancia de una base de datos de SQL Azure.
* Instalado y configurado **Azure PowerShell** localmente. Para obtener instrucciones, consulte [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md).

> [AZURE.NOTE] Este procedimiento utiliza el [portal de Azure](https://portal.azure.com/).


##<a name="upload-data"></a>Cargar los datos a SQL Server local

Usamos el [conjunto de datos de Nueva York Taxi](http://chriswhong.com/open-data/foil_nyc_taxi/) para demostrar el proceso de migración. El conjunto de datos de Nueva York Taxi está disponible, tal como se indica en que los [Datos de Nueva York Taxi](http://www.andresmh.com/nyctaxitrips/)de almacenamiento de blobs de publicación, en Azure. Los datos tienen dos archivos, el archivo trip_data.csv, que contiene los detalles de viaje, y el archivo trip_far.csv, que contiene detalles de la tarifa de pago para cada viaje. Un ejemplo y una descripción de estos archivos se proporcionan en la [Descripción del conjunto de datos de viajes de Nueva York Taxi](machine-learning-data-science-process-sql-walkthrough.md#dataset).


Puede adaptar el procedimiento para un conjunto de sus propios datos, o bien, siga los pasos que se describe utilizando el conjunto de datos de Nueva York Taxi. Para cargar el conjunto de datos de Nueva York Taxi en la base de datos de SQL Server local, siga el procedimiento descrito en [Masa importar datos en la base de datos de SQL Server](machine-learning-data-science-process-sql-walkthrough.md#dbload). Estas son instrucciones para un servidor de SQL Server en una máquina Virtual de Azure, pero el procedimiento para cargar en el servidor de SQL local es la misma.


##<a name="create-adf"></a>Crear un generador de datos de Azure

Se proporcionan las instrucciones para crear un nuevo generador de datos de Azure y un grupo de recursos en el [portal de Azure](https://portal.azure.com/) [crear un generador de datos de Azure](../data-factory/data-factory-build-your-first-pipeline-using-editor.md#step-1-creating-the-data-factory). Nombre de la instancia ADF nuevo *adfdsp* y nombre de grupo creado recursos *adfdsprg*.


## <a name="install-and-configure-up-the-data-management-gateway"></a>Instalar y configurar el Data Management Gateway

Para habilitar las canalizaciones en un generador de datos de Azure para trabajar con un servidor de SQL local, debe agregar como servicio vinculado a la fábrica de datos. Para crear un servicio vinculadas para un servidor de SQL local, debe:

- Descargue e instale Microsoft Data Management Gateway en el equipo local. 
- configurar el servicio vinculado para el origen de datos local usar la puerta de enlace. 

Data Management Gateway serializa y deserializa los datos de origen y el receptor en el equipo donde se hospeda.

Para instrucciones de configuración y en Data Management Gateway, vea [mover datos entre orígenes locales y la nube con Data Management Gateway](../data-factory/data-factory-move-data-between-onprem-and-cloud.md)


## <a name="adflinkedservices"></a>Crear servicios vinculados para conectarse a los recursos de datos

Un servicio vinculado define la información necesaria para que el generador de datos de Azure para conectarse a un recurso de datos. El procedimiento paso a paso para crear servicios vinculados se proporciona en [crear servicios vinculados](../data-factory/data-factory-move-data-between-onprem-and-cloud.md#step-2-create-linked-services).

Tenemos tres recursos en este escenario para el que se necesitan servicios vinculados.

1. [Servicio vinculada para local de SQL Server](#adf-linked-service-onprem-sql)
2. [Servicio vinculada para el almacenamiento de blobs de Windows Azure](#adf-linked-service-blob-store)
3. [Servicio vinculada para base de datos de SQL Azure](#adf-linked-service-azure-sql)


###<a name="adf-linked-service-onprem-sql"></a>Servicio vinculada para base de datos de SQL Server local

Para crear el servicio vinculado para SQL Server local:

- Haga clic en el **Almacén de datos** en la página de inicio de ADF en Portal clásico de Azure 
- Seleccione **SQL** y escriba las credenciales de *usuario* y *contraseña* para SQL Server local. Debe escribir el nombre del servidor como un **nombre de instancia de barra diagonal inversa nombre_de_servidor (nombreServidor\nombreInstancia) completo**. Nombre del servicio vinculado *adfonpremsql*.

###<a name="adf-linked-service-blob-store"></a>Servicio vinculada para BLOBs

Para crear el servicio vinculado de la cuenta de almacenamiento de blobs de Windows Azure:

- Haga clic en el **Almacén de datos** en la página de inicio de ADF en Portal clásico de Azure
- Seleccione la **Cuenta de almacenamiento de Azure** 
- Escriba el nombre de clave y el contenedor de la cuenta de almacenamiento de blobs de Windows Azure. Nombre del servicio vinculado *adfds*.

###<a name="adf-linked-service-azure-sql"></a>Servicio vinculada para base de datos de SQL Azure

Para crear el servicio vinculado para la base de datos de SQL Azure:

- Haga clic en el **Almacén de datos** en la página de inicio de ADF en Portal clásico de Azure
- Seleccione **SQL Azure** y escriba las credenciales de *usuario* y *contraseña* para la base de datos de SQL Azure. El *nombre de usuario* debe especificarse como *user@servername*.   


##<a name="adf-tables"></a>Definir y crear tablas para especificar cómo tener acceso a los conjuntos de datos

Crear tablas que especifique la estructura, la ubicación y la disponibilidad de los conjuntos de datos con los siguientes procedimientos de secuencias de comandos. Archivos JSON se usan para definir las tablas. Para obtener más información sobre la estructura de estos archivos, vea [conjuntos de datos](../data-factory/data-factory-create-datasets.md).

> [AZURE.NOTE]  Debe ejecutar el `Add-AzureAccount` cmdlet antes de ejecutar el cmdlet [AzureDataFactoryTable de nuevo](https://msdn.microsoft.com/library/azure/dn835096.aspx) para confirmar que la suscripción a la derecha de Azure está activada para la ejecución del comando. Para obtener la documentación de este cmdlet, vea [Agregar AzureAccount](https://msdn.microsoft.com/library/azure/dn790372.aspx).

Las definiciones de basada en JSON en las tablas usan los siguientes nombres:

* el **nombre de la tabla** de SQL server local es *nyctaxi_data*
* el **nombre del contenedor** de la cuenta de almacenamiento de blobs de Windows Azure es el *nombre del contenedor*  

Tres definiciones de tabla son necesarias para esta canalización ADF:

1. [Tabla local SQL](#adf-table-onprem-sql)
2. [Tabla de blobs](#adf-table-blob-store)
3. [SQL Azure tabla](#adf-table-azure-sql)

> [AZURE.NOTE]  Estos procedimientos utilizan Azure PowerShell para definir y crear las actividades ADF. Pero también se pueden realizar estas tareas con el portal de Azure. Para obtener información detallada, vea [Crear entrada y salida de conjuntos de datos](../data-factory/data-factory-move-data-between-onprem-and-cloud.md#step-3-create-input-and-output-datasets).

###<a name="adf-table-onprem-sql"></a>Tabla local SQL

En el siguiente archivo JSON se especifica la definición de tabla de SQL Server local:

        {
            "name": "OnPremSQLTable",
            "properties":
            {
                "location":
                {
                "type": "OnPremisesSqlServerTableLocation",
                "tableName": "nyctaxi_data",
                "linkedServiceName": "adfonpremsql"
                },
                "availability":
                {
                "frequency": "Day",
                "interval": 1,   
                "waitOnExternal":
                {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
                }

                }
            }
        }

Los nombres de columna no se incluyen aquí. Subtipo puede seleccionar en los nombres de columna incluidos aquí (para el tema de la [documentación de ADF](../data-factory/data-factory-data-movement-activities.md ) la comprobación de detalles.

Copie la definición de JSON de la tabla en un archivo denominado *onpremtabledef.json* y guárdelo en una ubicación conocida (Esto supuesta que *C:\temp\onpremtabledef.json*). Crear la tabla en ADF con el siguiente cmdlet de PowerShell de Azure:

    New-AzureDataFactoryTable -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp –File C:\temp\onpremtabledef.json


###<a name="adf-table-blob-store"></a>Tabla de blobs
Definición de la tabla para la ubicación de blobs de salida es la siguiente (se asignan los datos integrados de local a blobs de Windows Azure):

        {
            "name": "OutputBlobTable",
            "properties":
            {
                "location":
                {
                "type": "AzureBlobLocation",
                "folderPath": "containername",
                "format":
                {
                "type": "TextFormat",
                "columnDelimiter": "\t"
                },
                "linkedServiceName": "adfds"
                },
                "availability":
                {
                "frequency": "Day",
                "interval": 1
                }
            }
        }

Copie la definición de JSON de la tabla en un archivo denominado *bloboutputtabledef.json* y guárdelo en una ubicación conocida (Esto supuesta que *C:\temp\bloboutputtabledef.json*). Crear la tabla en ADF con el siguiente cmdlet de PowerShell de Azure:

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\bloboutputtabledef.json  

###<a name="adf-table-azure-sq"></a>SQL Azure tabla
Definición de la tabla de SQL Azure de salida es la siguiente (este esquema asigna los datos procedentes de blob):

    {
        "name": "OutputSQLAzureTable",
        "properties":
        {
            "structure":
            [
                { "name": "column1", type": "String"},
                { "name": "column2", type": "String"}                
            ],
            "location":
            {
                "type": "AzureSqlTableLocation",
                "tableName": "your_db_name",
                "linkedServiceName": "adfdssqlazure_linked_servicename"
            },
            "availability":
            {
                "frequency": "Day",
                "interval": 1            
            }
        }
    }

Copie la definición de JSON de la tabla en un archivo denominado *AzureSqlTable.json* y guárdelo en una ubicación conocida (Esto supuesta que *C:\temp\AzureSqlTable.json*). Crear la tabla en ADF con el siguiente cmdlet de PowerShell de Azure:

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\AzureSqlTable.json  


##<a name="adf-pipeline"></a>Definir y crear la canalización

Especificar las actividades que pertenecen a la canalización y crear la canalización de los siguientes procedimientos de secuencias de comandos. Un archivo JSON se usa para definir las propiedades de canalización.

* La secuencia de comandos se supone que el **nombre de la canalización** de *AMLDSProcessPipeline*.
* Tenga en cuenta que se debe establecer la periodicidad de la canalización se ejecutan diariamente y usar el tiempo de ejecución de forma predeterminada para el trabajo (12 a.m. UTC).

> [AZURE.NOTE]Los procedimientos siguientes use Azure PowerShell para definir y crear la canalización de ADF. Pero también se puede realizar esta tarea mediante el portal de Azure. Para obtener información detallada, vea [crear y ejecutar una canalización](../data-factory/data-factory-move-data-between-onprem-and-cloud.md#step-4-create-and-run-a-pipeline).

Mediante las definiciones de tabla proporcionadas anteriormente, la definición de canalización para el ADF se especifica como sigue:

        {
            "name": "AMLDSProcessPipeline",
            "properties":
            {
                "description" : "This pipeline has one Copy activity that copies data from an on-premise SQL to Azure blob",
                 "activities":
                [
                    {
                        "name": "CopyFromSQLtoBlob",
                        "description": "Copy data from on-premise SQL server to blob",     
                        "type": "CopyActivity",
                        "inputs": [ {"name": "OnPremSQLTable"} ],
                        "outputs": [ {"name": "OutputBlobTable"} ],
                        "transformation":
                        {
                            "source":
                            {                               
                                "type": "SqlSource",
                                "sqlReaderQuery": "select * from nyctaxi_data"
                            },
                            "sink":
                            {
                                "type": "BlobSink"
                            }   
                        },
                        "Policy":
                        {
                            "concurrency": 3,
                            "executionPriorityOrder": "NewestFirst",
                            "style": "StartOfInterval",
                            "retry": 0,
                            "timeout": "01:00:00"
                        }       

                     },

                    {
                        "name": "CopyFromBlobtoSQLAzure",
                        "description": "Push data to Sql Azure",        
                        "type": "CopyActivity",
                        "inputs": [ {"name": "OutputBlobTable"} ],
                        "outputs": [ {"name": "OutputSQLAzureTable"} ],
                        "transformation":
                        {
                            "source":
                            {                               
                                "type": "BlobSource"
                            },
                            "sink":
                            {
                                "type": "SqlSink",
                                "WriteBatchTimeout": "00:5:00",             
                            }           
                        },
                        "Policy":
                        {
                            "concurrency": 3,
                            "executionPriorityOrder": "NewestFirst",
                            "style": "StartOfInterval",
                            "retry": 2,
                            "timeout": "02:00:00"
                        }
                     }
                ]
            }
        }

Copie esta definición JSON de la canalización en un archivo denominado *pipelinedef.json* y guárdelo en una ubicación conocida (Esto supuesta que *C:\temp\pipelinedef.json*). Crear la canalización en ADF con el siguiente cmdlet de PowerShell de Azure:

    New-AzureDataFactoryPipeline  -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\pipelinedef.json

Confirme que puede ver la canalización de ADF en el Portal de clásico de Azure mostrarse como siguiente (al hacer clic en el diagrama)

![](media/machine-learning-data-science-move-sql-azure-adf/DJP1kji.png)


##<a name="adf-pipeline-start"></a>Iniciar la canalización
Ahora se puede ejecutar la canalización mediante el siguiente comando:

    Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp -StartDateTime startdateZ –EndDateTime enddateZ –Name AMLDSProcessPipeline

Los valores de parámetro *startdate* y *enddate* deben reemplazarse con las fechas reales entre los que desea que la canalización de ejecución.

Una vez que se ejecuta la canalización, debería poder ver los datos que aparecen en el contenedor seleccionado para el blob, un archivo al día.

Tenga en cuenta que no nos hemos aprovechar la funcionalidad de ADF a los datos de canalización incrementalmente. Para obtener más información sobre cómo hacer esto y otras funciones de ADF, consulte la [documentación de ADF](https://azure.microsoft.com/services/data-factory/).

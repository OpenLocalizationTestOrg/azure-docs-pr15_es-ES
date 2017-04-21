<properties 
   pageTitle="Introducción a análisis de lago de datos de Azure con interfaz de línea de comandos de Azure | Microsoft Azure" 
   description="Aprenda a usar la interfaz de línea de comandos de Azure para crear una cuenta de almacén de datos lago, cree una tarea de análisis de datos lago mediante SQL U y enviar el trabajo. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="05/16/2016"
   ms.author="edmaca"/>

# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-azure-command-line-interface-cli"></a>Tutorial: Introducción a análisis de lago de datos de Azure usa Azure de línea de comandos (CLI)

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]


Obtenga información sobre cómo usar Azure CLI para crear cuentas de análisis de lago de datos de Azure, definir los trabajos de análisis de datos lago en [SQL U](data-lake-analytics-u-sql-get-started.md)y enviar trabajos a cuentas de análisis de datos lago. Para obtener más información sobre el análisis de datos lago, vea [información general de análisis de lago de datos de Azure](data-lake-analytics-overview.md).

En este tutorial, desarrollará un trabajo que lee una pestaña archivo de valores (TSV) separados y lo convierte en un archivo de separados por comas (CSV) de valores. Para pasar por el mismo tutorial utilizar otras herramientas compatibles, haga clic en las pestañas en la parte superior de esta sección.

##<a name="prerequisites"></a>Requisitos previos

Antes de comenzar este tutorial, debe tener el siguiente:

- **Suscripción de un Azure**. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/pricing/free-trial/).
- **CLI azure**. Consulte [instalar y configurar Azure CLI](../xplat-cli-install.md).
    - Descargar e instalar la **versión preliminar** de [Herramientas de Azure CLI](https://github.com/MicrosoftBigData/AzureDataLake/releases) para completar esta demostración.
- **Autenticación**, con el siguiente comando:

        azure login
    Para obtener más información acerca de cómo autenticar con una cuenta profesional o educativa, vea [conectarse a una suscripción de Azure desde la CLI de Azure](../xplat-cli-connect.md).
- **Cambiar al modo de administrador de recursos de Azure**, la mediante el siguiente comando:

        azure config mode arm
        
## <a name="create-data-lake-analytics-account"></a>Crear cuenta de análisis de datos lago

Debe tener una cuenta de análisis de datos lago antes de poder ejecutar los trabajos. Para crear una cuenta de análisis de datos lago, debe especificar lo siguiente:

- **Grupo de recursos de Azure**: cuenta A datos lago análisis debe crearse dentro de un grupo de recursos de Azure. [Administrador de recursos de Azure](../azure-resource-manager/resource-group-overview.md) le permite trabajar con los recursos de la aplicación como un grupo. Puede implementar, actualizar o eliminar todos los recursos para la aplicación en una única operación coordinada.  

    Para enumerar los grupos de recursos en su suscripción:
    
        azure group list 
    
    Para crear un nuevo grupo de recursos:

        azure group create -n "<Resource Group Name>" -l "<Azure Location>"

- **Nombre de cuenta de análisis de datos lago**
- **Ubicación**: uno de los centros de datos de Azure que admita el análisis de datos lago.
- **Cuenta de lago de datos predeterminado**: cada cuenta de análisis de datos lago tiene una cuenta de lago de datos predeterminado.

    A la lista de la cuenta de lago de datos existente:
    
        azure datalake store account list

    Para crear una nueva cuenta de lago de datos:

        azure datalake store account create "<Data Lake Store Account Name>" "<Azure Location>" "<Resource Group Name>"

    > [AZURE.NOTE] El nombre de cuenta de lago datos solo debe contener letras minúsculas y números.



**Para crear una cuenta de análisis de datos lago**

        azure datalake analytics account create "<Data Lake Analytics Account Name>" "<Azure Location>" "<Resource Group Name>" "<Default Data Lake Account Name>"

        azure datalake analytics account list
        azure datalake analytics account show "<Data Lake Analytics Account Name>"          

![Análisis de datos lago Mostrar cuenta](./media/data-lake-analytics-get-started-cli/data-lake-analytics-show-account-cli.png)

> [AZURE.NOTE] El nombre de cuenta de análisis de datos lago solo debe contener letras minúsculas y números.


## <a name="upload-data-to-data-lake-store"></a>Cargar datos en el almacén de datos de lago

En este tutorial, procesará algunos registros de búsqueda.  El registro de búsqueda puede almacenarse en el almacén de datos lago o almacenamiento de blobs de Windows Azure. 

El Portal de Azure proporciona una interfaz de usuario para copiar algunos archivos de datos de ejemplo en la cuenta de lago de datos predeterminada, que incluyen un archivo de registro de la búsqueda. Vea [preparar los datos de origen](data-lake-analytics-get-started-portal.md#prepare-source-data) para cargar los datos en la cuenta predeterminada de almacén de datos lago.

Para cargar archivos con cli, utilice el siguiente comando:

    azure datalake store filesystem import "<Data Lake Store Account Name>" "<Path>" "<Destination>"
    azure datalake store filesystem list "<Data Lake Store Account Name>" "<Path>"

Análisis de datos lago también puede acceder a almacenamiento de blobs de Windows Azure.  Para cargar los datos en el almacenamiento de blobs de Windows Azure, vea [utilizar CLI Azure con el almacenamiento de Azure](../storage/storage-azure-cli.md).

## <a name="submit-data-lake-analytics-jobs"></a>Enviar trabajos de análisis de datos lago

Los trabajos de análisis de datos lago se escriben en lenguaje SQL U. Para obtener más información acerca de SQL U, consulte [referencia de lenguaje SQL U](http://go.microsoft.com/fwlink/?LinkId=691348)y [empezar a trabajar con el lenguaje SQL U](data-lake-analytics-u-sql-get-started.md) .

**Para crear un script de trabajo de análisis de datos lago**

- Crear un archivo de texto con la siguiente secuencia de comandos de SQL U y guarde el archivo de texto en su estación de trabajo:

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv"
            USING Extractors.Tsv();
        
        OUTPUT @searchlog   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();

    Esta secuencia de comandos SQL U lee el archivo de datos de origen con **Extractors.Tsv()**y, a continuación, crea un archivo csv con **Outputters.Csv()**. 
    
    No modifique las dos rutas a menos que copie el archivo de origen en una ubicación diferente.  Análisis de datos lago creará la carpeta de salida si no existe.
    
    Es más fácil usar rutas relativas de los archivos almacenan de forma predeterminada en cuentas de lago de datos. También puede usar rutas de acceso absolutas.  Por ejemplo 
    
        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
        
    Debe usar rutas de acceso absolutas para acceder a los archivos en las cuentas de almacenamiento vinculados.  La sintaxis de los archivos almacenados en cuenta vinculada de almacenamiento de Azure es:
    
        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

    >[AZURE.NOTE] Contenedor de blobs de Windows Azure con BLOB público o permisos de acceso de los contenedores público no se admiten actualmente.      

    
**Para enviar el trabajo**


    azure datalake analytics job create  "<Data Lake Analytics Account Name>" "<Job Name>" "<Script>"
    
    
Los comandos siguientes se pueden usar la lista de trabajos, obtener detalles del trabajo y Cancelar trabajos:

    azure datalake analytics job cancel "<Data Lake Analytics Account Name>" "<Job Id>"
    azure datalake analytics job list "<Data Lake Analytics Account Name>"
    azure datalake analytics job show "<Data Lake Analytics Account Name>" "<Job Id>"

Una vez completada la tarea, puede usar los siguientes cmdlets para el archivo de la lista y descargar el archivo:
    
    azure datalake store filesystem list "<Data Lake Store Account Name>" "/Output"
    azure datalake store filesystem export "<Data Lake Store Account Name>" "/Output/SearchLog-from-Data-Lake.csv" "<Destination>"
    azure datalake store filesystem read "<Data Lake Store Account Name>" "/Output/SearchLog-from-Data-Lake.csv" <Length> <Offset>

## <a name="see-also"></a>Vea también

- Para ver el mismo tutorial con otras herramientas, haga clic en los selectores de pestaña en la parte superior de la página.
- Para ver una consulta más compleja, vea [registros de sitio Web de analizar mediante el análisis de lago de datos de Azure](data-lake-analytics-analyze-weblogs.md).
- Para empezar a desarrollar aplicaciones SQL U, vea [secuencias de comandos SQL de U desarrollar mediante datos lago Tools para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Para obtener U SQL, consulte [Introducción a Azure datos lago análisis U-SQL idioma](data-lake-analytics-u-sql-get-started.md).
- Para las tareas de administración, vea [Administrar Azure datos lago el análisis con el Portal de Azure](data-lake-analytics-manage-use-portal.md).
- Para obtener una visión general de análisis de lago de datos, vea [información general de análisis de lago de datos de Azure](data-lake-analytics-overview.md).


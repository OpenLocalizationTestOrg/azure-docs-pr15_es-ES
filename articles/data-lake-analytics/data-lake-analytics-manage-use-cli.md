<properties 
   pageTitle="Administrar el análisis de lago de datos de Azure con interfaz de línea de comandos de Azure | Azure" 
   description="Obtenga información sobre cómo administrar cuentas de análisis de datos lago, orígenes de datos, trabajos y usuarios con CLI de Azure" 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="05/16/2016"
   ms.author="edmaca"/>

# <a name="manage-azure-data-lake-analytics-using-azure-command-line-interface-cli"></a>Administrar el análisis de lago de datos de Azure usa Azure de línea de comandos (CLI)

[AZURE.INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Obtenga información sobre cómo administrar cuentas de análisis de lago de datos de Azure, orígenes de datos, los usuarios y trabajos mediante el Azure. Para ver el tema de administración con otras herramientas, haga clic en la ficha, seleccione anterior.

**Requisitos previos**

Antes de comenzar este tutorial, debe tener el siguiente:

- **Suscripción de un Azure**. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/pricing/free-trial/).
- **CLI azure**. Consulte [instalar y configurar Azure CLI](../xplat-cli-install.md).
    - Descargar e instalar la **versión preliminar** de [Herramientas de Azure CLI](https://github.com/MicrosoftBigData/AzureDataLake/releases) para completar esta demostración.
- **Autenticación**, con el siguiente comando:

        azure login
    Para obtener más información acerca de cómo autenticar con una cuenta profesional o educativa, vea [conectarse a una suscripción de Azure desde la CLI de Azure](../xplat-cli-connect.md).
- **Cambiar al modo de administrador de recursos de Azure**, la mediante el siguiente comando:

        azure config mode arm

**Para obtener una lista de los comandos de la tienda de lago de datos y análisis de datos lago:**

    azure datalake store
    azure datalake analytics

<!-- ################################ -->
<!-- ################################ -->
## <a name="manage-accounts"></a>Administrar cuentas

Antes de ejecutar los trabajos de análisis de datos lago, debe tener una cuenta de análisis de datos lago. A diferencia de HDInsight de Azure no pagar cuando no se ejecuta un trabajo para una cuenta de análisis.  Solo paga por vez cuando se está ejecutando una tarea.  Para obtener más información, vea [Información general sobre el análisis de lago datos Azure](data-lake-analytics-overview.md).  

###<a name="create-accounts"></a>Crear cuentas

    azure datalake analytics account create "<Data Lake Analytics Account Name>" "<Azure Location>" "<Resource Group Name>" "<Default Data Lake Account Name>"


###<a name="update-accounts"></a>Actualizar cuentas

El siguiente comando actualiza las propiedades de una cuenta de análisis de lago de datos existente
    
    azure datalake analytics account set "<Data Lake Analytics Account Name>"


###<a name="list-accounts"></a>Lista de cuentas

Cuentas de análisis de lago de datos de lista 

    azure datalake analytics account list

Cuentas de análisis de lago de datos de lista dentro de un grupo de recursos específico

    azure datalake analytics account list -g "<Azure Resource Group Name>"

Obtener detalles de una cuenta de análisis de datos lago específica

    azure datalake analytics account show -g "<Azure Resource Group Name>" -n "<Data Lake Analytics Account Name>"

###<a name="delete-data-lake-analytics-accounts"></a>Eliminar cuentas de análisis de datos lago

    azure datalake analytics account delete "<Data Lake Analytics Account Name>"


<!-- ################################ -->
<!-- ################################ -->
## <a name="manage-account-data-sources"></a>Administrar orígenes de datos de la cuenta

Análisis de datos lago admite actualmente los siguientes orígenes de datos:

- [Almacén de lago de datos de Azure](../data-lake-store/data-lake-store-overview.md)
- [Almacenamiento de Azure](../storage/storage-introduction.md)

Cuando se crea una cuenta de análisis, debe designar una cuenta de almacenamiento de Azure datos lago sea la cuenta de almacenamiento. La cuenta de almacenamiento predeterminada ADL se usa para almacenar los registros de auditoría de metadatos y la tarea de trabajo. Después de haber creado una cuenta de análisis, puede agregar cuentas adicionales de almacenamiento de datos lago o cuenta de almacenamiento de Azure. 

### <a name="find-the-default-adl-storage-account"></a>Buscar la cuenta de almacenamiento predeterminada ADL

    azure datalake analytics account show "<Data Lake Analytics Account Name>"

El valor se mostrará bajo Propiedades: datalakeStoreAccount:name.

### <a name="add-additional-azure-blob-storage-accounts"></a>Agregar cuentas adicionales de almacenamiento de blobs de Windows Azure

    azure datalake analytics account datasource add -n "<Data Lake Analytics Account Name>" -b "<Azure Blob Storage Account Short Name>" -k "<Azure Storage Account Key>"

>[AZURE.NOTE] Solo las nombres cortos de las almacenamiento Blob son compatibles.  No use el nombre de dominio completo, por ejemplo "myblob.blob.core.windows.net".

### <a name="add-additional-data-lake-store-accounts"></a>Agregar cuentas adicionales de almacenamiento de datos lago

    azure datalake analytics account datasource add -n "<Data Lake Analytics Account Name>" -l "<Data Lake Store Account Name>" [-d]

[-[d] es un modificador opcional para indicar si el lago de datos que se agrega es la cuenta predeterminada de lago de datos. 

### <a name="update-existing-data-source"></a>Actualizar origen de datos existente

Para configurar una cuenta existente de almacén de lago de datos predeterminado:

    azure datalake analytics account datasource set -n "<Data Lake Analytics Account Name>" -l "<Azure Data Lake Store Account Name>" -d
      
Para actualizar una clave de cuenta de almacenamiento de blobs existente:

    azure datalake analytics account datasource set -n "<Data Lake Analytics Account Name>" -b "<Blob Storage Account Name>" -k "<New Blob Storage Account Key>"

### <a name="list-data-sources"></a>Orígenes de datos de lista:

    azure datalake analytics account show "<Data Lake Analytics Account Name>"
    
![Origen de datos de lista de análisis de datos lago](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-data-source.png)

### <a name="delete-data-sources"></a>Eliminar orígenes de datos:

Para eliminar una cuenta de la tienda de lago de datos:

    azure datalake analytics account datasource delete "<Data Lake Analytics Account Name>" "<Azure Data Lake Store Account Name>"

Para eliminar una cuenta de almacenamiento de blobs:

    azure datalake analytics account datasource delete "<Data Lake Analytics Account Name>" "<Blob Storage Account Name>"

## <a name="manage-jobs"></a>Administrar trabajos

Debe tener una cuenta de análisis de datos lago antes de poder crear una tarea.  Para obtener más información, vea [administrar el análisis de datos lago cuentas](#manage-accounts).

### <a name="list-jobs"></a>Lista tareas

    azure datalake analytics job list -n "<Data Lake Analytics Account Name>"

![Origen de datos de lista de análisis de datos lago](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-jobs.png)

### <a name="get-job-details"></a>Obtener detalles del trabajo

    azure datalake analytics job show -n "<Data Lake Analytics Account Name>" -j "<Job ID>"
    
### <a name="submit-jobs"></a>Enviar trabajos

> [AZURE.NOTE] La prioridad predeterminada de una tarea es 1000 y grado de paralelismo para una tarea predeterminado es 1.

    azure datalake analytics job create  "<Data Lake Analytics Account Name>" "<Job Name>" "<Script>"

### <a name="cancel-jobs"></a>Cancelar trabajos

Use el comando de la lista para buscar el Id. de trabajo y a continuación, utilice Cancelar para cancelar el trabajo.

    azure datalake analytics job list -n "<Data Lake Analytics Account Name>"
    azure datalake analytics job cancel "<Data Lake Analytics Account Name>" "<Job ID>"

## <a name="manage-catalog"></a>Administrar catálogo

El catálogo de SQL U se usa para estructurar los datos y el código para que se pueden compartir por secuencias de comandos SQL U. El catálogo permite el mejor rendimiento posible con datos de lago de datos de Azure. Para obtener más información, vea [usar U-SQL catálogo](data-lake-analytics-use-u-sql-catalog.md).
 
###<a name="list-catalog-items"></a>Elementos del catálogo de lista

    #List databases
    azure datalake analytics catalog list -n "<Data Lake Analytics Account Name>" -t database

    #List tables
    azure datalake analytics catalog list -n "<Data Lake Analytics Account Name>" -t table
    
Los tipos incluyen la base de datos, esquema, ensamblado, origen de datos externo, tabla, función con valores de tabla o las estadísticas de la tabla.

###<a name="create-catalog-secret"></a>Crear secreto de catálogo

    azure datalake analytics catalog secret create -n "<Data Lake Analytics Account Name>" <databaseName> <hostUri> <secretName>

### <a name="modify-catalog-secret"></a>Modificar el secreto de catálogo

    azure datalake analytics catalog secret set -n "<Data Lake Analytics Account Name>" <databaseName> <hostUri> <secretName>

###<a name="delete-catalog-secret"></a>Eliminar secreto de catálogo

    azure datalake analytics catalog secrete delete -n "<Data Lake Analytics Account Name>" <databaseName> <hostUri> <secretName>

<!-- ################################ -->
<!-- ################################ -->
## <a name="use-arm-groups"></a>Usar grupos ARM

Aplicaciones normalmente están compuestas de muchos componentes, por ejemplo una aplicación web, base de datos, el servidor de base de datos, almacenamiento y 3er servicios de terceros. Azure Resource Manager (ARM) le permite trabajar con los recursos de la aplicación como un grupo, que se conoce como un grupo de recursos de Azure. Puede implementar, actualizar, supervisar o eliminar todos los recursos para la aplicación en una única operación coordinada. Usar una plantilla para su implementación y esa plantilla puede trabajar para diferentes entornos, como pruebas, ensayo y producción. Puede aclarar facturación para su organización mediante la visualización de los costes para todo el grupo. Para obtener más información, vea [Información general del Administrador de recursos de Azure](../azure-resource-manager/resource-group-overview.md). 

Un servicio de análisis de datos lago puede incluir los siguientes componentes:

- Cuenta de análisis de lago de datos de Azure
- Cuenta de Azure datos lago almacenamiento predeterminado requerida
- Cuentas de almacenamiento adicional Azure datos lago
- Cuentas adicionales de almacenamiento de Azure

Puede crear todos estos componentes en un solo grupo ARM para facilitar la administración.

![Almacenamiento y cuenta de análisis de lago de datos de azure](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

Una cuenta de análisis de datos lago y las cuentas de almacenamiento dependientes deben situarse en el mismo centro de datos de Azure.
Sin embargo, puede encontrarse el grupo ARM en un centro de datos diferente.  


##<a name="see-also"></a>Vea también 

- [Información general de análisis de datos lago de Microsoft Azure](data-lake-analytics-overview.md)
- [Introducción a análisis de lago de datos con el Portal de Azure](data-lake-analytics-get-started-portal.md)
- [Administrar el análisis de lago de datos de Azure con el Portal de Azure](data-lake-analytics-manage-use-portal.md)
- [Supervisar y solucionar problemas de trabajos de análisis de lago de datos de Azure con el Portal de Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)


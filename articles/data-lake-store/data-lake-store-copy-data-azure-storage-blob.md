<properties
   pageTitle="Copie datos de Azure almacenamiento de Blobs en almacén de datos de lago | Microsoft Azure"
   description="Use la herramienta AdlCopy para copiar los datos de Azure almacenamiento de Blobs al almacén de datos lago"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/05/2016"
   ms.author="nitinme"/>

# <a name="copy-data-from-azure-storage-blobs-to-data-lake-store"></a>Copiar datos de Azure almacenamiento de Blobs al almacén de datos lago

> [AZURE.SELECTOR]
- [Usar DistCp](data-lake-store-copy-data-wasb-distcp.md)
- [Usar AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)

Almacén de lago de datos de Azure proporciona una herramienta de línea de comandos [AdlCopy](http://aka.ms/downloadadlcopy)para copiar los datos de los siguientes orígenes:

* Desde el almacenamiento de Azure BLOB en el almacén de datos de lago. No puede usar AdlCopy para copiar los datos del almacén de datos lago a almacenamiento Azure BLOB.

* Entre dos cuentas de almacén de lago de datos de Azure. 

Además, puede usar la herramienta de AdlCopy de dos modos diferentes:

* **Independiente**, donde la herramienta utiliza recursos de almacén de datos lago para realizar la tarea

* **Con una cuenta de análisis de datos lago**, donde las unidades asignadas a su cuenta de análisis de datos lago se usan para realizar la operación de copia. Desea utilizar esta opción si desea realizar las tareas de copia de una manera predecible.

##<a name="prerequisites"></a>Requisitos previos

Antes de comenzar este artículo, debe tener el siguiente:

- **Suscripción de un Azure**. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/pricing/free-trial/).

- Contenedor de **Azure almacenamiento de Blobs** con algunos datos.

- **Cuenta de azure datos lago análisis (opcional)** : vea [Introducción a análisis de lago de datos de Azure](../data-lake-analytics/data-lake-analytics-get-started-portal.md) para obtener instrucciones sobre cómo crear una cuenta de almacén de datos lago

- **Herramienta de AdlCopy**. Instale la herramienta de AdlCopy de [http://aka.ms/downloadadlcopy](http://aka.ms/downloadadlcopy).

## <a name="syntax-of-the-adlcopy-tool"></a>Sintaxis de la herramienta AdlCopy

Usar la siguiente sintaxis para trabajar con la herramienta AdlCopy

    AdlCopy /Source <Blob or Data Lake Store source> /Dest <Data Lake Store destination> /SourceKey <Key for Blob account> /Account <Data Lake Analytics account> /Unit <Number of Analytics units> /Pattern 

A continuación, se describen los parámetros de la sintaxis:

| Opción    | Descripción                                                                                                                                                                                                                                                                                                                                                                                                          |
|-----------|------------|
| Origen    | Especifica la ubicación del origen de datos en el blob de almacenamiento de Azure. El origen puede ser un contenedor de blob, un blob u otra cuenta de almacén de datos lago.                                                                                                                                                                                                                                                                                                 |
| Dest      | Especifica el destino de almacenamiento de datos lago copiar a.                                                                                                                                                                                                                                                                                                                                                                |
| SourceKey | Especifica la tecla de acceso de almacenamiento para el origen de blobs de Windows Azure almacenamiento. Esto es necesario únicamente si el origen es un contenedor de blob o un blob.                                                                                                                                                                                                                                                                                                                                                 |
| Cuenta   | **Opcional**. Use esta opción si desea usar la cuenta de Azure datos lago análisis para ejecutar el trabajo de copia. Si utiliza la opción Account en la sintaxis, pero no especifica una cuenta de análisis de datos lago, AdlCopy usa una cuenta predeterminada para ejecutar el trabajo. Además, si usa esta opción, debe agregar (Azure almacenamiento de blobs) de origen y de destino (almacén de lago de datos de Azure) como orígenes de datos para su cuenta de análisis de datos lago.  |
| Unidades     |  Especifica el número de unidades de análisis de lago de datos que se utilizará para el trabajo de copia. Esta opción es obligatoria si usa la opción **Account** para especificar la cuenta de análisis de datos lago.
| Trama   | Especifica un modelo de regex que indica qué BLOB o archivos que desea copiar. AdlCopy utiliza la concordancia entre mayúsculas y minúsculas. El modelo predeterminado utilizado cuando no se especifica ningún patrón es copiar todos los elementos. No se admite la especificación de varios patrones de archivo.                                                                                                                                                                                                                                                                                                                                               



## <a name="use-adlcopy-as-standalone-to-copy-data-from-an-azure-storage-blob"></a>Use AdlCopy (como independiente) para copiar los datos de un objeto binario de almacenamiento de Azure

1. Abra un símbolo del sistema y navegue hasta el directorio donde está instalado AdlCopy, normalmente `%HOMEPATH%\Documents\adlcopy`.

2. Ejecute el comando siguiente para copiar un blob específico del contenedor de origen a un almacén de lago de datos:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

    Por ejemplo:

        AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log /dest swebhdfs://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

    
    >[AZURE.NOTE] La sintaxis anterior especifica el archivo que se copiarán a una carpeta en la cuenta de almacén de datos lago. Herramienta de AdlCopy crea una carpeta si el nombre de carpeta especificado no existe.

    Se le pedirá que escriba las credenciales para la suscripción de Azure en el que tiene su cuenta de almacén de datos lago. Verá un resultado similar al siguiente:

        Initializing Copy.
        Copy Started.
        100% data copied.
        Finishing Copy.
        Copy Completed. 1 file copied.

3. También puede copiar todo el BLOB de un contenedor a la cuenta de almacén de datos lago mediante el siguiente comando:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/ /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>        

    Por ejemplo:

        AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest adl://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

## <a name="use-adlcopy-as-standalone-to-copy-data-from-another-data-lake-store-account"></a>Use AdlCopy (como independiente) para copiar los datos de otra cuenta de almacén de datos lago

También puede usar AdlCopy para mover datos entre dos cuentas de almacén de datos lago.

1. Abra un símbolo del sistema y navegue hasta el directorio donde está instalado AdlCopy, normalmente `%HOMEPATH%\Documents\adlcopy`.

2. Ejecute el comando siguiente para copiar un archivo específico de una cuenta de la tienda de lago de datos a otro.

        AdlCopy /Source adl://<source_adls_account>.azuredatalakestore.net/<path_to_file> /dest adl://<dest_adls_account>.azuredatalakestore.net/<path>/

    Por ejemplo:

        AdlCopy /Source adl://mydatastore.azuredatalakestore.net/mynewfolder/909f2b.log /dest adl://mynewdatalakestore.azuredatalakestore.net/mynewfolder/

    >[AZURE.NOTE] La sintaxis anterior especifica el archivo que se copiarán a una carpeta en la cuenta de la tienda de lago de datos de destino. Herramienta de AdlCopy crea una carpeta si el nombre de carpeta especificado no existe.

    Se le pedirá que escriba las credenciales para la suscripción de Azure en el que tiene su cuenta de almacén de datos lago. Verá un resultado similar al siguiente:

        Initializing Copy.
        Copy Started.|
        100% data copied.
        Finishing Copy.
        Copy Completed. 1 file copied.

3. El comando siguiente copia todos los archivos de una carpeta específica de la cuenta de almacén de lago de datos de origen a una carpeta de la cuenta de la tienda de lago de datos de destino.

        AdlCopy /Source adl://mydatastore.azuredatalakestore.net/mynewfolder/ /dest adl://mynewdatalakestore.azuredatalakestore.net/mynewfolder/

## <a name="use-adlcopy-with-data-lake-analytics-account-to-copy-data"></a>Utilice AdlCopy (con la cuenta de análisis de datos lago) para copiar los datos

También puede usar su cuenta de análisis de datos lago para ejecutar el trabajo AdlCopy para copiar los datos de blobs de Azure almacenamiento al almacén de datos lago. Normalmente debería usar esta opción cuando los datos que desea mover están en el intervalo de gigabytes y terabytes y desea predecible y mejor rendimiento.

Para usar su cuenta de análisis de datos lago con AdlCopy para copiar desde un Blob de almacenamiento de Azure, debe agregar el origen (Azure almacenamiento de blobs) como un origen de datos para su cuenta de análisis de datos lago. Para obtener instrucciones sobre cómo agregar orígenes de datos adicionales a su cuenta de análisis de datos lago, vea [administrar el análisis de datos lago orígenes de datos de la cuenta](..//data-lake-analytics/data-lake-analytics-manage-use-portal.md#manage-account-data-sources).

>[AZURE.NOTE] Si está copiando desde una cuenta de Azure datos lago almacén como origen con una cuenta de análisis de datos lago, no es necesario asociar la cuenta de la tienda de lago de datos con la cuenta de análisis de datos lago. El requisito para asociar el almacén de origen a la cuenta de análisis de datos lago es solo cuando el origen es una cuenta de almacenamiento de Azure.

Ejecute el comando siguiente para copiar desde un blobs de Windows Azure almacenamiento a una cuenta de almacén de datos lago mediante el análisis de datos lago cuenta:

    AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Account <data_lake_analytics_account> /Unit <number_of_data_lake_analytics_units_to_be_used>

Por ejemplo:

    AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest swebhdfs://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Account mydatalakeanalyticaccount /Units 2


Del mismo modo, ejecute el comando siguiente para copiar desde un blobs de Windows Azure almacenamiento a una cuenta de almacén de datos lago mediante el análisis de datos lago cuenta:

    AdlCopy /Source adl://mysourcedatalakestore.azuredatalakestore.net/mynewfolder/ /dest adl://mydestdatastore.azuredatalakestore.net/mynewfolder/ /Account mydatalakeanalyticaccount /Units 2

## <a name="use-adlcopy-to-copy-data-using-pattern-matching"></a>Usar AdlCopy para copiar datos utilizando la coincidencia de modelos

En esta sección, aprenda a usar AdlCopy para copiar datos de un origen (en nuestro ejemplo siguiente usamos Azure almacenamiento de blobs) a una cuenta de almacén de lago de datos de destino utilizando la coincidencia de modelos. Por ejemplo, puede usar los siguientes pasos para copiar todos los archivos con la extensión de archivo .csv de blobs de origen en el destino.

1. Abra un símbolo del sistema y navegue hasta el directorio donde está instalado AdlCopy, normalmente `%HOMEPATH%\Documents\adlcopy`.

2. Ejecute el comando siguiente para copiar todos los archivos con extensión *.csv de un blob específico del contenedor de origen a un almacén de lago de datos:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Pattern *.csv

    Por ejemplo:

        AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/FoodInspectionData/ /dest adl://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Pattern *.csv

    
## <a name="billing"></a>Facturación

* Si utiliza la herramienta AdlCopy como independiente se le facturará para los costos de salida para mover datos, si el origen de la cuenta de almacenamiento de Azure no está en la misma región como el almacén de datos de lago.

* Si utiliza la herramienta AdlCopy con su cuenta de análisis de datos lago, se aplicará estándar [tarifas de facturación de análisis de datos lago](https://azure.microsoft.com/pricing/details/data-lake-analytics/) .

## <a name="considerations-for-using-adlcopy"></a>Consideraciones sobre el uso de AdlCopy

* AdlCopy (para versión 1.0.5), es compatible con la copia de los datos de orígenes que colectivamente tienen más de miles de archivos y carpetas. Sin embargo, si tiene problemas al copiar un conjunto de datos grande, puede distribuir archivos o carpetas en subcarpetas diferentes y usar la ruta de acceso a las subcarpetas como origen en su lugar.

## <a name="next-steps"></a>Pasos siguientes

- [Proteger los datos en el almacén de datos de lago](data-lake-store-secure-data.md)
- [Usar el análisis de datos de Azure lago con el almacén de datos de lago](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Usar Azure HDInsight con el almacén de datos de lago](data-lake-store-hdinsight-hadoop-use-portal.md)

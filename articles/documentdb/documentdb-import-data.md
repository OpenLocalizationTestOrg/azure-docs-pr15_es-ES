<properties
    pageTitle="Herramienta de migración de base de datos para DocumentDB | Microsoft Azure"
    description="Obtenga información sobre cómo usar las herramientas de migración de código abierto DocumentDB datos para importar datos a DocumentDB desde diversos orígenes, incluidos los archivos de MongoDB, SQL Server, almacenamiento de tabla, DynamoDB de Amazon, CSV y JSON. CSV a la conversión de JSON."
    keywords="CSV en json, herramientas de migración de base de datos, convertir csv en json"
    services="documentdb"
    authors="andrewhoh"
    manager="jhubbard"
    editor="monicar"
    documentationCenter=""/>

<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/06/2016"
    ms.author="anhoh"/>

# <a name="import-data-to-documentdb-with-the-database-migration-tool"></a>Importar datos a DocumentDB con la herramienta de migración de base de datos

Este artículo le muestra cómo usar la herramienta de migración de datos de origen abierto oficial DocumentDB para importar datos a [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) desde varios orígenes, incluidos archivos JSON, archivos CSV, SQL, MongoDB, almacenamiento de tablas Azure, DynamoDB de Amazon y colecciones de DocumentDB.

Después de leer este artículo, podrá responder a las preguntas siguientes:  

-   ¿Cómo puedo importar archivo JSON, archivo CSV, los datos de SQL Server o datos MongoDB a DocumentDB?
-   ¿Cómo puedo importar datos de tabla de Azure almacenamiento Amazon DynamoDB y HBase a DocumentDB?
-   ¿Cómo puedo migrar datos entre colecciones de DocumentDB?

##<a id="Prerequisites"></a>Requisitos previos

Antes de seguir las instrucciones de este artículo, asegúrese de que tiene instalado lo siguiente:

- [Microsoft .NET Framework 4.51](https://www.microsoft.com/download/developer-tools.aspx) o superior.

##<a id="Overviewl"></a>Información general sobre la herramienta de migración de datos de DocumentDB

La herramienta de migración de datos de DocumentDB es una solución de Abrir origen que importa los datos a DocumentDB desde una gran variedad de orígenes, incluidos:

- Archivos JSON
- MongoDB
- SQL Server
- Archivos CSV
- Almacenamiento de tablas de Azure
- DynamoDB de Amazon
- HBase
- Colecciones DocumentDB

Mientras la herramienta de importación incluye una interfaz gráfica de usuario (dtui.exe), también se puede controlar desde la línea de comandos (dt.exe). De hecho, hay una opción para el comando asociado de salida después de configurar una importación a través de la interfaz de usuario. Tabla de origen de datos (por ejemplo, archivos CSV o de SQL Server) se puede transformar tal que se pueden crear relaciones jerárquicas (subdocumentos) durante la importación. Siga leyendo para obtener más información acerca de las opciones de fuente, las líneas de comandos para importar los resultados de cada origen, opciones de destino y la visualización de importación de ejemplo.


##<a id="Install"></a>Instalar la herramienta de migración de datos de DocumentDB

El código de origen de la herramienta de migración está disponible en GitHub en [este repositorio](https://github.com/azure/azure-documentdb-datamigrationtool) y una versión compilada está disponible desde el [Centro de descarga de Microsoft](http://www.microsoft.com/downloads/details.aspx?FamilyID=cda7703a-2774-4c07-adcc-ad02ddc1a44d). Puede compilar la solución o basta con descargar y extraer la versión compilada en un directorio de su elección. A continuación, ejecute uno:

- **Dtui.exe**: versión de la interfaz gráfica de la herramienta
- **DT.exe**: versión de línea de comandos de la herramienta

##<a id="JSON"></a>Importar archivos JSON

La opción importador origen JSON le permite importar uno o más único documento JSON archivos o JSON que contienen un conjunto de documentos JSON. Al agregar carpetas que contienen archivos JSON para importar, tiene la opción de búsqueda de archivos de subcarpetas de forma recursiva.

![Opciones de origen de archivo de captura de pantalla de JSON - herramientas de migración de base de datos](./media/documentdb-import-data/jsonsource.png)

Estos son algunos ejemplos de línea de comandos para importar archivos JSON:

    #Import a single JSON file
    dt.exe /s:JsonFile /s.Files:.\Sessions.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:Sessions /t.CollectionThroughput:2500

    #Import a directory of JSON files
    dt.exe /s:JsonFile /s.Files:C:\TESessions\*.json /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:Sessions /t.CollectionThroughput:2500

    #Import a directory (including sub-directories) of JSON files
    dt.exe /s:JsonFile /s.Files:C:\LastFMMusic\**\*.json /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:Music /t.CollectionThroughput:2500

    #Import a directory (single), directory (recursive), and individual JSON files
    dt.exe /s:JsonFile /s.Files:C:\Tweets\*.*;C:\LargeDocs\**\*.*;C:\TESessions\Session48172.json;C:\TESessions\Session48173.json;C:\TESessions\Session48174.json;C:\TESessions\Session48175.json;C:\TESessions\Session48177.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:subs /t.CollectionThroughput:2500

    #Import a single JSON file and partition the data across 4 collections
    dt.exe /s:JsonFile /s.Files:D:\\CompanyData\\Companies.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:comp[1-4] /t.PartitionKey:name /t.CollectionThroughput:2500

##<a id="MongoDB"></a>Importar desde MongoDB

La opción de importador MongoDB origen permite importar de una colección de MongoDB individual y filtrar de forma opcional documentos mediante una consulta o modificar la estructura del documento mediante una proyección.  

![Opciones de origen de captura de pantalla de MongoDB - documentdb vs mongodb](./media/documentdb-import-data/mongodbsource.png)

La cadena de conexión está en el formato de MongoDB estándar:

    mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database>

> [AZURE.NOTE] Utilice el comando comprobar para asegurarse de que puede tener acceso a la instancia de MongoDB especificada en el campo de cadena de conexión.

Escriba el nombre de la colección desde la que se va a importar los datos. Opcionalmente, puede especificar o proporcionar un archivo de una consulta (por ejemplo, {pop: {$gt: 5000}}) o proyección (por ejemplo, {loc:0}) para filtrar y dar forma a los datos que desea importar.

Estos son algunos ejemplos de línea de comandos para importar de MongoDB:

    #Import all documents from a MongoDB collection
    dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:BulkZips /t.IdField:_id /t.CollectionThroughput:2500

    #Import documents from a MongoDB collection which match the query and exclude the loc field
    dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /s.Query:{pop:{$gt:50000}} /s.Projection:{loc:0} /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:BulkZipsTransform /t.IdField:_id/t.CollectionThroughput:2500

##<a id="MongoDBExport"></a>Importar archivos de exportación de MongoDB

La opción de importador MongoDB exportación JSON archivo origen permite importar uno o más archivos JSON producidos a partir de la utilidad mongoexport.  

![Opciones de origen de exportación de captura de pantalla de MongoDB - documentdb vs mongodb](./media/documentdb-import-data/mongodbexportsource.png)

Al agregar carpetas que contienen archivos de MongoDB exportar JSON para importar, tiene la opción de búsqueda de archivos de subcarpetas de forma recursiva.

Este es un ejemplo de línea de comandos para importar de MongoDB exportar JSON archivos:

    dt.exe /s:MongoDBExport /s.Files:D:\mongoemployees.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:employees /t.IdField:_id /t.Dates:Epoch /t.CollectionThroughput:2500

##<a id="SQL"></a>Importar desde SQL Server

La opción de importador de origen SQL permite importar desde una base de datos de SQL Server individual y, opcionalmente, filtrar los registros que desea importar mediante una consulta. Además, puede modificar la estructura del documento especificando un separador de anidamiento (más en un momento).  

![Opciones de origen de captura de pantalla de SQL: herramientas de migración de base de datos](./media/documentdb-import-data/sqlexportsource.png)

El formato de la cadena de conexión es el formato de cadena de conexión de SQL estándar.

> [AZURE.NOTE] Utilice el comando comprobar para asegurarse de que puede tener acceso a la instancia de SQL Server especificada en el campo de cadena de conexión.

La propiedad de separador de anidamiento se usa para crear relaciones jerárquicas (documentos subcarpetas) durante la importación. Tenga en cuenta la siguiente consulta SQL:

*Seleccione conversión (BusinessEntityID AS varchar) como Id, nombre, tipo de dirección como [Address.AddressType], AddressLine1 como [Address.AddressLine1], ciudad como [Address.Location.City], StateProvinceName como [Address.Location.StateProvinceName], CódigoPostal como [Address.PostalCode], CountryRegionName como [Address.CountryRegionName] de Sales.vStoreWithAddresses donde tipo de dirección = 'Principal de Office'*

Que devuelve los resultados siguientes (parciales):

![Resultados de la consulta de captura de pantalla de SQL](./media/documentdb-import-data/sqlqueryresults.png)

Tenga en cuenta los alias como Address.AddressType y Address.Location.StateProvinceName. Al especificar un separador de anidamiento de '.', la herramienta de importación crea subdocumentos dirección y Address.Location durante la importación. Aquí tiene un ejemplo de un documento resultante en DocumentDB:

*{"id": "956", "nombre": "Más preciso ventas y servicio", "Dirección": {"tipo de dirección": "Principales Office", "AddressLine1": "#500 75 O'Connor calle", "Ubicación": {"Ciudad": "Ottawa", "StateProvinceName": "Ontario"}, "CódigoPostal": "K4B 1S2", "CountryRegionName": "Canadá"}}*

Estos son algunos ejemplos de línea de comandos para importar desde SQL Server:

    #Import records from SQL which match a query
    dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, * from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:Stores /t.IdField:Id /t.CollectionThroughput:2500

    #Import records from sql which match a query and create hierarchical relationships
    dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, Name, AddressType as [Address.AddressType], AddressLine1 as [Address.AddressLine1], City as [Address.Location.City], StateProvinceName as [Address.Location.StateProvinceName], PostalCode as [Address.PostalCode], CountryRegionName as [Address.CountryRegionName] from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /s.NestingSeparator:. /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:StoresSub /t.IdField:Id /t.CollectionThroughput:2500

##<a id="CSV"></a>Importar archivos CSV - CSV convertir en JSON

La opción de importador de origen de archivo CSV permite importar uno o más archivos CSV. Al agregar carpetas que contienen archivos CSV para importar, tiene la opción de búsqueda de archivos de subcarpetas de forma recursiva.

![Opciones de origen de captura de pantalla de CSV - CSV a JSON](media/documentdb-import-data/csvsource.png)

Similar al origen de SQL, la propiedad de separador de anidamiento puede utilizarse para crear relaciones jerárquicas (documentos subcarpetas) durante la importación. Tenga en cuenta la siguiente encabezado CSV fila y filas de datos:

![Registros de captura de pantalla de CSV de ejemplo - CSV a JSON](./media/documentdb-import-data/csvsample.png)

Tenga en cuenta los alias como DomainInfo.Domain_Name y RedirectInfo.Redirecting. Al especificar un separador de anidamiento de '.', la herramienta de importación creará subdocumentos DomainInfo y RedirectInfo durante la importación. Aquí tiene un ejemplo de un documento resultante en DocumentDB:

*{"DomainInfo": {"Nombre_dominio": "ACUS.GOV", "Domain_Name_Address": "http://www.ACUS.GOV"}, "Agencia Federal": "administrativo conferencia de Estados Unidos", "RedirectInfo": {"Redirigir": "0", "Redirect_Destination": ""}, "id": "9cc565c5-ebcd-1c03-ebd3-cc3e2ecd814d"}*

La herramienta de importación intentará deducir la información de tipo para valores sin comillas en archivos CSV (valores entre comillas siempre se tratan como cadenas).  Tipos de identificados en el siguiente orden: número, fecha y hora, booleano.  

Hay dos cosas Nota sobre la importación CSV:

1.  De forma predeterminada, se recortan siempre valores sin comillas por tabulaciones y espacios, mientras se conservan los valores entre comillas como-es. Puede reemplazar este comportamiento con la casilla de verificación de los valores entre comillas espacios o la opción de línea de comandos /s.TrimQuoted.

2.  De forma predeterminada, un valor null sin comillas se trata como un valor nulo. Puede reemplazar este comportamiento (es decir, tratar un valor null sin comillas como una cadena "null") con la tratar sin comillas NULL como casilla de verificación de cadena o la opción de línea de comandos /s.NoUnquotedNulls.


Este es un ejemplo de línea de comandos para importar CSV:

    dt.exe /s:CsvFile /s.Files:.\Employees.csv /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:Employees /t.IdField:EntityID /t.CollectionThroughput:2500

##<a id="AzureTableSource"></a>Importar desde el almacenamiento de tablas de Azure

La opción de tabla de Azure almacenamiento origen importador le permite importar desde una tabla de almacenamiento de Azure tabla individual y, opcionalmente, filtrar las entidades de tabla que desea importar.  

![Opciones de almacenamiento de origen de captura de pantalla de la tabla de Azure](./media/documentdb-import-data/azuretablesource.png)

El formato de la cadena de conexión de almacenamiento de Azure tabla es:

    DefaultEndpointsProtocol=<protocol>;AccountName=<Account Name>;AccountKey=<Account Key>;

> [AZURE.NOTE] Utilice el comando comprobar para asegurarse de que puede tener acceso a la instancia de almacenamiento de Azure tabla especificada en el campo de cadena de conexión.

Escriba el nombre de la tabla de Azure desde la que se va a importar los datos. Opcionalmente, puede especificar un [filtro](https://msdn.microsoft.com/library/azure/ff683669.aspx).

La opción de tabla de Azure almacenamiento origen importador tiene las siguientes opciones adicionales:

1. Incluir campos internos
    2. Todo: incluir todos los campos internos (PartitionKey, RowKey y marca de tiempo)
    3. Ninguno: excluir todos los campos internos
    4. RowKey - incluir solo el campo RowKey
3. Seleccionar columnas
    1. Filtros de almacenamiento de Azure tabla no admiten proyecciones. Si desea importar sólo las propiedades específicas de entidad de tabla de Azure, agregarlos a la lista Seleccionar columnas. Se omitirán todas las demás propiedades de la entidad.

Este es un ejemplo de línea de comandos para importar de almacenamiento de tablas de Azure:

    dt.exe /s:AzureTable /s.ConnectionString:"DefaultEndpointsProtocol=https;AccountName=<Account Name>;AccountKey=<Account Key>" /s.Table:metrics /s.InternalFields:All /s.Filter:"PartitionKey eq 'Partition1' and RowKey gt '00001'" /s.Projection:ObjectCount;ObjectSize  /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:metrics /t.CollectionThroughput:2500

##<a id="DynamoDBSource"></a>Importar desde DynamoDB de Amazon

La opción de Amazon DynamoDB origen importador le permite importar desde una tabla de Amazon DynamoDB individual y, opcionalmente, filtrar las entidades que desea importar. Se proporcionan varias plantillas para que la configuración de importación es tan fácil como sea posible.

![Opciones de origen de captura de pantalla de Amazon DynamoDB - herramientas de migración de base de datos](./media/documentdb-import-data/dynamodbsource1.png)

![Opciones de origen de captura de pantalla de Amazon DynamoDB - herramientas de migración de base de datos](./media/documentdb-import-data/dynamodbsource2.png)

El formato de la cadena de conexión de Amazon DynamoDB es:

    ServiceURL=<Service Address>;AccessKey=<Access Key>;SecretKey=<Secret Key>;

> [AZURE.NOTE] Utilice el comando comprobar para asegurarse de que puede tener acceso a la instancia de Amazon DynamoDB especificada en el campo de cadena de conexión.

Este es un ejemplo de línea de comandos para importar de Amazon DynamoDB:

    dt.exe /s:DynamoDB /s.ConnectionString:ServiceURL=https://dynamodb.us-east-1.amazonaws.com;AccessKey=<accessKey>;SecretKey=<secretKey> /s.Request:"{   """TableName""": """ProductCatalog""" }" /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:catalogCollection /t.CollectionThroughput:2500

##<a id="BlobImport"></a>Importar archivos de almacenamiento de blobs de Windows Azure

El archivo JSON, archivo de exportación de MongoDB y opciones de importador de origen de archivo CSV permiten importar uno o más archivos de almacenamiento de blobs de Windows Azure. Después de especificar una dirección URL de contenedor de blobs de Windows y la clave de cuenta, simplemente proporcionar una expresión regular para seleccionar los archivos para importar.

![Opciones de archivo de origen de captura de pantalla de blobs](./media/documentdb-import-data/blobsource.png)

Aquí tiene muestra de línea de comandos para importar archivos JSON de almacenamiento de blobs de Windows Azure:

    dt.exe /s:JsonFile /s.Files:"blobs://<account key>@account.blob.core.windows.net:443/importcontainer/.*" /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:doctest

##<a id="DocumentDBSource"></a>Importar desde DocumentDB

La opción de importador de origen DocumentDB le permite importar datos de una o varias colecciones de DocumentDB y filtrar de forma opcional documentos mediante una consulta.  

![Opciones de origen de captura de pantalla de DocumentDB](./media/documentdb-import-data/documentdbsource.png)

El formato de la cadena de conexión de DocumentDB es:

    AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;

DocumentDB se puede recuperar la cadena de conexión de cuenta desde el módulo de teclas del portal de Azure, como se describe en [cómo administrar una cuenta de DocumentDB](documentdb-manage-account.md), pero el nombre de la base de datos necesita se anexa a la cadena de conexión en el siguiente formato:

    Database=<DocumentDB Database>;

> [AZURE.NOTE] Utilice el comando comprobar para asegurarse de que puede tener acceso a la instancia de DocumentDB especificada en el campo de cadena de conexión.

Para importar desde una única colección DocumentDB, escriba el nombre de la colección desde la que se va a importar los datos. Para importar desde varias colecciones de DocumentDB, proporciona una expresión regular para que coincida con uno o más nombres de la colección (por ejemplo, collection01 | collection02 | collection03). Opcionalmente, puede especificar o proporcionar un archivo para una consulta para filtrar y forma de los datos que desea importar.

> [AZURE.NOTE] Dado que el campo colección acepta expresiones regulares, si va a importar desde una única colección cuyo nombre contiene caracteres de expresiones regulares, esos caracteres deben ser escape según corresponda.

La opción de DocumentDB origen importador tiene las siguientes opciones avanzadas:

1. Incluir campos internos: Especifica si desea incluir propiedades del sistema de documento DocumentDB en la exportación (por ejemplo, _rid, _ts) o no.
2. Número de reintentos de error: especifica el número de reintentos de la conexión a DocumentDB en caso de errores transitorias (por ejemplo, interrupción de conectividad de red).
3. Intervalo entre intentos: Especifica cuánto tiempo debe esperar entre reintentar la conexión a DocumentDB en caso de errores transitorias (por ejemplo, interrupción de conectividad de red).
4. Modo de conexión: Especifica el modo de conexión para usar con DocumentDB. Las opciones disponibles son DirectTcp, DirectHttps y puerta de enlace. Los modos de conexión directa son más rápidos, mientras que el modo de puerta de enlace es firewall más descriptivo, como solo utiliza el puerto 443.

![Origen de captura de pantalla de DocumentDB opciones avanzadas](./media/documentdb-import-data/documentdbsourceoptions.png)

> [AZURE.TIP] La herramienta de importación de forma predeterminada el modo de conexión DirectTcp. Si experimenta problemas de firewall, cambie al modo de conexión puerta de enlace, como sólo requiere el puerto 443.


Estos son algunos ejemplos de línea de comandos para importar desde DocumentDB:

    #Migrate data from one DocumentDB collection to another DocumentDB collections
    dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /s.Collection:TEColl /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:TESessions /t.CollectionThroughput:2500

    #Migrate data from multiple DocumentDB collections to a single DocumentDB collection
    dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /s.Collection:comp1|comp2|comp3|comp4 /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:singleCollection /t.CollectionThroughput:2500

    #Export a DocumentDB collection to a JSON file
    dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /s.Collection:StoresSub /t:JsonFile /t.File:StoresExport.json /t.Overwrite /t.CollectionThroughput:2500

##<a id="HBaseSource"></a>Importar desde HBase

La opción de importador de origen HBase le permite importar datos de una tabla de HBase y, opcionalmente, filtrar los datos. Se proporcionan varias plantillas para que la configuración de importación es tan fácil como sea posible.

![Opciones de origen de captura de pantalla de HBase](./media/documentdb-import-data/hbasesource1.png)

![Opciones de origen de captura de pantalla de HBase](./media/documentdb-import-data/hbasesource2.png)

El formato de la cadena de conexión HBase Stargate es:

    ServiceURL=<server-address>;Username=<username>;Password=<password>

> [AZURE.NOTE] Utilice el comando comprobar para asegurarse de que puede tener acceso a la instancia de HBase especificada en el campo de cadena de conexión.

Este es un ejemplo de línea de comandos para importar desde HBase:

    dt.exe /s:HBase /s.ConnectionString:ServiceURL=<server-address>;Username=<username>;Password=<password> /s.Table:Contacts /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:hbaseimport

##<a id="DocumentDBBulkTarget"></a>Importar a DocumentDB (importación masiva)

El importador DocumentDB masiva le permite importar desde cualquiera de las opciones de origen disponibles, con un procedimiento almacenado de DocumentDB para la eficiencia. Es compatible con la herramienta de importación para una colección de DocumentDB particiones único, así como importar sharded según la cual se dividen los datos a través de varias colecciones de DocumentDB particiones único. Para obtener más información acerca de particiones de datos, vea [particiones y escala en DocumentDB de Azure](documentdb-partition-data.md). La herramienta crear, ejecutar y, a continuación, eliminar el procedimiento almacenado de las colecciones de destino.  

![Opciones de masa de captura de pantalla de DocumentDB](./media/documentdb-import-data/documentdbbulk.png)

El formato de la cadena de conexión de DocumentDB es:

    AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;

DocumentDB se puede recuperar la cadena de conexión de cuenta desde el módulo de teclas del portal de Azure, como se describe en [cómo administrar una cuenta de DocumentDB](documentdb-manage-account.md), pero el nombre de la base de datos necesita se anexa a la cadena de conexión en el siguiente formato:

    Database=<DocumentDB Database>;

> [AZURE.NOTE] Utilice el comando comprobar para asegurarse de que puede tener acceso a la instancia de DocumentDB especificada en el campo de cadena de conexión.

Para importar a una sola colección, escriba el nombre de la colección en la que los datos se importarán y haga clic en el botón Agregar. Para importar a varias colecciones de, escriba el nombre de cada colección individualmente o use la siguiente sintaxis para especificar varias colecciones: *collection_prefix*[índice inicial - índice final]. Al especificar varias colecciones a través de la sintaxis de la mencionadas, tenga en cuenta lo siguiente:

1. Tramas entero rango nombre solo son compatibles. Por ejemplo, especificar la colección [0-3] generarán las siguientes colecciones: collection0, collection1, collection2, collection3.
2. Puede usar una sintaxis abreviada: colección [3] emite el mismo conjunto de colecciones mencionada en el paso 1.
3. Se puede proporcionar más de una sustitución. Por ejemplo, colección [0-1] [0-9] generará nombres de la colección de 20 con ceros (collection01,... 02... 03).

Una vez que se han especificado los nombres de la colección, seleccione el rendimiento de las colecciones (400 RUs 10.000 RUs) que desee. Para mejorar el rendimiento de importación, elija un rendimiento más alto. Para obtener más información acerca de los niveles de rendimiento, vea [niveles de rendimiento en DocumentDB](documentdb-performance-levels.md).

> [AZURE.NOTE] La configuración de rendimiento de rendimiento solo se aplica a la creación de la colección. Si la colección especificada ya existe, no se modificará el rendimiento.

Cuando importa a varias colecciones, el hash de es compatible con la herramienta de importación según sharding. En este escenario, especifique la propiedad de documento que desea usar como la clave de partición (si la clave de partición se deja en blanco, documentos será sharded aleatoriamente a través de las colecciones de destino).

Opcionalmente, puede especificar qué campo del origen de importación se debe utilizar como la propiedad de identificador de documento DocumentDB durante la importación (tenga en cuenta que si los documentos no contienen esta propiedad, a continuación, la herramienta de importación generará un GUID como el valor de propiedad id).

Existen varias opciones avanzadas durante la importación. En primer lugar, mientras la herramienta incluye una masa predeterminada importar procedimiento almacenado (BulkInsert.js), puede especificar su propio procedimiento almacenado de importación:

 ![Opción de procedimiento almacenado de inserción masiva de captura de pantalla de DocumentDB](./media/documentdb-import-data/bulkinsertsp.png)

Además, cuando se importan los tipos de fecha (por ejemplo, de SQL Server o MongoDB), puede elegir entre tres opciones de importación:

 ![Opciones de importación de captura de pantalla de DocumentDB fecha hora](./media/documentdb-import-data/datetimeoptions.png)

-   Cadena: Se mantiene como un valor de cadena
-   Tiempo: Se mantiene como un valor de número de tiempo
-   Ambas: Conservar la cadena y los valores numéricos de tiempo. Esta opción creará un subdocumento, por ejemplo: "date_joined": {"Valor": "2013-10-21T21:17:25.2410000Z", "tiempo": 1382390245}


El importador DocumentDB masiva tiene las siguientes adicionales, opciones avanzadas:

1. Tamaño del lote: Valores predeterminados de la herramienta a un tamaño de lote de 50.  Si los documentos que desea importar están grandes, considere la posibilidad de reducir el tamaño del lote. Por el contrario, si los documentos que desea importar son pequeños, considere la posibilidad de elevar el tamaño del lote.
2. Tamaño máximo de Script (bytes): valores predeterminados de la herramienta a un tamaño máximo de la secuencia de comandos de 512KB
3. Deshabilitar la generación automática de identificador: Si todos los documentos que se importarán contiene un campo id, a continuación, seleccionar esta opción puede aumentar el rendimiento. Falta un campo identificador exclusivo de documentos no se importará.
4. Documentos existentes de actualización: La herramienta predeterminado no reemplacen documentos existentes con conflictos de id. Al seleccionar esta opción permite sobrescribir documentos existentes con identificadores de coincidencia. Esta característica es útil para las migraciones de datos programada que actualización los documentos existentes.
5. Número de reintentos de error: especifica el número de reintentos de la conexión a DocumentDB en caso de errores transitorias (por ejemplo, interrupción de conectividad de red).
6. Intervalo entre intentos: Especifica cuánto tiempo debe esperar entre reintentar la conexión a DocumentDB en caso de errores transitorias (por ejemplo, interrupción de conectividad de red).
7. Modo de conexión: Especifica el modo de conexión para usar con DocumentDB. Las opciones disponibles son DirectTcp, DirectHttps y puerta de enlace. Los modos de conexión directa son más rápidos, mientras que el modo de puerta de enlace es firewall más descriptivo, como solo utiliza el puerto 443.

![Importación masiva de captura de pantalla de DocumentDB opciones avanzadas](./media/documentdb-import-data/docdbbulkoptions.png)

> [AZURE.TIP] La herramienta de importación de forma predeterminada el modo de conexión DirectTcp. Si experimenta problemas de firewall, cambie al modo de conexión puerta de enlace, como sólo requiere el puerto 443.

##<a id="DocumentDBSeqTarget"></a>Importar a DocumentDB (registro secuencial importar)

El importador de registro secuencial DocumentDB le permite importar desde cualquiera de las opciones de origen disponibles en una base de registro por registro. Puede elegir esta opción si va a importar a una colección existente que ha alcanzado su cuota de procedimientos almacenados. La herramienta permite importar a una sola colección DocumentDB, así como sharded importar según el cual los datos se dividen en múltiples colecciones DocumentDB partición único o múltiples partición (única partición y con varias particiones). Para obtener más información acerca de particiones de datos, vea [particiones y escala en DocumentDB de Azure](documentdb-partition-data.md).

![Captura de pantalla de DocumentDB opciones de importación de registros secuenciales](./media/documentdb-import-data/documentdbsequential.png)

El formato de la cadena de conexión de DocumentDB es:

    AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;

DocumentDB se puede recuperar la cadena de conexión de cuenta desde el módulo de teclas del portal de Azure, como se describe en [cómo administrar una cuenta de DocumentDB](documentdb-manage-account.md), pero el nombre de la base de datos necesita se anexa a la cadena de conexión en el siguiente formato:

    Database=<DocumentDB Database>;

> [AZURE.NOTE] Utilice el comando comprobar para asegurarse de que puede tener acceso a la instancia de DocumentDB especificada en el campo de cadena de conexión.

Para importar a una sola colección, escriba el nombre de la colección en la que los datos se importarán y haga clic en el botón Agregar. Para importar a varias colecciones de, escriba el nombre de cada colección individualmente o use la siguiente sintaxis para especificar varias colecciones: *collection_prefix*[índice inicial - índice final]. Al especificar varias colecciones a través de la sintaxis de la mencionadas, tenga en cuenta lo siguiente:

1. Tramas entero rango nombre solo son compatibles. Por ejemplo, especificar la colección [0-3] generarán las siguientes colecciones: collection0, collection1, collection2, collection3.
2. Puede usar una sintaxis abreviada: colección [3] emite el mismo conjunto de colecciones mencionada en el paso 1.
3. Se puede proporcionar más de una sustitución. Por ejemplo, colección [0-1] [0-9] generará nombres de la colección de 20 con ceros (collection01,... 02... 03).

Una vez que se han especificado los nombres de la colección, seleccione el rendimiento de las colecciones (400 RUs 250.000 RUs) que desee. Para mejorar el rendimiento de importación, elija un rendimiento más alto. Para obtener más información acerca de los niveles de rendimiento, vea [niveles de rendimiento en DocumentDB](documentdb-performance-levels.md). Cualquier importación a las colecciones de rendimiento > 10.000 RUs requerirá una clave de partición. Si opta por que más de 250.000 RUs, consulte [Solicitar aumente los límites de cuenta DocumentDB](documentdb-increase-limits.md).

> [AZURE.NOTE] La configuración de rendimiento solo se aplica a la creación de la colección. Si la colección especificada ya existe, no se modificará el rendimiento.

Cuando importa a varias colecciones, el hash de es compatible con la herramienta de importación según sharding. En este escenario, especifique la propiedad de documento que desea usar como la clave de partición (si la clave de partición se deja en blanco, documentos será sharded aleatoriamente a través de las colecciones de destino).

Opcionalmente, puede especificar qué campo del origen de importación se debe utilizar como la propiedad de identificador de documento DocumentDB durante la importación (tenga en cuenta que si los documentos no contienen esta propiedad, a continuación, la herramienta de importación generará un GUID como el valor de propiedad id).

Existen varias opciones avanzadas durante la importación. En primer lugar, cuando se importan los tipos de fecha (por ejemplo, de SQL Server o MongoDB), puede elegir entre tres opciones de importación:

 ![Opciones de importación de captura de pantalla de DocumentDB fecha hora](./media/documentdb-import-data/datetimeoptions.png)

-   Cadena: Se mantiene como un valor de cadena
-   Tiempo: Se mantiene como un valor de número de tiempo
-   Ambas: Conservar la cadena y los valores numéricos de tiempo. Esta opción creará un subdocumento, por ejemplo: "date_joined": {"Valor": "2013-10-21T21:17:25.2410000Z", "tiempo": 1382390245}

DocumentDB - importador registro secuencial tiene las siguientes opciones avanzadas adicionales:

1. Número de solicitudes paralelas: la herramienta de valores predeterminados para solicitudes paralelas 2. Si los documentos que desea importar son pequeños, considere la posibilidad de elevar el número de solicitudes paralelas. Tenga en cuenta que si este número se eleva demasiado, la importación, puede experimentar limitación.
2. Deshabilitar la generación automática de identificador: Si todos los documentos que se importarán contiene un campo id, a continuación, seleccionar esta opción puede aumentar el rendimiento. Falta un campo identificador exclusivo de documentos no se importará.
3. Documentos existentes de actualización: La herramienta predeterminado no reemplacen documentos existentes con conflictos de id. Al seleccionar esta opción permite sobrescribir documentos existentes con identificadores de coincidencia. Esta característica es útil para las migraciones de datos programada que actualización los documentos existentes.
4. Número de reintentos de error: especifica el número de reintentos de la conexión a DocumentDB en caso de errores transitorias (por ejemplo, interrupción de conectividad de red).
5. Intervalo entre intentos: Especifica cuánto tiempo debe esperar entre reintentar la conexión a DocumentDB en caso de errores transitorias (por ejemplo, interrupción de conectividad de red).
6. Modo de conexión: Especifica el modo de conexión para usar con DocumentDB. Las opciones disponibles son DirectTcp, DirectHttps y puerta de enlace. Los modos de conexión directa son más rápidos, mientras que el modo de puerta de enlace es firewall más descriptivo, como solo utiliza el puerto 443.

![Importación de registro secuencial de captura de pantalla de DocumentDB opciones avanzadas](./media/documentdb-import-data/documentdbsequentialoptions.png)

> [AZURE.TIP] La herramienta de importación de forma predeterminada el modo de conexión DirectTcp. Si experimenta problemas de firewall, cambie al modo de conexión puerta de enlace, como sólo requiere el puerto 443.

##<a id="IndexingPolicy"></a>Especificar una directiva de indexación al crear colecciones DocumentDB

Al permitir la herramienta de migración crear colecciones durante la importación, puede especificar la directiva de indización de las colecciones. En Opciones avanzadas de la sección de masa DocumentDB importar y DocumentDB secuenciales opciones de registro, vaya a la sección de la directiva de indización.

![Captura de pantalla de DocumentDB indización directivas Opciones avanzadas](./media/documentdb-import-data/indexingpolicy1.png)

Mediante la directiva de indización opción avanzada, puede seleccionar un archivo de directiva indización, manualmente especificar una directiva de indexación o seleccione un conjunto de plantillas predeterminadas (haciendo clic en el cuadro de texto de directiva indización).

La herramienta proporciona las plantillas de directiva son:

- De forma predeterminada. Esta directiva es mejor realizar consultas de igualdad de cadenas y no usa ORDER BY, rango y consultas de igualdad para los números. Esta directiva tiene una sobrecarga de almacenamiento de índice inferior rango.
- Rango. Esta directiva es mejor que usa consultas ORDER BY, rango e igualdad en cadenas y números. Esta directiva tiene una sobrecarga de almacenamiento de índice mayor que predeterminado o Hash.


![Captura de pantalla de DocumentDB indización directivas Opciones avanzadas](./media/documentdb-import-data/indexingpolicy2.png)

> [AZURE.NOTE] Si no especifica una directiva de indexación, se aplicará la directiva predeterminada. Para obtener más información acerca de las directivas de indexación, consulte [DocumentDB directivas de indexación](documentdb-indexing-policies.md).


## <a name="export-to-json-file"></a>Exportar a archivo JSON

El exportador DocumentDB JSON permite exportar cualquiera de las opciones de origen disponibles en un archivo JSON que contiene una matriz de documentos JSON. La herramienta controlará la exportación para usted, o puede elegir ver el comando resultante de migración y ejecute el comando usted mismo. El archivo JSON resultante puede almacenarse localmente o en el almacenamiento de blobs de Windows Azure.

![Opción de exportación de captura de pantalla de DocumentDB JSON archivo local](./media/documentdb-import-data/jsontarget.png)

![Opción de exportación de almacenamiento de blobs captura de pantalla de DocumentDB JSON Azure](./media/documentdb-import-data/jsontarget2.png)

Opcionalmente, puede optar por prettify el JSON resultante, lo que aumentará el tamaño del documento resultante mientras realiza el contenido más legible.

    Standard JSON export
    [{"id":"Sample","Title":"About Paris","Language":{"Name":"English"},"Author":{"Name":"Don","Location":{"City":"Paris","Country":"France"}},"Content":"Don's document in DocumentDB is a valid JSON document as defined by the JSON spec.","PageViews":10000,"Topics":[{"Title":"History of Paris"},{"Title":"Places to see in Paris"}]}]

    Prettified JSON export
    [
    {
    "id": "Sample",
    "Title": "About Paris",
    "Language": {
      "Name": "English"
    },
    "Author": {
      "Name": "Don",
      "Location": {
        "City": "Paris",
        "Country": "France"
      }
    },
    "Content": "Don's document in DocumentDB is a valid JSON document as defined by the JSON spec.",
    "PageViews": 10000,
    "Topics": [
      {
        "Title": "History of Paris"
      },
      {
        "Title": "Places to see in Paris"
      }
    ]
    }]

## <a name="advanced-configuration"></a>Configuración avanzada

En la pantalla de configuración avanzada, especifique la ubicación del archivo de registro al que desea que los errores escritos. Las reglas siguientes se aplican a esta página:

1.  Si no se proporciona un nombre de archivo, se devolverá todos los errores en la página de resultados.
2.  Si no se proporciona un nombre de archivo sin un directorio, a continuación, el archivo se esté creado (o sobrescribir) en el directorio actual del entorno.
3.  Si selecciona una existente archivo y, a continuación, el archivo se pierde, no hay ninguna opción de datos anexados.

A continuación, elija si desea registrar todos, críticas, o ningún mensaje de error. Por último, decidir con qué frecuencia se actualizará el mensaje de transferencia en pantalla con su progreso.

    ![Screenshot of Advanced configuration screen](./media/documentdb-import-data/AdvancedConfiguration.png)

## <a name="confirm-import-settings-and-view-command-line"></a>Confirmar la configuración de importación y la línea de comandos de vista

1. Después de especificar información de origen, la información de destino y la configuración avanzada, revise la migración de resumen y, opcionalmente, vista o copiar el comando resultante de migración (copiar el comando es útil para automatizar las operaciones de importación):

    ![Captura de pantalla de resumen](./media/documentdb-import-data/summary.png)

    ![Captura de pantalla de resumen](./media/documentdb-import-data/summarycommand.png)

2. Cuando esté satisfecho con las opciones de origen y destino, haga clic en **Importar**. El tiempo transcurrido, el recuento transferido y la información de error (si no ha proporcionado un nombre de archivo en la configuración avanzada) se actualizarán según la importación se encuentra en proceso. Una vez completado, puede exportar los resultados (por ejemplo, para solucionar los errores de importación).

    ![Opción de exportación de captura de pantalla de DocumentDB JSON](./media/documentdb-import-data/viewresults.png)

3. También puede iniciar una nueva importación, mantener la configuración existente (por ejemplo, opción de información de origen y de destino de la cadena de conexión, etc.) o restablecer todos los valores.

    ![Opción de exportación de captura de pantalla de DocumentDB JSON](./media/documentdb-import-data/newimport.png)

## <a name="next-steps"></a>Pasos siguientes

- Para obtener más información sobre DocumentDB, vea la [Ruta de aprendizaje](https://azure.microsoft.com/documentation/learning-paths/documentdb/).

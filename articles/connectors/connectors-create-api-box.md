<properties
    pageTitle="Agregar el conector cuadro a sus aplicaciones de lógica | Microsoft Azure"
    description="Información general sobre el conector de cuadro con los parámetros de la API de REST"
    services=""
    documentationCenter="" 
    authors="MandiOhlinger"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="08/18/2016"
   ms.author="mandia"/>

# <a name="get-started-with-the-box-connector"></a>Empezar a trabajar con el conector de cuadro
Conectarse al cuadro y crear archivos, eliminar archivos y mucho más. 

>[AZURE.NOTE] Esta versión de este artículo se aplica a la versión del esquema de 2015-08-01-vista previa de aplicaciones de lógica.

Cuadro, puede:

- Crear el flujo de negocio basándose en los datos que recibe del cuadro. 
- Usar desencadenadores cuando se crea o actualiza un archivo.
- Usar acciones que copiar un archivo, eliminar un archivo y mucho más. Estas acciones obtienen una respuesta y, a continuación, hacer que el resultado esté disponible para otras acciones. Por ejemplo, cuando se modifica un archivo en el cuadro, puede tomar ese archivo y envíelo por correo electrónico con Office 365.

Para agregar una operación en las aplicaciones de lógica, consulte [crear una aplicación de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Desencadenadores y acciones
Cuadro incluye los siguientes desencadenador y acciones.

| Desencadenadores | Acciones|
| --- | --- |
|<ul><li>Cuando se crea un archivo</li><li>Cuando se modifica un archivo</li></ul> | <ul><li>Crear archivo</li><li>Cuando se crea un archivo</li><li>Copiar archivo</li><li>Eliminar archivo</li><li>Extraer las archive a carpeta</li><li>Obtener el contenido del archivo con el Id.</li><li>Obtener el contenido del archivo mediante la ruta de acceso</li><li>Obtener los metadatos de archivo con el Id.</li><li>Obtener los metadatos de archivo mediante la ruta de acceso</li><li>Actualizar archivo</li><li>Cuando se modifica un archivo</li></ul>

Todos los conectores admiten datos en formatos XML y JSON.

## <a name="create-a-connection-to-box"></a>Crear una conexión al cuadro
Cuando agrega este conector a sus aplicaciones de lógica, debe autorizar aplicaciones lógica para conectarse a su cuadro.

>[AZURE.INCLUDE [Steps to create a connection to box](../../includes/connectors-create-api-box.md)]

Después de crear la conexión, escriba las propiedades del cuadro. La **referencia de la API de REST** en este tema se describen las propiedades.

>[AZURE.TIP] Puede utilizar esta misma conexión de cuadro en otras aplicaciones de lógica.

## <a name="swagger-rest-api-reference"></a>Referencia de la API de REST de swagger
Se aplica a la versión: 1.0.

### <a name="create-file"></a>Crear archivo
Carga un archivo en el cuadro.  
```POST: /datasets/default/files```

| Nombre|Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|folderPath|cadena|Sí|consulta|Ninguno |Cargue el archivo en el cuadro ruta de la carpeta|
|nombre|cadena|Sí|consulta|Ninguno |Nombre del archivo que desea crear en el cuadro|
|cuerpo|String(Binary) |Sí|cuerpo|Ninguno |Contenido del archivo para cargar en el cuadro|

#### <a name="response"></a>Respuesta
|Nombre|Descripción|
|---|---|
|200|Vale|
|predeterminado|Error en la operación.|


### <a name="when-a-file-is-created"></a>Cuando se crea un archivo
Activa un flujo cuando se crea un nuevo archivo en una carpeta del cuadro.  
```GET: /datasets/default/triggers/onnewfile```

| Nombre|Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|ID carpeta|cadena|Sí|consulta|Ninguno |Identificador único de la carpeta en el cuadro|

#### <a name="response"></a>Respuesta
|Nombre|Descripción|
|---|---|
|200|Vale|
|predeterminado|Error en la operación.|


### <a name="copy-file"></a>Copiar archivo
Copia un archivo al cuadro.  
```POST: /datasets/default/copyFile```

| Nombre|Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|origen|cadena|Sí|consulta|Ninguno |Dirección URL de archivo de origen|
|destino|cadena|Sí|consulta| Ninguno|Ruta de acceso de archivo de destino en el cuadro de nombre de archivo de destino|
|sobrescribir|valor booleano|No|consulta| Ninguno|Si se sobrescribe el archivo de destino establecido en 'true'|

#### <a name="response"></a>Respuesta
|Nombre|Descripción|
|---|---|
|200|Vale|
|predeterminado|Error en la operación.|


### <a name="delete-file"></a>Eliminar archivo
Elimina un archivo del cuadro.  
```DELETE: /datasets/default/files/{id}```


| Nombre|Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|Id.|cadena|Sí|ruta de acceso|Ninguno |Identificador único del archivo que desea eliminar del cuadro|

#### <a name="response"></a>Respuesta
|Nombre|Descripción|
|---|---|
|200|Vale|
|predeterminado|Error en la operación.|


### <a name="extract-archive-to-folder"></a>Extraer las archive a carpeta
Extrae un archivo en una carpeta en el cuadro (ejemplo: .zip).  
```POST: /datasets/default/extractFolderV2```

| Nombre|Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|origen|cadena|Sí|consulta| |Ruta de acceso al archivo de almacenamiento|
|destino|cadena|Sí|consulta| |Ruta de acceso en el cuadro para extraer el contenido del archivo|
|sobrescribir|valor booleano|No|consulta| |Si se sobrescribe los archivos de destino establecido en 'true'|

#### <a name="response"></a>Respuesta
|Nombre|Descripción|
|---|---|
|200|Vale|
|predeterminado|Error en la operación.|


### <a name="get-file-content-using-id"></a>Obtener el contenido del archivo con el Id.
Recupera contenido del archivo de cuadro mediante id.  
```GET: /datasets/default/files/{id}/content```

| Nombre|Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|Id.|cadena|Sí|ruta de acceso|Ninguno |Identificador único del archivo en el cuadro|

#### <a name="response"></a>Respuesta
|Nombre|Descripción|
|---|---|
|200|Vale|
|predeterminado|Error en la operación.|


### <a name="get-file-content-using-path"></a>Obtener el contenido del archivo mediante la ruta de acceso
Recupera el contenido del archivo de cuadro mediante la ruta de acceso.  
```GET: /datasets/default/GetFileContentByPath```

| Nombre|Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|ruta de acceso|cadena|Sí|consulta|Ninguno |Ruta de acceso única al archivo en el cuadro|

#### <a name="response"></a>Respuesta
|Nombre|Descripción|
|---|---|
|200|Vale|
|predeterminado|Error en la operación.|


### <a name="get-file-metadata-using-id"></a>Obtener los metadatos de archivo con el Id.
Recupera los metadatos de archivo de cuadro mediante el identificador de archivo.  
```GET: /datasets/default/files/{id}```

| Nombre|Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|Id.|cadena|Sí|ruta de acceso| Ninguno|Identificador único del archivo en el cuadro|

#### <a name="response"></a>Respuesta
|Nombre|Descripción|
|---|---|
|200|Vale|
|predeterminado|Error en la operación.|


### <a name="get-file-metadata-using-path"></a>Obtener los metadatos de archivo mediante la ruta de acceso
Recupera los metadatos de archivo de cuadro mediante la ruta de acceso.  
```GET: /datasets/default/GetFileByPath```

| Nombre|Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|ruta de acceso|cadena|Sí|consulta|Ninguno |Ruta de acceso única al archivo en el cuadro|

#### <a name="response"></a>Respuesta
|Nombre|Descripción|
|---|---|
|200|Vale|
|predeterminado|Error en la operación.|


### <a name="update-file"></a>Actualizar archivo
Actualiza un archivo en el cuadro.  
```PUT: /datasets/default/files/{id}```

| Nombre|Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|Id.|cadena|Sí|ruta de acceso| Ninguno|Identificador único del archivo de actualización en el cuadro|
|cuerpo|String(Binary) |Sí|cuerpo|Ninguno |Contenido del archivo que desea actualizar en el cuadro|

#### <a name="response"></a>Respuesta
|Nombre|Descripción|
|---|---|
|200|Vale|
|predeterminado|Error en la operación.|


### <a name="when-a-file-is-modified"></a>Cuando se modifica un archivo
Activa un flujo cuando se modifica un archivo en una carpeta del cuadro.  
```GET: /datasets/default/triggers/onupdatedfile```

| Nombre|Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|ID carpeta|cadena|Sí|consulta|Ninguno |Identificador único de la carpeta en el cuadro|

#### <a name="response"></a>Respuesta
|Nombre|Descripción|
|---|---|
|200|Vale|
|predeterminado|Error en la operación.|


## <a name="object-definitions"></a>Definiciones de objeto

#### <a name="datasetsmetadata"></a>DataSetsMetadata

|Nombre de propiedad | Tipo de datos | Obligatorio|
|---|---|---|
|tabular|no definido|No|
|BLOB|no definido|No|

#### <a name="tabulardatasetsmetadata"></a>TabularDataSetsMetadata

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|origen|cadena|No|
|displayName|cadena|No|
|urlEncoding|cadena|No|
|tableDisplayName|cadena|No|
|tablePluralName|cadena|No|

#### <a name="blobdatasetsmetadata"></a>BlobDataSetsMetadata

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|origen|cadena|No|
|displayName|cadena|No|
|urlEncoding|cadena|No|

#### <a name="blobmetadata"></a>BlobMetadata

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|Id.|cadena|No|
|Nombre|cadena|No|
|DisplayName|cadena|No|
|Ruta de acceso|cadena|No|
|LastModified|cadena|No|
|Tamaño|entero|No|
|Tipo de medio|cadena|No|
|IsFolder|valor booleano|No|
|ETag|cadena|No|
|FileLocator|cadena|No|

## <a name="next-steps"></a>Pasos siguientes

[Crear una aplicación de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

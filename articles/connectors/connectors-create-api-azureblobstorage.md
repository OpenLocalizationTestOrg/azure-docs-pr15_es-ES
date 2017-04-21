<properties
    pageTitle="Agregar el almacenamiento de blobs de Windows Azure conector en las aplicaciones de lógica | Microsoft Azure"
    description="Información general sobre el almacenamiento de blobs de Windows Azure conector con parámetros de la API de REST"
    services=""
    documentationCenter="" 
    authors="MandiOhlinger"
    manager="anneta"
    editor=""
    tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="10/18/2016"
   ms.author="mandia"/>

# <a name="get-started-with-the-azure-blob-storage-connector"></a>Empezar a trabajar con el conector de almacenamiento de blobs de Windows Azure
Almacenamiento de blobs de Windows Azure es un servicio para almacenar grandes cantidades de datos no estructurados. Realizar varias acciones, como la carga, actualizar, obtener y eliminar BLOB en el almacenamiento de blobs de Windows Azure. 

Con el almacenamiento de blobs de Windows Azure,:

- Crear un flujo de trabajo por cargar nuevos proyectos u obtener archivos que se han actualizado recientemente.
- Usar acciones para obtener los metadatos de archivo, eliminar un archivo, copiar archivos y mucho más. Por ejemplo, cuando se actualiza una herramienta en un sitio web de Azure (un desencadenador), actualizar un archivo en el almacenamiento de blobs (una acción). 

Este tema muestra cómo usar el conector de almacenamiento de blobs de Windows en una aplicación de lógica y también enumera las acciones.

>[AZURE.NOTE] Esta versión de este artículo se aplica a la disponibilidad de aplicaciones de lógica general (GA). 

Para obtener más información acerca de la lógica de aplicaciones, vea [¿qué aplicaciones de lógica](../app-service-logic/app-service-logic-what-are-logic-apps.md) y [crear una aplicación de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="connect-to-azure-blob-storage"></a>Conectarse al almacenamiento de blobs de Windows Azure

Antes de la aplicación de lógica puede tener acceso a cualquier servicio, primero crear una *conexión* al servicio. Una conexión proporciona conectividad entre una aplicación de lógica y otro servicio. Por ejemplo, para conectarse a una cuenta de almacenamiento, primero crear una *conexión*del almacenamiento de blobs de Windows. Para crear una conexión, escriba las credenciales a que normalmente se utilizan para tener acceso al servicio que se está conectando. Con el almacenamiento de Azure, indique las credenciales de su cuenta de almacenamiento para crear la conexión. 

#### <a name="create-the-connection"></a>Crear la conexión

>[AZURE.INCLUDE [Create a connection to Azure blob storage](../../includes/connectors-create-api-azureblobstorage.md)]
 
## <a name="use-a-trigger"></a>Use un desencadenador

Este conector tener desencadenadores. Usar otros desencadenadores para iniciar la aplicación de la lógica, como un desencadenador de periodicidad, un desencadenador HTTP Webhook, desencadenadores disponibles con otros conectores y mucho más. [Crear una aplicación de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md) proporciona un ejemplo.

## <a name="use-an-action"></a>Usar una acción
    
Una acción es una operación que lleva a cabo por el flujo de trabajo que se definen en una aplicación de lógica.

1. Seleccione el signo más. Ver varias opciones: **Agregar una acción**, **Agregar una condición**o una de las opciones **más** .

    ![](./media/connectors-create-api-azureblobstorage/add-action.png)

2. Elija **Agregar una acción**.

3. En el cuadro de texto, escriba "blob" para obtener una lista de todas las acciones disponibles.

    ![](./media/connectors-create-api-azureblobstorage/actions.png) 

4. En nuestro ejemplo, elija **AzureBlob - obtener metadatos de archivo mediante la ruta de acceso**. Si ya existe una conexión, a continuación, seleccione **...** Botón (Mostrar selector) para seleccionar un archivo.

    ![](./media/connectors-create-api-azureblobstorage/sample-file.png)

    Si se le pide la información de conexión, a continuación, escriba los detalles para crear la conexión. [Crear la conexión](connectors-create-api-azureblobstorage.md#create-the-connection) en este tema se describen las propiedades. 

    > [AZURE.NOTE] En este ejemplo, obtenemos los metadatos de un archivo. Para ver los metadatos, agregue otras acciones que se crea un nuevo archivo con otro conector. Por ejemplo, agregar una acción de OneDrive que crea un nuevo archivo "prueba" basándose en los metadatos. 

5. **Guardar** los cambios (esquina superior izquierda de la barra de herramientas). La aplicación de la lógica se guarda y se puede activar automáticamente.

> [AZURE.TIP] [Explorador de almacenamiento](http://storageexplorer.com/) es una excelente herramienta para administrar varias cuentas de almacenamiento.

## <a name="technical-details"></a>Detalles técnicos

## <a name="storage-blob-actions"></a>Acciones de blobs de almacenamiento

|Acción|Descripción|
|--- | ---|
|[Obtener los metadatos de archivo](connectors-create-api-azureblobstorage.md#get-file-metadata)|Esta operación obtiene metadatos de archivo con el identificador de archivo.|
|[Actualizar archivo](connectors-create-api-azureblobstorage.md#update-file)|Esta operación actualiza un archivo.|
|[Eliminar archivo](connectors-create-api-azureblobstorage.md#delete-file)|Esta operación elimina un archivo.|
|[Obtener los metadatos de archivo mediante la ruta de acceso](connectors-create-api-azureblobstorage.md#get-file-metadata-using-path)|Esta operación obtiene metadatos de archivo mediante la ruta de acceso.|
|[Obtener el contenido del archivo mediante la ruta de acceso](connectors-create-api-azureblobstorage.md#get-file-content-using-path)|Esta operación obtiene el contenido del archivo con la ruta de acceso.|
|[Obtener el contenido del archivo](connectors-create-api-azureblobstorage.md#get-file-content)|Esta operación obtiene el contenido del archivo con el id.|
|[Crear archivo](connectors-create-api-azureblobstorage.md#create-file)|Esta operación carga un archivo.|
|[Copiar archivo](connectors-create-api-azureblobstorage.md#copy-file)|Esta operación copia un archivo en el almacenamiento de blobs de Windows Azure.|
|[Extraer las archive a carpeta](connectors-create-api-azureblobstorage.md#extract-archive-to-folder)|Esta operación extrae un archivo en una carpeta (ejemplo: .zip).|

### <a name="action-details"></a>Detalles de acción

En esta sección, vea los detalles específicos acerca de cada acción, incluidas las propiedades de entrada opcionales o requeridas y ningún resultado correspondiente asociado con el conector.

#### <a name="get-file-metadata"></a>Obtener los metadatos de archivo
Esta operación obtiene metadatos de archivo con el identificador de archivo.  

|Nombre de propiedad| Nombre para mostrar|Descripción|
| ---|---|---|
|Id *|Archivo|Seleccione un archivo|

Un asterisco (*) significa que la propiedad es necesaria.

##### <a name="output-details"></a>Detalles de salida
BlobMetadata

| Nombre de propiedad | Tipo de datos |
|---|---|
|Id.|cadena|
|Nombre|cadena|
|DisplayName|cadena|
|Ruta de acceso|cadena|
|LastModified|cadena|
|Tamaño|entero|
|Tipo de medio|cadena|
|IsFolder|valor booleano|
|ETag|cadena|
|FileLocator|cadena|


#### <a name="update-file"></a>Actualizar archivo
Esta operación actualiza un archivo.  

|Nombre de propiedad| Nombre para mostrar|Descripción|
| ---|---|---|
|Id *|Archivo|Seleccione un archivo|
|cuerpo *|Contenido del archivo|Contenido del archivo al actualizar|

Un asterisco (*) significa que la propiedad es necesaria.

##### <a name="output-details"></a>Detalles de salida
BlobMetadata

| Nombre de propiedad | Tipo de datos |
|---|---|
|Id.|cadena|
|Nombre|cadena|
|DisplayName|cadena|
|Ruta de acceso|cadena|
|LastModified|cadena|
|Tamaño|entero|
|Tipo de medio|cadena|
|IsFolder|valor booleano|
|ETag|cadena|
|FileLocator|cadena|


#### <a name="delete-file"></a>Eliminar archivo
Esta operación elimina un archivo.  

|Nombre de propiedad| Nombre para mostrar|Descripción|
| ---|---|---|
|Id *|Archivo|Seleccione un archivo|

Un asterisco (*) significa que la propiedad es necesaria.

##### <a name="output-details"></a>Detalles de salida
Ninguno.


#### <a name="get-file-metadata-using-path"></a>Obtener los metadatos de archivo mediante la ruta de acceso
Esta operación obtiene metadatos de archivo mediante la ruta de acceso.  

|Nombre de propiedad| Nombre para mostrar|Descripción|
| ---|---|---|
|ruta de acceso *|Ruta de acceso de archivo|Seleccione un archivo|

Un asterisco (*) significa que la propiedad es necesaria.

##### <a name="output-details"></a>Detalles de salida
BlobMetadata

| Nombre de propiedad | Tipo de datos |
|---|---|
|Id.|cadena|
|Nombre|cadena|
|DisplayName|cadena|
|Ruta de acceso|cadena|
|LastModified|cadena|
|Tamaño|entero|
|Tipo de medio|cadena|
|IsFolder|valor booleano|
|ETag|cadena|
|FileLocator|cadena|


#### <a name="get-file-content-using-path"></a>Obtener el contenido del archivo mediante la ruta de acceso
Esta operación obtiene el contenido del archivo con la ruta de acceso.  

|Nombre de propiedad| Nombre para mostrar|Descripción|
| ---|---|---|
|ruta de acceso *|Ruta de acceso de archivo|Seleccione un archivo|

Un asterisco (*) significa que la propiedad es necesaria.

##### <a name="output-details"></a>Detalles de salida
Ninguno.


#### <a name="get-file-content"></a>Obtener el contenido del archivo
Esta operación obtiene el contenido del archivo con el id.  

|Nombre de propiedad| Tipo de datos|Descripción|
| ---|---|---|
|Id *|cadena|Seleccione un archivo|

Un asterisco (*) significa que la propiedad es necesaria.

##### <a name="output-details"></a>Detalles de salida
Ninguno.


#### <a name="create-file"></a>Crear archivo
Esta operación carga un archivo.  

|Nombre de propiedad| Nombre para mostrar|Descripción|
| ---|---|---|
|folderPath *|Ruta de la carpeta|Seleccione una carpeta|
|nombre *|Nombre de archivo|Nombre de archivo para cargar|
|cuerpo *|Contenido del archivo|Contenido del archivo para cargar|

Un asterisco (*) significa que la propiedad es necesaria.

##### <a name="output-details"></a>Detalles de salida
BlobMetadata

| Nombre de propiedad | Tipo de datos | 
|---|---|
|Id.|cadena|
|Nombre|cadena|
|DisplayName|cadena|
|Ruta de acceso|cadena|
|LastModified|cadena|
|Tamaño|entero|
|Tipo de medio|cadena|
|IsFolder|valor booleano|
|ETag|cadena|
|FileLocator|cadena|


#### <a name="copy-file"></a>Copiar archivo
Esta operación copia un archivo en el almacenamiento de blobs de Windows Azure.  

|Nombre de propiedad| Nombre para mostrar|Descripción|
| ---|---|---|
|origen *|Dirección url de origen|Especificar dirección Url al archivo de origen|
|destino *|Ruta de acceso de archivo de destino|Especificar la ruta de acceso del archivo de destino, incluyendo el nombre de archivo de destino|
|sobrescribir|¿Sobrescribir?|¿Un archivo de destino existente se debe sobrescribir (verdadero o falso)?  |

Un asterisco (*) significa que la propiedad es necesaria.

##### <a name="output-details"></a>Detalles de salida
BlobMetadata

| Nombre de propiedad | Tipo de datos |
|---|---|
|Id.|cadena|
|Nombre|cadena|
|DisplayName|cadena|
|Ruta de acceso|cadena|
|LastModified|cadena|
|Tamaño|entero|
|Tipo de medio|cadena|
|IsFolder|valor booleano|
|ETag|cadena|
|FileLocator|cadena|

#### <a name="extract-archive-to-folder"></a>Extraer las archive a carpeta
Esta operación extrae un archivo en una carpeta (ejemplo: .zip).  

|Nombre de propiedad| Nombre para mostrar|Descripción|
| ---|---|---|
|origen *|Ruta de archivo del archivo de origen|Seleccione un archivo de almacenamiento|
|destino *|Ruta de la carpeta de destino|Seleccionar el contenido para extraer|
|sobrescribir|¿Sobrescribir?|¿Un archivo de destino existente se debe sobrescribir (verdadero o falso)?|

Un asterisco (*) significa que la propiedad es necesaria.

##### <a name="output-details"></a>Detalles de salida
BlobMetadata

| Nombre de propiedad | Tipo de datos |
|---|---|
|Id.|cadena|
|Nombre|cadena|
|DisplayName|cadena|
|Ruta de acceso|cadena|
|LastModified|cadena|
|Tamaño|entero|
|Tipo de medio|cadena|
|IsFolder|valor booleano|
|ETag|cadena|
|FileLocator|cadena|


## <a name="http-responses"></a>Respuestas HTTP

Cuando llame a las distintas acciones, obtendrá determinadas respuestas. La siguiente tabla describe las respuestas y sus descripciones:  

|Nombre|Descripción|
|---|---|
|200|Vale|
|202|Aceptado|
|400|Solicitud incorrecta|
|401|No autorizado|
|403|Prohibido|
|404|No se encontró|
|500|Error de servidor interno. Error desconocido|
|predeterminado|Error en la operación.|

## <a name="next-steps"></a>Pasos siguientes

[Crear una aplicación de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md). Explore los otros conectores disponibles en las aplicaciones de lógica en nuestra [lista de API](apis-list.md).




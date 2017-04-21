
<properties
    pageTitle="Agregar el HTTP + Swagger acción en las aplicaciones de lógica | Microsoft Azure"
    description="Información general del HTTP + Swagger acción y operaciones"
    services=""
    documentationCenter=""
    authors="jeffhollan"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/18/2016"
   ms.author="jehollan"/>

# <a name="get-started-with-the-http--swagger-action"></a>Introducción a la HTTP + Swagger acción

Con el HTTP + Swagger acción, puede crear un conector de primera clase a cualquier extremo del resto a través de un [documento de Swagger](https://swagger.io). También puede extender una aplicación lógica para llamar a cualquier extremo del resto con una experiencia de diseñador de la aplicación de lógica de primera clase.

Para empezar con el HTTP + Swagger acción en una aplicación de la lógica, consulte [crear una nueva aplicación de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

---

## <a name="use-http--swagger-as-a-trigger-or-an-action"></a>Usar HTTP + Swagger como un desencadenador o una acción

El HTTP + Swagger desencadenador y acción funcionan del mismo modo que la [acción HTTP](connectors-native-http.md) pero proporcionar una mejor experiencia de diseño mostrando la forma de las API y los resultados en el Diseñador de la [Swagger metadatos](https://swagger.io). Además, puede usar HTTP + Swagger como un desencadenador. Si desea implementar un desencadenador de sondeo, debe seguir el modelo de sondeo que se describe en la [creación de una API personalizada para usarlas con aplicaciones de lógica](../app-service-logic/app-service-logic-create-api-app.md#polling-triggers).

[Más información sobre desencadenadores de aplicación lógica y acciones.](connectors-overview.md)

Aquí es un ejemplo de cómo usar HTTP + operación Swagger como una acción en un flujo de trabajo en una aplicación de lógica.

1. Seleccione el botón **Nuevo paso** .
2. Seleccione **Agregar una acción**.
3. En el cuadro de búsqueda de la acción, escriba **swagger** a lista HTTP + Swagger acción.

    ![Seleccione HTTP + Swagger acción](./media/connectors-native-http-swagger/using-action-1.png)

4. Escriba la dirección URL de un documento de Swagger:
    - Para trabajar en el Diseñador de lógica de aplicación, la dirección URL debe ser un extremo HTTPS y ha habilitado la CORS.
    - Si el documento Swagger no cumple este requisito, puede usar [El almacenamiento de Azure con CORS habilitado](#hosting-swagger-from-storage) para almacenar el documento.
5. Haga clic en **siguiente** para leer y representar del documento Swagger.
6. Agregar todos los parámetros necesarios para la llamada HTTP.

    ![Finalizar la acción de HTTP](./media/connectors-native-http-swagger/using-action-2.png)

1. Haga clic en **Guardar** en la esquina superior izquierda de la barra de herramientas y la lógica aplicación ahorrará ambos y publicar (activar).

### <a name="host-swagger-from-azure-storage"></a>Swagger de host de almacenamiento de Azure

Desea hacer referencia a un documento de Swagger que no está alojado o que no cumple los requisitos de origen de la cruz para el diseñador y seguridad. Para resolver este problema, puede almacenar el documento Swagger en el almacenamiento de Azure y habilitar CORS hacer referencia al documento.  

Estos son los pasos para crear, configurar y almacenar documentos Swagger en el almacenamiento de Azure:

1. [Crear una cuenta de almacenamiento de Azure con el almacenamiento de blobs de Windows Azure](../storage/storage-create-storage-account.md). (Para ello, establezca permisos acceso **público**).
2. Habilitar CORS en el blob. Puede usar [esta secuencia de comandos de PowerShell](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1) para configurar dicha configuración automáticamente.
3. Cargue el archivo Swagger en el objeto binario. Puede hacerlo desde el [portal de Azure](https://portal.azure.com) o desde una herramienta como el [Explorador de almacenamiento de Azure](http://storageexplorer.com/).
1. Hacer referencia a un vínculo HTTPS para el documento en el almacenamiento de blobs de Windows Azure. (El vínculo sigue el formato `https://*storageAccountName*.blob.core.windows.net/*container*/*filename*`.)



## <a name="technical-details"></a>Detalles técnicos

A continuación se muestran los detalles de los desencadenadores y las acciones que este HTTP + Swagger conector admite.

## <a name="http--swagger-triggers"></a>HTTP + Swagger desencadenadores

Un desencadenador es un evento que se pueden usar para iniciar el flujo de trabajo que se define en una aplicación de lógica. [Más información sobre desencadenadores.](connectors-overview.md) El HTTP + Swagger conector tiene un desencadenador.

|Desencadenador|Descripción|
|---|---|
|HTTP + Swagger|Realizar una llamada de HTTP y devolver el contenido de respuesta|

## <a name="http--swagger-actions"></a>HTTP + Swagger acciones

Una acción es una operación que se lleva a cabo por el flujo de trabajo que se define en una aplicación de lógica. [Más información acerca de las acciones.](connectors-overview.md) El HTTP + Swagger conector tiene una posible acción.

|Acción|Descripción|
|---|---|
|HTTP + Swagger|Realizar una llamada de HTTP y devolver el contenido de respuesta|

### <a name="action-details"></a>Detalles de acción

El HTTP + Swagger conector se suministra con una posible acción. A continuación se muestra información sobre cada una de las acciones, los campos de entrada obligatorios y opcionales y los detalles de salida correspondientes que están asociados a su uso.

#### <a name="http--swagger"></a>HTTP + Swagger

Realizar una solicitud de salida de HTTP con asistencia de metadatos de Swagger.
Un asterisco (*) significa un campo obligatorio.

|Nombre para mostrar|Nombre de propiedad|Descripción|
|---|---|---|
|Método *|método|Verbo HTTP para usar.|
|URI *|URI|URI de la solicitud HTTP.|
|Encabezados|encabezados|Objeto JSON de encabezados HTTP para incluir.|
|Cuerpo|cuerpo|Cuerpo de la solicitud HTTP.|
|Autenticación|autenticación|Autenticación para la solicitud. [Para obtener más detalles, vea HTTP](./connectors-native-http.md#authentication).|

**Detalles de salida**

Respuesta HTTP

|Nombre de propiedad|Tipo de datos|Descripción|
|---|---|---|
|Encabezados|objeto|Encabezados de respuesta|
|Cuerpo|objeto|Objeto de respuesta|
|Código de estado|int|Código de estado HTTP|

### <a name="http-responses"></a>Respuestas HTTP

Al realizar llamadas a varias acciones, que obtendría determinadas respuestas. A continuación se muestra una tabla que describe las descripciones y las respuestas correspondientes.

|Nombre|Descripción|
|---|---|
|200|Vale|
|202|Aceptado|
|400|Solicitud incorrecta|
|401|No autorizado|
|403|Prohibido|
|404|No se encontró|
|500|Error de servidor interno. Se produjo un error desconocido.|

---

## <a name="next-steps"></a>Pasos siguientes

Pruebe la plataforma y [crear una aplicación de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md) ahora. Puede explorar los otros conectores disponibles en las aplicaciones de lógica consultando nuestra [lista de API](apis-list.md).

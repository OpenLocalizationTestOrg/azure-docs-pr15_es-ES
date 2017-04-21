<properties
    pageTitle="Agregar la acción de HTTP en las aplicaciones de lógica | Microsoft Azure"
    description="Información general sobre la acción HTTP con propiedades"
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
   ms.date="07/15/2016"
   ms.author="jehollan"/>

# <a name="get-started-with-the-http-action"></a>Introducción a la acción de HTTP

Con la acción HTTP, puede ampliar los flujos de trabajo para su organización y comunicarse con cualquier extremo a través de HTTP.

Puedes:

- Crear lógica de flujos de trabajo de aplicación que activan (desencadenador) cuando un sitio Web que se encarga de administrar deja de funcionar.
- Comunicar a cualquier extremo sobre HTTP ocupen los flujos de trabajo de otros servicios.

Para empezar a usar la acción HTTP en una aplicación de lógica, consulte [crear una aplicación de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="use-the-http-trigger"></a>Utilice el desencadenador HTTP

Un desencadenador es un evento que se pueden usar para iniciar el flujo de trabajo que se define en una aplicación de lógica. [Obtenga más información acerca de desencadenadores](connectors-overview.md).

Aquí tiene una secuencia de ejemplo de cómo configurar el desencadenador HTTP en el Diseñador de lógica de aplicación.

1. Agregar el desencadenador HTTP en la aplicación de la lógica.
2. Rellene los parámetros del extremo de HTTP que desea sondear.
3. Modificar el intervalo de repetición en la frecuencia con la debería sondear.
4. La aplicación de la lógica se aplica ahora con cualquier contenido que se devuelve durante la comprobación de cada.

![Desencadenador HTTP](./media/connectors-native-http/using-trigger.png)

### <a name="how-the-http-trigger-works"></a>Cómo funciona el desencadenador HTTP

El desencadenador HTTP realiza una llamada a un extremo HTTP en un intervalo periódico. De forma predeterminada, las respuestas HTTP código menos de 300 resultados en una aplicación de lógica ejecutar. Puede agregar una condición en la vista código que se evaluará después de la llamada HTTP para determinar si debe activar la aplicación de la lógica. Aquí tenemos un ejemplo de un desencadenador de HTTP que se activa siempre que el código de estado devuelto es mayor o igual a `400`.

```javascript
"Http":
{
    "conditions": [
        {
            "expression": "@greaterOrEquals(triggerOutputs()['statusCode'], 400)"
        }
    ],
    "inputs": {
        "method": "GET",
        "uri": "https://blogs.msdn.microsoft.com/logicapps/",
        "headers": {
            "accept-language": "en"
        }
    },
    "recurrence": {
        "frequency": "Second",
        "interval": 15
    },
    "type": "Http"
}
```

Obtener más detalles acerca de los parámetros de desencadenador HTTP están disponibles en [MSDN](https://msdn.microsoft.com/library/azure/mt643939.aspx#HTTP-trigger).

## <a name="use-the-http-action"></a>Usar la acción HTTP

Una acción es una operación que se lleva a cabo por el flujo de trabajo que se define en una aplicación de lógica. [Más información acerca de las acciones](connectors-overview.md).

1. Seleccione el botón **Nuevo paso** .
2. Elija **Agregar una acción**.
3. En el cuadro de búsqueda de la acción, escriba **http** para mostrar la acción HTTP.

    ![Seleccione la acción de HTTP](./media/connectors-native-http/using-action-1.png)

4. Agregar todos los parámetros necesarios para la llamada HTTP.

    ![Completar la acción de HTTP](./media/connectors-native-http/using-action-2.png)

5. Haga clic en la esquina superior izquierda de la barra de herramientas para guardar. La aplicación de lógica guardar y publicar (activar).

## <a name="http-trigger"></a>Desencadenador HTTP

Estos son los detalles del desencadenador que admita este conector. El conector HTTP tiene un desencadenador.

|Desencadenador|Descripción|
|---|---|
|HTTP|Realiza una llamada HTTP y devuelve el contenido de la respuesta.|

## <a name="http-action"></a>Acción de HTTP

Estos son los detalles de la acción que admita este conector. El conector HTTP tiene una posible acción.

|Acción|Descripción|
|---|---|
|HTTP|Realiza una llamada HTTP y devuelve el contenido de la respuesta.|

## <a name="http-details"></a>Detalles HTTP

Las tablas siguientes describen los campos de entrada obligatorios y opcionales para la acción y los detalles de salida correspondientes que están asociados con el uso de la acción.


#### <a name="http-request"></a>Solicitud HTTP
Los siguientes son campos de entrada de la acción que realiza una solicitud HTTP saliente.
A * significa que es un campo obligatorio.

|Nombre para mostrar|Nombre de propiedad|Descripción|
|---|---|---|
|Método *|método|El verbo HTTP usar|
|URI *|URI|La URI de la solicitud HTTP|
|Encabezados|encabezados|Objeto JSON de encabezados HTTP para incluir|
|Cuerpo|cuerpo|El cuerpo de la solicitud HTTP|
|Autenticación|autenticación|Detalles de la sección de [autenticación](#authentication)|
<br>

#### <a name="output-details"></a>Detalles de salida

Los siguientes son los detalles de resultados de la respuesta HTTP.

|Nombre de propiedad|Tipo de datos|Descripción|
|---|---|---|
|Encabezados|objeto|Encabezados de respuesta|
|Cuerpo|objeto|Objeto de respuesta|
|Código de estado|int|Código de estado HTTP|

## <a name="authentication"></a>Autenticación

La característica de aplicaciones de la lógica de servicio de la aplicación de Azure le permite usar diferentes tipos de autenticación de los extremos HTTP. Puede utilizar esta autenticación con los conectores **HTTP**, **[HTTP + Swagger](./connectors-native-http-swagger.md)**y **[HTTP Webhook](./connectors-native-webhook.md)** . Los siguientes tipos de autenticación están configurables:

* [Autenticación básica](#basic-authentication)
* [Autenticación de certificado de cliente](#client-certificate-authentication)
* [Autenticación de Azure Active Directory (AD Azure) OAuth](#azure-active-directory-oauth-authentication)

#### <a name="basic-authentication"></a>Autenticación básica

El siguiente objeto de autenticación es necesario para la autenticación básica.
A * significa que es un campo obligatorio.

|Nombre de propiedad|Tipo de datos|Descripción|
|---|---|---|
|Tipo *|tipo|Tipo de autenticación (debe ser `Basic` para la autenticación básica)|
|Nombre de usuario *|nombre de usuario|Nombre de usuario para autenticar|
|Contraseña *|contraseña|Contraseña para la autenticación|

>[AZURE.TIP] Si desea usar una contraseña que no se puede recuperar la definición, use un `securestring` parámetro y la `@parameters()` [función de la definición de flujo de trabajo](http://aka.ms/logicappdocs).

Así que crearía un objeto como este en el campo de autenticación:

```javascript
{
    "type": "Basic",
    "username": "user",
    "password": "test"
}
```

#### <a name="client-certificate-authentication"></a>Autenticación de certificado de cliente

El siguiente objeto de autenticación es necesario para la autenticación de certificado de cliente. A * significa que es un campo obligatorio.

|Nombre de propiedad|Tipo de datos|Descripción|
|---|---|---|
|Tipo *|tipo|El tipo de autenticación (debe ser `ClientCertificate` de certificados SSL de cliente)|
|PFX *|PFX|El contenido codificado en base 64 del archivo de intercambio de información Personal (PFX)|
|Contraseña *|contraseña|La contraseña para tener acceso al archivo PFX|

>[AZURE.TIP] Puede usar un `securestring` parámetro y la `@parameters()` [función de la definición de flujo de trabajo](http://aka.ms/logicappdocs) para utilizar un parámetro que no se pueden leer en la definición después de guardar la aplicación lógica.

Por ejemplo:

```javascript
{
    "type": "ClientCertificate",
    "pfx": "aGVsbG8g...d29ybGQ=",
    "password": "@parameters('myPassword')"
}
```

#### <a name="azure-ad-oauth-authentication"></a>Autenticación de Azure AD OAuth

El siguiente objeto de autenticación es necesario para la autenticación de Azure AD OAuth. A * significa que es un campo obligatorio.

|Nombre de propiedad|Tipo de datos|Descripción|
|---|---|---|
|Tipo *|tipo|El tipo de autenticación (debe ser `ActiveDirectoryOAuth` de Azure AD OAuth)|
|Inquilino *|inquilino|El identificador del inquilino del inquilino de Azure AD|
|Audiencia *|audiencia|Establecer`https://management.core.windows.net/`|
|Cliente ID *|clientId|El identificador de cliente de la aplicación de Azure AD|
|Secreto *|secreto|El secreto del cliente que solicita el token|

>[AZURE.TIP] Puede usar un `securestring` parámetro y la `@parameters()` [función de la definición de flujo de trabajo](http://aka.ms/logicappdocs) para utilizar un parámetro que no se pueden leer en la definición después de guardar.

Por ejemplo:

```javascript
{
    "type": "ActiveDirectoryOAuth",
    "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47",
    "audience": "https://management.core.windows.net/",
    "clientId": "34750e0b-72d1-4e4f-bbbe-664f6d04d411",
    "secret": "hcqgkYc9ebgNLA5c+GDg7xl9ZJMD88TmTJiJBgZ8dFo="
}
```

## <a name="next-steps"></a>Pasos siguientes

Ahora, pruebe la plataforma y [crear una aplicación de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md). Puede explorar los otros conectores disponibles en las aplicaciones de lógica consultando nuestra [lista de API](apis-list.md).

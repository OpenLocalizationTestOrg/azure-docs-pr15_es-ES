<properties 
    pageTitle="Cómo delegar la suscripción de producto y registro de usuario" 
    description="Obtenga información sobre cómo delegar la suscripción del producto y registro de usuario a un tercero en administración de la API de Azure." 
    services="api-management" 
    documentationCenter="" 
    authors="antonba" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="antonba"/>

# <a name="how-to-delegate-user-registration-and-product-subscription"></a>Cómo delegar la suscripción de producto y registro de usuario

Delegación le permite usar su sitio Web existente de administración de desarrollador signo-en/Cerrar sesión-up y suscripción de productos en lugar de usar la funcionalidad integrada en el portal de programadores. Esto permite a su sitio Web para el propietario de los datos de usuario y realizar la validación de estos pasos de forma personalizada.

## <a name="delegate-signin-up"> </a>Delegar programador inicio de sesión y suscripción

Delegar programador inicio de sesión y suscripción al sitio Web existente, que debe crear un extremo de delegación especial en su sitio que actúa como punto de entrada para la solicitud iniciado desde el portal de administración de la API de desarrollador.

El flujo de trabajo final será como sigue:

1. Programador clics del ratón en el vínculo de inicio de sesión o inicio de sesión en el portal de administración de la API de desarrollador
2. Explorador se redirige al extremo delegación
3. Delegación de redirige a o extremo presenta pedir a inicio de sesión o de suscripción a un usuario de la interfaz de usuario
4. En caso de éxito, el usuario se redirige a la página del portal programador API administración que inicia desde


Para empezar, vamos a primera configuración API administración para enrutar solicitudes a través de su punto final de la delegación. En el portal de administración de la API de publisher, haga clic en **seguridad** y, a continuación, haga clic en la ficha **delegación** . Haga clic en la casilla de verificación para habilitar a 'Delegado inicio de sesión y suscripción'.

![Página de la delegación][api-management-delegation-signin-up]

* Decida qué la dirección URL del extremo delegación especial se y escriba en el campo **dirección URL del extremo de la delegación** . 

* Dentro de la **clave de autenticación de delegación en el** campo Escriba un secreto que se usará para calcular una firma provista de comprobación para asegurarse de que la solicitud de hecho procede de la administración de la API de Azure. Puede hacer clic en el botón **Generar** para tiene API Managemnet generar una clave de forma aleatoria por usted.

Ahora debe crear el **extremo de la delegación**. Tiene que realizar varias acciones:

1. Recibir una solicitud en la forma siguiente:

    > *http://www.yourwebsite.com/apimdelegation?operation=SignIn&returnUrl= {URL de la página de origen} & sal = {cadena} & sig = {cadena}*

    Parámetros de consulta para el caso de inicio de sesión / registro:
    - **operación**: identifica qué tipo de delegación solicitarlo es: sólo se puede **iniciar sesión** en este caso
    - **returnUrl**: la dirección URL de la página donde el usuario hace clic en un vínculo de inicio de sesión o suscripción
    - **salt**: una cadena salt especial que se usan para calcular un hash de seguridad
    - **SIG**: hash a calcular un hash calculado de seguridad que se usará para comparación a su propio

2. Compruebe que la solicitud procede de la administración de la API de Azure (opcional, pero muy recomendable para seguridad)

    * Calcular un valor hash SHA512 HMAC de una cadena según los parámetros de consulta **returnUrl** y **salt** ([código de ejemplo que se proporcionan a continuación]):
        > HMAC (**salt** '\n' + **returnUrl**)
         
    * Comparar el hash encima calcula el valor del parámetro de consulta **sig** . Si los dos valores coinciden, pasar al siguiente paso, en caso contrario, rechazar la solicitud.

2. Comprobar que se recibe una solicitud de inicio de sesión, en/Inicio de sesión y arriba: el parámetro de consulta de la **operación** se establecerá en "**Inicio de sesión**".

3. Presentar el usuario con la interfaz de usuario para iniciar sesión o suscripción

4. Si el usuario es la firma de seguridad debe crear una cuenta correspondiente para ellos en administración de la API. [Crear un usuario] con la API de REST API de administración. Al hacerlo, asegúrese de configurar el identificador de usuario al mismo está en el almacén de usuario o a un identificador que puede realizar un seguimiento de.

5. Cuando el usuario se autentica correctamente:

    * [Solicitar un símbolo de sesión único (SSO)] a través de la API de REST API de administración

    * anexar un parámetro de consulta returnUrl a la dirección URL de SSO que ha recibido de la llamada a la API anteriormente:
        > Por ejemplo, https://customer.portal.azure-api.net/signin-sso?token&returnUrl=/return/url 

    * redirigir al usuario a la dirección URL producida anterior

Además de la operación de **Inicio de sesión** , también puede realizar la administración de cuentas siguiendo los pasos anteriores y usando una de las siguientes operaciones.

-   **ChangePassword**
-   **ChangeProfile**
-   **CloseAccount**

Debe pasar los siguientes parámetros de consulta para operaciones de administración de cuenta.

-   **operación**: identifica qué tipo de solicitud de delegación es (ChangePassword, ChangeProfile o CloseAccount)
-   **ID de usuario**: el identificador de usuario de la cuenta para administrar
-   **salt**: una cadena salt especial que se usan para calcular un hash de seguridad
-   **SIG**: hash a calcular un hash calculado de seguridad que se usará para comparación a su propio

## <a name="delegate-product-subscription"> </a>Delegar la suscripción del producto

Delegar la suscripción del producto funciona de manera similar a delegar usuario inicio de sesión o de seguridad. El flujo de trabajo final sería como sigue:

1. Programador selecciona un producto en el portal de administración de la API programador y haga clic en el botón suscribirse
2. Explorador se redirige al extremo delegación
3. Extremo de delegación lleva a cabo operaciones de suscripción de producto requiere - esto depende del usuario y puede implicar redirigir a otra página para solicitar información de facturación, formular preguntas adicionales, o simplemente almacenar la información y no se requiere ninguna acción del usuario


Para habilitar la funcionalidad, en **la página** , haga clic en **suscripción de producto de delegado**.

Compruebe que el extremo delegación lleva a cabo las siguientes acciones:


1. Recibir una solicitud en la forma siguiente:

    > *http://www.yourwebsite.com/apimdelegation?operation= {operación} & productId = {producto suscribirse a} & ID = {usuario realizar solicitud} & salt = {cadena} & sig = {cadena}*

    Parámetros de consulta para el caso de suscripción de producto:
    - **operación**: identifica qué tipo de solicitud de delegación es. Suscripción de producto solicitudes de las opciones válidas son:
        - "Suscripción": una solicitud para suscribirse al usuario a un determinado producto con proporcionado ID (vea a continuación)
        - "Cancelar suscripción": una solicitud para cancelar la suscripción de un producto de un usuario
        - "Renovación": una requst para renovar una suscripción (por ejemplo, que se va a expirar posible)
    - **IdProducto**: el identificador de producto del usuario solicitado para suscribirse a
    - **ID de usuario**: el identificador del usuario para el que se realiza la solicitud
    - **salt**: una cadena salt especial que se usan para calcular un hash de seguridad
    - **SIG**: hash a calcular un hash calculado de seguridad que se usará para comparación a su propio


2. Compruebe que la solicitud procede de la administración de la API de Azure (opcional, pero muy recomendable para seguridad)

    * Calcular un SHA512 HMAC de una cadena según los parámetros de consulta **IdProducto**, **identificador de usuario** y **salt** :
        > HMAC (**salt** '\n' **IdProducto** + '\n' + + **ID**)
         
    * Comparar el hash encima calcula el valor del parámetro de consulta **sig** . Si los dos valores coinciden, pasar al siguiente paso, en caso contrario, rechazar la solicitud.
    
3. Realice cualquier procesamiento de suscripción de producto en función del tipo de operación solicitada en **operación** : por ejemplo, facturación, preguntas más frecuentes, etcetera.

4. Acerca de la suscripción correctamente al usuario el producto en el lado, suscribirse al usuario el producto de administración de la API mediante [una llamada a la API de REST de suscripción de producto].

## <a name="delegate-example-code"></a> Código de ejemplo ##

Estos ejemplos de código muestran cómo llevar la *tecla de validación de delegación*, que se establece en la pantalla de la delegación del portal de publisher para crear un HMAC que se utiliza para validar la firma que prueben la validez de la returnUrl pasado. El mismo código funciona para productId e ID de usuario con ligera modificación.

**Código de C# para generar hash de returnUrl**

    using System.Security.Cryptography;

    string key = "delegation validation key";
    string returnUrl = "returnUrl query parameter";
    string salt = "salt query parameter";
    string signature;
    using (var encoder = new HMACSHA512(Convert.FromBase64String(key)))
    {
        signature = Convert.ToBase64String(encoder.ComputeHash(Encoding.UTF8.GetBytes(salt + "\n" + returnUrl)));
        // change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
        // compare signature to sig query parameter
    }


**Código de NodeJS para generar hash de returnUrl**

    var crypto = require('crypto');
    
    var key = 'delegation validation key'; 
    var returnUrl = 'returnUrl query parameter';
    var salt = 'salt query parameter';
    
    var hmac = crypto.createHmac('sha512', new Buffer(key, 'base64'));
    var digest = hmac.update(salt + '\n' + returnUrl).digest();
    // change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
    // compare signature to sig query parameter
    
    var signature = digest.toString('base64');

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información acerca de la delegación, vea el siguiente vídeo.

> [AZURE.VIDEO delegating-user-authentication-and-product-subscription-to-a-3rd-party-site]

[Delegating developer sign-in and sign-up]: #delegate-signin-up
[Delegating product subscription]: #delegate-product-subscription
[solicitar un símbolo de sesión único (SSO)]: http://go.microsoft.com/fwlink/?LinkId=507409
[crear un usuario]: http://go.microsoft.com/fwlink/?LinkId=507655#CreateUser
[llamar a la API de REST de suscripción de producto]: http://go.microsoft.com/fwlink/?LinkId=507655#SSO
[Next steps]: #next-steps
[código de ejemplo que se proporcionan a continuación]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png 
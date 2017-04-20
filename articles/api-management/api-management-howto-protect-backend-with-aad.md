<properties
    pageTitle="Cómo proteger un servidor Web API con Azure Active Directory y administración de la API"
    description="Obtenga información sobre cómo proteger API Web back-end con Azure Active Directory y administración de la API." 
    services="api-management"
    documentationCenter=""
    authors="steved0x"
    manager="erikre"
    editor=""/>

<tags
    ms.service="api-management"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="sdanie"/>

# <a name="how-to-protect-a-web-api-backend-with-azure-active-directory-and-api-management"></a>Cómo proteger un servidor Web API con Azure Active Directory y administración de la API

El siguiente vídeo muestra cómo crear un servidor Web API y proteger mediante el protocolo OAuth 2.0 con Azure Active Directory y administración de la API.  Este artículo proporciona una introducción e información adicional para ver los pasos en el vídeo. Este vídeo 24 minutos le muestra cómo para:

-   Crear un servidor Web API y proteger con AAD - empezando por 1:30
-   Importar la API de administración de la API, empezando por 7:10
-   Configurar el portal de programadores para llamar a la API - empezando por 9:09
-   Configurar una aplicación de escritorio para llamar a la API - empezando por 18:08
-   Configurar una directiva de validación de JWT para autorizar previamente solicitudes - empezando en 20:47

>[AZURE.VIDEO protecting-web-api-backend-with-azure-active-directory-and-api-management]

## <a name="create-an-azure-ad-directory"></a>Crear un directorio de Azure AD

Para proteger su Web API copia con Azure Active Directory primero debe tener un un inquilino AAD. En este vídeo se usa un inquilino denominado **APIMDemo** . Para crear un inquilino AAD, inicie sesión en el [Portal de clásico de Azure](https://manage.windowsazure.com) y haga clic en **nuevo**->**Servicios de aplicación**->**Active Directory**->**directorio**->**Crear personalizado**. 

![Azure Active Directory][api-management-create-aad-menu]

En este ejemplo se crea un directorio denominado **APIMDemo** con el dominio predeterminado denominado **DemoAPIM.onmicrosoft.com**. Este directorio se usa en el vídeo.

![Azure Active Directory][api-management-create-aad]

## <a name="create-a-web-api-service-secured-by-azure-active-directory"></a>Crear un servicio Web API protegido por Azure Active Directory

En este paso, API Web back-end se crea con Visual Studio de 2013. Este paso del vídeo comienza en 1:30. Para crear la API Web back-end proyecto de Visual Studio, haga clic en **archivo**->**nuevo**->**proyecto**y elija la **Aplicación Web de ASP.NET** de la lista de plantillas **Web** . En este vídeo, el proyecto se denomina **APIMAADDemo**. Haga clic en **Aceptar** para crear el proyecto. 

![Visual Studio][api-management-new-web-app]

Haga clic en **Web API** **Seleccionar una lista de plantilla** para crear un proyecto de Web API. Para configurar la autenticación de directorio de Azure, haga clic en **Cambiar autenticación**.

![Nuevo proyecto][api-management-new-project]

Haga clic en **Cuentas de la organización**y especifique el **dominio** de su inquilino AAD. En este ejemplo, el dominio es **DemoAPIM.onmicrosoft.com**. La ficha **dominios** del directorio puede obtenerse el dominio del directorio.

![Dominios][api-management-aad-domains]

Configurar las opciones que desee en el cuadro de diálogo **Cambiar autenticación** y haga clic en **Aceptar**.

![Cambio de la autenticación][api-management-change-authentication]

Al hacer clic en **Aceptar** registrar la aplicación con el directorio de Azure AD tratará de Visual Studio y le pedirá que inicie sesión en Visual Studio. Inicie sesión con una cuenta administrativa para el directorio.

![Inicie sesión en Visual Studio][api-management-sign-in-vidual-studio]

Para configurar este proyecto como una comprobación de Azure Web API el cuadro para el **Host en la nube** y, a continuación, haga clic en **Aceptar**.

![Nuevo proyecto][api-management-new-project-cloud]

Le pedirá que inicie sesión en Azure y, a continuación, puede configurar la aplicación Web.

![Configurar][api-management-configure-web-app]

En este ejemplo se especifica un nuevo **plan de servicios de aplicación** denominada **APIMAADDemo** .

Haga clic en **Aceptar** para configurar la aplicación Web y cree el proyecto.

## <a name="add-the-code-to-the-web-api-project"></a>Agregar el código para el proyecto de Web API

El siguiente paso en el vídeo, agrega el código para el proyecto de Web API. Este paso se inicia en 4:35.

La API de Web en este ejemplo se implementa un servicio de calculadora básica con un modelo y un controlador. Para agregar el modelo para el servicio, secundario **modelos** en el **Explorador de soluciones** y seleccione **Agregar** **clase**. Nombre de la clase `CalcInput` y haga clic en **Agregar**.

Agregue las siguientes `using` instrucción a la parte superior de la `CalcInput.cs` archivo.

    using Newtonsoft.Json;

 Reemplace la clase generada por el código siguiente.

    public class CalcInput
    {
        [JsonProperty(PropertyName = "a")]
        public int a;

        [JsonProperty(PropertyName = "b")]
        public int b;
    }

Secundario **controladores** en **Explorador de soluciones** y elija **Agregar**->**controlador**. Elija el **Controlador de Web API 2 - vacío** y haga clic en **Agregar**. Escriba el nombre del controlador **CalcController** y haga clic en **Agregar**.

![Agregar controlador][api-management-add-controller]

Agregue las siguientes `using` instrucción a la parte superior de la `CalcController.cs` archivo.

    using System.IO;
    using System.Web;
    using APIMAADDemo.Models;

Reemplazar la clase de controlador generado con el código siguiente. Este código implementa el `Add`, `Subtract`, `Multiply`, y `Divide` las operaciones de la API de calculadora básica.

    [Authorize]
    public class CalcController : ApiController
    {
        [Route("api/add")]
        [HttpGet]
        public HttpResponseMessage GetSum([FromUri]int a, [FromUri]int b)
        {
            string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a + b);
            HttpResponseMessage response = Request.CreateResponse();
            response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
            return response;
        }

        [Route("api/sub")]
        [HttpGet]
        public HttpResponseMessage GetDiff([FromUri]int a, [FromUri]int b)
        {
            string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a - b);
            HttpResponseMessage response = Request.CreateResponse();
            response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
            return response;
        }

        [Route("api/mul")]
        [HttpGet]
        public HttpResponseMessage GetProduct([FromUri]int a, [FromUri]int b)
        {
            string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a * b);
            HttpResponseMessage response = Request.CreateResponse();
            response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
            return response;
        }

        [Route("api/div")]
        [HttpGet]
        public HttpResponseMessage GetDiv([FromUri]int a, [FromUri]int b)
        {
            string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a / b);
            HttpResponseMessage response = Request.CreateResponse();
            response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
            return response;
        }
    }

Presione **F6** para generar y comprobar la solución.

## <a name="publish-the-project-to-azure"></a>Publique el proyecto en Azure

En este paso Visual Studio project se publica en Azure. Este paso del vídeo comienza en 5:45.

Para publicar el proyecto en Azure, haga clic en el proyecto **APIMAADDemo** en Visual Studio y elija **Publicar**. Mantenga la configuración predeterminada en el cuadro de diálogo **Publicar en Web** y haga clic en **Publicar**.

![Publicación Web][api-management-web-publish]

## <a name="grant-permissions-to-the-azure-ad-backend-service-application"></a>Conceder permisos a la aplicación de servicio de back-end de Azure AD

En el directorio de Azure AD como parte del proceso de publicación y configuración de su proyecto de Web API, se crea una nueva aplicación del servicio de back-end. En este paso del vídeo, comenzando por 6:13, tienen los permisos para el servidor de la API de Web.

![Aplicación][api-management-aad-backend-app]

Haga clic en el nombre de la aplicación para configurar los permisos necesarios. Vaya a la ficha **Configurar** y desplácese hacia abajo hasta la sección **permisos a otras aplicaciones** . Haga clic en el menú desplegable junto a **Windows** **Azure Active Directory** **Permisos de aplicación** , active la casilla para **leer datos de directorio**y haga clic en **Guardar**.

![Agregar permisos][api-management-aad-add-permissions]

>[AZURE.NOTE] Si no se enumera **Windows** **Azure Active Directory** en permisos a otras aplicaciones, haga clic en **Agregar aplicación** y agregar de la lista.

Tome nota de la **Aplicación identificador URI** para su uso en un paso posterior cuando una aplicación de Azure AD está configurada para el portal de administración de la API de desarrollador.

![Identificador URI de aplicación][api-management-aad-sso-uri]

## <a name="import-the-web-api-into-api-management"></a>Importar la API de Web API administración

API se configuran desde el portal de publisher API, que se tiene acceso a través del Portal de clásico de Azure. Para alcanzar el portal de publisher, haga clic en **Administrar** en el Portal de Azure clásico de su servicio de administración de la API. Si aún no ha creado una instancia de servicio de administración de la API, vea [crear una instancia de servicio de administración de la API][] en el tutorial de [administrar su primera API][] .

![Portal de Publisher][api-management-management-console]

Operaciones pueden ser [agregado a API manualmente](api-management-howto-add-operations.md)o se pueden importar. En este vídeo, se importan las operaciones en formato Swagger empezando en 6:40.

Cree un archivo llamado `calcapi.json` con contenido siguiente y guárdelo en su equipo. Asegúrese de que el `host` atributo puntos a su Web API de back-end. En este ejemplo `"host": "apimaaddemo.azurewebsites.net"` se utiliza.

{"swagger": "2.0", "información": {"título": "Calculadora", "Descripción": "Arithmetics sobre HTTP!", "versión": "1.0"}, "host": "apimaaddemo.azurewebsites.net", "basePath": "/ api", "combinaciones": ["http"], "rutas": {"/ Agregar? un = {a} & b = {b}": {"get": {"Descripción": "Responde con una suma de dos números.", "operationId": "Agregar dos enteros", "parámetros": [{"nombre": "a", "en": "consulta", "Descripción": "primer operando. Valor predeterminado es <code>51</code>. ","necesario": true"predeterminado":"51","enumeración": ["51"]}, {"nombre":"b","en":"consulta","Descripción":"segundo operando. Valor predeterminado es <code>49</code>. ","necesario": true"predeterminado":"49","enumeración": ["49"]}],"respuestas": {}}}," / sub?a = {a} & b = {b} ": {"get": {"Descripción":"Responde con una diferencia entre dos números.","operationId":"Restar dos enteros","parámetros": [{"nombre":"a","en":"consulta","Descripción":"primer operando. Valor predeterminado es <code>100</code>. ","necesario": true"predeterminado":"100","enumeración": ["100"]}, {"nombre":"b","en":"consulta","Descripción":"segundo operando. Valor predeterminado es <code>50</code>. ","necesario": true"predeterminado":"50","enumeración": ["50"]}],"respuestas": {}}}," / div?a = {a} & b = {b} ": {"get": {"Descripción":"Responde con un cociente de dos números.","operationId":"Dividir dos enteros","parámetros": [{"nombre":"a","en":"consulta","Descripción":"primer operando. Valor predeterminado es <code>100</code>. ","necesario": true"predeterminado":"100","enumeración": ["100"]}, {"nombre":"b","en":"consulta","Descripción":"segundo operando. Valor predeterminado es <code>20</code>. ","necesario": true"predeterminado":"20","enumeración": ["20"]}],"respuestas": {}}}," / mul?a = {a} & b = {b} ": {"get": {"Descripción":"Responde a un producto de dos números.","operationId":"Multiplicar dos enteros","parámetros": [{"nombre":"a","en":"consulta","Descripción":"primer operando. Valor predeterminado es <code>20</code>. ","necesario": true"predeterminado":"20","enumeración": ["20"]}, {"nombre":"b","en":"consulta","Descripción":"segundo operando. Valor predeterminado es <code>5</code>. ","necesario": true"predeterminado":"5","enumeración": ["5"]}],"respuestas": {}}}}}

Para importar la API de calculadora, haga clic en **las API de** desde el menú de **Administración de la API** de la izquierda y, a continuación, haga clic en **Importar API**.

![Botón Importar API][api-management-import-api]

Siga estos pasos para configurar la API de calculadora.

1. Haga clic en **desde archivo**, busque la `calculator.json` de archivos que guardó y a continuación, haga clic en el botón de opción **Swagger** .
2. En el cuadro de texto **dirección URL de Web API sufijo** , escriba **calc** .
3. Haga clic en el cuadro de **productos (opcionales)** y elija **Starter**.
4. Haga clic en **Guardar** para importar la API.

![Agregar nueva API][api-management-import-new-api]

Una vez que se importa la API, se muestra la página de resumen de la API en el portal de publisher.

## <a name="call-the-api-unsuccessfully-from-the-developer-portal"></a>Llamar a la API correctamente desde el portal de programadores

En este momento, la API se ha importado en administración de la API, pero no se ha puede llamar correctamente desde el portal de programadores porque el servicio back-end está protegido con la autenticación de Azure AD. Esto se muestra en el vídeo empezando en 7:40 con los pasos siguientes.

Haga clic en el **portal de programadores** desde la parte superior derecha del portal de publisher.

![Portal de programadores][api-management-developer-portal-menu]

**API** y haga clic en la API de **Calculadora** .

![Portal de programadores][api-management-dev-portal-apis]

Haga clic en **probar la aplicación**.

![Pruébelo:][api-management-dev-portal-try-it]

Haga clic en **Enviar** y observe el estado de respuesta de **401 no autorizado**.

![Enviar][api-management-dev-portal-send-401]

La solicitud está autorizada porque el servidor de la API está protegido por Azure Active Directory. Antes de llamar correctamente a la API del programador portal debe estar configurado para autorizar los desarrolladores mediante OAuth 2.0. Este proceso se describe en las secciones siguientes.

## <a name="register-the-developer-portal-as-an-aad-application"></a>Registrar el portal de programadores como una aplicación de AAD

El primer paso para configurar el portal de programadores para autorizar los desarrolladores mediante OAuth 2.0 es registrar el portal de programadores como una aplicación de AAD. Esto se demuestra comenzando en 8:27 en el vídeo.

Desplácese hasta el inquilino de Azure AD desde el primer paso de este vídeo, en este ejemplo **APIMDemo** y vaya a la ficha **aplicaciones** .

![Nueva aplicación][api-management-aad-new-application-devportal]

Haga clic en el botón **Agregar** para crear una nueva aplicación de Azure Active Directory y elija **Agregar una aplicación se desarrolla mi organización**.

![Nueva aplicación][api-management-new-aad-application-menu]

Elija la **aplicación Web o la API de Web**, escriba un nombre y haga clic en la flecha siguiente. En este ejemplo se utiliza **APIMDeveloperPortal** .

![Nueva aplicación][api-management-aad-new-application-devportal-1]

Para **iniciar sesión en la dirección URL** , escriba la dirección URL de su servicio de administración de la API y anexar `/signin`. En este ejemplo se utiliza **https://contoso5.portal.azure-api.net/signin **.

Dirección URL de **Id** de aplicación escriba la dirección URL de su servicio de administración de la API y anexar algunos caracteres únicos. Pueden ser cualquier carácter que desee y en este ejemplo se utiliza **https://contoso5.portal.azure-api.net/dp** . Cuando se configuran las **Propiedades de la aplicación** que desee, haga clic en la marca de verificación para crear la aplicación.

![Nueva aplicación][api-management-aad-new-application-devportal-2]

## <a name="configure-an-api-management-oauth-20-authorization-server"></a>Configurar un servidor de autorización de API administración OAuth 2.0

El siguiente paso es configurar un servidor de autorización de OAuth 2.0 en administración de la API. Este paso se muestra en el vídeo empezando por 9:43.

Haga clic en **seguridad** en el menú de administración de la API de la izquierda, haga clic en **OAuth 2.0**y, a continuación, haga clic en el servidor de **autorización de agregar** .

![Agregar servidor de autorización][api-management-add-authorization-server]

En los campos **nombre** y **Descripción** , escriba un nombre y una descripción opcional. Estos campos se usan para identificar el servidor de autorización de OAuth 2.0 dentro de la instancia de servicio de administración de la API. En este ejemplo se usa la **demostración de servidor de autorización** . Más adelante al especificar un servidor de OAuth 2.0 que se usará para la autenticación para una API, se selecciona este nombre.

Para la **dirección URL de la página de registro de cliente** , escriba un valor de marcador de posición, como por ejemplo `http://localhost`.  La **dirección URL de la página de registro de cliente** se redirige a la página que los usuarios pueden usar para crear y configurar sus propias cuentas para los proveedores de OAuth 2.0 que admiten la administración de usuarios de cuentas. En este ejemplo los usuarios no crear y configurar sus propias cuentas para que se usa un marcador de posición.

![Agregar servidor de autorización][api-management-add-authorization-server-1]

A continuación, especifique la **dirección URL del extremo de autorización** y la **dirección URL del extremo del Token**.

![Servidor de autorización][api-management-add-authorization-server-1a]

Estos valores se pueden recuperar desde la página **Puntos finales de la aplicación** de la aplicación de AAD que ha creado para el portal de programadores. Para acceder a los extremos vaya a la ficha **Configurar** para la aplicación de AAD y haga clic en **los extremos de la vista**.

![Aplicación][api-management-aad-devportal-application]

![Extremos de vista][api-management-aad-view-endpoints]

Copie el **extremo de autorización de OAuth 2.0** y pegarlo en el cuadro de texto de la **dirección URL del extremo de autorización** .

![Agregar servidor de autorización][api-management-add-authorization-server-2]

Copie el **extremo de token OAuth 2.0** y pegarlo en el cuadro de texto de la **dirección URL del extremo de Token** .

![Agregar servidor de autorización][api-management-add-authorization-server-2a]

Además de pegar en el extremo de token, agregar un parámetro adicional cuerpo denominada **recursos** y el uso de valor del **URI de identificador de aplicación** de la aplicación de AAD para el servicio de back-end que se creó cuando se publicó el proyecto de Visual Studio.

![Identificador URI de aplicación][api-management-aad-sso-uri]

A continuación, especifique las credenciales del cliente. Estas son las credenciales para el recurso que desee tener acceso, en este caso, el servicio de back-end.

![Credenciales del cliente][api-management-client-credentials]

Para obtener el **Identificador de cliente**, vaya a la ficha **Configurar** de la aplicación de AAD para el servicio de back-end y copie el **Identificador de cliente**.

Para obtener el **Secreto de cliente** de haga clic en **Seleccione duración** desplegable en la sección de **claves** y especificar un intervalo. En este ejemplo se usa un año.

![Identificador de cliente][api-management-aad-client-id]

Haga clic en **Guardar** para guardar la configuración y mostrar la clave. 

>[AZURE.IMPORTANT] Tome nota de esta clave. Una vez que cierre la ventana de configuración de Azure Active Directory, la clave no se puede mostrar de nuevo.

Copiar la clave en el Portapapeles, vuelva al portal de publisher, pegue la clave en el cuadro de texto **Secreto a cliente** y haga clic en **Guardar**.

![Agregar servidor de autorización][api-management-add-authorization-server-3]

Inmediatamente después de las credenciales del cliente es una concesión de autorización de código. Copie este código de autorización y vuelva a la aplicación portal del desarrollador de Azure AD Configurar página y a continuación, pegue la autorización conceder en el campo **Dirección URL de la respuesta** y haga clic en **Guardar** de nuevo.

![Dirección URL de respuesta][api-management-aad-reply-url]

El siguiente paso es configurar los permisos para el portal de programadores aplicación AAD. Haga clic en **Permisos de aplicación** y Active la casilla para **leer datos de directorio**. Haga clic en **Guardar** para guardar los cambios y, a continuación, haga clic en **Agregar aplicación**.

![Agregar permisos][api-management-add-devportal-permissions]

Haga clic en el icono de búsqueda, escriba **APIM** en el comenzar con el cuadro de, seleccione **APIMAADDemo**y haga clic en la marca de verificación para guardar.

![Agregar permisos][api-management-aad-add-app-permissions]

Haga clic en **Permisos de delegado** para **APIMAADDemo** , active la casilla de **APIMAADDemo de Access**y haga clic en **Guardar**. Esto permite que el desarrollador de aplicación del portal para obtener acceso al servicio back-end.

![Agregar permisos][api-management-aad-add-delegated-permissions]

## <a name="enable-oauth-20-user-authorization-for-the-calculator-api"></a>Habilitar la autorización del usuario OAuth 2.0 para la API de calculadora

Ahora que está configurado el servidor de OAuth 2.0, puede especificar en la configuración de seguridad de la API. Este paso se muestra en el vídeo empezando por 14:30.

Haga clic en **las API de** menú de la izquierda y haga clic en **Calculadora** para ver y configurar las opciones.

![API de calculadora][api-management-calc-api]

Vaya a la pestaña **seguridad** , active la casilla de verificación **OAuth 2.0** , seleccione el servidor de autorización que desee en la lista desplegable **servidor de autorización** y haga clic en **Guardar**.

![API de calculadora][api-management-enable-aad-calculator]

## <a name="successfully-call-the-calculator-api-from-the-developer-portal"></a>Llamar correctamente a la API de Calculadora desde el portal de programadores

Ahora que la autorización de OAuth 2.0 está configurada en la API, se pueden llamar correctamente sus operaciones en el centro de desarrollo. Este paso se muestra en el vídeo empezando por 15:00.

Volver a la operación de **Agregar dos enteros** del servicio de calculadora en el portal de programadores y haga clic en **probar la aplicación**. Nota el nuevo elemento en la sección **autorización** correspondiente al servidor de autorización que acaba de agregar.

![API de calculadora][api-management-calc-authorization-server]

Seleccione el **código de autorización** de la lista desplegable de autorización y escriba las credenciales de la cuenta que desea usar. Si ya ha iniciado sesión con la cuenta que es podrán que no se le.

![API de calculadora][api-management-devportal-authorization-code]

Haga clic en **Enviar** y observe el **estado de respuesta** de **200 Aceptar** y los resultados de la operación en el contenido de la respuesta.

![API de calculadora][api-management-devportal-response]

## <a name="configure-a-desktop-application-to-call-the-api"></a>Configurar una aplicación de escritorio para llamar a la API

El procedimiento siguiente en el vídeo se inicia 16:30 y configura una aplicación de escritorio simple para llamar a la API. El primer paso es registrar la aplicación de escritorio en Azure AD y conceder acceso al directorio y el servicio de back-end. No hay una demostración de la aplicación de escritorio llamada a una operación de la API de calculadora en 18:25.

## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>Configurar una directiva de validación de JWT para autorizar previamente solicitudes

El procedimiento final en el vídeo se inicia 20:48 y muestra cómo usar la directiva de [JWT validar](https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#ValidateJWT) para autorizar previamente solicitudes al validar los identificadores de acceso de cada solicitud entrante. Si la directiva de JWT validar no valida la solicitud, la solicitud está bloqueada por la API de administración y no se pasa a lo largo del back-end.

    <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
        <openid-config url="https://login.windows.net/DemoAPIM.onmicrosoft.com/.well-known/openid-configuration" />
        <required-claims>
            <claim name="aud">
                <value>https://DemoAPIM.NOTonmicrosoft.com/APIMAADDemo</value>
            </claim>
        </required-claims>
    </validate-jwt>

Demostración de la configuración y el uso de esta directiva en otro [177 ataque carta de nube: más características de administración de API](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) y avance rápido a las 13:50. Avance rápido para ver las directivas configuradas en el editor de directivas de 15:00 a 18:50 para ver una demostración de llamar a una operación desde el portal de programadores con y sin el token de autorización necesarios.

## <a name="next-steps"></a>Pasos siguientes
-   Vea más [vídeos](https://azure.microsoft.com/documentation/videos/index/?services=api-management) sobre la administración de la API.
-   Para que conocer otras maneras de proteger su servicio de back-end, vea [autenticación de certificado mutuo](api-management-howto-mutual-certificates.md) y [conectarse mediante VPN o ExpressRoute](api-management-howto-setup-vpn.md).

[api-management-management-console]: ./media/api-management-howto-protect-backend-with-aad/api-management-management-console.png

[api-management-import-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-import-api.png
[api-management-import-new-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-import-new-api.png
[api-management-create-aad-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-create-aad-menu.png
[api-management-create-aad]: ./media/api-management-howto-protect-backend-with-aad/api-management-create-aad.png
[api-management-new-web-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-web-app.png
[api-management-new-project]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-project.png
[api-management-new-project-cloud]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-project-cloud.png
[api-management-change-authentication]: ./media/api-management-howto-protect-backend-with-aad/api-management-change-authentication.png
[api-management-sign-in-vidual-studio]: ./media/api-management-howto-protect-backend-with-aad/api-management-sign-in-vidual-studio.png
[api-management-configure-web-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-configure-web-app.png
[api-management-aad-domains]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-domains.png
[api-management-add-controller]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-controller.png
[api-management-web-publish]: ./media/api-management-howto-protect-backend-with-aad/api-management-web-publish.png
[api-management-aad-backend-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-backend-app.png
[api-management-aad-add-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-permissions.png
[api-management-developer-portal-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-developer-portal-menu.png
[api-management-dev-portal-apis]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-apis.png
[api-management-dev-portal-try-it]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-try-it.png
[api-management-dev-portal-send-401]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-send-401.png
[api-management-aad-new-application-devportal]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal.png
[api-management-aad-new-application-devportal-1]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal-1.png
[api-management-aad-new-application-devportal-2]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal-2.png
[api-management-aad-devportal-application]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-devportal-application.png
[api-management-add-authorization-server]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server.png
[api-management-aad-sso-uri]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-sso-uri.png
[api-management-aad-view-endpoints]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-view-endpoints.png
[api-management-aad-client-id]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-client-id.png
[api-management-add-authorization-server-1]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-1.png
[api-management-add-authorization-server-2]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-2.png
[api-management-add-authorization-server-2a]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-2a.png
[api-management-add-authorization-server-3]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-3.png
[api-management-aad-reply-url]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-reply-url.png
[api-management-add-devportal-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-devportal-permissions.png
[api-management-aad-add-app-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-app-permissions.png
[api-management-aad-add-delegated-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-delegated-permissions.png
[api-management-calc-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-calc-api.png
[api-management-enable-aad-calculator]: ./media/api-management-howto-protect-backend-with-aad/api-management-enable-aad-calculator.png
[api-management-devportal-authorization-code]: ./media/api-management-howto-protect-backend-with-aad/api-management-devportal-authorization-code.png
[api-management-devportal-response]: ./media/api-management-howto-protect-backend-with-aad/api-management-devportal-response.png
[api-management-calc-authorization-server]: ./media/api-management-howto-protect-backend-with-aad/api-management-calc-authorization-server.png
[api-management-add-authorization-server-1a]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-1a.png
[api-management-client-credentials]: ./media/api-management-howto-protect-backend-with-aad/api-management-client-credentials.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-aad-application-menu.png

[Crear una instancia de servicio de administración de la API]: api-management-get-started.md#create-service-instance
[Administrar su primera API]: api-management-get-started.md

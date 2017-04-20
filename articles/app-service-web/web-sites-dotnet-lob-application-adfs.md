<properties 
    pageTitle="Crear una aplicación de Azure de línea de negocio con autenticación de AD FS | Microsoft Azure" 
    description="Obtenga información sobre cómo crear una aplicación de línea de negocio en el servicio de aplicación de Azure autentica con STS local. En este tutorial se dirige a AD FS como el STS local." 
    services="app-service\web" 
    documentationCenter=".net" 
    authors="cephalin" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="web" 
    ms.date="08/31/2016" 
    ms.author="cephalin"/>

# <a name="create-a-line-of-business-azure-app-with-ad-fs-authentication"></a>Crear una aplicación de Azure de línea de negocio con autenticación de AD FS

En este artículo muestra cómo crear una aplicación de línea de negocio de ASP.NET MVC en [Servicio de la aplicación de Azure](../app-service/app-service-value-prop-what-is.md) utilizando un local de [Servicios de federación de Active Directory](http://technet.microsoft.com/library/hh831502.aspx) como el proveedor de identidades. Cuando desee crear aplicaciones de línea de negocio en la aplicación de servicio de Azure pero su organización requiere directorio datos se almacenan en las oficinas, puede trabajar en este escenario.

>[AZURE.NOTE] Para obtener información general de las opciones de autenticación y la autorización de empresa diferente para la aplicación de servicio de Azure, vea [autenticar con local de Active Directory en la aplicación de Azure](web-sites-authentication-authorization.md).

<a name="bkmk_build"></a>
## <a name="what-you-will-build"></a>¿Qué va a crear ##

Va a crear una aplicación de ASP.NET básica en las aplicaciones de Azure aplicación de servicio Web con las características siguientes:

- Autentica a los usuarios con AD FS
- Usa `[Authorize]` autorizar a los usuarios de diferentes acciones
- Configuración estática para depuración en Visual Studio y publicar en la aplicación de servicio Web Apps (configurar una sola vez, depurar y publicar en cualquier momento)  

<a name="bkmk_need"></a>
## <a name="what-you-need"></a>Lo que necesita ##

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

Necesita lo siguiente para completar este tutorial:

- En local implementación de AD FS (para obtener un tutorial to-end de la prueba que se utiliza en este tutorial, vea [laboratorio de pruebas: STS independiente con AD FS en Azure VM (para solo prueba)](https://blogs.msdn.microsoft.com/cephalin/2014/12/21/test-lab-standalone-sts-with-ad-fs-in-azure-vm-for-test-only/))
- Permisos para crear confiar confianzas en administración de AD FS de terceros
- Actualización de Visual Studio 2013 4 o posterior
- [Azure SDK 2.8.1](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409) o posterior

<a name="bkmk_sample"></a>
## <a name="use-sample-application-for-line-of-business-template"></a>Utilizar la aplicación de ejemplo para la plantilla de línea de negocio ##

La aplicación de ejemplo en este tutorial, [WSFederation de aplicación Web de DotNet)](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet), creado por el equipo de Azure Active Directory. Puesto que AD FS admite WS-Federation, puede usarlo como una plantilla para crear aplicaciones de línea de negocio con facilidad. Tiene las siguientes características:

- Usa [WS-Federation](http://msdn.microsoft.com/library/bb498017.aspx) para autenticar con un local implementación de AD FS
- Funcionalidad de inicio de sesión y cierre de sesión
- Usa [Microsoft.Owin](http://www.asp.net/aspnet/overview/owin-and-katana/an-overview-of-project-katana) (en lugar de Windows Foundation de identidad), que es el futuro de ASP.NET y mucho más fácil de configurar para la autenticación y la autorización de WIF

<a name="bkmk_setup"></a>
## <a name="set-up-the-sample-application"></a>Configurar la aplicación de ejemplo ##

2.  Clonar o descargue la solución de ejemplo en [WSFederation de aplicación Web de DotNet](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) en su directorio local.

    > [AZURE.NOTE] Las instrucciones de [README.md](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet/blob/master/README.md) muestran cómo configurar la aplicación con Azure Active Directory. Pero, en este tutorial, tiene una configuración con AD FS, siga estos pasos en su lugar.

3.  Abra la solución y, a continuación, abra Controllers\AccountController.cs en el **Explorador de soluciones**.

    Verá que el código simplemente emite un desafío de autenticación para autenticar al usuario mediante WS-Federation. Toda la autenticación está configurado en App_Start\Startup.Auth.cs.

4.  Abra App_Start\Startup.Auth.cs. En la `ConfigureAuth` método, tenga en cuenta la línea:

        app.UseWsFederationAuthentication(
            new WsFederationAuthenticationOptions
            {
                Wtrealm = realm,
                MetadataAddress = metadata                                      
            });

    En el mundo OWIN, este fragmento de código es realmente al mínimo que se debe configurar la autenticación de WS-Federation. Es mucho más fácil y más elegante que WIF, donde se inserta Web.config con XML diferentes lugares. La única información que necesita es del usuario de confianza (RP) identificador y la dirección URL del archivo de metadatos del servicio AD FS. Aquí tenemos un ejemplo:

    -   Identificador RP:`https://contoso.com/MyLOBApp`
    -   Dirección de metadatos:`http://adfs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`

5.  En App_Start\Startup.Auth.cs, cambie las definiciones de cadena estática siguientes:  
    <pre class="prettyprint">
    territorio de cadena estática privada = ConfigurationManager.AppSettings["ida:<mark>RPIdentifier</mark>"]; <mark><del>cadena estática privada aadInstance = ConfigurationManager.AppSettings["ida:AADInstance"];</del></mark> 
     <mark><del>inquilino de cadena estática privada = ConfigurationManager.AppSettings["ida:Tenant"];</del></mark> 
     <mark><del>cadena estática privada metadatos = cadena. Formato ("{0} / {1} /federationmetadata/2007-06/federationmetadata.xml", aadInstance, inquilino);</del></mark> 
     <mark>cadena estática privada metadatos = cadena. Formato ("https:// {0} /federationmetadata/2007-06/federationmetadata.xml", ConfigurationManager.AppSettings["ida:ADFS"]);</mark>

    <mark><del>autoridad de cadena = String.Format (CultureInfo.InvariantCulture, aadInstance, inquilino);</del></mark>
    </pre>

6.  Ahora, realice los cambios correspondientes en el archivo Web.config. Abra el archivo Web.config y modificar la siguiente configuración de aplicación:  
    <pre class="prettyprint">
    &lt;appSettings&gt;
    &lt;add key="webpages:Version" value="3.0.0.0" /&gt;
    &lt;add key="webpages:Enabled" value="false" /&gt;
    &lt;add key="ClientValidationEnabled" value="true" /&gt;
    &lt;add key="UnobtrusiveJavaScriptEnabled" value="true" /&gt;
      <mark><del>&lt;add key="ida:Wtrealm" value="[Enter the App ID URI of WebApp-WSFederation-DotNet https://contoso.onmicrosoft.com/WebApp-WSFederation-DotNet]" /&gt;</del></mark>
      <mark><del>&lt;add key="ida:AADInstance" value="https://login.windows.net" /&gt;</del></mark>
      <mark><del>&lt;add key="ida:Tenant" value="[Enter tenant name, e.g. contoso.onmicrosoft.com]" /&gt;</del></mark>
      <mark>&lt;add key="ida:RPIdentifier" value="[Enter the relying party identifier como se ha configurado en AD FS, por ejemplo, https://localhost:44320 /] "/&gt;</mark>
      <mark>&lt;Agregar clave = valor"ida: ADFS"="[Escriba el FQDN de servicio de AD FS, por ejemplo, adfs.contoso.com]"/&gt; </mark>        

    &lt;/appSettings&gt;
    </pre>

    Rellene los valores de clave por su entorno respectivo.

7.  Generar la aplicación para asegurarse de que no existen errores.

Eso es todo. Ahora la aplicación de ejemplo está preparada para trabajar con AD FS. Necesita configurar una confianza RP con esta aplicación de AD FS.

<a name="bkmk_deploy"></a>
## <a name="deploy-the-sample-application-to-azure-app-service-web-apps"></a>Implementar la aplicación de ejemplo para las aplicaciones de Azure aplicación de servicio Web

A continuación, publicar la aplicación para una aplicación web en la aplicación de servicio Web Apps conservando el entorno de depuración. Tenga en cuenta que va a publicar la aplicación antes de que tenga una relación de confianza RP con AD FS, para que autenticación sigue sin funciona todavía. Sin embargo, si lo hace ahora puede hacer que la dirección URL web app que puede usar para configurar la confianza RP más tarde.

1. Haga clic en el proyecto y seleccione **Publicar**.

    ![](./media/web-sites-dotnet-lob-application-adfs/01-publish-website.png)

2. Seleccione el **Servicio de aplicaciones de Microsoft Azure**.
3. Si no ha iniciado sesión en Azure, haga clic en **Iniciar sesión** y usar la cuenta de Microsoft para la suscripción de Azure para iniciar sesión.
4. Una vez que haya iniciado sesión, haga clic en **nuevo** para crear una aplicación web.
5. Rellene todos los campos necesarios. Va a conectar con una instalación local datos más adelante, por lo que no se crea una base de datos para esta aplicación web.

    ![](./media/web-sites-dotnet-lob-application-adfs/02-create-website.png)

6. Haga clic en **crear**. Una vez creada la aplicación web, se abre el cuadro de diálogo Publicar en Web.
7. En **Dirección URL de destino**, cambie **http** a **https**. Copie la dirección URL completa en un editor de texto para su uso posterior. A continuación, haga clic en **Publicar**.

    ![](./media/web-sites-dotnet-lob-application-adfs/03-destination-url.png)

11. En Visual Studio, abra **Web.Release.config** en su proyecto. Inserte el siguiente XML en el `<configuration>` etiquetar y reemplace el valor de clave con la dirección URL de la aplicación web publicación.  
    <pre class="prettyprint">
&lt;appSettings&gt;
   &lt;Agregar clave = valor "ida: RPIdentifier" = "<mark>[por ejemplo, https://mylobapp.azurewebsites.net/]</mark>" xdt = "SetAttributes" xdt:Locator="Match(key)" /&gt;
&lt;/appSettings&gt;</pre>

Cuando haya terminado, tiene dos identificadores RP configurados en su proyecto, uno para su entorno de depuración en Visual Studio y otro para la aplicación web publicado en Azure. Va a configurar una confianza RP para cada uno de los dos entornos de AD FS. Durante la depuración, la configuración de la aplicación en Web.config se usa para realizar la configuración de **depuración** de trabajar con AD FS. Cuando se publica (de forma predeterminada, la configuración de **lanzamiento** se publica), se carga un archivo de Web.config transformados incorpora los cambios de configuración de aplicación en Web.Release.config.

Si desea adjuntar la aplicación web publicado en Azure para el depurador (es decir, debe cargar símbolos de depuración del código en la aplicación web publicada), puede crear un duplicado de la configuración de depuración de depuración de Azure, pero con su propia transformación de Web.config personalizado (por ejemplo, Web.AzureDebug.config) que usa la configuración de la aplicación de Web.Release.config. Esto le permite mantener una configuración estática en distintos entornos.

<a name="bkmk_rptrusts"></a>
## <a name="configure-relying-party-trusts-in-ad-fs-management"></a>Configurar usuarios de confianza confianzas fiesta en administración de AD FS ##

Ahora debe configurar una confianza RP en administración de AD FS para poder usar la aplicación de ejemplo y realmente autenticar con AD FS. Debe configurar dos confianzas RP independientes, uno para su entorno de depuración y otro para la aplicación web publicado.

> [AZURE.NOTE] Asegúrese de que repetir los pasos siguientes para ambos de los entornos.

4.  En el servidor AD FS, inicie sesión con credenciales que tengan derechos de administración para AD FS.
5.  Abra Administración de AD FS. Secundario **confía AD FS\Trusted Relationships\Relying fiesta** y seleccione **Agregar confiar fiesta de confianza**.

    ![](./media/web-sites-dotnet-lob-application-adfs/1-add-rptrust.png)

5.  En la página **Seleccionar origen de datos** , seleccione **introducir manualmente los datos sobre el usuario de confianza**. 

    ![](./media/web-sites-dotnet-lob-application-adfs/2-enter-rp-manually.png)

6.  En la página **Especificar nombre para mostrar** , escriba un nombre para mostrar de la aplicación y haga clic en **siguiente**.
7.  En la página **Elegir protocolo** , haga clic en **siguiente**.
8.  En la página **Configurar certificado** , haga clic en **siguiente**.

    > [AZURE.NOTE] Puesto que se debe utilizar HTTPS ya, tokens cifrados son opcionales. Si realmente desea cifrar tokens de AD FS en esta página, también debe agregar lógica de descifrar token en el código. Para obtener más información, vea [configurar manualmente software intermedio OWIN WS-Federation y Aceptar tokens cifrados](http://chris.59north.com/post/2014/08/21/Manually-configuring-OWIN-WS-Federation-middleware-and-accepting-encrypted-tokens.aspx).
  
5.  Antes de pasar al siguiente paso, necesita una parte de la información de su proyecto de Visual Studio. En las propiedades del proyecto, tenga en cuenta la **Dirección URL de SSL** de la aplicación. 

    ![](./media/web-sites-dotnet-lob-application-adfs/3-ssl-url.png)

6.  En AD FS administración, en la página **Configurar URL** del **Confiar fiesta confiar en Asistente para agregar**, seleccione **Habilitar la compatibilidad con el protocolo WS-Federation pasivo** y escriba la dirección URL de SSL de su proyecto de Visual Studio que anotó en el paso anterior. A continuación, haga clic en **siguiente**.

    ![](./media/web-sites-dotnet-lob-application-adfs/4-configure-url.png)

    > [AZURE.NOTE] Dirección URL especifica dónde desea enviar al cliente tras autenticación. En el entorno de depuración, debe ser <code>https://localhost:&lt;port&gt;/</code>. Para la aplicación web publicado, debe ser la URL de la aplicación web.

7.  En la página **Configurar identificadores** , compruebe que la dirección URL de SSL de proyecto ya se encuentra y haga clic en **siguiente**. Haga clic en **siguiente** hasta el final del asistente con selecciones de forma predeterminada.

    > [AZURE.NOTE] En App_Start\Startup.Auth.cs de su proyecto de Visual Studio, este identificador se compara con el valor de <code>WsFederationAuthenticationOptions.Wtrealm</code> durante la autenticación federada. De forma predeterminada, la dirección URL de la aplicación desde el paso anterior se agrega como un identificador RP.

8.  Ha terminado la configuración de la aplicación de RP para el proyecto en AD FS. A continuación, configure esta aplicación para enviar las notificaciones necesarias para la aplicación. El cuadro de diálogo **Editar reglas de notificación** predeterminada se abre automáticamente al final del Asistente para que pueda empezar inmediatamente. Vamos a configurar al menos las siguientes notificaciones (con esquemas entre paréntesis):

    -   Nombre (http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name) - utilizado por ASP.NET para hacer uso `User.Identity.Name`.
    -   Nombre principal de usuario (http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn) - utilizada para identificar los usuarios de la organización.
    -   Pertenencias a grupos como roles (http://schemas.microsoft.com/ws/2008/06/identity/claims/role) - puede usarse con `[Authorize(Roles="role1, role2,...")]` decoración para autorizar controladores/acciones. En realidad, este enfoque no puede ser la mayoría eficaz autorización de funciones. Si los usuarios de AD pertenecen a cientos de grupos de seguridad, se convierten en cientos de reclamaciones de rol en el token de SAML. Un enfoque alternativo es enviar una solicitud de función única condicionalmente dependiendo de la suscripción del usuario en un grupo determinado. Sin embargo, se deberá ser sencillo para este tutorial.
    -   Nombre ID (http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier): se puede usar para la validación de contra. Para obtener más información sobre cómo hacer que funcione con la validación de contra, vea la sección **Agregar funcionalidad de línea de negocio** de [crear una línea de la empresa Azure aplicación con la autenticación de Azure Active Directory](web-sites-dotnet-lob-application-azure-ad.md#bkmk_crud).

    > [AZURE.NOTE] Los tipos de notificación que tenga que configurar para su aplicación depende de las necesidades de la aplicación. Para la lista de notificaciones compatible con aplicaciones de Azure Active Directory (es decir, confianzas RP), por ejemplo, vea [Token compatibles y los tipos de notificación](http://msdn.microsoft.com/library/azure/dn195587.aspx).

8.  En el cuadro de diálogo Editar reglas de notificación, haga clic en **Agregar regla**.
9.  Configurar las notificaciones de nombre, UPN y función como se muestra en la captura de pantalla y haga clic en **Finalizar**.

    ![](./media/web-sites-dotnet-lob-application-adfs/5-ldap-claims.png)

    A continuación, cree un nombre transitorio ID reclamar con los pasos que se muestra en los [Nombres de los identificadores de aserciones SAML](http://blogs.msdn.com/b/card/archive/2010/02/17/name-identifiers-in-saml-assertions.aspx).

9.  Haga clic en **Agregar regla de** nuevo.
10. Seleccione **Enviar notificaciones usando una regla personalizada** y haga clic en **siguiente**.
11. Pegue el siguiente idioma de la regla en el cuadro de la **regla personalizada** , asigne un nombre a la regla **Por el identificador de sesión** y haga clic en **Finalizar**.  
    <pre class="prettyprint">
    C1: [tipo == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"] &amp; &amp; 
    c2: [tipo == "http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant"] = > Agregar (almacenar = "_OpaqueIdStore", tipos = ("<mark>http://contoso.com/internal/sessionid</mark>"), consulta = "{0} { 1}; {2}; {3}; {4} ", parámetro ="useEntropy", el parámetro = c1. Value, parámetro = c1. OriginalIssuer, parámetro = "", parámetro = c2. Valor);
    </pre>

    La regla personalizada debería tener un aspecto similar a esta captura de pantalla:

    ![](./media/web-sites-dotnet-lob-application-adfs/6-per-session-identifier.png)

9.  Haga clic en **Agregar regla de** nuevo.
10. Seleccione **transformar una notificación entrante** y haga clic en **siguiente**.
11. Configurar la regla, como se muestra en la captura de pantalla (con el tipo de notificación que creó en la regla personalizada) y haga clic en **Finalizar**.

    ![](./media/web-sites-dotnet-lob-application-adfs/7-transient-name-id.png)

    Para obtener información detallada sobre los pasos necesarios para la notificación de nombre ID transitoria, vea [Identificadores de nombre de aserciones SAML](http://blogs.msdn.com/b/card/archive/2010/02/17/name-identifiers-in-saml-assertions.aspx).

12. En el cuadro de diálogo **Editar reglas de notificación** , haga clic en **Aplicar** . Ahora debe ser similar a la siguiente captura de pantalla:

    ![](./media/web-sites-dotnet-lob-application-adfs/8-all-claim-rules.png)

    > [AZURE.NOTE] De nuevo, asegúrese de que repita estos pasos para su entorno de depuración y la aplicación web publicado.

<a name="bkmk_test"></a>
## <a name="test-federated-authentication-for-your-application"></a>Probar la autenticación federada para la aplicación

Ya está listo para probar la lógica de autenticación de la aplicación con AD FS. En el entorno de prueba de AD FS, tengo un usuario de prueba que pertenece a un grupo de prueba en Active Directory (AD).

![](./media/web-sites-dotnet-lob-application-adfs/10-test-user-and-group.png)

Para probar la autenticación en el depurador, todo lo que necesita hacer ahora es tipo `F5`. Si desea probar la autenticación en la aplicación web publicada, vaya a la dirección URL.

Cuando se cargue la aplicación web, haga clic en **Iniciar sesión**. Ahora debe obtener un cuadro de diálogo de inicio de sesión o la página de inicio de sesión servida por AD FS, según el método de autenticación seleccionado por AD FS. Aquí es lo que aparece en Internet Explorer 11.

![](./media/web-sites-dotnet-lob-application-adfs/9-test-debugging.png)

Una vez que inicie sesión con un usuario en un dominio de Active Directory de la implementación de AD FS, ahora debe ver la página principal del nuevo con **Hola, <User Name>!** en la esquina. Aquí es lo que obtiene.

![](./media/web-sites-dotnet-lob-application-adfs/11-test-debugging-success.png)

Hasta el momento, ha correctamente de las siguientes maneras:

- La aplicación correctamente alcanzó AD FS y un identificador RP coincidente se encuentra en la base de datos de AD FS
- AD FS ha autenticado correctamente un usuario de AD y volver a la página principal de la aplicación a dirigir
- AD FS como enviado la notificación de nombre (http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name) a la aplicación, según indica el hecho de que el nombre de usuario se muestra en la esquina. 

Si falta la notificación de nombre, habría visto **Hola,!**. Si mira Views\Shared\_LoginPartial.cshtml, encontrará que utiliza `User.Identity.Name` para mostrar el nombre de usuario. Como se mencionó anteriormente, si la notificación de nombre del usuario autenticado está disponible en el token de SAML, ASP.NET hydrates esta propiedad con él. Para ver todas las notificaciones se envían por AD FS, coloque un punto de interrupción en Controllers\HomeController.cs, en el método de acción de índice. Después de que el usuario se autentica, inspeccionar la `System.Security.Claims.Current.Claims` colección.

![](./media/web-sites-dotnet-lob-application-adfs/12-test-debugging-all-claims.png) 

<a name="bkmk_authorize"></a>
## <a name="authorize-users-for-specific-controllers-or-actions"></a>Autorizar a los usuarios para determinados controladores o acciones

Puesto que ha incluido pertenencias a grupos como notificaciones de función en la configuración de confianza RP, ahora puede usar ellos directamente en el `[Authorize(Roles="...")]` decoración de controladores y acciones. En una aplicación de línea de negocio con el patrón de crear-lectura, actualización y eliminación (CRUD), puede autorizar funciones específicas para tener acceso a cada acción. Por ahora, simplemente intentará esta función en el controlador de inicio existente.

1. Abra Controllers\HomeController.cs.
2. Decorar los `About` y `Contact` métodos de acción similares al código siguiente, utilizando seguridad agrupar pertenencias a grupos que tiene el usuario autenticado.  
    <pre class="prettyprint">
    <mark>[Autorizar (Roles = "Grupo de pruebas")]</mark> 
    público ActionResult About() {ViewBag.Message = "Su aplicación descripción página.";

        return View();
    }

    <mark>[Autorizar (Roles = "Domain Admins")]</mark> 
    público ActionResult Contact() {ViewBag.Message = "Su contacto página.";

        return View();
    }  </pre>

    Puesto que he agregado **Probar el usuario** al **Grupo de prueba** en el entorno de prueba de AD FS, lo voy a utilizar grupo de prueba para comprobar la autorización en `About`. Para `Contact`, deberá probar el caso negativo de **Administradores de dominio**, a los que no pertenecen a **Probar el usuario** .

3. Iniciar el depurador escribiendo `F5` inicio de sesión y haga clic en **acerca de**. Ahora debe ver la `~/About/Index` página correctamente, si el usuario autenticado está autorizado para dicha acción.
4. Ahora haga clic en el **contacto**, que, en mi caso, no se debe autorizar el **Usuario de prueba** para la acción. Sin embargo, el explorador se redirige a AD FS, que se muestra al final de este mensaje:

    ![](./media/web-sites-dotnet-lob-application-adfs/13-authorize-adfs-error.png)

    Si investigar este error en el Visor de eventos en el servidor de AD FS, verá este mensaje de excepción:  
    <pre class="prettyprint">
   Microsoft.IdentityServer.Web.InvalidRequestException: MSIS7042: <mark>la misma sesión de explorador del cliente ha realizado '6' solicitudes en la última '11' segundos.</mark> Para obtener más información, póngase en contacto con el administrador.
   en Microsoft.IdentityServer.Web.Protocols.PassiveProtocolHandler.UpdateLoopDetectionCookie (contexto WrappedHttpListenerContext) en Microsoft.IdentityServer.Web.Protocols.WSFederation.WSFederationProtocolHandler.SendSignInResponse (contexto WSFederationContext, respuesta de MSISSignInResponse) en Microsoft.IdentityServer.Web.PassiveProtocolListener.ProcessProtocolRequest (ProtocolContext protocolContext, PassiveProtocolHandler protocolHandler) en Microsoft.IdentityServer.Web.PassiveProtocolListener.OnGetContext (contexto WrappedHttpListenerContext) </pre>

    El motivo de este error es que de forma predeterminada, MVC devuelve un 401 no autorizado cuando los roles de un usuario no están autorizados. Esto activa una solicitud de nueva autenticación para su proveedor de identidades (AD FS). Dado que el usuario ya está autenticado, AD FS devuelve a la misma página, que, a continuación, emite otra 401, crear un bucle de redirección. Invalidará de AuthorizeAttribute `HandleUnauthorizedRequest` método con lógica simple para mostrar algo que tiene sentido en lugar de seguir el bucle de redireccionamiento.

5. Crear un archivo en el proyecto denominado AuthorizeAttribute.cs y pegue el código siguiente.

        using System;
        using System.Web.Mvc;
        using System.Web.Routing;
        
        namespace WebApp_WSFederation_DotNet
        {
            [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited = true, AllowMultiple = true)]
            public class AuthorizeAttribute : System.Web.Mvc.AuthorizeAttribute
            {
                protected override void HandleUnauthorizedRequest(AuthorizationContext filterContext)
                {
                    if (filterContext.HttpContext.Request.IsAuthenticated)
                    {
                        filterContext.Result = new System.Web.Mvc.HttpStatusCodeResult((int)System.Net.HttpStatusCode.Forbidden);
                    }
                    else
                    {
                        base.HandleUnauthorizedRequest(filterContext);
                    }
                }
            }
        }

    El código de reemplazo envía un HTTP 403 (prohibido) en lugar de HTTP 401 (no autorizado) en los casos autenticados pero no autorizados.

6. Ejecutar el depurador nuevo con `F5`. Haga clic en **contacto** ahora muestra un mensaje de error más informativo (aunque poco atractivo):

    ![](./media/web-sites-dotnet-lob-application-adfs/14-unauthorized-forbidden.png)

7. Publicar la aplicación de nuevo a la aplicación de servicio Web Apps de Azure y probar el comportamiento de la aplicación activa.

<a name="bkmk_data"></a>
## <a name="connect-to-on-premises-data"></a>Conectarse a datos locales

Un motivo por el que quiera implementar la aplicación de línea de negocio con AD FS en lugar de Azure Active Directory es problemas de cumplimiento de memorizar organización datos fuera de las instalaciones. Esto puede también quiere decir que la aplicación web de Azure debe tener acceso a bases de datos local, ya que no tiene permiso para usar la [Base de datos SQL](/services/sql-database/) como el nivel de datos para las aplicaciones web.

Aplicaciones de Azure aplicación de servicio Web admite el acceso a las bases de datos local con dos métodos: [Híbrido conexiones](../biztalk-services/integration-hybrid-connection-overview.md) y [Redes virtuales](web-sites-integrate-with-vnet.md). Para obtener más información, vea [usar VNET integración y conexiones híbrido con aplicaciones de Azure aplicación de servicio Web](https://azure.microsoft.com/blog/2014/10/30/using-vnet-or-hybrid-conn-with-websites/).

<a name="bkmk_resources"></a>
## <a name="further-resources"></a>Recursos adicionales

- [Proteger la aplicación con SSL y el atributo de autorizar](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md#protect-the-application-with-ssl-and-the-authorize-attribute)
- [Autenticar con local de Active Directory en la aplicación de Azure](web-sites-authentication-authorization.md)
- [Crear una aplicación de Azure de línea de negocio con autenticación de Azure Active Directory](web-sites-dotnet-lob-application-azure-ad.md)
- [Use la opción de autenticación de organización local (ADFS) con ASP.NET en Visual Studio de 2013](http://www.cloudidentity.com/blog/2014/02/12/use-the-on-premises-organizational-authentication-option-adfs-with-asp-net-in-visual-studio-2013/)
- [Migrar un proyecto de Web VS2013 de WIF a Katana](http://www.cloudidentity.com/blog/2014/09/15/MIGRATE-A-VS2013-WEB-PROJECT-FROM-WIF-TO-KATANA/)
- [Información general de servicios de federación de Active Directory](http://technet.microsoft.com/library/hh831502.aspx)
- [Especificación WS-Federation 1.1](http://download.boulder.ibm.com/ibmdl/pub/software/dw/specs/ws-fed/WS-Federation-V1-1B.pdf?S_TACT=105AGX04&S_CMP=LP)


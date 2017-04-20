<properties
    pageTitle="Cómo habilitar la publicación de aplicaciones cliente nativo con aplicaciones de proxy | Microsoft Azure"
    description="Describe cómo habilitar aplicaciones cliente nativo para comunicarse con Azure AD aplicación Proxy conector para proporcionar acceso remoto seguro a sus aplicaciones locales."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/22/2016"
    ms.author="kgremban"/>

# <a name="how-to-enable-native-client-apps-to-interact-with-proxy-applications"></a>Cómo habilitar aplicaciones cliente nativo interactuar con el servidor proxy de aplicaciones

Azure Proxy de aplicación de Active Directory se utiliza ampliamente para publicar aplicaciones de explorador como SharePoint, Outlook Web Access y línea personalizada de aplicaciones empresariales. También puede utilizarse para publicar aplicaciones cliente nativo, que se diferencian de aplicaciones web que se instalan en un dispositivo. Para ello, auxiliares Azure AD emitido tokens que se envían en encabezados HTTP autorizar estándar.

![Relación entre los usuarios finales, Azure Active Directory y aplicaciones publicadas](./media/active-directory-application-proxy-native-client/richclientflow.png)

El método recomendado para publicar estas aplicaciones es usar la biblioteca de autenticación de Azure AD, que se encarga de los problemas de autenticación y es compatible con muchos entornos de cliente diferente. Proxy de la aplicación se adapta a la [Aplicación nativa para el escenario de Web API](active-directory-authentication-scenarios.md#native-application-to-web-api). El proceso para llevar a cabo esta es la siguiente:

## <a name="step-1-publish-your-application"></a>Paso 1: Publicar su aplicación

Publicar la aplicación proxy como lo haría con cualquier otra aplicación, asignar a los usuarios y proporcionarles premium o licencias básicas. Para obtener más información, consulte [publicar aplicaciones con Proxy de la aplicación](active-directory-application-proxy-publish.md).

## <a name="step-2-configure-your-application"></a>Paso 2: Configurar la aplicación

Configure su aplicación nativa como sigue:

1. Inicie sesión en el portal de clásico de Azure.
2. Seleccione el icono de Active Directory en el menú de la izquierda y, a continuación, seleccione el directorio.
3. En el menú superior, haga clic en **aplicaciones**. Si no hay aplicaciones se han agregado al directorio, esta página solo mostrará el vínculo **Agregar una aplicación** . Haga clic en el vínculo o, como alternativa, puede hacer clic en el botón **Agregar** en la barra de comandos.
4. En la página **¿Qué desea hacer** , haga clic en el vínculo para **Agregar una aplicación se desarrolla mi organización**.
5. En la página **Díganos acerca de la aplicación** , especifique un nombre para la aplicación y elija la **aplicación cliente nativa**. Haga clic en el icono de flecha para continuar.
6. En la página **información de la aplicación** , proporcionar la **Redirección URI** de la aplicación de cliente nativo y luego haga clic en la marca de verificación para finalizar.

Se ha agregado la aplicación y, a continuación, se le dirigirá a la página de inicio rápido de la aplicación.

## <a name="step-3-grant-access-to-other-applications"></a>Paso 3: Conceder acceso a otras aplicaciones

Habilitar la aplicación nativa exponer a otras aplicaciones en su directorio:

1. En el menú superior, haga clic en **aplicaciones**, seleccione la nueva aplicación nativa y, a continuación, haga clic en **Configurar**.
2. Desplácese hacia abajo hasta la sección **permisos a otras aplicaciones** . Haga clic en el botón **Agregar aplicación** , seleccione la aplicación de servidor proxy que desea conceder acceso a la aplicación nativa y haga clic en la marca de verificación en la esquina inferior derecha. En el menú desplegable de **Permisos de delegado** , seleccione el permiso nuevo.

![Permisos para la captura de pantalla de otra aplicaciones - Agregar aplicación](./media/active-directory-application-proxy-native-client/delegate_native_app.png)

## <a name="step-4-edit-the-active-directory-authentication-library"></a>Paso 4: Editar la biblioteca de autenticación de Active Directory

Editar el código de la aplicación nativa en el contexto de autenticación de la Active Directory autenticación biblioteca (ADAL) para incluir lo siguiente:

        // Acquire Access Token from AAD for Proxy Application
        AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/<TenantId>");
        AuthenticationResult result = authContext.AcquireToken("< Frontend Url of Proxy App >",
                                                        "< Client Id of the Native app>",
                                                        new Uri("< Redirect Uri of the Native App>"),
                                                        PromptBehavior.Never);

        //Use the Access Token to access the Proxy Application
        HttpClient httpClient = new HttpClient();
        httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
        HttpResponseMessage response = await httpClient.GetAsync("< Proxy App API Url >");

Las variables deben reemplazarse como sigue:

- **TenantId** puede encontrarse en el GUID en la dirección URL de la página de **configuración** de la aplicación, justo después de "Directorio /".
- **Dirección URL de front-end** es la dirección URL de front-end especificado en la aplicación de Proxy y se pueden encontrar en la página de **configuración** de la aplicación de proxy.
- **Identificador de cliente** de la aplicación nativa se encuentra en la página de **configuración** de la aplicación nativa.
- **Redirigir URI de la aplicación nativa** se puede encontrar en la página de **configuración** de la aplicación nativa.

![Configurar la nueva aplicación nativa captura de pantalla de página](./media/active-directory-application-proxy-native-client/new_native_app.png)

Para obtener más información acerca del flujo de la aplicación nativa, vea [aplicación nativa para web API](active-directory-authentication-scenarios.md#native-application-to-web-api).


## <a name="see-also"></a>Vea también

- [Publicar aplicaciones usando su propio nombre de dominio](active-directory-application-proxy-custom-domains.md)
- [Habilitar el acceso condicional](active-directory-application-proxy-conditional-access.md)
- [Trabajar con aplicaciones de notificaciones](active-directory-application-proxy-claims-aware-apps.md)
- [Habilitar inicio de sesión único](active-directory-application-proxy-sso-using-kcd.md)

Para las últimas noticias y actualizaciones, consulte el [blog de Proxy de aplicación](http://blogs.technet.com/b/applicationproxyblog/)

<properties
    pageTitle="Trabajar con notificaciones aplicaciones compatibles en Proxy de aplicación"
    description="Trata sobre cómo ponerse en marcha con Proxy de aplicación de Azure AD."
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



# <a name="working-with-claims-aware-apps-in-application-proxy"></a>Trabajar con notificaciones aplicaciones compatibles en Proxy de aplicación

Aplicaciones de reclamaciones considerar realizan una redirección a la seguridad Token servicio (STS), que a su vez solicita las credenciales del usuario a cambio de un símbolo antes de redirigir al usuario a la aplicación. Para habilitar el Proxy de la aplicación trabajar con estos redireccionamientos, deben realizarse los siguientes pasos.

## <a name="prerequisites"></a>Requisitos previos
Antes de realizar este procedimiento, asegúrese de que el STS de la aplicación de cuenta de reclamaciones redirige a está disponible fuera de su red local.

## <a name="azure-classic-portal-configuration"></a>Configuración del portal clásica Azure

1. Publicar su aplicación según las instrucciones descritas en [publicar aplicaciones con Proxy de la aplicación](active-directory-application-proxy-publish.md).
2. En la lista de aplicaciones, seleccione la aplicación de cuenta de notificaciones y haga clic en **Configurar**.
3. Si eligió **paso** como su **Método de autenticación previa**, asegúrese de seleccionar **HTTPS** como la combinación de la **Dirección URL externa** .
4. Si eligió **Azure Active Directory** como su **Método de autenticación previa**, seleccione **Ninguno** como **Método de autenticación interno**.


## <a name="adfs-configuration"></a>Configuración de ADFS

1. Abra la administración de ADFS.
2. Vaya a **Confiar fiesta confía**, haga clic con el botón secundario en la aplicación que está publicando con Proxy de la aplicación y elija **Propiedades**.  
  ![Confianza confianzas de fiesta haga clic en el nombre de la aplicación - screentshot](./media/active-directory-application-proxy-claims-aware-apps/appproxyrelyingpartytrust.png)  
3. En la pestaña **extremos** , en **tipo de extremo**, seleccione **WS-Federation**.
4. En la **Dirección URL de confianza** , escriba la dirección URL especificada en el Proxy de aplicación en la **Dirección URL externa** y haga clic en **Aceptar**.  
  ![Agregar un punto final - establecer valor de dirección URL de confianza - captura de pantalla](./media/active-directory-application-proxy-claims-aware-apps/appproxyendpointtrustedurl.png)  

## <a name="see-also"></a>Vea también

- [Publicar aplicaciones con Proxy de aplicación](active-directory-application-proxy-publish.md)
- [Habilitar inicio de sesión único](active-directory-application-proxy-sso-using-kcd.md)
- [Solucionar problemas que tiene con Proxy de aplicación](active-directory-application-proxy-troubleshoot.md)
- [Habilitar aplicaciones cliente nativo interactuar con las aplicaciones de proxy](active-directory-application-proxy-native-client.md)

Para las últimas noticias y actualizaciones, consulte el [blog de Proxy de aplicación](http://blogs.technet.com/b/applicationproxyblog/)

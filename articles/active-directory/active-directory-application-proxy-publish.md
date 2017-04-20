<properties
    pageTitle="Publicar aplicaciones con Proxy de la aplicación de Azure AD | Microsoft Azure"
    description="Publicar aplicaciones local en la nube con Proxy de aplicación de Azure AD."
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
    ms.topic="get-started-article"
    ms.date="07/19/2016"
    ms.author="kgremban"/>


# <a name="publish-applications-using-azure-ad-application-proxy"></a>Publicar aplicaciones mediante Proxy de aplicación de Azure AD

Proxy de aplicación de Azure AD le ayuda a los trabajadores remotos mediante la publicación de aplicaciones local para tener acceso a través de internet. En este punto, ya que debería tener [habilitado Proxy de aplicación en el portal de clásico de Azure](active-directory-application-proxy-enable.md). En este artículo le guiará por los pasos para publicar aplicaciones que se ejecutan en su red local y proporcionan acceso remoto seguro desde fuera de su red. Después de completar este artículo, estará listo para configurar la aplicación con información personalizada o requisitos de seguridad.

> [AZURE.NOTE] Proxy de la aplicación es una característica que solo está disponible si ha actualizado a la Premium o edición básica de Azure Active Directory. Para obtener más información, vea [ediciones de Azure Active Directory](active-directory-editions.md).

## <a name="publish-an-app-using-the-wizard"></a>Publicar una aplicación mediante el Asistente

1. Inicie sesión como administrador en el [portal de clásico de Azure](https://manage.windowsazure.com/).
2. Vaya a Active Directory y seleccione el directorio donde se ha habilitado Proxy de la aplicación.

    ![Active Directory - icono](./media/active-directory-application-proxy-publish/ad_icon.png)

3. Haga clic en la pestaña de **aplicaciones** y, a continuación, haga clic en el botón **Agregar** en la parte inferior de la pantalla

    ![Agregar aplicación](./media/active-directory-application-proxy-publish/aad_appproxy_selectdirectory.png)

4. Seleccione **publicar una aplicación que serán accesible desde fuera de su red**.

    ![Publicar una aplicación que será accesible desde fuera de su red](./media/active-directory-application-proxy-publish/aad_appproxy_addapp.png)

5. Proporcione la siguiente información acerca de la aplicación:

    - **Nombre**: el nombre descriptivo para la aplicación. Debe ser único dentro del directorio.
    - **Dirección URL interna**: la dirección que usa el conector de Proxy de aplicación para tener acceso a la aplicación desde dentro de la red privada. Puede proporcionar una ruta de acceso específica en el servidor back-end para publicar, mientras que el resto del servidor es no publicado. De esta forma, puede publicar sitios diferentes en el mismo servidor y dar a cada uno de sus propias reglas de nombre y access.

        > [AZURE.TIP] Si publica una ruta de acceso, asegúrese de que incluye todas las imágenes es necesarias, secuencias de comandos y hojas de estilos para la aplicación. Por ejemplo, si la aplicación se encuentra en https://yourapp/app y utiliza imágenes que se encuentra en https://yourapp/media, debe publicar https://yourapp/ como la ruta de acceso.

    - **Método de autenticación previa**: cómo Proxy de aplicación comprueba los usuarios antes de dar acceso a la aplicación. Elija una de las opciones en el menú desplegable.

        - Azure Active Directory: Proxy de la aplicación redirige a los usuarios que inicie sesión con Azure AD, que autentica sus permisos para el directorio y la aplicación.
        - Paso a través: Los usuarios no tienen autenticar para tener acceso a la aplicación.

    ![Propiedades de la aplicación](./media/active-directory-application-proxy-publish/aad_appproxy_appproperties.png)  

6. Para finalizar al asistente, haga clic en la marca de verificación en la parte inferior de la pantalla. Ahora, la aplicación se define en Azure AD.


## <a name="assign-users-and-groups-to-the-application"></a>Asignar usuarios y grupos a la aplicación

Para los usuarios tener acceso a su aplicación publicada, necesita asignarlas individualmente o en grupos. (Recuerde asignar acceso, demasiado). Esto requiere que cada usuario tiene una licencia para Azure básica o superior. Puede asignar licencias de forma individual o en grupos. Para obtener más detalles, vea [asignar usuarios a una aplicación](active-directory-applications-guiding-developers-assigning-users.md) . 

Para las aplicaciones que requieren autenticación previa, esto concede permisos para usar la aplicación. Para las aplicaciones que no requieren autenticación previa, los usuarios aún pueden asignar a la aplicación para que aparezca en su lista de aplicaciones, como Mis aplicaciones.

1. Cuando finalice al Asistente para agregar la aplicación, verá la página de inicio rápido de la aplicación. Para administrar quién tiene acceso a la aplicación, seleccione **usuarios y grupos**.

    ![Inicio rápido de Proxy de aplicación asignar usuarios - captura de pantalla](./media/active-directory-application-proxy-publish/aad_appproxy_usersgroups.png)

2. Buscar grupos específicos en el directorio, o mostrar todos los usuarios. Para mostrar los resultados de búsqueda, haga clic en la marca de verificación.

    ![Buscar grupos o usuarios: captura de pantalla](./media/active-directory-application-proxy-publish/aad_appproxy_search.png)

2. Seleccione cada usuario o grupo que desea asignar a esta aplicación y haga clic en **asignar**. Se le pedirá que confirme esta acción.

> [AZURE.NOTE] Para las aplicaciones de la autenticación de Windows, puede asignar sólo los usuarios y grupos a los que se sincronizan desde Active Directory local. No se pueden asignar los usuarios que inicie sesión con una cuenta de Microsoft y los invitados para aplicaciones publicadas con Proxy de aplicación de Azure Active Directory. Asegúrese de que los usuarios inicie sesión con credenciales que forman parte del mismo dominio de la aplicación que está publicando.

## <a name="test-your-published-application"></a>Probar la aplicación publicada

Una vez que haya publicado la aplicación, puede probar se abra, vaya a la dirección URL que ha publicado. Asegúrese de que tiene acceso, que se representa correctamente y que todo funciona según lo esperado. Si tiene problemas o recibe un mensaje de error, pruebe la [Guía de solución de problemas](active-directory-application-proxy-troubleshoot.md).

## <a name="configure-your-application"></a>Configurar la aplicación

Puede modificar aplicaciones publicadas o configurar opciones avanzadas en la página Configurar. En esta página, puede personalizar su aplicación cambiando el nombre o cargar un logotipo. También puede administrar las reglas de acceso como el método de autenticación previa o la autenticación multifactor.

![Configuración avanzada](./media/active-directory-application-proxy-publish/aad_appproxy_configure.png)


Después de publicar aplicaciones con Azure aplicación Proxy de Active Directory, que aparecen en la lista de aplicaciones en Azure AD y puede administrar allí.

Si deshabilita los servicios de Proxy de aplicación después de haber publicado aplicaciones, ya no están accesibles desde fuera de su red privada. No se eliminan las aplicaciones.

Para ver una aplicación y asegúrese de que es accesible, haga doble clic en el nombre de la aplicación. Si se deshabilita el servicio Proxy de la aplicación y la aplicación no está disponible, aparece un mensaje de advertencia en la parte superior de la pantalla.

Para eliminar una aplicación, seleccione una aplicación en la lista y, a continuación, haga clic en **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

- [Publicar aplicaciones usando su propio nombre de dominio](active-directory-application-proxy-custom-domains.md)
- [Habilitar inicio de sesión único](active-directory-application-proxy-sso-using-kcd.md)
- [Habilitar el acceso condicional](active-directory-application-proxy-conditional-access.md)
- [Trabajar con aplicaciones de notificaciones](active-directory-application-proxy-claims-aware-apps.md)

Para las últimas noticias y actualizaciones, consulte el [blog de Proxy de aplicación](http://blogs.technet.com/b/applicationproxyblog/)

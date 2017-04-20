<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con envío | Microsoft Azure" 
    description="¡Obtenga información sobre cómo utilizar envío con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más!" 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="09/29/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-envoy"></a>Tutorial: Integración de Azure Active Directory con envío
  
El objetivo de este tutorial es mostrar la integración de Azure y envío.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inquilino de envío
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a envío podrán al inicio de sesión único en la aplicación en el sitio de su empresa de envío (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
El escenario descrito en este tutorial consta de los siguientes elementos:

1.  Habilitar la integración de la aplicación de envío
2.  Configurar el inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-envoy-tutorial/IC776759.png "Escenario")
##<a name="enabling-the-application-integration-for-envoy"></a>Habilitar la integración de la aplicación de envío
  
El objetivo de esta sección es describen cómo habilitar la integración de la aplicación de envío.

###<a name="to-enable-the-application-integration-for-envoy-perform-the-following-steps"></a>Para habilitar la integración de la aplicación de envío, siga estos pasos:

1.  En el portal clásico Azure, en el panel de navegación de la izquierda, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-envoy-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-envoy-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-envoy-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-envoy-tutorial/IC749322.png "Agregar una aplicación de muestra")

6.  En el **cuadro de búsqueda**, escriba **envío**.

    ![Galería de aplicación] (./media/active-directory-saas-envoy-tutorial/IC776760.png "Galería de aplicación")

7.  En el panel de resultados, seleccione el **envío**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Envío] (./media/active-directory-saas-envoy-tutorial/IC776777.png "Envío")
##<a name="configuring-single-sign-on"></a>Configurar el inicio de sesión único
  
El objetivo de esta sección es describen cómo habilitar los usuarios autenticar envío con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
Configuración de inicio de sesión único para envío debe recuperar un valor de la huella digital de un certificado.  
Si no está familiarizado con este procedimiento, consulte [cómo recuperar el valor de la huella digital de un certificado](http://youtu.be/YKQF266SAxI)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación de **envío** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Habilitar inicio de sesión único] (./media/active-directory-saas-envoy-tutorial/IC776778.png "Habilitar inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión el envío** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-envoy-tutorial/IC776779.png "Configurar inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto de **Envío de inicio de sesión en la URL** , escriba la dirección URL con el siguiente patrón "https://*\<nombre del inquilino\>. Envoy.com*"y, a continuación, haga clic en **siguiente**.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-envoy-tutorial/IC776780.png "Configurar la dirección URL de la aplicación")

4.  En la página **configurar inicio de sesión único en envío** para descargar el certificado, haga clic en **Descargar certificado**y, a continuación, guarde el archivo de certificado localmente como **c:\\Envoy.cer**.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-envoy-tutorial/IC776781.png "Configurar inicio de sesión único")

5.  En una ventana de explorador web diferente, inicie sesión en el sitio de su empresa de envío como administrador.

6.  En la barra de herramientas en la parte superior, haga clic en **configuración**.

    ![Envío] (./media/active-directory-saas-envoy-tutorial/IC776782.png "Envío")

7.  Haga clic en la **compañía**.

    ![Compañía] (./media/active-directory-saas-envoy-tutorial/IC776783.png "Compañía")

8.  Haga clic en **SAML**.

    ![SAML] (./media/active-directory-saas-envoy-tutorial/IC776784.png "SAML")

9.  En la sección Configuración de **Autenticación de SAML** , realice los pasos siguientes:

    ![Autenticación de SAML] (./media/active-directory-saas-envoy-tutorial/IC776785.png "Autenticación de SAML")

    >[AZURE.NOTE] El valor para el ID de ubicación de HQ es automático generado por la aplicación.

    1.  Copie el valor de la **huella digital** del certificado exportado y, a continuación, péguelo en el cuadro de texto de la **huella** .  

        >[AZURE.TIP] Para obtener más detalles, consulte [cómo recuperar el valor de la huella digital de un certificado](http://youtu.be/YKQF266SAxI)

    2.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en envío** , copie el valor de **Dirección URL de SAML SSO** y péguelo en el cuadro de texto de **HTTP SAML URL del proveedor de identidades** .
    3.  Haga clic en **Guardar cambios**.

10. En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar inicio de sesión único] (./media/active-directory-saas-envoy-tutorial/IC776786.png "Configurar inicio de sesión único")
##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
No hay ningún elemento de acción para configurar el envío de aprovisionamiento de usuarios.  
Cuando un usuario asignado intenta iniciar sesión en envío mediante el panel de acceso, envío comprueba si existe el usuario.  
Si no hay ninguna cuenta de usuario aún, se crea automáticamente por envío.
##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-envoy-perform-the-following-steps"></a>Para asignar a los usuarios a envío, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación de **envío **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-envoy-tutorial/IC776787.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-envoy-tutorial/IC767830.png "Sí")
  
Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
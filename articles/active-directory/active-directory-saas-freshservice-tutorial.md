<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con FreshService | Microsoft Azure" 
    description="Aprenda a usar FreshService con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más." 
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

#<a name="tutorial-azure-active-directory-integration-with-freshservice"></a>Tutorial: Integración de Azure Active Directory con FreshService
  
El objetivo de este tutorial es mostrar la integración de Azure y FreshService.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inicio de sesión único de FreshService activado suscripción
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a FreshService será capaz de inicio de sesión único en la aplicación con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
El escenario descrito en este tutorial se compone de los siguientes bloques de creación:

1.  Habilitar la integración de aplicación para FreshService
2.  Configuración de inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-freshservice-tutorial/IC790807.png "Escenario")
##<a name="enabling-the-application-integration-for-freshservice"></a>Habilitar la integración de aplicación para FreshService
  
El objetivo de esta sección es describen cómo habilitar la integración de aplicación para FreshService.

###<a name="to-enable-the-application-integration-for-freshservice-perform-the-following-steps"></a>Para habilitar la integración de aplicación para FreshService, siga estos pasos:

1.  En el portal de clásico Azure, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-freshservice-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-freshservice-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-freshservice-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-freshservice-tutorial/IC749322.png "Agregar una aplicación de gallerry")

6.  En el **cuadro de búsqueda**, escriba **FreshService**.

    ![Galería de aplicación] (./media/active-directory-saas-freshservice-tutorial/IC790808.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **FreshService**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Freshservice] (./media/active-directory-saas-freshservice-tutorial/IC790809.png "Freshservice")
##<a name="configuring-single-sign-on"></a>Configuración de inicio de sesión único
  
El objetivo de esta sección es describen cómo habilitar los usuarios autenticar a FreshService con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
Configuración de inicio de sesión único para FreshService requiere que recuperar un valor de la huella digital de un certificado.  
Si no está familiarizado con este procedimiento, consulte [cómo recuperar el valor de la huella digital de un certificado](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación **FreshService** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-freshservice-tutorial/IC790810.png "Configurar el inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión FreshService** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-freshservice-tutorial/IC790811.png "Configurar el inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto **FreshService inicio de sesión en la URL** , escriba la dirección URL usada por los usuarios para iniciar sesión su aplicación Freshdesk (por ejemplo: "*http://democompany.freshservice.com/*") y, a continuación, haga clic en **siguiente**.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-freshservice-tutorial/IC790812.png "Configurar la dirección URL de la aplicación")

4.  En la página **configurar inicio de sesión único en FreshService** , para descargar el certificado, haga clic en **Descargar certificado**y, a continuación, guarde el archivo de certificado localmente en el equipo.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-freshservice-tutorial/IC790813.png "Configurar el inicio de sesión único")

5.  En una ventana de explorador web diferente, inicie sesión en el sitio de su empresa FreshService como administrador.

6.  En el menú de la parte superior, haga clic en **Administrador**.

    ![Administrador] (./media/active-directory-saas-freshservice-tutorial/IC790814.png "Administrador")

7.  En el **Portal de cliente**, haga clic en **seguridad**.

    ![Seguridad] (./media/active-directory-saas-freshservice-tutorial/IC790815.png "Seguridad")

8.  En la sección **seguridad** , siga estos pasos:

    ![Inicio de sesión único] (./media/active-directory-saas-freshservice-tutorial/IC790816.png "Inicio de sesión único")

    1.  Cambiar **OnON de inicio de sesión único**.
    2.  Seleccione **SAML SSO**.
    3.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en FreshService** , copie el valor de **Dirección URL de inicio de sesión remota** y, a continuación, péguelo en el cuadro de texto **Dirección URL de inicio de sesión de SAML** .
    4.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en FreshService** , copie el valor de **Dirección URL de cierre de sesión remota** y, a continuación, péguelo en el cuadro de texto **Dirección URL de cierre de sesión** .
    5.  Copie el valor de la **huella digital** del certificado exportado y, a continuación, péguelo en el cuadro de texto de la **Huella digital de certificado de seguridad** .
    
        >[AZURE.TIP]Para obtener más detalles, consulte [cómo recuperar el valor de la huella digital de un certificado](http://youtu.be/YKQF266SAxI)

9.  En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-freshservice-tutorial/IC790817.png "Configurar el inicio de sesión único")
##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD iniciar sesión en FreshService, debe aprovisionar en FreshService.  
En el caso de FreshService, aprovisionamiento es una tarea manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para crear cuentas de usuario, siga estos pasos:

1.  Inicie sesión como administrador en el sitio de su empresa **FreshService** .

2.  En el menú de la parte superior, haga clic en **Administrador**.

    ![Administrador] (./media/active-directory-saas-freshservice-tutorial/IC790814.png "Administrador")

3.  En la sección **Administración de usuarios** , haga clic en **los solicitantes**.

    ![Solicitantes] (./media/active-directory-saas-freshservice-tutorial/IC790818.png "Solicitantes")

4.  Haga clic en **nuevo solicitante**.

    ![Solicitantes nuevos] (./media/active-directory-saas-freshservice-tutorial/IC790819.png "Solicitantes nuevos")

5.  En la sección **Solicitante nuevo** , siga estos pasos:

    ![Nuevo solicitante] (./media/active-directory-saas-freshservice-tutorial/IC790820.png "Nuevo solicitante")

    1.  Escriba los atributos de **correo electrónico** y el **nombre** de una cuenta de Azure Active Directory válida que desee aprovisionar en los cuadros de texto relacionados.
    2.  Haga clic en **Guardar**.

    >[AZURE.NOTE] Titular de la cuenta de Azure Active Directory recibirá un correo electrónico que incluye un vínculo para confirmar la cuenta antes de que esté activa

>[AZURE.NOTE] Puede usar cualquier otros FreshService usuario cuenta herramientas de creación o API proporcionan por FreshService a cuentas de usuario AAD de aprovisionamiento.

##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-freshservice-perform-the-following-steps"></a>Para asignar a los usuarios a FreshService, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación **FreshService **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-freshservice-tutorial/IC790821.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-freshservice-tutorial/IC767830.png "Sí")
  
Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
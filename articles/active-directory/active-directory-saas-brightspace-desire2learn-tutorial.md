<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Brightspace por Desire2Learn | Microsoft Azure" 
    description="¡Obtenga información sobre cómo usar Brightspace por Desire2Learn con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más!" 
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

#<a name="tutorial-azure-active-directory-integration-with-brightspace-by-desire2learn"></a>Tutorial: Integración de Azure Active Directory con Brightspace por Desire2Learn

El objetivo de este tutorial es mostrar la integración de Azure y Brightspace por Desire2Learn.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un Brightspace por Desire2Learn inicio de sesión único habilitado suscripción

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Brightspace por Desire2Learn será capaz de inicio de sesión único en la aplicación en su Brightspace el sitio de empresa de Desire2Learn (servicio proveedor inicia sesión) o utilizando la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

El escenario descrito en este tutorial se compone de los siguientes bloques de creación:

1.  Habilitar la integración de aplicación para Brightspace por Desire2Learn
2.  Configuración de inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798957.png "Escenario")
##<a name="enabling-the-application-integration-for-brightspace-by-desire2learn"></a>Habilitar la integración de aplicación para Brightspace por Desire2Learn

El objetivo de esta sección es describen cómo habilitar la integración de aplicación para Brightspace por Desire2Learn.

###<a name="to-enable-the-application-integration-for-brightspace-by-desire2learn-perform-the-following-steps"></a>Para habilitar la integración de aplicación para Brightspace por Desire2Learn, siga estos pasos:

1.  En el portal de clásico Azure, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC749322.png "Agregar una aplicación de gallerry")

6.  En el **cuadro de búsqueda**, escriba **Brightspace por Desire2Learn**.

    ![Galería de Apllication] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798958.png "Galería de Apllication")

7.  En el panel de resultados, seleccione **Brightspace por Desire2Learn**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Brightspace por Desire2Learn] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC799321.png "Brightspace por Desire2Learn")
##<a name="configuring-single-sign-on"></a>Configuración de inicio de sesión único

El objetivo de esta sección es describen cómo habilitar los usuarios autenticar Brightspace por Desire2Learn con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación de **Brightspace por Desire2Learn** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798959.png "Configurar el inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión Brightspace por Desire2Learn** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798960.png "Configurar el inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , realice los pasos siguientes:

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798961.png "Configurar la dirección URL de la aplicación")

    1.  En el cuadro de texto de **Inicio de sesión en la dirección URL** , escriba la dirección URL utilizada por los usuarios para iniciar sesión en su **Brightspace por Desire2Learn** (por ejemplo: *https://partnershowcase.desire2learn.com/Shibboleth.sso/Login?entityID=https://sts.windows-ppe.net/5caf9349-fd93-4a74-b064-0070f65bfb49/&target=https%3A%2F%2Fpartnershowcase.desire2learn.com%2Fd2l%2FshibbolethSSO%2Faspinfo.asp*).
    2.  Haga clic en **siguiente**

4.  En la página **configurar inicio de sesión único en Brightspace por Desire2Learn** , para descargar los metadatos, haga clic en **descargar metadatos**y, a continuación, guarde los metadatos en el equipo.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798962.png "Configurar el inicio de sesión único")

5.  Enviar el archivo descargado metadatos a su Brightspace Desire2Learn equipo de soporte técnico.

    >[AZURE.NOTE] Su Brightspace equipo de soporte técnico de Desire2Learn tiene que hacer la configuración de SSO real.
Recibirá una notificación cuando se ha habilitado SSO para la suscripción.

6.  En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798963.png "Configurar el inicio de sesión único")
##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD iniciar sesión en Brightspace por Desire2Learn, debe aprovisionar en Brightspace por Desire2Learn.  
En el caso de Brightspace por Desire2Learn, las cuentas de usuario es necesario crear por su Brightspace Desire2Learn equipo de soporte técnico.

>[AZURE.NOTE] Puede usar cualquier otro Brightspace por las herramientas de creación de cuentas de usuario de Desire2Learn o API proporcionan por Brightspace por Desire2Learn aprovisionar Azure Active Directory las cuentas de usuario.

##<a name="assigning-users"></a>Asignación de usuarios

Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-brightspace-by-desire2learn-perform-the-following-steps"></a>Para asignar a los usuarios a Brightspace por Desire2Learn, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación de **Brightspace por Desire2Learn **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798964.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC767830.png "Sí")

Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

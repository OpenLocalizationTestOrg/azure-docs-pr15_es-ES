<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con la nueva Relic | Microsoft Azure" 
    description="¡Obtenga información sobre cómo utilizar Relic nueva con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más!" 
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

#<a name="tutorial-azure-active-directory-integration-with-new-relic"></a>Tutorial: Integración de Azure Active Directory con la nueva Relic
  
El objetivo de este tutorial es mostrar cómo configurar el inicio de sesión único entre Azure Active Directory y Relic de nuevo.
  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un nuevo Relic inicio de sesión único habilitado suscripción
  
Después de completar este tutorial, los usuarios de Azure Active Directory que se ha asignado a Relic nuevo podrán al inicio de sesión único mediante el Panel de acceso AAD.

1.  Habilitar la integración de aplicación para Relic nueva
2.  Configuración de inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-new-relic-tutorial/IC797030.png "Escenario")
##<a name="enabling-the-application-integration-for-new-relic"></a>Habilitar la integración de aplicación para Relic nueva
  
El objetivo de esta sección es describen cómo habilitar la integración de aplicación para Relic de nuevo.

###<a name="to-enable-the-application-integration-for-new-relic-perform-the-following-steps"></a>Para habilitar la integración de aplicación para Relic nueva, siga estos pasos:

1.  En el portal clásico Azure, en el panel de navegación de la izquierda, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-new-relic-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-new-relic-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-new-relic-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-new-relic-tutorial/IC749322.png "Agregar una aplicación de muestra")

6.  En el **cuadro de búsqueda**, escriba **Relic de nuevo**.

    ![Galería de aplicación] (./media/active-directory-saas-new-relic-tutorial/IC797031.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **Relic nuevo**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Nueva Relic] (./media/active-directory-saas-new-relic-tutorial/IC797032.png "Nueva Relic")
##<a name="configuring-single-sign-on"></a>Configuración de inicio de sesión único
  
Esta sección describe cómo permitir a los usuarios autenticar Relic nueva con su cuenta de Azure Active Directory, con la federación basada en el protocolo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación **Relic nuevo** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar inicio de sesión único] (./media/active-directory-saas-new-relic-tutorial/IC769534.png "Configurar inicio de sesión único")

2.  En la página **¿cómo le gustaría usuarios a iniciar sesión Relic nueva** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-new-relic-tutorial/IC797033.png "Configurar el inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto **Nueva Relic inicio de sesión en dirección URL** , escriba la dirección URL utilizada por los usuarios para iniciar sesión la aplicación de nuevo Relic y, a continuación, haga clic en **siguiente**. 

    La aplicación dirección URL es su Relic nuevo inquilino (por ejemplo: *https://rpm.newrelic.com*):

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-new-relic-tutorial/IC797034.png "Configurar la dirección URL de la aplicación")

4.  En la página **configurar inicio de sesión único en Relic nueva** , para descargar el certificado, haga clic en **Descargar certificado**y, a continuación, guarde el archivo de certificado localmente en el equipo.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-new-relic-tutorial/IC797035.png "Configurar el inicio de sesión único")

5.  En una ventana de explorador web diferente, inicie sesión el sitio de su empresa **Relic nuevo** como administrador.

6.  En el menú de la parte superior, haga clic en **Configuración de la cuenta**.

    ![Configuración de la cuenta] (./media/active-directory-saas-new-relic-tutorial/IC797036.png "Configuración de la cuenta")

7.  Haga clic en la pestaña **seguridad y la autenticación** y, a continuación, haga clic en la ficha **Inicio de sesión único** .

    ![Inicio de sesión único] (./media/active-directory-saas-new-relic-tutorial/IC797037.png "Inicio de sesión único")

8.  En la página de diálogo SAML, realice los pasos siguientes:

    ![SAML] (./media/active-directory-saas-new-relic-tutorial/IC797038.png "SAML")

    1.  Haga clic en **Elegir archivo** para cargar el certificado de Azure Active Directory descargado.
    2.  En el portal de clásico Azure, en la página **configurar inicio de sesión único en Relic nuevo** , copie el valor de **Dirección URL de inicio de sesión remota** y péguelo en el cuadro de texto **dirección URL de inicio de sesión remota** .
    3.  En el portal de clásico Azure, en la página **configurar inicio de sesión único en Relic nuevo** , copie el valor de **Dirección URL de cierre de sesión remota** y péguelo en el cuadro de texto **dirección URL de inicio de sesión** .
    4.  Haga clic en **Guardar mis cambios**.

9.  En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-new-relic-tutorial/IC797039.png "Configurar el inicio de sesión único")
##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure Active Directory iniciar sesión en Relic nuevo, debe aprovisionar en Relic de nuevo.  
En el caso de nuevo Relic, aprovisionamiento es una tarea manual.

###<a name="to-provision-a-user-account-to-new-relic-perform-the-following-steps"></a>Para crear una cuenta de usuario a Relic nueva, siga estos pasos:

1.  Inicie sesión como administrador en el sitio de su empresa **Relic nuevo** .

2.  En el menú de la parte superior, haga clic en **Configuración de la cuenta**.

    ![Configuración de la cuenta] (./media/active-directory-saas-new-relic-tutorial/IC797040.png "Configuración de la cuenta")

3.  En el panel de la **cuenta** en el lado izquierdo, haga clic en **Resumen**y, a continuación, haga clic en **Agregar usuario**.

    ![Configuración de la cuenta] (./media/active-directory-saas-new-relic-tutorial/IC797041.png "Configuración de la cuenta")

4.  En el cuadro de diálogo **usuarios activos** , realice los pasos siguientes:

    ![Usuarios activos] (./media/active-directory-saas-new-relic-tutorial/IC797042.png "Usuarios activos")

    1.  En el cuadro de texto de **correo electrónico** , escriba la dirección de correo electrónico de un usuario de Azure Active Directory válido que desee aprovisionar.
    2.  **Función** seleccione **usuario**.
    3.  Haga clic en **Agregar este usuario**.

>[AZURE.NOTE]Puede usar cualquier otros Relic nuevo usuario cuenta herramientas de creación o API proporcionan por Relic nueva a cuentas de usuario AAD de aprovisionamiento.

##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-new-relic-perform-the-following-steps"></a>Para asignar a los usuarios a Relic nueva, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación **Relic nuevo** , haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-new-relic-tutorial/IC797043.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-new-relic-tutorial/IC767830.png "Sí")
  
Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).





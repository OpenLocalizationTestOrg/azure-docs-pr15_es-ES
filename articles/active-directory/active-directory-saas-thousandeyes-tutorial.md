<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con ThousandEyes | Microsoft Azure" 
    description="Aprenda a usar ThousandEyes con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más." 
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
    ms.date="09/11/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-thousandeyes"></a>Tutorial: Integración de Azure Active Directory con ThousandEyes
  
El objetivo de este tutorial es mostrar cómo configurar el inicio de sesión único entre Azure Active Directory (AD Azure) y ThousandEyes.
  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   ThousandEyes de sesión único habilitado suscripción
  
Después de completar este tutorial, los usuarios AAD a quién tiene asignar acceso ThousandEyes podrán al inicio de sesión único en la aplicación en el sitio de su empresa de ThousandEyes (servicio proveedor inicia sesión), o mediante el Panel de acceso de AAD.

1.  Habilitar la integración de aplicación para ThousandEyes
2.  Configuración de inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-thousandeyes-tutorial/IC790059.png "Escenario")

##<a name="enabling-the-application-integration-for-thousandeyes"></a>Habilitar la integración de aplicación para ThousandEyes
  
El objetivo de esta sección es describen cómo habilitar la integración de aplicación para ThousandEyes.

###<a name="to-enable-the-application-integration-for-thousandeyes-perform-the-following-steps"></a>Para habilitar la integración de aplicación para ThousandEyes, siga estos pasos:

1.  En el portal de clásico Azure, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-thousandeyes-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-thousandeyes-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-thousandeyes-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-thousandeyes-tutorial/IC749322.png "Agregar una aplicación de gallerry")

6.  En el **cuadro de búsqueda**, escriba **ThousandEyes**.

    ![Galería de aplicación] (./media/active-directory-saas-thousandeyes-tutorial/IC790060.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **ThousandEyes**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![ThousandEyes] (./media/active-directory-saas-thousandeyes-tutorial/IC790061.png "ThousandEyes")

##<a name="configuring-single-sign-on"></a>Configuración de inicio de sesión único
  
Esta sección describe cómo permitir a los usuarios autenticar ThousandEyes con su cuenta de Azure Active Directory, con la federación basada en el protocolo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación **ThousandEyes** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-thousandeyes-tutorial/IC790062.png "Configurar el inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión ThousandEyes** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-thousandeyes-tutorial/IC790063.png "Configurar el inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto **ThousandEyes inicio de sesión en la URL** , tipo utilizan los usuarios de la dirección URL para iniciar sesión en la aplicación ThousandEyes (por ejemplo: "*https://app.thousandeyes.com/login/sso*") y, a continuación, haga clic en **siguiente**. 

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-thousandeyes-tutorial/IC790064.png "Configurar la dirección URL de la aplicación")

4.  En la página **configurar inicio de sesión único en ThousandEyes** , para descargar el certificado, haga clic en **Descargar certificado**y, a continuación, guarde el archivo de certificado localmente en el equipo.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-thousandeyes-tutorial/IC790065.png "Configurar el inicio de sesión único")

5.  En una ventana de explorador web diferente, inicie sesión el sitio de su empresa **ThousandEyes** como administrador.

6.  En el menú de la parte superior, haga clic en **configuración**.

    ![Configuración] (./media/active-directory-saas-thousandeyes-tutorial/IC790066.png "Configuración")

7.  Haga clic en **cuenta**

    ![Cuenta] (./media/active-directory-saas-thousandeyes-tutorial/IC790067.png "Cuenta")

8.  Haga clic en la pestaña **seguridad y autenticación** .

    ![Seguridad y autenticación] (./media/active-directory-saas-thousandeyes-tutorial/IC790068.png "Seguridad y autenticación")

9.  En la sección **Configuración de inicio de sesión único** , realice los pasos siguientes:

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-thousandeyes-tutorial/IC790069.png "Configurar el inicio de sesión único")

    1.  Seleccione **Habilitar inicio de sesión único**.
    2.  En el portal de Microsoft Azure clásico, en la página **configurar inicio de sesión único en ThousandEyes** , copie el valor de **Dirección URL de inicio de sesión remota** y, a continuación, péguelo en el cuadro de texto **Dirección URL de la página de inicio de sesión** .
    3.  En el portal de Microsoft Azure clásico, en la página **configurar inicio de sesión único en ThousandEyes** , copie el valor de **Dirección URL de cierre de sesión remota** y, a continuación, péguelo en el cuadro de texto **Dirección URL de la página de cierre de sesión** .
    4.  En el portal de Microsoft Azure clásico, en la página **configurar inicio de sesión único en ThousandEyes** , copie el valor de **Dirección URL del emisor** y péguelo en el cuadro de texto del **Emisor del proveedor de identidades** .
    5.  **Certificado del proveedor de identidades**, haga clic en **Elegir archivo**y, a continuación, cargar el certificado que ha descargado desde el portal de Microsoft Azure clásico.
    6.  Haga clic en **Guardar**.

10. En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-thousandeyes-tutorial/IC790070.png "Configurar el inicio de sesión único")

##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD iniciar sesión en ThousandEyes, debe aprovisionar en ThousandEyes.  
En el caso de ThousandEyes, aprovisionamiento es una tarea manual.

###<a name="to-provision-a-user-account-to-thousandeyes-perform-the-following-steps"></a>Para crear una cuenta de usuario a ThousandEyes, siga estos pasos:

1.  Inicie sesión en el sitio de su empresa ThousandEyes como administrador.

2.  Haga clic en **configuración**.

    ![Configuración] (./media/active-directory-saas-thousandeyes-tutorial/IC790066.png "Configuración")

3.  Haga clic en **cuenta**.

    ![Cuenta] (./media/active-directory-saas-thousandeyes-tutorial/IC790067.png "Cuenta")

4.  Haga clic en la pestaña **cuentas y los usuarios** .

    ![Cuentas y los usuarios] (./media/active-directory-saas-thousandeyes-tutorial/IC790073.png "Cuentas y los usuarios")

5.  En la sección **Agregar usuarios y cuentas** , realice los pasos siguientes:

    ![Agregar cuentas de usuario] (./media/active-directory-saas-thousandeyes-tutorial/IC790074.png "Agregar cuentas de usuario")

    1.  Escriba el **nombre**, **correo electrónico** y otros detalles de una cuenta de Azure Active Directory válida que desee aprovisionar en los cuadros de texto relacionados.
    2.  Haga clic en **Agregar nuevo usuario para la cuenta**.

        >[AZURE.NOTE] El titular AAD recibirá un correo electrónico que incluye un vínculo para confirmar y activar la cuenta.

>[AZURE.NOTE] Puede usar cualquier otros ThousandEyes usuario cuenta herramientas de creación o API proporcionan por ThousandEyes a cuentas de usuario AAD de aprovisionamiento.

##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-thousandeyes-perform-the-following-steps"></a>Para asignar a los usuarios a ThousandEyes, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación **ThousandEyes** , haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-thousandeyes-tutorial/IC790075.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-thousandeyes-tutorial/IC767830.png "Sí")
  
Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

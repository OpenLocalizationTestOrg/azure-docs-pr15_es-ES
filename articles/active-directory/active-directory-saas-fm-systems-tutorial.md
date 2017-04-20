<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con FM: sistemas | Microsoft Azure" 
    description="Aprenda a usar FM: sistemas con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más!" 
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

#<a name="tutorial-azure-active-directory-integration-with-fm-systems"></a>Tutorial: Integración de Azure Active Directory con FM: sistemas
  
El objetivo de este tutorial es mostrar la integración de Azure y FM:Systems.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inicio de sesión único de FM:Systems activado suscripción
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a FM:Systems será capaz de inicio de sesión único en la aplicación en el sitio de su empresa de FM:Systems (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
El escenario descrito en este tutorial se compone de los siguientes bloques de creación:

1.  Habilitar la integración de aplicación para FM:Systems
2.  Configuración de inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-fm-systems-tutorial/IC795899.png "Escenario")
##<a name="enabling-the-application-integration-for-fmsystems"></a>Habilitar la integración de aplicación para FM:Systems
  
El objetivo de esta sección es describen cómo habilitar la integración de aplicación para FM:Systems.

###<a name="to-enable-the-application-integration-for-fmsystems-perform-the-following-steps"></a>Para habilitar la integración de aplicación para FM:Systems, siga estos pasos:

1.  En el portal de clásico Azure, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-fm-systems-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-fm-systems-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-fm-systems-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-fm-systems-tutorial/IC749322.png "Agregar una aplicación de gallerry")

6.  En el **cuadro de búsqueda**, escriba **FM:Systems**.

    ![Galería de aplicación] (./media/active-directory-saas-fm-systems-tutorial/IC795900.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **FM:Systems**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![FM: sistemas] (./media/active-directory-saas-fm-systems-tutorial/IC800213.png "FM: sistemas")
##<a name="configuring-single-sign-on"></a>Configuración de inicio de sesión único
  
El objetivo de esta sección es describen cómo habilitar los usuarios autenticar a FM:Systems con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación **FM:Systems** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-fm-systems-tutorial/IC790810.png "Configurar el inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión FM:Systems** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-fm-systems-tutorial/IC795901.png "Configurar el inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , realice los pasos siguientes:

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-fm-systems-tutorial/IC795902.png "Configurar la dirección URL de la aplicación")

    1.  En el cuadro de texto **FM:Systems inicio de sesión en la URL** , escriba la **Dirección URL de respuesta** de FM:Systems (por ejemplo: *https://dpr.fmshosted.com/fminteract/ConsumerService2.aspx*).  

        >[AZURE.WARNING] Puede obtener este valor desde su FM: sistemas de soportan técnico.

    2.  Haga clic en **siguiente**

4.  En la página **configurar inicio de sesión único en FM:Systems** , para descargar los metadatos, haga clic en **descargar metadatos**y, a continuación, guarde los metadatos en el equipo.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-fm-systems-tutorial/IC795903.png "Configurar el inicio de sesión único")

5.  Enviar el archivo de metadatos descargados a su FM: sistemas de soportan técnico.

    >[AZURE.NOTE] Su FM: Sistemas de soporte técnico debe realizar la configuración de SSO real.
Recibirá una notificación cuando se ha habilitado SSO para la suscripción.

6.  En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-fm-systems-tutorial/IC795904.png "Configurar el inicio de sesión único")
##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD iniciar sesión en FM:Systems, debe aprovisionar en FM:Systems.  
En el caso de FM:Systems, aprovisionamiento es una tarea manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar el aprovisionamiento de usuario, siga estos pasos:

1.  En una ventana de explorador web, inicie sesión en el sitio de su empresa FM:Systems como administrador.

2.  Vaya a **administración del sistema \> administrar seguridad \> usuarios \> lista de usuarios**.

    ![Administración del sistema] (./media/active-directory-saas-fm-systems-tutorial/IC795905.png "Administración del sistema")

3.  Haga clic en **Crear nuevo usuario**.

    ![Crear nuevo usuario] (./media/active-directory-saas-fm-systems-tutorial/IC795906.png "Crear nuevo usuario")

4.  En la sección **Crear usuario** , siga estos pasos:

    ![Crear usuario] (./media/active-directory-saas-fm-systems-tutorial/IC795907.png "Crear usuario")

    1.  Escriba el nombre de usuario, la contraseña y su confirmación, la dirección de correo electrónico y el identificador del empleado de una cuenta de Azure Active Directory válida que desee aprovisionar en los cuadros de texto relacionados.
    2.  Haga clic en **siguiente**.

>[AZURE.NOTE] Puede usar cualquier otros FM:Systems usuario cuenta herramientas de creación o API proporcionan por FM:Systems a cuentas de usuario AAD de aprovisionamiento.

##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-fmsystems-perform-the-following-steps"></a>Para asignar a los usuarios a FM:Systems, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación **FM:Systems **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-fm-systems-tutorial/IC795908.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-fm-systems-tutorial/IC767830.png "Sí")
  
Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
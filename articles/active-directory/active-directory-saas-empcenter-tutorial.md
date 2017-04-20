<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con EmpCenter | Microsoft Azure" 
    description="Aprenda a usar EmpCenter con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más." 
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
    ms.date="08/16/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-empcenter"></a>Tutorial: Integración de Azure Active Directory con EmpCenter
  
El objetivo de este tutorial es mostrar la integración de Azure y EmpCenter.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inicio de sesión único de EmpCenter activado suscripción
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a EmpCenter será capaz de inicio de sesión único en la aplicación en el sitio de su empresa de EmpCenter (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
El escenario descrito en este tutorial consta de los siguientes elementos:

1.  Habilitar la integración de aplicación para EmpCenter
2.  Configurar el inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-empcenter-tutorial/IC802916.png "Escenario")
##<a name="enabling-the-application-integration-for-empcenter"></a>Habilitar la integración de aplicación para EmpCenter
  
El objetivo de esta sección es describen cómo habilitar la integración de aplicación para EmpCenter.

###<a name="to-enable-the-application-integration-for-empcenter-perform-the-following-steps"></a>Para habilitar la integración de aplicación para EmpCenter, siga estos pasos:

1.  En el portal clásico Azure, en el panel de navegación de la izquierda, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-empcenter-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-empcenter-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-empcenter-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-empcenter-tutorial/IC749322.png "Agregar una aplicación de muestra")

6.  En el **cuadro de búsqueda**, escriba **EmpCenter**.

    ![Galería de aplicación] (./media/active-directory-saas-empcenter-tutorial/IC802917.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **EmpCenter**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![EmpCentral] (./media/active-directory-saas-empcenter-tutorial/IC802918.png "EmpCentral")
##<a name="configuring-single-sign-on"></a>Configurar el inicio de sesión único
  
El objetivo de esta sección es describen cómo habilitar los usuarios autenticar a EmpCenter con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación **EmpCenter** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-empcenter-tutorial/IC802919.png "Configurar el inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión EmpCenter** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-empcenter-tutorial/IC802920.png "Configurar el inicio de sesión único")

3.  En la página **Configurar opciones de aplicación** , realice los pasos siguientes:

    ![Establecer la configuración de la aplicación] (./media/active-directory-saas-empcenter-tutorial/IC802921.png "Establecer la configuración de la aplicación")

    1.  En el cuadro de texto de **Inicio de sesión en la dirección URL** , escriba la dirección URL utilizada por los usuarios para el inicio de sesión en la aplicación EmpCenter (por ejemplo: *https://partner-authenticati.empcenter.com/workforce/SSO.do*).
    2.  Haga clic en **siguiente**

4.  En la página **configurar inicio de sesión único en EmpCenter** , para descargar los metadatos, haga clic en **descargar metadatos**y, a continuación, guarde el archivo de metadatos en el equipo.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-empcenter-tutorial/IC802922.png "Configurar el inicio de sesión único")

5.  Enviar el archivo descargado metadatos a su equipo de soporte técnico de EmpCenter.

    >[AZURE.NOTE] El equipo de soporte de EmpCenter tiene que ver la configuración de SSO real.
Recibirá una notificación cuando se ha habilitado SSO para la suscripción.

6.  En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-empcenter-tutorial/IC802923.png "Configurar el inicio de sesión único")
##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD iniciar sesión en EmpCenter, debe aprovisionar en EmpCenter.  
En el caso de EmpCenter, es necesario crear el equipo de soporte técnico de EmpCenter las cuentas de usuario.

>[AZURE.NOTE] Puede usar cualquier otros EmpCenter usuario cuenta herramientas de creación o API proporcionan por EmpCenter aprovisionar Azure Active Directory las cuentas de usuario.

##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-empcenter-perform-the-following-steps"></a>Para asignar a los usuarios a EmpCenter, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación **EmpCenter **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-empcenter-tutorial/IC802924.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-empcenter-tutorial/IC767830.png "Sí")
  
Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con unirse | Microsoft Azure" 
    description="Aprenda a usar ponerse a trabajar con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más." 
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

#<a name="tutorial-azure-active-directory-integration-with-huddle"></a>Tutorial: Integración de Azure Active Directory con ponerse a trabajar
  
El objetivo de este tutorial es mostrar la integración de Azure y ponerse a trabajar.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un unirse inicio de sesión único habilitado suscripción
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a unirse podrán al inicio de sesión único en la aplicación en su sitio de empresa de unirse (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
El escenario descrito en este tutorial se compone de los siguientes bloques de creación:

1.  Habilitar la integración de la aplicación para ponerse a trabajar
2.  Configuración de inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Configurar el inicio de sesión único] (./media/active-directory-saas-huddle-tutorial/IC787830.png "Configurar el inicio de sesión único")
##<a name="enabling-the-application-integration-for-huddle"></a>Habilitar la integración de la aplicación para ponerse a trabajar
  
El objetivo de esta sección es describen cómo habilitar la integración de la aplicación para ponerse a trabajar.

###<a name="to-enable-the-application-integration-for-huddle-perform-the-following-steps"></a>Para habilitar la integración de la aplicación para unirse, realice los pasos siguientes:

1.  En el portal de clásico Azure, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-huddle-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-huddle-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-huddle-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-huddle-tutorial/IC749322.png "Agregar una aplicación de gallerry")

6.  En el **cuadro de búsqueda**, escriba **ponerse a trabajar**.

    ![Galería de aplicación] (./media/active-directory-saas-huddle-tutorial/IC787831.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **unirse**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Ponerse a trabajar] (./media/active-directory-saas-huddle-tutorial/IC787832.png "Ponerse a trabajar")
##<a name="configuring-single-sign-on"></a>Configuración de inicio de sesión único
  
El objetivo de esta sección es describen cómo habilitar los usuarios autenticar a ponerse a trabajar con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación **unirse** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-huddle-tutorial/IC787833.png "Configurar el inicio de sesión único")

2.  En la página **¿cómo le gustaría usuarios a iniciar sesión ponerse a trabajar** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-huddle-tutorial/IC787834.png "Configurar el inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto de **Inicio de sesión de ponerse a trabajar en URL** , escriba la dirección URL de su inquilino de ponerse a trabajar con el siguiente patrón "*http://company.huddle.com*" y, a continuación, haga clic en **siguiente**.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-huddle-tutorial/IC787835.png "Configurar la dirección URL de la aplicación")

4.  En la página **configurar inicio de sesión único en ponerse a trabajar** , realice los pasos siguientes:

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-huddle-tutorial/IC787836.png "Configurar el inicio de sesión único")

    1.  Haga clic en **Descargar certificado**y, a continuación, guarde el archivo de certificado en el equipo.
    2.  Copie el valor de **Dirección URL del emisor** , el valor de **Dirección URL de SAML SSO** y el certificado descargado y, a continuación, enviarlos al equipo de soporte ponerse a trabajar.

    >[AZURE.NOTE] Inicio de sesión único debe estar habilitado en el equipo de soporte técnico ponerse a trabajar.
Obtendrá una notificación cuando se haya completado la configuración.

5.  En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-huddle-tutorial/IC787837.png "Configurar el inicio de sesión único")
##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD iniciar sesión en ponerse a trabajar, debe aprovisionar a ponerse a trabajar.  
En el caso de ponerse a trabajar, aprovisionamiento es una tarea manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar el aprovisionamiento de usuario, siga estos pasos:

1.  Inicie sesión como administrador en el sitio de empresa **ponerse a trabajar** .

2.  Haga clic en **área de trabajo**.

3.  Haga clic en **personas \> invitar a personas**.

    ![Personas] (./media/active-directory-saas-huddle-tutorial/IC787838.png "Personas")

4.  En la sección **crear una nueva invitación** , realice los pasos siguientes:

    ![Nueva invitación] (./media/active-directory-saas-huddle-tutorial/IC787839.png "Nueva invitación")

    1.  En la lista **Elija un grupo para invitar a personas a unirse a** , seleccione **grupo**.
    2.  Escriba la **Dirección de correo electrónico** de una cuenta AAD válida que desee aprovisionar en el cuadro de texto relacionado.
    3.  Haga clic en **Invitar**.

    >[AZURE.NOTE] Titular de la cuenta de Azure AD recibirán un correo electrónico que incluye un vínculo para confirmar la cuenta antes de que esté activa.

>[AZURE.NOTE] Puede usar cualquier otras ponerse a trabajar usuario cuenta herramientas de creación o API proporcionan por unirse a disposición AAD cuentas de usuario.

##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-huddle-perform-the-following-steps"></a>Para asignar a los usuarios a ponerse a trabajar, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación **unirse **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-huddle-tutorial/IC787840.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-huddle-tutorial/IC767830.png "Sí")
  
Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
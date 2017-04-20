<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con RightAnswers | Microsoft Azure" 
    description="Aprenda a usar RightAnswers con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más." 
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
    ms.date="09/26/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-rightanswers"></a>Tutorial: Integración de Azure Active Directory con RightAnswers
  
El objetivo de este tutorial es mostrar la integración de Azure y RightAnswers. El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inicio de sesión único de RightAnswers activado suscripción
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a RightAnswers será capaz de inicio de sesión único en la aplicación con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
El escenario descrito en este tutorial consta de los siguientes elementos:

1.  Habilitar la integración de aplicación para RightAnswers
2.  Configurar el inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-rightanswers-tutorial/IC802925.png "Escenario")
##<a name="enabling-the-application-integration-for-rightanswers"></a>Habilitar la integración de aplicación para RightAnswers
  
El objetivo de esta sección es describen cómo habilitar la integración de aplicación para RightAnswers.

###<a name="to-enable-the-application-integration-for-rightanswers-perform-the-following-steps"></a>Para habilitar la integración de aplicación para RightAnswers, siga estos pasos:

1.  En el portal clásico Azure, en el panel de navegación de la izquierda, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-rightanswers-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-rightanswers-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-rightanswers-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-rightanswers-tutorial/IC749322.png "Agregar una aplicación de muestra")

6.  En el **cuadro de búsqueda**, escriba **RightAnswers**.

    ![Galería de aplicación] (./media/active-directory-saas-rightanswers-tutorial/IC802926.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **RightAnswers**y, a continuación, haga clic en **completado** para agregar la aplicación.
##<a name="configuring-single-sign-on"></a>Configurar el inicio de sesión único
  
El objetivo de esta sección es describen cómo habilitar los usuarios autenticar a RightAnswers con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación **RightAnswers** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-rightanswers-tutorial/IC802927.png "Configurar el inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión RightAnswers** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-rightanswers-tutorial/IC802928.png "Configurar el inicio de sesión único")

3.  En la página **Configurar opciones de aplicación** , en el cuadro de texto de **Inicio de sesión en la dirección URL** , escriba la dirección URL utilizada por los usuarios para el inicio de sesión en la aplicación RightAnswers (por ejemplo: *https://fortify.rightanswers.com/portal/ss/*) y, a continuación, haga clic en **siguiente**.

    ![Establecer la configuración de la aplicación] (./media/active-directory-saas-rightanswers-tutorial/IC802929.png "Establecer la configuración de la aplicación")

4.  En la página **configurar inicio de sesión único en RightAnswers** , para descargar los metadatos, haga clic en **descargar metadatos**y, a continuación, guarde el archivo de metadatos localmente en el equipo.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-rightanswers-tutorial/IC802930.png "Configurar el inicio de sesión único")

5.  Enviar el archivo descargado metadatos a su equipo de soporte técnico de RightAnswers.

    >[AZURE.NOTE] El equipo de soporte de RightAnswers tiene que ver la configuración de SSO real.
Recibirá una notificación cuando se ha habilitado SSO para la suscripción.

6.  En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-rightanswers-tutorial/IC802931.png "Configurar el inicio de sesión único")
##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD iniciar sesión en RightAnswers, debe aprovisionar en RightAnswers.  
En el caso de RightAnswers, aprovisionamiento es una tarea automatizada.  
No hay ningún elemento de acción para usted.
  
Los usuarios se crean automáticamente durante el primer única sesión intento si es necesario.

>[AZURE.NOTE]Puede usar cualquier otros RightAnswers usuario cuenta herramientas de creación o API proporcionan por RightAnswers a cuentas de usuario AAD de aprovisionamiento.

##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-rightanswers-perform-the-following-steps"></a>Para asignar a los usuarios a RightAnswers, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación **RightAnswers **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-rightanswers-tutorial/IC802932.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-rightanswers-tutorial/IC767830.png "Sí")
  
Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
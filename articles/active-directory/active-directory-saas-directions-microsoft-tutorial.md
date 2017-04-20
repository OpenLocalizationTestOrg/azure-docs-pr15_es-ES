<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory indicaciones acerca de Microsoft | Microsoft Azure" 
    description="¡Obtenga información sobre cómo usar instrucciones en Microsoft con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más!" 
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

#<a name="tutorial-azure-active-directory-integration-with-directions-on-microsoft"></a>Tutorial: Integración de Azure Active Directory indicaciones acerca de Microsoft

El objetivo de este tutorial es mostrar la integración de Azure Active Directory y direcciones en Microsoft.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un instrucciones sobre la suscripción a Microsoft

Si no tiene un instrucciones federados de suscripción de Microsoft aún, de una solicitud de correo electrónico "*service@DirectionsOnMicrosoft.com*".

Después de completar este tutorial, los usuarios de Azure Active Directory que se ha asignado a direcciones en Microsoft será capaz de inicio de sesión único en la aplicación utilizando el inicio de sesión único.

El escenario descrito en este tutorial consta de los siguientes elementos:

1.  Habilitar la integración de la aplicación para obtener instrucciones sobre Microsoft
2.  Configurar el inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-directions-microsoft-tutorial/IC786877.png "Escenario")
##<a name="enabling-the-application-integration-for-directions-on-microsoft"></a>Habilitar la integración de la aplicación para obtener instrucciones sobre Microsoft

El objetivo de esta sección es describen cómo habilitar la integración de la aplicación para obtener instrucciones sobre Microsoft.

###<a name="to-enable-the-application-integration-for-directions-on-microsoft-perform-the-following-steps"></a>Para habilitar la integración de la aplicación para obtener instrucciones sobre Microsoft, siga estos pasos:

1.  En el portal clásico Azure, en el panel de navegación de la izquierda, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-directions-microsoft-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-directions-microsoft-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-directions-microsoft-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-directions-microsoft-tutorial/IC749322.png "Agregar una aplicación de muestra")

6.  En el **cuadro de búsqueda**, escriba las **direcciones en Microsoft**.

    ![Galería de aplicación] (./media/active-directory-saas-directions-microsoft-tutorial/IC786878.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **instrucciones en Microsoft**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Escenario] (./media/active-directory-saas-directions-microsoft-tutorial/IC793922.png "Escenario")
##<a name="configuring-single-sign-on"></a>Configurar el inicio de sesión único

El objetivo de esta sección es describen cómo habilitar los usuarios autenticar para obtener instrucciones sobre Microsoft con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación de **direcciones en Microsoft** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Habilitar inicio de sesión único] (./media/active-directory-saas-directions-microsoft-tutorial/IC786879.png "Habilitar inicio de sesión único")

2.  En la página **¿cómo le gustaría usuarios a iniciar sesión direcciones en Microsoft** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Inicio de sesión de Microsoft Azure AD Singel] (./media/active-directory-saas-directions-microsoft-tutorial/IC786880.png "Inicio de sesión de Microsoft Azure AD Singel")

3.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto de inicio de sesión en la dirección URL, escriba **https://www.directionsonmicrosoft.com/user/login**y, a continuación, haga clic en **siguiente**.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-directions-microsoft-tutorial/IC786881.png "Configurar la dirección URL de la aplicación")

4.  En la página **configurar inicio de sesión único en las instrucciones de Microsoft** , haga clic en **descargar metadatos**y, a continuación, guarde el archivo de metadatos localmente en el equipo.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-directions-microsoft-tutorial/IC786882.png "Configurar el inicio de sesión único")

5.  Enviar el archivo de metadatos a las instrucciones en el equipo de soporte técnico de Microsoft (*service@DirectionsOnMicrosoft.com*). Para habilitar las instrucciones en el equipo de soporte técnico de Microsoft localizar su pertenencia al sitio federado, incluir información de su compañía en el correo electrónico.

    >[AZURE.NOTE] Inicio de sesión único para obtener instrucciones sobre Microsoft debe estar habilitado por las instrucciones en el equipo de soporte técnico de Microsoft.
Recibirá una notificación cuando se ha habilitado el inicio de sesión único.

6.  En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-directions-microsoft-tutorial/IC786883.png "Configurar el inicio de sesión único")
##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario

No hay ningún elemento de acción para configurar las direcciones en Microsoft de aprovisionamiento de usuarios.  
Cuando un usuario asignado intenta iniciar sesión en direcciones en Microsoft mediante el panel de acceso, direcciones en Microsoft comprueba si existe el usuario. Si no hay ninguna cuenta de usuario aún, se crea automáticamente por direcciones en Microsoft.
##<a name="assigning-users"></a>Asignación de usuarios

Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-directions-on-microsoft-perform-the-following-steps"></a>Para asignar a los usuarios a direcciones en Microsoft, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación de **direcciones en Microsoft **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-directions-microsoft-tutorial/IC786884.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-directions-microsoft-tutorial/IC767830.png "Sí")

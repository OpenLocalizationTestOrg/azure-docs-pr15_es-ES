<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con efecto invernadero | Microsoft Azure" 
    description="Aprenda a usar invernadero con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y más." 
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

#<a name="tutorial-azure-active-directory-integration-with-greenhouse"></a>Tutorial: Integración de Azure Active Directory con efecto invernadero
  
El objetivo de este tutorial es mostrar la integración de Azure e invernadero.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Una suscripción de inicio de sesión único de invernadero
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a invernadero podrán al inicio de sesión único en la aplicación en el sitio de su empresa de invernadero (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
El escenario descrito en este tutorial consta de los siguientes elementos:

1.  Habilitar la integración de la aplicación para invernadero
2.  Configurar el inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-greenhouse-tutorial/IC790783.png "Escenario")
##<a name="enabling-the-application-integration-for-greenhouse"></a>Habilitar la integración de la aplicación para invernadero
  
El objetivo de esta sección es describen cómo habilitar la integración de la aplicación para invernadero.

###<a name="to-enable-the-application-integration-for-greenhouse-perform-the-following-steps"></a>Para habilitar la integración de la aplicación para invernadero, realice los pasos siguientes:

1.  En el portal clásico Azure, en el panel de navegación de la izquierda, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-greenhouse-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-greenhouse-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-greenhouse-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-greenhouse-tutorial/IC749322.png "Agregar una aplicación de muestra")

6.  En el **cuadro de búsqueda**, escriba **invernadero**.

    ![Galería de aplicación] (./media/active-directory-saas-greenhouse-tutorial/IC790784.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **invernadero**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Efecto invernadero] (./media/active-directory-saas-greenhouse-tutorial/IC790785.png "Efecto invernadero")
##<a name="configuring-single-sign-on"></a>Configurar el inicio de sesión único
  
El objetivo de esta sección es describen cómo habilitar los usuarios autenticar invernadero con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación de **invernadero** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar inicio de sesión único] (./media/active-directory-saas-greenhouse-tutorial/IC790786.png "Configurar inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión invernadero** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-greenhouse-tutorial/IC790787.png "Configurar inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto de **Inicio de sesión en la dirección URL** , escriba la dirección URL con el siguiente patrón "*https://company.greenhouse.io*" y, a continuación, haga clic en **siguiente**.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-greenhouse-tutorial/IC790788.png "Configurar la dirección URL de la aplicación")

4.  En la página **configurar inicio de sesión único en invernadero** , haga clic en **descargar metadatos**y, a continuación, guarde el archivo de metadatos localmente en el equipo.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-greenhouse-tutorial/IC790789.png "Configurar inicio de sesión único")

5.  Reenviar ese archivo de metadatos al equipo de soporte de invernadero.

    >[AZURE.NOTE] Inicio de sesión único debe estar habilitado en el equipo de soporte técnico invernadero.

6.  En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar inicio de sesión único] (./media/active-directory-saas-greenhouse-tutorial/IC790790.png "Configurar inicio de sesión único")
##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD iniciar sesión en invernadero, debe aprovisionar en invernadero.  
En el caso de invernadero, aprovisionamiento es una tarea manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para crear cuentas de usuario, siga estos pasos:

1.  Inicie sesión como administrador en el sitio de su empresa **invernadero** .

2.  En el menú de la parte superior, haga clic en **Configurar**y, a continuación, haga clic en **usuarios**.

    ![Usuarios] (./media/active-directory-saas-greenhouse-tutorial/IC790791.png "Usuarios")

3.  Haga clic en **nuevos usuarios**.

    ![Nuevo usuario] (./media/active-directory-saas-greenhouse-tutorial/IC790792.png "Nuevo usuario")

4.  En la sección **Agregar nuevo usuario** , siga estos pasos:

    ![Agregar nuevo usuario] (./media/active-directory-saas-greenhouse-tutorial/IC790793.png "Agregar nuevo usuario")

    1.  En el cuadro de texto de **correos electrónicos de usuario ENTRAR** , escriba la dirección de correo electrónico de una cuenta de Azure Active Directory válida que desee aprovisionar.
    2.  Haga clic en **Guardar**.
        
        >[AZURE.NOTE] Los propietarios de la cuenta de Azure Active Directory recibirán un correo electrónico que incluye un vínculo para confirmar la cuenta antes de que esté activa.

>[AZURE.NOTE] Puede usar cualquier otros efecto invernadero usuario cuenta herramientas de creación o API proporcionan por invernadero a cuentas de usuario AAD de aprovisionamiento.

##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-greenhouse-perform-the-following-steps"></a>Para asignar a los usuarios a invernadero, realice los pasos siguientes:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación de **invernadero **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-greenhouse-tutorial/IC790794.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-greenhouse-tutorial/IC767830.png "Sí")
  
Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
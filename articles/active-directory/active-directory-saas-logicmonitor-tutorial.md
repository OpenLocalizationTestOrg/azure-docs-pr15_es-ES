<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con LogicMonitor | Microsoft Azure" 
    description="Aprenda a usar LogicMonitor con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más." 
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

#<a name="tutorial-azure-active-directory-integration-with-logicmonitor"></a>Tutorial: Integración de Azure Active Directory con LogicMonitor
  
El objetivo de este tutorial es mostrar la integración de Azure y LogicMonitor.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inquilino LogicMonitor
  
El escenario descrito en este tutorial se compone de los siguientes bloques de creación:

1.  Habilitar la integración de aplicación para LogicMonitor
2.  Configuración de inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-logicmonitor-tutorial/IC790045.png "Escenario")
##<a name="enabling-the-application-integration-for-logicmonitor"></a>Habilitar la integración de aplicación para LogicMonitor
  
El objetivo de esta sección es describen cómo habilitar la integración de aplicación para LogicMonitor.

###<a name="to-enable-the-application-integration-for-logicmonitor-perform-the-following-steps"></a>Para habilitar la integración de aplicación para LogicMonitor, siga estos pasos:

1.  En el portal de clásico Azure, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-logicmonitor-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-logicmonitor-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-logicmonitor-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-logicmonitor-tutorial/IC749322.png "Agregar una aplicación de gallerry")

6.  En el **cuadro de búsqueda**, escriba **logicmonitor**.

    ![Galería de aplicación] (./media/active-directory-saas-logicmonitor-tutorial/IC790046.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **LogicMonitor**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![LogicMonitor] (./media/active-directory-saas-logicmonitor-tutorial/IC790047.png "LogicMonitor")
##<a name="configuring-single-sign-on"></a>Configuración de inicio de sesión único
  
El objetivo de esta sección es describen cómo habilitar los usuarios autenticar a LogicMonitor con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación **LogicMonitor **, haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-logicmonitor-tutorial/IC790048.png "Configurar el inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión LogicMonitor** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-logicmonitor-tutorial/IC790049.png "Configurar el inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto de **Inicio de sesión en la dirección URL** , escriba la dirección URL usada por los usuarios para iniciar sesión LogicMonitor \(e, g,: "*http://company.logicmonitor.com*"\)y, a continuación, haga clic en **siguiente**.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-logicmonitor-tutorial/IC790050.png "Configurar la dirección URL de la aplicación")

4.  En la página **configurar inicio de sesión único en LogicMonitor** , haga clic en **descargar metadatos**y, a continuación, guárdelo en el equipo.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-logicmonitor-tutorial/IC790051.png "Configurar el inicio de sesión único")

5.  Inicie sesión como administrador en el sitio de su empresa **LogicMonitor** .

6.  En el menú de la parte superior, haga clic en **configuración**.

    ![Configuración] (./media/active-directory-saas-logicmonitor-tutorial/IC790052.png "Configuración")

7.  En bat de navegación en el lado izquierdo, haga clic en **Inicio de sesión único**

    ![Inicio de sesión único] (./media/active-directory-saas-logicmonitor-tutorial/IC790053.png "Inicio de sesión único")

8.  En la sección **configuración de inicio de sesión único (SSO)** , siga estos pasos:

    ![Configuración de inicio de sesión único] (./media/active-directory-saas-logicmonitor-tutorial/IC790054.png "Configuración de inicio de sesión único")

    1.  Seleccione **Habilitar inicio de sesión único**.
    2.  Como **Valor predeterminado de asignación de roles**, seleccione **sólo lectura**.
    3.  Abra el archivo de metadatos descargados en el Bloc de notas y, a continuación, pegar el contenido del archivo en el cuadro de texto de **Metadatos del proveedor de identidades** .
    4.  Haga clic en **Guardar cambios**.

9.  En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-logicmonitor-tutorial/IC790055.png "Configurar el inicio de sesión único")
##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
Para que usuarios AAD poder iniciar sesión, debe contar con la aplicación LogicMonitor con sus nombres de usuario de Azure Active Directory.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar el aprovisionamiento de usuario, siga estos pasos:

1.  Inicie sesión como administrador en el sitio de su empresa LogicMonitor.

2.  En el menú de la parte superior, haga clic en **configuración**y, a continuación, haga clic en **usuarios y funciones**.

    ![Roles y los usuarios] (./media/active-directory-saas-logicmonitor-tutorial/IC790056.png "Roles y los usuarios")

3.  Haga clic en **Agregar**.

4.  En la sección **Agregar una cuenta** , siga estos pasos:

    ![Agregar una cuenta] (./media/active-directory-saas-logicmonitor-tutorial/IC790057.png "Agregar una cuenta")

    1.  Escriba los valores de **nombre de usuario**, **correo electrónico**, **contraseña** y **vuelva a escribir la contraseña** del usuario de Azure Active Directory que desee aprovisionar en los cuadros de texto relacionados.
    2.  Seleccione **Roles**, **permisos de vista** y el **estado**.
    3.  Haga clic en **Enviar**.

>[AZURE.NOTE]Puede usar cualquier otros LogicMonitor usuario cuenta herramientas de creación o API proporcionan por LogicMonitor aprovisionar Azure Active Directory las cuentas de usuario.

##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-logicmonitor-perform-the-following-steps"></a>Para asignar a los usuarios a LogicMonitor, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación **LogicMonitor** , haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-logicmonitor-tutorial/IC790058.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-logicmonitor-tutorial/IC767830.png "Sí")
  
Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).





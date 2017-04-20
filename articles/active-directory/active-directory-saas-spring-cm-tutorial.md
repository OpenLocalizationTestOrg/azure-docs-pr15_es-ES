<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con resorte CM | Microsoft Azure" 
    description="¡Obtenga información sobre cómo utilizar resorte CM con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más!" 
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
    ms.date="09/19/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-spring-cm"></a>Tutorial: Integración de Azure Active Directory con resorte CM
  
El objetivo de este tutorial es mostrar cómo configurar el inicio de sesión único entre Azure Active Directory y SpringCM.
  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inicio de sesión único de SpringCM activado suscripción
  
Después de completar este tutorial, los usuarios de Azure Active Directory que se ha asignado a SpringCM será capaz de inicio de sesión único mediante el Panel de acceso de AAD.

1.  Habilitar la integración de aplicación para SpringCM
2.  Configuración de inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-spring-cm-tutorial/IC797044.png "Escenario")

##<a name="enabling-the-application-integration-for-springcm"></a>Habilitar la integración de aplicación para SpringCM
  
El objetivo de esta sección es describen cómo habilitar la integración de aplicación para SpringCM.

###<a name="to-enable-the-application-integration-for-springcm-perform-the-following-steps"></a>Para habilitar la integración de aplicación para SpringCM, siga estos pasos:

1.  En el portal de clásico Azure, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-spring-cm-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-spring-cm-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-spring-cm-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-spring-cm-tutorial/IC749322.png "Agregar una aplicación de gallerry")

6.  En el **cuadro de búsqueda**, escriba **SpringCM**.

    ![Galería de aplicación] (./media/active-directory-saas-spring-cm-tutorial/IC797045.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **SpringCM**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![SpringCM] (./media/active-directory-saas-spring-cm-tutorial/IC797046.png "SpringCM")

##<a name="configuring-single-sign-on"></a>Configuración de inicio de sesión único
  
Esta sección describe cómo permitir a los usuarios autenticar SpringCM con su cuenta de Azure Active Directory, con la federación basada en el protocolo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación **SpringCM** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar inicio de sesión único] (./media/active-directory-saas-spring-cm-tutorial/IC797047.png "Configurar inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión SpringCM** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-spring-cm-tutorial/IC797048.png "Configurar inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto **SpringCM inicio de sesión en la URL** , escriba la dirección URL utilizada por los usuarios para iniciar sesión la aplicación SpringCM y, a continuación, haga clic en **siguiente**. 

    La dirección URL de la aplicación es la dirección URL de inquilinos de SpringCM (por ejemplo: *https://na11.springcm.com/atlas/SSO/SSOEndpoint.ashx?aid=16826*):

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-spring-cm-tutorial/IC797049.png "Configurar la dirección URL de la aplicación")

4.  En la página **configurar inicio de sesión único en SpringCM** , para descargar el certificado, haga clic en **Descargar certificado**y, a continuación, guarde el archivo de certificado localmente en el equipo.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-spring-cm-tutorial/IC797050.png "Configurar el inicio de sesión único")

5.  En una ventana de explorador web diferente, inicie sesión el sitio de su empresa **SpringCM** como administrador.

6.  En el menú de la parte superior, haga clic en **Ir a**, haga clic en **Preferencias**y, a continuación, en la sección **Preferencias de cuenta** , haga clic en **SAML SSO**.

    ![SAML SSO] (./media/active-directory-saas-spring-cm-tutorial/IC797051.png "SAML SSO")

7.  En la sección Configuración del proveedor de identidades, siga estos pasos:

    ![Configuración del proveedor de identidades] (./media/active-directory-saas-spring-cm-tutorial/IC797052.png "Configuración del proveedor de identidades")

    1.  Para cargar el certificado de Azure Active Directory descargado, haga clic en **Seleccionar certificado de emisor** o **cambiar emisor**.
    2.  En el portal de clásico Azure, en la página **configurar inicio de sesión único en SpringCM** , copie el valor de **Dirección URL del emisor** y péguelo en el cuadro de texto del **emisor** .
    3.  En el portal de clásico Azure, en la página **configurar inicio de sesión único en SpringCM** , copie el valor de **Singel inicio de sesión de dirección URL del servicio** y péguelo en el cuadro de texto de **Extremo de proveedor de servicio (SP) iniciado** .
    4.  Como **SAML habilitado**, seleccione **Activar**.
    5.  Haga clic en **Guardar**.

8.  En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-spring-cm-tutorial/IC797053.png "Configurar el inicio de sesión único")

##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure Active Directory iniciar sesión en SpringCM, debe aprovisionar en SpringCM.  
En el caso de SpringCM, aprovisionamiento es una tarea manual.

>[AZURE.NOTE] Para obtener más detalles, vea [crear y editar un usuario SpringCM](http://knowledge.springcm.com/create-and-edit-a-springcm-user)

###<a name="to-provision-a-user-account-to-springcm-perform-the-following-steps"></a>Para crear una cuenta de usuario a SpringCM, siga estos pasos:

1.  Inicie sesión como administrador en el sitio de su empresa **SpringCM** .

2.  Haga clic en **Ir a**y, a continuación, haga clic en **Libreta de direcciones**.

    ![Crear usuario] (./media/active-directory-saas-spring-cm-tutorial/IC797054.png "Crear usuario")

3.  Haga clic en **Crear usuario**.

4.  Seleccione un **rol de usuario**.

5.  Seleccione **Enviar correo electrónico de activación**.

6.  Escriba el nombre, apellidos y dirección de correo electrónico de una cuenta de usuario válida de Azure Active Directory que desee aprovisionar en los cuadros de texto relacionados.

7.  Agregar al usuario a un **grupo de seguridad**.

8.  Haga clic en **Guardar**.

>[AZURE.NOTE] Puede usar cualquier otros SpringCM usuario cuenta herramientas de creación o API proporcionan por SpringCM a cuentas de usuario AAD de aprovisionamiento.

##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-springcm-perform-the-following-steps"></a>Para asignar a los usuarios a SpringCM, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación **SpringCM** , haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-spring-cm-tutorial/IC797055.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-spring-cm-tutorial/IC767830.png "Sí")
  
Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).





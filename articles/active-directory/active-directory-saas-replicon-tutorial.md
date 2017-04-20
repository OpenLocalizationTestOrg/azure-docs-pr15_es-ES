<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Replicon | Microsoft Azure" 
    description="Aprenda a usar Replicon con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más." 
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

#<a name="tutorial-azure-active-directory-integration-with-replicon"></a>Tutorial: Integración de Azure Active Directory con Replicon
  
El objetivo de este tutorial es mostrar la integración de Azure y Replicon. El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inquilino Replicon
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Replicon será capaz de inicio de sesión único en la aplicación en el sitio de su empresa de Replicon (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
El escenario descrito en este tutorial consta de los siguientes elementos:

1.  Habilitar la integración de aplicación para Replicon
2.  Configurar el inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-replicon-tutorial/IC777798.png "Escenario")
##<a name="enabling-the-application-integration-for-replicon"></a>Habilitar la integración de aplicación para Replicon
  
El objetivo de esta sección es describen cómo habilitar la integración de aplicación para Replicon.

###<a name="to-enable-the-application-integration-for-replicon-perform-the-following-steps"></a>Para habilitar la integración de aplicación para Replicon, siga estos pasos:

1.  En el portal clásico Azure, en el panel de navegación de la izquierda, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-replicon-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-replicon-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-replicon-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-replicon-tutorial/IC749322.png "Agregar una aplicación de muestra")

6.  En el **cuadro de búsqueda**, escriba **Replicon**.

    ![Galería de aplicación] (./media/active-directory-saas-replicon-tutorial/IC777799.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **Replicon**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Replicon] (./media/active-directory-saas-replicon-tutorial/IC777800.png "Replicon")
##<a name="configuring-single-sign-on"></a>Configurar el inicio de sesión único
  
El objetivo de esta sección es describen cómo habilitar los usuarios autenticar a Replicon con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación **Replicon** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar inicio de sesión único] (./media/active-directory-saas-replicon-tutorial/IC777801.png "Configurar inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión Replicon** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-replicon-tutorial/IC777802.png "Configurar inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , realice los pasos siguientes:

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-replicon-tutorial/IC777803.png "Configurar la dirección URL de la aplicación")

    1.  En el cuadro de texto **Replicon inicio de sesión en la URL** , escriba la dirección URL de inquilinos de Replicon (por ejemplo: *https://na2.replicon.com/company/saml2/sp-sso/post*).
    2.  En el cuadro de texto **Dirección URL de respuesta Replicon** , escriba la dirección URL de Replicon **AssertionConsumerService** (por ejemplo: *https://global.replicon.com/! / saml2/compañía o sso/post*).  

        >[AZURE.NOTE] Puede obtener la dirección URL de los metadatos de Replicon en:         **https://global.replicon.com/! /saml2/\<YourCompanyKey\>**.

    3.  Haga clic en **siguiente**

4.  En la página **configurar inicio de sesión único en Replicon** , para descargar los metadatos, haga clic en **descargar metadatos**y, a continuación, guarde los metadatos en el equipo.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-replicon-tutorial/IC777804.png "Configurar inicio de sesión único")

5.  En una ventana de explorador web diferente, inicie sesión en el sitio de su empresa Replicon como administrador.

6.  Para configurar SAML 2.0, siga estos pasos:

    ![Habilitar SAML autenticación] (./media/active-directory-saas-replicon-tutorial/IC777805.png "Habilitar SAML autenticación")

    1.  Para mostrar el cuadro de diálogo de **Autenticación de EnableSAML 2** , añada lo siguiente a la dirección URL, después de la clave de empresa:  
        **/Services/SecurityService1.svc/help/Test/EnableSAMLAuthentication2**  
        La siguiente imagen muestra el esquema de la dirección URL completa:  
        **https://Na2.replicon.com/\<YourCompanyKey\>/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2**
    2.  Haga clic en el **+** para expandir la sección **v20Configuration** .
    3.  Haga clic en el **+** para expandir la sección **metaDataConfiguration** .
    4.  Haga clic en **Elegir archivo**para seleccionar el archivo XML de metadatos de proveedor de identidades y haga clic en **Enviar**.

7.  En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar inicio de sesión único] (./media/active-directory-saas-replicon-tutorial/IC778418.png "Configurar inicio de sesión único")
##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD iniciar sesión en Replicon, debe aprovisionar en Replicon.  
En el caso de Replicon, aprovisionamiento es una tarea manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar el aprovisionamiento de usuario, siga estos pasos:

1.  En una ventana de explorador web, inicie sesión en el sitio de su empresa Replicon como administrador.

2.  Vaya a **administración \> usuarios**.

    ![Usuarios] (./media/active-directory-saas-replicon-tutorial/IC777806.png "Usuarios")

3.  Haga clic en **+ Agregar usuario**.

    ![Agregar usuario] (./media/active-directory-saas-replicon-tutorial/IC777807.png "Agregar usuario")

4.  En la sección de **Perfil de usuario** , siga estos pasos:

    ![Perfil de usuario] (./media/active-directory-saas-replicon-tutorial/IC777808.png "Perfil de usuario")

    1.  En el cuadro de texto **Nombre de inicio de sesión** , escriba la dirección de correo electrónico de Azure AD del usuario de Azure AD que desee aprovisionar.
    2.  Como **Tipo de autenticación**, seleccione **SSO**.
    3.  En el cuadro de texto de **departamento** , escriba el departamento del usuario.
    4.  Como **Tipo de empleado**, seleccione **Administrador**.
    5.  Haga clic en **Guardar el perfil de usuario**.

>[AZURE.NOTE]Puede usar cualquier otros Replicon usuario cuenta herramientas de creación o API proporcionan por Replicon a cuentas de usuario AAD de aprovisionamiento.

##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-replicon-perform-the-following-steps"></a>Para asignar a los usuarios a Replicon, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación **Replicon **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-replicon-tutorial/IC777809.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-replicon-tutorial/IC767830.png "Sí")
  
Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
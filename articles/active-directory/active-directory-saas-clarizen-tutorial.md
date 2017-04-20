<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Clarizen | Microsoft Azure" 
    description="Aprenda a usar Clarizen con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más." 
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

#<a name="tutorial-azure-active-directory-integration-with-clarizen"></a>Tutorial: Integración de Azure Active Directory con Clarizen

El objetivo de este tutorial es mostrar la integración de Azure y Clarizen.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inicio de sesión único de Clarizen activado suscripción

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Clarizen será capaz de inicio de sesión único en la aplicación en el sitio de su empresa de Clarizen (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

El escenario descrito en este tutorial consta de los siguientes elementos:

1.  Habilitar la integración de aplicación para Clarizen
2.  Configurar el inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-clarizen-tutorial/IC784679.png "Escenario")
##<a name="enabling-the-application-integration-for-clarizen"></a>Habilitar la integración de aplicación para Clarizen

El objetivo de esta sección es describen cómo habilitar la integración de aplicación para Clarizen.

###<a name="to-enable-the-application-integration-for-clarizen-perform-the-following-steps"></a>Para habilitar la integración de aplicación para Clarizen, siga estos pasos:

1.  En el portal clásico Azure, en el panel de navegación de la izquierda, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-clarizen-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-clarizen-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-clarizen-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-clarizen-tutorial/IC749322.png "Agregar una aplicación de muestra")

6.  En el **cuadro de búsqueda**, escriba **Clarizen**.

    ![Galería de aplicación] (./media/active-directory-saas-clarizen-tutorial/IC784680.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **Clarizen**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Clarizen] (./media/active-directory-saas-clarizen-tutorial/IC784681.png "Clarizen")
##<a name="configuring-single-sign-on"></a>Configurar el inicio de sesión único

El objetivo de esta sección es describen cómo habilitar los usuarios autenticar a Clarizen con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación **Clarizen** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-clarizen-tutorial/IC784682.png "Configurar el inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión Clarizen** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-clarizen-tutorial/IC784683.png "Configurar el inicio de sesión único")

3.  En la página **configurar inicio de sesión único en Clarizen** , para descargar el certificado, haga clic en **Descargar certificado**y, a continuación, guarde el archivo de certificado en el equipo.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-clarizen-tutorial/IC784684.png "Configurar el inicio de sesión único")

4.  En una ventana de explorador web diferente, inicie sesión en el sitio de su empresa **Clarizen** como administrador (por ejemplo: *https://app2.clarizen.com/Clarizen/Pages/Service/Login.aspx*).

5.  Haga clic en el nombre de usuario y, a continuación, haga clic en **configuración**.

    ![Configuración] (./media/active-directory-saas-clarizen-tutorial/IC784685.png "Configuración")

6.  Haga clic en la pestaña **Configuración Global** y, a continuación, seleccione **Autenticación federados**, haga clic en **Editar**.

    ![Configuración global] (./media/active-directory-saas-clarizen-tutorial/IC786906.png "Configuración global")

7.  En el cuadro de diálogo **Autenticación federados** , realice los pasos siguientes:

    ![Autenticación de federado] (./media/active-directory-saas-clarizen-tutorial/IC785892.png "Autenticación de federado")

    1.  Haga clic en **cargar** para cargar el certificado descargado.
    2.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en Clarizen** , copie el valor de **URL de servicio de inicio de sesión único** y, a continuación, péguelo en el cuadro de texto **dirección URL de inicio de sesión** .
    3.  En el portal de clásico Azure, en la página de diálogo **Configurar solo cerrar sesión en Clarizen** , copie el valor de **Dirección URL del servicio de Sign-Out único** y péguelo en el cuadro de texto **Dirección URL de Sign-out** .
    4.  Seleccione **Usar entrada**.
    5.  Haga clic en **Guardar**.

8.  En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-clarizen-tutorial/IC784688.png "Configurar el inicio de sesión único")
##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD iniciar sesión en Clarizen, debe aprovisionar en Clarizen.  
En el caso de Clarizen, aprovisionamiento es una tarea manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para crear cuentas de usuario, siga estos pasos:

1.  Inicie sesión como administrador en el sitio de su empresa **Clarizen** .

2.  Haga clic en **personas**.

    ![Personas] (./media/active-directory-saas-clarizen-tutorial/IC784689.png "Personas")

3.  Haga clic en **Invitar a usuario**.

    ![Invitar a usuarios] (./media/active-directory-saas-clarizen-tutorial/IC784690.png "Invitar a usuarios")

4.  En la página de diálogo Invitar a personas, realice los pasos siguientes:

    ![Invitar a personas] (./media/active-directory-saas-clarizen-tutorial/IC784691.png "Invitar a personas")

    1.  En el cuadro de texto de **correo electrónico** , escriba la dirección de correo electrónico de una cuenta de Azure Active Directory válida que desee aprovisionar.
    2.  Haga clic en **Invitar**.

    >[AZURE.NOTE] Titular de la cuenta de Azure Active Directory recibirá un correo electrónico y seguir un vínculo para confirmar su cuenta antes de que esté activa.

##<a name="assigning-users"></a>Asignación de usuarios

Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-clarizen-perform-the-following-steps"></a>Para asignar a los usuarios a Clarizen, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación **Clarizen **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-clarizen-tutorial/IC784692.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-clarizen-tutorial/IC767830.png "Sí")

Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

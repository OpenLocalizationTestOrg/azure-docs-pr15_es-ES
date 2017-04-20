<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Suite adaptación | Microsoft Azure"
    description="¡Obtenga información sobre cómo utilizar Suite adaptación con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más!" 
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

#<a name="tutorial-azure-active-directory-integration-with-adaptive-suite"></a>Tutorial: Integración de Azure Active Directory con adaptación Suite

El objetivo de este tutorial es mostrar la integración de Azure y adaptación Suite.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inquilino adaptación Suite

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Suite adaptación podrán al inicio de sesión único en la aplicación en el sitio de su empresa de adaptación Suite (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

El escenario descrito en este tutorial consta de los siguientes elementos:

1.  Habilitar la integración de la aplicación para adaptación Suite
2.  Configurar el inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-adaptive-suite-tutorial/IC805637.png "Escenario")
##<a name="enabling-the-application-integration-for-adaptive-suite"></a>Habilitar la integración de la aplicación para adaptación Suite

El objetivo de esta sección es describen cómo habilitar la integración de la aplicación para adaptación conjunto.

###<a name="to-enable-the-application-integration-for-adaptive-suite-perform-the-following-steps"></a>Para habilitar la integración de la aplicación para adaptación de conjunto, realice los pasos siguientes:

1.  En el portal clásico Azure, en el panel de navegación de la izquierda, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-adaptive-suite-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-adaptive-suite-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-adaptive-suite-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-adaptive-suite-tutorial/IC749322.png "Agregar una aplicación de muestra")

6.  En el **cuadro de búsqueda**, escriba **Suite adaptación**.

    ![Galería de aplicación] (./media/active-directory-saas-adaptive-suite-tutorial/IC805638.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **Conjunto adaptación**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Adaptación Suite] (./media/active-directory-saas-adaptive-suite-tutorial/IC805639.png "Adaptación Suite")
##<a name="configuring-single-sign-on"></a>Configurar el inicio de sesión único

El objetivo de esta sección es describen cómo habilitar los usuarios autenticar al conjunto de adaptación con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
Configuración de inicio de sesión único para adaptación conjunto requiere que recuperar un valor de la huella digital de un certificado.  
Si no está familiarizado con este procedimiento, consulte [cómo recuperar el valor de la huella digital de un certificado](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación de **Adaptación del conjunto de programas** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-adaptive-suite-tutorial/IC805640.png "Configurar el inicio de sesión único")

2.  En la página **¿cómo le gustaría usuarios a iniciar sesión conjunto adaptación** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-adaptive-suite-tutorial/IC805641.png "Configurar el inicio de sesión único")

3.  En la página **Configurar opciones de aplicación** , en el cuadro de texto **Dirección URL de respuesta** , escriba la dirección URL con el siguiente patrón "*samlsso/https://login.adaptiveinsights.com:443/RlJFRVRSSUFMMTI3MTE =*" y, a continuación, haga clic en **siguiente**.

    >[AZURE.NOTE] Puede obtener este valor desde la página de **Configuración de SSO de SAML** del conjunto adaptación.

    ![Establecer la configuración de la aplicación] (./media/active-directory-saas-adaptive-suite-tutorial/IC805642.png "Establecer la configuración de la aplicación")

4.  En la página **configurar inicio de sesión único en el conjunto de aplicaciones de adaptación** , para descargar el certificado, haga clic en **Descargar certificado**y, a continuación, guarde el archivo de certificado localmente en el equipo.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-adaptive-suite-tutorial/IC805643.png "Configurar el inicio de sesión único")

5.  En una ventana de explorador web diferente, inicie sesión en el sitio de su empresa Suite adaptación como administrador.

6.  Vaya a **Administrador**.

    ![Administrador] (./media/active-directory-saas-adaptive-suite-tutorial/IC805644.png "Administrador")

7.  En la sección **usuarios y Roles** , haga clic en **Administrar configuración de SSO de SAML**.

    ![Administrar la configuración de SAML SSO] (./media/active-directory-saas-adaptive-suite-tutorial/IC805645.png "Administrar la configuración de SAML SSO")

8.  En la página **Configuración de SSO de SAML** , realice los pasos siguientes:

    ![Configuración de SAML SSO] (./media/active-directory-saas-adaptive-suite-tutorial/IC805646.png "Configuración de SAML SSO")

    1.  En el cuadro de texto **nombre del proveedor de identidades** , escriba un nombre para la configuración.
    2.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en el conjunto de aplicaciones de adaptación** , copie el valor de **Id. de entidad** y péguelo en el cuadro de texto **Id. de entidad del proveedor de identidades** .
    3.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en el conjunto de aplicaciones de adaptación** , copie el valor de **Dirección URL de SAML SSO** y péguelo en el cuadro de texto **dirección URL de SSO del proveedor de identidades** .
    4.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en el conjunto de aplicaciones de adaptación** , copie el valor de **Dirección URL de SAML SSO** y péguelo en el cuadro de texto **dirección URL de cierre de sesión de** .
    5.  Para cargar el certificado descargado, haga clic en **Elegir archivo**.
    6.  **Identificador de usuario SAML**, seleccione **perspectivas adaptación nombre de usuario**.
    7.  Como **ubicación de id de usuario SAML**, seleccione el **identificador de usuario en NameID de asunto**.
    8.  **Formato de SAML NameID**, seleccione **dirección de correo electrónico**.
    9.  Como **Habilitar SAML**, seleccione **Permitir SAML SSO e inicio de sesión de perspectivas adaptación directa**.
    10. Haga clic en **Guardar**.

9.  En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-adaptive-suite-tutorial/IC805647.png "Configurar el inicio de sesión único")
##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD iniciar sesión en el conjunto de aplicaciones de adaptación, debe aprovisionar en conjunto adaptación.  
En el caso de adaptación Suite, aprovisionamiento es una tarea manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar el aprovisionamiento de usuario, siga estos pasos:

1.  Inicie sesión como administrador en el sitio de su empresa **Suite adaptación** .

2.  Vaya a **Administrador**.

    ![Administrador] (./media/active-directory-saas-adaptive-suite-tutorial/IC805644.png "Administrador")

3.  En la sección **usuarios y Roles** , haga clic en **Agregar usuario**.

    ![Agregar usuario] (./media/active-directory-saas-adaptive-suite-tutorial/IC805648.png "Agregar usuario")

4.  En la sección **Nuevo usuario** , siga estos pasos:

    ![Enviar] (./media/active-directory-saas-adaptive-suite-tutorial/IC805649.png "Enviar")

    1.  Escriba el **nombre de** **Inicio de sesión**, **correo electrónico**, la **contraseña** de un usuario de Azure Active Directory válido que desee aprovisionar en los cuadros de texto relacionados.
    2.  Seleccione una **función**.
    3.  Haga clic en **Enviar**.

>[AZURE.NOTE] Puede usar cualquier otros conjunto de adaptación usuario cuenta herramientas de creación o API proporcionan por Suite adaptación a cuentas de usuario AAD de aprovisionamiento.

##<a name="assigning-users"></a>Asignación de usuarios

Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-adaptive-suite-perform-the-following-steps"></a>Para asignar a los usuarios a adaptación del conjunto de aplicaciones, realice los pasos siguientes:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación de **Adaptación del conjunto de programas **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-adaptive-suite-tutorial/IC805650.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-adaptive-suite-tutorial/IC767830.png "Sí")

Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

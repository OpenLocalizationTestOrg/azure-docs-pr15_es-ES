<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Boomi | Microsoft Azure" 
    description="Aprenda a usar Boomi con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más." 
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

#<a name="tutorial-azure-active-directory-integration-with-boomi"></a>Tutorial: Integración de Azure Active Directory con Boomi

El objetivo de este tutorial es mostrar la integración de Azure y Boomi.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inicio de sesión único de Boomi activado suscripción

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Boomi será capaz de inicio de sesión único en la aplicación en el sitio de su empresa de Boomi (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

El escenario descrito en este tutorial se compone de los siguientes bloques de creación:

1.  Habilitar la integración de aplicación para Boomi
2.  Configuración de inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-boomi-tutorial/IC791134.png "Escenario")
##<a name="enabling-the-application-integration-for-boomi"></a>Habilitar la integración de aplicación para Boomi

El objetivo de esta sección es describen cómo habilitar la integración de aplicación para Boomi.

###<a name="to-enable-the-application-integration-for-boomi-perform-the-following-steps"></a>Para habilitar la integración de aplicación para Boomi, siga estos pasos:

1.  En el portal de clásico Azure, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-boomi-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-boomi-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-boomi-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-boomi-tutorial/IC749322.png "Agregar una aplicación de gallerry")

6.  En el **cuadro de búsqueda**, escriba **Boomi**.

    ![Galería de aplicación] (./media/active-directory-saas-boomi-tutorial/IC790822.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **Boomi**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Boomi] (./media/active-directory-saas-boomi-tutorial/IC790823.png "Boomi")
##<a name="configuring-single-sign-on"></a>Configuración de inicio de sesión único

El objetivo de esta sección es describen cómo habilitar los usuarios autenticar a Boomi con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación **Boomi** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-boomi-tutorial/IC790824.png "Configurar el inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión Boomi** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-boomi-tutorial/IC790825.png "Configurar el inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto **Dirección URL de respuesta Boomi** , escriba la **Dirección URL de inicio de sesión de AtomSphere Boomi** (por ejemplo: "*https://platform.boomi.com/sso/AccountName/saml*") y, a continuación, haga clic en **siguiente**.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-boomi-tutorial/IC790826.png "Configurar la dirección URL de la aplicación")

4.  En la página **configurar inicio de sesión único en Boomi** , para descargar el certificado, haga clic en **Descargar certificado**y, a continuación, guarde el archivo de certificado localmente en el equipo.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-boomi-tutorial/IC790827.png "Configurar el inicio de sesión único")

5.  En una ventana de explorador web diferente, inicie sesión en el sitio de su empresa Boomi como administrador.

6.  En la barra de herramientas en la parte superior, haga clic en el nombre de la compañía y **configuración**.

    ![Programa de instalación] (./media/active-directory-saas-boomi-tutorial/IC790828.png "Programa de instalación")

7.  Haga clic en **Opciones de SSO**.

    ![Opciones de SSO] (./media/active-directory-saas-boomi-tutorial/IC790829.png "Opciones de SSO")

8.  En la sección **Opciones de inicio de sesión único** , realice los pasos siguientes:

    ![Opciones de inicio de sesión único] (./media/active-directory-saas-boomi-tutorial/IC790830.png "Opciones de inicio de sesión único")

    1.  Seleccione **Habilitar inicio de sesión único de SAML**.
    2.  Haga clic en **Importar**para cargar el certificado descargado.
    3.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en Boomi** , copie el valor de **Dirección URL de inicio de sesión remota** y, a continuación, péguelo en el cuadro de texto **Dirección URL de inicio de sesión del proveedor de identidades** .
    4.  Como **Federación Id ubicación**, seleccione **Id de federación es NameID elemento del asunto**.
    5.  Haga clic en **Guardar**.

9.  En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-boomi-tutorial/IC775560.png "Configurar el inicio de sesión único")
##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD iniciar sesión en Boomi, debe aprovisionar en Boomi.  
En el caso de Boomi, aprovisionamiento es una tarea manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar el aprovisionamiento de usuario, siga estos pasos:

1.  Inicie sesión como administrador en el sitio de su empresa **Boomi** .

2.  Vaya a **administración de usuarios \> usuarios**.

    ![Usuarios] (./media/active-directory-saas-boomi-tutorial/IC790831.png "Usuarios")

3.  Haga clic en **Agregar usuario**.

    ![Agregar usuario] (./media/active-directory-saas-boomi-tutorial/IC790832.png "Agregar usuario")

4.  En la página de diálogo **Agregar Roles de usuario** , realice los pasos siguientes:

    ![Agregar usuario] (./media/active-directory-saas-boomi-tutorial/IC790833.png "Agregar usuario")

    1.  Escriba el **nombre**, **Apellido** y **correo electrónico** de una cuenta AAD válida que desee aprovisionar en los cuadros de texto relacionados.
    2.  Haga clic en Aceptar.

>[AZURE.NOTE] Puede usar cualquier otros Boomi usuario cuenta herramientas de creación o API proporcionan por Boomi a cuentas de usuario AAD de aprovisionamiento.

##<a name="assigning-users"></a>Asignación de usuarios

Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-boomi-perform-the-following-steps"></a>Para asignar a los usuarios a Boomi, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación **Boomi **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-boomi-tutorial/IC790834.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-boomi-tutorial/IC767830.png "Sí")

Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

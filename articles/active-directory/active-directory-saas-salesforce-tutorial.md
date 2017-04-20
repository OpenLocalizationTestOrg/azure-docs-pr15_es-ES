<properties
    pageTitle="Tutorial: Integración de Azure Active Directory con Salesforce | Microsoft Azure"
    description="Aprenda a usar Salesforce con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y más."
    services="active-directory"
    documentationCenter=""
    authors="asmalser-msft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="05/16/2016"
    ms.author="asmalser-msft"/>

#<a name="tutorial-how-to-integrate-salesforce-with-azure-active-directory"></a>Tutorial: Cómo integrar Salesforce con Azure Active Directory

Este tutorial le mostrará cómo conectar su entorno de Salesforce a Azure Active Directory. Aprenderá cómo configurar el inicio de sesión único de Salesforce, cómo habilitar aprovisionamiento automatizado usuario y cómo asignar los usuarios tengan acceso a Salesforce.

##<a name="prerequisites"></a>Requisitos previos

1. Para obtener acceso a Azure Active Directory a través del [portal clásica Azure](https://manage.windowsazure.com), primero debe tener una suscripción válida de Azure.

2. Debe tener un inquilino válido en [Salesforce.com](https://www.salesforce.com/).

> [AZURE.IMPORTANT] Si está utilizando una cuenta de **prueba** de Salesforce.com, a continuación, podrá configurar aprovisionamiento automatizado usuario. Cuentas de prueba no tiene el acceso de API necesario habilitado hasta que se adquieren.
> 
> Puede moverse por esta limitación utilizando una [cuenta gratuita para programador](https://developer.salesforce.com/signup) para completar este tutorial.

Si está utilizando un entorno de recinto de seguridad de Salesforce, consulte el [tutorial de integración de espacio aislado de Salesforce](https://go.microsoft.com/fwLink/?LinkID=521879).

##<a name="video-tutorials"></a>Tutoriales de vídeo

Puede seguir este tutorial mediante los siguientes vídeos.

**Tutorial de vídeo parte 1: cómo habilitar inicio de sesión único**

> [AZURE.VIDEO integrating-salesforce-with-azure-ad-how-to-enable-single-sign-on]

**Tutorial de vídeo parte dos: Cómo automatizar el aprovisionamiento de usuario**

> [AZURE.VIDEO integrating-salesforce-with-azure-ad-how-to-automate-user-provisioning]

##<a name="step-1-add-salesforce-to-your-directory"></a>Paso 1: Agregar Salesforce al directorio

1. En el [portal de clásica Azure](https://manage.windowsazure.com), en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Desde el panel de navegación izquierdo, seleccione Active Directory.][0]

2. En la lista de **directorio** , seleccione el directorio que le gustaría agregar Salesforce a.

3. En el menú superior, haga clic en acerca de las **aplicaciones** .

    ![Haga clic en aplicaciones.][1]

4. Haga clic en **Agregar** en la parte inferior de la página.

    ![Haga clic en Agregar para agregar una nueva aplicación.][2]

5. En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Haga clic en Agregar una aplicación de la galería.][3]

6. En el **cuadro de búsqueda**, escriba **Salesforce**. A continuación, seleccione **Salesforce** desde los resultados y haga clic en **completado** para agregar la aplicación.

    ![Agregar Salesforce.][4]

7. Ahora debe ver la página de inicio rápido de Salesforce:

    ![Página de inicio rápido de Salesforce en Azure AD][5]

##<a name="step-2-enable-single-sign-on"></a>Paso 2: Habilitar inicio de sesión único

1. Antes de configurar el inicio de sesión único, debe configurar e implementar un dominio personalizado para su entorno de Salesforce. Para obtener instrucciones sobre cómo hacerlo, vea [Establecer un nombre de dominio](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_setup.htm&language=en_US).

2. En la página de inicio rápido de Salesforce en Azure AD, haga clic en el botón **Inicio de sesión único en configurar** .

    ![El botón de inicio de sesión único de configurar][6]

3. Se abrirá un cuadro de diálogo y verá una pantalla que le pregunta "¿cómo desea que los usuarios para iniciar sesión Salesforce?" Seleccione **Azure AD inicio de sesión único**y, a continuación, haga clic en **siguiente**.

    ![Seleccione en el inicio de sesión único de Azure AD][7]

    > [AZURE.NOTE] Para conocer más acerca de las diferentes solo inicio de sesión de opciones, [haga clic aquí](../active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work)

4. En la página **Configurar opciones de aplicación** , rellene el **Inicio de sesión en la dirección URL** , escriba la dirección URL de dominio de Salesforce con el siguiente formato:
 - Cuenta de la empresa:`https://<domain>.my.salesforce.com`
 - Cuenta de desarrollador:`https://<domain>-dev-ed.my.salesforce.com` 

    ![Escriba la dirección URL de inicio de sesión][8]

5. En la página **configurar inicio de sesión único en Salesforce** , haga clic en **Descargar certificado**y, a continuación, guarde el archivo de certificado localmente en el equipo.

    ![Descargar certificado][9]

6. Abrir una nueva pestaña en el explorador e inicie sesión en su cuenta de administrador de Salesforce.

7. En el panel de navegación de **Administrador** , haga clic en **Controles de seguridad** para expandir la sección relacionada. A continuación, haga clic en **Configuración de inicio de sesión único**.

    ![Haga clic en configuración de inicio de sesión único en controles de seguridad][10]

8. En la página de **Configuración de inicio de sesión único** , haga clic en el botón **Editar** .

    ![Haga clic en el botón Editar][11]

    > [AZURE.NOTE] Si no puede habilitar la configuración de inicio de sesión único para su cuenta de Salesforce, deberá ponerse en contacto con soporte técnico de Salesforce para tiene habilitada para la característica.

9. Seleccione **SAML habilitado**y, a continuación, haga clic en **Guardar**.

    ![Seleccione SAML habilitado][12]

10. Para configurar la configuración de inicio de sesión único de SAML, haga clic en **nuevo**.

    ![Seleccione SAML habilitado][13]

11. En la página **SAML solo inicio de sesión en configuración de editar** , realizar las configuraciones siguientes:

    ![Captura de pantalla de las configuraciones que debe hacer][14]

 - Para el campo **nombre** , escriba un nombre descriptivo para esta configuración. Proporcionar un valor de **nombre** rellenar automáticamente el cuadro de texto **Nombre de la API** .

 - En Azure AD, copie el valor de **Dirección URL del emisor** y péguelo en el campo del **emisor** de Salesforce.

 - En el **cuadro de texto Id. de entidad**y escriba su nombre de dominio de Salesforce utilizando el modelo siguiente:
     - Cuenta de la empresa:`https://<domain>.my.salesforce.com`
     - Cuenta de desarrollador:`https://<domain>-dev-ed.my.salesforce.com`

 - Haga clic en **Examinar** o **Elegir archivo** para abrir el cuadro de diálogo **Elegir archivo para cargar** , seleccione el certificado de Salesforce y, a continuación, haga clic en **Abrir** para cargar el certificado.

 - **Tipo de identidad de SAML**, seleccione **aserción contiene salesforce.com nombre de usuario**.

 - **Ubicación de identidades SAML**, seleccione **identidad está en el elemento NameIdentifier de la instrucción de asunto**

 - En Azure AD, copie el valor de **Dirección URL de inicio de sesión remota** y péguelo en el campo **Dirección URL de inicio de sesión del proveedor de identidades** de Salesforce.

 - Para **Solicitar iniciados por enlace servicio proveedor**, seleccione **Redirigir HTTP**.

 - Por último, haga clic en **Guardar** para aplicar la configuración de inicio de sesión único de SAML.

12. En el panel de navegación izquierdo de Salesforce, haga clic en **Administración de dominios** para expandir la sección relacionada y, a continuación, haga clic en **Mi dominio**.

    ![Haga clic en mi dominio][15]

13. Desplácese hacia abajo hasta la sección **Configuración de autenticación** y haga clic en el botón **Editar** .

    ![Haga clic en el botón Editar][16]

14. En la sección **Servicio de autenticación** , seleccione el nombre descriptivo de la configuración de SAML SSO y, a continuación, haga clic en **Guardar**.

    ![Seleccione la configuración de SSO][17]

    > [AZURE.NOTE] Si se selecciona más de un servicio de autenticación, a continuación, cuando los usuarios intentan iniciar inicio de sesión único en su entorno de Salesforce, se le pedirá que selecciona el servicio de autenticación que desea iniciar sesión. Si no desea que esto suceda, a continuación, debe **Salir de todos los otros servicios de autenticación desactivadas**.

15. En Azure AD, seleccione la casilla de verificación de confirmación de configuración de inicio de sesión único para habilitar el certificado que ha cargado en Salesforce. A continuación, haga clic en **siguiente**.

    ![Active la casilla de verificación confirmación][18]

16. En la página final del cuadro de diálogo, escriba una dirección de correo electrónico si desea recibir notificaciones de correo electrónico de errores y advertencias relacionadas con el mantenimiento de esta configuración de inicio de sesión único. 

    ![Escriba su dirección de correo electrónico.][19]

17. Haga clic en **completado** para cerrar el cuadro de diálogo. Para probar la configuración, consulte la sección siguiente titulada [Asignar los usuarios de Salesforce](#step-4-assign-users-to-salesforce).

##<a name="step-3-enable-automated-user-provisioning"></a>Paso 3: Habilitar aprovisionamiento automatizado usuario

1. En la página de inicio rápido de Azure AD de Salesforce, haga clic en el botón de **aprovisionamiento de usuario de configurar** .

    ![Haga clic en el botón configurar aprovisionamiento de usuario][20]

2. En el cuadro de diálogo **Configurar el aprovisionamiento de usuario** , escriba el nombre de usuario de administración de Salesforce y la contraseña.

    ![Escriba su contraseña o el nombre de usuario de administración][21]

    > [AZURE.NOTE] Si está configurando un entorno de producción, lo mejor es crear una nueva cuenta de administrador de Salesforce específicamente para este paso. Esta cuenta debe tener asignado en Salesforce el perfil de **Administrador del sistema** .

3. Para obtener la seguridad de Salesforce token, abra una nueva pestaña e inicie sesión en la misma cuenta de administrador de Salesforce. En la esquina superior derecha de la página, haga clic en su nombre y, a continuación, haga clic en **Configuración de mi**.

    ![Haga clic en su nombre y luego haga clic en configuración de mi][22]

4. En el panel de navegación izquierdo, haga clic en **Personal** para expandir la sección relacionada y, a continuación, haga clic en **Restablecer mi Token de seguridad**.

    ![Haga clic en su nombre y luego haga clic en configuración de mi][23]

5. En la página **Restablecer mi Token de seguridad** , haga clic en el botón **Restablecer el Token de seguridad** .

    ![Lea las advertencias.][24]

6. Compruebe la Bandeja de entrada de correo electrónico asociada a esta cuenta de administrador. Buscar un mensaje de correo desde Salesforce.com que contiene el token de seguridad nuevo.

7. Copiar el token, vaya a la ventana de Azure AD y péguela en el campo de **Token de seguridad del usuario** . A continuación, haga clic en **siguiente**.

    ![Pegue el token de seguridad][25]

8. En la página confirmación, puede recibir notificaciones por correo electrónico cuando se produzcan errores de aprovisionamiento. Haga clic en **completado** para cerrar el cuadro de diálogo.

    ![Escriba su dirección de correo electrónico para recibir notificaciones][26]

##<a name="step-4-assign-users-to-salesforce"></a>Paso 4: Asignar usuarios a Salesforce

1. Para probar la configuración, comience generando una nueva cuenta de prueba en el directorio.

2. En la página de inicio rápido de Salesforce, haga clic en el botón **Asignar usuarios** .

    ![Haga clic en asignar a los usuarios][27]

3. Seleccione el usuario de prueba y haga clic en el botón **asignar** en la parte inferior de la pantalla:

 - Si no lo ha hecho habilitar aprovisionamiento automatizado usuario, verá el mensaje siguiente para confirmar:

        ![Confirm the assignment.][28]

 - Si ha habilitado automatizado de aprovisionamiento de usuarios, verá un mensaje para definir qué tipo de perfil de Salesforce debería tener el usuario. Los usuarios recientemente creados deberían aparecer en su entorno de Salesforce después de unos minutos.

        ![Confirm the assignment.][29]

        > [AZURE.IMPORTANT] Si se aprovisionamiento en un entorno de **desarrollo** de Salesforce, tendrá un número limitado de licencias disponibles para cada perfil. Por lo tanto, es mejor de aprovisionamiento de usuarios para el perfil de **Usuario gratuitos vacías** , que tiene 4,999 licencias disponibles.

4. Para probar la configuración de inicio de sesión único, abra el Panel de acceso en [https://myapps.microsoft.com](https://myapps.microsoft.com/), a continuación, inicie sesión en la cuenta de prueba y haga clic en **Salesforce**.

##<a name="related-articles"></a>Artículos relacionados

- [Índice de artículos de administración de aplicaciones de Azure Active Directory](active-directory-apps-index.md)
- [Lista de tutoriales sobre cómo integrar aplicaciones de SaaS](active-directory-saas-tutorial-list.md)

[0]: ./media/active-directory-saas-salesforce-tutorial/azure-active-directory.png
[1]: ./media/active-directory-saas-salesforce-tutorial/applications-tab.png
[2]: ./media/active-directory-saas-salesforce-tutorial/add-app.png
[3]: ./media/active-directory-saas-salesforce-tutorial/add-app-gallery.png
[4]: ./media/active-directory-saas-salesforce-tutorial/add-salesforce.png
[5]: ./media/active-directory-saas-salesforce-tutorial/salesforce-added.png
[6]: ./media/active-directory-saas-salesforce-tutorial/config-sso.png
[7]: ./media/active-directory-saas-salesforce-tutorial/select-azure-ad-sso.png
[8]: ./media/active-directory-saas-salesforce-tutorial/config-app-settings.png
[9]: ./media/active-directory-saas-salesforce-tutorial/download-certificate.png
[10]: ./media/active-directory-saas-salesforce-tutorial/sf-admin-sso.png
[11]: ./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-edit.png
[12]: ./media/active-directory-saas-salesforce-tutorial/sf-enable-saml.png
[13]: ./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-new.png
[14]: ./media/active-directory-saas-salesforce-tutorial/sf-saml-config.png
[15]: ./media/active-directory-saas-salesforce-tutorial/sf-my-domain.png
[16]: ./media/active-directory-saas-salesforce-tutorial/sf-edit-auth-config.png
[17]: ./media/active-directory-saas-salesforce-tutorial/sf-auth-config.png
[18]: ./media/active-directory-saas-salesforce-tutorial/sso-confirm.png
[19]: ./media/active-directory-saas-salesforce-tutorial/sso-notification.png
[20]: ./media/active-directory-saas-salesforce-tutorial/config-prov.png
[21]: ./media/active-directory-saas-salesforce-tutorial/config-prov-dialog.png
[22]: ./media/active-directory-saas-salesforce-tutorial/sf-my-settings.png
[23]: ./media/active-directory-saas-salesforce-tutorial/sf-personal-reset.png
[24]: ./media/active-directory-saas-salesforce-tutorial/sf-reset-token.png
[25]: ./media/active-directory-saas-salesforce-tutorial/got-the-token.png
[26]: ./media/active-directory-saas-salesforce-tutorial/prov-confirm.png
[27]: ./media/active-directory-saas-salesforce-tutorial/assign-users.png
[28]: ./media/active-directory-saas-salesforce-tutorial/assign-confirm.png
[29]: ./media/active-directory-saas-salesforce-tutorial/assign-sf-profile.png

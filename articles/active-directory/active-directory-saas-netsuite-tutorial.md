<properties
    pageTitle="Tutorial: Integración de Azure Active Directory con NetSuite | Microsoft Azure"
    description="Aprenda a usar NetSuite con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más."
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

#<a name="tutorial-how-to-integrate-netsuite-with-azure-active-directory"></a>Tutorial: Cómo integrar NetSuite con Azure Active Directory

Este tutorial le mostrará cómo conectar el entorno de NetSuite a Azure Active Directory (AD Azure). Aprenderá cómo configurar el inicio de sesión único en NetSuite, cómo habilitar aprovisionamiento automatizado usuario y cómo asignar los usuarios tengan acceso a NetSuite. 

##<a name="prerequisites"></a>Requisitos previos

1. Para obtener acceso a Azure Active Directory a través del [portal clásica Azure](https://manage.windowsazure.com), primero debe tener una suscripción válida de Azure.

2. Debe tener acceso de administrador a una suscripción [NetSuite](http://www.netsuite.com/portal/home.shtml) . Puede usar una cuenta de prueba gratuita para alguno de los servicios.

##<a name="step-1-add-netsuite-to-your-directory"></a>Paso 1: Agregar NetSuite al directorio

1. En el [portal de clásica Azure](https://manage.windowsazure.com), en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Desde el panel de navegación izquierdo, seleccione Active Directory.][0]

2. En la lista de **directorio** , seleccione el directorio que le gustaría agregar NetSuite a.

3. En el menú superior, haga clic en acerca de las **aplicaciones** .

    ![Haga clic en aplicaciones.][1]

4. Haga clic en **Agregar** en la parte inferior de la página.

    ![Haga clic en Agregar para agregar una nueva aplicación.][2]

5. En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Haga clic en Agregar una aplicación de la galería.][3]

6. En el **cuadro de búsqueda**, escriba **NetSuite**. A continuación, seleccione **NetSuite** en los resultados y haga clic en **completado** para agregar la aplicación.

    ![Agregar NetSuite.][4]

7. Ahora debe ver la página de inicio rápido para NetSuite:

    ![Página de inicio rápido de NetSuite en Azure AD][5]

##<a name="step-2-enable-single-sign-on"></a>Paso 2: Habilitar inicio de sesión único

1. En Azure AD, en la página de inicio rápido para NetSuite, haga clic en el botón **Inicio de sesión único en configurar** .

    ![El botón de inicio de sesión único de configurar][6]

2. Se abrirá un cuadro de diálogo y verá una pantalla que le pregunta "¿cómo desea que los usuarios para iniciar sesión NetSuite?" Seleccione **Azure AD inicio de sesión único**y, a continuación, haga clic en **siguiente**.

    ![Seleccione en el inicio de sesión único de Azure AD][7]

    > [AZURE.NOTE] Para conocer más acerca de las diferentes solo inicio de sesión de opciones, [haga clic aquí](../active-directory-appssoaccess-whatis/#how-does-single-sign-on-with-azure-active-directory-work)

3. En la página de **Configuración de aplicación** , para el campo **Dirección URL de respuesta** , escriba la dirección URL de inquilinos de NetSuite mediante uno de los siguientes formatos:
    - `https://<tenant-name>.netsuite.com/saml2/acs`
    - `https://<tenant-name>.na1.netsuite.com/saml2/acs`
    - `https://<tenant-name>.na2.netsuite.com/saml2/acs`
    - `https://<tenant-name>.sandbox.netsuite.com/saml2/acs`
    - `https://<tenant-name>.na1.sandbox.netsuite.com/saml2/acs`
    - `https://<tenant-name>.na2.sandbox.netsuite.com/saml2/acs`

    ![Escriba la dirección URL de inquilinos][8]

4. En la página **configurar inicio de sesión único en NetSuite** , haga clic en la **descarga de metadatos**y, a continuación, guarde el archivo de certificado localmente en el equipo.

    ![Descargue los metadatos.][9]

5. Abrir una nueva pestaña en el explorador e inicie sesión en el sitio de su empresa Netsuite como administrador.

6. En la barra de herramientas en la parte superior de la página, haga clic en **configuración**, haga clic en **Administrador de la instalación**.

    ![Vaya a Administrador de instalación][10]

7. En la lista de **Tareas de configuración** , seleccione **integración**.

    ![Vaya a integración.][11]

8. En la sección **Autenticación de administrar** , haga clic en **el inicio de sesión único de SAML**.

    ![Vaya a inicio de sesión único de SAML][12]

9. En la página **Configuración de SAML** , realice los pasos siguientes:

    - En Azure Active Directory, copie el valor de **Dirección URL de inicio de sesión remota** y péguela en el campo de la **Página de inicio de sesión del proveedor de identidades** en NetSuite.

        ![La página de configuración de SAML.][13]

    - En NetSuite, seleccione el **Método de autenticación principal**.

    - El campo con la etiqueta **SAMLV2 metadatos del proveedor de identidades**, seleccione **Cargar un archivo de metadatos de IDP**. A continuación, haga clic en **Examinar** para cargar el archivo de metadatos que ha descargado en el paso 4 #.

        ![Cargar los metadatos][16]

    - Haga clic en **Enviar**.

9. En Azure AD, seleccione la casilla de verificación de confirmación de configuración de inicio de sesión único para habilitar el certificado que ha cargado en NetSuite. A continuación, haga clic en **siguiente**.

    ![Active la casilla de verificación confirmación][14]

10. En la página final del cuadro de diálogo, escriba una dirección de correo electrónico si desea recibir notificaciones de correo electrónico de errores y advertencias relacionadas con el mantenimiento de esta configuración de inicio de sesión único. 

    ![Escriba su dirección de correo electrónico.][15]

11. Haga clic en **completado** para cerrar el cuadro de diálogo. A continuación, haga clic en **atributos** en la parte superior de la página.

    ![Haga clic en atributos.][17]

12. Haga clic en **Agregar usuario atributo**.

    ![Haga clic en Agregar usuario atributo.][18]

13. Para el campo **Nombre de atributo** , escriba en `account`. Para el campo de **Valor de atributo** , escriba su ID de cuenta de NetSuite. Instrucciones sobre cómo encontrar el identificador de cuenta se incluyen a continuación:

    ![Agregar su cuenta de NetSuite Id.][19]

    - NetSuite, haga clic en **configuración** en el menú de navegación superior.
    - Haga clic en la sección de **Tareas de configuración** del menú de navegación izquierdo, seleccione la sección **integración** y haga clic en **Preferencias de servicios Web**.
    - Copie su ID de cuenta de NetSuite y péguelo en el campo de **Valor del atributo** en Azure AD.

        ![Obtener el ID de cuenta][20]

14. Haga clic en **completado** para terminar de agregar el atributo SAML Azure AD. A continuación, haga clic en **Aplicar cambios** en el menú de la parte inferior.

    ![Guarde los cambios.][21]

15. Antes de que los usuarios pueden realizar el inicio de sesión único en NetSuite, se deben primero asignar los permisos adecuados en NetSuite. Siga las instrucciones siguientes para asignar los permisos.

    - En el menú de navegación superior, haga clic en **configuración**, haga clic en **Administrador de la instalación**.

        ![Vaya a Administrador de instalación][10]

    - En el menú de navegación izquierdo, seleccione **Usuarios y funciones**y luego haga clic en **Administrar Roles**.

        ![Ir a administrar Roles][22]

    - Haga clic en **nuevo rol**.

    - Escriba un **nombre** para la nueva función y seleccione la casilla de verificación **Única sesión solo** .

        ![Nombre de la nueva función.][23]

    - Haga clic en **Guardar**.

    - En el menú de la parte superior, haga clic en **permisos**. A continuación, haga clic en **configuración**.

        ![Vaya a permisos][24]

    - Seleccione **establecer el SAM inicio de sesión único**y, a continuación, haga clic en **Agregar**.

    - Haga clic en **Guardar**.

    - En el menú de navegación superior, haga clic en **configuración**, haga clic en **Administrador de la instalación**.

        ![Vaya a Administrador de instalación][10]

    - En el menú de navegación izquierdo, seleccione **Usuarios y funciones**y luego haga clic en **Administrar usuarios**.

        ![Vaya a administrar usuarios][25]

    - Seleccione un usuario de prueba. A continuación, haga clic en **Editar**.

        ![Vaya a administrar usuarios][26]

    - En el cuadro de diálogo de Roles, seleccione el rol que ha creado y haga clic en **Agregar**.

        ![Vaya a administrar usuarios][27]

    - Haga clic en **Guardar**.

19. Para probar la configuración, consulte la sección siguiente titulada [Asignar usuarios a NetSuite](#step-4-assign-users-to-netsuite).

##<a name="step-3-enable-automated-user-provisioning"></a>Paso 3: Habilitar aprovisionamiento automatizado usuario

> [AZURE.NOTE] De forma predeterminada, los usuarios aprovisionados se agregará a la subsidiaria de raíz de su entorno de NetSuite.

1. En Azure Active Directory, en la página de inicio rápido para NetSuite, haga clic en **Configurar el aprovisionamiento de usuario**.

    ![Configurar el aprovisionamiento de usuario][28]

2. En el cuadro de diálogo que se abre, escriba sus credenciales de administrador para NetSuite, a continuación, haga clic en **siguiente**.

    ![Escriba sus credenciales de administrador de NetSuite.][29]

3. En la página de confirmación, escriba su dirección de correo electrónico para recibir notificaciones de errores de aprovisionamiento.

    ![Confirmar.][30]

4. Haga clic en **completado** para cerrar el cuadro de diálogo.

##<a name="step-4-assign-users-to-netsuite"></a>Paso 4: Asignar usuarios a NetSuite

1. Para probar la configuración, empiece a crear una nueva cuenta de prueba en el directorio.

2. En la página de inicio rápido de NetSuite, haga clic en el botón **Asignar usuarios** .

    ![Haga clic en asignar a los usuarios][31]

3. Seleccione el usuario de prueba y haga clic en el botón **asignar** en la parte inferior de la pantalla:

 - Si no lo ha hecho habilitar aprovisionamiento automatizado usuario, verá el mensaje siguiente para confirmar:

        ![Confirm the assignment.][32]

 - Si ha habilitado automatizado de aprovisionamiento de usuarios, verá un mensaje para definir qué tipo de función el usuario debe tener en NetSuite. Los usuarios recientemente creados deberían aparecer en su entorno de NetSuite después de unos minutos.

4. Para probar la configuración de inicio de sesión único, abra el Panel de acceso en [https://myapps.microsoft.com](https://myapps.microsoft.com/), inicie sesión en la cuenta de prueba y haga clic en **NetSuite**.

##<a name="related-articles"></a>Artículos relacionados

- [Índice de artículos de administración de aplicaciones de Azure Active Directory](active-directory-apps-index.md)
- [Lista de tutoriales sobre cómo integrar aplicaciones de SaaS](active-directory-saas-tutorial-list.md)

[0]: ./media/active-directory-saas-netsuite-tutorial/azure-active-directory.png
[1]: ./media/active-directory-saas-netsuite-tutorial/applications-tab.png
[2]: ./media/active-directory-saas-netsuite-tutorial/add-app.png
[3]: ./media/active-directory-saas-netsuite-tutorial/add-app-gallery.png
[4]: ./media/active-directory-saas-netsuite-tutorial/add-netsuite.png
[5]: ./media/active-directory-saas-netsuite-tutorial/quick-start-netsuite.png
[6]: ./media/active-directory-saas-netsuite-tutorial/config-sso.png
[7]: ./media/active-directory-saas-netsuite-tutorial/sso-netsuite.png
[8]: ./media/active-directory-saas-netsuite-tutorial/sso-config-netsuite.png
[9]: ./media/active-directory-saas-netsuite-tutorial/config-sso-netsuite.png
[10]: ./media/active-directory-saas-netsuite-tutorial/ns-setup.png
[11]: ./media/active-directory-saas-netsuite-tutorial/ns-integration.png
[12]: ./media/active-directory-saas-netsuite-tutorial/ns-saml.png
[13]: ./media/active-directory-saas-netsuite-tutorial/ns-saml-setup.png
[14]: ./media/active-directory-saas-netsuite-tutorial/ns-sso-confirm.png
[15]: ./media/active-directory-saas-netsuite-tutorial/sso-email.png
[16]: ./media/active-directory-saas-netsuite-tutorial/ns-sso-setup.png
[17]: ./media/active-directory-saas-netsuite-tutorial/ns-attributes.png
[18]: ./media/active-directory-saas-netsuite-tutorial/ns-add-attribute.png
[19]: ./media/active-directory-saas-netsuite-tutorial/ns-add-account.png
[20]: ./media/active-directory-saas-netsuite-tutorial/ns-account-id.png
[21]: ./media/active-directory-saas-netsuite-tutorial/ns-save-saml.png
[22]: ./media/active-directory-saas-netsuite-tutorial/ns-manage-roles.png
[23]: ./media/active-directory-saas-netsuite-tutorial/ns-new-role.png
[24]: ./media/active-directory-saas-netsuite-tutorial/ns-sso.png
[25]: ./media/active-directory-saas-netsuite-tutorial/ns-manage-users.png
[26]: ./media/active-directory-saas-netsuite-tutorial/ns-edit-user.png
[27]: ./media/active-directory-saas-netsuite-tutorial/ns-add-role.png
[28]: ./media/active-directory-saas-netsuite-tutorial/netsuite-provisioning.png
[29]: ./media/active-directory-saas-netsuite-tutorial/ns-creds.png
[30]: ./media/active-directory-saas-netsuite-tutorial/ns-confirm.png
[31]: ./media/active-directory-saas-netsuite-tutorial/assign-users.png
[32]: ./media/active-directory-saas-netsuite-tutorial/assign-confirm.png

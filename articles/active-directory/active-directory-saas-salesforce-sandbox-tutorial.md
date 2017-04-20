<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con recinto de seguridad de Salesforce | Microsoft Azure"
    description="Obtenga información sobre cómo utilizar espacio aislado de Salesforce con Azure Active Directory para habilitar el inicio de sesión único, automatizado de aprovisionamiento y más!." 
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
    ms.date="10/28/2016" 
    ms.author="jeedes" />


#<a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>Tutorial: Integración de Azure Active Directory con recinto de seguridad de Salesforce
>[AZURE.TIP]Para enviar comentarios, haga clic en [aquí](http://go.microsoft.com/fwlink/?LinkId=521878).
  
El objetivo de este tutorial es mostrar la integración de Azure y espacio aislado de Salesforce.  
Entornos limitados le permiten crear varias copias de su organización en entornos independientes para una gran variedad de efectos, como desarrollo, pruebas y aprendizaje, sin afectar a los datos y aplicaciones de su organización de producción de Salesforce.  
Para obtener más detalles, vea [Información general de espacio aislado](https://help.salesforce.com/HTViewHelpDoc?id=create_test_instance.htm&language=en_US)
  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un espacio aislado en Salesforce.com
  
Si aún no tiene un espacio aislado válido en Salesforce.com, debe ponerse en contacto con Salesforce.
  
El escenario descrito en este tutorial se compone de los siguientes bloques de creación:

1.  Habilitar la integración de las aplicaciones de recinto de seguridad de Salesforce
2.  Configuración de inicio de sesión único
3.  Habilitar el dominio
4.  Configuración de aprovisionamiento de usuario
5.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC769571.png "Escenario")
##<a name="enabling-the-application-integration-for-salesforce-sandbox"></a>Habilitar la integración de las aplicaciones de recinto de seguridad de Salesforce
  
El objetivo de esta sección es describen cómo habilitar la integración de las aplicaciones de recinto de seguridad de Salesforce.

###<a name="to-enable-the-application-integration-for-salesforce-sandbox-perform-the-following-steps"></a>Para habilitar la integración de las aplicaciones de recinto de seguridad de Salesforce, realice los pasos siguientes:

1.  En el portal de clásico Azure, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC700994.png "Aplicaciones")

4.  Para abrir la **Galería de aplicación**, haga clic en **Agregar una aplicación**y, a continuación, haga clic en **Agregar una aplicación para mi organización usar**.

    ![¿Qué desea hacer?] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC700995.png "¿Qué desea hacer?")

5.  En el **cuadro de búsqueda**, escriba el **Espacio aislado de Salesforce**.

    ![Galería de aplicación] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC710978.png "Galería de aplicación")

6.  En el panel de resultados, seleccione **Salesforce recinto de seguridad**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Espacio aislado de Salesforce] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC746474.png "Espacio aislado de Salesforce")
##<a name="configuring-single-sign-on"></a>Configuración de inicio de sesión único
  
El objetivo de esta sección es describen cómo habilitar los usuarios autenticar Salesforce con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación de **Espacio aislado de Salesforce** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar inicio de sesión único] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC749323.png "Configurar inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión espacio aislado de Salesforce** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Espacio aislado de Salesforce] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC746479.png "Espacio aislado de Salesforce")

3.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto de **Inicio de sesión en la dirección URL** , escriba la dirección URL con el siguiente patrón `http://company.my.salesforce.com`y, a continuación, haga clic en **siguiente**.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC781022.png "Configurar la dirección URL de la aplicación")

4. Si ya ha configurado un inicio de sesión único para otra instancia de espacio aislado de Salesforce en su directorio, también debe configurar el **identificador** para tener el mismo valor que la **dirección URL de inicio de sesión**. El campo **identificador** puede encontrarse activando la casilla de verificación **Mostrar configuración avanzada** en la página **Configurar la dirección URL de aplicación** del cuadro de diálogo.

4.  En la página **configurar inicio de sesión único en espacio aislado de Salesforce** , haga clic en **Descargar certificado**y, a continuación, guarde el archivo de certificado en el equipo.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC781023.png "Configurar el inicio de sesión único")

5.  En una ventana de explorador web diferente, inicie sesión en el espacio aislado de Salesforce como administrador.

6.  En el menú de la parte superior, haga clic en **configuración**.

    ![Programa de instalación] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC781024.png "Programa de instalación")

7.  En el panel de navegación de la izquierda, haga clic en **Controles de seguridad**y, a continuación, haga clic en **Configuración de inicio de sesión único**.

    ![Configuración de inicio de sesión único] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC781025.png "Configuración de inicio de sesión único")

8.  En la sección Configuración de inicio de sesión único, realice los pasos siguientes:

    ![Configuración de inicio de sesión único] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC781026.png "Configuración de inicio de sesión único")

    una.  Seleccione **SAML habilitado**.
    
    b.  Haga clic en **nuevo**.

9.  En la sección Configuración de inicio de sesión único de SAML, realice los pasos siguientes:

    ![Configuración de inicio de sesión único de SAML] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC781027.png "Configuración de inicio de sesión único de SAML")

    una.  En el cuadro de texto Nombre, escriba el nombre de la configuración (por ejemplo: *SPSSOWAAD\_prueba*).
    
    b.  En el portal de clásico Azure, en la página de cuadro de diálogo **configurar inicio de sesión único en espacio aislado de Salesforce** , copie el valor de **Dirección URL del emisor** y péguelo en el cuadro de texto del **emisor** .
    
    c.  En el cuadro de texto **Id. de entidad** , escriba **https://test.salesforce.com** si esta es la primera instancia de espacio aislado de Salesforce que va a agregar al directorio. Si ya ha agregado una instancia de espacio aislado de Salesforce, a continuación, para el tipo de **Id. de entidad** en el **Inicio de sesión en la dirección URL**, que debe estar en este formato:`http://company.my.salesforce.com`
    
    d.  Haga clic en **Examinar** para cargar el certificado descargado.
    
    e.  Como **Tipo de identidad de SAML**, seleccione **aserción contiene el identificador de la federación desde el objeto de usuario**.
    
    f.  Como **Ubicación de identidades SAML**, seleccione **identidad está en el elemento NameIdentifier de la instrucción de asunto**.
    
    g.  En el portal de clásico Azure, en la página de cuadro de diálogo **configurar inicio de sesión único en espacio aislado de Salesforce** , copie el valor de **Dirección URL de inicio de sesión remota** y, a continuación, péguelo en el cuadro de texto **Dirección URL de inicio de sesión del proveedor de identidades** .
    
    h.  SFDC no admite el cierre de sesión SAML.  Como solución alternativa, pegue 'https://login.windows.net/common/wsfederation?wa=wsignout1.0' en el cuadro de texto **Dirección URL de cierre de sesión del proveedor de identidades** .
    
    .  Como **Servicio iniciado solicitar enlace proveedor**, seleccione **HTTP POST**.
    
    j. Haga clic en **Guardar**.

10. En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC781028.png "Configurar el inicio de sesión único")

##<a name="enabling-your-domain"></a>Habilitar el dominio
  
Esta sección se supone que ya ha creado un dominio.  
Para obtener más detalles, vea [Definir su nombre de dominio](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US).

###<a name="to-enable-your-domain-perform-the-following-steps"></a>Para habilitar el dominio, siga estos pasos:

1.  En el panel de navegación izquierdo, haga clic en **Administración de dominios**y, a continuación, haga clic en **mi dominio.**

    ![Mi dominio] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC781029.png "Mi dominio")

    >[AZURE.NOTE]Asegúrese de que el dominio se ha configurado correctamente.

2.  En la sección **Configuración de la página de inicio de sesión** , haga clic en **Editar**, a continuación, como **Servicio de autenticación**, seleccione el nombre de la configuración de inicio de sesión único de SAML de la sección anterior y, por último haga clic en **Guardar**.

    ![Mi dominio] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC781030.png "Mi dominio")
  
Tan pronto como tiene un dominio configurado, los usuarios deben usar la dirección URL de dominio para iniciar sesión en el espacio aislado de Salesforce.  
Para obtener el valor de la dirección URL, haga clic en el perfil SSO que haya creado en la sección anterior.
##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
El objetivo de esta sección es describen cómo habilitar el usuario de aprovisionamiento de cuentas de usuario de Active Directory de espacio aislado de Salesforce.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar el aprovisionamiento de usuario, siga estos pasos:

1.  En el portal de Salesforce, en la barra de navegación, seleccione su nombre para expandir el menú de usuario:

    ![Mi configuración] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC698773.png "Mi configuración")

2.  En el menú de usuario, seleccione **Configuración de mi** para abrir su página de **Configuración de mi** .

3.  En el panel izquierdo, haga clic en **Personal** para expandir la sección Personal y, a continuación, haga clic en **Restablecer mi Token de seguridad**:

    ![Mi configuración] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC698774.png "Mi configuración")

4.  En la página **Restablecer mi Token de seguridad** , haga clic en **Restablecer el Token de seguridad** para solicitar un correo electrónico que contiene el token de seguridad de Salesforce.com.

    ![Nuevo símbolo] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC698776.png "Nuevo símbolo")

5.  Compruebe la Bandeja de entrada de correo electrónico para un correo electrónico desde Salesforce.com con "**confirmación de seguridad de Salesforce.com**" como asunto.

6.  Revise este correo electrónico y copie el valor del token de seguridad.

7.  En el portal de clásico Azure, en la página de integración de aplicación de **salesforce recinto de seguridad** , haga clic en **Configurar el aprovisionamiento de usuario** para abrir el cuadro de diálogo **Configurar el aprovisionamiento de usuario** .

    ![Configurar el aprovisionamiento de usuario] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC769573.png "Configurar el aprovisionamiento de usuario")

8.  En la página **Escriba sus credenciales de Salesforce recinto de seguridad para habilitar el aprovisionamiento de usuario automática** , proporcione la siguiente configuración:

    ![Espacio aislado de Salesforce] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC746476.png "Espacio aislado de Salesforce")

    una.  En el cuadro de texto **Nombre de usuario de administración de espacio aislado de Salesforce** , escriba un nombre de cuenta de espacio aislado de Salesforce que tiene el perfil de **Administrador del sistema** en Salesforce.com asignado.

    b.  En el cuadro de texto **Contraseña de administrador de espacio aislado de Salesforce** , escriba la contraseña para esta cuenta.

    c.  En el cuadro de texto **El Token de seguridad de usuario** , pegue el valor del token de seguridad.

    d.  Haga clic en **Validar** para comprobar la configuración.

    e.  Haga clic en el botón **siguiente** para abrir la página de **confirmación** .

9.  En la página de **confirmación** , haga clic en **completado** para guardar la configuración.
##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-salesforce-sandbox-perform-the-following-steps"></a>Para asignar a los usuarios a espacio aislado de Salesforce, realice los pasos siguientes:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación de **Espacio aislado de Salesforce **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC769574.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC767830.png "Sí")
  
Ahora debe esperar 10 minutos y compruebe que la cuenta se ha sincronizado con recinto de seguridad de Salesforce.
  
Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](https://msdn.microsoft.com/library/dn308586).

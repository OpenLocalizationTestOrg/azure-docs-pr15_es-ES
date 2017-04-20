<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con UserVoice | Microsoft Azure" 
    description="Obtenga información sobre cómo usar UserVoice con Azure Active Directory para habilitar el inicio de sesión único, automatizado de aprovisionamiento y más." 
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
    ms.date="09/11/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-uservoice"></a>Tutorial: Integración de Azure Active Directory con UserVoice
  
El objetivo de este tutorial es mostrar la integración de Azure y UserVoice.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inquilino UserVoice
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a UserVoice será capaz de inicio de sesión único en la aplicación en el sitio de su empresa de UserVoice (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
El escenario descrito en este tutorial se compone de los siguientes bloques de creación:

1.  Habilitar la integración de aplicación para UserVoice
2.  Configuración de inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-uservoice-tutorial/IC777514.png "Escenario")

##<a name="enabling-the-application-integration-for-uservoice"></a>Habilitar la integración de aplicación para UserVoice
  
El objetivo de esta sección es describen cómo habilitar la integración de aplicación para UserVoice.

###<a name="to-enable-the-application-integration-for-uservoice-perform-the-following-steps"></a>Para habilitar la integración de aplicación para UserVoice, siga estos pasos:

1.  En el portal de clásico Azure, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-uservoice-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-uservoice-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-uservoice-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-uservoice-tutorial/IC749322.png "Agregar una aplicación de gallerry")

6.  En el **cuadro de búsqueda**, escriba **UserVoice**.

    ![Galería de aplicación] (./media/active-directory-saas-uservoice-tutorial/IC777513.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **UserVoice**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![UserVoice] (./media/active-directory-saas-uservoice-tutorial/IC777810.png "UserVoice")

##<a name="configuring-single-sign-on"></a>Configuración de inicio de sesión único
  
El objetivo de esta sección es describen cómo habilitar los usuarios autenticar a UserVoice con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
Configuración de inicio de sesión único para UserVoice requiere que recuperar un valor de la huella digital de un certificado.  
Si no está familiarizado con este procedimiento, consulte [cómo recuperar el valor de la huella digital de un certificado](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación **UserVoice** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar inicio de sesión único] (./media/active-directory-saas-uservoice-tutorial/IC777515.png "Configurar inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión UserVoice** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-uservoice-tutorial/IC777516.png "Configurar inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto **UserVoice inicio de sesión en la URL** , escriba la dirección URL con el siguiente patrón "https://*\<nombre del inquilino\>. UserVoice.com*"y, a continuación, haga clic en **siguiente**.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-uservoice-tutorial/IC777517.png "Configurar la dirección URL de la aplicación")

4.  En la página **configurar inicio de sesión único en UserVoice** , para descargar el certificado, haga clic en **Descargar certificado**y, a continuación, guarde el archivo de certificado localmente como **c:\\UserVoice.cer**.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-uservoice-tutorial/IC777518.png "Configurar inicio de sesión único")

5.  En una ventana de explorador web diferente, inicie sesión en el sitio de su empresa UserVoice como administrador.

6.  En la barra de herramientas en la parte superior, haga clic en configuración y, a continuación, seleccione portal Web en el menú.

    ![Configuración] (./media/active-directory-saas-uservoice-tutorial/IC777519.png "Configuración")

7.  En la pestaña **portal Web** , en la sección **autenticación de usuario** , haga clic en **Editar** para abrir la página de diálogo **Editar autenticación de usuario**

    ![Portal Web] (./media/active-directory-saas-uservoice-tutorial/IC777520.png "Portal Web")

8.  En la página de diálogo **Editar autenticación de usuario** , realice los pasos siguientes:

    ![Editar la autenticación de usuario] (./media/active-directory-saas-uservoice-tutorial/IC777521.png "Editar la autenticación de usuario")

    1.  Haga clic en **Inicio de sesión único (SSO)**.
    2.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en UserVoice** , copie el valor de **Dirección URL de inicio de sesión remota** y péguelo en el cuadro de texto de inicio de **Sesión remota SSO** .
    3.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en UserVoice** , copie el valor de **Dirección URL de cierre de sesión remota** y péguelo en el **cuadro de texto de SSO remoto Sign-Out**.
    4.  Copie el valor de la **huella digital** del certificado exportado y, a continuación, péguelo en el cuadro de texto **actual huella digital SHA1 de certificado** .  

        >[AZURE.TIP] Para obtener más detalles, consulte [cómo recuperar el valor de la huella digital de un certificado](http://youtu.be/YKQF266SAxI)

    5.  Haga clic en **Guardar la configuración de autenticación**.

9.  En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar inicio de sesión único] (./media/active-directory-saas-uservoice-tutorial/IC777522.png "Configurar inicio de sesión único")

##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD iniciar sesión en UserVoice, debe aprovisionar en UserVoice.  
En el caso de UserVoice, aprovisionamiento es una tarea manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para crear cuentas de usuario, siga estos pasos:

1.  Inicie sesión en su inquilino **UserVoice** .

2.  Vaya a **configuración**.

    ![Configuración] (./media/active-directory-saas-uservoice-tutorial/IC777811.png "Configuración")

3.  Haga clic en **General**.

4.  Haga clic en **permisos y agentes**.

    ![Agentes y permisos] (./media/active-directory-saas-uservoice-tutorial/IC777812.png "Agentes y permisos")

5.  Haga clic en **Agregar administradores**.

    ![Agregar administradores] (./media/active-directory-saas-uservoice-tutorial/IC777813.png "Agregar administradores")

6.  En el cuadro de diálogo **Invitar a los administradores** , realice los pasos siguientes:

    ![Invitar a los administradores] (./media/active-directory-saas-uservoice-tutorial/IC777814.png "Invitar a los administradores")

    1.  En el TextBox puede correos electrónicos, escriba la dirección de correo electrónico de la cuenta que desee aprovisionar y, a continuación, haga clic en **Agregar**.
    2.  Haga clic en **Invitar**.

>[AZURE.NOTE] Puede usar cualquier otros UserVoice usuario cuenta herramientas de creación o API proporcionan por UserVoice a cuentas de usuario AAD de aprovisionamiento.

##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-uservoice-perform-the-following-steps"></a>Para asignar a los usuarios a UserVoice, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación **UserVoice **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-uservoice-tutorial/IC777523.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-uservoice-tutorial/IC767830.png "Sí")
  
Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
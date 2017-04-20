<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con futuro | Microsoft Azure" 
    description="Obtenga información sobre cómo usar futuro con Azure Active Directory para habilitar el inicio de sesión único, automatizado de aprovisionamiento y más!." 
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
    ms.date="09/09/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-zendesk"></a>Tutorial: Integración de Azure Active Directory con futuro
  
El objetivo de este tutorial es mostrar la integración de Azure y futuro.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inquilino futuro
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a futuro podrán al inicio de sesión único en la aplicación en el sitio de su empresa de futuro (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
El escenario descrito en este tutorial se compone de los siguientes bloques de creación:

1.  Habilitar la integración de aplicaciones para el futuro
2.  Configuración de inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-zendesk-tutorial/IC773083.png "Escenario")

##<a name="enabling-the-application-integration-for-zendesk"></a>Habilitar la integración de aplicaciones para el futuro
  
El objetivo de esta sección es describen cómo habilitar la integración de aplicaciones para el futuro.

###<a name="to-enable-the-application-integration-for-zendesk-perform-the-following-steps"></a>Para habilitar la integración de aplicaciones para el futuro, realice los pasos siguientes:

1.  En el Portal de administración de Azure, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-zendesk-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-zendesk-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-zendesk-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-zendesk-tutorial/IC749322.png "Agregar una aplicación de gallerry")

6.  En el **cuadro de búsqueda**, escriba el **futuro**.

    ![Galería de aplicación] (./media/active-directory-saas-zendesk-tutorial/IC773084.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **futuro**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Futuro] (./media/active-directory-saas-zendesk-tutorial/IC773085.png "Futuro")

##<a name="configuring-single-sign-on"></a>Configuración de inicio de sesión único
  
El objetivo de esta sección es describen cómo habilitar los usuarios para autenticarse en el futuro con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
Configuración de inicio de sesión único para futuro requiere que recuperar un valor de la huella digital de un certificado.  
Si no está familiarizado con este procedimiento, consulte [cómo recuperar el valor de la huella digital de un certificado](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de Azure AD, en la página de integración de aplicación **futuro** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Inicio de sesión único] (./media/active-directory-saas-zendesk-tutorial/IC773086.png "Inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión el futuro** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-zendesk-tutorial/IC773087.png "Configurar inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , realice los pasos siguientes:

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-zendesk-tutorial/IC773088.png "Configurar la dirección URL de la aplicación")
  
    una. En el cuadro de texto **Futuro inicio de sesión en la URL** , escriba la dirección URL utilizando el modelo siguiente:`https://<tenant-name>.zendesk.com`

    b. Haga clic en **siguiente**.



4.  En la página **configurar inicio de sesión único en el futuro** , haga clic en **Descargar certificado**y, a continuación, guarde el archivo de certificado localmente en su compiter.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-zendesk-tutorial/IC777534.png "Configurar inicio de sesión único")

5.  En una ventana de explorador web diferente, inicie sesión en el sitio de su empresa futuro como administrador.

6.  Haga clic en **Administrador**.

7.  En el panel de navegación izquierdo, haga clic en **configuración**y, a continuación, haga clic en **seguridad**.

    ![Seguridad] (./media/active-directory-saas-zendesk-tutorial/IC773089.png "Seguridad")

8.  En la página de **seguridad** , haga clic en la pestaña **administración y agentes** .

9.  Seleccione **un solo inicio de sesión único (SSO) y SAML**y, a continuación, seleccione **SAML**.

10. En el portal de Azure AD, en la página **configurar inicio de sesión único en el futuro** , copie el valor de **Dirección URL de SAML SSO** y péguelo en el cuadro de texto **Dirección URL de SAML SSO** .

11. En el portal de Azure AD, en la página **configurar inicio de sesión único en el futuro** , copie el valor de **Dirección URL de cierre de sesión remota** y, a continuación, péguelo en el cuadro de texto **Dirección URL de cierre de sesión remota** .

    ![Inicio de sesión único] (./media/active-directory-saas-zendesk-tutorial/IC773090.png "Inicio de sesión único")

12. Copie el valor de la **huella digital** del certificado exportado y, a continuación, péguelo en el cuadro de texto de la **Huella digital de certificado** .

    >[AZURE.TIP] Para obtener más detalles, consulte [cómo recuperar el valor de la huella digital de un certificado](http://youtu.be/YKQF266SAxI)

13. Haga clic en **Guardar**.

14. En el portal de Azure AD, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar inicio de sesión único] (./media/active-directory-saas-zendesk-tutorial/IC773093.png "Configurar inicio de sesión único")

##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD iniciar sesión en el **futuro**, debe aprovisionar en el **futuro**.  
En el caso de **futuro**, aprovisionamiento es una tarea manual.

###<a name="to-provision-a-user-account-to-zendesk-perform-the-following-steps"></a>Para crear una cuenta de usuario en el futuro, realice los pasos siguientes:

1.  Inicie sesión en su inquilino **futuro** .

2.  Seleccione la pestaña **Lista de clientes** .

3.  Seleccione la ficha de **usuario** y haga clic en **Agregar**.

    ![Agregar usuario] (./media/active-directory-saas-zendesk-tutorial/IC773632.png "Agregar usuario")

4.  Escriba la dirección de correo electrónico de una cuenta de Azure AD existente que desee aprovisionar y, a continuación, haga clic en **Guardar**.

    ![Nuevo usuario] (./media/active-directory-saas-zendesk-tutorial/IC773633.png "Nuevo usuario")

>[AZURE.NOTE] Puede usar cualquier otros futuro usuario cuenta herramientas de creación o API proporcionan por futuro a cuentas de usuario AAD de aprovisionamiento.

##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-zendesk-perform-the-following-steps"></a>Para asignar a los usuarios a futuro, realice los pasos siguientes:

1.  En el portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de aplicación **futuro **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-zendesk-tutorial/IC773094.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-zendesk-tutorial/IC767830.png "Sí")
  
Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

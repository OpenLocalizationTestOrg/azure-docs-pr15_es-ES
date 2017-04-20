<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Freshdesk | Microsoft Azure" 
    description="Aprenda a usar Freshdesk con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más." 
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

#<a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Tutorial: Integración de Azure Active Directory con Freshdesk
  
El objetivo de este tutorial es mostrar la integración de Azure y Freshdesk.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inquilino Freshdesk
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Freshdesk será capaz de inicio de sesión único en la aplicación en el sitio de su empresa de Freshdesk (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
El escenario descrito en este tutorial consta de los siguientes elementos:

1.  Habilitar la integración de aplicación para Freshdesk
2.  Configurar el inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-freshdesk-tutorial/IC776761.png "Escenario")
##<a name="enabling-the-application-integration-for-freshdesk"></a>Habilitar la integración de aplicación para Freshdesk
  
El objetivo de esta sección es describen cómo habilitar la integración de aplicación para Freshdesk.

###<a name="to-enable-the-application-integration-for-freshdesk-perform-the-following-steps"></a>Para habilitar la integración de aplicación para Freshdesk, siga estos pasos:

1.  En el portal clásico Azure, en el panel de navegación de la izquierda, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-freshdesk-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-freshdesk-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-freshdesk-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-freshdesk-tutorial/IC749322.png "Agregar una aplicación de muestra")

6.  En el **cuadro de búsqueda**, escriba **Freshdesk**.

    ![Galería de aplicación] (./media/active-directory-saas-freshdesk-tutorial/IC776762.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **Freshdesk**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Freshdesk] (./media/active-directory-saas-freshdesk-tutorial/IC776763.png "Freshdesk")
##<a name="configuring-single-sign-on"></a>Configurar el inicio de sesión único
  
El objetivo de esta sección es describen cómo habilitar los usuarios autenticar a Freshdesk con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
Configuración de inicio de sesión único para Freshdesk requiere que recuperar un valor de la huella digital de un certificado.  
Si no está familiarizado con este procedimiento, consulte [cómo recuperar el valor de la huella digital de un certificado](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación **Freshdesk** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-freshdesk-tutorial/IC776764.png "Configurar el inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión Freshdesk** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-freshdesk-tutorial/IC776765.png "Configurar el inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto **Freshdesk inicio de sesión en la URL** , escriba la dirección URL con el siguiente patrón "https://*\<nombre del inquilino\>. Freshdesk.com*"y, a continuación, haga clic en **siguiente**.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-freshdesk-tutorial/IC776766.png "Configurar la dirección URL de la aplicación")

4.  En la página **configurar inicio de sesión único en Freshdesk** , para descargar el certificado, haga clic en **Descargar certificado**y, a continuación, guarde el archivo de certificado localmente como **c:\\Freshdesk.cer**.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-freshdesk-tutorial/IC776767.png "Configurar el inicio de sesión único")

5.  En una ventana de explorador web diferente, inicie sesión en el sitio de su empresa Freshdesk como administrador.

6.  En el menú de la parte superior, haga clic en **Administrador**.

    ![Administrador] (./media/active-directory-saas-freshdesk-tutorial/IC776768.png "Administrador")

7.  En la pestaña **Configuración General** , haga clic en **seguridad**.

    ![Seguridad] (./media/active-directory-saas-freshdesk-tutorial/IC776769.png "Seguridad")

8.  En la sección **seguridad** , siga estos pasos:

    ![Inicio de sesión único] (./media/active-directory-saas-freshdesk-tutorial/IC776770.png "Inicio de sesión único")

    1.  ** **Inicio de sesión único (SSO)**, seleccione.**
    2.  Seleccione **SAML SSO**.
    3.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en Freshdesk** , copie el valor de **Dirección URL de inicio de sesión remota** y, a continuación, péguelo en el cuadro de texto **Dirección URL de inicio de sesión de SAML** .
    4.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en Freshdesk** , copie el valor de **Dirección URL de cierre de sesión remota** y, a continuación, péguelo en el cuadro de texto **Dirección URL de cierre de sesión** .
    5.  Copie el valor de la **huella digital** del certificado exportado y, a continuación, péguelo en el cuadro de texto de la **Huella digital de certificado de seguridad** .  

        >[AZURE.TIP]Para obtener más detalles, consulte [cómo recuperar el valor de la huella digital de un certificado](http://youtu.be/YKQF266SAxI)

    6.  Haga clic en **Guardar**.

9.  En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-freshdesk-tutorial/IC776771.png "Configurar el inicio de sesión único")
##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD iniciar sesión en Freshdesk, debe aprovisionar en Freshdesk.  
En el caso de Freshdesk, aprovisionamiento es una tarea manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para crear cuentas de usuario, siga estos pasos:

1.  Inicie sesión en su inquilino **Freshdesk** .

2.  En el menú de la parte superior, haga clic en **Administrador**.

    ![Administrador] (./media/active-directory-saas-freshdesk-tutorial/IC776772.png "Administrador")

3.  En la pestaña **Configuración General** , haga clic en **agentes**.

    ![Agentes] (./media/active-directory-saas-freshdesk-tutorial/IC776773.png "Agentes")

4.  Haga clic en **nuevo agente**.

    ![Nuevo agente] (./media/active-directory-saas-freshdesk-tutorial/IC776774.png "Nuevo agente")

5.  En el cuadro de diálogo información de agente, realice los pasos siguientes:

    ![Información de agente] (./media/active-directory-saas-freshdesk-tutorial/IC776775.png "Información de agente")

    1.  En el cuadro de texto **Nombre completo** , escriba el nombre de la cuenta de Azure AD que desee aprovisionar.
    2.  En el cuadro de texto de **correo electrónico** , escriba la dirección de correo electrónico de Azure AD de la cuenta de Azure AD que desee aprovisionar.
    3.  En el cuadro de texto **título** , escriba el título de la cuenta de Azure AD que desee aprovisionar.
    4.  Seleccione **el rol de agentes**y, a continuación, haga clic en **asignar**.
    5.  Haga clic en **Guardar**.
    
        >[AZURE.NOTE] Titular de la cuenta de Azure AD recibirá un correo electrónico que incluye un vínculo para confirmar la cuenta antes de que se activa.

>[AZURE.NOTE] Puede usar cualquier otros Freshdesk usuario cuenta herramientas de creación o API proporcionan por Freshdesk a cuentas de usuario AAD de aprovisionamiento.

##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-freshdesk-perform-the-following-steps"></a>Para asignar a los usuarios a Freshdesk, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación **Freshdesk **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-freshdesk-tutorial/IC776776.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-freshdesk-tutorial/IC767830.png "Sí")
  
Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Onit | Microsoft Azure" 
    description="Aprenda a usar Onit con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más." 
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

#<a name="tutorial-azure-active-directory-integration-with-onit"></a>Tutorial: Integración de Azure Active Directory con Onit
  
El objetivo de este tutorial es mostrar la integración de Azure y Onit.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inicio de sesión único de Onit activado suscripción
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Onit será capaz de inicio de sesión único en la aplicación en el sitio de su empresa de Onit (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
El escenario descrito en este tutorial consta de los siguientes elementos:

1.  Habilitar la integración de aplicación para Onit
2.  Configurar el inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-onit-tutorial/IC791166.png "Escenario")
##<a name="enabling-the-application-integration-for-onit"></a>Habilitar la integración de aplicación para Onit
  
El objetivo de esta sección es describen cómo habilitar la integración de aplicación para Onit.

###<a name="to-enable-the-application-integration-for-onit-perform-the-following-steps"></a>Para habilitar la integración de aplicación para Onit, siga estos pasos:

1.  En el portal clásico Azure, en el panel de navegación de la izquierda, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-onit-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-onit-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-onit-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-onit-tutorial/IC749322.png "Agregar una aplicación de muestra")

6.  En el **cuadro de búsqueda**, escriba **Onit**.

    ![Galería de aplicación] (./media/active-directory-saas-onit-tutorial/IC791167.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **Onit**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Onit] (./media/active-directory-saas-onit-tutorial/IC795325.png "Onit")
##<a name="configuring-single-sign-on"></a>Configurar el inicio de sesión único
  
El objetivo de esta sección es describen cómo habilitar los usuarios autenticar a Onit con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
Configuración de inicio de sesión único para Onit requiere que recuperar un valor de la huella digital de un certificado.  
Si no está familiarizado con este procedimiento, consulte [cómo recuperar el valor de la huella digital de un certificado](http://youtu.be/YKQF266SAxI).
  
La aplicación de Onit espera las afirmaciones SAML en un formato específico, lo que requiere que agregue asignaciones de atributos personalizados a la configuración de **atributos de token saml** .  
La siguiente captura de pantalla muestra un ejemplo de este.

![Inicio de sesión único] (./media/active-directory-saas-onit-tutorial/IC791168.png "Inicio de sesión único")

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación **Onit** , en el menú de la parte superior, haga clic en **atributos** para abrir el cuadro de diálogo **Atributos de Token SAML** .

    ![Atributos] (./media/active-directory-saas-onit-tutorial/IC791169.png "Atributos")

2.  Para agregar las asignaciones de atributo obligatorio, realice los pasos siguientes:

    
  	|Nombre del atributo|Valor del atributo|
  	|---|---|
  	|nombre|User.userPrincipalName|
  	|Correo electrónico|User.Mail|

    1.  Para cada fila de datos de la tabla anterior, haga clic en **Agregar usuario atributo**.
    2.  En el cuadro de texto **Nombre de atributo** , escriba el nombre del atributo que se muestra de la fila.
    3.  En la lista **Valor del atributo** , seleccione el valor del atributo que se muestra de la fila.
    4.  Haga clic en **Finalizar**.

3.  Haga clic en **Aplicar cambios**.

4.  En el explorador, haga clic en **Atrás** para volver a abrir el cuadro de diálogo de **Inicio rápido** .

5.  Haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-onit-tutorial/IC791170.png "Configurar el inicio de sesión único")

6.  En la página **¿cómo desea que los usuarios para iniciar sesión Onit** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-onit-tutorial/IC791171.png "Configurar el inicio de sesión único")

7.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto **Onit inicio de sesión en la URL** , escriba la dirección URL utilizada por los usuarios para iniciar sesión la aplicación Onit (por ejemplo: "*https://ms-sso-test.onit.com*") y, a continuación, haga clic en **siguiente**.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-onit-tutorial/IC791172.png "Configurar la dirección URL de la aplicación")

8.  En la página **configurar inicio de sesión único en Onit** , para descargar el certificado, haga clic en **Descargar certificado**y, a continuación, guarde el archivo de certificado localmente en el equipo.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-onit-tutorial/IC791173.png "Configurar el inicio de sesión único")

9.  En una ventana de explorador web diferente, inicie sesión en el sitio de su empresa Onit como administrador.

10. En el menú de la parte superior, haga clic en **administración**.

    ![Administración] (./media/active-directory-saas-onit-tutorial/IC791174.png "Administración")

11. Haga clic en **Editar Corporation**.

    ![Editar Corporation] (./media/active-directory-saas-onit-tutorial/IC791175.png "Editar Corporation")

12. Haga clic en la pestaña **seguridad** .

    ![Editar info.] (./media/active-directory-saas-onit-tutorial/IC791176.png "Editar info.")

13. En la pestaña **seguridad** , siga estos pasos:

    ![Inicio de sesión único] (./media/active-directory-saas-onit-tutorial/IC791177.png "Inicio de sesión único")

    1.  Como **Estrategia de autenticación**, seleccione **Inicio de sesión único y una contraseña**.
    2.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en Onit** , copie el valor de **Dirección URL de inicio de sesión remota** y péguelo en el cuadro de texto **Dirección URL de destino Idp** .
    3.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en Onit** , copie el valor de **Dirección URL de cierre de sesión remota** y péguelo en el cuadro de texto **dirección URL de cierre de sesión de Idp** .
    4.  Copie el valor de la **huella digital** del certificado exportado y, a continuación, péguelo en el cuadro de texto de la **Huella digital de certificado de Idp (SHA1)** .  

        >[AZURE.TIP] Para obtener más detalles, consulte [cómo recuperar el valor de la huella digital de un certificado](http://youtu.be/YKQF266SAxI)

    5.  Como **Tipo de SSO**, seleccione **SAML**.
    6.  En el cuadro de texto del **texto del botón de inicio de sesión SSO** , escriba un texto de botón que le guste.
    7.  Seleccione **Inicio de sesión con SSO: necesario para los siguientes dominios o usuarios**, escriba la dirección de correo electrónico de un usuario de prueba en el cuadro de texto relacionado y, a continuación, haga clic en **Actualizar**.
        ![Editar Corporation] (./media/active-directory-saas-onit-tutorial/IC791178.png "Editar Corporation")

14. En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-onit-tutorial/IC791179.png "Configurar el inicio de sesión único")
##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD iniciar sesión en Onit, debe aprovisionar en Onit.  
En el caso de Onit, aprovisionamiento es una tarea manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar el aprovisionamiento de usuario, siga estos pasos:

1.  Inicie sesión como administrador el sitio de su empresa **Onit** .

2.  Haga clic en **Agregar usuario**.

    ![Administración] (./media/active-directory-saas-onit-tutorial/IC791180.png "Administración")

3.  En la página de diálogo **Agregar usuario** , realice los pasos siguientes:

    ![Agregar usuario] (./media/active-directory-saas-onit-tutorial/IC791181.png "Agregar usuario")

    1.  Escriba el **nombre** y la **Dirección de correo electrónico** de una cuenta AAD válida que desee aprovisionar en los cuadros de texto relacionados.
    2.  Haga clic en **crear**.  

        >[AZURE.NOTE] El propietario de la cuenta recibirá un correo electrónico que incluye un vínculo para confirmar la cuenta antes de que esté activa.

>[AZURE.NOTE] Puede usar cualquier otros Onit usuario cuenta herramientas de creación o API proporcionan por Onit a cuentas de usuario AAD de aprovisionamiento.

##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-onit-perform-the-following-steps"></a>Para asignar a los usuarios a Onit, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación **Onit **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-onit-tutorial/IC791182.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-onit-tutorial/IC767830.png "Sí")
  
Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
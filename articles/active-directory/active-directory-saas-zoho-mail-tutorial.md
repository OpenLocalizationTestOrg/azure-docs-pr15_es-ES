<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con correo Zoho | Microsoft Azure" 
    description="Aprenda a usar correo de Zoho con Azure Active Directory para habilitar el inicio de sesión único, automatizado de aprovisionamiento y más!." 
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
    ms.author="markvi" />

#<a name="tutorial-azure-active-directory-integration-with-zoho-mail"></a>Tutorial: Integración de Azure Active Directory con correo Zoho
  
El objetivo de este tutorial es mostrar la integración de Azure y Zoho correo.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inquilino Zoho correo
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a correo Zoho podrán al inicio de sesión único en la aplicación en el sitio de su empresa de correo Zoho (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
El escenario descrito en este tutorial se compone de los siguientes bloques de creación:

1.  Habilitar la integración de la aplicación para el correo Zoho
2.  Configuración de inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-zoho-mail-tutorial/IC789600.png "Escenario")

##<a name="enabling-the-application-integration-for-zoho-mail"></a>Habilitar la integración de la aplicación para el correo Zoho
  
El objetivo de esta sección es describen cómo habilitar la integración de aplicación de correo de Zoho.

###<a name="to-enable-the-application-integration-for-zoho-mail-perform-the-following-steps"></a>Para habilitar la integración de las aplicaciones de correo Zoho, siga estos pasos:

1.  En el portal de clásico Azure, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-zoho-mail-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-zoho-mail-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-zoho-mail-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-zoho-mail-tutorial/IC749322.png "Agregar una aplicación de gallerry")

6.  En el **cuadro de búsqueda**, escriba **Correo Zoho**.

    ![Galería de aplicación] (./media/active-directory-saas-zoho-mail-tutorial/IC789601.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **Correo Zoho**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Correo Zoho] (./media/active-directory-saas-zoho-mail-tutorial/IC789602.png "Correo Zoho")

##<a name="configuring-single-sign-on"></a>Configuración de inicio de sesión único
  
El objetivo de esta sección es describen cómo habilitar los usuarios autenticar al correo de Zoho con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
Como parte de este procedimiento, es necesario para crear un archivo de certificado codificado de base 64.  
Si no está familiarizado con este procedimiento, vea [cómo convertir un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación de **Correo de Zoho** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-zoho-mail-tutorial/IC789603.png "Configurar el inicio de sesión único")

2.  En la página **¿cómo le gustaría usuarios a iniciar sesión correo Zoho** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-zoho-mail-tutorial/IC789604.png "Configurar el inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , realice los pasos siguientes:

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-zoho-mail-tutorial/IC789605.png "Configurar la dirección URL de la aplicación")

    una. En el cuadro de texto **Zoho correo de inicio de sesión en dirección URL** , escriba la dirección URL utilizando el modelo siguiente:`http://<company name>.ZohoMail.com`

    b. Haga clic en **siguiente**.


4.  En la página **configurar inicio de sesión único en Zoho correo** , haga clic en **Descargar certificado**y, a continuación, guarde el archivo de certificado en el equipo.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-zoho-mail-tutorial/IC789606.png "Configurar el inicio de sesión único")

5.  En una ventana de explorador web diferente, inicie sesión en el sitio de su empresa Zoho correo como administrador.

6.  Vaya al **panel de Control**.

    ![Panel de control] (./media/active-directory-saas-zoho-mail-tutorial/IC789607.png "Panel de control")

7.  Haga clic en la ficha **Autenticación SAML** .

    ![Autenticación de SAML] (./media/active-directory-saas-zoho-mail-tutorial/IC789608.png "Autenticación de SAML")

8.  En la sección de **Detalles de la autenticación de SAML** , realice los pasos siguientes:

    ![Detalles de la autenticación de SAML] (./media/active-directory-saas-zoho-mail-tutorial/IC789609.png "Detalles de la autenticación de SAML")

    1.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en correo Zoho** , copie el valor de **Dirección URL de inicio de sesión remota** y péguelo en el cuadro de texto **Dirección URL de inicio de sesión** .
    2.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en correo Zoho** , copie el valor de **Dirección URL de cierre de sesión remota** y péguelo en el cuadro de texto **Dirección URL de cierre de sesión** .
    3.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en Zoho correo** , copie el valor de **Dirección URL de cambiar contraseña** y, a continuación, péguelo en el cuadro de texto **Cambiar contraseña dirección URL** .
    4.  Crear un archivo **codificado base 64** de certificado descargado.  

        >[AZURE.TIP] Para obtener más detalles, consulte [cómo convertir un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    5.  Abra el certificado codificado de base 64 en el Bloc de notas, copie el contenido de la misma en el Portapapeles y, a continuación, péguela en el cuadro de texto **PublicKey** .
    6.  **Algoritmo de**, seleccione **RSA**.
    7.  Haga clic en **Aceptar**.

9.  En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-zoho-mail-tutorial/IC789610.png "Configurar el inicio de sesión único")

##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD iniciar sesión en correo Zoho, debe aprovisionar a Zoho correo.  
En el caso de correo Zoho, aprovisionamiento es una tarea manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para crear cuentas de usuario, siga estos pasos:

1.  Inicie sesión como administrador en el sitio de su empresa **Zoho correo** .

2.  Vaya a **Panel de Control \> correo y documentos**.

3.  Vaya a **Detalles del usuario \> Agregar usuario**.

    ![Agregar usuario] (./media/active-directory-saas-zoho-mail-tutorial/IC789611.png "Agregar usuario")

4.  En el cuadro de diálogo **Agregar usuarios** , realice los pasos siguientes:

    ![Agregar usuario] (./media/active-directory-saas-zoho-mail-tutorial/IC789612.png "Agregar usuario")

    1.  Escriba el **nombre**, **Apellido**, **ID de correo electrónico**, la **contraseña** de una cuenta de Azure Active Directory válida que desee aprovisionar en los cuadros de texto relacionados.
    2.  Haga clic en **Aceptar**.  

        >[AZURE.NOTE] Titular de la cuenta de Azure Active Directory recibirán un correo electrónico con un vínculo para confirmar la cuenta antes de que esté activa.

>[AZURE.NOTE] Puede usar cualquier otros correo Zoho usuario cuenta herramientas de creación o API proporcionan por correo Zoho a cuentas de usuario AAD de aprovisionamiento.

##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-zoho-mail-perform-the-following-steps"></a>Para asignar a los usuarios a Zoho correo, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación de **Correo de Zoho **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-zoho-mail-tutorial/IC789613.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-zoho-mail-tutorial/IC767830.png "Sí")
  
Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
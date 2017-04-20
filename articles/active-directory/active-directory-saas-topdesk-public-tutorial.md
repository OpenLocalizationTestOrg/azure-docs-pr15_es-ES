<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con TOPdesk - público | Microsoft Azure" 
    description="Aprenda a usar TOPdesk - público con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y más." 
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

#<a name="tutorial-azure-directory-integration-with-topdesk---public"></a>Tutorial: Integración de directorios Azure con TOPdesk - público

El objetivo de este tutorial es mostrar la integración de Azure y TOPdesk - público.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un TOPdesk - público inicio de sesión único habilitado suscripción
  
Después de completar este tutorial, los usuarios de Azure AD ha asignado a TOPdesk - público será capaz de inicio de sesión único en la aplicación en su TOPdesk - sitio de empresa pública (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
El escenario descrito en este tutorial se compone de los siguientes bloques de creación:

1.  Habilitar la integración de aplicación para TOPdesk - público
2.  Configuración de inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-topdesk-public-tutorial/IC790613.png "Escenario")

##<a name="enabling-the-application-integration-for-topdesk---public"></a>Habilitar la integración de aplicación para TOPdesk - público
  
El objetivo de esta sección es describen cómo habilitar la integración de aplicación para TOPdesk - público.

###<a name="to-enable-the-application-integration-for-topdesk---public-perform-the-following-steps"></a>Para habilitar la integración de aplicación para TOPdesk - pública, realice los pasos siguientes:

1.  En el portal de clásico Azure, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-topdesk-public-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-topdesk-public-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-topdesk-public-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-topdesk-public-tutorial/IC749322.png "Agregar una aplicación de gallerry")

6.  En el **cuadro de búsqueda**, escriba **TOPdesk - público**.

    ![Galería de aplicación] (./media/active-directory-saas-topdesk-public-tutorial/IC790614.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **TOPdesk - público**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![TOPdesk público] (./media/active-directory-saas-topdesk-public-tutorial/IC791317.png "TOPdesk público")

##<a name="configuring-single-sign-on"></a>Configuración de inicio de sesión único
  
El objetivo de esta sección es describen cómo habilitar los usuarios autenticar TOPdesk - público con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
Configuración de inicio de sesión único para TOPdesk - público debe cargar un archivo de icono de logotipo. Para obtener el archivo de icono, póngase en contacto con el equipo de soporte técnico de TOPdesk.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  Inicie sesión como administrador el sitio de su empresa **TOPdesk - público** .

2.  En el menú **TOPdesk** , haga clic en **configuración**.

    ![Configuración] (./media/active-directory-saas-topdesk-public-tutorial/IC790598.png "Configuración")

3.  Haga clic en **configuración de inicio de sesión**.

    ![Configuración de inicio de sesión] (./media/active-directory-saas-topdesk-public-tutorial/IC790599.png "Configuración de inicio de sesión")

4.  Expandir el menú de **Configuración de inicio de sesión** y, a continuación, haga clic en **General**.

    ![General] (./media/active-directory-saas-topdesk-public-tutorial/IC790600.png "General")

5.  En la sección **pública** de la sección de configuración de **Inicio de sesión SAML** , realice los pasos siguientes:

    ![Configuración técnica] (./media/active-directory-saas-topdesk-public-tutorial/IC790601.png "Configuración técnica")

    1.  Haga clic en **Descargar** para descargar el archivo de metadatos pública y guardarlo localmente en el equipo.
    2.  Abra el archivo de metadatos y, a continuación, busque el nodo **AssertionConsumerService** .
        ![AssertionConsumerService] (./media/active-directory-saas-topdesk-public-tutorial/IC790619.png "AssertionConsumerService")
    3.  Copie el valor de **AssertionConsumerService** .  

        >[AZURE.NOTE] El valor de la sección **Configurar la dirección URL de aplicación** necesitará más adelante en este tutorial.

6.  En una ventana de explorador web diferente, inicie sesión en su **portal clásica Azure** como administrador.

7.  En la página de integración de aplicación **TOPdesk - público** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-topdesk-public-tutorial/IC790620.png "Configurar el inicio de sesión único")

8.  En la página **¿cómo desea que los usuarios para iniciar sesión TOPdesk - público** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-topdesk-public-tutorial/IC790621.png "Configurar el inicio de sesión único")

9.  En la página **Configurar la dirección URL de aplicación** , realice los pasos siguientes:

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-topdesk-public-tutorial/IC790622.png "Configurar la dirección URL de la aplicación")

    1.  En el cuadro de texto **TOPdesk - inicio de sesión en dirección URL pública** , escriba la dirección URL utilizada por los usuarios para iniciar sesión en su TOPdesk - aplicación público (por ejemplo: "*https://qssolutions.topdesk.net*").
    2.  En el cuadro de texto **TOPdesk: dirección URL pública de respuesta** , pegue la **TOPdesk - dirección URL pública de AssertionConsumerService** (por ejemplo: "*https://qssolutions.topdesk.net/tas/public/login/saml*")
    3.  Haga clic en **siguiente**.

10. En la página **configurar inicio de sesión único en TOPdesk - público** , haga clic en **descargar metadatos**para descargar el archivo de metadatos y, a continuación, guarde el archivo localmente en el equipo.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-topdesk-public-tutorial/IC790623.png "Configurar el inicio de sesión único")

11. Para crear un archivo de certificado, siga estos pasos:

    ![Certificado] (./media/active-directory-saas-topdesk-public-tutorial/IC790606.png "Certificado")

    1.  Abra el archivo descargado metadatos.
    2.  Expanda el nodo **RoleDescriptor** que tiene un **xsi: Type** de **introduciendo: ApplicationServiceType**.
    3.  Copie el valor del nodo **X509Certificate** .
    4.  Guardar el valor de **X509Certificate** copiado localmente en el equipo en un archivo.

12. En el TOPdesk - sitio de empresa pública, en el menú **TOPdesk** , haga clic en **configuración**.

    ![Configuración] (./media/active-directory-saas-topdesk-public-tutorial/IC790598.png "Configuración")

13. Haga clic en **configuración de inicio de sesión**.

    ![Configuración de inicio de sesión] (./media/active-directory-saas-topdesk-public-tutorial/IC790599.png "Configuración de inicio de sesión")

14. Expandir el menú de **Configuración de inicio de sesión** y, a continuación, haga clic en **General**.

    ![General] (./media/active-directory-saas-topdesk-public-tutorial/IC790600.png "General")

15. En la sección **público** , haga clic en **Agregar**.

    ![Inicio de sesión SAML] (./media/active-directory-saas-topdesk-public-tutorial/IC790625.png "Inicio de sesión SAML")

16. En la página de diálogo **Asistente de configuración de SAML** , realice los pasos siguientes:

    ![Asistente de configuración de SAML] (./media/active-directory-saas-topdesk-public-tutorial/IC790608.png "Asistente de configuración de SAML")

    1.  Para cargar el archivo descargado metadatos, en **Los metadatos de federación**, haga clic en **Examinar**.
    2.  Para cargar el archivo de certificado, bajo **Certificado (RSA)**, haga clic en **Examinar**.
    3.  Para cargar el archivo del logotipo que obtuvo desde el equipo de soporte técnico de TOPdesk, en el **icono del logotipo**, haga clic en **Examinar**.
    4.  En el cuadro de texto de **atributo de nombre de usuario** , escriba **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
    5.  En el cuadro de texto **nombre para mostrar** , escriba un nombre para la configuración.
    6.  Haga clic en **Guardar**.

17. En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-topdesk-public-tutorial/IC790627.png "Configurar el inicio de sesión único")

##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD iniciar sesión en TOPdesk - pública, debe aprovisionar en TOPdesk - público.  
En el caso de TOPdesk - pública, aprovisionamiento es una tarea manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar el aprovisionamiento de usuario, siga estos pasos:

1.  Inicie sesión como administrador el sitio de su empresa **TOPdesk - público** .

2.  En el menú de la parte superior, haga clic en **TOPdesk \> nuevo \> archivos de soporte técnico \> persona**.

    ![Persona] (./media/active-directory-saas-topdesk-public-tutorial/IC790628.png "Persona")

3.  En el cuadro de diálogo nueva persona, realice los pasos siguientes:

    ![Nueva persona] (./media/active-directory-saas-topdesk-public-tutorial/IC790629.png "Nueva persona")

    1.  Haga clic en la pestaña General.
    2.  En el cuadro de texto Apellido, escriba el apellido de una cuenta de Azure Active Directory válida que desee aprovisionar.
    3.  Seleccione un **sitio** para la cuenta.
    4.  Haga clic en **Guardar**.

>[AZURE.NOTE] Puede usar cualquier otro TOPdesk - herramientas de creación de cuenta de usuario público o API proporcionan por TOPdesk - público a cuentas de usuario AAD de aprovisionamiento.

##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-topdesk---public-perform-the-following-steps"></a>Para asignar los usuarios a TOPdesk - pública, realice los pasos siguientes:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación **TOPdesk - público **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-topdesk-public-tutorial/IC790630.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-topdesk-public-tutorial/IC767830.png "Sí")
  
Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
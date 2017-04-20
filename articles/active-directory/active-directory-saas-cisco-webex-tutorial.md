<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Cisco Webex | Microsoft Azure" 
    description="¡Obtenga información sobre cómo utilizar Cisco Webex con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más!" 
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

#<a name="tutorial-azure-active-directory-integration-with-cisco-webex"></a>Tutorial: Integración de Azure Active Directory con Cisco Webex

El objetivo de este tutorial es mostrar la integración de Azure y Cisco Webex.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inquilino Cisco Webex

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Cisco Webex será capaz de inicio de sesión único en la aplicación en el sitio de su compañía de Cisco Webex (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

El escenario descrito en este tutorial consta de los siguientes elementos:

1.  Habilitar la integración de la aplicación para Cisco Webex
2.  Configurar el inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-cisco-webex-tutorial/IC777614.png "Escenario")
##<a name="enabling-the-application-integration-for-cisco-webex"></a>Habilitar la integración de la aplicación para Cisco Webex

El objetivo de esta sección es describen cómo habilitar la integración de la aplicación para Cisco Webex.

###<a name="to-enable-the-application-integration-for-cisco-webex-perform-the-following-steps"></a>Para habilitar la integración de la aplicación para Cisco Webex, realice los pasos siguientes:

1.  En el portal clásico Azure, en el panel de navegación de la izquierda, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-cisco-webex-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-cisco-webex-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-cisco-webex-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-cisco-webex-tutorial/IC749322.png "Agregar una aplicación de muestra")

6.  En el **cuadro de búsqueda**, escriba **Cisco Webex**.

    ![Galería de aplicación] (./media/active-directory-saas-cisco-webex-tutorial/IC777615.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **Cisco Webex**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Cisco Webex] (./media/active-directory-saas-cisco-webex-tutorial/IC777616.png "Cisco Webex")
##<a name="configuring-single-sign-on"></a>Configurar el inicio de sesión único

El objetivo de esta sección es describen cómo habilitar los usuarios autenticar Cisco Webex con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
Como parte de este procedimiento, es necesario para crear un certificado de codificación de base 64.  
Si no está familiarizado con este procedimiento, vea [cómo convertir un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación **Cisco Webex** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar inicio de sesión único] (./media/active-directory-saas-cisco-webex-tutorial/IC777617.png "Configurar inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión Cisco Webex** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-cisco-webex-tutorial/IC777618.png "Configurar inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , realice los pasos siguientes y, a continuación, haga clic en **siguiente**.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-cisco-webex-tutorial/IC777619.png "Configurar la dirección URL de la aplicación")

    1.  En el cuadro de texto **Destaque en dirección URL** , escriba la dirección URL de inquilinos de Cisco Webex (por ejemplo: *http://contoso.webex.com*).
    2.  En el cuadro de texto **Dirección URL de respuesta de Cisco Webex** , escriba la **Dirección URL de Cisco Webex AssertionConsumerService** (por ejemplo: *https://company.webex.com/dispatcher/SAML2AuthService?siteurl=company*).

4.  En la página **configurar inicio de sesión único en Cisco Webex** , para descargar el certificado, haga clic en **Descargar certificado**y, a continuación, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-cisco-webex-tutorial/IC777620.png "Configurar inicio de sesión único")

5.  En una ventana de explorador web diferente, inicie sesión en el sitio de su empresa Cisco Webex como administrador.

6.  En el menú de la parte superior, haga clic en **Administración del sitio**.

    ![Administración de sitios] (./media/active-directory-saas-cisco-webex-tutorial/IC777621.png "Administración de sitios")

7.  En la sección **Administración del sitio** , haga clic en **Configuración de SSO**.

    ![Configuración de SSO] (./media/active-directory-saas-cisco-webex-tutorial/IC777622.png "Configuración de SSO")

8.  En la sección Configuración de SSO Web federado, realice los pasos siguientes:

    ![Configuración de SSO de federado] (./media/active-directory-saas-cisco-webex-tutorial/IC777623.png "Configuración de SSO de federado")

    1.  En la lista **Protocolo de federación** , seleccione **SAML 2.0**.
    2.  Crear un archivo **codificado Base 64** de certificado descargado.  

        >[AZURE.TIP] Para obtener más detalles, consulte [cómo convertir un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    3.  Abrir el certificado codificado de base 64 en el Bloc de notas y, a continuación, copie el contenido de la misma.
    4.  Haga clic en **Importar SAML metadatos**y, a continuación, pegue el certificado codificado de base 64.
    5.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en Cisco Webex** , copie el valor de **Dirección URL del emisor** y péguelo en el cuadro de texto del **emisor para SAML (IdP ID)** .
    6.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en Cisco Webex** , copie el valor de **Dirección URL de inicio de sesión remota** y, a continuación, péguelo en el cuadro de texto **Dirección URL de inicio de sesión del servicio de SSO de cliente** .
    7.  En la lista **Formato NameID** , seleccione **dirección de correo electrónico**.
    8.  En el cuadro de texto **AuthnContextClassRef** , escriba **Urn: oasis: nombres: tc: SAML:2.0:ac:classes:Password**.
    9.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en Cisco Webex** , copie el valor de **Dirección URL de cierre de sesión remota** y, a continuación, péguelo en el cuadro de texto **Dirección URL de cierre de sesión del servicio de SSO de cliente** .
    10. Haga clic en **Actualizar**.

9.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en Cisco Webex** , seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado**.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-cisco-webex-tutorial/IC777624.png "Configurar inicio de sesión único")
##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD iniciar sesión en Cisco Webex, debe aprovisionar en Cisco Webex.  
En el caso de Cisco Webex, aprovisionamiento es una tarea manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para crear cuentas de usuario, siga estos pasos:

1.  Inicie sesión en su inquilino **Cisco Webex** .

2.  Vaya a **Administrar usuarios \> Agregar usuario**.

    ![Agregar usuarios] (./media/active-directory-saas-cisco-webex-tutorial/IC777625.png "Agregar usuarios")

3.  En la sección Agregar usuario, siga estos pasos:

    ![Agregar usuario] (./media/active-directory-saas-cisco-webex-tutorial/IC777626.png "Agregar usuario")

    1.  Como **Tipo de cuenta**, seleccione **Host**.
    2.  Escriba la información de un usuario existente de Azure AD en los cuadros de texto siguientes: **nombre, apellido**, **nombre de usuario**, **correo electrónico**, **contraseña**, **Confirmar contraseña**.
    3.  Haga clic en **Agregar**.

>[AZURE.NOTE] Puede usar cualquier otros Cisco Webex usuario cuenta herramientas de creación o API proporcionan por Cisco Webex a cuentas de usuario AAD de aprovisionamiento.

##<a name="assigning-users"></a>Asignación de usuarios

Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-cisco-webex-perform-the-following-steps"></a>Para asignar a los usuarios a Cisco Webex, realice los pasos siguientes:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación **Cisco Webex **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-cisco-webex-tutorial/IC777627.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-cisco-webex-tutorial/IC767830.png "Sí")

Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

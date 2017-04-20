<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con escritorio Central | Microsoft Azure" 
    description="¡Obtenga información sobre cómo utilizar Escritorio Central con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más!" 
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

#<a name="tutorial-azure-active-directory-integration-with-central-desktop"></a>Tutorial: Integración de Azure Active Directory con escritorio Central

El objetivo de este tutorial es mostrar la integración de Azure y escritorio Central. El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inicio de sesión único escritorio Central de suscripción habilitado / Escritorio Central de inquilinos

El escenario descrito en este tutorial consta de los siguientes elementos:

1.  Habilitar la integración de las aplicaciones de escritorio Central
2.  Configurar el inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-central-desktop-tutorial/IC769558.png "Escenario")
##<a name="enabling-the-application-integration-for-central-desktop"></a>Habilitar la integración de las aplicaciones de escritorio Central

El objetivo de esta sección es describen cómo habilitar la integración de las aplicaciones de escritorio Central.

###<a name="to-enable-the-application-integration-for-central-desktop-perform-the-following-steps"></a>Para habilitar la integración de las aplicaciones de escritorio Central, realice los pasos siguientes:

1.  En el portal clásico Azure, en el panel de navegación de la izquierda, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-central-desktop-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-central-desktop-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-central-desktop-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-central-desktop-tutorial/IC749322.png "Agregar una aplicación de muestra")

6.  En el **cuadro de búsqueda**, escriba **Escritorio Central**.

    ![Galería de aplicación] (./media/active-directory-saas-central-desktop-tutorial/IC769559.png "Galería de aplicación")

7.  En el panel de resultados, haga clic en **Escritorio Central**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Escritorio central] (./media/active-directory-saas-central-desktop-tutorial/IC769560.png "Escritorio central")
##<a name="configuring-single-sign-on"></a>Configurar el inicio de sesión único

El objetivo de esta sección es describen cómo habilitar los usuarios autenticar al escritorio Central con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
Como parte de este procedimiento, es necesario para cargar un certificado codificado de base 64 en el inquilino de escritorio Central.  
Si no está familiarizado con este procedimiento, vea [cómo convertir un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).



###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación de **Escritorio Central** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar inicio de sesión único] (./media/active-directory-saas-central-desktop-tutorial/IC749323.png "Configurar inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión el escritorio Central** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-central-desktop-tutorial/IC777628.png "Configurar inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , realice los pasos siguientes y, a continuación, haga clic en **siguiente**: 

    -   En el cuadro de texto **Central de inicio de sesión de escritorio en URL** , escriba la dirección URL de su inquilino de escritorio Central (por ejemplo: *http://contoso.centraldesktop.com*).
    -   En el cuadro de texto dirección URL de respuesta de escritorio Central, escriba la dirección URL Central AssertionConsumerService de escritorio (por ejemplo: https://contoso.centraldesktop.com/saml2-assertion.php).

    >[AZURE.NOTE] Puede obtener el valor de los metadatos de escritorio central (por ejemplo: *http://contoso.centraldesktop.com*).

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-central-desktop-tutorial/IC769561.png "Configurar la dirección URL de la aplicación")

4.  En la página **configurar inicio de sesión único en el equipo de escritorio Central** , para descargar el certificado, haga clic en **Descargar certificado**y, a continuación, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-central-desktop-tutorial/IC769562.png "Configurar inicio de sesión único")

5.  Inicie sesión en su inquilino de **Escritorio Central** .

6.  Vaya a **configuración**, haga clic en **Avanzadas**y, a continuación, haga clic en **Inicio de sesión único**.

    ![Instalación: avanzadas] (./media/active-directory-saas-central-desktop-tutorial/IC769563.png "Instalación: avanzadas")

7.  En la página de **Inicio de sesión único en configuración** , realice los pasos siguientes:

    ![Configuración de inicio de sesión único] (./media/active-directory-saas-central-desktop-tutorial/IC769564.png "Configuración de inicio de sesión único")

    1.  Seleccione **Habilitar SAML v2 solo inicio de sesión**.
    2.  En el portal de clásico Azure, en la página **configurar inicio de sesión único en el equipo de escritorio Central** , copie el valor de **Dirección URL del emisor** y péguelo en el cuadro de texto **Dirección URL de SSO** .
    3.  En el portal de clásico Azure, en la página **configurar inicio de sesión único en el equipo de escritorio Central** , copie el valor de **Dirección URL de inicio de sesión remota** y péguelo en el cuadro de texto **Dirección URL de inicio de sesión SSO** .
    4.  En el portal de clásico Azure, en la página **configurar inicio de sesión único en el equipo de escritorio Central** , copie el valor de **Dirección URL del servicio de Sign-Out único** y péguelo en el cuadro de texto **Dirección URL de cierre de sesión de SSO** .

8.  En la sección **Método de comprobación de firma de mensaje** , siga estos pasos:

    ![Método de comprobación de firma de mensaje] (./media/active-directory-saas-central-desktop-tutorial/IC769565.png "Método de comprobación de firma de mensaje")

    1.  Seleccione el **certificado**.
    2.  En la lista de **Certificados de SSO** , seleccione **SHA256 RSH**.
    3.  Crear un archivo de texto desde el certificado descargado, copie el contenido del archivo de texto y péguelo en el campo de **Certificado de SSO** .  

        >[AZURE.TIP] Para obtener más detalles, consulte [cómo convertir un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    4.  Active **Mostrar un vínculo a la página de inicio de sesión de SAMLv2**.

9.  Haga clic en **Actualizar**.

10. En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar inicio de sesión único] (./media/active-directory-saas-central-desktop-tutorial/IC769566.png "Configurar inicio de sesión único")
##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario

Para que usuarios AAD poder iniciar sesión, debe contar con la aplicación de escritorio Central. Esta sección describe cómo crear cuentas de usuario AAD Central de escritorio.

###<a name="to-provision-user-accounts-to-central-desktop"></a>Configurar cuentas de usuario para escritorio Central:

1.  Inicie sesión en su inquilino de escritorio Central.

2.  Vaya a **personas \> miembros internos**.

3.  Haga clic en **Agregar a miembros internos**.

    ![Personas] (./media/active-directory-saas-central-desktop-tutorial/IC781051.png "Personas")

4.  En el cuadro de texto **Dirección de correo electrónico de nuevos miembros** , escriba una cuenta AAD que desee aprovisionar y, a continuación, haga clic en **siguiente**.

    ![Direcciones de correo electrónico de los nuevos miembros] (./media/active-directory-saas-central-desktop-tutorial/IC781052.png "Direcciones de correo electrónico de los nuevos miembros")

5.  Haga clic en **Agregar interno miembro**.

    ![Agregar a miembro interno] (./media/active-directory-saas-central-desktop-tutorial/IC781053.png "Agregar a miembro interno")

    >[AZURE.NOTE] Los usuarios que haya agregado recibirán un correo electrónico que incluye un vínculo de confirmación que necesitan hacer clic para activar la cuenta.

>[AZURE.NOTE] Puede usar cualquier otros equipo de escritorio Central usuario cuenta herramientas de creación o API proporcionan por escritorio Central a cuentas de usuario AAD de aprovisionamiento

##<a name="assigning-users"></a>Asignación de usuarios

Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-central-desktop-perform-the-following-steps"></a>Para asignar a los usuarios a escritorio Central, realice los pasos siguientes:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación de **Escritorio Central** , haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-central-desktop-tutorial/IC769567.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-central-desktop-tutorial/IC767830.png "Sí")

Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

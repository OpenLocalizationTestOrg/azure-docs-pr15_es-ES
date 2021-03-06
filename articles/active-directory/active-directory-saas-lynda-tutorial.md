<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Lynda.com | Microsoft Azure" 
    description="Aprenda a usar Lynda.com con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más." 
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

#<a name="tutorial-azure-active-directory-integration-with-lyndacom"></a>Tutorial: Integración de Azure Active Directory con Lynda.com
  
El objetivo de este tutorial es mostrar la integración de Azure y Lynda.com.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inquilino Lynda.com
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Lynda.com será capaz de inicio de sesión único en la aplicación en el sitio de su empresa de Lynda.com (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
El escenario descrito en este tutorial se compone de los siguientes bloques de creación:

1.  Habilitar la integración de la aplicación para Lynda.com
2.  Configuración de inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-lynda-tutorial/IC781046.png "Escenario")
##<a name="enabling-the-application-integration-for-lyndacom"></a>Habilitar la integración de la aplicación para Lynda.com
  
El objetivo de esta sección es describen cómo habilitar la integración de la aplicación para Lynda.com.

###<a name="to-enable-the-application-integration-for-lyndacom-perform-the-following-steps"></a>Para habilitar la integración de la aplicación para Lynda.com, realice los pasos siguientes:

1.  En el portal de clásico Azure, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-lynda-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-lynda-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-lynda-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-lynda-tutorial/IC749322.png "Agregar una aplicación de gallerry")

6.  En el **cuadro de búsqueda**, escriba **Lynda.com**.

    ![Galería de aplicación] (./media/active-directory-saas-lynda-tutorial/IC777524.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **Lynda.com**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Lynda.com] (./media/active-directory-saas-lynda-tutorial/IC777525.png "Lynda.com")
##<a name="configuring-single-sign-on"></a>Configuración de inicio de sesión único
  
El objetivo de esta sección es describen cómo habilitar los usuarios autenticar Lynda.com con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

>[AZURE.IMPORTANT]Para poder configurar el inicio de sesión único en su inquilino Lynda.com, debe ponerse en contacto con el soporte técnico de Lynda.com para obtener esta característica está habilitada en primer lugar.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación **Lynda.com** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar inicio de sesión único] (./media/active-directory-saas-lynda-tutorial/IC777526.png "Configurar inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión Lynda.com** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-lynda-tutorial/IC777527.png "Configurar inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto **Lynda.com inicio de sesión en la URL** , escriba la URL del inquilino Lynda.com (por ejemplo: *https://shib.lynda.com/Shibboleth.sso/InCommon?providerId=https://sts.windows-ppe.net/6247032d-9415-403c-b72b-277e3fb6f2c8/&target= https://shib.lynda.com/InCommon*) y, a continuación, haga clic en **siguiente**.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-lynda-tutorial/IC781047.png "Configurar la dirección URL de la aplicación")

4.  En la página **configurar inicio de sesión único en Lynda.com** , para descargar los metadatos, haga clic en **descargar metadatos**y, a continuación, guarde el archivo de certificado localmente en el equipo.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-lynda-tutorial/IC777529.png "Configurar inicio de sesión único")

5.  Enviar el archivo de metadatos descargado al equipo de soporte de Lynda.com. El equipo de soporte técnico de Lynda.com realiza la configuración de inicio de sesión único para usted.

6.  En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar inicio de sesión único] (./media/active-directory-saas-lynda-tutorial/IC777530.png "Configurar inicio de sesión único")
##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
No hay ningún elemento de acción para configurar Lynda.com de aprovisionamiento de usuarios.  
Cuando un usuario asignado intenta iniciar sesión en Lynda.com mediante el panel de acceso, Lynda.com comprueba si existe el usuario.  
Si no hay ninguna cuenta de usuario aún, se crea automáticamente por Lynda.com.

>[AZURE.NOTE]Puede usar cualquier otros Lynda.com usuario cuenta herramientas de creación o API proporcionan por Lynda.com a cuentas de usuario AAD de aprovisionamiento.

##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-lyndacom-perform-the-following-steps"></a>Para asignar a los usuarios a Lynda.com, realice los pasos siguientes:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación **Lynda.com **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-lynda-tutorial/IC777531.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-lynda-tutorial/IC767830.png "Sí")
  
Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
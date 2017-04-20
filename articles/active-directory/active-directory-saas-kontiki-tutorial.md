<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Kontiki | Microsoft Azure" 
    description="Aprenda a usar Kontiki con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más." 
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

#<a name="tutorial-azure-active-directory-integration-with-kontiki"></a>Tutorial: Integración de Azure Active Directory con Kontiki
  
El objetivo de este tutorial es mostrar la integración de Azure y Kontiki.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inicio de sesión único de Kontiki activado suscripción
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Kontiki será capaz de inicio de sesión único en la aplicación en el sitio de su empresa de Kontiki (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
El escenario descrito en este tutorial se compone de los siguientes bloques de creación:

1.  Habilitar la integración de aplicación para Kontiki
2.  Configuración de inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-kontiki-tutorial/IC790235.png "Escenario")
##<a name="enabling-the-application-integration-for-kontiki"></a>Habilitar la integración de aplicación para Kontiki
  
El objetivo de esta sección es describen cómo habilitar la integración de aplicación para Kontiki.

###<a name="to-enable-the-application-integration-for-kontiki-perform-the-following-steps"></a>Para habilitar la integración de aplicación para Kontiki, siga estos pasos:

1.  En el portal de clásico Azure, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-kontiki-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-kontiki-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-kontiki-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-kontiki-tutorial/IC749322.png "Agregar una aplicación de gallerry")

6.  En el **cuadro de búsqueda**, escriba **Kontiki**.

    ![Galería de aplicación] (./media/active-directory-saas-kontiki-tutorial/IC790236.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **Kontiki**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Kontiki] (./media/active-directory-saas-kontiki-tutorial/IC790237.png "Kontiki")
##<a name="configuring-single-sign-on"></a>Configuración de inicio de sesión único
  
El objetivo de esta sección es describen cómo habilitar los usuarios autenticar a Kontiki con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación **Kontiki** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-kontiki-tutorial/IC790238.png "Configurar el inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión Kontiki** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-kontiki-tutorial/IC790239.png "Configurar el inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto **Kontiki inicio de sesión en la URL** , escriba la dirección URL utilizada por los usuarios para iniciar sesión Kontiki (por ejemplo: "*https://company.mc.eval.kontiki.com/*") y, a continuación, haga clic en **siguiente**.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-kontiki-tutorial/IC790240.png "Configurar la dirección URL de la aplicación")

4.  En la página **configurar inicio de sesión único en Kontiki** , haga clic en **descargar metadatos**y, a continuación, guarde el archivo de metadatos en el equipo.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-kontiki-tutorial/IC790241.png "Configurar el inicio de sesión único")

5.  Enviar la metadatafile al equipo de soporte de Kontiki.

    >[AZURE.NOTE] La configuración de inicio de sesión único debe realizarse en el equipo de soporte técnico Kontiki. Recibirá una notificación tan pronto como se ha completado la configuración.

6.  En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-kontiki-tutorial/IC790242.png "Configurar el inicio de sesión único")
##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
No hay ningún elemento de acción para configurar Kontiki de aprovisionamiento de usuarios.  
Cuando un usuario asignado intenta iniciar sesión en Kontiki mediante el panel de acceso, Kontiki comprueba si existe el usuario.  
Si no hay ninguna cuenta de usuario aún, se crea automáticamente por Kontiki.
##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-kontiki-perform-the-following-steps"></a>Para asignar a los usuarios a Kontiki, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación **Kontiki **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-kontiki-tutorial/IC790243.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-kontiki-tutorial/IC767830.png "Sí")
  
Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
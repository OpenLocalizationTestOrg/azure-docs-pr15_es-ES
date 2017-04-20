<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con la primera piedra petición | Microsoft Azure" 
    description="¡Obtenga información sobre cómo usar primera piedra petición con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más!" 
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

#<a name="tutorial-azure-active-directory-integration-with-cornerstone-ondemand"></a>Tutorial: Integración de Azure Active Directory con la primera piedra petición

El objetivo de este tutorial es mostrar la integración de Azure y primera piedra petición.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inquilino de petición de la primera piedra

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a petición de la primera piedra podrán al inicio de sesión único en la aplicación en el sitio de su empresa de petición de la primera piedra (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

El escenario descrito en este tutorial se compone de los siguientes bloques de creación:

1.  Habilitar la integración de aplicación de la primera piedra petición
2.  Configuración de inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781593.png "Escenario")
##<a name="enabling-the-application-integration-for-cornerstone-ondemand"></a>Habilitar la integración de aplicación de la primera piedra petición

El objetivo de esta sección es describen cómo habilitar la integración de aplicación de la primera piedra petición.

###<a name="to-enable-the-application-integration-for-cornerstone-ondemand-perform-the-following-steps"></a>Para habilitar la integración de aplicación de la primera piedra petición, realice los pasos siguientes:

1.  En el portal de clásico Azure, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC749322.png "Agregar una aplicación de gallerry")

6.  En el **cuadro de búsqueda**, escriba la **primera piedra petición**.

    ![Galería de aplicación] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781594.png "Galería de aplicación")

7.  En el panel de resultados, seleccione la **Primera piedra petición**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Petición de la primera piedra] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781595.png "Petición de la primera piedra")
##<a name="configuring-single-sign-on"></a>Configuración de inicio de sesión único

El objetivo de esta sección es describen cómo habilitar los usuarios autenticar a petición de la primera piedra con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación de **Petición de la primera piedra** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Habilitar inicio de sesión único] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781596.png "Habilitar inicio de sesión único")

2.  En la página **¿cómo le gustaría usuarios a iniciar sesión primera piedra petición** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Inicio de sesión único de AD en Microsoft Azure] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781597.png "Inicio de sesión único de AD en Microsoft Azure")

3.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto de la **Primera piedra petición de inicio de sesión en dirección URL** , escriba la dirección URL con el siguiente patrón "*http://company.csod.com*" y, a continuación, haga clic en **siguiente**.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781598.png "Configurar la dirección URL de la aplicación")

4.  En la página **configurar inicio de sesión único en la primera piedra petición** para descargar el certificado, haga clic en **Descargar certificado**y, a continuación, guarde el archivo de certificado localmente en el equipo.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781599.png "Configurar el inicio de sesión único")

5.  Enviar el que equipo de soporte técnico de los elementos siguientes a la petición de la primera piedra:

    1.  El certificado descargado
    2.  El valor de **Dirección URL de inicio de sesión remota**
    3.  El valor de **Dirección URL de cierre de sesión remota** .

    >[AZURE.NOTE] Inicio de sesión único debe estar configurado en el equipo de soporte técnico petición de la primera piedra.
Una vez completada la configuración, recibirá una notificación desde el equipo de soporte técnico.

6.  Seleccione la configuración de inicio de sesión único de confirmación y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781600.png "Configurar el inicio de sesión único")
##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD iniciar sesión en la primera piedra petición, debe aprovisionar a petición de la primera piedra.  
En el caso de petición de la primera piedra, aprovisionamiento es una tarea manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar el aprovisionamiento de usuario, siga estos pasos:

1.  Enviar la información (por ejemplo: nombre, Emial) acerca del usuario de Azure AD desee aprovisionar a petición de la primera piedra soporte técnico.

>[AZURE.NOTE] Puede usar cualquier otras petición de la primera piedra usuario cuenta herramientas de creación o API proporcionan por la primera piedra petición a cuentas de usuario AAD de aprovisionamiento.

##<a name="assigning-users"></a>Asignación de usuarios

Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-cornerstone-ondemand-perform-the-following-steps"></a>Para asignar a los usuarios a petición de la primera piedra, realice los pasos siguientes:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación de **Petición de la primera piedra **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC775564.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Asignar usuarios] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781601.png "Asignar usuarios")

Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

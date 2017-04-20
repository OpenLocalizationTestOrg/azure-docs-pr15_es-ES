<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Sciforma | Microsoft Azure" 
    description="Aprenda a usar Sciforma con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más." 
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
    ms.date="09/26/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-ad-integration-with-sciforma"></a>Tutorial: Integración de Azure AD con Sciforma
  
El objetivo de este tutorial es mostrar la integración de Azure y Sciforma.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inquilino Sciforma
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Sciforma será capaz de inicio de sesión único en la aplicación en el sitio de su empresa de Sciforma (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
El escenario descrito en este tutorial se compone de los siguientes bloques de creación:

1.  Habilitar la integración de aplicación para Sciforma
2.  Configuración de inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-sciforma-tutorial/IC777369.png "Escenario")
##<a name="enabling-the-application-integration-for-sciforma"></a>Habilitar la integración de aplicación para Sciforma
  
El objetivo de esta sección es describen cómo habilitar la integración de aplicación para Sciforma.

###<a name="to-enable-the-application-integration-for-sciforma-perform-the-following-steps"></a>Para habilitar la integración de aplicación para Sciforma, siga estos pasos:

1.  En el portal de clásico Azure, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-sciforma-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-sciforma-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-sciforma-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-sciforma-tutorial/IC749322.png "Agregar una aplicación de gallerry")

6.  En el **cuadro de búsqueda**, escriba **Sciforma**.

    ![Galería de aplicación] (./media/active-directory-saas-sciforma-tutorial/IC777370.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **Sciforma**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Sciforma] (./media/active-directory-saas-sciforma-tutorial/IC777371.png "Sciforma")
##<a name="configuring-single-sign-on"></a>Configuración de inicio de sesión único
  
El objetivo de esta sección es describen cómo habilitar los usuarios autenticar a Sciforma con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación **Sciforma** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar inicio de sesión único] (./media/active-directory-saas-sciforma-tutorial/IC777372.png "Configurar inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión Sciforma** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-sciforma-tutorial/IC777373.png "Configurar inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto **Sciforma inicio de sesión en la URL** , escriba la dirección URL con el siguiente patrón "https://*\<nombre del inquilino\>. Sciforma.com*"y, a continuación, haga clic en **siguiente**.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-sciforma-tutorial/IC777374.png "Configurar la dirección URL de la aplicación")

4.  En **configurar inicio de sesión único en Sciforma** página para descargar los metadatos, haga clic en **descargar metadatos**y, a continuación, el archivo de datos local como **c:\\SciformaMetaData.xml**.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-sciforma-tutorial/IC777375.png "Configurar inicio de sesión único")

5.  Reenviar ese archivo de metadatos para Sciforma equipo de soporte técnico. El equipo de soporte técnico necesita configura un inicio de sesión único para usted.

6.  Seleccione la configuración de inicio de sesión único de confirmación y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar inicio de sesión único] (./media/active-directory-saas-sciforma-tutorial/IC777376.png "Configurar inicio de sesión único")
##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
No hay ningún elemento de acción para configurar Sciforma de aprovisionamiento de usuarios.  
Cuando un usuario asignado intenta iniciar sesión en Sciforma mediante el panel de acceso, Sciforma comprueba si existe el usuario.  
Si no hay ninguna cuenta de usuario aún, se crea automáticamente por Sciforma.
##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-sciforma-perform-the-following-steps"></a>Para asignar a los usuarios a Sciforma, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación **Sciforma **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-sciforma-tutorial/IC777377.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-sciforma-tutorial/IC767830.png "Sí")
  
Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
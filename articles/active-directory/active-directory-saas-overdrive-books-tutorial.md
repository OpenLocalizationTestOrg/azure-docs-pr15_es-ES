<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con libros Overdrive | Microsoft Azure" 
    description="¡Obtenga información sobre cómo utilizar Overdrive libros con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más!" 
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

#<a name="tutorial-azure-active-directory-integration-with-overdrive-books"></a>Tutorial: Integración de Azure Active Directory Overdrive libros
  
El objetivo de este tutorial es mostrar la integración de Azure y OverDrive.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inicio de sesión único de OverDrive activado suscripción
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a OverDrive será capaz de inicio de sesión único en la aplicación en el sitio de su empresa de OverDrive (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
El escenario descrito en este tutorial consta de los siguientes elementos:

1.  Habilitar la integración de la aplicación para OverDrive
2.  Configurar el inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-overdrive-books-tutorial/IC784462.png "Escenario")
##<a name="enabling-the-application-integration-for-overdrive"></a>Habilitar la integración de la aplicación para OverDrive
  
El objetivo de esta sección es describen cómo habilitar la integración de la aplicación para OverDrive.

###<a name="to-enable-the-application-integration-for-overdrive-perform-the-following-steps"></a>Para habilitar la integración de la aplicación para OverDrive, realice los pasos siguientes:

1.  En el portal clásico Azure, en el panel de navegación de la izquierda, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-overdrive-books-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-overdrive-books-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-overdrive-books-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-overdrive-books-tutorial/IC749322.png "Agregar una aplicación de muestra")

6.  En el **cuadro de búsqueda**, escriba **OverDrive**.

    ![Galería de aplicación] (./media/active-directory-saas-overdrive-books-tutorial/IC784463.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **OverDrive**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![OverDrive] (./media/active-directory-saas-overdrive-books-tutorial/IC799950.png "OverDrive")
##<a name="configuring-single-sign-on"></a>Configurar el inicio de sesión único
  
El objetivo de esta sección es describen cómo habilitar los usuarios autenticar OverDrive con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación **OverDrive** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Habilitar inicio de sesión único] (./media/active-directory-saas-overdrive-books-tutorial/IC784465.png "Habilitar inicio de sesión único")

2.  En la página **¿cómo le gustaría usuarios a iniciar sesión OverDrive** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-overdrive-books-tutorial/IC784466.png "Configurar inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto **OverDrive inicio de sesión en la URL** , escriba la dirección URL con el siguiente patrón "*http://mslibrarytest.libraryreserve.com*" y, a continuación, haga clic en **siguiente**.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-overdrive-books-tutorial/IC784467.png "Configurar la dirección URL de la aplicación")

4.  En la página **configurar inicio de sesión único en OverDrive** , descargue el archivo de metadatos y, a continuación, envíelo al equipo de soporte OverDrive.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-overdrive-books-tutorial/IC784468.png "Configurar inicio de sesión único")

    >[AZURE.NOTE]El equipo de soporte técnico de OverDrive configura un inicio de sesión único para usted y le envía una notificación cuando se haya completado la configuración.

5.  En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar inicio de sesión único] (./media/active-directory-saas-overdrive-books-tutorial/IC784469.png "Configurar inicio de sesión único")
##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
No hay ningún elemento de acción para configurar el aprovisionamiento de usuario a OverDrive.  
Cuando un usuario asignado intenta iniciar sesión en OverDrive, una cuenta de OverDrive se crea automáticamente si es necesario.

>[AZURE.NOTE]Puede usar cualquier otros OverDrive usuario cuenta herramientas de creación o API proporcionan por OverDrive a cuentas de usuario AAD de aprovisionamiento.

##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-overdrive-perform-the-following-steps"></a>Para asignar a los usuarios a OverDrive, realice los pasos siguientes:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación **OverDrive **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-overdrive-books-tutorial/IC784470.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-overdrive-books-tutorial/IC767830.png "Sí")
  
Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
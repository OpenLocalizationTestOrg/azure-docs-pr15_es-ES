<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Projectplace | Microsoft Azure" 
    description="Aprenda a usar Projectplace con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más." 
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

#<a name="tutorial-azure-active-directory-integration-with-projectplace"></a>Tutorial: Integración de Azure Active Directory con Projectplace
  
El objetivo de este tutorial es mostrar la integración de Azure y Projectplace.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inicio de sesión único de Projectplace activado suscripción
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Projectplace será capaz de inicio de sesión único en la aplicación en el sitio de su empresa de Projectplace (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
El escenario descrito en este tutorial consta de los siguientes elementos:

1.  Habilitar la integración de aplicación para Projectplace
2.  Configurar el inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-projectplace-tutorial/IC790217.png "Escenario")
##<a name="enabling-the-application-integration-for-projectplace"></a>Habilitar la integración de aplicación para Projectplace
  
El objetivo de esta sección es describen cómo habilitar la integración de aplicación para Projectplace.

###<a name="to-enable-the-application-integration-for-projectplace-perform-the-following-steps"></a>Para habilitar la integración de aplicación para Projectplace, siga estos pasos:

1.  En el portal clásico Azure, en el panel de navegación de la izquierda, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-projectplace-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-projectplace-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-projectplace-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-projectplace-tutorial/IC749322.png "Agregar una aplicación de muestra")

6.  En el **cuadro de búsqueda**, escriba **Projectplace**.

    ![Galería de aplicación] (./media/active-directory-saas-projectplace-tutorial/IC790218.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **Projectplace**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![ProjectPlace] (./media/active-directory-saas-projectplace-tutorial/IC790219.png "ProjectPlace")
##<a name="configuring-single-sign-on"></a>Configurar el inicio de sesión único
  
El objetivo de esta sección es describen cómo habilitar los usuarios autenticar a Projectplace con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación **Projectplace** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-projectplace-tutorial/IC790220.png "Configurar el inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión Projectplace** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-projectplace-tutorial/IC790221.png "Configurar el inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto **Projectplace inicio de sesión en la URL** , escriba la dirección URL de inquilinos de ProjectPlace (por ejemplo: "*http://company.projectplace.com*") y, a continuación, haga clic en **siguiente**.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-projectplace-tutorial/IC790222.png "Configurar la dirección URL de la aplicación")

4.  En la página **configurar inicio de sesión único en Projectplace** , haga clic en **descargar metadatos**y, a continuación, guarde el archivo de metadatos en el equipo.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-projectplace-tutorial/IC790223.png "Configurar el inicio de sesión único")

5.  Enviar la metadatafile al equipo de soporte de Projectplace.

    >[AZURE.NOTE] La configuración de inicio de sesión único debe realizarse en el equipo de soporte técnico Projectplace. Recibirá una notificación tan pronto como se ha completado la configuración.

6.  En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-projectplace-tutorial/IC790227.png "Configurar el inicio de sesión único")
##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD iniciar sesión en Projectplace, debe aprovisionar en Projectplace.  
En el caso de Projectplace, aprovisionamiento es una tarea manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para crear cuentas de usuario, siga estos pasos:

1.  Inicie sesión como administrador en el sitio de su empresa **Projectplace** .

2.  Vaya a **contactos**y, a continuación, haga clic en **miembros**.

    ![Personas] (./media/active-directory-saas-projectplace-tutorial/IC790228.png "Personas")

3.  Haga clic en **Agregar a miembro**.

    ![Agregar miembros] (./media/active-directory-saas-projectplace-tutorial/IC790232.png "Agregar miembros")

4.  En la sección **Agregar miembros** , siga estos pasos:

    ![Nuevos miembros] (./media/active-directory-saas-projectplace-tutorial/IC790233.png "Nuevos miembros")

    1.  En el cuadro de texto **Nuevos miembros** , escriba la dirección de correo electrónico de una cuenta AAD válida que desee aprovisionar en los cuadros de texto relacionados.
    2.  Haga clic en **Enviar**

        >[AZURE.NOTE] Se envía un correo electrónico que incluye un vínculo para confirmar la cuenta antes de que esté activa al titular de la cuenta de Azure Active Directory.
    
>[AZURE.NOTE]Puede usar cualquier otros Projectplace usuario cuenta herramientas de creación o API proporcionan por Projectplace a cuentas de usuario AAD de aprovisionamiento.

##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-projectplace-perform-the-following-steps"></a>Para asignar a los usuarios a Projectplace, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación **Projectplace **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-projectplace-tutorial/IC790234.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-projectplace-tutorial/IC767830.png "Sí")
  
Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
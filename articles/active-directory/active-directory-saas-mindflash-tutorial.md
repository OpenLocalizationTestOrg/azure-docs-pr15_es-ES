<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Mindflash | Microsoft Azure" 
    description="Aprenda a usar Mindflash con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más." 
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

#<a name="tutorial-azure-active-directory-integration-with-mindflash"></a>Tutorial: Integración de Azure Active Directory con Mindflash
  
El objetivo de este tutorial es mostrar la integración de Azure y Mindflash.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inicio de sesión único de Mindflash activado suscripción
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Mindflash será capaz de inicio de sesión único en la aplicación en el sitio de su empresa de Mindflash (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
El escenario descrito en este tutorial consta de los siguientes elementos:

1.  Habilitar la integración de aplicación para Mindflash
2.  Configurar el inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-mindflash-tutorial/IC787132.png "Escenario")
##<a name="enabling-the-application-integration-for-mindflash"></a>Habilitar la integración de aplicación para Mindflash
  
El objetivo de esta sección es describen cómo habilitar la integración de aplicación para Mindflash.

###<a name="to-enable-the-application-integration-for-mindflash-perform-the-following-steps"></a>Para habilitar la integración de aplicación para Mindflash, siga estos pasos:

1.  En el portal clásico Azure, en el panel de navegación de la izquierda, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-mindflash-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-mindflash-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-mindflash-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-mindflash-tutorial/IC749322.png "Agregar una aplicación de muestra")

6.  En el **cuadro de búsqueda**, escriba **Mindflash**.

    ![Galería de aplicación] (./media/active-directory-saas-mindflash-tutorial/IC787133.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **Mindflash**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Mindflash] (./media/active-directory-saas-mindflash-tutorial/IC787134.png "Mindflash")
##<a name="configuring-single-sign-on"></a>Configurar el inicio de sesión único
  
El objetivo de esta sección es describen cómo habilitar los usuarios autenticar a Mindflash con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación **Mindflash** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-mindflash-tutorial/IC787135.png "Configurar el inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión Mindflash** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-mindflash-tutorial/IC787136.png "Configurar el inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto de **Inicio de sesión en la dirección URL** , escriba la dirección URL con el siguiente patrón "*http://company.mindflash.com*" y, a continuación, haga clic en **siguiente**.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-mindflash-tutorial/IC787137.png "Configurar la dirección URL de la aplicación")

4.  En la página **configurar inicio de sesión único en Mindflash** , haga clic en **descargar metadatos**y, a continuación, guarde el archivo de metadatos en el equipo.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-mindflash-tutorial/IC787138.png "Configurar el inicio de sesión único")

5.  Enviar la metadatafile al equipo de soporte de Mindflash.

    >[AZURE.NOTE] La configuración de inicio de sesión único debe realizarse en el equipo de soporte técnico Mindflash. Recibirá una notificación tan pronto como se ha completado la configuración.

6.  En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-mindflash-tutorial/IC787139.png "Configurar el inicio de sesión único")
##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD iniciar sesión en Mindflash, debe aprovisionar en Mindflash.  
En el caso de Mindflash, aprovisionamiento es una tarea manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para crear cuentas de usuario, siga estos pasos:

1.  Inicie sesión como administrador en el sitio de su empresa **Mindflash** .

2.  Vaya a **administración de usuarios**.

    ![Administrar usuarios] (./media/active-directory-saas-mindflash-tutorial/IC787140.png "Administrar usuarios")

3.  Haga clic en los **Usuarios agregar**y, a continuación, haga clic en **nuevo**.

4.  En la sección **Agregar nuevos usuarios** , siga estos pasos:

    ![Agregar nuevos usuarios] (./media/active-directory-saas-mindflash-tutorial/IC787141.png "Agregar nuevos usuarios")

    1.  Escriba el **nombre**, **apellido** y **correo electrónico** de una cuenta AAD válida que desee aprovisionar en los cuadros de texto relacionados.
    2.  Haga clic en **Agregar**.

>[AZURE.NOTE]Puede usar cualquier otros Mindflash usuario cuenta herramientas de creación o API proporcionan por Mindflash a cuentas de usuario AAD de aprovisionamiento.

##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-mindflash-perform-the-following-steps"></a>Para asignar a los usuarios a Mindflash, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación **Mindflash **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-mindflash-tutorial/IC787142.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-mindflash-tutorial/IC767830.png "Sí")
  
Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
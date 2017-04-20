<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Innotas | Microsoft Azure"
    description="Aprenda a usar Innotas con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más." 
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

#<a name="tutorial-azure-active-directory-integration-with-innotas"></a>Tutorial: Integración de Azure Active Directory con Innotas
  
El objetivo de este tutorial es mostrar la integración de Azure y Innotas.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inquilino Innotas
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Innotas será capaz de inicio de sesión único en la aplicación en el sitio de su empresa de Innotas (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
El escenario descrito en este tutorial consta de los siguientes elementos:

1.  Habilitar la integración de la aplicación para Innotas
2.  Configurar el inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-innotas-tutorial/IC777331.png "Escenario")
##<a name="enabling-the-application-integration-for-innotas"></a>Habilitar la integración de la aplicación para Innotas
  
El objetivo de esta sección es describen cómo habilitar la integración de aplicación para Innotas.

###<a name="to-enable-the-application-integration-for-innotas-perform-the-following-steps"></a>Para habilitar la integración de aplicación para Innotas, siga estos pasos:

1.  En el portal clásico Azure, en el panel de navegación de la izquierda, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-innotas-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-innotas-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-innotas-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-innotas-tutorial/IC749322.png "Agregar una aplicación de muestra")

6.  En el **cuadro de búsqueda**, escriba **Innotas**.

    ![Galería de aplicación] (./media/active-directory-saas-innotas-tutorial/IC777332.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **Innotas**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Innotas] (./media/active-directory-saas-innotas-tutorial/IC777333.png "Innotas")
##<a name="configuring-single-sign-on"></a>Configurar el inicio de sesión único
  
El objetivo de esta sección es describen cómo habilitar los usuarios autenticar a Innotas con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación **Innotas** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar inicio de sesión único] (./media/active-directory-saas-innotas-tutorial/IC777334.png "Configurar inicio de sesión único")

2.  En la página **¿cómo desea que a los usuarios iniciar sesión Innotas** , seleccione **AD Azure de Microsoft Single Sign-On**y, a continuación, haga clic en **siguiente**.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-innotas-tutorial/IC777335.png "Configurar inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto **Innotas inicio de sesión en la URL** , escriba la dirección URL con el siguiente patrón "https://*\<nombre del inquilino\>. Innotas.com*"y, a continuación, haga clic en **siguiente**.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-innotas-tutorial/IC777336.png "Configurar la dirección URL de la aplicación")

4.  En **configurar inicio de sesión único en Innotas** página para descargar los metadatos, haga clic en **descargar metadatos**y, a continuación, el archivo de datos local como **c:\\InnotasMetaData.xml**.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-innotas-tutorial/IC777337.png "Configurar inicio de sesión único")

5.  Reenviar ese archivo de metadatos para Innotas equipo de soporte técnico. El equipo de soporte técnico necesita configura un inicio de sesión único para usted.

6.  Seleccione la configuración de inicio de sesión único de confirmación y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar inicio de sesión único] (./media/active-directory-saas-innotas-tutorial/IC777338.png "Configurar inicio de sesión único")
##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
No hay ningún elemento de acción para configurar Innotas de aprovisionamiento de usuarios.  
Cuando un usuario asignado intenta iniciar sesión en Innotas mediante el panel de acceso, Innotas comprueba si existe el usuario.  
Si no hay ninguna cuenta de usuario aún, se crea automáticamente por Innotas.
##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-innotas-perform-the-following-steps"></a>Para asignar a los usuarios a Innotas, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación **Innotas **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-innotas-tutorial/IC777339.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-innotas-tutorial/IC767830.png "Sí")
  
Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
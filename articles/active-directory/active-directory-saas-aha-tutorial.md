<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Aha! | Microsoft Azure" 
    description="¡Obtenga información sobre cómo usar Aha! con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más!" 
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

#<a name="tutorial-azure-active-directory-integration-with-aha"></a>Tutorial: Integración de Azure Active Directory con Aha!

El objetivo de este tutorial es mostrar la integración de Azure y Aha!  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un ahí! inicio de sesión único habilitado suscripción

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Aha! ¡podrá solo inicio de sesión en la aplicación en su Aha! sitio de la empresa (servicio proveedor inicia sesión), o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

El escenario descrito en este tutorial consta de los siguientes elementos:

1.  ¡Habilitar la integración de la aplicación para Aha!
2.  Configurar el inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-aha-tutorial/IC798944.png "Escenario")
##<a name="enabling-the-application-integration-for-aha"></a>¡Habilitar la integración de la aplicación para Aha!

El objetivo de esta sección es describen cómo habilitar la integración de la aplicación para Aha!.

###<a name="to-enable-the-application-integration-for-aha-perform-the-following-steps"></a>Para habilitar la integración de la aplicación para Aha!, realice los pasos siguientes:

1.  En el portal clásico Azure, en el panel de navegación de la izquierda, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-aha-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-aha-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-aha-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-aha-tutorial/IC749322.png "Agregar una aplicación de muestra")

6.  En el **cuadro de búsqueda**, escriba **Aha!**.

    ![Galería de aplicación] (./media/active-directory-saas-aha-tutorial/IC798945.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **Aha!**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Ahí!] (./media/active-directory-saas-aha-tutorial/IC802746.png "Ahí!")
##<a name="configuring-single-sign-on"></a>Configurar el inicio de sesión único

El objetivo de esta sección es describen cómo habilitar los usuarios autenticar Aha! con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de Azure clásico, en el **Aha!** integración de las aplicaciones de página, haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-aha-tutorial/IC798946.png "Configurar el inicio de sesión único")

2.  En la **cómo le gustaría usuarios a iniciar sesión Aha!** página, seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-aha-tutorial/IC798947.png "Configurar el inicio de sesión único")

3.  ¡En la página **Configurar la dirección URL de aplicación** , en la **Aha! Inicio de sesión en la dirección URL** cuadro de texto, escriba la dirección URL utiliza los usuarios para iniciar sesión en su Aha! Aplicación (por ejemplo: "*https://company.aha.io/session/new*") y, a continuación, haga clic en **siguiente**.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-aha-tutorial/IC798948.png "Configurar la dirección URL de la aplicación")

4.  En la **Configurar el inicio de sesión único en Aha!** página para descargar el archivo de metadatos, haga clic en **descargar metadatos**y, a continuación, guarde el archivo de metadatos localmente en el equipo.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-aha-tutorial/IC798949.png "Configurar el inicio de sesión único")

5.  En una ventana de explorador web diferente, inicie sesión en su Aha! sitio de la empresa como administrador.

6.  En el menú de la parte superior, haga clic en **configuración**.

    ![Configuración] (./media/active-directory-saas-aha-tutorial/IC798950.png "Configuración")

7.  Haga clic en **cuenta**.

    ![Perfil] (./media/active-directory-saas-aha-tutorial/IC798951.png "Perfil")

8.  Haga clic en **seguridad y el inicio de sesión único**.

    ![Seguridad y el inicio de sesión único] (./media/active-directory-saas-aha-tutorial/IC798952.png "Seguridad y el inicio de sesión único")

9.  En la sección **Inicio de sesión único** , como el **Proveedor de identidades**, seleccione **SAML2.0**.

    ![Seguridad y el inicio de sesión único] (./media/active-directory-saas-aha-tutorial/IC798953.png "Seguridad y el inicio de sesión único")

10. En la página de configuración de **Inicio de sesión único** , realice los pasos siguientes:

    ![Inicio de sesión único] (./media/active-directory-saas-aha-tutorial/IC798954.png "Inicio de sesión único")

    1.  En el cuadro de texto **nombre** , escriba un nombre para la configuración.
    2.  Para **configurar con**, seleccione el **Archivo de metadatos**.
    3.  Para cargar el archivo descargado metadatos, haga clic en **Examinar**.
    4.  Haga clic en **Actualizar**.

11. En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-aha-tutorial/IC798955.png "Configurar el inicio de sesión único")
##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD iniciar sesión en Aha!, debe aprovisionar en Aha!.  
En el caso de Aha!, aprovisionamiento es una tarea automatizada.  
No hay ningún elemento de acción para usted.
  
Los usuarios se crean automáticamente durante el primer única sesión intento si es necesario.

>[AZURE.NOTE] ¡Puede usar cualquier otro Aha! ¡herramientas de creación de cuenta de usuario o las API proporcionadas por Aha! aprovisionar cuentas de usuario AAD.

##<a name="assigning-users"></a>Asignación de usuarios

Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-aha-perform-the-following-steps"></a>Para asignar los usuarios a Aha!, realice los pasos siguientes:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la **ahí!** integración de las aplicaciones de página, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-aha-tutorial/IC798956.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-aha-tutorial/IC767830.png "Sí")

Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

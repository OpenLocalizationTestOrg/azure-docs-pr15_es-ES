<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Panorama9 | Microsoft Azure" 
    description="Aprenda a usar Panorama9 con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más." 
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

#<a name="tutorial-azure-active-directory-integration-with-panorama9"></a>Tutorial: Integración de Azure Active Directory con Panorama9
  
El objetivo de este tutorial es mostrar la integración de Azure y Panorama9.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inicio de sesión único de Panorama9 activado suscripción
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Panorama9 será capaz de inicio de sesión único en la aplicación en el sitio de su empresa de Panorama9 (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
El escenario descrito en este tutorial consta de los siguientes elementos:

1.  Habilitar la integración de aplicación para Panorama9
2.  Configurar el inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-panorama9-tutorial/IC790016.png "Escenario")
##<a name="enabling-the-application-integration-for-panorama9"></a>Habilitar la integración de aplicación para Panorama9
  
El objetivo de esta sección es describen cómo habilitar la integración de aplicación para Panorama9.

###<a name="to-enable-the-application-integration-for-panorama9-perform-the-following-steps"></a>Para habilitar la integración de aplicación para Panorama9, siga estos pasos:

1.  En el portal clásico Azure, en el panel de navegación de la izquierda, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-panorama9-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-panorama9-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-panorama9-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-panorama9-tutorial/IC749322.png "Agregar una aplicación de muestra")

6.  En el **cuadro de búsqueda**, escriba **Panorama9**.

    ![Galería de aplicación] (./media/active-directory-saas-panorama9-tutorial/IC790017.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **Panorama9**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Panorama9] (./media/active-directory-saas-panorama9-tutorial/IC790018.png "Panorama9")
##<a name="configuring-single-sign-on"></a>Configurar el inicio de sesión único
  
El objetivo de esta sección es describen cómo habilitar los usuarios autenticar a Panorama9 con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
Configuración de inicio de sesión único para Panorama9 requiere que recuperar un valor de la huella digital de un certificado.  
Si no está familiarizado con este procedimiento, consulte [cómo recuperar el valor de la huella digital de un certificado](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación **Panorama9** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-panorama9-tutorial/IC790019.png "Configurar el inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión Panorama9** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-panorama9-tutorial/IC790020.png "Configurar el inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto **Panorama9 inicio de sesión en la URL** , escriba la dirección URL que utilizan los usuarios para iniciar sesión en Panorama9 (por ejemplo: "*https://dashboard.panorama9.com/saml/access/3262*") y, a continuación, haga clic en **siguiente**.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-panorama9-tutorial/IC790021.png "Configurar la dirección URL de la aplicación")

4.  En la página **configurar inicio de sesión único en Panorama9** para descargar el certificado, haga clic en **Descargar certificado**y guardarlo localmente en el equipo.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-panorama9-tutorial/IC790022.png "Configurar el inicio de sesión único")

5.  En una ventana de explorador web diferente, inicie sesión en el sitio de su empresa Panorama9 como administrador.

6.  En la barra de herramientas en la parte superior, haga clic en **Administrar**y, a continuación, haga clic en **extensiones**.

    ![Extensiones] (./media/active-directory-saas-panorama9-tutorial/IC790023.png "Extensiones")

7.  En el cuadro de diálogo **extensiones** , haga clic en **Inicio de sesión único**.

    ![Inicio de sesión único] (./media/active-directory-saas-panorama9-tutorial/IC790024.png "Inicio de sesión único")

8.  En la sección **configuración** , siga estos pasos:

    ![Configuración] (./media/active-directory-saas-panorama9-tutorial/IC790025.png "Configuración")

    1.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en Panorama9** , copie el valor de **URL de servicio de inicio de sesión único** y, a continuación, péguelo en el cuadro de texto de la **dirección URL del proveedor de identidades** .
    2.  Copie el valor de la **huella digital** del certificado exportado y, a continuación, péguelo en el cuadro de texto de la **huella digital de certificado** .  

        >[AZURE.TIP]Para obtener más detalles, consulte [cómo recuperar el valor de la huella digital de un certificado](http://youtu.be/YKQF266SAxI)

    3.  Haga clic en **Guardar**.

9.  En el portal de Azure AD clásico, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-panorama9-tutorial/IC790026.png "Configurar el inicio de sesión único")
##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD iniciar sesión en Panorama9, debe aprovisionar en Panorama9.  
En el caso de Panorama9, aprovisionamiento es una tarea manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar el aprovisionamiento de usuario, siga estos pasos:

1.  Inicie sesión como administrador en el sitio de su empresa **Panorama9** .

2.  En el menú de la parte superior, haga clic en **Administrar**y, a continuación, haga clic en **usuarios**.

    ![Usuarios] (./media/active-directory-saas-panorama9-tutorial/IC790027.png "Usuarios")

3.  Haga clic en **+**.

4.  En la sección de datos de usuario, siga estos pasos:

    ![Usuarios] (./media/active-directory-saas-panorama9-tutorial/IC790028.png "Usuarios")

    1.  En el cuadro de texto de **correo electrónico** , escriba la dirección de correo electrónico de un usuario de Azure Active Directory válido que desee aprovisionar.
    2.  Haga clic en **Guardar**.

>[AZURE.NOTE]Puede usar cualquier otros Panorama9 usuario cuenta herramientas de creación o API proporcionan por Panorama9 a cuentas de usuario AAD de aprovisionamiento.

##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-panorama9-perform-the-following-steps"></a>Para asignar a los usuarios a Panorama9, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación **Panorama9** , haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-panorama9-tutorial/IC790029.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-panorama9-tutorial/IC767830.png "Sí")
  
Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
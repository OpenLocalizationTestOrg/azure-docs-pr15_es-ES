<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Thirdlight | Microsoft Azure" 
    description="Aprenda a usar Thirdlight con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más." 
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
    ms.date="09/11/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-thirdlight"></a>Tutorial: Integración de Azure Active Directory con Thirdlight
  
El objetivo de este tutorial es mostrar la integración de Azure y Thirdlight.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inicio de sesión único de Thirdlight activado suscripción
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Thirdlight será capaz de inicio de sesión único en la aplicación en el sitio de su empresa de Thirdlight (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
El escenario descrito en este tutorial consta de los siguientes elementos:

1.  Habilitar la integración de aplicación para Thirdlight
2.  Configurar el inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-thirdlight-tutorial/IC805836.png "Escenario")

##<a name="enabling-the-application-integration-for-thirdlight"></a>Habilitar la integración de aplicación para Thirdlight
  
El objetivo de esta sección es describen cómo habilitar la integración de aplicación para Thirdlight.

###<a name="to-enable-the-application-integration-for-thirdlight-perform-the-following-steps"></a>Para habilitar la integración de aplicación para Thirdlight, siga estos pasos:

1.  En el portal clásico Azure, en el panel de navegación de la izquierda, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-thirdlight-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-thirdlight-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-thirdlight-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-thirdlight-tutorial/IC749322.png "Agregar una aplicación de muestra")

6.  En el **cuadro de búsqueda**, escriba **Thirdlight**.

    ![Galería de aplicación] (./media/active-directory-saas-thirdlight-tutorial/IC805837.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **Thirdlight**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![ThirdLight] (./media/active-directory-saas-thirdlight-tutorial/IC805838.png "ThirdLight")

##<a name="configuring-single-sign-on"></a>Configurar el inicio de sesión único
  
El objetivo de esta sección es describen cómo habilitar los usuarios autenticar a Thirdlight con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
Configuración de inicio de sesión único para Thirdlight requiere que recuperar un valor de la huella digital de un certificado.  
Si no está familiarizado con este procedimiento, consulte [cómo recuperar el valor de la huella digital de un certificado](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación **Thirdlight** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-thirdlight-tutorial/IC805839.png "Configurar el inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión Thirdlight** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-thirdlight-tutorial/IC805840.png "Configurar el inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto **Thirdlight inicio de sesión en la URL** , escriba la dirección URL usada por los usuarios para iniciar sesión su aplicación Thirdlight (por ejemplo: "*http://azuresso2.thirdlight.com/*") y, a continuación, haga clic en **siguiente**.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-thirdlight-tutorial/IC805841.png "Configurar la dirección URL de la aplicación")

4.  En la página **configurar inicio de sesión único en Thirdlight** , para descargar los metadatos, haga clic en **descargar metadatos**y, a continuación, guarde el archivo de metadatos localmente en el equipo.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-thirdlight-tutorial/IC805842.png "Configurar el inicio de sesión único")

5.  En una ventana de explorador web diferente, inicie sesión en el sitio de su empresa Thirdlight como administrador.

6.  Vaya a **configuración \> administración del sistema**y, a continuación, haga clic en **SAML2**.

    ![Administración del sistema] (./media/active-directory-saas-thirdlight-tutorial/IC805843.png "Administración del sistema")

7.  En la sección Configuración de SAML2, siga estos pasos:

    ![SAML inicio de sesión único] (./media/active-directory-saas-thirdlight-tutorial/IC805844.png "SAML inicio de sesión único")

    1.  Seleccione **Habilitar SAML2 inicio de sesión único**.
    2.  Como **origen de para metadatos IdP**, seleccione **Carga IdP metadatos XML**.
    3.  Abra el archivo descargado metadatos, copie el contenido y péguelo en el cuadro de texto **IdP metadatos XML** .
    4.  Haga clic en **configuración de guardar SAML2**.

8.  En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-thirdlight-tutorial/IC805845.png "Configurar el inicio de sesión único")

##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD iniciar sesión en Thirdlight, debe aprovisionar en Thirdlight.  
En el caso de Thirdlight, aprovisionamiento es una tarea manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar el aprovisionamiento de usuario, siga estos pasos:

1.  Inicie sesión como administrador en el sitio de su empresa **Thirdlight** .

2.  Vaya a la ficha **usuarios** .

3.  Seleccione **usuarios y grupos**.

4.  Haga clic en el botón **Agregar nuevo usuario** .

5.  Escriba el **nombre de usuario, nombre o descripción, correo electrónico, elija un valor preestablecido o de nuevo los miembros del grupo** de una cuenta AAD válida que desee aprovisionar.

6.  Haga clic en **crear**.

>[AZURE.NOTE] Puede usar cualquier otros Thirdlight usuario cuenta herramientas de creación o API proporcionan por Thirdlight a cuentas de usuario AAD de aprovisionamiento.

##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-thirdlight-perform-the-following-steps"></a>Para asignar a los usuarios a Thirdlight, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación **Thirdlight **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-thirdlight-tutorial/IC805846.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-thirdlight-tutorial/IC767830.png "Sí")
  
Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con IdeaScale | Microsoft Azure" 
    description="Aprenda a usar IdeaScale con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más." 
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

#<a name="tutorial-azure-active-directory-integration-with-ideascale"></a>Tutorial: Integración de Azure Active Directory con IdeaScale
  
El objetivo de este tutorial es mostrar la integración de Azure y IdeaScale.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inicio de sesión único de IdeaScale activado suscripción
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a IdeaScale será capaz de inicio de sesión único en la aplicación con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
El escenario descrito en este tutorial consta de los siguientes elementos:

1.  Habilitar la integración de aplicación para IdeaScale
2.  Configurar el inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-ideascale-tutorial/IC790838.png "Escenario")
##<a name="enabling-the-application-integration-for-ideascale"></a>Habilitar la integración de aplicación para IdeaScale
  
El objetivo de esta sección es describen cómo habilitar la integración de aplicación para IdeaScale.

###<a name="to-enable-the-application-integration-for-ideascale-perform-the-following-steps"></a>Para habilitar la integración de aplicación para IdeaScale, siga estos pasos:

1.  En el portal clásico Azure, en el panel de navegación de la izquierda, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-ideascale-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-ideascale-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-ideascale-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-ideascale-tutorial/IC749322.png "Agregar una aplicación de muestra")

6.  En el **cuadro de búsqueda**, escriba **IdeaScale**.

    ![Galería de aplicación] (./media/active-directory-saas-ideascale-tutorial/IC790841.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **IdeaScale**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![IdeaScale] (./media/active-directory-saas-ideascale-tutorial/IC790842.png "IdeaScale")
##<a name="configuring-single-sign-on"></a>Configurar el inicio de sesión único
  
El objetivo de esta sección es describen cómo habilitar los usuarios autenticar a IdeaScale con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
Configuración de inicio de sesión único para IdeaScale requiere que recuperar un valor de la huella digital de un certificado.  
Si no está familiarizado con este procedimiento, consulte [cómo recuperar el valor de la huella digital de un certificado](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación **IdeaScale** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-ideascale-tutorial/IC790843.png "Configurar el inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión IdeaScale** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-ideascale-tutorial/IC790844.png "Configurar el inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto **IdeaScale inicio de sesión en la URL** , escriba la dirección URL utilizada por los usuarios para iniciar sesión la aplicación IdeaScale (por ejemplo: "*https://company.IdeaScale.com*") y, a continuación, haga clic en **siguiente**.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-ideascale-tutorial/IC790845.png "Configurar la dirección URL de la aplicación")

4.  En la página **configurar inicio de sesión único en IdeaScale** , para descargar los metadatos, haga clic en **descargar metadatos**y, a continuación, guarde el archivo de metadatos localmente en el equipo.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-ideascale-tutorial/IC790846.png "Configurar el inicio de sesión único")

5.  En una ventana de explorador web diferente, inicie sesión en el sitio de su empresa IdeaScale como administrador.

6.  Vaya a **configuración de la Comunidad**.

    ![Configuración de la Comunidad] (./media/active-directory-saas-ideascale-tutorial/IC790847.png "Configuración de la Comunidad")

7.  Vaya a **seguridad \> solo la configuración de inicio de sesión**.

    ![Configuración de inicio de sesión único] (./media/active-directory-saas-ideascale-tutorial/IC790848.png "Configuración de inicio de sesión único")

8.  Como **Tipo de inicio de sesión único**, seleccione **SAML 2.0**.

    ![Tipo de inicio de sesión único] (./media/active-directory-saas-ideascale-tutorial/IC790849.png "Tipo de inicio de sesión único")

9.  En el cuadro de diálogo **Configuración de inicio de sesión único** , realice los pasos siguientes:

    ![Configuración de inicio de sesión único] (./media/active-directory-saas-ideascale-tutorial/IC790850.png "Configuración de inicio de sesión único")

    1.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en IdeaScale** , copie el valor de **Id. de entidad** y péguelo en el cuadro de texto **ID de SAML IdP entidad** .
    2.  Copiar el contenido del archivo descargado metadatos y péguelo en el cuadro de texto de **SAML IdP metadatos** .
    3.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en IdeaScale** , copie el valor de **Dirección URL de cierre de sesión remota** y, a continuación, péguelo en el cuadro de texto **Dirección URL de éxito de cierre de sesión** .
    4.  Haga clic en **Guardar cambios**.

10. En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-ideascale-tutorial/IC790851.png "Configurar el inicio de sesión único")
##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD iniciar sesión en IdeaScale, debe aprovisionar en IdeaScale.  
En el caso de IdeaScale, aprovisionamiento es una tarea manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar el aprovisionamiento de usuario, siga estos pasos:

1.  Inicie sesión como administrador en el sitio de su empresa **IdeaScale** .

2.  Vaya a **configuración de la Comunidad**.

    ![Configuración de la Comunidad] (./media/active-directory-saas-ideascale-tutorial/IC790847.png "Configuración de la Comunidad")

3.  Vaya a **configuración básica \> miembro administración**.

4.  Haga clic en **Agregar a miembro**.

    ![Administración de miembros] (./media/active-directory-saas-ideascale-tutorial/IC790852.png "Administración de miembros")

5.  En la sección Agregar miembro nuevo, siga estos pasos:

    ![Agregar nuevo miembro] (./media/active-directory-saas-ideascale-tutorial/IC790853.png "Agregar nuevo miembro")

    1.  En el cuadro de texto de **Las direcciones de correo electrónico** , escriba la dirección de correo electrónico de una cuenta AAD válida que desee aprovisionar.
    2.  Haga clic en **Guardar cambios**.

    >[AZURE.NOTE] Titular de la cuenta de Azure Active Directory recibirá un correo electrónico con un vínculo para confirmar la cuenta antes de que esté activa.

>[AZURE.NOTE] Puede usar cualquier otros IdeaScale usuario cuenta herramientas de creación o API proporcionan por IdeaScale a cuentas de usuario AAD de aprovisionamiento.

##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-ideascale-perform-the-following-steps"></a>Para asignar a los usuarios a IdeaScale, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación **IdeaScale **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-ideascale-tutorial/IC790854.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-ideascale-tutorial/IC767830.png "Sí")
  
Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
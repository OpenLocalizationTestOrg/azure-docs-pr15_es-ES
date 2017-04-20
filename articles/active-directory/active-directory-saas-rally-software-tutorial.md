<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Software de carreras | Microsoft Azure" 
    description="¡Obtenga información sobre cómo utilizar Software Rally con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y más!" 
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

#<a name="tutorial-azure-active-directory-integration-with-rally-software"></a>Tutorial: Integración de Azure Active Directory con Software de carreras
  
El objetivo de este tutorial es mostrar la integración de Azure y Software Rally.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inquilino Rally Software
  
El escenario descrito en este tutorial consta de los siguientes elementos:

1.  Habilitar la integración de aplicaciones para Software Rally
2.  Configurar el inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-rally-software-tutorial/IC769525.png "Escenario")
##<a name="enabling-the-application-integration-for-rally-software"></a>Habilitar la integración de aplicaciones para Software Rally
  
El objetivo de esta sección es describen cómo habilitar la integración de aplicaciones para Software Rally.

###<a name="to-enable-the-application-integration-for-rally-software-perform-the-following-steps"></a>Para habilitar la integración de aplicaciones para Software Rally, realice los pasos siguientes:

1.  En el portal clásico Azure, en el panel de navegación de la izquierda, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-rally-software-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-rally-software-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-rally-software-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-rally-software-tutorial/IC749322.png "Agregar una aplicación de muestra")

6.  En el **cuadro de búsqueda**, escriba **carreras**.

    ![Galería de aplicación] (./media/active-directory-saas-rally-software-tutorial/IC769526.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **Rally Software**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Rally Software] (./media/active-directory-saas-rally-software-tutorial/IC769527.png "Rally Software")
##<a name="configuring-single-sign-on"></a>Configurar el inicio de sesión único
  
El objetivo de esta sección es describen cómo habilitar los usuarios autenticar al Software Rally con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
Como parte de este procedimiento, debe cargar un certificado en Rally Software.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación de **Software Rally **, haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar inicio de sesión único] (./media/active-directory-saas-rally-software-tutorial/IC749323.png "Configurar inicio de sesión único")

2.  En la página **¿cómo le gustaría usuarios a iniciar sesión carreras** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Inicio de sesión único de AD en Microsoft Azure] (./media/active-directory-saas-rally-software-tutorial/IC769528.png "Inicio de sesión único de AD en Microsoft Azure")

3.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto **Dirección URL Rally inquilino de Software** , escriba la dirección URL con el siguiente patrón "*https://\<nombre del inquilino\>. rally.com*" y, a continuación, haga clic en **siguiente**.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-rally-software-tutorial/IC769529.png "Configurar la dirección URL de la aplicación")

4.  En la página **configurar inicio de sesión único en carreras** , haga clic en descargar metadatos y, a continuación, guárdelo en el equipo.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-rally-software-tutorial/IC769530.png "Configurar inicio de sesión único")

5.  Inicie sesión en su inquilino **Rally Software** .

6.  En la barra de herramientas en la parte superior, haga clic en **configuración**y, a continuación, seleccione la **suscripción**.

    ![Suscripción] (./media/active-directory-saas-rally-software-tutorial/IC769531.png "Suscripción")

7.  Haga clic en el botón de **acción** en la barra de herramientas en la parte superior de la derecha y, a continuación, seleccione **Editar suscripción**.

8.  En la página de diálogo **suscripción** , realice los pasos siguientes y, a continuación, haga clic en **Guardar y cerrar**:

    ![Autenticación] (./media/active-directory-saas-rally-software-tutorial/IC769542.png "Autenticación")

    1.  Seleccione **autenticación carreras o SSO** en la lista desplegable de autenticación
    2.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en Rally Software** , copie el valor del **Identificador de proveedor de servicios de identidad** y péguelo en el cuadro de texto de la **Dirección URL del proveedor de identidades**
    3.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en Rally Software** , copie el valor de **Dirección URL de cierre de sesión remota** .

9.  En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar inicio de sesión único] (./media/active-directory-saas-rally-software-tutorial/IC769547.png "Configurar inicio de sesión único")
##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
Para que usuarios AAD poder iniciar sesión, debe contar con la aplicación de Software Rally con sus nombres de usuario de Azure Active Directory.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar el aprovisionamiento de usuario, siga estos pasos:

1.  Inicie sesión en su inquilino Rally Software.

2.  Vaya a **instalación \> usuarios**y, a continuación, haga clic en **+ Add New**.

    ![Usuarios] (./media/active-directory-saas-rally-software-tutorial/IC781039.png "Usuarios")

3.  Escriba el nombre en el cuadro de texto nuevo usuario y, a continuación, haga clic en **Agregar con detalles**.

4.  En la sección **Crear usuario** , siga estos pasos:

    ![Crear usuario] (./media/active-directory-saas-rally-software-tutorial/IC781040.png "Crear usuario")

    1.  En el cuadro de texto **Nombre de usuario** , escriba el nombre del usuario de Azure AD que desee aprovisionar.
    2.  En el cuadro de texto **Dirección de correo electrónico** , escriba la dirección de correo electrónico del usuario de Azure AD que desee aprovisionar.
    3.  Haga clic en **Guardar y cerrar**.

>[AZURE.NOTE]Puede usar cualquier otros Software Rally usuario cuenta herramientas de creación o API proporcionan por Software Rally a cuentas de usuario AAD de aprovisionamiento.

##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-rally-software-perform-the-following-steps"></a>Para asignar a los usuarios a Rally Software, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación de **Software Rally** , haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-rally-software-tutorial/IC769548.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-rally-software-tutorial/IC767830.png "Sí")
  
Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).





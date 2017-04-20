<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con ponerme Thoughtworks | Microsoft Azure" 
    description="Aprenda a usar Thoughtworks ponerme con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más." 
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

#<a name="tutorial-azure-active-directory-integration-with-thoughtworks-mingle"></a>Tutorial: Integración de Azure Active Directory con ponerme Thoughtworks
  
El objetivo de este tutorial es mostrar la integración de Azure y Thoughtworks ponerme.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inquilino ponerme Thoughtworks
  
El escenario descrito en este tutorial se compone de los siguientes bloques de creación:

1.  Habilitar la integración de la aplicación para ponerme Thoughtworks
2.  Configuración de inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785150.png "Escenario")

##<a name="enabling-the-application-integration-for-thoughtworks-mingle"></a>Habilitar la integración de la aplicación para ponerme Thoughtworks
  
El objetivo de esta sección es describen cómo habilitar la integración de la aplicación para ponerme Thoughtworks.

###<a name="to-enable-the-application-integration-for-thoughtworks-mingle-perform-the-following-steps"></a>Para habilitar la integración de la aplicación para ponerme Thoughtworks, siga estos pasos:

1.  En el portal de clásico Azure, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC749322.png "Agregar una aplicación de gallerry")

6.  En el **cuadro de búsqueda**, escriba **ponerme thoughtworks**.

    ![Galería de aplicación] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785151.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **Ponerme Thoughtworks**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Ponerme Thoughtworks] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785152.png "Ponerme Thoughtworks")

##<a name="configuring-single-sign-on"></a>Configuración de inicio de sesión único
  
El objetivo de esta sección es describen cómo habilitar los usuarios autenticar ponerme Thoughtworks con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
Como parte de este procedimiento, debe cargar un certificado para Thoughtworks ponerme en.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página **Thoughtworks ponerme **integración de aplicación, haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar inicio de sesión único] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785153.png "Configurar inicio de sesión único")

2.  En la página **¿cómo le gustaría usuarios a iniciar sesión Thoughtworks ponerme** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785154.png "Configurar inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto **Dirección URL de Thoughtworks ponerme inquilino** , escriba la dirección URL con el siguiente patrón "*http://company.mingle.thoughtworks.com*" y, a continuación, haga clic en **siguiente**.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785155.png "Configurar la dirección URL de la aplicación")

4.  En la página **configurar inicio de sesión único en ponerme Thoughtworks** , haga clic en descargar metadatos y, a continuación, guárdelo en el equipo.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785156.png "Configurar inicio de sesión único")

5.  Inicie sesión como administrador en el sitio de empresa **Thoughtworks ponerme** .

6.  Haga clic en la pestaña **administración** y, a continuación, haga clic en **Configuración de SSO**.

    ![Configuración SSO] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785157.png "Configuración SSO")

7.  En la sección **Configuración de SSO** , realice los pasos siguientes:

    ![Configuración SSO] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785158.png "Configuración SSO")

    1.  Para cargar el archivo de metadatos, haga clic en **Elegir archivo**.
    2.  Haga clic en **Guardar cambios**.

8.  En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar inicio de sesión único] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785159.png "Configurar inicio de sesión único")

##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
Para que usuarios AAD poder iniciar sesión, debe contar con la aplicación Thoughtworks ponerme con sus nombres de usuario de Azure Active Directory.  
En el caso de Thoughtworks ponerme, aprovisionamiento es una tarea manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar el aprovisionamiento de usuario, siga estos pasos:

1.  Inicie sesión como administrador en el sitio de empresa Thoughtworks ponerme.

2.  Haga clic en el **perfil**.

    ![El primer proyecto] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785160.png "El primer proyecto")

3.  Haga clic en la pestaña **administración** y, a continuación, haga clic en **usuarios**.

    ![Usuarios] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785161.png "Usuarios")

4.  Haga clic en **nuevo usuario**.

    ![Nuevo usuario] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785162.png "Nuevo usuario")

5.  En la página de diálogo **Nuevo usuario** , realice los pasos siguientes:

    ![Nuevo usuario] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785163.png "Nuevo usuario")

    1.  Escriba el **nombre de inicio de sesión**, **nombre para mostrar**, **Elija contraseña**, **Confirmar contraseña** de una cuenta AAD válida que desee aprovisionar en los cuadros de texto relacionados.
    2.  Como **tipo de usuario**, seleccione **usuario completo**.
    3.  Haga clic en **crear este perfil**.

>[AZURE.NOTE] Puede usar cualquier otros ponerme Thoughtworks usuario cuenta herramientas de creación o API proporcionan por Thoughtworks ponerme a cuentas de usuario AAD de aprovisionamiento.

##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-thoughtworks-mingle-perform-the-following-steps"></a>Para asignar a los usuarios a ponerme Thoughtworks, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página **Thoughtworks ponerme** integración de aplicación, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785164.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC767830.png "Sí")
  
Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

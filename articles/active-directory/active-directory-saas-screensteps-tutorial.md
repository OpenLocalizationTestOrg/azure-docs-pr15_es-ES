<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con ScreenSteps | Microsoft Azure" 
    description="Aprenda a usar ScreenSteps con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más." 
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

#<a name="tutorial-azure-active-directory-integration-with-screensteps"></a>Tutorial: Integración de Azure Active Directory con ScreenSteps
  
El objetivo de este tutorial es mostrar la integración de Azure y ScreenSteps.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inquilino ScreenSteps
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a ScreenSteps será capaz de inicio de sesión único en la aplicación en el sitio de su empresa de ScreenSteps (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
El escenario descrito en este tutorial se compone de los siguientes bloques de creación:

1.  Habilitar la integración de aplicación para ScreenSteps
2.  Configuración de inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-screensteps-tutorial/IC778516.png "Escenario")
##<a name="enabling-the-application-integration-for-screensteps"></a>Habilitar la integración de aplicación para ScreenSteps
  
El objetivo de esta sección es describen cómo habilitar la integración de aplicación para ScreenSteps.

###<a name="to-enable-the-application-integration-for-screensteps-perform-the-following-steps"></a>Para habilitar la integración de aplicación para ScreenSteps, siga estos pasos:

1.  En el portal de clásico Azure, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-screensteps-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-screensteps-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-screensteps-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-screensteps-tutorial/IC749322.png "Agregar una aplicación de gallerry")

6.  En el **cuadro de búsqueda**, escriba **ScreenSteps**.

    ![Galería de aplicación] (./media/active-directory-saas-screensteps-tutorial/IC778517.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **ScreenSteps**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![ScreenSteps] (./media/active-directory-saas-screensteps-tutorial/IC778518.png "ScreenSteps")
##<a name="configuring-single-sign-on"></a>Configuración de inicio de sesión único
  
El objetivo de esta sección es describen cómo habilitar los usuarios autenticar a ScreenSteps con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación **ScreenSteps** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar inicio de sesión único] (./media/active-directory-saas-screensteps-tutorial/IC778519.png "Configurar inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión ScreenSteps** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-screensteps-tutorial/IC778520.png "Configurar inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto **ScreenSteps inicio de sesión en la URL** , escriba la dirección URL con el siguiente patrón "https://*\<nombre del inquilino\>. ScreenSteps.com*"y, a continuación, haga clic en **siguiente**.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-screensteps-tutorial/IC778521.png "Configurar la dirección URL de la aplicación")

4.  En la página **configurar inicio de sesión único en ScreenSteps** , para descargar el certificado, haga clic en **Descargar certificado**y, a continuación, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-screensteps-tutorial/IC778522.png "Configurar inicio de sesión único")

5.  En una ventana de explorador web diferente, inicie sesión en el sitio de su empresa ScreenSteps como administrador.

6.  Haga clic en **administración de cuentas**.

    ![Administración de cuentas] (./media/active-directory-saas-screensteps-tutorial/IC778523.png "Administración de cuentas")

7.  Haga clic en **autenticación remota**.

    ![Autenticación remota] (./media/active-directory-saas-screensteps-tutorial/IC778524.png "Autenticación remota")

8.  Haga clic en **crear extremo de autenticación**.

    ![Autenticación remota] (./media/active-directory-saas-screensteps-tutorial/IC778525.png "Autenticación remota")

9.  En la sección **crear un extremo de autenticación** , realice los pasos siguientes:

    ![Crear un extremo de autenticación] (./media/active-directory-saas-screensteps-tutorial/IC778526.png "Crear un extremo de autenticación")

    1.  En el cuadro de texto **título** , escriba un título.
    2.  En la lista **modo** , seleccione **SAML**.
    3.  Haga clic en **crear**.

10. En la sección de **Extremo de autenticación remoto** , siga estos pasos:

    ![Extremo de autenticación remota] (./media/active-directory-saas-screensteps-tutorial/IC778527.png "Extremo de autenticación remota")

    1.  En el portal de clásico Azure, en la página **configurar inicio de sesión único en ScreenSteps** , copie el valor de **Dirección URL de inicio de sesión remota** y, a continuación, péguelo en el cuadro de texto **Dirección URL de inicio de sesión remota** .
    2.  En el portal de clásico Azure, en la página **configurar inicio de sesión único en ScreenSteps** , copie el valor de **Dirección URL de cierre de sesión remota** y, a continuación, péguelo en el cuadro de texto **dirección URL de cerrar la sesión** .
    3.  Haga clic en **Elegir un archivo**y, a continuación, cargar el certificado descargado.
    4.  Haga clic en **Actualizar**.

11. En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar inicio de sesión único] (./media/active-directory-saas-screensteps-tutorial/IC778542.png "Configurar inicio de sesión único")
##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD iniciar sesión en **ScreenSteps**, debe aprovisionar en **ScreenSteps**.  
En el caso de **ScreenSteps**, aprovisionamiento es una tarea manual.

###<a name="to-provision-a-user-account-to-screensteps-perform-the-following-steps"></a>Para crear una cuenta de usuario a ScreenSteps, siga estos pasos:

1.  Inicie sesión en su inquilino **ScreenSteps** .

2.  Haga clic en **administración de cuentas**.

    ![Administración de cuentas] (./media/active-directory-saas-screensteps-tutorial/IC778523.png "Administración de cuentas")

3.  Haga clic en **usuarios**.

    ![Usuarios] (./media/active-directory-saas-screensteps-tutorial/IC778544.png "Usuarios")

4.  Haga clic en **crear un usuario**.

    ![Todos los usuarios] (./media/active-directory-saas-screensteps-tutorial/IC778545.png "Todos los usuarios")

5.  En la lista de **Funciones de usuario** , seleccione una función para el usuario.

6.  En la sección roles de usuario, escriba el**"nombre**, **apellido**, **correo electrónico**, **Inicio de sesión**, **contraseña** y la **Confirmación"**de una cuenta AAD válida que desee aprovisionar en los cuadros de texto relacionados.

    ![Nuevo usuario] (./media/active-directory-saas-screensteps-tutorial/IC778546.png "Nuevo usuario")

7.  En la sección grupos, seleccione el **Grupo de autenticación (SAML)**y, a continuación, haga clic en **Crear usuario**.

    ![Grupos] (./media/active-directory-saas-screensteps-tutorial/IC778547.png "Grupos")

>[AZURE.NOTE]Puede usar cualquier otros ScreenSteps usuario cuenta herramientas de creación o API proporcionan por ScreenSteps a cuentas de usuario AAD de aprovisionamiento.

##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-screensteps-perform-the-following-steps"></a>Para asignar a los usuarios a ScreenSteps, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación **ScreenSteps **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-screensteps-tutorial/IC773094.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Asignar usuarios] (./media/active-directory-saas-screensteps-tutorial/IC778548.png "Asignar usuarios")
  
Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
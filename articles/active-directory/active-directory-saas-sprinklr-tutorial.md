<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Sprinklr | Microsoft Azure" 
    description="Aprenda a usar Sprinklr con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más." 
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
    ms.date="09/19/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-sprinklr"></a>Tutorial: Integración de Azure Active Directory con Sprinklr
  
El objetivo de este tutorial es mostrar la integración de Azure y Sprinklr.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inquilino Sprinklr
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Sprinklr será capaz de inicio de sesión único en la aplicación en el sitio de su empresa de Sprinklr (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
El escenario descrito en este tutorial se compone de los siguientes bloques de creación:

1.  Habilitar la integración de aplicación para Sprinklr
2.  Configuración de inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-sprinklr-tutorial/IC782900.png "Escenario")

##<a name="enabling-the-application-integration-for-sprinklr"></a>Habilitar la integración de aplicación para Sprinklr
  
El objetivo de esta sección es describen cómo habilitar la integración de aplicación para Sprinklr.

###<a name="to-enable-the-application-integration-for-sprinklr-perform-the-following-steps"></a>Para habilitar la integración de aplicación para Sprinklr, siga estos pasos:

1.  En el portal de clásico Azure, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-sprinklr-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-sprinklr-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-sprinklr-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-sprinklr-tutorial/IC749322.png "Agregar una aplicación de gallerry")

6.  En el **cuadro de búsqueda**, escriba **Sprinklr**.

    ![Galería de aplicación] (./media/active-directory-saas-sprinklr-tutorial/IC782901.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **Sprinklr**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Sprinklr] (./media/active-directory-saas-sprinklr-tutorial/IC782902.png "Sprinklr")

##<a name="configuring-single-sign-on"></a>Configuración de inicio de sesión único
  
El objetivo de esta sección es describen cómo habilitar los usuarios autenticar a Sprinklr con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
Como parte de este procedimiento, es necesario para crear un archivo de certificado codificado de base 64.  
Si no está familiarizado con este procedimiento, vea [cómo convertir un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación **Sprinklr** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar inicio de sesión único] (./media/active-directory-saas-sprinklr-tutorial/IC782903.png "Configurar inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión Sprinklr** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-sprinklr-tutorial/IC782904.png "Configurar inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto **Sprinklr inicio de sesión en la URL** , escriba la dirección URL con el siguiente patrón "*https://\<nombre del inquilino\>. sprinklr.com*" y, a continuación, haga clic en **siguiente**.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-sprinklr-tutorial/IC782905.png "Configurar la dirección URL de la aplicación")

4.  En la página **configurar inicio de sesión único en Sprinklr** , para descargar el certificado, haga clic en **Descargar certificado**y, a continuación, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-sprinklr-tutorial/IC782906.png "Configurar inicio de sesión único")

5.  En una ventana de explorador web diferente, inicie sesión en el sitio de su empresa Sprinklr como administrador.

6.  Vaya a **administración \> configuración**.

    ![Administración] (./media/active-directory-saas-sprinklr-tutorial/IC782907.png "Administración")

7.  Vaya a **Administrar Partner \> inicio de sesión único** en el panel izquierdo.

    ![Administrar Partner] (./media/active-directory-saas-sprinklr-tutorial/IC782908.png "Administrar Partner")

8.  Haga clic en **+ Agregar inicios de sesión único**.

    ![Inicio de sesión único] (./media/active-directory-saas-sprinklr-tutorial/IC782909.png "Inicio de sesión único")

9.  En la página de **Inicio de sesión único** , realice los pasos siguientes:

    ![Inicio de sesión único] (./media/active-directory-saas-sprinklr-tutorial/IC782910.png "Inicio de sesión único")

    1.  En el cuadro de texto **nombre** , escriba un nombre para la configuración (por ejemplo: *WAADSSOTest*).
    2.  Seleccione **habilitado**.
    3.  Seleccione **Usar nueva SSO certificado**.
    4.  Crear un archivo **codificado base 64** de certificado descargado.  

        >[AZURE.TIP] Para obtener más detalles, consulte [cómo convertir un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    5.  Abra el certificado codificado de base 64 en el Bloc de notas, copie el contenido de la misma en el Portapapeles y, a continuación, péguela en el cuadro de texto de **Certificado del proveedor de identidades**
    6.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en Sprinklr** , copie el valor del **Identificador de proveedor de servicios de identidad** y péguelo en el cuadro de texto **Id. de entidad** .
    7.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en Sprinklr** , copie el valor de **Dirección URL de inicio de sesión remota** y, a continuación, péguelo en el cuadro de texto **Dirección URL de inicio de sesión del proveedor de identidades** .
    8.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en Sprinklr** , copie el valor de **Dirección URL de cierre de sesión remota** y, a continuación, péguelo en el cuadro de texto **Dirección URL de cierre de sesión del proveedor de identidades** .
    9.  Como **Tipo de identificador de usuario de SAML**, seleccione **aserción contiene usuario "nombre de usuario de s sprinklr.com**.
    10. Como **Ubicación de ID de usuario de SAML**, seleccione **ID de usuario está en el elemento de identificador de nombre de la instrucción de asunto**.
    11. Cierre **Guardar**.

        ![SAML] (./media/active-directory-saas-sprinklr-tutorial/IC782911.png "SAML")

10. En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar inicio de sesión único] (./media/active-directory-saas-sprinklr-tutorial/IC782912.png "Configurar inicio de sesión único")

##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
Para que usuarios AAD poder iniciar sesión, debe aprovisionar acceso dentro de la aplicación Sprinklr.  
Esta sección describe cómo crear cuentas de usuario AAD dentro de Sprinklr.

###<a name="to-provision-a-user-account-in-sprinklr-perform-the-following-steps"></a>Para crear una cuenta de usuario en Sprinklr, siga estos pasos:

1.  Inicie sesión en el sitio de su empresa Sprinklr como administrador.

2.  Vaya a **administración \> configuración**.

    ![Administración] (./media/active-directory-saas-sprinklr-tutorial/IC782907.png "Administración")

3.  Vaya a **cliente administrar \> usuarios** desde el panel izquierdo.

    ![Configuración] (./media/active-directory-saas-sprinklr-tutorial/IC782914.png "Configuración")

4.  Haga clic en **Agregar usuario**.

    ![Configuración] (./media/active-directory-saas-sprinklr-tutorial/IC782915.png "Configuración")

5.  En el cuadro de diálogo **Editar usuario** , siga estos pasos:

    ![Editar usuario] (./media/active-directory-saas-sprinklr-tutorial/IC782916.png "Editar usuario")

    1.  En el **correo electrónico**, cuadros de texto **nombre** y **Apellido** , escriba la información de una cuenta de usuario de Azure AD desee aprovisionar.
    2.  Seleccione **contraseña deshabilitado**.
    3.  Seleccione un **idioma**.
    4.  Seleccione un **tipo de usuario**.
    5.  Haga clic en **Actualizar**.

    >[AZURE.IMPORTANT] **Contraseña deshabilitado** debe estar activada para habilitar un usuario iniciar sesión a través de un proveedor de identidades.

6.  Vaya a la **función**y, a continuación, realice los pasos siguientes:

    ![Funciones de Partner] (./media/active-directory-saas-sprinklr-tutorial/IC782917.png "Funciones de Partner")

    1.  En la lista **Global** , seleccione **todos los\_permisos**.
    2.  Haga clic en **Actualizar**.

>[AZURE.NOTE] Puede usar cualquier otros Sprinklr usuario cuenta herramientas de creación o API proporcionan por Sprinklr proporcionando cuentas de usuario de Azure AD.

##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-sprinklr-perform-the-following-steps"></a>Para asignar a los usuarios a Sprinklr, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación **Sprinklr **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-sprinklr-tutorial/IC782918.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-sprinklr-tutorial/IC767830.png "Sí")
  
Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
<properties
    pageTitle="Tutorial: Integración de Azure Active Directory con lienzo LMS | Microsoft Azure" 
    description="¡Obtenga información sobre cómo utilizar lienzo LMS con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más!" 
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

#<a name="tutorial-azure-active-directory-integration-with-canvas-lms"></a>Tutorial: Integración de Azure Active Directory con lienzo LMS

El objetivo de este tutorial es mostrar la integración de Azure y lienzo.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inquilino lienzo

Después de completar este tutorial, los usuarios de Azure AD que ha asignado al lienzo podrán al inicio de sesión único en la aplicación en el sitio de su empresa de lienzo (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

El escenario descrito en este tutorial se compone de los siguientes bloques de creación:

1.  Habilitar la integración de la aplicación para lienzo
2.  Configuración de inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-canvas-lms-tutorial/IC775984.png "Escenario")
##<a name="enabling-the-application-integration-for-canvas"></a>Habilitar la integración de la aplicación para lienzo

El objetivo de esta sección es describen cómo habilitar la integración de aplicaciones para el lienzo.

###<a name="to-enable-the-application-integration-for-canvas-perform-the-following-steps"></a>Para habilitar la integración de la aplicación para lienzo, realice los pasos siguientes:

1.  En el portal de clásico Azure, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-canvas-lms-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-canvas-lms-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-canvas-lms-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-canvas-lms-tutorial/IC749322.png "Agregar una aplicación de gallerry")

6.  En el **cuadro de búsqueda**, escriba el **lienzo**.

    ![Galería de aplicación] (./media/active-directory-saas-canvas-lms-tutorial/IC775985.png "Galería de aplicación")

7.  En el panel de resultados, seleccione el **lienzo**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Lienzo] (./media/active-directory-saas-canvas-lms-tutorial/IC775986.png "Lienzo")
##<a name="configuring-single-sign-on"></a>Configuración de inicio de sesión único

El objetivo de esta sección es describen cómo habilitar los usuarios autenticar al lienzo con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
Configuración de inicio de sesión único para lienzo requiere que recuperar un valor de la huella digital de un certificado.  
Si no está familiarizado con este procedimiento, consulte [cómo recuperar el valor de la huella digital de un certificado](http://youtu.be/YKQF266SAxI)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación **lienzo** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar inicio de sesión único] (./media/active-directory-saas-canvas-lms-tutorial/IC771709.png "Configurar inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión el lienzo** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-canvas-lms-tutorial/IC775987.png "Configurar el inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto del **Lienzo de inicio de sesión en la URL** , escriba la dirección URL con el siguiente patrón `https://<tenant-name>.instructure.com`y, a continuación, haga clic en **siguiente**.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-canvas-lms-tutorial/IC775988.png "Configurar la dirección URL de la aplicación")

4.  En la página **configurar inicio de sesión único en el lienzo** , para descargar el certificado, haga clic en **Descargar certificado**y, a continuación, guarde el archivo de certificado localmente en el equipo.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-canvas-lms-tutorial/IC775989.png "Configurar el inicio de sesión único")

5.  En una ventana de explorador web diferente, inicie sesión en el sitio de su empresa lienzo como administrador.

6.  Vaya a **cursos \> las cuentas administradas \> Microsoft**.

    ![Lienzo] (./media/active-directory-saas-canvas-lms-tutorial/IC775990.png "Lienzo")

7.  En el panel de navegación de la izquierda, seleccione **autenticación**y, a continuación, haga clic en **Agregar nueva configuración de SAML**.

    ![Autenticación] (./media/active-directory-saas-canvas-lms-tutorial/IC775991.png "Autenticación")

8.  En la página de integración actual, realice los pasos siguientes:

    ![Integración actual] (./media/active-directory-saas-canvas-lms-tutorial/IC775992.png "Integración actual")

    1.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en el lienzo** , copie el valor de **Id. de entidad** y péguelo en el cuadro de texto **ID de entidad IdP** .
    2.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en el lienzo** , copie el valor de **Dirección URL de inicio de sesión remota** y péguelo en el cuadro de texto de **Registro en la dirección URL** .
    3.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en el lienzo** , copie el valor de **Dirección URL de inicio de sesión remota** y péguelo en el cuadro de texto **Dirección URL de fuera de registro** .
    4.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en el lienzo** , copie el valor de **Dirección URL de cambiar contraseña** y, a continuación, péguelo en el cuadro de texto de **Vínculo de cambiar contraseña** .
    5.  Copie el valor de la **huella digital** del certificado exportado y, a continuación, péguelo en el cuadro de texto de la **Huella digital de certificado** .  

        >[AZURE.TIP] Para obtener más detalles, consulte [cómo recuperar el valor de la huella digital de un certificado](http://youtu.be/YKQF266SAxI)

    6.  En la lista de **Atributos de inicio de sesión** , seleccione **NameID**.
    7.  En la lista **Formato de identificador** , seleccione **dirección de correo electrónico**.
    8.  Haga clic en **Guardar la configuración de autenticación**.

9.  En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-canvas-lms-tutorial/IC775993.png "Configurar el inicio de sesión único")
##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD iniciar sesión en el lienzo, debe aprovisionar en el lienzo.  
En el caso de lienzo, aprovisionamiento es una tarea manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para crear cuentas de usuario, siga estos pasos:

1.  Inicie sesión en su inquilino de **lienzo** .

2.  Vaya a **cursos \> las cuentas administradas \> Microsoft**.

    ![Lienzo] (./media/active-directory-saas-canvas-lms-tutorial/IC775990.png "Lienzo")

3.  Haga clic en **usuarios**.

    ![Usuarios] (./media/active-directory-saas-canvas-lms-tutorial/IC775995.png "Usuarios")

4.  Haga clic en **Agregar nuevo usuario**.

    ![Usuarios] (./media/active-directory-saas-canvas-lms-tutorial/IC775996.png "Usuarios")

5.  En la página Agregar un usuario nuevo cuadro de diálogo, realice los pasos siguientes:

    ![Agregar usuario] (./media/active-directory-saas-canvas-lms-tutorial/IC775997.png "Agregar usuario")

    1.  En el cuadro de texto **Nombre completo** , escriba el nombre del usuario.
    2.  En el cuadro de texto de **correo electrónico** , escriba la dirección de correo electrónico del usuario.
    3.  En el cuadro de texto de **Inicio de sesión** , escriba la dirección de correo electrónico de Azure AD del usuario.
    4.  Seleccione **el usuario sobre la creación de cuentas de correo electrónico**.
    5.  Haga clic en **Agregar usuario**.

>[AZURE.NOTE] Puede usar cualquier otros lienzo usuario cuenta herramientas de creación o API proporcionan por lienzo a cuentas de usuario AAD de aprovisionamiento.

##<a name="assigning-users"></a>Asignación de usuarios

Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-canvas-perform-the-following-steps"></a>Para asignar a los usuarios al lienzo, realice los pasos siguientes:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación **lienzo **, haga clic en **asignar a los usuarios**.

    ![Asignación de usuarios] (./media/active-directory-saas-canvas-lms-tutorial/IC775998.png "Asignación de usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-canvas-lms-tutorial/IC767830.png "Sí")

Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

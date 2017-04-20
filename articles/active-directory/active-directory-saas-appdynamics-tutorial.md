<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con AppDynamics | Microsoft Azure" 
    description="Aprenda a usar AppDynamics con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más." 
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

#<a name="tutorial-azure-active-directory-integration-with-appdynamics"></a>Tutorial: Integración de Azure Active Directory con AppDynamics

El objetivo de este tutorial es mostrar la integración de Azure y AppDynamics. El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inicio de sesión único de AppDynamics activado suscripción

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a AppDynamics será capaz de inicio de sesión único en la aplicación en el sitio de su empresa de AppDynamics (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

El escenario descrito en este tutorial consta de los siguientes elementos:

1.  Habilitar la integración de aplicación para AppDynamics
2.  Configurar el inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-appdynamics-tutorial/IC790209.png "Escenario")
##<a name="enabling-the-application-integration-for-appdynamics"></a>Habilitar la integración de aplicación para AppDynamics

El objetivo de esta sección es describen cómo habilitar la integración de aplicación para AppDynamics.

###<a name="to-enable-the-application-integration-for-appdynamics-perform-the-following-steps"></a>Para habilitar la integración de aplicación para AppDynamics, siga estos pasos:

1.  En el portal clásico Azure, en el panel de navegación de la izquierda, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-appdynamics-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-appdynamics-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-appdynamics-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-appdynamics-tutorial/IC749322.png "Agregar una aplicación de muestra")

6.  En el **cuadro de búsqueda**, escriba **AppDynamics**.

    ![Galería de aplicación] (./media/active-directory-saas-appdynamics-tutorial/IC790210.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **AppDynamics**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![AppDynamics] (./media/active-directory-saas-appdynamics-tutorial/IC790211.png "AppDynamics")
##<a name="configuring-single-sign-on"></a>Configurar el inicio de sesión único

El objetivo de esta sección es describen cómo habilitar los usuarios autenticar a AppDynamics con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
Como parte de este procedimiento, es necesario para crear un archivo de certificado codificado de base 64.  
Si no está familiarizado con este procedimiento, vea [cómo convertir un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación **AppDynamics** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-appdynamics-tutorial/IC790212.png "Configurar el inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión AppDynamics** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-appdynamics-tutorial/IC790213.png "Configurar el inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto **AppDynamics inicio de sesión en la URL** , escriba la dirección URL de utilizar los usuarios para el inicio de sesión en AppDynamics ("*https://companyname.saas.appdynamics.com*") y, a continuación, haga clic en **siguiente**.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-appdynamics-tutorial/IC790214.png "Configurar la dirección URL de la aplicación")

4.  En la página **configurar inicio de sesión único en AppDynamics** , para descargar el certificado, haga clic en **Descargar certificado**y, a continuación, guarde el archivo de certificado en el equipo.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-appdynamics-tutorial/IC790215.png "Configurar el inicio de sesión único")

5.  En una ventana de explorador web diferente, inicie sesión en el sitio de su empresa AppDynamics como administrador.

6.  En la barra de herramientas en la parte superior, haga clic en **configuración**y, a continuación, haga clic en **administración**.

    ![Administración] (./media/active-directory-saas-appdynamics-tutorial/IC790216.png "Administración")

7.  Haga clic en la ficha de **Proveedor de autenticación** .

    ![Proveedor de autenticación] (./media/active-directory-saas-appdynamics-tutorial/IC790224.png "Proveedor de autenticación")

8.  En la sección **Proveedor de autenticación** , realice los pasos siguientes:

    ![Configuración de SAML] (./media/active-directory-saas-appdynamics-tutorial/IC790225.png "Configuración de SAML")

    1.  Como **Proveedor de autenticación**, seleccione **SAML**.
    2.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en AppDynamics** , copie el valor de **Dirección URL de inicio de sesión remota** y, a continuación, péguelo en el cuadro de texto **Dirección URL de inicio de sesión** .
    3.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en AppDynamics** , copie el valor de **Dirección URL de cierre de sesión remota** y, a continuación, péguelo en el cuadro de texto **Dirección URL de cierre de sesión** .
    4.  Crear un archivo **codificado base 64** de certificado descargado.  

        >[AZURE.TIP] Para obtener más detalles, consulte [cómo convertir un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    5.  Abra el certificado codificado de base 64 en el Bloc de notas, copie el contenido de la misma en el Portapapeles y, a continuación, péguela en el cuadro de texto de **certificado**
    6.  Haga clic en **Guardar**.
        ![Guardar] (./media/active-directory-saas-appdynamics-tutorial/IC777673.png "Guardar")

9.  En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-appdynamics-tutorial/IC790226.png "Configurar el inicio de sesión único")
##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD iniciar sesión en AppDynamics, debe aprovisionar en AppDynamics.  
En el caso de AppDynamics, aprovisionamiento es una tarea manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar el aprovisionamiento de usuario, siga estos pasos:

1.  Inicie sesión en el sitio de su empresa AppDynamics como administrador.

2.  Vaya a **usuarios**y, a continuación, haga clic en **+** para abrir el cuadro de diálogo **Crear usuario** .

    ![Usuarios] (./media/active-directory-saas-appdynamics-tutorial/IC790229.png "Usuarios")

3.  En la sección **Crear usuario** , siga estos pasos:

    ![Crear usuario] (./media/active-directory-saas-appdynamics-tutorial/IC790230.png "Crear usuario")

    1.  Escriba el **nombre de usuario**, **nombre**, **correo electrónico**, **Nueva contraseña**y **Repetir nueva contraseña** de una cuenta AAD válida que desee aprovisionar en los cuadros de texto relacionados.
    2.  Haga clic en **Guardar**.

>[AZURE.NOTE] Puede usar cualquier otros AppDynamics usuario cuenta herramientas de creación o API proporcionan por AppDynamics proporcionando cuentas de usuario de Azure AD.

##<a name="assigning-users"></a>Asignación de usuarios

Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-appdynamics-perform-the-following-steps"></a>Para asignar a los usuarios a AppDynamics, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación **AppDynamics **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-appdynamics-tutorial/IC790231.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-appdynamics-tutorial/IC767830.png "Sí")

Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

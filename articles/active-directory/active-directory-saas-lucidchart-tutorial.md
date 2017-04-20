<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Lucidchart | Microsoft Azure" 
    description="Aprenda a usar Lucidchart con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más." 
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

#<a name="tutorial-azure-active-directory-integration-with-lucidchart"></a>Tutorial: Integración de Azure Active Directory con Lucidchart
  
El objetivo de este tutorial es mostrar la integración de Azure y Lucidchart.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inicio de sesión único de Lucidchart activado suscripción
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Lucidchart será capaz de inicio de sesión único en la aplicación en el sitio de su empresa de Lucidchart (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
El escenario descrito en este tutorial consta de los siguientes elementos:

1.  Habilitar la integración de aplicación para Lucidchart
2.  Configurar el inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-lucidchart-tutorial/IC791183.png "Escenario")
##<a name="enabling-the-application-integration-for-lucidchart"></a>Habilitar la integración de aplicación para Lucidchart
  
El objetivo de esta sección es describen cómo habilitar la integración de aplicación para Lucidchart.

###<a name="to-enable-the-application-integration-for-lucidchart-perform-the-following-steps"></a>Para habilitar la integración de aplicación para Lucidchart, siga estos pasos:

1.  En el portal clásico Azure, en el panel de navegación de la izquierda, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-lucidchart-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-lucidchart-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-lucidchart-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-lucidchart-tutorial/IC749322.png "Agregar una aplicación de muestra")

6.  En el **cuadro de búsqueda**, escriba **Lucidchart**.

    ![Galería de aplicación] (./media/active-directory-saas-lucidchart-tutorial/IC791184.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **Lucidchart**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Lucidchart] (./media/active-directory-saas-lucidchart-tutorial/IC791185.png "Lucidchart")
##<a name="configuring-single-sign-on"></a>Configurar el inicio de sesión único
  
El objetivo de esta sección es describen cómo habilitar los usuarios autenticar a Lucidchart con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación **Lucidchart** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-lucidchart-tutorial/IC791186.png "Configurar el inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión Lucidchart** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-lucidchart-tutorial/IC791187.png "Configurar el inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto **Lucidchart inicio de sesión en la URL** , escriba la dirección URL utilizada por los usuarios para iniciar sesión la aplicación Lucidchart (por ejemplo: "*https://chart2.office.lucidchart.com/saml/sso/azure*") y, a continuación, haga clic en **siguiente**.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-lucidchart-tutorial/IC791188.png "Configurar la dirección URL de la aplicación")

4.  En la página **configurar inicio de sesión único en Lucidchart** , para descargar los metadatos, haga clic en **descargar metadatos**y, a continuación, guarde el archivo de datos local en el equipo.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-lucidchart-tutorial/IC791189.png "Configurar el inicio de sesión único")

5.  En una ventana de explorador web diferente, inicie sesión en el sitio de su empresa Lucidchart como administrador.

6.  En el menú de la parte superior, haga clic en **grupo**.

    ![Grupo] (./media/active-directory-saas-lucidchart-tutorial/IC791190.png "Grupo")

7.  Haga clic en **aplicación \> administrar SAML**.

    ![Administrar SAML] (./media/active-directory-saas-lucidchart-tutorial/IC791191.png "Administrar SAML")

8.  En la página de diálogo **Configuración de autenticación de SAML** , realice los pasos siguientes:

    1.  Seleccione **Habilitar la autenticación de SAML**y, a continuación, haga clic en **opcional**.
        ![Configuración de autenticación de SAML] (./media/active-directory-saas-lucidchart-tutorial/IC791192.png "Configuración de autenticación de SAML")
    2.  En el cuadro de texto **dominio** , escriba el dominio y, a continuación, haga clic en **Cambiar certificado**.
        ![Cambiar certificado] (./media/active-directory-saas-lucidchart-tutorial/IC791193.png "Cambiar certificado")
    3.  Abra el archivo descargado metadatos, copiar el contenido y péguelo en el cuadro de texto de **Cargar metadatos** .
        ![Cargar metadatos] (./media/active-directory-saas-lucidchart-tutorial/IC791194.png "Cargar metadatos")
    4.  Seleccione **Agregar automáticamente un nuevo usuario al equipo**y, a continuación, haga clic en **Guardar cambios**.
        ![Guardar cambios] (./media/active-directory-saas-lucidchart-tutorial/IC791195.png "Guardar cambios")

9.  Seleccione la configuración de inicio de sesión único de confirmación y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-lucidchart-tutorial/IC791196.png "Configurar el inicio de sesión único")
##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
No hay ningún elemento de acción para configurar Lucidchart de aprovisionamiento de usuarios.  
Cuando un usuario asignado intenta iniciar sesión en Lucidchart mediante el panel de acceso, Lucidchart comprueba si existe el usuario.  
Si no hay ninguna cuenta de usuario aún, se crea automáticamente por Lucidchart.
##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-lucidchart-perform-the-following-steps"></a>Para asignar a los usuarios a Lucidchart, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación **Lucidchart **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-lucidchart-tutorial/IC791197.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-lucidchart-tutorial/IC767830.png "Sí")
  
Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
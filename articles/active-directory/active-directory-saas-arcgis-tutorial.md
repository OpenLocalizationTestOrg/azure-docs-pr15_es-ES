<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con ArcGIS | Microsoft Azure" 
    description="Aprenda a usar ArcGIS con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más." 
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

#<a name="tutorial-azure-active-directory-integration-with-arcgis"></a>Tutorial: Integración de Azure Active Directory con ArcGIS

El objetivo de este tutorial es mostrar la integración de Azure y ArcGIS. El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inicio de sesión único de ArcGIS activado suscripción

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a ArcGIS podrán al inicio de sesión único en la aplicación en el sitio de su empresa de ArcGIS (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

El escenario descrito en este tutorial se compone de los siguientes bloques de creación:

1.  Habilitar la integración de la aplicación para ArcGIS
2.  Configuración de inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-arcgis-tutorial/IC784735.png "Escenario")
##<a name="enabling-the-application-integration-for-arcgis"></a>Habilitar la integración de la aplicación para ArcGIS

El objetivo de esta sección es describen cómo habilitar la integración de la aplicación para ArcGIS.

###<a name="to-enable-the-application-integration-for-arcgis-perform-the-following-steps"></a>Para habilitar la integración de la aplicación para ArcGIS, realice los pasos siguientes:

1.  En el portal de clásico Azure, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-arcgis-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-arcgis-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-arcgis-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-arcgis-tutorial/IC749322.png "Agregar una aplicación de gallerry")

6.  En el **cuadro de búsqueda**, escriba **ArcGIS**.

    ![Galería de Applcation] (./media/active-directory-saas-arcgis-tutorial/IC784736.png "Galería de Applcation")

7.  En el panel de resultados, seleccione **ArcGIS**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![ArcGIS] (./media/active-directory-saas-arcgis-tutorial/IC784737.png "ArcGIS")
##<a name="configuring-single-sign-on"></a>Configuración de inicio de sesión único

El objetivo de esta sección es describen cómo habilitar los usuarios autenticar ArcGIS con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación **ArcGIS** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-arcgis-tutorial/IC784738.png "Configurar el inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión ArcGIS** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-arcgis-tutorial/IC784739.png "Configurar el inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto **ArcGIS inicio de sesión en la URL** , escriba la dirección URL utilizar los usuarios para iniciar sesión utilizando el modelo siguiente "*https://company.maps.arcgis.com*" y, a continuación, haga clic en **siguiente**.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-arcgis-tutorial/IC784740.png "Configurar la dirección URL de la aplicación")

4.  En la página **configurar inicio de sesión único en ArcGIS** , haga clic en **descargar metadatos**y, a continuación, guarde el archivo de metadatos localmente en el equipo.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-arcgis-tutorial/IC784741.png "Configurar el inicio de sesión único")

5.  En una ventana de explorador web diferente, inicie sesión en el sitio de su empresa ArcGIS como administrador.

6.  Haga clic en **Editar configuración**.

    ![Editar la configuración] (./media/active-directory-saas-arcgis-tutorial/IC784742.png "Editar la configuración")

7.  Haga clic en **seguridad**.

    ![Seguridad] (./media/active-directory-saas-arcgis-tutorial/IC784743.png "Seguridad")

8.  **Inicios de sesión de empresa**, haga clic en **Establecer el proveedor de identidades**.

    ![Inicios de sesión de empresa] (./media/active-directory-saas-arcgis-tutorial/IC784744.png "Inicios de sesión de empresa")

9.  En la página de configuración de **Establecer el proveedor de identidades** , realice los pasos siguientes:

    ![Establecer el proveedor de identidades] (./media/active-directory-saas-arcgis-tutorial/IC784745.png "Establecer el proveedor de identidades")

    1.  En el cuadro de texto Nombre, escriba el nombre de la organización.
    2.  **Que metadatos para el proveedor de identidades de empresa se proporcionará con**, seleccione **Un archivo**.
    3.  Para cargar el archivo descargado metadatos, haga clic en **Elegir archivo**.
    4.  Haga clic en **establecer el proveedor de identidades**.

10. En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-arcgis-tutorial/IC784746.png "Configurar el inicio de sesión único")
##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD iniciar sesión en ArcGIS, debe aprovisionar en ArcGIS.  
En el caso de ArcGIS, aprovisionamiento es una tarea manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar el aprovisionamiento de usuario, siga estos pasos:

1.  Inicie sesión en su inquilino **ArcGIS** .

2.  Haga clic en **Invitar a los miembros**.

    ![Invitar a miembros] (./media/active-directory-saas-arcgis-tutorial/IC784747.png "Invitar a miembros")

3.  Seleccione **Agregar miembros automáticamente sin enviar un correo electrónico**y, a continuación, haga clic en **siguiente**.

    ![Agregar a miembros automáticamente] (./media/active-directory-saas-arcgis-tutorial/IC784748.png "Agregar a miembros automáticamente")

4.  En la página de diálogo de **miembros** , realice los pasos siguientes:

    ![Agregar y revisión] (./media/active-directory-saas-arcgis-tutorial/IC784749.png "Agregar y revisión")

    1.  Escriba el **nombre**, **Apellido** y **correo electrónico** de una cuenta AAD válida que desee aprovisionar.
    2.  Haga clic en **Agregar y revisar**.

5.  Revise los datos que ha escrito y, a continuación, haga clic en **Agregar miembros**.

    ![Agregar miembro] (./media/active-directory-saas-arcgis-tutorial/IC784750.png "Agregar miembro")

>[AZURE.NOTE] Puede usar cualquier otros ArcGIS usuario cuenta herramientas de creación o API proporcionan por ArcGIS a cuentas de usuario AAD de aprovisionamiento.

##<a name="assigning-users"></a>Asignación de usuarios

Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-arcgis-perform-the-following-steps"></a>Para asignar a los usuarios a ArcGIS, realice los pasos siguientes:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación **ArcGIS **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-arcgis-tutorial/IC784751.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-arcgis-tutorial/IC767830.png "Sí")

Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

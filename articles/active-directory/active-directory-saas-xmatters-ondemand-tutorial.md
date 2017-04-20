<properties 
    pageTitle="Tutorial: Azure integración de Active Directory con xMatters petición | Microsoft Azure"
    description="¡Obtenga información sobre cómo usar xMatters petición con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más!" 
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
    ms.date="09/09/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-xmatters-ondemand"></a>Tutorial: Azure integración de Active Directory con xMatters petición
  
El objetivo de este tutorial es mostrar la integración de Azure y xMatters petición. El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inquilino de petición de xMatters
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a xMatters petición podrán al inicio de sesión único en la aplicación en el sitio de su empresa de petición de xMatters (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
El escenario descrito en este tutorial consta de los siguientes elementos:

1.  Habilitar la integración de aplicación para xMatters petición
2.  Configurar el inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776788.png "Escenario")

##<a name="enabling-the-application-integration-for-xmatters-ondemand"></a>Habilitar la integración de aplicación para xMatters petición
  
El objetivo de esta sección es describen cómo habilitar la integración de aplicación para xMatters petición.

###<a name="to-enable-the-application-integration-for-xmatters-ondemand-perform-the-following-steps"></a>Para habilitar la integración de aplicación para xMatters petición, realice los pasos siguientes:

1.  En el portal clásico Azure, en el panel de navegación de la izquierda, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC749322.png "Agregar una aplicación de muestra")

6.  En el **cuadro de búsqueda**, escriba **xMatters petición**.

    ![Galería de aplicación] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776789.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **Petición XMatters**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![petición de xMatters] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776790.png "petición de xMatters")

##<a name="configuring-single-sign-on"></a>Configurar el inicio de sesión único
  
El objetivo de esta sección es describen cómo habilitar los usuarios autenticar XMatters OnDemand con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación de **Petición de XMatters** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar inicio de sesión único] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776791.png "Configurar inicio de sesión único")

2.  En la página **¿cómo le gustaría usuarios a iniciar sesión XMatters petición** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776792.png "Configurar inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , realice los pasos siguientes:

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776793.png "Configurar la dirección URL de la aplicación")

    una. En el cuadro de texto **XMatters petición de inicio de sesión en dirección URL** , escriba la dirección URL utilizando el modelo siguiente:`https://<tenant-name>.XMattersOnDemandapp.com`

    b. Haga clic en **siguiente**.


4.  En la página **configurar inicio de sesión único en petición XMatters** , para descargar el certificado, haga clic en **Descargar certificado**y, a continuación, guarde el archivo de certificado localmente como **c:\\XMatters OnDemand.cer**.

    >[AZURE.IMPORTANT] Tiene que reenviar el certificado al equipo de soporte de xMatters. El certificado debe cargarse en el equipo de soporte técnico xMatters antes de que puede finalizar la configuración de inicio de sesión único.

    ![Inicio de sesión único de configurar] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776794.png "Inicio de sesión único de configurar")

5.  En una ventana de explorador web diferente, inicie sesión en el sitio de su empresa XMatters OnDemand como administrador.

6.  En la barra de herramientas en la parte superior, haga clic en **Administrador**y, a continuación, haga clic en **Detalles de la compañía** en la barra de navegación en el lado izquierdo.

    ![Administrador] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776795.png "Administrador")

7.  En la página **Configuración de SAML** , realice los pasos siguientes:

    ![Configuración de SAML] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776796.png "Configuración de SAML")

    1.  Seleccione **Habilitar SAML**.
    2.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en petición XMatters** , copie el valor del **Identificador de proveedor de servicios de identidad** y péguelo en el cuadro de texto **ID de proveedor de identidades** .
    3.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en petición XMatters** , copie el valor de la **URL de servicio de inicio de sesión único** y péguelo en el cuadro de texto de **Inicio de sesión único en URL** .
    4.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en petición XMatters** , copie el valor de **Dirección URL del servicio de Sign-Out único** y péguelo en el cuadro de texto **Dirección URL de cierre de sesión único** .
    5.  En la página de detalles de la compañía, en la parte superior, haga clic en **Guardar cambios**.
        ![Detalles de la compañía] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776797.png "Detalles de la compañía")

8.  En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Inicio de sesión único de configurar] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776798.png "Inicio de sesión único de configurar")

##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD iniciar sesión en XMatters OnDemand, debe aprovisionar en XMatters OnDemand.  
En el caso de XMatters OnDemand, aprovisionamiento es una tarea manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para crear cuentas de usuario, siga estos pasos:

1.  Inicie sesión en su inquilino de **Petición XMatters** .

2.  Haga clic en la ficha **usuarios** .

3.  Haga clic en **Agregar usuario**.

    ![Usuarios] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC781048.png "Usuarios")

4.  Seleccione **activo**.

5.  En la sección **Agregar un usuario** , siga estos pasos:

    ![Agregar un usuario] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC781049.png "Agregar un usuario")

    1.  Escriba el **identificador de usuario**, **nombre**, **apellido**, **sitios** de una cuenta AAD válida que desee aprovisionar.
    2.  Haga clic en **Guardar**.

>[AZURE.NOTE] Puede usar cualquier otros XMatters OnDemand usuario cuenta herramientas de creación o API proporcionan por XMatters OnDemand a cuentas de usuario AAD de aprovisionamiento.

##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-xmatters-ondemand-perform-the-following-steps"></a>Para asignar a los usuarios a XMatters OnDemand, realice los pasos siguientes:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación de **Petición de XMatters **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776799.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC767830.png "Sí")
  
Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
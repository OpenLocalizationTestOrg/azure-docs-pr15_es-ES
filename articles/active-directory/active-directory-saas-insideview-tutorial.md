<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con InsideView | Microsoft Azure" 
    description="Aprenda a usar InsideView con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más." 
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

#<a name="tutorial-azure-active-directory-integration-with-insideview"></a>Tutorial: Integración de Azure Active Directory con InsideView
  
El objetivo de este tutorial es mostrar la integración de Azure y InsideView.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inquilino InsideView
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a InsideView será capaz de inicio de sesión único en la aplicación en el sitio de su empresa de InsideView (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
El escenario descrito en este tutorial consta de los siguientes elementos:

1.  Habilitar la integración de aplicación para InsideView
2.  Configurar el inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-insideview-tutorial/IC794128.png "Escenario")
##<a name="enabling-the-application-integration-for-insideview"></a>Habilitar la integración de aplicación para InsideView
  
El objetivo de esta sección es describen cómo habilitar la integración de aplicación para InsideView.

###<a name="to-enable-the-application-integration-for-insideview-perform-the-following-steps"></a>Para habilitar la integración de aplicación para InsideView, siga estos pasos:

1.  En el portal clásico Azure, en el panel de navegación de la izquierda, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-insideview-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-insideview-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-insideview-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-insideview-tutorial/IC749322.png "Agregar una aplicación de muestra")

6.  En el **cuadro de búsqueda**, escriba **InsideView**.

    ![Galería de aplicación] (./media/active-directory-saas-insideview-tutorial/IC794129.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **InsideView**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![InsideView] (./media/active-directory-saas-insideview-tutorial/IC794130.png "InsideView")
##<a name="configuring-single-sign-on"></a>Configurar el inicio de sesión único
  
El objetivo de esta sección es describen cómo habilitar los usuarios autenticar a InsideView con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
Como parte de este procedimiento, es necesario para crear un archivo de certificado codificado de base 64.  
Si no está familiarizado con este procedimiento, vea [cómo convertir un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación **InsideView** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-insideview-tutorial/IC794131.png "Configurar el inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión InsideView** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-insideview-tutorial/IC794132.png "Configurar el inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto **Dirección URL de respuesta InsideView** , escriba la dirección URL de SSO de InsideView (por ejemplo: `https://my.insideview.com/iv/<STS Name>/login.iv`) y, a continuación, haga clic en **siguiente**.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-insideview-tutorial/IC794133.png "Configurar la dirección URL de la aplicación")

4.  En la página **configurar inicio de sesión único en InsideView** , para descargar el certificado, haga clic en **Descargar certificado**y, a continuación, guarde el archivo de certificado en el equipo.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-insideview-tutorial/IC794134.png "Configurar el inicio de sesión único")

5.  En una ventana de explorador web diferente, inicie sesión en el sitio de su empresa InsideView como administrador.

6.  En la barra de herramientas en la parte superior, haga clic en **Administrador**, **Configuración de ensamblados SingleSignOn**y, a continuación, haga clic en **Agregar SAML**.

    ![Configuración de la sesión único de SAML] (./media/active-directory-saas-insideview-tutorial/IC794135.png "Configuración de la sesión único de SAML")

7.  En la sección **Agregar una nueva SAML** , realice los pasos siguientes:

    ![Agregar un nuevo SAML] (./media/active-directory-saas-insideview-tutorial/IC794136.png "Agregar un nuevo SAML")

    1.  En el cuadro de texto **Nombre de STS** , escriba un nombre para la configuración.
    2.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en InsideView** , copie el valor de **Extremo de proveedor de servicio (SP) iniciado** y péguelo en el cuadro de texto de **Extremo de Unsolicated SamlP, WS-Federal de tecnología** .
    3.  Crear un archivo **codificado base 64** de certificado descargado.
        
        >[AZURE.TIP]Para obtener más detalles, consulte [cómo convertir un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    4.  Abra el certificado codificado de base 64 en el Bloc de notas, copie el contenido de la misma en el Portapapeles y, a continuación, péguela en el cuadro de texto de **Certificado STS**
    5.  En el cuadro de texto de la **Asignación de Id de usuario de Crm** , escriba **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
    6.  En el cuadro de texto de la **Asignación de correo electrónico de Crm** , escriba **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
    7.  En el cuadro de texto de la **Asignación de nombre de Crm** , escriba **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
    8.  En el cuadro de texto **asignación Crm apellido** , escriba **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.
    9.  Haga clic en **Guardar**.

8.  En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-insideview-tutorial/IC794137.png "Configurar el inicio de sesión único")
##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD iniciar sesión en InsideView, debe aprovisionar en InsideView.  
En el caso de InsideView, aprovisionamiento es una tarea manual.
  
Para obtener los usuarios o contactos creados en InsideView, póngase en contacto con su administrador de éxito de clientes o enviar correo electrónico**support@insideview.com**

>[AZURE.NOTE] Puede usar cualquier otros InsideView usuario cuenta herramientas de creación o API proporcionan por InsideView proporcionando cuentas de usuario de Azure AD.

##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-insideview-perform-the-following-steps"></a>Para asignar a los usuarios a InsideView, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación **InsideView **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-insideview-tutorial/IC794138.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-insideview-tutorial/IC767830.png "Sí")
  
Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
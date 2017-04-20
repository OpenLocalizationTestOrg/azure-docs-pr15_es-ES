<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con NetDocuments | Microsoft Azure" 
    description="Aprenda a usar NetDocuments con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más." 
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

#<a name="tutorial-azure-active-directory-integration-with-netdocuments"></a>Tutorial: Integración de Azure Active Directory con NetDocuments
  
El objetivo de este tutorial es mostrar la integración de Azure y NetDocuments.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inquilino NetDocuments
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a NetDocuments será capaz de inicio de sesión único en la aplicación en el sitio de su empresa de NetDocuments (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
El escenario descrito en este tutorial consta de los siguientes elementos:

1.  Habilitar la integración de aplicación para NetDocuments
2.  Configurar el inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-netdocuments-tutorial/IC795040.png "Escenario")
##<a name="enabling-the-application-integration-for-netdocuments"></a>Habilitar la integración de aplicación para NetDocuments
  
El objetivo de esta sección es describen cómo habilitar la integración de aplicación para NetDocuments.

###<a name="to-enable-the-application-integration-for-netdocuments-perform-the-following-steps"></a>Para habilitar la integración de aplicación para NetDocuments, siga estos pasos:

1.  En el portal clásico Azure, en el panel de navegación de la izquierda, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-netdocuments-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-netdocuments-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-netdocuments-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-netdocuments-tutorial/IC749322.png "Agregar una aplicación de muestra")

6.  En el **cuadro de búsqueda**, escriba **NetDocuments**.

    ![Galería de aplicación] (./media/active-directory-saas-netdocuments-tutorial/IC795041.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **NetDocuments**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![NetDocuments] (./media/active-directory-saas-netdocuments-tutorial/IC795042.png "NetDocuments")
##<a name="configuring-single-sign-on"></a>Configurar el inicio de sesión único
  
El objetivo de esta sección es describen cómo habilitar los usuarios autenticar a NetDocuments con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
Configuración de inicio de sesión único para NetDocuments requiere que recuperar un valor de la huella digital de un certificado.  
Si no está familiarizado con este procedimiento, consulte [cómo recuperar el valor de la huella digital de un certificado](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación **NetDocuments** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-netdocuments-tutorial/IC795043.png "Configurar el inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión NetDocuments** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-netdocuments-tutorial/IC795044.png "Configurar el inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , realice los pasos siguientes:

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-netdocuments-tutorial/IC795045.png "Configurar la dirección URL de la aplicación")

    1.  En el cuadro de texto de **Inicio de sesión en la dirección URL** , escriba la dirección URL usada por los usuarios para iniciar sesión su aplicación NetDocuments (por ejemplo: "*https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=CA-JI1BG3H1*").
    2.  En el cuadro de texto **Dirección URL de respuesta NetDocuments** , escriba el mismo valor que ha escrito en el cuadro de texto de **Inicio de sesión en la dirección URL** .  

        >[AZURE.NOTE]Puede encontrar el valor correcto al final del cuadro de diálogo de **Identidad federado** (consulte la captura de pantalla de paso 9).

    3.  Haga clic en **siguiente**

4.  En la página **configurar inicio de sesión único en NetDocuments** , para descargar el certificado, haga clic en **Descargar certificado**y, a continuación, guarde el archivo de certificado localmente en el equipo.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-netdocuments-tutorial/IC795046.png "Configurar el inicio de sesión único")

5.  En una ventana de explorador web diferente, inicie sesión en el sitio de su empresa NetDocuments como administrador.

6.  Vaya a **Administrador**.

7.  Haga clic en **Agregar y quitar usuarios y grupos**.

    ![Repositorio] (./media/active-directory-saas-netdocuments-tutorial/IC795047.png "Repositorio")

8.  Haga clic en **avanzadas de configurar opciones de autenticación**.

    ![Configurar opciones de autenticación de avanzada] (./media/active-directory-saas-netdocuments-tutorial/IC795048.png "Configurar opciones de autenticación de avanzada")

9.  En el cuadro de diálogo de **La identidad federada** , realice los pasos siguientes:

    ![Federado Identitty] (./media/active-directory-saas-netdocuments-tutorial/IC795049.png "Federado Identitty")

    1.  Como **tipo de servidor de identidad federado**, seleccione **Los servicios de federación de Active Directory**.
    2.  Haga clic en **Elegir archivo**para cargar el archivo de metadatos descargado.
    3.  Haga clic en **Aceptar**.

10. En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-netdocuments-tutorial/IC795050.png "Configurar el inicio de sesión único")
##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD iniciar sesión en NetDocuments, debe aprovisionar en NetDocuments.  
En el caso de NetDocuments, aprovisionamiento es una tarea manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar el aprovisionamiento de usuario, siga estos pasos:

1.  Destaque en el sitio de su organización **NetDocuments** como administrador.

2.  En el menú de la parte superior, haga clic en **Administrador**.

    ![Administrador] (./media/active-directory-saas-netdocuments-tutorial/IC795051.png "Administrador")

3.  Haga clic en **Agregar y quitar usuarios y grupos**.

    ![Repositorio] (./media/active-directory-saas-netdocuments-tutorial/IC795047.png "Repositorio")

4.  En el cuadro de texto **Dirección de correo electrónico** , escriba la dirección de correo electrónico de una cuenta de Azure Active Directory válida que desee aprovisionar y, a continuación, haga clic en **Agregar usuario**.

    ![Dirección de correo electrónico] (./media/active-directory-saas-netdocuments-tutorial/IC795053.png "Dirección de correo electrónico")

    >[AZURE.NOTE]Titular de la cuenta de Azure Active Directory recibirá un correo electrónico que incluye un vínculo para confirmar la cuenta antes de que esté activa.

>[AZURE.NOTE]Puede usar cualquier otros NetDocuments usuario cuenta herramientas de creación o API proporcionan por NetDocuments a cuentas de usuario AAD de aprovisionamiento.

##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-netdocuments-perform-the-following-steps"></a>Para asignar a los usuarios a NetDocuments, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación **NetDocuments **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-netdocuments-tutorial/IC795054.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-netdocuments-tutorial/IC767830.png "Sí")
  
Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
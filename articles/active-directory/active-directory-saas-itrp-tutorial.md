<properties
    pageTitle="Tutorial: Integración de Azure Active Directory con ITRP | Microsoft Azure" 
    description="Aprenda a usar ITRP con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más." 
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
    ms.date="09/07/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-itrp"></a>Tutorial: Integración de Azure Active Directory con ITRP
  
El objetivo de este tutorial es mostrar la integración de Azure y ITRP.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inquilino ITRP
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a ITRP será capaz de inicio de sesión único en la aplicación en el sitio de su empresa ITRP (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
El escenario descrito en este tutorial consta de los siguientes elementos:

1.  Habilitar la integración de aplicación para ITRP
2.  Configurar el inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-itrp-tutorial/IC775551.png "Escenario")
##<a name="enabling-the-application-integration-for-itrp"></a>Habilitar la integración de aplicación para ITRP
  
El objetivo de esta sección es describen cómo habilitar la integración de aplicación para ITRP.

###<a name="to-enable-the-application-integration-for-itrp-perform-the-following-steps"></a>Para habilitar la integración de aplicación para ITRP, siga estos pasos:

1.  En el portal clásico Azure, en el panel de navegación de la izquierda, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-itrp-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-itrp-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-itrp-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-itrp-tutorial/IC749322.png "Agregar una aplicación de muestra")

6.  En el **cuadro de búsqueda**, escriba **ITRP**.

    ![Galería de aplicación] (./media/active-directory-saas-itrp-tutorial/IC775565.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **ITRP**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![ITRP] (./media/active-directory-saas-itrp-tutorial/IC775566.png "ITRP")
##<a name="configuring-single-sign-on"></a>Configurar el inicio de sesión único
  
El objetivo de esta sección es describen cómo habilitar los usuarios autenticar a ITRP con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
Configuración de inicio de sesión único para ITRP requiere que recuperar un valor de la huella digital de un certificado.  
Si no está familiarizado con este procedimiento, consulte [cómo recuperar el valor de la huella digital de un certificado](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación **ITRP** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar inicio de sesión único] (./media/active-directory-saas-itrp-tutorial/IC771709.png "Configurar inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión ITRP** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-itrp-tutorial/IC775567.png "Configurar el inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto **ITRP inicio de sesión en la URL** , escriba la dirección URL con el siguiente patrón "https://*\<nombre del inquilino\>. ITRP.com*"y, a continuación, haga clic en **siguiente**.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-itrp-tutorial/IC775568.png "Configurar la dirección URL de la aplicación")

4.  En la página **configurar inicio de sesión único en ITRP** , para descargar el certificado, haga clic en **Descargar certificado**y, a continuación, guarde el archivo de certificado localmente como **c:\\ITRP.cer**.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-itrp-tutorial/IC775569.png "Configurar el inicio de sesión único")

5.  En una ventana de explorador web diferente, inicie sesión en el sitio de su empresa ITRP como administrador.

6.  En la barra de herramientas en la parte superior, haga clic en **configuración**.

    ![ITRP] (./media/active-directory-saas-itrp-tutorial/IC775570.png "ITRP")

7.  En el panel de navegación izquierdo, seleccione **Inicio de sesión único**.

    ![Inicio de sesión único] (./media/active-directory-saas-itrp-tutorial/IC775571.png "Inicio de sesión único")

8.  En la sección de configuración de inicio de sesión único, realice los pasos siguientes:

    ![Inicio de sesión único] (./media/active-directory-saas-itrp-tutorial/IC775572.png "Inicio de sesión único")

    ![Inicio de sesión único] (./media/active-directory-saas-itrp-tutorial/IC775573.png "Inicio de sesión único")

    1.  Haga clic en **Habilitar**.
    2.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en ITRP** , copie el valor de **Dirección URL de cierre de sesión remota** y, a continuación, péguelo en el cuadro de texto **Dirección URL de cierre de sesión remota** .
    3.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en ITRP** , copie el valor de **Dirección URL de SAML SSO** y péguelo en el cuadro de texto **Dirección URL de SAML SSO** .
    4.  Copie el valor de la **huella digital** del certificado exportado y, a continuación, péguelo en el cuadro de texto de la **Huella digital de certificado** .
        
        >[AZURE.TIP]Para obtener más detalles, consulte [cómo recuperar el valor de la huella digital de un certificado](http://youtu.be/YKQF266SAxI)

    5.  Haga clic en **Guardar**.

9.  En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-itrp-tutorial/IC775574.png "Configurar el inicio de sesión único")
##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD iniciar sesión en ITRP, debe aprovisionar en ITRP.  
En el caso de ITRP, aprovisionamiento es una tarea manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para crear cuentas de usuario, siga estos pasos:

1.  Inicie sesión en su inquilino **ITRP** .

2.  En la barra de herramientas en la parte superior, haga clic en **registros**.

    ![Administrador] (./media/active-directory-saas-itrp-tutorial/IC775575.png "Administrador")

3.  En el menú emergente, seleccione **personas**.

    ![Personas] (./media/active-directory-saas-itrp-tutorial/IC775587.png "Personas")

4.  Haga clic en **Agregar a nueva persona** ("+").

    ![Administrador] (./media/active-directory-saas-itrp-tutorial/IC775576.png "Administrador")

5.  En el cuadro de diálogo Agregar nueva persona, realice los pasos siguientes:

    ![Usuario] (./media/active-directory-saas-itrp-tutorial/IC775577.png "Usuario")

    1.  Escriba el **nombre**, el **correo electrónico** de una cuenta AAD válida que desee aprovisionar.
    2.  Haga clic en **Guardar**.

>[AZURE.NOTE] Puede usar cualquier otros ITRP usuario cuenta herramientas de creación o API proporcionan por ITRP a cuentas de usuario AAD de aprovisionamiento.

##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-itrp-perform-the-following-steps"></a>Para asignar a los usuarios a ITRP, siga estos pasos:

1.  En el portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de aplicación **ITRP **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-itrp-tutorial/IC775588.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-itrp-tutorial/IC767830.png "Sí")
  
Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
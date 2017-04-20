<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con SumoLogic | Microsoft Azure" 
    description="Aprenda a usar SumoLogic con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más." 
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
    ms.date="09/11/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-sumologic"></a>Tutorial: Integración de Azure Active Directory con SumoLogic
  
El objetivo de este tutorial es mostrar la integración de Azure y SumoLogic.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inquilino SumoLogic
  
Después de completar este tutorial, los usuarios de Azure AD ha asignado a SumoLogicwill ser capaz de solo inicio de sesión en la aplicación en su SumoLogic empresa sitio (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
El escenario descrito en este tutorial consta de los siguientes elementos:

1.  Habilitar la integración de aplicación para SumoLogic
2.  Configurar el inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-sumologic-tutorial/IC778549.png "Escenario")

##<a name="enabling-the-application-integration-for-sumologic"></a>Habilitar la integración de aplicación para SumoLogic
  
El objetivo de esta sección es describen cómo habilitar la integración de aplicación para SumoLogic.

###<a name="to-enable-the-application-integration-for-sumologic-perform-the-following-steps"></a>Para habilitar la integración de aplicación para SumoLogic, siga estos pasos:

1.  En el portal clásico Azure, en el panel de navegación de la izquierda, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-sumologic-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-sumologic-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-sumologic-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-sumologic-tutorial/IC749322.png "Agregar una aplicación de muestra")

6.  En el **cuadro de búsqueda**, escriba **sumologic**.

    ![Galería de aplicación] (./media/active-directory-saas-sumologic-tutorial/IC778550.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **SumoLogic**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![SumoLogic] (./media/active-directory-saas-sumologic-tutorial/IC778551.png "SumoLogic")

##<a name="configuring-single-sign-on"></a>Configurar el inicio de sesión único
  
El objetivo de esta sección es describen cómo habilitar los usuarios autenticar a SumoLogic con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
Como parte de este procedimiento, es necesario para cargar un certificado codificado de base 64 a su SumoLogictenant.  
Si no está familiarizado con este procedimiento, vea [cómo convertir un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación **SumoLogic** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar inicio de sesión único] (./media/active-directory-saas-sumologic-tutorial/IC778552.png "Configurar inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión SumoLogic** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-sumologic-tutorial/IC778553.png "Configurar inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto **SumoLogic inicio de sesión en la URL** , escriba la dirección URL con el siguiente patrón "https://*\<nombre del inquilino\>. SumoLogic.com*"y, a continuación, haga clic en **siguiente**.

    ![Configurar aoo URL] (./media/active-directory-saas-sumologic-tutorial/IC778554.png "Configurar aoo URL")

4.  En la página **configurar inicio de sesión único en SumoLogic** , para descargar el certificado, haga clic en **Descargar certificado**y, a continuación, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-sumologic-tutorial/IC778555.png "Configurar inicio de sesión único")

5.  En una ventana de explorador web diferente, inicie sesión en el sitio de su empresa SumoLogic como administrador.

6.  Vaya a **administrar \> seguridad**.

    ![Administrar] (./media/active-directory-saas-sumologic-tutorial/IC778556.png "Administrar")

7.  Haga clic en **SAML**.

    ![Configuración de seguridad global] (./media/active-directory-saas-sumologic-tutorial/IC778557.png "Configuración de seguridad global")

8.  En la lista **Seleccionar una configuración o crear uno nuevo** , seleccione **Azure AD**y, a continuación, haga clic en **Configurar**.

    ![Configurar SAML 2.0] (./media/active-directory-saas-sumologic-tutorial/IC778558.png "Configurar SAML 2.0")

9.  En el cuadro de diálogo **Configurar SAML 2.0** , siga estos pasos:

    ![Configurar SAML 2.0] (./media/active-directory-saas-sumologic-tutorial/IC778559.png "Configurar SAML 2.0")

    1.  En el cuadro de texto **Nombre de la configuración** , escriba **Azure AD**.
    2.  Seleccione el **modo de depuración**.
    3.  En el portal de clásico Azure, en la página de cuadro de diálogo **configurar inicio de sesión único en SumoLogic** , copie el valor de **Dirección URL del emisor** y péguelo en el cuadro de texto del **emisor** .
    4.  En el portal de clásico Azure, en la página de cuadro de diálogo **configurar inicio de sesión único en SumoLogic** , copie el valor de **Dirección URL de solicitud de autenticación** y, a continuación, péguelo en el cuadro de texto de la **URL de solicitud de niveles de autenticación** .
    5.  Crear un archivo **codificado Base 64** de certificado descargado.  

        >[AZURE.TIP] Para obtener más detalles, consulte [cómo convertir un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    6.  Abrir el certificado codificado de base 64 en el Bloc de notas, copie el contenido de la misma en el Portapapeles y, a continuación, pegue todo el certificado en el cuadro de texto de **Certificado X.509** .
    7.  Como **El atributo de correo electrónico**, seleccione **usar SAML asunto**.
    8.  Seleccione **SP iniciados por la configuración de inicio de sesión**.
    9.  En el cuadro de texto de la **Ruta de acceso de inicio de sesión** , escriba **Azure**.
    10. Haga clic en **Guardar**.

10. En el portal de clásico Azure, en la página de cuadro de diálogo **configurar inicio de sesión único en SumoLogic** , seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado**.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-sumologic-tutorial/IC778560.png "Configurar inicio de sesión único")

##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD iniciar sesión en SumoLogic, debe aprovisionar a SumoLogic.  
En el caso de SumoLogic, aprovisionamiento es una tarea manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para crear cuentas de usuario, siga estos pasos:

1.  Inicie sesión en su inquilino **SumoLogic** .

2.  Vaya a **administrar \> usuarios**.

    ![Usuarios] (./media/active-directory-saas-sumologic-tutorial/IC778561.png "Usuarios")

3.  Haga clic en **Agregar**.

    ![Usuarios] (./media/active-directory-saas-sumologic-tutorial/IC778562.png "Usuarios")

4.  En el cuadro de diálogo **Nuevo usuario** , siga estos pasos:

    ![Nuevo usuario] (./media/active-directory-saas-sumologic-tutorial/IC778563.png "Nuevo usuario")

    1.  Escriba la información relacionada de la cuenta de Azure AD que desee aprovisionar en los cuadros de texto **nombre**, **Apellido** y **correo electrónico** .
    2.  Seleccione una función.
    3.  Como **estado**, seleccione **activo**.
    4.  Haga clic en **Guardar**.

>[AZURE.NOTE] Puede usar cualquier otros SumoLogic usuario cuenta herramientas de creación o API proporcionan por SumoLogic a cuentas de usuario AAD de aprovisionamiento.

##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-sumologic-perform-the-following-steps"></a>Para asignar a los usuarios a SumoLogic, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación **SumoLogic** , haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-sumologic-tutorial/IC778564.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-sumologic-tutorial/IC767830.png "Sí")
  
Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
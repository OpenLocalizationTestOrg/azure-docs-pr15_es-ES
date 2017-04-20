<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Adobe EchoSign | Microsoft Azure" 
    description="¡Obtenga información sobre cómo utilizar Adobe EchoSign con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más!" 
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

#<a name="tutorial-azure-active-directory-integration-with-adobe-echosign"></a>Tutorial: Integración de Azure Active Directory con Adobe EchoSign

El objetivo de este tutorial es mostrar la integración de Azure y EchoSign de Adobe.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Inicie sesión una sola EchoSign de Adobe de suscripción habilitado

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Adobe EchoSign podrán al inicio de sesión único en la aplicación en el sitio de su empresa de Adobe EchoSign (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

El escenario descrito en este tutorial consta de los siguientes elementos:

1.  Habilitar la integración de las aplicaciones de Adobe EchoSign
2.  Configurar el inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-adobe-echosign-tutorial/IC789511.png "Escenario")
##<a name="enabling-the-application-integration-for-adobe-echosign"></a>Habilitar la integración de las aplicaciones de Adobe EchoSign

El objetivo de esta sección es describen cómo habilitar la integración de las aplicaciones de Adobe EchoSign.

###<a name="to-enable-the-application-integration-for-adobe-echosign-perform-the-following-steps"></a>Para habilitar la integración de las aplicaciones de Adobe EchoSign, siga estos pasos:

1.  En el portal clásico Azure, en el panel de navegación de la izquierda, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-adobe-echosign-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-adobe-echosign-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-adobe-echosign-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-adobe-echosign-tutorial/IC749322.png "Agregar una aplicación de muestra")

6.  En el **cuadro de búsqueda**, escriba **EchoSign de Adobe**.

    ![Galería de aplicación] (./media/active-directory-saas-adobe-echosign-tutorial/IC789514.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **Adobe EchoSign**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![EchoSign de Adobe] (./media/active-directory-saas-adobe-echosign-tutorial/IC789515.png "EchoSign de Adobe")
##<a name="configuring-single-sign-on"></a>Configurar el inicio de sesión único

El objetivo de esta sección es describen cómo habilitar los usuarios autenticar a Adobe EchoSign con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
Como parte de este procedimiento, es necesario para crear un archivo de certificado codificado de base 64.  
Si no está familiarizado con este procedimiento, vea [cómo convertir un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación de **Adobe EchoSign** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-adobe-echosign-tutorial/IC789516.png "Configurar el inicio de sesión único")

2.  En la página **¿cómo le gustaría usuarios a iniciar sesión EchoSign de Adobe** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-adobe-echosign-tutorial/IC789517.png "Configurar el inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto de **Adobe EchoSign inicio de sesión en dirección URL** , escriba la dirección URL con el siguiente patrón "*https://company.echosign.com/*" y, a continuación, haga clic en **siguiente**.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-adobe-echosign-tutorial/IC789518.png "Configurar la dirección URL de la aplicación")

4.  En la página **configurar inicio de sesión único en Adobe EchoSign** , haga clic en **Descargar certificado**y, a continuación, guarde el archivo de certificado en el equipo.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-adobe-echosign-tutorial/IC789519.png "Configurar el inicio de sesión único")

5.  En una ventana de explorador web diferente, inicie sesión en el sitio de su empresa de Adobe EchoSign como administrador.

6.  En el menú de la parte superior, haga clic en **cuenta**y, a continuación, en el panel de navegación en el dado izquierdo, haga clic en **Configuración de SAML** en **Configuración de cuenta**.

    ![Cuenta] (./media/active-directory-saas-adobe-echosign-tutorial/IC789520.png "Cuenta")

7.  En la sección Configuración de SAML, realice los pasos siguientes:

    ![Configuración de SAML] (./media/active-directory-saas-adobe-echosign-tutorial/IC789521.png "Configuración de SAML")

    1.  **Modo de SAML**, seleccione **SAML obligatorio**.
    2.  Seleccione **Permitir que los administradores EchoSign cuenta para iniciar sesión con sus credenciales de EchoSign**.
    3.  Como **La creación del usuario**, seleccione **Agregar automáticamente a los usuarios autenticados a través de SAML**.

8.  Mover, realice los pasos siguientes:

    ![Configuración de SAML] (./media/active-directory-saas-adobe-echosign-tutorial/IC789522.png "Configuración de SAML")

    1.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en Adobe EchoSign** , copie el valor de **Id. de entidad** y péguelo en el cuadro de texto **ID de entidad IdP** .
    2.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en Adobe EchoSign** , copie el valor de **Dirección URL de inicio de sesión remota** y péguelo en el cuadro de texto **Dirección URL de inicio de sesión de IdP** .
    3.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en Adobe EchoSign** , copie el valor de **Dirección URL de cierre de sesión remota** y péguelo en el cuadro de texto **Dirección URL de cierre de sesión de IdP** .
    4.  Crear un archivo **codificado base 64** de certificado descargado.  

        >[AZURE.TIP] Para obtener más detalles, consulte [cómo convertir un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o) 
 5.  Abra el certificado codificado de base 64 en el Bloc de notas, copie el contenido de la misma en el Portapapeles y, a continuación, péguela en el cuadro de texto de **Certificado IdP** 6.  Haga clic en **Guardar cambios**.

9.  En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-adobe-echosign-tutorial/IC789523.png "Configurar el inicio de sesión único")
##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD iniciar sesión en Adobe EchoSign, debe aprovisionar en EchoSign de Adobe.  
En el caso de Adobe EchoSign, aprovisionamiento es una tarea manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para crear cuentas de usuario, siga estos pasos:

1.  Inicie sesión como administrador en el sitio de su empresa **EchoSign de Adobe** .

2.  En el menú de la parte superior, haga clic en **cuenta**y, a continuación, en el panel de navegación en el dado izquierdo, haga clic en **usuarios y grupos**y, a continuación, haga clic en **crear un nuevo usuario**.

    ![Cuenta] (./media/active-directory-saas-adobe-echosign-tutorial/IC789524.png "Cuenta")

3.  En la sección **Crear un nuevo usuario** , siga estos pasos:

    ![Crear usuario] (./media/active-directory-saas-adobe-echosign-tutorial/IC789525.png "Crear usuario")

    1.  Escriba la **Dirección de correo electrónico**, **nombre** y **Apellido** de una cuenta AAD válida que desee aprovisionar en los cuadros de texto relacionados.
    2.  Haga clic en **Crear usuario**.

        >[AZURE.NOTE] Titular de la cuenta de Azure Active Directory recibirán un correo electrónico que incluye un vínculo para confirmar la cuenta antes de que esté activa.

>[AZURE.NOTE] Puede usar cualquier otros Adobe EchoSign usuario cuenta herramientas de creación o API proporcionan por Adobe EchoSign a cuentas de usuario AAD de aprovisionamiento.

##<a name="assigning-users"></a>Asignación de usuarios

Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-adobe-echosign-perform-the-following-steps"></a>Para asignar a los usuarios a Adobe EchoSign, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación de **Adobe EchoSign **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-adobe-echosign-tutorial/IC789526.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-adobe-echosign-tutorial/IC767830.png "Sí")

Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Bonus.ly | Microsoft Azure" 
    description="Aprenda a usar Bonus.ly con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más." 
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

#<a name="tutorial-azure-active-directory-integration-with-bonusly"></a>Tutorial: Integración de Azure Active Directory con Bonus.ly

El objetivo de este tutorial es mostrar la integración de Azure y Bonus.ly. El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inquilino de prueba en Bonus.ly

El escenario descrito en este tutorial se compone de los siguientes bloques de creación:

1.  Habilitar la integración de aplicación para Bonus.ly
2.  Configuración de inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-bonus-tutorial/IC773679.png "Escenario")
##<a name="enabling-the-application-integration-for-bonusly"></a>Habilitar la integración de aplicación para Bonus.ly

El objetivo de esta sección es describen cómo habilitar la integración de aplicación para Bonus.ly.

###<a name="to-enable-the-application-integration-for-bonusly-perform-the-following-steps"></a>Para habilitar la integración de aplicación para Bonus.ly, siga estos pasos:

1.  En el portal de clásico Azure, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Habilitar inicio de sesión único] (./media/active-directory-saas-bonus-tutorial/IC773680.png "Habilitar inicio de sesión único")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-bonus-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-bonus-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-bonus-tutorial/IC749322.png "Agregar una aplicación de gallerry")

6.  En el **cuadro de búsqueda**, escriba **Bonus.ly**.

    ![Galería de aplicación] (./media/active-directory-saas-bonus-tutorial/IC773681.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **Bonus.ly**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Bonusly] (./media/active-directory-saas-bonus-tutorial/IC773682.png "Bonusly")
##<a name="configuring-single-sign-on"></a>Configuración de inicio de sesión único

El objetivo de esta sección es describen cómo habilitar los usuarios autenticar a Bonus.ly con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
Configuración de inicio de sesión único para Bonus.ly requiere que recuperar un valor de la huella digital de un certificado.  
Si no está familiarizado con este procedimiento, consulte [cómo recuperar el valor de la huella digital de un certificado](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación **Bonus.ly** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar inicio de sesión único] (./media/active-directory-saas-bonus-tutorial/IC749323.png "Configurar inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión Bonus.ly** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-bonus-tutorial/IC773683.png "Configurar inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto de la **Dirección URL del inquilino de Bonus.ly** , escriba la dirección URL con el siguiente patrón "https://*\<nombre del inquilino\>. Bonus.ly*"y, a continuación, haga clic en **siguiente**: 

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-bonus-tutorial/IC773684.png "Configurar la dirección URL de la aplicación")

4.  En la página **configurar inicio de sesión único en Bonus.ly** , haga clic en **Descargar certificado**y, a continuación, guarde el archivo de certificado localmente como **c:\\Bonusly.cer**.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-bonus-tutorial/IC773685.png "Configurar inicio de sesión único")

5.  Inicie sesión en su inquilino **Bonus.ly** en una ventana de explorador diferente.

6.  En la barra de herramientas en la parte superior, haga clic en **configuración**y, a continuación, seleccione **integraciones y aplicaciones**.

    ![Bonusly] (./media/active-directory-saas-bonus-tutorial/IC773686.png "Bonusly")

7.  En **El inicio de sesión único**, seleccione **SAML**.

8.  En la página de diálogo **SAML** , realice los pasos siguientes:

    ![Bonusly] (./media/active-directory-saas-bonus-tutorial/IC773687.png "Bonusly")

    1.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en Bonus.ly** , copie el valor de **Dirección URL de inicio de sesión remota** y péguelo en el cuadro de texto **dirección URL de destino de SSO IdP** .
    2.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en Bonus.ly** , copie el valor del **Identificador del emisor** y péguelo en el cuadro de texto **IdP emisor** .
    3.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en Bonus.ly** , copie el valor de **Dirección URL de inicio de sesión remota** y péguelo en el cuadro de texto **Dirección URL de inicio de sesión de IdP** .
    4.  Copie el valor de la **huella digital** del certificado exportado y, a continuación, péguelo en el cuadro de texto de la **Huella digital de certificado** .

        >[AZURE.TIP] Para obtener más detalles, consulte [cómo recuperar el valor de la huella digital de un certificado](http://youtu.be/YKQF266SAxI)

9.  Haga clic en **Guardar**.

10. En el portal de Microsoft Azure clásico, seleccione la confirmación de la configuración y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar inicio de sesión único] (./media/active-directory-saas-bonus-tutorial/IC773689.png "Configurar inicio de sesión único")
##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD iniciar sesión en Bonus.ly, debe aprovisionar en Bonus.ly.  
En el caso de Bonus.ly, aprovisionamiento es una tarea manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar el aprovisionamiento de usuario, siga estos pasos:

1.  En una ventana de explorador web, inicie sesión en su inquilino de Bonus.ly.

2.  Haga clic en **configuración**

    ![Configuración] (./media/active-directory-saas-bonus-tutorial/IC781041.png "Configuración")

3.  Haga clic en la pestaña **usuarios y bonificaciones** .

    ![Los usuarios y bonificaciones] (./media/active-directory-saas-bonus-tutorial/IC781042.png "Los usuarios y bonificaciones")

4.  Haga clic en **Administrar usuarios**.

    ![Administrar usuarios] (./media/active-directory-saas-bonus-tutorial/IC781043.png "Administrar usuarios")

5.  Haga clic en **Agregar usuario**.

    ![Agregar usuario] (./media/active-directory-saas-bonus-tutorial/IC781044.png "Agregar usuario")

6.  En el cuadro de diálogo **Agregar usuario** , siga estos pasos:

    ![Agregar usuario] (./media/active-directory-saas-bonus-tutorial/IC781045.png "Agregar usuario")

    1.  Escriba el "**correo electrónico**, **nombre**, **apellido**" de una cuenta AAD válida que desee aprovisionar en los cuadros de texto relacionados.
    2.  Haga clic en **Guardar**.

    >[AZURE.NOTE] El titular AAD recibirán un correo electrónico que incluye un vínculo para confirmar la cuenta antes de que esté activa.

>[AZURE.NOTE] Puede usar cualquier otros Bonus.ly usuario cuenta herramientas de creación o API proporcionan por Bonus.ly a cuentas de usuario AAD de aprovisionamiento.

##<a name="assigning-users"></a>Asignación de usuarios

Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-bonusly-perform-the-following-steps"></a>Para asignar a los usuarios a Bonus.ly, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación Bonus.ly, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-bonus-tutorial/IC773690.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-bonus-tutorial/IC767830.png "Sí")

Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Cherwell | Microsoft Azure" 
    description="Aprenda a usar Cherwell con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más." 
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
    ms.date="10/14/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-cherwell"></a>Tutorial: Integración de Azure Active Directory con Cherwell

El objetivo de este tutorial es mostrar la integración de Azure y Cherwell. El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inicio de sesión único de Cherwell activado suscripción

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Cherwell será capaz de inicio de sesión único en la aplicación en el sitio de su empresa de Cherwell (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

El escenario descrito en este tutorial consta de los siguientes elementos:

1.  Habilitar la integración de aplicación para Cherwell
2.  Configurar el inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-cherwell-tutorial/IC798988.png "Escenario")
##<a name="enabling-the-application-integration-for-cherwell"></a>Habilitar la integración de aplicación para Cherwell

El objetivo de esta sección es describen cómo habilitar la integración de aplicación para Cherwell.

###<a name="to-enable-the-application-integration-for-cherwell-perform-the-following-steps"></a>Para habilitar la integración de aplicación para Cherwell, siga estos pasos:

1.  En el portal clásico Azure, en el panel de navegación de la izquierda, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-cherwell-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-cherwell-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-cherwell-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-cherwell-tutorial/IC749322.png "Agregar una aplicación de muestra")

6.  En el **cuadro de búsqueda**, escriba **Cherwell**.

    ![Cherwell] (./media/active-directory-saas-cherwell-tutorial/IC798989.png "Cherwell")

7.  En el panel de resultados, seleccione **Cherwell**y, a continuación, haga clic en **completado** para agregar la aplicación.
##<a name="configuring-single-sign-on"></a>Configurar el inicio de sesión único

    ![Cherwell](./media/active-directory-saas-cherwell-tutorial/IC798996.png "Cherwell")

El objetivo de esta sección es describen cómo habilitar los usuarios autenticar a Cherwell con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación **Cherwell** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-cherwell-tutorial/IC798990.png "Configurar el inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión Cherwell** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-cherwell-tutorial/IC798991.png "Configurar el inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , realice los pasos siguientes:

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-cherwell-tutorial/IC798992.png "Configurar la dirección URL de la aplicación")

    una.  En el cuadro de texto de **Inicio de sesión en la dirección URL** , escriba la dirección URL utilizada por los usuarios para iniciar sesión en su **Cherwell** (por ejemplo: *https://\<nombre de la compañía\>.cherwellondemand.com/cherwellclient*).

    b.  Haga clic en **siguiente**

4.  En la página **configurar inicio de sesión único en Cherwell** , realice los pasos siguientes:

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-cherwell-tutorial/IC798993.png "Configurar el inicio de sesión único")

    una.  Haga clic en **Descargar certificado**y, a continuación, guarde el certificado localmente en el equipo.

    b.  Copie la **dirección URL del proveedor de identidades**.

    c.  Copie la **dirección URL del servicio de inicio de sesión único**.

    d.  Haga clic en **siguiente**.

5.  Enviar el certificado descargado, la **Dirección URL del proveedor de identidades** y la **URL de servicio de inicio de sesión único** a su equipo de soporte técnico de Cherwell.

    >[AZURE.NOTE] El equipo de soporte de Cherwell tiene que ver la configuración de SSO real.
Recibirá una notificación cuando se ha habilitado SSO para la suscripción.

6.  En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-cherwell-tutorial/IC798994.png "Configurar el inicio de sesión único")

##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD iniciar sesión en Cherwell, debe aprovisionar en Cherwell.  
En el caso de Cherwell, es necesario crear el equipo de soporte técnico de Cherwell las cuentas de usuario.

>[AZURE.NOTE] Puede usar cualquier otros Cherwell usuario cuenta herramientas de creación o API proporcionan por Cherwell aprovisionar Azure Active Directory las cuentas de usuario.

##<a name="assigning-users"></a>Asignación de usuarios

Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-cherwell-perform-the-following-steps"></a>Para asignar a los usuarios a Cherwell, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación **Cherwell** , haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-cherwell-tutorial/IC798995.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-cherwell-tutorial/IC767830.png "Sí")

Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

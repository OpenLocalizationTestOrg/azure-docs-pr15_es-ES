<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con PolicyStat | Microsoft Azure" 
    description="Aprenda a usar PolicyStat con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más." 
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
    ms.date="09/26/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-policystat"></a>Tutorial: Integración de Azure Active Directory con PolicyStat
  
El objetivo de este tutorial es mostrar la integración de Azure y PolicyStat.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inquilino PolicyStat
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a PolicyStat será capaz de inicio de sesión único en la aplicación en el sitio de su empresa de PolicyStat (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
El escenario descrito en este tutorial consta de los siguientes elementos:

1.  Habilitar la integración de aplicación para PolicyStat
2.  Configurar el inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-policystat-tutorial/IC808662.png "Escenario")
##<a name="enabling-the-application-integration-for-policystat"></a>Habilitar la integración de aplicación para PolicyStat
  
El objetivo de esta sección es describen cómo habilitar la integración de aplicación para PolicyStat.

###<a name="to-enable-the-application-integration-for-policystat-perform-the-following-steps"></a>Para habilitar la integración de aplicación para PolicyStat, siga estos pasos:

1.  En el portal clásico Azure, en el panel de navegación de la izquierda, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-policystat-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-policystat-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-policystat-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-policystat-tutorial/IC749322.png "Agregar una aplicación de muestra")

6.  En el **cuadro de búsqueda**, escriba **PolicyStat**.

    ![Galería de aplicación] (./media/active-directory-saas-policystat-tutorial/IC808627.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **PolicyStat**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![PolicyStat] (./media/active-directory-saas-policystat-tutorial/IC810430.png "PolicyStat")
##<a name="configuring-single-sign-on"></a>Configurar el inicio de sesión único
  
El objetivo de esta sección es describen cómo habilitar los usuarios autenticar a PolicyStat con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
La aplicación de PolicyStat espera las afirmaciones SAML en un formato específico, lo que requiere que agregue asignaciones de atributos personalizados a la configuración de **atributos de token saml** .  
La siguiente captura de pantalla muestra un ejemplo de este.

![Atributos] (./media/active-directory-saas-policystat-tutorial/IC808628.png "Atributos")

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación **PolicyStat** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-policystat-tutorial/IC808629.png "Configurar el inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión PolicyStat** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-policystat-tutorial/IC808630.png "Configurar el inicio de sesión único")

3.  En la página **Configurar opciones de aplicación** , en el cuadro de texto de **Inicio de sesión en la dirección URL** , escriba la dirección URL utilizada por los usuarios para el inicio de sesión en la aplicación de la dirección URL PolicyStat (por ejemplo: *"https://demo-azure.policystat.com"*) y, a continuación, haga clic en **siguiente**.

    ![Establecer la configuración de la aplicación] (./media/active-directory-saas-policystat-tutorial/IC808631.png "Establecer la configuración de la aplicación")

4.  En la página **configurar inicio de sesión único en PolicyStat** , haga clic en **descargar metadatos**y, a continuación, guarde el archivo de metadatos en el equipo.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-policystat-tutorial/IC808632.png "Configurar el inicio de sesión único")

5.  En una ventana de explorador web diferente, inicie sesión en el sitio de su empresa PolicyStat como administrador.

6.  Haga clic en la pestaña **administración** y, a continuación, haga clic en **Configuración de inicio de sesión único** en el panel de navegación izquierdo.

    ![Menú Administrador] (./media/active-directory-saas-policystat-tutorial/IC808633.png "Menú Administrador")

7.  En la sección **configuración** , seleccione **Habilitar solo inicio de sesión de integración**.

    ![Configuración de inicio de sesión único] (./media/active-directory-saas-policystat-tutorial/IC808634.png "Configuración de inicio de sesión único")

8.  Haga clic en **Configurar atributos**y, a continuación, en la sección **Configurar atributos** , realice los pasos siguientes:

    ![Configuración de inicio de sesión único] (./media/active-directory-saas-policystat-tutorial/IC808635.png "Configuración de inicio de sesión único")

    1.  En el cuadro de texto de **Atributo Username** , escriba **uid**.
    2.  En el cuadro de texto **Nombre atributo** , escriba el **nombre**.
    3.  En el cuadro de texto del **Último atributo de nombre** , escriba **Apellidos**.
    4.  En el cuadro de texto de **Atributo de correo electrónico** , escriba la **dirección de correo electrónico**.
    5.  Haga clic en **Guardar cambios**.

9.  Haga clic en **Su metadatos IDP**y, a continuación, en la sección de **Metadatos de su IDP** , realice los pasos siguientes:

    ![Configuración de inicio de sesión único] (./media/active-directory-saas-policystat-tutorial/IC808635.png "Configuración de inicio de sesión único")

    1.  Abra el archivo descargado metadatos, copiar el contenido y, a continuación, péguelo en el cuadro de texto de **Los metadatos de proveedor de su identidad**
    2.  Haga clic en **Guardar cambios**.

10. En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-policystat-tutorial/IC771723.png "Configurar el inicio de sesión único")

11. 12. En el menú de la parte superior, haga clic en **atributos** para abrir el cuadro de diálogo **Atributos de Token SAML** .

    ![Atributos] (./media/active-directory-saas-policystat-tutorial/IC795920.png "Atributos")

13. Para agregar las asignaciones de atributo obligatorio, realice los pasos siguientes:

    ![Atributos] (./media/active-directory-saas-policystat-tutorial/IC804823.png "Atributos")

    1.  Haga clic en **Agregar usuario atributo**.
    2.  En el cuadro de texto **Nombre de atributo** , escriba **uid**.
    3.  En el cuadro de texto del **Valor del atributo** , seleccione **ExtractMailPrefix()**.
    4.  En la lista de **correo** , seleccione **User.mail**.
    5.  Haga clic en **Finalizar**.
##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD iniciar sesión en PolicyStat, debe aprovisionar en PolicyStat.  
Es compatible con PolicyStat acaba de aprovisionamiento de usuario de tiempo. Esto significa, es necesario agregar los usuarios manualmente a PolicyStat.  
Los usuarios se agregarán automáticamente en su primer inicio de sesión a través de inicio de sesión único en.

>[AZURE.NOTE]Puede usar cualquier otros PolicyStat usuario cuenta herramientas de creación o API proporcionan por PolicyStat a cuentas de usuario AAD de aprovisionamiento.

##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-policystat-perform-the-following-steps"></a>Para asignar a los usuarios a PolicyStat, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación **PolicyStat **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-policystat-tutorial/IC808636.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-policystat-tutorial/IC767830.png "Sí")
  
Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
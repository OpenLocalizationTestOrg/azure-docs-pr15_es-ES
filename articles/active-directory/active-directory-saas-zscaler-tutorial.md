<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Zscaler | Microsoft Azure" 
    description="Obtenga información sobre cómo usar Zscaler con Azure Active Directory para habilitar el inicio de sesión único, automatizado de aprovisionamiento y más!." 
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
    ms.date="08/16/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-zscaler"></a>Tutorial: Integración de Azure Active Directory con Zscaler
  
El objetivo de este tutorial es mostrar la integración de Azure y Zscaler. El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inquilino en Zscaler
  
El escenario descrito en este tutorial se compone de los siguientes bloques de creación:

1.  Habilitar la integración de aplicación para Zscaler
2.  Configuración de inicio de sesión único
3.  Configuración de proxy
4.  Configuración de aprovisionamiento de usuario
5.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-zscaler-tutorial/IC769226.png "Escenario")

##<a name="enabling-the-application-integration-for-zscaler"></a>Habilitar la integración de aplicación para Zscaler
  
El objetivo de esta sección es describen cómo habilitar la integración de aplicación para Zscaler.

###<a name="to-enable-the-application-integration-for-zscaler-perform-the-following-steps"></a>Para habilitar la integración de aplicación para Zscaler, siga estos pasos:

1.  En el portal de clásico Azure, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-zscaler-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-zscaler-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-zscaler-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-zscaler-tutorial/IC749322.png "Agregar una aplicación de gallerry")

6.  En el **cuadro de búsqueda**, escriba **Zscaler**.

    ![Galería de aplicación] (./media/active-directory-saas-zscaler-tutorial/IC769227.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **Zscaler**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Zscaler] (./media/active-directory-saas-zscaler-tutorial/IC769228.png "Zscaler")

##<a name="configuring-single-sign-on"></a>Configuración de inicio de sesión único
  
El objetivo de esta sección es describen cómo habilitar los usuarios autenticar a Zscaler con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
Como parte de este procedimiento, debe cargar un certificado en Zscaler.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación **Zscaler** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Habilitar inicio de sesión único] (./media/active-directory-saas-zscaler-tutorial/IC769229.png "Habilitar inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión Zscaler** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Inicio de sesión único de configurar] (./media/active-directory-saas-zscaler-tutorial/IC769230.png "Inicio de sesión único de configurar")

3.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto **Zscaler inicio de sesión en la URL** , escriba el inicio de sesión en la dirección URL que obtuvo de Zscaler y, a continuación, haga clic en **siguiente**: 

    >[AZURE.NOTE] Póngase en contacto con el equipo de soporte técnico de Zscaler si no sabe cuál es la dirección URL de inicio de sesión.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-zscaler-tutorial/IC769231.png "Configurar la dirección URL de la aplicación")

4.  En la página **configurar inicio de sesión único en Zscaler** , realice los pasos siguientes:

    ![Configurar inicio de sesión único] (./media/active-directory-saas-zscaler-tutorial/IC769232.png "Configurar inicio de sesión único")

    1.  Haga clic en **Descargar certificado**y, a continuación, guarde el archivo de certificado localmente como **c:\\Zscaler.cer**.
    2.  Copie la **dirección URL de la solicitud de autenticación** en el Portapapeles.

5.  Inicie sesión en su inquilino Zscaler.

6.  En el menú de la parte superior, haga clic en **administración**.

    ![Administración] (./media/active-directory-saas-zscaler-tutorial/IC769486.png "Administración")

7.  **Administrar administradores y Roles**, haga clic en **Administrar usuarios y autenticación**.

    ![Administrar los administradores y funciones] (./media/active-directory-saas-zscaler-tutorial/IC769487.png "Administrar los administradores y funciones")

8.  En la sección **Seleccione la opción de autenticación para su organización** , realice los pasos siguientes:

    ![Elija las opciones de autenticación] (./media/active-directory-saas-zscaler-tutorial/IC769488.png "Elija las opciones de autenticación")

    1.  Seleccione **autenticar con el inicio de sesión único de SAML**.
    2.  Haga clic en **configurar los parámetros de inicio de sesión único de SAML**.

9.  En la página de diálogo **Configurar SAML solo inicio de sesión de los parámetros** , realice los pasos siguientes y, a continuación, haga clic en **Listo**:

    ![Cargar el certificado] (./media/active-directory-saas-zscaler-tutorial/IC769489.png "Cargar el certificado")

    1.  En el cuadro de texto de la **dirección URL del Portal de SAML al que se envían los usuarios para la autenticación** , pegue el valor del campo **URL de la solicitud de autenticación** desde el portal de clásico de Azure.
    2.  En el cuadro de texto **nombre de inicio de sesión que contiene el atributo** , escriba **NameID**.
    3.  En el campo **Cargar el certificado público de SSL** , cargar el certificado que ha descargado desde el portal de clásico de Azure.
    4.  Seleccione **habilitar Auto aprovisionamiento SAML**.

10. En la página de diálogo **Configurar autenticación de usuario** , realice los pasos siguientes:

    ![Configurar la autenticación de usuario] (./media/active-directory-saas-zscaler-tutorial/IC769490.png "Configurar la autenticación de usuario")

    1.  Haga clic en **Guardar**.
    2.  Haga clic en **Activar ahora**.

11. En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar inicio de sesión único] (./media/active-directory-saas-zscaler-tutorial/IC769491.png "Configurar inicio de sesión único")

##<a name="configuring-proxy-settings"></a>Configuración de proxy

###<a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Para establecer la configuración de proxy en Internet Explorer

1.  Inicie **Internet Explorer**.

2.  Seleccione **Opciones de Internet** en el menú **Herramientas** para abrir el cuadro de diálogo **Opciones de Internet** .

    ![Opciones de Internet] (./media/active-directory-saas-zscaler-tutorial/IC769492.png "Opciones de Internet")

3.  Haga clic en la pestaña **conexiones** .

    ![Conexiones] (./media/active-directory-saas-zscaler-tutorial/IC769493.png "Conexiones")

4.  Haga clic en **configuración de LAN** para abrir el cuadro de diálogo **Configuración de LAN** .

5.  En la sección servidor Proxy, siga estos pasos:

    ![Servidor proxy] (./media/active-directory-saas-zscaler-tutorial/IC769494.png "Servidor proxy")

    1.  Seleccione Usar un servidor proxy para la LAN.
    2.  En el cuadro de texto dirección, escriba **gateway.zscalertwo.net**.
    3.  En el cuadro de texto puerto, escriba **80**.
    4.  Seleccione el **servidor proxy para direcciones locales**.
    5.  Haga clic en **Aceptar** para cerrar el cuadro de diálogo **configuración de red de área Local (LAN)** .

6.  Haga clic en **Aceptar** para cerrar el cuadro de diálogo **Opciones de Internet** .

##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD iniciar sesión en Zscaler, debe aprovisionar en Zscaler.  
En el caso de Zscaler, aprovisionamiento es una tarea manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar el aprovisionamiento de usuario, siga estos pasos:

1.  Inicie sesión en su inquilino **Zscaler** .

2.  Haga clic en **administración**.

    ![Administración] (./media/active-directory-saas-zscaler-tutorial/IC781035.png "Administración")

3.  Haga clic en **administración de usuarios**.

    ![Administración de usuarios] (./media/active-directory-saas-zscaler-tutorial/IC781036.png "Administración de usuarios")

4.  En la ficha **usuarios** , haga clic en **Agregar**.

    ![Agregar] (./media/active-directory-saas-zscaler-tutorial/IC781037.png "Agregar")

5.  En la sección Agregar usuario, siga estos pasos:

    ![Agregar usuario] (./media/active-directory-saas-zscaler-tutorial/IC781038.png "Agregar usuario")

    1.  Escriba el **identificador de usuario**, **Nombre de la interfaz de usuario**, **contraseña**, **Confirmar contraseña**y, a continuación, seleccione el **departamento** de una cuenta AAD válida que desee aprovisionar y **grupos** .
    2.  Haga clic en **Guardar**.

>[AZURE.NOTE] Puede usar cualquier otro Zscaler usuario cuenta herramienta de creación o API proporcionan por Zscaler a cuentas de usuario AAD de aprovisionamiento.

##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-zscaler-perform-the-following-steps"></a>Para asignar a los usuarios a Zscaler, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación **Zscaler** , haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-zscaler-tutorial/IC769495.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-zscaler-tutorial/IC767830.png "Sí")
  
Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con la versión Beta de Zscaler | Microsoft Azure" 
    description="Obtenga información sobre cómo usar Zscaler Beta con Azure Active Directory para habilitar el inicio de sesión único, automatizado de aprovisionamiento y más!." 
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

#<a name="tutorial-azure-active-directory-integration-with-zscaler-beta"></a>Tutorial: Integración de Azure Active Directory con Zscaler Beta
  
El objetivo de este tutorial es mostrar la integración de Azure y ZScaler Beta.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inicio de sesión único de ZScaler Beta activado suscripción
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a ZScaler Beta podrán al inicio de sesión único en la aplicación en el sitio de su empresa de ZScaler Beta (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
El escenario descrito en este tutorial se compone de los siguientes bloques de creación:

1.  Habilitar la integración de aplicación para ZScaler Beta
2.  Configuración de inicio de sesión único
3.  Configuración de proxy
4.  Configuración de aprovisionamiento de usuario
5.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-zscaler-beta-tutorial/IC800223.png "Escenario")

##<a name="enabling-the-application-integration-for-zscaler-beta"></a>Habilitar la integración de aplicación para ZScaler Beta
  
El objetivo de esta sección es describen cómo habilitar la integración de la aplicación para la versión ZScaler Beta.

###<a name="to-enable-the-application-integration-for-zscaler-beta-perform-the-following-steps"></a>Para habilitar la integración de aplicación para ZScaler Beta, siga estos pasos:

1.  En el portal de clásico Azure, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-zscaler-beta-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-zscaler-beta-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-zscaler-beta-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-zscaler-beta-tutorial/IC749322.png "Agregar una aplicación de gallerry")

6.  En el **cuadro de búsqueda**, escriba **ZScaler Beta**.

    ![Galería de aplicación] (./media/active-directory-saas-zscaler-beta-tutorial/IC800224.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **ZScaler Beta**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![ZScaler uno] (./media/active-directory-saas-zscaler-beta-tutorial/IC800216.png "ZScaler uno")

##<a name="configuring-single-sign-on"></a>Configuración de inicio de sesión único
  
El objetivo de esta sección es describen cómo habilitar los usuarios autenticar ZScaler Beta con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
Como parte de este procedimiento, es necesario para cargar un certificado codificado de base 64 en su inquilino ZScaler Beta.  
Si no está familiarizado con este procedimiento, vea [cómo convertir un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación **ZScaler Beta** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-zscaler-beta-tutorial/IC800225.png "Configurar el inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión ZScaler Beta** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-zscaler-beta-tutorial/IC800226.png "Configurar el inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto **ZScaler Beta de inicio de sesión en dirección URL** , escriba la dirección URL utilizada por los usuarios para el inicio de sesión en la aplicación de la versión ZScaler Beta y, a continuación, haga clic en **siguiente**.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-zscaler-beta-tutorial/IC800227.png "Configurar la dirección URL de la aplicación")

    >[AZURE.NOTE] Para obtener el valor real en su entorno de su equipo de soporte técnico de ZScaler Beta si lo necesita.

4.  En la página **configurar inicio de sesión único en ZScaler Beta** , para descargar el certificado, haga clic en **Descargar certificado**y, a continuación, guarde el archivo de certificado en el equipo.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-zscaler-beta-tutorial/IC800228.png "Configurar el inicio de sesión único")

5.  En una ventana de explorador web diferente, inicie sesión en el sitio de su empresa ZScaler Beta como administrador.

6.  En el menú de la parte superior, haga clic en **administración**.

    ![Administración] (./media/active-directory-saas-zscaler-beta-tutorial/IC800206.png "Administración")

7.  **Administrar administradores y Roles**, haga clic en **Administrar usuarios y autenticación**.

    ![Administrar usuarios y autenticación] (./media/active-directory-saas-zscaler-beta-tutorial/IC800207.png "Administrar usuarios y autenticación")

8.  En la sección **Elegir opciones de autenticación para su organización** , realice los pasos siguientes:

    ![Autenticación] (./media/active-directory-saas-zscaler-beta-tutorial/IC800208.png "Autenticación")

    1.  Seleccione **autenticar con SAML inicio de sesión único**.
    2.  Haga clic en **configurar los parámetros de inicio de sesión único de SAML**.

9.  En la página de diálogo **Configurar SAML solo inicio de sesión de los parámetros** , realice los pasos siguientes y, a continuación, haga clic en **Listo**:

    ![Inicio de sesión único] (./media/active-directory-saas-zscaler-beta-tutorial/IC800209.png "Inicio de sesión único")

    1.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en ZScaler Beta** , copie el valor de **Dirección URL de solicitud de autenticación** y péguelo en el cuadro de texto de la **dirección URL del Portal de SAML al que se envían los usuarios para la autenticación** .
    2.  En el cuadro de texto **nombre de inicio de sesión que contiene el atributo** , escriba **NameID**.
    3.  Para cargar el certificado descargado, haga clic en **Zscaler pem**.
    4.  Seleccione **habilitar Auto aprovisionamiento SAML**.

10. En la página de diálogo **Configurar autenticación de usuario** , realice los pasos siguientes:

    ![Administración] (./media/active-directory-saas-zscaler-beta-tutorial/IC800210.png "Administración")

    1.  Haga clic en **Guardar**.
    2.  Haga clic en **Activar ahora**.

11. En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en ZScaler Beta** , seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado**.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-zscaler-beta-tutorial/IC800229.png "Configurar el inicio de sesión único")

##<a name="configuring-proxy-settings"></a>Configuración de proxy

###<a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Para establecer la configuración de proxy en Internet Explorer

1.  Inicie **Internet Explorer**.

2.  Seleccione **Opciones de Internet** en el menú **Herramientas** para abrir el cuadro de diálogo **Opciones de Internet** .

    ![Opciones de Internet] (./media/active-directory-saas-zscaler-beta-tutorial/IC769492.png "Opciones de Internet")

3.  Haga clic en la pestaña **conexiones** .

    ![Conexiones] (./media/active-directory-saas-zscaler-beta-tutorial/IC769493.png "Conexiones")

4.  Haga clic en **configuración de LAN** para abrir el cuadro de diálogo **Configuración de LAN** .

5.  En la sección servidor Proxy, siga estos pasos:

    ![Servidor proxy] (./media/active-directory-saas-zscaler-beta-tutorial/IC769494.png "Servidor proxy")

    1.  Seleccione Usar un servidor proxy para la LAN.
    2.  En el cuadro de texto dirección, escriba **gateway.zscalerBeta.net**.
    3.  En el cuadro de texto puerto, escriba **80**.
    4.  Seleccione el **servidor proxy para direcciones locales**.
    5.  Haga clic en **Aceptar** para cerrar el cuadro de diálogo **configuración de red de área Local (LAN)** .

6.  Haga clic en **Aceptar** para cerrar el cuadro de diálogo **Opciones de Internet** .

##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD iniciar sesión en ZScaler Beta, debe aprovisionar a ZScaler Beta.  
En el caso de ZScaler Beta, aprovisionamiento es una tarea manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar el aprovisionamiento de usuario, siga estos pasos:

1.  Inicie sesión en su inquilino **Zscaler** .

2.  Haga clic en **administración**.

    ![Administración] (./media/active-directory-saas-zscaler-beta-tutorial/IC781035.png "Administración")

3.  Haga clic en **administración de usuarios**.

    ![Agregar] (./media/active-directory-saas-zscaler-beta-tutorial/IC781037.png "Agregar")

4.  En la ficha **usuarios** , haga clic en **Agregar**.

    ![Agregar] (./media/active-directory-saas-zscaler-beta-tutorial/IC781037.png "Agregar")

5.  En la sección Agregar usuario, siga estos pasos:

    ![Agregar usuario] (./media/active-directory-saas-zscaler-beta-tutorial/IC781038.png "Agregar usuario")

    1.  Escriba el **identificador de usuario**, **Nombre de la interfaz de usuario**, **contraseña**, **Confirmar contraseña**y, a continuación, seleccione el **departamento** de una cuenta AAD válida que desee aprovisionar y **grupos** .
    2.  Haga clic en **Guardar**.

>[AZURE.NOTE] Puede usar cualquier otros ZScaler Beta usuario cuenta herramientas de creación o API proporcionan por ZScaler Beta a cuentas de usuario AAD de aprovisionamiento.

##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-zscaler-beta-perform-the-following-steps"></a>Para asignar a los usuarios a ZScaler Beta, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación **ZScaler Beta** , haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-zscaler-beta-tutorial/IC800230.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-zscaler-beta-tutorial/IC767830.png "Sí")
  
Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
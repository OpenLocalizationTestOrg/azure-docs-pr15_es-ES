<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con TimeOffManager | Microsoft Azure" 
    description="Aprenda a usar TimeOffManager con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más." 
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
    ms.date="10/10/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-timeoffmanager"></a>Tutorial: Integración de Azure Active Directory con TimeOffManager
  
El objetivo de este tutorial es mostrar la integración de Azure y TimeOffManager.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inicio de sesión único de TimeOffManager activado suscripción
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a TimeOffManager será capaz de inicio de sesión único en la aplicación en el sitio de su empresa de TimeOffManager (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
El escenario descrito en este tutorial se compone de los siguientes bloques de creación:

1.  Habilitar la integración de aplicación para TimeOffManager
2.  Configuración de inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-timeoffmanager-tutorial/IC795909.png "Escenario")

##<a name="enabling-the-application-integration-for-timeoffmanager"></a>Habilitar la integración de aplicación para TimeOffManager
  
El objetivo de esta sección es describen cómo habilitar la integración de aplicación para TimeOffManager.

###<a name="to-enable-the-application-integration-for-timeoffmanager-perform-the-following-steps"></a>Para habilitar la integración de aplicación para TimeOffManager, siga estos pasos:

1.  En el portal de clásico Azure, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-timeoffmanager-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-timeoffmanager-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-timeoffmanager-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-timeoffmanager-tutorial/IC749322.png "Agregar una aplicación de gallerry")

6.  En el **cuadro de búsqueda**, escriba **TimeOffManager**.

    ![Galería de aplicación] (./media/active-directory-saas-timeoffmanager-tutorial/IC795910.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **TimeOffManager**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![TimeOffManager] (./media/active-directory-saas-timeoffmanager-tutorial/IC795911.png "TimeOffManager")

##<a name="configuring-single-sign-on"></a>Configuración de inicio de sesión único
  
El objetivo de esta sección es describen cómo habilitar los usuarios autenticar a TimeOffManager con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
Como parte de este procedimiento, es necesario para cargar un certificado codificado de base 64 en su inquilino TimeOffManager.  
Si no está familiarizado con este procedimiento, vea [cómo convertir un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación **TimeOffManager** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-timeoffmanager-tutorial/IC795912.png "Configurar el inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión TimeOffManager** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-timeoffmanager-tutorial/IC795913.png "Configurar el inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto **Dirección URL de respuesta TimeOffManager** , escriba la dirección URL AssertionConsumerService de TimeOffManager (por ejemplo: "*ejemplo: https://www.timeoffmanager.com/cpanel/sso/consume.aspx?company\_id = IC34216*" y, a continuación, haga clic en **siguiente**.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-timeoffmanager-tutorial/IC795914.png "Configurar la dirección URL de la aplicación")

    Puede obtener la dirección URL de respuesta desde el inicio de sesión único de TimeOffManager en la página de configuración.

    ![Configuración de inicio de sesión único] (./media/active-directory-saas-timeoffmanager-tutorial/IC795915.png "Configuración de inicio de sesión único")

4.  En la página **configurar inicio de sesión único en TimeOffManager** , para descargar el certificado, haga clic en **Descargar certificado**y, a continuación, guarde el archivo de certificado en el equipo.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-timeoffmanager-tutorial/IC795916.png "Configurar el inicio de sesión único")

5.  En una ventana de explorador web diferente, inicie sesión en el sitio de su empresa TimeOffManager como administrador.

6.  Vaya a **cuenta \> opciones de cuenta \> un solo inicio de sesión de configuración**.

    ![Configuración de inicio de sesión único] (./media/active-directory-saas-timeoffmanager-tutorial/IC795917.png "Configuración de inicio de sesión único")

7.  En la sección **Configuración de inicio de sesión único** , realice los pasos siguientes:

    ![Configuración de inicio de sesión único] (./media/active-directory-saas-timeoffmanager-tutorial/IC795918.png "Configuración de inicio de sesión único")

    una.  Crear un archivo **codificado Base 64** de certificado descargado.  

        >[AZURE.TIP] Para obtener más detalles, consulte [cómo convertir un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    b.  Abrir el certificado codificado de base 64 en el Bloc de notas, copie el contenido de la misma en el Portapapeles y, a continuación, pegue todo el certificado en el cuadro de texto de **Certificado X.509** .
    
    c.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en TimeOffManager** , copie el valor de **Dirección URL del emisor** y péguelo en el cuadro de texto **Idp emisor** .
    
    d.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en TimeOffManager** , copie el valor de **Dirección URL de inicio de sesión remota** y péguelo en el cuadro de texto de la **Dirección URL del extremo IdP** .
    
    e.  Como **Exigir SAML**, seleccione **No**.
    

    f.  **Creación automática de usuarios**, seleccione **Sí**.
    
    g.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en TimeOffManager** , copie el valor de **Dirección URL de cierre de sesión remota** y, a continuación, péguelo en el cuadro de texto **Dirección URL de cierre de sesión** .
    
    h.  Haga clic en **Guardar cambios**.

8.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en TimeOffManager** , seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado**.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-timeoffmanager-tutorial/IC795919.png "Configurar el inicio de sesión único")

9.  En el menú de la parte superior, haga clic en **atributos** para abrir el cuadro de diálogo **Atributos de Token SAML** .

    ![Atributos] (./media/active-directory-saas-timeoffmanager-tutorial/IC795920.png "Atributos")

10. Para agregar las asignaciones de atributo obligatorio, realice los pasos siguientes:

    ![atributos de token SAML] (./media/active-directory-saas-timeoffmanager-tutorial/123.png "atributos de token SAML")

  	|Nombre del atributo|Valor del atributo|
  	|---|---|
  	|Correo electrónico|User.Mail|
  	|Nombre|User.givenName|
  	|Apellido|User.Surname|

    una.  Para cada fila de datos de la tabla anterior, haga clic en **Agregar usuario atributo**.

    b.  En el cuadro de texto **Nombre de atributo** , escriba el nombre del atributo que se muestra de la fila.

    c.  En el cuadro de texto del **Valor del atributo** , seleccione el valor del atributo que se muestra de la fila.

    d.  Haga clic en **Finalizar**.

11. Haga clic en **Aplicar cambios**.

##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD iniciar sesión en TimeOffManager, debe aprovisionar a TimeOffManager.  
Es compatible con TimeOffManager acaba de aprovisionamiento de usuario de tiempo. No hay ningún elemento de acción para usted.  
Los usuarios se agregan automáticamente durante el inicio de sesión primero en el inicio de sesión único.

>[AZURE.NOTE] Puede usar cualquier otros TimeOffManager usuario cuenta herramientas de creación o API proporcionan por TimeOffManager a cuentas de usuario AAD de aprovisionamiento.

##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-timeoffmanager-perform-the-following-steps"></a>Para asignar a los usuarios a TimeOffManager, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación **TimeOffManager** , haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-timeoffmanager-tutorial/IC795922.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-timeoffmanager-tutorial/IC767830.png "Sí")
  
Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

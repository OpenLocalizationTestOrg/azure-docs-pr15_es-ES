<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con día laborable | Microsoft Azure" 
    description="Obtenga información sobre cómo utilizar día laborable con Azure Active Directory para habilitar el inicio de sesión único, automatizado de aprovisionamiento y más!." 
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
    ms.date="09/09/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-workday"></a>Tutorial: Integración de Azure Active Directory con los días laborables
  
El objetivo de este tutorial es mostrar la integración de Azure y jornada laboral. El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inquilino de día laborable
  
El escenario descrito en este tutorial se compone de los siguientes bloques de creación:

1.  Habilitar la integración de aplicaciones para el día laborable
2.  Configuración de inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Configuración de aprovisionamiento de usuario

![Escenario] (./media/active-directory-saas-workday-tutorial/IC782919.png "Escenario")

##<a name="enabling-the-application-integration-for-workday"></a>Habilitar la integración de aplicaciones para el día laborable
  
El objetivo de esta sección es describen cómo habilitar la integración de las aplicaciones de Salesforce.

###<a name="to-enable-the-application-integration-for-workday-perform-the-following-steps"></a>Para habilitar la integración de aplicaciones para el día laborable, siga estos pasos:

1.  En el portal de clásico Azure, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-workday-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-workday-tutorial/IC700994.png "Aplicaciones")

4.  Para abrir la **Galería de aplicación**, haga clic en **Agregar una aplicación**y, a continuación, haga clic en **Agregar una aplicación para mi organización usar**.

    ![¿Qué desea hacer?] (./media/active-directory-saas-workday-tutorial/IC700995.png "¿Qué desea hacer?")

5.  En el **cuadro de búsqueda**, escriba el **día laborable**.

    ![Día laborable] (./media/active-directory-saas-workday-tutorial/IC701021.png "Día laborable")

6.  En el panel de resultados, seleccione **los días laborables**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Día laborable] (./media/active-directory-saas-workday-tutorial/IC701022.png "Día laborable")

##<a name="configuring-single-sign-on"></a>Configuración de inicio de sesión único
  
El objetivo de esta sección es describen cómo habilitar los usuarios autenticar al día laborable con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
Como parte de este procedimiento, es necesario para crear un certificado de codificación de base 64.  
Si no está familiarizado con este procedimiento, vea [cómo convertir un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En la página de integración de aplicación de **jornada laboral** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar inicio de sesión único] (./media/active-directory-saas-workday-tutorial/IC782920.png "Configurar inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión el día laborable** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-workday-tutorial/IC782921.png "Configurar inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , realice los pasos siguientes y, a continuación, haga clic en **siguiente**.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-workday-tutorial/IC782957.png "Configurar la dirección URL de la aplicación")

    una. En el cuadro de texto de **Inicio de sesión en la dirección URL** , escriba la dirección URL utilizada por los usuarios para iniciar sesión en el día laborable utilizando el modelo siguiente:`https://impl.workday.com/<tenant>/login-saml2.htmld`

    b.  En el cuadro de texto **Dirección URL de respuesta del día laborable** , escriba la dirección URL de respuesta de día laborable utilizando el modelo siguiente:`https://impl.workday.com/<tenant>/login-saml.htmld`

    >[AZURE.NOTE]La dirección URL de respuesta debe tener un subdominio (por ejemplo: www, wd2, wd3, implementa wd3, wd5, wd5 implementa). 
    >Usar algo como "*http://www.myworkday.com*" funciona pero no "*http://myworkday.com*". 
 
4.  En la página **configurar inicio de sesión único en el día laborable** , para descargar el certificado, haga clic en **Descargar certificado**y, a continuación, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-workday-tutorial/IC782922.png "Configurar inicio de sesión único")

5.  En una ventana de explorador web diferente, inicie sesión en el sitio de su empresa de jornada laboral como administrador.

6.  Vaya a **menú \> trabajo**.

    ![Trabajo] (./media/active-directory-saas-workday-tutorial/IC782923.png "Trabajo")

7.  Vaya a **administración de cuenta**.

    ![Administración de cuenta] (./media/active-directory-saas-workday-tutorial/IC782924.png "Administración de cuenta")

8.  Vaya a **Editar la configuración del inquilino: seguridad**.

    ![Editar seguridad del inquilino] (./media/active-directory-saas-workday-tutorial/IC782925.png "Editar seguridad del inquilino")

9.  En la sección **Direcciones URL de redirección** , siga estos pasos:

    ![Direcciones URL de redireccionamiento] (./media/active-directory-saas-workday-tutorial/IC7829581.png "Direcciones URL de redireccionamiento")

    una. Haga clic en **Agregar fila**.

    b. En el cuadro de texto **Dirección URL de redirección de inicio de sesión** y el cuadro de texto **Dirección URL de redirección de móvil** , escriba la **Dirección URL del inquilino de día laborable** que ha escrito en la página **Configurar la dirección URL de aplicación** del portal clásica Azure.
    
    c. En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en el día laborable** , copie la **Dirección URL del servicio de Sign-Out único**y péguelo en el cuadro de texto **Dirección URL de redirección de cierre de sesión** .

    d.  En el **entorno** de cuadro de texto, escriba el nombre del entorno.  


    >[AZURE.NOTE] El valor del atributo entorno está vinculado al valor de la dirección URL del inquilino:
    >
    >-   Si el nombre de dominio del inquilino día laborable URL comienza con la implementación (por ejemplo: *https://impl.workday.com/\<inquilino\>/login-saml2.htmld*), el atributo de **entorno** debe estar establecido en implementación.
    >-   Si el nombre de dominio se inicia con algo más, debe ponerse en contacto con el día laborable para obtener el valor de **entorno** coincidente.

10. En la sección **Configuración de SAML** , realice los pasos siguientes:

    ![Configuración SAML] (./media/active-directory-saas-workday-tutorial/IC782926.png "Configuración SAML")

    una.  Seleccione **Habilitar la autenticación de SAML**.

    b.  Haga clic en **Agregar fila**.

11. En la sección proveedores de identidades SAML, realice los pasos siguientes:

    ![Proveedores de identidades SAML] (./media/active-directory-saas-workday-tutorial/IC7829271.png "Proveedores de identidades SAML")

    una. En el cuadro de texto Nombre del proveedor de identidades, escriba un nombre de proveedor (por ejemplo: *SPInitiatedSSO*).

    b. En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en el día laborable** , copie el valor del **Identificador de proveedor de servicios de identidad** y péguelo en el cuadro de texto del **emisor** .

    c. Seleccione **Habilitar el cierre de sesión de Workday recomendados**.

    d. En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en el día laborable** , copie el valor de **Dirección URL del servicio de Sign-Out único** y péguelo en el cuadro de texto de la **URL de solicitud de cierre de sesión** .


    e. Haga clic en el **Certificado de clave pública de identidad proveedor**y, a continuación, haga clic en **crear**. 

    ![Crear] (./media/active-directory-saas-workday-tutorial/IC782928.png "Crear")

    f. Haga clic en **crear x509 clave pública**. 
        
    ![Crear] (./media/active-directory-saas-workday-tutorial/IC782929.png "Crear")


1. En la sección **Ver x509 clave pública** , realice los pasos siguientes: 

    ![Ver x509 clave pública] (./media/active-directory-saas-workday-tutorial/IC782930.png "Ver x509 clave pública") 

    una. En el cuadro de texto **nombre** , escriba un nombre para el certificado (por ejemplo: *EPI\_SP*).
        
    b. En el cuadro de texto **Válido desde** , escriba válida desde el valor del atributo de certificado.
    
    c.  En el cuadro de texto **Válido para** , escriba el valor del atributo de certificado válido.
        
    >[AZURE.NOTE] Puede obtener la validez de fecha y válido hasta la fecha del certificado descargado haciendo doble clic en él. Las fechas se muestran en la ficha **Detalles** .

    d. Crear un archivo **codificado Base 64** de certificado descargado.  

    >[AZURE.TIP] Para obtener más detalles, consulte [cómo convertir un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    e.  Abrir el certificado codificado de base 64 en el Bloc de notas y, a continuación, copie el contenido de la misma.
    
    f.  En el cuadro de texto del **certificado** , pegue el contenido del Portapapeles.
    
    g.  Haga clic en **Aceptar**.

12.  Realice los pasos siguientes: 

    ![Configuración de SSO] (./media/active-directory-saas-workday-tutorial/IC7829351111.png "Configuración de SSO")

    una.  Habilitar la **x509 clave privada**.

    b.  En el cuadro de texto **ID de proveedor de servicio** , escriba **http://www.workday.com**.

    c.  Seleccione **Habilitar SP iniciadas SAML autenticación**.

    d.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en el día laborable** , copie el valor de la **URL de servicio de inicio de sesión único** y péguelo en el cuadro de texto de la **Dirección URL del servicio de SSO de IdP** .
     
    e. Seleccione **no disminuir solicitud de autenticación iniciado por SP**.

    f. Como **Método de autenticación solicitud de firma**, seleccione **SHA256**. 
        
    ![Método de firma de la solicitud de autenticación] (./media/active-directory-saas-workday-tutorial/IC782932.png "Método de firma de la solicitud de autenticación") 
 
    g. Haga clic en **Aceptar**. 
        
    ![OK] (./media/active-directory-saas-workday-tutorial/IC782933.png "OK")

12. En el portal de clásico Azure, en la página **configurar inicio de sesión único en los días laborables** , haga clic en **siguiente**. 

    ![Configurar inicio de sesión único] (./media/active-directory-saas-workday-tutorial/IC782934.png "Configurar inicio de sesión único")

13. En la página de **confirmación de inicio de sesión único** , haga clic en **completado**. 

    ![Configurar inicio de sesión único] (./media/active-directory-saas-workday-tutorial/IC782935111.png "Configurar inicio de sesión único")



##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
Para obtener un usuario de prueba que se aprovisione en día laborable, debe ponerse en contacto con el equipo de soporte técnico de la jornada laboral.  
El equipo de soporte técnico de día laborable creará el usuario.

##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-workday-perform-the-following-steps"></a>Para asignar a los usuarios al día laborable, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación de **jornada laboral **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-workday-tutorial/IC782935.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-workday-tutorial/IC767830.png "Sí")
  
Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
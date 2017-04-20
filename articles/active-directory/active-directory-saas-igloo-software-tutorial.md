<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Software de Igloo | Microsoft Azure" 
    description="¡Obtenga información sobre cómo utilizar Software de Igloo con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más!" 
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
    ms.date="10/20/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-igloo-software"></a>Tutorial: Integración de Azure Active Directory con Software de Igloo
  
El objetivo de este tutorial es mostrar la integración de Azure y Igloo Software.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inicio de sesión único de [Igloo Software](http://www.igloosoftware.com/) de suscripción habilitado
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Igloo Software será capaz de inicio de sesión único en la aplicación en el sitio de su compañía de Software Igloo (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
El escenario descrito en este tutorial se compone de los siguientes bloques de creación:

1.  Habilitar la integración de aplicaciones de Software Igloo
2.  Configuración de inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-igloo-software-tutorial/IC783961.png "Escenario")
##<a name="enabling-the-application-integration-for-igloo-software"></a>Habilitar la integración de aplicaciones de Software Igloo
  
El objetivo de esta sección es describen cómo habilitar la integración de aplicaciones de Software de Igloo.

###<a name="to-enable-the-application-integration-for-igloo-software-perform-the-following-steps"></a>Para habilitar la integración de aplicaciones de Software Igloo, siga estos pasos:

1.  En el portal de clásico Azure, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-igloo-software-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-igloo-software-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-igloo-software-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-igloo-software-tutorial/IC749322.png "Agregar una aplicación de gallerry")

6.  En el **cuadro de búsqueda**, escriba **Igloo Software**.

    ![Galería de aplicación] (./media/active-directory-saas-igloo-software-tutorial/IC783962.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **Igloo Software**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Igloo] (./media/active-directory-saas-igloo-software-tutorial/IC783963.png "Igloo")
##<a name="configuring-single-sign-on"></a>Configuración de inicio de sesión único
  
El objetivo de esta sección es describen cómo habilitar los usuarios autenticar al Software Igloo con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
Como parte de este procedimiento, es necesario para cargar un certificado codificado de base 64 en el inquilino de escritorio Central.  
Si no está familiarizado con este procedimiento, vea [cómo convertir un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación de **Software de Igloo** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-igloo-software-tutorial/IC783964.png "Configurar el inicio de sesión único")

2.  En la página **¿cómo le gustaría usuarios a iniciar sesión Igloo Software** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Inicio de sesión único de AD en Microsoft Azure] (./media/active-directory-saas-igloo-software-tutorial/IC783965.png "Inicio de sesión único de AD en Microsoft Azure")

3.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto **Igloo Software de inicio de sesión en dirección URL** , escriba la dirección URL con el siguiente patrón "*https://company.igloocommunities.com/?signin*" y, a continuación, haga clic en **siguiente**.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-igloo-software-tutorial/IC773625.png "Configurar la dirección URL de la aplicación")

4.  En la página **configurar inicio de sesión único en Igloo Software** , para descargar el certificado, haga clic en **Descargar certificado**y, a continuación, guarde el archivo de certificado localmente en el equipo.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-igloo-software-tutorial/IC783966.png "Configurar el inicio de sesión único")

5.  En una ventana de explorador web diferente, inicie sesión en el sitio de su empresa de Software de Igloo como administrador.

6.  Vaya al **Panel de Control**.

    ![Panel de control] (./media/active-directory-saas-igloo-software-tutorial/IC799949.png "Panel de control")

7.  En la ficha **pertenencia** , haga clic en **Configuración de inicio de sesión**.

    ![Configuración de inicio de sesión] (./media/active-directory-saas-igloo-software-tutorial/IC783968.png "Configuración de inicio de sesión")

8.  En la sección Configuración de SAML, haga clic en **Configurar autenticación de SAML**.

    ![Configuración de SAML] (./media/active-directory-saas-igloo-software-tutorial/IC783969.png "Configuración de SAML")

9.  En la sección **Configuración General** , siga estos pasos:

    ![Configuración general] (./media/active-directory-saas-igloo-software-tutorial/IC783970.png "Configuración general")

    1.  En el cuadro de texto **Nombre de la conexión** , escriba un nombre personalizado para la configuración.
    2.  En el portal de clásico Azure, en la página de cuadro de diálogo **configurar inicio de sesión único en Igloo Software** , copie el valor de **Dirección URL de inicio de sesión remota** y péguelo en el cuadro de texto **Dirección URL de inicio de sesión de IdP** .
    3.  En el portal de clásico Azure, en la página de cuadro de diálogo **configurar inicio de sesión único en Igloo Software** , copie el valor de **Dirección URL de cierre de sesión remota** y péguelo en el cuadro de texto **Dirección URL de cierre de sesión de IdP** .
    4.  Como **respuesta de cierre de sesión y tipo de la solicitud de HTTP**, seleccione **Publicar**.
    5.  Crear un archivo de texto desde el certificado descargado.
        
        >[AZURE.TIP]Para obtener más detalles, consulte [cómo convertir un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    6.  Quitar la primera línea y la última línea de la versión de archivo de certificado de texto, copie el texto restante del certificado y péguelo en el cuadro de texto de **Certificado público** .

10. En la **respuesta y la configuración de autenticación**, realice los pasos siguientes:

    ![Configuración de autenticación y de respuesta] (./media/active-directory-saas-igloo-software-tutorial/IC783971.png "Configuración de autenticación y de respuesta")

    1.  Como **Proveedor de identidades**, seleccione **Microsoft ADFS**.
    2.  **Identificador de tipo**, seleccione **Dirección de correo electrónico**.
    3.  En el cuadro de texto de **Atributo de correo electrónico** , escriba la **dirección de correo electrónico**.
    4.  En el cuadro de texto de **Atributo de nombre** , escriba **givenname**.
    5.  En el cuadro de texto del **Último atributo de nombre** , escriba **Apellidos**.

11. Realizar los pasos siguientes para completar la configuración:

    ![Creación de usuario en el inicio de sesión] (./media/active-directory-saas-igloo-software-tutorial/IC783972.png "Creación de usuario en el inicio de sesión")

    1.  Como **la creación del usuario en el inicio de sesión**, seleccione **crear un nuevo usuario en el sitio cuando iniciar sesión**.
    2.  Como la **configuración de inicio de sesión**, seleccione el **botón de SAML de uso en pantalla "Iniciar sesión"**.
    3.  Haga clic en **Guardar**.

12. En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-igloo-software-tutorial/IC783973.png "Configurar el inicio de sesión único")
##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
No hay ningún elemento de acción para configurar el aprovisionamiento de Igloo software de usuario.  
Cuando un usuario asignado intenta iniciar sesión en el Software de Igloo mediante el panel de acceso, Software Igloo comprueba si existe el usuario.  
Si no hay ninguna cuenta de usuario aún, se crea automáticamente por Igloo Software.
##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-igloo-software-perform-the-following-steps"></a>Para asignar a los usuarios a Igloo Software, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación de **Software de Igloo **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-igloo-software-tutorial/IC783974.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-igloo-software-tutorial/IC767830.png "Sí")
  
Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
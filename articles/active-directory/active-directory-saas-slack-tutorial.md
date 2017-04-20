<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con demora | Microsoft Azure" 
    description="¡Obtenga información sobre cómo utilizar demora con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más!" 
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
    ms.date="09/19/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-slack"></a>Tutorial: Integración de Azure Active Directory con margen de demora
  
El objetivo de este tutorial es mostrar la integración de Azure y margen de demora.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un margen de demora de inicio de sesión único habilitado suscripción
  
Después de completar este tutorial, los usuarios de Azure AD asignados al margen de demora podrán al inicio de sesión único en la aplicación en el sitio de su empresa demora (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
El escenario descrito en este tutorial se compone de los siguientes bloques de creación:

1.  Habilitar la integración de las aplicaciones de margen de demora
2.  Configuración de inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-slack-tutorial/IC794980.png "Escenario")

##<a name="enabling-the-application-integration-for-slack"></a>Habilitar la integración de las aplicaciones de margen de demora
  
El objetivo de esta sección es describen cómo habilitar la integración de las aplicaciones de margen de demora.

###<a name="to-enable-the-application-integration-for-slack-perform-the-following-steps"></a>Para habilitar la integración de las aplicaciones de margen de demora, realice los pasos siguientes:

1.  En el portal de clásico Azure, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-slack-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-slack-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-slack-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-slack-tutorial/IC749322.png "Agregar una aplicación de gallerry")

6.  En el **cuadro de búsqueda**, escriba el **margen de demora**.

    ![Galería de aplicación] (./media/active-directory-saas-slack-tutorial/IC794981.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **margen de demora**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Escenario] (./media/active-directory-saas-slack-tutorial/IC796925.png "Escenario")

##<a name="configuring-single-sign-on"></a>Configuración de inicio de sesión único
  
El objetivo de esta sección es describen cómo habilitar los usuarios autenticar al margen de demora con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
Como parte de este procedimiento, es necesario para crear un archivo de certificado codificado de base 64.  
Si no está familiarizado con este procedimiento, vea [cómo convertir un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación de **margen de demora** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-slack-tutorial/IC794982.png "Configurar el inicio de sesión único")

2.  En la página **¿cómo le gustaría usuarios a iniciar sesión el margen de demora** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-slack-tutorial/IC794983.png "Configurar el inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto **Demora de inicio de sesión en la URL** , escriba la dirección URL de su inquilino de margen de demora (por ejemplo: "*https://azuread.slack.com*") y, a continuación, haga clic en **siguiente**.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-slack-tutorial/IC794984.png "Configurar la dirección URL de la aplicación")

4.  En la página **configurar inicio de sesión único en el margen de demora** , para descargar el certificado, haga clic en **Descargar certificado**y, a continuación, guarde el archivo de certificado localmente en el equipo.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-slack-tutorial/IC794985.png "Configurar el inicio de sesión único")

5.  En una ventana de explorador web diferente, inicie sesión en el sitio de su empresa demora como administrador.

6.  Vaya a **a Microsoft Azure AD \> configuración de grupo**.

    ![Configuración de grupo] (./media/active-directory-saas-slack-tutorial/IC794986.png "Configuración de grupo")

7.  En la sección **Configuración de grupo** , haga clic en la ficha **autenticación** y, a continuación, haga clic en **Cambiar configuración**.

    ![Configuración de grupo] (./media/active-directory-saas-slack-tutorial/IC794987.png "Configuración de grupo")

8.  En el cuadro de diálogo **Configuración de autenticación de SAML** , realice los pasos siguientes:

    ![Configuración de SAML] (./media/active-directory-saas-slack-tutorial/IC794988.png "Configuración de SAML")

    1.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en el margen de demora** , copie el valor de **Dirección URL de SAML SSO** y péguelo en el cuadro de texto de **SAML 2.0 extremo (HTTP)** .
    2.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en el margen de demora** , copie el valor de **Dirección URL del emisor** y péguelo en el cuadro de texto del **Emisor del proveedor de identidades** .
    3.  Crear un archivo **codificado base 64** de certificado descargado.
    
        >[AZURE.TIP] Para obtener más detalles, consulte [cómo convertir un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    4.  Abra el certificado codificado de base 64 en el Bloc de notas, copie el contenido de la misma en el Portapapeles y, a continuación, péguela en el cuadro de texto de **Certificado público** .
    5.  Anule la selección de **Permitir a los usuarios cambiar su dirección de correo electrónico**.
    6.  Seleccione **Permitir a los usuarios elegir su propio nombre de usuario**.
    7.  Como **autenticación de que el equipo debe ser usada por**, seleccione **es opcional**.
    8.  Haga clic en **Guardar configuración**.

9.  En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-slack-tutorial/IC794989.png "Configurar el inicio de sesión único")

##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD iniciar sesión en el margen de demora, debe aprovisionar en margen de demora.
  
No hay ningún elemento de acción para configurar el aprovisionamiento de usuario al margen de demora.  
Cuando un usuario asignado intenta iniciar sesión en el margen de demora, se crea automáticamente una cuenta de margen de demora si es necesario.

##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-slack-perform-the-following-steps"></a>Para asignar a los usuarios al margen de demora, realice los pasos siguientes:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación de **margen de demora **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-slack-tutorial/IC794990.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-slack-tutorial/IC767830.png "Sí")
  
Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
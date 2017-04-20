<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con el Portal Mimecast Personal | Microsoft Azure" 
    description="¡Obtenga información sobre cómo utilizar Mimecast Personal Portal con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más!" 
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

#<a name="tutorial-azure-active-directory-integration-with-mimecast-personal-portal"></a>Tutorial: Integración de Azure Active Directory con el Portal de Mimecast Personal
  
El objetivo de este tutorial es mostrar la integración de Azure y Mimecast Personal Portal.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un Portal Personal de Mimecast inicio de sesión único habilitado suscripción
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado Mimecast Personal Portal podrán al inicio de sesión único en la aplicación en el sitio de su empresa de Portal Personal de Mimecast (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
El escenario descrito en este tutorial consta de los siguientes elementos:

1.  Habilitar la integración de aplicación de Portal de Mimecast Personal
2.  Configurar el inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794991.png "Escenario")
##<a name="enabling-the-application-integration-for-mimecast-personal-portal"></a>Habilitar la integración de aplicación de Portal de Mimecast Personal
  
El objetivo de esta sección es describen cómo habilitar la integración de aplicación de Portal de Mimecast Personal.

###<a name="to-enable-the-application-integration-for-mimecast-personal-portal-perform-the-following-steps"></a>Para habilitar la integración de aplicación de Portal de Mimecast Personal, siga estos pasos:

1.  En el portal clásico Azure, en el panel de navegación de la izquierda, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC749322.png "Agregar una aplicación de muestra")

6.  En el **cuadro de búsqueda**, escriba **Mimecast Portal de Personal**.

    ![Galería de aplicación] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794992.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **Portal de Mimecast Personal**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Portal de Mimecast Personal] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794993.png "Portal de Mimecast Personal")
##<a name="configuring-single-sign-on"></a>Configurar el inicio de sesión único
  
El objetivo de esta sección es describen cómo habilitar los usuarios autenticar al Portal de Mimecast Personal con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
Como parte de este procedimiento, es necesario para crear un archivo de certificado codificado de base 64.  
Si no está familiarizado con este procedimiento, vea [cómo convertir un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación de **Portal de Mimecast Personal** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794994.png "Configurar el inicio de sesión único")

2.  En la página **¿cómo le gustaría usuarios a iniciar sesión el Portal de Mimecast Personal** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794995.png "Configurar el inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto **Mimecast Portal de inicio de sesión en la dirección URL Personal** , escriba la dirección URL utilizada por los usuarios para iniciar sesión la aplicación del Portal de Mimecast Personal (por ejemplo: "https://webmail-uk.mimecast.com" o "https://webmail-us.mimecast.com") y, a continuación, haga clic en **siguiente**.

    >[AZURE.NOTE] La dirección URL de inicio de sesión es región específica.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794996.png "Configurar la dirección URL de la aplicación")

4.  En la página **configurar inicio de sesión único en el Portal de Mimecast Personal** , para descargar el certificado, haga clic en **Descargar certificado**y, a continuación, guarde el archivo de certificado localmente en el equipo.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794997.png "Configurar el inicio de sesión único")

5.  En una ventana de explorador web diferente, inicie sesión en el Portal de Personal Mimecast como administrador.

6.  Vaya a **servicios \> aplicación**.

    ![Aplicaciones] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794998.png "Aplicaciones")

7.  Haga clic en **perfiles de autenticación**.

    ![Perfiles de autenticación] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794999.png "Perfiles de autenticación")

8.  Haga clic en **nuevo perfil de autenticación**.

    ![Nuevos perfiles de autenticación] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795000.png "Nuevos perfiles de autenticación")

9.  En la sección **Autenticación de perfil** , siga estos pasos:

    ![Perfiles de autenticación] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795001.png "Perfiles de autenticación")

    1.  En el cuadro de texto **Descripción** , escriba un nombre para la configuración.
    2.  Seleccione **Exigir autenticación SAML Portal Mimecast Personal**.
    3.  Como **proveedor**, seleccione **Azure Active Directory**.
    4.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en el Portal de Mimecast Personal** , copie el valor de **Dirección URL del emisor** y péguelo en el cuadro de texto de la **Dirección URL del emisor** .
    5.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en el Portal de Mimecast Personal** , copie el valor de **Dirección URL de inicio de sesión remota** y péguelo en el cuadro de texto **Dirección URL de inicio de sesión** .
    6.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en el Portal de Mimecast Personal** , copie el valor de **Dirección URL de inicio de sesión remota** y péguelo en el cuadro de texto **Dirección URL de cierre de sesión** .  

        >[AZURE.NOTE] El valor de dirección URL de inicio de sesión y el valor de dirección URL de cierre de sesión están de-en el Portal de Personal Mimecast la misma.

    7.  Crear un archivo **codificado base 64** de certificado descargado.  

        >[AZURE.TIP]Para obtener más detalles, consulte [cómo convertir un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

    8.  Abrir el certificado codificado de base 64 en el Bloc de notas, quite la primera línea ("*--*") y la última línea ("*--*"), copie el contenido restante de la misma en el Portapapeles y, a continuación, péguela en el cuadro de texto de **Certificado de proveedor de identidades (metadatos)** .
    9.  Seleccione **Permitir inicio de sesión único en**.
    10. Haga clic en **Guardar**.

10. En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795002.png "Configurar el inicio de sesión único")
##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD iniciar sesión en el Portal de Mimecast Personal, debe aprovisionar en el Portal de Mimecast Personal.  
En el caso de Mimecast Portal Personal, aprovisionamiento es una tarea manual.
  
Debe registrar un dominio para poder crear usuarios.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar el aprovisionamiento de usuario, siga estos pasos:

1.  Inicie sesión como administrador el **Portal de Mimecast Personal** .

2.  Vaya a **directorios \> interno**.

    ![Directorios] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795003.png "Directorios")

3.  Haga clic en **registrar un dominio nuevo**.

    ![Registrar un dominio nuevo] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795004.png "Registrar un dominio nuevo")

4.  Una vez creado el dominio nuevo, haga clic en **Nueva dirección**.

    ![Nueva dirección] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795005.png "Nueva dirección")

5.  En el cuadro de diálogo nueva dirección, realice los pasos siguientes:

    ![Guardar] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795006.png "Guardar")

    1.  Escriba los atributos de **Dirección de correo electrónico**, **Nombre Global**, **contraseña** y **Confirmar contraseña** de una cuenta AAD válida que desee aprovisionar en los cuadros de texto relacionados.
    2.  Haga clic en **Guardar**.

>[AZURE.NOTE]Puede usar otras herramientas de creación de cuenta de usuario de Portal de Personal Mimecast o API proporcionadas por el Portal de Mimecast Personal a cuentas de usuario AAD de aprovisionamiento.

##<a name="assigning-users"></a>Asignación de usuarios

Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-mimecast-personal-portal-perform-the-following-steps"></a>Para asignar a los usuarios al Portal de Mimecast Personal, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación de **Portal de Mimecast Personal **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795007.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC767830.png "Sí")
  
Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
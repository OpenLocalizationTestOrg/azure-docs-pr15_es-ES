<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con AirWatch | Microsoft Azure" 
    description="Aprenda a usar AirWatch con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más." 
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

#<a name="tutorial-azure-active-directory-integration-with-airwatch"></a>Tutorial: Integración de Azure Active Directory con AirWatch

El objetivo de este tutorial es mostrar la integración de Azure y AirWatch.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inicio de sesión único de AirWatch activado suscripción

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a AirWatch será capaz de inicio de sesión único en la aplicación en el sitio de su empresa de AirWatch (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

El escenario descrito en este tutorial consta de los siguientes elementos:

1.  Habilitar la integración de aplicación para AirWatch
2.  Configurar el inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![AirWatch] (./media/active-directory-saas-airwatch-tutorial/IC791913.png "AirWatch")
##<a name="enabling-the-application-integration-for-airwatch"></a>Habilitar la integración de aplicación para AirWatch

El objetivo de esta sección es describen cómo habilitar la integración de aplicación para AirWatch.

###<a name="to-enable-the-application-integration-for-airwatch-perform-the-following-steps"></a>Para habilitar la integración de aplicación para AirWatch, siga estos pasos:

1.  En el portal clásico Azure, en el panel de navegación de la izquierda, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-airwatch-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-airwatch-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-airwatch-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-airwatch-tutorial/IC749322.png "Agregar una aplicación de muestra")

6.  En el **cuadro de búsqueda**, escriba **AirWatch**.

    ![Galería de aplicación] (./media/active-directory-saas-airwatch-tutorial/IC791914.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **AirWatch**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![AirWatch] (./media/active-directory-saas-airwatch-tutorial/IC791915.png "AirWatch")
##<a name="configuring-single-sign-on"></a>Configurar el inicio de sesión único

El objetivo de esta sección es describen cómo habilitar los usuarios autenticar a AirWatch con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
Como parte de este procedimiento, es necesario para crear un archivo de certificado codificado de base 64.  
Si no está familiarizado con este procedimiento, vea [cómo convertir un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación **AirWatch** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-airwatch-tutorial/IC791916.png "Configurar el inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión AirWatch** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-airwatch-tutorial/IC791917.png "Configurar el inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto **AirWatch inicio de sesión en la URL** , escriba la dirección URL que utilizan los usuarios para iniciar sesión en su aplicación AirWatch (por ejemplo: "*https:// companycode.awmdm.com/AirWatch/Login?gid=companycode*") y, a continuación, haga clic en **siguiente**.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-airwatch-tutorial/IC791918.png "Configurar la dirección URL de la aplicación")

4.  En la página **configurar inicio de sesión único en AirWatch** , haga clic en **Descargar certificado**y, a continuación, guarde el archivo de certificado en el equipo.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-airwatch-tutorial/IC791919.png "Configurar el inicio de sesión único")

5.  En una ventana de explorador web diferente, inicie sesión en el sitio de su empresa AirWatch como administrador.

6.  En el panel de navegación izquierdo, haga clic en **cuentas**y, a continuación, haga clic en **administradores**.

    ![Administradores] (./media/active-directory-saas-airwatch-tutorial/IC791920.png "Administradores")

7.  Expandir el menú **configuración** y, a continuación, haga clic en **Servicios de directorio**.

    ![Configuración] (./media/active-directory-saas-airwatch-tutorial/IC791921.png "Configuración")

8.  Haga clic en la ficha de **usuario** , en el campo **Base DN** del texto, escriba el nombre de dominio y, a continuación, haga clic en **Guardar**.

    ![Usuario] (./media/active-directory-saas-airwatch-tutorial/IC791922.png "Usuario")

9.  Haga clic en la ficha **servidor** .

    ![Servidor] (./media/active-directory-saas-airwatch-tutorial/IC791923.png "Servidor")

10. Realice los pasos siguientes:

    ![Cargar] (./media/active-directory-saas-airwatch-tutorial/IC791924.png "Cargar")

    1.  Como **El tipo de directorio**, seleccione **Ninguno**.
    2.  Seleccione **usar SAML para la autenticación**.
    3.  Para cargar el certificado descargado, haga clic en **cargar**.

11. En la sección **solicitud** , realice los pasos siguientes:

    ![Solicitar] (./media/active-directory-saas-airwatch-tutorial/IC791925.png "Solicitar")

    1.  **Solicitar el tipo de enlace**, seleccione **Publicar**.
    2.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en Airwatch** , copie el valor de **URL de servicio de inicio de sesión único** y, a continuación, péguelo en el cuadro de texto de **Identidad proveedor de inicio de sesión único en URL** .
    3.  Como **NameID formato**, seleccione **Dirección de correo electrónico**.
    4.  Haga clic en **Guardar**.

12. Haga clic en la pestaña del **usuario** nuevamente.

    ![Usuario] (./media/active-directory-saas-airwatch-tutorial/IC791926.png "Usuario")

13. En la sección de **atributo** , siga estos pasos:

    ![Atributo] (./media/active-directory-saas-airwatch-tutorial/IC791927.png "Atributo")

    1.  En el cuadro de texto del **Identificador de objeto** , escriba **http://schemas.microsoft.com/identity/claims/objectidentifier**.
    2.  En el cuadro de texto **nombre de usuario** , escriba **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
    3.  En el cuadro de texto **Nombre para mostrar** , escriba **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
    4.  En el cuadro de texto **nombre** , escriba **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
    5.  En el cuadro de texto **Apellido** , escriba **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.
    6.  En el cuadro de texto de **correo electrónico** , escriba **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
    7.  Haga clic en **Guardar**.

14. En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-airwatch-tutorial/IC791928.png "Configurar el inicio de sesión único")
##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD iniciar sesión en AirWatch, debe aprovisionar en AirWatch.  
En el caso de AirWatch, aprovisionamiento es una tarea manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para crear cuentas de usuario, siga estos pasos:

1.  Inicie sesión como administrador en el sitio de su empresa **AirWatch** .

2.  En el panel de navegación en el lado izquierdo, haga clic en **cuentas**y, a continuación, haga clic en **usuarios**.

    ![Usuarios] (./media/active-directory-saas-airwatch-tutorial/IC791929.png "Usuarios")

3.  En el menú **usuarios** , haga clic en **Vista de lista**y, a continuación, haga clic en **Agregar \> Agregar usuario**.

    ![Agregar usuario] (./media/active-directory-saas-airwatch-tutorial/IC791930.png "Agregar usuario")

4.  En el cuadro de diálogo **Agregar / Editar usuario** , siga estos pasos:

    ![Agregar usuario] (./media/active-directory-saas-airwatch-tutorial/IC791931.png "Agregar usuario")

    1.  Escriba el **nombre de usuario**, **contraseña**, **Confirmar contraseña**, **nombre**, **Apellidos**, **Dirección de correo electrónico** de una cuenta de Azure Active Directory válida que desee aprovisionar en los cuadros de texto relacionados.
    2.  Haga clic en **Guardar**.

>[AZURE.NOTE] Puede usar cualquier otros AirWatch usuario cuenta herramientas de creación o API proporcionan por AirWatch a cuentas de usuario AAD de aprovisionamiento.

##<a name="assigning-users"></a>Asignación de usuarios

Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-airwatch-perform-the-following-steps"></a>Para asignar a los usuarios a AirWatch, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación **AirWatch **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-airwatch-tutorial/IC791932.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-airwatch-tutorial/IC767830.png "Sí")

Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Citrix ShareFile | Microsoft Azure" 
    description="¡Obtenga información sobre cómo utilizar Citrix ShareFile con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más!" 
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

#<a name="tutorial-azure-active-directory-integration-with-citrix-sharefile"></a>Tutorial: Integración de Azure Active Directory con Citrix ShareFile

El objetivo de este tutorial es mostrar la integración de Azure y Citrix ShareFile.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inquilino de Citrix ShareFile

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Citrix ShareFile podrán al inicio de sesión único en la aplicación en el sitio de su empresa de Citrix ShareFile (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

El escenario descrito en este tutorial se compone de los siguientes bloques de creación:

1.  Habilitar la integración de la aplicación para Citrix ShareFile
2.  Configuración de inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773620.png "Escenario")
##<a name="enabling-the-application-integration-for-citrix-sharefile"></a>Habilitar la integración de la aplicación para Citrix ShareFile

El objetivo de esta sección es describen cómo habilitar la integración de la aplicación para Citrix ShareFile.

###<a name="to-enable-the-application-integration-for-citrix-sharefile-perform-the-following-steps"></a>Para habilitar la integración de la aplicación para Citrix ShareFile, siga estos pasos:

1.  En el portal de clásico Azure, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-citrix-sharefile-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-citrix-sharefile-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-citrix-sharefile-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-citrix-sharefile-tutorial/IC749322.png "Agregar una aplicación de gallerry")

6.  En el **cuadro de búsqueda**, escriba **Citrix ShareFile**.

    ![Galería de aplicación] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773621.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **Citrix ShareFile**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Citrix ShareFile] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773622.png "Citrix ShareFile")
##<a name="configuring-single-sign-on"></a>Configuración de inicio de sesión único

El objetivo de esta sección es describen cómo habilitar los usuarios autenticar Citrix ShareFile con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación de **Citrix ShareFile** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Habilitar inicio de sesión único] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773623.png "Habilitar inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión Citrix ShareFile** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773624.png "Configurar el inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto de **Citrix ShareFile inicio de sesión en dirección URL** , escriba la dirección URL con el siguiente patrón `https://<tenant-name>.shareFile.com`y, a continuación, haga clic en **siguiente**.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773625.png "Configurar la dirección URL de la aplicación")

4.  En la página **configurar inicio de sesión único en Citrix ShareFile** para descargar el certificado, haga clic en **Descargar certificado**y, a continuación, guarde el archivo de certificado en el equipo.

    ![Inicio de sesión en ConfigureSingle] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773626.png "Inicio de sesión en ConfigureSingle")

5.  En una ventana de explorador web diferente, inicie sesión en el sitio de su empresa **Citrix ShareFile** como administrador.

6.  En la barra de herramientas en la parte superior, haga clic en **Administrador**.

7.  En el panel de navegación izquierdo, seleccione **Configurar inicio de sesión único**.

    ![Administración de cuenta] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773627.png "Administración de cuenta")

8.  En la **Inicio de sesión único y configuración de SAML 2.0** página del cuadro de diálogo en **Configuración básica**, realice los pasos siguientes:

    ![Inicio de sesión único] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773628.png "Inicio de sesión único")

    1.  Haga clic en **Habilitar SAML**.
    2.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en Citrix ShareFile** , copie el valor de **Id. de entidad** y, a continuación, péguela en la **el emisor IDP / Id. de entidad** cuadro de texto.
    3.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en Citrix ShareFile** , copie el valor de **Dirección URL de inicio de sesión remota** y péguelo en el cuadro de texto **Dirección URL de inicio de sesión** .
    4.  En el portal de clásico Azure, en **la configurar inicio de sesión único en Citrix ShareFile** página de diálogo, copie el valor de **Dirección URL de cierre de sesión remota** y, a continuación, péguelo en el cuadro de texto **Dirección URL de cierre de sesión** .
    5.  Haga clic en **cambiar** junto al campo de **Certificado X.509** y, a continuación, cargar el certificado que ha descargado desde el portal de Azure AD clásico.
        ![Configuración básica] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773629.png "Configuración básica")

9.  En el portal de administración de Citrix ShareFile, haga clic en **Guardar** .

10. En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar inicio de sesión único] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773630.png "Configurar inicio de sesión único")
##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD iniciar sesión en Citrix ShareFile, debe aprovisionar en Citrix ShareFile.  
En el caso de Citrix ShareFile, aprovisionamiento es una tarea manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para crear cuentas de usuario, siga estos pasos:

1.  Inicie sesión en su inquilino de **Citrix ShareFile** .

2.  Haga clic en **Administrar usuarios \> administrar usuarios inicio \> + Crear empleado**.

    ![Crear empleado] (./media/active-directory-saas-citrix-sharefile-tutorial/IC781050.png "Crear empleado")

3.  Escriba el **correo electrónico**, **nombre** y **apellido** de un anuncio de Azure válido de la cuenta quiera suministrar.

    ![Información básica] (./media/active-directory-saas-citrix-sharefile-tutorial/IC799951.png "Información básica")

4.  Haga clic en **Agregar usuario**.

    >[AZURE.NOTE] El titular AAD recibirá un correo electrónico y seguir un vínculo para confirmar su cuenta antes de que esté activa.

>[AZURE.NOTE] Puede usar cualquier otros Citrix ShareFile usuario cuenta herramientas de creación o API proporcionan por Citrix ShareFile a cuentas de usuario AAD de aprovisionamiento.

##<a name="assigning-users"></a>Asignación de usuarios

Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-citrix-sharefile-perform-the-following-steps"></a>Para asignar a los usuarios a Citrix ShareFile, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación de **Citrix ShareFile **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773631.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-citrix-sharefile-tutorial/IC767830.png "Sí")

Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

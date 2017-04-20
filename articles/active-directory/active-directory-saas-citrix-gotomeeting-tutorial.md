<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Citrix GoToMeeting | Microsoft Azure" 
    description="Obtenga información sobre cómo usar Citrix GoToMeeting con Azure Active Directory para habilitar el inicio de sesión único, automatizado de aprovisionamiento y más!." 
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

#<a name="tutorial-azure-active-directory-integration-with-citrix-gotomeeting"></a>Tutorial: Integración de Azure Active Directory con Citrix GoToMeeting  
Se aplica a: Azure

>[AZURE.TIP]Para enviar comentarios, haga clic en [aquí](http://go.microsoft.com/fwlink/?LinkId=522412).

El objetivo de este tutorial es mostrar la integración de Azure y Citrix GoToMeeting. El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inquilino en Citrix GoToMeeting

El escenario descrito en este tutorial consta de los siguientes elementos:

1.  Habilitar la integración de la aplicación para Citrix GoToMeeting
2.  Configurar el inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Configuración] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768996.png "Configuración")



##<a name="enabling-the-application-integration-for-citrix-gotomeeting"></a>Habilitar la integración de la aplicación para Citrix GoToMeeting

El objetivo de esta sección es describen cómo habilitar la integración de la aplicación para Citrix GoToMeeting.

###<a name="to-enable-the-application-integration-for-citrix-gotomeeting-perform-the-following-steps"></a>Para habilitar la integración de la aplicación para Citrix GoToMeeting, realice los pasos siguientes:

1.  En el portal clásico Azure, en el panel de navegación de la izquierda, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de galería] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC749322.png "Agregar una aplicación de galería")

6.  En el **cuadro de búsqueda**, escriba **Citrix GoToMeeting**.

    ![Citrix GoToMeeting] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC701006.png "Citrix GoToMeeting")

7.  En el panel de resultados, seleccione **Citrix GoToMeeting**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Citrix GoToMeeting] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC701012.png "Citrix GoToMeeting")
##<a name="configuring-single-sign-on"></a>Configurar el inicio de sesión único

El objetivo de esta sección es describen cómo habilitar los usuarios autenticar Citrix GoToMeeting con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
Como parte de este procedimiento, es necesario para cargar un certificado codificado de base 64 en su inquilino Citrix GoToMeeting.  
Si no está familiarizado con este procedimiento, vea [cómo convertir un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En la página de integración de aplicación **Citrix GoToMeeting** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar ON de inicio de sesión único** .

    ![Habilitar inicio de sesión único] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768997.png "Habilitar inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión Citrix GoToMeeting** , seleccione **Inicio de sesión único de Microsoft Azure AD**.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768998.png "Configurar inicio de sesión único")


3. En la página **Configurar opciones de aplicación** , haga clic en **siguiente**. 

    ![Habilitar inicio de sesión único] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC7689981.png "Habilitar inicio de sesión único")

4.  En la página **configurar inicio de sesión único en Citrix GoToMeeting** , haga clic en **Descargar certificado**y, a continuación, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768999.png "Configurar inicio de sesión único")

5.  En otra ventana del explorador, inicie sesión en el centro de la organización de Citrix ([https://account.citrixonline.com/organization/administration/](https://account.citrixonline.com/organization/administration/)).

6. Haga clic en la pestaña **Proveedor de identidades** y, a continuación, realice los pasos siguientes:  

    ![Configuración SAML] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC6892321.png "Configuración SAML")

    una. Haga clic en **Manual**

    
    b. En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en Citrix GoToMeeting** , copie el valor de **Dirección URL de la página de inicio de sesión** y, a continuación, péguelo en el cuadro de texto **dirección URL de la página de inicio de sesión** . 

    
    c. En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en Citrix GoToMeeting** , copie el valor de **Dirección URL de la página de Sign-Out** y péguelo en el cuadro de texto **dirección URL de la página de cierre de sesión** .

    
    d. En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en Citrix GoToMeeting** , copie el valor de **Id. de entidad** y péguelo en el cuadro de texto **ID de entidad de proveedor de identidades** .

   
    e. Para cargar el certificado descargado, haga clic en **Cargar certificado**.

    
    f. Haga clic en **Guardar**.

6.  En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **siguiente**.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769000.png "Configurar inicio de sesión único")


7. En la página de **confirmación de inicio de sesión único** , haga clic en **completado**.

    ![Configuración SAML] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC7689982.png "Configuración SAML")





##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario

El objetivo de esta sección es describen cómo habilitar el aprovisionamiento de cuentas de usuario de Active Directory para Citrix GoToMeeting.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar el aprovisionamiento de usuario, siga estos pasos:

1.  En el portal de clásico Azure, en la página de integración de aplicación **Citrix GoToMeeting** , haga clic en **Configurar el aprovisionamiento de usuario** para abrir el cuadro de diálogo **Configurar el aprovisionamiento de usuario** .

    ![Configurar el aprovisionamiento de usuario] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769001.png "Configurar el aprovisionamiento de usuario")

2.  En la página de **configuración y las credenciales de administrador** , realice los pasos siguientes:

    ![Configurar el aprovisionamiento de usuario] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769002.png "Configurar el aprovisionamiento de usuario")

    una. En el cuadro de texto **Nombre de usuario de administración de Citrix GoToMeeting** , escriba el nombre de usuario de un administrador.

    
    b. En el cuadro de texto de **Contraseña de administrador de Citrix GoToMeeting** , la contraseña de administrador.

    
    c. Haga clic en **siguiente**.

3.  En la página de **confirmación** , haga clic en la marca de verificación para guardar la configuración.

4.  Haga clic en el botón **Validar** para comprobar la configuración.


##<a name="assigning-users"></a>Asignación de usuarios

Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-citrix-gotomeeting-perform-the-following-steps"></a>Para asignar a los usuarios a Citrix GoToMeeting, realice los pasos siguientes:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación **Citrix GoToMeeting** , haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769003.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC767830.png "Sí")

Ahora debe esperar 10 minutos y compruebe que la cuenta se ha sincronizado en Dropbox para empresas.

Como primer paso de comprobación, puede comprobar el estado de aprovisionamiento, haciendo clic en el panel en el D en la página de integración de aplicación **Citrix GoToMeeting** en el portal de clásico de Azure.

![Panel] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769004.png "Panel")

Un usuario completado correctamente el aprovisionamiento de ciclo viene indicado por un estado relacionadas:

![Estado de integración] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769005.png "Estado de integración")

Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso.

Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](https://msdn.microsoft.com/library/dn308586).

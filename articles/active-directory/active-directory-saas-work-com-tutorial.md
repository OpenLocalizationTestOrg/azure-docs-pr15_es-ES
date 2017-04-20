<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Work.com | Microsoft Azure" 
    description="Obtenga información sobre cómo usar Work.com con Azure Active Directory para habilitar el inicio de sesión único, automatizado de aprovisionamiento y más!." 
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
    ms.date="09/11/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-workcom"></a>Tutorial: Integración de Azure Active Directory con Work.com
  
El objetivo de este tutorial es mostrar la integración de Azure y Work.com.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inicio de sesión único de Work.com activado suscripción
  
Después de completar este tutorial, los usuarios AAD a quién tiene asignen Work.com acceso será capaz de solo inicio de sesión en la aplicación en el sitio de su empresa de Work.com (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
El escenario descrito en este tutorial se compone de los siguientes bloques de creación:

1.  Habilitar la integración de aplicación para Work.com
2.  Configuración de inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-work-com-tutorial/IC794105.png "Escenario")

##<a name="enabling-the-application-integration-for-workcom"></a>Habilitar la integración de aplicación para Work.com
  
El objetivo de esta sección es describen cómo habilitar la integración de aplicación para Work.com.

###<a name="to-enable-the-application-integration-for-workcom-perform-the-following-steps"></a>Para habilitar la integración de aplicación para Work.com, siga estos pasos:

1.  En el portal de clásico Azure, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-work-com-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-work-com-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-work-com-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-work-com-tutorial/IC749322.png "Agregar una aplicación de gallerry")

6.  En el **cuadro de búsqueda**, escriba **Work.com**.

    ![Galería de aplicación] (./media/active-directory-saas-work-com-tutorial/IC794106.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **Work.com**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Work.com] (./media/active-directory-saas-work-com-tutorial/IC794107.png "Work.com")

##<a name="configuring-single-sign-on"></a>Configuración de inicio de sesión único
  
El objetivo de esta sección es describen cómo habilitar los usuarios autenticar a Work.com con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
Como parte de este procedimiento, debe cargar un certificado en Work.com.com.

>[AZURE.NOTE] Para configurar el inicio de sesión único, debe configurar un nombre de dominio personalizado Work.com todavía. Debe definir al menos un nombre de dominio, pruebe el nombre de dominio e implementar para toda la organización.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  Inicie sesión en su inquilino Work.com como administrador.

2.  Vaya a **configuración**.

    ![Programa de instalación] (./media/active-directory-saas-work-com-tutorial/IC794108.png "Programa de instalación")

3.  En el panel de navegación izquierdo, en la sección **Administrar** , haga clic en **Administración de dominios** para expandir la sección relacionada y, a continuación, haga clic en **Mi dominio** para abrir la página de **Mi dominio** . 

    ![Mi dominio] (./media/active-directory-saas-work-com-tutorial/IC767825.png "Mi dominio")

4.  Para comprobar que su dominio ha sido correctamente el programa de instalación, asegúrese de que se encuentra en "**Paso 4 implementado a los usuarios**" y revise la "**Configuración de mi dominio**".

    ![Dominios implementan al usuario] (./media/active-directory-saas-work-com-tutorial/IC784377.png "Dominios implementan al usuario")

5.  En una ventana de explorador web diferente, inicie sesión en su portal clásica Azure.

6.  En la página de integración de aplicación **Work.com **, haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-work-com-tutorial/IC794109.png "Configurar el inicio de sesión único")

7.  En la página **¿cómo desea que los usuarios para iniciar sesión Work.com** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-work-com-tutorial/IC794110.png "Configurar el inicio de sesión único")

8.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto **Work.com inicio de sesión en la URL** , escriba la dirección URL utilizada por los usuarios para iniciar sesión la aplicación Work.com (por ejemplo: " *http://company.my.salesforce.com*") y, a continuación, haga clic en **siguiente**: 

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-work-com-tutorial/IC794111.png "Configurar la dirección URL de la aplicación")

9.  En la página **configurar inicio de sesión único en Work.com** , para descargar el certificado, haga clic en **Descargar certificado**y, a continuación, guarde el archivo de certificado localmente en el equipo.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-work-com-tutorial/IC794112.png "Configurar el inicio de sesión único")

10. Inicie sesión en su inquilino Work.com.

11. Vaya a **configuración**.

    ![Programa de instalación] (./media/active-directory-saas-work-com-tutorial/IC794108.png "Programa de instalación")

12. Expandir el menú de **Controles de seguridad** y, a continuación, haga clic en **Configuración de inicio de sesión único**.

    ![Configuración de inicio de sesión único] (./media/active-directory-saas-work-com-tutorial/IC794113.png "Configuración de inicio de sesión único")

13. En la página de diálogo **Configuración de inicio de sesión único** , realice los pasos siguientes:

    ![SAML habilitado] (./media/active-directory-saas-work-com-tutorial/IC781026.png "SAML habilitado")

    1.  Seleccione **SAML habilitado**.
    2.  Haga clic en **nuevo**.

14. En la sección **Configuración de inicio de sesión único de SAML** , realice los pasos siguientes:

    ![Configuración de inicio de sesión único de SAML] (./media/active-directory-saas-work-com-tutorial/IC794114.png "Configuración de inicio de sesión único de SAML")

    1.  En el cuadro de texto **nombre** , escriba un nombre para la configuración.  

        >[AZURE.NOTE] Proporcionar un valor para **nombre** rellenar automáticamente el cuadro de texto **Nombre de la API** .

    2.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en Work.com** , copie el valor de **Dirección URL del emisor** y péguelo en el cuadro de texto del **emisor** .
    3.  Para cargar el certificado descargado, haga clic en **Examinar**.
    4.  En el cuadro de texto **Id. de entidad** , escriba **https://salesforce-work.com**.
    5.  Como **Tipo de identidad de SAML**, seleccione **aserción contiene el identificador de la federación desde el objeto de usuario**.
    6.  Como **Ubicación de identidades SAML**, seleccione **identidad está en el elemento NameIdentfier de la instrucción de asunto**.
    7.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en Work.com** , copie el valor de **Dirección URL de inicio de sesión remota** y, a continuación, péguelo en el cuadro de texto **Dirección URL de inicio de sesión del proveedor de identidades** .
    8.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en Work.com** , copie el valor de **Dirección URL de cierre de sesión remota** y, a continuación, péguelo en el cuadro de texto **Dirección URL de cierre de sesión del proveedor de identidades** .
    9.  Como **Servicio iniciado solicitar enlace proveedor**, seleccione **HTTP Post**.
    10. Haga clic en **Guardar**.

15. En el portal de Work.com clásica, en el panel de navegación izquierdo, haga clic en **Administración de dominios** para expandir la sección relacionada y, a continuación, haga clic en **Mi dominio** para abrir la página de **Mi dominio** . 

    ![Mi dominio] (./media/active-directory-saas-work-com-tutorial/IC794115.png "Mi dominio")

16. En la página **Mi dominio** , en la sección **Personalización de marca de página de inicio de sesión** , haga clic en **Editar**.

    ![Página de inicio de sesión de marca] (./media/active-directory-saas-work-com-tutorial/IC767826.png "Página de inicio de sesión de marca")

17. En la página de **Personalización de marca de página de inicio de sesión** , en la sección **Servicio de autenticación** , se muestra el nombre de la **Configuración de SSO de SAML** . Selecciónelo y, a continuación, haga clic en **Guardar**.

    ![Página de inicio de sesión de marca] (./media/active-directory-saas-work-com-tutorial/IC784366.png "Página de inicio de sesión de marca")

18. En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-work-com-tutorial/IC794116.png "Configurar el inicio de sesión único")

##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
Para que usuarios de Azure Active Directory poder iniciar sesión, debe aprovisionar a Work.com.  
En el caso de Work.com, aprovisionamiento es una tarea manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar el aprovisionamiento de usuario, siga estos pasos:

1.  Inicie sesión como administrador el sitio de su empresa Work.com.

2.  Vaya a **configuración**.

    ![Programa de instalación] (./media/active-directory-saas-work-com-tutorial/IC794108.png "Programa de instalación")

3.  Vaya a **Administrar usuarios \> usuarios**.

    ![Administrar usuarios] (./media/active-directory-saas-work-com-tutorial/IC784369.png "Administrar usuarios")

4.  Haga clic en **nuevo usuario**.

    ![Todos los usuarios] (./media/active-directory-saas-work-com-tutorial/IC794117.png "Todos los usuarios")

5.  En la sección Editar usuario, siga estos pasos:

    ![Editar usuarios] (./media/active-directory-saas-work-com-tutorial/IC794118.png "Editar usuarios")

    1.  Escriba el **Apellido**, **Alias**, **correo electrónico**, **nombre de usuario** y atributos de **alias** de una cuenta de Azure Active Directory válida que desee aprovisionar en los cuadros de texto relacionados.
    2.  Seleccione el **rol de** **licencia de usuario** y el **perfil**.
    3.  Haga clic en **Guardar**.  

        >[AZURE.NOTE] Titular de la cuenta de Azure Active Directory recibirá un correo electrónico que incluye un vínculo para confirmar la cuenta antes de que esté activa.

>[AZURE.NOTE] Puede usar cualquier otros Work.com usuario cuenta herramientas de creación o API proporcionan por Work.com a cuentas de usuario AAD de aprovisionamiento.

##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-workcom-perform-the-following-steps"></a>Para asignar a los usuarios a Work.com, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación Work.com, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-work-com-tutorial/IC794119.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-work-com-tutorial/IC767830.png "Sí")
  
Ahora debe esperar 10 minutos y compruebe que la cuenta se ha sincronizado con Work.com.com.
  
Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
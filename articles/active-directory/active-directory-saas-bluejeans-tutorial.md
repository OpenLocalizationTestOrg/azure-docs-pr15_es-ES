<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con BlueJeans | Microsoft Azure" 
    description="Aprenda a usar BlueJeans con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más." 
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

#<a name="tutorial-azure-ad-integration-with-bluejeans"></a>Tutorial: Azure AD integración con BlueJeans

El objetivo de este tutorial es mostrar la integración de Azure y BlueJeans.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inicio de sesión único de BlueJeans activado suscripción

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a BlueJeans será capaz de inicio de sesión único en la aplicación en el sitio de su empresa de BlueJeans (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

El escenario descrito en este tutorial consta de los siguientes elementos:

1.  Habilitar la integración de aplicación para BlueJeans
2.  Configurar el inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-bluejeans-tutorial/IC785860.png "Escenario")
##<a name="enabling-the-application-integration-for-bluejeans"></a>Habilitar la integración de aplicación para BlueJeans

El objetivo de esta sección es describen cómo habilitar la integración de aplicación para BlueJeans.

###<a name="to-enable-the-application-integration-for-bluejeans-perform-the-following-steps"></a>Para habilitar la integración de aplicación para BlueJeans, siga estos pasos:

1.  En el portal clásico Azure, en el panel de navegación de la izquierda, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-bluejeans-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-bluejeans-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-bluejeans-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-bluejeans-tutorial/IC749322.png "Agregar una aplicación de muestra")

6.  En el **cuadro de búsqueda**, escriba **BlueJeans**.

    ![Galería de aplicación] (./media/active-directory-saas-bluejeans-tutorial/IC785861.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **BlueJeans**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![BlueJeans] (./media/active-directory-saas-bluejeans-tutorial/IC785862.png "BlueJeans")
##<a name="configuring-single-sign-on"></a>Configurar el inicio de sesión único

El objetivo de esta sección es describen cómo habilitar los usuarios autenticar a BlueJeans con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación **BlueJeans** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar inicio de sesión único] (./media/active-directory-saas-bluejeans-tutorial/IC785863.png "Configurar inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión BlueJeans** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-bluejeans-tutorial/IC785864.png "Configurar el inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto **BlueJeans inicio de sesión en la URL** , escriba la dirección URL con el siguiente patrón "*https://company.BlueJeans.com*" y, a continuación, haga clic en **siguiente**.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-bluejeans-tutorial/IC785865.png "Configurar la dirección URL de la aplicación")

4.  En la página **configurar inicio de sesión único en BlueJeans** , para descargar el certificado, haga clic en **Descargar certificado**y, a continuación, guarde el archivo de certificado en el equipo.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-bluejeans-tutorial/IC785866.png "Configurar el inicio de sesión único")

5.  En una ventana de explorador web diferente, inicie sesión en el sitio de su empresa **BlueJeans** como administrador.

6.  Vaya a **administración \> configuración del grupo \> seguridad**.

    ![Administrador] (./media/active-directory-saas-bluejeans-tutorial/IC785868.png "Administrador")

7.  En la sección **seguridad** , siga estos pasos:

    ![SAML solo inicio de sesión] (./media/active-directory-saas-bluejeans-tutorial/IC785869.png "SAML solo inicio de sesión")

    1.  Seleccione **SAML solo inicio de sesión**.
    2.  Seleccione **Habilitar aprovisionamiento automático**.

8.  Mover con los siguientes pasos:

    ![Ruta de certificado] (./media/active-directory-saas-bluejeans-tutorial/IC785870.png "Ruta de certificado")

    1.  Haga clic en **Elegir archivo**y, a continuación, cargar el certificado descargado.
    2.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en BlueJeans** , copie el valor de **Dirección URL de inicio de sesión remota** y péguelo en el cuadro de texto **Dirección URL de inicio de sesión** .
    3.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en BlueJeans** , copie el valor de **Dirección URL de cambiar contraseña** y, a continuación, péguelo en el cuadro de texto **Dirección URL de cambio de contraseña** .
    4.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en BlueJeans** , copie el valor de **Dirección URL de cierre de sesión remota** y, a continuación, péguelo en el cuadro de texto **Dirección URL de cierre de sesión** .

9.  Mover con los siguientes pasos:

    ![Guardar cambios] (./media/active-directory-saas-bluejeans-tutorial/IC785874.png "Guardar cambios")

    1.  En el cuadro de texto **id de usuario** , escriba **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**.
    2.  En el cuadro de texto de **correo electrónico** , escriba **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**.
    3.  Haga clic en **Guardar cambios**.

10. En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-bluejeans-tutorial/IC785876.png "Configurar el inicio de sesión único")
##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD iniciar sesión en BlueJeans, debe aprovisionar en BlueJeans.  
En el caso de BlueJeans, aprovisionamiento es una tarea manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para crear cuentas de usuario, siga estos pasos:

1.  Inicie sesión como administrador en el sitio de su empresa **BlueJeans** .

2.  Vaya a **administración \> administrar usuarios \> Agregar usuario**.

    ![Administrador] (./media/active-directory-saas-bluejeans-tutorial/IC785877.png "Administrador")

    >[AZURE.IMPORTANT] La ficha **Agregar usuario** solo está disponible si, en la **pestaña seguridad**, **Habilitar aprovisionamiento automática** está desactivada.

3.  En la sección **Agregar usuario** , siga estos pasos:

    ![Agregar usuario] (./media/active-directory-saas-bluejeans-tutorial/IC785886.png "Agregar usuario")

    1.  Escriba un **Nombre de usuario de BlueJeans**, una **dirección de correo electrónico**, un **Identificador de reunión BlueJeans**, el **Código de acceso de moderador**, un **Nombre completo**, la **empresa** de una cuenta AAD válida que desee aprovisionar en los cuadros de texto relacionados.
    2.  Haga clic en **Agregar usuario**.

>[AZURE.NOTE] Puede usar cualquier otros BlueJeans usuario cuenta herramientas de creación o API proporcionan por BlueJeans a cuentas de usuario AAD de aprovisionamiento.

##<a name="assigning-users"></a>Asignación de usuarios

Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-bluejeans-perform-the-following-steps"></a>Para asignar a los usuarios a BlueJeans, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación **BlueJeans **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-bluejeans-tutorial/IC785887.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-bluejeans-tutorial/IC767830.png "Sí")

Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

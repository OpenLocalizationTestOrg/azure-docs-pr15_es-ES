<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Concur | Microsoft Azure" 
    description="Aprenda a usar Concur con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más." 
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

#<a name="tutorial-azure-active-directory-integration-with-concur"></a>Tutorial: Integración de Azure Active Directory con Concur  


El objetivo de este tutorial es mostrar la integración de Azure y Concur.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inquilino en Concur

El escenario descrito en este tutorial consta de los siguientes elementos:

1.  Habilitar la integración de aplicación para Concur
2.  Configurar el inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-concur-tutorial/IC769766.png "Escenario")

>[AZURE.NOTE] La configuración de su suscripción Concur para SSO federado mediante SAML es una tarea independiente, póngase en contacto con Concur para realizar.

##<a name="enabling-the-application-integration-for-concur"></a>Habilitar la integración de aplicación para Concur

El objetivo de esta sección es describen cómo habilitar la integración de aplicación para Concur.

###<a name="to-enable-the-application-integration-for-concur-perform-the-following-steps"></a>Para habilitar la integración de aplicación para Concur, siga estos pasos:

1.  En el portal clásico Azure, en el panel de navegación de la izquierda, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-concur-tutorial/IC700993.png "Active Directory")

2.  Desde el **directorio de** lista, seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-concur-tutorial/IC700994.png "Aplicaciones")

4.  Para abrir la **Galería de aplicación**, haga clic en **Agregar una aplicación**y, a continuación, haga clic en **Agregar una aplicación para mi organización usar**.

    ![¿Qué desea hacer?] (./media/active-directory-saas-concur-tutorial/IC700995.png "¿Qué desea hacer?")

5.  En el **cuadro de búsqueda**, escriba **Concur**.

    ![Galería de aplicación] (./media/active-directory-saas-concur-tutorial/IC721727.png "Galería de aplicación")

6.  En el panel de resultados, seleccione **Concur**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Contribuyan] (./media/active-directory-saas-concur-tutorial/IC721728.png "Contribuyan")
##<a name="configuring-single-sign-on"></a>Configurar el inicio de sesión único

El objetivo de esta sección es describen cómo habilitar los usuarios autenticar a Concur con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

>[AZURE.NOTE] La configuración de su suscripción Concur para SSO federado mediante SAML es una tarea independiente, póngase en contacto con Concur para realizar.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación **Concur **, haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar inicio de sesión único] (./media/active-directory-saas-concur-tutorial/IC769767.png "Configurar inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión Concur** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-concur-tutorial/IC769768.png "Configurar inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto **Contribuyan inicio de sesión en la URL** , escriba la URL de inicio de sesión de inquilino de concur y, a continuación, haga clic en **siguiente**: 

    ![Configurar iniciar sesión en la dirección URL] (./media/active-directory-saas-concur-tutorial/IC769769.png "Configurar iniciar sesión en la dirección URL")

4.  En la página **configurar inicio de sesión único en Concur** , realice los pasos siguientes.

    ![Configurar iniciar sesión en la dirección URL] (./media/active-directory-saas-concur-tutorial/IC769770.png "Configurar iniciar sesión en la dirección URL")

    1.  Haga clic en descargar los metadatos y el archivo de datos de seguridad, a continuación, en el equipo.
    2.  Póngase en contacto con el equipo de soporte técnico de Concur para configurar SSO para su inquilino.
    3.  Seleccione la configuración de inicio de sesión único de confirmación y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .  

    >[AZURE.NOTE] La configuración de su suscripción Concur para SSO federado mediante SAML es una tarea independiente, póngase en contacto con Concur para realizar.

##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario

El objetivo de esta sección es describen cómo habilitar el aprovisionamiento de cuentas de usuario de Active Directory para Concur.

Para habilitar las aplicaciones en el servicio de gastos, hay tiene que ser la configuración correcta y el uso de un perfil de administrador de servicios Web. No agregue simplemente el rol de administrador de WS a su perfil de administrador existente y funciones administrativas.

Contribuyan a asesores o el Administrador de cliente debe crear un perfil de administrador del servicio Web distintas y el Administrador de cliente debe usar este perfil para las funciones de administrador de servicios Web (por ejemplo, habilitación aplicaciones). Estos perfiles se mantendrán independientes diarias y administración perfil del Administrador de cliente (el perfil de administrador y no debe tener asignado el rol de WSAdmin).

Cuando se crea el perfil que se usará para habilitar la aplicación, escriba el nombre del Administrador de cliente en los campos del perfil de usuario. Esto asignará la propiedad en el perfil. Una vez creada la perfiles, debe iniciar el cliente con este perfil, haga clic en el botón "*Permitir*" para una aplicación de Partner en el menú de servicios Web.

Por los siguientes motivos, esta acción no debe hacerse con el perfil que se utilizan para la administración de T & E normal.

1.  El cliente debe ser la que se hace clic en "*Sí*" en la ventana de cuadro de diálogo que aparece después de habilita una aplicación. Haga clic en reconoce que el cliente está dispuesto para la aplicación Partner tener acceso a sus datos, por lo que el Partner no se hace clic en ese botón Sí.
2.  Si un administrador de cliente que se ha habilitado una aplicación utilizando el Administrador de & E perfil abandona la compañía (resultado: en el perfil que se está desactivado), ninguna aplicación habilitada mediante el perfil no funcionará hasta que la aplicación está habilitada con otro perfil WS administrador activo. Esta es la razón por la que desea para crear distintos administración WS perfiles.
3.  Si un administrador deja la empresa, el nombre asociado al perfil WS administrador puede cambiarse al administrador de reemplazo si lo desea sin afectar a que la aplicación habilitada porque no es necesario que perfil desactivado

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar el aprovisionamiento de usuario, siga estos pasos:

1.  Inicie sesión en su inquilino **Concur** .

2.  En el menú **administración** , seleccione **Servicios Web**.

    ![Concur inquilino] (./media/active-directory-saas-concur-tutorial/IC721729.png "Concur inquilino")

3.  En el lado izquierdo, en el panel de **Servicios Web** , seleccione **Habilitar aplicación de Partner**.

    ![Habilitar la aplicación Partner] (./media/active-directory-saas-concur-tutorial/IC721730.png "Habilitar la aplicación Partner")

4.  En la lista de **Habilitar la aplicación** , seleccione **Azure Active Directory**y, a continuación, haga clic en **Habilitar**.

    ![Microsoft Azure Active Directory] (./media/active-directory-saas-concur-tutorial/IC721731.png "Microsoft Azure Active Directory")

5.  Haga clic en **Sí** para cerrar el cuadro de diálogo **Confirmar la acción** .

    ![Confirmar la acción] (./media/active-directory-saas-concur-tutorial/IC721732.png "Confirmar la acción")

6.  En el portal de clásico Azure, seleccione **Concur** de la lista de aplicaciones para abrir la página de diálogo **Concur** .

7.  Para abrir la página de diálogo **Configurar el aprovisionamiento de usuario** , haga clic en **Configurar el aprovisionamiento de usuario**.

8.  Escriba el nombre de usuario y la contraseña del administrador del Concur y, a continuación, haga clic en **siguiente**.

9.  Para finalizar la configuración, en la página de **confirmación** , haga clic en el botón **completado** .

Ahora puede crear una cuenta de prueba, espere 10 minutos y compruebe que la cuenta se ha sincronizado con Concur.
##<a name="assigning-users"></a>Asignación de usuarios

Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-concur-perform-the-following-steps"></a>Para asignar a los usuarios a Concur, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación **Concur **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-concur-tutorial/IC769771.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-concur-tutorial/IC767830.png "Sí")

Ahora debe esperar 10 minutos y compruebe que la cuenta se ha sincronizado con Concur.

Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

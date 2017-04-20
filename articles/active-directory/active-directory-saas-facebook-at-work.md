<properties
    pageTitle="Tutorial: Integración de Azure Active Directory con Facebook en el trabajo | Microsoft Azure"
    description="Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y Facebook en el trabajo."
    services="active-directory"
    documentationCenter=""
    authors="asmalser-msft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="04/26/2016"
    ms.author="asmalser"/>


# <a name="tutorial-azure-active-directory-integration-with-facebook-at-work"></a>Tutorial: Integración de Azure Active Directory con Facebook en el trabajo

El objetivo de este tutorial es mostrar cómo integrar Facebook en el trabajo con Azure Active Directory (AD Azure).

Integración de Facebook en el trabajo con Azure AD proporciona las siguientes ventajas: 

- Puede controlar en Azure AD quién tiene acceso a Facebook en el trabajo 
- Automáticamente pueden aprovisionar cuenta para los usuarios que se han concedido acceso a Facebook en el trabajo
- Puede habilitar los usuarios automáticamente obtener ha iniciado sesión en Facebook en el trabajo (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar las cuentas en una ubicación central 

Si desea conocer más detalles sobre la integración de la aplicación de SaaS con Azure AD, vea [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory](active-directory-appssoaccess-whatis.md).


## <a name="prerequisites"></a>Requisitos previos 

Para configurar la integración de Azure AD con estrellas CS, necesitará los elementos siguientes:

- Una suscripción de Azure AD
- Facebook en el trabajo con el inicio de sesión único habilitado

Para probar los pasos de este tutorial, debería seguir estas recomendaciones:

- No debe utilizar su entorno de producción, a menos que esto es necesario.
- Si no tiene un entorno de prueba de Azure AD, puede obtener un mes de una prueba [aquí](https://azure.microsoft.com/pricing/free-trial/). 


## <a name="adding-facebook-at-work-from-the-gallery"></a>Agregar Facebook en el trabajo de la Galería
Para configurar la integración de Facebook en el trabajo en Azure AD, debe agregar Facebook en el trabajo de la galería a la lista de aplicaciones de SaaS administradas.

**Para agregar Facebook en el trabajo de la galería, realice los pasos siguientes:**

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**. 

    ![Active Directory][1]

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones][2]

4. Haga clic en **Agregar** en la parte inferior de la página.
    
    ![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Aplicaciones][4]

6. En el cuadro Buscar, escriba **Facebook en el trabajo**.

7. En el panel de resultados, seleccione **Facebook en el trabajo**y, a continuación, haga clic en **completado** para agregar la aplicación.


### <a name="configuring-azure-ad-single-sign-on"></a>Configuración de inicio de sesión único de Azure AD en

Esta sección describe cómo habilitar los usuarios para autenticarse en Facebook en el trabajo con su cuenta de Azure Active Directory, con la federación basada en el protocolo SAML.

**Para configurar el inicio de sesión único Azure AD con Facebook en el trabajo, siga estos pasos:**

1.  Después de agregar Facebook en el trabajo en el portal Azure clásico, haga clic en **Configurar inicio de sesión único**.

2.  En la pantalla **Configurar aplicación URL** , escriba la dirección URL donde los usuarios se inicie sesión en su Facebook en la aplicación de trabajo. Esta es su Facebook en la dirección URL del inquilino de trabajo (ejemplo: https://example.facebook.com/). Una vez que haya terminado, haga clic en **siguiente**.

3.  En una ventana de explorador web diferente, inicie sesión en su Facebook en el sitio de la empresa de trabajo como administrador.

4. Siga las instrucciones que aparecen en la siguiente URL para configurar Facebook en el trabajo para usar Azure AD como un proveedor de identidades: [https://developers.facebook.com/docs/facebook-at-work/authentication/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/authentication/cloud-providers)

5.  Una vez finalizado, vuelva a las ventanas del explorador que muestra el portal Azure clásico, haga clic en la casilla de verificación para confirmar que haya finalizado el procedimiento y luego haga clic en **siguiente** y **completado**.


## <a name="automatically-provisioning-users-to-facebook-at-work"></a>Automáticamente el aprovisionamiento de usuarios a Facebook en el trabajo

Azure AD es compatible con la capacidad de sincronizar automáticamente los detalles de la cuenta de usuarios asignados a Facebook en el trabajo. Esta sincronización automática permite Facebook en el trabajo para obtener los datos que necesita para autorizar a los usuarios para el acceso, antes de que intenta iniciar sesión por primera vez. También anule la suministra los usuarios de Facebook en el trabajo cuando se deniegue el acceso en Azure AD.

Aprovisionamiento automático puede configurar haciendo clic en **Configurar el aprovisionamiento de cuenta** en la ventana de portal clásica Azure.

Para obtener más información sobre cómo configurar aprovisionamiento automático, consulte [https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers)


## <a name="assigning-users-to-facebook-at-work"></a>Asignar usuarios a Facebook en el trabajo

Aprovisiona AAD los usuarios podrán ver Facebook en el trabajo en el Panel de acceso, se debe asignar acceso dentro del portal clásico Azure.

**Para asignar a los usuarios a Facebook en el trabajo:**

1.  En la página de inicio de Facebook en el trabajo en el portal Azure clásica, haga clic en **asignar cuentas**.

2.  En el menú **Mostrar** , seleccione si desea asignar un usuario o grupo a Facebook en el trabajo y haga clic en el botón de marca de verificación.

3.  En la lista resultante, seleccione los usuarios o el grupo al que desea asignar Facebook en el trabajo.

4.  En el pie de página, haga clic en el botón **asignar** .


## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones de SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_04.png





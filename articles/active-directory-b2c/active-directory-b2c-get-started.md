<properties
    pageTitle="Azure Active B2C de directorio: Cree un inquilino de Azure Active Directory B2C | Microsoft Azure"
    description="Un tema sobre cómo crear a un inquilino de Azure Active Directory B2C"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.topic="article"
    ms.devlang="na"
    ms.date="08/30/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-create-an-azure-ad-b2c-tenant"></a>Azure Active B2C de directorio: Cree a un inquilino de Azure AD B2C

Para empezar a usar Microsoft Azure Active Directory (AD Azure) B2C, siga los tres pasos indicados en este artículo.

## <a name="step-1-sign-up-for-an-azure-subscription"></a>Paso 1: Registrarse para una suscripción de Azure

Si ya tiene una suscripción de Azure, omita este paso. Si no es así, registrarse para una [suscripción de Azure](../active-directory/sign-up-organization.md) y obtener acceso a Azure AD B2C.

## <a name="step-2-create-an-azure-ad-b2c-tenant"></a>Paso 2: Cree a un inquilino de Azure AD B2C

Use los siguientes pasos para crear a un nuevo inquilino de Azure AD B2C. Actualmente B2C características no pueden activarse en los inquilinos existentes.

1. Inicie sesión en el [portal de clásica Azure](https://manage.windowsazure.com/) como administrador de suscripción. Este es el mismo trabajo o escuela o la misma cuenta de Microsoft que usó para iniciar sesión en Azure.
2. Haga clic en **nuevo** > **Servicios de aplicación** > **Active Directory** > **directorio** > **Crear personalizado**.

    ![Captura de pantalla de inicio crear un inquilino](./media/active-directory-b2c-get-started/new-directory.png)

3. Elija el **nombre**, el **Nombre de dominio** y el **país o región** de su inquilino.
4. Active la opción que indica que **se trata de un directorio B2C**.
5. Haga clic en la marca de verificación para completar la acción.

    ![Captura de pantalla de la marca de verificación para crear un directorio B2C](./media/active-directory-b2c-get-started/create-b2c-directory.png)

6. El inquilino se creará y aparecerá en la extensión de Active Directory. También se realizan un administrador Global del inquilino. Puede agregar otros administradores globales según sea necesario.

    > [AZURE.IMPORTANT]
    Si va a usar a un inquilino B2C para una aplicación de producción, lea el artículo de la [escala de producción frente a inquilinos de vista previa B2C](active-directory-b2c-reference-tenant-type.md). Tenga en cuenta que existen conocidos problemas al eliminar a un inquilino B2C existente y volver a crearlo con el mismo nombre de dominio. Debe crear a un inquilino B2C con otro nombre de dominio.

## <a name="step-3-navigate-to-the-b2c-features-blade-on-the-azure-portal"></a>Paso 3: Vaya a la hoja de características B2C en el portal de Azure

1. Vaya a la extensión de Active Directory en la barra de navegación en el lado izquierdo.
2. Busque a su inquilino en la ficha **directorio** y haga clic en él.
3. Haga clic en la ficha **Configurar** .
4. Haga clic en el vínculo **Administrar B2C configuración** en la sección **administración de B2C** .

    ![Captura de pantalla de configuración del directorio B2C](./media/active-directory-b2c-get-started/b2c-directory-configure-tab.png)

5. El portal de Azure con el módulo de características B2C que muestra se abrirá en una ventana o una nueva pestaña del navegador.

    > [AZURE.IMPORTANT]
    Puede tardar hasta 2-3 minutos para su inquilino sea accesible en el portal de Azure. Reintentar estos pasos después de algún tiempo se solucionar este problema. Si no es así, póngase en contacto con soporte técnico.

6. Anclar este módulo a su Startboard para facilitar el acceso. (La herramienta de Pin está marcada en rojo en la esquina superior derecha del módulo características).

    ![Captura de pantalla del módulo de características B2C](./media/active-directory-b2c-get-started/b2c-features-blade.png)

    > [AZURE.NOTE]
    Puede administrar usuarios y grupos, configuración de restablecimiento de contraseña de autoservicio y características de personalización de marca de empresa de su inquilino de [Azure portal clásica](https://manage.windowsazure.com/).

## <a name="easy-access-to-the-b2c-features-blade-on-the-azure-portal"></a>Facilitar el acceso a los módulos de características B2C en el portal de Azure

Para mejorar la detectabilidad, hemos agregado un acceso directo para el módulo de características B2C en el portal de Azure.

1. Inicie sesión en el portal de Azure como administrador Global de su inquilino B2C. Si ya ha iniciado sesión en un inquilino diferentes, cambie los inquilinos (en la esquina superior derecha).
2. En el panel de navegación izquierdo, haga clic en **Examinar** .
3. Haga clic en **Azure AD B2C** para tener acceso a los módulos de características B2C.

    ![Captura de pantalla de examinar a B2C módulo de características](./media/active-directory-b2c-get-started/b2c-browse.png)

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre cómo registrar una aplicación con Azure AD B2C y crear una aplicación de inicio rápido lea [Azure Active Directory B2C: registrar la aplicación](active-directory-b2c-app-registration.md).

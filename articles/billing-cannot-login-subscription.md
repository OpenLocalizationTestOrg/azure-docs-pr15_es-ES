<properties
    pageTitle="No puede iniciar sesión en Azure suscripción | Microsoft Azure"
    description="Describe cómo solucionar algunos problemas comunes de inicio de sesión de suscripción de Azure."
    services=""
    documentationCenter=""
    authors="genlin"
    manager="mbaldwin"
    editor=""
    tags="billing"
    />

<tags
    ms.service="billing"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="genli"/>

# <a name="i-cant-sign-in-to-manage-my-azure-subscription"></a>No puedo iniciar sesión en Administrar mi suscripción de Azure

En este artículo le guía a través de algunos de los métodos más comunes para resolver problemas de inicio de sesión.

## <a name="page-hangs-in-the-loading-status"></a>Página deja de responder en el estado de carga

Si se bloquea la página del explorador de internet, pruebe a cada uno de los siguientes pasos hasta que puedan acceder al [portal de Azure](https://portal.azure.com).

-   Actualice la página.
-   Use otro explorador de Internet.
-   Si usa Microsoft Internet Explorer, vaya al portal de Azure usando el modo de exploración de InPrivate. 

    A.  Haga clic en **Herramientas** ![botón herramientas](./media/billing-cannot-login-subscription/Toolsbutton.png) > **seguridad** > **Exploración de InPrivate**.

    B.  Examine el [portal de Azure](https://portal.azure.com)y, a continuación, inicie sesión el portal.

## <a name="error-message-no-subscriptions-found"></a>Mensaje de error "No se han encontrado suscripciones"

Si su cuenta no tiene permisos suficientes, verá un mensaje de error **no se encontró ninguna suscripción** . Puede obtener un administrador de la cuenta en el [Centro de la cuenta](https://account.windowsazure.com/), no los administradores del servicio (SA) o coadministradores (CA).

**Escenario 1: Mensaje de Error aparece en el [portal de Azure](https://portal.azure.com)**

Para resolver este problema, [Agregue el rol de administrador compañeros o el propietario](billing-add-change-azure-subscription-administrator.md) de la cuenta.

**Escenario 2: Se ha recibido el mensaje de Error en el [Centro de la cuenta de Azure](https://account.windowsazure.com/Subscriptions)**

Compruebe si la cuenta que ha usado es el Administrador de la cuenta. Para comprobar que es el Administrador de la cuenta, siga estos pasos:

1.  Inicie sesión en el [portal de Azure](https://portal.azure.com).
2.  En el menú de concentrador, seleccione la **suscripción**.
3.  Seleccione la suscripción que desea comprobar y, a continuación, seleccione **configuración**.
4.  Seleccione **Propiedades**. El Administrador de la cuenta de la suscripción se muestra en el cuadro **Cuenta de administrador** .

## <a name="you-are-automatically-signed-in-as-a-different-user"></a>Automáticamente ha iniciado sesión como un usuario diferente

Este problema puede ocurrir si usa más de una cuenta de usuario en un explorador de Internet.

Para resolver el problema, pruebe uno de los métodos siguientes:

-   Borrar la memoria caché y eliminar las cookies de Internet. En Internet Explorer, haga clic en **Herramientas** ![botón herramientas](./media/billing-cannot-login-subscription/Toolsbutton.png) > **Opciones de Internet** > **Eliminar**. Asegúrese de que están seleccionadas las casillas de los archivos temporales, cookies, contraseña y el historial de exploración y, a continuación, haga clic en eliminar.

-   Restablecer la configuración de Internet Explorer para revertir cualquier configuración personal que ha realizado. Haga clic en **Herramientas** ![botón herramientas](./media/billing-cannot-login-subscription/Toolsbutton.png)> **Opciones de Internet** > **Avanzadas** > Active la casilla **Eliminar configuración personal** > **Restablecer**.

-   Desplácese hasta el portal de Azure en modo de exploración de InPrivate. Haga clic en **Herramientas** ![botón herramientas](./media/billing-cannot-login-subscription/Toolsbutton.png) > **seguridad** > **Exploración de InPrivate**.

## <a name="need-help-contact-support"></a>¿Necesita ayuda? Póngase en contacto con soporte técnico. 

Si necesita ayuda, [póngase en contacto con soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para obtener el problema resuelto rápidamente. 
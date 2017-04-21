<properties
    pageTitle="Compartir una única inquilino de Azure AD entre suscripciones a Office 365 y Azure | Microsoft Azure"
    description="Aprenda a compartir su inquilino de Azure AD de Office 365 y sus usuarios a su suscripción de Azure, o viceversa"
    services=""
    documentationCenter=""
    authors="JiangChen79"
    manager="mbaldwin"
    editor=""
    tags="billing,top-support-issue"/>

<tags
    ms.service="billing"
    ms.workload="na"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/17/2016"
    ms.author="cjiang"/>

# <a name="use-an-existing-office-365-account-with-your-azure-subscription-or-vice-versa"></a>Usar una cuenta existente de Office 365 con su suscripción a Azure o viceversa
Escenario: Ya tiene una suscripción de Office 365 y está listo para una suscripción de Azure, pero desea utilizar las cuentas de usuario de Office 365 existentes para la suscripción de Azure. Como alternativa, un suscriptor de Azure y desea obtener una suscripción de Office 365 para los usuarios de Azure Active Directory existente. Este artículo le muestra lo fácil que es conseguir ambos.

> [AZURE.NOTE] En este artículo no se aplica a los clientes del contrato Enterprise (EA). Si necesita más ayuda en cualquier momento en este artículo, [póngase en contacto con soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para obtener el problema resuelto rápidamente.


## <a name="quick-guidance"></a>Guía rápida

- Si ya tiene una suscripción a Office 365 y desea suscribirse a Azure, use la opción de **iniciar sesión con su cuenta de la organización** . Continúe el proceso de suscripción Azure con su cuenta de Office 365. Consulte [los pasos detallados más adelante en este artículo](#s1).

- Si ya tiene una suscripción de Azure y desea obtener una suscripción de Office 365, inicie sesión en Office 365 con su cuenta de Azure. Continúe con los pasos de la suscripción. Después de completar la suscripción, la suscripción a Office 365 se agrega a la misma instancia de Azure Active Directory al que pertenece la suscripción de Azure. Para obtener más información, vea la sección [pasos detallados más adelante en este artículo](#s2).

>[AZURE.NOTE] Para obtener una suscripción de Office 365, la cuenta utilice para suscripción debe ser miembro del rol de administrador Global o administrador de facturación de directorio de su inquilino de Azure Active Directory. [Obtenga información sobre cómo determinar la función de Azure Active Directory](#how-to-know-your-role-in-your-azure-active-directory).

Para entender lo que ocurre cuando se agrega una suscripción a una cuenta, consulte la [información de fondo más adelante en el artículo](#background-information).

## <a name="detailed-steps"></a>Pasos detallados
<a id="s1"></a>
### <a name="scenario-1-office-365-users-who-plan-to-buy-azure"></a>Escenario 1: Los usuarios de Office 365 que va a comprar Azure
En este escenario, se supone que Kelley Wall es un usuario que tiene una suscripción de Office 365 y planificación para suscribirse a Azure. Existen dos usuarios activos adicionales, Jane y Tricia. Cuenta de Kelley es admin@contoso.onmicrosoft.com.

![Centro de administración de usuario de Office 365](./media/billing-use-existing-office-365-account-azure-subscription/1-office365-users-admin-center.png)

Para iniciar sesión en Azure, siga estos pasos:

1. Suscríbase a Azure en [Azure.com](https://azure.microsoft.com/). Haga clic en **probar de forma gratuita**. En la siguiente página, haga clic en **Iniciar ahora**.

    ![Pruebe gratuitamente Azure.](./media/billing-use-existing-office-365-account-azure-subscription/2-azure-signup-try-free.png)

2. Haga clic en **iniciar sesión con su cuenta de la organización**.

    ![Inicie sesión en Azure.](./media/billing-use-existing-office-365-account-azure-subscription/3-sign-in-to-azure.png)

3. Inicie sesión con su cuenta de Office 365. En este caso, es una cuenta de Office 365 de Kelley.

    ![Inicie sesión con su cuenta de Office 365.](./media/billing-use-existing-office-365-account-azure-subscription/4-sign-in-with-org-account.png)

4. Rellene la información y completar el proceso de suscripción.

    ![Rellene la información y completar la suscripción.](./media/billing-use-existing-office-365-account-azure-subscription/5-azure-sign-up-fill-information.png)

    ![Haga clic en administrar el servicio de inicio.](./media/billing-use-existing-office-365-account-azure-subscription/6-azure-start-managing-my-service.png)

Ahora está listo. En el portal de Azure, verá los mismos usuarios que aparecen. Para comprobarlo, siga estos pasos:

1. En la pantalla que se muestra anteriormente, haga clic en **Iniciar mi servicio de administración** .
2. Haga clic en **Examinar**y, a continuación, haga clic en **Active Directory**.

    ![Haga clic en Examinar y, a continuación, haga clic en Active Directory.](./media/billing-use-existing-office-365-account-azure-subscription/7-azure-portal-browse-ad.png)

3. Haga clic en **usuarios**.

    ![La ficha usuarios](./media/billing-use-existing-office-365-account-azure-subscription/8-azure-portal-ad-users-tab.png)

4. Se muestran todos los usuarios, incluidos a Kelley, según lo esperado.

    ![Lista de usuarios](./media/billing-use-existing-office-365-account-azure-subscription/9-azure-portal-ad-users.png)

<a id="s2"></a>
### <a name="scenario-2-azure-users-who-plan-to-buy-office-365"></a>Escenario 2: Los usuarios de Azure que va a comprar Office 365

En este escenario, Kelley Wall es un usuario que tiene una suscripción de Azure en la cuenta admin@contoso.onmicrosoft.com. Kelley desea suscribirse a Office 365 y utilizar el mismo directorio que ya tiene con Azure.

>[AZURE.NOTE] Para obtener una suscripción de Office 365, la cuenta que utiliza para iniciar sesión debe ser miembro del rol de administrador Global o administrador de facturación de directorio de su inquilino de Azure Active Directory. [Obtenga información sobre cómo saber el rol de Azure Active Directory](#how-to-know-your-role-in-your-azure-active-directory).

![Configuración de suscripción de portal de Azure](./media/billing-use-existing-office-365-account-azure-subscription/10-azure-portal-settings-subscription.png)

![Usuarios Azure Active Directory del portal](./media/billing-use-existing-office-365-account-azure-subscription/11-azure-portal-ads-users.png)

Para suscribirse a Office 365, siga estos pasos:

1. Vaya a la [página de producto de Office 365](https://products.office.com/business)y, a continuación, seleccione un plan que es adecuado para usted.
2. Después de seleccionar el plan, se abrirá la página siguiente. Rellene el formulario. En la esquina superior derecha de la página, haga clic en **iniciar sesión** .

    ![Página de prueba de Office 365](./media/billing-use-existing-office-365-account-azure-subscription/12-office-365-trial-page.png)

3. Inicie sesión con sus credenciales de cuenta. En este ejemplo, es una cuenta de Kelley.

    ![Inicio de sesión en Office 365](./media/billing-use-existing-office-365-account-azure-subscription/13-office-365-sign-in.png)

4. Haga clic en **Probar ahora**.

    ![Confirme su pedido para Office 365.](./media/billing-use-existing-office-365-account-azure-subscription/14-office-365-confirm-your-order.png)

5. En la página de confirmación del pedido, haga clic en **continuar**.

    ![Confirmación de pedido de Office 365](./media/billing-use-existing-office-365-account-azure-subscription/15-office-365-order-receipt.png)

Ahora está listo. En el centro de administración de Office 365, debe ver usuarios desde el directorio de Contoso que se muestran como usuarios activos. Para comprobarlo, siga estos pasos:

1. Abra el centro de administración de Office 365.
2. Expanda **usuarios**y, a continuación, haga clic en **Usuarios activos**.

    ![Usuarios del centro de administración de Office 365](./media/billing-use-existing-office-365-account-azure-subscription/16-office-365-admin-center-users.png)

### <a name="how-to-know-your-role-in-your-azure-active-directory"></a>Cómo saber su rol en Azure Active Directory

1. Inicie sesión en el [portal de Azure](https://portal.azure.com/).
2. Haga clic en **Examinar**y, a continuación, haga clic en **Active Directory**.

    ![Active Directory en el portal de Azure](./media/billing-use-existing-office-365-account-azure-subscription/7-azure-portal-browse-ad.png)

3. Haga clic en **usuarios**.

    ![Usuarios de Active Directory predeterminada de portal de Azure](./media/billing-use-existing-office-365-account-azure-subscription/17-azure-portal-default-ad-users.png)

4. Haga clic en el usuario. En este ejemplo, el usuario está Kelley Wall.

    Observe que el campo de **Función de la organización**.

    ![Identidad de usuario del portal Azure](./media/billing-use-existing-office-365-account-azure-subscription/18-azure-portal-user-identity.png)

## <a name="background-information-about-azure-and-office-365-subscriptions"></a>Información general acerca de las suscripciones de Azure y Office 365
Office 365 y Azure utilizan el servicio de Azure Active Directory para administrar los usuarios y las suscripciones. Considere la posibilidad de un directorio de Azure como un contenedor en el que puede agrupar usuarios y suscripciones. Para usar la misma cuenta de usuario para las suscripciones de Azure y Office 365, debe asegurarse de que las suscripciones se crean en el mismo directorio. Tenga en cuenta los siguientes puntos:

- Una suscripción se creará en un directorio, no al revés.
- Los usuarios pertenecen a directorios, no al revés.
- Una suscripción lleva en el directorio del usuario que crea la suscripción. Como resultado, la suscripción de Office 365 está ligada a la misma cuenta que la suscripción de Azure cuando utilice esa cuenta para crear la suscripción de Office 365.

![Información general](./media/billing-use-existing-office-365-account-azure-subscription/19-background-information.png)

Para obtener más información, vea [cómo Azure suscripciones están asociadas a Azure Active Directory](./active-directory/active-directory-how-subscriptions-associated-directory.md).

>[AZURE.NOTE] Suscripciones de Azure pertenecen a usuarios individuales en el directorio.

>[AZURE.NOTE] Suscripciones a Office 365 pertenecen el propio directorio. Si los usuarios dentro del directorio tienen los permisos necesarios, puede trabajar con en estas suscripciones.

## <a name="next-steps"></a>Pasos siguientes
Si adquirió la Azure y Office 365 suscripciones por separado en el pasado y que desea tener acceso a los inquilinos de Office 365 de la suscripción de Azure, vea [asociar a un inquilino de Office 365 con una suscripción de Azure](billing-add-office-365-tenant-to-azure-subscription.md).

> [AZURE.NOTE] Si sigue teniendo preguntas, [póngase en contacto con soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para obtener el problema resuelto rápidamente.

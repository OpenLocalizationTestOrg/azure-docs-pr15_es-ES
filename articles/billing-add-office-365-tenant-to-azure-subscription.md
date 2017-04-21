<properties
    pageTitle="Usar un inquilino de Office 365 con una suscripción de Azure | Microsoft Azure"
    description="Obtenga información sobre cómo agregar un directorio de Office 365 (inquilino) a una suscripción de Azure para realizar la asociación."
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
    ms.date="09/16/2016"
    ms.author="cjiang"/>

# <a name="associate-an-office-365-tenant-with-an-azure-subscription"></a>Asociar a un inquilino de Office 365 con una suscripción de Azure
Si ha adquirido suscripciones de Azure y Office 365 por separado en el pasado y ahora desea tener acceso a los inquilinos de Office 365 de la suscripción de Azure, es fácil hacerlo. Este artículo le muestra cómo.

> [AZURE.NOTE] En este artículo no se aplica a los clientes del contrato Enterprise (EA).

## <a name="quick-guidance"></a>Guía rápida
Para asociar al inquilino de Office 365 con su suscripción de Azure, utilice su cuenta de Azure para agregar al inquilino de Office 365 y asociar la suscripción Azure el inquilino de Office 365.

## <a name="detailed-steps"></a>Pasos detallados
En este escenario, Kelley Wall es un usuario que tiene una suscripción de Azure en la cuenta kelley.wall@outlook.com. Kelley también tiene una suscripción de Office 365 con la cuenta kelley.wall@contoso.onmicrosoft.com. Ahora Kelley desea acceder al inquilino de Office 365 con la suscripción de Azure.

![Estado de la captura de pantalla de Azure Active Directory](./media/billing-add-office-365-tenant-to-azure-subscription/s31_msa-aad-status.png)

![Usuarios activos del centro de administración de captura de pantalla de Office 365](./media/billing-add-office-365-tenant-to-azure-subscription/s32_office-365-user.png)

### <a name="prerequisites"></a>Requisitos previos
Para que la asociación funcione correctamente, son necesarios los siguientes requisitos previos:

- Debe tener las credenciales del administrador del servicio de suscripción de Azure. Coadministradores no pueden ejecutar un subconjunto de los pasos.
- Debe tener las credenciales de administrador global del inquilino de Office 365.
- La dirección de correo electrónico del administrador del servicio no debe incluirse en el inquilino de Office 365.
- La dirección de correo electrónico del administrador del servicio no coinciden con de cualquier administrador global del inquilino de Office 365.
- Si actualmente usa una dirección de correo electrónico que es una cuenta de Microsoft y una cuenta profesional, cambiar temporalmente el Administrador de su suscripción de Azure para usar otra cuenta de Microsoft. Puede crear una nueva cuenta de Microsoft en la [página de registro de cuenta de Microsoft](https://signup.live.com/).


Para cambiar el administrador del servicio, siga estos pasos:

1. Inicie sesión en el [portal de administración de cuenta](https://account.windowsazure.com/subscriptions).
2. Seleccione la suscripción que desea cambiar.
3. Seleccione **Editar detalles de la suscripción**.

    ![Información de la suscripción de captura de pantalla de Azure, con "Editar detalles de la suscripción" resaltado](./media/billing-add-office-365-tenant-to-azure-subscription/s33_azure-edit-subscription-details.png)

4. En el cuadro **Administrador del servicio** , escriba la dirección de correo electrónico del administrador del servicio de nuevo.

    ![Captura de pantalla del cuadro de diálogo "Editar la suscripción"](./media/billing-add-office-365-tenant-to-azure-subscription/s34_change-subscription-service-admin.png)

### <a name="associate-the-office-365-tenant-with-the-azure-subscription"></a>Asociar al inquilino de Office 365 con la suscripción de Azure
Para asociar al inquilino de Office 365 con la suscripción de Azure, siga estos pasos:

1.  Inicie sesión en el [portal de administración de cuentas](https://account.windowsazure.com/subscriptions) con las credenciales de administrador de servicio.
2.  En el panel izquierdo, seleccione **ACTIVE DIRECTORY**.

    ![Entrada de captura de pantalla de Active Directory](./media/billing-add-office-365-tenant-to-azure-subscription/s35-classic-portal-active-directory-entry.png)

    > [AZURE.NOTE] No verá al inquilino de Office 365. Si ve, omita el paso siguiente.

    ![Captura de pantalla del directorio predeterminado de Azure Active Directory](./media/billing-add-office-365-tenant-to-azure-subscription/s36-aad-tenant-default.png)

3. Agregar al inquilino de Office 365 a su suscripción de Azure.

    una. Seleccione **nuevo** > **directorio** > **Crear personalizado**.

    ![Crear personalizado de captura de pantalla de Azure Active Directory](./media/billing-add-office-365-tenant-to-azure-subscription/s37-aad-custom-create.png)

    b. En la página **Agregar directorio** , bajo **directorio**, seleccione **Usar directorio existente**. A continuación, seleccione **estoy listo para la sesión ahora**y seleccione **completado** ![icono completar](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png).

    ![Captura de pantalla de "Usar el directorio existente"](./media/billing-add-office-365-tenant-to-azure-subscription/s39_add-directory-use-existing.png)

    c. Después de que ha cerrado sesión, inicie sesión con credenciales de administrador global de su inquilino de Office 365.

    ![Inicio de sesión de administrador global de captura de pantalla de Office 365](./media/billing-add-office-365-tenant-to-azure-subscription/s310_sign-in-global-admin-office-365.png)

    d. Seleccione **continuar**.

    ![Captura de pantalla de verificación](./media/billing-add-office-365-tenant-to-azure-subscription/s311_use-contoso-directory-azure-verify.png)

    e. Seleccione **Cerrar sesión ahora**.

    ![Captura de pantalla de cierre de sesión](./media/billing-add-office-365-tenant-to-azure-subscription/s312_use-contoso-directory-azure-confirm-and-sign-out.png)

    f. Inicie sesión en el [portal de administración de cuentas](https://account.windowsazure.com/subscriptions) con las credenciales de administrador de servicio.

    ![Inicio de sesión de captura de pantalla de Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s313_azure-sign-in-service-admin.png)

    g. Verá a su inquilino de Office 365 en el panel.

    ![Captura de pantalla del panel](./media/billing-add-office-365-tenant-to-azure-subscription/s314_office-365-tenant-appear-in-azure.png)

4. Cambie el directorio asociado con la suscripción de Azure.

    una. Seleccione **configuración**.

    ![Icono de configuración del portal clásico de captura de pantalla de Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s315_azure-classic-portal-settings-icon.png)

    b. Seleccione la suscripción de Azure y, a continuación, seleccione **Editar directorio**.
    ![Directorio de edición de suscripción de captura de pantalla de Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s316_azure-subscription-edit-directory.png)

    c. Seleccione **siguiente** ![icono siguiente](./media/billing-add-office-365-tenant-to-azure-subscription/s317_next-icon.png).

    ![Captura de pantalla de "Cambiar el directorio asociado"](./media/billing-add-office-365-tenant-to-azure-subscription/s318_azure-change-associated-directory.png)

    > [AZURE.WARNING] Recibirá una advertencia que se eliminarán todos los coadministradores.

    ![Azure confirmar-directorio-asignación](./media/billing-add-office-365-tenant-to-azure-subscription/s322_azure-confirm-directory-mapping.png)

    >[AZURE.WARNING] Además, también se quitará todos los usuarios de [Control de acceso basado en roles (RBAC)](./active-directory/role-based-access-control-configure.md) con acceso asignadas en los grupos de recursos existentes. Sin embargo, la advertencia que reciba menciones solo la eliminación de coadministradores.

    ![asignar usuarios-quitado-recursos-grupos](./media/billing-add-office-365-tenant-to-azure-subscription/s325_assigned-users-removed-resource-groups.png)

    d. Seleccione **completa** ![icono completar](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png).

5. Ahora puede agregar sus cuentas de organización de Office 365 como coadministradores al inquilino de Azure Active Directory.

    una. Seleccione la pestaña **administradores** y, a continuación, seleccione **Agregar**.

    ![Ficha de administradores de configuración del portal clásico de captura de pantalla de Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s319_azure-classic-portal-settings-administrators.png)

    b. Escriba una cuenta de su inquilino de Office 365, seleccione la suscripción de Azure y luego seleccione **completado** ![icono completar](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png).

    ![Captura de pantalla de Azure Agregar cuadro de diálogo Administrador de compañeros](./media/billing-add-office-365-tenant-to-azure-subscription/s320_azure-add-co-administrator.png)

    c. Vuelva a la pestaña **administradores** . Debería ver mostrando como administrador con la cuenta organizativa.

    ![Captura de pantalla de la ficha administradores](./media/billing-add-office-365-tenant-to-azure-subscription/s321_azure-co-administrator-added.png)

6. A continuación puede probar el acceso con el Administrador de compañeros.

    una. Cerrar sesión en el portal de administración de cuenta.

    b. Abra el [portal de administración de cuenta](https://account.windowsazure.com/subscriptions) o el [portal de Azure](https://portal.azure.com/).

    c. Si la página de inicio de sesión de Azure tiene un vínculo de **iniciar sesión con su cuenta de la organización**, seleccione el vínculo. En caso contrario, omita este paso.

    ![Página de inicio de sesión de captura de pantalla de Azure](./media/billing-add-office-365-tenant-to-azure-subscription/3-sign-in-to-azure.png)

    d. Escriba las credenciales del Administrador con y, a continuación, seleccione **iniciar sesión**.

    ![Página de inicio de sesión de captura de pantalla de Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s324_azure-sign-in-with-co-admin.png)

## <a name="next-steps"></a>Pasos siguientes
Escenarios relacionados incluyen:

- Ya tiene una suscripción a Office 365 y está listo para una suscripción de Azure, pero desea utilizar las cuentas de usuario de Office 365 existentes para la suscripción de Azure.
- Está suscrito a Azure y desea obtener una suscripción de Office 365 para los usuarios de la instancia de Azure Active Directory existente.

Para obtener información sobre cómo realizar estas tareas, consulte [usar la versión existente de Office 365 cuenta con su suscripción de Azure, o viceversa](billing-use-existing-office-365-account-azure-subscription.md).

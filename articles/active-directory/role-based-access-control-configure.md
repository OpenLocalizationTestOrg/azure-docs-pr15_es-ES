<properties
    pageTitle="Usar el control de acceso basado en roles en el portal de Azure | Microsoft Azure"
    description="Introducción en la administración de acceso de Control de acceso basado en roles en el Portal de Azure. Utilice las asignaciones de roles para asignar permisos a los recursos."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="10/10/2016"
    ms.author="kgremban"/>

# <a name="use-role-assignments-to-manage-access-to-your-azure-subscription-resources"></a>Usar las asignaciones de roles para administrar el acceso a los recursos de suscripción de Azure

> [AZURE.SELECTOR]
- [Administrar el acceso de usuario o grupo](role-based-access-control-manage-assignments.md)
- [Administrar el acceso por recurso](role-based-access-control-configure.md)

Azure Control de acceso basado en roles (RBAC) permite la administración de acceso específico para Azure. Using RBAC, puede conceder solo la cantidad de acceso que necesitan los usuarios para realizar su trabajo. Este artículo le ayudará a ponerse en marcha con RBAC en el portal de Azure. Si desea más detalles sobre cómo RBAC le ayuda a administrar el acceso, vea [¿Qué es el Control de acceso basado en roles](role-based-access-control-what-is.md).

## <a name="view-access"></a>Vista de access
Puede ver quién tiene acceso a un recurso, el grupo de recursos o la suscripción de su placa principal en el [portal de Azure](https://portal.azure.com). Por ejemplo, queremos ver quién tiene acceso a uno de nuestros grupos de recursos:

1. Seleccione los **grupos de recursos** en la barra de navegación de la izquierda.  
    ![Grupos de recursos - icono](./media/role-based-access-control-configure/resourcegroups_icon.png)
2. Seleccione el nombre del grupo de recursos en el módulo de **grupos de recursos** .
3. Seleccione **el control de acceso (IAM)** en el menú de la izquierda.  
4. El módulo de control de acceso enumera todos los usuarios, grupos y las aplicaciones que ha concedido acceso al grupo de recursos.  

    ![Módulo de usuarios - vs heredados asignado captura de pantalla de access](./media/role-based-access-control-configure/view-access.png)

Observe que algunos usuarios eran **asignado** acceso mientras que otros **heredados** . Access se asigna específicamente al grupo de recursos o heredado de una asignación a la suscripción principal.

> [AZURE.NOTE] Suscripción clásico y administradores de co se consideran los propietarios de la suscripción en el nuevo modelo RBAC.


## <a name="add-access"></a>Agregar acceso
Conceder acceso a desde el recurso, el grupo de recursos o la suscripción que es el ámbito de la asignación de roles.

1. Seleccione **Agregar** en el módulo de control de acceso.  
2. Seleccione el rol que desea asignar desde el módulo de **Seleccionar una función** .
3. Seleccione el usuario, grupo o aplicación en el directorio que desea conceder acceso a. Puede buscar en el directorio con nombres para mostrar, direcciones de correo electrónico y los identificadores de objeto.  

    ![Agregar módulo de usuarios: captura de pantalla de búsqueda](./media/role-based-access-control-configure/grant-access2.png)

4. Seleccione **Aceptar** para crear la asignación. El menú emergente **Agregar usuario** realiza un seguimiento del progreso.  
    ![Agregar barras de progreso de usuario - captura de pantalla](./media/role-based-access-control-configure/addinguser_popup.png)

Después de agregar una asignación de roles de correctamente, aparecerá en el módulo de **los usuarios** .

## <a name="remove-access"></a>Quitar el acceso

1. Seleccione la asignación de roles en el módulo de control de acceso.
2. Seleccione **Quitar** en el módulo de detalles de la asignación.  
3. Seleccione **Sí** para confirmar la eliminación.  
    ![Módulo de usuarios - quitar de la captura de pantalla de funciones](./media/role-based-access-control-configure/remove-access1.png)

No se puede quitar las asignaciones heredadas. En la imagen siguiente, observe que el botón Quitar está atenuado. En su lugar, mire el detalle **Asignado a** . Vaya al recurso en la lista para quitar la asignación de roles.

![Módulo de usuarios - heredado access deshabilita quita captura de pantalla del botón](./media/role-based-access-control-configure/remove-access2.png)

## <a name="other-tools-to-manage-access"></a>Otras herramientas para administrar el acceso
Puede asignar roles y administrar el acceso con los comandos de Azure RBAC de herramientas que no sea el portal de Azure.  Siga los vínculos para obtener más información sobre los requisitos previos y empezar a trabajar con los comandos de Azure RBAC.

- [Azure PowerShell](role-based-access-control-manage-access-powershell.md)
- [Interfaz de línea de comandos de Azure](role-based-access-control-manage-access-azure-cli.md)
- [API DE REST](role-based-access-control-manage-access-rest.md)

## <a name="next-steps"></a>Pasos siguientes
- [Crear un informe de historial de cambios de access](role-based-access-control-access-change-history-report.md)
- Vea las [funciones integradas de RBAC](role-based-access-built-in-roles.md)
- Definir sus propios [roles personalizados en Azure RBAC](role-based-access-control-custom-roles.md)

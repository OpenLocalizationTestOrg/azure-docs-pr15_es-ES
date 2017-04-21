<properties
   pageTitle="Ver las operaciones de implementación con el portal de | Microsoft Azure"
   description="Describe cómo usar el portal de Azure para detectar errores de implementación del Administrador de recursos."
   services="azure-resource-manager,virtual-machines"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure"
   ms.date="06/15/2016"
   ms.author="tomfitz"/>

# <a name="view-deployment-operations-with-azure-portal"></a>Operaciones de implementación de vista con el Portal de Azure

> [AZURE.SELECTOR]
- [Portal](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [CLI de Azure](resource-manager-troubleshoot-deployments-cli.md)
- [API DE REST](resource-manager-troubleshoot-deployments-rest.md)

Puede ver las operaciones de una implementación a través del portal de Azure. Es posible que más le interese viendo las operaciones cuando haya recibido un error durante la implementación para que este artículo se centra en operaciones erróneas de visualización. El portal ofrece una interfaz que le permite buscar los errores fácilmente y determinar las soluciones posibles.

[AZURE.INCLUDE [resource-manager-troubleshoot-introduction](../includes/resource-manager-troubleshoot-introduction.md)]

## <a name="use-deployment-operations-to-troubleshoot"></a>Utilizar las operaciones de implementación para solucionar problemas

Para ver las operaciones de implementación, siga estos pasos:

1. Para el grupo de recursos implicado en la implementación, observe el estado de la última distribución. Puede seleccionar este estado para obtener más detalles.

    ![estado de implementación](./media/resource-manager-troubleshoot-deployments-portal/deployment-status.png)

2. Se muestra el historial de implementación reciente. Seleccione la implementación que no se pudo.

    ![estado de implementación](./media/resource-manager-troubleshoot-deployments-portal/select-deployment.png)

3. Error al seleccionar **. Haga clic aquí para obtener más información** para ver una descripción de por qué ha fallado la implementación. En la imagen siguiente, el registro DNS no es único.  

    ![ver el error en la implementación](./media/resource-manager-troubleshoot-deployments-portal/view-error.png)

    Este mensaje de error debería ser suficiente para comenzar la solución de problemas. Sin embargo, si necesita más información acerca de las tareas que se han completado, puede ver las operaciones como se muestra en los siguientes pasos.

4. Puede ver todas las operaciones de implementación en el módulo de **implementación** . Seleccione cualquier acción para ver más detalles.

    ![Ver operaciones](./media/resource-manager-troubleshoot-deployments-portal/view-operations.png)

    En este caso, verá que la cuenta de almacenamiento, una red virtual y conjunto de disponibilidad se crearon correctamente. Error en la dirección IP pública y no se han intentado otros recursos.

5. Puede ver los eventos para la implementación mediante la selección de **eventos**.

    ![Ver eventos](./media/resource-manager-troubleshoot-deployments-portal/view-events.png)

6. Ver todos los eventos de la implementación y seleccione uno para obtener más detalles.

    ![Ver eventos](./media/resource-manager-troubleshoot-deployments-portal/see-all-events.png)

## <a name="use-audit-logs-to-troubleshoot"></a>Usar registros de auditoría para solucionar problemas

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

Para ver errores para una implementación, siga estos pasos:

1. Ver los registros de auditoría para un grupo de recursos, seleccione **Los registros de auditoría**.

    ![seleccionar registros de auditoría](./media/resource-manager-troubleshoot-deployments-portal/select-audit-logs.png)

2. En el módulo de **Registros de auditoría** , verá un resumen de las operaciones de recientes para todos los grupos de recursos en la suscripción. Incluye una representación gráfica de la hora y el estado de las operaciones, así como una lista de las operaciones.

    ![Mostrar acciones](./media/resource-manager-troubleshoot-deployments-portal/audit-summary.png)

3. Puede filtrar la vista de los registros de auditoría para concentrarse en determinadas condiciones. Seleccione **filtro** en la parte superior de la hoja de **registros de auditoría** .

    ![filtrar registros](./media/resource-manager-troubleshoot-deployments-portal/filter-logs.png)

4. En el módulo de **filtro** , seleccione condiciones para restringir la vista de los registros de auditoría a sólo aquellas operaciones que desea ver. Por ejemplo, puede filtrar las operaciones para mostrar solo los errores del grupo de recursos.

    ![configurar opciones de filtro](./media/resource-manager-troubleshoot-deployments-portal/set-filter.png)

5. Puede filtrar aún más operaciones definiendo un intervalo de tiempo. La siguiente imagen filtra la vista a un intervalo de tiempo determinado de 20 minutos.

    ![Establecer hora](./media/resource-manager-troubleshoot-deployments-portal/select-time.png)

6. Puede seleccionar cualquiera de las operaciones en la lista. Elija la operación que contiene el error que desea buscar referencias.

    ![Seleccione la operación](./media/resource-manager-troubleshoot-deployments-portal/select-operation.png)
  
7. Verá todos los eventos para esa operación. Observe que los **Identificadores de correlación** de resumen. Este ID se usa para realizar un seguimiento de los eventos relacionados. Puede ser útil cuando se trabaja con el soporte técnico para solucionar un problema. Puede seleccionar cualquier evento para ver detalles sobre el evento.

    ![Seleccione el evento](./media/resource-manager-troubleshoot-deployments-portal/select-event.png)

8. Se muestra detalles sobre el evento. En concreto, preste atención a las **Propiedades** para obtener información sobre el error.

    ![Mostrar detalles de registro de auditoría](./media/resource-manager-troubleshoot-deployments-portal/audit-details.png)

El filtro aplicado en el registro de auditoría se conserva la próxima vez que se ve, por lo que debe cambiar estos valores para ampliar la vista de las operaciones.

## <a name="next-steps"></a>Pasos siguientes

- Para obtener ayuda con la resolución de errores de implementación particular, consulte [resolver errores comunes al implementar recursos de Azure con el Administrador de recursos de Azure](resource-manager-common-deployment-errors.md).
- Para obtener información sobre cómo usar los registros de auditoría para supervisar otros tipos de acciones, consulte [con el Administrador de recursos de operaciones de auditoría](resource-group-audit.md).
- Para validar la implementación antes de ejecutar en él, vea [implementar un grupo de recursos con el Administrador de recursos de Azure plantilla](resource-group-template-deploy.md).

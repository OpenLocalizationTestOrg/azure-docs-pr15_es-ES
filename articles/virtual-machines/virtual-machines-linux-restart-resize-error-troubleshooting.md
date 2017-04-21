<properties
   pageTitle="VM reiniciar o cambiar el tamaño de problemas | Microsoft Azure"
   description="Solucionar problemas de implementación de administrador de recursos de reiniciar o cambiar el tamaño de una máquina Virtual de Linux existente en Azure"
   services="virtual-machines-linux, azure-resource-manager"
   documentationCenter=""
   authors="Deland-Han"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>

<tags
   ms.service="virtual-machines-linux"
   ms.topic="support-article"
   ms.tgt_pltfrm="vm-linux"
   ms.devlang="na"
   ms.workload="required"
   ms.date="09/09/2016"
   ms.author="delhan"/>

# <a name="troubleshoot-resource-manager-deployment-issues-with-restarting-or-resizing-an-existing-linux-virtual-machine-in-azure"></a>Solucionar problemas de implementación de administrador de recursos de reiniciar o cambiar el tamaño de una máquina Virtual de Linux existente en Azure

Cuando intenta iniciar una máquina Virtual ha dejado de Azure (VM), o cambiar el tamaño de una máquina virtual de Azure existente, el error común que encuentra es un error de asignación. Este error se produce cuando el clúster o región no tiene recursos disponibles o no es compatible con el tamaño de memoria virtual solicitado.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="collect-audit-logs"></a>Registros de auditoría recopilar

Para iniciar la solución de problemas, recopilar los registros de auditoría para identificar el error asociado con el problema. Los vínculos siguientes contienen información detallada sobre el proceso:

[Solución de problemas de implementaciones de grupo de recursos con el Portal de Azure](../resource-manager-troubleshoot-deployments-portal.md)

[Operaciones de auditoría con el Administrador de recursos](../resource-group-audit.md)

## <a name="issue-error-when-starting-a-stopped-vm"></a>Problema: Error al iniciar una máquina virtual detenida

Intenta iniciar una máquina virtual detenida pero obtener un error de asignación.

### <a name="cause"></a>Causa

Solicitud para iniciar la máquina virtual ha dejado debe realizarse en el clúster original que hospeda el servicio de nube. Sin embargo, el clúster no tiene espacio libre disponible para completar la solicitud.

### <a name="resolution"></a>Resolución

*   Detener todas las máquinas virtuales en el conjunto de disponibilidad y, a continuación, reinicie cada VM.

  1. Haga clic en **grupos de recursos** > _el grupo de recursos_ > **recursos** > _establece su disponibilidad_ > **máquinas virtuales** > _su máquina virtual_ > **Detener**.

  2. Después de detener todas las máquinas virtuales, seleccione cada una de las VM detenidas y haga clic en Inicio.

*   Vuelva a intentar la solicitud de reinicio en un momento posterior.

## <a name="issue-error-when-resizing-an-existing-vm"></a>Problema: Error al cambiar el tamaño de una máquina virtual existente

Pruebe a cambiar el tamaño de una máquina virtual existente y recibe un error de asignación.

### <a name="cause"></a>Causa

La solicitud para cambiar el tamaño de la máquina virtual debe realizarse en el clúster original que hospeda el servicio de nube. Sin embargo, el clúster no admite el tamaño de memoria virtual solicitado.

### <a name="resolution"></a>Resolución

* Vuelva a intentar la solicitud con un tamaño de la máquina virtual.

* Si no se puede cambiar el tamaño de la máquina virtual solicitado:

  1. Detener todas las máquinas virtuales en el conjunto de disponibilidad.

    * Haga clic en **grupos de recursos** > _el grupo de recursos_ > **recursos** > _establece su disponibilidad_ > **máquinas virtuales** > _su máquina virtual_ > **Detener**.

  2. Después de detener todas las máquinas virtuales, cambiar el tamaño de la máquina virtual que desee a un tamaño más grande.
  3. Seleccione la máquina virtual ha cambiado de tamaño y haga clic en **Inicio**y, a continuación, inicie cada una de las VM ha dejado de.

## <a name="next-steps"></a>Pasos siguientes

Si encuentra problemas cuando se crea una nueva VM Linux en Azure, consulte [solucionar problemas de implementación con la creación de una nueva máquina virtual de Linux en Azure](../virtual-machines/virtual-machines-linux-troubleshoot-deployment-new-vm.md).

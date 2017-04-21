<properties
   pageTitle="Solucionar problemas de implementación de Linux VM-RM | Microsoft Azure"
   description="Solucionar problemas de implementación de administrador de recursos cuando se crea una nueva máquina virtual de Linux en Azure"
   services="virtual-machines-linux, azure-resource-manager"
   documentationCenter=""
   authors="JiangChen79"
   manager="felixwu"
   editor=""
   tags="top-support-issue, azure-resource-manager"/>

<tags
  ms.service="virtual-machines-linux"
  ms.workload="na"
  ms.tgt_pltfrm="vm-linux"
  ms.devlang="na"
  ms.topic="article"
  ms.date="09/09/2016"
  ms.author="cjiang"/>

# <a name="troubleshoot-resource-manager-deployment-issues-with-creating-a-new-linux-virtual-machine-in-azure"></a>Solucionar problemas de implementación de administrador de recursos con la creación de una nueva máquina virtual de Linux en Azure

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="collect-audit-logs"></a>Registros de auditoría recopilar

Para iniciar la solución de problemas, recopilar los registros de auditoría para identificar el error asociado con el problema. Los vínculos siguientes contienen información detallada sobre el proceso para seguir.

[Solución de problemas de implementaciones de grupo de recursos con el Portal de Azure](../resource-manager-troubleshoot-deployments-portal.md)

[Operaciones de auditoría con el Administrador de recursos](../resource-group-audit.md)

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[AZURE.INCLUDE [virtual-machines-linux-troubleshoot-deployment-new-vm-table](../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-table.md)]

**Y:** Si el sistema operativo es Linux generalizado y se cargó o se genera con la configuración general, no habrá errores. Del mismo modo, si es el sistema operativo Linux especializado y se cargó o se genera con la configuración de especializadas, no habrá errores.

**Cargar errores:**

**N<sup>1</sup>:** Si el sistema operativo es Linux generalizado y se carga como especializado, obtendrá un error de tiempo de espera de aprovisionamiento porque la máquina virtual se detiene en la fase de aprovisionamiento.

**N<sup>2</sup>:** Si el sistema operativo es Linux especializado y, a continuación, se carga como generalizado, obtendrá un error de aprovisionamiento ya se está ejecutando la nueva máquina virtual con el nombre del equipo original, nombre de usuario y contraseña.

**Resolución:**

Para resolver ambos estos errores, cargue el VHD original, disponible en local, con la misma configuración que para el sistema operativo (generalizado/especializado). Para cargar generalizado, no olvide ejecutar - activar en primer lugar.

**Capturar errores:**

**N<sup>3</sup>:** Si el sistema operativo es Linux generalizado y se captura como especializado, obtendrá un error de tiempo de espera de aprovisionamiento porque la máquina virtual original no es utilizable tal como se marca como generalizado.

**N<sup>4</sup>:** Si el sistema operativo es Linux especializado y, a continuación, se captura como generalizado, obtendrá un error de aprovisionamiento ya se está ejecutando la nueva máquina virtual con el nombre del equipo original, nombre de usuario y contraseña. Además, el original VM no es utilizable porque está marcado como especializado.

**Resolución:**

Para resolver ambos estos errores, elimine la imagen actual desde el portal y [recuperarla desde los VHD actuales](virtual-machines-linux-capture-image.md) con la misma configuración que para el sistema operativo (generalizado/especializado).

## <a name="issue-custom-gallery-marketplace-image-allocation-failure"></a>Problema: Personalizado o galería o imagen de mercado; Error de asignación
Este error se produce en situaciones cuando la nueva solicitud VM está anclada a un clúster que no es compatible con el tamaño de la memoria virtual que se solicita o no tiene espacio libre disponible para adaptarse a la solicitud.

**Causa 1:** El clúster no es compatible con el tamaño de memoria virtual solicitado.

**Solución 1:**

- Vuelva a intentar la solicitud con un tamaño de la máquina virtual.
- Si no se puede cambiar el tamaño de la máquina virtual solicitado:
  - Detener todas las máquinas virtuales en el conjunto de disponibilidad.
  Haga clic en **grupos de recursos** > *el grupo de recursos* > **recursos** > *establece su disponibilidad* > **máquinas virtuales** > *su máquina virtual* > **Detener**.
  - Después de detener todas las máquinas virtuales, cree la nueva máquina virtual en el tamaño deseado.
  - Iniciar la nueva máquina virtual en primer lugar y seleccione cada una de las VM detenidas y haga clic en **Inicio**.

**Causa 2:** El clúster no tiene liberar los recursos.

**Solución 2:**

- Vuelva a intentar la solicitud más tarde.
- Si la nueva máquina virtual puede ser parte de un conjunto diferente de disponibilidad
  - Crear una nueva máquina virtual en una disponibilidad diferentes establecer (en la misma región).
  - Agregar la nueva máquina virtual a la misma red virtual.

## <a name="next-steps"></a>Pasos siguientes
Si tiene problemas al iniciar una VM Linux detenido o cambiar el tamaño de una VM Linux existente en Azure, consulte [problemas de implementación de solución de problemas de administrador de recursos de reiniciar o cambiar el tamaño de una máquina Virtual de Linux existente en Azure](virtual-machines-linux-restart-resize-error-troubleshooting.md).

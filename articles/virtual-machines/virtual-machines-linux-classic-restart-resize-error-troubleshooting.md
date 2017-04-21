<properties
   pageTitle="VM reiniciar o cambiar el tamaño de problemas | Microsoft Azure"
   description="Solucionar problemas de implementación clásica con reiniciar o cambiar el tamaño de una máquina Virtual de Linux existente en Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="Deland-Han"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>

<tags
   ms.service="virtual-machines-linux"
   ms.topic="support-article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="required"
   ms.date="09/20/2016"
   ms.devlang="na"
   ms.author="delhan"/>

# <a name="troubleshoot-classic-deployment-issues-with-restarting-or-resizing-an-existing-linux-virtual-machine-in-azure"></a>Solucionar problemas de implementación clásica con reiniciar o cambiar el tamaño de una máquina Virtual de Linux existente en Azure

> [AZURE.SELECTOR]
- [Clásico](../articles/virtual-machines/virtual-machines-linux-classic-restart-resize-error-troubleshooting.md)
- [Administrador de recursos](../articles/virtual-machines/virtual-machines-linux-restart-resize-error-troubleshooting.md)

Cuando intenta iniciar una máquina Virtual ha dejado de Azure (VM), o cambiar el tamaño de una máquina virtual de Azure existente, el error común que encuentra es un error de asignación. Este error se produce cuando el clúster o región no tiene recursos disponibles o no es compatible con el tamaño de memoria virtual solicitado.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="collect-audit-logs"></a>Registros de auditoría recopilar

Para iniciar la solución de problemas, recopilar los registros de auditoría para identificar el error asociado con el problema.

En el portal de Azure, haga clic en **Examinar** > **máquinas virtuales** > _la máquina virtual de Linux_ > **configuración** > **registros de auditoría**.

## <a name="issue-error-when-starting-a-stopped-vm"></a>Problema: Error al iniciar una máquina virtual detenida

Intenta iniciar una máquina virtual detenida pero obtener un error de asignación.

### <a name="cause"></a>Causa

Solicitud para iniciar la máquina virtual ha dejado debe realizarse en el clúster original que hospeda el servicio de nube. Sin embargo, el clúster no tiene espacio libre disponible para completar la solicitud.

### <a name="resolution"></a>Resolución

* Crear un nuevo servicio de nube y asociarlo con cualquiera de una región o una red virtual basada en regiones, pero no en un grupo de afinidad.

* Eliminar la máquina virtual detenida.

* Volver a crear la máquina virtual en el nuevo servicio de nube mediante los discos.

* Inicie la máquina virtual vuelve a crear.

Si recibe un error cuando intenta crear un nuevo servicio de nube, vuelva a intentarlo más tarde o cambiar la región para el servicio de nube.

> [AZURE.IMPORTANT] El nuevo servicio de nube tendrá un nuevo nombre y la dirección VIP, por lo que deberá cambiar esa información para todas las dependencias que utilizar esa información para el servicio de nube existente.

## <a name="issue-error-when-resizing-an-existing-vm"></a>Problema: Error al cambiar el tamaño de una máquina virtual existente

Pruebe a cambiar el tamaño de una máquina virtual existente y recibe un error de asignación.

### <a name="cause"></a>Causa

La solicitud para cambiar el tamaño de la máquina virtual debe realizarse en el clúster original que hospeda el servicio de nube. Sin embargo, el clúster no admite el tamaño de memoria virtual solicitado.

### <a name="resolution"></a>Resolución

Reducir el tamaño de memoria virtual solicitado y vuelva a intentar la solicitud de cambio de tamaño.

* Haga clic en **Examinar todos los** > **máquinas virtuales de Windows (clásico)** > _su máquina virtual_ > **configuración** > **tamaño**. Para conocer los pasos detallados, vea [cambiar el tamaño de la máquina virtual](https://msdn.microsoft.com/library/dn168976.aspx).

Si no es posible reducir el tamaño de la máquina virtual, siga estos pasos:

  * Crear un nuevo servicio de nube, se garantiza que no está vinculado a un grupo de afinidad y no asociada a una red virtual que está vinculada a un grupo de afinidad.

  * Crear una máquina virtual nueva, de mayor tamaño en él.

Puede consolidar sus máquinas virtuales en el mismo servicio de nube. Si su servicio de nube existente está asociado a una red virtual basado en la región, puede conectar el nuevo servicio de nube a la red virtual existente.

Si no está asociado a una red virtual basado en la región del servicio de nube existente, debe eliminar las máquinas virtuales en el servicio de nube existente y créelas de nuevo en el nuevo servicio de nube de sus discos. Sin embargo, es importante recordar que el nuevo servicio de nube tendrá un nuevo nombre y la dirección VIP, por lo que deberá actualizar estos para todas las dependencias que usan actualmente esta información para el servicio de nube existente.

## <a name="next-steps"></a>Pasos siguientes

Si encuentra problemas cuando se crea una nueva VM Linux en Azure, consulte [solucionar problemas de implementación con la creación de una nueva máquina virtual de Linux en Azure](../virtual-machines/virtual-machines-linux-troubleshoot-deployment-new-vm.md).

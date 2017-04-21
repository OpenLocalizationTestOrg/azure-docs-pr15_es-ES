<properties
   pageTitle="Solucionar problemas clásico de implementación de Linux VM | Microsoft Azure"
   description="Solucionar problemas de implementación clásica cuando se crea una nueva máquina virtual de Linux en Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="JiangChen79"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>

<tags
  ms.service="virtual-machines-linux"
  ms.workload="na"
  ms.tgt_pltfrm="vm-linux"
  ms.devlang="na"
  ms.topic="article"
  ms.date="09/06/2016"
  ms.author="cjiang"/>

# <a name="troubleshoot-classic-deployment-issues-with-creating-a-new-linux-virtual-machine-in-azure"></a>Solucionar problemas de implementación clásica con la creación de una nueva máquina virtual de Linux en Azure

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-selectors](../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-selectors-include.md)]

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="collect-audit-logs"></a>Registros de auditoría recopilar

Para iniciar la solución de problemas, recopilar los registros de auditoría para identificar el error asociado con el problema.

En el portal de Azure, haga clic en **Examinar** > **máquinas virtuales** > *la máquina virtual de Windows* > **configuración** > **registros de auditoría**.

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[AZURE.INCLUDE [virtual-machines-linux-troubleshoot-deployment-new-vm-table](../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-table.md)]

**Y:** Si el sistema operativo es Linux generalizado y se cargó o se genera con la configuración general, no habrá errores. Del mismo modo, si es el sistema operativo Linux especializado y se cargó o se genera con la configuración de especializadas, no habrá errores.

**Cargar errores:**

**N<sup>1</sup>:** Si el sistema operativo es Linux generalizado y se carga como especializado, obtendrá un error de tiempo de espera de aprovisionamiento porque la máquina virtual se detiene en la fase de aprovisionamiento.

**N<sup>2</sup>:** Si el sistema operativo es Linux especializado y, a continuación, se carga como generalizado, obtendrá un error de aprovisionamiento ya se está ejecutando la nueva máquina virtual con el nombre del equipo original, nombre de usuario y contraseña.

**Resolución:**

Para resolver ambos estos errores, cargue el VHD original, disponible en local, con la misma configuración que para el sistema operativo (generalizado/especializado). Para cargar generalizado, no olvide ejecutar - activar en primer lugar. Para obtener más información, vea [crear y cargar un disco duro Virtual que contiene el sistema operativo Linux](virtual-machines-linux-classic-create-upload-vhd.md) .

**Capturar errores:**

**N<sup>3</sup>:** Si el sistema operativo es Linux generalizado y se captura como especializado, obtendrá un error de tiempo de espera de aprovisionamiento porque la máquina virtual original no es utilizable tal como se marca como generalizado.

**N<sup>4</sup>:** Si el sistema operativo es Linux especializado y, a continuación, se captura como generalizado, obtendrá un error de aprovisionamiento ya se está ejecutando la nueva máquina virtual con el nombre del equipo original, nombre de usuario y contraseña. Además, el original VM no es utilizable porque está marcado como especializado.

**Resolución:**

Para resolver ambos estos errores, elimine la imagen actual desde el portal y [recuperarla desde los VHD actuales](virtual-machines-linux-classic-capture-image.md) con la misma configuración que para el sistema operativo (generalizado/especializado).

## <a name="issue-custom-gallery-marketplace-image-allocation-failure"></a>Problema: Personalizado o galería o imagen de mercado; Error de asignación
Este error se produce en situaciones cuando la nueva solicitud VM se envía a un clúster que no tiene espacio libre disponible para adaptarse a la solicitud, o no es compatible con el tamaño de la memoria virtual que se solicita. No es posible combinar diferentes series de máquinas virtuales en el mismo servicio de nube. Así que si desea crear una nueva máquina virtual de un tamaño distinto de lo que puede admitir su servicio de nube, la solicitud de cálculo se producirá un error.

Dependiendo de las restricciones del servicio de nube que se utiliza para crear la nueva máquina virtual, puede que encuentre un error debido a una de dos situaciones.

**Causa 1:** El servicio de nube está anclado a un clúster específico o está vinculado a un grupo de afinidad y, por tanto, anclado a un clúster específico por diseño. Solicitudes de modo que el nuevo recurso de cálculo en que se trata de afinidad del grupo en el mismo clúster donde se hospedan los recursos existentes. Sin embargo, el mismo clúster puede no admite el tamaño de memoria virtual solicitado o tiene suficiente espacio disponible, lo que genera un error de asignación. Esto ocurre si se crean los nuevos recursos a través de un nuevo servicio de nube o un servicio de nube existente.

**Solución 1:**

- Crear un nuevo servicio de nube y asociarlo a una región o en una red virtual de región.
- Crear una nueva máquina virtual en el nuevo servicio de nube.
  Si recibe un error cuando intenta crear un nuevo servicio de nube, vuelva a intentarlo más tarde o cambiar la región para el servicio de nube.

> [AZURE.IMPORTANT] Si intenta crear una nueva máquina virtual en un servicio de nube existente pero no pudo y tenía que crear un nuevo servicio de nube para la nueva máquina virtual, puede consolidar sus máquinas virtuales en el mismo servicio de nube. Para ello, elimine las máquinas virtuales en el servicio de nube existente y capturar a ellos desde sus discos en el nuevo servicio de nube. Sin embargo, es importante recordar que el nuevo servicio de nube tendrá un nuevo nombre y la dirección VIP, por lo que deberá actualizar estos para todas las dependencias que usan actualmente esta información para el servicio de nube existente.

**Causa 2:** El servicio de nube está asociado con una red virtual que está vinculada a un grupo de afinidad, por lo que está anclado a un clúster específico por diseño. Nuevo recurso de cálculo todas las solicitudes de este grupo afinidad, por tanto, se ha intentado en el mismo clúster donde se hospedan los recursos existentes. Sin embargo, el mismo clúster puede no admite el tamaño de memoria virtual solicitado o tiene suficiente espacio disponible, lo que genera un error de asignación. Esto ocurre si se crean los nuevos recursos a través de un nuevo servicio de nube o un servicio de nube existente.

**Solución 2:**

- Crear una nueva red virtual regional.
- Crear la nueva máquina virtual en la nueva red virtual.
- [Conectar la red virtual existente](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/) a la nueva red virtual. Obtenga más información acerca de las [redes virtuales regionales](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/). Como alternativa, puede [migrar su red virtual a una red virtual regional basada en el grupo afinidad](https://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/)y, a continuación, cree la nueva máquina virtual.

## <a name="next-steps"></a>Pasos siguientes
Si tiene problemas al iniciar una VM Linux detenido o cambiar el tamaño de una VM Linux existente en Azure, consulte [solucionar problemas de implementación clásica con reiniciar o cambiar el tamaño de una máquina Virtual de Linux existente en Azure](virtual-machines-linux-classic-restart-resize-error-troubleshooting.md).

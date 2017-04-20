<properties
   pageTitle="Se produce un error en la copia de seguridad de Azure VM: no se puede comunicar con el agente de máquina virtual para el estado de instantánea: tarea de instantánea VM sub agotado | Microsoft Azure"
   description="Causas de los síntomas y resolución de errores de copia de seguridad de Azure VM relacionados con no pudo comunicarse con el agente de máquina virtual para el estado de la instantánea. Tarea de instantánea de VM sub agotado error"
   services="backup"
   documentationCenter=""
   authors="genlin"
   manager="cfreeman"
   editor=""/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="jimpark; markgal;genli"/>

# <a name="azure-vm-backup-fails-could-not-communicate-with-the-vm-agent-for-snapshot-status---snapshot-vm-sub-task-timed-out"></a>Se produce un error en la copia de seguridad de Azure VM: no se puede comunicar con el agente de máquina virtual para el estado de instantánea: tarea de instantánea VM sub agotado el tiempo de espera

## <a name="summary"></a>Resumen

Después de registrar y programar una máquina Virtual (VM) para la copia de seguridad de Azure, el servicio de copia de seguridad de Azure inicia el trabajo de copia de seguridad en el tiempo programado mediante la comunicación con la extensión de copia de seguridad de la máquina virtual para tomar una instantánea de punto en el tiempo. Hay condiciones que pueden impedir que la instantánea activa lo que lleva a un error de copia de seguridad. Este artículo proporciona pasos para solucionar problemas relacionados con errores de copia de seguridad de Azure VM relacionado con el error de tiempo de espera de instantánea.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="symptom"></a>Síntoma

Copia de seguridad de Microsoft Azure de infraestructura como servicio (IaaS) VM se produce un error y devuelve el siguiente mensaje de error de los detalles del error de trabajo en el [portal de Azure](https://portal.azure.com/):

**No se puede comunicar con el agente de máquina virtual para el estado de instantánea: tarea de instantánea VM sub agotado el tiempo de espera.**

## <a name="cause"></a>Causa
Hay cuatro causas comunes de este error:

- La máquina virtual no tiene acceso a Internet.
- El agente de Microsoft Azure VM instalado en la máquina virtual está actualizado (para máquinas virtuales de Linux).
- No se puede actualizar o cargar la extensión de copia de seguridad.
- No se puede recuperar el estado de instantáneas o no se puede tomar las instantáneas.

## <a name="cause-1-the-vm-does-not-have-internet-access"></a>Causa 1: La máquina virtual no tiene acceso a Internet
Por el requisito de implementación, la máquina virtual no tiene acceso a Internet o tiene las restricciones en el lugar que evitar el acceso a la infraestructura de Azure.

La extensión de copia de seguridad requiere conectividad con las direcciones IP públicas Azure funcione correctamente. Esto es porque envía comandos a un extremo de almacenamiento de Azure (dirección URL de HTTP) para administrar las instantáneas de la máquina virtual. Si la extensión no tiene acceso a Internet, la copia de seguridad no se completa.

### <a name="solution"></a>Solución
En este escenario, use uno de los siguientes métodos para solucionar el problema:

- Intervalos de IP blanca del centro de datos de Azure
- Crear una ruta de acceso para el flujo del tráfico HTTP

### <a name="to-whitelist-the-azure-datacenter-ip-ranges"></a>Para blanca la Azure intervalos IP del centro de datos

1. Obtener la [lista de direcciones IP del centro de datos Azure](https://www.microsoft.com/download/details.aspx?id=41653) para estar en la lista blanca.
2. Desbloquear las direcciones IP mediante el cmdlet NetRoute de nuevo. Ejecutar este cmdlet en la máquina virtual de Azure en una ventana de PowerShell elevada (ejecutar como administrador).
3. Agregar reglas para el grupo de seguridad de red (GSN) si tiene uno para permitir el acceso a las direcciones IP.

### <a name="to-create-a-path-for-http-traffic-to-flow"></a>Para crear una ruta de acceso para el flujo del tráfico HTTP

1. Si tiene las restricciones de red en su lugar (por ejemplo, un GSN), implementar un servidor de proxy HTTP para enrutar el tráfico.
2. Si tiene un grupo de seguridad de la red (GSN), agregue reglas para permitir el acceso a Internet desde el servidor proxy HTTP.

Obtenga información sobre cómo [configurar un servidor proxy HTTP para las copias de seguridad de la máquina virtual](backup-azure-vms-prepare.md#using-an-http-proxy-for-vm-backups).

## <a name="cause-2-the-microsoft-azure-vm-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Causa 2: El agente de Microsoft Azure VM instalado en la máquina virtual está actualizado (para máquinas virtuales de Linux)

### <a name="solution"></a>Solución
Más errores relacionados con el agente o extensión para máquinas virtuales de Linux son causados por problemas que afectan a un agente VM antiguo. Como regla general, los primeros pasos para solucionar este problema son las siguientes:

1. [Instalar al agente de Azure VM más reciente](https://github.com/Azure/WALinuxAgent).
2. Asegúrese de que está ejecutando el agente de Azure en la máquina virtual. Para ello, ejecute el siguiente comando:```ps -e```

    Si este proceso no se está ejecutando, use los comandos siguientes para reiniciarlo.

    Para Ubuntu:```service walinuxagent start```

    Para otras distribuciones: "' waagent inicio del servicio
```

3. [Configure the auto restart agent](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).

4. Run a new test backup. If the failures persist, please collect logs from the following folders for further debugging.

    We require the following logs from the customer’s VM:

    - /var/lib/waagent/*.xml
    - /var/log/waagent.log
    - /var/log/azure/*

If we require verbose logging for waagent, follow these steps to enable this:

1. In the /etc/waagent.conf file, locate the following line:

    Enable verbose logging (y|n)

2. Change the **Logs.Verbose** value from n to y.
3. Save the change, and then restart waagent by following the previous steps in this section.

## Cause 3: The backup extension fails to update or load
If extensions cannot be loaded, then Backup fails because a snapshot cannot be taken.

### Solution
For Windows guests:

1. Verify that the iaasvmprovider service is enabled and has a startup type of automatic.
2. If this is not the configuration, enable the service to determine whether the next backup succeeds.

For Linux guests:

The latest version of VMSnapshot Linux (extension used by backup) is 1.0.91.0

If the backup extension still fails to update or load, you can force the VMSnapshot extension to be reloaded by uninstalling the extension. The next backup attempt will reload the extension.

### To uninstall the extension

1. Go to the [Azure portal](https://portal.azure.com/).
2. Locate the particular VM that has backup problems.
3. Click **Settings**.
4. Click **Extensions**.
5. Click **Vmsnapshot Extension**.
6. Click **uninstall**.

This will cause the extension to be reinstalled during the next backup.

## Cause 4: The snapshots status cannot be retrieved or the snapshots cannot be taken
VM backup relies on issuing snapshot command to underlying storage. The backup can fail because it has no access to storage or because of a delay in snapshot task execution.

### Solution
The following conditions can cause snapshot task failure:

| Cause | Solution |
| ----- | ----- |
| VMs that have Microsoft SQL Server Backup configured. By default, VM Backup runs a VSS Full backup on Windows VMs. On VMs that are running SQL Server-based servers and on which SQL Server Backup is configured, snapshot execution delays may occur. | Set following registry key if you are experiencing backup failures because of snapshot issues.<br><br>[HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT] "USEVSSCOPYBACKUP"="TRUE" |
| VM status is reported incorrectly because the VM is shut down in RDP. If you shut down the virtual machine in RDP, check the portal to determine whether that VM status is reflected correctly. | If it’s not, shut down the VM in the portal by using the ”Shutdown” option in the VM dashboard. |
| Many VMs from the same cloud service are configured to back up at the same time. | It’s a best practice to spread out the VMs from the same cloud service to have different backup schedules. |
| The VM is running at high CPU or memory usage. | If the VM is running at high CPU usage (more than 90 percent) or high memory usage, the snapshot task is queued and delayed and eventually times out. In this situation, try on-demand backup. |
|The VM cannot get host/fabric address from DHCP.|DHCP must be enabled inside the guest for IaaS VM Backup to work.  If the VM cannot get host/fabric address from DHCP response 245, then it cannot download ir run any extensions. If you need a static private IP, you should configure it through the platform. The DHCP option inside the VM should be left enabled. View more information about [Setting a Static Internal Private IP](../virtual-network/virtual-networks-reserved-private-ip.md).|

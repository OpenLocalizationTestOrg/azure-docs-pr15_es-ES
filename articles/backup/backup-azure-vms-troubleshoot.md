<properties
    pageTitle="Solucionar problemas de copia de seguridad de Azure máquina virtual | Microsoft Azure"
    description="Solucionar problemas de copia de seguridad y restauración de máquinas virtuales de Windows Azure"
    services="backup"
    documentationCenter=""
    authors="trinadhk"
    manager="shreeshd"
    editor=""/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/26/2016"
    ms.author="trinadhk;jimpark;"/>


# <a name="troubleshoot-azure-virtual-machine-backup"></a>Solucionar problemas de copia de seguridad de Azure máquina virtual

> [AZURE.SELECTOR]
- [Depósito de servicios de recuperación](backup-azure-vms-troubleshoot.md)
- [Depósito de copia de seguridad](backup-azure-vms-troubleshoot-classic.md)

Puede solucionar errores mientras mediante copia de seguridad de Azure con la información que aparece en la tabla siguiente.

## <a name="backup"></a>Copia de seguridad

| Operación de copia de seguridad | Detalles del error | Solución alternativa |
| -------- | -------- | -------|
|Copia de seguridad|    No se pudo realizar la operación como VM ya no existe. -Detener la protección de máquina virtual sin eliminar datos de copia de seguridad. Obtener más detalles en http://go.microsoft.com/fwlink/?LinkId=808124   |Esto ocurre cuando se elimina la máquina virtual principal pero sigue de la directiva de copia de seguridad buscar una máquina virtual para realizar copia de seguridad. Para corregir este error: <ol><li> Vuelva a crear la máquina virtual con el mismo nombre y el mismo nombre de grupo de recursos [nombre de servicio de nube]<br>(OR)</li><li> Detener la protección de máquina con o sin eliminar datos de copia de seguridad. [Más detalles](http://go.microsoft.com/fwlink/?LinkId=808124)</li></ol>|
|Copia de seguridad|No se pudo comunicarse con el agente de máquina virtual para el estado de la instantánea. -Asegúrese de que la máquina virtual tiene acceso a internet. Además, actualice al agente de VM como se mencionó en la Guía de solución de problemas en http://go.microsoft.com/fwlink/?LinkId=800034 |Este error se produce si hay un problema con el agente de VM o se bloquea el acceso de red a la infraestructura de Azure de alguna forma. Obtenga más información sobre VM de depuración instantánea problemas.<br> Si el agente VM no está causando problemas, a continuación, reinicie la máquina virtual. A veces un estado VM incorrecto puede provocar problemas y reiniciar la máquina virtual restablece este "mal estado"|
|Copia de seguridad|    Error en la operación de extensión de servicios de recuperación. -Asegúrese de que está presente en la máquina virtual agente de máquina virtual más reciente y se está ejecutando el servicio de agente. Vuelva a intentar la operación copia de seguridad y si se produce un error, póngase en contacto con el soporte técnico de Microsoft.|   Este error se produce cuando el agente VM está actualizado. Consulte la sección "Actualizar el agente de VM" para actualizar al agente de máquina virtual.|
|Copia de seguridad |No existe la máquina virtual. -Asegúrese de que la máquina virtual existe o seleccione una máquina virtual diferente. | Esto ocurre cuando se elimina la máquina virtual principal pero sigue de la directiva de copia de seguridad buscar una máquina virtual para realizar copia de seguridad. Para corregir este error: <ol><li> Vuelva a crear la máquina virtual con el mismo nombre y el mismo nombre de grupo de recursos [nombre de servicio de nube]<br>(OR)<br></li><li>Detener la protección de máquina virtual sin eliminar datos de copia de seguridad. [Más detalles](http://go.microsoft.com/fwlink/?LinkId=808124)</li></ol>|
|Copia de seguridad |Error de ejecución de comando. -Otra operación está en curso en este artículo. Espere hasta que se complete la operación anterior y vuelva a intentar |Se está ejecutando una copia de seguridad existente o el trabajo de restauración de la máquina virtual y no se puede iniciar un nuevo trabajo mientras se está ejecutando el trabajo existente.|
| Copia de seguridad | Copiar VHD de la copia de seguridad cámara agotado - vuelva a intentar la operación en unos minutos. Si el problema continúa, póngase en contacto con Microsoft Support. | Esto ocurre cuando hay demasiados datos para copiar. Compruebe si tiene menos de 16 discos de datos. |
| Copia de seguridad | Error interno durante la copia de seguridad, vuelva a intentar la operación en unos minutos. Si el problema continúa, póngase en contacto con Microsoft Support | Este error puede aparecer por razones de 2: <ol><li> Hay un problema temporal de acceso al almacenamiento de máquina virtual. Compruebe el [Estado de Azure](https://azure.microsoft.com/en-us/status/) para ver si hay algún problema en curso cálculo, almacenamiento y red en la región. Por favor, se soluciona el problema de entrada de copia de seguridad de intentos. <li>Se ha eliminado la máquina virtual original y, por tanto, no se realizará la copia de seguridad. Para mantener los datos de copia de seguridad de una máquina virtual eliminada pero dejar los errores de copia de seguridad, desproteger la máquina virtual y elija la opción para mantener los datos. Esto detendrá la programación de copia de seguridad y también los mensajes de error periódica. |
| Copia de seguridad | No se pudo instalar los servicios de recuperación de Azure extensión en el elemento seleccionado - agente de VM es un requisito previo para la extensión de servicios de recuperación de Azure. Instale al agente de Azure VM y reinicie la operación de registro | <ol> <li>Compruebe si se ha instalado el agente VM correctamente. <li>Asegúrese de que el indicador de configuración de la máquina virtual esté configurado correctamente.</ol> [Obtenga más información](#validating-vm-agent-installation) acerca de instalación de agente VM y cómo validar la instalación de agente de máquina virtual. |
| Copia de seguridad | Error de instalación de extensión con el error "COM + no pudo póngase en contacto con el Coordinador de transacciones de Microsoft Distributed | Normalmente, esto significa que no se está ejecutando el servicio de COM +. Para obtener ayuda sobre cómo solucionar este problema, póngase en contacto con el soporte técnico de Microsoft. |
| Copia de seguridad | Error en la operación de instantánea con el error de la operación de VSS "esta unidad está bloqueada por el cifrado de unidad BitLocker. Debe desbloquear esta unidad desde el Panel de Control. | Desactivar BitLocker para todas las unidades de la máquina virtual y observe si se resuelve el problema VSS |
| Copia de seguridad | Máquinas virtuales tener discos duro virtuales almacenados en Premium no son compatibles para la copia de seguridad | Ninguno |
| Copia de seguridad | Máquina Virtual Azure no encontrado. | Esto ocurre cuando se elimina la máquina virtual principal pero sigue de la directiva de copia de seguridad buscar una máquina virtual para realizar copia de seguridad. Para corregir este error: <ol><li>Vuelva a crear la máquina virtual con el mismo nombre y el mismo nombre de grupo de recursos [nombre de servicio de nube] <br>(OR) <li> Desactivar la protección de esta máquina virtual para que no se crearán trabajos de copia de seguridad </ol> |
| Copia de seguridad | Agente de máquina virtual no está presente en la máquina virtual: instale el requisito previo, agente VM y reiniciar la operación. | [Obtenga más información](#vm-agent) acerca de instalación de agente VM y cómo validar la instalación de agente de máquina virtual. |

## <a name="jobs"></a>Trabajos

| Operación | Detalles del error | Solución alternativa |
| -------- | -------- | -------|
| Cancelar trabajo | No se admite la cancelación para este tipo de tarea: espere a que finalice el trabajo. | Ninguno |
| Cancelar trabajo | El trabajo no está en un estado puede cancelar: espere a que finalice el trabajo. <br>OR<br> La tarea seleccionada no está en un estado puede cancelar: espere a completar el trabajo.| Probable casi se haya completado el trabajo; Espere a que finalice el trabajo |
| Cancelar trabajo | No se puede cancelar el trabajo porque no está en curso: cancelación solo es compatible para las tareas que están en curso. Cancele la intento de un curso de trabajo. | Esto ocurre debido a un estado transitorio. Espere un minuto y vuelva a intentar la operación de cancelación |
| Cancelar trabajo | No se puede cancelar el trabajo - espere hasta que finalice el trabajo. | Ninguno |


## <a name="restore"></a>Restaurar
| Operación | Detalles del error | Solución alternativa |
| -------- | -------- | -------|
| Restaurar | Restaurar con error interno de nube | <ol><li>Servicio de nube a la que está intentando restaurar está configurado con la configuración de DNS. Puede comprobar <br>$deployment = AzureDeployment get - ServiceName "ServiceName"-franja "Producción" Get-AzureDns - DnsSettings $deployment. DnsSettings<br>Si no hay dirección configurada, esto significa que se ha configurado DNS settings.<br> <li>Servicio de nube a la que está intentando restaurar está configurado con IP reservada y máquinas virtuales existentes en servicio de nube que se encuentran en estado de detención.<br>Puede comprobar que un servicio de nube ha reservado IP usando las siguientes cmdlets de powershell:<br>$deployment = AzureDeployment get - ServiceName "servicename"-DEP de franja $ "Producción". ReservedIPName <br><li>Está intentando restaurar una máquina virtual con las siguientes configuraciones de red especiales al mismo servicio de nube. <br>-Máquinas virtuales de Windows en configuración del equilibrador de carga (internas y externas)<br>-Máquinas virtuales con varias direcciones IP reservadas<br>-Máquinas virtuales con varias NIC<br>Seleccione un nuevo servicio de nube en la interfaz de usuario o consulte [Restaurar consideraciones](./backup-azure-arm-restore-vms.md/#restoring-vms-with-special-network-configurations) para máquinas virtuales con las configuraciones de red especiales</ol> |
| Restaurar | El nombre DNS seleccionado ya existe: especifique un nombre DNS diferente e inténtelo de nuevo. | Aquí el nombre DNS que hace referencia el nombre de servicio de nube (normalmente que termina en @. cloudapp.net). Debe ser único. Si aparece este error, debe elegir un nombre diferente de la máquina virtual durante la restauración. <br><br> Tenga en cuenta que este error se muestra solo a los usuarios del portal de Azure. La operación de restauración a través de PowerShell se realizará correctamente porque solo restaura los discos y no crear la máquina virtual. El error se enfrentará cuando la máquina virtual se crea explícitamente por el usuario después de la operación de restaurar el disco. |
| Restaurar | La configuración de red virtual especificado no es correcta: especifique una configuración de red virtual diferente y vuelva a intentarlo. | Ninguno |
| Restaurar | El servicio de nube especificada está usando una dirección IP reservada, que no coinciden con la configuración de la máquina virtual está restaurarla: especifique un servicio de nube diferente que no está usando IP reservada o elegir otro punto de recuperación para restaurar a partir de. | Ninguno |
| Restaurar | Servicio de nube ha alcanzado el límite en el número de puntos finales de entrada: vuelva a intentar la operación que especifica un servicio de nube diferente o mediante un extremo existente. | Ninguno |
| Restaurar | Cuenta de almacenamiento copia de seguridad de cámara y destino están en dos regiones diferentes: asegúrese de que la cuenta de almacenamiento especificada en la operación de restauración se encuentra en la misma región Azure como la cámara de copia de seguridad. | Ninguno |
| Restaurar | Cuenta de almacenamiento especificado para la operación de restauración no es compatible - cuentas de almacenamiento solo básico/estándar con redundante local o configuración de replicación redundantes geo es compatibles. Seleccione una cuenta de almacenamiento compatibles | Ninguno |
| Restaurar | Tipo de cuenta de almacenamiento especificada para la operación de restauración no está en línea, asegúrese de que la cuenta de almacenamiento especificada en la operación de restauración está en línea | Esto puede ocurrir debido a un error temporal en el almacenamiento de Azure o debido a una interrupción. Elija otra cuenta de almacenamiento. |
| Restaurar | Se alcanzó la cuota de grupo de recursos: elimine algunos grupos de recursos desde el portal de Azure o póngase en contacto con soporte técnico de Azure para aumentar los límites. | Ninguno |
| Restaurar | No existe la subred seleccionada: seleccione una subred que existe | Ninguno |


## <a name="policy"></a>Directiva
| Operación | Detalles del error | Solución alternativa |
| -------- | -------- | -------|
| Crear directivas | Error al crear la directiva - reduzca las opciones de retención para continuar con la configuración de la directiva. | Ninguno |


## <a name="vm-agent"></a>Agente VM

### <a name="setting-up-the-vm-agent"></a>Configurar el agente de VM
Normalmente, el agente de VM ya está presente en máquinas virtuales que se crean desde la Galería de Azure. Sin embargo, máquinas virtuales que se migran desde los centros de datos locales no tendrá instalado el agente de máquina virtual. Para dichas máquinas virtuales, el agente de VM debe instalarse explícitamente. Obtenga más acerca de cómo [instalar al agente de máquina virtual en una máquina virtual existente](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx).

Para máquinas virtuales de Windows:

- Descargue e instale el [agente de MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Necesitará privilegios de administrador para completar la instalación.
- [Actualizar la propiedad VM](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) para indicar que está instalado el agente.

Para máquinas virtuales de Linux:

- Instale más reciente [Linux agente](https://github.com/Azure/WALinuxAgent) de github.
- [Actualizar la propiedad VM](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) para indicar que está instalado el agente.


### <a name="updating-the-vm-agent"></a>Actualizar al agente VM
Para máquinas virtuales de Windows:

- Actualizar al agente de VM es tan sencillo como volver a instalar los [archivos binarios de agente de máquina virtual](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Sin embargo, debe asegurarse de que ninguna operación de copia de seguridad se está ejecutando mientras se actualiza el agente de máquina virtual.

Para máquinas virtuales de Linux:

- Siga las instrucciones en [Actualizar agente de máquina virtual Linux](../virtual-machines/virtual-machines-linux-update-agent.md).


### <a name="validating-vm-agent-installation"></a>Validar la instalación de agente de VM
Cómo comprobar la versión de agente de VM en máquinas virtuales de Windows:

1. Inicie sesión en la máquina virtual de Azure y vaya a la carpeta *C:\WindowsAzure\Packages*. Encontrará el archivo de WaAppAgent.exe.
2. Haga clic en el archivo, vaya a las **Propiedades**y, a continuación, seleccione la ficha **Detalles** . El campo de versión del producto debe ser 2.6.1198.718 o superior

## <a name="troubleshoot-vm-snapshot-issues"></a>Solucionar problemas de instantáneas de máquina virtual
Copia de seguridad de máquina virtual se basa en comando instantánea almacenamiento subyacente. No tenga acceso de almacenamiento o retraso en la ejecución de la tarea de instantánea puede fallar la copia de seguridad. Los siguientes pueden provocar errores de tareas de la instantánea.

1. Se bloquea el acceso de red al almacenamiento usando GSN<br>
   Más información sobre cómo [Habilitar el acceso de red](backup-azure-vms-prepare.md#2-network-connectivity) al almacenamiento usando cualquier lista blanca de direcciones IP o a través de servidor proxy.
2.  Máquinas virtuales con copia de seguridad de Sql Server configurado pueden producir retraso de la tarea de instantánea <br>
    De forma predeterminada, máquina virtual de copia de seguridad problemas completa copia de seguridad en máquinas virtuales de Windows. En máquinas virtuales que ejecutan Sql Server y se configura la copia de seguridad de Sql Server, esto puede provocar retraso en la ejecución de instantáneas. Establezca el siguiente clave del registro si experimenta errores de copia de seguridad debido a problemas de instantánea.

    ```
    [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
    "USEVSSCOPYBACKUP"="TRUE"
    ```
3.  Estado VM informado incorrectamente porque VM está apagado en RDP.  <br>
    Si apaga el equipo virtual en RDP, compruebe el portal de que el estado de la máquina virtual se refleja correctamente. Si no es así, cierre la máquina virtual de portal con la opción 'Apagado' en el panel de la máquina virtual.
4.  Si más de participación cuatro VM de la misma nube servicio, configurar varias directivas de copia de seguridad para no organizar los tiempos de copia de seguridad por lo que que se inician las copias de seguridad de más de cuatro VM al mismo tiempo. Intente separe el comienzo una hora de diferencia entre las directivas de copia de seguridad. 
5.  Máquina virtual se está ejecutando en CPU/memoria alta.<br>
    Si está ejecutando la máquina virtual en alto de CPU usage(>90%) o memoria, tareas de instantánea en cola, retrasar y finalmente se obtiene el tiempo de espera. Pruebe la copia de seguridad a petición en estos casos.

<br>

## <a name="networking"></a>Redes
Al igual que todas las extensiones, extensión de copia de seguridad necesitan tener acceso a internet público para que funcione. No tengan acceso a internet puede manifestarse de varias maneras:

- Puede fallar la instalación de extensión
- Pueden fallar las operaciones de copia de seguridad (como instantánea de disco)
- Mostrar el estado de la operación de copia de seguridad puede fallar

Ha sido se articularon la necesidad de resolver direcciones de internet público [aquí](http://blogs.msdn.com/b/mast/archive/2014/06/18/azure-vm-provisioning-stuck-on-quot-installing-extensions-on-virtual-machine-quot.aspx). Debe comprobar las configuraciones de DNS para el VNET y asegúrese de que se puede resolver los URI de Azure.

Una vez que la resolución de nombres se realiza correctamente, también debe proporcionar acceso a las direcciones IP de Azure. Para desbloquear el acceso a la infraestructura de Azure, siga uno de estos pasos:

1. Lista blanca del centro de datos de Azure intervalos de IP.
    - Obtener la lista de [direcciones IP del centro de datos Azure](https://www.microsoft.com/download/details.aspx?id=41653) estar en la lista blanca.
    - Desbloquear las direcciones IP mediante el cmdlet [NetRoute de nuevo](https://technet.microsoft.com/library/hh826148.aspx) . Ejecutar este cmdlet dentro de la máquina virtual de Azure en una ventana de PowerShell elevado (ejecutar como administrador).
    - Agregar reglas para la GSN (si tiene en su lugar) para permitir el acceso a las direcciones IP.
2. Crear una ruta de acceso para el flujo del tráfico HTTP
    - Si tiene alguna restricción de red en su lugar (por ejemplo, un grupo de seguridad de red) implementar un servidor de proxy HTTP para enrutar el tráfico. Pasos para implementar un servidor HTTP Proxy pueden encontrar [aquí](backup-azure-vms-prepare.md#2-network-connectivity).
    - Agregar reglas para la GSN (si tiene en su lugar) para permitir el acceso a INTERNET desde el servidor HTTP Proxy.

>[AZURE.NOTE] Debe estar habilitado DHCP dentro del invitado para copia de seguridad de IaaS VM funcione.  Si necesita una dirección IP privada estática, debe configurar a través de la plataforma. La opción de DHCP dentro de la máquina virtual debe habilitarse hacia la izquierda.
Ver más información sobre cómo [Configurar una dirección IP estática de privada interna](../virtual-network/virtual-networks-reserved-private-ip.md).

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

## <a name="discovery"></a>Detección

| Operación de copia de seguridad | Detalles del error | Solución alternativa |
| -------- | -------- | -------|
| Detección | No se pudo descubrir nuevos elementos - copia de seguridad de Microsoft Azure encontró y error interno. Espere unos minutos y, a continuación, vuelva a intentarlo. | Vuelva a intentar el proceso de detección después de 15 minutos.
| Detección | No se puede detectar nuevos elementos: la operación de descubrimiento ya está en curso. Espere hasta que se complete la operación de detección actual. | Ninguno |

## <a name="register"></a>Registrar
| Operación de copia de seguridad | Detalles del error | Solución alternativa |
| -------- | -------- | -------|
| Registrar | Número de discos de datos adjuntado a la máquina virtual ha superado el límite admitido - desasociar algunos discos de datos en este equipo virtual y vuelva a intentar la operación. Copia de seguridad de Azure admite hasta 16 discos de datos adjuntados a una máquina virtual Azure para copia de seguridad | Ninguno |
| Registrar | Copia de seguridad de Microsoft Azure error interno - espere unos minutos y vuelva a intentarlo. Si el problema continúa, póngase en contacto con Microsoft Support. | Puede obtener este error debido a uno de la siguiente configuración no admitida de VM en LRS Premium. <br> Almacenamiento de Premium máquinas virtuales puede copia usando depósito de servicios de recuperación. [Aprende más](backup-introduction-to-azure-backup.md/#back-up-and-restore-premium-storage-vms) |
| Registrar | Error en el registro con el tiempo de espera de instalar agente operación | Compruebe si se admite la versión del sistema operativo de la máquina virtual. |
| Registrar | Error - de ejecución de comando otra operación está en curso en este artículo. Espere hasta que se complete la operación anterior | Ninguno |
| Registrar | Máquinas virtuales tener discos duro virtuales almacenados en Premium no son compatibles para la copia de seguridad | Ninguno |
| Registrar | Agente de máquina virtual no está presente en la máquina virtual: instale el requisito previo, agente VM y reiniciar la operación. | [Obtenga más información](#vm-agent) acerca de instalación de agente VM y cómo validar la instalación de agente de máquina virtual. |

## <a name="backup"></a>Copia de seguridad

| Operación de copia de seguridad | Detalles del error | Solución alternativa |
| -------- | -------- | -------|
| Copia de seguridad | No se pudo comunicarse con el agente de máquina virtual para el estado de la instantánea. Tarea de instantánea de VM sub ha agotado el tiempo de espera. -Vea a la Guía de solución de problemas acerca de cómo resolver este problema. | Este error se produce si hay un problema con el agente de VM o se bloquea el acceso de red a la infraestructura de Azure de alguna forma. Obtenga más información sobre [problemas de depuración de instantánea de VM](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md). <br> Si el agente VM no está causando problemas, a continuación, reinicie la máquina virtual. A veces un estado VM incorrecto puede provocar problemas y reiniciar la máquina virtual restablece este "mal estado" |
| Copia de seguridad | Error interno durante la copia de seguridad, vuelva a intentar la operación en unos minutos. Si el problema continúa, póngase en contacto con Microsoft Support | Compruebe si hay un problema temporal de acceso al almacenamiento de máquina virtual. Compruebe el [Estado de Azure](https://azure.microsoft.com/en-us/status/) para ver si hay algún problema en curso cálculo, almacenamiento y red en la región. Por favor, se soluciona el problema de entrada de copia de seguridad de intentos. |
| Copia de seguridad | No se pudo realizar la operación como VM ya no existe. | No se puede realizar copia de seguridad como se ha eliminado la máquina virtual configurada para la copia de seguridad. Detener más copias de seguridad e ir a la vista protegida de elementos, seleccione el elemento protegido y haga clic en Detener protección. Puede conservar los datos, seleccione la opción de conservar la copia de seguridad de datos. Más tarde puede reanudar la protección de este equipo virtual haciendo clic en configurar en la protección de la vista de elementos registrados|
| Copia de seguridad | No se pudo instalar los servicios de recuperación de Azure extensión en el elemento seleccionado - agente de VM es un requisito previo para la extensión de servicios de recuperación de Azure. Instale al agente de Azure VM y reinicie la operación de registro | <ol> <li>Compruebe si se ha instalado el agente VM correctamente. <li>Asegúrese de que el indicador de configuración de la máquina virtual esté configurado correctamente.</ol> [Obtenga más información](#validating-vm-agent-installation) acerca de instalación de agente VM y cómo validar la instalación de agente de máquina virtual. |
| Copia de seguridad | Ejecución de comando no se pudo - otra operación está en curso en este artículo. Espere hasta que se complete la operación anterior y vuelva a intentar | Se está ejecutando una copia de seguridad existente o el trabajo de restauración de la máquina virtual y no se puede iniciar un nuevo trabajo mientras se está ejecutando el trabajo existente. |
| Copia de seguridad | Error de instalación de extensión con el error "COM + no pudo póngase en contacto con el Coordinador de transacciones de Microsoft Distributed | Normalmente, esto significa que no se está ejecutando el servicio de COM +. Para obtener ayuda sobre cómo solucionar este problema, póngase en contacto con el soporte técnico de Microsoft. |
| Copia de seguridad | Error en la operación de instantánea con el error de la operación de VSS "esta unidad está bloqueada por el cifrado de unidad BitLocker. Debe desbloquear esta unidad desde el Panel de Control. | Desactivar BitLocker para todas las unidades de la máquina virtual y observe si se resuelve el problema VSS |
| Copia de seguridad | Máquinas virtuales tener discos duro virtuales almacenados en Premium no son compatibles para la copia de seguridad | Ninguno |
| Copia de seguridad | Máquina Virtual Azure no encontrado. | Esto ocurre cuando se elimina la máquina virtual principal pero sigue de la directiva de copia de seguridad buscar una máquina virtual para realizar copia de seguridad. Para corregir este error: <ol><li>Vuelva a crear la máquina virtual con el mismo nombre y el mismo nombre de grupo de recursos [nombre de servicio de nube] <br>(OR) <li> Desactivar la protección de esta máquina virtual para que no le volverse copias de seguridad posteriores. </ol> |
| Copia de seguridad | Agente de máquina virtual no está presente en la máquina virtual: instale el requisito previo, agente VM y reiniciar la operación. | [Obtenga más información](#vm-agent) acerca de instalación de agente VM y cómo validar la instalación de agente de máquina virtual. |

## <a name="jobs"></a>Trabajos
| Operación | Detalles del error | Solución alternativa |
| -------- | -------- | -------|
| Cancelar trabajo | No se admite la cancelación para este tipo de tarea: espere a que finalice el trabajo. | Ninguno |
| Cancelar trabajo | El trabajo no está en un estado puede cancelar: espere a que finalice el trabajo. <br>OR<br> La tarea seleccionada no está en un estado puede cancelar: espere a completar el trabajo.| Seguramente, casi se haya completado el trabajo; Espere a que finalice el trabajo |
| Cancelar trabajo | No se puede cancelar el trabajo porque no está en curso: cancelación solo es compatible para las tareas que están en curso. Cancele la intento de un curso de trabajo. | Esto ocurre debido a un estado transitorio. Espere un minuto y vuelva a intentar la operación de cancelación |
| Cancelar trabajo | No se puede cancelar el trabajo - espere hasta que finalice el trabajo. | Ninguno |


## <a name="restore"></a>Restaurar
| Operación | Detalles del error | Solución alternativa |
| -------- | -------- | -------|
| Restaurar | Restaurar con error interno de nube | <ol><li>Servicio de nube a la que está intentando restaurar está configurado con la configuración de DNS. Puede comprobar <br>$deployment = AzureDeployment get - ServiceName "ServiceName"-franja "Producción" Get-AzureDns - DnsSettings $deployment. DnsSettings<br>Si no hay dirección configurada, esto significa que se ha configurado DNS settings.<br> <li>Servicio de nube a la que está intentando restaurar está configurado con IP reservada y máquinas virtuales existentes en servicio de nube que se encuentran en estado de detención.<br>Puede comprobar que un servicio de nube ha reservado IP usando las siguientes cmdlets de powershell:<br>$deployment = AzureDeployment get - ServiceName "servicename"-DEP de franja $ "Producción". ReservedIPName <br><li>Está intentando restaurar una máquina virtual con las siguientes configuraciones de red especiales al mismo servicio de nube. <br>-Máquinas virtuales de Windows en configuración del equilibrador de carga (internas y externas)<br>-Máquinas virtuales con varias direcciones IP reservadas<br>-Máquinas virtuales con varias NIC<br>Seleccione un nuevo servicio de nube en la interfaz de usuario o consulte [Restaurar consideraciones](./backup-azure-restore-vms.md/#restoring-vms-with-special-network-configurations) para máquinas virtuales con las configuraciones de red especiales</ol> |
| Restaurar | El nombre DNS seleccionado ya existe: especifique un nombre DNS diferente e inténtelo de nuevo. | Aquí el nombre DNS que hace referencia el nombre de servicio de nube (normalmente que termina en @. cloudapp.net). Debe ser único. Si aparece este error, debe elegir un nombre diferente de la máquina virtual durante la restauración. <br><br> Este error se muestra solo a los usuarios del portal de Azure. La operación de restauración a través de PowerShell se realiza correctamente porque solo restaura los discos y no crear la máquina virtual. El error se enfrentará cuando la máquina virtual se crea explícitamente por el usuario después de la operación de restaurar el disco. |
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






<properties
    pageTitle="Azure hacer copia de seguridad: implementar y administrar atrás para DPM con PowerShell | Microsoft Azure"
    description="Aprenda a implementar y administrar la copia de seguridad de Azure para Administrador de protección de datos (DPM) con PowerShell"
    services="backup"
    documentationCenter=""
    authors="NKolli1"
    manager="shreeshd"
    editor=""/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/01/2016"
    ms.author="jimpark; anuragm;trinadhk;markgal"/>


# <a name="deploy-and-manage-backup-to-azure-for-data-protection-manager-dpm-servers-using-powershell"></a>Implementar y administrar la copia de seguridad en Azure para servidores de administrador de protección de datos (DPM) con PowerShell

> [AZURE.SELECTOR]
- [ARM](backup-dpm-automation.md)
- [Clásico](backup-dpm-automation-classic.md)

Este artículo le muestra cómo usar PowerShell para configurar copias de seguridad de Azure en un servidor DPM y administrar la copia de seguridad y recuperación.

## <a name="setting-up-the-powershell-environment"></a>Configurar el entorno de PowerShell

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]

Para poder usar PowerShell para administrar las copias de seguridad de administrador de protección de datos en Azure, debe tener un entorno de PowerShell. En el inicio de sesión de PowerShell, asegúrese de ejecutar el comando siguiente para importar los módulos derecha y le permiten hacer referencia correctamente a los cmdlets DPM:

```
PS C:> & "C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin\DpmCliInitScript.ps1"

Welcome to the DPM Management Shell!

Full list of cmdlets: Get-Command
Only DPM cmdlets: Get-DPMCommand
Get general help: help
Get help for a cmdlet: help <cmdlet-name> or <cmdlet-name> -?
Get definition of a cmdlet: Get-Command <cmdlet-name> -Syntax
Sample DPM scripts: Get-DPMSampleScript
```

## <a name="setup-and-registration"></a>Configuración y registro
Para empezar:

1. [Descargar la última PowerShell](https://github.com/Azure/azure-powershell/releases) (es la versión mínima requerida: 1.0.0)
2. Habilitar el commandlet de copia de seguridad de Azure cambiando al modo de *AzureResourceManager* usando el commandlet **AzureMode cambiar** :

```
PS C:\> Switch-AzureMode AzureResourceManager
```

Las siguientes tareas de configuración y el registro se pueden automatizar con PowerShell:

- Crear un depósito de servicios de recuperación
- Instalar al agente de copia de seguridad de Azure
- Registro con el servicio de copia de seguridad de Azure
- Configuración de red
- Configuración de cifrado

## <a name="create-a-recovery-services-vault"></a>Crear un depósito de servicios de recuperación

Los siguientes pasos le guiarán en el proceso de creación de un depósito de servicios de recuperación. Un depósito de servicios de recuperación es diferente a un depósito de copia de seguridad.

1. Si está utilizando la copia de seguridad de Azure por primera vez, debe usar el cmdlet **AzureRMResourceProvider registrar** para registrar el proveedor de servicios de recuperación de Azure con su suscripción.

    ```
    PS C:\> Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

2. El depósito de servicios de recuperación es un recurso ARM, por lo que es necesario colocar dentro de un grupo de recursos. Puede usar un grupo de recursos existente o cree uno nuevo. Al crear un nuevo grupo de recursos, especifique el nombre y la ubicación del grupo de recursos.  

    ```
    PS C:\> New-AzureRmResourceGroup –Name "test-rg" –Location "West US"
    ```

3. Usar el cmdlet **AzureRmRecoveryServicesVault de nuevo** para crear un nuevo depósito. Asegúrese de especificar la misma ubicación para el depósito que se utilizó para el grupo de recursos.

    ```
    PS C:\> New-AzureRmRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "West US"
    ```

4. Especificar el tipo de redundancia de almacenamiento para usar; Puede usar [Almacenamiento redundantes localmente (LRS)](../storage/storage-redundancy.md#locally-redundant-storage) o [Geo redundantes almacenamiento (GRS)](../storage/storage-redundancy.md#geo-redundant-storage). En el ejemplo siguiente se muestra que la opción - BackupStorageRedundancy testVault está establecida en GeoRedundant.

    > [AZURE.TIP] Cmdlets de copia de seguridad de Azure muchos requieren el objeto de la cámara de servicios de recuperación como una entrada. Por este motivo, es conveniente almacenar el objeto del depósito de servicios de recuperación de copia de seguridad en una variable.

    ```
    PS C:\> $vault1 = Get-AzureRmRecoveryServicesVault –Name "testVault"
    PS C:\> Set-AzureRmRecoveryServicesBackupProperties  -vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```



## <a name="view-the-vaults-in-a-subscription"></a>Ver los depósitos en una suscripción
Use **Get-AzureRmRecoveryServicesVault** para ver la lista de todos los depósitos en la suscripción actual. Puede utilizar este comando para comprobar que se ha creado un depósito nuevo, o para ver qué depósitos están disponibles en la suscripción.

Ejecutar el comando Get-AzureRmRecoveryServicesVault, y se muestran todos los depósitos en la suscripción.

```
PS C:\> Get-AzureRmRecoveryServicesVault
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```


## <a name="installing-the-azure-backup-agent-on-a-dpm-server"></a>Instalar al agente de copia de seguridad de Azure en un servidor DPM
Antes de instalar al agente de copia de seguridad de Azure, debe tener el instalador descargado y presente en el servidor de Windows. Puede obtener la última versión del instalador desde el [Centro de descarga de Microsoft](http://aka.ms/azurebackup_agent) o desde la página de panel del depósito de servicios de recuperación. Guardar el instalador en una ubicación fácilmente accesible como * C:\Downloads\*.

Para instalar al agente, ejecute el siguiente comando en un elevados PowerShell consola **en el servidor DPM**:

```
PS C:\> MARSAgentInstaller.exe /q
```

El agente se instala con todas las opciones predeterminadas. La instalación tardará unos minutos en segundo plano. Si no especifica la opción */nu* se abre la ventana de **Windows Update** al final de la instalación para comprobar si hay actualizaciones.

El agente se muestra en la lista de programas instalados. Para ver la lista de programas instalados, vaya a **Panel de Control** > **programas** > **programas y características**.

![Agente instalado](./media/backup-dpm-automation/installed-agent-listing.png)

### <a name="installation-options"></a>Opciones de instalación
Para ver todas las opciones disponibles a través de la línea de comandos, use el siguiente comando:

```
PS C:\> MARSAgentInstaller.exe /?
```

Las opciones disponibles son:

| Opción | Detalles | Predeterminado |
| ---- | ----- | ----- |
| / q | Instalación silencioso | - |
| / p: "ubicación" | Ruta de acceso a la carpeta de instalación para el agente de copia de seguridad de Azure. | Agente de servicios de C:\Program Files\Microsoft recuperación de Azure |
| / s: "ubicación" | Ruta de acceso a la carpeta de caché para el agente de copia de seguridad de Azure. | C:\Program Files\Microsoft recuperación Azure servicios Agent\Scratch |
| /m | Participar en Microsoft Update | - |
| /Nu | No busque actualizaciones una vez completada la instalación | - |
| / d | Desinstala el agente de servicios de recuperación de Microsoft Azure | - |
| /pH | Dirección del proxy | - |
| pD | Número de puerto de Host de proxy | - |
| / pu | Nombre de usuario de Host de proxy | - |
| /pw | Contraseña de proxy | - |

## <a name="registering-dpm-to-a-recovery-services-vault"></a>Registrar DPM para una recuperación de servicios de cámara

Una vez creado el depósito de servicios de recuperación, descargue el agente más reciente y las credenciales de la cámara y guardarlo en una ubicación adecuada como C:\Downloads.

```
PS C:\> $credspath = "C:\downloads"
PS C:\> $credsfilename = Get-AzureRmRecoveryServicesVaultSettingsFile -Backup -Vault $vault1 -Path  $credspath
PS C:\> $credsfilename
C:\downloads\testvault\_Sun Apr 10 2016.VaultCredentials
```

En el servidor DPM, ejecute el cmdlet de [Inicio OBRegistration](https://technet.microsoft.com/library/hh770398%28v=wps.630%29.aspx) para registrar el equipo con la cámara.

```
PS C:\> $cred = $credspath + $credsfilename
PS C:\> Start-OBRegistration-VaultCredentials $cred -Confirm:$false
CertThumbprint      :7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName: testvault
Region              :West US
Machine registration succeeded.
```

### <a name="initial-configuration-settings"></a>Configuración inicial
Una vez que el servidor DPM está registrado con la cámara de servicios de recuperación, se inicia con la configuración predeterminada de la suscripción. Estas opciones de suscripción incluyen las redes, el cifrado y el área de ensayo. Para cambiar la configuración de la suscripción debe obtener un identificador en la configuración (predeterminado) existente mediante el cmdlet [Get-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612793) :

```
$setting = Get-DPMCloudSubscriptionSetting -DPMServerName "TestingServer"
```

Todas las modificaciones se realizan en este objeto de PowerShell local ```$setting``` y, a continuación, se compromete a DPM y copia de seguridad de Azure guardarlas mediante el cmdlet [Set-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612791) el objeto completo. Debe usar la ```–Commit``` marca para asegurarse de que los cambios se conservan. La configuración no se aplican y copia de seguridad de Azure usarlo a menos que confirmada.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="networking"></a>Redes
Si la conectividad de la máquina DPM con el servicio de copia de seguridad de Azure en internet es a través de un servidor proxy, la configuración del servidor proxy se debe proporcionar para copias de seguridad correctas. Esto se realiza mediante la ```-ProxyServer```y ```-ProxyPort```, ```-ProxyUsername``` y la ```ProxyPassword``` parámetros con el cmdlet [Set-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612791) . En este ejemplo, no hay ningún servidor proxy para que nos estamos borrar explícitamente cualquier información relacionada con el servidor proxy.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoProxy
```

También se puede controlar el uso de ancho de banda con opciones de ```-WorkHourBandwidth``` y ```-NonWorkHourBandwidth``` para un conjunto determinado de días de la semana. En este ejemplo, no estamos definiendo cualquier limitación.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoThrottle
```

## <a name="configuring-the-staging-area"></a>Configurar el área de ensayo
El agente de copia de seguridad de Azure que se ejecuta en el servidor DPM necesita almacenamiento temporal de datos que restaura de la nube (área de ensayo local). Configurar el área de ensayo mediante el cmdlet [Set-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612791) y la ```-StagingAreaPath``` parámetro.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -StagingAreaPath "C:\StagingArea"
```

En el ejemplo anterior, el área de ensayo se establecerá en *C:\StagingArea* en el objeto de PowerShell ```$setting```. Asegúrese de que la carpeta especificada ya existe, o bien la confirmación final de la configuración de la suscripción se producirá un error.


### <a name="encryption-settings"></a>Configuración de cifrado
Los datos de copia de seguridad enviados a copia de seguridad de Azure está cifrados para proteger la confidencialidad de los datos. La frase de contraseña de cifrado es la contraseña de"" descifrar los datos en el momento de restaurar. Es importante mantener esta información segura una vez que se establece.

En el ejemplo siguiente, el primer comando convierte la cadena ```passphrase123456789``` una cadena segura y asigna la cadena segura a la variable denominada ```$Passphrase```. el segundo comando establece la cadena segura ```$Passphrase``` como la contraseña para cifrar las copias de seguridad.

```
PS C:\> $Passphrase = ConvertTo-SecureString -string "passphrase123456789" -AsPlainText -Force

PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -EncryptionPassphrase $Passphrase
```

> [AZURE.IMPORTANT] Mantener la información de la frase de contraseña segura una vez que se establece. No podrá restaurar los datos de Azure sin esta frase de contraseña.

En este momento, debería haya realizado todos los cambios necesarios para la ```$setting``` objeto. No olvide confirmar los cambios.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="protect-data-to-azure-backup"></a>Proteger los datos de copia de seguridad de Azure
En esta sección, agregará un servidor de producción a DPM y, a continuación, proteger los datos al almacenamiento DPM local y, a continuación, copia de seguridad de Azure. En los ejemplos, mostramos cómo realizar copias de seguridad de los archivos y carpetas. La lógica puede ampliarse fácilmente para cualquier origen de datos compatibles con DPM de copia de seguridad. Las copias de seguridad DPM se rigen por un grupo de protección (PG) con cuatro elementos:

1. **Los miembros del grupo** es una lista de todos los que se pueden proteger objetos (también conocido como *orígenes de datos* en DPM) que desea proteger en el mismo grupo de protección. Por ejemplo, desea proteger máquinas virtuales de producción en un grupo de protección y las bases de datos de SQL Server en otro grupo de protección que tienen requisitos distintos de copia de seguridad. Antes de que puede realizar una copia cualquier origen de datos en un servidor de producción que debe asegurarse de que el agente de DPM está instalado en el servidor y administrado por DPM. Siga los pasos para [instalar el agente de DPM](https://technet.microsoft.com/library/bb870935.aspx) y vincular al servidor DPM correspondiente.
2. **Método de protección de datos** especifica las ubicaciones de copia de seguridad de destino: cinta, disco y la nube. En nuestro ejemplo se proteger los datos en el disco local y en la nube.
3. **Programación de copia de seguridad** que especifica cuando es necesario realizar copias de seguridad y con qué frecuencia se deben sincronizar los datos entre el servidor DPM y el servidor de producción.
4. **Programación de retención** que especifica cuánto tiempo para conservar los puntos de recuperación de Azure.

### <a name="creating-a-protection-group"></a>Crear un grupo de protección
Empiece por crear un nuevo grupo de protección mediante el cmdlet [DPMProtectionGroup de nuevo](https://technet.microsoft.com/library/hh881722) .

```
PS C:\> $PG = New-DPMProtectionGroup -DPMServerName " TestingServer " -Name "ProtectGroup01"
```

El cmdlet anterior creará un grupo de protección denominado *ProtectGroup01*. Un grupo de protección existente también se pueden modificar más adelante para agregar la copia de seguridad en la nube de Azure. Sin embargo, para realizar cambios en el grupo de protección - nuevos o existentes - se necesita obtener un identificador en un objeto *modificable* mediante el cmdlet [Get-DPMModifiableProtectionGroup](https://technet.microsoft.com/library/hh881713) .

```
PS C:\> $MPG = Get-ModifiableProtectionGroup $PG
```

### <a name="adding-group-members-to-the-protection-group"></a>Agregar miembros de grupo para el grupo de protección
Cada agente de DPM conoce a la lista de orígenes de datos en el servidor que se instala en. Para agregar un origen de datos al grupo de protección, el agente de DPM debe enviar primero una lista de los orígenes de datos al servidor DPM. Uno o varios orígenes de datos se selecciona y agregados al grupo de protección. Son los pasos de PowerShell necesarios para lograr lo siguiente:

1. Obtener una lista de todos los servidores administrados por DPM mediante el agente de DPM.
2. Elija un servidor específico.
3. Obtener una lista de todos los orígenes de datos en el servidor.
4. Elija uno o varios orígenes de datos y agregarlos al grupo de protección

La lista de servidores en el que el agente de DPM está instalado y administrado por el servidor DPM se adquiere de forma con el cmdlet [Get-DPMProductionServer](https://technet.microsoft.com/library/hh881600) . En este ejemplo filtrará y configurar sólo PS con nombre *productionserver01* de copia de seguridad.

```
PS C:\> $server = Get-ProductionServer -DPMServerName "TestingServer" | where {($_.servername) –contains “productionserver01”
```

Capturar ahora la lista de orígenes de datos en ```$server``` mediante el cmdlet [Get-DPMDatasource](https://technet.microsoft.com/library/hh881605) . En este ejemplo estamos filtrado para el volumen *D:\* que va a configurar para la copia de seguridad. Este origen de datos, a continuación, se agrega al grupo de protección mediante el cmdlet [Add-DPMChildDatasource](https://technet.microsoft.com/library/hh881732) . No olvide utilizar el *modificable * objeto de grupo de protección ```$MPG``` para hacer las adiciones.

```
PS C:\> $DS = Get-Datasource -ProductionServer $server -Inquire | where { $_.Name -contains “D:\” }

PS C:\> Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS
```

Repita este paso tantas veces como sea necesario, hasta que haya agregado todos los orígenes de datos seleccionados al grupo de protección. También puede empezar con un solo origen de datos y completar el flujo de trabajo para crear el grupo de protección y en un momento posterior, agregar más orígenes de datos al grupo de protección.

### <a name="selecting-the-data-protection-method"></a>Seleccionar el método de protección de datos
Una vez que se agregaron los orígenes de datos al grupo de protección, el siguiente paso es especificar el método de protección mediante el cmdlet [Set-DPMProtectionType](https://technet.microsoft.com/library/hh881725) . En este ejemplo, el grupo de protección está configurado para disco local y copia de seguridad de nube. También debe especificar el origen de datos que desea proteger en nube mediante el cmdlet [Add-DPMChildDatasource](https://technet.microsoft.com/library/hh881732.aspx) con - marca en línea.

```
PS C:\> Set-DPMProtectionType -ProtectionGroup $MPG -ShortTerm Disk –LongTerm Online
PS C:\> Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS –Online
```

### <a name="setting-the-retention-range"></a>Establecer el intervalo de retención
Establecer la retención para los puntos de copia de seguridad con el cmdlet [Set-DPMPolicyObjective](https://technet.microsoft.com/library/hh881762) . Aunque puede parecer impar establecer la retención antes de que se ha definido la programación de copia de seguridad, usando la ```Set-DPMPolicyObjective``` cmdlet establece automáticamente una programación de copia de seguridad predeterminados que puede modificarse. Siempre es posible al conjunto de la copia de seguridad programar primero y después de la directiva de retención.

En el ejemplo siguiente, el cmdlet establece los parámetros de retención para las copias de seguridad de disco. Esto conservará copias de seguridad de 10 días y sincronizar datos cada 6 horas entre el servidor de producción y el servidor DPM. La ```SynchronizationFrequencyMinutes``` no definir con qué frecuencia se crea un punto de copia de seguridad, pero ¿cómo se copian a menudo datos al servidor DPM.  Esta configuración impide que las copias de seguridad está volviendo demasiado grande.

```
PS C:\> Set-DPMPolicyObjective –ProtectionGroup $MPG -RetentionRangeInDays 10 -SynchronizationFrequencyMinutes 360
```

Copias de seguridad que se va a Azure (DPM refiere a ellos como copias de seguridad en línea) se pueden configurar los intervalos de retención de [retención a largo plazo con una combinación de-pie-hijo (GFS)](backup-azure-backup-cloud-as-tape.md). Es decir, puede definir una directiva de retención combinados que implican diaria, semanal, mensual o anual directivas de retención. En este ejemplo, cree una matriz que representa la combinación de retención complejos queremos y, a continuación, configure el intervalo de retención mediante el cmdlet [Set-DPMPolicyObjective](https://technet.microsoft.com/library/hh881762) .

```
PS C:\> $RRlist = @()
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 180, Days)
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 104, Weeks)
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 60, Month)
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 10, Years)
PS C:\> Set-DPMPolicyObjective –ProtectionGroup $MPG -OnlineRetentionRangeList $RRlist
```

### <a name="set-the-backup-schedule"></a>Establecer la programación de copia de seguridad
DPM establece automáticamente una programación de copia de seguridad de forma predeterminada si se especifica el objetivo protección utilizando la ```Set-DPMPolicyObjective``` cmdlet. Para cambiar los planes de forma predeterminada, use el cmdlet [Get-DPMPolicySchedule](https://technet.microsoft.com/library/hh881749) seguido del cmdlet [Set-DPMPolicySchedule](https://technet.microsoft.com/library/hh881723) .

```
PS C:\> $onlineSch = Get-DPMPolicySchedule -ProtectionGroup $mpg -LongTerm Online
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[0] -TimesOfDay 02:00
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[1] -TimesOfDay 02:00 -DaysOfWeek Sa,Su –Interval 1
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[2] -TimesOfDay 02:00 -RelativeIntervals First,Third –DaysOfWeek Sa
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[3] -TimesOfDay 02:00 -DaysOfMonth 2,5,8,9 -Months Jan,Jul
PS C:\> Set-DPMProtectionGroup -ProtectionGroup $MPG
```

En el ejemplo anterior, ```$onlineSch``` es una matriz con cuatro elementos que contiene la programación de protección en línea existente para el grupo de protección de la combinación de GFS:

1. ```$onlineSch[0]```contiene la programación diaria
2. ```$onlineSch[1]```contiene la programación semanal
3. ```$onlineSch[2]```contiene la programación mensual
4. ```$onlineSch[3]```contiene la programación anual

Si necesita modificar la programación semanal, debe hacer referencia a la ```$onlineSch[1]```.

### <a name="initial-backup"></a>Copia de seguridad inicial
Cuando copia un origen de datos por primera vez, necesidades DPM crea réplica inicial que crea una copia completa del origen de datos que se desea proteger en el volumen de réplica DPM. Esta actividad pueden programarse una hora específica o puede activarse manualmente, usar el cmdlet [Set-DPMReplicaCreationMethod](https://technet.microsoft.com/library/hh881715) con el parámetro ```-NOW```.

```
PS C:\> Set-DPMReplicaCreationMethod -ProtectionGroup $MPG -NOW
```
### <a name="changing-the-size-of-dpm-replica--recovery-point-volume"></a>Cambiar el tamaño de réplica de DPM & volumen de punto de recuperación
También puede cambiar el tamaño de réplica de DPM y volumen de instantánea mediante el cmdlet [Set-DPMDatasourceDiskAllocation](https://technet.microsoft.com/library/hh881618.aspx) como en el ejemplo siguiente: Get-DatasourceDiskAllocation - Datasource $DS Set-DatasourceDiskAllocation - Datasource $DS - ProtectionGroup $MPG-manual - ReplicaArea (2 gb) - ShadowCopyArea (2 gb)

### <a name="committing-the-changes-to-the-protection-group"></a>Confirmar los cambios en el grupo de protección
Por último, los cambios deben estar confirmada antes de realizar la copia de seguridad por la nueva configuración de grupo de protección DPM. Esto puede conseguirse mediante el cmdlet [Set-DPMProtectionGroup](https://technet.microsoft.com/library/hh881758) .

```
PS C:\> Set-DPMProtectionGroup -ProtectionGroup $MPG
```
## <a name="view-the-backup-points"></a>Ver los puntos de copia de seguridad
Puede usar el cmdlet [Get-DPMRecoveryPoint](https://technet.microsoft.com/library/hh881746) para obtener una lista de todos los puntos de recuperación para un origen de datos. En este ejemplo, se hará lo siguiente:
- capturar todo el documento en el servidor DPM y se almacenan en una matriz.```$PG```
- obtener los orígenes de datos correspondiente a la```$PG[0]```
- obtener todos los puntos de recuperación para un origen de datos.

```
PS C:\> $PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
PS C:\> $DS = Get-DPMDatasource -ProtectionGroup $PG[0]
PS C:\> $RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online
```

## <a name="restore-data-protected-on-azure"></a>Restaurar datos protegidos en Azure
Restauración de datos es una combinación de un ```RecoverableItem``` objeto y un ```RecoveryOption``` objeto. En la sección anterior, tenemos una lista de los puntos de copia de seguridad para un origen de datos.

En el ejemplo siguiente se muestra cómo restaurar una máquina virtual Hyper-V de copia de seguridad de Azure combinando puntos de copia de seguridad con el destino de recuperación. En este ejemplo se incluyen:

- Creación de una opción de recuperación mediante el cmdlet [DPMRecoveryOption de nuevo](https://technet.microsoft.com/library/hh881592) .
- Obtención de la matriz de puntos de copia de seguridad con el ```Get-DPMRecoveryPoint``` cmdlet.
- Elegir un punto para restaurar a partir de una copia de seguridad.

```
PS C:\> $RecoveryOption = New-DPMRecoveryOption -HyperVDatasource -TargetServer "HVDCenter02" -RecoveryLocation AlternateHyperVServer -RecoveryType Recover -TargetLocation “C:\VMRecovery”

PS C:\> $PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
PS C:\> $DS = Get-DPMDatasource -ProtectionGroup $PG[0]
PS C:\> $RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online

PS C:\> Restore-DPMRecoverableItem -RecoverableItem $RecoveryPoints[0] -RecoveryOption $RecoveryOption
```

Los comandos pueden ampliarse fácilmente para cualquier tipo de origen de datos.

## <a name="next-steps"></a>Pasos siguientes

- Para obtener más información sobre Administrador para copia de seguridad de Azure, vea [Introducción a la copia de seguridad de DPM](backup-azure-dpm-introduction.md)

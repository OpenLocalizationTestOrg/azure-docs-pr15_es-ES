<properties
    pageTitle="Restaurar clave clave depósito y secreto de cifrado máquinas virtuales mediante copia de seguridad de Azure | Microsoft Azure"
    description="Obtenga información sobre cómo restaurar clave clave depósito y secreto en copia de seguridad de Azure con PowerShell"
    services="backup"
    documentationCenter=""
    authors="JPallavi"
    manager="vijayts"
    editor=""/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="JPallavi" />

# <a name="restore-key-vault-key-and-secret-for-encrypted-vms-using-azure-backup"></a>Restaurar clave clave depósito y secreto de cifrado máquinas virtuales mediante copia de seguridad de Azure
En este artículo se habla con copia de seguridad de Azure VM para realizar la restauración de máquinas virtuales de Azure cifrado, si su clave y secreto no existen en el depósito clave. También pueden utilizarse estos pasos si desea mantener una copia independiente de clave (clave de cifrado) y secreto (clave de cifrado BitLocker) para VM restaurada.

## <a name="pre-requisites"></a>Requisitos previos

1. **Copia de seguridad cifra VM** - cifrado máquinas virtuales de Azure copiado previamente con copia de seguridad de Azure. Para obtener detalles acerca de cómo hacer copia de seguridad cifrada máquinas virtuales de Azure, consulte el artículo [Administrar copia de seguridad y restauración de máquinas virtuales de Azure con PowerShell](backup-azure-vms-automation.md) .

2. **Configurar Azure clave depósito** : asegúrese de que depósito al que debe restaurarse las claves y la información confidencial de clave ya está presente. Para obtener más información sobre la administración de claves de cámara, consulte el artículo [Introducción a Azure clave depósito](../key-vault/key-vault-get-started.md) .

## <a name="setup-recovery-services-vault"></a>Depósito de servicios de recuperación de instalación 
Siga estos pasos para iniciar sesión en PowerShell y establecer el contexto de depósito de servicios de recuperación

### <a name="log-in-to-azure-powershell"></a>Inicie sesión en Azure PowerShell 

Inicie sesión en la cuenta mediante el siguiente cmdlet de Azure

```
PS C:\> Login-AzureRmAccount
```

### <a name="set-recovery-services-vault-context"></a>Definir el contexto de recuperación servicios depósito

Una vez que inició sesión, use el siguiente cmdlet para obtener la lista de sus suscripciones disponibles

```
PS C:\> Get-AzureRmSubscription
```

Seleccione la suscripción en la que los recursos están disponibles

```
PS C:\> Set-AzureRmContext -SubscriptionId "<subscription-id>"
```

Establecer el contexto de la cámara mediante depósito de servicios de recuperación donde se habilitó la copia de seguridad para máquinas virtuales cifradas

```
PS C:\> Get-AzureRmRecoveryServicesVault -ResourceGroupName "<rg-name>" -Name "<rs-vault-name>" | Set-AzureRmRecoveryServicesVaultContext
```

### <a name="get-recovery-point"></a>Obtener el punto de recuperación 

Seleccionar el contenedor de la cámara que representa la máquina virtual de Azure cifrada

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -Name "<vm-name>"
```

Con este contenedor, volver el elemento de la máquina virtual correspondiente

```
PS C:\> $backupitem = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM"
```

Obtener una matriz de puntos de recuperación para el elemento seleccionado de copia de seguridad en el punto de reunión variable

```
PS C:\> $startDate = (Get-Date).AddDays(-7)
PS C:\> $endDate = Get-Date
PS C:\> $rp = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
```

## <a name="restore-encrypted-virtual-machine"></a>Restaurar cifrado máquina virtual
Realice los pasos siguientes para restaurar VM cifrado, su clave y secreto.

### <a name="restore-key"></a>Restaurar clave

La matriz $rp anterior, se ordena en orden inverso de tiempo con el punto de recuperación más reciente en el índice 0. Por ejemplo: $rp [0] selecciona el último punto de recuperación.

```
PS C:\> $rp1 = Get-AzureRmRecoveryServicesBackupRecoveryPoint -RecoveryPointId $rp[0].RecoveryPointId -Item $backupItem -KeyFileDownloadLocation "C:\Users\downloads"
```

> [AZURE.NOTE]
Después de este cmdlet se ejecuta correctamente, obtiene genera un archivo blob en la carpeta especificada en el equipo donde se ejecuta. Este archivo blob representa la clave de cifrado de clave cifrada.

Restaure la clave del depósito clave mediante el siguiente cmdlet. 

```
PS C:\> Restore-AzureKeyVaultKey -VaultName "contosokeyvault" -InputFile "C:\Users\downloads\key.blob"
```

### <a name="restore-secret"></a>Restaurar el secreto

Restaurar datos secretos de punto de recuperación obtenida anteriormente

```
PS C:\> $rp1.KeyAndSecretDetails.SecretUrl

https://contosokeyvault.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/20aaae9eaa99996d89d99a29990d999a
```

> [AZURE.NOTE]
El texto antes de vault.azure.net representa el nombre de clave depósito original. El texto después de información confidencial o representa secreto nombre. 

Obtener el nombre secreto y el valor de los resultados del cmdlet ejecutar anterior, en caso de que desee usar el mismo nombre secreto. En los demás casos, debe actualizarse $secretname siguiente para usar el nuevo nombre secreto. 

```
PS C:\> $secretname = "B3284AAA-DAAA-4AAA-B393-60CAA848AAAA"
PS C:\> $secretdata = $rp1.KeyAndSecretDetails.SecretData
PS C:\> $Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
```

Configurar etiquetas para el secreto, en caso de VM necesita restaurar también. Para la etiqueta DiskEncryptionKeyFileName, valor debe contener el secreto que planea usar. 

```
PS C:\> $Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = 'https://contosokeyvault.vault.azure.net:443/keys/KeyName/84daaac999949999030bf99aaa5a9f9';'MachineName' = 'vm-name'}
```

> [AZURE.NOTE]
Valor de DiskEncryptionKeyFileName es igual que el nombre secreto obtenido por encima. Valor para DiskEncryptionKeyEncryptionKeyURL puede obtenerse de la cámara clave después de restaurar las teclas atrás y usar el cmdlet [Get-AzureKeyVaultKey](https://msdn.microsoft.com/library/dn868053.aspx)   

Establecer el secreto volver a la cámara clave

```
PS C:\> Set-AzureKeyVaultSecret -VaultName "contosokeyvault" -Name $secretname -SecretValue $secret -Tags $Tags -SecretValue $Secret -ContentType  "Wrapped BEK"
```

### <a name="restore-virtual-machine"></a>Restaurar la máquina virtual
Los cmdlets de PowerShell anteriores ayudan a restaurar clave y back secreto al depósito clave, si ha copiado VM cifrada mediante copia de seguridad de Azure VM. Después de restaurar, consulte el artículo [Administrar copia de seguridad y restauración de máquinas virtuales de Azure con PowerShell](backup-azure-vms-automation.md) para restaurar máquinas virtuales cifradas.

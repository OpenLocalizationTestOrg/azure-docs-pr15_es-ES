<properties
    pageTitle="Configurar la integración de Azure depósito clave para SQL Server en máquinas virtuales de Azure (clásico)"
    description="Aprenda a automatizar la configuración de cifrado de SQL Server para su uso con depósito de clave de Azure. En este tema se explica cómo usar Azure clave depósito integración con máquinas virtuales crear en el modelo de implementación clásico de SQL Server."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/26/2016"
    ms.author="jroth"/>

# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-vms-classic"></a>Configurar la integración de Azure depósito clave para SQL Server en máquinas virtuales de Azure (clásico)

> [AZURE.SELECTOR]
- [Administrador de recursos](virtual-machines-windows-ps-sql-keyvault.md)
- [Clásico](virtual-machines-windows-classic-ps-sql-keyvault.md)

## <a name="overview"></a>Información general
Existen varias características de cifrado de SQL Server, como el [cifrado de datos transparente (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), el [cifrado de nivel de columna (borrar)](https://msdn.microsoft.com/library/ms173744.aspx)y [cifrado de copia de seguridad](https://msdn.microsoft.com/library/dn449489.aspx). Estas formas de cifrado requieren administrar y almacenar las claves de cifrado que se usan para el cifrado. El servicio de depósito de clave de Azure (AKV) está diseñado para mejorar la seguridad y la administración de estas teclas en una ubicación segura y altamente disponible. El [Conector de SQL Server](http://www.microsoft.com/download/details.aspx?id=45344) permite a SQL Server usar estas teclas de la cámara de clave de Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Si está ejecutando SQL Server con equipos locales, hay [pasos que puede seguir para tener acceso a Azure clave depósito desde el equipo de SQL Server local](https://msdn.microsoft.com/library/dn198405.aspx). Pero, para SQL Server en máquinas virtuales de Azure, puede ahorrar tiempo utilizando la característica de *Integración de depósito de clave de Azure* . Con algunos cmdlets de PowerShell de Azure para habilitar esta característica, puede automatizar la configuración necesaria para una VM SQL tener acceso a su cámara clave.

Cuando esta característica está habilitada, automáticamente el conector de SQL Server se instala, configura el proveedor EKM para tener acceso a Azure clave depósito y crea la credencial para que pueda tener acceso a su cámara. Si busca en los pasos de la documentación mencionada anteriormente en local, puede ver que esta característica automatiza los pasos 2 y 3. Lo único que tendría que hacer manualmente es crear las teclas y depósito clave. Desde allí, toda la configuración de la VM SQL es automático. Una vez completada la instalación, esta característica puede ejecutar instrucciones SQL T para empezar a cifrar sus bases de datos o realizar copias de seguridad como lo haría normalmente.

[AZURE.INCLUDE [AKV Integration Prepare](../../includes/virtual-machines-sql-server-akv-prepare.md)]

## <a name="configure-akv-integration"></a>Configurar la integración de AKV
Usar PowerShell para configurar la integración de depósito de clave de Azure. Las siguientes secciones proporcionan una descripción general de los parámetros requeridos y, a continuación, en una secuencia de comandos de PowerShell.

### <a name="install-the-sql-server-iaas-extension"></a>Instale la extensión de IaaS SQL Server

En primer lugar, [instale la IaaS extensión de SQL Server](virtual-machines-windows-classic-sql-server-agent-extension.md).

### <a name="understand-the-input-parameters"></a>Comprender los parámetros de entrada
La siguiente tabla enumeran los parámetros necesarios para ejecutar la secuencia de comandos de PowerShell en la siguiente sección.

|Parámetro|Descripción|Ejemplo|
|---|---|---|
|**$akvURL**|**La dirección URL de la cámara clave**|"https://contosokeyvault.vault.azure.net/"|
|**$spName**|**Nombre Principal de servicio**|"fde2b411 - 33d 5-4e11-af04eb07b669ccf2"|
|**$spSecret**|**Secreto Principal del servicio**|"9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM ="|
|**$credName**|**Nombre de la credencial**: integración de AKV crea una credencial dentro de SQL Server, permitiendo la máquina virtual tener acceso a la cámara clave. Elija un nombre para esta credencial.|"mycred1"|
|**$vmName**|**Nombre de la máquina virtual**: el nombre de una VM SQL creado previamente.|"myvmname"|
|**$serviceName**|**Nombre de servicio**: nombre del servicio de nube asociado con VM SQL.|"mycloudservicename"|

### <a name="enable-akv-integration-with-powershell"></a>Habilitar la integración de AKV con PowerShell
El cmdlet **New-AzureVMSqlServerKeyVaultCredentialConfig** crea un objeto de configuración de la característica de integración de depósito de clave de Azure. El **Conjunto AzureVMSqlServerExtension** configura esta integración con el parámetro **KeyVaultCredentialSettings** . Los pasos siguientes muestran cómo usar estos comandos.

1. En Azure PowerShell, configurar los parámetros de entrada con los valores específicos como se describe en las secciones anteriores de este tema. La siguiente secuencia de comandos es un ejemplo.

        $akvURL = "https://contosokeyvault.vault.azure.net/"
        $spName = "fde2b411-33d5-4e11-af04eb07b669ccf2"
        $spSecret = "9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM="
        $credName = "mycred1"
        $vmName = "myvmname"
        $serviceName = "mycloudservicename"
2.  A continuación, use la siguiente secuencia de comandos para configurar y habilitar la integración de AKV.

        $secureakv =  $spSecret | ConvertTo-SecureString -AsPlainText -Force
        $akvs = New-AzureVMSqlServerKeyVaultCredentialConfig -Enable -CredentialName $credname -AzureKeyVaultUrl $akvURL -ServicePrincipalName $spName -ServicePrincipalSecret $secureakv
        Get-AzureVM -ServiceName $serviceName -Name $vmName | Set-AzureVMSqlServerExtension -KeyVaultCredentialSettings $akvs | Update-AzureVM

La extensión del Agente SQL IaaS se actualizará la VM SQL con esta nueva configuración.

[AZURE.INCLUDE [AKV Integration Next Steps](../../includes/virtual-machines-sql-server-akv-next-steps.md)]

<properties
   pageTitle="Pasar las credenciales a Azure con DSC | Microsoft Azure"
   description="Información general sobre cómo pasar de forma segura las credenciales a Azure máquinas virtuales de Windows mediante la configuración de estado de PowerShell deseado"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="zjalexander"
   manager="timlt"
   editor=""
   tags="azure-service-management,azure-resource-manager"
   keywords=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="09/15/2016"
   ms.author="zachal"/>

# <a name="passing-credentials-to-the-azure-dsc-extension-handler"></a>Pasar credenciales para el controlador de extensión DSC de Azure #

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

En este artículo trata sobre la extensión de configuración de estado deseado para Azure. Información general sobre el controlador de extensión DSC, visite [Introducción al controlador de extensión de configuración de estado de Azure deseado](virtual-machines-windows-extensions-dsc-overview.md). 


## <a name="passing-in-credentials"></a>Pasar credenciales
Como parte del proceso de configuración, puede necesita configurar cuentas de usuario, acceder a los servicios, o instalar un programa en un contexto de usuario. Para realizar estos elementos, debe proporcionar las credenciales. 

DSC permite parametrizadas configuraciones que están pasa a la configuración y se almacenan de forma segura en archivos MOF credenciales. El controlador de extensión de Azure simplifica la administración de credenciales al proporcionar la administración automática de certificados. 

Tenga en cuenta el siguiente script de configuración de DLC que crea una cuenta de usuario local con la contraseña especificada:

*user_configuration.ps1*

```
configuration Main
{
    param(
        [Parameter(Mandatory=$true)]
        [ValidateNotNullorEmpty()]
        [PSCredential]
        $Credential
    )    
    Node localhost {       
        User LocalUserAccount
        {
            Username = $Credential.UserName
            Password = $Credential
            Disabled = $false
            Ensure = "Present"
            FullName = "Local User Account"
            Description = "Local User Account"
            PasswordNeverExpires = $true
        } 
    }  
} 
```

Es importante incluir *host local de nodo* como parte de la configuración. Si falta esta declaración, los siguientes pasos no funcionan como el controlador de extensión busca específicamente para la instrucción de nodo host local. También es importante incluir la conversión de tipo *[PsCredential]*, como este tipo específico activa la extensión para cifrar las credenciales. 

Publicar esta secuencia de comandos con el almacenamiento de blobs:

`Publish-AzureVMDscConfiguration -ConfigurationPath .\user_configuration.ps1`

Establecer la extensión de Azure DSC y proporcione la credencial:

```
$configurationName = "Main"
$configurationArguments = @{ Credential = Get-Credential }
$configurationArchive = "user_configuration.ps1.zip"
$vm = Get-AzureVM "example-1"
 
$vm = Set-AzureVMDSCExtension -VM $vm -ConfigurationArchive $configurationArchive 
-ConfigurationName $configurationName -ConfigurationArgument @configurationArguments
 
$vm | Update-AzureVM
```
## <a name="how-credentials-are-secured"></a>¿Cómo se protegen las credenciales
Ejecutar este código pide una credencial. Una vez que se proporciona, se almacena en la memoria brevemente. Cuando se publica con `Set-AzureVmDscExtension` cmdlet, se transmite a través de HTTPS VM, se almacena en Azure cifrados en disco, usando el certificado VM local. A continuación, se descifra en memoria brevemente y cifrado de nuevo para pasar a DSC.

Este comportamiento es diferente que [con configuraciones seguras sin el controlador de extensión](https://msdn.microsoft.com/powershell/dsc/securemof). El entorno de Azure le ofrece una manera de transmisión de datos de configuración de forma segura a través de certificados. Cuando se utiliza el controlador de extensión DSC, no es necesario para proporcionar $CertificatePath o un $CertificateID / entrada $Thumbprint en ConfigurationData.


## <a name="next-steps"></a>Pasos siguientes ##

Para obtener más información sobre el controlador de extensión DSC de Azure, vea [Introducción al controlador de extensión de configuración de estado de Azure deseado](virtual-machines-windows-extensions-dsc-overview.md). 

Examine la [plantilla de administrador de recursos de Azure para la extensión DSC](virtual-machines-windows-extensions-dsc-template.md).

Para obtener más información acerca de PowerShell DSC, [visite el centro de documentación de PowerShell](https://msdn.microsoft.com/powershell/dsc/overview). 

Para buscar funciones adicionales puede administrar con PowerShell DSC, [busque en la Galería de PowerShell](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0) para obtener más recursos DSC.

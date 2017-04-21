<properties
   pageTitle="Desea configuración de estado de introducción a Azure | Microsoft Azure"
   description="Información general para usar el controlador de extensión de Microsoft Azure de configuración de estado de PowerShell deseado. Incluidos los requisitos previos, arquitectura, cmdlets..."
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

# <a name="introduction-to-the-azure-desired-state-configuration-extension-handler"></a>Introducción al controlador de extensión de configuración de estado de deseado de Azure #

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

El agente de Azure VM y extensiones asociadas forman parte de los servicios de infraestructura de Microsoft Azure. Extensiones de VM son componentes de software que amplían la funcionalidad VM y simplifican diversas operaciones de administración de la máquina virtual. Por ejemplo, la extensión de VMAccess puede utilizarse para restablecer una contraseña de administrador o la extensión de Script personalizado se puede utilizar para ejecutar una secuencia de comandos en la máquina virtual.

En este artículo se presenta la extensión de configuración de estado deseado (DSC) de PowerShell para máquinas virtuales de Azure como parte del SDK de PowerShell de Azure. Puede usar cmdlets de nuevo para cargar y aplicar una configuración de PowerShell DSC en una máquina virtual de Azure habilitado con la extensión de PowerShell DSC. Las llamadas de extensión DSC de PowerShell en DSC de PowerShell para activar la configuración de DSC recibida en la máquina virtual. Esta funcionalidad también está disponible a través del portal de Azure.

## <a name="prerequisites"></a>Requisitos previos ##
**Equipo local** Para interactuar con la extensión de Azure VM, debe usar el portal de Azure o el SDK de PowerShell de Azure. 

**Agente de invitado** La máquina virtual de Azure que va a configurar mediante la configuración de DSC debe ser un sistema operativo que admita en Windows Management Framework (WMF) 4.0 o 5.0. La lista completa de versiones de sistemas operativos compatibles se puede encontrar en el [Historial de versiones de extensión DSC](https://blogs.msdn.microsoft.com/powershell/2014/11/20/release-history-for-the-azure-dsc-extension/).

## <a name="terms-and-concepts"></a>Conceptos y términos ##
Esta guía se da por supuesto estar familiarizado con los conceptos siguientes:

Configuración: un documento de configuración de DLC. 

Nodo - un destino para una configuración de DSC. En este documento, "nodo" siempre se refiere a una máquina virtual de Azure.

Datos de configuración - ambientales que contiene los datos para una configuración de archivo de un .psd1

## <a name="architectural-overview"></a>Introducción a la arquitectura ##

La extensión de Azure DSC utiliza el marco de Azure VM agente para ofrecer, adoptar e informar sobre configuraciones de DSC que se ejecutan en máquinas virtuales de Azure. La extensión DSC espera un archivo .zip que contiene al menos un documento de configuración y un conjunto de parámetros proporcionados por el SDK de PowerShell de Azure o a través del portal de Azure.

Cuando se llama a la extensión por primera vez, se ejecuta un proceso de instalación. Este proceso instala una versión del marco de trabajo de administración de Windows (WMF) utilizando la lógica siguiente:

1. Si el sistema operativo VM de Azure es Windows Server 2016, se realiza ninguna acción. Windows Server 2016 ya tiene la última versión de PowerShell instalado.
2. Si el `wmfVersion` se especifica la propiedad, que la versión de WMF se instala a menos que no es compatible con el sistema operativo de la máquina virtual.
3. Si no hay `wmfVersion` se especifica la propiedad, se instala la versión más reciente aplicable WMF.

Instalación de WMF requiere reiniciar el equipo. Después de reiniciar, la extensión de descarga el archivo .zip especificado en el `modulesUrl` propiedad. Si esta ubicación en el almacenamiento de blobs de Windows Azure, un símbolo de SA se puede especificar en la `sasToken` propiedad para tener acceso al archivo. Después de la .zip se descarga y desempaquetado, la función de configuración definida en `configurationFunction` ejecutar para generar el archivo MOF. A continuación, ejecuta la extensión `Start-DscConfiguration -Force` en el archivo MOF generado. La extensión de captura de salida y escribe de nuevo para el canal de estado de Azure. Desde este punto, el LCM DSC controladores de supervisión y corrección como normal. 

## <a name="powershell-cmdlets"></a>Cmdlets de PowerShell ##

Cmdlets de PowerShell puede usarse con BRAZO o ASM para empaquetar, publicar y supervisar implementaciones de extensión DSC. Los siguientes cmdlets enumerados son los módulos ASM, pero se puede reemplazar "Azure" por "AzureRm" para usar el modelo ARM. Por ejemplo, `Publish-AzureVMDscConfiguration` usa ASM, donde `Publish-AzureRmVMDscConfiguration` usa ARM. 

`Publish-AzureVMDscConfiguration`toma en un archivo de configuración, analiza para recursos DSC dependientes y crea un archivo .zip que contiene la configuración y recursos de DSC necesarios para establecer la configuración. También puede crear el paquete localmente mediante la `-ConfigurationArchivePath` parámetro. En caso contrario, se publica el archivo .zip con el almacenamiento de blobs de Windows Azure y seguro con un símbolo de SA.

El archivo .zip creado por este cmdlet tiene el script de configuración. ps1 en la raíz de la carpeta de archivado. Los recursos tienen la carpeta de módulo que se coloca en la carpeta de archivado. 

`Set-AzureVMDscExtension`Inserta la configuración es necesaria por la extensión de PowerShell DSC en un objeto de configuración de máquina virtual, a continuación, se puede aplicar a una máquina virtual de Azure con `Update-AzureVM`.

`Get-AzureVMDscExtension`Recupera el estado de extensión DSC de una máquina virtual determinada. 

`Get-AzureVMDscExtensionStatus`Recupera el estado de la configuración de DSC aprobado por el controlador de extensión DSC. Esta acción puede realizarse en una única VM o grupo de máquinas virtuales.

`Remove-AzureVMDscExtension`Quita el controlador de extensión de una máquina virtual determinada. Este cmdlet **quitar la configuración,** desinstalar WMF o cambiar la configuración aplicada en la máquina virtual. Solo se quita el controlador de extensión. 

**Diferencias clave en cmdlets ASM y ARM**

- Cmdlets ARM son sincrónicos. Cmdlets ASM son asincrónicas.
- ResourceGroupName, VMName, ArchiveStorageAccountName, versión y ubicación son todos los parámetros requeridos nuevos.
- ArchiveResourceGroupName es un parámetro opcional nuevo de ARM. Puede especificar este parámetro cuando su cuenta de almacenamiento pertenece a otro grupo de recursos a la que se crea la máquina virtual.
- ConfigurationArchive se denomina ArchiveBlobName en el BRAZO
- Nombre del contenedor se denomina ArchiveContainerName en el BRAZO
- StorageEndpointSuffix se denomina ArchiveStorageEndpointSuffix en el BRAZO
- Se ha agregado el modificador AutoUpdate a ARM para habilitar la actualización automática del controlador de extensión a la versión más reciente que está disponible. Parámetros de que este parámetro se pueden causar reinicia en la máquina virtual cuando se publique una nueva versión del WMF. 


## <a name="azure-portal-functionality"></a>Funcionalidad de portal de Azure ##
Vaya a una máquina virtual clásica. En Configuración -> General haga clic en "Extensiones". Crea un nuevo panel. Haga clic en "Agregar" y seleccione DSC de PowerShell.

El portal de entrada necesita.
**Configuración de módulos o Script**: este campo es obligatorio. Requiere un archivo. ps1 que contiene un script de configuración o un archivo .zip con un script de configuración. ps1 en la raíz y todos los recursos dependientes en carpetas de módulo dentro de la .zip. Se puede crear con la `Publish-AzureVMDscConfiguration -ConfigurationArchivePath` cmdlet incluido en el SDK de PowerShell de Azure. El archivo .zip se carga en el almacenamiento de blobs de usuario protegido por un símbolo de SA. 

**Archivo de datos de configuración de PSD1**: este campo es opcional. Si su configuración requiere un archivo de datos de configuración en .psd1, use este campo para seleccionarlo y cárguelo en el almacenamiento de blobs de usuario, donde está protegido por un símbolo de SA. 
 
**Nombre de configuración de Module-Qualified**: archivos. ps1 pueden tener varias funciones de configuración. Escriba el nombre de la secuencia de comandos de configuración. ps1 seguida por un '\' y el nombre de la función de la configuración. Por ejemplo, si su script. ps1 tiene el nombre "configuration.ps1" y la configuración es "IisInstall", escriba:`configuration.ps1\IisInstall`

**Argumentos de configuración**: si la función configuración toma argumentos, escriba aquí en el formato `argumentName1=value1,argumentName2=value2`. Tenga en cuenta que este formato es un formato distinto cómo se aceptan argumentos de configuración a través de los cmdlets de PowerShell o plantillas de administrador de recursos. 

## <a name="getting-started"></a>Introducción ##

La extensión de Azure DSC toma en documentos de configuración de DLC e implementa el en máquinas virtuales de Azure. Un ejemplo sencillo de una configuración sigue. Guardarlo localmente como "IisInstall.ps1":

```powershell
configuration IISInstall 
{ 
    node "localhost"
    { 
        WindowsFeature IIS 
        { 
            Ensure = "Present" 
            Name = "Web-Server"                       
        } 
    } 
}
```

Los siguientes pasos colocar la secuencia de comandos de IisInstall.ps1 en la máquina virtual especificada, ejecutan la configuración y volver informan sobre el estado.
 
```powershell
#Azure PowerShell cmdlets are required
Import-Module Azure

#Use an existing Azure Virtual Machine, 'DscDemo1'
$demoVM = Get-AzureVM DscDemo1

#Publish the configuration script into user storage.
Publish-AzureVMDscConfiguration -ConfigurationPath ".\IisInstall.ps1" -StorageContext $storageContext -Verbose -Force

#Set the VM to run the DSC configuration
Set-AzureVMDscExtension -VM $demoVM -ConfigurationArchive "demo.ps1.zip" -StorageContext $storageContext -ConfigurationName "runScript" -Verbose

#Update the configuration of an Azure Virtual Machine
$demoVM | Update-AzureVM -Verbose

#check on status
Get-AzureVMDscExtensionStatus -VM $demovm -Verbose
```

## <a name="logging"></a>Registro de información ##

Los registros se colocan en:

C:\WindowsAzure\Logs\Plugins\Microsoft.PowerShell.DSC\[número de versión]

## <a name="next-steps"></a>Pasos siguientes ##

Para obtener más información acerca de PowerShell DSC, [visite el centro de documentación de PowerShell](https://msdn.microsoft.com/powershell/dsc/overview). 

Examine la [plantilla de administrador de recursos de Azure para la extensión DSC](virtual-machines-windows-extensions-dsc-template.md
). 

Para buscar funciones adicionales puede administrar con PowerShell DSC, [busque en la Galería de PowerShell](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0) para obtener más recursos DSC.

Para obtener detalles sobre pasar parámetros confidenciales en configuraciones, vea [Administrar credenciales forma segura con el controlador de extensión DSC](virtual-machines-windows-extensions-dsc-credentials.md).

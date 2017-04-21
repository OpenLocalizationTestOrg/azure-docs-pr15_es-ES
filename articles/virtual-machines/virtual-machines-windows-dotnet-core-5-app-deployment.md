<properties
   pageTitle="Automatización de implementación de la aplicación con las extensiones de máquina Virtual | Microsoft Azure"
   description="Tutorial de DotNet principales de Azure Máquina Virtual"
   services="virtual-machines-windows"
   documentationCenter="virtual-machines"
   authors="neilpeterson"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="10/21/2016"
   ms.author="nepeters"/>

# <a name="application-deployment-with-azure-resource-manager-templates"></a>Implementación de la aplicación con las plantillas de administrador de recursos de Azure

Una vez identificados todos los requisitos de infraestructura Azure y traducir a una plantilla de implementación, la implementación de aplicaciones reales debe solucionarse. Implementación de la aplicación se refiere a la instalación de los archivos binarios real a recursos de Azure. Para obtener el ejemplo de la tienda de música, .net Core y IIS necesita estar instalado y configurado en cada máquina virtual. Los archivos binarios de la tienda de música deben instalarse en la máquina virtual y la base de datos de la tienda de música creado previamente.

Este documento detalla cómo pueden automatizar extensiones de máquina Virtual implementación de aplicaciones y la configuración para máquinas virtuales de Windows Azure. Se resaltan todas las dependencias y configuraciones únicas. Para obtener la mejor experiencia, implementar previamente una instancia de la solución a su suscripción de Azure y funcionan junto con la plantilla de administrador de recursos de Azure. La plantilla completa puede encontrarse aquí: [Implementación de almacenamiento de música en Windows](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-Windows).

## <a name="configuration-script"></a>Secuencia de comandos de configuración

Extensiones de máquina virtual son programas especializados que se ejecutan en máquinas virtuales de Windows para proporcionar automatización de configuración. Extensiones están disponibles para muchos propósitos específicos como antivirus, la configuración de registro y la configuración de Docker. La extensión de Script personalizado puede utilizarse para ejecutar cualquier secuencia de comandos en una máquina virtual. Con el ejemplo de la tienda de música, es la extensión de scripts personalizados para configurar las máquinas virtuales de Windows e instalar la aplicación de almacenamiento de música.

Antes de que detalla cómo se declaraban extensiones de máquina virtual en una plantilla de administrador de recursos de Azure, examine la secuencia de comandos que se ejecute. Esta secuencia de comandos configura la máquina virtual de Windows para la aplicación de almacenamiento de música de host. Cuando se ejecuta, la secuencia de comandos instala todos es necesario software, instale la aplicación de almacenamiento de música de control de código fuente y preparar la base de datos. 

> Este ejemplo es para la demostración.

```none
Param (
    [string]$user,
    [string]$password,
    [string]$sqlserver
)

# firewall
netsh advfirewall firewall add rule name="http" dir=in action=allow protocol=TCP localport=80

# folders
New-Item -ItemType Directory c:\temp
New-Item -ItemType Directory c:\music

# install iis
Install-WindowsFeature web-server -IncludeManagementTools

# install dot.net core sdk
Invoke-WebRequest http://go.microsoft.com/fwlink/?LinkID=615460 -outfile c:\temp\vc_redistx64.exe
Start-Process c:\temp\vc_redistx64.exe -ArgumentList '/quiet' -Wait
Invoke-WebRequest https://go.microsoft.com/fwlink/?LinkID=809122 -outfile c:\temp\DotNetCore.1.0.0-SDK.Preview2-x64.exe
Start-Process c:\temp\DotNetCore.1.0.0-SDK.Preview2-x64.exe -ArgumentList '/quiet' -Wait
Invoke-WebRequest https://go.microsoft.com/fwlink/?LinkId=817246 -outfile c:\temp\DotNetCore.WindowsHosting.exe
Start-Process c:\temp\DotNetCore.WindowsHosting.exe -ArgumentList '/quiet' -Wait

# download / config music app
Invoke-WebRequest  https://github.com/Microsoft/dotnet-core-sample-templates/raw/master/dotnet-core-music-windows/music-app/music-store-azure-demo-pub.zip -OutFile c:\temp\musicstore.zip
Expand-Archive C:\temp\musicstore.zip c:\music
(Get-Content C:\music\config.json) | ForEach-Object { $_ -replace "<replaceserver>", $sqlserver } | Set-Content C:\music\config.json
(Get-Content C:\music\config.json) | ForEach-Object { $_ -replace "<replaceuser>", $user } | Set-Content C:\music\config.json
(Get-Content C:\music\config.json) | ForEach-Object { $_ -replace "<replacepass>", $password } | Set-Content C:\music\config.json

# workaround for db creation bug
Start-Process 'C:\Program Files\dotnet\dotnet.exe' -ArgumentList 'c:\music\MusicStore.dll'

# configure iis
Remove-WebSite -Name "Default Web Site"
Set-ItemProperty IIS:\AppPools\DefaultAppPool\ managedRuntimeVersion ""
New-Website -Name "MusicStore" -Port 80 -PhysicalPath C:\music\ -ApplicationPool DefaultAppPool
& iisreset
```

## <a name="vm-script-extension"></a>Extensión de secuencia de comandos VM

Extensiones de VM se puede ejecutar en una máquina virtual en tiempo de compilación incluyendo el recurso de extensión en la plantilla de administrador de recursos de Azure. Pueden agregarse la extensión con el asistente Agregar recurso de Visual Studio o insertando JSON válido en la plantilla. El recurso de secuencia de comandos extensión está anidado dentro de los recursos de la máquina Virtual; Esto puede verse en el ejemplo siguiente.

Siga este vínculo para ver el ejemplo JSON dentro de la plantilla de administrador de recursos: [Extensión de secuencia de comandos de máquina virtual](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L339). 

Observe que en la debajo de JSON que se almacena el script en GitHub. Esta secuencia de comandos también puede almacenarse en el almacenamiento de blobs de Windows Azure. Además, las plantillas de administrador de recursos de Azure permiten la cadena de ejecución de scripts se construye de modo que los valores de parámetros de plantilla se pueden utilizar como parámetros para la ejecución de scripts. En este caso se proporcionan datos al implementar las plantillas y, a continuación, estos valores pueden usarse al ejecutar la secuencia de comandos.

```none
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.4",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
      ]
    },
    "protectedSettings": {
      "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
  }
}
```

Para obtener más información sobre el uso de la extensión de scripts personalizados, vea [extensiones de script personalizado con las plantillas de administrador de recursos](./virtual-machines-windows-extensions-customscript.md).

## <a name="next-step"></a>Siguiente paso

<hr>

[Explorar Azure más plantillas de administrador de recursos](https://github.com/Azure/azure-quickstart-templates)

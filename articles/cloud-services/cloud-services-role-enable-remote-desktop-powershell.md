<properties
pageTitle="Habilitar la conexión a Escritorio remoto para un rol de servicios de nube de Azure con PowerShell"
description="Cómo configurar la aplicación de servicio de nube azure mediante PowerShell para permitir conexiones a Escritorio remoto"
services="cloud-services"
documentationCenter=""
authors="thraka"
manager="timlt"
editor=""/>
<tags
ms.service="cloud-services"
ms.workload="tbd"
ms.tgt_pltfrm="na"
ms.devlang="na"
ms.topic="article"
ms.date="08/05/2016"
ms.author="adegeo"/>

# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-using-powershell"></a>Habilitar la conexión a Escritorio remoto para un rol de servicios de nube de Azure con PowerShell

>[AZURE.SELECTOR]
- [Portal de clásico de Azure](cloud-services-role-enable-remote-desktop.md)
- [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
- [Visual Studio](../vs-azure-tools-remote-desktop-roles.md)


Escritorio remoto le permite tener acceso a la versión de escritorio de una función que se ejecuta en Azure. Puede usar una conexión a Escritorio remoto para solucionar problemas y diagnóstico de problemas con la aplicación mientras se está ejecutando.

En este artículo se describe cómo habilitar Escritorio remoto en las funciones de servicio de nube con PowerShell. Vea [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) para los requisitos previos necesarios para este artículo. PowerShell utiliza la extensión de escritorio remoto para poder habilitar Escritorio remoto después de implementar la aplicación.


## <a name="configure-remote-desktop-from-powershell"></a>Configurar Escritorio remoto de PowerShell

El cmdlet [Set-AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495117.aspx) le permite habilitar Escritorio remoto en funciones especificadas o todas las funciones de la implementación de servicio de nube. El cmdlet le permite especificar el nombre de usuario y la contraseña para el usuario de escritorio remoto a través del parámetro de *credenciales* que acepta un objeto PSCredential.

Si está utilizando PowerShell de forma interactiva, puede establecer fácilmente el objeto PSCredential llamando el cmdlet [Get-credenciales](https://technet.microsoft.com/library/hh849815.aspx) .

```
$remoteusercredentials = Get-Credential
```

Este comando muestra un cuadro de diálogo que permite especificar el nombre de usuario y la contraseña para el usuario remoto de forma segura.

Puesto que ayuda PowerShell en escenarios de automatización, también puede configurar el objeto **PSCredential** de manera que no requiere interacción del usuario. En primer lugar, debe configurar una contraseña segura. Comenzar con la que se especifica una contraseña de texto sin formato convertirla en una cadena segura con [ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx). A continuación debe convertir esta cadena segura en una cadena estándar cifrada con [ConvertFrom-SecureString](https://technet.microsoft.com/library/hh849814.aspx). Ahora puede guardar esta cadena cifrada estándar a un archivo con el [Contenido del conjunto](https://technet.microsoft.com/library/ee176959.aspx).

También puede crear un archivo de contraseña segura para que no tiene que escribir la contraseña cada vez. Además, un archivo de contraseña segura es mejor que un archivo de texto sin formato. Use el siguiente PowerShell para crear un archivo de contraseña segura:

```
ConvertTo-SecureString -String "Password123" -AsPlainText -Force | ConvertFrom-SecureString | Set-Content "password.txt"
```

>[AZURE.IMPORTANT] Al establecer la contraseña, asegúrese de que cumple los [requisitos de complejidad](https://technet.microsoft.com/library/cc786468.aspx).

Para crear el objeto de credencial desde el archivo de contraseña segura, debe leer el contenido del archivo y volver a convertirlas una cadena segura con [ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx).

El cmdlet [Set-AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495117.aspx) también acepta un parámetro de *expiración* , que se especifica una **fecha y hora** en que expira la cuenta de usuario. Por ejemplo, puede establecer la cuenta caduque unos días desde la fecha y hora actuales.

Este ejemplo de PowerShell muestra cómo configurar la extensión de escritorio remoto en un servicio de nube:

```
$servicename = "cloudservice"
$username = "RemoteDesktopUser"
$securepassword = Get-Content -Path "password.txt" | ConvertTo-SecureString
$expiry = $(Get-Date).AddDays(1)
$credential = New-Object System.Management.Automation.PSCredential $username,$securepassword
Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry
```
Opcionalmente, también puede especificar la franja de implementación y funciones que desea habilitar Escritorio remoto en. Si no se especifican estos parámetros, el cmdlet habilita Escritorio remoto en todas las funciones en la franja de implementación de **producción** .

La extensión de escritorio remoto está asociada con una implementación. Si crea una nueva implementación para el servicio, debe habilitar Escritorio remoto en esa implementación. Si desea tener habilitado Escritorio remoto siempre, debe considerar la integración de las secuencias de comandos de PowerShell en el flujo de trabajo de implementación.


## <a name="remote-desktop-into-a-role-instance"></a>Escritorio remoto en una instancia de funciones
El cmdlet [Get-AzureRemoteDesktopFile](https://msdn.microsoft.com/library/azure/dn495261.aspx) se utiliza para escritorio remoto en una instancia de funciones específicas de su servicio de nube. Puede usar el parámetro *LocalPath* para descargar el archivo RDP localmente. O bien, puede usar el parámetro de *Inicio* para iniciar directamente el cuadro de diálogo conexión a Escritorio remoto para tener acceso a la instancia del rol de servicio de nube.

```
Get-AzureRemoteDesktopFile -ServiceName $servicename -Name "WorkerRole1_IN_0" -Launch
```


## <a name="check-if-remote-desktop-extension-is-enabled-on-a-service"></a>Comprobar si está habilitada la extensión de escritorio remoto en un servicio
El cmdlet [Get-AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495261.aspx) muestra que Escritorio remoto está habilitado o deshabilitado en una implementación de servicio. El cmdlet devuelve el nombre de usuario para el usuario de escritorio remoto y los roles que tiene habilitada la extensión de escritorio remoto. De forma predeterminada, esto sucede en la franja de implementación y puede usar la franja provisional en su lugar.

```
Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
```

## <a name="remove-remote-desktop-extension-from-a-service"></a>Quitar la extensión de escritorio remoto de un servicio
Si ya ha habilitado la extensión de escritorio remoto en una implementación y necesita actualizar la configuración de escritorio remota, quitar la extensión. Y habilitarlo de nuevo con la nueva configuración. Por ejemplo, si desea establecer una contraseña nueva para la cuenta de usuario remoto o la cuenta ha expirado. Esto es necesario en implementaciones existentes que tienen la extensión de escritorio remoto habilitada. Para implementaciones nuevas, simplemente se puede aplicar la extensión directamente.

Para quitar la extensión de escritorio remoto de la implementación, puede usar el cmdlet [Quitar AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495280.aspx) . Opcionalmente, también puede especificar la franja de implementación y la función desde la que desea quitar la extensión de escritorio remoto.

```
Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration
```

>[AZURE.NOTE] Para quitar completamente la configuración de la extensión, debe llamar el cmdlet *Quitar* con el parámetro **UninstallConfiguration** .
>
>El parámetro **UninstallConfiguration** desinstala cualquier configuración de extensión que se aplica al servicio. Cada configuración de extensión está asociado a la configuración del servicio. Lo que se llama el cmdlet *Quitar* sin **UninstallConfiguration** anula la asociación la <mark>implementación</mark> de la configuración de la extensión, elimina eficazmente la extensión. Sin embargo, la configuración de extensión permanece asociada con el servicio.



## <a name="additional-resources"></a>Recursos adicionales

[Cómo configurar los servicios de nube](cloud-services-how-to-configure.md)

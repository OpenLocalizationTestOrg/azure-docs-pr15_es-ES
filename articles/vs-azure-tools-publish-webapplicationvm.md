<properties
   pageTitle="WebApplicationVM publicar | Microsoft Azure"
   description="Obtenga información sobre cómo implementar una aplicación web a una máquina virtual. Esta secuencia de comandos crea los recursos necesarios en su suscripción de Azure si no existen."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="publish-webapplicationvm-windows-powershell-script"></a>Publicar-WebApplicationVM (script de Windows PowerShell)

Implementa una aplicación web a una máquina virtual. La secuencia de comandos crea los recursos necesarios en su suscripción de Azure si no existen.

```
Publish-WebApplicationVM
–Configuration <configuration>
-SubscriptionName <subscriptionName>
-WebDeployPackage <packageName>
-VMPassword @{Name = "name"; Password = "password")
-DatabaseServerPassword @{Name = "name"; Password = "password"}
-SendHostMessagesToOutput
-Verbose
```

### <a name="configuration"></a>Configuración

La ruta de acceso al archivo de configuración de JSON que describe los detalles de la implementación.

|Alias|Ninguno|
|---|---|
|¿Obligatorio?|Verdadero|
|Posición|con el nombre|
|Valor predeterminado|Ninguno|
|¿Aceptar entradas de canalización?|falso|
|¿Aceptar caracteres comodín?|falso|

### <a name="subscriptionname"></a>SubscriptionName

El nombre de la suscripción de Azure en la que desea crear la máquina virtual.

|Alias|Ninguno|
|---|---|
|¿Obligatorio?|falso|
|Posición|con el nombre|
|Valor predeterminado|Usa la primera suscripción en el archivo de suscripción|
|¿Aceptar entradas de canalización?|falso|
|¿Aceptar caracteres comodín?|falso|

### <a name="webdeploypackage"></a>WebDeployPackage

La ruta de acceso para el paquete de implementación web para publicar en la máquina virtual. Puede crear este paquete mediante el Asistente para la publicación Web en Visual Studio. Vea [Cómo: crear un paquete de implementación Web en Visual Studio](https://msdn.microsoft.com/library/dd465323.aspx).

|Alias|Ninguno|
|---|---|
|¿Obligatorio?|falso|
|Posición|con el nombre|
|Valor predeterminado|Ninguno|
|¿Aceptar entradas de canalización?|falso|
|¿Aceptar caracteres comodín?|falso|

### <a name="allowuntrusted"></a>AllowUntrusted

Si es true, permite el uso de certificados que no haya iniciado sesión por una autoridad de raíz de confianza.

|Alias|Ninguno|
|---|---|
|¿Obligatorio?|falso|
|Posición|con el nombre|
|Valor predeterminado|falso|
|¿Aceptar entradas de canalización?|falso|
|¿Aceptar caracteres comodín?|falso|

### <a name="vmpassword"></a>VMPassword

Las credenciales de la cuenta de máquina virtual. Ejemplo: - VMPassword @{Name = "admin"; Contraseña = "contraseña"}

|Alias|Ninguno|
|---|---|
|¿Obligatorio?|falso|
|Posición|con el nombre|
|Valor predeterminado|Ninguno|
|¿Aceptar entradas de canalización?|falso|
|¿Aceptar caracteres comodín?|falso|

### <a name="databaseserverpassword"></a>DatabaseServerPassword

Las credenciales para la base de datos SQL Azure. Ejemplo: - DatabaseServerPassword @{Name = "admin"; Contraseña = "contraseña"}

|Alias|Ninguno|
|---|---|
|¿Obligatorio?|falso|
|Posición|con el nombre|
|Valor predeterminado|Ninguno|
|¿Aceptar entradas de canalización?|falso|
|¿Aceptar caracteres comodín?|falso|

### <a name="sendhostmessagestooutput"></a>SendHostMessagesToOutput

Si es true, imprimir mensajes de la secuencia de comandos a la secuencia de salida.

|Alias|Ninguno|
|---|---|
|¿Obligatorio?|falso|
|Posición|con el nombre|
|Valor predeterminado|falso|
|¿Aceptar entradas de canalización?|falso|
|¿Aceptar caracteres comodín?|falso|

## <a name="remarks"></a>Notas

Para ver una explicación completa de cómo utilizar la secuencia de comandos para crear entornos de prueba y desarrollo, vea [Usar Windows scripts de PowerShell que publicar en entornos de prueba y desarrollo](vs-azure-tools-publishing-using-powershell-scripts.md).

El archivo de configuración de JSON especifica los detalles de lo que implementará. Incluye la información que especificó al crear el proyecto, como el nombre, el grupo afinidad, la imagen de disco duro virtual y el tamaño de la máquina virtual. También incluye los extremos de la máquina virtual, las bases de datos proporcionando, si hay alguna y parámetros de implementación de web. El código siguiente muestra un ejemplo de archivo de configuración de JSON:

```
{
    "environmentSettings": {
        "cloudService": {
            "name": "myvmname",
            "affinityGroup": "",
            "location": "West US",
            "virtualNetwork": "",
            "subnet": "",
            "availabilitySet": "",
            "virtualMachine": {
                "name": "myvmname",
                "vhdImage": "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201404.01-en.us-127GB.vhd",
                "size": "Small",
                "user": "vmuser1",
                "password": "",
                "enableWebDeployExtension": true,
                "endpoints": [
                    {
                        "name": "Http",
                        "protocol": "TCP",
                        "publicPort": "80",
                        "privatePort": "80"
                    },
                    {
                        "name": "Https",
                        "protocol": "TCP",
                        "publicPort": "443",
                        "privatePort": "443"
                    },
                    {
                        "name": "WebDeploy",
                        "protocol": "TCP",
                        "publicPort": "8172",
                        "privatePort": "8172"
                    },
                    {
                        "name": "Remote Desktop",
                        "protocol": "TCP",
                        "publicPort": "3389",
                        "privatePort": "3389"
                    },
                    {
                        "name": "Powershell",
                        "protocol": "TCP",
                        "publicPort": "5986",
                        "privatePort": "5986"
                    }
                ]
            }
        },
        "databases": [
            {
                "connectionStringName": "",
                "databaseName": "",
                "serverName": "",
                "user": "",
                "password": ""
            }
        ],
        "webDeployParameters": {
            "iisWebApplicationName": "Default Web Site"
        }
    }
}
```

Puede editar el archivo de configuración de JSON para cambiar lo que se aprovisiona. Se requiere una máquina virtual y un servicio de nube, pero la sección de la base de datos es opcional.

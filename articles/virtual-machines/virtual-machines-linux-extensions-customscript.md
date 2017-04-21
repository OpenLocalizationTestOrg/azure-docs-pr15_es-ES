<properties
   pageTitle="Scripts personalizados en máquinas virtuales de Linux | Microsoft Azure"
   description="Automatizar tareas de configuración de Linux VM mediante la extensión de Script personalizado"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="neilpeterson"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="09/22/2016"
   ms.author="nepeters"/>

# <a name="using-the-azure-custom-script-extension-with-linux-virtual-machines"></a>Usar la extensión de Azure Script personalizado con máquinas virtuales de Linux

La extensión de Script personalizado descarga y ejecuta scripts en máquinas virtuales de Windows Azure. Esta extensión resulta útil para la configuración de implementación de publicación, instalación de software o cualquier otra configuración o tareas de administración. Las secuencias de comandos se pueden descargados de almacenamiento de Azure o en otra ubicación de internet accesible o proporcionados a la extensión de tiempo de ejecución. La extensión de Script personalizado se integra con las plantillas de administrador de recursos de Azure y también se puede ejecutar mediante la CLI de Azure, PowerShell, portal Azure o la API de REST de máquina Virtual de Azure.

Este documento detalla cómo utilizar la extensión de Script personalizado de Azure CLI y una plantilla de administrador de recursos de Azure y también detalla los pasos para solucionar problemas en los sistemas Linux.

## <a name="extension-configuration"></a>Configuración de extensión

La configuración de la extensión de Script personalizado especifica cosas como la ubicación de la secuencia de comandos y el comando para ejecutar. Esta configuración se puede almacenar archivos de configuración, especificada en la línea de comandos o en una plantilla de administrador de recursos de Azure. Datos confidenciales pueden almacenarse en una configuración protegida, que se cifra y descifra solo dentro de la máquina virtual. La configuración protegida es útil cuando el comando de ejecución incluye información confidencial, como una contraseña.

### <a name="public-configuration"></a>Configuración de pública

Esquema:

- **commandToExecute**: (obligatorio, cadena) la secuencia de comandos de punto de entrada para ejecutar
- **fileUris**: (opcional, matriz de cadenas) las direcciones URL para descargar archivos.
- **marca de tiempo** (opcional, integer) use este campo solo para desencadenar un volver a ejecutar de la secuencia de comandos cambiando el valor de este campo.

```none
{
  "fileUris": ["<url>"],
  "commandToExecute": "<command-to-execute>"
}
```

### <a name="protected-configuration"></a>Configuración protegida

Esquema:

- **commandToExecute**: (opcional, cadena) la secuencia de comandos de punto de entrada para ejecutar. Si el comando contiene información confidencial, como contraseñas, utilice este campo.
- **storageAccountName**: (opcional, cadena) el nombre de cuenta de almacenamiento. Si especifica credenciales de almacenamiento, fileUris todos deben ser direcciones URL para Blobs de Azure.
- **storageAccountKey**: (opcional, cadena) la tecla de acceso de la cuenta de almacenamiento.


```json
{
  "commandToExecute": "<command-to-execute>",
  "storageAccountName": "<storage-account-name>",
  "storageAccountKey": "<storage-account-key>"
}
```

## <a name="azure-cli"></a>CLI de Azure

Al utilizar la CLI de Azure para ejecutar la extensión de Script personalizado, cree un archivo de configuración o archivos que contienen como mínimo el uri de archivo y a continuación, la secuencia de comandos de ejecución.

```none
azure vm extension set <resource-group> <vm-name> CustomScript Microsoft.Azure.Extensions 2.0 --auto-upgrade-minor-version --public-config-path /scirpt-config.json
```

Opcionalmente, se puede ejecutar el comando mediante la `--public-config` y `--private-config` opción, que permite la configuración que se especifique durante la ejecución y sin un archivo de configuración independiente.

```none
azure vm extension set <resource-group> <vm-name> CustomScript Microsoft.Azure.Extensions 2.0 --auto-upgrade-minor-version --public-config '{"fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"],"commandToExecute": "./hello.sh"}'
```

### <a name="azure-cli-examples"></a>Ejemplos de Azure CLI

**Ejemplo 1** : configuración de pública con el archivo de script.

```json
{
  "fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"],
  "commandToExecute": "./hello.sh"
}
```

Comando CLI Azure:

```none
azure vm extension set <resource-group> <vm-name> CustomScript Microsoft.Azure.Extensions 2.0 --auto-upgrade-minor-version --public-config-path /public.json
```

**Ejemplo 2** : configuración de pública con ningún archivo de script.

```json
{
  "commandToExecute": "apt-get -y update && apt-get install -y apache2"
}
```

Comando CLI Azure:

```none
azure vm extension set <resource-group> <vm-name> CustomScript Microsoft.Azure.Extensions 2.0 --auto-upgrade-minor-version --public-config-path /public.json
```

**Ejemplo 3** : un archivo de configuración público se utiliza para especificar el archivo de script URI y un archivo de configuración protegida se utiliza para especificar el comando que se ejecutará.

Archivo de configuración público:

```json
{
  "fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"],
}
```

Archivo de configuración protegida:  

```json
{
  "commandToExecute": "./hello.sh <password>"
}
```

Comando CLI Azure:

```none
azure vm extension set <resource-group> <vm-name> CustomScript Microsoft.Azure.Extensions 2.0 --auto-upgrade-minor-version --public-config-path ./public.json --private-config-path ./protected.json
```

## <a name="resource-manager-template"></a>Plantilla de administrador de recursos

La extensión de secuencia de comandos de Azure personalizada se puede ejecutar en tiempo de implementación de máquina Virtual usando una plantilla de administrador de recursos. Para ello, agregue JSON con el formato correcto a la plantilla de implementación.

### <a name="resource-manager-examples"></a>Ejemplos de administrador de recursos

**Ejemplo 1** : configuración público.

```json
{
    "name": "scriptextensiondemo",
    "type": "extensions",
    "location": "[resourceGroup().location]",
    "apiVersion": "2015-06-15",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', parameters('scriptextensiondemoName'))]"
    ],
    "tags": {
        "displayName": "scriptextensiondemo"
    },
    "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
      "settings": {
        "fileUris": [
          "https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"
        ],
        "commandToExecute": "sh hello.sh"
      }
    }
}
```

**Ejemplo 2** : comando de ejecución de configuración protegida.

```json
{
  "name": "config-app",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh
      ]              
    },
    "protectedSettings": {
      "commandToExecute": "sh hello.sh <password>"
    }
  }
}
```

Vea .net Core música almacén de demostración para obtener un ejemplo completo - [Demostración de la tienda de música](https://github.com/neilpeterson/nepeters-azure-templates/tree/master/dotnet-core-music-linux-vm-sql-db).

## <a name="troubleshooting"></a>Solución de problemas

Cuando se ejecuta la extensión de Script personalizado, la secuencia de comandos es creado o descargado en un directorio similar al siguiente ejemplo. El resultado del comando también se guarda en este directorio en `stdout` y `stderr` archivo.

```none
/var/lib/azure/custom-script/download/0/
```

La extensión de secuencia de comandos de Azure genera un registro, que se encuentran aquí.

```none
/var/log/azure/customscript/handler.log
```

También se puede recuperar el estado de ejecución de la extensión de Script personalizado con la CLI de Azure.

```none
azure vm extension get <resource-group> <vm-name>
```

El resultado es similar a la siguiente texto:

```none
info:    Executing command vm extension get
+ Looking up the VM "scripttst001"
data:    Publisher                   Name                                      Version  State
data:    --------------------------  ----------------------------------------  -------  ---------
data:    Microsoft.Azure.Extensions  CustomScript                              2.0      Succeeded
data:    Microsoft.OSTCExtensions    Microsoft.Insights.VMDiagnosticsSettings  2.3      Succeeded
info:    vm extension get command OK
```

## <a name="next-steps"></a>Pasos siguientes

Para obtener información sobre otras extensiones de secuencia de comandos de máquina virtual, vea [Introducción a la extensión de secuencia de comandos de Azure para Linux](./virtual-machines-linux-extensions-features.md).
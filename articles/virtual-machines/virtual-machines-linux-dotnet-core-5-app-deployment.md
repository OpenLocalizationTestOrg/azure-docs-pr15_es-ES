<properties
   pageTitle="Automatización de implementación de la aplicación con las extensiones de máquina Virtual | Microsoft Azure"
   description="Tutorial de DotNet principales de Azure Máquina Virtual"
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="neilpeterson"
   manager="timlt"
   editor="tysonn"
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="09/21/2016"
   ms.author="nepeters"/>

# <a name="application-deployment-with-azure-resource-manager-templates"></a>Implementación de la aplicación con las plantillas de administrador de recursos de Azure

Una vez identificados todos los requisitos de infraestructura Azure y traducir a una plantilla de implementación, la implementación de aplicaciones reales debe solucionarse. Implementación de la aplicación se refiere a la instalación de los archivos binarios real a recursos de Azure. Para obtener el ejemplo de la tienda de música, .net Core, NGINX y Supervisor necesitan estar instalado y configurado en cada máquina virtual. Los archivos binarios de la tienda de música deben instalarse en la máquina virtual y la base de datos de la tienda de música creado previamente.

Este documento detalla cómo pueden automatizar extensiones de máquina Virtual implementación de aplicaciones y la configuración para máquinas virtuales de Windows Azure. Se resaltan todas las dependencias y configuraciones únicas. Para obtener la mejor experiencia, implementar previamente una instancia de la solución a su suscripción de Azure y funcionan junto con la plantilla de administrador de recursos de Azure. La plantilla completa puede encontrarse aquí: [Implementación de almacenamiento de música en Ubuntu](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).

## <a name="configuration-script"></a>Secuencia de comandos de configuración

Extensiones de máquina virtual son programas especializados que se ejecutan en máquinas virtuales de Windows para proporcionar automatización de configuración. Extensiones están disponibles para muchos propósitos específicos como antivirus, la configuración de registro y la configuración de Docker. Una extensión de script personalizado puede utilizarse para ejecutar cualquier secuencia de comandos en una máquina virtual. Con el ejemplo de la tienda de música, es la extensión de scripts personalizados para configurar las máquinas virtuales de Ubuntu e instalar la aplicación de almacenamiento de música.

Antes de que detalla cómo se declaraban extensiones de máquina virtual en una plantilla de administrador de recursos de Azure, examine la secuencia de comandos que se ejecute. Esta secuencia de comandos configura la máquina virtual de Ubuntu para hospedar la aplicación de almacenamiento de música. Cuando se ejecuta, la secuencia de comandos instala todos es necesario software, instale la aplicación de almacenamiento de música de control de código fuente y preparar la base de datos. 

Para obtener más información acerca del hospedaje .net principales de la aplicación en Linux, consulte [publicar en un entorno de producción Linux](https://docs.asp.net/en/latest/publishing/linuxproduction.html). 

> Este ejemplo es para la demostración.

```none
#!/bin/bash

# install dotnet core
sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ trusty main" > /etc/apt/sources.list.d/dotnetdev.list'
sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
sudo apt-get update
sudo apt-get install -y dotnet-dev-1.0.0-preview2-003121

# download application
sudo wget https://raw.github.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/music-app/music-store-azure-demo-pub.tar /
sudo mkdir /opt/music
sudo tar -xf music-store-azure-demo-pub.tar -C /opt/music

# install nginx, update config file
sudo apt-get install -y nginx
sudo service nginx start
sudo touch /etc/nginx/sites-available/default
sudo wget https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/music-app/nginx-config/default -O /etc/nginx/sites-available/default
sudo cp /opt/music/nginx-config/default /etc/nginx/sites-available/
sudo nginx -s reload

# update and secure music config file
sed -i "s/<replaceserver>/$1/g" /opt/music/config.json
sed -i "s/<replaceuser>/$2/g" /opt/music/config.json
sed -i "s/<replacepass>/$3/g" /opt/music/config.json
sudo chown $2 /opt/music/config.json
sudo chmod 0400 /opt/music/config.json

# config supervisor
sudo apt-get install -y supervisor
sudo touch /etc/supervisor/conf.d/music.conf
sudo wget https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/music-app/supervisor/music.conf -O /etc/supervisor/conf.d/music.conf
sudo service supervisor stop
sudo service supervisor start

# pre-create music store database
/usr/bin/dotnet /opt/music/MusicStore.dll &
```

## <a name="vm-script-extension"></a>Extensión de secuencia de comandos VM

Extensiones de VM se puede ejecutar en una máquina virtual en tiempo de compilación incluyendo el recurso de extensión en la plantilla de administrador de recursos de Azure. Pueden agregarse la extensión con el asistente Agregar recurso de Visual Studio o insertando JSON válido en la plantilla. El recurso de secuencia de comandos extensión está anidado dentro de los recursos de la máquina Virtual; Esto puede verse en el ejemplo siguiente.

Siga este vínculo para ver el ejemplo JSON dentro de la plantilla de administrador de recursos: [Extensión de secuencia de comandos de máquina virtual](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L359). 

Observe que en la debajo de JSON que se almacena el script en GitHub. Esta secuencia de comandos también puede almacenarse en el almacenamiento de blobs de Windows Azure. Además, las plantillas de administrador de recursos de Azure permiten la cadena de ejecución de scripts construye de modo que los valores de parámetros de plantilla se pueden utilizar como parámetros para la ejecución de scripts. En este caso se proporcionan datos al implementar las plantillas y, a continuación, estos valores pueden usarse al ejecutar la secuencia de comandos.

```none
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
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
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
      ]
    },
    "protectedSettings": {
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
  }
}
```

Para obtener más información sobre el uso de la extensión de scripts personalizados, vea [extensiones de script personalizado con las plantillas de administrador de recursos](./virtual-machines-linux-extensions-customscript.md).

## <a name="next-step"></a>Siguiente paso

<hr>

[Explorar Azure más plantillas de administrador de recursos](https://github.com/Azure/azure-quickstart-templates)

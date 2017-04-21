<properties
   pageTitle="Uso de la extensión de Azure Docker VM | Microsoft Azure"
   description="Obtenga información sobre cómo usar la extensión Docker VM rápidamente y de forma segura implementar un entorno de Docker en Azure con plantillas de administrador de recursos."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="10/25/2016"
   ms.author="iainfou"/>

# <a name="create-a-docker-environment-in-azure-using-the-docker-vm-extension"></a>Crear un entorno de Docker en Azure con la extensión Docker VM
Docker es una plataforma de imagen que le permite trabajar rápidamente con contenedores Linux (y Windows también) y la administración de contenedor populares. En Azure, hay varias maneras puede implementar Docker según sus necesidades. En este artículo se centra en usar la extensión Docker VM y plantillas de administrador de recursos de Azure. 

Para obtener más información acerca de los métodos de implementación diferentes, incluido el uso de la máquina Docker y servicios de contenedor de Azure, consulte los artículos siguientes:

- Prototipo rápidamente una aplicación, puede crear un único host Docker con [Machine Docker](./virtual-machines-linux-docker-machine.md).
- Para entornos más grandes, más estables, puede usar la extensión de Azure Docker VM, que también es compatible con [Docker redactar](https://docs.docker.com/compose/overview/) para generar implementaciones de contenedor coherentes. Este artículo se describen con la extensión de Azure Docker VM.
- Para crear entornos de producción: preparados, scalable que proporcionan herramientas adicionales de programación y administración, puede implementar un [clúster Docker enjambre en Azure contenedor Servicios](../container-service/container-service-deployment.md).


## <a name="azure-docker-vm-extension-overview"></a>Introducción a la extensión Docker VM Azure
La extensión de Azure Docker VM se instala y configura el daemon Docker cliente Docker y redacte Docker en la máquina virtual (VM) de Linux. Mediante la extensión de Azure Docker VM, tener más control y características que simplemente usar Docker máquina o crear el host de Docker usted mismo. Estas características adicionales, como [Docker redacte](https://docs.docker.com/compose/overview/), asegúrese de la extensión de Azure Docker VM adecuada para entornos de producción o programador más rigurosa.

Plantillas de administrador de recursos de Azure definen la estructura completa de su entorno. Las plantillas permiten crear y configurar recursos como el host de Docker máquinas virtuales, almacenamiento, controles de acceso basado en roles (RBAC) y diagnósticos. Puede volver a usar estas plantillas para crear distribuciones adicionales de manera coherente. Para obtener más información acerca del Administrador de recursos de Azure y plantillas, vea [información general del Administrador de recursos](../azure-resource-manager/resource-group-overview.md). 


## <a name="deploy-a-template-with-the-azure-docker-vm-extension"></a>Implementar una plantilla con la extensión de Azure Docker VM
Vamos a usar una plantilla de tutorial existente para crear una VM Ubuntu que usa la extensión de Azure Docker VM para instalar y configurar el host de Docker. Puede ver la plantilla aquí: [implementación Simple de una máquina virtual de Ubuntu con Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). 

Necesita la [Última CLI Azure](../xplat-cli-install.md) instalado y conectado con el modo de administrador de recursos como sigue:

```
azure config mode arm
```

Implementar la plantilla mediante CLI Azure, que especifica la URI de la plantilla. En el ejemplo siguiente se crea un grupo de recursos denominado `myResourceGroup` en la `WestUS` ubicación. Usar su propio nombre de grupo de recursos y la ubicación del modo siguiente:

```
azure group create --name myResourceGroup --location "West US" \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

Responda a las indicaciones para asignar un nombre a su cuenta de almacenamiento, proporcione un nombre de usuario y la contraseña y proporcione un nombre DNS. El resultado es similar al siguiente ejemplo:

```
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Updating resource group myResourceGroup
info:    Updated resource group myResourceGroup
info:    Supply values for the following parameters
newStorageAccountName: mystorageaccount
adminUsername: ops
adminPassword: P@ssword!
dnsNameForPublicIP: mypublicip
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/guid/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK

```

Devuelve de Azure CLI a la pregunta después de unos pocos segundos, pero el host Docker todavía está creado y configurado por la extensión de Azure Docker VM. Se necesitan unos minutos para la implementación Finalizar. Puede ver los detalles sobre el estado de host Docker utilizando la `azure vm show` comando.

En el ejemplo siguiente se comprueba el estado de la máquina virtual denominado `myDockerVM` (el nombre predeterminado de la plantilla - no cambiar este nombre) en el grupo de recursos denominado `myResourceGroup`. Escriba el nombre del grupo de recursos que creó en el paso anterior:

```bash
azure vm show -g myResourceGroup -n myDockerVM
```

El resultado de la `azure vm show` comando es similar al siguiente ejemplo:

```
info:    Executing command vm show
+ Looking up the VM "myDockerVM"
+ Looking up the NIC "myVMNicD"
+ Looking up the public ip "myPublicIPD"
data:    Id                              :/subscriptions/guid/resourceGroups/myresourcegroup/providers/Microsoft.Compute/virtualMachines/MyDockerVM
data:    ProvisioningState               :Succeeded
data:    Name                            :MyDockerVM
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
[...]
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-33-D3-95
data:          Provisioning State        :Succeeded
data:          Name                      :myVMNicD
data:          Location                  :westus
data:            Public IP address       :13.91.107.235
data:            FQDN                    :mypublicip.westus.cloudapp.azure.com]
data:
data:    Diagnostics Instance View:
info:    vm show command OK
```

Cerca de la parte superior del resultado, verá la `ProvisioningState` de la máquina virtual. Cuando se mostrarán `Succeeded`, ha finalizado la implementación y puede SSH para la máquina virtual.

Hacia el final de la salida `FQDN` muestra el nombre de dominio completo de su host Docker. Este FQDN es lo utilice para SSH a su host Docker en el resto de los pasos.


## <a name="deploy-your-first-nginx-container"></a>Implementar el primer contenedor nginx
Finaliza la implementación, SSH a su nuevo host Docker desde el equipo local. Escriba su propio nombre de usuario y el nombre de dominio completo como sigue:

```bash
ssh ops@mypublicip.westus.cloudapp.azure.com
```

Una vez que ha iniciado sesión el host Docker, vamos a ejecutar un contenedor de nginx:

```
sudo docker run -d -p 80:80 nginx
```

El resultado es similar al siguiente ejemplo mientras se descarga la imagen nginx y un contenedor iniciado:

```
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
efd26ecc9548: Pull complete
a3ed95caeb02: Pull complete
a48df1751a97: Pull complete
8ddc2d7beb91: Pull complete
Digest: sha256:2ca2638e55319b7bc0c7d028209ea69b1368e95b01383e66dfe7e4f43780926d
Status: Downloaded newer image for nginx:latest
b6ed109fb743a762ff21a4606dd38d3e5d35aff43fa7f12e8d4ed1d920b0cd74
```

Comprobar el estado de los contenedores que se ejecuta en el host Docker como sigue:

```
sudo docker ps
```

El resultado es similar al siguiente ejemplo, que muestra que el contenedor nginx se está ejecutando y puertos TCP 80 y 443 y reenvíen:

```
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                         NAMES
b6ed109fb743        nginx               "nginx -g 'daemon off"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp, 443/tcp   adoring_payne
```

Para ver el contenedor en acción, abra un explorador web y escriba el nombre FQDN de su host de Docker:

![Ejecución de ngnix de contenedor](./media/virtual-machines-linux-dockerextension/nginxrunning.png)


## <a name="azure-docker-vm-extension-template-reference"></a>Referencia de plantillas de extensión de Azure VM Docker
En el ejemplo anterior, se utiliza una plantilla de tutorial existente. También puede implementar la extensión de Azure Docker VM con sus propias plantillas de administrador de recursos. Para ello, agregue lo siguiente a las plantillas de administrador de recursos, definir la `vmName` de la máquina virtual correctamente:

```
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(variables('vmName'), '/DockerExtension'))]",
  "apiVersion": "2015-05-01-preview",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "DockerExtension",
    "typeHandlerVersion": "1.1",
    "autoUpgradeMinorVersion": true,
    "settings": {},
    "protectedSettings": {}
  }
}
```

Puede encontrar Tutorial más detallada sobre el uso de plantillas de administrador de recursos, lea la [información general del Administrador de recursos de Azure](../azure-resource-manager/resource-group-overview.md).


## <a name="next-steps"></a>Pasos siguientes
Puede que desee [Configurar el puerto TCP de Docker daemon](https://docs.docker.com/engine/reference/commandline/dockerd/#/bind-docker-to-another-hostport-or-a-unix-socket), comprender la [seguridad de Docker](https://docs.docker.com/engine/security/security/)o implementar contenedores con [Docker redactar](https://docs.docker.com/compose/overview/). Para obtener más información sobre la extensión de máquina virtual de Azure Docker propio, vea el [proyecto GitHub](https://github.com/Azure/azure-docker-extension/).

Obtenga más información sobre las opciones de implementación de Docker adicionales en Azure:

- [Usar Docker equipo con el controlador de Azure](./virtual-machines-linux-docker-machine.md)  
- [Introducción a Docker y redactar definir y ejecutar una aplicación de contenedor múltiples en una máquina virtual Azure](virtual-machines-linux-docker-compose-quickstart.md).
- [Implementar un clúster de servicio del contenedor de Azure](../container-service/container-service-deployment.md)

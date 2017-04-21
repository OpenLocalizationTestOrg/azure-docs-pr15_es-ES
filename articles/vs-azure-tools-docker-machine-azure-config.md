<properties
   pageTitle="Crear hosts Docker en Azure con Machine Docker | Microsoft Azure"
   description="Describe el uso de la máquina Docker crear hosts docker en Azure."
   services="azure-container-service"
   documentationCenter="na"
   authors="mlearned"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="06/08/2016"
   ms.author="mlearned" />

# <a name="create-docker-hosts-in-azure-with-docker-machine"></a>Crear Docker Hosts en Azure con el equipo de Docker

Ejecutando [Docker](https://www.docker.com/) contenedores requiere un host VM ejecutando el daemon docker.
Este tema describe cómo usar el comando [docker automática](https://docs.docker.com/machine/) para crear nuevas máquinas virtuales de Linux, configurado con el daemon Docker, que se ejecuta en Azure. 

**Nota:** 
- *En este artículo depende de la máquina docker versión 0.7.0 o superior*
- *Contenedores de Windows se admitirá a través de máquina docker en un futuro próximo*

## <a name="create-vms-with-docker-machine"></a>Crear máquinas virtuales con Machine Docker

Crear máquinas virtuales de host de docker en Azure con los `docker-machine create` comando utilizando la `azure` controlador. 

El controlador de Azure necesita su identificador de suscripción. Puede utilizar la [CLI de Azure](xplat-cli-install.md) o el [Portal de Azure](https://portal.azure.com) para recuperar su suscripción de Azure. 

**Con el Portal de Azure**
- Seleccione las suscripciones de la página panel de navegación izquierdo y copie a Id. de suscripción.

**Uso de la CLI de Azure**
- Tipo de ```azure account list``` y copie el identificador de la suscripción.

Tipo de `docker-machine create --driver azure` para ver las opciones y sus valores predeterminados.
También puede ver la [documentación del controlador de Azure Docker](https://docs.docker.com/machine/drivers/azure/) para obtener más información. 

El ejemplo siguiente se basa en los valores predeterminados, pero si lo desea abrir puerto 80 en la máquina virtual para el acceso a internet. 

```
docker-machine create -d azure --azure-subscription-id <Your AZURE_SUBSCRIPTION_ID> --azure-open-port 80 mydockerhost
```

## <a name="choose-a-docker-host-with-docker-machine"></a>Elija un host de docker con el equipo de docker
Una vez que tenga una entrada en el equipo de docker para su host, puede establecer el host de forma predeterminada cuando se ejecuta comandos docker.
##<a name="using-powershell"></a>Uso de PowerShell

```powershell
docker-machine env MyDockerHost | Invoke-Expression 
```

##<a name="using-bash"></a>Uso de fiesta

```bash
eval $(docker-machine env MyDockerHost)
```

Ahora se pueden ejecutar comandos docker en el host especificado

```
docker ps
docker info
```

## <a name="run-a-container"></a>Ejecutar un contenedor

Con un host configurado, ahora puede ejecutar un servidor web simple para comprobar si el host se ha configurado correctamente.
Aquí se utilice una imagen de nginx estándar, especifique que debe escuchar en el puerto 80 y que si se reinicia el host de máquina virtual, el contenedor reiniciarán también (`--restart=always`). 

```bash
docker run -d -p 80:80 --restart=always nginx
```

El resultado debería tener un aspecto similar al siguiente:

```
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
efd26ecc9548: Pull complete
a3ed95caeb02: Pull complete
83f52fbfa5f8: Pull complete
fa664caa1402: Pull complete
Digest: sha256:12127e07a75bda1022fbd4ea231f5527a1899aad4679e3940482db3b57383b1d
Status: Downloaded newer image for nginx:latest
25942c35d86fe43c688d0c03ad478f14cc9c16913b0e1c2971cb32eb4d0ab721
```

## <a name="test-the-container"></a>El contenedor de prueba

Examinar los contenedores de ejecución con `docker ps`:

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                         NAMES
d5b78f27b335        nginx               "nginx -g 'daemon off"   5 minutes ago       Up 5 minutes        0.0.0.0:80->80/tcp, 443/tcp   goofy_mahavira
```

Y compruebe el contenedor en ejecución, escriba `docker-machine ip <VM name>` para buscar la dirección IP que escriba en el explorador:

```
PS C:\> docker-machine ip MyDockerHost
191.237.46.90
```

![Ejecución de ngnix de contenedor](./media/vs-azure-tools-docker-machine-azure-config/nginxsuccess.png)

##<a name="summary"></a>Resumen
Con el equipo de docker puede aprovisionar fácilmente hosts docker en Azure para su validaciones de host docker individuales.
Para producción hospedaje de contenedores, consulte el [Servicio de contenedor de Azure](http://aka.ms/AzureContainerService)

Para desarrollar aplicaciones principales de .NET con Visual Studio, vea [Docker Tools para Visual Studio](http://aka.ms/DockerToolsForVS)

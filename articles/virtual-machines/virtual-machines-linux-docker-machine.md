<properties
    pageTitle="Crear hosts Docker en Azure con Machine Docker | Microsoft Azure"
    description="Describe el uso de la máquina Docker crear hosts docker en Azure."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="squillace"
    manager="timlt"
    editor="tysonn"/>

<tags
    ms.service="virtual-machines-linux"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure-services"
    ms.date="07/22/2016"
    ms.author="rasquill"/>

# <a name="use-docker-machine-with-the-azure-driver"></a>Usar Docker equipo con el controlador de Azure

[Docker](https://www.docker.com/) es una de las soluciones de virtualización más populares que usa Linux contenedores en lugar de máquinas virtuales como una manera de aislar los datos de la aplicación y la informática en recursos compartidos. Este tema describe cuándo y cómo usar [Docker máquina](https://docs.docker.com/machine/) (la `docker-machine` comando) para crear nuevas máquinas virtuales de Linux en Azure habilitado como un host de docker para los contenedores de Linux.


## <a name="create-vms-with-docker-machine"></a>Crear máquinas virtuales con Machine Docker

Crear máquinas virtuales de host de docker en Azure con los `docker-machine create` comando mediante la `azure` argumento controlador para que la opción de controlador (`-d`) y cualquier otro argumento. 

El ejemplo siguiente se basa en los valores predeterminados, pero abra el puerto 80 en la máquina virtual a internet para probar con un contenedor de nginx hace `ops` el usuario de inicio de sesión para SSH y llamadas de la nueva máquina virtual `machine`. 

Tipo de `docker-machine create --driver azure` para ver las opciones y sus valores predeterminados; También puede leer la [documentación del controlador de Azure Docker](https://docs.docker.com/machine/drivers/azure/). (Tenga en cuenta que si tiene habilitada la autenticación de dos factores, se le pedirá para autenticar con el segundo factor).

```bash
docker-machine create -d azure \
  --azure-ssh-user ops \
  --azure-subscription-id <Your AZURE_SUBSCRIPTION_ID> \
  --azure-open-port 80 \
  machine
```

El resultado debería tener un aspecto similar a este, dependiendo de si tiene configurada en su cuenta de autenticación de dos factores.

```
Creating CA: /Users/user/.docker/machine/certs/ca.pem
Creating client certificate: /Users/user/.docker/machine/certs/cert.pem
Running pre-create checks...
(machine) Microsoft Azure: To sign in, use a web browser to open the page https://aka.ms/devicelogin. Enter the code <code> to authenticate.
(machine) Completed machine pre-create checks.
Creating machine...
(machine) Querying existing resource group.  name="machine"
(machine) Creating resource group.  name="machine" location="eastus"
(machine) Configuring availability set.  name="docker-machine"
(machine) Configuring network security group.  name="machine-firewall" location="eastus"
(machine) Querying if virtual network already exists.  name="docker-machine-vnet" location="eastus"
(machine) Configuring subnet.  name="docker-machine" vnet="docker-machine-vnet" cidr="192.168.0.0/16"
(machine) Creating public IP address.  name="machine-ip" static=false
(machine) Creating network interface.  name="machine-nic"
(machine) Creating storage account.  name="vhdsolksdjalkjlmgyg6" location="eastus"
(machine) Creating virtual machine.  name="machine" location="eastus" size="Standard_A2" username="ops" osImage="canonical:UbuntuServer:15.10:latest"
Waiting for machine to be running, this may take a few minutes...
Detecting operating system of created instance...
Waiting for SSH to be available...
Detecting the provisioner...
Provisioning with ubuntu(systemd)...
Installing Docker...
Copying certs to the local machine directory...
Copying certs to the remote machine...
Setting Docker configuration on the remote daemon...
Checking connection to Docker...
Docker is up and running!
To see how to connect your Docker Client to the Docker Engine running on this virtual machine, run: docker-machine env machine
```

## <a name="configure-your-docker-shell"></a>Configurar el shell docker

Ahora, escriba `docker-machine env <VM name>` para ver lo que debe hacer para configurar la consola de. 

```bash
docker-machine env machine
```

Que imprime la información de entorno, que tiene un aspecto parecido al siguiente. Tenga en cuenta que la dirección IP se ha asignado, que es necesario probar la máquina virtual.

```
export DOCKER_TLS_VERIFY="1"
export DOCKER_HOST="tcp://191.237.46.90:2376"
export DOCKER_CERT_PATH="/Users/rasquill/.docker/machine/machines/machine"
export DOCKER_MACHINE_NAME="machine"
# Run this command to configure your shell:
# eval $(docker-machine env machine)
```

Puede ejecutar el comando configuración sugerida o puede establecer las variables de entorno. 

## <a name="run-a-container"></a>Ejecutar un contenedor

Ahora puede ejecutar un servidor web simple para comprobar que todo funciona correctamente. Aquí se use una imagen estándar nginx, especifique que debe escuchar en el puerto 80 y si la máquina virtual se reinicia el contenedor debe reiniciarán también (`--restart=always`). 

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

Y compruebe el contenedor en ejecución, escriba `docker-machine ip <VM name>` para buscar la dirección IP (si ha olvidado desde el `env` comando):

![Ejecución de ngnix de contenedor](./media/virtual-machines-linux-docker-machine/nginxsuccess.png)

## <a name="next-steps"></a>Pasos siguientes

Si está interesado, puede probar la Azure [Docker VM extensión](virtual-machines-linux-dockerextension.md) hacer la misma operación con CLI Azure o plantillas de administrador de recursos de Azure. 

Para obtener más ejemplos sobre cómo trabajar con Docker, consulte [trabajar con Docker](https://github.com/Microsoft/HealthClinic.biz/wiki/Working-with-Docker) de la conexión de [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 [demostración](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/). Para más tutoriales de la demostración HealthClinic.biz, vea [Tutoriales de herramientas de desarrollador de Azure](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts).


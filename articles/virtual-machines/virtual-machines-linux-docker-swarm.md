<properties
   pageTitle="Empezar a usar docker con conjunto de Azure"
   description="Describe cómo crear un grupo de máquinas virtuales con la extensión de VM Docker y usar conjunto para crear un clúster de Docker."
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="squillace"
   manager="timlt"
   editor="tysonn"
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="01/04/2016"
   ms.author="rasquill"/>

# <a name="how-to-use-docker-with-swarm"></a>Cómo usar docker con conjunto

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Este tema muestra una forma muy sencilla de utilizar [docker](https://www.docker.com/) con [enjambre](https://github.com/docker/swarm) para crear un clúster de conjunto administrados en Azure. Crea cuatro máquinas virtuales de Windows Azure, uno para que actúe como el Administrador de conjunto y tres como parte del clúster de hosts docker. Cuando haya terminado, puede usar el conjunto para ver el clúster y, a continuación, empezar a usar docker en él. Además, las llamadas de Azure CLI en este tema utilizan el modo de administración (asm) de servicio. 

> [AZURE.NOTE] Este tema usa docker con conjunto y la CLI de Azure *sin* usar **docker equipo** con el fin de mostrar el funcionamiento conjunto de las distintas herramientas pero permanecen independientes. **máquina docker** tiene **--enjambre** modificadores que le permite usar **docker máquina** directamente agregar nodos a un conjunto. Para obtener un ejemplo, consulte la documentación de [máquina docker](https://github.com/docker/machine) . Si perdió **máquina docker** ejecutar en máquinas virtuales de Azure, consulte [cómo usar docker máquina con Azure](virtual-machines-linux-docker-machine.md).

## <a name="create-docker-hosts-with-azure-virtual-machines"></a>Crear hosts docker con Azure máquinas virtuales de Windows

En este tema crea cuatro VM, pero puede usar cualquier número que desee. Llamar a la siguiente con * &lt;contraseña&gt; * reemplazada por la contraseña que ha elegido.

    azure vm docker create swarm-master -l "East US" -e 22 $imagename ops <password>
    azure vm docker create swarm-node-1 -l "East US" -e 22 $imagename ops <password>
    azure vm docker create swarm-node-2 -l "East US" -e 22 $imagename ops <password>
    azure vm docker create swarm-node-3 -l "East US" -e 22 $imagename ops <password>

Cuando haya finalizado debería poder usar **azure vm lista** para ver sus máquinas virtuales de Azure:

    $ azure vm list | grep "swarm-[mn]"
    data:    swarm-master     ReadyRole           East US       swarm-master.cloudapp.net                               100.78.186.65
    data:    swarm-node-1     ReadyRole           East US       swarm-node-1.cloudapp.net                               100.66.72.126
    data:    swarm-node-2     ReadyRole           East US       swarm-node-2.cloudapp.net                               100.72.18.47  
    data:    swarm-node-3     ReadyRole           East US       swarm-node-3.cloudapp.net                               100.78.24.68  

## <a name="installing-swarm-on-the-swarm-master-vm"></a>Instalar el conjunto en el patrón de conjunto VM

Este tema usa el [modelo de contenedor de instalación desde el docker enjambre documentación](https://github.com/docker/swarm#1---docker-image) , pero también podría SSH en el **patrón de conjunto**. En este modelo, **enjambre** se descargará como un contenedor de docker ejecutando conjunto. A continuación, realice este paso *remotamente desde nuestro equipo portátil usando docker* para conectar con el **patrón de conjunto** VM e indicarle que utilice el comando de creación de id de clúster **Crear conjunto**. El identificador de clúster es cómo **enjambre** descubre a los miembros del grupo conjunto. (También puede clonar el repositorio y generar usted mismo, lo que le ofrecen control total y habilitar la depuración.)

    $ docker --tls -H tcp://swarm-master.cloudapp.net:2376 run --rm swarm create
    Unable to find image 'swarm:latest' locally
    511136ea3c5a: Pull complete
    a8bbe4db330c: Pull complete
    9dfb95669acc: Pull complete
    0b3950daf974: Pull complete
    633f3d9a9685: Pull complete
    bba5f98a0414: Pull complete
    defbc1ab4462: Pull complete
    92d78d321ff2: Pull complete
    swarm:latest: The image you are pulling has been verified. Important: image verification is a tech preview feature and should not be relied on to provide security.
    Status: Downloaded newer image for swarm:latest
    36731c17189fd8f450c395db8437befd

Última línea es el identificador de clúster; copiar en algún lugar porque se utilizará de nuevo cuando se une al nodo máquinas virtuales al conjunto maestro para crear el conjunto de"". En este ejemplo, el identificador de clúster es **36731c17189fd8f450c395db8437befd**.

> [AZURE.NOTE] Para que quede claro, usamos nuestra instalación docker local para conectar con el **patrón de conjunto** VM en Azure e instrucciones **conjunto maestro** para descargar, instalar y ejecutar el comando **crear** , que devuelve nuestra id de clúster que usamos para propósitos de búsqueda más adelante.
<!-- -->
> Para confirmar esto, ejecute `docker -H tcp://` * &lt;hostname&gt; * ` images` para enumerar los procesos de contenedor en el equipo de **patrón de conjunto** y en otro nodo de comparación (dado que nos hemos encontrado el comando conjunto anterior con el modificador **--rm** , se quita el contenedor después de haya terminado, utilizando **docker ps - a** no devuelva nada).:


        $ docker --tls -H tcp://swarm-master.cloudapp.net:2376 images
        REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
        swarm               latest              92d78d321ff2        11 days ago         7.19 MB
        $ docker --tls -H tcp://swarm-node-1.cloudapp.net:2376 images
        REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
        $
<P />
>Si ya está familiarizado con **docker**, sabrá que los demás nodos no tengan entradas porque no hay imágenes descargados y ejecutar todavía.

## <a name="join-the-node-vms-to-our-docker-cluster"></a>Unir el nodo máquinas virtuales a nuestro clúster docker

Para cada nodo de la lista la información del extremo mediante la CLI de Azure. A continuación para ello para el host de docker **nodo de conjunto de 1** para obtener el puerto de docker del nodo.

    $ azure vm endpoint list swarm-node-1
    info:    Executing command vm endpoint list
    + Getting virtual machines
    data:    Name    Protocol  Public Port  Private Port  Virtual IP      EnableDirectServerReturn  Load Balanced
    data:    ------  --------  -----------  ------------  --------------  ------------------------  -------------
    data:    docker  tcp       2376         2376          138.91.112.194  false                     No
    data:    ssh     tcp       22           22            138.91.112.194  false                     No
    info:    vm endpoint list command OK


Con **docker** y la `-H` opción para señalar el cliente de docker en el nodo de VM, unirse a ese nodo en el conjunto que está creando pasando el identificador de clúster y puerto de docker del nodo (el último con **--dirección**):

    $ docker --tls -H tcp://swarm-node-1.cloudapp.net:2376 run -d swarm join --addr=138.91.112.194:2376 token://36731c17189fd8f450c395db8437befd
    Unable to find image 'swarm:latest' locally
    511136ea3c5a: Pull complete
    a8bbe4db330c: Pull complete
    9dfb95669acc: Pull complete
    0b3950daf974: Pull complete
    633f3d9a9685: Pull complete
    bba5f98a0414: Pull complete
    defbc1ab4462: Pull complete
    92d78d321ff2: Pull complete
    swarm:latest: The image you are pulling has been verified. Important: image verification is a tech preview feature and should not be relied on to provide security.
    Status: Downloaded newer image for swarm:latest
    bbf88f61300bf876c6202d4cf886874b363cd7e2899345ac34dc8ab10c7ae924

Que es satisfactorio. Para confirmar que **enjambre** se ejecuta en el **nodo de conjunto de 1** , escriba:

    $ docker --tls -H tcp://swarm-node-1.cloudapp.net:2376 ps -a
        CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS               NAMES
        bbf88f61300b        swarm:latest        "/swarm join --addr=   13 seconds ago      Up 12 seconds       2375/tcp            angry_mclean

Repita para todos los nodos en el clúster. En este caso, para ello para **nodo de conjunto de 2** y **3 de nodo de conjunto**.

## <a name="begin-managing-the-swarm-cluster"></a>Empezar a administrar el clúster de conjunto

    $ docker --tls -H tcp://swarm-master.cloudapp.net:2376 run -d -p 2375:2375 swarm manage token://36731c17189fd8f450c395db8437befd
    d7e87c2c147ade438cb4b663bda0ee20981d4818770958f5d317d6aebdcaedd5

y, a continuación, muestre los nodos en el clúster:

    ralph@local:~$ docker --tls -H tcp://swarm-master.cloudapp.net:2376 run --rm swarm list token://73f8bc512e94195210fad6e9cd58986f
    54.149.104.203:2375
    54.187.164.89:2375
    92.222.76.190:2375

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Pasos siguientes

Vaya a ejecutar cosas en su conjunto. Para buscar ideas, consulte [https://github.com/docker/swarm/](https://github.com/docker/swarm/)o tal vez un [vídeo](https://www.youtube.com/watch?v=EC25ARhZ5bI).

<!-- links -->

[docker-machine-azure]: virtual-machines-linux-docker-machine.md
 

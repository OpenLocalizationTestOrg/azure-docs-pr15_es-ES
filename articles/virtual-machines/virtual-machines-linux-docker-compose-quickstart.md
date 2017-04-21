<properties
   pageTitle="Docker y Redactar en una máquina virtual | Microsoft Azure"
   description="Introducción rápida a trabajar con redactar y Docker en máquinas virtuales de Linux en Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="09/22/2016"
   ms.author="iainfou"/>

# <a name="get-started-with-docker-and-compose-to-define-and-run-a-multi-container-application-on-an-azure-virtual-machine"></a>Introducción a Docker y redactar definir y ejecutar una aplicación de contenedor múltiples en una máquina virtual Azure

Empezar a usar Docker y [Redactar](http://github.com/docker/compose) para definir y ejecutar una aplicación compleja en una máquina virtual de Linux en Azure. Con redactar, utilice un archivo de texto simple para definir una aplicación que consta de varios contenedores de Docker. A continuación, girar la aplicación en un único comando que realiza todo para implementar el entorno definido. 

Por ejemplo, en este artículo muestra cómo configurar rápidamente un blog WordPress con un servidor de base de datos de SQL de MariaDB en una VM Ubuntu. También puede usar redactar para configurar las aplicaciones más complejas.


## <a name="step-1-set-up-a-linux-vm-as-a-docker-host"></a>Paso 1: Configurar una VM Linux como host Docker

Puede usar diversos procedimientos de Azure e imágenes disponibles o el Administrador de recursos plantillas de Azure Marketplace para crear una VM Linux y configurar como host Docker. Por ejemplo, consulte [con la extensión de VM Docker para implementar su entorno](virtual-machines-linux-dockerextension.md) para crear rápidamente una VM Ubuntu con la extensión de Azure Docker VM mediante una [plantilla de tutorial rápido](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). 

Cuando usa la extensión VM Docker, la máquina virtual se configura automáticamente como host Docker y redactar ya está instalado. El ejemplo en este artículo le muestra cómo usar la [interfaz de línea de comandos de Azure para Mac, Linux y Windows](../xplat-cli-install.md) la Azure en modo de administrador de recursos para crear la máquina virtual.

El comando básico desde el documento anterior crea un grupo de recursos denominado `myResourceGroup` en la `West US` ubicación e implementa una máquina virtual con la extensión de Azure Docker VM instalada:

```bash
azure group create --name myResourceGroup --location "West US" \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

## <a name="step-2-verify-that-compose-is-installed"></a>Paso 2: Compruebe que está instalado redactar

Una vez finalizada la implementación, SSH a su nuevo host Docker con el DNS nombre se proporcionó durante la implementación. Puede usar `azure vm show -g myDockerResourceGroup -n myDockerVM` para ver los detalles de la máquina virtual, incluido el nombre DNS.

Para comprobar que redactar está instalado en la máquina virtual, ejecute el siguiente comando:

```bash
docker-compose --version
```

Verá un resultado similar al `docker-compose 1.6.2, build 4d72027`.

>[AZURE.TIP] Si utiliza otro método para crear un host de Docker y necesita instalar redactar usted mismo, consulte la [documentación de redactar](https://github.com/docker/compose/blob/882dc673ce84b0b29cd59b6815cb93f74a6c4134/docs/install.md).


## <a name="step-3-create-a-docker-composeyml-configuration-file"></a>Paso 3: Crear un archivo de configuración de docker compose.yml

A continuación, cree una `docker-compose.yml` archivo, que es simplemente un texto archivo de configuración para definir los contenedores Docker ejecutar en la máquina virtual. El archivo especifica la imagen para que se ejecute en cada contenedor (o podría ser una compilación de un Dockerfile), variables de entorno necesarias y las dependencias, puertos y los vínculos entre contenedores. Para obtener más información sobre la sintaxis de archivo yml, vea [referencia de archivo de redactar](http://docs.docker.com/compose/yml/).

Crear la `docker-compose.yml` archivo como sigue:

```bash
touch docker-compose.yml
```

Use el editor de texto para agregar datos al archivo. En el ejemplo siguiente se utiliza la `vi` editor:

```bash
vi docker-compose.yml
```

Pegue el siguiente ejemplo en el archivo de texto. Esta configuración usa imágenes desde el [Registro de DockerHub](https://registry.hub.docker.com/_/wordpress/) para instalar WordPress (el sistema de administración de contenido y crear blogs Abrir origen) y un base de datos SQL de MariaDB de back-end vinculado. Escriba su propio `MYSQL_ROOT_PASSWORD` como sigue:

```bash
wordpress:
  image: wordpress
  links:
    - db:mysql
  ports:
    - 80:80

db:
  image: mariadb
  environment:
    MYSQL_ROOT_PASSWORD: <your password>
```

## <a name="step-4-start-the-containers-with-compose"></a>Paso 4: Iniciar los contenedores con redactar

En el mismo directorio que su `docker-compose.yml` archivo, ejecute el comando siguiente (dependiendo de su entorno, debe ejecutar `docker-compose` con `sudo`.):

```bash
docker-compose up -d

```

Este comando inicia los contenedores Docker especificados en `docker-compose.yml`. Tarda un minuto o dos para este paso completar. Ver resultado similar al siguiente ejemplo:

```bash
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
...
```

>[AZURE.NOTE] Asegúrese de usar la opción **-d** en el inicio para que los contenedores se ejecuten continuamente en segundo plano.

Para comprobar que los contenedores son hacia arriba, escriba `docker-compose ps`. Verá algo parecido:

```bash
Name             Command             State              Ports
-------------------------------------------------------------------------
wordpress_db_1     /docker-           Up                 3306/tcp
             entrypoint.sh
             mysqld
wordpress_wordpr   /entrypoint.sh     Up                 0.0.0.0:80->80
ess_1              apache2-for ...                       /tcp
```

Ahora puede conectarse a WordPress directamente en la máquina virtual en el puerto 80. Abra un explorador web y escriba el nombre DNS de la máquina virtual (como `http://myresourcegroup.westus.cloudapp.azure.com`). Ahora debe ver la WordPress pantalla de inicio, donde puede completar la instalación y empezar a trabajar con la aplicación.

![Pantalla de inicio de WordPress][wordpress_start]


## <a name="next-steps"></a>Pasos siguientes

* Vaya a la [Guía del usuario de extensión Docker VM](https://github.com/Azure/azure-docker-extension/blob/master/README.md) para ver más opciones configurar Docker y Redactar en la máquina virtual de Docker. Por ejemplo, es una opción para colocar el archivo de yml redactar (que se convierten en JSON) directamente en la configuración de la extensión de VM Docker.
* Consulte la [Guía del usuario](http://docs.docker.com/compose/) para consultar más ejemplos de creación e implementación de aplicaciones de múltiples contenedor y la [referencia de línea de comandos de redactar](http://docs.docker.com/compose/reference/) .
* Usar una plantilla de administrador de recursos de Azure, ya sea su propio o uno contribuido de la [Comunidad](https://azure.microsoft.com/documentation/templates/), para implementar una máquina virtual de Azure con Docker y una aplicación configurado con redactar. Por ejemplo, la plantilla de [implementar un blog WordPress con Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql) usa Docker y redactar implementar rápidamente WordPress con un servidor MySQL en una VM Ubuntu.
* Pruebe la integración Docker redacte con un clúster [Docker enjambre](virtual-machines-linux-docker-swarm.md) . Vea [Utilizando redacte con conjunto](https://docs.docker.com/compose/swarm/) de escenarios.

<!--Image references-->

[wordpress_start]: ./media/virtual-machines-linux-docker-compose-quickstart/WordPress.png

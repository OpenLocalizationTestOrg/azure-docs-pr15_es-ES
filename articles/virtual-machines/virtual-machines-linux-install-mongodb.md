<properties
   pageTitle="Instalar MongoDB en una máquina virtual de Linux | Microsoft Azure"
   description="Obtenga información sobre cómo instalar y configurar MongoDB en una máquina virtual Linux Azure utiliza el modelo de implementación de administrador de recursos."
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
   ms.date="09/29/2016"
   ms.author="iainfou"/>

# <a name="install-and-configure-mongodb-on-a-linux-vm-in-azure"></a>Instalar y configurar MongoDB en una máquina virtual de Linux en Azure
[MongoDB](http://www.mongodb.org) es una base de datos de NoSQL popular de código abierto y de alto rendimiento. Este artículo le muestra cómo instalar y configurar MongoDB en una VM Linux en Azure utiliza el modelo de implementación de administrador de recursos. Se muestran algunos ejemplos que detallan cómo para:

- [Instalar y configurar una instancia de MongoDB básica manualmente](#manually-install-and-configure-mongodb-on-a-vm)
- [Crear una instancia de MongoDB básica con una plantilla de administrador de recursos](#create-basic-mongodb-instance-on-centos-using-a-template)
- [Crear un clúster sharded con la réplica establece con una plantilla de administrador de recursos de MongoDB compleja](#create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template)


## <a name="prerequisites"></a>Requisitos previos
En este artículo requiere lo siguiente:

- una cuenta de Azure ([obtener una prueba gratuita](https://azure.microsoft.com/pricing/free-trial/)).
- [Azure CLI](../xplat-cli-install.md) iniciado sesión`azure login`
- la CLI de Azure *debe estar* en modo de administrador de recursos de Azure usando`azure config mode arm`


## <a name="manually-install-and-configure-mongodb-on-a-vm"></a>Instalar y configurar MongoDB en una máquina virtual manualmente
MongoDB [proporcionan instrucciones de instalación](https://docs.mongodb.com/manual/administration/install-on-linux/) para distros Linux incluidos sombrero rojo / CentOS, SUSE, Ubuntu y Debian. En el ejemplo siguiente se crea un `CoreOS` VM mediante una clave SSH almacenada en `.ssh/azure_id_rsa.pub`. Responder a las indicaciones para el nombre de la cuenta de almacenamiento, DNS y credenciales de administrador:

```bash
azure vm quick-create --ssh-publickey-file .ssh/azure_id_rsa.pub --image-urn CentOS
```

Inicie sesión en la máquina virtual con la dirección IP pública que se muestra al final del paso de creación de VM anterior:

```bash
ssh ops@40.78.23.145
```

Para agregar los orígenes de instalación para MongoDB, crear una `yum` archivo repositorio como sigue:

```bash
sudo touch /etc/yum.repos.d/mongodb-org-3.2.repo
```

Abra el archivo de repo MongoDB para su edición. Agregue las siguientes líneas:

```bash
[mongodb-org-3.2]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.2/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.2.asc
```

Instalar MongoDB usando `yum` como sigue:

```bash
sudo yum install -y mongodb-org
```

De forma predeterminada, se exige SELinux en imágenes de CentOS que impide el acceso a MongoDB. Instalar herramientas de administración de la directiva y configurar SELinux para permitir MongoDB para que funcione como sigue en su 27017 el puerto TCP predeterminado. 

```bash
sudo yum install -y policycoreutils-python
sudo semanage port -a -t mongod_port_t -p tcp 27017
```

Iniciar el servicio de MongoDB como sigue:

```bash
sudo service mongod start
```

Comprobar la instalación de MongoDB mediante una conexión con el equipo local `mongo` cliente:

```bash
mongo
```

Ahora puede probar la instancia de MongoDB agregando algunos datos y, a continuación, buscar:

```
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```

Si lo desea, configurar MongoDB se inicie automáticamente durante el reinicio del sistema:

```bash
sudo chkconfig mongod on
```


## <a name="create-basic-mongodb-instance-on-centos-using-a-template"></a>Crear instancia de MongoDB básica en CentOS usando una plantilla
Puede crear una instancia de MongoDB básica en una única VM CentOS con la plantilla de Azure tutorial siguiente desde Github. Esta plantilla usa la extensión de Script personalizado para Linux para agregar una `yum` repositorio a su máquina virtual de CentOS recién creado y, a continuación, instalar MongoDB.

- [Instancia de MongoDB básica en CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-on-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json

En el ejemplo siguiente se crea un grupo de recursos con el nombre `myResourceGroup` en la `WestUS` región. Escriba sus propios valores como sigue:

```bash
azure group create --name myResourceGroup --location WestUS \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json
```

> [AZURE.NOTE] CLI Azure vuelve a una solicitud de confirmación en unos segundos de la creación de la implementación, pero la instalación y configuración tarda unos minutos en completarse. Comprobar el estado de la implementación con `azure group deployment show myResourceGroup`, escriba el nombre de su grupo de recursos en consecuencia. Espere hasta que la `ProvisioningState` muestra 'Correcto' antes de intentar SSH para la máquina virtual.

Una vez que la implementación está completa, SSH VM. Obtener la dirección IP de la máquina virtual utilizando la `azure vm show` comando como en el ejemplo siguiente:

```bash
azure vm show --resource-group myResourceGroup --name myVM
```

Cerca del final de la salida, la `Public IP address` se muestra. SSH a su máquina virtual con la dirección IP de su máquina virtual:

```bash
ssh ops@138.91.149.74
```

Comprobar la instalación de MongoDB mediante una conexión con el equipo local `mongo` cliente como sigue:

```bash
mongo
```

Ahora puede probar la instancia agregando algunos datos y búsqueda como sigue:

```
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```


## <a name="create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template"></a>Crear un clúster de Sharded MongoDB complejas en CentOS usando una plantilla
Puede crear un clúster de sharded MongoDB complejo con la plantilla de Azure tutorial siguiente desde Github. Esta plantilla sigue las [prácticas recomendadas de MongoDB clúster sharded](https://docs.mongodb.com/manual/core/sharded-cluster-components/) para proporcionar redundancia y alta disponibilidad. La plantilla crea dos shards, con tres nodos en cada conjunto de réplica. También se crea una réplica de servidor config establecer con tres nodos, más dos `mongos` servidores de enrutador para proporcionar coherencia a las aplicaciones a través de la shards.

- [MongoDB Sharding clúster de CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-sharding-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json

> [AZURE.WARNING] Implementar este clúster de sharded MongoDB compleja requiere más de 20 núcleos, que normalmente es el recuento de núcleo predeterminado por región para una suscripción. Abra una solicitud de soporte técnico de Azure para aumentar el número principal.

En el ejemplo siguiente se crea un grupo de recursos con el nombre `myResourceGroup` en la `WestUS` región. Escriba sus propios valores como sigue:

```bash
azure group create --name myResourceGroup --location WestUS \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json
```

> [AZURE.NOTE] CLI Azure vuelve a una solicitud de confirmación en unos segundos de la creación de la implementación, pero la instalación y configuración pueden tardar más de una hora en completarse. Comprobar el estado de la implementación con `azure group deployment show myResourceGroup`, ajuste el nombre de su grupo de recursos en consecuencia. Espere hasta que la `ProvisioningState` muestra 'Correcto' antes de conectarse a las máquinas virtuales.


## <a name="next-steps"></a>Pasos siguientes
En estos ejemplos, debe conectarse a la instancia de MongoDB localmente desde la máquina virtual. Si desea conectarse a la instancia de MongoDB desde otra red o VM, asegúrese de que el correspondiente [se crean reglas de grupo de seguridad de red](virtual-machines-linux-nsg-quickstart.md).

Para obtener más información sobre la creación de plantillas, vea la [información general del Administrador de recursos de Azure](../azure-resource-manager/resource-group-overview.md).

Las plantillas de administrador de recursos de Azure utilizan la extensión de Script personalizado para descargar y ejecutar secuencias de comandos en sus máquinas virtuales. Para obtener más información, consulte [con la extensión de Script personalizado de Azure con Linux máquinas virtuales de Windows](virtual-machines-linux-extensions-customscript.md).
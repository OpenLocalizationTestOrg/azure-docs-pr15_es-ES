<properties
    pageTitle="Con la nube al inicio para personalizar una VM Linux durante la creación de | Microsoft Azure"
    description="Uso de nube al inicio para personalizar una VM Linux durante la creación."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="vlivech"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"
/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/26/2016"
    ms.author="v-livech"
/>

# <a name="using-cloud-init-to-customize-a-linux-vm-during-creation"></a>Uso de nube al inicio para personalizar una VM Linux durante la creación

Este artículo le muestra cómo hacer que una secuencia de comandos de inicialización de la nube para establecer el nombre de host, paquetes de actualización instalado y administrar cuentas de usuario.  Las secuencias de comandos de la nube al inicio se denominan durante la creación de la máquina virtual de Azure CLI.  El artículo requiere:

- una cuenta de Azure ([obtener una prueba gratuita](https://azure.microsoft.com/pricing/free-trial/)).

- [Azure CLI](../xplat-cli-install.md) iniciado en con `azure login`.

- el modo de administrador de recursos de Azure Azure CLI _debe estar en_ `azure config mode arm`.

## <a name="quick-commands"></a>Comandos rápidos

Crear un script de nube init.txt que establece el nombre de host, actualiza todos los paquetes y a continuación, agrega un usuario sudo Linux.

```bash
#cloud-config
hostname: myVMhostname
apt_upgrade: true
users:
  - name: myNewAdminUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>==myAdminUser@myVM
```
Crear un grupo de recursos para iniciar máquinas virtuales en.

```bash
azure group create myResourceGroup westus
```

Crear una VM Linux con la nube al inicio para configurar durante el inicio.

```bash
azure vm create \
-g myResourceGroup \
-n myVM \
-l westus \
-y Linux \
-f myVMnic \
-F myVNet \
-P 10.0.0.0/22 \
-j mySubnet \
-k 10.0.0.0/24 \
-Q canonical:ubuntuserver:14.04.2-LTS:latest \
-M ~/.ssh/id_rsa.pub \
-u myAdminUser \
-C cloud-init.txt
```

## <a name="detailed-walkthrough"></a>Tutorial detallado

### <a name="introduction"></a>Introducción

Inicie una nueva VM Linux, obtenga un estándar Linux VM con nada personalizada o listo para satisfacer sus necesidades. [Inicialización de nube](https://cloudinit.readthedocs.org) es una forma estándar para insertar una secuencia de comandos u opciones de configuración en esa VM Linux mientras se está iniciando por primera vez.

En Azure, hay tres formas de diferentes realizar cambios en una VM Linux mientras se implementa o iniciar.

- Insertar secuencias de comandos con la nube al inicio.
- Insertar secuencias de comandos con la [VMAccess extensión](virtual-machines-linux-using-vmaccess-extension.md)de Azure.
- Una plantilla de Azure con la nube al inicio.
- Una plantilla de Azure mediante [CustomScriptExtention](virtual-machines-linux-extensions-customscript.md).

Para insertar secuencias de comandos en cualquier momento después del inicio:

- SSH para ejecutar comandos directamente
- Insertar secuencias de comandos con la [VMAccess extensión](virtual-machines-linux-using-vmaccess-extension.md)de Azure, imperativamente o en una plantilla de Azure
- Herramientas de administración de configuración como Ansible, sal, Chef y posición libre.

>[AZURE.NOTE]: VMAccess Extension executes a script as root in the same way using SSH can.  However, using the VM extension enables several features that Azure offers that can be useful depending upon your scenario.

## <a name="cloud-init-availability-on-azure-vm-quick-create-image-aliases"></a>Disponibilidad de nube al inicio en Azure VM crear rápido el alias de imagen:

| Alias     | Publisher | Oferta        | SKU         | Versión | inicialización de nube |
|:----------|:----------|:-------------|:------------|:--------|:-----------|
| CentOS    | OpenLogic | CentOS       | 7.2         | más reciente  | No         |
| CoreOS    | CoreOS    | CoreOS       | Estable      | más reciente  | Sí        |
| Debian    | credativ  | Debian       | 8           | más reciente  | No         |
| openSUSE  | SUSE      | openSUSE     | 13.2        | más reciente  | No         |
| RHEL      | RedHat    | RHEL         | 7.2         | más reciente  | No         |
| UbuntuLTS | Canónico | UbuntuServer | 14.04.4-LTS | más reciente  | Sí        |

Microsoft está trabajando con nuestros socios para obtener la nube al inicio incluido y trabajar en las imágenes que se proporcionan en Azure.

## <a name="adding-a-cloud-init-script-to-the-vm-creation-with-the-azure-cli"></a>Agregar una secuencia de comandos de la nube al inicio para la creación de la máquina virtual con la CLI de Azure

Para iniciar una secuencia de comandos de la nube al inicio cuando se crea una máquina virtual en Azure, especifique el archivo de inicialización de la nube mediante CLI Azure `--custom-data` cambiar.

Crear un grupo de recursos para iniciar máquinas virtuales en.

```bash
azure group create myResourceGroup westus
```

Crear una VM Linux con la nube al inicio para configurar durante el inicio.

```bash
azure vm create \
--resource-group myResourceGroup \
--name myVM \
--location westus \
--os-type Linux \
--nic-name myVMnic \
--vnet-name myVNet \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name mySubnet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/id_rsa.pub \
--admin-username myAdminUser \
--custom-data cloud-init.txt
```

## <a name="creating-a-cloud-init-script-to-set-the-hostname-of-a-linux-vm"></a>Crear un script de inicialización de la nube para establecer el nombre de host de una VM Linux

Uno de los ajustes más importantes y más sencillos para cualquier VM Linux sea el nombre de host. Nos podemos fácilmente se establece con inicialización de nube con esta secuencia de comandos.  

### <a name="example-cloud-init-script-named-cloudconfighostnametxt"></a>Secuencia de comandos de inicialización de nube de ejemplo denominada `cloud_config_hostname.txt`.

``` bash
#cloud-config
hostname: myservername
```

Durante el inicio de la máquina virtual, esta secuencia de comandos de inicialización de nube establece el nombre de host en `myservername`.

```bash
azure vm create \
--resource-group myResourceGroup \
--name myVM \
--location westus \
--os-type Linux \
--nic-name myVMnic \
--vnet-name myVNet \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name mySubNet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/id_rsa.pub \
--admin-username myAdminUser \
--custom-data cloud_config_hostname.txt
```

Inicio de sesión y compruebe que el nombre de host de la máquina virtual de nueva.

```bash
ssh myVM
hostname
myservername
```

## <a name="creating-a-cloud-init-script-to-update-linux"></a>Crear un script de inicialización de la nube para actualizar Linux

Seguridad, se desea el VM Ubuntu actualizar en el primer inicio.  Uso de nube al inicio que podemos hacer con la secuencia de comandos de seguimiento, dependiendo de la distribución de Linux que está utilizando.

### <a name="example-cloud-init-script-cloudconfigaptupgradetxt-for-the-debian-family"></a>Secuencia de comandos de inicialización de nube de ejemplo `cloud_config_apt_upgrade.txt` para la familia Debian

```bash
#cloud-config
apt_upgrade: true
```

Después de que ha iniciado Linux, todos los paquetes instalados se actualizan a través de `apt-get`.

```bash
azure vm create \
--resource-group myResourceGroup \
--name myVM \
--location westus \
--os-type Linux \
--nic-name myVMnic \
--vnet-name myVNet \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name mySubNet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/id_rsa.pub \
--admin-username myAdminUser \
--custom-data cloud_config_apt_upgrade.txt
```

Inicio de sesión y compruebe que se actualizan todos los paquetes.

```bash
ssh myUbuntuVM
sudo apt-get upgrade
Reading package lists... Done
Building dependency tree
Reading state information... Done
Calculating upgrade... Done
The following packages have been kept back:
  linux-generic linux-headers-generic linux-image-generic
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

## <a name="creating-a-cloud-init-script-to-add-a-user-to-linux"></a>Crear un script de nube al inicio para agregar un usuario a Linux

Una de las primeras tareas en cualquier nueva VM Linux es para agregar un usuario para usted o para evitar el uso de `root`. Claves SSH son práctica recomendada para la seguridad y capacidad de uso y se agregan a la `~/.ssh/authorized_keys` archivo con esta secuencia de comandos de inicialización de la nube.

### <a name="example-cloud-init-script-cloudconfigadduserstxt-for-debian-family"></a>Secuencia de comandos de inicialización de nube de ejemplo `cloud_config_add_users.txt` para Debian familia

```bash
#cloud-config
users:
  - name: myCloudInitAddedAdminUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>==myAdminUser@myUbuntuVM
```

Después de que ha iniciado Linux, todos los usuarios de la lista se crean y se agregan al grupo sudo.

```bash
azure vm create \
--resource-group myResourceGroup \
--name myVM \
--location westus \
--os-type Linux \
--nic-name myVMnic \
--vnet-name myVNet \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name mySubNet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/id_rsa.pub \
--admin-username myAdminUser \
--custom-data cloud_config_add_users.txt
```

Inicio de sesión y compruebe que el usuario recién creado.

```bash
ssh myVM
cat /etc/group
```

Salida

```bash
root:x:0:
<snip />
sudo:x:27:myCloudInitAddedAdminUser
<snip />
myCloudInitAddedAdminUser:x:1000:
```

## <a name="next-steps"></a>Pasos siguientes

Inicialización de nube se convierte en una forma estándar para modificar su Linux VM en Inicio. Azure también tiene extensiones de máquina virtual, que permiten modificar su LinuxVM inicio o mientras se está ejecutando. Por ejemplo, puede usar el VMAccessExtension de Azure para restablecer la información de usuario o SSH mientras se está ejecutando la máquina virtual. Con la nube al inicio, es necesario reiniciar el equipo para restablecer la contraseña.

[Sobre las características y extensiones de máquina virtual](virtual-machines-linux-extensions-features.md)

[Administrar usuarios, SSH y comprobar o reparar discos en máquinas virtuales de Linux Azure con la VMAccess extensión](virtual-machines-linux-using-vmaccess-extension.md)

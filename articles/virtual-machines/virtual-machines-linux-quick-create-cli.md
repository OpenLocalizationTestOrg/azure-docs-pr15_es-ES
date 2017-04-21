<properties
   pageTitle="Crear una VM Linux en Azure mediante CLI | Microsoft Azure"
   description="Crear una VM Linux en Azure mediante CLI."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="vlivech"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="10/27/2016"
   ms.author="v-livech"/>


# <a name="create-a-linux-vm-on-azure-by-using-the-cli"></a>Crear una VM Linux en Azure mediante CLI

Este artículo le muestra cómo implementar rápidamente una máquina virtual de Linux (VM) en Azure mediante la `azure vm quick-create` comando en la interfaz de línea de comandos de Azure (CLI). La `quick-create` comando implementa una máquina virtual dentro de una infraestructura básica y segura que puede usar para prototipo o probar un concepto rápidamente. El artículo requiere:

- una cuenta de Azure ([obtener una prueba gratuita](https://azure.microsoft.com/pricing/free-trial/)).

- [Azure CLI](../xplat-cli-install.md) iniciado en con `azure login`.

- el modo de administrador de recursos de Azure Azure CLI _debe estar en_ `azure config mode arm`.

Puede implementar también rápidamente una VM Linux mediante el [portal de Azure](virtual-machines-linux-quick-create-portal.md).

## <a name="quick-commands"></a>Comandos rápidos

En el ejemplo siguiente se muestra cómo implementar una VM CoreOS y adjuntar la clave de Shell seguro (SSH) (los argumentos pueden ser diferentes):

```bash
azure vm quick-create -M ~/.ssh/id_rsa.pub -Q CoreOS
```

## <a name="detailed-walkthrough"></a>Tutorial detallado

El siguiente tutorial tiene una VM UbuntuLTS está implementada, paso a paso, con explicaciones de lo que hace cada paso.

## <a name="vm-quick-create-aliases"></a>Alias de creación rápida VM

Elija una distribución de forma rápida es usar el alias de CLI de Azure asignados a las distribuciones de OS más comunes. La siguiente tabla enumeran los alias (a partir de Azure CLI versión 0.10). Todas las implementaciones que usan `quick-create` predeterminada para máquinas virtuales que la copia de almacenamiento unidad de estado sólido (SSD), que ofrece acceso de disco más rápido aprovisionamiento y de alto rendimiento. (Estos alias representan una pequeña parte de las distribuciones disponibles en Azure. Buscar más imágenes en el catálogo de soluciones de Azure [buscando una imagen en PowerShell](virtual-machines-linux-cli-ps-findimage.md), [en la web](https://azure.microsoft.com/marketplace/virtual-machines/)o [cargue su propia imagen personalizada](virtual-machines-linux-create-upload-generic.md).)

| Alias     | Publisher | Oferta        | SKU         | Versión |
|:----------|:----------|:-------------|:------------|:--------|
| CentOS    | OpenLogic | CentOS       | 7.2         | más reciente  |
| CoreOS    | CoreOS    | CoreOS       | Estable      | más reciente  |
| Debian    | credativ  | Debian       | 8           | más reciente  |
| openSUSE  | SUSE      | openSUSE     | 13.2        | más reciente  |
| RHEL      | Sombrero rojo    | RHEL         | 7.2         | más reciente  |
| UbuntuLTS | Canónico | Servidor de Ubuntu | 14.04.4-LTS | más reciente  |

Las siguientes secciones usar la `UbuntuLTS` alias de la opción **ImageURN** (`-Q`) para implementar un servidor de LTADOS Ubuntu 14.04.4.

El anterior `quick-create` ejemplo resaltadas solo la `-M` marca para identificar la clave pública SSH cargar al deshabilitar las contraseñas SSH, por lo que se le pedirá los siguientes argumentos:

- nombre de grupo de recursos (cualquier cadena es suelen correctamente para el primer grupo de recursos de Azure)
- Nombre de VM
- ubicación (`westus` o `westeurope` predeterminados buena)
- Linux (para comunicar a Azure qué sistema operativo desea)
- nombre de usuario

En el ejemplo siguiente se especifica todos los valores para que preguntar no es necesario. Siempre y cuando tenga una `~/.ssh/id_rsa.pub` como un ssh rsa formato archivo de clave pública, funciona como es:

```bash
azure vm quick-create \
--resource-group myResourceGroup \
--name myVM \
--location westus \
--os-type Linux \
--admin-username myAdminUser \
--ssh-public-file ~/.ssh/id_rsa.pub \
--image-urn UbuntuLTS
```

El resultado debe ser similar al siguiente bloque de resultados:

```bash
info:    Executing command vm quick-create
+ Listing virtual machine sizes available in the location "westus"
+ Looking up the VM "myVM"
info:    Verifying the public key SSH file: /Users/ahmet/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_DS1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account cli16330708391032639673
+ Looking up the NIC "examp-westu-1633070839-nic"
info:    An nic with given name "examp-westu-1633070839-nic" not found, creating a new one
+ Looking up the virtual network "examp-westu-1633070839-vnet"
info:    Preparing to create new virtual network and subnet
/ Creating a new virtual network "examp-westu-1633070839-vnet" [address prefix: "10.0.0.0/16"] with subnet "examp-westu-1633070839-snet" [address prefix: "10.+.1.0/24"]
+ Looking up the virtual network "examp-westu-1633070839-vnet"
+ Looking up the subnet "examp-westu-1633070839-snet" under the virtual network "examp-westu-1633070839-vnet"
info:    Found public ip parameters, trying to setup PublicIP profile
+ Looking up the public ip "examp-westu-1633070839-pip"
info:    PublicIP with given name "examp-westu-1633070839-pip" not found, creating a new one
+ Creating public ip "examp-westu-1633070839-pip"
+ Looking up the public ip "examp-westu-1633070839-pip"
+ Creating NIC "examp-westu-1633070839-nic"
+ Looking up the NIC "examp-westu-1633070839-nic"
+ Looking up the storage account clisto1710997031examplev
+ Creating VM "myVM"
+ Looking up the VM "myVM"
+ Looking up the NIC "examp-westu-1633070839-nic"
+ Looking up the public ip "examp-westu-1633070839-pip"
data:    Id                              :/subscriptions/2<--snip-->d/resourceGroups/exampleResourceGroup/providers/Microsoft.Compute/virtualMachines/exampleVMName
data:    ProvisioningState               :Succeeded
data:    Name                            :exampleVMName
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_DS1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :Canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :14.04.4-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clic7fadb847357e9cf-os-1473374894359
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://cli16330708391032639673.blob.core.windows.net/vhds/clic7fadb847357e9cf-os-1473374894359.vhd
data:
data:    OS Profile:
data:      Computer Name                 :myVM
data:      User Name                     :myAdminUser
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-33-42-FB
data:          Provisioning State        :Succeeded
data:          Name                      :examp-westu-1633070839-nic
data:          Location                  :westus
data:            Public IP address       :138.91.247.29
data:            FQDN                    :examp-westu-1633070839-pip.westus.cloudapp.azure.com
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://clisto1710997031examplev.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm quick-create command OK
```

## <a name="log-in-to-the-new-vm"></a>Inicie sesión en la nueva máquina virtual

Inicie sesión en la máquina virtual usando la dirección IP pública que aparece en el resultado. También puede usar el nombre de dominio completo (FQDN) que aparece:

```bash
ssh -i ~/.ssh/id_rsa.pub ahmet@138.91.247.29
```

El proceso de inicio de sesión debe tener un aspecto similar al siguiente bloque de resultados:

```bash
Warning: Permanently added '138.91.247.29' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 14.04.4 LTS (GNU/Linux 3.19.0-65-generic x86_64)

 * Documentation:  https://help.ubuntu.com/

  System information as of Thu Sep  8 22:50:57 UTC 2016

  System load: 0.63              Memory usage: 2%   Processes:       81
  Usage of /:  39.6% of 1.94GB   Swap usage:   0%   Users logged in: 0

  Graph this data and manage this system at:
    https://landscape.canonical.com/

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.



The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

myAdminUser@myVM:~$
```

## <a name="next-steps"></a>Pasos siguientes

La `azure vm quick-create` comando es la manera de implementar rápidamente una máquina virtual para que pueda iniciar sesión en un shell de fiesta y comenzar a trabajar. Sin embargo, mediante `vm quick-create` no le ofrecen un amplio control ni tampoco le permite crear un entorno más complejo.  Para implementar una VM Linux que personalizado para su infraestructura, siga cualquiera de estos artículos:

- [Usar una plantilla de administrador de recursos de Azure para crear una implementación específica](virtual-machines-linux-cli-deploy-templates.md)
- [Crear su propio entorno personalizado para una VM Linux con los comandos de Azure CLI directamente](virtual-machines-linux-create-cli-complete.md)
- [Crear un SSH protegidos Linux VM en Azure con plantillas](virtual-machines-linux-create-ssh-secured-vm-from-template.md)

También puede [usar la `docker-machine` controlador Azure con diversos comandos para crear rápidamente una VM Linux como host docker](virtual-machines-linux-docker-machine.md).

<properties
    pageTitle="Restablecer el acceso en máquinas virtuales de Linux Azure con la VMAccess extensión | Microsoft Azure"
    description="Restablecer el acceso en máquinas virtuales de Linux Azure con la VMAccess extensión."
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
    ms.date="10/25/2016"
    ms.author="v-livech"
/>

# <a name="manage-users-ssh-and-check-or-repair-disks-on-azure-linux-vms-using-the-vmaccess-extension"></a>Administrar usuarios, SSH y comprobar o reparar discos en máquinas virtuales de Linux Azure con la VMAccess extensión

Este artículo le muestra cómo usar la VMAcesss extensión de Azure para comprobar o reparar un disco, restablecer el acceso de usuarios, administrar cuentas de usuario o restablecer la configuración SSHD en Linux. El artículo requiere:

- una cuenta de Azure ([obtener una prueba gratuita](https://azure.microsoft.com/pricing/free-trial/)).

- [Azure CLI](../xplat-cli-install.md) iniciado en con `azure login`.

- el modo de administrador de recursos de Azure Azure CLI _debe estar en_ `azure config mode arm`.

## <a name="quick-commands"></a>Comandos rápidos

Hay dos maneras de utilizar VMAccess en sus máquinas virtuales de Linux:

- Usar la CLI de Azure y los parámetros requeridos.
- Usar archivos JSON sin formato que VMAccess procesa y actuar en.

La sección de comandos rápidos, vamos a utilizar la CLI Azure `azure vm reset-access` método. En los siguientes ejemplos de comando, reemplace los valores que contienen "ejemplo" con los valores de su entorno.

## <a name="create-a-resource-group-and-linux-vm"></a>Crear un grupo de recursos y Linux VM

```bash
azure group create myResourceGroup westus
```

## <a name="create-a-debian-vm"></a>Crear una máquina virtual Debian

```bash
azure vm quick-create \
-M ~/.ssh/id_rsa.pub \
-u myAdminUser \
-g myResourceGroup \
-l westus \
-y Linux \
-n myVM \
-Q Debian
```

## <a name="reset-root-password"></a>Restablecer la contraseña de raíz

Para restablecer la contraseña de raíz:

```bash
azure vm reset-access \
-g myResourceGroup \
-n myVM \
-u root \
-p myNewPassword
```

## <a name="ssh-key-reset"></a>Restablecer clave de SSH

Para restablecer la clave SSH de un usuario no raíz:

```bash
azure vm reset-access \
-g myResourceGroup \
-n myVM \
-u myAdminUser \
-M ~/.ssh/id_rsa.pub
```

## <a name="create-a-user"></a>Crear un usuario

Para crear un usuario:

```bash
azure vm reset-access \
-g myResourceGroup \
-n myVM \
-u myAdminUser \
-p myAdminUserPassword
```

## <a name="remove-a-user"></a>Quitar un usuario

```bash
azure vm reset-access \
-g myResourceGroup \
-n myVM \
-R myRemovedUser
```

## <a name="reset-sshd"></a>Restablecer SSHD

Para restablecer la configuración SSHD:

```bash
azure vm reset-access \
-g myResourceGroup \
-n myVM
-r
```


## <a name="detailed-walkthrough"></a>Tutorial detallado

### <a name="vmaccess-defined"></a>VMAccess definidos:

El disco en el VM Linux muestra errores. Restablezca la contraseña de raíz algún modo la VM Linux o elimina por accidente su clave privada de SSH. Si esto sucede en los días del centro de datos, necesitará unidad allí y, a continuación, abra KVM para ir de la consola del servidor. Considere la extensión de Azure VMAccess como ese conmutador KVM que le permite acceder a la consola para restablecer el acceso a Linux o realizar el mantenimiento de nivel de disco.

Para obtener un tutorial detallado, vamos a usar la forma larga de VMAccess, que usa los archivos JSON sin formato.  Estos archivos VMAccess JSON también se llama desde plantillas de Azure.

### <a name="using-vmaccess-to-check-or-repair-the-disk-of-a-linux-vm"></a>Usar VMAccess para comprobar o reparar el disco de una VM Linux

Con VMAccess puede hacer un fsck ejecutar en el disco en el VM Linux.  También puede hacer una comprobación del disco y una reparación de disco mediante un VMAccess.

Para comprobar y, a continuación, reparar el disco Utilice esta secuencia de comandos de VMAccess:

`disk_check_repair.json`

```json
{
  "check_disk": "true",
  "repair_disk": "true, user-disk-name"
}
```

Ejecute la secuencia de comandos de VMAccess con:

```bash
azure vm extension set \
myResourceGroup \
myVM \
VMAccessForLinux \
Microsoft.OSTCExtensions * \
--private-config-path disk_check_repair.json
```

### <a name="using-vmaccess-to-reset-user-access-to-linux"></a>Usar VMAccess para restablecer el acceso de usuarios a Linux

Si ha perdido acceso a raíz de la VM Linux, puede iniciar una secuencia de comandos VMAccess para restablecer la contraseña de raíz.

Para restablecer la contraseña de raíz, utilice esta secuencia de comandos de VMAccess:

`reset_root_password.json`

```json
{
  "username":"root",
  "password":"myNewPassword",   
}
```

Ejecute la secuencia de comandos de VMAccess con:

```bash
azure vm extension set \
myResourceGroup \
myVM \
VMAccessForLinux \
Microsoft.OSTCExtensions * \
--private-config-path reset_root_password.json
```

Para restablecer la clave SSH de un usuario no raíz, utilice esta secuencia de comandos de VMAccess:

`reset_ssh_key.json`

```json
{
  "username":"myAdminUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myAdminUser@myVM",   
}
```

Ejecute la secuencia de comandos de VMAccess con:

```bash
azure vm extension set \
myResourceGroup \
myVM \
VMAccessForLinux \
Microsoft.OSTCExtensions * \
--private-config-path reset_ssh_key.json
```

### <a name="using-vmaccess-to-manage-user-accounts-on-linux"></a>Usar VMAccess para administrar cuentas de usuario en Linux

VMAccess es una secuencia de comandos de Python que se pueden usar para administrar los usuarios de su VM Linux sin iniciar sesión y el uso de sudo o la cuenta raíz.

Para crear un usuario, utilice esta secuencia de comandos de VMAccess:

`create_new_user.json`

```json
{
"username":"myNewUser",
"ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myNewUser@myVM",
"password":"myNewUserPassword",
}
```

Ejecute la secuencia de comandos de VMAccess con:

```bash
azure vm extension set \
myResourceGroup \
myVM \
VMAccessForLinux \
Microsoft.OSTCExtensions * \
--private-config-path create_new_user.json
```

Para eliminar un usuario, utilice esta secuencia de comandos de VMAccess:

`remove_user.json`

```json
{
"remove_user":"myDeletedUser",
}
```

Ejecute la secuencia de comandos de VMAccess con:

```bash
azure vm extension set \
myResourceGroup \
myVM \
VMAccessForLinux \
Microsoft.OSTCExtensions * \
--private-config-path remove_user.json
```

### <a name="using-vmaccess-to-reset-the-sshd-configuration"></a>Uso de VMAccess para restablecer la configuración SSHD

Si realiza cambios en la configuración SSHD máquinas virtuales de Linux y cerrar la conexión SSH antes de comprobar los cambios, es podrán que no pueda SSH'ing vuelva.  VMAccess puede utilizarse para restablecer la configuración SSHD a una configuración buena conocida sin iniciar sesión sobre SSH.

Para restablecer la configuración SSHD Utilice esta secuencia de comandos de VMAccess:

`reset_sshd.json`

```json
{
  "reset_ssh": true
}
```

Ejecute la secuencia de comandos de VMAccess con:

```bash
azure vm extension set \
myResourceGroup \
myVM \
VMAccessForLinux \
Microsoft.OSTCExtensions * \
--private-config-path reset_sshd.json
```

## <a name="next-steps"></a>Pasos siguientes

Actualizar Linux mediante extensiones de Azure VMAccess es un método para realizar cambios en una VM en ejecución Linux.  También puede usar herramientas como plantillas de Azure y la nube al inicio para modificar su Linux VM en Inicio.

[Sobre las características y extensiones de máquina virtual](virtual-machines-linux-extensions-features.md)

[Creación de plantillas de administrador de recursos de Azure con extensiones Linux VM](virtual-machines-linux-extensions-authoring-templates.md)

[Uso de nube al inicio para personalizar una VM Linux durante la creación](virtual-machines-linux-using-cloud-init.md)

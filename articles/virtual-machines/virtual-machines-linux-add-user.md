<properties
        pageTitle="Agregar un usuario a una VM Linux en Azure | Microsoft Azure"
        description="Agregar un usuario a una VM Linux en Azure."
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
        ms.date="08/18/2016"
        ms.author="v-livech"
/>

# <a name="add-a-user-to-an-azure-vm"></a>Agregar un usuario a una máquina virtual de Azure

Una de las primeras tareas en cualquier nueva VM Linux es crear un nuevo usuario.  En este artículo, hemos repase la creación de una cuenta de usuario sudo establecer la contraseña, agregar SSH las claves públicas y por último use `visudo` para permitir sudo sin una contraseña.

Requisitos previos: [una cuenta de Azure](https://azure.microsoft.com/pricing/free-trial/), [las claves públicas y privadas de SSH](virtual-machines-linux-mac-create-ssh-keys.md), un grupo de recursos de Azure y Azure CLI instalado y cambiar a modo de administrador de recursos de Azure usando `azure config mode arm`.

## <a name="quick-commands"></a>Comandos rápidos

```bash
# Add a new user on RedHat family distros
sudo useradd -G wheel exampleUser

# Add a new user on Debian family distros
sudo useradd -G sudo exampleUser

# Set a password
sudo passwd exampleUser
Enter new UNIX password:
Retype new UNIX password:
passwd: password updated successfully

# Copy the SSH Public Key to the new user
ssh-copy-id -i ~/.ssh/id_rsa exampleuser@exampleserver

# Change sudoers to allow no password
# Execute visudo as root to edit the /etc/sudoers file
visudo

# On RedHat family distros uncomment this line:
## Same thing without a password
# %wheel        ALL=(ALL)       NOPASSWD: ALL

# to this
## Same thing without a password
%wheel        ALL=(ALL)       NOPASSWD: ALL

# On Debian family distros change this line:
# Allow members of group sudo to execute any command
%sudo   ALL=(ALL:ALL) ALL

# to this
%sudo   ALL=(ALL) NOPASSWD:ALL

# Verify everything
# Verify the SSH keys & User account
bill@slackware$ ssh -i ~/.ssh/id_rsa exampleuser@exampleserver

# Verify sudo access
sudo top
```

## <a name="detailed-walkthrough"></a>Tutorial detallado

### <a name="introduction"></a>Introducción

Una de la tareas más comunes y primera con un nuevo servidor es agregar una cuenta de usuario.  Deben estar deshabilitadas inicios de sesión de raíz y no se debe utilizar la propia cuenta raíz con el servidor Linux, sólo sudo.  Que le da una raíz de usuario privilegios de escalado mediante sudo la forma correcta de administración y el uso de Linux.

Con el comando `useradd` estamos agregando cuentas de usuario VM Linux.  Ejecutar `useradd` modifica `/etc/passwd`, `/etc/shadow`, `/etc/group`, y `/etc/gshadow`.  Agregamos una marca de línea de comandos para la `useradd` comando también agregar el nuevo usuario al grupo sudo correcta en Linux.  Incluso tú `useradd` crea una entrada en `/etc/passwd` no da la nueva cuenta de usuario una contraseña.  Estamos creando una contraseña para el nuevo usuario con la simple inicial `passwd` comando.  El último paso es modificar las reglas de sudo para permitir que el usuario ejecute comandos con privilegios de sudo sin tener que escribir una contraseña para todos los comandos.  Iniciar sesión con la clave privada suponemos esa cuenta de usuario es seguro de malas actores y va a permitir el acceso de sudo sin una contraseña.  

### <a name="adding-a-single-sudo-user-to-an-azure-vm"></a>Agregar un usuario único sudo a una máquina virtual de Azure

Inicie sesión en la máquina virtual de Azure con claves SSH.  Si tiene no configuración pública clave acceso SSH, completado este artículo se primera [Usar autenticación de clave pública con Azure](http://link.to/article).  

La `useradd` comando realiza las siguientes tareas:

- crear una nueva cuenta de usuario
- crear un nuevo grupo de usuarios con el mismo nombre
- Agregar una entrada en blanco para`/etc/passwd`
- Agregar una entrada en blanco para`/etc/gpasswd`

La `-G` indicador de línea de comandos agrega la nueva cuenta de usuario al grupo Linux NOMPROPIO ofreciendo privilegios de escalado de raíz de la nueva cuenta de usuario.

#### <a name="add-the-user"></a>Agregar al usuario

```bash
# On RedHat family distros
sudo useradd -G wheel exampleUser

# On Debian family distros
sudo useradd -G sudo exampleUser
```

#### <a name="set-a-password"></a>Establecer una contraseña

La `useradd` comando crea el usuario y agrega una entrada a ambos `/etc/passwd` y `/etc/gpasswd` pero realmente no se establece la contraseña.  La contraseña se agrega a la entrada mediante el `passwd` comando.

```bash
sudo passwd exampleUser
Enter new UNIX password:
Retype new UNIX password:
passwd: password updated successfully
```

Ahora tenemos un usuario con privilegios de sudo en el servidor.

### <a name="add-your-ssh-public-key-to-the-new-user-account"></a>Agregar la clave pública SSH a la nueva cuenta de usuario

Desde su equipo, use la `ssh-copy-id` comando con la nueva contraseña.

```bash
ssh-copy-id -i ~/.ssh/id_rsa exampleuser@exampleserver
```

### <a name="using-visudo-to-allow-sudo-usage-without-a-password"></a>Usar visudo para permitir el uso de sudo sin una contraseña

Usando `visudo` para editar la `/etc/sudoers` archivo agrega unos niveles de protección contra la modificación incorrecta de este archivo importante.  Al ejecutar `visudo`, el `/etc/sudoers` archivo está bloqueado para asegurarse de que ningún otro usuario puede realizar cambios mientras se está editando actualmente.  La `/etc/sudoers` archivo también se comprueba errores por `visudo` cuando intenta guardar o salir de modo que no se puede guardar un archivo sudoers rotos.

Ya tenemos los usuarios en el grupo predeterminado correcto para el acceso de sudo.  Ahora vamos a habilitar dichos grupos para usar sudo sin contraseña.

```bash
# Execute visudo as root to edit the /etc/sudoers file
visudo

# On RedHat family distros uncomment this line:
## Same thing without a password
# %wheel        ALL=(ALL)       NOPASSWD: ALL

# to this
## Same thing without a password
%wheel        ALL=(ALL)       NOPASSWD: ALL

# On Debian family distros change this line:
# Allow members of group sudo to execute any command
%sudo   ALL=(ALL:ALL) ALL

# to this
%sudo   ALL=(ALL) NOPASSWD:ALL
```

### <a name="verify-the-user-ssh-keys-and-sudo"></a>Compruebe que el usuario, ssh teclas y sudo

```bash
# Verify the SSH keys & User account
ssh -i ~/.ssh/id_rsa exampleuser@exampleserver

# Verify sudo access
sudo top
```

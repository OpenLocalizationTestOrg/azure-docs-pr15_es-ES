<properties
        pageTitle="Restablecer la contraseña de Linux VM y clave de SSH CLI | Microsoft Azure"
        description="Cómo usar la extensión VMAccess de la interfaz de línea de comandos de Azure (CLI) para restablecer una contraseña de Linux VM o clave SSH, corrija la configuración de SSH y comprobar la coherencia de disco"
        services="virtual-machines-linux"
        documentationCenter=""
        authors="cynthn"
        manager="timlt"
        editor=""
        tags="azure-service-management"/>

<tags
        ms.service="virtual-machines-linux"
        ms.workload="infrastructure-services"
        ms.tgt_pltfrm="vm-linux"
        ms.devlang="na"
        ms.topic="article"
        ms.date="06/14/2016"
        ms.author="cynthn"/>

# <a name="how-to-reset-a-linux-vm-password-or-ssh-key-fix-the-ssh-configuration-and-check-disk-consistency-using-the-vmaccess-extension"></a>Cómo restablecer una contraseña de Linux VM o clave SSH, corrija la configuración de SSH y comprobar la coherencia de disco con la extensión VMAccess


Si no puede conectarse a una máquina virtual de Linux en Azure debido a una contraseña olvidada, una clave de Shell seguro (SSH) incorrecta, o un problema con la configuración de SSH, use la extensión VMAccessForLinux con Azure CLI para restablecer la contraseña o clave SSH, corrija la configuración de SSH y comprobar coherencia de disco. 

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Obtenga información sobre cómo llevar a [cabo estos pasos con el modelo de administrador de recursos](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess).

Con la CLI Azure, use el comando **set azure vm extensión** desde la interfaz de línea de comandos (fiesta, Terminal, símbolo del sistema) a los comandos de access. Ejecutar **Ayuda azure vm extensión establecer** para el uso de la extensión detallada.

Con la CLI Azure, puede realizar las siguientes tareas:

+ [Restablecer la contraseña](#pwresetcli)
+ [Restablecer la clave SSH](#sshkeyresetcli)
+ [Restablecer la contraseña y la clave SSH](#resetbothcli)
+ [Crear una nueva cuenta de usuario de sudo](#createnewsudocli)
+ [Restablecer la configuración de SSH](#sshconfigresetcli)
+ [Eliminar un usuario](#deletecli)
+ [Mostrar el estado de la extensión VMAccess](#statuscli)
+ [Comprobar coherencia de discos agregados](#checkdisk)
+ [Reparar discos agregados en el VM Linux](#repairdisk)


## <a name="prerequisites"></a>Requisitos previos

Debe hacer lo siguiente:

- Debe [instalar CLI Azure](../xplat-cli-install.md) y [conectarse a su suscripción](../xplat-cli-connect.md) a utilizar recursos Azure asociados a su cuenta.
- Establecer el modo correcto para el modelo de implementación clásica, escriba lo siguiente en el símbolo del sistema:
        
        azure config mode asm
        
- Tiene una nueva contraseña o el conjunto de claves SSH, si desea restablecer cualquiera de ellas. No necesita estos si desea restablecer la configuración de SSH.


## <a name="pwresetcli"></a>Restablecer la contraseña

1. Crear un archivo llamado PrivateConf.json con estas líneas. Reemplazar los corchetes y la & #60; marcador de posición & #62; valores con su propia información.

        {
        "username":"<currentusername>",
        "password":"<newpassword>",
        "expiration":"<2016-01-01>"
        }

2. Ejecutar este comando, sustituir el nombre de la máquina virtual para & #60; nombre de vm & #62;.

        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* –-private-config-path PrivateConf.json

## <a name="sshkeyresetcli"></a>Restablecer la clave SSH

1. Crear un archivo llamado PrivateConf.json con este contenido. Reemplazar los corchetes y la & #60; marcador de posición & #62; valores con su propia información.

        {
        "username":"<currentusername>",
        "ssh_key":"<contentofsshkey>"
        }

2. Ejecutar este comando, sustituir el nombre de la máquina virtual para & #60; nombre de vm & #62;.

        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

## <a name="resetbothcli"></a>Restablecer la contraseña y la clave SSH

1. Crear un archivo llamado PrivateConf.json con este contenido. Reemplazar los corchetes y la & #60; marcador de posición & #62; valores con su propia información.

        {
        "username":"<currentusername>",
        "ssh_key":"<contentofsshkey>",
        "password":"<newpassword>"
        }

2. Ejecutar este comando, sustituir el nombre de la máquina virtual para & #60; nombre de vm & #62;.

        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

## <a name="createnewsudocli"></a>Crear una nueva cuenta de usuario de sudo

Si ha olvidado su nombre de usuario, puede usar VMAccess para crear uno nuevo con la autoridad de sudo. En este caso, no se modificará el nombre de usuario y la contraseña existente.

Para crear un nuevo usuario sudo con acceso con contraseña, utilice la secuencia de comandos en [Restablecer la contraseña](#pwresetcli) y especifique el nuevo nombre de usuario.

Para crear un nuevo usuario sudo con acceso a la clave SSH, use la secuencia de comandos en [Restablecer la clave SSH](#sshkeyresetcli) y especifique el nuevo nombre de usuario.

También puede usar [Restablecer la contraseña y la clave SSH](#resetbothcli) para crear un nuevo usuario con acceso a la clave SSH y la contraseña.

## <a name="sshconfigresetcli"></a>Restablecer la configuración de SSH

Si la configuración de SSH está en un estado no deseado, también puede perder el acceso a la máquina virtual. Puede usar la extensión VMAccess para restablecer la configuración a su estado predeterminado. Para ello, simplemente debe establecer la clave "reset_ssh" a "True". La extensión de reiniciar el servidor SSH, abra el puerto SSH en la máquina virtual y restablecer la configuración de SSH valores predeterminados. No se cambiará la cuenta de usuario (nombre, contraseña o SSH teclas).

> [AZURE.NOTE] El archivo de configuración de SSH obtiene restablecer se encuentra en /etc/ssh/sshd_config.

1. Crear un archivo llamado PrivateConf.json con este contenido.

        {
        "reset_ssh":"True"
        }

2. Ejecutar este comando, sustituir el nombre de la máquina virtual para & #60; nombre de vm & #62;. 

        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

## <a name="deletecli"></a>Eliminar un usuario

Si va a eliminar una cuenta de usuario sin iniciar sesión en la máquina virtual directamente, puede usar esta secuencia de comandos.

1. Crear un archivo llamado PrivateConf.json con este contenido, sustituir el nombre de usuario para eliminar para & #60; usernametoremove & #62;. 

        {
        "remove_user":"<usernametoremove>"
        }

2. Ejecutar este comando, sustituir el nombre de la máquina virtual para & #60; nombre de vm & #62;. 

        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

## <a name="statuscli"></a>Mostrar el estado de la extensión VMAccess

Para mostrar el estado de la extensión VMAccess, ejecutar este comando.

        azure vm extension get

## <a name="a-namecheckdiskacheck-consistency-of-added-disks"></a>< un name = 'checkdisk' <</a>comprobar coherencia de discos agregados

Para ejecutar fsck en todos los discos de la máquina virtual de Linux, debe hacer lo siguiente:

1. Crear un archivo llamado PublicConf.json con este contenido. Compruebe el disco tiene un valor boolean si comprobar los discos conectados a su equipo virtual o no. 

        {   
        "check_disk": "true"
        }

2. Ejecutar este comando para ejecutar, sustituir el nombre de la máquina virtual para & #60; nombre de vm & #62;.

        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --public-config-path PublicConf.json 

## <a name='repairdisk'></a>Reparar discos 

Para reparar discos que no son de montaje o que tienen errores de configuración de montaje, use la extensión VMAccess para restablecer la configuración de montaje en la máquina virtual de Linux. Sustituir el nombre de su disco para & #60; yourdisk & #62;.

1. Crear un archivo llamado PublicConf.json con este contenido. 

        {
        "repair_disk":"true",
        "disk_name":"<yourdisk>"
        }

2. Ejecutar este comando para ejecutar, sustituir el nombre de la máquina virtual para & #60; nombre de vm & #62;.

        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --public-config-path PublicConf.json



## <a name="next-steps"></a>Pasos siguientes

* Si desea usar cmdlets de PowerShell de Azure o administrador de recursos de Azure plantillas para restablecer la contraseña o clave SSH, corrija la configuración de SSH y comprobar la coherencia de disco, consulte la [documentación de extensión VMAccess en GitHub](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess). 

* También puede usar el [portal de Azure](https://portal.azure.com) para restablecer la contraseña o clave SSH de una VM Linux implementada en el modelo de implementación clásico. Actualmente no puede usar el portal hacer este para una VM Linux implementada en el modelo de implementación de administrador de recursos.

* Para obtener más información sobre el uso de extensiones VM para máquinas virtuales de Windows Azure, consulte [acerca de las características y extensiones de máquina virtual](virtual-machines-linux-extensions-features.md) .

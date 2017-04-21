<properties 
    pageTitle="Usar privilegios raíz en máquinas virtuales de Linux | Microsoft Azure" 
    description="Obtenga información sobre cómo usar privilegios raíz en una máquina virtual de Linux en Azure." 
    services="virtual-machines-linux" 
    documentationCenter="" 
    authors="szarkos" 
    manager="timlt" 
    editor=""
    tags="azure-service-management,azure-resource-manager" />

<tags 
    ms.service="virtual-machines-linux" 
    ms.workload="infrastructure-services" 
    ms.tgt_pltfrm="vm-linux" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/17/2016" 
    ms.author="szark"/>


# <a name="using-root-privileges-on-linux-virtual-machines-in-azure"></a>Con privilegios de raíz en máquinas virtuales de Linux en Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

De forma predeterminada, la `root` usuario está deshabilitado en máquinas virtuales de Linux en Azure. Los usuarios pueden ejecutar comandos con privilegios elevados mediante el uso de la `sudo` comando. Sin embargo, la experiencia puede variar según cómo se ha configurado el sistema.

1. **Clave SSH y contraseña o solo** - la máquina virtual aprovisionada con un certificado (`.CER` archivo) o SSH clave, así como una contraseña, o simplemente un nombre de usuario y contraseña. En este caso `sudo` pedirá la contraseña del usuario antes de ejecutar el comando.

2. **Sólo clave SSH** : la máquina virtual aprovisionada con un certificado (`.cer`, `.pem`, o `.pub` archivo) o SSH clave, pero no hay ninguna contraseña.  En este caso `sudo` **no puede** solicitar la contraseña del usuario antes de ejecutar el comando.


## <a name="ssh-key-and-password-or-password-only"></a>SSH clave y la contraseña o la contraseña únicamente.

Inicie sesión en la máquina virtual de Linux mediante la autenticación de contraseña o clave SSH, a continuación, ejecute los comandos mediante `sudo`, por ejemplo:

    # sudo <command>
    [sudo] password for azureuser:

En este caso, el usuario le pedirá una contraseña. Después de escribir la contraseña `sudo` se ejecute el comando con `root` privilegios.

También puede habilitar sudo passwordless modificando la `/etc/sudoers.d/waagent` de archivo, por ejemplo:

    #/etc/sudoers.d/waagent
    azureuser ALL = (ALL) NOPASSWD: ALL

Este cambio permitirá passwordless sudo por el usuario "azureuser".

## <a name="ssh-key-only"></a>SSH clave solo

Inicie sesión en la máquina virtual de Linux mediante la autenticación de clave SSH, a continuación, ejecute los comandos mediante `sudo`, por ejemplo:

    # sudo <command>

En este caso el usuario tendrá **no** se le pide una contraseña. Después de presionar `<enter>`, `sudo` se ejecute el comando con `root` privilegios.

 

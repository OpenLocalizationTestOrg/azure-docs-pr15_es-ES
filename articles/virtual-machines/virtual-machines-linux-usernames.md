<properties 
    pageTitle="Seleccionar los nombres de usuario de Linux | Microsoft Azure" 
    description="Obtenga información sobre cómo seleccionar los nombres de usuario para una máquina virtual Linux en Azure." 
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



#<a name="selecting-user-names-for-linux-on-azure"></a>Seleccionar los nombres de usuario de Linux en Azure#

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Cuando se aprovisiona una máquina virtual de Linux en Azure debe especificar el nombre de usuario no raíz que más tarde puede utilizar para iniciar sesión en la máquina virtual. Puede elegir el nombre del nuevo usuario, o si aprovisionamiento a través del portal clásico Azure puede aceptar el nombre predeterminado "azureuser".

En la mayoría de los casos este usuario no existe en la imagen de base y se creará durante el proceso de aprovisionamiento. Si el usuario no existe en la imagen VM base, a continuación, el agente de Azure Linux simplemente configura la contraseña o clave SSH para ese usuario según la información que especificó al crear la máquina virtual.

**Sin embargo**, Linux define un conjunto de nombres de usuario que no se debe usar. El proceso de aprovisionamiento mostrará **un error** si intenta aprovisionar una VM Linux con un usuario de sistema existente, que se define como un usuario con UID 0-99. Un ejemplo típico es el `root` usuario, que tiene UID 0.

 - Vea también: [rangos de Base Linux estándar - ID de usuario](http://refspecs.linuxfoundation.org/LSB_4.1.0/LSB-Core-generic/LSB-Core-generic/uidrange.html)

A continuación se muestra una lista de usuarios de sistema integrada comunes para CentOS y Ubuntu que debe evitar el uso de al hacer el aprovisionamiento de una máquina virtual de Linux en Azure. Esta lista es simplemente un ejemplo, consulte la documentación de la distribución para asegurarse de que el nombre de usuario que elija no entran en conflicto con un usuario existente del sistema.


## <a name="centos"></a>CentOS
- abrt
- ADM
- audio
- Papelera
- CD-ROM
- cgred
- daemon
- dbus
- acceso telefónico
- DIP
- disco
- disquete
- FTP
- FTP
- juegos
- Gopher
- haldaemon
- detener
- kmem
- bloquear
- LP
- correo
- hombre
- memoria
- nfsnobody
- nadie
- NTP
- operador
- oprofile
- postdrop
- sufijo
- qpidd
- raíz
- RPC
- rpcuser
- saslauth
- Cierre
- slocate
- sshd
- stapdev
- stapusr
- sincronización
- sistema
- cinta
- prueba
- tcpdump
- TTY
- usuarios
- utempter
- utmp
- UUCP
- vcsa
- vídeo
- rueda


## <a name="ubuntu"></a>Ubuntu
- ADM
- Administrador
- audio
- copia de seguridad
- Papelera
- CD-ROM
- crontab
- daemon
- acceso telefónico
- DIP
- disco
- fax
- disquete
- Fusible
- juegos
- gnats
- IRC
- kmem
- horizontal
- libuuid
- lista
- LP
- correo
- hombre
- MessageBus
- mlocate
- netdev
- noticias
- nadie
- nogroup
- operador
- plugdev
- proxy
- raíz
- SASL
- sombra
- src
- SSH
- sshd
- personal
- SUDO
- sincronización
- sistema
- registro del sistema
- cinta
- TTY
- usuarios
- utmp
- UUCP
- vídeo
- voz
- whoopsie
- datos de www

 
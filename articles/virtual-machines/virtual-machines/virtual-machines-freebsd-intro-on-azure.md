<properties
   pageTitle="Introducción a FreeBSD en Azure | Microsoft Azure"
   description="Aprenda a usar máquinas virtuales de FreeBSD en Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="KylieLiang"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="08/27/2016"
   ms.author="kyliel"/>

# <a name="introduction-to-freebsd-on-azure"></a>Introducción a FreeBSD en Azure
Este tema proporciona una descripción general de la ejecución de una máquina virtual de FreeBSD en Azure.

## <a name="overview"></a>Información general
FreeBSD para Microsoft Azure es un sistema operativo avanzado usado para los servidores modernos power, equipos de sobremesa e incrustados plataformas. La imagen de FreeBSD 10.3 proporcionada por Microsoft Corporation y está disponible en Azure. Se basa en la versión 10.3 FreeBSD y agente de Invitado VM Azure [2.1.4](https://github.com/Azure/WALinuxAgent/releases/tag/v2.1.4) está instalado. El agente es responsable de la comunicación entre la VM FreeBSD y la estructura de Azure para operaciones, como el aprovisionamiento de la máquina virtual en el primer uso (nombre de usuario, contraseña, nombre de host, etc.) y habilitar la funcionalidad de extensiones VM selectivas.
En cuanto a las futuras versiones de FreeBSD, la estrategia es mantenerse actualizado y disponer las últimas versiones de poco después de que se publiquen por el equipo de ingeniería de lanzamiento de FreeBSD. La próxima versión es [11 de FreeBSD](https://www.freebsd.org/releases/11.0R/schedule.html).

## <a name="deploying-a-freebsd-virtual-machine"></a>Implementar una máquina virtual de FreeBSD
Implementar una máquina virtual de FreeBSD es un proceso sencillo con una imagen de [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/).

## <a name="vm-extensions-for-freebsd"></a>Extensiones VM para FreeBSD
Siguientes son las extensiones VM admitidas en FreeBSD.

### <a name="vmaccess"></a>VMAccess

La extensión [VMAccess](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) puede:

- Restablecer la contraseña del usuario sudo original.
- Crear un nuevo usuario sudo con la contraseña especificada.
- Establecer la clave de host público con la clave dada.
- Restablecer la clave de host público proporcionada durante el aprovisionamiento de VM si no se proporciona la clave de host.
- Abra el puerto SSH (22) y restaurar la sshd_config si reset_ssh se establece en true.
- Eliminar el usuario existente.
- Comprobar discos.
- Reparar un disco agregado.

### <a name="customscript"></a>CustomScript

La extensión [CustomScript](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript) puede:

- Si se proporciona, descargue las secuencias de comandos personalizados de Azure almacenamiento o almacenamiento público externo (por ejemplo, GitHub).
- Ejecute la secuencia de comandos de punto de entrada.
- Compatible con los comandos de en línea.
- Convertir la nueva línea de estilo de Windows en shell y secuencias de comandos de Python automáticamente.
- Quitar marca BOM en shell y secuencias de comandos de Python automáticamente.
- Proteger datos confidenciales en CommandToExecute.

## <a name="authentication-user-names-passwords-and-ssh-keys"></a>Autenticación: nombres de usuario, contraseñas y SSH claves
Cuando está creando una máquina virtual de FreeBSD mediante el portal de Azure, debe proporcionar un nombre de usuario, contraseña o clave pública de SSH.
Nombres de usuario para implementar una máquina virtual de FreeBSD en Azure no deben coincidir con los nombres de las cuentas del sistema (UID < 100) ya están en la máquina virtual (por ejemplo "" raíz,).
Actualmente, solo la RSA clave SSH es compatible. Una clave SSH varias líneas debe comenzar con "---inicial SSH2 clave pública---" y termine con "---final SSH2 clave pública---".

## <a name="obtaining-superuser-privileges"></a>Obtener privilegios de superusuario
La cuenta de usuario que se especifica durante la implementación de la instancia de máquina virtual en Azure es una cuenta con privilegios. Se instaló el paquete de sudo en la imagen de FreeBSD publicada.
Después de haber iniciado sesión a través de esta cuenta de usuario, puede ejecutar comandos como raíz mediante la sintaxis de comando.

    # sudo <COMMAND>

Si lo desea, puede obtener un shell raíz mediante sudo -s.

## <a name="next-steps"></a>Pasos siguientes
- Vaya a [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/) para crear una VM FreeBSD.
- Si desea aportar su propia FreeBSD a Azure, consulte [crear y cargar un disco duro virtual de FreeBSD en Azure](../virtual-machines-linux-classic-freebsd-create-upload-vhd.md).

<properties
   pageTitle="Obtener acceso a ID de máquina virtual"
   description="Describe acceder y utilizar identificador exclusivo de Azure VM"
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="kmouss"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="02/08/2016"
   ms.author="kmouss"/>
   
# <a name="accessing-and-using-azure-vm-unique-id"></a>Acceder y utilizar identificador exclusivo de Azure VM

Identificador exclusivo de Azure VM es un identificador de 128 codificada y almacenada en todos los Azure IaaS VM SMBIOS y actualmente puede leerse con los comandos de BIOS de plataforma.

Identificador exclusivo de Azure VM es una propiedad de solo lectura. Identificador exclusivo de VM Azure no cambiará durante el cierre de reinicio (ya sea planificado para planificado), inicio o parada cancelar la asignación, resolución de problemas del servicio o restaurar en su lugar. Sin embargo, si la máquina virtual es una instantánea y copia para crear una nueva instancia, nuevo ID de máquina virtual de Azure está configurado.

> [AZURE.NOTE] Si tiene más antiguos crean máquinas virtuales y ejecutan desde esta nueva característica obtuvo despliegan las en (18 de septiembre de 2014), por favor, reinicie la máquina virtual para obtener automáticamente una Azure único Id.


Para acceder a Azure VM identificador exclusivo desde dentro de la máquina virtual:


## <a name="create-a-vm"></a>Crear una máquina virtual
 

Para obtener más información, vea [crear una máquina Virtual](virtual-machines-linux-creation-choices.md)


## <a name="connect-to-the-vm"></a>Conectarse a la máquina virtual
 

Para obtener más información, consulte [SSH de Linux](virtual-machines-linux-mac-create-ssh-keys.md)


## <a name="query-vm-unique-id"></a>Identificador exclusivo de máquina virtual de consulta

Comando (en el ejemplo se usa **Ubuntu**):

    sudo dmidecode | grep UUID
    
Ejemplo de resultados esperados:

    UUID: 090556DA-D4FA-764F-A9F1-63614EDA019A
    
Debido a Big Endian bit pedidos, el identificador exclusivo de VM real en este caso será:

    DA 56 05 09 – FA D4 – 4f 76 - A9F1-63614EDA019A
    
    
Identificador exclusivo de Azure VM puede usarse en escenarios diferentes si la máquina virtual se ejecuta en Azure o local y puede ayudar a sus requisitos de seguimiento de licencias, informes o general es posible que tenga en las implementaciones de Azure IaaS. Muchos proveedores de software independientes creación de aplicaciones y que se certifique en Azure pueden necesitar para identificar una máquina virtual de Azure a lo largo de su ciclo de vida y saber si está ejecutando la máquina virtual en Azure, local o en otros proveedores de nube. Este identificador de plataforma por ejemplo puede ayudar a detectar si el software correctamente tiene licencia o ayuda para relacionar datos VM a su origen como para ayudar a sobre cómo configurar la métrica adecuada para la plataforma adecuada para realizar un seguimiento y relacionar estas métricas entre otros usos.

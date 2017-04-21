<properties
    pageTitle="Crear una copia de la máquina virtual Linux de Azure | Microsoft Azure"
    description="Obtenga información sobre cómo crear una copia de la máquina virtual de Azure Linux en el modelo de implementación de administrador de recursos"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/28/2016"
    ms.author="cynthn"/>

# <a name="create-a-copy-of-a-linux-virtual-machine-running-on-azure"></a>Crear una copia de una máquina virtual de Linux ejecuta en Azure


Este artículo le muestra cómo crear una copia de la máquina virtual (VM) Azure ejecutan Linux utiliza el modelo de implementación de administrador de recursos. En primer lugar copie sobre el sistema operativo y discos de datos a un nuevo contenedor, a continuación, configurar los recursos de red y crear la nueva máquina virtual.

También puede [cargar y crear una máquina virtual de la imagen de disco personalizada](virtual-machines-linux-upload-vhd.md).


## <a name="before-you-begin"></a>Antes de empezar

Asegúrese de que cumple los siguientes requisitos previos antes de comenzar con los pasos:

- Tiene [Azure CLI] (.. / xplat-cli-install.md) descargó e instaló en su equipo. 

- Debe también incluye información acerca de la máquina virtual Linux de Azure existente:

| Información de la máquina virtual de origen | Dónde puede conseguirla |
|------------|-----------------|
| Nombre de VM | `azure vm list` |
| Nombre del grupo de recursos | `azure vm list` |
| Ubicación | `azure vm list` |
| Nombre de la cuenta de almacenamiento | `azure storage account list -g <resourceGroup>` |
| Nombre del contenedor | `azure storage container list -a <sourcestorageaccountname>` |
| Nombre de archivo de origen VHD VM | `azure storage blob list --container <containerName>` |



- Debe hacer algunas elecciones sobre la nueva máquina virtual:   <br> : Nombre del contenedor   <br> Nombre de - VM   <br> Tamaño de - VM   <br> nombre de vNet-   <br> : Nombre de subred   <br> Nombre - IP   <br> Nombre - NIC
    

## <a name="login-and-set-your-subscription"></a>Inicio de sesión y configurar la suscripción

1. Inicie sesión en CLI.
        
        azure login

2. Asegúrese de que está en modo de administrador de recursos.
    
        azure config mode arm

3. Establecer la suscripción correcta. Puede usar 'lista cuenta de azure' para ver todas las suscripciones.

        azure account set <SubscriptionId>



## <a name="stop-the-vm"></a>Detener la máquina virtual 

Detener y desasignar la máquina virtual de origen. Puede usar 'vm azure lista' para obtener una lista de todas las máquinas virtuales de la suscripción y sus recursos de nombres de grupo.
    
        azure vm stop <ResourceGroup> <VmName>
        azure vm deallocate <ResourceGroup> <VmName>




## <a name="copy-the-vhd"></a>Copiar el disco duro virtual


Puede copiar el disco duro virtual de almacenamiento de origen al destino mediante la `azure storage blob copy start`. En este ejemplo, vamos a copiar el disco duro virtual en la misma cuenta de almacenamiento, pero un contenedor diferente.

Para copiar el disco duro virtual a otro contenedor de la misma cuenta de almacenamiento, escriba:

        azure storage blob copy start https://<sourceStorageAccountName>.blob.core.windows.net:8080/<sourceContainerName>/<SourceVHDFileName.vhd> <newcontainerName>
        

## <a name="set-up-the-virtual-network-for-your-new-vm"></a>Configurar una red virtual para su nueva máquina virtual

Configurar una red virtual y NIC para la nueva máquina virtual. 

    azure network vnet create <ResourceGroupName> <VnetName> -l <Location>

    azure network vnet subnet create -a <address.prefix.in.CIDR/format> <ResourceGroupName> <VnetName> <SubnetName>

    azure network public-ip create <ResourceGroupName> <IpName> -l <yourLocation>

    azure network nic create <ResourceGroupName> <NicName> -k <SubnetName> -m <VnetName> -p <IpName> -l <Location>


## <a name="create-the-new-vm"></a>Crear la nueva máquina virtual 

Ahora puede crear una máquina virtual desde el disco virtual cargados [mediante una plantilla de administrador de recursos](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd) o a través de la CLI especificando la URI en el disco copiado escribiendo:

```bash
azure vm create -n <newVMName> -l "<location>" -g <resourceGroup> -f <newNicName> -z "<vmSize>" -d https://<storageAccountName>.blob.core.windows.net/<containerName/<fileName.vhd> -y Linux
```



## <a name="next-steps"></a>Pasos siguientes

Para obtener información sobre cómo usar Azure CLI para administrar su nuevo equipo virtual, vea [comandos de Azure CLI para el Administrador de recursos de Azure](azure-cli-arm-commands.md).

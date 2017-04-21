<properties
   pageTitle="Cómo cambiar el tamaño de una VM Linux | Microsoft Azure"
   description="Cómo ampliar o reducir una máquina virtual Linux, cambiando el tamaño de la máquina virtual."
   services="virtual-machines-linux"
   documentationCenter="na"
   authors="mikewasson"
   manager="timlt"
   editor=""
   tags=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/16/2016"
   ms.author="mikewasson"/>


# <a name="how-to-resize-a-linux-vm"></a>Cómo cambiar el tamaño de una VM Linux

## <a name="overview"></a>Información general 

Después de que se aprovisiona una máquina virtual (VM), puede escalar la máquina virtual hacia arriba o hacia abajo al cambiar el [tamaño de la máquina virtual][vm-sizes]. En algunos casos, es necesario desasignar la máquina virtual. Esto puede suceder si el nuevo tamaño no está disponible en el clúster de hardware que hospeda la máquina virtual.

Este artículo le muestra cómo cambiar el tamaño de una VM Linux con [Azure CLI][azure-cli].

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásico.


## <a name="resize-a-linux-vm"></a>Cambiar el tamaño de una máquina virtual de Linux 

Para cambiar el tamaño de una máquina virtual, realice los pasos siguientes.

1. Ejecute el siguiente comando CLI. Este comando muestra los tamaños VM que están disponibles en el clúster de hardware donde se hospeda la máquina virtual.

    ```
    azure vm sizes -g <resource-group> --vm-name <vm-name>
    ```

2. Si el tamaño deseado en la lista, ejecute el comando siguiente para cambiar el tamaño de la máquina virtual.

    ```
    azure vm set -g <resource-group> --vm-size <new-vm-size> -n <vm-name>  
        --enable-boot-diagnostics --boot-diagnostics-storage-uri
        https://<storage-account-name>.blob.core.windows.net/ 
    ```

    La máquina virtual se reinicie durante este proceso. Después de reiniciar, el sistema operativo existente y los discos de datos se volverán a asignar. Nada en el disco temporal se perderán.

    Usar la `--enable-boot-diagnostics` opción habilita los [Diagnósticos de arranque][boot-diagnostics]para registrar errores relacionados con el inicio.

3. En caso contrario, si el tamaño deseado no aparece, ejecute los comandos siguientes para desasignar la máquina virtual, cambiar su tamaño y luego reinicie la máquina virtual.

    ```
    azure vm deallocate -g <resource-group> <vm-name>
    azure vm set -g <resource-group> --vm-size <new-vm-size> -n <vm-name>  
        --enable-boot-diagnostics --boot-diagnostics-storage-uri
        https://<storage-account-name>.blob.core.windows.net/ 
    azure vm start -g <resource-group> <vm-name>
    ```

   > [AZURE.WARNING] Cancelar la asignación de la máquina virtual, también libera las direcciones IP dinámicas asignadas a la máquina virtual. Los discos de sistema operativo y los datos no se ven afectados.
   
## <a name="next-steps"></a>Pasos siguientes

Para obtener más escalabilidad ejecutar varias instancias VM y escalar. Para obtener más información, vea [cambiar automáticamente la escala de equipos Linux en un conjunto de escala de máquina Virtual][scale-set]. 

<!-- links -->
   
[azure-cli]: ../xplat-cli-install.md
[boot-diagnostics]: https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md 
[vm-sizes]: virtual-machines-linux-sizes.md
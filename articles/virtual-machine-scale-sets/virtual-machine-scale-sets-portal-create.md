<properties
    pageTitle="Crear un conjunto de escala de máquina Virtual con el portal de Azure | Microsoft Azure"
    description="Implementar conjuntos de escala con Azure portal."
    keywords="conjuntos de escala de máquina virtual" 
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="gatneil"
    manager="madhana"
    editor="tysonn"
    tags="azure-resource-manager" />

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/15/2016"
    ms.author="gatneil"/>

# <a name="create-a-virtual-machine-scale-set-using-the-azure-portal"></a>Crear un conjunto de escala de máquina Virtual con el portal de Azure

Este tutorial muestra lo fácil que es crear una escala de máquina Virtual configurada en tan solo unos minutos con el portal de Azure. Si no tiene una suscripción de Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="choose-the-vm-image-from-the-marketplace"></a>Elija la imagen de VM desde el catálogo de soluciones

Desde el portal, puede implementar fácilmente una escala de conjunto con CentOS, CoreOS, Debian, Suse abierto, rojo sombrero Enterprise Linux, SUSE Linux Enterprise Server, servidor Ubuntu o imágenes de Windows Server.

En primer lugar, vaya al [portal de Azure](https://portal.azure.com) en un explorador web. Haga clic en `New`, busque `scale set`y, a continuación, seleccione la `Virtual machine scale set` entrada:

![ScaleSetPortalOverview](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalOverview.PNG)

## <a name="create-the-linux-virtual-machine"></a>Crear el equipo virtual Linux

Ahora puede usar la configuración predeterminada y crear rápidamente la máquina virtual.

* En la `Basics` módulo, escriba un nombre para el conjunto de escala. Este nombre se convierte en la base del FQDN del equilibrador de carga delante del conjunto de escala, así que asegúrese de que el nombre es único en todos los de Azure.

* Seleccione tipo de su sistema operativo que desee, escriba el nombre de usuario que desee y seleccione el tipo de autenticación que prefiera. Si elige una contraseña, debe ser al menos 12 caracteres y cumplir tres de estos cuatro requisitos de complejidad: una minúscula, una mayúscula, un número y un carácter especial. Obtenga más información sobre [los requisitos de usuario y contraseña](../virtual-machines/virtual-machines-windows-faq.md#what-are-the-username-requirements-when-creating-a-vm). Si elige `SSH public key`, asegúrese de que solo pegar en su clave pública, no su clave privada:

![ScaleSetPortalBasics](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalBasics.PNG)

* Escriba el nombre del grupo de recursos que desee y una ubicación y, a continuación, haga clic en `OK`.

* En la `Virtual machine scale set service settings` módulo: Introduzca la etiqueta de nombre de dominio que desee (base del FQDN de equilibrador de carga delante del conjunto de escala). Esta etiqueta debe ser única en todos los de Azure.

* Elija la imagen de disco del sistema operativo que desee, el recuento de instancia y el tamaño de la máquina.

* Habilitar o deshabilitar Autoescala y configurar si está habilitado:

![ScaleSetPortalService](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalService.PNG)

* En la `Summary` módulo, cuando se realiza la validación, haga clic en `OK`.

* Por último, en la `Purchase` módulo, haga clic en `Purchase` para iniciar la escala del conjunto de implementación.

## <a name="connect-to-a-vm-in-the-scale-set"></a>Conectarse a una máquina virtual en el conjunto de escala

Una vez que se implementa el conjunto de escala, desplácese hasta la `Inbound NAT Rules` ficha del equilibrador de carga para el conjunto de escala:

![ScaleSetPortalNatRules](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalNatRules.PNG)

Puede conectarse a cada máquina virtual de la escala que establezca con estas reglas NAT. Por ejemplo, para un conjunto de escala de Windows, si hay una regla NAT en puerto entrante 50000, puede conectarse a través de RDP en `<load-balancer-ip-address>:50000`. Para un conjunto de escala de Linux para conectarse con el comando `ssh -p 50000 <username>@<load-balancer-ip-address>`.

## <a name="next-steps"></a>Pasos siguientes

Para obtener documentación sobre cómo implementar conjuntos de escala de CLI, consulte [esta documentación](./virtual-machine-scale-sets-cli-quick-create.md).

Documentación sobre cómo implementar conjuntos de escala de PowerShell, vea [esta documentación](./virtual-machine-scale-sets-windows-create.md).

Para obtener documentación sobre cómo implementar conjuntos de escala de Visual Studio, consulte la [documentación de esta](./virtual-machine-scale-sets-vs-create.md).

Para documentación general, consulte la [página de información general de la documentación de escala de conjuntos](./virtual-machine-scale-sets-overview.md).

Para obtener información general, consulte la [página de inicio principal para los conjuntos de escala](https://azure.microsoft.com/services/virtual-machine-scale-sets/).


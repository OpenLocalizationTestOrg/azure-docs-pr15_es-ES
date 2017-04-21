<properties
    pageTitle="Instalar MySQL en una máquina virtual de OpenSUSE | Microsoft Azure"
    description="Aprenda a instalar MySQL en un equipo OpenSUSE Linux VMirtual en Azure."
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
    ms.date="07/19/2016"
    ms.author="cynthn"/>

# <a name="install-mysql-on-a-virtual-machine-running-opensuse-linux-in-azure"></a>Instalar MySQL en una máquina virtual ejecuta OpenSUSE Linux en Azure

[MySQL] [ MySQL] es una base de datos SQL popular, Abrir origen. En este tutorial se muestra cómo crear una máquina virtual ejecuta OpenSUSE Linux, a continuación, instalar MySQL.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


<br>


## <a name="create-a-virtual-machine-running-opensuse-linux"></a>Crear una máquina virtual ejecuta OpenSUSE Linux

[AZURE.INCLUDE [create-and-configure-opensuse-vm-in-portal](../../includes/create-and-configure-opensuse-vm-in-portal.md)]

## <a name="install-and-run-mysql-on-the-virtual-machine"></a>Instalar y ejecutar MySQL en la máquina virtual

[AZURE.INCLUDE [install-and-run-mysql-on-opensuse-vm](../../includes/install-and-run-mysql-on-opensuse-vm.md)]

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información sobre cómo MySQL, consulte la [Documentación MySQL][MySQLDocs].

[MySQLDocs]: http://dev.mysql.com/doc/index-topic.html
[MySQL]: http://www.mysql.com


<properties
    pageTitle="Instalar MongoDB en una máquina virtual de Windows | Microsoft Azure"
    description="Obtenga información sobre cómo instalar MongoDB en una máquina virtual de Azure creadas con el modelo de implementación clásica que ejecutan Windows Server."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="iainfou"/>

#<a name="install-mongodb-on-a-windows-vm"></a>Instalar MongoDB en una máquina virtual de Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Para instalar y configurar MongoDB utiliza el modelo de implementación de administrador de recursos, consulte [este artículo](virtual-machines-windows-classic-install-mongodb.md).

[MongoDB] [ MongoDB] es una base de datos de NoSQL popular de código abierto y de alto rendimiento. En este artículo le guiará para crear una máquina virtual de Windows Server (VM) con el [portal clásica Azure][AzurePortal]. A continuación, crear y adjuntar un disco de datos a la máquina virtual antes de instalar y configurar MongoDB. Si tiene una máquina virtual existente en Azure que desea usar, puede saltar directamente a [instalar y configurar MongoDB](#install-and-run-mongodb-on-the-virtual-machine).


## <a name="create-a-virtual-machine-running-windows-server"></a>Crear una máquina virtual ejecuta Windows Server

Siga estas instrucciones para crear una máquina virtual.

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

> [AZURE.NOTE] Puede agregar un extremo de MongoDB al crear la máquina virtual y configúrelo como sigue: nombre como **Mongo**, utilice **TCP** como protocolo y configurar los puertos públicos y privados a **27017**.

## <a name="attach-a-data-disk"></a>Adjuntar un disco de datos
Para proporcionar almacenamiento para la máquina virtual, adjuntar un disco de datos e inicializarlo para que Windows pueda usarlo. Si ya tiene un disco de datos, puede adjuntar ese disco existente, o puede adjuntar un disco vacío.

[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

Para obtener instrucciones sobre la inicialización del disco, consulte "Cómo: inicializar un nuevo disco de datos en Windows Server" en [cómo adjuntar un disco de datos a una máquina virtual de Windows](virtual-machines-windows-classic-attach-disk.md).

## <a name="install-and-run-mongodb-on-the-virtual-machine"></a>Instalar y ejecutar MongoDB en la máquina virtual

[AZURE.INCLUDE [install-and-run-mongo-on-win2k8-vm](../../includes/install-and-run-mongo-on-win2k8-vm.md)]

## <a name="summary"></a>Resumen
En este tutorial, ha aprendido cómo crear una máquina virtual ejecuta Windows Server, conectarse a él de forma remota y adjuntar un disco de datos.  Además, ha aprendido cómo instalar y configurar MongoDB en la máquina virtual de Windows. Ahora puede acceder MongoDB en la máquina virtual basado en Windows, siguiendo los temas avanzados en la [documentación de MongoDB][MongoDocs].

[MongoDocs]: http://docs.mongodb.org/manual/
[MongoDB]: http://www.mongodb.org/
[AzurePortal]: http://manage.windowsazure.com

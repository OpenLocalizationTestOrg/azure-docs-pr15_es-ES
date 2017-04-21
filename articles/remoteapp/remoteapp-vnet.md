
<properties
    pageTitle="Validar la VNET de Azure para usar con RemoteApp de Azure | Microsoft Azure"
    description="Obtenga información sobre cómo asegurarse de que su VNET de Azure está listo para usar con RemoteApp de Azure"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />



# <a name="validate-the-azure-vnet-to-use-with-azure-remoteapp"></a>Validar la VNET de Azure para usar con RemoteApp de Azure

> [AZURE.IMPORTANT]
> RemoteApp Azure es que se utilizan. Lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) para obtener información detallada.

Antes de utilizar una VNET de Azure con RemoteApp de Azure, desee validar la VNET. Esto ayuda a evitar problemas de conectividad.

Para validar el VNET de Azure, haga lo siguiente:

1. Crear una máquina virtual Azure dentro de la subred de la VNET de Azure que desea usar con RemoteApp de Azure.

2. Conectarse a esa VM mediante el uso de la opción **Conectar** en el portal de administración.
3. Unirse a la máquina virtual para el mismo dominio que desea usar con RemoteApp de Azure. Si va a crear una colección de híbrido en el que se conecta a su red local, unirse a la máquina virtual al dominio local.

Si es correcta, está listo para usar con RemoteApp VNET de Azure.

Para obtener más información sobre el flujo de trabajo de la colección híbrido de llevar a cabo, consulte los artículos siguientes:

- [Cómo planear la red virtual de RemoteApp de Azure](remoteapp-planvnet.md)
- [Crear una colección de híbrido](remoteapp-create-hybrid-deployment.md)
- [Implementar colección RemoteApp de Azure en la red Virtual de Azure (con el soporte técnico para ExpressRoute)](http://blogs.msdn.com/b/rds/archive/2015/04/23/deploy-azure-remoteapp-collection-to-your-azure-virtual-network-with-support-for-expressroute.aspx)

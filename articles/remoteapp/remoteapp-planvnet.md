<properties
    pageTitle="Cómo planear la red virtual para una colección de RemoteApp de Azure | Microsoft Azure"
    description="Obtenga información sobre cómo planear la red virtual para una colección de RemoteApp de Azure."
    services="remoteapp"
    documentationCenter="" 
    authors="mghosh1616"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />

# <a name="how-to-plan-your-virtual-network-for-azure-remoteapp"></a>Cómo planear la red virtual de RemoteApp de Azure

> [AZURE.IMPORTANT]
> RemoteApp Azure es que se utilizan. Lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) para obtener información detallada.

Este documento describe cómo configurar su red virtual Azure (VNET) y la subred de RemoteApp de Azure. Si no está familiarizado con las redes virtuales Azure, esta es una función que le ayuda a virtualización de la infraestructura de red en la nube y crear soluciones híbridas con Azure y los recursos locales. Puede obtener más información acerca de él [aquí](../virtual-network/virtual-networks-overview.md).

Si desea definir directivas de seguridad para el tráfico (entrante y saliente) en su red virtual donde va a implementar RemoteApp de Azure, recomendamos crear una subred independiente para Azure RemoteApp del resto de las instalaciones de la red virtual Azure. Para obtener más información sobre cómo definir directivas de seguridad en su subred virtual Azure, lea [¿Qué es un grupo de seguridad de la red (GSN)?](../virtual-network/virtual-networks-nsg.md).

## <a name="types-of-azure-remoteapp-collections-with-azure-virtual-networks"></a>Tipos de colecciones de Azure RemoteApp con Azure redes virtuales

Los gráficos siguientes muestran las dos opciones de otra colección cuando desee utilizar una red virtual.

### <a name="azure-remoteapp-cloud-collection-with-vnet"></a>Colección de nube de RemoteApp Azure con VNET

 ![Azure RemoteApp - colección de nube con un VNET](./media/remoteapp-planvpn/ra-cloudvpn.png)

Esto representa una colección de Azure RemoteApp donde se implementan todos los recursos que la sesión de RemoteApp hosts necesitan tener acceso a en Azure. Pueden estar en el mismo VNET como la VNET RemoteApp o un VNET diferente en Azure.

### <a name="azure-remoteapp-hybrid-collection-with-vnet"></a>Colección de híbrido RemoteApp Azure con VNET

![Azure RemoteApp - colección híbrido con un VNET](./media/remoteapp-planvpn/ra-hybridvpn.png)

Esto representa una colección de Azure RemoteApp donde algunos de los recursos que necesitan obtener acceso a los hosts de sesión de RemoteApp están implementado en local. La VNET RemoteApp está vinculado a la red local mediante tecnologías de Azure híbrido como VPN o directa de sitio a otro.


## <a name="how-the-system-works"></a>Cómo funciona el sistema

En el interior RemoteApp de Azure implementa Azure máquinas virtuales de Windows (con la imagen cargada) a la subred virtual que seleccionó durante el aprovisionamiento. Si ha optado por una colección de híbridos, intentamos resolver el FQDN del controlador de dominio que escribió en el flujo de trabajo de aprovisionamiento con el servidor DNS que se proporcionan en la red virtual.  
Si se está conectando a una red virtual existente, asegúrese de que exponer los puertos necesarios en los grupos de seguridad de la red de su subred RemoteApp de Azure. 

Le recomendamos que use una [subred lo suficientemente grande para RemoteApp de Azure](remoteapp-vnetsizing.md). Es compatible con una red Virtual de Azure mayor /8 (usando las definiciones de subred CIDR). La subred debería suficientemente grande para acomodar el VM de RemoteApp de Azure durante escalado cuando más usuarios acceden las aplicaciones. 

Siguientes son las cosas que debe habilitar en la subred de red virtual: 

2.  El tráfico saliente de la subred debe permitido en el intervalo de puertos 10101 10175 para comunicarse con uno de los servicios de Azure RemoteApp internos.
3.  El tráfico saliente debe poder desde su subred conectarse al almacenamiento de Azure en el puerto 443
4.  Si dispone de Active Directory hospedado en Azure, asegúrese de que cualquier máquina virtual dentro de la subred de una red virtual para RemoteApp de Azure es capaz de conectar con el controlador de dominio. El DNS de la red virtual debería poder resolver el FQDN de este controlador de dominio.


## <a name="virtual-network-with-forced-tunneling"></a>Red virtual con túnel forzado

Ahora es compatible [forzado túnel](../vpn-gateway/vpn-gateway-about-forced-tunneling.md) para todas las nuevas colecciones de RemoteApp de Azure. Actualmente no se admite la migración de una colección existente para admitir túnel forzado.  Tendrá que eliminar todas las colecciones existentes mediante la VNET que desea vincular a RemoteApp de Azure y cree uno nuevo para obtener forzado habilitado en las colecciones de túnel. 

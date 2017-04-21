
<properties
    pageTitle="Lista de puertos y direcciones URL a la lista blanca para Azure RemoteApp implementado en red virtual de cliente | Microsoft Azure"
    description="Obtenga información sobre qué puertos y direcciones URL tendrá que configurar para la comunicación a través de RemoteApp de Azure."
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
    ms.date="08/16/2016"
    ms.author="elizapo" />



# <a name="list-of-ports-and-urls-to-permit-access-for-azure-remoteapp-deployed-in-customer-virtual-network"></a>Lista de puertos y las direcciones URL para permitir el acceso de Azure RemoteApp implementado en una red Virtual de cliente 

> [AZURE.IMPORTANT]
> RemoteApp Azure es que se utilizan. Lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) para obtener información detallada.

La siguiente aplica a Azure RemoteApp una colección de nube o híbrido si implementa en una red virtual (VNET). Para obtener más información sobre redes virtuales, lea la [Información general de red Virtual](../virtual-network/virtual-networks-overview.md). Si ha creado un grupo de seguridad de la red (GSN) restringir el tráfico a los recursos de una red virtual que ha elegido para RemoteApp de Azure, asegúrese de que los siguientes son accesibles y permitidos a través de las directivas de seguridad de la red virtual. Para obtener más información sobre los grupos de seguridad de red, lea [¿Qué es un grupo de seguridad de red? (GSN)](../virtual-network/virtual-networks-nsg.md).

##  <a name="azure-remoteapp-subnet-needs-access-to-these-endpoints-and-urls"></a>Azure subred RemoteApp necesita obtener acceso a estos extremos y direcciones URL: 
*   *. servicebus.windows.net
*    *. servicebus.net
*    https://*.RemoteApp.windwsazure.com  
*    https://www.RemoteApp.windowsazure.com 
*    https://*RemoteApp.windowsazure.com  
*    https://*.Core.Windows.NET  
*    Saliente: TCP: 443, TCP: 10101 10175 
*    Opcional: UDP: 10201 10275  
 
## <a name="azure-remoteapp-clients-need-access-to-these-endpoints-and-urls"></a>Azure RemoteApp clientes necesitan tener acceso a estos extremos y direcciones URL: 

Los clientes significa los escritorios, etc. que los usuarios usar para conectarse a las aplicaciones implementadas en la colección de Azure RemoteApp de dispositivos.

-  https://Telemetry.RemoteApp.windowsazure.com  
-  https://*.RemoteApp.windowsazure.com (los puertos UDP opcionales son para esta dirección) 
-  https://Login.Windows.NET  
-  https://Login.microsoftonline.com  
-  https://www.RemoteApp.windowsazure.com 
-  https://*.Core.Windows.NET  
-  Saliente: TCP: 443  
-  Opcional: UDP: 3391 
<properties 
   pageTitle="Especificar la configuración de DNS en un archivo de configuración de servicio | Microsoft Azure"
   description="especificar la configuración de DNS personalizada con el archivo de configuración de servicio para una red virtual"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/24/2016"
   ms.author="jdial" />

# <a name="specifying-dns-settings-in-a-service-configuration-file"></a>Especificar la configuración de DNS en un archivo de configuración de servicio

## <a name="dns-elements"></a>Elementos DNS

Un archivo de configuración de servicio puede contener un elemento de DnsServers con una lista de direcciones IPv4 de los servidores de sistema de nombres de dominio (DNS) que usará el servicio. Configuración en el archivo de configuración de servicio tiene prioridad sobre la configuración en el archivo de configuración de red. Para obtener más información, vea [Esquema de configuración de servicio de Azure (.cscfg archivo)](https://msdn.microsoft.com/library/azure/ee758710.aspx).

**Elemento NetworkConfiguration**

      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>

>[AZURE.WARNING] El atributo de **nombre** en el elemento **servidorDNS** se usa solo como un nombre de referencia. No se representa el nombre de host del servidor DNS. Cada valor de atributo **servidorDNS** debe ser único en toda la suscripción de Microsoft Azure.

## <a name="see-also"></a>Vea también

[Esquema de configuración de servicio de Azure (.cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710)

[Esquema de configuración de red Virtual Azure](http://go.microsoft.com/fwlink/?LinkId=248093)

[Configurar una red Virtual mediante archivos de configuración de red](http://go.microsoft.com/fwlink/?LinkId=248094)

[Acerca de la configuración de red Virtual en el Portal de administración](http://go.microsoft.com/fwlink/?LinkId=248092)


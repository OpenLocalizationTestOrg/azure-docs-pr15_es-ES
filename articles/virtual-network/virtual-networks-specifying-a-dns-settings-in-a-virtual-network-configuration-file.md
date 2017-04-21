<properties 
   pageTitle="Especificar la configuración de DNS en un archivo de configuración de red virtual | Microsoft Azure"
   description="Cómo cambiar la configuración del servidor DNS en una red virtual mediante un archivo de configuración de red virtual en el modelo de implementación clásica"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" 
   tags="azure-service-management" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/23/2016"
   ms.author="jdial" /> 


# <a name="specifying-dns-settings-in-a-virtual-network-configuration-file"></a>Especificar la configuración de DNS en un archivo de configuración de red virtual

Un archivo de configuración de red tiene dos elementos que puede usar para especificar la configuración del sistema de nombres de dominio (DNS): **DnsServers** y **DnsServerRef**. Puede agregar una lista de los servidores DNS especificando sus direcciones IP y nombres de referencia para el elemento **DnsServers** . A continuación, puede usar un elemento **DnsServerRef** para especificar qué entradas del servidor DNS desde el elemento de DnsServers se usan para los sitios de red diferente de su red virtual.

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]En este artículo trata sobre el modelo de implementación clásico.

El archivo de configuración de red puede contener los siguientes elementos. El título de cada elemento está vinculado a una página que proporciona información adicional sobre la configuración de valor del elemento.

>[AZURE.IMPORTANT] Para obtener información sobre cómo configurar el archivo de configuración de red, vea [Configurar una red Virtual con un archivo de configuración de red](virtual-networks-using-network-configuration-file.md). Para obtener información acerca de cada elemento contenido en el archivo de configuración de red, vea [Esquema de configuración de red Virtual de Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx).

[Elemento de DNS](http://go.microsoft.com/fwlink/?LinkId=248093)

    <Dns>
      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>
    </Dns>

>[AZURE.WARNING] El atributo de **nombre** en el elemento **servidorDNS** se usa solo como referencia para el elemento **DnsServerRef** . No se representa el nombre de host del servidor DNS. Cada valor de atributo **servidorDNS** debe ser único en toda la suscripción de Microsoft Azure

[Elemento de sitios de una red virtual](http://go.microsoft.com/fwlink/?LinkId=248093)

    <DnsServersRef>
      <DnsServerRef name="ID1" />
      <DnsServerRef name="ID2" />
      <DnsServerRef name="ID3" />
    </DnsServersRef>

>[AZURE.NOTE] Para especificar esta configuración para el elemento de sitios de red Virtual, se deben definirse previamente en el elemento DNS. El *nombre* de DnsServerRef en el elemento de sitios de red Virtual debe hacer referencia a un valor de nombre especificado en el elemento DNS para servidorDNS *nombre*.

## <a name="next-steps"></a>Pasos siguientes

- Comprender el [esquema de configuración de red Virtual Azure](http://go.microsoft.com/fwlink/?LinkId=248093).
- Comprender el [esquema de configuración de servicio de Azure](https://msdn.microsoft.com/library/windowsazure/ee758710).
- [Configurar una red virtual mediante archivos de configuración de red](virtual-networks-using-network-configuration-file.md).

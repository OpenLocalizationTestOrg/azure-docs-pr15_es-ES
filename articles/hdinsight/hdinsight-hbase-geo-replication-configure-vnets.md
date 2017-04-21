<properties 
   pageTitle="Configurar la conexión VPN entre dos redes virtuales | Microsoft Azure" 
   description="Obtenga información sobre cómo configurar las conexiones VPN y resolución de nombres de dominio entre dos redes virtuales Azure y cómo configurar la replicación de geo HBase." 
   services="hdinsight,virtual-network" 
   documentationCenter="" 
   authors="mumian" 
   manager="jhubbard" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="06/28/2016"
   ms.author="jgao"/>

# <a name="configure-a-vpn-connection-between-two-azure-virtual-networks"></a>Configurar una conexión VPN entre dos redes virtuales de Azure  

> [AZURE.SELECTOR]
- [Configurar la conectividad VPN](hdinsight-hbase-geo-replication-configure-vnets.md)
- [Configurar el DNS](hdinsight-hbase-geo-replication-configure-dns.md)
- [Configurar la replicación de HBase](hdinsight-hbase-geo-replication.md) 

Conectividad de red virtual Azure a sitios usa una puerta de enlace VPN para proporcionar un túnel seguro mediante Ipsec/IKE. La VNets puede tener varias suscripciones y de diferentes regiones. Incluso puede combinar VNet VNet comunicación con configuraciones de varios sitios. Existen varios motivos para VNet VNet conectividad:

- Combinación cruzada redundancia de región geográfica y presencia geo 
- Aplicaciones de varios niveles regionales con el límite de aislamiento fuerte 
- Combinación cruzada suscripción, comunicación entre organizaciones en Azure

Para obtener más información, consulte [configurar un VNet a VNet conexión](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md). 

Para verla en vídeo:

> [AZURE.VIDEO configure-the-vpn-connectivity-between-two-azure-virtual-networks]

Este tutorial es parte de la [serie] [ hdinsight-hbase-replication] sobre la creación de replicación geo HBase. 

- Configurar una conexión VPN entre dos redes virtuales (en este tutorial)
- [Configurar el DNS para las redes virtuales][hdinsight-hbase-geo-replication-dns]
- [Configurar la replicación de geo HBase][hdinsight-hbase-geo-replication]

El siguiente diagrama muestra las dos redes virtuales que se creará en este tutorial:

![Diagrama de red virtual de replicación de HDInsight HBase][img-vnet-diagram]
 

##<a name="prerequisites"></a>Requisitos previos
Antes de comenzar este tutorial, debe tener el siguiente:

- **Suscripción de un Azure**. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Una estación de trabajo con PowerShell de Azure**.

    Antes de ejecutar secuencias de comandos de PowerShell, asegúrese de que está conectado a su suscripción de Azure mediante el siguiente cmdlet:

        Add-AzureAccount

    Si tiene varias suscripciones de Azure, use el cmdlet siguiente para establecer la suscripción actual:

        Select-AzureSubscription <AzureSubscriptionName>
        
    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]


>[AZURE.NOTE] Nombres de Azure servicio y máquina virtual deben ser únicos. El nombre utilizado en este tutorial es Contoso-[nombre del servicio de Azure/VM]-[UE / US]. Por ejemplo, Contoso-VNet-UE es la red virtual Azure en el centro de datos de Europa del Norte; Contoso-DNS-US es el servidor DNS VM en el centro de datos de Estados Unidos oriental. Debe dar con sus propios nombres.
 

##<a name="create-two-azure-vnets"></a>Crear dos VNets de Azure



**Para crear una red virtual denominada Contoso-VNet-UE en Europa del Norte**

1.  Inicie sesión en el [Portal de clásica Azure][azure-portal].
2.  Haga clic en **nuevo**, **Servicios de red**, **red VIRTUAL**, **Crear personalizado**.
3.  Escriba:

    - **Nombre**: Contoso-VNet-UE
    - **Ubicación**: Europa del Norte

        Este tutorial usa centros de datos de Europa del Norte y oriental de Estados Unidos. Puede elegir sus propios centros de datos.
4.  Escriba:

    - **Servidor DNS**: (deje en blanco) 
    
        Necesitará su propio servidor DNS para la resolución de nombres en redes virtuales. Para obtener más información sobre cuándo utilizar la resolución de nombre proporcionado Azure y cuándo usar su propio servidor DNS, vea [Resolución de nombres (DNS)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md). Para que obtener instrucciones configurar la resolución de nombres entre VNets, vea [Configurar DNS entre dos redes virtuales Azure][hdinsight-hbase-dns].
  
    - **Configurar una VPN de sitio a punto**: (desactivada)

        Sitio de punto no se aplica a este escenario.

    - **Configurar una VPN de sitio a sitio**: (desactivada)
    
        En el centro de datos de Estados Unidos oriental configurará la conexión VPN de sitio a sitio a la red virtual Azure.
5.  Escriba:

    -   **IP de inicio del espacio de direcciones**: red dividida 10.1.0.0
    -   **CIDR de espacio de direcciones**: / 16
    -   **Inicial de 1 de subred IP**: red dividida 10.1.0.0
    -   **CIDR subred 1**: / 24

    El espacio de direcciones no puede superponerse con la red virtual de Estados Unidos.  

**Para crear una red virtual denominada Contoso-VNet-UE en Europa occidental**

- Repita el último procedimiento con los siguientes valores:

    - **Nombre**: Contoso-VNet-US
    - **Ubicación**: oriental de Estados Unidos
     
    - **Servidor DNS**: (deje en blanco)
    - **Configurar una VPN de sitio a punto**: (desactivada)
    - **Configurar una VPN de sitio a sitio**: (desactivada)
     
    - **IP de inicio del espacio de direcciones**: 10.2.0.0
    - **CIDR de espacio de direcciones**: / 16
    - **Inicial de 1 de subred IP**: 10.2.0.0
    - **CIDR subred 1**: / 24

















##<a name="configure-a-vpn-connection-between-the-two-vnets"></a>Configurar una conexión VPN entre los dos VNets

###<a name="create-local-networks"></a>Crear redes locales

Cuando se crea un VNet VNet configuración, debe configurar cada VNet para identificar los distintos como un sitio de la red local. En esta sección, deberá configurar cada VNet como una red local. Las redes locales compartan los mismos espacios de direcciones IP con la VNet correspondiente.

![Establecer configuración de sitio a sitio VPN de Azure - azure redes locales][img-vnet-lnet-diagram]


**Para crear una red local denominada Contoso-LNet-UE que coincidan con el espacio de direcciones de la red de Contoso-VNet-UE**

1. Desde el Portal de clásico de Azure, haga clic en **nuevo**, **Servicios de red**, **Una red VIRTUAL**, **Agregue la red LOCAL**.
3. Escriba:

    - **Nombre**: Contoso-LNet-UE
    - **Dirección IP del dispositivo VPN**: 192.168.0.1 (esta dirección se actualizará más tarde)

        Normalmente, usaría la dirección IP externa real de un dispositivo VPN. Para VNet VNet configuraciones, usará la dirección IP de puerta de enlace VPN. Dado que no ha creado aún las puertas de enlace VPN para los dos VNets, escriba una dirección IP de arbitary y vuelva a repararlo.
4.  Escriba:

    - **IP de inicio del espacio de direcciones:** red dividida 10.1.0.0
    - **CIDR de espacio de direcciones:** /16
    
    Debe corresponder exactamente al rango especificado anteriormente para Contoso-VNet-UE.

**Para crear una red local denominada Contoso-LNet-US que coincidan con el espacio de direcciones de la red de Contoso-VNet-US**

- Repita el último procedimiento con los parámetros siguientes:

    - **Nombre**: Contoso-LNet-US
    - **Dirección IP del dispositivo VPN**: 192.168.0.1 (esta dirección se actualizará más tarde)
     
    - **IP de inicio del espacio de direcciones**: 10.2.0.0
    - **CIDR de espacio de direcciones**: / 16


###<a name="create-vpn-gateways"></a>Crear puertas de enlace VPN

Existen dos partes en esta configuración. Primero configura una conexión de sitio a sitio VNet a una red local y, a continuación, crear una VPN de enrutamiento dinámica. VNet a VNet requiere puertas de enlace VPN de Azure con VPN enrutamiento dinámicas. Azure VPN enrutamiento estáticas no son compatibles.

**Para configurar la conexión de sitio a sitio Contoso-VNet-UE a Contoso-LNet-US**

1.  Desde el Portal de clásico de Azure, haga clic en **redes** en el panel izquierdo,
2.  Haga clic en **Contoso-VNet-UE**.
3.  Haga clic en la ficha **CONFIGUE** .
4.  Consulte **conectarse a la red local**.
5.  En la **Red LOCAL**, seleccione **Contoso-LNet-US**.
6.  Haga clic en **Agregar subred de puerta de enlace** en la sección de espacios de dirección de una red virtual.
7.  Haga clic en **Guardar**.
8.  Haga clic en **Aceptar** para confirmar.


**Para crear una puerta de enlace VPN para Contoso-VNet-UE**

1.  Desde el Portal de clásico de Azure, haga clic en la pestaña **panel** .
4.  Haga clic en **Crear la puerta de enlace** en la parte inferior de la página y, a continuación, haga clic en **Enrutamiento dinámico**.
5.  Haga clic en **Sí** para confirmar. Observe el gráfico de la puerta de enlace en la página cambia a amarillo y muestra la creación de puerta de enlace. Normalmente se tarda aproximadamente 15 minutos para crear la puerta de enlace.

    Cuando se cambia el estado de la puerta de enlace para conectar, la dirección IP de cada puerta de enlace estará visible en el panel. Anote la dirección IP que corresponde a cada VNet, con cuidado de no para mezclar hacia arriba. Estos son las direcciones IP que se utilizará al editar las direcciones IP de marcador de posición para el dispositivo VPN en redes locales.

6.  Hacer una copia de la **Dirección IP de puerta de enlace**. Se usará para configurar la dirección IP de puerta de enlace VPN para Contoso-VNet-UE en la siguiente sección.

**Para crear una puerta de enlace VPN para Contoso-VNet-UE**

- Repita los dos últimos procedimiento para configurar la conectividad de sitio a sitio Contoso-VNet-US Contoso-LNet-UE y la creación de una puerta de enlace VPN para Contoso-Vnet-US. Cuando haya terminado, tendrá la dirección IP de puerta de enlace VPN para Contoso-VNet-US.


### <a name="set-the-vpn-device-ip-addresses-for-local-networks"></a>Configurar el dispositivo VPN direcciones IP para redes locales
En la última sección, creará una puerta de enlace VPN para cada uno de los VNets. Tengo las direcciones IP de las puertas de enlace VPN. Ahora puede volver a configurar las direcciones IP de red local VPN dispositivo.

**Para configurar la dirección IP del dispositivo VPN para Contoso-LNet-UE** 

1.  Desde el Portal de clásico de Azure, haga clic en **redes** en el panel izquierdo.
2.  Haga clic en **Redes locales** desde la parte superior.
3.  Haga clic en **Contoso-LNet-UE**y, a continuación, haga clic en **Editar** en la parte inferior.
4.  Actualizar la **dirección IP del dispositivo VPN**.  Esta es la dirección que acceder desde la pestaña Panel de Contoso-VNET-UE.
5.  Haga clic en el botón derecho.
6.  Haga clic en el botón comprobar.

**Para configurar la dirección IP del dispositivo VPN para Contoso-LNet-US** 

- Repita el último procedimiento para configurar la dirección IP del dispositivo VPN para Contoso-LNet-US.

###<a name="set-vnet-gateway-keys"></a>Establezca las claves de puerta de enlace VNet

Las puertas de enlace Vnet utilizan una clave compartida para autenticar conexiones entre las redes virtuales. No se puede configurar la clave desde el Portal de clásico de Azure. Debe usar PowerShell o .NET SDK.

**Para establecer las claves**

1. Desde su estación de trabajo, abra **Windows PowerShell ISE** o la consola de Windows PowerShell.
2. Actualizar los parámetros de esta secuencia de comandos de seguimiento y ejecútelo:

        Add-AuzreAccount
        Select-AzureSubscription -[AzureSubscriptionName]
        Set-AzureVNetGatewayKey -VNetName ContosoVNet-EU -LocalNetworkSiteName Contoso-LNet-US -SharedKey A1b2C3D4
        Set-AzureVNetGatewayKey -VNetName ContosoVNet-US -LocalNetworkSiteName Contoso-LNet-EU -SharedKey A1b2C3D4 


##<a name="check-the-vpn-connection"></a>Compruebe la conexión VPN 

Sin ningún VM implementadas a la VNets, puede usar el diagrama de red virtual visual la página panel de VNet en el Portal de Azure clásico para comprobar el estado de conexión:

![Red virtual replicación de HDInsight HBase estado de la conexión VPN][img-vpn-status]
  



##<a name="next-steps"></a>Pasos siguientes

En este tutorial ha aprendido a configurar una conexión VPN entre dos redes virtuales Azure. Carta de los dos artículos de la serie:

- [Configurar el DNS entre dos redes virtuales de Azure][hdinsight-hbase-geo-replication-dns]
- [Configurar la replicación de geo HBase][hdinsight-hbase-geo-replication]



[hdinsight-hbase-geo-replication-dns]: hdinsight-hbase-geo-replication-configure-dns.md
[hdinsight-hbase-geo-replication]: hdinsight-hbase-geo-replication.md


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-portal]: https://portal.azure.com

[powershell-install]: ../install-configure-powershell.md



[hdinsight-hbase-replication]: hdinsight-hbase-geo-replication.md
[hdinsight-hbase-dns]: hdinsight-hbase-geo-replication-configure-dns.md


[img-vnet-diagram]: ./media/hdinsight-hbase-geo-replication-configure-vnets/hdinsight-hbase-vpn-diagram.png
[img-vnet-lnet-diagram]: ./media/hdinsight-hbase-geo-replication-configure-vnets/hdinsight-hbase-vpn-lnet-diagram.png
[img-vpn-status]: ./media/hdinsight-hbase-geo-replication-configure-vnets/hdinsight-hbase-vpn-status.png 

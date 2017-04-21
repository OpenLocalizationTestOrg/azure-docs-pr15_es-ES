<properties 
   pageTitle="Configurar el DNS entre dos redes virtuales Azure | Microsoft Azure" 
   description="Obtenga información sobre cómo configurar las conexiones VPN y resolución de nombres de dominio entre dos redes virtuales y cómo configurar la replicación de geo HBase." 
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

# <a name="configure-dns-between-two-azure-virtual-networks"></a>Configurar el DNS entre dos redes virtuales de Azure

> [AZURE.SELECTOR]
- [Configurar la conectividad VPN](../hdinsight-hbase-geo-replication-configure-vnets.md)
- [Configurar el DNS](hdinsight-hbase-geo-replication-configure-dns.md)
- [Configurar la replicación de HBase](hdinsight-hbase-geo-replication.md) 


Obtenga información sobre cómo agregar y configurar los servidores DNS a Azure redes virtuales para controlar la resolución de nombres de dentro y entre las redes virtuales.

Este tutorial es la segunda parte de la [serie] [ hdinsight-hbase-geo-replication] sobre la creación de replicación geo HBase:

- [Configurar una conexión VPN entre dos redes virtuales][hdinsight-hbase-geo-replication-vnet]
- Configurar el DNS para las redes virtuales (en este tutorial)
- [Configurar la replicación de geo HBase][hdinsight-hbase-geo-replication]


El siguiente diagrama muestra las dos redes virtuales que creó en [Configurar una conexión VPN entre dos redes virtuales][hdinsight-hbase-geo-replication-vnet]:

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

- **Red virtual de Azure dos con conectividad VPN**.  Para obtener instrucciones, vea [Configurar una conexión VPN entre dos redes virtuales Azure][hdinsight-hbase-geo-replication-vnet].

>[AZURE.NOTE] Nombres de Azure servicio y máquina virtual deben ser únicos. El nombre utilizado en este tutorial es Contoso-[nombre del servicio de Azure/VM]-[UE / US]. Por ejemplo, Contoso-VNet-UE es la red virtual Azure en el centro de datos de Europa del Norte; Contoso-DNS-US es el servidor DNS VM en el centro de datos de Estados Unidos oriental. Debe dar con sus propios nombres.
 
 
##<a name="create-azure-virtual-machines-to-be-used-as-dns-servers"></a>Crear Azure máquinas virtuales de Windows que se utilizará como servidores DNS

**Para crear una máquina virtual dentro de Contoso UE VNet, denominada Contoso-DNS-UE**

1.  Haga clic en **nuevo**, **calcular**, **máquina VIRTUAL**, **De galería**.
2.  Elija **Centro de datos de Windows Server 2012 R2**.
3.  Escriba:
    - **Nombre de la máquina VIRTUAL**: Contoso-DNS-UE
    - **Nuevo nombre de usuario**: 
    - **Nueva contraseña**: 
4.  Escriba:
    - **Servicio de nube**: crear un nuevo servicio de nube
    - **Región/grupo AFINIDAD/VIRTUAL de red**: (seleccione Contoso-VNet-UE)
    - **SUBREDES de red VIRTUAL**: subred 1
    - **Cuenta de almacenamiento**: usar una cuenta de almacenamiento generada automáticamente
    
        El nombre de servicio de nube será el mismo que el nombre de la máquina virtual. En este caso, es Contoso-DNS-UE. Para las siguientes máquinas virtuales, puedo elegir usar el mismo servicio de nube.  Todas las máquinas virtuales en el mismo servicio de nube compartir la misma red virtual y el sufijo del dominio.

        La cuenta de almacenamiento se usa para almacenar el archivo de imagen de la máquina virtual. 
    - **EXTREMOS**: (desplácese hacia abajo y seleccione **DNS**) 

Después de crear la máquina virtual, descubra interno externo IP y.

1.  Haga clic en el nombre de la máquina virtual, **Contoso-DNS-UE**.
2.  Haga clic en **panel**.
3.  Anote:
    - DIRECCIÓN IP VIRTUAL PÚBLICA
    - DIRECCIÓN IP INTERNA


**Para crear una máquina virtual dentro de Contoso-VNet-US denominada Contoso-DNS-US** 

- Repita el mismo procedimiento para crear una máquina virtual con los siguientes valores:
    - NOMBRE de la máquina VIRTUAL: Contoso-DNS-US
    - REGIÓN/grupo AFINIDAD/red VIRTUAL: seleccione Contoso-VNET-US
    - SUBREDES de red VIRTUAL: Subred-1
    - CUENTA de almacenamiento: Usar una cuenta de almacenamiento generada automáticamente
    - EXTREMOS: (seleccione DNS)

##<a name="set-static-ip-addresses-for-the-two-virtual-machines"></a>Configurar direcciones IP para los dos equipos virtuales

Los servidores DNS requiere direcciones IP.  No se puede realizar este paso desde el Portal de clásico de Azure. Use PowerShell de Azure.

**Para configurar la dirección IP estática para las dos máquinas virtuales**

1. Abrir Windows PowerShell ISE.
2. Ejecute los cmdlets siguientes.  

        Add-AzureAccount
        Select-AzureSubscription [YourAzureSubscriptionName]
        
        Get-AzureVM -ServiceName Contoso-DNS-EU -Name Contoso-DNS-EU | Set-AzureStaticVNetIP -IPAddress 10.1.0.4 | Update-AzureVM
        Get-AzureVM -ServiceName Contoso-DNS-US -Name Contoso-DNS-US | Set-AzureStaticVNetIP -IPAddress 10.2.0.4 | Update-AzureVM 

    Nombre de servicio es el nombre de servicio de nube. Dado que el servidor DNS es la primera máquina virtual del servicio de nube, el nombre de servicio de nube es el mismo que el nombre de la máquina virtual.

    Debe actualizar ServiceName y el nombre para que coincida con los nombres que tiene.


##<a name="add-the-dns-server-role-the-two-virtual-machines"></a>Agregar las máquinas virtuales de los dos de la función de servidor DNS

**Para agregar el rol de servidor DNS para Contoso-DNS-UE**

1.  Desde el Portal de clásico de Azure, haga clic en **máquinas virtuales** de la izquierda. 
2.  Haga clic en **Contoso-DNS-UE**.
3.  Haga clic en **panel** de la parte superior.
4.  Haga clic en **Conectar** desde la parte inferior y siga las instrucciones para conectarse a la máquina virtual a través de RDP.
2.  En la sesión RDP, haga clic en el botón en la esquina inferior izquierda para abrir la pantalla de inicio de Windows.
3.  Haga clic en el mosaico **Administrador del servidor** .
4.  Haga clic en **Agregar funciones y características**.
5.  Haga clic en **siguiente**
6.  Seleccione **basado en roles o característica de instalación**y, a continuación, haga clic en **siguiente**.
7.  Seleccione la máquina virtual DNS (se deberá resaltado ya) y, a continuación, haga clic en **siguiente**.
8.  Compruebe el **servidor DNS**.
9.  Haga clic en **Agregar características**y, a continuación, haga clic en **continuar**.
10. Haga clic en **siguiente** tres veces y, a continuación, haga clic en **instalar**. 

**Para agregar el rol de servidor DNS para Contoso-DNS-US**

- Repita los pasos para agregar el rol DNS a **Contoso-DNS-US**.

##<a name="assign-dns-servers-to-the-virtual-networks"></a>Asignar a los servidores DNS a las redes virtuales

**Para registrar los dos servidores DNS**

1.  Desde el Portal de clásico de Azure, haga clic en **nuevo**, **Servicios de red**, **Una red VIRTUAL**, **Registrar DNS SERVER**.
2.  Escriba:
    - **Nombre**: Contoso-DNS-UE
    - **Dirección IP del servidor DNS**: 10.1.0.4: la dirección IP debe que coincidan con la dirección IP de máquina virtual de servidor DNS.
     
3.  Repita el proceso para registrar Contoso-DNS-US con los siguientes valores:
    - **Nombre**: Contoso-DNS-US
    - **Dirección IP del servidor DNS**: 10.2.0.4

**Para asignar a los dos servidores DNS a las dos redes virtuales**

1.  Haga clic en **redes** desde el panel izquierdo en el Portal de clásico.
2.  Haga clic en **Contoso-VNet-UE**.
3.  Haga clic en **Configurar**.
4.  Seleccione **Contoso-DNS-UE** en la sección **servidores dns** .
5.  Haga clic en **Guardar** en la parte inferior de la página y haga clic en **Sí** para confirmar.
6.  Repita el proceso para asignar al servidor DNS de **Contoso-DNS-US** a la red virtual de **Contoso-VNet-US** .

Todas las máquinas virtuales que se han implementado en las redes virtuales se debe reiniciar para actualizar la configuración del servidor DNS.

**Para reiniciar los equipos virtuales**

1. Desde el Portal de clásico de Azure, haga clic en **máquinas virtuales** de la izquierda.
2. Haga clic en **Contoso-DNS-UE**.
3. Haga clic en **panel** de la parte superior.
4. En la parte inferior, haga clic en **reiniciar** .
5. Repita los mismos pasos para reiniciar **Contoso-DNS-US**.


##<a name="configure-dns-conditional-forwarders"></a>Configure reenviadores condicionales DNS

El servidor DNS en cada red virtual solo puede resolver los nombres DNS dentro de una red virtual. Debe configurar un servidor de reenvío condicional para apunte al servidor DNS del mismo nivel de resolución de nombres en la red virtual de punto.

Para configurar reenviador condicional, debe saber los sufijos de dominio de los dos servidores DNS. Los sufijos DNS pueden ser diferentes dependiendo de la configuración de servicios en la nube que utilizó cuando creó las máquinas virtuales. Para cada sufijo DNS que se usan en la red virtual, debe agregar un reenviador condicional. 

**Para buscar los sufijos de dominio de los dos servidores DNS**

1. RDP en **UE de DNS de Contoso**.
2. Abra la consola de Windows PowerShell o símbolo.
3. Ejecutar **ipconfig**y anote el **sufijo DNS específico de conexión**.
4. No se cierra la sesión RDP, que lo necesitará. 
5. Repita los mismos pasos para descubrir el **sufijo DNS específico de la conexión** de **Contoso-DNS-US**.


**Para configurar los reenviadores DNS**
 
1.  Desde la sesión RDP **UE de DNS de Contoso**, haga clic en la tecla Windows en la esquina inferior izquierda.
2.  Haga clic en **Herramientas administrativas**.
3.  Haga clic en **DNS**.
4.  En el panel izquierdo, expanda **DSN**, **Contoso-DNS-UE**.
5.  Escriba la información siguiente:
    - **Dominio DNS**: escriba el sufijo DNS de Contoso-DNS-US. Por ejemplo: Contoso-DNS-US.b5.internal.cloudapp.net.
    - **Direcciones IP de los servidores maestros**: escriba 10.2.0.4, que es la dirección IP de Contoso-DNS-EE.UU..
6.  Presione **ENTRAR**y, a continuación, haga clic en **Aceptar**.  Ahora podrá resolver la dirección IP de Contoso-DNS-EE.UU. de Contoso-DNS-UE.
7.  Repita los pasos para agregar un reenviador DNS para el servicio DNS en la máquina virtual de Contoso-DNS-US con los siguientes valores:
    - **Dominio DNS**: escriba el sufijo DNS de la UE de DNS de Contoso. 
    - **Direcciones IP de los servidores maestros**: escriba 10.2.0.4, que es la dirección IP de Contoso-DNS-de la Unión Europea.

##<a name="test-the-name-resolution-across-the-virtual-networks"></a>Probar la resolución de nombres a través de las redes virtuales

Ahora puede probar la resolución de nombres de host a través de las redes virtuales. Ping está bloqueada por firewall de forma predeterminada.  Puede utilizar nslookup para resolver las máquinas virtuales de DNS server (debe utilizar FQDN) en las redes del mismo nivel.  


##<a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido a configurar la resolución de nombres a través de redes virtuales con conexiones VPN. Carta de los dos artículos de la serie:

- [Configurar una conexión VPN entre dos redes virtuales de Azure][hdinsight-hbase-geo-replication-vnet]
- [Configurar la replicación de geo HBase][hdinsight-hbase-geo-replication]



[hdinsight-hbase-geo-replication]: hdinsight-hbase-geo-replication.md
[hdinsight-hbase-geo-replication-vnet]: hdinsight-hbase-geo-replication-configure-vnets.md
[powershell-install]: powershell-install-configure.md

[img-vnet-diagram]: ./media/hdinsight-hbase-geo-replication-configure-DNS/HDInsight.HBase.VPN.diagram.png 

<properties
    pageTitle="Conectarse a un equipo Virtual con SQL Server (Administrador de recursos) | Microsoft Azure"
    description="Obtenga información sobre cómo conectarse a SQL Server en una máquina Virtual en Azure. En este tema se utiliza el modelo de implementación clásico. Los escenarios varían según la configuración de red y la ubicación del cliente."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"    
    tags="azure-resource-manager"/>
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/21/2016"
    ms.author="jroth" />

# <a name="connect-to-a-sql-server-virtual-machine-on-azure-resource-manager"></a>Conectarse a un equipo Virtual con SQL Server en Azure (jefe de recursos)

> [AZURE.SELECTOR]
- [Administrador de recursos](virtual-machines-windows-sql-connect.md)
- [Clásico](virtual-machines-windows-classic-sql-connect.md)

## <a name="overview"></a>Información general

Este tema describe cómo conectarse a su instancia de SQL Server que se ejecuta en una máquina virtual Azure. Cubre algunos [escenarios de conectividad general](#connection-scenarios) y, a continuación, se proporcionan [instrucciones detalladas para configurar la conectividad de SQL Server en una máquina virtual de Azure](#steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásico. Para ver la versión básica de este artículo, vea [conectarse a una máquina Virtual de SQL Server en Azure clásico](virtual-machines-windows-classic-sql-connect.md).

Si prefiere que un tutorial completo de aprovisionamiento y conectividad, vea [el aprovisionamiento de un equipo Virtual con SQL Server en Azure](virtual-machines-windows-portal-sql-server-provision.md).

## <a name="connection-scenarios"></a>Escenarios de conexión

La manera en que un cliente se conecta a SQL Server en una máquina Virtual varía según la ubicación del cliente y la configuración de red o equipo. Estos escenarios incluyen:

- [Conectarse a SQL Server a través de internet](#connect-to-sql-server-over-the-internet)
- [Conectarse a SQL Server en la misma red virtual](#connect-to-sql-server-in-the-same-virtual-network)

### <a name="connect-to-sql-server-over-the-internet"></a>Conectarse a SQL Server a través de Internet

Si desea conectarse a su motor de base de datos de SQL Server desde Internet, hay varios pasos necesarios, por ejemplo, configurar el firewall, habilitar la autenticación de SQL y configuración de su grupo de seguridad de red debe tener una regla de grupo de seguridad de la red para permitir el tráfico TCP en el puerto 1433.

Si utiliza el portal proporcionando una imagen de la máquina virtual de SQL Server con el Administrador de recursos, estos pasos se realizan cuando selecciona **público** para que la opción de conectividad SQL:

![Opción de conectividad SQL público durante el aprovisionamiento](./media/virtual-machines-windows-sql-connect/sql-vm-portal-connectivity.png)

Si no era una durante el aprovisionamiento, a continuación, puede configurar manualmente SQL Server y sus máquinas virtuales siguiendo los [pasos de este artículo para configurar la conectividad manualmente](#steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm).

>[AZURE.NOTE] La imagen de la máquina virtual de SQL Server Express edition no habilita automáticamente el protocolo TCP/IP. Para Express edition, debe usar el Administrador de configuración de SQL Server para [habilitar manualmente el protocolo TCP/IP](#configure-sql-server-to-listen-on-the-tcp-protocol) después de crear la máquina virtual.

Una vez hecho esto, cualquier cliente con acceso a internet puede conectarse a la instancia de SQL Server especificando la dirección IP pública de la máquina virtual o la etiqueta DNS asignado a dicha dirección IP. Si el puerto de SQL Server es 1433, no necesitará especificar en la cadena de conexión.

    "Server=sqlvmlabel.eastus.cloudapp.azure.com;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

Aunque esto permite la conectividad de clientes en internet, esto no implica que cualquier usuario puede conectarse a SQL Server. Fuera de clientes deben tener el nombre de usuario correcto y la contraseña. Para obtener seguridad adicional, puede evitar el puerto conocido 1433. Por ejemplo, si configura SQL Server para escuchar en puerto 1500 y había establecido reglas del grupo de seguridad de red y de firewall apropiado, podría conectar agregando el número de puerto al nombre del servidor como en el ejemplo siguiente:

    "Server=sqlvmlabel.eastus.cloudapp.azure.com,1500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

>[AZURE.NOTE] Es importante que tenga en cuenta que al usar esta técnica para comunicarse con SQL Server, todos los datos salientes desde el centro de datos de Azure está sujeto a normal [precios de transferencias de datos de salida](https://azure.microsoft.com/pricing/details/data-transfers/).

### <a name="connect-to-sql-server-in-the-same-virtual-network"></a>Conectarse a SQL Server en la misma red virtual

[Una red virtual](../virtual-network/virtual-networks-overview.md) permite escenarios adicionales. Puede conectar máquinas virtuales en la misma red virtual, incluso si existen esas máquinas virtuales en distintos grupos de recursos. Y con una [VPN de sitio a sitio](../vpn-gateway/vpn-gateway-site-to-site-create.md), puede crear una arquitectura híbrida que conecta máquinas virtuales con equipos y redes local.

Redes virtuales también le permite unirse a sus máquinas virtuales de Azure a un dominio. Esta es la única manera de usar autenticación de Windows para SQL Server. Los demás escenarios de conexión requieren autenticación de SQL con nombres de usuario y contraseñas.

Si utiliza el portal proporcionando una imagen de la máquina virtual de SQL Server con el Administrador de recursos, las reglas de firewall apropiado para la comunicación de la red virtual son configuración cuando seleccione **privado** para la opción de conectividad SQL. Si no era una durante el aprovisionamiento, a continuación, puede configurar manualmente SQL Server y sus máquinas virtuales siguiendo los [pasos de este artículo para configurar la conectividad manualmente](#steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm). Pero si va a configurar un entorno de dominio y la autenticación de Windows, siga los pasos de este artículo para configurar la autenticación de SQL e inicios de sesión no es necesario. También es necesario configurar reglas de grupo de seguridad de red para acceso a través de internet.

>[AZURE.NOTE] La imagen de la máquina virtual de SQL Server Express edition no habilita automáticamente el protocolo TCP/IP. Para Express edition, debe usar el Administrador de configuración de SQL Server para [habilitar manualmente el protocolo TCP/IP](#configure-sql-server-to-listen-on-the-tcp-protocol) después de crear la máquina virtual.

Suponiendo que ha configurado DNS de su red virtual, puede conectarse a su instancia de SQL Server especificando el nombre del equipo de máquina virtual de SQL Server en la cadena de conexión. En el siguiente ejemplo también se supone que también se ha configurado la autenticación de Windows y que el usuario se le ha concedido acceso a la instancia de SQL Server.

    "Server=mysqlvm;Integrated Security=true"

Tenga en cuenta que en este escenario, también puede especificar la dirección IP de la máquina virtual.

## <a name="steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm"></a>Pasos para configurar manualmente la conectividad de SQL Server en una máquina virtual de Azure

Los pasos siguientes muestran cómo configurar manualmente la conectividad a la instancia de SQL Server y, a continuación, si lo desea conectarse a través de internet a través de SQL Server Management Studio (SSMS). Tenga en cuenta que muchos de estos pasos se realizan cuando seleccione las opciones adecuadas de conectividad de SQL Server en el portal.

Antes de que puede conectarse a la instancia de SQL Server desde otra VM o en internet, debe completar las siguientes tareas como se describe en las secciones siguientes:

- [Puertos TCP abiertos en el firewall de Windows](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
- [Configurar SQL Server para escuchar en el protocolo TCP](#configure-sql-server-to-listen-on-the-tcp-protocol)
- [Configurar SQL Server para la autenticación de modo mixto](#configure-sql-server-for-mixed-mode-authentication)
- [Crear inicios de sesión de autenticación de SQL Server](#create-sql-server-authentication-logins)
- [Configurar una regla de entrada del grupo de seguridad de red](#configure-a-network-security-group-inbound-rule-for-the-vm)
- [Configurar una etiqueta de DNS para la dirección IP pública](#configure-a-dns-label-for-the-public-ip-address)
- [Conectar con el motor de base de datos desde otro equipo](#connect-to-the-database-engine-from-another-computer)

[AZURE.INCLUDE [Connect to SQL Server in a VM](../../includes/virtual-machines-sql-server-connection-steps.md)]

[AZURE.INCLUDE [Connect to SQL Server in a VM Resource Manager](../../includes/virtual-machines-sql-server-connection-steps-resource-manager-nsg-rule.md)]

[AZURE.INCLUDE [Connect to SQL Server in a VM Resource Manager](../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## <a name="next-steps"></a>Pasos siguientes

Para ver instrucciones junto con estos pasos de conectividad de aprovisionamiento, vea [el aprovisionamiento de un equipo Virtual con SQL Server en Azure](virtual-machines-windows-portal-sql-server-provision.md).

[Explorar la ruta de aprendizaje](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) para SQL Server en máquinas virtuales de Windows Azure.

Para otros temas relacionados con la ejecución de SQL Server en máquinas virtuales de Azure, consulte [SQL Server en Azure máquinas virtuales de Windows](virtual-machines-windows-sql-server-iaas-overview.md).

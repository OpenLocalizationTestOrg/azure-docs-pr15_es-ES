<properties
    pageTitle="Conectarse a un equipo Virtual con SQL Server (clásico) | Microsoft Azure"
    description="Obtenga información sobre cómo conectarse a SQL Server en una máquina Virtual en Azure. En este tema se utiliza el modelo de implementación clásico. Los escenarios varían según la configuración de red y la ubicación del cliente."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"
    tags="azure-service-management"/>
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/22/2016"
    ms.author="jroth" />

# <a name="connect-to-a-sql-server-virtual-machine-on-azure-classic-deployment"></a>Conectarse a un equipo Virtual con SQL Server en Azure (implementación clásico)

> [AZURE.SELECTOR]
- [Administrador de recursos](virtual-machines-windows-sql-connect.md)
- [Clásico](virtual-machines-windows-classic-sql-connect.md)

## <a name="overview"></a>Información general

Este tema describe cómo conectarse a su instancia de SQL Server que se ejecuta en una máquina virtual Azure. Cubre algunos [escenarios de conectividad general](#connection-scenarios) y, a continuación, se proporcionan [instrucciones detalladas para configurar la conectividad de SQL Server en una máquina virtual de Azure](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Si está utilizando máquinas virtuales de administrador de recursos, vea [conectarse a una máquina Virtual de SQL Server en Azure con el Administrador de recursos](virtual-machines-windows-sql-connect.md).

## <a name="connection-scenarios"></a>Escenarios de conexión

La manera en que un cliente se conecta a SQL Server en una máquina Virtual varía según la ubicación del cliente y la configuración de red o equipo. Estos escenarios incluyen:

- [Conectarse a SQL Server en el mismo servicio de nube](#connect-to-sql-server-in-the-same-cloud-service)
- [Conectarse a SQL Server a través de internet](#connect-to-sql-server-over-the-internet)
- [Conectarse a SQL Server en la misma red virtual](#connect-to-sql-server-in-the-same-virtual-network)

>[AZURE.NOTE] Antes de conectar con cualquiera de estos métodos, debe seguir los [pasos de este artículo para configurar la conectividad](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

### <a name="connect-to-sql-server-in-the-same-cloud-service"></a>Conectarse a SQL Server en el mismo servicio de nube

Varias máquinas virtuales pueden crearse en el mismo servicio de nube. Para comprender este escenario máquinas virtuales de Windows, consulte [cómo conectar máquinas virtuales con un servicio de nube o de red virtual](virtual-machines-windows-classic-connect-vms.md#connect-vms-in-a-standalone-cloud-service). En este escenario es cuando un cliente en una máquina virtual intenta conectarse a SQL Server en otro equipo virtual en el mismo servicio de nube.

En este escenario, puede conectarse usando el **nombre** de VM (también se muestra como **Nombre del equipo** o el **nombre de host** en el portal). Este es el nombre proporcionado para la máquina virtual durante la creación. Por ejemplo, si el nombre de su SQL VM **mysqlvm**un cliente VM en el mismo servicio de nube puede utilizar la cadena de conexión siguiente para conectarse:

    "Server=mysqlvm;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

### <a name="connect-to-sql-server-over-the-internet"></a>Conectarse a SQL Server a través de Internet

Si desea conectarse a su motor de base de datos de SQL Server desde Internet, debe crear un extremo de la máquina virtual para la comunicación TCP entrante. Este paso de la configuración de Azure, dirige el tráfico de puerto TCP a un puerto TCP que se puede acceder a la máquina virtual.

Para conectarse a través de internet, debe usar el nombre DNS de la máquina virtual y el número de puerto del extremo VM (configurado más adelante en este artículo). Para buscar el nombre de DNS, vaya al Portal de Azure y seleccione **máquinas virtuales de Windows (clásico)**. A continuación, seleccione la máquina virtual. El **nombre DNS** se muestra en la sección **información general** .

Por ejemplo, considere la posibilidad de una máquina virtual clásica denominada **mysqlvm** con un nombre de DNS de **mysqlvm7777.cloudapp.net** y un punto final de la máquina virtual de **57500**. Suponiendo que la conectividad configurada correctamente, la cadena de conexión siguiente podría utilizarse para tener acceso a la máquina virtual desde cualquier lugar de internet:

    "Server=mycloudservice.cloudapp.net,57500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

Aunque esto permite la conectividad de clientes en internet, esto no implica que cualquier usuario puede conectarse a SQL Server. Fuera de clientes deben tener el nombre de usuario correcto y la contraseña. Para obtener seguridad adicional, no use el puerto conocido 1433 del extremo de público máquina virtual. Y si es posible, considere la posibilidad de agregar una ACL en el extremo para restringir el tráfico únicamente a los clientes de permiso. Para obtener instrucciones sobre cómo usar las ACL con extremos, consulte [administrar las ACL de un extremo](virtual-machines-windows-classic-setup-endpoints.md#manage-the-acl-on-an-endpoint).

>[AZURE.NOTE] Es importante que tenga en cuenta que al usar esta técnica para comunicarse con SQL Server, todos los datos salientes desde el centro de datos de Azure está sujeto a normal [precios de transferencias de datos de salida](https://azure.microsoft.com/pricing/details/data-transfers/).

### <a name="connect-to-sql-server-in-the-same-virtual-network"></a>Conectarse a SQL Server en la misma red virtual

[Una red virtual](../virtual-network/virtual-networks-overview.md) permite escenarios adicionales. Puede conectar máquinas virtuales en la misma red virtual, incluso si existen esas máquinas virtuales de servicios de nube diferente. Y con una [VPN de sitio a sitio](../vpn-gateway/vpn-gateway-site-to-site-create.md), puede crear una arquitectura híbrida que conecta máquinas virtuales con equipos y redes local.

Redes virtuales también le permite unirse a sus máquinas virtuales de Azure a un dominio. Esta es la única manera de usar autenticación de Windows para SQL Server. Los demás escenarios de conexión requieren autenticación de SQL con nombres de usuario y contraseñas.

Si va a configurar un entorno de dominio y la autenticación de Windows, no necesitará siga los pasos de este artículo para configurar el extremo público o la autenticación de SQL e inicios de sesión. En este escenario, puede conectarse a su instancia de SQL Server que especifica el nombre de la máquina virtual de SQL Server en la cadena de conexión. En el ejemplo siguiente se supone que también se ha configurado la autenticación de Windows y que el usuario se le ha concedido acceso a la instancia de SQL Server.

    "Server=mysqlvm;Integrated Security=true"

## <a name="steps-for-configuring-sql-server-connectivity-in-an-azure-vm"></a>Pasos para configurar la conectividad de SQL Server en una máquina virtual de Azure

Los pasos siguientes muestran cómo conectarse a la instancia de SQL Server en internet con SQL Server Management Studio (SSMS). Sin embargo, se aplican los mismos pasos para hacer que su máquina virtual de SQL Server que sean accesibles para aplicaciones, con lo que se ejecutan en ambos local y en Azure.

Antes de que puede conectarse a la instancia de SQL Server desde otra VM o en internet, debe completar las siguientes tareas como se describe en las secciones siguientes:

- [Crear un extremo TCP para la máquina virtual](#create-a-tcp-endpoint-for-the-virtual-machine)
- [Puertos TCP abiertos en el firewall de Windows](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
- [Configurar SQL Server para escuchar en el protocolo TCP](#configure-sql-server-to-listen-on-the-tcp-protocol)
- [Configurar SQL Server para la autenticación de modo mixto](#configure-sql-server-for-mixed-mode-authentication)
- [Crear inicios de sesión de autenticación de SQL Server](#create-sql-server-authentication-logins)
- [Determinar el nombre DNS de la máquina virtual](#determine-the-dns-name-of-the-virtual-machine)
- [Conectar con el motor de base de datos desde otro equipo](#connect-to-the-database-engine-from-another-computer)

La ruta de acceso de la conexión se resume en el diagrama siguiente:

![Conectarse a una máquina virtual de SQL Server](../../includes/media/virtual-machines-sql-server-connection-steps/SQLServerinVMConnectionMap.png)

[AZURE.INCLUDE [Connect to SQL Server in a VM Classic TCP Endpoint](../../includes/virtual-machines-sql-server-connection-steps-classic-tcp-endpoint.md)]

[AZURE.INCLUDE [Connect to SQL Server in a VM](../../includes/virtual-machines-sql-server-connection-steps.md)]

[AZURE.INCLUDE [Connect to SQL Server in a VM Classic Steps](../../includes/virtual-machines-sql-server-connection-steps-classic.md)]

## <a name="next-steps"></a>Pasos siguientes

Si también va a usar los grupos de disponibilidad AlwaysOn para alta disponibilidad y recuperación, considere la posibilidad de implementar una escucha. Se conectan los clientes de la base de datos a la escucha en lugar de directamente a una de las instancias de SQL Server. La escucha enruta a los clientes a la réplica principal en el grupo de disponibilidad. Para obtener más información, consulte [configurar un detector de ILB para grupos de disponibilidad AlwaysOn en Azure](virtual-machines-windows-classic-ps-sql-int-listener.md).

Es importante revisar todas las mejores prácticas de seguridad de SQL Server en una máquina virtual Azure. Para obtener más información, vea [Consideraciones de seguridad de SQL Server en Azure máquinas virtuales de Windows](virtual-machines-windows-sql-security.md).

[Explorar la ruta de aprendizaje](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) para SQL Server en máquinas virtuales de Windows Azure. 

Para otros temas relacionados con la ejecución de SQL Server en máquinas virtuales de Azure, consulte [SQL Server en Azure máquinas virtuales de Windows](virtual-machines-windows-sql-server-iaas-overview.md).

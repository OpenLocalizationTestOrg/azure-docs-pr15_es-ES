<properties
   pageTitle="Crear escucha para grupo de disponibilidad AlwaysOn para SQL Server en Azure máquinas virtuales de Windows"
   description="Instrucciones paso a paso para crear una escucha para un grupo de disponibilidad AlwaysOn para SQL Server en Azure máquinas virtuales de Windows"
   services="virtual-machines"
   documentationCenter="na"
   authors="MikeRayMSFT"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows-sql-server"
   ms.workload="infrastructure-services"
   ms.date="07/12/2016"
   ms.author="MikeRayMSFT"/>

# <a name="configure-an-internal-load-balancer-for-an-alwayson-availability-group-in-azure"></a>Configurar un equilibrador de carga interno para un grupo de disponibilidad AlwaysOn en Azure

En este tema se explica cómo crear un equilibrador de carga interno para un grupo de disponibilidad de SQL Server AlwaysOn en Azure máquinas virtuales que se ejecutan en el modelo de administrador de recursos. Un grupo de disponibilidad AlwaysOn requiere un equilibrador de carga cuando las instancias de SQL Server en máquinas virtuales de Windows Azure. El equilibrador de carga almacena la dirección IP para la escucha de grupo de disponibilidad. Si un grupo de disponibilidad abarca áreas múltiple, cada región necesita un equilibrador de carga.

Para completar esta tarea, debe tener un grupo de disponibilidad de SQL Server AlwaysOn implementado en máquinas virtuales de Azure en el modelo de administrador de recursos. Ambas máquinas virtuales de SQL Server debe pertenecer al mismo conjunto de disponibilidad. Puede usar la [plantilla de Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) para crear automáticamente el grupo de disponibilidad AlwaysOn en Administrador de recursos de Azure. Esta plantilla crea automáticamente equilibrador de carga interno. 

Si lo prefiere, puede [configurar manualmente un grupo de disponibilidad AlwaysOn](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

En este tema se requiere que los grupos de disponibilidad ya están configurados.  

Temas relacionados se incluyen:

 - [Configurar grupos de disponibilidad AlwaysOn en Azure VM (gráfico)](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)   
 
 - [Configurar una conexión de VNet a VNet mediante PowerShell y el Administrador de recursos de Azure](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

## <a name="steps"></a>Pasos

A través de este documento se cree y configure un equilibrador de carga en el portal de Azure. Una vez completada, va a configurar el clúster para usar la dirección IP del equilibrador de carga para la escucha de grupo de disponibilidad AlwaysOn.

## <a name="create-and-configure-the-load-balancer-in-the-azure-portal"></a>Crear y configurar el equilibrador de carga en el portal de Azure

En esta parte de la tarea se realice los pasos siguientes en el portal de Azure:

1. Crear el equilibrador de carga y configurar la dirección IP

1. Configurar el grupo de back-end

1. Crear el sondeo 

1. Establecer las reglas de equilibrio de carga

>[AZURE.NOTE] Si los servidores de SQL están en distintos grupos de recursos y de regiones, hará todos estos pasos dos veces, una vez en cada grupo de recursos.

## <a name="1-create-the-load-balancer-and-configure-the-ip-address"></a>1. crear el equilibrador de carga y configurar la dirección IP

El primer paso es crear el equilibrador de carga. En el portal de Azure, abra el grupo de recursos que contiene las máquinas virtuales de SQL Server. En el grupo de recursos, haga clic en **Agregar**.

- Buscar **equilibrador de carga**. Resultados de la búsqueda, seleccione **Equilibrador de carga**, que se ha publicado por **Microsoft**.

- En el módulo de **Equilibrador de carga** , haga clic en **crear**.

- En **crear equilibrador de carga**, configure el equilibrador de carga como sigue:

| Configuración | Valor |
| ----- | ----- |
| **Nombre** | Un nombre de texto que representa el equilibrador de carga. Por ejemplo, **sqlLB**. |
| **Esquema** | **Interno** |
| **Red virtual** | Elija la red virtual que se encuentran en los servidores de SQL.   |
| **Subred**  | Elija la subred que se encuentran en los servidores de SQL. |
| **Suscripción** | Si tiene varias suscripciones, puede aparecer este campo. Seleccione la suscripción que desea asociar con este recurso. Normalmente, es la misma suscripción como todos los recursos del grupo de disponibilidad.  |
| **Grupo de recursos** | Elija el grupo de recursos que se encuentran en los servidores de SQL. | 
| **Ubicación** | Elija la ubicación de Azure que se encuentran en los servidores de SQL. |

- Haga clic en **crear**. 

Azure crea el equilibrador de carga configurado más arriba. El equilibrador de carga pertenece a una red específica, la subred, el grupo de recursos y la ubicación. Después de que finalice de Azure, compruebe la configuración del equilibrador de carga en Azure. 

Ahora, configure la dirección IP del equilibrador de carga.  

- En el módulo de **configuración** del equilibrador de carga, haga clic en **dirección IP**. El módulo de **dirección IP** muestra que se trata de un equilibrador de carga privado en la misma red virtual como servidores SQL. 

- Establecer la configuración siguiente: 

| Configuración | Valor |
| ----- | ----- |
| **Subred** | Elija la subred que se encuentran en los servidores de SQL. |
| **Asignación** | **Estático** |
| **Dirección IP** | Escriba una dirección IP virtual sin usar de la subred.  |

- Guardar la configuración.

Ahora tiene la dirección IP del equilibrador de carga. Grabar esta dirección IP. Cuando se crea una escucha en el clúster usará esta dirección IP. En un script de PowerShell más adelante en este artículo, use esta dirección para la `$ILBIP` variable.



## <a name="2-configure-the-backend-pool"></a>2. configurar el grupo de back-end

El siguiente paso es crear un grupo de direcciones de back-end. El grupo de dirección de back-end *grupo back-end*de llamadas de Azure. En este caso, el grupo de back-end es las direcciones de los dos servidores de SQL en su grupo de disponibilidad. 

- En el grupo de recursos, haga clic en el equilibrador de carga que creó. 

- En **configuración**, haga clic en **grupos de back-end**.

- En **grupos de direcciones de back-end**, haga clic en **Agregar** para crear un grupo de direcciones de back-end. 

- En el **grupo de back-end de agregar** en **nombre**, escriba un nombre para el grupo de back-end.

- En **máquinas virtuales de Windows** , haga clic en **+ Agregar una máquina virtual**. 

- Haga clic en **Elegir un conjunto de disponibilidad** y especificar que la disponibilidad del conjunto que pertenecen las máquinas virtuales de SQL Server a **máquinas virtuales de elegir** .

- Una vez que haya elegido el conjunto de disponibilidad, haga clic en **Elegir las máquinas virtuales**. Haga clic en las dos máquinas virtuales que hospeda las instancias de SQL Server en el grupo de disponibilidad. Haga clic en **Seleccionar**. 

- Haga clic en **Aceptar** para cerrar los módulos para **Elegir máquinas virtuales de Windows**y a continuación, **Agregar grupo de back-end**. 

Azure actualiza la configuración de la agrupación de direcciones de back-end. Ahora el conjunto de disponibilidad tiene un grupo de dos servidores de SQL Server.

## <a name="3-create-a-probe"></a>3. crear un sondeo

El siguiente paso es crear un sondeo. El sondeo define cómo Azure comprobar cuál de los servidores de SQL posee actualmente la escucha de grupo de disponibilidad. Azure sondeo el servicio basado en la dirección IP en un puerto que defina al crear el sondeo.

- En el módulo de **configuración** del equilibrador de carga, haga clic en **sondeos**. 

- En el módulo **sondeos** , haga clic en **Agregar**.

- Configurar el sondeo en el módulo de **sondeo de agregar** . Para configurar el sondeo, use los siguientes valores:

| Configuración | Valor |
| ----- | ----- |
| **Nombre** | Un nombre de texto que representa el sondeo. Por ejemplo, **SQLAlwaysOnEndPointProbe**. |
| **Protocolo** | **TCP** |
| **Puerto** | Puede usar cualquier puerto disponible. Por ejemplo, *59999*.    |
| **Intervalo**  | *5* | 
| **Umbral de mal estado**  | *2* | 

- Haga clic en **Aceptar**. 

>[AZURE.NOTE] Asegúrese de que el puerto que especifique está abierto en el firewall de ambos servidores SQL Server. Ambos servidores requieren una regla de entrada para el puerto TCP que usa. Para obtener más información, vea [Agregar o editar reglas de Firewall](http://technet.microsoft.com/library/cc753558.aspx) . 

Azure crea el sondeo. Azure usará el sondeo para comprobar qué servidor SQL tiene la escucha para el grupo de disponibilidad.

## <a name="4-set-the-load-balancing-rules"></a>4. Establezca las reglas de equilibrio de carga

Establecer las reglas de equilibrio de carga. Las reglas de equilibrio de carga configurar cómo el equilibrador de carga enruta el tráfico a los servidores de SQL. Para este equilibrador de carga permitirá a servidor directo devuelto porque solo uno de los dos servidores de SQL Server nunca posee el recurso de escucha del grupo de disponibilidad a la vez.

- En el módulo de **configuración** del equilibrador de carga, haga clic en **reglas de equilibrio de carga**. 

- En el módulo de **Equilibrio de carga de reglas** , haga clic en **Agregar**.

- Use el módulo **Agregar reglas de equilibrio de carga** para configurar la regla de equilibrio de carga. Utilice la siguiente configuración: 

| Configuración | Valor |
| ----- | ----- |
| **Nombre** | Un nombre de texto que representa las reglas de equilibrio de carga. Por ejemplo, **SQLAlwaysOnEndPointListener**. |
| **Protocolo** | **TCP** |
| **Puerto** | *1433*   |
| **Puerto de back-end** | *1433*. tenga en cuenta que esta se desactivarán porque esta regla usa **IP flotante (servidor directo devuelto)**.   |
| **Sondeo** | Use el nombre del sondeo que ha creado para este equilibrador de carga. |
| **Persistencia de sesión**  | **Ninguno** | 
| **Tiempo de espera de inactividad (minutos)**  | *4* | 
| **Flotante IP (servidor directo devuelto)**  | **Habilitado** | 

 >[AZURE.NOTE] Es posible que tenga que desplazarse hacia abajo en el módulo para ver todas las opciones.

- Haga clic en **Aceptar**. 

- Azure configura la regla de equilibrio de carga. Ahora el equilibrador de carga está configurado para enrutar el tráfico a SQL Server que hospeda el detector para el grupo de disponibilidad. 

En este momento el grupo de recursos tiene un equilibrador de carga, conectarse a los equipos de SQL Server. El equilibrador de carga también contiene una dirección IP para la escucha de grupo de disponibilidad de SQL Server AlwaysOn para que cualquier equipo puede responder a solicitudes de los grupos de disponibilidad.

>[AZURE.NOTE] Si los servidores de SQL en dos regiones independientes, repita los pasos de las regiones. Cada región requiere un equilibrador de carga. 

## <a name="configure-the-cluster-to-use-the-load-balancer-ip-address"></a>Configurar el clúster para usar la dirección IP del equilibrador de carga 

El siguiente paso es configurar la escucha en el clúster y a continuación, visualice el oyente en línea. Para ello, haga lo siguiente: 

1. Crear la escucha de grupo de disponibilidad en el clúster 

1. Traer el oyente en línea

## <a name="1-create-the-availablity-group-listener-on-the-failover-cluster"></a>1. crear la escucha de grupo de disponibilidad en el clúster

En este paso, crear manualmente la escucha de grupo de disponibilidad en el Administrador de clúster de migración tras error y SQL Server Management Studio (SSMS).

- Usar RDP para conectarse a la máquina virtual Azure que aloja la réplica principal. 

- Abra el Administrador de clúster de migración tras error.

- Seleccione el nodo de **redes** y anote el nombre de red de clúster. Este nombre se utilizará en la `$ClusterNetworkName` variable en la secuencia de comandos de PowerShell.

- Expanda el nombre del clúster y, a continuación, haga clic en **Roles**.

- En el panel de **Roles** , haga clic en el nombre del grupo de disponibilidad y, a continuación, seleccione **Agregar recurso** > **Punto de acceso de cliente**.

- En el cuadro **nombre** , crear un nombre para esta escucha de nuevo, a continuación, haga doble clic en **siguiente** y, a continuación, haga clic en **Finalizar**. No poner el oyente o recursos en línea en este momento.

 >[AZURE.NOTE] El nombre de la nueva escucha es el nombre de red que aplicaciones usará para conectarse a bases de datos en el grupo de disponibilidad de SQL Server.

- Haga clic en la pestaña **recursos** , expanda el punto de acceso de cliente que acaba de crear. Haga clic en el recurso de IP y haga clic en Propiedades. Anote el nombre de la dirección IP. Usará este nombre en la `$IPResourceName` variable en la secuencia de comandos de PowerShell.

- En **Dirección IP** haga clic en **Dirección IP estática** y establezca la dirección IP estática en la misma dirección que utilizó cuando se establece la dirección IP del equilibrador de carga en el portal de Azure. Habilitar NetBIOS para esta dirección y haga clic en **Aceptar**. Si su solución abarca varios VNets de Azure, repita este paso para cada recurso IP. 

- En el nodo que aloja actualmente la réplica principal, abra un PowerShell ISE elevados y pegue los siguientes comandos en una secuencia de comandos nuevo.

        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $ILBIP = “<X.X.X.X>” # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
    
        Import-Module FailoverClusters
    
        Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}

- Actualice las variables y ejecute la secuencia de comandos de PowerShell para configurar la dirección IP y el puerto para la nueva escucha.

 >[AZURE.NOTE] Si los servidores de SQL en áreas independientes, debe ejecutar la secuencia de comandos de PowerShell dos veces. La primera vez que use el nombre de red clúster, nombre de recurso IP de clúster y la dirección IP del equilibrador de carga del primer grupo de recursos. La segunda vez que use el nombre de red clúster, nombre de recurso IP de clúster y la dirección IP del equilibrador de carga del segundo grupo de recursos.

Ahora el clúster tiene un recurso de escucha del grupo de disponibilidad.

## <a name="2-bring-the-listener-online"></a>2. Traer el oyente en línea

Con el recurso de escucha disponibilidad grupo configurado, puede poner en línea la escucha para que las aplicaciones pueden conectar a bases de datos en el grupo de disponibilidad con la escucha.

- Desplácese atrás hasta migración tras error clúster administrador. Expanda **Roles** y, a continuación, resalte el grupo de disponibilidad. En la pestaña **recursos** , haga clic en el nombre de la escucha y haga clic en **Propiedades**.

- Haga clic en la ficha **dependencias** . Si hay varios recursos que se muestran, compruebe que las direcciones IP que tienen o no AND, dependencias. Haga clic en **Aceptar**.

- Haga clic en el nombre de la escucha y haga clic en **Conectar**.


- Una vez que el oyente está en línea, en la ficha **recursos** , haga clic en el grupo de disponibilidad y haga clic en **Propiedades**.

- Crear una dependencia en el recurso de nombre de escucha (no nombre de los recursos de direcciones IP). Haga clic en **Aceptar**.


- Inicie SQL Server Management Studio y conectar con la réplica principal.


- Vaya a **AlwaysOn alta disponibilidad** | **grupos de disponibilidad** | **escucha de disponibilidad de grupo**. 


- Ahora debe ver el nombre de escucha que creó en el Administrador de clúster de migración tras error. Haga clic en el nombre de la escucha y haga clic en **Propiedades**.


- En el cuadro **puerto** , especifique el número de puerto para la escucha de grupo de disponibilidad mediante el uso de la $EndpointPort que usa versiones anteriores (1433 era el valor predeterminado), a continuación, haga clic en **Aceptar**.

Ahora tiene un grupo de disponibilidad de SQL Server AlwaysOn en Azure máquinas virtuales que se ejecutan en modo de administrador de recursos. 

## <a name="test-the-connection-to-the-listener"></a>Probar la conexión a la escucha

Para probar la conexión:

1. RDP a SQL Server que está en la misma red virtual, pero no posee la réplica. Esto puede ser el otro servidor de SQL en el clúster.

1. Use la utilidad **sqlcmd** para probar la conexión. Por ejemplo, la siguiente secuencia de comandos establece una conexión de **sqlcmd** con la réplica principal a través de la escucha con autenticación de Windows:

        sqlcmd -S <listenerName> -E

La conexión de SQLCMD se conectará automáticamente a la instancia de SQL Server aloja la réplica principal. 

## <a name="guidelines-and-limitations"></a>Directrices y limitaciones

Tenga en cuenta las siguientes directrices en escucha de grupo de disponibilidad en Azure con interno equilibrador de carga:

- Escucha de grupo interno disponibilidad solo es compatible por cada servicio de nube porque la escucha está configurada para el equilibrador de carga y no hay un único equilibrador de carga interno. Sin embargo es posible crear varios agentes de escucha externos. 

- Con un equilibrador de carga interno que solo tener acceso a la escucha desde dentro de la misma red virtual.
 

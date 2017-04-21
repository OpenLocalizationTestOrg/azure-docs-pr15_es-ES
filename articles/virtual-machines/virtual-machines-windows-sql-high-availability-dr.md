<properties
    pageTitle="Alta disponibilidad y recuperación para SQL Server | Microsoft Azure"
    description="Explicación de los distintos tipos de estrategias HADR para SQL Server se ejecuta en Azure máquinas virtuales de Windows."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="MikeRayMSFT"
    manager="jhubbard"
    editor=""
    tags="azure-service-management"/>
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/20/2016"
    ms.author="MikeRayMSFT" />

# <a name="high-availability-and-disaster-recovery-for-sql-server-in-azure-virtual-machines"></a>Alta disponibilidad y recuperación ante desastres para SQL Server en Azure máquinas virtuales de Windows

## <a name="overview"></a>Información general

Máquinas virtuales de Microsoft Azure (VM) con SQL Server puede ayudar a reducir el costo de una solución de base de datos alta disponibilidad y desastres recuperación (HADR). La mayoría de soluciones de SQL Server HADR son compatibles con Azure máquinas virtuales de Windows, como de solo Azure y como soluciones híbridas. En una solución solo Azure, todo el sistema HADR se ejecuta en Azure. En una configuración híbrida, parte de la solución se ejecuta en Azure y la otra parte se ejecuta local en su organización. La flexibilidad del entorno de Azure le permite desplazarse o parcialmente a Azure para cumplir con el presupuesto y los requisitos de HADR de los sistemas de base de datos de SQL Server.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


## <a name="understanding-the-need-for-an-hadr-solution"></a>Comprender la necesidad de una solución HADR

Depende de usted para garantizar que el sistema de base de datos posee las capacidades HADR requiere el contrato de nivel de servicio (SLA). El hecho de Azure proporciona mecanismos de alta disponibilidad, como servicio de resolución de problemas para los servicios en la nube y detección de recuperación de errores en los equipos virtuales, no garantiza que pueda cumplir el SLA deseado. Estos mecanismos protegen la alta disponibilidad de las máquinas virtuales pero no la alta disponibilidad de ejecuta dentro de las máquinas virtuales de SQL Server. Es posible que la instancia de SQL Server no mientras la máquina virtual está en línea y correcto. Además, incluso la alta disponibilidad mecanismos de Azure permiten tiempo de inactividad de las máquinas virtuales debido a eventos, como la recuperación de software o errores de hardware y actualizaciones del sistema operativo.

Además, almacenamiento redundantes de Geo (GRS) en Azure, que se implementa con una característica denominada geo replicación, no puede ser una solución de recuperación de desastres adecuado para las bases de datos. Como geo replicación envía datos de forma asincrónica, se pueden perder en caso de desastre las actualizaciones más recientes. Para obtener más información sobre las limitaciones de replicación geo se tratan en la sección [no admite la replicación de Geo para archivos de datos y de registro en discos independientes](#geo-replication-support) .

## <a name="hadr-deployment-architectures"></a>Arquitecturas de implementación de HADR

Tecnologías de SQL Server HADR que son compatibles en Azure incluyen:

- [Siempre en grupos de disponibilidad](https://technet.microsoft.com/library/hh510230.aspx)
- [Creación de reflejos de base de datos](https://technet.microsoft.com/library/ms189852.aspx)
- [Envío de registro](https://technet.microsoft.com/library/ms187103.aspx)
- [Copia de seguridad y restauración con el servicio de almacenamiento de blobs de Windows Azure](https://msdn.microsoft.com/library/jj919148.aspx)
- [Siempre en las instancias de clúster](https://technet.microsoft.com/library/ms189134.aspx)

Es posible combinar las tecnologías para implementar una solución de SQL Server que tiene alta disponibilidad y capacidad de recuperación. Dependiendo de la tecnología que utilice, una implementación híbrida puede requerir un túnel VPN con una red virtual Azure. Las secciones siguientes muestran algunas de las arquitecturas de implementación de ejemplo.

## <a name="azure-only-high-availability-solutions"></a>Solo Azure: soluciones de alta disponibilidad

Puede tener una solución de alta disponibilidad para las bases de datos de SQL Server en Azure con siempre en grupos de disponibilidad o que coinciden con la base de datos.

| Tecnología                               | Arquitecturas de ejemplo                    |
| ---------------------------------------- | ---------------------------------------- |
| **Siempre en grupos de disponibilidad**        | Todas las réplicas de disponibilidad ejecutando en máquinas virtuales de Azure para alta disponibilidad dentro de la misma región. Necesita configurar un controlador de dominio VM, porque el clúster de migración tras error de servidor de Windows (WSFC) requiere un dominio de Active Directory.<br/> ![Siempre en grupos de disponibilidad](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_ha_always_on.gif)<br/>Para obtener más información, vea [Configurar siempre en disponibilidad de grupos en Azure (gráfico)](virtual-machines-windows-portal-sql-alwayson-availability-groups.md). |
| **Siempre en las instancias de clúster** | Instancias de clúster de migración tras error (FCI) que requieren almacenamiento compartido, se pueden crear de 2 formas diferentes.<br/><br/>1. un FCI en un WSFC de dos nodos en máquinas virtuales de Azure con almacenamiento compatible con una solución de clústeres de terceros. Para obtener un ejemplo específico que usa SIOS DataKeeper, vea [alta disponibilidad de un recurso compartido de archivos utilizando WSFC y 3er SIOS Datakeeper de software de terceros](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/).<br/><br/>2. un FCI en un WSFC de dos nodos en máquinas virtuales de Azure con iSCSI remoto destino compartido bloque de almacenamiento a través de ExpressRoute. Por ejemplo, almacenamiento privado de NetApp (NPS) expone un destino iSCSI a través de ExpressRoute con Equinix en máquinas virtuales de Azure.<br/><br/>De almacenamiento compartido de terceros y soluciones de replicación de datos, debe ponerse en contacto con el proveedor para los problemas relacionados con el acceso a los datos de migración tras error.<br/><br/>Tenga en cuenta que uso FCI encima de [almacenamiento de archivos de Azure](https://azure.microsoft.com/services/storage/files/) aún no se admite, porque esta solución no utiliza almacenamiento Premium. Estamos trabajando para admitir esto pronto. |

## <a name="azure-only-disaster-recovery-solutions"></a>Solo Azure: soluciones de recuperación de desastres

Puede tener una solución de recuperación de desastres para sus bases de datos de SQL Server en Azure con siempre en grupos de disponibilidad, reflejo de la base de datos o copia de seguridad y restaurar con BLOB de almacenamiento.

| Tecnología                               | Arquitecturas de ejemplo                    |
| ---------------------------------------- | ---------------------------------------- |
| **Siempre en grupos de disponibilidad**        | Réplicas de disponibilidad que se ejecuta en varios centros de datos en máquinas virtuales de Azure para recuperación. Esta solución entre región protege de interrupción en el sitio completo. <br/> ![Siempre en grupos de disponibilidad](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_alwayson.png)<br/>Dentro de una región, todas las réplicas deben estar dentro del mismo servicio de nube y el mismo VNet. Dado que cada región tendrá un VNet independiente, estas soluciones requieren VNet VNet conectividad. Para obtener más información, vea [Configurar una VPN de sitio a sitio en el portal de clásico de Azure](../vpn-gateway/vpn-gateway-site-to-site-create.md). |
| **Creación de reflejos de base de datos**                   | Principal y reflejada y servidores en centros de datos distintos para la recuperación. Debe implementar mediante certificados de servidor debido a un dominio de active directory no puede abarcar varios centros de datos.<br/>![Creación de reflejos de base de datos](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_dbmirroring.gif) |
| **Copia de seguridad y restauración con el servicio de almacenamiento de blobs de Windows Azure** | Copia de las bases de datos de producción directamente al almacenamiento de blobs de Windows en un centro de datos diferente para la recuperación.<br/>![Copia de seguridad y restauración](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_backup_restore.gif)<br/>Para obtener más información, vea [copia de seguridad y restauración de SQL Server en Azure máquinas virtuales de Windows](virtual-machines-windows-sql-backup-recovery.md). |

## <a name="hybrid-it-disaster-recovery-solutions"></a>TI híbridos: Soluciones de recuperación de desastres

Puede tener una solución de recuperación de desastres para las bases de datos de SQL Server en un entorno de TI híbrida mediante siempre en grupos de disponibilidad, reflejo de base de datos, envío de registros y copia de seguridad y restaurar con el almacenamiento de Azure blog.

| Tecnología                               | Arquitecturas de ejemplo                    |
| ---------------------------------------- | ---------------------------------------- |
| **Siempre en grupos de disponibilidad**        | Algunas réplicas de disponibilidad que se ejecuta en máquinas virtuales de Azure y otras réplicas ejecutando local entre sitios de recuperación. El sitio de producción puede estar en modo local o en un centro de datos de Azure.<br/>![Siempre en grupos de disponibilidad](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_alwayson.gif)<br/>Dado que todas las réplicas de disponibilidad deben estar en el mismo clúster WSFC, el clúster WSFC debe abarcar ambas redes (un clúster WSFC de subred múltiple). Esta configuración requiere una conexión VPN entre Azure y la red local.<br/><br/>Recuperación correcta de las bases de datos, también debe instalar una réplica de controlador de dominio en el sitio de recuperación de desastres.<br/><br/>Es posible usar al Asistente para agregar réplica en SSMS para agregar una réplica de Azure a un grupo existente de siempre en disponibilidad. Para obtener más información, vea Tutorial: ampliar su siempre en grupo disponibilidad en Azure. |
| **Creación de reflejos de base de datos**                   | Un compañero que se ejecuta en una máquina virtual de Azure y la ejecución de otra local mediante certificados de servidor de recuperación de entre sitios. No necesita estar en el mismo dominio de Active Directory asociados y conexión VPN no es necesaria.<br/>![Creación de reflejos de base de datos](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_dbmirroring.gif)<br/>Otra base de datos que coinciden con escenario implica a un compañero que se ejecuta en una máquina virtual de Azure y los otros ejecución local en el mismo dominio de Active Directory para entre sitios de recuperación. Se requiere una [conexión VPN entre la red virtual Azure y la red local](../vpn-gateway/vpn-gateway-site-to-site-create.md) .<br/><br/>Recuperación correcta de las bases de datos, también debe instalar una réplica de controlador de dominio en el sitio de recuperación de desastres. |
| **Envío de registro**                         | Un servidor que ejecuta en una máquina virtual de Azure y la ejecución de otra local entre sitios de recuperación. Envío de registro depende de compartir archivos de Windows, por lo que se requiere una conexión VPN entre la red virtual Azure y la red local.<br/>![Envío de registro](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_log_shipping.gif)<br/>Recuperación correcta de las bases de datos, también debe instalar una réplica de controlador de dominio en el sitio de recuperación de desastres. |
| **Copia de seguridad y restauración con el servicio de almacenamiento de blobs de Windows Azure** | Bases de datos de producción directamente al almacenamiento de blobs de Windows Azure para la recuperación de una copia local.<br/>![Copia de seguridad y restauración](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_backup_restore.gif)<br/>Para obtener más información, vea [copia de seguridad y restauración de SQL Server en Azure máquinas virtuales de Windows](virtual-machines-windows-sql-backup-recovery.md). |

## <a name="important-considerations-for-sql-server-hadr-in-azure"></a>Consideraciones importantes para SQL Server HADR en Azure

Máquinas virtuales de Azure, almacenamiento y redes tienen diferentes características operativas que una infraestructura de TI no virtualizado en local. Una implementación correcta de una solución HADR SQL Server en Azure requiere que estas diferencias y diseñar su solución para adaptarse a ellos.

### <a name="high-availability-nodes-in-an-availability-set"></a>Nodos de alta disponibilidad en un conjunto de disponibilidad

Conjuntos de disponibilidad en Azure le permiten colocar los nodos de alta disponibilidad en independiente errores dominios (FDs) y actualización (UDs). Para que Azure VM colocar en el mismo conjunto de disponibilidad, se debe implementar en el mismo servicio de nube. Solo los nodos en el mismo servicio de nube pueden participar en el mismo conjunto de disponibilidad. Para obtener más información, consulte [Administrar la disponibilidad de las máquinas virtuales](virtual-machines-windows-manage-availability.md).

### <a name="wsfc-cluster-behavior-in-azure-networking"></a>Comportamiento de clúster WSFC en redes de Azure

El servicio DHCP compatible con RFC en Azure puede provocar la creación de algunas configuraciones de clúster WSFC un error debido a que se asigna una dirección IP duplicada, como la misma dirección IP como uno de los nodos de clúster el nombre de red de clúster. Se trata de un problema al implementar siempre en disponibilidad de grupos, que depende de la característica WSFC.

Considere el escenario al crear un clúster de dos nodos y poner en línea:

1. El clúster se suministra en línea y luego nodo 1 solicita una dirección IP asignada dinámicamente para el nombre de red de clúster.

2. Ninguna dirección IP que no sea la dirección IP del Nodo1 es indicada por el servicio DHCP, ya que el servicio DHCP reconoce que la solicitud proviene de nodo 1 propiamente dicho.

3. Windows detecta que está asignada una dirección duplicada a nodo 1 y el nombre de red de clúster y el grupo de clúster predeterminado no se puede poner en línea.

4. El grupo de clúster predeterminado se mueve al nodo 2, que trata la dirección IP de nodo 1 como la dirección IP y obtiene el grupo de clúster de forma predeterminada.

5. Cuando intenta nodo 2 establecer la conexión con el nodo 1, paquetes dirigidos al nodo 1 no deje nunca nodo 2 porque se resuelve la dirección IP del nodo 1 a sí mismo. Nodo 2 no puede establecer la conexión con el nodo 1, pierde quórum y cierra el clúster.

6. Mientras tanto, nodo 1 puede enviar paquetes al nodo 2, pero no puede responder nodo 2. Nodo 1 se pierde quórum y se cierra el clúster.

Este escenario se puede evitar asignando una sin usar dirección IP estática, como una dirección IP de vínculo local como 169.254.1.1, el nombre de red de clúster para poner el nombre de red de clúster. Para simplificar este proceso, vea [Configurar Windows clúster en Azure para siempre en grupos de disponibilidad](http://social.technet.microsoft.com/wiki/contents/articles/14776.configuring-windows-failover-cluster-in-windows-azure-for-alwayson-availability-groups.aspx).

Para obtener más información, vea [Configurar siempre en disponibilidad de grupos en Azure (gráfico)](virtual-machines-windows-portal-sql-alwayson-availability-groups.md).

### <a name="availability-group-listener-support"></a>Soporte de escucha del grupo de disponibilidad

Escucha de grupo de disponibilidad es compatibles en máquinas virtuales de Azure que ejecutan Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 y Windows Server 2016. Esta compatibilidad se realiza mediante el uso de equilibrio de carga extremos habilitado en las máquinas virtuales de Azure que son nodos de grupo de disponibilidad. Debe seguir los pasos de configuración especial para los agentes de escucha para que trabaje para ambas aplicaciones cliente que se ejecutan en Azure, así como los que ejecutan local.

Existen dos opciones principales para configurar la escucha: externo (público) o interno. La escucha externa (pública) usa un opuestas equilibrador de carga de internet y está asociada con un público IP Virtual (VIP) que se puede acceder a través de internet. Un detector interno utiliza un equilibrador de carga interno y solo admite clientes dentro de la misma red Virtual. Para cualquiera tipo equilibrador de carga, debe habilitar volver directa de servidor. 

Si el grupo de disponibilidad abarca varias subredes Azure (por ejemplo, una implementación que cruza regiones Azure), debe incluir la cadena de conexión de cliente "**MultisubnetFailover = True**". El resultado de intentos de conexión paralelo a las réplicas en las diferentes subredes. Para obtener instrucciones sobre cómo configurar un agente de escucha, vea

- [Configurar un detector de ILB para siempre en disponibilidad de los grupos de Azure](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md).
- [Configurar una escucha externa para siempre en disponibilidad de los grupos en Azure](virtual-machines-windows-classic-ps-sql-ext-listener.md).

Aún puede conectarse a cada réplica de disponibilidad por separado conectándose directamente a la instancia de servicio. Además, puesto que siempre en grupos de disponibilidad son compatibles con la base de datos que coinciden con los clientes, puede conectarse a las réplicas disponibilidad como reflejo a socios como las réplicas se configuran similar a la creación de reflejos de base de datos de la base de datos:

- Una réplica principal y una réplica secundaria

- La réplica secundaria está configurada como no se puede leer (**Legible secundario** opción establecida en **No**)

Un ejemplo de cadena de conexión de cliente que corresponde a esta configuración como reflejo de base de datos con ADO.NET o SQL Server Native Client está por debajo de:

    Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;

Para obtener más información sobre la conectividad de cliente, consulte:

- [Uso de palabras clave de cadena de conexión con SQL Server Native Client](https://msdn.microsoft.com/library/ms130822.aspx)
- [Conectar a clientes a una base de datos que coinciden con sesión (SQL Server)](https://technet.microsoft.com/library/ms175484.aspx)
- [Conectarse a grupo disponibilidad escucha en híbrida de TI](http://blogs.msdn.com/b/sqlalwayson/archive/2013/02/14/connecting-to-availability-group-listener-in-hybrid-it.aspx)
- [Escucha de grupo de disponibilidad, la conectividad de cliente y aplicación dé (SQL Server)](https://technet.microsoft.com/library/hh213417.aspx)
- [Usar las cadenas de conexión que coinciden con la base de datos con grupos de disponibilidad](https://technet.microsoft.com/library/hh213417.aspx)

### <a name="network-latency-in-hybrid-it"></a>Latencia de la red de TI híbridos

Debe implementar la solución HADR con la suposición de que es posible que existan períodos de tiempo con la red de alta latencia entre su red local y Azure. Al implementar réplicas en Azure, debe usar confirmación asincrónica en lugar de ejecutar sincrónico para el modo de sincronización. Al implementar la base de datos que coinciden con los servidores locales tanto en Azure, use el modo de alto rendimiento en lugar de en el modo de alta seguridad.

### <a name="geo-replication-support"></a>Compatibilidad con la replicación de geo

Replicación de geo en Azure discos no admite el archivo de datos y el archivo de registro de la misma base de datos se almacenan en discos independientes. GRS aplica cambios en cada disco por separado y asincrónica. Este mecanismo garantiza que el orden de escritura en un disco en la copia replicada geo, pero no a través de replicar geo copias de varios discos. Si configura una base de datos para almacenar su archivo de datos y su archivo de registro en discos independientes, los discos recuperados después de un desastre pueden contener una copia actualizada del archivo de datos que el archivo de registro que se divide el registro de escritura anticipada en SQL Server y las propiedades ACID de las transacciones. Si no tiene la opción para deshabilitar la replicación de geo en la cuenta de almacenamiento, debe conservar todos los datos de archivos de registro y base de datos en el mismo disco. Si debe usar más de un disco debido al tamaño de la base de datos, debe implementar una de las soluciones de recuperación de desastres enumeradas anteriormente para garantizar la redundancia de datos.

## <a name="next-steps"></a>Pasos siguientes

Si necesita crear una máquina virtual Azure con SQL Server, consulte [el aprovisionamiento de un equipo Virtual con SQL Server en Azure](virtual-machines-windows-portal-sql-server-provision.md).

Para obtener el mejor rendimiento de SQL Server en una máquina virtual de Azure, consulte la Guía de [Prácticas recomendadas de rendimiento para SQL Server en Azure máquinas virtuales de Windows](virtual-machines-windows-sql-performance.md).

Para otros temas relacionados con la ejecución de SQL Server en máquinas virtuales de Azure, consulte [SQL Server en Azure máquinas virtuales de Windows](virtual-machines-windows-sql-server-iaas-overview.md).

### <a name="other-resources"></a>Otros recursos

- [Instalar un nuevo bosque de Active Directory en Azure](../active-directory/active-directory-new-forest-virtual-machine.md)
- [Crear clúster WSFC para siempre en los grupos de disponibilidad de Azure VM](http://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a)

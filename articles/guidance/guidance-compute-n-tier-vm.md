<properties
   pageTitle="Ejecución de máquinas virtuales de Windows para una arquitectura de N niveles | Arquitectura de referencia | Microsoft Azure"
   description="Cómo implementar una arquitectura de múltiples niveles en Azure, pago atención especial a la disponibilidad, seguridad, escalabilidad y seguridad de facilidad de uso."
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/20/2016"
   ms.author="mwasson"/>

# <a name="running-windows-vms-for-an-n-tier-architecture-on-azure"></a>Ejecución de máquinas virtuales de Windows para una arquitectura de N niveles en Azure

> [AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

> [AZURE.SELECTOR]
- [Ejecución de máquinas virtuales de Linux para una arquitectura de N niveles en Azure](guidance-compute-n-tier-vm-linux.md)
- [Ejecución de máquinas virtuales de Windows para una arquitectura de N niveles en Azure](guidance-compute-n-tier-vm.md)

En este artículo se describe un conjunto de prácticas para ejecutar máquinas virtuales de Windows (VM) para una aplicación con una arquitectura de capas. Se basa en el artículo de [varios VM en Azure][multi-vm].

> [AZURE.NOTE] Azure tiene dos diferentes modelos de implementación: [El Administrador de recursos] [ resource-manager-overview] y clásica. Este artículo, se utiliza el Administrador de recursos, que Microsoft recomienda para implementaciones nuevas.

## <a name="architecture-diagram"></a>Diagrama de arquitectura

Hay variaciones de arquitecturas de N niveles. La mayor parte las diferencias no es relevante para los fines de estas recomendaciones. Este artículo presupone una aplicación web de nivel 3 típica:

- **Nivel de Web.** Controladores de solicitudes HTTP entrantes. Las respuestas se devuelven a través de este nivel.

- **Nivel de empresa.** Procesos de negocio implementa y otra lógica funcional para el sistema.

- **Nivel de base de datos.** Proporciona almacenamiento de datos persistentes, uso de [SQL Server siempre en disponibilidad de grupos de] [ sql-alwayson] de alta disponibilidad.

> Un documento de Visio que incluye este diagrama de arquitectura está disponible para su descarga en el [Centro de descarga de Microsoft][visio-download]. Este diagrama se encuentra en la "cálculo - página de múltiples niveles (Windows).

![[0]][0]

- **Conjuntos de disponibilidad.** Crear un [Conjunto de disponibilidad] [ azure-availability-sets] para cada nivel y aprovisionar al menos dos máquinas virtuales en cada nivel. Este enfoque es necesario para llegar a la disponibilidad [SLA] [ vm-sla] para máquinas virtuales.

- **Subredes.** Crear una subred independiente para cada nivel. Especifique la dirección rango y máscara de subred utilizando la notación [CIDR] . 

- **Equilibradores de carga.** Utilizar un [equilibrador de carga a través de Internet] [ load-balancer-external] para distribuir el tráfico de Internet entrante para el nivel de web y un [equilibrador de carga interno] [ load-balancer-internal] para distribuir el tráfico de red del nivel de web al nivel de empresa.

- **Jumpbox**. Un _jumpbox_, que también se denomina [host de baluarte]es una máquina virtual en la red que utilizan los administradores para conectarse a las otras máquinas virtuales. El jumpbox tiene un GSN que permite tráfico remoto únicamente de las direcciones IP públicas en la lista blanca. La GSN debe permitir el tráfico de escritorio remoto (RDP).

- **Supervisión**. Software de supervisión como [Nagios], [Zabbix]o [Icinga] puede dar una perspectiva de tiempo de respuesta, el tiempo de actividad de la máquina virtual y el estado general del sistema. Instale el software de supervisión en una máquina virtual que se coloca en una subred independiente de administración.

- **NSGs**. Usar [grupos de seguridad de red] [ nsg] (NSGs) para restringir el tráfico de red dentro de la VNet. Por ejemplo, en la arquitectura de 3 niveles que se muestra aquí, el nivel de la base de datos no acepta el tráfico de la web front-end, solo desde el nivel empresarial y la subred de administración.

- **SQL Server siempre en el grupo de disponibilidad.** Proporciona alta disponibilidad en el nivel de datos, por lo que permite la replicación y migración tras error.

- **Servidores de active Directory Domain Services (AD DS)**. Servicios de dominio de Active Directory (AD DS) almacena datos de directorio y administra la comunicación entre usuarios y dominios, incluidos los procesos de inicio de sesión de usuario, autenticación y búsquedas de directorio. Un controlador de dominio de Active Directory es un servidor que ejecuta AD DS. Antes de Windows Server 2016, siempre en grupos de disponibilidad debe estar unidos a un dominio. Esto es porque los grupos de disponibilidad dependen de la tecnología de clúster de migración tras error de servidor de Windows (WSFC). Windows Server 2016 presenta la capacidad para crear un clúster de migración tras error sin Active Directory. Para obtener más información, consulte [Novedades de conmutación en Windows Server 2016][wsfc-whats-new]

## <a name="recommendations"></a>Recomendaciones

Azure ofrece numerosos recursos distintos y tipos de recursos, por lo que puede ser esta arquitectura de referencia se aprovisione varias formas diferentes. Proporcionamos una plantilla de administrador de recursos de Azure para instalar la arquitectura de referencia que sigue estas recomendaciones. Si elige crear su propia arquitectura de referencia siguiendo estas recomendaciones, a menos que tenga un requisito específico que no se ajusta una recomendación.

### <a name="vnet--subnets"></a>VNet / subredes

Cuando se crea la VNet, asignar suficiente espacio de direcciones para las subredes que debe. Especifique la VNet dirección rango y máscara de subred utilizando la notación [CIDR] . Usar un espacio de direcciones que esté dentro de los [bloques de direcciones IP privados]de estándar[private-ip-space], que son 10.0.0.0/8, 172.16.0.0/12 y 192.168.0.0/16.

Elija un intervalo de direcciones que no se superpone con su red local, en caso de necesite configurar una puerta de enlace entre la VNet y su red local más adelante. Una vez creado el VNet, no puede cambiar el intervalo de direcciones.

Diseñar subredes con los requisitos de seguridad y funcionalidad en cuenta. Todas las máquinas virtuales dentro del mismo nivel o función deben ir a la misma subred, que puede ser un límite de seguridad. Para obtener más información acerca del diseño de VNets y subredes, consulte [Plan y diseño redes virtuales de Azure][plan-network].

Para cada subred, especifique el espacio de direcciones para la subred en la notación CIDR. Por ejemplo, '10.0.0.0/24' crea un intervalo de 256 direcciones IP. (VM pueden usar 251 de estos; cinco están reservado. Consulte las [preguntas más frecuentes sobre una red Virtual][vnet faq].) Asegúrese de que no se superponen los intervalos de direcciones en subredes.

### <a name="network-security-groups"></a>Grupos de seguridad de red

Usar reglas de GSN para restringir el tráfico entre niveles. Por ejemplo, en la arquitectura de 3 niveles mostrada arriba, el nivel de web no se comunica directamente con el nivel de base de datos. Para exigir esto, el nivel de la base de datos debe bloquear el tráfico entrante desde la subred del nivel de web.  

  1. Crear un GSN y asociar a la subred de nivel de base de datos.

  2. Agregar una regla que deniegue todo el tráfico entrante de la VNet. (Utilice el `VIRTUAL_NETWORK` etiqueta en la regla.) 

  3. Agregar una regla con mayor prioridad que permite el tráfico entrante desde la subred de nivel empresarial. Esta regla invalida la regla anterior y permite el nivel de empresa a hablar con el nivel de la base de datos.

  4. Agregar una regla que permita el tráfico entrante desde dentro de la propia subred de nivel de base de datos. Esta regla permite la comunicación entre máquinas virtuales en el nivel de base de datos, que es necesario para la replicación de base de datos y migración tras error.

  5. Agregar una regla que permita el tráfico RDP desde la subred jumpbox. Esta regla permite a los administradores conectar con el nivel de base de datos de la jumpbox.

  > [AZURE.NOTE] Un GSN tiene [reglas predeterminadas] [ nsg-rules] que permitir el tráfico entrante desde dentro de la VNet. No se puede eliminar estas reglas, pero se puede reemplazar mediante la creación de reglas de mayor prioridad.

### <a name="load-balancers"></a>Equilibradores de carga

Equilibrador de carga externo distribuye el tráfico de Internet para el nivel de web. Crear una dirección IP pública para esta equilibrador de carga. Consulte [crear un equilibrador de carga a través de Internet][lb-external-create].

Equilibrador de carga interno distribuye el tráfico de red del nivel de web al nivel de empresa. Para dar a una dirección IP privada de este equilibrador de carga, cree una configuración de IP de front-end y asociar a la subred para el nivel de empresa. Vea [empezar a crear un equilibrador de carga interno][lb-internal-create].

### <a name="sql-server-always-on-availability-groups"></a>SQL Server siempre en grupos de disponibilidad

Se recomienda [Siempre en grupos de disponibilidad] [ sql-alwayson] para alta disponibilidad de SQL Server. Siempre en grupos de disponibilidad requieren un controlador de dominio. Todos los nodos en el grupo de disponibilidad deben estar en el mismo dominio de Active Directory.

Otros niveles de conectan a la base de datos a través de un [agente de escucha de disponibilidad grupo][sql-alwayson-listeners]. La escucha permite a un cliente SQL para conectarse sin necesidad de conocer el nombre de la instancia de SQL Server física. Máquinas virtuales que la base de datos de access se debe unirse al dominio. El cliente (en este caso, otro nivel) utiliza DNS para resolver el nombre de red virtual de la escucha en las direcciones IP.

Configurar SQL Server siempre en como sigue:

- Crear un clúster de clúster de migración tras error de servidor de Windows (WSFC) y un grupo de disponibilidad de SQL Server siempre en. Para obtener más información, vea [Introducción a siempre en grupos de disponibilidad][sql-alwayson-getting-started].

- Crear un equilibrador de carga interno con una dirección IP privada estática.

- Crear una escucha de grupo de disponibilidad y la asignación de nombre DNS de la escucha a la dirección IP de un equilibrador de carga interno. 

- Crear una regla de equilibrador de carga para el puerto de escucha de SQL Server (el puerto TCP 1433 de forma predeterminada). La regla de equilibrador de carga debe habilitar _IP flotante_, también denominado servidor directa devolver. Hace que la máquina virtual responder directamente al cliente, lo que permite una conexión directa a la réplica principal.

    > [AZURE.NOTE] Cuando flotante IP está habilitado, el número de puerto front-end debe ser el mismo que el número de puerto de back-end en la regla de equilibrador de carga.

Cuando un cliente SQL intenta conectarse, el equilibrador de carga enruta la solicitud de conexión a la réplica principal actual. Si hay una migración tras error a otra réplica, el equilibrador de carga enruta automáticamente las solicitudes subsiguientes a la nueva réplica principal. Para obtener más información, vea [Configurar equilibrador de carga para SQL siempre en][sql-alwayson-ilb].

En caso de error, conexiones de cliente existentes estén cerradas. Una vez completada la migración tras error, nuevas conexiones se se dirige a la nueva réplica principal.

Si su aplicación realiza significativamente más lecturas que escribe, puede descargar algunas de las consultas de solo lectura a una réplica secundaria. Consulte [con un agente de escucha para conectarse a un secundario de solo lectura réplica (enrutamiento de solo lectura)][sql-alwayson-read-only-routing].

Probar la implementación forzar [un error manual][sql-alwayson-force-failover].

### <a name="jumpbox"></a>Jumpbox

No permitir el acceso RDP desde Internet a las máquinas virtuales que se ejecutan la carga de trabajo de la aplicación. En su lugar, todo el acceso a estas máquinas virtuales RDP/SSH debe proceder a través de la jumpbox. Un administrador inicia sesión en la jumpbox y, a continuación, inicie sesión en la otra máquina virtual de la jumpbox. El jumpbox permite tráfico RDP desde Internet, pero solo de las direcciones IP de conocidos, en la lista blanca.

Coloque el jumpbox en el mismo VNet como las otras máquinas virtuales, pero en una subred independiente de administración.

Crear una [dirección IP pública] para la jumpbox.

Usar un tamaño VM pequeño para jumpbox, como A1 estándar.

Configurar la NSGs para el nivel de web, nivel empresarial y subredes de nivel de base de datos para que admita el tráfico administrativo (RDP) pasar a través de la subred de administración.

Para proteger la jumpbox, cree un GSN y aplicarlo a la subred jumpbox. Agregar una regla de GSN que permita las conexiones RDP sólo de un conjunto de lista blanca de direcciones IP públicas.

La GSN puede estar asociado a la subred o a la jumpbox NIC. En este caso, se recomienda asociar a la NIC para que tráfico RDP únicamente se admitirá la jumpbox, aunque agregar otras máquinas virtuales a la misma subred.

## <a name="availability-considerations"></a>Consideraciones de disponibilidad

Coloque cada nivel o el rol de VM en un conjunto de disponibilidad independiente. No poner máquinas virtuales de diferentes niveles en el mismo conjunto de disponibilidad. 

En el nivel de la base de datos, tiene varias VM no convierte automáticamente en una base de datos altamente disponible. Para una base de datos relacional, normalmente debe utilizar replicación y migración tras error para lograr una alta disponibilidad. Para SQL Server, se recomienda utilizar [Siempre en grupos de disponibilidad][sql-alwayson]. 

Si necesita mayor disponibilidad que el [SLA de Azure para máquinas virtuales] [ vm-sla] proporciona, replicar la aplicación a través de dos regiones y usar el Administrador de tráfico de Azure para migración tras error. Para obtener más información, vea [Ejecutar máquinas virtuales de Windows en varias áreas para alta disponibilidad][multi-dc].   

## <a name="security-considerations"></a>Consideraciones de seguridad

Cifrar datos almacenados. Usar [Azure clave depósito] [ azure-key-vault] para administrar las claves de cifrado de base de datos. Depósito de clave puede almacenar las claves de cifrado en los módulos de seguridad de hardware (HSM). Para obtener más información, vea [Configurar Azure clave depósito de integración de SQL Server en máquinas virtuales de Azure] [ sql-keyvault] también se recomienda para almacenar información confidencial de la aplicación, como cadenas de conexión de base de datos, en depósito de clave.

Considere la posibilidad de agregar un dispositivo de red virtual (NVA) para crear una DMZ entre Internet pública y la red virtual Azure. NVA es un término genérico para un dispositivo virtual que puede realizar tareas relacionadas con la red como firewall, inspección de paquetes, auditoría, enrutamiento personalizado o una variedad de otras operaciones. Para obtener más información, vea [implementar una DMZ entre Azure e Internet][dmz].

## <a name="scalability-considerations"></a>Consideraciones de escalabilidad

Equilibradores de carga distribución el tráfico de red a los niveles de web y empresa. Ajustar tamaño horizontalmente agregando nuevas instancias de máquina virtual. Tenga en cuenta que puede escalar los niveles de web y empresa independientemente, en función de la carga. Para reducir posibles complicaciones causadas por la necesidad de mantener la afinidad del cliente, las máquinas virtuales en el nivel de web deben ser independientes. Las VM hospedaje la lógica empresarial también deben ser independientes.

## <a name="manageability-considerations"></a>Consideraciones de capacidad de administración

Simplificar la administración de todo el sistema mediante herramientas de administración centralizada como [Automatización de Azure][azure-administration], [Conjunto de aplicaciones de administración de operaciones de Microsoft][operations-management-suite], [Chef][chef], o [posición libre][puppet]. Estas herramientas pueden consolidar información de diagnóstico y estado capturada en varias máquinas virtuales para proporcionar una visión general del sistema.

## <a name="solution-deployment"></a>Implementación de soluciones

Una implementación de una arquitectura de referencia que implementa estas recomendaciones está disponible en [Github][github-folder]. Esta arquitectura de referencia incluye un nivel de web, nivel empresarial, un nivel de datos, así como jumpbox VM y Active Directory controladores de dominio.

La arquitectura de referencia se puede implementar siguiendo las instrucciones siguientes: 

1. Haga clic en el botón siguiente.  
[! ["Implementar en Azure"] [1]][2]

2. Una vez que ha abierto el vínculo en el portal de Azure, escriba los valores siguientes: 
  - El nombre del **grupo de recursos** ya está definido en el archivo de parámetros, así que seleccione **Crear nuevo** y escriba `ra-ntier-sql-network-rg` en el cuadro de texto.
  - Seleccione la región en el cuadro desplegable de **ubicación** .
  - No modifique la **Plantilla raíz Uri** o los cuadros de texto de **Uri raíz del parámetro** .
  - Revise los términos y condiciones y luego haga clic en la casilla de verificación **que acepto los términos y condiciones indicadas arriba** .
  - Haga clic en el botón de **compra** .

3. Comprobar Azure notificación portal para un mensaje de la implementación está completada.

4. Haga clic en el botón siguiente.  
[! ["Implementar en Azure"] [1]][3]

5. Una vez que ha abierto el vínculo en el portal de Azure, escriba los valores siguientes: 
  - El nombre del **grupo de recursos** ya está definido en el archivo de parámetros, así que seleccione **Usar existente** y escriba `ra-ntier-sql-workload-rg` en el cuadro de texto.
  - Seleccione la región en el cuadro desplegable de **ubicación** .
  - No modifique la **Plantilla raíz Uri** o los cuadros de texto de **Uri raíz del parámetro** .
  - Revise los términos y condiciones y luego haga clic en la casilla de verificación **que acepto los términos y condiciones indicadas arriba** .
  - Haga clic en el botón de **compra** .

6. Comprobar Azure notificación portal para un mensaje de la implementación está completada.

7. Haga clic en el botón siguiente.  
[! ["Implementar en Azure"] [1]][4]

8. Una vez que ha abierto el vínculo en el portal de Azure, escriba los valores siguientes: 
  - El nombre del **grupo de recursos** ya está definido en el archivo de parámetros, así que seleccione **Crear nuevo** y escriba `ra-ntier-sql-network-rg` en el cuadro de texto.
  - Seleccione la región en el cuadro desplegable de **ubicación** .
  - No modifique la **Plantilla raíz Uri** o los cuadros de texto de **Uri raíz del parámetro** .
  - Revise los términos y condiciones y luego haga clic en la casilla de verificación **que acepto los términos y condiciones indicadas arriba** .
  - Haga clic en el botón de **compra** .

9. Comprobar Azure notificación portal para un mensaje de la implementación está completada.

10. Los archivos de parámetro incluyen una modificable nombres de usuario de administrador y las contraseñas y se recomienda cambiar inmediatamente en todas las máquinas virtuales. Haga clic en cada VM en el Portal de Azure, a continuación, haga clic en **Restablecer la contraseña** en el módulo de **compatibilidad + solución de problemas** . Seleccione **Restablecer la contraseña** en el cuadro de lista desplegable de **modo** , seleccione un nuevo **nombre de usuario** y **contraseña**. Haga clic en el botón **Actualizar** para conservar el nuevo nombre de usuario y la contraseña. 

Para obtener información sobre maneras adicionales para implementar la arquitectura de referencia, vea el archivo Léame de la [orientación de la vm solo] [ github-folder] Github carpeta. 

## <a name="next-steps"></a>Pasos siguientes

Para lograr alta disponibilidad para esta arquitectura de referencia, se recomienda [implementar para varias regiones][multi-dc].

<!-- links -->

[arm-templates]: https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/
[azure-administration]: ../automation/automation-intro.md
[azure-audit-logs]: ../resource-group-audit.md
[azure-availability-sets]: ../virtual-machines/virtual-machines-windows-manage-availability.md#configure-each-application-tier-into-separate-availability-sets
[azure-cli]: ../virtual-machines-command-line-tools.md
[azure-key-vault]: https://azure.microsoft.com/services/key-vault.md
[azure-load-balancer]: ../load-balancer/load-balancer-overview.md
[host bastión]: https://en.wikipedia.org/wiki/Bastion_host
[CIDR]: https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing
[chef]: https://www.chef.io/solutions/azure/
[dmz]: guidance-iaas-ra-secure-vnet-dmz.md
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier-sql
[lb-external-create]: ../load-balancer/load-balancer-get-started-internet-portal.md
[lb-internal-create]: ../load-balancer/load-balancer-get-started-ilb-arm-portal.md
[load-balancer-external]: ../load-balancer/load-balancer-internet-overview.md
[load-balancer-internal]: ../load-balancer/load-balancer-internal-overview.md
[multi-dc]: guidance-compute-multiple-datacenters.md
[multi-vm]: guidance-compute-multi-vm.md
[n-tier]: guidance-compute-n-tier-vm.md
[naming conventions]: guidance-naming-conventions.md
[nsg]: ../virtual-network/virtual-networks-nsg.md
[nsg-rules]: ../best-practices-resource-manager-security.md#network-security-groups
[operations-management-suite]: https://www.microsoft.com/en-us/server-cloud/operations-management-suite/overview.aspx
[plan-network]: ../virtual-network/virtual-network-vnet-plan-design-arm.md
[private-ip-space]: https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces
[dirección IP pública]: ../virtual-network/virtual-network-ip-addresses-overview-arm.md
[puppet]: https://puppetlabs.com/blog/managing-azure-virtual-machines-puppet
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[sql-alwayson]: https://msdn.microsoft.com/en-us/library/hh510230.aspx
[sql-alwayson-force-failover]: https://msdn.microsoft.com/en-us/library/ff877957.aspx
[sql-alwayson-getting-started]: https://msdn.microsoft.com/en-us/library/gg509118.aspx
[sql-alwayson-ilb]: https://blogs.msdn.microsoft.com/igorpag/2016/01/25/configure-an-ilb-listener-for-sql-server-alwayson-availability-groups-in-azure-arm/
[sql-alwayson-listeners]: https://msdn.microsoft.com/en-us/library/hh213417.aspx
[sql-alwayson-read-only-routing]: https://technet.microsoft.com/en-us/library/hh213417.aspx#ConnectToSecondary
[sql-keyvault]: ../virtual-machines/virtual-machines-windows-ps-sql-keyvault.md
[vm-planned-maintenance]: ../virtual-machines/virtual-machines-windows-planned-maintenance.md
[vm-sla]: https://azure.microsoft.com/en-us/support/legal/sla/virtual-machines
[vnet faq]: ../virtual-network/virtual-networks-faq.md
[wsfc-whats-new]: https://technet.microsoft.com/windows-server-docs/failover-clustering/whats-new-in-failover-clustering
[Nagios]: https://www.nagios.org/
[Zabbix]: http://www.zabbix.com/
[Icinga]: http://www.icinga.org/
[VM-sizes]: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
[solution-script]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/Deploy-ReferenceArchitecture.ps1
[solution-script-bash]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/deploy-reference-architecture.sh
[vnet-parameters-windows]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/windows/virtualNetwork.parameters.json
[vnet-parameters-linux]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/linux/virtualNetwork.parameters.json
[nsg-parameters-windows]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/windows/networkSecurityGroups.parameters.json
[nsg-parameters-linux]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/linux/networkSecurityGroups.parameters.json
[webtier-parameters-windows]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/windows/webTier.parameters.json
[webtier-parameters-linux]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/linux/webTier.parameters.json
[businesstier-parameters-windows]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/windows/businessTier.parameters.json
[businesstier-parameters-linux]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/linux/businessTier.parameters.json
[datatier-parameters-windows]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/windows/dataTier.parameters.json
[datatier-parameters-linux]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/linux/dataTier.parameters.json
[azure-powershell-download]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[0]: ./media/blueprints/compute-n-tier.png "Arquitectura de N niveles con Microsoft Azure"
[1]: ./media/blueprints/deploybutton.png 
[2]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-compute-n-tier-sql%2FvirtualNetwork.azuredeploy.json
[3]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-compute-n-tier-sql%2Fworkload.azuredeploy.json
[4]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-compute-n-tier-sql%2Fsecurity.azuredeploy.json
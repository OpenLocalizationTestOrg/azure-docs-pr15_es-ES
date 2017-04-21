<properties
    pageTitle="Diseño de la infraestructura de red para recuperación | Microsoft Azure"
    description="Este artículo describe las consideraciones de diseño de red para la recuperación de sitio de Azure"
    services="site-recovery"
    documentationCenter=""
    authors="prateek9us"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="09/19/2016"
    ms.author="pratshar"/>

#  <a name="designing-your-network-infrastructure-for-disaster-recovery"></a>Diseño de la infraestructura de red para recuperación

En este artículo está dirigido a profesionales de TI que son responsables de diseñar, implementar y auxiliares continuidad empresarial e infraestructura de recuperación (BCDR), y que quiere aprovechar Microsoft Azure sitio recuperación (ASR) para soporte y mejorar sus servicios BCDR. Este artículo trata consideraciones prácticas de implementación de servidor de administrador de máquina Virtual de System Center, las ventajas y desventajas de subredes extendidos frente subred migración tras error y cómo estructurar recuperación a sitios virtuales de Microsoft Azure.

## <a name="overview"></a>Información general

[Recuperación de sitio de Azure (ASR)](https://azure.microsoft.com/services/site-recovery/) es un servicio de Microsoft Azure que organiza la protección y la recuperación de las aplicaciones virtualizadas con fines empresariales continuidad desastres recuperación (BCDR). Este documento está pensado para guiar al lector a través del proceso de diseño de las redes, centrarse en crear intervalos de IP y subredes en el sitio de recuperación de desastres, al replicar máquinas virtuales (VM) mediante el sitio de recuperación.

Además, en este artículo se explica cómo sitio recuperación permite diseñar e implementar un centro de datos virtual multisitio para admitir servicios BCDR en tiempo de prueba o desastres.

En un mundo donde todos los usuarios espera 24/7 conectividad, es más importante que nunca para mantener la infraestructura y las aplicaciones y en funcionamiento. El propósito de continuidad empresarial y recuperación de desastres (BCDR) es restaurar los componentes de errores para que la organización puede reanudar rápidamente el funcionamiento normal. Desarrollo de estrategias para tratar los eventos devastadoras poco probable es muy difícil. Esto es debido a la dificultad inherente de predecir el futuro, especialmente en relación con el alto costo para proporcionar las medidas adecuadas de protección contra catástrofes alcance y eventos improbable. 

Crucial para BCDR planificación, objetivo de tiempo de recuperación (RTO) y objetivo de punto de recuperación (RPO) deben definirse como parte de un plan de recuperación. Cuando produzca un desastre centro de datos del cliente, mediante la recuperación de sitio de Azure, los clientes pueden rápidamente (bajo RTO) poner en línea sus máquinas virtuales replicadas situados en el centro de datos secundario o en Microsoft Azure con pérdida mínima de datos (RPO baja). 

Migración tras error se realiza mediante ASR que inicialmente copia designado máquinas virtuales de Windows desde el centro de datos principal en el centro de datos secundario o en Azure (según el escenario) y, a continuación, se actualiza periódicamente las réplicas. Durante la planeación de infraestructura, diseño de red se debe considerar como botella potencial que puede impedir que se de compañía reunión RTOs y RPOs..  

Cuando planeación los administradores a implementar una solución de recuperación de desastres, una de las preguntas clave en sus talentos es cómo la máquina virtual sería accesible una vez completada la migración tras error. ASR permite al administrador elegir la red a la que podría estar conectada a una máquina virtual después de la migración. Si un servidor VMM administra el sitio primario, a continuación, esto se logra utilizando la asignación de red. Para obtener más detalles, consulte [prepararse para la asignación de red](site-recovery-network-mapping.md) .

Al diseñar la red para el sitio de recuperación, el administrador tiene dos opciones:

- Utilizar un intervalo de direcciones IP diferentes de red al sitio de recuperación. En este escenario, la máquina virtual después de la migración se proporciona una nueva dirección IP y el administrador tiene que hacer una actualización de DNS. Obtenga más información sobre cómo hacer el DNS actualización [aquí](site-recovery-vmm-to-vmm.md#test-your-deployment) 
- Use el mismo intervalo de direcciones IP de la red en el sitio de recuperación. En algunos escenarios de los administradores prefieren conservar las direcciones IP que tienen en el sitio primario incluso después del error. En un escenario normal, un administrador tendría que actualizar las rutas para indicar la nueva ubicación de las direcciones IP. Pero, en el escenario donde se implementa una VLAN extendida entre el principal y los sitios de recuperación, que mantiene las direcciones IP de los equipos virtuales se convierte en una opción atractiva. Mantener la misma IP direcciones simplifica el proceso de recuperación eliminando cualquier red relacionados con los pasos de la migración tras error de publicación.


Cuando planeación los administradores a implementar una solución de recuperación de desastres, una de las preguntas clave en su cuenta es cómo las aplicaciones estará disponibles una vez completada la migración tras error. Las aplicaciones modernas casi siempre dependen de las redes hasta cierto punto, así que física mover que un servicio de un sitio a otro representa un reto red. Hay dos formas principales de que este problema se trata en soluciones de recuperación de desastres. El primer enfoque es mantener direcciones IP fijas. A pesar de los servicios de movimiento y los servidores de hospedaje en distintas ubicaciones físicas, aplicaciones tomar la configuración de direcciones IP con ellos a la nueva ubicación. El segundo método implica cambiar la dirección IP durante la transición en el sitio recuperado por completo. Cada enfoque tiene varias variaciones de implementación que se resumen debajo.

Al diseñar la red para el sitio de recuperación, el administrador tiene dos opciones:

## <a name="option-1-retain-ip-addresses"></a>Opción 1: Conservar las direcciones IP 

Desde una perspectiva de proceso de recuperación de desastres, mediante IP fijo direcciones parece ser el método más fácil de implementar, pero tiene una serie de retos potenciales que, en la práctica, asegúrese de el enfoque menos popular. Recuperación de sitio de Azure proporciona la capacidad para conservar las direcciones IP de todos los escenarios. Antes de que decide conservar IP, debe otorgar pensamiento correspondiente a las restricciones que impone a las capacidades de migración tras error. Veamos los factores que pueden ayudarle a decidir si desea conservar las direcciones IP, o no. Esto se puede lograr de dos maneras, usando una subred extendida o siguiendo un error de subred completa.

### <a name="stretched-subnet"></a>Subred extendido

Aquí la subred estará disponible en principal y DR ubicaciones al mismo tiempo. En términos sencillos, esto significa puede mover un servidor y su configuración IP (capa 3) para el segundo sitio y la red enruta el tráfico a la nueva ubicación automáticamente. Esto es muy fácil de atender desde la perspectiva del servidor, pero tiene una serie de retos:

- Desde una perspectiva de nivel 2 (capa de vínculo de datos), se requiere un equipo de redes que puede administrar una VLAN extendida, pero esto se ha convertido en menos de un problema como ahora está esté disponible. El problema de segundo y más difícil es que si ajusta la VLAN dominio posibles errores se extiende a ambos sitios esencialmente convertirse en un punto único de error. Aunque es probable que suceda, ha ocurrido que una difusión tormenta iniciado pero no se aislará. Se han encontrado mixtos opiniones sobre este último problema y se han visto muchas implementaciones correctas así como "no se implementará nunca esta tecnología aquí".
- No es posible si está utilizando Microsoft Azure como el sitio de DR subred extendido.


### <a name="subnet-failover"></a>Subred migración tras error

Es posible implementar la migración tras error de subred para obtener los beneficios de la solución de subred extendido descrito anteriormente sin expandir la subred en varios sitios. Aquí cualquier subred determinada sería presente en el sitio de 1 o 2 del sitio, pero nunca en ambos sitios simultáneamente. Para mantener el espacio de direcciones IP en el caso de un error, es posible organizar mediante programación para que la infraestructura de enrutador mover las subredes desde un sitio a otro. En un escenario de migración tras error que mueven las subredes con el asociado protegido máquinas virtuales. La principal desventaja de este enfoque es si se produce un error que tenga que mover la subred toda, lo que puede aceptar, pero puede afectar a las consideraciones de detalle de la migración tras error. 

Examinemos cómo una empresa ficticia denominada Contoso es pueden replicar sus máquinas virtuales a una ubicación de recuperación mientras error sobre la subred completa. Primero veamos cómo Contoso es capaz de administrar sus subredes mientras replicar VM entre dos local ubicaciones y, a continuación, trataremos cómo subred migración tras error funciona cuando [que se usa Azure como el sitio de recuperación de desastres](#failover-to-azure).

#### <a name="failover-to-a-secondary-on-premises-site"></a>Migración tras error a un sitio secundario en local

Veamos un escenario donde queremos conservar la dirección IP de cada una de las máquinas virtuales y conmutar la subred completa en conjunto. El sitio primario tiene aplicaciones que se ejecutan en subred 192.168.1.0/24. Cuando se produce la migración tras error, todas las máquinas virtuales que forman parte de esta subred error sobre el sitio de recuperación y conservar sus direcciones IP. Rutas tendrán que adecuadamente modificarse para reflejar el hecho de que todas las máquinas virtuales que pertenecen a la subred 192.168.1.0/24 ahora se han movido al sitio de recuperación. 

En la siguiente ilustración, las rutas entre sitio principal y el sitio de recuperación, tercer sitio y sitio primario y tercer sitio y recuperación tendrá que modificarse adecuadamente. 

Las siguientes imágenes muestran las subredes antes de la migración tras error. Subred 192.168.0.1/24 está activo en el sitio principal antes de la migración tras error y activo del sitio de recuperación después del error 

![Antes de la migración tras error](./media/site-recovery-network-design/network-design2.png)

Antes de la migración tras error


La imagen siguiente muestra las redes y subredes después de la migración.
    
![Después de la migración](./media/site-recovery-network-design/network-design3.png)

Después de la migración

En el sitio secundario es local y usa un servidor VMM para administrarlo, al habilitar la protección de una máquina virtual específica, ASR asignará recursos de red según el flujo de trabajo siguiente:

- ASR asigna una dirección IP para cada interfaz de red en la máquina virtual del grupo de direcciones IP estático definido en la red correspondiente para cada instancia de System Center VMM.
- Si el administrador define el mismo conjunto de direcciones IP de la red en el sitio de recuperación que del grupo de direcciones IP de la red en el sitio primario, mientras que asignar la dirección IP a la máquina virtual de réplica ASR podría asignar la misma dirección IP que el de la máquina virtual principal.  La dirección IP está reservada en VMM pero no ha establecido como IP de migración tras error. IP Failover se establece justo antes de la migración tras error.

![Conservar la dirección IP](./media/site-recovery-network-design/network-design4.png)
    
Figura 5

Figura 5 muestra la configuración de migración tras error TCP/IP de la máquina virtual de réplica (en la consola de Hyper-V). Esta configuración se rellenará antes de la máquina virtual se inicia después de un error

Si la misma IP no está disponible, ASR podría asignar algunas direcciones IP disponibles desde el grupo de direcciones IP definido. 

Después de la máquina virtual está habilitada para la protección, que puede usar la siguiente secuencia de comandos para comprobar la dirección IP que se ha asignado a la máquina virtual. La misma IP se desea establecer como Failover IP y asignada a la máquina virtual en el momento de la migración tras error:

        $vm = Get-SCVirtualMachine -Name <VM_NAME>
        $na = $vm[0].VirtualNetworkAdapters>
        $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
        $ip.address  

>[AZURE.NOTE] En el escenario donde máquinas virtuales usar DHCP, la administración de direcciones IP es completamente fuera del control de ASR. Un administrador tiene que garantizar que haberlo servir las direcciones IP en el sitio de recuperación puede servir desde el mismo intervalo del sitio primario.

#### <a name="failover-to-azure"></a>Migración tras error en Azure

Recuperación de sitio de Azure (ASR) permite a Microsoft Azure que se usará como un sitio de recuperación de desastres para las máquinas virtuales. En este caso, deberá tratar una restricción más. 

Examinemos un escenario donde una empresa ficticia denominada Alzaga tiene infraestructura local que aloja sus aplicaciones de línea de negocio y aloja sus aplicaciones móviles en Azure. Conectividad entre máquinas virtuales de banco de Woodgrove en servidores de Azure y local se proporciona por una red privada Virtual de sitio a sitio (S2S) (VPN). S2S VPN permite una red virtual del banco Woodgrove en Azure a aparecer como una extensión de red de local del banco de Woodgrove. Esta comunicación se habilita mediante VPN S2S entre borde Alzaga y una red virtual Azure. Ahora Woodgrove desea utilizar ASR para replicar sus cargas de trabajo que se ejecuta en su centro de datos en Azure. Esta opción satisfaga las necesidades de Woodgrove, que quiere una opción de DR económica y puede almacenar datos en entornos de nube pública. Woodgrove tiene que tratar con aplicaciones y configuraciones que dependen de las direcciones IP de modificable, por lo tanto, tienen un requisito para conservar las direcciones IP de sus aplicaciones después del error sobre a Azure.

Se decidió asignar direcciones IP de intervalo de direcciones IP (172.16.1.0/24, 172.16.2.0/24) a sus recursos que se ejecuta en Azure.

Para que Woodgrove puedan replicar sus máquinas virtuales en Azure conservando las direcciones IP, es necesario crear una red Virtual de Azure. Debe ser una extensión de la red local para que las aplicaciones pueden alternar entre el sitio local a Azure sin problemas. Azure le permite agregar conectividad VPN de sitio a otro, así como sitio de punto a las redes virtuales creadas en Azure. Cuando configure la conexión de sitio a otro, red Azure le permite enrutar el tráfico a la ubicación local (Azure lo llama red local) solo si el intervalo de direcciones IP es diferente del intervalo de direcciones IP local, porque Azure no admite subredes estiramiento.  Esto significa que si tiene una subred 192.168.1.0/24 local, no puede agregar un 192.168.1.0/24 red local en la red de Azure. Esto ocurre porque Azure no sabe que no hay ningún VM activas en la subred y que está creando la subred solo con fines de DR. Para poder correctamente enrutar el tráfico de red fuera de una red de Azure las subredes de la red y la red local debe estar en conflicto. 

![Antes de la migración tras error de subred](./media/site-recovery-network-design/network-design7.png)

Antes de la migración tras error

Para ayudar a Woodgrove cumplir los requisitos empresariales, necesitamos implementar los siguientes flujos de trabajo:

- Crear una red adicional, deje que nosotros llámelo recuperación de red, donde se crearán las máquinas virtuales de error sobre.
- Para asegurarse de que la dirección IP de una máquina virtual se conserva después de un error, vaya a la ficha configurar en Propiedades de la máquina virtual, especifique la misma IP que la máquina virtual tiene local y, a continuación, haga clic en Guardar. Cuando es el error en la máquina virtual sobre recuperación de sitio de Azure asignará la dirección IP proporcionada en la máquina virtual. 

![Propiedades de red](./media/site-recovery-network-design/network-design8.png)

Una vez que la migración tras error se activa y las máquinas virtuales se crean en la red de recuperación con la dirección IP deseada, conectividad a esta red se puede establecer mediante una [Vnet a Vnet conexión](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md). Si es necesario que esta acción puede secuencias de comandos.  Como se ha comentado en la sección anterior sobre migración tras error de subred, incluso en el caso de migración tras error en Azure, rutas tendría que adecuadamente modificarse para reflejar que ese 192.168.1.0/24 ahora se ha movido a Azure. 

![Después de la migración de subred](./media/site-recovery-network-design/network-design9.png)

Después de la migración

Si no tiene una red de Azure' ' tal como se muestra en la imagen anterior. Puede crear una conexión vpn de sitio a sitio entre su 'Sitio principal' y 'Recuperación de red' después del error.  


## <a name="option-2-changing-ip-addresses"></a>Opción 2: Cambiar las direcciones IP

Este enfoque parece ser mayor frecuencia en función de lo que hemos visto. Toma la forma de cambiar la dirección IP de cada máquina virtual que está relacionado con la migración tras error. Una desventaja de este enfoque requiere la red entrante para 'más' que la aplicación que estaba en IPx está ahora en IPy. Incluso si IPx y IPy son nombres lógicos, entradas DNS suelen tengan que cambiar o vacían en toda la red y entradas de caché de tablas de red tienen actualizarse o vacían, por tanto, se puede ver un tiempo de inactividad dependiendo de cómo ha configurado la infraestructura DNS. Pueden mitigar estos problemas mediante los valores TTL mínimos en el caso de aplicaciones de intranet y [Administrador de tráfico de Azure con ASR](http://azure.microsoft.com/blog/2015/03/03/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/) para aplicaciones basadas en internet

### <a name="changing-the-ip-addresses---illustration"></a>Cambiar las direcciones IP - ilustración

Veamos el escenario donde va a usar varias direcciones IP en el principal y los sitios de recuperación. En el ejemplo siguiente se también hemos un tercer sitio desde donde se hospeda las aplicaciones en principal o recuperación pueden tener acceso a sitios.

![IP diferentes - antes de la migración tras error](./media/site-recovery-network-design/network-design10.png)

Figura 11

En la figura 11, hay algunas aplicaciones alojadas en subred 192.168.1.0/24 de subred en el sitio primario, y se han configurado para que se plantean en el sitio de recuperación en subred 172.16.1.0/24 después de un error. Rutas de red o conexiones de VPN se han configurado correctamente para que puedan acceder a los tres sitios entre sí.
 
Como en la figura 12 muestra, después del error sobre una o más aplicaciones, se restauran en la subred de recuperación. En este caso, no estamos restringidos para la recuperación ante la subred toda al mismo tiempo. Cambios no son necesarios para configurar las rutas de red o VPN. Un error y algunas actualizaciones DNS se asegurará de que las aplicaciones permanecen accesibles. Si el DNS está configurado para permitir actualizaciones dinámicas ¿registrar las máquinas virtuales a sí mismos mediante la nueva dirección IP una vez que inicia después de un error. 

![IP diferentes - después de la migración](./media/site-recovery-network-design/network-design11.png)

Figura 12

Después de error sobre la máquina virtual de réplica posible que tenga una dirección IP que no sea la misma que la dirección IP de la máquina virtual principal. Máquinas virtuales se actualizará el servidor DNS que están usando una vez que empiezan. Entradas DNS suelen tengan que cambiar o vacían en toda la red y las entradas en caché en las tablas de red tienen actualizarse o descargado, de modo que no es extraño enfrentarse el tiempo de inactividad mientras realizar estos cambios de estado. Este problema puede eliminarse mediante:

- Con los valores TTL bajos para aplicaciones de intranet.
- Usar el Administrador de tráfico de Azure con ASR para internet, aplicaciones basadas en.
- Mediante el siguiente script dentro de su plan de recuperación para actualizar el servidor DNS para garantizar una actualización oportuna (la secuencia de comandos no es necesario si se ha configurado el registro DNS dinámico)

        string]$Zone,
        [string]$name,
        [string]$IP
        )
        $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
        $newrecord = $record.clone()
        $newrecord.RecordData[0].IPv4Address  =  $IP
        Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord


### <a name="changing-the-ip-addresses--dr-to-azure"></a>Cambiar las direcciones IP: DR en Azure

La entrada de blog de [Instalación de infraestructura de redes de Microsoft Azure como un sitio de recuperación de desastres](http://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/) explica cómo configurar la infraestructura de red Azure necesaria cuando conservar direcciones IP no es necesario. Comienza con la descripción de la aplicación y después busque en cómo configurar la red local y en Azure y terminando con cómo hacer un error de prueba y un error planeado.



## <a name="next-steps"></a>Pasos siguientes

[Descubra](site-recovery-network-mapping.md) cómo sitio recuperación mapas de origen y destino redes cuando un servidor VMM se utiliza para administrar el sitio principal. 

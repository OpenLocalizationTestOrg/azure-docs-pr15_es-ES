<properties
    pageTitle="Proteger Active Directory y DNS con la recuperación de sitio de Azure | Microsoft Azure"
    description="Este artículo describe cómo implementar una solución de recuperación de desastres de Active Directory mediante la recuperación de sitios de Azure."
    services="site-recovery"
    documentationCenter=""
    authors="prateek9us"
    manager="abhiag"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="08/31/2016"
    ms.author="pratshar"/>

# <a name="protect-active-directory-and-dns-with-azure-site-recovery"></a>Proteger Active Directory y DNS con la recuperación de sitio de Azure

Las aplicaciones empresariales como SAP, Dynamics AX y SharePoint dependen de Active Directory y una infraestructura DNS funcione correctamente. Cuando se crea una solución de recuperación de desastres para aplicaciones, es importante recordar que debe proteger y recuperar Active Directory y DNS antes de los otros componentes de aplicación, para asegurarse de que las cosas funcionan correctamente cuando se produce desastre.

Recuperación de sitio es un servicio de Azure que proporciona recuperación por coordinar replicación, migración tras error y recuperación de máquinas virtuales. Recuperación de sitio es compatible con un número de escenarios de replicación para proteger de forma uniforme y sin problemas en máquinas virtuales de migración tras error y aplicaciones de nubes privado, público o anfitrión.

Recuperación de sitio puede crear un plan de recuperación completo automatizado de Active Directory. Cuando se producen interrupciones, puede iniciar un error en pocos segundos desde cualquier lugar y ponerse en marcha Active Directory en unos minutos. Si ha implementado Active Directory para varias aplicaciones como SharePoint y SAP en el sitio principal y desea conmutar el sitio completo, puede conmutar Active Directory primero mediante el sitio de recuperación y conmutar el resto de las aplicaciones con los planes de recuperación específicos de la aplicación.

En este artículo se explica cómo crear una solución de recuperación de desastres de Active Directory, cómo llevar a cabo planeada, no planeada y con un plan de recuperación de un solo clic, las configuraciones compatibles y los requisitos previos de migraciones tras error de prueba.  Debe estar familiarizado con Active Directory y recuperación de sitio de Azure antes de empezar.

Hay dos opciones recomendadas en función de la complejidad de su entorno.

### <a name="option-1"></a>Opción 1

Si tiene un pequeño número de aplicaciones y un controlador de dominio y desea conmutar todo el sitio, se recomienda usar recuperación de sitios para replicar el controlador de dominio en el sitio secundario (si está experimentando problemas a Azure o a un sitio secundario). La misma máquina virtual replicada puede utilizarse también para migración tras error de prueba.

### <a name="option-2"></a>Opción 2

Si tiene una gran cantidad de aplicaciones y hay más de un controlador de dominio en el entorno o si va a conmutar algunas aplicaciones a la vez, se recomienda que además de replicar la máquina virtual de controlador de dominio con el sitio de recuperación también configurará un controlador de dominio en el sitio de destino (Azure o un centro de datos local secundario).

>[AZURE.NOTE] Incluso si se está implementando opción 2, para realizar una migración de prueba tras error aún necesitará replicar el controlador de dominio con el sitio de recuperación. Lea la [migración tras error consideraciones de prueba](#considerations-for-test-failover) para obtener más información.


Las siguientes secciones explican cómo habilitar la protección de un controlador de dominio en el sitio de recuperación y cómo configurar un controlador de dominio en Azure.


## <a name="prerequisites"></a>Requisitos previos

- Una implementación local de Active Directory y servidor DNS.
- Un depósito de servicios de recuperación de sitio de Azure en una suscripción de Microsoft Azure.
- Si está duplicar en Azure ejecutar la herramienta de evaluación de disponibilidad de la máquina Virtual de Azure en máquinas virtuales para asegurarse de que sean compatibles con servicios de recuperación de sitio de Azure y máquinas virtuales de Azure.


## <a name="enable-protection-using-site-recovery"></a>Habilitar la protección con la recuperación de sitios


### <a name="protect-the-virtual-machine"></a>Proteger la máquina virtual

Habilitar la protección de la máquina virtual de DNS o controlador de dominio en el sitio de recuperación. Configurar las opciones de recuperación del sitio en función del tipo de máquina virtual (Hyper-V o sus). Se recomienda una frecuencia de replicación consistente de bloqueo de 15 minutos.

###<a name="configure-virtual-machine-network-settings"></a>Configurar la red de la máquina virtual

En la máquina virtual de controlador/DNS de dominio, configurar opciones de red de recuperación de sitios para que la máquina virtual se asociará a la red adecuada después de la migración. Por ejemplo, si va a duplicar máquinas virtuales de Hyper-V en Azure puede seleccionar la máquina virtual en la nube VMM o en el grupo de protección para establecer la configuración de red, tal como se muestra a continuación

![Configuración de la red VM](./media/site-recovery-active-directory/VM-Network-Settings.png)

## <a name="protect-active-directory-with-active-directory-replication"></a>Proteger Active Directory con la replicación de Active Directory

### <a name="site-to-site-protection"></a>Protección del sitio a otro

Crear un controlador de dominio en el sitio secundario y especifique el nombre del mismo dominio que se utiliza en el sitio primario al promover el servidor a una función de controlador de dominio. Puede usar el complemento **servicios y sitios de Active Directory** para configurar las opciones en el objeto de vínculo de sitio al que se agregan los sitios. Mediante la configuración en un vínculo de sitio, puede controlar cuándo se produce la replicación entre dos o más sitios y con qué frecuencia. Para obtener más detalles, consulte [Programación de la replicación entre sitios](https://technet.microsoft.com/library/cc731862.aspx) .

###<a name="site-to-azure-protection"></a>Protección del sitio a Azure

Siga las instrucciones para [crear un controlador de dominio en una red virtual Azure](../active-directory/active-directory-install-replica-active-directory-domain-controller.md). Cuando se promueve el servidor para una función de controlador de dominio, especifique el mismo nombre de dominio que se utiliza en el sitio primario.

A continuación, [vuelva a configurar el servidor DNS de la red virtual](../active-directory/active-directory-install-replica-active-directory-domain-controller.md#reconfigure-dns-server-for-the-virtual-network), usar el servidor DNS en Azure.

![Red de Azure](./media/site-recovery-active-directory/azure-network.png)

## <a name="test-failover-considerations"></a>Consideraciones de migración tras error de prueba

Se produce error de prueba en una red que se ha aislado de red de producción para que no hay ningún impacto en las cargas de trabajo de producción.

Muchas aplicaciones también requieren la presencia de un controlador de dominio y un servidor DNS para que funcione, por tanto antes de la aplicación no pudo sobre, es necesario crear en la red aislada que se usará para la migración tras error de prueba de un controlador de dominio. La manera más sencilla de hacerlo es Habilitar protección en la máquina virtual de DNS o controlador de dominio con la recuperación de sitio y a continuación, ejecute una prueba de migración tras error de esa máquina virtual, antes de ejecutar una migración tras error de prueba del plan de recuperación de la aplicación. Le mostramos cómo hacerlo:

1. Habilitar la protección de recuperación de sitios de la máquina virtual DNS o controlador de dominio.
2. Crear una red aislada. Una red virtual creada en Azure predeterminada se aislará desde otras redes. Le recomendamos que el intervalo de direcciones IP de esta red es el mismo que el de su red de producción. No habilitar la conectividad de sitio para el sitio de esta red.
3. Proporcionar una dirección IP de DNS en la red que se creó, como la dirección IP que esperan la máquina virtual DNS para obtener. Si va a duplicar en Azure, a continuación, proporcionar la dirección IP de la máquina virtual que se utilizará en migración tras error en la configuración de **IP de destino** en las propiedades de la máquina virtual. Si va a duplicar en otro sitio y local está con DHCP, siga las instrucciones para [Configurar DNS y DHCP para migración tras error de prueba](site-recovery-failover.md#prepare-dhcp)

>[AZURE.NOTE] La dirección IP asignada a una máquina virtual durante un error de prueba es igual que la dirección IP que obtendría durante un error planificado o, si la dirección IP está disponible en la red de migración tras error de prueba. Si no es así, la máquina virtual recibe una dirección IP diferente que está disponible en la red de migración tras error de prueba.

4. En la máquina virtual de controlador de dominio ejecutar una migración tras error de prueba de ella en la red aislada. Usar el punto de recuperación consistente aplicación disponible más reciente de la máquina virtual de controlador de dominio para hacer la migración tras error de prueba. 
5. Ejecutar un error de prueba para el plan de recuperación de aplicaciones.
6. Una vez completada la comprobación, marque el trabajo de migración tras error de prueba de máquina virtual de controlador de dominio y el plan de recuperación 'Completado' en la ficha **trabajos** en el portal de recuperación del sitio.

### <a name="dns-and-domain-controller-on-different-machines"></a>Controlador de dominio y DNS en equipos diferentes

Si DNS no está en la misma máquina virtual como el controlador de dominio, que deberá crear una VM DNS para la migración tras error de prueba. Si se encuentra en la misma VM, puede omitir esta sección.

Puede usar un servidor DNS fresco y crear todas las zonas necesarias. Por ejemplo, si su dominio de Active Directory es contoso.com, puede crear una zona DNS con el nombre contoso.com. Las entradas correspondientes a Active Directory se deben actualizar en DNS, como sigue:

1. Asegúrese de esta configuración antes de que aparezca cualquier otra máquina virtual en el plan de recuperación:

    - Debe tener el nombre de la zona después del nombre de la raíz del bosque.
    - La zona debe ser una copia del archivo.
    - La zona debe habilitarse para las actualizaciones seguras y no seguras.
    - La resolución de la máquina virtual de controlador de dominio debe apuntar a la dirección IP de la máquina virtual DNS.

2. Ejecute el comando siguiente en la máquina virtual de controlador de dominio:

    `nltest /dsregdns`

3. Agregar una zona en el servidor DNS, permitir actualizaciones no seguras y agregar una entrada correspondiente a DNS:

        dnscmd /zoneadd contoso.com  /Primary
        dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1
        dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM>
        dnscmd /config contoso.com /allowupdate 1


## <a name="next-steps"></a>Pasos siguientes

Lectura [¿Qué cargas de trabajo se debe proteger?](../site-recovery/site-recovery-workload.md) para obtener más información acerca de cómo protegerse de cargas de trabajo empresariales con la recuperación de sitio de Azure.

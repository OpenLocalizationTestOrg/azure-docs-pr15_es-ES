<properties
pageTitle="Consideraciones sobre el uso de imágenes de máquina virtual de Oracle | Microsoft Azure"
description="Obtenga más información sobre configuraciones compatibles y las limitaciones de una máquina virtual de Oracle en servidor de Windows Azure antes de implementar."
services="virtual-machines-windows"
documentationCenter=""
manager="timlt"
authors="rickstercdn"
tags="azure-service-management"/>

<tags
ms.service="virtual-machines-windows"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="vm-windows"
ms.workload="infrastructure-services"
ms.date="09/06/2016"
ms.author="rclaus" />

#<a name="miscellaneous-considerations-for-oracle-virtual-machine-images"></a>Varias consideraciones para las imágenes de máquina virtual de Oracle



En este artículo trata sobre consideraciones para Oracle máquinas virtuales de Windows Azure, en el que se basan en las imágenes de software de Oracle proporcionadas por Oracle.  

-  Imágenes de máquina virtual de base de datos de Oracle
-  Imágenes de máquina virtual de Oracle WebLogic Server
-  Imágenes de máquina virtual JDK de Oracle

##<a name="oracle-database-virtual-machine-images"></a>Imágenes de máquina virtual de base de datos de Oracle
### <a name="clustering-rac-is-not-supported"></a>No se admite clústeres (RAC)

Azure actualmente no admite clústeres de aplicación Real (RAC) de Oracle de la base de datos de Oracle. Solo independiente instancias de base de datos de Oracle son posibles. Esto es porque Azure actualmente no admite compartir de disco virtual de un modo de lectura y escritura entre varias instancias de máquina virtual. Multidifusión UDP también es incompatible.

### <a name="no-static-internal-ip"></a>Sin IP estática interna

Azure asigna una dirección IP interna de cada máquina virtual. A menos que la máquina virtual es parte de una red virtual, la dirección IP de la máquina virtual es dinámica y podría cambiar después de reiniciar la máquina virtual. Esto puede causar problemas porque la dirección IP estática de espera de la base de datos de Oracle. Para evitar el problema, considere la posibilidad de agregar la máquina virtual a una red Virtual de Azure. Para obtener más información, consulte [Red Virtual](https://azure.microsoft.com/documentation/services/virtual-network/) y [crear una red virtual en Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) .

### <a name="attached-disk-configuration-options"></a>Opciones de configuración de disco conectado

Puede colocar los archivos de datos en el disco de sistema operativo de la máquina virtual o en discos conectados, también conocida como discos de datos. Discos conectados pueden ofrecer una mayor flexibilidad de rendimiento y el tamaño que el sistema operativo del disco. El disco de sistema operativo podría ser preferible sólo para bases de datos en 10 gigabytes (GB).

Discos conectados se basan en el servicio de almacenamiento de blobs de Windows Azure. Cada disco es capaz de un máximo teórico de aproximadamente 500 operaciones de entrada y salida por segundo (IOPS). El rendimiento de discos adjuntos no sea óptimo inicialmente y puede mejorar el rendimiento de IOPS considerablemente tras un periodo "grabación en" de aproximadamente 60 y 90 minutos de funcionamiento continuo. Si un disco posteriormente permanece inactivo, podría disminuir el rendimiento de IOPS hasta otro período en grabación de funcionamiento continuo. En resumen, la más activa un disco, más probable es el rendimiento óptimo de IOPS de enfoque.

Aunque es el enfoque más sencillo adjuntar un disco a la máquina virtual y colocar los archivos de base de datos en el disco, este método también es más restrictivo en términos de rendimiento. En su lugar, a menudo puede mejorar el rendimiento de IOPS eficaz si usar varios discos conectados, ocupa la base de datos y, a continuación, usar Administración de almacenamiento automático (ASM) de Oracle. Para obtener más información, vea [información general de almacenamiento automático de Oracle](http://www.oracle.com/technetwork/database/index-100339.html) . Aunque es posible utilizar bandas de varios discos en el nivel de sistema operativo, este enfoque no se recomienda porque no se conoce para mejorar el rendimiento de IOPS.

Tenga en cuenta dos enfoques diferentes para adjuntar varios discos en función de si desea establecer prioridades el rendimiento de las operaciones de lectura o escritura de la base de datos:

- **ASM de Oracle en su propia** es probable que provocan un mejor rendimiento de la operación de escritura, pero peor IOPS para operaciones de lectura en comparación con el enfoque mediante matrices de disco. La ilustración siguiente muestra lógicamente este acuerdo.  
    ![](media/virtual-machines-windows-classic-oracle-considerations/image2.png)

>[AZURE.IMPORTANT] Evaluar el equilibrio entre el rendimiento de escritura y lectura en caso por caso. Los resultados reales pueden variar cuando se utiliza.

### <a name="high-availability-and-disaster-recovery-considerations"></a>Consideraciones de recuperación de desastres y disponibilidad altos

Al usar la base de datos de Oracle en máquinas virtuales de Windows Azure, usted es responsable de implementar una solución de recuperación de desastres y disponibilidad alta para evitar el tiempo de inactividad. También es responsable de la copia de seguridad de sus propios datos y la aplicación.

Alta disponibilidad y recuperación ante desastres para Oracle Database Enterprise Edition (sin RAC) en Azure puede conseguirse con [protección de datos, protección de datos activo](http://www.oracle.com/technetwork/articles/oem/dataguardoverview-083155.html)o [puerta de oro de Oracle](http://www.oracle.com/technetwork/middleware/goldengate), con dos bases de datos en dos máquinas virtuales separadas. Ambos equipos virtuales debe estar en el mismo [servicio en la nube](virtual-machines-linux-classic-connect-vms.md) y la misma [red virtual](https://azure.microsoft.com/documentation/services/virtual-network/) para asegurarse de que pueden acceder a ellos a través de la dirección IP persistente privada.  Además, se recomienda colocar los equipos virtuales en el mismo [conjunto de disponibilidad](virtual-machines-windows-manage-availability.md) para permitir Azure colocarlos en dominios de error independiente y actualizar los dominios. Solo máquinas virtuales en el mismo servicio de nube puede participar en el mismo conjunto de disponibilidad. Cada máquina virtual debe tener al menos 2 GB de memoria y 5 GB de espacio en disco.

Con protección de datos de Oracle, se puede lograr alta disponibilidad con una base de datos principal en una máquina virtual en una base de datos (en espera) secundario en otro equipo virtual y la replicación unidireccional configurar entre ellas. El resultado es el acceso de lectura a la copia de la base de datos. Con GoldenGate de Oracle, puede configurar la replicación bidireccional entre las dos bases de datos. Para obtener información sobre cómo configurar una solución de alta disponibilidad para las bases de datos con estas herramientas, consulte la documentación de [Protección de datos activa](http://www.oracle.com/technetwork/database/features/availability/data-guard-documentation-152848.html) y [GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/index.html) en el sitio Web de Oracle. Si se necesita lectura y escritura acceso a la copia de la base de datos, puede utilizar [Protección de datos activo de Oracle](http://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html).

##<a name="oracle-weblogic-server-virtual-machine-images"></a>Imágenes de máquina virtual de Oracle WebLogic Server

-  **Clústeres sólo es compatible en Enterprise Edition.** Tiene la licencia para usar WebLogic clústeres solo cuando se usa WebLogic servidor Enterprise Edition. No use clústeres con WebLogic Server Standard Edition.

-  **Tiempos de espera de conexión:** Si la aplicación se basa en conexiones a extremos públicas de otro servicio de nube Azure (por ejemplo, un servicio de nivel de base de datos), Azure podría cerrar estas conexiones abiertas después de cuatro minutos de inactividad. Porque conexiones inactivas durante más de ese límite ya no pueden permanecer válidas Esto puede afectar a las características y las aplicaciones basadas en grupos de conexión. Si esto afecta a la aplicación, considere la posibilidad de habilitar lógica "de mantenimiento" en los grupos de conexión.

    Si un punto final es *interno* a la implementación de servicio de nube Azure (por ejemplo, una máquina de virtual de base de datos independiente en el *mismo* servicio de nube como su máquinas virtuales de WebLogic), a continuación, la conexión es directa y no se basa en el equilibrador de carga de Azure y, por tanto, no está sujeta a un tiempo de espera de la conexión.

-  **No se admite multidifusión UDP.** Es compatible con Azure unidifusión UDP, pero no la multidifusión o difusión. WebLogic Server es posible dependen de las capacidades de unidifusión UDP de Azure. Para mejor resultados depender unidifusión UDP, se recomienda que el tamaño de clúster WebLogic mantenerse estático, o se conservarán con no más de 10 servidores administrados incluidos en el clúster.

-  **WebLogic Server espera puertos públicos y privados a ser el mismo acceso T3 (por ejemplo, cuando se usa Enterprise JavaBeans).** Considere la posibilidad de un escenario de varios niveles en una aplicación de servicio de la capa (EJB) se está ejecutando en un clúster de servidor WebLogic que consta de dos o más servidores administrados, en un servicio de nube denominado **SLWLS**. El nivel de cliente se encuentra en un servicio de nube diferente que se ejecuta un programa Java simple intenta llamarle EJB en la capa de servicio. Dado que es necesario para la capa de servicio de equilibrio de carga, un extremo de equilibrio de carga público debe crearse en los equipos virtuales en el clúster WebLogic Server. Si el puerto privado que especifique para ese extremo es diferente del puerto público (por ejemplo, 7006:7008), se produce un error como el siguiente:

        [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

        Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]

    Esto es porque para acceso remoto T3, WebLogic Server espera el puerto del equilibrador de carga y el puerto del servidor WebLogic administrada sea la misma. En el caso anterior, el cliente tiene acceso puerto 7006 (el puerto del equilibrador de carga) y el servidor administrado está escuchando en 7008 (el puerto privado). Esta restricción es aplicable solo para el acceso de T3, no HTTP.

    Para evitar este problema, use una de las siguientes soluciones:

    -  Utilizar el mismo número de puerto públicas y privadas para los extremos de equilibrio de carga dedicados T3 de Access.

    -  Incluya el siguiente parámetro JVM al iniciar WebLogic Server:

            -Dweblogic.rjvm.enableprotocolswitch=true

Para obtener información relacionada, vea de artículo KB **860340.1** en <http://support.oracle.com>.

-  **Clústeres dinámicos y limitaciones de equilibrio de carga.** Suponga que desea usar un clúster dinámico WebLogic Server y exponer a través de un extremo único, público de equilibrio de carga en Azure. Esto se puede hacer como usar un número de puerto fijo para cada uno de los servidores administrados (dinámicamente no se asignan desde un rango) y lo inicia más servidores administrados que han máquinas realiza un seguimiento del administrador (es decir, no más de un servidor administrado por máquina virtual). Si la configuración de resultado más servidores de WebLogic se inicie que hay máquinas virtuales de Windows (es decir, donde varias instancias de WebLogic Server compartan la misma máquina virtual), y no es posible que más de una de las instancias de servidores WebLogic Server enlazar a un número de puerto determinado: otros usuarios en esa máquina virtual se producirá un error.

    Por otro lado, si configura el servidor de administración para asignar automáticamente los números de puerto exclusivos a los servidores administrados, a continuación, equilibrio de carga no es posible porque Azure no admite la asignación de un único puerto público a varios puertos privados, como sería necesario para esta configuración.

-  **Varias instancias de Weblogic Server en una máquina virtual.** Dependiendo de los requisitos de la implementación, considere la posibilidad de ejecutar varias instancias de WebLogic Server en la misma máquina virtual, si la máquina virtual es lo suficientemente grande. Por ejemplo, en una máquina virtual de tamaño medio, que contiene dos núcleos, puede elegir ejecutar dos instancias de WebLogic Server. Tenga en cuenta que aún se recomienda evitar la introducción de puntos únicos de falla en su arquitectura, que sería el caso, si utiliza una máquina virtual que se está ejecutando varias instancias de WebLogic Server. Utilizando al menos dos máquinas virtuales podría ser mejor y, a continuación, cada una de esas máquinas virtuales podría ejecutar varias instancias de WebLogic Server. Cada una de estas instancias de WebLogic Server aún podría ser parte del mismo clúster. Tenga en cuenta, sin embargo, actualmente no es posible usar Azure para extremos de equilibrio de carga que se exponen por dicha implementaciones WebLogic Server dentro de la misma máquina virtual, porque equilibrador de carga de Azure requiere los servidores de equilibrio de carga se distribuya entre únicos máquinas virtuales de Windows.

##<a name="oracle-jdk-virtual-machine-images"></a>Imágenes de máquina virtual JDK de Oracle

-  **JDK 6 y 7 las últimas actualizaciones.** Aunque es aconsejable usar la versión más reciente pública compatible de Java (actualmente Java 8), Azure también permite JDK 6 y 7 imágenes disponibles. Está destinado a aplicaciones heredadas que no todavía está listos para actualizarse a JDK 8. Mientras que las actualizaciones de las imágenes JDK anteriores ya no esté disponibles para el público en general, dado la asociación de Microsoft con Oracle, JDK 6 y 7 imágenes proporcionadas por Azure están pensadas para contienen una actualización no públicos más reciente que se ofrece normalmente por Oracle para solo un grupo selecto de clientes de Oracle compatibles. Las nuevas versiones de las imágenes JDK estarán disponibles con el tiempo con las versiones actualizadas de JDK 6 y 7.

    El JDK disponible en este 6 JDK y 7 imágenes, máquinas virtuales e imágenes derivadas de ellos, sólo puede usarse en Azure.

-  **JDK de 64 bits.** Las imágenes de máquina virtual de Oracle WebLogic Server y las imágenes de máquina virtual de Oracle JDK proporcionadas por Azure contienen las versiones de 64 bits de Windows Server y el JDK.

##<a name="additional-resources"></a>Recursos adicionales
[Imágenes de máquina virtual de Oracle para Azure](virtual-machines-linux-classic-oracle-images.md)

<properties
    pageTitle="Prueba de rendimiento y escala resultados para local a la replicación de Hyper-V local con el sitio de recuperación | Microsoft Azure"
    description="En este artículo se proporciona información sobre el rendimiento de pruebas de replicación locales con Azure sitio recuperación local."
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor="tysonn"/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="07/06/2016"
    ms.author="raynew"/>

# <a name="performance-test-and-scale-results-for-on-premises-to-on-premises-hyper-v-replication-with-site-recovery"></a>Rendimiento, probar y escala local a la replicación de Hyper-V local con la recuperación de sitio en los resultados

Puede usar Microsoft Azure sitio recuperación para organizar y administrar la replicación de máquinas virtuales y servidores físicos a Azure o a un centro de datos secundario. Este artículo proporciona los resultados de pruebas de rendimiento que hemos hecho cuando replicar máquinas virtuales Hyper-V entre dos local centros de datos.



## <a name="overview"></a>Información general

El objetivo de las pruebas era examinar cómo realiza la recuperación de sitios de Azure durante la replicación de estado de equilibrio. Replicación de estado estable se produce cuando haya completado la replicación inicial de máquinas virtuales y está sincronizando cambios delta. Es importante medir el rendimiento con estado estable porque es el estado en el que la mayoría máquinas virtuales permanecen a menos que se produzcan interrupciones inesperadas.


La distribución de prueba consiste en dos sitios local con un servidor de VMM en cada sitio. Esta implementación de prueba es típica de una implementación de office central y sucursales, con sede actúa como el sitio primario y la sucursal que el sitio secundario o de recuperación.

### <a name="what-we-did"></a>¿Qué se hizo

Esto es lo que se en la prueba pasa:

1. Crear máquinas virtuales con plantillas VMM.

1. Introducción a máquinas virtuales y medidas de rendimiento de línea base captura más de 12 horas.

1. Nubes creados en los servidores VMM principal y recuperación.

1. Protección de nube configurado en Azure sitio recuperación, incluida la asignación de nubes de origen y la recuperación.

1. Habilita la protección para máquinas virtuales y permitirles complete la replicación inicial.

1. Espera un par de horas de estabilización del sistema.

1. Genera métricas de rendimiento más de 12 horas, garantiza que todos los equipos virtuales permanece en un estado de replicación prevista durante las 12 horas.

1. Medir la distancia entre las métricas de rendimiento de línea base y las medidas de rendimiento de replicación.

## <a name="test-deployment-results"></a>Resultados de la implementación de prueba

### <a name="primary-server-performance"></a>Rendimiento del servidor principal

- Hyper-V réplica asincrónica realiza un seguimiento de cambios en un archivo de registro con una carga mínima de almacenamiento en el servidor principal.

- Réplica de Hyper-V utiliza mantiene la memoria caché para minimizar la sobrecarga de IOPS para realizar un seguimiento. Almacena escribe en el VHDX en memoria y vacía en el archivo de registro antes de la hora en que el registro se envía al sitio de recuperación. Un disco vaciado también ocurre si la escritura alcanza un límite predeterminado.

- El siguiente gráfico muestra la sobrecarga IOPS estado estable para la replicación. Podemos ver que las IOPS sobrecarga debido a la replicación alrededor es del 5%, que es bastante baja.

![Resultados principales](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744913.png)

Réplica de Hyper-V utiliza la memoria en el servidor principal para optimizar el rendimiento de disco. Como se muestra en el siguiente gráfico, es marginal memoria sobrecarga en todos los servidores en el clúster principal. La memoria sobrecarga mostrada es el porcentaje de memoria utilizada por replicación en comparación con la memoria total instalada en el servidor de Hyper-V.

![Resultados principales](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744914.png)

Réplica de Hyper-V tiene mínima sobrecarga de la CPU. Como se muestra en el gráfico, carga de replicación se encuentra en el rango de 2-3%.

![Resultados principales](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744915.png)

### <a name="secondary-recovery-server-performance"></a>Rendimiento del servidor secundario (recuperación)

Réplica de Hyper-V usa una cantidad pequeña de memoria en el servidor de recuperación para optimizar el número de operaciones de almacenamiento. El gráfico resume el uso de memoria en el servidor de recuperación. La memoria sobrecarga mostrada es el porcentaje de memoria utilizada por replicación en comparación con la memoria total instalada en el servidor de Hyper-V.

![Resultados secundarios](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744916.png)

La cantidad de operaciones de i/OS en el sitio de recuperación es una función del número de operaciones de escritura en el sitio primario. Veamos las operaciones de i/OS total en el sitio de recuperación en comparación con el total de operaciones de i/OS y escribir operaciones en el sitio primario. Los gráficos muestran que es el total IOPS en el sitio de recuperación

- Alrededor de 1,5 veces la IOPS de escritura en el principal.

- Alrededor del 37% de la IOPS total en el sitio primario.

![Resultados secundarios](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744917.png)

![Resultados secundarios](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744918.png)

### <a name="effect-of-replication-on-network-utilization"></a>Efecto de la replicación en uso de la red

Promedio de 275 MB por segundo de ancho de banda de red se utilizó entre los nodos principal y recuperación (con la compresión habilitada) con un ancho de banda existente de 5 GB por segundo.

![Uso de la red de resultados](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744919.png)

### <a name="effect-of-replication-on-virtual-machine-performance"></a>Efecto de replicación en el rendimiento de la máquina virtual

Un factor importante es el impacto de la replicación en ejecución en las máquinas virtuales de cargas de trabajo de producción. Si el sitio primario se aprovisiona adecuada para la replicación, no debe haber ningún impacto en las cargas de trabajo. Ligero de réplica de Hyper-V mecanismo de seguimiento asegura que las cargas de trabajo que se ejecuta en los equipos virtuales no se ven afectados durante la replicación de estado. Esto se muestra en los gráficos siguientes.

Este gráfico muestra IOPS realizado por máquinas virtuales que ejecutan diferentes cargas de trabajo antes y después de que se ha habilitado la replicación. Puede observar que no hay ninguna diferencia entre los dos.

![Resultados del efecto de réplica](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744920.png)

El siguiente gráfico muestra el rendimiento de máquinas virtuales que ejecutan diferentes cargas de trabajo antes y después de que se ha habilitado la replicación. Puede observar que la replicación no tiene ningún efecto significativo.

![Efectos de réplica de resultados](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744921.png)

### <a name="conclusion"></a>Conclusión

Los resultados muestran claramente escalas de recuperación de sitios de Azure, junto con la réplica de Hyper-V, bien con mínima sobrecarga de un clúster grande.  Recuperación de sitio de Azure proporciona implementación simple, replicación, administración y supervisión. Réplica de Hyper-V proporciona la infraestructura necesaria para la escala de replicación correcta. Para planear una implementación óptima, le recomendamos que descargue el [Organizador de la capacidad de réplica de Hyper-V](https://www.microsoft.com/download/details.aspx?id=39057).

## <a name="test-environment-details"></a>Detalles del entorno de prueba

### <a name="primary-site"></a>Sitio principal

- El sitio primario tiene un clúster que contiene los servidores de Hyper-V cinco máquinas virtuales 470.

- Los equipos virtuales ejecutar diferentes cargas de trabajo y todo tiene habilitada la protección de recuperación de sitios de Azure.

- Almacenamiento de información para el nodo proporciona una Ethernet. Modelo: HUS130 de Hitachi.

- Cada servidor de clúster tiene cuatro tarjetas de red (NIC) de un Gbps.

- Dos de las tarjetas de red están conectado a una red privada de iSCSI y dos están conectada a una red empresarial externa. Una de las redes externas está reservada para las comunicaciones de clúster.

![Requisitos de hardware principal](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744922.png)

|Servidor|RAM|Modelo|Procesador|Número de procesadores|NIC|Software|
|---|---|---|---|---|---|---|
|Servidores de Hyper-V de clúster: <br />ESTLAB HOST11<br />ESTLAB HOST12<br />ESTLAB HOST13<br />ESTLAB HOST14<br />ESTLAB HOST25|128ESTLAB HOST25 tiene 256|Dell™ PowerEdge™ R820|CPU E5 4620 r Xeon 0 @ 2,20 GHz|4|Puedo GB/s x 4|Centro de datos de Windows Server 2012 R2 (x64) + rol Hyper-v.|
|Servidor VMM|2|||2|1 GB/s|Windows Server 2012 de base de datos R2 (x 64) + VMM 2012 R2|

### <a name="secondary-recovery-site"></a>Sitio secundario (recuperación)

- El sitio secundario tiene un clúster de seis nodos.

- Almacenamiento de información para el nodo proporciona una Ethernet. Modelo: HUS130 de Hitachi.

![Especificación de hardware principal](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744923.png)

|Servidor|RAM|Modelo|Procesador|Número de procesadores|NIC|Software|
|---|---|---|---|---|---|---|
|Servidores de Hyper-V de clúster: <br />ESTLAB HOST07<br />ESTLAB HOST08<br />ESTLAB HOST09<br />ESTLAB HOST10|96|Dell™ PowerEdge™ R720|CPU E5 2630 r Xeon 0 @ 2,30 GHz|2|Puedo GB/s x 4|Centro de datos de Windows Server 2012 R2 (x64) + rol Hyper-v.|
|ESTLAB HOST17|128|Dell™ PowerEdge™ R820|CPU E5 4620 r Xeon 0 @ 2,20 GHz|4||Centro de datos de Windows Server 2012 R2 (x64) + rol Hyper-v.|
|ESTLAB HOST24|256|Dell™ PowerEdge™ R820|CPU E5 4620 r Xeon 0 @ 2,20 GHz|2||Centro de datos de Windows Server 2012 R2 (x64) + rol Hyper-v.|
|Servidor VMM|2|||2|1 GB/s|Windows Server 2012 de base de datos R2 (x 64) + VMM 2012 R2|

### <a name="server-workloads"></a>Cargas de trabajo

- Para fines de pruebas elegimos cargas de trabajo que se usan normalmente en escenarios de cliente de empresa.

- Usamos [IOMeter](http://www.iometer.org) con la característica de carga de trabajo mostrada en la tabla de simulación.

- Todos los perfiles de IOMeter se establecen para escribir bytes aleatorios para simular peor escribir patrones para cargas de trabajo.

|Carga de trabajo|Tamaño de i/OS (KB)|% De access|% De lectura|Pendiente i/OS|Trama de i/OS|
|---|---|---|---|---|---|
|Servidor de archivos|48163264|60% 20 %5 %5% 10%|80 80% 80% 80% 80%|88888|Todos los 100% aleatorios|
|SQL Server (volumen 1) SQL Server (volumen 2)|864|100% 100%|70 %0%|88|100% random100% secuencial|
|Exchange|32|100%|67%|8|aleatorio al 100%|
|Estación de trabajo o VDI|464|66% 34%|95 70%|11|Ambos aleatorio 100%|
|Servidor de archivos de Web|4864|34% 33 33%|95% 95 95%|888|Todos los 75% aleatorio|

### <a name="virtual-machine-configuration"></a>Configuración de máquina virtual

- 470 máquinas virtuales de Windows en el clúster principal.

- Todos los equipos virtuales con disco VHDX.

- Máquinas virtuales que ejecutan cargas de trabajo que se resumen en la tabla. Todo creada con plantillas VMM.

|Carga de trabajo|# Máquinas virtuales|RAM mínima (GB)|Cantidad máxima de RAM (GB)|Tamaño del disco lógico (GB) por máquina virtual|IOPS máximas|
|---|---|---|---|---|---|
|SQL Server|51|1|4|167|10|
|Exchange Server|71|1|4|552|10|
|Servidor de archivos|50|1|2|552|22|
|VDI|149|.5|1|80|6|
|Servidor Web|149|.5|1|80|6|
|TOTAL|470|||96.83 TB|4108|

### <a name="azure-site-recovery-settings"></a>Configuración de la recuperación de sitio de Azure

- Azure recuperación de sitio se ha configurado para protección local locales

- El servidor VMM tiene cuatro nubes configurados, que contiene los servidores de clúster de Hyper-V y sus máquinas virtuales.

|Nube VMM principal|Protegido máquinas virtuales en la nube|Frecuencia de replicación|Puntos de recuperación adicionales|
|---|---|---|---|
|PrimaryCloudRpo15m|142|15 minutos|Ninguno|
|PrimaryCloudRpo30s|47|30 segundos|Ninguno|
|PrimaryCloudRpo30sArp1|47|30 segundos|1|
|PrimaryCloudRpo5m|235|5 minutos|Ninguno|

### <a name="performance-metrics"></a>Indicadores de rendimiento

La tabla resumen los indicadores de rendimiento y contadores que se miden en la implementación.

|Métrica|Contador|
|---|---|
|CPU|\Processor(_Total)\% tiempo de procesador|
|Memoria disponible|\Memory\Available MB|
|IOPS|\PhysicalDisk (_Total) \Disk de s.|
|VM lee de operaciones por segundo (IOPS)|Dispositivo de almacenamiento virtual de \Hyper-V (<VHD>) \Read de operaciones por segundo|
|Operaciones de escritura (IOPS) VM/seg.|Dispositivo de almacenamiento virtual de \Hyper-V (<VHD>) \Write operaciones/S|
|Rendimiento de lectura de VM|Dispositivo de almacenamiento virtual de \Hyper-V (<VHD>) \Read Bytes por segundo|
|Rendimiento de escritura VM|Dispositivo de almacenamiento virtual de \Hyper-V (<VHD>) \Write Bytes por segundo|


## <a name="next-steps"></a>Pasos siguientes

- [Configurar la protección entre dos sitios VMM local](site-recovery-vmm-to-vmm.md)

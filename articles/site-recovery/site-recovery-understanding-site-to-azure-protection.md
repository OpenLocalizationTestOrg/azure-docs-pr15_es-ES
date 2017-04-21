<properties
    pageTitle="Replicación de Hyper-V con recuperación de sitio de Azure | Microsoft Azure"
    description="Use este artículo para conocer los conceptos técnicos que le ayudarán a correctamente instalan, configuran y administración la recuperación de sitios de Azure."
    services="site-recovery"
    documentationCenter=""
    authors="Rajani-Janaki-Ram"
    manager="mkjain"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="09/12/2016"
    ms.author="rajanaki"/>  


# <a name="hyper-v-replication-with-azure-site-recovery"></a>Replicación de Hyper-V con recuperación de sitio de Azure

En este artículo se describe los conceptos técnicos que pueden ayudarle a configurar y administrar un sitio de Hyper-V o un sitio de System Center Virtual Machine Manager (VMM) Azure protección mediante la recuperación de sitios de Azure correctamente.

## <a name="setting-up-the-source-environment-for-replication-between-on-premises-and-azure"></a>Configurar el entorno de origen para la replicación entre Azure y local

Como parte de la configuración de recuperación entre local y Azure, asegúrese de descargar e instalar el proveedor de servicios de recuperación de sitio de Azure en el servidor VMM. Instalar a agente de servicios de recuperación de Azure en cada host de Hyper-V.

![Implementación de VMM sitio para la replicación entre Azure y local](media/site-recovery-understanding-site-to-azure-protection/image00.png)

Configurar el entorno de origen en una infraestructura de Hyper-V administrada es similar a la configuración del entorno de origen en una infraestructura VMM administrado. La única diferencia es que el proveedor de servicios y el agente están instalados en el propio host Hyper-V. En el entorno de VMM, el proveedor está instalado en el servidor VMM y está instalado el agente en los hosts de Hyper-V (en caso de replicación de Azure).

## <a name="workflows"></a>Flujos de trabajo

### <a name="enable-protection"></a>Habilitar protección
Después de proteger una máquina virtual desde el portal de Azure o local, se inicia un trabajo de recuperación de un sitio denominado **Habilitar protección** . Se puede supervisar en la ficha **trabajos** .

!["Habilitar protección" trabajo en la lista de trabajos](media/site-recovery-understanding-site-to-azure-protection/image001.PNG)

El trabajo de **Habilitar protección** comprueba los requisitos previos antes de invocar el método [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) . Este método crea la replicación de Azure mediante entradas que se han configurado durante la protección.

**Habilitar la protección** comienza el trabajo la replicación inicial de local al método [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) . Este método envía discos virtuales de la máquina virtual en Azure.

![Detalles de la tarea "Habilitar protección"](media/site-recovery-understanding-site-to-azure-protection/IMAGE002.PNG)

### <a name="finalize-protection-on-the-virtual-machine"></a>Finalizar la protección en la máquina virtual
Cuando se activa la replicación inicial, se toma una [instantánea de máquina virtual Hyper-V](https://technet.microsoft.com/library/dd560637.aspx) . Discos duro virtuales están procesados uno por uno hasta que todos los discos se cargan en Azure. Normalmente se abrirá un rato en Finalizar, basándose en el tamaño del disco y el ancho de banda. Para optimizar el uso de la red, consulte [cómo administrar local para el uso de ancho de banda de red de protección de Azure](https://support.microsoft.com/kb/3056159).

Una vez que termine la replicación inicial, el trabajo de **protección Finalize en la máquina virtual** configura la configuración de red y posteriores a la replicación. Mientras esté realizando la replicación inicial:

- Se realiza un seguimiento de todos los cambios de los discos. 
- Almacenamiento adicional en el disco se consume para la instantánea y los archivos de registro de réplica de Hyper-V (HRL).

Al finalizar la replicación inicial, se elimina la instantánea de la máquina virtual Hyper-V. Esta eliminación el resultado de combinar los cambios de datos después de la replicación inicial en el disco principal.

![Trabajo "Finalizar la protección en la máquina virtual" en la lista de trabajos](media/site-recovery-understanding-site-to-azure-protection/image03.png)

### <a name="delta-replication"></a>Replicación delta
Seguimiento de replicación de réplica de Hyper-V, que forma parte del motor de replicación de réplica de Hyper-V, pistas los cambios en un disco duro virtual como archivos de registro de réplica de Hyper-V (*.hrl). Archivos HRL se encuentran en el mismo directorio que los discos asociados.

Cada disco que está configurada para replicación tiene un archivo HRL asociado. Este registro se envía a la cuenta del cliente almacenamiento una vez completada la replicación inicial. Cuando un registro está en tránsito a Azure, se controlan los cambios en el principal en otro archivo de registro en el mismo directorio.

Durante la duplicación inicial o delta, puede supervisar el mantenimiento de la replicación de VM en la vista de la máquina virtual, como se mencionó en [supervisar el mantenimiento de la replicación de máquina virtual](./site-recovery-monitoring-and-troubleshooting.md#monitor-replication-health-for-virtual-machine).  

### <a name="resynchronization"></a>Sincronización
Una máquina virtual está marcada para sincronización cuando se produce un error en la replicación delta tanto la replicación inicial completa es costosa en términos de ancho de banda o el tiempo. Por ejemplo, cuando el tamaño de archivo HRL pilas hasta el 50 por ciento del tamaño total del disco, la máquina virtual está marcada para la sincronización. Sincronización minimiza la cantidad de datos enviados a través de la red calculando los totales de los discos de máquina virtual de origen y destino y enviando sólo la diferencial.

Cuando termine de sincronización, debe reanudar replicación delta normal. Puede reanudar la sincronización si se produce una interrupción de la red o interrupciones del otro.

De forma predeterminada, la sincronización programada automáticamente está configurado para ocurran fuera del horario de trabajo. Si la máquina virtual debe ser sincronizar manualmente, seleccione la máquina virtual desde el portal y haga clic en **sincronizar**.

![Sincronización manual](media/site-recovery-understanding-site-to-azure-protection/image04.png)

Sincronización usa un algoritmo de fragmentación de bloque fixed donde los archivos de origen y destino se dividen en fragmentos fijos. Totales para cada fragmento se genera y, a continuación, se comparan para determinar qué bloques del origen necesitan que se aplique el destino.

### <a name="retry-logic"></a>Lógica de reintento
Hay lógica de reintento integrado para errores de replicación. Esta lógica se pueden clasificar en dos categorías:

| Categoría                  | Escenarios                                    |
|---------------------------|----------------------------------------------|
| Error no recuperable     | No se intenta ningún reintento. Estado de la replicación de máquina virtual es **crítico**y se requiere la intervención del administrador. Algunos ejemplos: <ul><li>Cadena de disco duro virtual roto</li><li>Estado no válido para la máquina virtual de réplica</li><li>Error de autenticación de red</li><li>Error de autorización</li><li>Máquina virtual que no se encuentra, en el caso de un servidor independiente Hyper-V</li></ul>|
| Error recuperable         | Reintentos producen cada intervalo de replicación usando un exponencial interrupción que aumenta el intervalo entre intentos de inicio de la primera vez que intenta (1, 2, 4, 8, 10 minutos). Si el error persiste, vuelva a intentar cada 30 minutos. Algunos ejemplos: <ul><li>Error de red</li><li>Poco espacio en disco</li><li>Condición de memoria insuficiente</li></ul>|

## <a name="hyper-v-virtual-machine-protection-and-recovery-life-cycle"></a>Ciclo de vida de protección y recuperación de máquina virtual Hyper-V

![Ciclo de vida de protección y recuperación de máquina virtual Hyper-V](media/site-recovery-understanding-site-to-azure-protection/image05.png)

## <a name="other-references"></a>Otras referencias

- [Supervisar y solucionar problemas de protección para los sitios de VMware, VMM, Hyper-V y físicos](./site-recovery-monitoring-and-troubleshooting.md)
- [Llegar para Microsoft Support](./site-recovery-monitoring-and-troubleshooting.md#reaching-out-for-microsoft-support)
- [Errores comunes de recuperación de sitios de Azure y sus soluciones](./site-recovery-monitoring-and-troubleshooting.md#common-asr-errors-and-their-resolutions)

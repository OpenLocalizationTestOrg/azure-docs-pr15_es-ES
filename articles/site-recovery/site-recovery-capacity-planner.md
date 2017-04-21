<properties
    pageTitle="Planear la capacidad para proteger máquinas virtuales y servidores físicos de recuperación de sitios de Azure | Microsoft Azure"
    description="Recuperación de sitio de Azure coordenadas la replicación, la migración tras error y la recuperación de máquinas virtuales y servidores físicos ubicados en local a Azure o a un sitio secundario en local." 
    services="site-recovery" 
    documentationCenter="" 
    authors="rayne-wiselman" 
    manager="jwhit" 
    editor=""/>

<tags 
    ms.service="site-recovery" 
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery" 
    ms.date="07/12/2016" 
    ms.author="raynew"/>

# <a name="plan-capacity-for-protecting-virtual-machines-and-physical-servers-in-azure-site-recovery"></a>Planear la capacidad para proteger máquinas virtuales y servidores físicos de recuperación de sitios de Azure

La herramienta de planificador de capacidad de recuperación de sitio de Azure le ayuda a averiguar los requisitos de capacidad de proteger máquinas virtuales de Hyper-V, máquinas virtuales de VMware y Windows/Linux servidores físicos con la recuperación de sitio de Azure.


## <a name="overview"></a>Información general

Use el programador de capacidad de recuperación de sitio para analizar el entorno de origen y las cargas de trabajo y a continuación, calcular las necesidades de ancho de banda, recursos de servidor que necesita en la ubicación de origen y los recursos (máquinas virtuales y almacenamiento etcetera), que necesitará en la ubicación de destino. 

Puede ejecutar la herramienta de dos modos:

- **Diseño rápido**: ejecutar la herramienta en este modo para obtener proyecciones de red y del servidor en función de un número medio de máquinas virtuales, discos, almacenamiento y cambio de tasa.
- **Planificación detallada**: ejecute la herramienta en este modo y proporcione información de cada carga de trabajo en el nivel de la máquina virtual. Analizar la compatibilidad VM y obtener proyecciones de red y del servidor.

## <a name="before-you-start"></a>Antes de empezar

Antes de ejecutar la herramienta:

1. Recopilar información acerca de su entorno, incluidos VM, discos por VM, almacenamiento por disco.
2. Identificar la velocidad de cambio (renovación) diaria de datos duplicados. Para hacer esto:

    - Si está replicar máquinas virtuales de Hyper-V, descargue la [herramienta de planificación de capacidad de Hyper-V](https://www.microsoft.com/download/details.aspx?id=39057) para obtener la tasa de cambios. [Obtenga más](site-recovery-capacity-planning-for-hyper-v-replication.md) información acerca de esta herramienta. Se recomienda que ejecutar esta herramienta en una semana para capturar promedios.
    - Si va a replicar máquinas virtuales VMware, utilice el [dispositivo de planificación de la capacidad de vSphere](https://labs.vmware.com/flings/vsphere-replication-capacity-planning-appliance) para calcular la tasa de renovación.
    - Si va a replicar servidores físicos debe estimar manualmente.

## <a name="run-the-quick-planner"></a>Ejecutar el organizador rápido
1.  Descargar y abrir la herramienta [Planificador de capacidad de recuperación de sitio de Azure](http://aka.ms/asr-capacity-planner-excel) . Debe ejecutar macros así que seleccione esta opción para habilitar la edición y habilitar contenido cuando se le solicite. 
2.  En **Seleccione un tipo de planificador** seleccione **Planificador rápido** en el cuadro de lista.

    ![Introducción](./media/site-recovery-capacity-planner/getting-started.png)

3.  En la hoja de cálculo de **Planificador de capacidad** escriba la información requerida. Rellene todos los campos con un círculo rojo en la siguiente captura de pantalla.

    - En **Seleccionar el escenario** elija **Hyper-V para Azure** o **VMware o físico en Azure**.
    - En **datos diarios promedio cambian tasa (%)** coloca en la información se recopilan mediante la [herramienta de planificación de capacidad de Hyper-V](site-recovery-capacity-planning-for-hyper-v-replication.md) o el [dispositivo de planificación de la capacidad de vSphere](https://labs.vmware.com/flings/vsphere-replication-capacity-planning-appliance).  
    - **Compresión** solo se aplica a la compresión ofrecida al replicar máquinas virtuales de VMware o servidores físicos en Azure. Nos estimar 30% o más, pero se puede modificar la configuración según sea necesario. Para replicar máquinas virtuales de Hyper-V en Azure compresión que puede usar un dispositivo de terceros como Riverbed. 
    -  En **Entradas de retención** especificar cuánto tiempo deben conservarse réplicas. Si está replicar VMware o el valor de entrada de servidores físicos en días. Si va a replicar Hyper-V especificar el tiempo en horas.
    -  En **número de horas que la replicación inicial del lote de máquinas virtuales debe completar** y el **número de máquinas virtuales por lotes de replicación inicial** entrada de configuración que se usa para calcular los requisitos de replicación inicial.  Cuando se implementa el sitio de recuperación deben cargar todo el conjunto de datos inicial. 

    ![Entradas](./media/site-recovery-capacity-planner/inputs.png)

2.  Una vez que haya colocado en los valores para el entorno de origen, salida mostrada incluye:

    - **Ancho de banda necesarios para la replicación de delta** (MB/seg). Ancho de banda para la replicación de delta se calcula la tasa de cambio promedio diaria de datos.
    - **Ancho de banda necesarios para la replicación inicial** (MB/seg). Ancho de banda para la replicación inicial se calcula en los valores de replicación inicial que se ponga en. 
    - **Almacenamiento requiere (en GB)** es el almacenamiento de Azure total necesario.
    - Se calcula según el tamaño de la unidad de 8 K IOPS en las cuentas de almacenamiento estándar total de **IOPS total en cuentas de almacenamiento estándar** .  El organizador rápido que se calcula el número basado en todos los discos de máquinas virtuales de origen y los datos diaria cambiar tasa. Tasa en máquinas virtuales de esas cambiar los planes detallada que el número se calcula según el número total de máquinas virtuales que se asignan a estándar máquinas virtuales de Azure y datos. 
    - **Número de cuentas de almacenamiento estándar** proporciona el número total de cuentas de almacenamiento estándar es necesario para proteger las máquinas virtuales. Tenga en cuenta que una cuenta de almacenamiento estándar puede contener hasta 20.000 IOPS a través de todas las máquinas virtuales en un almacenamiento estándar y máximas 500 IOPS admitidos por disco. 
    - **Número de discos blob necesarios** proporciona el número de discos que se creará en el almacenamiento de Azure.
    - **Número de cuentas de almacenamiento premium necesarios** proporciona el número total de cuentas de almacenamiento premium es necesario proteger las máquinas virtuales. Observe que un origen de máquina virtual con alta IOPS (mayor que 20000) necesita una cuenta de almacenamiento premium. Una cuenta de almacenamiento premium puede contener hasta 80.000 IOPS.
    - Se calcula según el tamaño de la unidad de 256 K IOPS en las cuentas de almacenamiento total premium de **IOPS total de almacenamiento de premium** .  El organizador rápido que se calcula el número basado en todos los discos de máquinas virtuales de origen y los datos diaria cambiar tasa. Tasa en máquinas virtuales de esas cambiar los planes detallada que el número se calcula en función del número total de máquinas virtuales que se asignan a premium Azure VM (serie DS y GS) y los datos. 
    - **Número de servidores de configuración necesarios** muestra cuántos servidores de configuración son necesarios para la implementación (1)
    - **Número de servidores de procesos adicionales necesarios** muestra si los servidores de proceso adicional son necesarios además del servidor del proceso que está configurado en el servidor de configuración de forma predeterminada.
    - **100% de almacenamiento adicional en el origen de** muestra si se necesita almacenamiento adicional en la ubicación de origen.
            
    ![Salida](./media/site-recovery-capacity-planner/output.png)
 
## <a name="run-the-detailed-planner"></a>Ejecutar el organizador detallada


1.  Descargar y abrir la herramienta [Planificador de capacidad de recuperación de sitio de Azure](http://aka.ms/asr-capacity-planner-excel) . Debe ejecutar macros así que seleccione esta opción para habilitar la edición y habilitar contenido cuando se le solicite. 
2.  En **Seleccione un tipo de planificador** seleccione **Planificador detallada** del cuadro de lista.

    ![Introducción](./media/site-recovery-capacity-planner/getting-started-2.png)

3.  En la hoja de cálculo de **Calificación de carga de trabajo** , escriba la información requerida. Rellene todos los campos marcados.

    - En **núcleos de procesador** especifique el número total de núcleos en un servidor de origen.
    - En la **asignación de memoria en MB** especifique el tamaño de RAM de un servidor de origen. 
    - El **Número de NIC** especifique el número de adaptadores de red en un servidor de origen. 
    -  En el **almacenamiento Total (en GB)** , especifique el tamaño total del almacenamiento de máquina virtual. Por ejemplo, si el servidor de origen tiene 3 discos de 500 GB, el tamaño de almacenamiento total es 1500 GB.
    -  Número **de discos conectados** , especifique el número total de discos de un servidor de origen.
    -  En la **utilización de la capacidad de disco** , especifique la utilización Media.
    -  En **cambio diaria tasa (%)** , especifique los datos diarios cambiarán la frecuencia de un servidor de origen.
    -  En **tamaño de la asignación de Azure** escriba tamaño de Azure VM que desea asignar. Si no desea hacerlo manualmente, haga clic en las**Máquinas virtuales de IaaS calcular**. Tenga en cuenta que si la configuración manual de entrada y, a continuación, haga clic en calcular máquinas virtuales de IaaS su configuración manual podría sobrescribirán porque el proceso de cálculo identifica automáticamente el tamaño de Azure VM que mejor coincide.

    ![Calificación de carga de trabajo](./media/site-recovery-capacity-planner/workload-qualification.png)

4.  Si hace clic en **Calcular máquinas virtuales de IaaS** aquí es lo que hace:

    - Valida las entradas obligatorio.
    - Calcula IOPS y se sugiere la máquina virtual de Azure mejor aize coincidencia para cada VM que es elegible para la replicación de Azure. Si no se detecta un tamaño apropiado de Azure VM que haya emitido un error. Por ejemplo, si el número de discos 65 adjunto en un error problemas desde el mayor tamaño Azure VM es 64.
    - Sugiere una cuenta de almacenamiento que se pueden usar para una máquina virtual de Azure.
    - Calcula el número total de cuentas de almacenamiento estándar y de premium almacenamiento necesarios para la carga de trabajo. Desplácese hacia abajo a la derecha para ver el tipo de almacenamiento de Azure y la cuenta de almacenamiento que se pueden usar para un servidor de origen
    - Completa y ordena el resto de la tabla en función de tipo de almacenamiento necesario (estándar o premium) asignado para una máquina virtual y el número de discos conectados. Para todas las máquinas virtuales que cumplen los requisitos para las copias de seguridad Azure, columna A (es VM cualificado?) muestran sí. Si no puede ser copia hasta una máquina virtual se muestra Azure un error.

Columnas AA a AE salen y proporcionan información para cada máquina virtual.

![Calificación de carga de trabajo](./media/site-recovery-capacity-planner/workload-qualification-2.png)


### <a name="example"></a>Ejemplo
Por ejemplo, para seis máquinas virtuales con los valores que aparecen en la tabla, la herramienta calcula y asigna la mejor coincidencia de Azure VM y los requisitos de almacenamiento de Azure.

![Calificación de carga de trabajo](./media/site-recovery-capacity-planner/workload-qualification-3.png)

- En el resultado para que el ejemplo tenga en cuenta lo siguiente:
    
    - La primera columna es una columna de validación para máquinas virtuales, discos y renovación.
    - Dos cuentas de almacenamiento estándar y cuenta de almacenamiento de una prima son necesarias para máquinas virtuales de cinco. 
    -  VM3 no cumple los requisitos para la protección porque uno o más discos son más de 1 TB.
    -  VM1 y VM2 pueden usar la primera cuenta de almacenamiento estándar
    -  VM4 puede usar la segunda cuenta de almacenamiento estándar.
    -  VM5 y VM6 necesitan una cuenta de almacenamiento premium y pueden usar tanto una sola cuenta.

    >[AZURE.NOTE]  Se calculan IOPS en almacenamiento estándar y premium en el nivel de la máquina virtual y no en el nivel de disco. Una máquina virtual estándar puede controlar hasta 500 IOPS por disco. Si es superiores a 500 IOPS para un disco tendrá almacenamiento premium. Sin embargo si IOPS para un disco son más de 500 pero IOPS para los discos VM total dentro de los límites de Azure VM estándares de soporte técnico (tamaño de memoria virtual, número de discos, número de adaptadores, CPU, memoria), a continuación, el organizador elige un estándar VM y no el DS o GS series. Debe actualizar manualmente la celda de Azure tamaño de asignación con una serie de DS o GS apropiada VM.

5. Una vez todos los detalles, haga clic en **enviar datos a la herramienta de organizador** para abrir el **Programador de capacidad** se resaltan las cargas de trabajo para mostrar si es elegibles para la protección o no.


### <a name="submit-data-in-the-capacity-planner"></a>Enviar los datos en el programador de capacidad

1.  Al abrir la hoja de cálculo de **Planificador de capacidad** se rellena en función de la configuración que haya especificado. La palabra 'Carga de trabajo' aparece en la celda de **origen de entradas de Infra** para mostrar la hoja de cálculo de **Calificación de carga de trabajo de** entrada. 
2.  Si desea realizar cambios, que tendrá que modificar la hoja de cálculo de **Calificación de carga de trabajo** y haga clic en enviar datos a la herramienta de planificador de nuevo.  

    ![Planificador de capacidad](./media/site-recovery-capacity-planner/capacity-planner.png)



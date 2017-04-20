<properties
   pageTitle="Solucionar problemas de lento copia de seguridad de archivos y carpetas de copia de seguridad de Azure | Microsoft Azure"
   description="Se proporcionan pautas de solución de problemas para ayudarle a diagnosticar la causa de problemas de rendimiento de copia de seguridad de Azure"
   services="backup"
   documentationCenter=""
   authors="genlin"
   manager="jimpark"
   editor=""/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/13/2016"
    ms.author="genli"/>

# <a name="troubleshoot-slow-backup-of-files-and-folders-in-azure-backup"></a>Solucionar problemas de lento copia de seguridad de archivos y carpetas de copia de seguridad de Azure

Este artículo proporcionan instrucciones de solución de problemas para ayudarle a diagnosticar la causa del rendimiento lento de copia de seguridad de archivos y carpetas cuando usa copia de seguridad de Azure. Cuando se utiliza el agente de copia de seguridad de Azure para copias de seguridad, el proceso de copia de seguridad puede tardar más de lo esperado. Este retraso puede deberse a uno o varios de estos procedimientos:

-   [Hay botella de rendimiento en el equipo que se copia.](#cause1)
-   [Otro proceso o el software antivirus está interfiriendo con el proceso de copia de seguridad de Azure.](#cause2)
-   [El agente de copia de seguridad se ejecuta en una máquina virtual Azure (VM).](#cause3)  
-   [Es una copia de seguridad un gran número (millones) de archivos.](#cause4)

Antes de iniciar la solución de problemas, le recomendamos que descargue e instale el [agente de copia de seguridad de Azure más reciente](http://aka.ms/azurebackup_agent). Ofrecemos actualizaciones frecuentes el agente de copia de seguridad para corregir problemas diversos, agregar características y mejorar el rendimiento.

Se recomienda encarecidamente que revise las [preguntas más frecuentes de copia de seguridad de Azure servicio](backup-azure-backup-faq.md) para asegurarse de que no experimenta cualquiera de los problemas de configuración comunes.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

<a id="cause1"></a>
## <a name="cause-performance-bottlenecks-on-the-computer"></a>Causa: Botella de rendimiento en el equipo

Botella en el equipo que la copia de seguridad puede provocar retrasos. Por ejemplo, la capacidad del equipo para leer o escribir en disco o ancho de banda disponible para enviar datos a la red, puede provocar botella.

Windows proporciona una herramienta integrada que ha llamado [Monitor de rendimiento](https://technet.microsoft.com/magazine/2008.08.pulse.aspx) (Monitor de rendimiento) para detectar estos botella.

Estas son algunas contadores de rendimiento y los rangos que pueden resultar útiles para diagnosticar botella óptimo copias de seguridad.

| Contador  | Estado  |
|---|---|
|Disco lógico (disco físico)--% inactivo   | • 100% inactivo al 50% inactivo = correcto</br>• 49% inactivo 20% inactivo = advertencia o Monitor</br>• 19% inactivo al 0% inactivo = crítico o fuera de la especificación|
|  Disco lógico (disco físico)--% promedio Segundos lectura o escritura |  • 0,001 ms a ms 0,015 = correcto</br>• 0,015 ms a ms 0.025 = advertencia o Monitor</br>• ms 0.026 o ya = crítico o fuera de la especificación|
|  Disco lógico (disco físico): longitud de cola de disco actual (para todas las instancias) | 80 solicitudes durante más de 6 minutos |
| Memoria: grupo no pagina Bytes|• Menos de 60% de agrupación consumida = correcto<br>• 61 al 80% de agrupación consumida = advertencia o Monitor</br>• Mayor grupo 80% consumida = crítico o fuera de la especificación|
| Memoria: grupo pagina Bytes |• Menos de 60% de agrupación consumida = correcto</br>• 61 al 80% de agrupación consumida = advertencia o Monitor</br>• Mayor grupo 80% consumida = crítico o fuera de la especificación|
| Memoria: Megabytes disponibles| • 50% de memoria libre disponible o más = correcto</br>• 25% de memoria libre disponible = Monitor</br>• 10% de memoria libre disponible = advertencia</br>• Menor que 100 MB o 5% de memoria libre disponible = crítico o fuera de la especificación|
|Procesador:\%tiempo de procesador (todas las instancias)|• Menos de 60% consumido = correcto</br>• de 61 a 90% consumido = Monitor o precaución</br>• del 91 al 100% consumido = tareas críticas|


> [AZURE.NOTE] Si determina que la infraestructura es el culpable, se recomienda que desfragmente los discos periódicamente para mejorar el rendimiento.

<a id="cause2"></a>
## <a name="cause-another-process-or-antivirus-software-interfering-with-azure-backup"></a>Causa: Otro proceso o software antivirus interferir con copia de seguridad de Azure

Hemos visto varias instancias donde otros procesos en el sistema de Windows negativo han afectado el rendimiento del proceso de agente de copia de seguridad de Azure. Por ejemplo, si utiliza el agente de copia de seguridad de Azure y otro programa para copiar los datos o si el software antivirus está ejecutando y tiene un bloqueo en archivos de copia de seguridad, el múltiplo se bloquea en archivos podrían causar conflictos. En esta situación, puede producir un error en la copia de seguridad o el trabajo puede tardar más de lo esperado.

Mejor en este escenario se recomienda desactivar el otro programa de copia de seguridad para ver si cambia el tiempo de copia de seguridad para el agente de copia de seguridad de Azure. Normalmente, es suficiente para impedir que se afecten entre sí y asegúrese de que no se ejecutan varios trabajos de copia de seguridad al mismo tiempo.

Para los programas de antivirus, se recomienda que excluya los siguientes archivos y ubicaciones:

- Agent\bin\cbengine.exe C:\Program Files\Microsoft Azure recuperación servicios como un proceso
- C:\Program Files\Microsoft Azure recuperación servicios Agent\ carpetas
- Ubicación de borrador (si no está utilizando la ubicación estándar)

<a id="cause3"></a>
## <a name="cause-backup-agent-running-on-an-azure-virtual-machine"></a>Causa: Agente de copia de seguridad se ejecuta en una máquina virtual Azure

Si está ejecutando al agente de copia de seguridad en una máquina virtual, rendimiento será más lento que cuando se ejecuta en una máquina física. Esto se debe a limitaciones de IOPS.  Sin embargo, se puede optimizar el rendimiento al cambiar las unidades de datos que se copian al almacenamiento de Azure Premium. Estamos trabajando para solucionar este problema, y la corrección estará disponible en una versión futura.

<a id="cause4"></a>
## <a name="cause-backing-up-a-large-number-millions-of-files"></a>Causa: Copia de un gran número (millones) de archivos

Mover un gran volumen de datos tardará más de mover un volumen más pequeño de datos. En algunos casos, el tiempo de copia de seguridad está relacionado con no sólo el tamaño de los datos, pero también el número de archivos o carpetas. Esto es cierto cuando se hace una copia de seguridad de millones de archivos pequeños (unos pocos bytes a unos cuantos kilobytes).

Esto ocurre porque mientras es una copia de seguridad de los datos y mover a Azure, Azure es clasificación simultáneamente los archivos. En algunos casos poco comunes, la operación de catálogo puede tardar más de lo esperado.

Los siguientes indicadores pueden ayudarle a entender la botella y trabajar en consecuencia en los siguientes pasos:

- **Interfaz de usuario muestra el progreso de la transferencia de datos**. Aún se transfiere los datos. El ancho de banda o el tamaño de datos podría estar provocando retrasos.

- **Interfaz de usuario no muestra el progreso de la transferencia de datos**. Abra los registros que se encuentra en Agent\Temp de servicios de recuperación de Azure C:\Microsoft y, a continuación, busque la entrada FileProvider::EndData en los registros. Esta entrada indica que finalice la transferencia de datos y sucede la operación de catálogo. No cancelar los trabajos de copia de seguridad. En su lugar, espere un poco más finalizar la operación de catálogo. Si el problema continúa, póngase en contacto con [Azure](https://portal.azure.com/#create/Microsoft.Support).

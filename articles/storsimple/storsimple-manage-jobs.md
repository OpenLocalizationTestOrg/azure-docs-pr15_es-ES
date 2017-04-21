<properties 
   pageTitle="Ver y administrar trabajos de StorSimple | Microsoft Azure"
   description="Describe la página de trabajos de servicio de StorSimple Manager y cómo usarlo para realizar un seguimiento de los trabajos de copia de seguridad recientes, actuales y programados."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor=""/>
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-view-and-manage-storsimple-jobs"></a>Usar el servicio Administrador de StorSimple para ver y administrar trabajos StorSimple

[AZURE.INCLUDE [storsimple-version-selector-manage-jobs](../../includes/storsimple-version-selector-manage-jobs.md)]

## <a name="overview"></a>Información general

La página de **trabajos** proporciona un solo portal central para ver y administrar los trabajos que se han iniciado en dispositivos conectados a su servicio de administrador de StorSimple. Puede ver las tareas programadas, en ejecución, completadas y errores de varios dispositivos. Los resultados se presentan en un formato tabular. 

![Página trabajos](./media/storsimple-manage-jobs/HCS_JobsPage.png)

Puede encontrar rápidamente las tareas que le interesa filtrando en campos como:

- **Estado** : pueden estar ejecutándose trabajos programada, error, completado, cancelar o cancelado.

- **Tipo** : trabajos pueden crearse como resultado un programada o una copia de seguridad a petición (**Realizar copia de seguridad**), clonar, una restauración de dispositivo o una operación de actualización.

- **Dispositivos** : trabajos se inician en un determinado dispositivo conectado a su servicio.

- **Desde y hasta** : se pueden filtrar tareas según el intervalo de fecha y hora.

Los trabajos filtrados, a continuación, se hayan publicado en función de los siguientes atributos:

- **Tipo** : copia de seguridad, clonar, restaurar, migración tras error o actualización.

- **Estado** : la ejecución programada, error, completado, cancelar o cancelado.

- **Entidad** : los trabajos se pueden asociados con un volumen, una directiva de copia de seguridad o un dispositivo. Un trabajo clonar está asociado con un volumen, mientras que un trabajo de copia de seguridad programado está asociado a una directiva de copia de seguridad. Se crea una tarea de dispositivo como resultado una recuperación (DR) o una operación de restauración.

- **Dispositivo** : el nombre del dispositivo en que se ha iniciado el trabajo.

- **Iniciar en** : la hora de inicio de la tarea.

- **Curso** : el porcentaje completado de una tarea de ejecución. Para una tarea completada, siempre debe 100%.

La lista de trabajos se actualiza cada 30 segundos.

Puede realizar las siguientes acciones relacionadas con el trabajo en esta página:

- Ver detalles del trabajo

- Cancelar un trabajo

## <a name="view-job-details"></a>Ver detalles del trabajo

Realice los pasos siguientes para ver los detalles de cualquier tarea.

#### <a name="to-view-job-details"></a>Para ver los detalles del trabajo

1. En la página **tareas** , mostrar los trabajos que le interesa, ejecute una consulta con los filtros apropiados. Puede buscar completado, ejecuta o cancelar trabajos.

2. Seleccione una tarea.

3. En la parte inferior de la página, haga clic en **Detalles**.

4. En el cuadro de diálogo **Detalles de la tarea de copia de seguridad** , puede ver el estado, detalles, estadísticas de tiempo y estadísticas de datos.

## <a name="cancel-a-job"></a>Cancelar un trabajo

Realice los pasos siguientes para cancelar un trabajo en ejecución.

### <a name="to-cancel-a-job"></a>Cancelar un trabajo

1. En la página **tareas** , mostrar los trabajos en curso que desee cancelar, ejecute una consulta con los filtros apropiados.

1. Seleccione el trabajo.

1. En la parte inferior de la página, haga clic en **Cancelar**.

1. Cuando se le solicite confirmación, haga clic en **Sí**.

Este trabajo se cancela ahora.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre cómo [administrar las directivas de copia de seguridad de StorSimple](storsimple-manage-backup-policies.md).

- Obtenga información sobre cómo [usar el servicio de administrador de StorSimple para administrar el dispositivo de StorSimple](storsimple-manager-service-administration.md).

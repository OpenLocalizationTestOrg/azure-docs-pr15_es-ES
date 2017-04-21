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

# <a name="use-the-storsimple-manager-service-to-view-and-manage-storsimple-jobs-update-2"></a>Usar el servicio Administrador de StorSimple para ver y administrar trabajos de StorSimple (actualización de 2)

[AZURE.INCLUDE [storsimple-version-selector-manage-jobs](../../includes/storsimple-version-selector-manage-jobs.md)]

## <a name="overview"></a>Información general

La página de **trabajos** proporciona un solo portal central para ver y administrar los trabajos que se han iniciado en dispositivos conectados a su servicio de administrador de StorSimple. Puede ver las tareas programadas, ejecución, completadas, canceladas y errores de varios dispositivos. Los resultados se presentan en un formato tabular. 

![Página trabajos](./media/storsimple-manage-jobs-u2/jobs.png)

Puede encontrar rápidamente las tareas que le interesa filtrando en campos como:

- **Estado** : se ejecuta trabajos, completado, cancelado, error, cancelación o completado con errores.
- **Desde y hasta** : se pueden filtrar tareas según el intervalo de fecha y hora.
- **Tipo** : el tipo de trabajo puede ser una copia de seguridad, copia de seguridad manual, restaurar, clonar, migración tras error de dispositivo, crear volumen localmente anclada, modificar volumen, actualizar, paquete o dispositivo virtual de aprovisionamiento de soporte técnico.

- **Dispositivos** : trabajos se inician en un determinado dispositivo conectado a su servicio.
Los trabajos filtrados, a continuación, se hayan publicado en función de los siguientes atributos:

    - **Tipo** : copia de seguridad, copia de seguridad manual, restaurar, clonar, migración tras error de dispositivo, crear volumen localmente anclada, modificar volumen, actualizar, paquete o dispositivo virtual de aprovisionamiento de soporte técnico.

    - **Estado** : ejecutando, completado, cancelado, error, cancelación o completado con errores

    - **Entidad** : los trabajos se pueden asociados con un volumen, una directiva de copia de seguridad o un dispositivo. Por ejemplo, un trabajo clonar está asociado con un volumen, mientras que un trabajo de copia de seguridad programado está asociado a una directiva de copia de seguridad. Se crea una tarea de dispositivo como resultado una recuperación (DR) o una operación de restauración.

    - **Dispositivo** : el nombre del dispositivo en que se ha iniciado el trabajo.

    - **Introducción en** : la hora de inicio de la tarea.

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
 
    ![Página de detalles de la tarea](./media/storsimple-manage-jobs-u2/JobDetails.png)

## <a name="cancel-a-job"></a>Cancelar un trabajo

Realice los pasos siguientes para cancelar un trabajo en ejecución.

>[AZURE.NOTE] Algunas tareas, como modificar un volumen para cambiar el tipo de volumen o expandir un volumen, no se puede cancelar.

### <a name="to-cancel-a-job"></a>Cancelar un trabajo

1. En la página **tareas** , mostrar los trabajos en curso que desee cancelar, ejecute una consulta con los filtros apropiados.

1. Seleccione el trabajo.

1. En la parte inferior de la página, haga clic en **Cancelar**.

1. Cuando se le solicite confirmación, haga clic en **Sí**.

Este trabajo se cancela ahora.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre cómo [administrar las directivas de copia de seguridad de StorSimple](storsimple-manage-backup-policies.md).

- Obtenga información sobre cómo [usar el servicio de administrador de StorSimple para administrar el dispositivo de StorSimple](storsimple-manager-service-administration.md).

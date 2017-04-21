<properties 
   pageTitle="Trabajos de copia de seguridad de administrador de instantáneas StorSimple | Microsoft Azure"
   description="Describe cómo usar el complemento de MMC StorSimple instantánea administrador para ver y administrar trabajos de copia de seguridad programados, que se está ejecutando y completados."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="04/26/2016"
   ms.author="v-sharos" />


# <a name="use-storsimple-snapshot-manager-to-view-and-manage-backup-jobs"></a>Usar el Administrador de instantánea StorSimple para ver y administrar trabajos de copia de seguridad

## <a name="overview"></a>Información general

El nodo de **trabajos** en el panel de **alcance** muestra los **programada**, **últimas 24 horas**y **ejecuta** tareas de copia de seguridad que ha iniciado de forma interactiva o por una directiva configurada. 

En este tutorial se explica cómo puede usar el nodo **trabajos** para mostrar información acerca de los trabajos de copia de seguridad programadas, recientes y que se está ejecutando. (La lista de tareas y la información correspondiente aparece en el panel de **resultados** ). Además, puede haga un trabajo de lista y ver un menú contextual que muestra las acciones disponibles.

## <a name="view-scheduled-jobs"></a>Ver las tareas programadas

Utilice el procedimiento siguiente para ver las tareas programadas de copia de seguridad.

#### <a name="to-view-scheduled-jobs"></a>Para ver las tareas programadas

1. Haga clic en el icono del escritorio para iniciar el Administrador de instantánea StorSimple. 

2. En el panel de **ámbito** , expanda el nodo de **trabajos** y haga clic en **programada**. La siguiente información aparece en el panel de **resultados** :

    - **Nombre** : el nombre de la instantánea programada

    - **Ejecutar siguiente** : la fecha y hora de la siguiente instantánea programada

    - **Última ejecución** : la fecha y hora de la instantánea programada más reciente

    >[AZURE.NOTE] Las instantáneas solo una sola vez, **El siguiente ejecute** y **Último** será el mismo. 
 
    ![Programar trabajos de copia de seguridad](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_scheduled.png) 
 
3. Para realizar acciones adicionales en una tarea específica, haga clic en el nombre del trabajo en el panel de **resultados** y seleccione las opciones de menú.

## <a name="view-recent-jobs"></a>Ver tareas recientes

Use el procedimiento siguiente para ver la copia de seguridad y restaurar las tareas que se completaron en las últimas 24 horas.

#### <a name="to-view-recent-jobs"></a>Para ver los trabajos recientes

1. Haga clic en el icono del escritorio para iniciar el Administrador de instantánea StorSimple.

2. En el panel de **ámbito** , expanda el nodo de **trabajos** y haga clic en las **últimas 24 horas**. El panel de **resultados** muestra los trabajos de copia de seguridad de las últimas 24 horas (hasta un máximo de 64 trabajos). La siguiente información aparece en el panel de **resultados** , dependiendo de las opciones de **vista** que especifique:

    - **Nombre** : el nombre de la instantánea programada.
 
    - **Introducción** : la fecha y hora cuando inició la instantánea.

    - **Detenido** : la fecha y hora de la instantánea terminado o ha finalizado.

    - Tiempo **transcurrido** : la cantidad de tiempo entre la **Introducción** y **su interrupción** .

    - **Estado** : el estado del trabajo completado recientemente. **Correcto** indica que la copia de seguridad se creó correctamente. **Error** indica que el trabajo no se ejecutó correctamente.

    - **Información** : la causa del error.

    - **Bytes procesados (MB)** : la cantidad de datos desde el grupo de volumen que se ha procesado (en MB). 

    ![Tareas que ha encontrado en las últimas 24 horas](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_Last_24_hours.png) 

3. Para realizar acciones adicionales en una tarea específica, haga clic en el nombre del trabajo en el panel de **resultados** y seleccione las opciones de menú.

    ![Eliminar un trabajo](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Delete_backup.png) 
     
## <a name="view-currently-running-jobs"></a>Permite ver las tareas que se está ejecutando

Use el siguiente procedimiento para ver las tareas que se estén ejecutando.

#### <a name="to-view-currently-running-jobs"></a>Para ver los trabajos que se está ejecutando

1. Haga clic en el icono del escritorio para iniciar el Administrador de instantánea StorSimple.

2. En el panel de **ámbito** , expanda el nodo de **trabajos** y haga clic en **Ejecutar**. Dependiendo de las opciones de **vista** que especifique, la siguiente información aparece en el panel de **resultados** : 

    - **Nombre** : el nombre de la instantánea programada.

    - **Introducción** : la fecha y hora cuando inició la instantánea.

    - **Punto de control** : la acción actual de la copia de seguridad.

    - **Estado** : el porcentaje de finalización.
    
    - **Transcurrido** : la cantidad de tiempo transcurrido desde el inicio de la copia de seguridad. 

    - **Rendimiento medio (MB)** : relación del total de bytes de datos que procesa al de tiempo total de procesamiento (MB).

    - **Bytes procesados (MB)** : número total de bytes de datos (en MB).

    - **Escritura de bytes (MB)** – bytes total de los datos escritos (en MB). Incluye los datos, así como los metadatos y, por tanto, es normalmente mayor que el de Bytes procesados.

    ![Tareas actualmente en ejecución](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_running.png)

3. Para realizar acciones adicionales en una tarea específica, haga clic en el nombre del trabajo en el panel de **resultados** y seleccione las opciones de menú.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre cómo [usar StorSimple instantánea Manager para administrar la solución StorSimple](storsimple-snapshot-manager-admin.md).
- Obtenga información sobre cómo [usar el Administrador de instantáneas StorSimple para administrar el catálogo de copia de seguridad](storsimple-snapshot-manager-manage-backup-catalog.md).















            


 


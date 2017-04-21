<properties 
   pageTitle="Ver y administrar trabajos de matriz Virtual de StorSimple | Microsoft Azure"
   description="Describe la página de trabajos de servicio de StorSimple Manager y cómo usarlo para realizar un seguimiento de los trabajos recientes y actuales para la matriz Virtual StorSimple."
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
   ms.workload="na"
   ms.date="06/07/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-view-jobs-for-the-storsimple-virtual-array"></a>Usar el servicio Administrador de StorSimple para ver los trabajos de la matriz Virtual StorSimple

## <a name="overview"></a>Información general

La página de **trabajos** proporciona un solo portal central para ver y administrar los trabajos iniciados en matrices Virtual (también conocido como local dispositivos virtuales) que están conectados a su servicio de administrador de StorSimple. Puede ver los trabajos de ejecución, completados y errores de varios dispositivos virtuales. Los resultados se presentan en un formato tabular. 

![Página trabajos](./media/storsimple-ova-manage-jobs/ovajobs1.png)

Puede encontrar rápidamente las tareas que le interesa filtrando en campos como:

- **Estado** : puede buscar todo, trabajos de ejecución, completados o error.
- **Desde y hasta** : se pueden filtrar tareas según el intervalo de fecha y hora.
- **Tipo** : el tipo de trabajo puede ser una copia de seguridad, restaurar todos, migración tras error, descargar actualizaciones o instalar actualizaciones.
- **Dispositivos** : trabajos se inician en un dispositivo específico conectado a su servicio. Los trabajos filtrados, a continuación, se hayan publicado en función de los siguientes atributos:

    - **Tipo** : el tipo de trabajo puede ser una copia de seguridad, restaurar todos, migración tras error, descargar actualizaciones o instalar actualizaciones.

    - **Estado** : puede trabajos ser todo, ejecutar, completado, o error.

    - **Entidad** : los trabajos se pueden asociados con volumen, compartir o dispositivo. 

    - **Dispositivo** : el nombre del dispositivo en que se ha iniciado el trabajo.

    - **Introducción en** : la hora de inicio de la tarea.

    - **Curso** : el porcentaje completado de una tarea de ejecución. Para una tarea completada, siempre debe 100%.

La lista de trabajos se actualiza cada 30 segundos.

## <a name="view-job-details"></a>Ver detalles del trabajo

Realice los pasos siguientes para ver los detalles de cualquier tarea.

#### <a name="to-view-job-details"></a>Para ver los detalles del trabajo

1. En la página **tareas** , mostrar los trabajos que le interesa, ejecute una consulta con los filtros apropiados. Puede buscar las tareas completadas o se está ejecutando.

2. Seleccione un trabajo de la lista de trabajos tabular.

3. En la parte inferior de la página, haga clic en **Detalles**.

4. En el cuadro de diálogo **Detalles** , puede ver el estado, detalles y las estadísticas de tiempo. La siguiente ilustración muestra un ejemplo del cuadro de diálogo **Detalles de la tarea de copia de seguridad** .
 
    ![Página de detalles de la tarea](./media/storsimple-ova-manage-jobs/ovajobs2.png)

#### <a name="job-failures-when-the-virtual-machine-is-paused-in-the-hypervisor"></a>Errores del trabajo cuando la máquina virtual está en pausa en el hipervisor

Cuando una tarea está en curso en la matriz Virtual de StorSimple y el dispositivo (aprovisionado en hipervisor de máquina virtual) está en pausa para más de 15 minutos, se producirá un error en el trabajo. Esto se debido a su tiempo StorSimple Virtual matriz está sincronizada con la hora de Microsoft Azure. En la siguiente captura de pantalla se muestra un ejemplo de un error de trabajo de restauración.

![Restaurar el error de trabajo](./media/storsimple-ova-manage-jobs/restorejobfailure.png)

Estos errores se aplicarán a trabajos de copia de seguridad, restaurar, actualización y migración tras error. Si su máquina virtual está configurada en Hyper-V, el equipo sincronizará finalmente vez con el hipervisor. Una vez que esto sucede, puede reiniciar el trabajo. 

## <a name="next-steps"></a>Pasos siguientes

[Aprenda a usar la interfaz de usuario para administrar la matriz Virtual de StorSimple de web local](storsimple-ova-web-ui-admin.md).

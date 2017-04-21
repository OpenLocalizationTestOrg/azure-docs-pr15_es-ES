<properties 
   pageTitle="Tutorial de copia de seguridad de matriz Virtual de StorSimple | Microsoft Azure"
   description="Describe cómo hacer copia de seguridad de volúmenes y recursos compartidos de matriz Virtual de StorSimple."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="06/07/2016"
   ms.author="alkohli" />

# <a name="back-up-your-storsimple-virtual-array"></a>Realizar copias de seguridad de la matriz Virtual de StorSimple

## <a name="overview"></a>Información general 

En este tutorial se aplica a la versión de Microsoft Azure StorSimple matriz Virtual (también conocido como el dispositivo virtual de StorSimple local o dispositivo virtual de StorSimple) ejecución marzo de 2016 disponibilidad general (GA) o versiones posteriores.

La matriz Virtual StorSimple es un híbrido nube local virtual dispositivo de almacenamiento que puede configurarse como un servidor de archivos o en un servidor de iSCSI. Puede crear copias de seguridad, restaurar a partir de las copias de seguridad y realizar la migración tras error del dispositivo si es necesaria la recuperación. Cuando está configurado como un servidor de archivos, también permite la recuperación de nivel de elemento. Este tutorial describe cómo usar el portal de clásico Azure o la interfaz de usuario de StorSimple web para crear copias de seguridad manuales y programadas de la matriz Virtual de StorSimple.


## <a name="back-up-shares-and-volumes"></a>Realizar copias de seguridad de volúmenes y recursos compartidos

Realizar copias de seguridad ofrecen protección en un momento, mejorar la capacidad de recuperación y minimizar los tiempos de restauración para volúmenes y recursos compartidos. Puede haga una copia de un recurso compartido o volumen en el dispositivo StorSimple de dos maneras: **programada** o **Manual**. Cada uno de los métodos se describe en las secciones siguientes.

> [AZURE.NOTE] En esta versión, se crean copias de seguridad programadas por una directiva predeterminada que se ejecuta diariamente en un momento determinado y copias de seguridad de todos los recursos compartidos o volúmenes en el dispositivo. No es posible crear directivas personalizadas para copias de seguridad programadas en este momento.

## <a name="change-the-backup-schedule"></a>Cambiar la programación de copia de seguridad

El dispositivo virtual de StorSimple tiene una directiva de copia de seguridad predeterminados que se inicia en un momento determinado del día (22:30) y realiza una copia de seguridad de todos los recursos compartidos o volúmenes en el dispositivo una vez al día. Puede cambiar la hora a la que no se puede cambiar el inicio de la copia de seguridad, pero la frecuencia y la retención (que especifica el número de copias de seguridad para conservar). Durante las copias de seguridad, el dispositivo virtual completo se realizan copias de seguridad; por lo tanto, se recomienda que programe estas copias de seguridad para las horas.

Realice los pasos siguientes en el [portal de clásica Azure](https://manage.windowsazure.com/) para cambiar la hora de inicio de copia de seguridad de forma predeterminada.

#### <a name="to-change-the-start-time-for-the-default-backup-policy"></a>Para cambiar la hora de inicio para la directiva de copia de seguridad de forma predeterminada

1. Vaya a la ficha de **configuración** de dispositivo.

2. En la sección de **copia de seguridad** , especifique la hora de inicio de la copia de seguridad diaria.

3. Haga clic en **Guardar**.

### <a name="take-a-manual-backup"></a>Realice una copia de seguridad manual

Además de las copias de seguridad programadas, puede aprovechar las manuales (a petición) copia de seguridad en cualquier momento.

#### <a name="to-create-a-manual-on-demand-backup"></a>Para crear una copia de seguridad manual (a petición)

1. Vaya a la pestaña **recursos compartidos** o **volúmenes** .

2. En la parte inferior de la página, haga clic en **copia de seguridad de todos**. Se le pedirá para verificar que le gustaría deben llevarse a cabo la copia de seguridad. Haga clic en el icono de verificación ![Active icono](./media/storsimple-ova-backup/image3.png) para continuar con la copia de seguridad.

    ![confirmación de copia de seguridad](./media/storsimple-ova-backup/image4.png)

    Se le notificará que se inicia un trabajo de copia de seguridad.

    ![inicio de la copia de seguridad](./media/storsimple-ova-backup/image5.png)

    Se le notificará que el trabajo se creó correctamente.

    ![trabajo de copia de seguridad creada](./media/storsimple-ova-backup/image7.png)

3. Para realizar un seguimiento del progreso de la tarea, haga clic en **Vista de trabajo**.

4. Cuando haya finalizado el trabajo de copia de seguridad, vaya a la pestaña de **catálogo de copia de seguridad** . Debe ver la copia de seguridad completada.

    ![Copia de seguridad completada](./media/storsimple-ova-backup/image8.png)

5. Establezca las selecciones del filtro en el dispositivo adecuado, la directiva de copia de seguridad y el intervalo de tiempo y, a continuación, haga clic en el icono de verificación ![icono de comprobación](./media/storsimple-ova-backup/image3.png).

    La copia de seguridad debería aparecer en la lista de conjuntos de copia de seguridad que se muestra en el catálogo.

## <a name="view-existing-backups"></a>Ver las copias de seguridad existentes

Realice los pasos siguientes en el portal de Azure clásico para ver las copias de seguridad.

#### <a name="to-view-existing-backups"></a>Para ver las copias de seguridad existentes

1. En la página de servicio StorSimple Manager, haga clic en el **catálogo de copia de seguridad** .

2. Seleccione una copia de seguridad como sigue:

    1. Seleccione el dispositivo.

    2. En la lista desplegable, seleccione el recurso compartido o volumen para la copia de seguridad que desee seleccionar.

    3. Especifique el intervalo de tiempo.

    4. Haga clic en el icono de verificación ![](./media/storsimple-ova-backup/image3.png) para ejecutar esta consulta.

    Las copias de seguridad asociado con el recurso compartido seleccionado o volumen debería aparecer en la lista de conjuntos de copia de seguridad.

![video_icon](./media/storsimple-ova-backup/video_icon.png) **vídeo disponible**

Vea el vídeo para ver cómo puede crear recursos compartidos, realizar copias de seguridad de recursos compartidos y restaurar los datos en una matriz Virtual de StorSimple.

> [AZURE.VIDEO use-the-storsimple-virtual-array]

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre la [administración de la matriz Virtual de StorSimple](storsimple-ova-web-ui-admin.md).

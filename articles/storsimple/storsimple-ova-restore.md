<properties
   pageTitle="Restaurar a partir de una copia de seguridad de la matriz de StorSimple Virtual."
   description="Más información sobre cómo restaurar una copia de seguridad de la matriz Virtual de StorSimple."
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
   ms.workload="NA"
   ms.date="06/07/2016"
   ms.author="alkohli"/>

# <a name="restore-from-a-backup-of-your-storsimple-virtual-array"></a>Restaurar a partir de una copia de seguridad de la matriz de StorSimple Virtual.

## <a name="overview"></a>Información general 

En este artículo se aplica a la versión de Microsoft Azure StorSimple matriz Virtual (también conocido como el dispositivo virtual de StorSimple local o dispositivo virtual de StorSimple) ejecución marzo de 2016 disponibilidad general (GA) o posterior. Este artículo describe paso a paso cómo restaurar a partir de un conjunto de copia de seguridad de los recursos compartidos o volúmenes en la matriz Virtual de StorSimple. El artículo también detalla cómo funciona la recuperación de nivel de elemento de la matriz Virtual StorSimple que está configurado como un servidor de archivos.


## <a name="restore-shares-from-a-backup-set"></a>Restaurar los recursos compartidos de un conjunto de copia de seguridad


**Antes de intentar restaurar recursos compartidos, asegúrese de que tiene suficiente espacio en el dispositivo para completar esta operación.** Para restaurar una copia de seguridad, en el [portal de clásica Azure](https://manage.windowsazure.com/), realice los pasos siguientes.

#### <a name="to-restore-a-share"></a>Para restaurar un recurso compartido

1.  Vaya a la **copia de seguridad del catálogo**. Filtrar por intervalo de tiempo para buscar las copias de seguridad y el dispositivo adecuado. Haga clic en el icono de verificación ![](./media/storsimple-ova-restore/image1.png) para ejecutar la consulta.


1.  En la lista de conjuntos de copia de seguridad aparece, haga clic en y seleccione una copia de seguridad específico. Expanda la copia de seguridad para ver los distintos recursos compartidos debajo de ella. Haga clic en y seleccione un recurso compartido que desea restaurar.

2.  En la parte inferior de la página, haga clic en **Restaurar como nuevo**.

3.  Se iniciará al Asistente para **Restaurar como nuevo recurso compartido** . En la página **especificar el nombre** de la ubicación:


    1.  Compruebe el nombre del dispositivo de origen. Debe ser el dispositivo que contiene el recurso compartido que desea restaurar. La selección de dispositivo está atenuada. Para seleccionar un dispositivo de otro origen, debe salir del asistente y vuelva a seleccionar el conjunto nuevo de la copia de seguridad.

    2.  Proporcione un nombre de recurso compartido. El nombre del recurso compartido debe contener caracteres 3 a 127.

    3.  Revise el tamaño, tipo y permisos asociados con el recurso compartido que está intentando restaurar. Podrá modificar las propiedades de compartir a través del explorador de Windows una vez finalizada la restauración.

    4.  Haga clic en el icono de verificación ![](./media/storsimple-ova-restore/image1.png).

        ![](./media/storsimple-ova-restore/image9.png)

1.  Una vez completada la tarea de restauración, la restauración se iniciará y aparecerá otra notificación. Para supervisar el progreso de la restauración, haga clic en **el trabajo de la vista**. Esto le llevará a la página de **trabajos** .

2.  Puede realizar un seguimiento del progreso de la tarea de restauración. Cuando la restauración está completada al 100%, vaya a la página de **recursos compartidos** en el dispositivo.

3.  Ahora puede ver el nuevo recurso compartido restaurado en la lista de acciones en el dispositivo. Observe que la opción Restaurar se realiza en el mismo tipo de recurso compartido. Un recurso compartido en niveles se restaura como niveles y un recurso compartido anclado localmente como un recurso compartido de forma local anclado.

Ahora haya completado la configuración del dispositivo y cómo hacer copia de seguridad o restaurar un recurso compartido que ha aprendido. 


## <a name="restore-volumes-from-a-backup-set"></a>Restaurar volúmenes de un conjunto de copia de seguridad


Para restaurar una copia de seguridad, en el portal de Azure clásico, realice los pasos siguientes. La operación de restauración restaura la copia de seguridad a un nuevo volumen en el mismo dispositivo virtual; no puede restaurar a un dispositivo diferente.

#### <a name="to-restore-a-volume"></a>Para restaurar un volumen

1.  Vaya a la **copia de seguridad del catálogo**. Filtrar por intervalo de tiempo para buscar las copias de seguridad y el dispositivo adecuado. Haga clic en el icono de verificación ![](./media/storsimple-ova-restore/image1.png) para ejecutar la consulta.

2.  En la lista de conjuntos de copia de seguridad aparece, haga clic en y seleccione una copia de seguridad específico. Expanda la copia de seguridad para ver los distintos volúmenes debajo de ella. Seleccione el volumen que desea restaurar. 

5.  En la parte inferior de la página, haga clic en **Restaurar como nuevo**. Se iniciará el Asistente para **Restaurar como nuevo volumen** .

1.  En la página **especificar el nombre** de la ubicación:


    1.  Compruebe el nombre del dispositivo de origen. Debe ser el dispositivo que contiene el volumen que desea restaurar. La selección de dispositivo no está disponible. Para seleccionar un dispositivo de otro origen, debe salir del asistente y vuelva a seleccionar el conjunto nuevo de la copia de seguridad.

    2.  Proporcione un nombre de volumen para el volumen está restaurando como nuevo. El nombre del volumen debe contener caracteres 3 a 127.

    3.  Haga clic en el icono de flecha.

        ![](./media/storsimple-ova-restore/image12.png)

1.  En la página **especificar los hosts que pueden usar este volumen** , seleccione la ACRs apropiado de la lista desplegable.

    ![](./media/storsimple-ova-restore/image13.png)

1.  Haga clic en el icono de verificación ![](./media/storsimple-ova-restore/image1.png). Se iniciará un trabajo de restauración y verá la siguiente notificación de que la tarea está en curso.

2.  Una vez completada la tarea de restauración, la restauración se iniciará y aparecerá otra notificación. Para supervisar el progreso de la restauración, haga clic en **el trabajo de la vista**. Esto le llevará a la página de **trabajos** .

3.  Puede realizar un seguimiento del progreso de la tarea de restauración. Vaya a la página de **volúmenes** en el dispositivo.

4.  Ahora puede ver el nuevo volumen restaurado en la lista de volúmenes en el dispositivo. Observe que la opción Restaurar se realiza en el mismo tipo de volumen. Se restaura un volumen en niveles como niveles y se restaura un volumen localmente anclado como volumen anclado localmente.

5.  Una vez que el volumen aparece en línea en la lista de volúmenes, el volumen está disponible para su uso.  En el host de iniciador iSCSI, actualice la lista de destinos en la ventana de propiedades del iniciador de iSCSI.  Un nuevo destino que contiene el nombre de volumen restaurado debería aparecer como 'inactive' en la columna Estado.

6.  Seleccione el destino y haga clic en **Conectar**.   Después de conecta el iniciador al destino, debe cambiar el estado **conectado**. 

7.  En la ventana **Administración de discos** , los volúmenes montados aparecerá como se muestra en la siguiente ilustración. Haga clic en el volumen descubierto (haga clic en el nombre del disco) y, a continuación, haga clic en **línea**.

> [AZURE.IMPORTANT] Cuando intenta restaurar un volumen o un recurso compartido de un conjunto de copia de seguridad, si se produce un error en el trabajo de restauración, un volumen de destino o compartir aún pueden crearse en el portal. Es importante que eliminar este volumen de destino o compartir en el portal de minimizar futuros problemas que surjan de este elemento.

## <a name="item-level-recovery-ilr"></a>Recuperación de nivel de elemento (ILR)

Esta versión introduce la recuperación de nivel de elemento (ILR) en un dispositivo virtual StorSimple está configurado como un servidor de archivos. La característica le permite hacer recuperación detallada de archivos y carpetas desde una copia de seguridad de la nube de todas las acciones en el dispositivo StorSimple. Los usuarios pueden recuperar archivos eliminados de las copias de seguridad recientes con un modelo de autoservicio.

Cada recurso tiene una carpeta de *.backups* que contiene las copias de seguridad más recientes. El usuario puede navegar a la copia de seguridad deseada, copie relevantes archivos y carpetas de la copia de seguridad y restaurarlas. Esto elimina las llamadas a los administradores para restaurar archivos de copias de seguridad.

1.  Al realizar la ILR, puede ver las copias de seguridad a través del explorador de Windows. Haga clic en la opción compartir específica que desea buscar en la copia de seguridad. Verá una carpeta *.backups* creada en el recurso compartido que almacena las copias de seguridad. Expanda la carpeta *.backups* para ver las copias de seguridad. La carpeta, aparecerá la vista de explosión de toda la jerarquía de copia de seguridad. Esta vista se crea a petición y suele solo un par de segundos que desee crear.

    Los últimos 5 copias de seguridad se muestran en este modo y pueden utilizarse para realizar una recuperación de nivel de elemento. Las copias de seguridad recientes 5 incluyen predeterminado programada y las copias de seguridad manuales.

    
    -   **Copias de seguridad programadas** denominadas como &lt;nombre de dispositivo&gt;DailySchedule-AAAAMMDD-HHMMSS-UTC.

    -   **Copias de seguridad manuales** denominadas como Ad hoc AAAAMMDD HHMMSS UTC.
    
        ![](./media/storsimple-ova-restore/image14.png)

1.  Identificar la copia de seguridad que contiene la versión más reciente del archivo eliminado. Aunque el nombre de carpeta contiene una marca de tiempo UTC en cada uno de los casos anteriores, la hora en que se creó la carpeta es la hora del dispositivo real cuando se inicia la copia de seguridad. Use la marca de tiempo de carpeta para localizar e identificar las copias de seguridad.

2.  Busque la carpeta o el archivo que desea restaurar en la copia de seguridad que ha identificado en el paso anterior. Nota que solo pueden ver los archivos o carpetas que tienen permisos para. Si no puede tener acceso a determinados archivos o carpetas, deberá ponerse en contacto con un administrador de compartir que puede usar el Explorador de Windows para editar los permisos de compartir y tener acceso a la carpeta o archivo específico. Es recomendable que el Administrador de compartir sea un grupo de usuarios en lugar de un solo usuario.

3.  Copie el archivo o la carpeta en el recurso compartido adecuado en su servidor de archivos de StorSimple.

![video_icon](./media/storsimple-ova-restore/video_icon.png) **vídeo disponible**

Vea el vídeo para ver cómo puede crear recursos compartidos, realizar copias de seguridad de recursos compartidos y restaurar los datos en una matriz Virtual de StorSimple.

> [AZURE.VIDEO use-the-storsimple-virtual-array]

## <a name="next-steps"></a>Pasos siguientes

Más información sobre cómo [Administrar la matriz Virtual StorSimple utilizando la interfaz de usuario de web local](storsimple-ova-web-ui-admin.md).

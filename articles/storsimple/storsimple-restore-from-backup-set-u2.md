<properties 
   pageTitle="Restaurar un volumen StorSimple de copia de seguridad | Microsoft Azure"
   description="Se explica cómo usar la página del catálogo de copia de seguridad de servicio de administrador de StorSimple para restaurar un volumen StorSimple de un conjunto de copia de seguridad."
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

# <a name="restore-a-storsimple-volume-from-a-backup-set-update-2"></a>Restaurar un volumen StorSimple de un conjunto de copia de seguridad (actualización 2)

[AZURE.INCLUDE [storsimple-version-selector-restore-from-backup](../../includes/storsimple-version-selector-restore-from-backup.md)]

## <a name="overview"></a>Información general

La página **Catálogo de copia de seguridad** muestra todos los conjuntos de copia de seguridad que se crean cuando se realizan copias de seguridad manuales o automática. Puede usar esta página para todas las copias de seguridad de una directiva de copia de seguridad o un volumen, seleccione la lista o eliminar las copias de seguridad o use una copia de seguridad para restaurar o clonar un volumen.

 ![Página del catálogo de copia de seguridad](./media/storsimple-restore-from-backup-set-u2/restore.png)

En este tutorial se explica cómo usar la página **Catálogo de copia de seguridad** para restaurar el dispositivo de un conjunto de copia de seguridad.

Puede restaurar una copia de seguridad de nube o el volumen de un local. En cualquier caso, la operación de restauración recupera el volumen en línea inmediatamente cuando los datos se descargan en segundo plano. 

Antes de iniciar una operación de restauración, debe tener en cuenta lo siguiente:

- **Que debe tener el volumen sin conexión** : tomar el volumen sin conexión en el host y el dispositivo antes de iniciar la operación de restauración. Aunque la operación de restauración recupera automáticamente el volumen en línea en el dispositivo, debe conectar manualmente el dispositivo en línea en el host. Puede poner el volumen en línea en el host tan pronto como el volumen está en línea en el dispositivo. (Es necesario esperar a que termine la operación de restauración). Para obtener información, vaya a [realizar un volumen sin conexión](storsimple-manage-volumes-u2.md#take-a-volume-offline).

- **Tipo de volumen después de la restauración** : volúmenes eliminados se restauran en función del tipo de instantánea; es decir, se restauran volúmenes que se han anclado localmente como volúmenes localmente anclados y volúmenes que se hayan niveles se restauran como volúmenes en niveles.

    Para volúmenes existentes, el tipo de uso actual del volumen reemplaza el tipo que se almacena en la instantánea. Por ejemplo, si se restaura un volumen desde una instantánea tomada el tipo de volumen se niveles y que el tipo de volumen ahora está anclado localmente (debido a una operación de conversión que se realizó), se restaura el volumen como un volumen localmente anclado. Del mismo modo, si un volumen localmente anclado existente se ha expandido y posteriormente se restaura de instantáneas más antiguas efectuar cuando el volumen es menor, el volumen restaurado conservará el tamaño expandido actual.

    No se puede convertir un volumen de un volumen en niveles a un volumen anclado localmente o desde un volumen localmente anclado a un volumen en niveles mientras se está restaurando el volumen. Espere a que finalice la operación de restauración y, a continuación, puede convertir el volumen a otro tipo. Para obtener información acerca de cómo convertir un volumen, vaya a [cambiar el tipo de volumen](storsimple-manage-volumes-u2.md#change-the-volume-type). 

- **El tamaño del volumen se reflejarán en el volumen restaurado** : este es un factor importante si está restaurando un volumen localmente anclado que se ha eliminado (porque se configurarán localmente anclados volúmenes por completo). Asegúrese de que tiene suficiente espacio antes de intentar restaurar un volumen localmente anclado que se eliminó. 

- **Que no se puede expandir un volumen mientras se está restaurando** : espera hasta que finalice la operación de restauración antes de intentar expandir el volumen. Para obtener información acerca de la expansión de un volumen, vaya a [modificar un volumen](storsimple-manage-volumes-u2.md#modify-a-volume).

- **Puede realizar una copia de seguridad mientras está restaurando un volumen local** para procedimientos vaya a [usar el servicio de administrador de StorSimple para administrar las directivas de copia de seguridad](storsimple-manage-backup-policies.md).

- **Que puede cancelar una operación de restauración** : Si cancela el trabajo de restauración y, a continuación, el volumen volverá al estado que estaba antes de iniciar la operación de restauración. Para obtener información, vaya a [Cancelar un trabajo](storsimple-manage-jobs-u2.md#cancel-a-job).

## <a name="how-to-use-the-backup-catalog"></a>Cómo usar el catálogo de copia de seguridad

La página **Catálogo de copia de seguridad** proporciona una consulta que le ayuda a reducir la copia de seguridad establece selección. Puede filtrar los conjuntos de copia de seguridad se recuperan basados en los parámetros siguientes:

- **Dispositivo** – el dispositivo en el que se creó el conjunto de copia de seguridad.
- **Directiva de copia de seguridad** o **volumen** : la directiva de copia de seguridad o volumen asociado con este conjunto de copia de seguridad.
- **Desde** y **a** – el intervalo de fecha y hora cuando se creó el conjunto de copia de seguridad.

Los conjuntos de copia de seguridad de filtrado se tabular, a continuación, en función de los siguientes atributos:

- **Nombre** : el nombre de la directiva de copia de seguridad o volumen asociado con el conjunto de copia de seguridad.
- **Tamaño** : el tamaño real del conjunto de copia de seguridad.
- **Creado en** : la fecha y hora cuando se crearon las copias de seguridad. 
- **Tipo** : conjuntos de copia de seguridad pueden instantáneas locales o instantáneas en la nube. Una instantánea local es una copia de seguridad de todos los datos de volumen que se almacenan localmente en el dispositivo, mientras que una instantánea de la nube hace referencia a la copia de seguridad de los datos de volumen que reside en la nube. Instantáneas locales proporcionan acceso más rápido, mientras que se escogen instantáneas de nube de la resistencia de datos.
- **Iniciado por** : las copias de seguridad se pueden iniciar automáticamente según una programación o manualmente por un usuario. (Puede utilizar una directiva de copia de seguridad para programar las copias de seguridad. Como alternativa, puede usar la opción **realizar copia de seguridad** para realizar una copia de seguridad interactiva.)

## <a name="how-to-restore-your-storsimple-volume-from-a-backup"></a>Cómo restaurar el volumen StorSimple desde una copia de seguridad

Puede usar la página **Catálogo de copia de seguridad** para restaurar el volumen StorSimple a partir de una copia de seguridad específico. Tenga en cuenta, sin embargo, que restaurar un volumen volverá el volumen para el estado que estaba cuando se realizó la copia de seguridad. Se perderán los datos que se ha agregado después de la operación de copia de seguridad.

> [AZURE.WARNING] Restaurar a partir de una copia de seguridad reemplazarán los volúmenes existentes de la copia de seguridad. Esto puede provocar la pérdida de datos que se escriben cuando se realizó la copia de seguridad.

### <a name="to-restore-your-volume"></a>Para restaurar el volumen

1. En la página de servicio StorSimple Manager, haga clic en el **catálogo de copia de seguridad** .

    ![Catálogo de copia de seguridad](./media/storsimple-restore-from-backup-set-u2/restore.png)

2. Seleccione una copia de seguridad como sigue:
  1. Seleccione el dispositivo adecuado.
  2. En la lista desplegable, seleccione la directiva de volumen o copia de seguridad de la copia de seguridad que desee seleccionar.
  3. Especifique el intervalo de tiempo.
  4. Haga clic en el icono de verificación ![icono de comprobación](./media/storsimple-restore-from-backup-set-u2/HCS_CheckIcon.png) Para ejecutar esta consulta.
 
    Las copias de seguridad asociado con el volumen seleccionado o directiva de copia de seguridad debería aparecer en la lista de conjuntos de copia de seguridad.

3. Expanda la copia de seguridad para ver los volúmenes asociados. Estos volúmenes deben desconectar en el host y el dispositivo antes de poder restaurarlos. Acceder a los volúmenes en la página de **Contenedores de volumen** y, a continuación, siga los pasos de [tomar un volumen sin conexión](storsimple-manage-volumes-u2.md#take-a-volume-offline) para que lo haga sin conexión.

    > [AZURE.IMPORTANT] Asegúrese de que se han realizado los volúmenes sin conexión en el host en primer lugar, antes de realizar los volúmenes sin conexión en el dispositivo. Si no realiza los volúmenes sin conexión en el host, podría causar daños en los datos.

4. Volver a la ficha **Catálogo de copia de seguridad** y seleccione un conjunto de copia de seguridad.

5. Haga clic en **Restaurar** en la parte inferior de la página.

6. Se le pedirá confirmación. Revise la información de restaurar y, a continuación, seleccione la casilla de verificación confirmación.

    ![Página de confirmación](./media/storsimple-restore-from-backup-set-u2/ConfirmRestore.png)

7. Haga clic en el icono de verificación ![Active icono](./media/storsimple-restore-from-backup-set-u2/HCS_CheckIcon.png). Se iniciará un trabajo de restauración que se puede ver mediante el acceso a la página de **trabajos** . 

8. Una vez completada la restauración, puede comprobar que el contenido de los volúmenes se sustituye por volúmenes de la copia de seguridad.

![Vídeo disponible](./media/storsimple-restore-from-backup-set-u2/Video_icon.png) **vídeo disponible**

Para ver un vídeo que muestra cómo puede utilizar la copia y restaurar funciones en StorSimple para recuperar archivos eliminados, haga clic en [aquí](https://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/).

## <a name="if-the-restore-fails"></a>Si se produce un error en la restauración

Si se produce un error en la operación de restauración por cualquier motivo, recibirá una alerta. Si es así, actualice la lista de copia de seguridad para comprobar que la copia de seguridad sigue siendo válido. Si la copia de seguridad es válida y está restaurando de la nube, problemas de conectividad podrían estar causando el problema. 

Para completar la operación de restauración, llevar el volumen sin conexión en el host y vuelva a intentar la operación de restauración. Tenga en cuenta que las modificaciones de los datos de volumen que se realizaron durante el proceso de restauración se perderán.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre cómo [Administrar StorSimple volúmenes](storsimple-manage-volumes-u2.md).

- Obtenga información sobre cómo [usar el servicio de administrador de StorSimple para administrar el dispositivo de StorSimple](storsimple-manager-service-administration.md).

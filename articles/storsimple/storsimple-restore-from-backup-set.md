<properties 
   pageTitle="Restaurar un volumen StorSimple de copia de seguridad | Microsoft Azure"
   description="Se explica cómo usar la página del catálogo de copia de seguridad de servicio de administrador de StorSimple para restaurar un volumen StorSimple de un conjunto de copia de seguridad."
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
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="restore-a-storsimple-volume-from-a-backup-set"></a>Restaurar un volumen StorSimple de un conjunto de copia de seguridad

[AZURE.INCLUDE [storsimple-version-selector-restore-from-backup](../../includes/storsimple-version-selector-restore-from-backup.md)]

## <a name="overview"></a>Información general

La página **Catálogo de copia de seguridad** muestra todos los conjuntos de copia de seguridad que se crean cuando se realizan copias de seguridad manuales o automática. Puede usar esta página para todas las copias de seguridad de una directiva de copia de seguridad o un volumen, seleccione la lista o eliminar las copias de seguridad o use una copia de seguridad para restaurar o clonar un volumen.

 ![Página del catálogo de copia de seguridad](./media/storsimple-restore-from-backup-set/HCS_BackupCatalog.png)

En este tutorial se explica cómo usar la página **Catálogo de copia de seguridad** para restaurar en el dispositivo un volumen de un conjunto de copia de seguridad.

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

Puede usar la página **Catálogo de copia de seguridad** para restaurar el volumen StorSimple a partir de una copia de seguridad específico. 

> [AZURE.WARNING] Restaurar a partir de una copia de seguridad reemplazarán los volúmenes existentes de la copia de seguridad. Esto puede provocar la pérdida de datos que se escriben cuando se realizó la copia de seguridad.

Antes de iniciar una restauración en un volumen, asegúrese de que el volumen está sin conexión. Debe tener el volumen sin conexión en el host de primera y, a continuación, en el dispositivo. Siga los pasos de [tomar un volumen sin conexión](storsimple-manage-volumes.md#take-a-volume-offline). Siga estos pasos para restaurar un volumen de un conjunto de copia de seguridad.

### <a name="to-restore-from-a-backup-set"></a>Restaurar a partir de un conjunto de copia de seguridad

1. En la página de servicio StorSimple Manager, haga clic en el **catálogo de copia de seguridad** .

    ![Catálogo de copia de seguridad](./media/storsimple-restore-from-backup-set/HCS_Restore.png)

2. Seleccione una copia de seguridad como sigue:
  1. Seleccione el dispositivo adecuado.
  2. En la lista desplegable, seleccione la directiva de volumen o copia de seguridad de la copia de seguridad que desee seleccionar.
  3. Especifique el intervalo de tiempo.
  4. Haga clic en el icono de verificación ![icono de comprobación](./media/storsimple-restore-from-backup-set/HCS_CheckIcon.png) Para ejecutar esta consulta.
 
    Las copias de seguridad asociado con el volumen seleccionado o directiva de copia de seguridad debería aparecer en la lista de conjuntos de copia de seguridad.

3. Expanda la copia de seguridad para ver los volúmenes asociados. Estos volúmenes deben desconectar en el host y el dispositivo antes de poder restaurarlos. Siga los pasos de [tomar un volumen sin conexión](storsimple-manage-volumes.md#take-a-volume-offline).

    >  [AZURE.IMPORTANT] Asegúrese de que se han realizado los volúmenes sin conexión en el host en primer lugar, antes de realizar los volúmenes sin conexión en el dispositivo. Si no realiza los volúmenes sin conexión en el host, podría causar daños en los datos.

4. Seleccione un conjunto de copia de seguridad. Haga clic en **Restaurar** en la parte inferior de la página.

6. Se le pedirá confirmación. 

    ![Página de confirmación](./media/storsimple-restore-from-backup-set/HCS_ConfirmRestore.png)

7. Revise la información de restauración y haga clic en el icono de verificación ![Active icono](./media/storsimple-restore-from-backup-set/HCS_CheckIcon.png). Se iniciará un trabajo de restauración que se puede ver mediante el acceso a la página de **trabajos** . 

8. Una vez completada la restauración, puede comprobar que el contenido de los volúmenes se sustituye por volúmenes de la copia de seguridad.

![Vídeo disponible](./media/storsimple-restore-from-backup-set/Video_icon.png) **vídeo disponible**

Para ver un vídeo que muestra cómo puede utilizar la copia y restaurar funciones en StorSimple para recuperar archivos eliminados, haga clic en [aquí](https://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/).

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre cómo [Administrar StorSimple volúmenes](storsimple-manage-volumes.md).

- Obtenga información sobre cómo [usar el servicio de administrador de StorSimple para administrar el dispositivo de StorSimple](storsimple-manager-service-administration.md).

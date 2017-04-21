<properties 
   pageTitle="Administrar el catálogo de copia de seguridad StorSimple | Microsoft Azure"
   description="Se explica cómo usar la página del catálogo de copia de seguridad de servicio de administrador de StorSimple a la lista, seleccione y eliminar los conjuntos de copia de seguridad de un volumen."
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
   ms.date="04/28/2016"
   ms.author="v-sharos" />

# <a name="use-the-storsimple-manager-service-to-manage-your-backup-catalog"></a>Usar el servicio Administrador de StorSimple para administrar el catálogo de copia de seguridad

## <a name="overview"></a>Información general

La página del **Catálogo de copia de seguridad** de servicio de administrador de StorSimple muestra todos los conjuntos de copia de seguridad que se crean cuando se realizan copias de seguridad manuales o programados. Puede usar esta página para todas las copias de seguridad de una directiva de copia de seguridad o un volumen, seleccione la lista o eliminar las copias de seguridad o use una copia de seguridad para restaurar o clonar un volumen.

En este tutorial se explica cómo la lista, seleccione y eliminar un conjunto de copia de seguridad. Para obtener información sobre cómo restaurar el dispositivo de copia de seguridad, vaya a [restaurar el dispositivo de un conjunto de copia de seguridad](storsimple-restore-from-backup-set.md). Para obtener información sobre cómo clonar un volumen, vaya a [clonar un volumen StorSimple](storsimple-clone-volume.md).

![Catálogo de copia de seguridad](./media/storsimple-manage-backup-catalog/backupcatalog.png) 

La página **Catálogo de copia de seguridad** proporciona una consulta para restringir la copia de seguridad establece selección. Puede filtrar los conjuntos de copia de seguridad se recuperan, en función de los parámetros siguientes:

- **Dispositivo** – el dispositivo en el que se creó el conjunto de copia de seguridad.

- **Directiva de copia de seguridad o volumen** : la directiva de copia de seguridad o volumen asociado con este conjunto de copia de seguridad.

- **Desde y hasta** el intervalo de fecha y hora cuando se creó el conjunto de copia de seguridad.

Los conjuntos de copia de seguridad de filtrado se tabular, a continuación, en función de los siguientes atributos:

- **Nombre** : el nombre de la directiva de copia de seguridad o volumen asociado con el conjunto de copia de seguridad.

- **Tamaño** : el tamaño real del conjunto de copia de seguridad.

- **Creado en** : la fecha y hora cuando se crearon las copias de seguridad. 

- **Tipo** : conjuntos de copia de seguridad pueden instantáneas locales o instantáneas en la nube. Una instantánea local es una copia de seguridad de todos los datos de volumen que se almacenan localmente en el dispositivo, mientras que una instantánea de la nube hace referencia a la copia de seguridad de los datos de volumen que reside en la nube. Instantáneas locales proporcionan acceso más rápido, mientras que se escogen instantáneas de nube de la resistencia de datos.

- **Iniciado por** : las copias de seguridad se pueden iniciar automáticamente mediante una programación o manualmente por un usuario. Puede utilizar una directiva de copia de seguridad para programar copias de seguridad. Como alternativa, puede usar la opción **realizar copia de seguridad** para realizar una copia de seguridad manual.

## <a name="list-backup-sets-for-a-volume"></a>Conjuntos de copia de seguridad de lista para un volumen
 
Complete los pasos siguientes para obtener una lista de todas las copias de seguridad para un volumen.

#### <a name="to-list-backup-sets"></a>Para los conjuntos de copia de seguridad de lista

1. En la página de servicio StorSimple Manager, haga clic en el **catálogo de copia de seguridad** .

2. Filtrar las selecciones como sigue:

    1. Seleccione el dispositivo adecuado.

    2. En la lista desplegable, elija un volumen para ver las copias de seguridad de los correspondientes.

    3. Especifique el intervalo de tiempo.

    4. Haga clic en el icono de verificación ![Icono de comprobación](./media/storsimple-manage-backup-catalog/HCS_CheckIcon.png) Para ejecutar esta consulta.
 
    Las copias de seguridad asociados con el volumen seleccionado deberían aparecer en la lista de conjuntos de copia de seguridad.

## <a name="select-a-backup-set"></a>Seleccione un conjunto de copia de seguridad

Complete los pasos siguientes para seleccionar una copia de seguridad de un volumen o una directiva de copia de seguridad.

#### <a name="to-select-a-backup-set"></a>Para seleccionar un conjunto de copia de seguridad

1. En la página de servicio StorSimple Manager, haga clic en el **catálogo de copia de seguridad** .

2. Filtrar las selecciones como sigue:

    1. Seleccione el dispositivo adecuado.

    2. En la lista desplegable, seleccione la directiva de volumen o copia de seguridad de la copia de seguridad que desee seleccionar.

    3. Especifique el intervalo de tiempo.

    4. Haga clic en el icono de verificación ![Icono de comprobación](./media/storsimple-manage-backup-catalog/HCS_CheckIcon.png) Para ejecutar esta consulta.

    Las copias de seguridad asociado con el volumen seleccionado o directiva de copia de seguridad debería aparecer en la lista de conjuntos de copia de seguridad.

3. Seleccione y expanda un conjunto de copia de seguridad. Se muestran las opciones de **Restaurar** y **Eliminar** en la parte inferior de la página. Puede realizar cualquiera de estas acciones en el conjunto de copia de seguridad que ha seleccionado.

## <a name="delete-a-backup-set"></a>Eliminar un conjunto de copia de seguridad

Eliminar una copia de seguridad cuando ya no desea conservar los datos asociados con él. Realice los pasos siguientes para eliminar un conjunto de copia de seguridad.

#### <a name="to-delete-a-backup-set"></a>Para eliminar un conjunto de copia de seguridad

1. En la página de servicio StorSimple Manager, haga clic en la **ficha catálogo de copia de seguridad**.

2. Filtrar las selecciones como sigue:

    1. Seleccione el dispositivo adecuado.

    2. En la lista desplegable, seleccione la directiva de volumen o copia de seguridad de la copia de seguridad que desee seleccionar.

    3. Especifique el intervalo de tiempo.

    4. Haga clic en el icono de verificación ![Icono de comprobación](./media/storsimple-manage-backup-catalog/HCS_CheckIcon.png) Para ejecutar esta consulta.

    Las copias de seguridad asociado con el volumen seleccionado o directiva de copia de seguridad debería aparecer en la lista de conjuntos de copia de seguridad.

3. Seleccione y expanda un conjunto de copia de seguridad. Se muestran las opciones de **Restaurar** y **Eliminar** en la parte inferior de la página. Haga clic en **Eliminar**.

4. Se le notificará cuando la eliminación es en curso y cuando haya finalizado correctamente. Cuando haya terminado la eliminación, actualizar la consulta en esta página. El conjunto de copia de seguridad eliminado ya no aparecerán en la lista de conjuntos de copia de seguridad.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre cómo [usar el catálogo de copia de seguridad para restaurar el dispositivo de un conjunto de copia de seguridad](storsimple-restore-from-backup-set.md).

- Obtenga información sobre cómo [usar el servicio de administrador de StorSimple para administrar el dispositivo de StorSimple](storsimple-manager-service-administration.md).

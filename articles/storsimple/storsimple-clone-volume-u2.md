<properties
   pageTitle="Clonar el volumen StorSimple | Microsoft Azure"
   description="Describe los tipos diferentes de clonar y cuándo usarlos y se explica cómo puede usar una copia de seguridad para clonar un volumen individual."
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
   ms.date="07/27/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-clone-a-volume-update-2"></a>Utilizar el servicio Administrador de StorSimple para clonar un volumen (actualización 2)

[AZURE.INCLUDE [storsimple-version-selector-clone-volume](../../includes/storsimple-version-selector-clone-volume.md)]

## <a name="overview"></a>Información general

La página del **Catálogo de copia de seguridad** de servicio de administrador de StorSimple muestra todos los conjuntos de copia de seguridad que se crean cuando se realizan copias de seguridad manuales o automática. Puede usar esta página para todas las copias de seguridad de una directiva de copia de seguridad o un volumen, seleccione la lista o eliminar las copias de seguridad o use una copia de seguridad para restaurar o clonar un volumen.

![Página del catálogo de copia de seguridad](./media/storsimple-clone-volume-u2/backupCatalog.png)  

Este tutorial describe cómo puede usar una copia de seguridad para clonar un volumen individual. También se explica la diferencia entre clones *transitorias* y *permanente* .

>[AZURE.NOTE] 
>
>Se pueden clonar un volumen localmente anclado como un volumen en niveles. Si necesita el volumen duplicado a estar anclada localmente, puede convertir el duplicado en un volumen localmente anclado después de la operación de clonar se completó correctamente. Para obtener información sobre cómo convertir un volumen en niveles en un volumen localmente anclado, vaya a [cambiar el tipo de volumen](storsimple-manage-volumes-u2.md#change-the-volume-type).
>
>Si intenta convertir un volumen duplicado de niveles a localmente anclados inmediatamente después de clonar (cuando sigue siendo un clonar transitoria), la conversión se producirá un error con el siguiente mensaje de error:
>
>`Unable to modify the usage type for volume {0}. This can happen if the volume being modified is a transient clone and hasn’t been made permanent. Take a cloud snapshot of this volume and then retry the modify operation.` 
>
>Este error se recibe únicamente si clonar en un dispositivo diferente. Se puede convertir correctamente el volumen a anclados localmente si convertir primero la clonar transitoria a una copia permanente. Para convertir la clonar transitoria a una copia permanente, tomar una instantánea de la nube de ella.

## <a name="create-a-clone-of-a-volume"></a>Crear un duplicado de un volumen

Puede crear un duplicado en el mismo dispositivo, en otro dispositivo o incluso una máquina virtual mediante un local o una instantánea de la nube.

#### <a name="to-clone-a-volume"></a>Clonar un volumen

1. En la página de servicio StorSimple Manager, haga clic en la ficha **catálogo de copia de seguridad** y seleccione un conjunto de copia de seguridad.

2. Expanda la copia de seguridad para ver los volúmenes asociados. Haga clic en y seleccione un volumen del conjunto de copia de seguridad.

     ![Clonar un volumen](./media/storsimple-clone-volume-u2/CloneVol.png) 

3. Haga clic en **clonar** para comenzar a clonar el volumen seleccionado.

4. En el Asistente de volumen clonar, en **ubicación y especifique el nombre de**:

  1. Identificar un dispositivo de destino. Esta es la ubicación donde se creará el duplicado. Puede elegir el mismo dispositivo o especificar cualquier otro dispositivo. Si elige un volumen asociado con otros proveedores de servicios de nube (no Azure), la lista desplegable para el dispositivo de destino solo mostrará los dispositivos físicos. No se puede clonar un volumen asociado con otros proveedores de servicios de nube en un dispositivo virtual.

        >[AZURE.NOTE] Asegúrese de que la capacidad necesaria para el duplicado es menor que la capacidad disponible en el dispositivo de destino.

  2. Especifique un nombre de volumen único para el duplicado. El nombre debe contener entre 3 y 127 caracteres. 
    
        >[AZURE.NOTE] El campo **Clonar volumen como** estará **en niveles** aunque clonar un volumen localmente anclado. No puede cambiar esta opción. Sin embargo, si necesita el volumen duplicado localmente debe anclar así, puede convertir el duplicado en un volumen localmente anclado después de crear correctamente el duplicado. Para obtener información sobre cómo convertir un volumen en niveles en un volumen localmente anclado, vaya a [cambiar el tipo de volumen](storsimple-manage-volumes-u2.md#change-the-volume-type).

        ![Asistente de clonar 1](./media/storsimple-clone-volume-u2/clone1.png) 

  3. Haga clic en el icono de flecha ![icono de flecha](./media/storsimple-clone-volume-u2/HCS_ArrowIcon.png) para pasar a la página siguiente.

5. En **hosts de especificar que pueden usar este volumen**:

  1. Especificar un registro de control de acceso (ACR) para el duplicado. Puede agregar un nuevo ACR o elija de la lista existente.

        ![Asistente de clonar 2](./media/storsimple-clone-volume-u2/clone2.png) 

  2. Haga clic en el icono de verificación ![icono de comprobación](./media/storsimple-clone-volume-u2/HCS_CheckIcon.png)para completar la operación.

6. Se iniciará un trabajo clonar y se le notificará cuando se crea correctamente. Haga clic en **Vista de trabajo** para supervisar el trabajo clonar en la página de **trabajos** . Cuando haya finalizado el trabajo clonar, verá el siguiente mensaje:

    ![Mensaje de clonar](./media/storsimple-clone-volume-u2/CloneMsg.png) 

7. Una vez completada la tarea clonar:

  1. Vaya a la página de **dispositivos** y seleccione la pestaña **Contenedores de volumen** . 
  2. Seleccione el contenedor de volumen que está asociado con el volumen de origen clona. En la lista de volúmenes, debe ver la copia que acaba de crear.

>[AZURE.NOTE] Supervisar y predeterminado copia de seguridad se desactiva automáticamente en un volumen duplicado.

Un duplicado que se creó de esta manera es un clonar transitoria. Para obtener más información acerca de los tipos de clonar, consulte [transitorias frente clones permanentes](#transient-vs.-permanent-clones).

Este clonar ahora es un volumen normal y cualquier operación que es posible en un volumen estará disponible para el duplicado. Deberá configurar este volumen para las copias de seguridad.

## <a name="transient-vs-permanent-clones"></a>Transitorio frente clones permanentes

Transitorias creada solo al clonar a un dispositivo diferente. Puede clonar un volumen específico de una copia de seguridad para un dispositivo diferente administrado por el Administrador de StorSimple. La clonar transitoria con referencias a los datos en el volumen original y utilizará esos datos para leer y escribir localmente en el dispositivo de destino. 

Después de tomar una instantánea de la nube de un clonar transitoria, la copia resultante será un clonar *permanente* . Durante este proceso, se crea una copia de los datos en la nube y el tiempo para copiar estos datos es determinado por el tamaño de los datos y la latencia de Azure (es decir, una copia de Azure a Azure). Este proceso puede tardar días a semanas. La clonar transitoria se convierte en un clonar permanente este modo y no tiene todas las referencias a los datos originales de volumen que se clonó desde. 

## <a name="scenarios-for-transient-and-permanent-clones"></a>Escenarios para copias transitorias y permanentes

Las secciones siguientes describen situaciones de ejemplo en el que pueden usarse clones transitorios y permanentes.

### <a name="item-level-recovery-with-a-transient-clone"></a>Recuperación de nivel de elemento con un clonar transitoria

Debe recuperar un archivo de presentación de Microsoft PowerPoint uno años. El Administrador de TI identifica la copia de seguridad específico de ese período de tiempo y, a continuación, filtra el volumen. El Administrador de la copia el volumen, busca el archivo que está buscando y proporciona a usted. En este escenario, se utiliza un clonar transitoria. 
 
![Vídeo disponible](./media/storsimple-clone-volume-u2/Video_icon.png) **vídeo disponible**

Para ver un vídeo que muestra cómo puede utilizar la copia y restaurar funciones en StorSimple para recuperar archivos eliminados, haga clic en [aquí](https://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/).

### <a name="testing-in-the-production-environment-with-a-permanent-clone"></a>Probar en el entorno de producción con un clonar permanente

Es necesario comprobar un error de prueba en el entorno de producción. Crear una copia del volumen en el entorno de producción y, a continuación, tomar una instantánea de la nube de este clonar para crear un volumen duplicado independiente. En este escenario, se utiliza un clonar permanente.  

## <a name="next-steps"></a>Pasos siguientes
- Obtenga información sobre cómo [restaurar un volumen StorSimple de un conjunto de copia de seguridad](storsimple-restore-from-backup-set-u2.md).

- Obtenga información sobre cómo [usar el servicio de administrador de StorSimple para administrar el dispositivo de StorSimple](storsimple-manager-service-administration.md).

 

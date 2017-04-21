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
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-clone-a-volume"></a>Utilizar el servicio Administrador de StorSimple para clonar un volumen

[AZURE.INCLUDE [storsimple-version-selector-clone-volume](../../includes/storsimple-version-selector-clone-volume.md)]

## <a name="overview"></a>Información general

La página del **Catálogo de copia de seguridad** de servicio de administrador de StorSimple muestra todos los conjuntos de copia de seguridad que se crean cuando se realizan copias de seguridad manuales o automática. Puede usar esta página para todas las copias de seguridad de una directiva de copia de seguridad o un volumen, seleccione la lista o eliminar las copias de seguridad o use una copia de seguridad para restaurar o clonar un volumen.

![Página del catálogo de copia de seguridad](./media/storsimple-clone-volume/HCS_BackupCatalog.png)  

Este tutorial describe cómo puede usar una copia de seguridad para clonar un volumen individual. También se explica la diferencia entre clones *transitorias* y *permanente* . 

## <a name="create-a-clone-of-a-volume"></a>Crear un duplicado de un volumen

Puede crear un duplicado en el mismo dispositivo, en otro dispositivo o incluso una máquina virtual mediante un local o una instantánea de la nube.

#### <a name="to-clone-a-volume"></a>Clonar un volumen

1. En la página de servicio StorSimple Manager, haga clic en la ficha **catálogo de copia de seguridad** y seleccione un conjunto de copia de seguridad.

2. Expanda la copia de seguridad para ver los volúmenes asociados. Haga clic en y seleccione un volumen del conjunto de copia de seguridad.

     ![Clonar un volumen](./media/storsimple-clone-volume/HCS_Clone.png) 

3. Haga clic en **clonar** para comenzar a clonar el volumen seleccionado.

4. En el Asistente de volumen clonar, en **ubicación y especifique el nombre de**:

  1. Identificar un dispositivo de destino. Esta es la ubicación donde se creará el duplicado. Puede elegir el mismo dispositivo o especificar cualquier otro dispositivo. Si elige un volumen asociado con otros proveedores de servicios de nube (no Azure), la lista desplegable para el dispositivo de destino solo mostrará los dispositivos físicos. No se puede clonar un volumen asociado con otros proveedores de servicios de nube en un dispositivo virtual.

        >  [AZURE.NOTE] Asegúrese de que la capacidad necesaria para el duplicado es menor que la capacidad disponible en el dispositivo de destino.
  2. Especifique un nombre de volumen único para el duplicado. El nombre debe contener entre 3 y 127 caracteres.
  3. Haga clic en el icono de flecha ![icono de flecha](./media/storsimple-clone-volume/HCS_ArrowIcon.png) para pasar a la página siguiente.

5. En **hosts de especificar que pueden usar este volumen**:

  1. Especificar un registro de control de acceso (ACR) para el duplicado. Puede agregar un nuevo ACR o elija de la lista existente.
  2. Haga clic en el icono de verificación ![icono de comprobación](./media/storsimple-clone-volume/HCS_CheckIcon.png)para completar la operación.

6. Se iniciará un trabajo clonar y se le notificará cuando se crea correctamente. Haga clic en **Vista de trabajo** para supervisar el trabajo clonar en la página de **trabajos** .

7. Una vez completada la tarea clonar:

  1. Vaya a la página de **dispositivos** y seleccione la pestaña **Contenedores de volumen** . 
  2. Seleccione el contenedor de volumen que está asociado con el volumen de origen clona. En la lista de volúmenes, debe ver la copia que acaba de crear.

>[AZURE.NOTE] Supervisar y predeterminado copia de seguridad se desactiva automáticamente en un volumen duplicado.

Un duplicado que se creó de esta manera es un clonar transitoria. Para obtener más información acerca de los tipos de clonar, consulte [transitorias frente clones permanentes](#transient-vs.-permanent-clones).

Este clonar ahora es un volumen normal y cualquier operación que es posible en un volumen estará disponible para el duplicado. Deberá configurar este volumen para las copias de seguridad.

## <a name="transient-vs-permanent-clones"></a>Transitorio frente clones permanentes

Transitorias y permanente creada solo al clonar en un dispositivo diferente. Puede clonar un volumen específico de una copia de seguridad para un dispositivo diferente. Un clonar creada de esta forma es un clonar *transitorias* . El duplicado transitorio tendrá referencias en el volumen original y utilizará ese volumen para leer al escribir localmente. 

Después de tomar una instantánea de la nube de un clonar transitoria, la copia resultante será un clonar *permanente* . El duplicado permanente es independiente y no tiene ninguna referencia al volumen original que se ha clonar desde.  

## <a name="scenarios-for-transient-and-permanent-clones"></a>Escenarios para copias transitorias y permanentes

Las secciones siguientes describen situaciones de ejemplo en el que pueden usarse clones transitorios y permanentes.

### <a name="item-level-recovery-with-a-transient-clone"></a>Recuperación de nivel de elemento con un clonar transitoria

Debe recuperar un archivo de presentación de Microsoft PowerPoint uno años. El Administrador de TI identifica la copia de seguridad específico de ese período de tiempo y, a continuación, filtra el volumen. El Administrador de la copia el volumen, busca el archivo que está buscando y proporciona a usted. En este escenario, se utiliza un clonar transitoria. 
 
![Vídeo disponible](./media/storsimple-clone-volume/Video_icon.png) **vídeo disponible**

Para ver un vídeo que muestra cómo puede utilizar la copia y restaurar funciones en StorSimple para recuperar archivos eliminados, haga clic en [aquí](https://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/).

### <a name="testing-in-the-production-environment-with-a-permanent-clone"></a>Probar en el entorno de producción con un clonar permanente

Es necesario comprobar un error de prueba en el entorno de producción. Para crear un duplicado del volumen en el entorno de producción, tomar una instantánea de la nube de este clonar. El volumen duplicado ahora es independiente. En este escenario, se utiliza un clonar permanente.

## <a name="next-steps"></a>Pasos siguientes
- Obtenga información sobre cómo [restaurar un volumen StorSimple de un conjunto de copia de seguridad](storsimple-restore-from-backup-set.md).

- Obtenga información sobre cómo [usar el servicio de administrador de StorSimple para administrar el dispositivo de StorSimple](storsimple-manager-service-administration.md).

 

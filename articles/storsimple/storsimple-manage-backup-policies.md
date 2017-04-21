<properties 
   pageTitle="Administrar las directivas de copia de seguridad StorSimple | Microsoft Azure"
   description="Explica cómo puede usar el servicio Administrador de StorSimple para crear y administrar copias de seguridad manuales, las programaciones de copia de seguridad y retención de copia de seguridad."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor=""/>
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="05/10/2016"
   ms.author="v-sharos"/>

# <a name="use-the-storsimple-manager-service-to-manage-backup-policies"></a>Usar el servicio Administrador de StorSimple para administrar las directivas de copia de seguridad

[AZURE.INCLUDE [storsimple-version-selector-manage-backup-policies](../../includes/storsimple-version-selector-manage-backup-policies.md)]

## <a name="overview"></a>Información general

En este tutorial se explica cómo utilizar la página de **Directivas de copia de seguridad** del servicio de administrador de StorSimple para controlar los procesos de copia de seguridad y retención de copia de seguridad para los volúmenes StorSimple. También se describe cómo realizar una copia de seguridad manual.

La página **Directivas de copia de seguridad** le permite administrar directivas de copia de seguridad y programar local e instantáneas en la nube. (Directivas de copia de seguridad se usan para configurar las programaciones de copia de seguridad y retención de copia de seguridad para una colección de volúmenes). Directivas de copia de seguridad le permiten tomar una instantánea de varios volúmenes al mismo tiempo. Esto significa que las copias de seguridad creadas por una directiva de copia de seguridad será copias bloqueo coherentes. Esta página enumera las directivas de copia de seguridad, sus tipos, los volúmenes asociados, el número de copias de seguridad que se conservan y la opción de habilitar estas directivas.

La página **Directivas de copia de seguridad** también le permite filtrar las directivas de copia de seguridad existentes por uno o varios de los siguientes campos:

- **Nombre de la directiva** : el nombre de la directiva. Incluyen los diferentes tipos de directivas:

   - Directivas programadas, que se crean explícitamente por el usuario.
   - Directivas automáticas, que se crean cuando se ha habilitado la copia de seguridad predeterminados para esta opción de volumen en el momento de la creación de volumen. Estas directivas se denominan VolumeName_Default, donde nombre de volumen hace referencia al nombre del volumen StorSimple configurado por el usuario en el portal de clásico de Azure. Las directivas automáticas resultado instantáneas de nube diarias empezando por la hora del dispositivo 22:30.
   - Directivas importadas, que se crearon en el Administrador de instantáneas StorSimple. Tienen una etiqueta que describa el host de StorSimple Administrador de instantáneas que se importaron las directivas de.

- **Volúmenes** : los volúmenes asociados a la directiva. Todos los volúmenes asociados a una directiva de copia de seguridad se agrupan cuando se crean las copias de seguridad.

- **Última copia de seguridad correcta** : la fecha y hora de la última copia de seguridad correcta que se ha realizado con esta directiva.

- **Siguiente copia de seguridad** : la fecha y hora de la siguiente copia de seguridad programada que se inicie esta directiva.

- **Las programaciones** : el número de programaciones asociadas a la directiva de copia de seguridad.

Las operaciones de uso frecuentes que puede realizar en esta página son:

- Agregar una directiva de copia de seguridad 
- Agregar o modificar una programación 
- Eliminar una directiva de copia de seguridad 
- Realice una copia de seguridad manual 
- Crear una directiva de copia de seguridad personalizada con varios volúmenes y programaciones 

## <a name="add-a-backup-policy"></a>Agregar una directiva de copia de seguridad

Agregar una directiva de copia de seguridad para programar automáticamente las copias de seguridad. Realice los pasos siguientes en el portal de Azure clásico para agregar una directiva de copia de seguridad de su dispositivo StorSimple. Después de agregar la directiva, puede definir una programación (consulte [Agregar o modificar una programación](#add-or-modify-a-schedule)).

[AZURE.INCLUDE [storsimple-add-backup-policy](../../includes/storsimple-add-backup-policy.md)]

![Vídeo disponible](./media/storsimple-manage-backup-policies/Video_icon.png) **vídeo disponible**

Para ver un vídeo que muestra cómo crear una local o directiva de copia de seguridad de la nube, haga clic en [aquí](https://azure.microsoft.com/documentation/videos/create-storsimple-backup-policies/).


## <a name="add-or-modify-a-schedule"></a>Agregar o modificar una programación

Puede agregar o modificar una programación que está conectada a una directiva de copia de seguridad existente en el dispositivo StorSimple. Realice los pasos siguientes en el portal de Azure clásico para agregar o modificar una programación.

[AZURE.INCLUDE [storsimple-add-modify-backup-schedule](../../includes/storsimple-add-modify-backup-schedule.md)]

## <a name="delete-a-backup-policy"></a>Eliminar una directiva de copia de seguridad

Realice los pasos siguientes en el portal de Azure clásico para eliminar una directiva de copia de seguridad en el dispositivo StorSimple.

[AZURE.INCLUDE [storsimple-delete-backup-policy](../../includes/storsimple-delete-backup-policy.md)]


## <a name="take-a-manual-backup"></a>Realice una copia de seguridad manual

Realice los pasos siguientes en el portal de Azure clásico para crear una copia de seguridad (manual) a petición un único volumen.

[AZURE.INCLUDE [storsimple-create-manual-backup](../../includes/storsimple-create-manual-backup.md)]

## <a name="create-a-custom-backup-policy-with-multiple-volumes-and-schedules"></a>Crear una directiva de copia de seguridad personalizada con varios volúmenes y programaciones

Realice los pasos siguientes en el portal de Azure clásico para crear una directiva de copia de seguridad personalizada que tiene varios volúmenes y programaciones.

[AZURE.INCLUDE [storsimple-create-custom-backup-policy](../../includes/storsimple-create-custom-backup-policy.md)]


## <a name="next-steps"></a>Pasos siguientes

Más información sobre cómo [usar el servicio de administrador de StorSimple para administrar el dispositivo de StorSimple](storsimple-manager-service-administration.md).

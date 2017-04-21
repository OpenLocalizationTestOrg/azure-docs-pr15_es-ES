<properties 
   pageTitle="Desactivar y eliminar un dispositivo StorSimple | Microsoft Azure"
   description="Describe cómo quitar StorSimple dispositivo de servicio, en primer lugar la desactivación y luego la eliminación."
   services="storsimple"
   documentationCenter=""
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/18/2016"
   ms.author="anoobbacker" />

# <a name="deactivate-and-delete-a-storsimple-device"></a>Desactivar y eliminar un dispositivo StorSimple

## <a name="overview"></a>Información general

Puede tardar un dispositivo StorSimple fuera de servicio (por ejemplo, si va a reemplazar o actualizar el dispositivo o si ya no está utilizando StorSimple). Si este es el caso, debe desactivar el dispositivo antes de que pueda eliminar. Desactivación de servidores la conexión entre el dispositivo y el servicio de administrador de StorSimple correspondiente. En este tutorial se explica cómo quitar un dispositivo de StorSimple de servicio por primera desactivación y luego la eliminación. 

Al desactivar un dispositivo, todos los datos que se almacenan localmente en el dispositivo ya no será accesibles. Se pueden recuperar solo los datos asociados con el dispositivo que almacenó en la nube.  

>[AZURE.WARNING] Desactivación es una operación permanente y no se puede deshacer. No se puede registrar un dispositivo desactivado con el servicio Administrador de StorSimple a menos que primero se restablece la fábrica. 
>
>El generador de restablece proceso elimina todos los datos se almacenan localmente en el dispositivo. Por lo tanto, es fundamental tomar una instantánea de la nube de todos los datos antes de desactivar un dispositivo. Esto le permitirá recuperar todos los datos más adelante.

En este tutorial se explica cómo:

- Desactivar un dispositivo y eliminar los datos
- Desactivar un dispositivo y conservar los datos

También se explica cómo desactivación y eliminación funciona en un dispositivo virtual de StorSimple.

>[AZURE.NOTE] Antes de desactivar un dispositivo físico o virtual de StorSimple, asegúrese de detener o eliminar clientes y hosts que dependen de ese dispositivo.

## <a name="deactivate-and-delete-data"></a>Desactivar y eliminar datos

Si está interesado en eliminar el dispositivo por completo y no desea conservar los datos en el dispositivo, siga los pasos siguientes.

#### <a name="to-deactivate-the-device-and-delete-the-data"></a>Para desactivar el dispositivo y eliminar los datos  

1. Antes de desactivar un dispositivo, debe eliminar todo el volumen contenedores (y los volúmenes) asociados con el dispositivo. Puede eliminar los contenedores de volumen después de eliminar las copias de seguridad asociados.

2. Desactivar el dispositivo como sigue:

    1. En la página de **dispositivos** de servicio de StorSimple Manager, seleccione el dispositivo que desea desactivar y, en la parte inferior de la página, haga clic en **desactivar**.

    2. Aparecerá un mensaje de confirmación. Haga clic en **Sí** para continuar. El proceso de desactivar se iniciará y tardar unos minutos en completarse.

3. Tras la activación, puede eliminar por completo el dispositivo. Eliminar un dispositivo, quita de la lista de dispositivos conectados al servicio. El servicio, a continuación, ya no puede administrar el dispositivo eliminado. Use los siguientes pasos para eliminar el dispositivo:

    1. En la página de **dispositivos** de servicio de StorSimple Manager, seleccione un dispositivo desactivado que desea eliminar.

    2. En la parte inferior de la página, haga clic en **Eliminar**.

    3. Se le pedirá confirmación. Haga clic en **Sí** para continuar.

    Puede tardar unos minutos para el dispositivo que desea eliminar.

## <a name="deactivate-and-retain-data"></a>Desactivar y conservar los datos

Si está interesado en eliminar el dispositivo pero desea conservar los datos, a continuación, complete los pasos siguientes.

####<a name="to-deactivate-a-device-and-retain-the-data"></a>Para desactivar un dispositivo y conservar los datos 

1. Desactivar el dispositivo. Todos los contenedores de volumen y las instantáneas del dispositivo estarán disponibles.

    1. En la página de **dispositivos** de servicio de StorSimple Manager, seleccione el dispositivo que desea desactivar y, en la parte inferior de la página, haga clic en **desactivar**.

    2. Aparecerá un mensaje de confirmación. Haga clic en **Sí** para continuar. El proceso de desactivar se iniciará y tardar unos minutos en completarse.

2. Ahora puede conmutar los contenedores de volumen y las instantáneas asociadas. Para obtener información, vaya a la [migración tras error y recuperación de desastres de su dispositivo StorSimple](storsimple-device-failover-disaster-recovery.md).

3. Después de la desactivación y migración tras error, puede eliminar por completo el dispositivo. Eliminar un dispositivo, quita de la lista de dispositivos conectados al servicio. El servicio, a continuación, ya no puede administrar el dispositivo eliminado. Complete los pasos siguientes para eliminar el dispositivo:
 
    1. En la página de **dispositivos** de servicio de StorSimple Manager, seleccione un dispositivo desactivado que desea eliminar.

    2. En la parte inferior de la página, haga clic en **Eliminar**.

    3. Se le pedirá confirmación. Haga clic en **Sí** para continuar.

    Puede tardar unos minutos para el dispositivo que desea eliminar.

## <a name="deactivate-and-delete-a-virtual-device"></a>Desactivar y eliminar un dispositivo virtual

Para un dispositivo virtual StorSimple, desactivación cancela la asignación de la máquina virtual. A continuación, puede eliminar la máquina virtual y los recursos que se crean cuando se establecieron los servicios. Después de que el dispositivo virtual se desactiva, no se puede restaurar a su estado anterior. 

Resultados de la desactivación de las siguientes acciones:

- Se quita el dispositivo virtual StorSimple.

- Se quitan los OSDisk y los discos de datos creado para el dispositivo virtual StorSimple.

- El servicio hospedado y una red Virtual que se crearon durante el aprovisionamiento se conservan. Si no utiliza estas entidades, debe eliminarlos manualmente.

- Instantáneas de nube creadas por el dispositivo virtual de StorSimple se conservan.

## <a name="next-steps"></a>Pasos siguientes
- Para restaurar el dispositivo desactivado valores predeterminados de fábrica, vaya a [Restablecer el dispositivo a la configuración predeterminada de fábrica](storsimple-manage-device-controller.md#reset-the-device-to-factory-default-settings).

- Para obtener asistencia técnica, [póngase en contacto con soporte técnico de Microsoft](storsimple-contact-microsoft-support.md).

- Para obtener más información sobre cómo usar el servicio Administrador de StorSimple, vaya a [usar el servicio de administrador de StorSimple para administrar el dispositivo de StorSimple](storsimple-manager-service-administration.md). 

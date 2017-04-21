<properties
   pageTitle="Recuperación de errores de desastres dispositivo y recuperación para su matriz Virtual de StorSimple"
   description="Más información sobre cómo para la recuperación ante la matriz Virtual de StorSimple."
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

# <a name="disaster-recovery-and-device-failover-for-your-storsimple-virtual-array"></a>Recuperación de errores de desastres dispositivo y recuperación para su matriz Virtual de StorSimple


## <a name="overview"></a>Información general

Este artículo describe la recuperación de la matriz Virtual de Microsoft Azure StorSimple (también conocido como el StorSimple local dispositivo virtual) incluidos detalle los pasos necesarios para conmutar a otro dispositivo virtual un desastre. Permite migrar los datos desde un dispositivo de *origen* en el centro de datos a otro dispositivo de *destino* que se encuentra en la misma u otra ubicación geográfica. La migración tras error del dispositivo es para el dispositivo completo. Durante la migración tras error, los datos de la nube para el dispositivo de origen cambian la propiedad a la que el dispositivo de destino.

Migración tras error del dispositivo se dirige a través de la característica de recuperación (DR) desastres y se inicia desde la página de **dispositivos** . Esta página recoge todos los dispositivos de StorSimple conectados a su servicio de administrador de StorSimple. Para cada dispositivo, se muestran el nombre descriptivo, estado, capacidad de preparación y máxima, tipo y modelo.

![](./media/storsimple-ova-failover-dr/image15.png)

En este artículo es aplicable a StorSimple Virtual matrices solo. Para conmutar un dispositivo 8000 serie, vaya a la [migración tras error y recuperación de su dispositivo StorSimple](storsimple-device-failover-disaster-recovery.md).


## <a name="what-is-disaster-recovery"></a>¿Qué es recuperación?

En un escenario de recuperación (DR) desastres, el dispositivo principal deja de funcionar. En esta situación, puede mover los datos de nube asociados con el dispositivo defectuoso a otro dispositivo con el dispositivo principal como el *origen* y especificando otro dispositivo como *destino*. Este proceso se conoce como la *migración tras error*. Durante la migración tras error, todos los volúmenes o los recursos compartidos desde el dispositivo de origen cambie la propiedad y se transfieren al dispositivo de destino. Filtrado de datos no está permitida.

DR es modelar como una restauración completa del dispositivo con los niveles de basados en mapa de calor y seguimiento. Se define un mapa de calor asignando un valor de calor a los datos basándose en leer y escribir patrones. Este calor asigne, a continuación, niveles de los fragmentos de datos más bajos de calor en la nube en primer lugar y mantener los fragmentos de datos de alta calor (usada) en el nivel local. Durante un DR, se utiliza el mapa de calor para restaurar y rehidratarse los datos de la nube. El dispositivo Obtiene todos los volúmenes y recursos compartidos en la última copia de seguridad reciente (según lo determinado internamente) y realiza una restauración de copia de seguridad. Todo el proceso de DR es organizado por el dispositivo.


## <a name="prerequisites-for-device-failover"></a>Requisitos previos para la migración tras error de dispositivo


### <a name="prerequisites"></a>Requisitos previos

Para cualquier recuperación de fallos de dispositivo, deben cumplirse los siguientes requisitos previos:

- El dispositivo de origen debe estar en un estado **desactivado** .

- El dispositivo de destino debe mostrarse como **activo** en el portal de clásico de Azure. Debe proporcionar un dispositivo virtual de destino de la capacidad de igual o superior. A continuación, debe usar la interfaz de usuario de web local para configurar y registrar correctamente el dispositivo virtual.

    > [AZURE.IMPORTANT] No intente configurar el dispositivo virtual registrado a través del servicio, haga clic en **configuración de dispositivo completo**. Ninguna configuración de dispositivo debe realizarse a través del servicio.

- El dispositivo de origen y destino deben ser del mismo tipo. Solo puede conmutar un dispositivo virtual está configurado como un servidor de archivos a otro servidor de archivos. El mismo es true para un servidor de iSCSI.

- Para un servidor de archivos DR, se recomienda unir el dispositivo de destino en el mismo dominio que el origen de para que los permisos se resuelven automáticamente. Solo la migración de tras de error en un dispositivo de destino en el mismo dominio es compatible con esta versión.

### <a name="other-considerations"></a>Otras consideraciones

- Se recomienda realizar todos los recursos compartidos o volúmenes en el dispositivo de origen sin conexión.

- Si se trata de un error planeado, se recomienda que realice una copia de seguridad del dispositivo y, a continuación, continúe con la migración tras error para minimizar las pérdidas de datos. Si se trata de una migración tras error no planeado, se utilizará la copia de seguridad más reciente para restaurar el dispositivo.

- Los dispositivos de destino disponibles para DR son dispositivos que tienen la capacidad de igual o mayor en comparación con el dispositivo de origen. Los dispositivos que están conectados a su servicio pero no cumple los criterios de espacio suficiente no estarán disponibles como dispositivos de destino.

### <a name="dr-prechecks"></a>DR prechecks

Antes de comenzar el DR, prechecks se realizan en el dispositivo. Estas comprobaciones ayudan a garantizar que no se producirán errores cuando DR comienza. Incluyen la prechecks:

- Validar la cuenta de almacenamiento

- Comprobación de la conectividad de la nube en Azure

- Comprobar el espacio disponible en el dispositivo de destino

- Comprobar si un dispositivo de origen del servidor iSCSI tiene nombres ACR válidos, IQN (no superior más 220 caracteres de longitud) y contraseña CHAP (12 y 16 caracteres de longitud) asociadas con los volúmenes

Si alguno de los anteriores prechecks no, no puede continuar con el DR. Debe resolver los problemas y, a continuación, vuelva a intentar DR.

Después de que el DR se completó correctamente, la propiedad de los datos de la nube en el dispositivo de origen se transfiere al dispositivo de destino. El dispositivo de origen, a continuación, ya no está disponible en el portal. Bloquear el acceso a todos los volúmenes y recursos compartidos en el dispositivo de origen y el dispositivo de destino se activa.

> [AZURE.IMPORTANT]
> 
> Aunque el dispositivo ya no está disponible, la máquina virtual que haya proporcionado en el sistema host aún consume recursos. Una vez completado correctamente el DR, puede eliminar esta máquina virtual de su sistema de host.

## <a name="fail-over-to-a-virtual-array"></a>Conmutar a una matriz virtual

Se recomienda que haya otra matriz Virtual de StorSimple se aprovisione, configurado a través de la interfaz de usuario de web local y registrado con el servicio de administrador de StorSimple antes de ejecutar este procedimiento.


> [AZURE.IMPORTANT]
> 
> - No se permite conmutar desde un dispositivo de serie 8000 StorSimple a un dispositivo virtual de 1200.
> - Puede conmutar desde un dispositivo virtual de información de procesamiento estándar Federal (FIPS) habilitado implementado en el portal de administración pública en un dispositivo virtual en Azure portal clásico. A la inversa, también es true.

Realice los pasos siguientes para restaurar el dispositivo a un dispositivo virtual de StorSimple de destino.

1. Tomar volúmenes o recursos compartidos sin conexión en el host. Consulte las instrucciones de sistema operativo específico en el host de poner las cuotas de volúmenes sin conexión. Si no está sin conexión, debe tener todos los volúmenes y recursos compartidos sin conexión en el dispositivo, vaya a **dispositivos > recursos compartidos** (o **dispositivo > volúmenes**). Seleccione un volumen de compartir y haga clic en **trabajar sin conexión** en la parte inferior de la página. Cuando se le solicite confirmación, haga clic en **Sí**. Repita este proceso para todos los recursos compartidos o volúmenes en el dispositivo.

2. En la página de **dispositivos** , seleccione el dispositivo de origen para la migración tras error y haga clic en **desactivar**. 
    ![](./media/storsimple-ova-failover-dr/image16.png)

3. Se le pedirá confirmación. Desactivación de dispositivo es un proceso permanente que no se puede deshacer. Se le recordará tomar los recursos compartidos o volúmenes sin conexión en el host.

    ![](./media/storsimple-ova-failover-dr/image18.png)

3. Al recibir confirmación, se iniciará la desactivación. Después de la desactivación se ha completado correctamente, se le notificará.

    ![](./media/storsimple-ova-failover-dr/image19.png)

4. En la página de **dispositivos** , el estado del dispositivo ahora cambiará a **desactivado**.

    ![](./media/storsimple-ova-failover-dr/image20.png)

5. Seleccione el dispositivo desactivado y en la parte inferior de la página, haga clic en **migración tras error**.

6. En el Asistente de migración tras error confirme que se abre, haga lo siguiente:

    1. En la lista desplegable de dispositivos disponibles, elija un **dispositivo de destino.** Sólo los dispositivos que tienen la capacidad suficiente se muestran en la lista desplegable.

    2. Revise los detalles asociados con el dispositivo de origen como nombre del dispositivo, la capacidad total y los nombres de los recursos compartidos que se conmutar por error.

        ![](./media/storsimple-ova-failover-dr/image21.png)

7. Active **acepto que la migración tras error es una operación permanente y una vez completada la migración tras error, se eliminará el dispositivo de origen**.

8. Haga clic en el icono de verificación ![](./media/storsimple-ova-failover-dr/image1.png).


9. Se iniciará un trabajo de migración tras error y se le notificará. Haga clic en el **trabajo de la vista** para supervisar la migración tras error.

    ![](./media/storsimple-ova-failover-dr/image22.png)

10. En la página **tareas** , verá un trabajo de migración tras error creado para el dispositivo de origen. Este trabajo realiza el prechecks DR.

    ![](./media/storsimple-ova-failover-dr/image23.png)

    Después de la prechecks DR son correctas, el trabajo de migración tras error generará trabajos de restauración para cada recurso compartido o volumen que existe en el dispositivo de origen.

    ![](./media/storsimple-ova-failover-dr/image24.png)

11. Una vez completada la migración tras error, vaya a la página de **dispositivos** .

    una. Seleccione el dispositivo virtual StorSimple que se usó como el dispositivo de destino para el proceso de migración tras error.

    b. Vaya a la página de **recursos compartidos** (o **volúmenes** si servidor iSCSI). Ahora deben aparecer todas las acciones (volúmenes) desde el dispositivo antiguo.
    
    ![](./media/storsimple-ova-failover-dr/image25.png)

![](./media/storsimple-ova-failover-dr/video_icon.png)**Vídeo disponible**

Este vídeo muestra cómo puede conmutar un dispositivo virtual de StorSimple local a otro dispositivo virtual.

> [AZURE.VIDEO storsimple-virtual-array-disaster-recovery]

## <a name="business-continuity-disaster-recovery-bcdr"></a>Empresa continuidad recuperación (BCDR)

Un escenario de recuperación (BCDR) de ante de continuidad de empresa se produce cuando el centro de datos de Azure toda deja de funcionar. Esto puede afectar el servicio de administrador de StorSimple y los dispositivos StorSimple asociados.

Si hay dispositivos StorSimple que se registraron antes de que se produzca un desastre, estos dispositivos StorSimple necesite eliminarse. Después del desastre, puede volver a crear y configurar los dispositivos.

## <a name="errors-during-dr"></a>Errores durante la DR

**Interrupción de conectividad de nube durante DR**

Si se interrumpe la conectividad de nube una vez comenzada DR y antes de que finalice la restauración de dispositivo, se producirá un error en el DR y se le notificará. El dispositivo de destino que se utilizó para DR se marca como *inutilizable.* El mismo dispositivo de destino no se pueden usar después para futuras DRs.

**No hay dispositivos de destino compatibles**

Si los dispositivos de destino disponibles no tiene suficiente espacio, verá un error en el efecto que no hay ningún dispositivo de destino compatible.

**Prueba de errores**

Si no se cumple una de las prechecks, verá errores anterior al cheque.

## <a name="next-steps"></a>Pasos siguientes

Más información sobre cómo [Administrar la matriz Virtual StorSimple utilizando la interfaz de usuario de web local](storsimple-ova-web-ui-admin.md).

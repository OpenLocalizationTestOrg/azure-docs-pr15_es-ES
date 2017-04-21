<properties 
   pageTitle="Panel de servicio de administrador de StorSimple - matriz Virtual | Microsoft Azure"
   description="Describe el panel de servicio de administrador de StorSimple y se explica cómo usarlo para supervisar el estado de la matriz Virtual de StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/07/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-dashboard-for-the-storsimple-virtual-array"></a>Usar el panel de servicio de administrador de StorSimple para la matriz Virtual StorSimple

## <a name="overview"></a>Información general

La página de panel de servicio de StorSimple Manager proporciona un resumen de las StorSimple Virtual matrices (también conocido como StorSimple local dispositivos virtuales o virtual) que están conectados al servicio de administrador de StorSimple, resaltar aquellos que necesitan atención del administrador del sistema. Este tutorial presenta la página de panel, explica la función y el contenido de los paneles y describe las tareas que puede realizar desde esta página.

![Paneles de servicio](./media/storsimple-ova-service-dashboard/dashboard1.png)

El panel de servicio de StorSimple Manager muestra la siguiente información:

- En el área de **gráfico** en la parte superior de la página, puede ver las métricas relevantes para los dispositivos virtuales. Puede ver el almacenamiento principal que se utiliza en todos los dispositivos virtuales, así como el almacenamiento de nube consumido por dispositivos virtuales durante un período de tiempo. Use los controles en la esquina superior derecha del gráfico para especificar el uso de relativo o absoluto y para ver una escala de tiempo de 1 semana, 1 mes, 3 meses o años 1. Usar el control de actualización ![control de actualizaciones](./media/storsimple-ova-service-dashboard/refresh-control.png) para actualizar el gráfico.

- **Información general de uso** muestra el almacenamiento principal se aprovisione y utilizado por todos los dispositivos virtuales en relación con el almacenamiento total disponible en todos los dispositivos virtuales. **Provisioned** hace referencia a la cantidad de almacenamiento que está preparado y asignado para su uso, **cuando se usa** hace referencia al uso de recursos compartidos o volúmenes como se ve en los iniciadores que están conectados a los dispositivos virtuales y **Capacidad máx.** muestra la capacidad máxima de todos los dispositivos virtuales.

- El área de **alertas** proporciona una instantánea de todas las alertas activas en todos los dispositivos virtuales, agrupados por gravedad de alerta. Haga clic en el nivel de gravedad, se abre la página de **alertas** , el ámbito para mostrar las alertas. En la página de **alertas** , puede hacer clic en una alerta individual para ver detalles adicionales sobre dicha alerta, incluidas las acciones recomendadas. También puede borrar la alerta si se ha resuelto el problema.

- El área **proyectos** proporciona una instantánea de trabajos recientes en todos los dispositivos virtuales que están conectados a su servicio. Existen vínculos que puede usar para ver los trabajos que se encuentran actualmente en curso y las que se realizó correctamente o no en las últimas 24 horas. 

- El área de **un vistazo rápido** a la derecha de la página proporciona información útil, como el estado del servicio, número total de dispositivos virtuales conectado al servicio, la ubicación del servicio y los detalles de la suscripción que está asociado con el servicio. También es un vínculo en el registro de operaciones. Haga clic en el vínculo para ver una lista de todas las operaciones de servicio StorSimple Manager completadas. 

Puede usar la página de panel de servicio de administrador de StorSimple para iniciar las siguientes tareas:

- Obtener la clave de registro de servicio.
- Ver los registros de operación.

## <a name="get-the-service-registration-key"></a>Obtener la clave de registro de servicio

La clave de registro de servicio se usa para registrar un dispositivo virtual de StorSimple con el servicio de administrador de StorSimple, para que el dispositivo aparece en el portal de clásico Azure más acciones de administración. La clave se crea en el primer dispositivo virtual y compartida con los dispositivos virtuales restantes. 

Haga clic en **Clave de registro** (en la parte inferior de la página), se abre el cuadro de diálogo **Clave de registro de servicio** , donde puede copiar la clave de registro del servicio actual en el Portapapeles o regenerar la clave de registro de servicio.

![clave de registro](./media/storsimple-ova-service-dashboard/service-dashboard3.png)

Regenerar la clave no afecta a los dispositivos virtuales previamente registrados: afecta a solo los dispositivos virtuales que están registrados con el servicio después de regenera la clave.

Para obtener más información sobre cómo obtener la clave de registro de servicio, vaya a [obtener la clave de registro de servicio](storsimple-ova-manage-service.md#get-the-service-registration-key).

## <a name="view-the-operations-logs"></a>Ver los registros de operaciones

Puede ver los registros de operación haciendo clic en el vínculo de registros de operación disponible en el panel de **vista rápida** del panel. Esto le llevará a la página de servicios de administración, donde puede filtrar y ver los registros específicos en el servicio de administrador de StorSimple.

![Registro de operaciones](./media/storsimple-ova-service-dashboard/ops-log.png)

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre cómo [usar la interfaz de usuario para administrar la matriz Virtual de StorSimple de web local](storsimple-ova-web-ui-admin.md).
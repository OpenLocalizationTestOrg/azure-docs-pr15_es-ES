<properties 
   pageTitle="Ver y administrar alertas de matriz Virtual de StorSimple | Microsoft Azure"
   description="Describe las condiciones de alerta StorSimple Virtual matriz y gravedad y cómo usar el servicio Administrador de StorSimple para administrar alertas."
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
   ms.workload="NA"
   ms.date="06/07/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-view-and-manage-alerts-for-the-storsimple-virtual-array"></a>Usar el servicio de administrador de StorSimple para ver y administrar alertas para la matriz Virtual StorSimple

## <a name="overview"></a>Información general

La ficha **alertas** en el servicio Administrador de StorSimple ofrece una manera para revisar y desactive las alertas relacionadas con matriz Virtual de StorSimple en tiempo real. En esta pestaña, puede supervisar de forma centralizada los problemas de estado de las matrices Virtual de StorSimple (también conocido como StorSimple local dispositivos virtuales) y la solución de Microsoft Azure StorSimple general.

Este tutorial describe cómo configurar las notificaciones de alerta, las condiciones de alerta comunes, niveles de gravedad de las alertas y cómo ver y realizar un seguimiento de las alertas. Además, incluye tablas de alerta de referencia rápida, que le permite rápidamente, busque una alerta específica y responder según corresponda.

![Página de alertas](./media/storsimple-ova-manage-alerts/alerts1.png)

## <a name="configure-alert-settings"></a>Configurar las alertas

Puede elegir si desea recibir una notificación por correo electrónico de las condiciones de alerta para cada uno de los dispositivos virtuales StorSimple. Además, puede identificar a otros destinatarios de notificación de alerta escribiendo sus direcciones de correo electrónico en el cuadro de **otros destinatarios de correo electrónico** , separado por punto y coma.

>[AZURE.NOTE] Puede introducir un máximo de 20 direcciones de correo electrónico por dispositivo virtual.

Después de habilitar la notificación de correo electrónico de un dispositivo virtual, miembros de la lista de notificación recibirá un mensaje de correo electrónico cada vez que se produce una alerta crítica. Los mensajes se enviarán de *storsimple-alerts-noreply@mail.windowsazure.com* y se describe la condición de alerta. Los destinatarios pueden haga clic en **Cancelar suscripción** para que se quiten de la lista de notificación de correo electrónico.

#### <a name="to-enable-email-notification-of-alerts-for-a-virtual-device"></a>Para habilitar la notificación de correo electrónico de alertas para un dispositivo virtual

1. Vaya a **dispositivos** > **configuración** del dispositivo virtual. Vaya a la sección **configuración de alertas** .

    ![configuración de alertas](./media/storsimple-ova-manage-alerts/alerts2.png)

2. En **configuración de alertas**, defina las siguientes opciones:

    1. En el campo **Enviar notificación de correo electrónico** , seleccione **Sí**.

    2. En el campo **administradores de servicios de correo electrónico** , seleccione **Sí** si desea que el administrador del servicio y todos los coadministradores reciben las notificaciones de alerta.

    3. En el campo **otros destinatarios de correo electrónico** , escriba las direcciones de correo electrónico de los demás destinatarios que deben recibir las notificaciones de alerta. Escriba los nombres en el formato *someone@somewhere.com*. Use punto y coma para separar las direcciones de correo electrónico. Puede configurar un máximo de 20 direcciones de correo electrónico por dispositivo virtual. 

        ![configuración de alertas de notificación](./media/storsimple-ova-manage-alerts/alerts3.png)

3. En la parte inferior de la página, haga clic en **Guardar** para guardar la configuración.

4. Para enviar una notificación de correo electrónico de prueba, haga clic en el icono de flecha junto a **Enviar correo electrónico de prueba**. El servicio Administrador de StorSimple mostrará mensajes de estado cuando reenvía la notificación de prueba. 

5. Cuando aparezca el siguiente mensaje, haga clic en **Aceptar**. 

    ![Prueba de alertas de notificación por correo electrónico enviado](./media/storsimple-ova-manage-alerts/alerts4.png)

    >[AZURE.NOTE] Si no se puede enviar el mensaje de notificación de prueba, el servicio Administrador de StorSimple mostrará un mensaje adecuado. Haga clic en **Aceptar**, espere unos minutos y, a continuación, intente volver a enviar el mensaje de notificación de prueba. 

    El mensaje de notificación de prueba será similar al siguiente.

    ![Ejemplo de correo electrónico de prueba de alertas](./media/storsimple-ova-manage-alerts/alerts5.png)

## <a name="common-alert-conditions"></a>Condiciones de alerta comunes

La matriz Virtual de StorSimple genera alertas en respuesta a una amplia variedad de condiciones. Estos son los tipos más comunes de las condiciones de alerta:

- **Problemas de conectividad** – estas alertas se producen cuando hay problemas de transferencia de datos. Problemas de comunicación pueden producir durante la transferencia de datos a y desde la cuenta de almacenamiento de Azure o debido a la falta de conectividad entre los dispositivos virtuales y el servicio Administrador de StorSimple. Problemas de comunicación son algunos de los más difíciles de solucionar porque hay muchos puntos de error. En primer lugar siempre debe comprobar que la conectividad de red y acceso a Internet están disponibles antes de continuar con la solución de problemas más avanzados. Para obtener información acerca de puertos y configuración del firewall, vaya a [StorSimple Virtual matriz requisitos del sistema](storsimple-ova-system-requirements.md). Para obtener ayuda con la solución de problemas, vaya a [solucionar problemas con el cmdlet de conexión de prueba](storsimple-troubleshoot-deployment.md).

- **Problemas de rendimiento** – estas alertas se producen cuando el sistema no realiza de manera óptima, como cuando está en una gran carga.

Además, es posible que vea alertas relacionadas con la seguridad, actualizaciones o errores del trabajo.

## <a name="alert-severity-levels"></a>Niveles de gravedad de alerta

Alertas tienen distintos niveles de gravedad, según el impacto que tendrá la situación alerta y la necesidad de una respuesta a la alerta. Los niveles de gravedad son:

- **Tareas críticas** : esta alerta es en respuesta a una condición que afecta el rendimiento óptimo del sistema. Se requiere una acción para asegurarse de que el servicio no se interrumpe de StorSimple.

- **Advertencia** : esta condición podría convertirse en crítica si no se ha resuelto. Debe investigar la situación y realice ninguna acción necesaria para borrar el problema.

- **Información** : esta alerta contiene información que puede ser útil en seguimiento y administración de su sistema.

## <a name="view-and-track-alerts"></a>Ver y realizar un seguimiento de alertas

El panel de servicio de administrador de StorSimple le proporciona un vistazo rápido a la cantidad de alertas en sus dispositivos virtuales, organizados por nivel de gravedad.

![Panel de alertas](./media/storsimple-ova-manage-alerts/alerts6.png)

Haga clic en el nivel de gravedad, se abre la pestaña **alertas** . Los resultados incluyen solo las alertas que coinciden con ese nivel de gravedad.

![Informe de alertas ámbito del tipo de alerta](./media/storsimple-ova-manage-alerts/alerts7.png)

Haga clic en una alerta en la lista le ofrece detalles adicionales para la alerta, incluida la última vez que se ha informado la alerta, el número de apariciones de la alerta en el dispositivo y la acción recomendada para resolver la alerta.

Puede copiar los detalles de alerta en un archivo de texto si debe enviar la información a Microsoft Support. Después de seguir la recomendación y resolver la condición de alerta local, debe desactivar la alerta de selección de la alerta en la pestaña **alertas** y haciendo clic en **Borrar**. Para borrar varias alertas, seleccione cada alerta, haga clic en todas las columnas excepto la columna de la **alerta** y, a continuación, haga clic en **Borrar** después de haber seleccionado todas las alertas se borra. Tenga en cuenta que algunas alertas se desactivarán automáticamente cuando se resuelve el problema o cuando el sistema se actualiza la alerta con información nueva.

Al hacer clic en **Borrar**, tendrá la oportunidad de proporcionar comentarios sobre la alerta y los pasos que realizó para resolver el problema. 

![comentarios de alerta](./media/storsimple-ova-manage-alerts/clear-alert.png)

Haga clic en el icono de verificación ![icono de comprobación](./media/storsimple-ova-manage-alerts/check-icon.png) Para guardar sus comentarios.

Algunos eventos se borrarán por el sistema si se activa otro evento con información nueva. En este caso, verá el siguiente mensaje.

![Mensaje de alerta borrar](./media/storsimple-ova-manage-alerts/alerts8.png)

## <a name="sort-and-review-alerts"></a>Ordenar y revisar alertas

La pestaña **alertas** puede mostrar hasta 250 alertas. Si ha superado el número de alertas, no todas las alertas se mostrará en la vista predeterminada. Puede combinar los siguientes campos para personalizar las alertas que se muestran:

- **Estado** : puede mostrar alertas **activa** o **desactiva** . Aún se desencadenan alertas activas en el sistema mientras desactivadas alertas se han, ya sea manualmente desactivadas por el administrador o desactivada mediante programación porque el sistema actualiza la condición de alerta con información nueva.

- **Gravedad** : puede mostrar alertas de todos los niveles de gravedad (crítica, advertencia, información), o solo una determinada gravedad, como alertas solo críticas.

- **Fuente** : puede mostrar avisos de todas las fuentes, o limitar las alertas a las que se incluyen en el servicio o uno o todos los dispositivos virtuales.

- **Intervalo de tiempo** : especificando las fechas **desde** y **hasta** y marcas de tiempo, puede mirar alertas durante el período de tiempo que le interesa.

## <a name="alerts-quick-reference"></a>Referencia rápida de alertas

Las tablas siguientes enumeran algunas de las alertas de Microsoft Azure StorSimple que pueden surgir, así como información adicional y recomendaciones donde esté disponible. Alertas del dispositivo virtual StorSimple se dividen en una de las siguientes categorías:

- [Alertas de conectividad de nube](#cloud-connectivity-alerts)

- [Alertas de configuración](#configuration-alerts)

- [Alertas de error de trabajo](#job-failure-alerts)

- [Alertas de rendimiento](#performance-alerts)

- [Alertas de seguridad](#security-alerts)

- [Alertas de actualización](#update-alerts)

### <a name="cloud-connectivity-alerts"></a>Alertas de conectividad de nube

|Texto de alerta|Evento|Obtener más información o acciones recomendadas|
|:---|:---|:---|
|Dispositivo *<device name>* no está conectado a la nube.|No se puede conectar el dispositivo con nombre en la nube. |No se pudo conectar a la nube. Esto puede deberse a uno de estos procedimientos:<ul><li>Puede haber un problema con la configuración de red en el dispositivo.</li><li>Puede haber un problema con las credenciales de cuenta de almacenamiento.</li></ul>Para obtener más información acerca de cómo solucionar problemas de conectividad, vaya a la [interfaz de usuario de web local](storsimple-ova-web-ui-admin.md) del dispositivo.|


### <a name="configuration-alerts"></a>Alertas de configuración

|Texto de alerta|Evento|Obtener más información o acciones recomendadas|
|:---|:---|:---|
|No se admite la configuración de dispositivo virtual local.|Rendimiento lento.|La configuración actual puede traducirse en degradación del rendimiento. Asegúrese de que el servidor cumple los requisitos mínimos de configuración. Para obtener más información, vaya a [Requisitos de matriz Virtual StorSimple](storsimple-ova-system-requirements.md). 
|Se está quedando sin espacio en disco aprovisiona en <*nombre de dispositivo*>.|Advertencia de espacio de disco.|Está quedando sin espacio en disco aprovisiona. Para liberar espacio, considere la posibilidad de mover cargas de trabajo a otro volumen o compartir o eliminar datos.

### <a name="job-failure-alerts"></a>Alertas de error de trabajo

|Texto de alerta|Evento|Obtener más información o acciones recomendadas|
|:---|:---|:---|
|No se pudo completar la copia de seguridad de <*nombre del dispositivo*>.|Error de trabajo de copia de seguridad.|No se puede crear una copia de seguridad. Considere la posibilidad de uno de estos procedimientos:<ul><li>Problemas de conectividad podrían impedir la operación de copia de seguridad se complete correctamente. Asegúrese de que no hay ningún problema de conectividad. Para obtener más información acerca de cómo solucionar problemas de conectividad, vaya a la [interfaz de usuario de web local](storsimple-ova-web-ui-admin.md) de su dispositivo virtual.</li><li>Ha alcanzado el límite de almacenamiento disponible. Para liberar espacio, considere la posibilidad de eliminar las copias de seguridad que ya no son necesarias.</li></ul> Resolver los problemas, desactive la alerta y vuelva a intentar la operación.|
|No se pudo completar la restauración de <*nombre del dispositivo*>.|Restaurar el error el trabajo.|No se pudo restaurar copia de seguridad. Considere la posibilidad de uno de estos procedimientos:<ul><li>La lista de copia de seguridad puede no ser válida. Actualizar la lista para comprobar que sigue siendo válido.</li><li>Problemas de conectividad podrían impedir la operación de restauración se complete correctamente. Asegúrese de que no hay ningún problema de conectividad.</li><li>Ha alcanzado el límite de almacenamiento disponible. Para liberar espacio, considere la posibilidad de eliminar las copias de seguridad que ya no son necesarias.</li></ul>Resolver los problemas, desactive la alerta y vuelva a intentar la operación de restauración.|
|Clonar de <*nombre del dispositivo*> no se pudo completar.|Clonar error de trabajo.|No se pudo crear un duplicado. Considere la posibilidad de uno de estos procedimientos:<ul><li>La lista de copia de seguridad puede no ser válida. Actualizar la lista para comprobar que sigue siendo válido.</li><li>Problemas de conectividad podrían impedir la operación de clonar completar correctamente. Asegúrese de que no hay ningún problema de conectividad.</li><li>Ha alcanzado el límite de almacenamiento disponible. Para liberar espacio, considere la posibilidad de eliminar las copias de seguridad que ya no son necesarias.</li></ul>Resolver los problemas, desactive la alerta y vuelva a intentar la operación.|

### <a name="performance-alerts"></a>Alertas de rendimiento

|Texto de alerta|Evento|Obtener más información o acciones recomendadas|
|:---|:---|:---|
|Experimenta retrasos inesperados en transferencia de datos.|Transferencia de datos es lenta.|Se producen errores de limitación cuando supere los objetivos de escalabilidad de un servicio de almacenamiento. El servicio de almacenamiento hace esto para asegurarse de que ningún inquilino o solo cliente puede usar el servicio a costa de otros usuarios. Para obtener más información sobre solución de problemas de la cuenta de almacenamiento de Azure, vaya a [supervisar, diagnosticar y solucionar problemas de Microsoft Azure almacenamiento](storage-monitoring-diagnosing-troubleshooting.md).
|Si ejecuta bajo local reserva espacio en disco en <*nombre de dispositivo*>.|Tiempo de respuesta lento.|10% del total tamaño aprovisiona <*nombre del dispositivo*> está reservado en el dispositivo local y ahora se ejecuta bajo en el espacio reservado. La carga de trabajo en <*nombre de dispositivo*> generan un mayor porcentaje de renovación o podría recientemente migrar una gran cantidad de datos. Esto puede traducirse en disminución del rendimiento. Tenga en cuenta las siguientes acciones para resolver este problema:<ul><li>Aumentar el ancho de banda de la nube para este dispositivo.</li><li>Reducir o mueva las cargas de trabajo a otro volumen o recurso compartido.</li></ul>

### <a name="security-alerts"></a>Alertas de seguridad

|Texto de alerta|Evento|Obtener más información o acciones recomendadas|
|:---|:---|:---|
|Contraseña para <*nombre de dispositivo*> caducará en <*número*> días.|Advertencia de contraseña.| Su contraseña caducará en < número < días. Considere la posibilidad de cambiar la contraseña. Para obtener más información, vaya a [cambiar la contraseña de administrador del dispositivo StorSimple Virtual matriz](storsimple-ova-change-device-admin-password.md).

### <a name="update-alerts"></a>Alertas de actualización

|Texto de alerta|Evento|Obtener más información o acciones recomendadas|
|:---|:---|:---|
|Nuevas actualizaciones están disponibles para su dispositivo.|Actualizaciones de la matriz Virtual StorSimple están disponibles.|Puede instalar las nuevas actualizaciones desde la página **Mantenimiento** .|
|No se pudo analizar nuevas actualizaciones en <*nombre de dispositivo*>.|Error de actualización. |Se produjo un error al instalar las nuevas actualizaciones. Puede instalar manualmente las actualizaciones. Si el problema continúa, póngase en contacto con el [Soporte técnico de Microsoft](storsimple-contact-microsoft-support.md).|


## <a name="next-steps"></a>Pasos siguientes

- [Obtenga más información sobre la matriz Virtual StorSimple](storsimple-ova-overview.md).



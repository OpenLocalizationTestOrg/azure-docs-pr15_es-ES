<properties 
   pageTitle="Ver y administrar alertas StorSimple | Microsoft Azure"
   description="Describe las condiciones de alerta de StorSimple y gravedad, cómo configurar las notificaciones de alerta y cómo usar el servicio Administrador de StorSimple para administrar alertas."
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
   ms.workload="NA"
   ms.date="10/18/2016"
   ms.author="anbacker" />

# <a name="use-the-storsimple-manager-service-to-view-and-manage-storsimple-alerts"></a>Usar el servicio Administrador de StorSimple para ver y administrar alertas StorSimple

## <a name="overview"></a>Información general

La ficha **alertas** en el servicio Administrador de StorSimple ofrece una manera para revisar y desactive las alertas relacionadas con el dispositivo de StorSimple en tiempo real. En esta pestaña, puede supervisar de forma centralizada los problemas de salud de los dispositivos de StorSimple y la solución de Microsoft Azure StorSimple general.

Este tutorial describe las condiciones de alerta comunes, niveles de gravedad de alerta y cómo configurar las notificaciones de alerta. Además, incluye tablas de alerta de referencia rápida, que le permite rápidamente, busque una alerta específica y responder según corresponda.

![Página de alertas](./media/storsimple-manage-alerts/HCS_AlertsPage.png)

## <a name="common-alert-conditions"></a>Condiciones de alerta comunes

El dispositivo StorSimple genera alertas en respuesta a una amplia variedad de condiciones. Estos son los tipos más comunes de las condiciones de alerta:

- **Problemas de hardware** : estas alertas informarle acerca del estado del hardware. Le avise si se necesitan actualizaciones de firmware, si una interfaz de red tiene problemas, o si hay un problema con una de las unidades de datos.

- **Problemas de conectividad** – estas alertas se producen cuando hay problemas de transferencia de datos. Problemas de comunicación pueden producir durante la transferencia de datos a y desde la cuenta de almacenamiento de Azure o debido a la falta de conectividad entre los dispositivos y el servicio Administrador de StorSimple. Problemas de comunicación son algunos de los más difíciles de solucionar porque hay muchos puntos de error. En primer lugar siempre debe comprobar que la conectividad de red y acceso a Internet están disponibles antes de continuar con la solución de problemas más avanzados. Para obtener ayuda con la solución de problemas, vaya a [solucionar problemas con el cmdlet de conexión de prueba](storsimple-troubleshoot-deployment.md).

- **Problemas de rendimiento** – estas alertas se producen cuando el sistema no realiza de manera óptima, como cuando está en una gran carga.

Además, es posible que vea alertas relacionadas con la seguridad, actualizaciones o errores del trabajo.

## <a name="alert-severity-levels"></a>Niveles de gravedad de alerta

Alertas tienen distintos niveles de gravedad, según el impacto que tendrá la situación alerta y la necesidad de una respuesta a la alerta. Los niveles de gravedad son:

- **Tareas críticas** : esta alerta es en respuesta a una condición que afecta el rendimiento óptimo del sistema. Se requiere una acción para asegurarse de que el servicio no se interrumpe de StorSimple.

- **Advertencia** : esta condición podría convertirse en crítica si no se ha resuelto. Debe investigar la situación y realice ninguna acción necesaria para borrar el problema.

- **Información** : esta alerta contiene información que puede ser útil en seguimiento y administración de su sistema.

## <a name="configure-alert-settings"></a>Configurar las alertas

Puede elegir si desea recibir una notificación por correo electrónico de las condiciones de alerta para cada uno de los dispositivos StorSimple. Además, puede identificar a otros destinatarios de notificación de alerta escribiendo sus direcciones de correo electrónico en el cuadro de **otros destinatarios de correo electrónico** , separado por punto y coma.

>[AZURE.NOTE] Puede introducir un máximo de 20 direcciones de correo electrónico por dispositivo.

Después de habilitar la notificación de correo electrónico de un dispositivo, los miembros de la lista de notificación recibirá un mensaje de correo electrónico cada vez que se produce una alerta crítica. Los mensajes se enviarán de *storsimple-alerts-noreply@mail.windowsazure.com* y se describe la condición de alerta. Los destinatarios pueden haga clic en **Cancelar suscripción** para que se quiten de la lista de notificación de correo electrónico.

#### <a name="to-enable-email-notification-of-alerts-for-a-device"></a>Para habilitar la notificación de correo electrónico de alertas para un dispositivo

1. Vaya a **dispositivos** > **Configurar** el dispositivo.

2. En **Configuración de alertas**, defina las siguientes opciones:

    1. En el campo **Enviar notificación de correo electrónico** , seleccione **Sí**.

    2. En el campo **administradores de servicios de correo electrónico** , seleccione **Sí** si desea que el administrador del servicio y todos los coadministradores reciben las notificaciones de alerta.

    3. En el campo **otros destinatarios de correo electrónico** , escriba las direcciones de correo electrónico de los demás destinatarios que deben recibir las notificaciones de alerta. Escriba los nombres en el formato *someone@somewhere.com*. Use punto y coma para separar las direcciones de correo electrónico. Puede configurar un máximo de 20 direcciones de correo electrónico por dispositivo. 

        ![Configuración de alertas de notificación](./media/storsimple-manage-alerts/AlertNotify.png)

3. Para enviar una notificación de correo electrónico de prueba, haga clic en el icono de flecha junto a **Enviar correo electrónico de prueba**. El servicio Administrador de StorSimple mostrará mensajes de estado cuando reenvía la notificación de prueba. 

4. Cuando aparezca el siguiente mensaje, haga clic en **Aceptar**. 

    ![Prueba de alertas de notificación por correo electrónico enviado](./media/storsimple-manage-alerts/HCS_AlertNotificationConfig3.png)

    >[AZURE.NOTE] Si no se puede enviar el mensaje de notificación de prueba, el servicio Administrador de StorSimple mostrará un mensaje adecuado. Haga clic en **Aceptar**, espere unos minutos y, a continuación, intente volver a enviar el mensaje de notificación de prueba. 

## <a name="view-and-track-alerts"></a>Ver y realizar un seguimiento de alertas

El panel de servicio de administrador de StorSimple le proporciona un vistazo rápido a la cantidad de alertas en sus dispositivos, organizados por nivel de gravedad.

![Panel de alertas](./media/storsimple-manage-alerts/admin_alerts_dashboard.png)

Haga clic en el nivel de gravedad, se abre la pestaña **alertas** . Los resultados incluyen solo las alertas que coinciden con ese nivel de gravedad.

![Informe de alertas ámbito del tipo de alerta](./media/storsimple-manage-alerts/admin_alerts_scoped.png)

Haga clic en una alerta en la lista le ofrece detalles adicionales para la alerta, incluida la última vez que se ha informado la alerta, el número de apariciones de la alerta en el dispositivo y la acción recomendada para resolver la alerta. Si se trata de una alerta de hardware, también identifica el componente de hardware.

![Ejemplo de alerta de hardware](./media/storsimple-manage-alerts/admin_alerts_hardware.png)

Puede copiar los detalles de alerta en un archivo de texto si debe enviar la información a Microsoft Support. Después de seguir la recomendación y resolver la condición de alerta local, debe desactivar la alerta desde el dispositivo seleccionando la alerta en la pestaña **alertas** y haciendo clic en **Borrar**. Para borrar varias alertas, seleccione cada alerta, haga clic en todas las columnas excepto la columna de la **alerta** y, a continuación, haga clic en **Borrar** después de haber seleccionado todas las alertas se borra. Tenga en cuenta que algunas alertas se desactivarán automáticamente cuando se resuelve el problema o cuando el sistema se actualiza la alerta con información nueva.

Al hacer clic en **Borrar**, tendrá la oportunidad de proporcionar comentarios sobre la alerta y los pasos que realizó para resolver el problema. Algunos eventos se borrarán por el sistema si se activa otro evento con información nueva. En este caso, verá el siguiente mensaje.

![Mensaje de alerta borrar](./media/storsimple-manage-alerts/admin_alerts_system_clear.png)

## <a name="sort-and-review-alerts"></a>Ordenar y revisar alertas

Puede que le resulte más eficaz ejecutar informes sobre alertas para que puedan revisar y anular la selección de grupos. Además, la pestaña **alertas** puede mostrar hasta 250 alertas. Si ha superado el número de alertas, no todas las alertas se mostrará en la vista predeterminada. Puede combinar los siguientes campos para personalizar las alertas que se muestran:

- **Estado** : puede mostrar alertas **activa** o **desactiva** . Aún se desencadenan alertas activas en el sistema mientras desactivadas alertas se han, ya sea manualmente desactivadas por el administrador o desactivada mediante programación porque el sistema actualiza la condición de alerta con información nueva.

- **Gravedad** : puede mostrar alertas de todos los niveles de gravedad (crítica, advertencia, información), o solo una determinada gravedad, como alertas solo críticas.

- **Fuente** : puede mostrar avisos de todas las fuentes o limitar las alertas a las que se incluyen en el servicio o uno o todos los dispositivos.

- **Intervalo de tiempo** : especificando las fechas **desde** y **hasta** y marcas de tiempo, puede mirar alertas durante el período de tiempo que le interesa.

## <a name="alerts-quick-reference"></a>Referencia rápida de alertas

Las tablas siguientes enumeran algunas de las alertas de Microsoft Azure StorSimple que pueden surgir, así como información adicional y recomendaciones donde esté disponible. Alertas del dispositivo StorSimple se dividen en una de las siguientes categorías:

- [Alertas de conectividad de nube](#cloud-connectivity-alerts)

- [Alertas de clúster](#cluster-alerts)

- [Alertas de recuperación de desastres](#disaster-recovery-alerts)

- [Alertas de hardware](#hardware-alerts)

- [Alertas de error de trabajo](#job-failure-alerts)

- [Alertas de volumen localmente anclada](#locally-pinned-volume-alerts)

- [Alertas de redes](#networking-alerts)

- [Alertas de rendimiento](#performance-alerts)

- [Alertas de seguridad](#security-alerts)

- [Alertas del paquete de compatibilidad](#support-package-alerts)

- [Alertas de actualización](#update-alerts)

### <a name="cloud-connectivity-alerts"></a>Alertas de conectividad de nube

|Texto de alerta|Evento|Obtener más información o acciones recomendadas|
|:---|:---|:---|
|No se puede establecer la conectividad con <*nombre de la credencial de nube*>.|No puede conectarse a la cuenta de almacenamiento.|Parece que podría haber un problema de conectividad con el dispositivo. Vuelva a ejecutar la `Test-HcsmConnection` cmdlet desde la interfaz de Windows PowerShell para StorSimple en su dispositivo para identificar y solucionar el problema. Si la configuración es correcta, el problema podría ser con las credenciales de la cuenta de almacenamiento para el que se produce la alerta. En este caso, use la `Test-HcsStorageAccountCredential` cmdlet para determinar si hay problemas que se pueden resolver.<ul><li>Compruebe la configuración de red.</li><li>Compruebe las credenciales de cuenta de almacenamiento.</li></ul>|
|No hemos recibido un latido desde el dispositivo para los últimos <*número*> minutos.|No puede conectarse al dispositivo.|Parece que hay un problema de conectividad con el dispositivo. Utilice la `Test-HcsmConnection` cmdlet desde la interfaz de Windows PowerShell para StorSimple en su dispositivo para identificar y solucionar el problema o póngase en contacto con el Administrador de red.|

### <a name="storsimple-behavior-when-cloud-connectivity-fails"></a>Comportamiento de StorSimple cuando se produce un error en la conectividad de nube

¿Qué ocurre si se produce un error en la nube conectividad de mi dispositivo StorSimple en producción?

Si se produce un error en la nube conectividad en el dispositivo de producción StorSimple, a continuación, dependiendo del estado de su dispositivo, puede ocurrir lo siguiente: 

- **Para los datos locales en su dispositivo**: algún tiempo no estará interrupciones y lecturas seguirá representarse. Sin embargo, como el número de IOs pendientes aumenta y excede el límite, las lecturas pueden iniciar un error. 

    Según la cantidad de datos en el dispositivo, la escritura también seguirá ocurrir por las primeras horas después de la interrupción de la conectividad de la nube. La escritura se luego ralentizar y finalmente que se produzcan errores si se interrumpe la conectividad de nube para varias horas. (Hay almacenamiento temporal en el dispositivo de datos que se enviarán a la nube. Esta área se vacía cuando se envían los datos. Si se produce un error en la conectividad, datos en esta área de almacenamiento no se enviarán a la nube y se producirá un error IO.)   

 
- **Para los datos en la nube**: para la mayoría de los errores de conectividad de nube, se devuelve un error. Una vez que se restaura la conectividad, se reanude el IOs sin que el usuario tenga para que aparezca el volumen en línea. En algunas ocasiones, intervención del usuario puede ser necesario realizar para devolver el volumen en línea desde el portal de clásico de Azure. 
 
- **Para las instantáneas de la nube en curso**: se vuelve a intentar la operación varias veces dentro de 4-5 horas y si no se restaura la conectividad, se producirá un error en las instantáneas de la nube.


### <a name="cluster-alerts"></a>Alertas de clúster

|Texto de alerta|Evento|Obtener más información o acciones recomendadas|
|:---|:---|:---|
|Dispositivo no ha podido sobre <*nombre del dispositivo*>.|Dispositivo está en modo de mantenimiento.|Error de dispositivo sobre debido a entra o sale de modo de mantenimiento. Esto es normal y es necesario realizar ninguna acción. Después de que ha confirmado esta alerta, desactive desde la página de alertas.|
|Dispositivo no ha podido sobre <*nombre del dispositivo*>.|Solo se actualizó firmware del dispositivo o el software.|Se ha producido un error de clúster debido a una actualización. Esto es normal y es necesario realizar ninguna acción. Después de que ha confirmado esta alerta, desactive desde la página de alertas.|
|Dispositivo no ha podido sobre <*nombre del dispositivo*>.|Apagar o reiniciar controlador.|Error de dispositivo sobre porque el controlador de active apagar o reiniciar un administrador. Es necesario realizar ninguna acción. Después de que ha confirmado esta alerta, desactive desde la página de alertas.|
|Dispositivo no ha podido sobre <*nombre del dispositivo*>.|Failover planificado.|Compruebe que se trataba de un error planeado. Después de haber realizado la acción correspondiente, desactive esta alerta en la página de alertas.|
|Dispositivo no ha podido sobre <*nombre del dispositivo*>.|No planeado de migración tras error.|StorSimple integrado para recuperar automáticamente de planificado migraciones tras error. Si ve un gran número de las alertas, póngase en contacto con Microsoft Support.|
|Dispositivo no ha podido sobre <*nombre del dispositivo*>.|Causa de otro o desconocido.|Si ve un gran número de las alertas, póngase en contacto con Microsoft Support. Después de que se resuelve el problema, desactive esta alerta en la página de alertas.|
|Un servicio de dispositivos críticos informes de estado como error.|Error en el servicio DataPath. |Para obtener ayuda, póngase en contacto con Microsoft Support.|
|Dirección IP virtual de interfaz de red <*datos #*> informes de estado de error. |Causa de otro o desconocido. |Condiciones temporales a veces pueden provocar estas alertas. Si este es el caso, a continuación, esta alerta automáticamente borrarán después de algún tiempo. Si el problema continúa, póngase en contacto con Microsoft Support.|
|Dirección IP virtual de interfaz de red <*datos #*> informes de estado de error.|Nombre de la interfaz: <*datos #*> dirección IP <IP address> no se puede poner en línea porque se ha detectado una dirección IP duplicada en la red. |Asegúrese de que la dirección IP duplicada se quita de la red o vuelva a la interfaz con una dirección IP diferente.|


### <a name="disaster-recovery-alerts"></a>Alertas de recuperación de desastres

|Texto de alerta|Evento|Obtener más información o acciones recomendadas|
|:---|:---|:---|
|Las operaciones de recuperación no pueden restaurar toda la configuración de este servicio. Datos de configuración de dispositivo se están en un estado incoherente para algunos dispositivos.|Incoherencia de datos detectada después de la recuperación.|Datos cifrados en el servicio no se sincronizan con la en el dispositivo. Autorizar el dispositivo <*nombre de dispositivo*> desde el Administrador de StorSimple para iniciar el proceso de sincronización. Usar la interfaz de Windows PowerShell para StorSimple para ejecutar el `Restore-HcsmEncryptedServiceData` en dispositivo <*nombre de dispositivo*> cmdlet, proporcionar la contraseña antigua como entrada para este cmdlet para restaurar el perfil de seguridad. Vuelva a ejecutar la `Invoke-HcsmServiceDataEncryptionKeyChange` cmdlet de para actualizar la clave de cifrado de datos de servicio. Después de haber realizado la acción correspondiente, desactive esta alerta en la página de alertas.|


### <a name="hardware-alerts"></a>Alertas de hardware

|Texto de alerta|Evento|Obtener más información o acciones recomendadas|
|:---|:---|:---|
|Componente de hardware <*Id. de componente*> informes de estado como <*estado*>.||Condiciones temporales a veces pueden provocar estas alertas. Si es así, esta alerta se borrarán automáticamente después de algún tiempo. Si el problema continúa, póngase en contacto con Microsoft Support.|
|Controlador pasivo no funciona correctamente.|No funciona el controlador pasivo (secundario).|El dispositivo funciona, pero uno de los controladores se funciona correctamente. Intente reiniciar dicho controlador. Si no se resuelve el problema, póngase en contacto con Microsoft Support.|

### <a name="job-failure-alerts"></a>Alertas de error de trabajo

|Texto de alerta|Evento|Obtener más información o acciones recomendadas|
|:---|:---|:---|
|Error de copia de seguridad de <*Id. de grupo de volumen de origen*>.|Error de trabajo de copia de seguridad.|Problemas de conectividad podrían impedir la operación de copia de seguridad se complete correctamente. Si no hay ningún problema de conectividad, puede que ha alcanzado el número máximo de copias de seguridad. Eliminar las copias de seguridad que ya no son necesarias y vuelva a intentar la operación. Después de haber realizado la acción correspondiente, desactive esta alerta en la página de alertas.|
|No se pudo clonar de <*identificadores de elemento de copia de seguridad de origen*> a <*números de serie de volumen de destino*>.|Error al clonar trabajo.|Actualizar la lista de copia de seguridad para comprobar que la copia de seguridad sigue siendo válido. Si la copia de seguridad es válida, es posible que los problemas de conectividad de nube están impidiendo complete la operación clonar de correctamente. Si no hay ningún problema de conectividad, puede que ha alcanzado el límite de almacenamiento. Eliminar las copias de seguridad que ya no son necesarias y vuelva a intentar la operación. Después de haber completado la acción apropiada para resolver el problema, desactive esta alerta en la página de alertas.|
|No se pudo restaurar de <*identificadores de elemento de copia de seguridad de origen*>.|Restaurar error del trabajo.|Actualizar la lista de copia de seguridad para comprobar que la copia de seguridad sigue siendo válido. Si la copia de seguridad es válida, es posible que problemas de conectividad de nube impiden la operación de restauración se complete correctamente. Si no hay ningún problema de conectividad, puede que ha alcanzado el límite de almacenamiento. Eliminar las copias de seguridad que ya no son necesarias y vuelva a intentar la operación. Después de haber completado la acción apropiada para resolver el problema, desactive esta alerta en la página de alertas.|

### <a name="locally-pinned-volume-alerts"></a>Alertas de volumen localmente anclada

|Texto de alerta|Evento|Obtener más información o acciones recomendadas|
|:---|:---|:---|
|Error al crear volumen local <*nombre de volumen*>.| Error en el trabajo de creación de volumen. <*Error mensaje correspondiente al código de error en*>.|Problemas de conectividad podrían impedir la operación de creación de espacio se complete correctamente. Grosor aprovisionados volúmenes localmente anclados y el proceso de creación espacio implica salpicadura volúmenes en niveles en la nube. Si no hay ningún problema de conectividad, puede haber agotado el espacio en el dispositivo local. Determinar si hay espacio en el dispositivo antes de reintentar esta operación.|
|Error de expansión de volumen local <*nombre de volumen*>.|El trabajo de modificación de volumen error debido a que <*error mensaje correspondiente al código de error en*>.|Problemas de conectividad podrían impedir la operación de expansión de volumen de correctamente. Grosor aprovisionados volúmenes localmente anclados y el proceso de extensión del espacio existente implica salpicadura volúmenes en niveles en la nube. Si no hay ningún problema de conectividad, puede haber agotado el espacio en el dispositivo local. Determinar si hay espacio en el dispositivo antes de reintentar esta operación.|
|Error de conversión de <*nombre de volumen*> volumen.|El trabajo de conversión de volumen para convertir el tipo de volumen de localmente anclados a niveles de error.|Conversión del volumen de tipo localmente anclado a niveles no pudo completarse. Asegúrese de que no hay ningún problema de conectividad impide complete la operación de correctamente. Para solucionar problemas de conectividad vaya a [solucionar problemas con el cmdlet HcsmConnection de prueba](storsimple-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet).<br>El volumen localmente anclado original se marcó como un volumen en niveles puesto que algunos de los datos desde el volumen localmente anclado ha derramado en la nube durante la conversión. El volumen resultante en niveles aún ocupen espacio local en el dispositivo que no se puede reclamar para futuros volúmenes locales.<br>Resolver los problemas de conectividad, desactive la alerta y este volumen volver a convertir el tipo de volumen localmente anclada original para garantizar que todos los datos se localmente vuelve a estar disponibles.|
|Error de conversión de <*nombre de volumen*> volumen.|El trabajo de conversión de volumen para convertir el tipo de volumen de niveles a localmente anclados falló.|Conversión del volumen de tipo niveles a localmente anclados no se pueden completar. Asegúrese de que no hay ningún problema de conectividad impide complete la operación de correctamente. Para solucionar problemas de conectividad vaya a [solucionar problemas con el cmdlet HcsmConnection de prueba](storsimple-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet).<br>El volumen niveles original marcado como un volumen localmente anclado como parte del proceso de conversión sigue tener datos que se encuentran en la nube, mientras el grosor aprovisiona espacio en el dispositivo para este volumen ya no puede reclamar para futuros volúmenes locales.<br>Resolver los problemas de conectividad, desactive la alerta y este volumen volver a convertir el tipo de volumen en niveles original para asegurarse de que puede reclamar espacio local que grosor se incluye en el dispositivo.|
|Casi consumo de espacio local para instantáneas locales de <*nombre del grupo de volumen*>|Instantáneas locales para la directiva de copia de seguridad pronto podrían quedarse sin espacio y se invalidarán para evitar errores de escritura de host.|Instantáneas locales frecuentes junto con una renovación alta datos en los volúmenes asociados a este grupo de directivas de copia de seguridad causan el espacio local en el dispositivo para consumir rápidamente. Eliminar cualquier instantáneas locales que ya no son necesarias. Asimismo, actualice las programaciones de instantánea local para que tenga menos frecuentes instantáneas locales y, asegúrese de que se toman con regularidad instantáneas de nube directiva de copia de seguridad. Si no se realizan estas acciones, pronto se podría agota el espacio local para estas instantáneas y el sistema eliminará automáticamente para asegurarse de que escribe host seguirán procesando correctamente.|
|Han sido invalidarán instantáneas locales para <*nombre del grupo de volumen*>.|Las instantáneas locales <*nombre del grupo de volumen*> se invalidarán y, a continuación, eliminadas debido a superaban el espacio en el dispositivo local.|Para asegurarse de que esto no se repite en el futuro, revise las programaciones de instantánea local de esta directiva de copia de seguridad y eliminar cualquier instantáneas locales que ya no son necesarias. Instantáneas locales frecuentes junto con una renovación alta datos en los volúmenes asociados a este grupo de directivas de copia de seguridad pueden provocar que el espacio local en el dispositivo para consumir rápidamente.|
|No se pudo restaurar de <*identificadores de elemento de copia de seguridad de origen*>.|Error en el trabajo de restauración.|Si se han anclado localmente o una mezcla de volúmenes localmente anclados y niveles de esta directiva de copia de seguridad, actualizar la lista de copia de seguridad para comprobar que la copia de seguridad sigue siendo válido. Si la copia de seguridad es válida, es posible que problemas de conectividad de nube impiden la operación de restauración se complete correctamente. Los volúmenes localmente anclados ser restaurados como parte de este grupo de instantánea no tiene todos sus datos que se descargan en el dispositivo y, si tiene una mezcla de volúmenes en niveles y anclados localmente en este grupo de instantánea, no estarán sincronizados entre sí. Para completar la operación de restauración, tomar los volúmenes en este grupo sin conexión en el host y vuelva a intentar la operación de restauración. Tenga en cuenta que las modificaciones de los datos de volumen que se realizaron durante el proceso de restauración se perderán.|

### <a name="networking-alerts"></a>Alertas de redes

|Texto de alerta|Evento|Obtener más información o acciones recomendadas|
|:---|:---|:---|
|No se pudo iniciar el servicio StorSimple.|Error de DataPath |Si el problema continúa, póngase en contacto con Microsoft Support.|
|Dirección IP duplicada detectada para 'Data0'.| |El sistema ha detectado un conflicto de dirección IP '10.0.0.1'. El recurso de red 'Data0' en el dispositivo *<device1>* está desconectado. Asegúrese de que no se usa esta dirección IP por cualquier otra entidad de esta red. Para solucionar problemas de red, vaya a [solucionar problemas con el cmdlet Get-AdaptadorRed](storsimple-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). Para resolver este problema, póngase en contacto con el Administrador de red. Si el problema continúa, póngase en contacto con Microsoft Support. |
|Direcciones IPv4 (o IPv6) para 'Data0' está sin conexión.| |El recurso de red 'Data0' con la dirección IP '10.0.0.1'. y longitud '22' en el dispositivo del prefijo *<device1>* está desconectado. Asegúrese de que los puertos del conmutador a la que está conectada esta interfaz están en funcionamiento. Para solucionar problemas de red, vaya a [solucionar problemas con el cmdlet Get-AdaptadorRed](storsimple-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). |
 

### <a name="performance-alerts"></a>Alertas de rendimiento

|Texto de alerta|Evento|Obtener más información o acciones recomendadas|
|:---|:---|:---|
|La carga del dispositivo ha sobrepasado <*umbral*>.|Más lento que los tiempos de respuesta esperado.|La utilización de informes de dispositivo en una gran carga de entrada y salida. Esto podría provocar que el dispositivo que no funciona bien como debería. Revisar las cargas de trabajo que ha conectado al dispositivo y determinar si hay alguna que se pudieron mover a otro dispositivo o que ya no son necesarias.<br>Para conocer el estado actual, vaya a [usar el servicio de administrador de StorSimple para supervisar su dispositivo](storsimple-monitor-device.md)|

### <a name="security-alerts"></a>Alertas de seguridad

|Texto de alerta|Evento|Obtener más información o acciones recomendadas|
|:---|:---|:---|
|Ha iniciado sesión Microsoft Support.|Terceros acceso a la sesión de soporte técnico.|Confirme que está autorizado el acceso. Después de haber realizado la acción correspondiente, desactive esta alerta en la página de alertas.|
|Contraseña para <*elemento*> caducará en <*longitud de tiempo*>.|Se está acercando la caducidad de la contraseña.|Cambiar la contraseña antes de que caduque.|
|Información de configuración de seguridad que faltan <*Id. de elemento*>.||No se pueden usar los volúmenes asociados a este contenedor de volumen para replicar la configuración de StorSimple. Para asegurarse de que sus datos están almacenados de forma segura, se recomienda que elimina el contenedor de volumen y los volúmenes asociados con el contenedor de volumen. Después de haber realizado la acción correspondiente, desactive esta alerta en la página de alertas.|
|<*número*> intentos de inicio de sesión no se pudo <*Id. de elemento*>.|Inicio de sesión varios intentos.|El dispositivo esté en ataque o un usuario no autorizado intenta conectar con una contraseña incorrecta.<ul><li>Póngase en contacto con los usuarios autorizados y compruebe que estos intentos eran desde un origen de confianza. Si sigue viendo grandes cantidades de intentos de inicio de sesión, considere la posibilidad de deshabilitar la administración remota y ponerse en contacto con el Administrador de red. Después de haber realizado la acción correspondiente, desactive esta alerta en la página de alertas.</li><li>Compruebe que las instancias de administrador de instantáneas estén configuradas con la contraseña correcta. Después de haber realizado la acción correspondiente, desactive esta alerta en la página de alertas.</li></ul>Para obtener más información, vaya a [cambiar una contraseña de dispositivo expirada](storsimple-snapshot-manager-manage-devices.md#change-an-expired-device-password).|
|Uno o más errores al cambiar la clave de cifrado de datos de servicio.||Hubo errores al cambiar la clave de cifrado de datos de servicio. Después de que se han resuelto las condiciones de error, ejecute el `Invoke-HcsmServiceDataEncryptionKeyChange` cmdlet desde la interfaz de Windows PowerShell para StorSimple en su dispositivo para actualizar el servicio. Si este problema persiste, póngase en contacto con el soporte técnico de Microsoft. Después de resolver el problema, desactive esta alerta en la página de alertas.|

### <a name="support-package-alerts"></a>Alertas del paquete de compatibilidad

|Texto de alerta|Evento|Obtener más información o acciones recomendadas|
|:---|:---|:---|
|Error al crear el paquete de compatibilidad.|StorSimple no se pudo generar el paquete.|Vuelva a intentar esta operación. Si el problema continúa, póngase en contacto con Microsoft Support. Una vez que haya resuelto el problema, desactive esta alerta en la página de alertas.|

### <a name="update-alerts"></a>Alertas de actualización

|Texto de alerta|Evento|Obtener más información o acciones recomendadas|
|:---|:---|:---|
|Revisión instalada.|Actualización de software o firmware completada.|La revisión se ha instalado correctamente en el dispositivo.|
|Manuales actualizaciones disponibles.|Notificación de actualizaciones disponibles.|Usar la interfaz de Windows PowerShell para StorSimple en su dispositivo para instalar estas actualizaciones. <br>Para obtener más información, vaya a [actualizar el dispositivo de la serie 8000 StorSimple](storsimple-update-device.md).|
|Nuevas actualizaciones disponibles.|Notificación de actualizaciones disponibles.|Puede instalar estas actualizaciones desde la página **Mantenimiento** o utilizando la interfaz de Windows PowerShell para StorSimple en el dispositivo. <br>Para obtener más información, vaya a [actualizar el dispositivo de la serie 8000 StorSimple](storsimple-update-device.md).|
|No se pudo instalar actualizaciones.|Las actualizaciones no se instalaron correctamente.|El sistema no pudo instalar las actualizaciones. Puede instalar estas actualizaciones desde la página **Mantenimiento** o utilizando la interfaz de Windows PowerShell para StorSimple en el dispositivo. Si el problema continúa, póngase en contacto con Microsoft Support. <br>Para obtener más información, vaya a [actualizar el dispositivo de la serie 8000 StorSimple](storsimple-update-device.md).|
|No se puede buscar nuevas actualizaciones automáticamente.|Error de comprobación automática.|Puede comprobar manualmente nuevas actualizaciones desde la página **Mantenimiento** .|
|Nuevo agente WUA disponible.|Notificación de actualización disponible.|El agente de Windows Update más reciente de descargar e instalar desde la interfaz de Windows PowerShell.|
|Versión del componente de firmware <*Id. de componente*> no coincide con el hardware.|Actualizaciones de firmware no se instalaron correctamente.|Póngase en contacto con soporte técnico de Microsoft.|

## <a name="next-steps"></a>Pasos siguientes

Más información sobre [StorSimple errores y solución de problemas de un dispositivo de operaciones](storsimple-troubleshoot-operational-device.md).


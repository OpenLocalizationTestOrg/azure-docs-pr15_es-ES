<properties
    pageTitle="Habilitar supervisión y diagnósticos de Microsoft Azure | Microsoft Azure "
    description="Obtenga información sobre cómo configurar el diagnóstico para los recursos en Azure."
    authors="rboucher"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2015"
    ms.author="robb"/>

# <a name="enable-monitoring-and-diagnostics"></a>Habilitar la supervisión y diagnósticos

En el [Portal de Azure](https://portal.azure.com), puede configurar sofisticada y frecuentes, supervisión y datos de diagnóstico acerca de los recursos. También puede usar la [API de REST](https://msdn.microsoft.com/library/azure/dn931932.aspx) o [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) para configurar diagnósticos mediante programación.

Datos de diagnóstico, supervisión y métrica en Azure se guardan en una cuenta de almacenamiento de su elección. Esto le permite usar las herramientas que desea leen los datos desde el Explorador de almacenamiento, Power BI para herramientas de terceros.

## <a name="when-you-create-a-resource"></a>Cuando se crea un recurso

La mayoría de servicios le permiten diagnósticos cuando se crea en primer lugar en el [Portal de Azure](https://portal.azure.com).

1. Vaya a **nuevo** y elija el recurso que le interesa.

2. Seleccione **Configuración opcional**.
    ![Módulo de diagnóstico](./media/insights-how-to-use-diagnostics/Insights_CreateTime.png)

3. Seleccione **Diagnósticos**y haga clic **en**. Debe elegir la cuenta de almacenamiento que desee diagnósticos se guarden en. Se le cobrará velocidades de datos normal de almacenamiento y las transacciones cuando envíe diagnósticos a una cuenta de almacenamiento.

4. Haga clic en **Aceptar** y vuelva a crear el recurso.

## <a name="change-settings-for-an-existing-resource"></a>Cambiar la configuración de un recurso existente

Si ya ha creado un recurso y desea cambiar la configuración de diagnósticos (para cambiar el nivel de recopilación de datos, por ejemplo), puede hacerlo directamente en el Portal de Azure.

1. Vaya al recurso y haga clic en el comando de **configuración** .

2. Seleccione **diagnóstico**.

3. El módulo de **Diagnósticos** tiene todos los posibles diagnósticos y la supervisión de conjunto de datos para ese recurso. También puede elegir una directiva de **retención** para los datos, para limpiar desde su cuenta de almacenamiento para algunos recursos.
    ![Diagnósticos de almacenamiento](./media/insights-how-to-use-diagnostics/Insights_StorageDiagnostics.png)

4. Una vez que ha elegido la configuración, haga clic en el comando **Guardar** . Puede tardar un poco mientras para supervisar los datos para mostrar si se habilita por primera vez.

### <a name="categories-of-data-collection-for-virtual-machines"></a>Categorías de recopilación de datos para máquinas virtuales
Para máquinas virtuales todas las medidas y los registros se registrará en intervalos de un minuto, para que siempre tenga la información más actualizada sobre su equipo.

- **Métricas básicas** : medidas de estado acerca de la máquina virtual como procesador y memoria
- **Métrica de red y web** : métricas sobre sus conexiones de red y servicios web
- **Métricas .NET** : métricas sobre las aplicaciones de .NET y ASP.NET que se ejecutan en el equipo virtual
- **Métrica de SQL** : si está ejecutando el servicio de Microsoft SQL, sus indicadores de rendimiento
- **Registros de aplicación de eventos de Windows** : eventos de Windows que se envían al canal de aplicación
- **Registros del sistema de eventos de Windows** : eventos de Windows que se envían al canal del sistema. Esto también incluye todos los eventos de [Antimalware de Microsoft](http://go.microsoft.com/fwlink/?LinkID=404171&clcid=0x409).
- **Registros de seguridad de eventos de Windows** : eventos de Windows que se envían al canal de seguridad
- **Registros de diagnósticos de infraestructura** : el registro de información sobre la infraestructura de recopilación de diagnóstico
- **Registros de IIS** : registros acerca de su servidor IIS

Tenga en cuenta que en este momento no se admiten algunas distribuciones de Linux y debe estar instalado el agente de invitado en la máquina virtual.

## <a name="next-steps"></a>Pasos siguientes

* [Recibir notificaciones de alerta](insights-receive-alert-notifications.md) cuando se producen eventos operativos o métricas entre un umbral.
* [Medidas de servicio del monitor](insights-how-to-customize-monitoring.md) para asegurarse de que el servicio está disponible y capacidad de respuesta.
* [Ajustar automáticamente el recuento de instancias de](insights-how-to-scale.md) para asegurarse de que su escala de servicio basado en la demanda.
* [Supervisar el rendimiento de aplicación](../application-insights/app-insights-azure-web-apps.md) si desea saber exactamente cómo está funcionando su código en la nube.
* [Ver eventos y registros de auditoría](insights-debugging-with-events.md) para obtener todo lo que ha sucedido en el servicio.
* [Estado del servicio de seguimiento](insights-service-health.md) para averiguar cuándo Azure ha sufrido interrupciones de servicio o degradación del rendimiento.

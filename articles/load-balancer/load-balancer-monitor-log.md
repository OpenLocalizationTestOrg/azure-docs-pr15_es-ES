<properties
   pageTitle="Supervisar las operaciones, eventos y contadores para equilibrador de carga | Microsoft Azure"
   description="Aprenda a habilitar eventos de alerta y registro de estado de mantenimiento de Azure equilibrador de carga de sondeo"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="sewhee" />

# <a name="log-analytics-for-azure-load-balancer-preview"></a>Análisis de registro de equilibrador de carga de Azure (vista preliminar)

Puede usar diferentes tipos de registros en Azure para administrar y solucionar problemas equilibradores de carga. Algunos de estos registros pueden tener acceso a través del portal. Todos los registros pueden extraídos de un almacenamiento de blobs de Windows Azure y se ven en distintas herramientas, como Excel y PowerBI. Puede obtener más información acerca de los diferentes tipos de registros de la lista siguiente.

- **Registros de auditoría:** Puede usar [Los registros de auditoría de Azure](../../articles/monitoring-and-diagnostics/insights-debugging-with-events.md) (anteriormente conocida como registros operativos) para ver todas las operaciones que se envía a sus suscripciones a Azure y su estado. Registros de auditoría habilitados de forma predeterminada y se pueden visualizar en el portal de Azure.
- **Registros de eventos de alerta:** Puede utilizar este registro para ver qué alertas para equilibrador de carga se producen. El estado de equilibrador de carga se recopila cada cinco minutos. Este registro solo se escribe si se produce un evento de alerta de equilibrador de carga.
- **Registros de sondeo de estado:** Puede utilizar este registro para comprobar el estado de comprobación de estado de sondeo, cuántas instancias están en línea en el equilibrador de carga back-end y el porcentaje de máquinas virtuales recibir tráfico de red desde el equilibrador de carga. Este registro se escribe en el cambio de evento de estado de sondeo.

>[AZURE.IMPORTANT] Iniciar el análisis actualmente sólo funciona para Internet opuestas equilibradores de carga. Registros solo están disponibles para los recursos implementados en el modelo de implementación de administrador de recursos. No puede usar los registros de recursos en el modelo de implementación clásico. Para obtener más información acerca de los modelos de implementación, vea [Descripción de administrador de recursos e implementación clásico](../../articles/resource-manager-deployment-model.md).

## <a name="enable-logging"></a>Habilitar el registro

Registro de auditoría se habilita automáticamente para todos los recursos de administrador de recursos. Debe habilitar el registro de sondeo de estado para iniciar la recopilación de datos disponibles a través de los registros y eventos. Realice los pasos siguientes para habilitar el registro.

Inicie sesión en el [portal de Azure](http://portal.azure.com). Si aún no dispone de un equilibrador de carga, [Cree un equilibrador de carga](load-balancer-get-started-internet-arm-ps.md) antes de continuar.

1. En el portal, haga clic en **Examinar**.
2. Seleccione **equilibradores de carga**.

    ![Portal - equilibrador de carga](./media/load-balancer-monitor-log/load-balancer-browse.png)

3. Seleccione un equilibrador de carga existente >> **Toda la configuración**.
4. En el lado derecho del cuadro de diálogo en el nombre del equilibrador de carga, desplácese hasta la **supervisión**, haga clic en **Diagnósticos**.

    ![Portal - equilibrador de carga](./media/load-balancer-monitor-log/load-balancer-settings.png)

5. **En el panel de **Diagnósticos** , en **estado**, seleccione.**
6. Haga clic en la **cuenta de almacenamiento**.
7. En **registros**, seleccione una cuenta de almacenamiento existente o cree uno nuevo. Use el control deslizante para determinar cuántas dat de evento días se guardarán en los registros de eventos. 8. Haga clic en **Guardar**.

    ![Portal - registros de diagnóstico](./media/load-balancer-monitor-log/load-balancer-diagnostics.png)

>[AZURE.INFORMATION] registros de auditoría, no se requiere una cuenta de almacenamiento independiente. El uso de almacenamiento de evento y la salud de registro de sondeo producirá cargos de servicio.

## <a name="audit-log"></a>Registro de auditoría

El registro de auditoría se genera de forma predeterminada. Los registros se conservan de 90 días en el almacén de registros de eventos de Azure. Más información acerca de estos registros, lea el artículo [Ver eventos y registros de auditoría](../../articles/monitoring-and-diagnostics/insights-debugging-with-events.md) .

## <a name="alert-event-log"></a>Registro de eventos de alerta

Este registro solo se genera si se ha habilitado en una base de equilibrador de carga por. Los eventos se ha iniciado sesión en formato JSON y se almacenan en la cuenta de almacenamiento que especificó cuando habilita el registro. El siguiente es un ejemplo de un evento.

    {
    "time": "2016-01-26T10:37:46.6024215Z",
    "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
    "category": "LoadBalancerAlertEvent",
    "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
    "operationName": "LoadBalancerProbeHealthStatus",
    "properties": {
        "eventName": "Resource Limits Hit",
        "eventDescription": "Ports exhausted",
        "eventProperties": {
            "public ip address": "40.117.227.32"
        }
    }

El resultado JSON muestra la propiedad *eventname* que describa el motivo por el equilibrador de carga creado una alerta. En este caso, la alerta generada fue debido a agotamiento de puerto TCP causado por NAT de IP de origen límites (SNAT).

## <a name="health-probe-log"></a>Registro de sondeo de estado

Este registro solo se genera si se ha habilitado en una carga equilibrador base por como detallada anterior. Los datos se almacenan en la cuenta de almacenamiento que especificó cuando habilita el registro. Se crea un contenedor denominado 'loadbalancerprobehealthstatus de registros de perspectivas' y se registran los datos siguientes:

    {
        "records":
        {
            "time": "2016-01-26T10:37:46.6024215Z",
            "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
            "category": "LoadBalancerProbeHealthStatus",
            "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
            "operationName": "LoadBalancerProbeHealthStatus",
            "properties": {
                "publicIpAddress": "40.83.190.158",
                "port": "81",
                "totalDipCount": 2,
                "dipDownCount": 1,
                "healthPercentage": 50.000000
            }
        },
        {
            "time": "2016-01-26T10:37:46.6024215Z",
            "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
            "category": "LoadBalancerProbeHealthStatus",
            "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
            "operationName": "LoadBalancerProbeHealthStatus",
            "properties": {
                "publicIpAddress": "40.83.190.158",
                "port": "81",
                "totalDipCount": 2,
                "dipDownCount": 0,
                "healthPercentage": 100.000000
            }
        }
    }

El resultado JSON muestra en el campo de propiedades de la información básica para el estado de salud de sondeo. La propiedad *dipDownCount* muestra el número total de instancias en back-end que no están recibiendo tráfico de red debido a las respuestas de error de sondeo.

## <a name="view-and-analyze-the-audit-log"></a>Ver y analizar el registro de auditoría

Puede ver y analizar los datos de registro de auditoría con cualquiera de los métodos siguientes:

- **Herramientas azure:** Recuperar información de los registros de auditoría a través de PowerShell de Azure, la línea de comandos de Azure (CLI), la API de REST de Azure o el portal de vista previa de Azure. En el artículo de [operaciones de auditoría con el Administrador de recursos](../../articles/resource-group-audit.md) se detallan instrucciones paso a paso para cada método.
- **Power BI:** Si no dispone de una cuenta de [Power BI](https://powerbi.microsoft.com/pricing) , puede intentar de forma gratuita. Con el [contenido de los registros de auditoría de Azure pack para Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs), puede analizar los datos con paneles preconfigurados o puede personalizar las vistas para adaptarlos a sus necesidades.

## <a name="view-and-analyze-the-health-probe-and-event-log"></a>Ver y analizar el sondeo de estado y el registro de eventos

Debe conectarse a su cuenta de almacenamiento y recuperar las entradas de registro JSON para los registros de sondeo de estado y eventos. Una vez que descargar los archivos JSON, puede convertirlos en CSV y view en Excel, PowerBI o cualquier otra herramienta de visualización de datos.

>[AZURE.TIP] Si está familiarizado con Visual Studio y conceptos básicos de cambiar los valores de constantes y variables de C#, puede usar las [Herramientas de convertidor de registro](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponibles desde Github.

## <a name="additional-resources"></a>Recursos adicionales

- Entrada de blog de [visualizar los registros de auditoría de Azure con Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) .
- [Ver y analizar los registros de auditoría de Azure en Power BI y mucho más](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) entrada de blog.

## <a name="next-steps"></a>Pasos siguientes

[Conocer sondeos del equilibrador de carga](load-balancer-custom-probe-overview.md)

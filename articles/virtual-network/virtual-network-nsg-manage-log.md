<properties
   pageTitle="Supervisar las operaciones, eventos y contadores para NSGs | Microsoft Azure"
   description="Aprenda a habilitar contadores, eventos y registro de operaciones para NSGs"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/14/2016"
   ms.author="jdial" />

#<a name="log-analytics-for-network-security-groups-nsgs"></a>Análisis de registro de grupos de seguridad de red (NSGs)

Puede usar diferentes tipos de registros en Azure para administrar y solucionar problemas NSGs. Algunos de estos registros se pueden acceder a través del portal y todos los registros pueden extraídos de un almacenamiento de blobs de Windows Azure y se ven en distintas herramientas, como [El análisis de registro](../log-analytics/log-analytics-azure-networking-analytics.md), Excel y PowerBI. Puede obtener más información acerca de los diferentes tipos de registros de la lista siguiente.

- **Registros de auditoría:** Puede usar [Los registros de auditoría de Azure](../monitoring-and-diagnostics/insights-debugging-with-events.md) (anteriormente conocida como registros operativos) para ver todas las operaciones que se envía a sus suscripciones a Azure y su estado. Registros de auditoría habilitados de forma predeterminada y se pueden visualizar en el portal de vista previa de Azure.
- **Registros de eventos:** Puede utilizar este registro para ver qué reglas GSN se aplican a máquinas virtuales y funciones de instancia basadas en dirección MAC. El estado de estas reglas se recopila cada 60 segundos.
- **Registros de contador:** Puede utilizar este registro para ver cuántas veces se aplicó cada regla GSN para denegar o permitir el tráfico.

>[AZURE.WARNING] Registros solo están disponibles para los recursos implementados en el modelo de implementación de administrador de recursos. No puede usar los registros de recursos en el modelo de implementación clásico. Para comprender mejor de los dos modelos, haga referencia al artículo de [Descripción de administrador de recursos e implementación clásico](../resource-manager-deployment-model.md) .

##<a name="enable-logging"></a>Habilitar el registro
Registro de auditoría se habilita automáticamente en todo momento para cada recurso del Administrador de recursos. Debe habilitar el evento y registro para iniciar la recopilación de datos disponibles a través de los registros de contador. Para habilitar el registro, siga los pasos siguientes.

1.  Inicie sesión en el [portal de Azure](https://portal.azure.com). Si aún no dispone de un grupo de seguridad de red existente, [crear una GSN](virtual-networks-create-nsg-arm-ps.md) antes de continuar.

2.  En el portal de vista previa, haga clic en **Examinar** >> **grupos de seguridad de red**.

    ![Portal de vista previa - grupos de seguridad de red](./media/virtual-network-nsg-manage-log/portal-enable1.png)

3. Seleccione un grupo de seguridad de red existente.

    ![Portal de vista previa - configuración del grupo de seguridad de red](./media/virtual-network-nsg-manage-log/portal-enable2.png)

4. En el módulo de **configuración** , haga clic en **Diagnósticos**y, a continuación, en el panel de **Diagnósticos** , junto al **estado**, haga clic **en**
5. En el módulo de **configuración** , haga clic en **Cuenta de almacenamiento**y, a continuación, seleccione un existentes de almacenamiento de la cuenta, o cree uno nuevo.  

>[AZURE.INFORMATION] registros de auditoría, no se requiere una cuenta de almacenamiento independiente. El uso de almacenamiento en el registro de la regla y evento producirá cargos de servicio.

6. En la lista desplegable justo debajo de la **Cuenta de almacenamiento**, seleccione si desea que el registro de eventos, contadores o ambos y, a continuación, haga clic en **Guardar**.

    ![Portal de vista previa - registros de diagnóstico](./media/virtual-network-nsg-manage-log/portal-enable3.png)

## <a name="audit-log"></a>Registro de auditoría
Azure genera este registro (anteriormente conocida como "registro operativa") de forma predeterminada.  Los registros se conservan de 90 días en el almacén de registros de eventos de Azure. Más información acerca de estos registros, lea el artículo [Ver eventos y registros de auditoría](../monitoring-and-diagnostics/insights-debugging-with-events.md) .

## <a name="counter-log"></a>Registro de contador
Este registro solo se genera si se ha habilitado en una base por GSN descrito anteriormente. Los datos se almacenan en la cuenta de almacenamiento que especificó cuando habilita el registro. Cada regla aplicada a los recursos se registra en formato JSON, tal como se muestra a continuación.

    {
        "time": "2015-09-11T23:14:22.6940000Z",
        "systemId": "e22a0996-e5a7-4952-8e28-4357a6e8f0c5",
        "category": "NetworkSecurityGroupRuleCounter",
        "resourceId": "/SUBSCRIPTIONS/D763EE4A-9131-455F-8C5E-876035455EC4/RESOURCEGROUPS/INSIGHTOBONRP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/NSGINSIGHTOBONRP",
        "operationName": "NetworkSecurityGroupCounters",
        "properties": {
            "vnetResourceGuid":"{DD0074B1-4CB3-49FA-BF10-8719DFBA3568}",
            "subnetPrefix":"10.0.0.0/24",
            "macAddress":"001517D9C43C",
            "ruleName":"DenyAllOutBound",
            "direction":"Out",
            "type":"block",
            "matchedConnections":0
            }
    }

## <a name="event-log"></a>Registro de eventos
Este registro solo se genera si se ha habilitado en una base por GSN descrito anteriormente. Los datos se almacenan en la cuenta de almacenamiento que especificó cuando habilita el registro. Se registran los datos siguientes:

    {
        "time": "2015-09-11T23:05:22.6860000Z",
        "systemId": "e22a0996-e5a7-4952-8e28-4357a6e8f0c5",
        "category": "NetworkSecurityGroupEvent",
        "resourceId": "/SUBSCRIPTIONS/D763EE4A-9131-455F-8C5E-876035455EC4/RESOURCEGROUPS/INSIGHTOBONRP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/NSGINSIGHTOBONRP",
        "operationName": "NetworkSecurityGroupEvents",
        "properties": {
            "vnetResourceGuid":"{DD0074B1-4CB3-49FA-BF10-8719DFBA3568}",
            "subnetPrefix":"10.0.0.0/24",
            "macAddress":"001517D9C43C",
            "ruleName":"AllowVnetOutBound",
            "direction":"Out",
            "priority":65000,
            "type":"allow",
            "conditions":{
                "destinationPortRange":"0-65535",
                "sourcePortRange":"0-65535",
                "destinationIP":"10.0.0.0/8,172.16.0.0/12,169.254.0.0/16,192.168.0.0/16,168.63.129.16/32",
                "sourceIP":"10.0.0.0/8,172.16.0.0/12,169.254.0.0/16,192.168.0.0/16,168.63.129.16/32"
            }
        }
    }

## <a name="view-and-analyze-the-audit-log"></a>Ver y analizar el registro de auditoría
Puede ver y analizar los datos de registro de auditoría con cualquiera de los métodos siguientes:

- **Herramientas azure:** Recuperar información de los registros de auditoría a través de PowerShell de Azure, la línea de comandos de Azure (CLI), la API de REST de Azure o el portal de vista previa de Azure.  En el artículo de [operaciones de auditoría con el Administrador de recursos](../resource-group-audit.md) se detallan instrucciones paso a paso para cada método.
- **Power BI:** Si todavía no tiene una cuenta de [Power BI](https://powerbi.microsoft.com/pricing) , puede intentar de forma gratuita. Con el [paquete de Power BI de contenido de los registros de auditoría de Azure](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/) puede analizar sus datos con paneles preconfigurados que puede usar como-está o personalizar.

## <a name="view-and-analyze-the-counter-and-event-log"></a>Ver y analizar el contador y el registro de eventos

[Registro de análisis](../log-analytics/log-analytics-azure-networking-analytics.md) de Azure puede recopilar el contador y archivos desde su cuenta de almacenamiento de blobs de registro de eventos e incluye visualizaciones y capacidades de búsqueda eficaces para analizar los registros.

También puede conectarse a su cuenta de almacenamiento y recuperar las entradas de registro JSON para los registros de eventos y contador. Una vez que descargar los archivos JSON, puede convertirlos en CSV y view en Excel, PowerBI o cualquier otra herramienta de visualización de datos.

>[AZURE.TIP] Si está familiarizado con Visual Studio y conceptos básicos de cambiar los valores de constantes y variables de C#, puede usar las [Herramientas de convertidor de registro](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponibles desde Github.

## <a name="next-steps"></a>Pasos siguientes

- Visualizar contador y registros de eventos con [El análisis de registro](../log-analytics/log-analytics-azure-networking-analytics.md)
- Entrada de blog de [visualizar los registros de auditoría de Azure con Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) .
- [Ver y analizar los registros de auditoría de Azure en Power BI y mucho más](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) entrada de blog.

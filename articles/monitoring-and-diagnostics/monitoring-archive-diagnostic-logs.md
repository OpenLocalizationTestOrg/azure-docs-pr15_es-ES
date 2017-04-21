<properties
    pageTitle="Archivar registros de diagnóstico de Azure | Microsoft Azure"
    description="Obtenga información sobre cómo archivar los registros de diagnóstico de Azure retención a largo plazo en una cuenta de almacenamiento."
    authors="johnkemnetz"
    manager="rboucher"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/26/2016"
    ms.author="johnkem"/>

# <a name="archive-azure-diagnostic-logs"></a>Archivar registros de diagnóstico de Azure
En este artículo, le mostraremos cómo puede usar el portal de Azure, Cmdlets de PowerShell, CLI o API de REST archivar los [Registros de diagnóstico de Azure](monitoring-overview-of-diagnostic-logs.md) en una cuenta de almacenamiento. Esta opción es útil si desea conservar los registros de diagnóstico con una directiva de retención opcional para auditoría, análisis estático o copia de seguridad.

## <a name="prerequisites"></a>Requisitos previos
Antes de empezar, debe [crear una cuenta de almacenamiento](../storage/storage-create-storage-account.md#create-a-storage-account) a la que puede archivar los registros de diagnóstico. Se recomienda no usar una cuenta de almacenamiento existente con otros, sin supervisión de los datos almacenados en él para que mejor puede controlar el acceso a datos de supervisión. Sin embargo, si va a archivar también el registro de actividad y diagnósticos métricas a una cuenta de almacenamiento, podría ser conveniente usar esa cuenta de almacenamiento para los registros de diagnóstico para conservar todos los datos de seguimiento en una ubicación central. La cuenta de almacenamiento que utilice debe ser una cuenta de almacenamiento de propósito general, no es una cuenta de almacenamiento de blobs de Windows.

## <a name="diagnostic-settings"></a>Configuración de diagnóstico
Para archivar los registros de diagnóstico con cualquiera de los siguientes métodos, establezca una **Configuración de diagnóstico** para un recurso determinado. Una configuración de diagnóstico para un recurso define las categorías de registros que están en la que se almacenan o transmiten y los resultados: concentrador de cuenta o evento de almacenamiento. También se define la directiva de retención (número de días que se conservarán) para los eventos de cada categoría de registro que se almacena en una cuenta de almacenamiento. Si una directiva de retención se establece en cero, eventos de esa categoría de registro se almacenan indefinidamente (que es decir, para siempre). Una directiva de retención en caso contrario, puede ser cualquier número de días entre 1 y 2147483647. [Puede obtener más información sobre la configuración de diagnóstico aquí](monitoring-overview-of-diagnostic-logs.md#diagnostic-settings).

## <a name="archive-diagnostic-logs-using-the-portal"></a>Registros de diagnóstico de archivo con el portal

1. En el portal, haga clic en la hoja de recursos para el recurso en el que desea habilitar archivado de registros de diagnóstico.
2. En la sección **supervisión** del menú de configuración de recursos, seleccione **diagnóstico**.

    ![Sección del menú de recursos de supervisión](media/monitoring-archive-diagnostic-logs/diag-log-monitoring-sec.png)
3. Active la casilla para **Exportar a la cuenta de almacenamiento**, seleccione una cuenta de almacenamiento. Opcionalmente, establecer un número de días que desea conservar estos registros mediante la **retención (días)** controles deslizantes. Retención de cero días almacena los registros de forma indefinida.

    ![Módulo de registros de diagnóstico](media/monitoring-archive-diagnostic-logs/diag-log-monitoring-blade.png)
4. Haga clic en **Guardar**.

Registros de diagnóstico se archivan a esa cuenta de almacenamiento tan pronto como se generarán nuevos datos de evento.

## <a name="archive-diagnostic-logs-via-the-powershell-cmdlets"></a>Archivar registros de diagnóstico a través de los PowerShell Cmdlets

```
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg -StorageAccountId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Categories networksecuritygroupevent,networksecuritygrouprulecounter -Enabled $true -RetentionEnabled $true -RetentionInDays 90
```

| (Propiedad)         | Obligatorio | Descripción                                                                                           |
|------------------|----------|-------------------------------------------------------------------------------------------------------|
| ResourceId       | Sí      | Id. de recurso del recurso en el que desea establecer una configuración de diagnóstico.                            |
| StorageAccountId | No       | Id. de recurso de la cuenta de almacenamiento a la que se debe guardar registros de diagnóstico.                          |
| Categorías       | No       | Lista de valores separados por comas de las categorías de registro para habilitar.                                                     |
| Habilitado          | Sí      | Valor Boolean que indica si diagnósticos están habilitados o deshabilitados en este recurso.                  |
| RetentionEnabled | No       | Valor Boolean que indica si una directiva de retención están habilitados en este recurso.                            |
| RetentionInDays  | No       | Número de días que deben conservarse eventos entre 1 y 2147483647. Un valor de cero almacena los registros de forma indefinida. |

## <a name="archive-the-activity-log-via-the-cross-platform-cli"></a>Archivar el registro de actividad a través de varias plataformas CLI

```
azure insights diagnostic set --resourceId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg --storageId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage –categories networksecuritygroupevent,networksecuritygrouprulecounter --enabled true
```

| (Propiedad)         | Obligatorio | Descripción                                                                                           |
|------------------|----------|-------------------------------------------------------------------------------------------------------|
| resourceId       | Sí      | Id. de recurso del recurso en el que desea establecer una configuración de diagnóstico.                            |
| storageId        | No       | Id. de recurso de la cuenta de almacenamiento a la que se debe guardar registros de diagnóstico.                          |
| categorías       | No       | Lista de valores separados por comas de las categorías de registro para habilitar.                                                     |
| habilitado          | Sí      | Valor Boolean que indica si diagnósticos están habilitados o deshabilitados en este recurso.                  |

## <a name="archive-diagnostic-logs-via-the-rest-api"></a>Archivar registros de diagnóstico a través de la API de REST
Para obtener información sobre cómo configurar una configuración de diagnóstico con la API de REST de Azure Monitor, [consulte este documento](https://msdn.microsoft.com/library/azure/dn931931.aspx) .

## <a name="schema-of-diagnostic-logs-in-the-storage-account"></a>Esquema de registros de diagnóstico en la cuenta de almacenamiento
Una vez que haya configurado archivado, se crea un contenedor de almacenamiento en la cuenta de almacenamiento tan pronto como se produce un evento en una de las categorías de registro que se ha habilitado. Los BLOB dentro del contenedor siguen el mismo formato en registros de diagnóstico y el registro de actividad. La estructura de estos BLOB es:

> perspectivas - registros-{nombre de categoría de registro} / resourceId = / suscripciones / {Id. de suscripción} /RESOURCEGROUPS/ {nombre de grupo de recursos} /PROVIDERS/ {nombre del proveedor de recursos} / {tipo de recurso} / {nombre del recurso} / y = {año de cuatro dígitos numérico} / m = {mes en dos dígitos numéricos} / d = {dos dígitos numérico día} / h = {reloj de 24 horas dos dígitos hour}/m=00/PT1H.json

O bien, sencillamente,

> perspectivas - registros-{nombre de categoría de registro} / resourceId = / {Id. de recurso} / y = {año de cuatro dígitos numérico} / m = {mes en dos dígitos numéricos} / d = {dos dígitos numérico día} / h = {reloj de 24 horas dos dígitos hour}/m=00/PT1H.json

Por ejemplo, podría ser un nombre de blobs de Windows:

> insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json

Cada blob PT1H.json contiene un blob JSON de eventos que ocurrieron durante la hora especificada en la dirección URL de blobs (por ejemplo, h = 12). Durante la hora actual, los eventos se anexan al archivo PT1H.json según se van produciendo. El valor de minuto (m = 00) es siempre 00, ya que los eventos de registro de diagnóstico se dividen en BLOB individual por hora.

En el archivo PT1H.json, cada evento se almacena en la matriz "registros", siguiendo este formato:

```
{
    "records": [
        {
            "time": "2016-07-01T00:00:37.2040000Z",
            "systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1",
            "category": "NetworkSecurityGroupRuleCounter",
            "resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG",
            "operationName": "NetworkSecurityGroupCounters",
            "properties": {
                "vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}",
                "subnetPrefix": "10.3.0.0/24",
                "macAddress": "000123456789",
                "ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp",
                "direction": "In",
                "type": "allow",
                "matchedConnections": 1988
            }
        }
    ]
}
```

| Nombre del elemento  | Descripción                                                                                                 |
|---------------|-------------------------------------------------------------------------------------------------------------|
| hora          | Marca de tiempo cuando el evento se genera por el servicio de Azure procesamiento de la solicitud correspondiente al evento. |
| resourceId    | Identificador de recursos de los recursos afectados.                                                                       |
| operationName | Nombre de la operación.                                                                                      |
| categoría      | Categoría de registro del evento.                                                                                  |
| propiedades    | Conjunto de `<Key, Value>` pares (es decir, diccionario) que describe los detalles del evento.                            |

> [AZURE.NOTE] Las propiedades y el uso de las propiedades pueden variar según el recurso.

## <a name="next-steps"></a>Pasos siguientes
- [Descargar BLOB para análisis](../storage/storage-dotnet-how-to-use-blobs.md#download-blobs)
- [Secuencia de registros de diagnóstico para Hubs de evento](monitoring-stream-diagnostic-logs-to-event-hubs.md)
- [Obtenga más información acerca de los registros de diagnóstico](monitoring-overview-of-diagnostic-logs.md)

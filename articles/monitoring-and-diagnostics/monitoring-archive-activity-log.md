<properties
    pageTitle="Archivar el registro de actividad de Azure | Microsoft Azure"
    description="Obtenga información sobre cómo archivar el registro de actividad de Azure retención a largo plazo en una cuenta de almacenamiento."
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
    ms.date="08/23/2016"
    ms.author="johnkem"/>

# <a name="archive-the-azure-activity-log"></a>Archivar el registro de actividad de Azure
En este artículo, le mostraremos cómo puede usar el portal de Azure, Cmdlets de PowerShell, o entre plataformas CLI para archivar el [**Registro de actividad de Azure**](monitoring-overview-activity-logs.md) en una cuenta de almacenamiento. Esta opción es útil si desea conservar el registro de actividad de más de 90 días (con control total sobre la directiva de retención) para auditoría, análisis estático o copia de seguridad. Si desea conservar sus eventos de 90 días o menos no es necesario configurar el archivado a una cuenta de almacenamiento, ya que los eventos de registro de actividad se conservan en la plataforma de Windows Azure para 90 días sin habilitar archivado.

## <a name="prerequisites"></a>Requisitos previos
Antes de empezar, debe [crear una cuenta de almacenamiento](../storage/storage-create-storage-account.md#create-a-storage-account) a la que puede comprimir el registro de actividad. Se recomienda no usar una cuenta de almacenamiento existente con otros, sin supervisión de los datos almacenados en él para que mejor puede controlar el acceso a datos de supervisión. Sin embargo, si también se archivan registros de diagnóstico y mediciones a una cuenta de almacenamiento, podría ser conveniente usar esa cuenta de almacenamiento para el registro de actividad también debe para tener todos los datos de seguimiento en una ubicación central. La cuenta de almacenamiento que utilice debe ser una cuenta de almacenamiento de propósito general, no es una cuenta de almacenamiento de blobs de Windows.

## <a name="log-profile"></a>Perfil de registro
Para archivar el registro de actividad con cualquiera de los métodos siguientes, establezca el **Perfil de registro** para una suscripción. El perfil de registro define el tipo de eventos que se almacenan o transmiten y los resultados: concentrador de cuenta o evento de almacenamiento. También se define la directiva de retención (número de días que se conservarán) para los eventos que se almacenan en una cuenta de almacenamiento. Si la directiva de retención se establece en cero, los eventos se almacenan indefinidamente. En caso contrario, se puede establecer en cualquier valor entre 1 y 2147483647. [Puede obtener más información sobre el registro de perfiles aquí](monitoring-overview-activity-logs.md#export-the-activity-log-with-log-profiles).

## <a name="archive-the-activity-log-using-the-portal"></a>Archivar el registro de actividad con el portal
1. En el portal, haga clic en el vínculo de **Registro de actividad** en el panel de navegación izquierda. Si no ve un vínculo para el registro de actividad, haga clic en el vínculo **Más servicios** en primer lugar.

    ![Vaya a módulo de registro de actividad](media/monitoring-archive-activity-log/act-log-portal-navigate.png)
2. En la parte superior de la hoja, haga clic en **Exportar**.

    ![Haga clic en el botón Exportar](media/monitoring-archive-activity-log/act-log-portal-export-button.png)
3. En el módulo que aparece, active la casilla para **Exportar a una cuenta de almacenamiento** y seleccione una cuenta de almacenamiento.

    ![Configurar una cuenta de almacenamiento](media/monitoring-archive-activity-log/act-log-portal-export-blade.png)
4. Mediante el control deslizante o el cuadro de texto, defina un número de días que se mantendrán eventos de registro de actividad de su cuenta de almacenamiento. Si prefiere que los datos se conservan en la cuenta de almacenamiento indefinidamente, establezca este número en cero.
5. Haga clic en **Guardar**.

## <a name="archive-the-activity-log-via-powershell"></a>Archivar el registro de actividad a través de PowerShell
```
Add-AzureRmLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus -RetentionInDays 180 -Categories Write,Delete,Action
```

| (Propiedad)         | Obligatorio | Descripción                                                                                                                                                                                                                                                                                       |
|------------------|----------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| StorageAccountId | No       | Id. de recurso de la cuenta de almacenamiento a la que se debe guardar registros de actividad.                                                                                                                                                                                                                        |
| Ubicaciones        | Sí      | Valores separados por comas a la lista de regiones que le gustaría recopilar eventos de registro de actividad. Puede ver una lista de todas las regiones [, visite esta página](https://azure.microsoft.com/en-us/regions) o mediante [La API de REST de administración de Azure](https://msdn.microsoft.com/library/azure/gg441293.aspx). |
| RetentionInDays  | Sí      | Número de días para los eventos que deben conservarse, entre 1 y 2147483647. Un valor de cero almacena los registros de forma indefinida (siempre).                                                                                                                                                                                             |
| Categorías       | Sí      | Lista de valores separados por comas de categorías de eventos que se deben recopilar. Valores posibles son escribir, eliminar y acción.                                                                                                                                                                                 |
## <a name="archive-the-activity-log-via-cli"></a>Archivar el registro de actividad a través de CLI
```
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --locations global,westus,eastus,northeurope --retentionInDays 180 –categories Write,Delete,Action
```

| (Propiedad)        | Obligatorio | Descripción                                                                                                                                                                                                                                                                                       |
|-----------------|----------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| nombre            | Sí      | Nombre de su perfil de registro.                                                                                                                                                                                                                                                                         |
| storageId       | No       | Id. de recurso de la cuenta de almacenamiento a la que se debe guardar registros de actividad.                                                                                                                                                                                                                        |
| ubicaciones       | Sí      | Valores separados por comas a la lista de regiones que le gustaría recopilar eventos de registro de actividad. Puede ver una lista de todas las regiones [, visite esta página](https://azure.microsoft.com/en-us/regions) o mediante [La API de REST de administración de Azure](https://msdn.microsoft.com/library/azure/gg441293.aspx). |
| retentionInDays | Sí      | Número de días para los eventos que deben conservarse, entre 1 y 2147483647. Un valor de cero almacenará los registros de forma indefinida (siempre).                                                                                                                                                                                             |
| categorías      | Sí      | Lista de valores separados por comas de categorías de eventos que se deben recopilar. Valores posibles son escribir, eliminar y acción.                                                                                                                                                                                 |

## <a name="storage-schema-of-the-activity-log"></a>Esquema de almacenamiento del registro de actividad
Una vez que haya configurado archivado, se creará un contenedor de almacenamiento en la cuenta de almacenamiento tan pronto como se produce un evento de registro de actividad. Los BLOB dentro del contenedor siguen el mismo formato en el registro de actividad y los registros de diagnóstico. La estructura de estos BLOB es:

> perspectivas operativas registros/nombre de = predeterminado o resourceId = / suscripciones / {Id. de suscripción} / y = {año de cuatro dígitos numérico} / m = {mes en dos dígitos numéricos} / d = {dos dígitos numérico día} / h = {reloj de 24 horas dos dígitos hour}/m=00/PT1H.json

Por ejemplo, podría ser un nombre de blobs de Windows:

> Insights-Operational-logs/Name=default/ResourceID=/Subscriptions/s1id1234-5679-0123-4567-890123456789/y=2016/m=08/d=22/h=18/m=00/PT1H.JSON

Cada blob PT1H.json contiene un blob JSON de eventos que ocurrieron durante la hora especificada en la dirección URL de blobs (por ejemplo, h = 12). Durante la hora actual, los eventos se anexan al archivo PT1H.json según se van produciendo. El valor de minuto (m = 00) es siempre 00, ya que los eventos de registro de actividad se dividen en BLOB individual por hora.

En el archivo PT1H.json, cada evento se almacena en la matriz "registros", siguiendo este formato:

```
{
    "records": [
        {
            "time": "2015-01-21T22:14:26.9792776Z",
            "resourceId": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
            "operationName": "microsoft.support/supporttickets/write",
            "category": "Write",
            "resultType": "Success",
            "resultSignature": "Succeeded.Created",
            "durationMs": 2826,
            "callerIpAddress": "111.111.111.11",
            "correlationId": "c776f9f4-36e5-4e0e-809b-c9b3c3fb62a8",
            "identity": {
                "authorization": {
                    "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
                    "action": "microsoft.support/supporttickets/write",
                    "evidence": {
                        "role": "Subscription Admin"
                    }
                },
                "claims": {
                    "aud": "https://management.core.windows.net/",
                    "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
                    "iat": "1421876371",
                    "nbf": "1421876371",
                    "exp": "1421880271",
                    "ver": "1.0",
                    "http://schemas.microsoft.com/identity/claims/tenantid": "1e8d8218-c5e7-4578-9acc-9abbd5d23315 ",
                    "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
                    "puid": "20030000801A118C",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
                    "name": "John Smith",
                    "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
                    "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
                    "appidacr": "2",
                    "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
                    "http://schemas.microsoft.com/claims/authnclassreference": "1"
                }
            },
            "level": "Information",
            "location": "global",
            "properties": {
                "statusCode": "Created",
                "serviceRequestId": "50d5cddb-8ca0-47ad-9b80-6cde2207f97c"
            }
        }
    ]
}
```


| Nombre del elemento    | Descripción                                                                                                    |
|-----------------|----------------------------------------------------------------------------------------------------------------|
| hora            | Marca de tiempo cuando el evento se genera por el servicio de Azure procesamiento de la solicitud correspondiente al evento.    |
| resourceId      | Identificador de recursos de los recursos afectados.                                                                          |
| operationName   | Nombre de la operación.                                                                                         |
| categoría        | Categoría de la acción, por ejemplo. Escritura, lectura, en acción.                                                               |
| resultType      | El tipo de resultado, por ejemplo. Inicio de éxito, error,                                                            |
| resultSignature | Depende del tipo de recurso.                                                                                  |
| durationMs      | Duración de la operación en milisegundos.                                                                      |
| callerIpAddress | Dirección IP del usuario que ha realizado la operación, notificación de UPN o reclamo SPN de disponibilidad.         |
| correlationId   | Normalmente un GUID con el formato de cadena. Los eventos que comparten un correlationId pertenecen a la misma acción ejercicios.         |
| identidad        | Blob JSON que describa la autorización y notificaciones.                                                             |
| autorización   | BLOB de propiedades RBAC del evento. Normalmente incluye las propiedades de "acción", "rol" y "ámbito".            |
| nivel           | Nivel del evento. Uno de los valores siguientes: "Crítica", "Error", "Advertencia", "Informativo" y "Detallado" |
| ubicación        | Región en la que se ha producido la ubicación (o global).                                                             |
| propiedades      | Conjunto de `<Key, Value>` pares (es decir, diccionario) que describe los detalles del evento.                             |

> [AZURE.NOTE] Las propiedades y el uso de las propiedades pueden variar según el recurso.

## <a name="next-steps"></a>Pasos siguientes
- [Descargar BLOB para análisis](../storage/storage-dotnet-how-to-use-blobs.md#download-blobs)
- [El registro de actividad al evento Hubs de secuencia](monitoring-stream-activity-logs-event-hubs.md)
- [Obtenga más información sobre el registro de actividad](monitoring-overview-activity-logs.md)

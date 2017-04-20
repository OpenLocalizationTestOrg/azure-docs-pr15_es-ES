<properties
    pageTitle="Usar el resto de opciones para realizar copias de seguridad y restaurar aplicaciones de servicio de aplicaciones"
    description="Obtenga información sobre cómo utilizar llamadas API de REST para realizar copias de seguridad y restauración de una aplicación de servicio de la aplicación de Azure"
    services="app-service"
    documentationCenter=""
    authors="NKing92"
    manager="wpickett"
    editor="" />

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="nicking"/>
# <a name="use-rest-to-back-up-and-restore-app-service-apps"></a>Usar el resto de opciones para realizar copias de seguridad y restaurar aplicaciones de servicio de aplicaciones

> [AZURE.SELECTOR]
- [PowerShell](../app-service/app-service-powershell-backup.md)
- [API DE REST](websites-csm-backup.md)

Puede ser una copia de seguridad de [aplicaciones de servicio de aplicaciones](https://azure.microsoft.com/services/app-service/web/) como BLOB en el almacenamiento de Azure. La copia de seguridad también puede contener las bases de datos de la aplicación. Si la aplicación se elimina accidentalmente alguna vez, o si la aplicación debe revertir a una versión anterior, se puede restaurar desde cualquier copia de seguridad anterior. Se puede hacer copias de seguridad en cualquier momento a petición o realizar copias de seguridad se pueden programar a intervalos apropiados.

En este artículo se explica cómo hacer copia de seguridad y restauración de una aplicación con la API de REST solicitudes. Si desea crear y administrar las copias de seguridad de aplicación gráficamente a través del portal de Azure, consulte [realizar copias de seguridad de una aplicación web en la aplicación de servicio de Azure](web-sites-backup.md)

<a name="gettingstarted"></a>
## <a name="getting-started"></a>Introducción
Para enviar solicitudes REST, debe saber el **nombre**de la aplicación, **grupo de recursos**e **Id. de suscripción**. Esta información se puede encontrar haciendo clic en la aplicación en el módulo de **Aplicación de servicio** del [portal de Azure](https://portal.azure.com). Para los ejemplos de este artículo, nos estamos configurando el sitio Web **backuprestoreapiexamples.azurewebsites.net**. Se almacena en el grupo de recursos de WestUS de Web de forma predeterminada y se ejecuta en una suscripción con el identificador 00001111-2222-3333-4444-555566667777.

![Información del sitio Web de ejemplo][SampleWebsiteInformation]

<a name="backup-restore-rest-api"></a>
## <a name="backup-and-restore-rest-api"></a>Hacer copia de seguridad y restaurar la API de REST
Ahora trataremos varios ejemplos de cómo usar la API de REST hacer copia de seguridad y restauración de una aplicación. Cada ejemplo incluye una dirección URL y HTTP cuerpo de la solicitud. La dirección URL de ejemplo contiene marcadores de posición ajustados entre llaves, como {Id. de suscripción}. Reemplace los marcadores de posición por la información correspondiente a la aplicación. Para referencia, aquí es una explicación de cada marcador de posición que aparece en las direcciones URL de ejemplo.

* identificador de suscripción: identificador de la suscripción de Azure que contiene la aplicación
* recurso de nombre de grupo: nombre del grupo de recursos que contiene la aplicación
* nombre: nombre de la aplicación
* identificador de copia de seguridad: el identificador de la copia de seguridad de la aplicación

Para obtener la documentación completa de la API, incluidos varios parámetros opcionales que se pueden incluir en la solicitud HTTP, consulte el [Explorador de recursos de Azure](https://resources.azure.com/).

<a name="backup-on-demand"></a>
## <a name="backup-an-app-on-demand"></a>Copia de seguridad de una aplicación a petición
Para hacer una copia una aplicación inmediatamente, envíe una solicitud de **publicación** para **https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backup/**.

Aquí es lo que la dirección URL es similar a usar nuestro ejemplo de sitio Web. **https://Management.Azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/Providers/Microsoft.Web/Sites/backuprestoreapiexamples/backup/**

Proporcionar un objeto JSON en el cuerpo de la solicitud para especificar la cuenta de almacenamiento que desea usar para almacenar la copia de seguridad. El objeto JSON debe tener una propiedad denominada **storageAccountUrl**, que contiene una [Dirección URL de SA](../storage/storage-dotnet-shared-access-signature-part-1.md) conceder acceso de escritura en el contenedor de almacenamiento de Azure que almacena el blob de copia de seguridad. Si desea hacer copia de seguridad de las bases de datos, también debe proporcionar una lista que contiene los nombres, tipos y cadenas de conexión de la copia de seguridad de las bases de datos.

```
{
    "properties":
    {
        "storageAccountUrl": “https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl”,
        “databases”: [
            {
                “databaseType”: “SqlAzure”,
                “name”: “MyDatabase1”,
                "connectionString": "<your database connection string>"
            }
        ]
    }
}
```

Una copia de seguridad de la aplicación se inicia inmediatamente cuando se recibe la solicitud. El proceso de copia de seguridad puede tardar mucho tiempo en completarse. La respuesta HTTP contiene un ID que puede usar en otra solicitud para ver el estado de la copia de seguridad. Aquí es un ejemplo del cuerpo de la respuesta HTTP a nuestra solicitud de copia de seguridad.

```
{
    "id": "/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples",
    "name": " backuprestoreapiexamples ",
    "type": "Microsoft.Web/sites",
    "location": "WestUS",
    "properties":    {
        "id": 1,
        "storageAccountUrl": “https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl”,
        "blobName": “backup_201509291825.zip”,
        "name": "backup_201509291825",
        "status": 4,
        "sizeInBytes": 0,
        "created": "2015-09-29T18:25:26.3992707Z",
        "log": null,
        "databases": [
            {
                "databaseType": "SqlAzure",
                "name": "MyDatabase1",
                "connectionString": "<your database connection string>"
            }
        ],
        "scheduled": false,
        "correlationId": "ea730f4d-dd06-4f7f-a090-9aa09k662h36",
    }
}
```

>[AZURE.NOTE] Mensajes de error se pueden encontrar en la propiedad de registro de la respuesta HTTP.

<a name="schedule-automatic-backups"></a>
## <a name="schedule-automatic-backups"></a>Programar copias de seguridad automáticas
Además de la copia de una aplicación a petición, también puede programar una copia de seguridad realiza de forma automática.

### <a name="set-up-a-new-automatic-backup-schedule"></a>Configurar una nueva programación de copia de seguridad automática
Para configurar una programación de copia de seguridad, enviar una solicitud de **poner** a **https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/config/backup**.

Aquí es el aspecto de la dirección URL para el sitio Web de ejemplo. **https://Management.Azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/Providers/Microsoft.Web/Sites/backuprestoreapiexamples/config/backup**

El cuerpo de la solicitud debe tener un objeto JSON que especifica la configuración de copia de seguridad. Aquí tenemos un ejemplo con todos los parámetros necesarios.

```
{
    "location": "WestUS",
    "properties": // Represents an app restore request
    {
        "backupSchedule": { // Required for automatically scheduled backups
            "frequencyInterval": "7",
            "frequencyUnit": "Day",
            "keepAtLeastOneBackup": "True",
            "retentionPeriodInDays": "10",
        },
        "enabled": "True", // Must be set to true to enable automatic backups
        "name": “mysitebackup”,
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl"
    }
}
```

En este ejemplo se configura la aplicación para que se copia automáticamente cada siete días. Los parámetros **frequencyInterval** y **frequencyUnit** juntos determinan con qué frecuencia se producen las copias de seguridad. Valores válidos para **frequencyUnit** son la **hora** y el **día**. Por ejemplo, para hacer una copia una aplicación cada 12 horas, establezca frequencyInterval en 12 y frequencyUnit a la hora.

Copias de seguridad antiguas se eliminan automáticamente de la cuenta de almacenamiento. Puede controlar la antigüedad las copias de seguridad pueden ser estableciendo el parámetro **retentionPeriodInDays** . Si desea tener siempre al menos una copia de seguridad guardado, independientemente de la antigüedad, establecerlo **keepAtLeastOneBackup** en true.

### <a name="get-the-automatic-backup-schedule"></a>Obtener la programación de copia de seguridad automática
Para obtener la configuración de copia de seguridad de la aplicación, enviar una solicitud de **entrada** a la dirección URL **https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/config/backup/list**.

La dirección URL de nuestro sitio de ejemplo es **https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/config/backup/list**.

<a name="get-backup-status"></a>
## <a name="get-the-status-of-a-backup"></a>Obtener el estado de una copia de seguridad
Dependiendo de cómo grande la aplicación, una copia de seguridad puede tardar varios minutos en completarse. Copias de seguridad podrían también un error, el tiempo de espera, o parcialmente correctamente. Para ver el estado de las copias de seguridad de todos los de las aplicaciones, enviar una solicitud **GET** a la dirección URL **https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backups**.

Para ver el estado de una copia de seguridad específico, envíe una solicitud GET a la dirección URL **https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backups/{backup-id}**.

Aquí es el aspecto de la dirección URL para el sitio Web de ejemplo. **https://Management.Azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/Providers/Microsoft.Web/Sites/backuprestoreapiexamples/backups/1**

El cuerpo de la respuesta contiene un objeto JSON similar a este ejemplo.

```
{
    "properties":  {
        "id": 1,
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl",
        "blobName": "backup_201509291734.zip",
        "name": "backup_201509291734",
        "status": 2,
        "sizeInBytes": 151973,
        "created": "2015-09-29T17:34:57.2091605",
        "scheduled": false,
        "lastRestoreTimeStamp": null,
        "finishedTimeStamp": "2015-09-29T17:35:11.3293602",
        "correlationId": "2379fc13-418a-4b9c-920d-d06e73c5028d",
        "websiteSizeInBytes": 209920
    }
}
```

El estado de una copia de seguridad es un tipo enumerado. Aquí es posible cada estado.

* En curso 0 –: la copia de seguridad se ha iniciado, pero aún no ha completado.
* 1: error: La copia de seguridad tuvo éxito.
* 2-correcta: La copia de seguridad que se completó correctamente.
* 3-tiempo de espera agotado: La copia de seguridad no se completó en el tiempo y se ha cancelado.
* 4: creado: La solicitud de copia de seguridad en cola pero no se ha iniciado.
* 5-omitidos: No continuar la copia de seguridad debido a una programación desencadenante demasiadas copias de seguridad.
* 6-PartiallySucceeded: La copia de seguridad se realizó correctamente, pero algunos archivos no se copian porque no se puede leer. Normalmente, esto ocurre porque se coloca un bloqueo exclusivo en los archivos.
* 7-DeleteInProgress: La copia de seguridad se ha solicitado se eliminarán, pero aún no se ha eliminado.
* 8: DeleteFailed: No se pudo eliminar la copia de seguridad. Esto puede suceder porque ha caducado la dirección URL de SA que se usó para crear la copia de seguridad.
* 9 – eliminados: La copia de seguridad se ha eliminado correctamente.

<a name="restore-app"></a>
## <a name="restore-an-app-from-a-backup"></a>Restaurar una aplicación desde una copia de seguridad
Si se ha eliminado la aplicación, o si desea volver a la aplicación a una versión anterior, puede restaurar la aplicación desde una copia de seguridad. Para llamar a una restauración, enviar una solicitud de **entrada** a la dirección URL **https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backups/{backup-id}/restore**.

Aquí es el aspecto de la dirección URL para el sitio Web de ejemplo. **https://Management.Azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/Providers/Microsoft.Web/Sites/backuprestoreapiexamples/backups/1/restore**

En el cuerpo de la solicitud, enviar un objeto JSON que contiene las propiedades de la operación de restauración. Aquí tenemos un ejemplo que contiene todas las propiedades necesarias:

```
{
    "location": "WestUS",
    "properties":
    {
        "blobName": "backup_201509280431.zip",
        "databases": [ // Not required unless you’re restoring databases
            {
                “databaseType”: “SqlAzure”,
                “name”: “MyDatabase1”
        }],
        "overwrite": "true",
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl" // SAS URL to storage container containing your website backup
    }
}
```

### <a name="restore-to-a-new-app"></a>Restaurar a una nueva aplicación
A veces que desea crear una nueva aplicación cuando se restaura una copia de seguridad, en vez de sobrescribirlos una aplicación existente. Para ello, cambie la dirección URL de solicitud para que apunten a la nueva aplicación que desea crear y cambiar la propiedad **Sobrescribir** el JSON como **Falso**.

<a name="delete-app-backup"></a>
## <a name="delete-an-app-backup"></a>Eliminar una copia de seguridad de la aplicación
Si desea eliminar una copia de seguridad, enviar una solicitud de **Eliminar** a la dirección URL **https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backups/{backup-id}**.

Aquí es el aspecto de la dirección URL para el sitio Web de ejemplo. **https://Management.Azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/Providers/Microsoft.Web/Sites/backuprestoreapiexamples/backups/1**

<a name="manage-sas-url"></a>
## <a name="manage-a-backups-sas-url"></a>Administrar la dirección URL de asociaciones de seguridad de una copia de seguridad
Servicio de aplicación de Azure intentará eliminar la copia de seguridad del almacenamiento de Azure con la URL de SA que se proporcionó cuando se creó la copia de seguridad. Si esta dirección URL de SA ya no es válida, no se puede eliminar la copia de seguridad a través de la API de REST. Sin embargo, puede actualizar la dirección URL de SA asociada a una copia de seguridad al enviar una solicitud de **entrada** a la dirección URL **https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backups/{backup-id}/list**.

Aquí es el aspecto de la dirección URL para el sitio Web de ejemplo. **https://Management.Azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/Providers/Microsoft.Web/Sites/backuprestoreapiexamples/backups/1/List**

En el cuerpo de la solicitud, enviar un objeto JSON que contiene la dirección URL de SA nuevo. Aquí tenemos un ejemplo.

```
{
    "properties":
    {
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl"
    }
}
```

>[AZURE.NOTE] Por motivos de seguridad, no se devuelve la dirección URL de SA asociada a una copia de seguridad al enviar una solicitud GET para una copia de seguridad específico. Si desea ver la dirección URL de SA asociada a una copia de seguridad, enviar una solicitud de entrada a la misma dirección URL anterior. Incluir un objeto JSON vacío en el cuerpo de la solicitud. La respuesta del servidor contiene todos los datos de la copia, incluida su dirección URL de SA.

<!-- IMAGES -->
[SampleWebsiteInformation]: ./media/websites-csm-backup/01siteconfig.png

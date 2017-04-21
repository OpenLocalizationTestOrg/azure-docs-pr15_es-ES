<properties
 pageTitle="Importar Exportar identidades de dispositivo concentrador IoT | Microsoft Azure"
 description="Conceptos y .NET fragmentos para la administración de masa de identidades de dispositivo IoT concentrador de código"
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/05/2016"
 ms.author="dobett"/>

# <a name="bulk-management-of-iot-hub-device-identities"></a>Administración de identidades de dispositivo concentrador IoT en masa

Cada concentrador IoT tiene un registro de la identidad de dispositivo que puede usar para crear recursos por dispositivo en el servicio, por ejemplo, una cola que contiene los mensajes de dispositivo de nube en curso. El registro de la identidad de dispositivo también le permite controlar el acceso a los extremos de la orientación del dispositivo. En este artículo se describe cómo importar y exportar identidades de dispositivo de forma masiva desde un registro de la identidad de dispositivo.

Importar y exportar se llevan a cabo operaciones en el contexto de las *tareas* que le permiten ejecutar las operaciones de servicio de masa frente a un concentrador IoT.

La clase **RegistryManager** incluye los métodos de **ImportDevicesAsync** que utilizan el marco de **trabajo** y **ExportDevicesAsync** . Estos métodos le permiten exportar, importar y sincronizar la totalidad de un registro de dispositivo IoT concentrador.

## <a name="what-are-jobs"></a>¿Qué son los trabajos?

Las operaciones de registro de identidad de dispositivo usan el sistema de **trabajo** cuando la operación:

*  Tiene potencialmente mucho tiempo de ejecución en comparación con las operaciones de tiempo de ejecución estándar, o
*  Devuelve una gran cantidad de datos para el usuario.

En estos casos, en lugar de una sola llamada API en espera o bloqueos en el resultado de la operación, la operación asincrónica crea un **trabajo** de ese concentrador IoT. A continuación, la operación inmediatamente devuelve un objeto **JobProperties** .

Fragmento de código de C# siguiente muestra cómo crear un trabajo de exportación:

```
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await registryManager.ExportDevicesAsync(containerSasUri, false);
```

A continuación, puede usar la clase **RegistryManager** para consultar el estado del **trabajo** con los metadatos de **JobProperties** devueltos.

Fragmento de código de C# siguiente muestra cómo sondear cada cinco segundos para ver si ha terminado de ejecutar el trabajo:

```
// Wait until job is finished
while(true)
{
  exportJob = await registryManager.GetJobAsync(exportJob.JobId);
  if (exportJob.Status == JobStatus.Completed || 
      exportJob.Status == JobStatus.Failed ||
      exportJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="export-devices"></a>Exportar dispositivos

Use el método **ExportDevicesAsync** para exportar la totalidad de un registro de dispositivo de concentrador IoT a un contenedor de blobs de Windows [Azure almacenamiento](https://azure.microsoft.com/documentation/services/storage/) con una [Firma de acceso compartido](https://msdn.microsoft.com/library/ee395415.aspx).

Este método permite crear copias de seguridad confiables de información de su dispositivo en un contenedor de blob que puede controlar.

El método **ExportDevicesAsync** requiere dos parámetros:

*  Una *cadena* que contiene un URI de un contenedor de blob. Este URI debe contener un símbolo de SA que conceda acceso de escritura en el contenedor. El trabajo crea un blob de bloque en este contenedor para almacenar los datos del dispositivo de exportación de número de serie. El token de sa debe incluir los permisos siguientes:
    
    ```
    SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Delete
    ```

*  *Valor boolean* que indica si desea excluir claves de autenticación de los datos de exportación. Si el resultado **Falso**, autenticación de claves se incluyen en Exportar; en caso contrario, las claves se exportan como **null**.

Fragmento de código de C# siguiente muestra cómo iniciar un trabajo de exportación que incluye claves de autenticación de dispositivo en los datos de exportación y, a continuación, sondear la finalización:

```
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await registryManager.ExportDevicesAsync(containerSasUri, false);

// Wait until job is finished
while(true)
{
    exportJob = await registryManager.GetJobAsync(exportJob.JobId);
    if (exportJob.Status == JobStatus.Completed || 
        exportJob.Status == JobStatus.Failed ||
        exportJob.Status == JobStatus.Cancelled)
    {
    // Job has finished executing
    break;
    }

    await Task.Delay(TimeSpan.FromSeconds(5));
}
```

El trabajo almacena los resultados en el contenedor de blob suministrado como blob bloque con el nombre **devices.txt**. Los datos de salida constan de los datos de dispositivo JSON serializada con un dispositivo por línea.

En el ejemplo siguiente se muestra los datos de salida:

```
{"id":"Device1","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device2","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device3","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device4","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device5","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
```

Si necesita acceso a los datos en el código, puede deserializar fácilmente estos datos utilizando la clase **ExportImportDevice** . Fragmento de código de C# siguiente muestra cómo leer la información de dispositivo previamente exportado a un blob bloque:

```
var exportedDevices = new List<ExportImportDevice>();

using (var streamReader = new StreamReader(await blob.OpenReadAsync(AccessCondition.GenerateIfExistsCondition(), RequestOptions, null), Encoding.UTF8))
{
  while (streamReader.Peek() != -1)
  {
    string line = await streamReader.ReadLineAsync();
    var device = JsonConvert.DeserializeObject<ExportImportDevice>(line);
    exportedDevices.Add(device);
  }
}
```

> [AZURE.NOTE]  También puede usar el método **GetDevicesAsync** de la clase **RegistryManager** para capturar una lista de los dispositivos. Sin embargo, este enfoque tiene un límite de disco duro de 1000 en el número de objetos de dispositivo que se devuelven. El caso de uso esperado para el método **GetDevicesAsync** es para que escenarios de desarrollo facilitar la depuración y no se recomienda para las cargas de trabajo de producción.

## <a name="import-devices"></a>Dispositivos de importación

El método **ImportDevicesAsync** en la clase **RegistryManager** permite realizar operaciones de importación y la sincronización de masa en un registro de dispositivo IoT concentrador. Al igual que el método **ExportDevicesAsync** , el método **ImportDevicesAsync** utiliza el marco de **trabajo** .

Tenga cuidado con el método de **ImportDevicesAsync** porque además aprovisionar dispositivos nuevos en el registro de la identidad de dispositivo, también puede actualizar y eliminar dispositivos existentes.

> [AZURE.WARNING]  Una operación de importación no se puede deshacer. Hacer siempre una copia de los datos existentes con el método de **ExportDevicesAsync** a otro contenedor de blob antes de realizar cambios masivos en el registro de la identidad de dispositivos.

El método **ImportDevicesAsync** toma dos parámetros:

*  Una *cadena* que contiene un URI de un contenedor de [Azure almacenamiento de](https://azure.microsoft.com/documentation/services/storage/) blobs para usar como *entrada* para el trabajo. Este URI debe contener un símbolo de SA que conceda acceso de lectura al contenedor. Este contenedor debe contener un blob con el nombre **devices.txt** que contiene los datos del dispositivo serie para importar el registro de identidad del dispositivo. Importar datos deben contener información del dispositivo en el mismo formato JSON que el trabajo **ExportImportDevice** usa cuando crea un blob **devices.txt** . El token de sa debe incluir los permisos siguientes:

    ```
    SharedAccessBlobPermissions.Read
    ```

*  Una *cadena* que contiene un URI de un contenedor de [Azure almacenamiento de](https://azure.microsoft.com/documentation/services/storage/) blobs para usarla como *resultado* de la tarea. El trabajo crea un blob de bloque en este contenedor para almacenar la información de errores de la importación finalizada **trabajo**. El token de sa debe incluir los permisos siguientes:
    
    ```
    SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Delete
    ```

> [AZURE.NOTE]  Los dos parámetros pueden apuntar al mismo contenedor blob. Los parámetros independientes simplemente permiten tener más control sobre los datos como el contenedor de salida requiere permisos adicionales.

Fragmento de código de C# siguiente muestra cómo iniciar un trabajo de importación:

```
JobProperties importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);
```

## <a name="import-behavior"></a>Comportamiento de importación

Puede usar el método **ImportDevicesAsync** para realizar las siguientes operaciones de masa en el registro de identidad del dispositivo:

-   Registro de masa de nuevos dispositivos
-   Eliminaciones de masa de dispositivos existentes
-   Cambios de estado de forma masiva (habilitar o deshabilitar dispositivos)
-   Asignación en masa de nuevas claves de autenticación de dispositivo
-   La regeneración automática de claves de autenticación del dispositivo de forma masiva

Puede realizar una combinación de las operaciones anteriores en una sola llamada **ImportDevicesAsync** . Por ejemplo, puede registrar nuevos dispositivos y eliminar o actualizar los dispositivos existentes al mismo tiempo. Cuando se usa junto con el método **ExportDevicesAsync** , puede migrar completamente todos los dispositivos de un concentrador IoT a otro.

Utilice la propiedad opcional **importMode** en los datos de serialización de importación para cada dispositivo para controlar el proceso importar por dispositivo. La propiedad **importMode** tiene las siguientes opciones:

| importMode |  Descripción |
| -------- | ----------- |
| **createOrUpdate** | Si no existe un dispositivo con el **Id.**, recién registrada. <br/>Si ya existe el dispositivo, se sobrescribe la información existente con los datos de entrada proporcionados sin tener en cuenta el valor **ETag** . |
| **crear** | Si no existe un dispositivo con el **Id.**, recién registrada. <br/>Si ya existe el dispositivo, se escribe un error en el archivo de registro. |
| **actualizar** | Si ya existe un dispositivo con el **id**especificado, se sobrescribe la información existente con los datos de entrada proporcionados sin tener en cuenta el valor **ETag** . <br/>Si el dispositivo no existe, se escribe un error en el archivo de registro. |
| **updateIfMatchETag** | Si ya existe un dispositivo con el **id**especificado, se sobrescribe información existente con los datos de entrada proporcionados sólo si hay una coincidencia de **ETag** . <br/>Si el dispositivo no existe, se escribe un error en el archivo de registro. <br/>Si hay una incoherencia **ETag** , se escribe un error en el archivo de registro. |
| **createOrUpdateIfMatchETag** | Si no existe un dispositivo con el **Id.**, recién registrada. <br/>Si ya existe el dispositivo, se sobrescribe la información existente con los datos de entrada proporcionados sólo si hay una coincidencia de **ETag** . <br/>Si hay una incoherencia **ETag** , se escribe un error en el archivo de registro. |
| **eliminar** | Si ya existe un dispositivo con el **id**especificado, se elimina sin tener en cuenta el valor **ETag** . <br/>Si el dispositivo no existe, se escribe un error en el archivo de registro. |
| **deleteIfMatchETag** | Si ya existe un dispositivo con el **id**especificado, se elimina sólo si hay una coincidencia de **ETag** . Si el dispositivo no existe, se escribe un error en el archivo de registro. <br/>Si hay una incoherencia ETag, se escribe un error en el archivo de registro. |

> [AZURE.NOTE] Si los datos de serialización no definen explícitamente un indicador de **importMode** para un dispositivo, como opción predeterminada **createOrUpdate** durante la operación de importación.

## <a name="import-devices-example--bulk-device-provisioning"></a>Importar ejemplo de dispositivos: aprovisionamiento de dispositivos en masa 

El siguiente ejemplo de código de C# muestra cómo generar varias identidades de dispositivo que:

- Incluir claves de autenticación.
- Escribir esa información de dispositivo en un blob de bloque.
- Importar los dispositivos en el registro de la identidad de dispositivo.

```
// Provision 1,000 more devices
var serializedDevices = new List<string>();

for (var i = 0; i < 1000; i++)
{
// Create a new ExportImportDevice
  var deviceToAdd = new ExportImportDevice()
  {
    Id = Guid.NewGuid().ToString(),
    Status = DeviceStatus.Enabled,
    Authentication = new AuthenticationMechanism()
    {
      SymmetricKey = new SymmetricKey()
      {
        PrimaryKey = CryptoKeyGenerator.GenerateKey(32),
        SecondaryKey = CryptoKeyGenerator.GenerateKey(32)
      }
    },
    ImportMode = ImportMode.Create
  };

  // Add device to existing list
  serializedDevices.Add(JsonConvert.SerializeObject(deviceToAdd));
}

// Write this list to the blob
var sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice => sb.AppendLine(serializedDevice));
await blob.DeleteIfExistsAsync();

using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Call import using the same blob to add new devices!
// This normally takes 1 minute per 100 devices the normal way
JobProperties importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="import-devices-example--bulk-deletion"></a>Ejemplo de dispositivos de importación: eliminación en masa

El ejemplo siguiente muestra cómo eliminar los dispositivos que ha agregado en el ejemplo de código anterior:

```
// Step 1: Update each device's ImportMode to be Delete
sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice =>
{
  // Deserialize back to an ExportImportDevice
  var device = JsonConvert.DeserializeObject<ExportImportDevice>(serializedDevice);

  // Update property
  device.ImportMode = ImportMode.Delete;

  // Re-serialize
  sb.AppendLine(JsonConvert.SerializeObject(device));
});

// Step 2: Write the new import data back to the block blob
await blob.DeleteIfExistsAsync();
using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Step 3: Call import using the same blob to delete all devices!
importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}

```

## <a name="getting-the-container-sas-uri"></a>Obtener el contenedor SAS URI


El ejemplo siguiente muestra cómo generar un [URI de SA](../storage/storage-dotnet-shared-access-signature-part-2.md) con permisos de lectura, escritura y eliminar un contenedor de blobs de:

```
static string GetContainerSasUri(CloudBlobContainer container)
{
  // Set the expiry time and permissions for the container.
  // In this case no start time is specified, so the
  // shared access signature becomes valid immediately.
  var sasConstraints = new SharedAccessBlobPolicy();
  sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
  sasConstraints.Permissions = 
    SharedAccessBlobPermissions.Write | 
    SharedAccessBlobPermissions.Read | 
    SharedAccessBlobPermissions.Delete;

  // Generate the shared access signature on the container,
  // setting the constraints directly on the signature.
  string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

  // Return the URI string for the container,
  // including the SAS token.
  return container.Uri + sasContainerToken;
}

```

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido cómo realizar operaciones de forma masiva con el registro de la identidad de dispositivo en un concentrador IoT. Siga estos vínculos para obtener más información sobre la administración de Azure IoT concentrador:

- [Métrica de uso][lnk-metrics]
- [Operaciones de supervisión][lnk-monitor]

Para explorar aún más las capacidades de concentrador IoT, consulte:

- [Guía para programadores][lnk-devguide]
- [Simulación de un dispositivo con el SDK de puerta de enlace IoT][lnk-gateway]

[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
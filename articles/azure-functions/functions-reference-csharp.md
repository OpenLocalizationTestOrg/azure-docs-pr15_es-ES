<properties
    pageTitle="Referencia del programador funciones Azure | Microsoft Azure"
    description="Entender cómo desarrollar funciones de Azure mediante C#."
    services="functions"
    documentationCenter="na"
    authors="christopheranderson"
    manager="erikre"
    editor=""
    tags=""
    keywords="Azure funciones, funciones, procesamiento de eventos, webhooks, cálculo dinámica, sin servidor arquitectura"/>

<tags
    ms.service="functions"
    ms.devlang="dotnet"
    ms.topic="reference"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="05/13/2016"
    ms.author="chrande"/>

# <a name="azure-functions-c-developer-reference"></a>Referencia del programador de Azure funciones C#

> [AZURE.SELECTOR]
- [Secuencia de comandos de C#](../articles/azure-functions/functions-reference-csharp.md)
- [Script de F #](../articles/azure-functions/functions-reference-fsharp.md)
- [Node.js](../articles/azure-functions/functions-reference-node.md)
 
La experiencia de C# para las funciones de Azure se basa en el SDK de WebJobs de Azure. Flujos de datos en la función C# a través de los argumentos del método. Nombres de los argumentos se especifican en `function.json`, y no hay nombres predefinidos para tener acceso a cosas como los símbolos de registrador y la cancelación de la función.

En este artículo se supone que ya ha leído la [referencia del programador de funciones de Azure](functions-reference.md).

## <a name="how-csx-works"></a>Cómo funciona .csx

La `.csx` formato permite escribir menos "repetitivo" y centrarse en escribir solo una función de C#. Funciones de Azure, que solo incluya los espacios de nombres y las referencias de ensamblado necesita arriba arriba, de forma habitual y en lugar de ajustar todo en un espacio de nombres y una clase, simplemente puede definir su `Run` método. Si debe incluir ninguna clase, por ejemplo para definir objetos POCO, puede incluir una clase dentro del mismo archivo.

## <a name="binding-to-arguments"></a>Enlace de argumentos

Los distintos enlaces se enlazan a una función de C# a través de la `name` propiedad en la configuración de *function.json* . Cada enlace tiene sus propio tipos compatibles que se documenta por enlace; Por ejemplo, un desencadenador de blobs puede admitir una cadena, un POCO u otros tipos. Puede usar el tipo que mejor se adapte a sus necesidades. 

```csharp
public static void Run(string myBlob, out MyClass myQueueItem)
{
    log.Verbose($"C# Blob trigger function processed: {myBlob}");
    myQueueItem = new MyClass() { Id = "myid" };
}

public class MyClass
{
    public string Id { get; set; }
}
```

## <a name="logging"></a>Registro de información

Para iniciar sesión salida los registros streaming en C#, puede incluir un `TraceWriter` escribió argumento. Se recomienda que se nombre `log`. Se recomienda evitar `Console.Write` en las funciones de Azure.

```csharp
public static void Run(string myBlob, TraceWriter log)
{
    log.Info($"C# Blob trigger function processed: {myBlob}");
}
```

## <a name="async"></a>Asincrónica

Para hacer una función asincrónica, utilice el `async` palabra clave y devolver un `Task` objeto.

```csharp
public async static Task ProcessQueueMessageAsync(
        string blobName, 
        Stream blobInput,
        Stream blobOutput)
    {
        await blobInput.CopyToAsync(blobOutput, 4096, token);
    }
```

## <a name="cancellation-token"></a>Token de cancelación

En algunos casos, puede que tenga las operaciones que dependen de que se cierre. Aunque siempre es mejor escribir código que puede controlar bloquea, en los casos donde desee controlar se está saliendo solicita, defina un [`CancellationToken`](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx) escribió argumento.  A `CancellationToken` se proporcionará si se activa un apagado de host. 

```csharp
public async static Task ProcessQueueMessageAsyncCancellationToken(
        string blobName, 
        Stream blobInput,
        Stream blobOutput,
        CancellationToken token)
    {
        await blobInput.CopyToAsync(blobOutput, 4096, token);
    }
```

## <a name="importing-namespaces"></a>Importar espacios de nombres

Si necesita importar espacios de nombres, puede hacer lo habitual, con la `using` cláusula.

```csharp
using System.Net;
using System.Threading.Tasks;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
```

Los espacios de nombres siguientes se importan automáticamente y, por tanto, son opcionales:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`.

## <a name="referencing-external-assemblies"></a>Hacer referencia a ensamblados externos

Ensamblados de framework, agregar referencias mediante la `#r "AssemblyName"` directiva.

```csharp
#r "System.Web.Http"

using System.Net;
using System.Net.Http;
using System.Threading.Tasks;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
```

Los siguientes ensamblados se agregan automáticamente a las funciones de Azure entorno de hospedaje:

* `mscorlib`,
* `System`
* `System.Core`
* `System.Xml`
* `System.Net.Http`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`
* `Microsoft.Azure.WebJobs.Extensions`
* `System.Web.Http`
* `System.Net.Http.Formatting`.

Además, los siguientes ensamblados son especiales mayúsculas y minúsculas y pueden hacer referencia a simplename (por ejemplo, `#r "AssemblyName"`):

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNEt.WebHooks.Common`
* `Microsoft.Azure.NotificationHubs`

Si necesita hacer referencia a un ensamblado privado, puede cargar el archivo de ensamblado en un `bin` carpeta en relación con su función y la referencia mediante el archivo (por ejemplo, el nombre  `#r "MyAssembly.dll"`). Para obtener información sobre cómo cargar archivos a la carpeta de la función, vea la sección siguiente en la administración de paquetes.

## <a name="package-management"></a>Administración de paquetes

Para usar paquetes de NuGet en una función de C#, cargar un archivo de *project.json* a la carpeta de la función sistema de la aplicación de la función de archivos. Aquí es un archivo de *project.json* de ejemplo que agrega una referencia a Microsoft.ProjectOxford.Face versión 1.1.0:

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.ProjectOxford.Face": "1.1.0"
      }
    }
   }
}
```

Solo la 4.6 de .NET Framework es compatible, asegúrese de que especifica el archivo *project.json* `net46` como se muestra aquí.

Cuando se carga un archivo de *project.json* , el tiempo de ejecución obtiene los paquetes y agrega automáticamente las referencias a los ensamblados de paquete. No es necesario agregar `#r "AssemblyName"` directivas. Basta con agregar los necesarios `using` instrucciones al archivo *run.csx* usar los tipos definidos en los paquetes de NuGet.


### <a name="how-to-upload-a-projectjson-file"></a>Cómo cargar un archivo de project.json

1. Comenzar por asegurarse de que la aplicación de la función se está ejecutando, lo que puede hacer abriendo la función en el portal de Azure. 

    Esto también proporciona acceso a los registros de streaming donde se mostrarán los resultados de la instalación de paquete. 

2. Para cargar un archivo de project.json, siga uno de los métodos descritos en la sección **cómo actualizar archivos de aplicación de función** del [tema de referencia del programador de funciones de Azure](functions-reference.md#fileupdate). 

3. Después de carga el archivo *project.json* , verá resultados como el siguiente ejemplo de la función de transmisión de registro:

```
2016-04-04T19:02:48.745 Restoring packages.
2016-04-04T19:02:48.745 Starting NuGet restore
2016-04-04T19:02:50.183 MSBuild auto-detection: using msbuild version '14.0' from 'D:\Program Files (x86)\MSBuild\14.0\bin'.
2016-04-04T19:02:50.261 Feeds used:
2016-04-04T19:02:50.261 C:\DWASFiles\Sites\facavalfunctest\LocalAppData\NuGet\Cache
2016-04-04T19:02:50.261 https://api.nuget.org/v3/index.json
2016-04-04T19:02:50.261 
2016-04-04T19:02:50.511 Restoring packages for D:\home\site\wwwroot\HttpTriggerCSharp1\Project.json...
2016-04-04T19:02:52.800 Installing Newtonsoft.Json 6.0.8.
2016-04-04T19:02:52.800 Installing Microsoft.ProjectOxford.Face 1.1.0.
2016-04-04T19:02:57.095 All packages are compatible with .NETFramework,Version=v4.6.
2016-04-04T19:02:57.189 
2016-04-04T19:02:57.189 
2016-04-04T19:02:57.455 Packages restored.
```

## <a name="environment-variables"></a>Variables de entorno

Para obtener una variable de entorno o una aplicación de valor, use `System.Environment.GetEnvironmentVariable`, como se muestra en el ejemplo siguiente:

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    log.Info(GetEnvironmentVariable("AzureWebJobsStorage"));
    log.Info(GetEnvironmentVariable("WEBSITE_SITE_NAME"));
}

public static string GetEnvironmentVariable(string name)
{
    return name + ": " + 
        System.Environment.GetEnvironmentVariable(name, EnvironmentVariableTarget.Process);
}
```

## <a name="reusing-csx-code"></a>Volver a utilizar el código de .csx

Puede usar las clases y métodos definidos en otros archivos de *.csx* en el archivo *run.csx* . Para ello, use `#load` directivas en el archivo *run.csx* , tal como se muestra en el ejemplo siguiente.

Ejemplo *run.csx*:

```csharp
#load "mylogger.csx"

public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Verbose($"Log by run.csx: {DateTime.Now}"); 
    MyLogger(log, $"Log by MyLogger: {DateTime.Now}");
}
```

Ejemplo *mylogger.csx*:

```csharp
public static void MyLogger(TraceWriter log, string logtext)
{
    log.Verbose(logtext); 
}
```

Puede usar una ruta de acceso relativa con el `#load` directiva:

* `#load "mylogger.csx"`carga un archivo ubicado en la carpeta de la función.

* `#load "loadedfiles\mylogger.csx"`carga un archivo ubicado en una carpeta en la carpeta de la función.

* `#load "..\shared\mylogger.csx"`carga un archivo ubicado en una carpeta en el mismo nivel que la carpeta de la función, es decir, directamente debajo de *wwwroot*.
 
La `#load` directiva funciona únicamente con los archivos *.csx* (C# script), no con archivos *. cs* . 

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información, consulte los siguientes recursos:

* [Referencia del programador de Azure funciones](functions-reference.md)
* [Referencia del programador de Azure funciones F #](functions-reference-fsharp.md)
* [Referencia del programador de Azure NodeJS de funciones](functions-reference-node.md)
* [Enlaces y azure desencadenadores de funciones](functions-triggers-bindings.md)


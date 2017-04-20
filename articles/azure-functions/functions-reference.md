<properties
    pageTitle="Referencia del programador funciones Azure | Microsoft Azure"
    description="Comprender los conceptos de funciones de Azure y componentes que sean comunes a todos los idiomas y enlaces."
    services="functions"
    documentationCenter="na"
    authors="christopheranderson"
    manager="erikre"
    editor=""
    tags=""
    keywords="Azure funciones, funciones, procesamiento de eventos, webhooks, cálculo dinámica, sin servidor arquitectura"/>

<tags
    ms.service="functions"
    ms.devlang="multiple"
    ms.topic="reference"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="05/13/2016"
    ms.author="chrande"/>

# <a name="azure-functions-developer-reference"></a>Referencia del programador de Azure funciones

Funciones de Azure compartan algunos conceptos básicos de técnica y componentes, independientemente del idioma o enlace que usa. Antes de pasar a detalles específicos de un idioma determinado o un enlace de aprendizaje, asegúrese de leer esta información general que se aplica a todas ellas.

En este artículo se supone que ya ha leído la [Descripción general de las funciones de Azure](functions-overview.md) y está familiarizado con [los conceptos de SDK WebJobs como desencadenadores, enlaces y el tiempo de ejecución de JobHost](../app-service-web/websites-dotnet-webjobs-sdk.md). Funciones de Azure se basa en el SDK de WebJobs. 


## <a name="function-code"></a>Código de función

Una *función* es el concepto principal en las funciones de Azure. Escribir el código para una función en un idioma de su elección y guardar los archivos de código y un archivo de configuración en la misma carpeta. Configuración está en JSON y, a continuación, el archivo se denomina `function.json`. Una gran variedad de idiomas son compatibles, y cada una tiene una experiencia ligeramente distinta optimizada para funcionar mejor en ese idioma. 

La `function.json` archivo contiene configuración específica de una función, incluidas sus enlaces. El tiempo de ejecución lee este archivo para determinar qué eventos para desencadenar fuera de los datos que incluyen al llamar a la función y dónde enviar datos pasan a lo largo de la función. 

```json
{
    "disabled":false,
    "bindings":[
        // ... bindings here
        {
            "type": "bindingType",
            "direction": "in",
            "name": "myParamName",
            // ... more depending on binding
        }
    ]
}
```

Puede impedir que el tiempo de ejecución ejecuta la función estableciendo la `disabled` propiedad `true`.

La `bindings` propiedad es que configurar desencadenadores y enlaces. Cada enlace comparte algunas configuraciones comunes y algunas configuraciones que son específicos de un determinado tipo de enlace. Todos los enlaces requieren la siguiente configuración:

|(Propiedad)|Y tipos de valores|Comentarios|
|---|-----|------|
|`type`|cadena|Tipo de enlace. Por ejemplo, `queueTrigger`.
|`direction`|'in', 'salida'| Indica si el enlace es para recibir datos en la función o enviar datos de la función.
| `name` | cadena | El nombre que se usará para los datos enlazados en la función. Para C# será el nombre de un argumento; JavaScript será la clave en una lista de clave y valor.

## <a name="function-app"></a>Aplicación de función

Una aplicación de la función consta de una o más funciones individuales se administran juntos por el servicio de aplicación de Azure. Todas las funciones en una aplicación de la función de compartan el mismo plan de precio, la implementación continua y la versión de tiempo de ejecución. Funciones escritas en varios idiomas pueden compartir la misma aplicación de función. Piense en una aplicación de la función como una manera de organizar y administrar colectivamente sus funciones. 

## <a name="runtime-script-host-and-web-host"></a>Tiempo de ejecución (script host y host web)

El tiempo de ejecución o script host, es el host de WebJobs SDK subyacente que escucha los eventos, recopila y envía los datos y se ejecuta el código. 

Para facilitar la desencadenadores HTTP, también es un host de web diseñado para sentarse delante el host de script en escenarios de producción. Esto ayuda a aislar el host de script del tráfico front-end administrado por el host de web.

## <a name="folder-structure"></a>Estructura de carpetas

[AZURE.INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Al configurar un proyecto para implementar funciones en una aplicación de la función en la aplicación de servicio de Azure, puede tratar esta estructura de carpetas que el código del sitio. Puede utilizar otras herramientas, implementación y la integración continua o secuencias de comandos de implementación personalizado para hacerlo implementar el paquete de instalación de tiempo o código transpilation.

>[AZURE.NOTE] La `wwwroot` carpeta aquí es donde se implementará en los archivos. Sin embargo, no debe incluir en esa carpeta en los archivos que se implementa, que terminan con `wwwroot\wwwroot`. En su lugar, su `host.json` carpetas de archivo y la función debería directamente en la raíz de lo que implementa.

## <a id="fileupdate"></a>Cómo actualizar archivos de aplicación de función

El editor de funciones integrado en el portal de Azure le permite actualizar el archivo de *function.json* y el código para una función. Para cargar o actualizar otros archivos, como *package.json* o *project.json* o dependencias, debe usar otros métodos de implementación.

Función aplicaciones se basan en el servicio de aplicación, para que todas las [Opciones de implementación disponibles para las aplicaciones web estándar](../app-service-web/web-sites-deploy.md) están disponibles para las aplicaciones de la función también. Estos son algunos métodos que puede utilizar para cargar o actualizar archivos de aplicación de función. 

#### <a name="to-use-app-service-editor"></a>Usar el Editor de servicios de aplicación

1. En el portal de funciones de Azure, haga clic en **configuración de la aplicación de función**.

2. En la sección **Configuración avanzada** , haga clic en **Ir a configuración del servicio de aplicación**.

3. En **Herramientas de desarrollo**, haga clic en **Editor de servicios de aplicación** en el menú aplicación Nav.

4.  Haga clic en **Ir**.

    Cuando se cargue la aplicación de servicio de Editor, verá las carpetas de archivo y la función *host.json* en *wwwroot*. 

5. Abrir archivos para modificarlos, o arrastrar y colocar desde su equipo de desarrollo para cargar archivos.

#### <a name="to-use-the-function-apps-scm-kudu-endpoint"></a>Usar extremo de la aplicación función SCM (Kudu)

1. Vaya a: `https://<function_app_name>.scm.azurewebsites.net`.

2. Haga clic en **consola de depuración > CMD**.

3. Vaya a `D:\home\site\wwwroot\` actualizar *host.json* o `D:\home\site\wwwroot\<function_name>` para actualizar los archivos de la función.

4. Arrastrar y colocar un archivo que desea cargar en la carpeta correspondiente de la cuadrícula de archivo. Hay dos áreas de la cuadrícula de archivo donde se puede colocar un archivo. Para los archivos *.zip* , aparece un cuadro con la etiqueta "Arrastre aquí para cargar y descomprima." Para otros tipos de archivo, colocar en la cuadrícula de archivo, pero fuera del cuadro "descomprima".

#### <a name="to-use-ftp"></a>Usar FTP

1. Siga las instrucciones [aquí](../app-service-web/web-sites-deploy.md#ftp) para obtener FTP configurado.

2. Cuando esté conectado en el sitio de aplicación de la función, copiar un archivo actualizado *host.json* `/site/wwwroot` o copiar archivos de la función a `/site/wwwroot/<function_name>`.

#### <a name="to-use-continuous-deployment"></a>Para utilizar la implementación continua

Siga las instrucciones en el tema [implementación continua para las funciones de Azure](functions-continuous-deployment.md).

## <a name="parallel-execution"></a>Ejecución en paralelo

Cuando se producen varios eventos de activación con mayor rapidez que puede procesar un tiempo de ejecución de la función de un único subproceso, puede invocar la función varias veces en paralelo.  Si una aplicación de la función usa el [Plan de servicio dinámico](functions-scale.md#dynamic-service-plan), la aplicación de la función podría escalar automáticamente.  Cada instancia de la aplicación de la función, si la aplicación se ejecuta en el Plan de servicio dinámico o un normal de [Plan de servicio de aplicación](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md), puede procesar las llamadas de función simultáneas en paralelo con varios subprocesos.  El número máximo de las llamadas de función simultáneas en cada instancia de aplicación de la función varía según el tamaño de la memoria de la aplicación de la función. 

## <a name="azure-functions-pulse"></a>Pulse funciones de Azure  

Pulse es una secuencia de evento directo que muestra ¿con qué frecuencia se ejecuta la función, así como correctas y erróneas. También puede supervisar su tiempo de ejecución promedio. Agregaremos más características y personalización a lo largo del tiempo. Puede acceder a la página de **impulsos** desde la ficha **supervisión** .

## <a name="repositories"></a>Repositorios

El código para las funciones de Azure es Abrir origen y almacenado en GitHub repositorios:

* [Azure en tiempo de ejecución de funciones](https://github.com/Azure/azure-webjobs-sdk-script/)
* [Portal de funciones de Azure](https://github.com/projectkudu/AzureFunctionsPortal)
* [Plantillas de funciones de Azure](https://github.com/Azure/azure-webjobs-sdk-templates/)
* [Azure SDK WebJobs](https://github.com/Azure/azure-webjobs-sdk/)
* [Extensiones de Azure SDK WebJobs](https://github.com/Azure/azure-webjobs-sdk-extensions/)

## <a name="bindings"></a>Enlaces

Aquí tiene una tabla de todos los enlaces compatibles.

[AZURE.INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

## <a name="reporting-issues"></a>Informar de problemas

[AZURE.INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)] 

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información, consulte los siguientes recursos:

* [Referencia del programador de Azure funciones C#](functions-reference-csharp.md)
* [Referencia del programador de Azure funciones F #](functions-reference-fsharp.md)
* [Referencia del programador de Azure NodeJS de funciones](functions-reference-node.md)
* [Enlaces y azure desencadenadores de funciones](functions-triggers-bindings.md)
* [Funciones de azure: viaje por la](https://blogs.msdn.microsoft.com/appserviceteam/2016/04/27/azure-functions-the-journey/) en el blog del equipo de servicio de aplicaciones de Azure. Un historial de cómo se ha desarrollado funciones de Azure.






<properties
    pageTitle="Salida a un archivo de caché de Azure Redis, mediante funciones de Azure, desde el análisis de secuencia de Azure | Microsoft Azure"
    description="Obtenga información sobre cómo usar una función de Azure conectado de una cola de Bus de servicio, para rellenar una caché de Azure Redis desde el resultado de una tarea de análisis de la secuencia."
    keywords="flujo de datos, caché redis, cola de bus de servicio"
    services="stream-analytics"
    authors="ryancrawcour"
    manager="jhubbard"
    documentationCenter=""
    />

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/26/2016"
    ms.author="ryancraw"/>

# <a name="how-to-store-data-from-azure-stream-analytics-in-an-azure-redis-cache-using-azure-functions"></a>Cómo almacenar los datos de análisis de secuencia de Azure en una caché de Azure Redis mediante funciones de Azure

Análisis de secuencia de Azure le permite desarrollar e implementar soluciones de bajo costo para obtener perspectivas en tiempo real desde dispositivos, sensores, infraestructura y aplicaciones o cualquier secuencia de datos rápidamente. Permite que varios casos de uso, como en tiempo real administración y supervisión, comando y control, detección de fraudes, automóviles conectadas y muchos más. En muchos de estos casos, desea almacenar datos que se va a producir por el análisis de secuencia de Azure en un almacén de datos distribuidos como una caché Redis Azure.

Suponga que forma parte de una empresa de telecomunicaciones. Está intentando detectar el fraude SIM donde varias llamadas procedentes de la misma identidad, al mismo tiempo, pero en distintos geográfica ubicaciones. Le asigna la tarea con el almacenamiento de todas las llamadas telefónicas fraudulentas posibles en una caché Redis Azure. En este blog, le proporcionamos instrucciones sobre cómo completar fácilmente la tarea. 

## <a name="prerequisites"></a>Requisitos previos
Completar la [Detección de fraudes en tiempo real] [ fraud-detection] tutorial para ASA

## <a name="architecture-overview"></a>Introducción a la arquitectura
![Captura de pantalla de arquitectura](./media/stream-analytics-functions-redis/architecture-overview.png)

Tal como se muestra en la figura anterior, análisis de secuencia permite la transmisión de datos de entrada para consultar y envía a un resultado. En función de los resultados, funciones de Azure pueden desencadenar algún tipo de evento. 

En este blog se centrarse en la parte de Azure funciones de esta canalización o específicamente la activación de un evento que almacena datos fraudulentos en la memoria caché.
Después de completar la [Detección de fraudes en tiempo real] [ fraud-detection] tutorial, tiene una entrada (un concentrador de evento), una consulta y un resultado (almacenamiento de blobs) ya configurado y en funcionamiento. En este blog, se cambia el resultado para usar en su lugar de una cola de Bus de servicio. Después de eso, se conecta una función de Azure a esta cola. 

## <a name="create-and-connect-a-service-bus-queue-output"></a>Crear y conectar un resultado de cola de Bus de servicio
Para crear una cola de Bus de servicio, siga los pasos 1 y 2 de la sección .NET en [Introducción a colas de Bus de servicio][servicebus-getstarted].
Ahora vamos a conectar la cola a la tarea de análisis de flujo que se creó en el tutorial de detección de fraude anteriores.



1. En el portal de Azure, vaya a la hoja de **resultados** de su trabajo y seleccione **Agregar** en la parte superior de la página.

    ![Agregar resultados](./media/stream-analytics-functions-redis/adding-outputs.png)

2. Elegir **Servicio de Bus de cola** como el **receptor** y siga las instrucciones de la pantalla. Asegúrese de elegir el espacio de nombres de la cola de Bus de servicio que creó en [Introducción a colas de Bus de servicio][servicebus-getstarted]. Cuando haya terminado, haga clic en el botón "derecho".
3. Especifique los valores siguientes:
    - **Evento serializador formato**: JSON
    - **Codificación**: UTF8
    - **Formato**: línea separadas

4. Haga clic en el botón **crear** para agregar esta fuente y compruebe que el análisis de secuencia puede conectarse correctamente a la cuenta de almacenamiento.

5. En la pestaña **consulta** , reemplace la consulta actual con lo siguiente. Reemplazar *[Nombre de su servicio BUS]* con el nombre de salida que creó en el paso 3. 

    ```    

        SELECT 
            System.Timestamp as Time, CS1.CallingIMSI, CS1.CallingNum as CallingNum1, 
            CS2.CallingNum as CallingNum2, CS1.SwitchNum as Switch1, CS2.SwitchNum as Switch2

        INTO [YOUR SERVICE BUS NAME]
    
        FROM CallStream CS1 TIMESTAMP BY CallRecTime
        JOIN CallStream CS2 TIMESTAMP BY CallRecTime
            ON CS1.CallingIMSI = CS2.CallingIMSI AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
    
        WHERE CS1.SwitchNum != CS2.SwitchNum
    
    ```

## <a name="create-an-azure-redis-cache"></a>Crear una caché Redis Azure
Crear una caché de Azure Redis siguiendo la sección .NET en [cómo usar Azure Redis caché] [ use-rediscache] hasta la sección denominada ***configurar los clientes de la memoria caché***.
Cuando haya finalizado, tendrá una nueva caché Redis. En **todas las opciones**, seleccione **las teclas de acceso** y Nota hacia abajo de la ***cadena de conexión principal***.

![Captura de pantalla de arquitectura](./media/stream-analytics-functions-redis/redis-cache-keys.png)

## <a name="create-an-azure-function"></a>Crear una función de Azure
Siga [crear su primera función Azure] [ functions-getstarted] tutorial empezar a trabajar con funciones de Azure. Si ya dispone de una función de Azure que le gustaría usar, a continuación, vaya directamente al [escribir en caché Redis](#Writing-to-Redis-Cache)

1. En el portal, seleccione Servicios de aplicación en el panel de navegación izquierdo, haga clic en el nombre de la aplicación de la función Azure para ir al sitio Web de aplicación de la función.
    ![Captura de pantalla de la lista de funciones de servicios de aplicación](./media/stream-analytics-functions-redis/app-services-function-list.png)

2. Haga clic en **nueva función > ServiceBusQueueTrigger-C#**. Para los campos siguientes, siga estas instrucciones:
    - **Nombre de cola**: el mismo nombre que el nombre que escribió cuando creó la cola en [Introducción a colas de Bus de servicio] [ servicebus-getstarted] (no el nombre del bus de servicio). Asegúrese de que usar la cola que está conectada a la salida de análisis de la secuencia.
    - **Conexión de Bus de servicio**: seleccione **Agregar una cadena de conexión**. Para buscar la cadena de conexión, vaya al portal clásico, seleccione **Bus de servicio**, el bus de servicio que creó y la **Información de conexión** en la parte inferior de la pantalla. Asegúrese de que se encuentra en la pantalla principal de esta página. Copiar y pegar la cadena de conexión. No dude en cualquier nombre de la conexión.
    
        ![Captura de pantalla de conexión de bus de servicio](./media/stream-analytics-functions-redis/servicebus-connection.png)
    - **AccessRights**: elija **Administrar**


3. Haga clic en **crear**

## <a name="writing-to-redis-cache"></a>Escribir en Redis caché
Ahora se ha creado una función de Azure que se lee de una cola de Bus de servicio. Todo lo que queda por hacer es utilizar la función para escribir datos en la caché Redis. 

1. Seleccione su recién creado **ServiceBusQueueTrigger**y haga clic en **configuración de la aplicación de función** en la esquina superior derecha. Seleccione **vaya a configuración del servicio de aplicación > Configuración > configuración de la aplicación**

2. En la sección de cadenas de conexión, crear un nombre en la sección **nombre** . Pegue la cadena de conexión principal que encuentra en el paso de **crear una caché Redis** en la sección **valor** . Seleccione **personalizado** donde dice **Base de datos de SQL**.

3. En la parte superior, haga clic en **Guardar** .

    ![Captura de pantalla de conexión de bus de servicio](./media/stream-analytics-functions-redis/function-connection-string.png)

4. Ahora vuelva a la configuración del servicio de aplicación y seleccione **Herramientas > Editor de aplicación de servicio (vista preliminar) > en > Ir**.

    ![Captura de pantalla de conexión de bus de servicio](./media/stream-analytics-functions-redis/app-service-editor.png)

5. En un editor de su elección, cree un archivo JSON denominado **project.json** con el siguiente y guardarlo en el disco local.

        {
            "frameworks": {
                "net46": {
                    "dependencies": {
                        "StackExchange.Redis":"1.1.603",
                        "Newtonsoft.Json": "9.0.1"
                    }
                }
            }
        }

6. Cargar este archivo en el directorio de raíz de la función (no WWWROOT). Debería ver un archivo llamado **project.lock.json** automáticamente aparece, confirme que la Nuget paquetes "StackExchange.Redis" y "Newtonsoft.Json" se han importado.

7. En el archivo **run.csx** , reemplace el código generado previamente con el código siguiente. En la función lazyConnection, reemplace "Nombre de la conexión" con el nombre que creó en el paso 2 de **datos de almacenamiento en la caché Redis**.

````

    using System;
    using System.Threading.Tasks;
    using StackExchange.Redis;
    using Newtonsoft.Json;
    using System.Configuration;
    
    public static void Run(string myQueueItem, TraceWriter log)
    {
        log.Info($"Function processed message: {myQueueItem}");

        // Connection refers to a property that returns a ConnectionMultiplexer
        IDatabase db = Connection.GetDatabase();
        log.Info($"Created database {db}");
    
        // Parse JSON and extract the time
        var message = JsonConvert.DeserializeObject<dynamic>(myQueueItem);
        string time = message.time;
        string callingnum1 = message.callingnum1;

        // Perform cache operations using the cache object...
        // Simple put of integral data types into the cache
        string key = time + " - " + callingnum1;
        db.StringSet(key, myQueueItem);
        log.Info($"Object put in database. Key is {key} and value is {myQueueItem}");

        // Simple get of data types from the cache
        string value = db.StringGet(key);
        log.Info($"Database got: {value}"); 
    }

    // Connect to the Service Bus
    private static Lazy<ConnectionMultiplexer> lazyConnection = 
        new Lazy<ConnectionMultiplexer>(() =>
            {
                var cnn = ConfigurationManager.ConnectionStrings["CONN NAME"].ConnectionString
                return ConnectionMultiplexer.Connect();
            });
    
    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }

````

## <a name="start-the-stream-analytics-job"></a>Iniciar el trabajo de análisis de secuencia

1. Inicie la aplicación telcodatagen.exe. El uso es como sigue:````telcodatagen.exe [#NumCDRsPerHour] [SIM Card Fraud Probability] [#DurationHours]````

2. En el módulo de trabajo de análisis de secuencia en el portal, haga clic en **Inicio** en la parte superior de la página.

    ![Captura de pantalla de trabajo de inicio](./media/stream-analytics-functions-redis/starting-job.png)

3. En el módulo de **iniciar el trabajo** que aparece, seleccione **ahora** y, a continuación, haga clic en el botón **Inicio** en la parte inferior de la pantalla. Los cambios de estado de trabajo inicial y después de algunos cambios de tiempo en ejecución.
 
    ![Captura de pantalla de selección de tiempo de trabajo de inicio](./media/stream-analytics-functions-redis/start-job-time.png)

## <a name="run-solution-and-check-results"></a>Ejecute la solución y comprobar los resultados
Volver a la página **ServiceBusQueueTrigger** , ahora debe ver instrucciones log. Estos registros mostrar que tiene algo de la cola de Bus de servicio, en la base de datos y obtener usando la hora como clave!

Para comprobar que los datos están en la memoria caché Redis, vaya a la página de caché Redis en el nuevo portal (como se muestra en el paso anterior de [crear una caché de Azure Redis](#Create-an-Azure-Redis-Cache) ) y seleccione consola.

Ahora puede escribir comandos Redis para confirmar que los datos son en realidad en la memoria caché.

![Captura de pantalla de consola Redis](./media/stream-analytics-functions-redis/redis-console.png)

## <a name="next-steps"></a>Pasos siguientes
Estamos satisfechos sobre las novedades en las funciones de Azure y análisis de secuencia pueden hacer conjuntamente y esperamos que esto desbloquea nuevas posibilidades para usted. Si tiene cualquier comentarios sobre lo que desea en la siguiente, no dude en usar el [sitio de UserVoice de Azure](https://feedback.azure.com/forums/270577-stream-analytics).

Si es nuevo Microsoft Azure, nos invitar para probarlo por registrarse para obtener una [cuenta gratuita de prueba de Azure](https://azure.microsoft.com/pricing/free-trial/). Si es nuevo en el análisis de secuencia, a continuación, se invitar a usted para [crear su primer análisis de flujo de trabajo](stream-analytics-create-a-job.md).

Si necesita alguna ayuda o tiene preguntas, publicar en los foros [MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics) o [Stackoverflow](http://stackoverflow.com/questions/tagged/azure-stream-analytics) . 

También puede ver los siguientes recursos:

- [Referencia del programador de Azure funciones](../azure-functions/functions-reference.md)
- [Referencia del programador de Azure funciones C#](../azure-functions/functions-reference-csharp.md)
- [Referencia del programador de Azure funciones F #](../azure-functions/functions-reference-fsharp.md)
- [Referencia del programador de Azure NodeJS de funciones](../azure-functions/functions-reference.md)
- [Enlaces y azure desencadenadores de funciones](../azure-functions/functions-triggers-bindings.md)
- [Cómo supervisar Azure Redis caché](../redis-cache/cache-how-to-monitor.md)

Para mantenerse actualizado en todas las últimas noticias y características, siga [@AzureStreaming](https://twitter.com/AzureStreaming) en Twitter.


[fraud-detection]: stream-analytics-real-time-fraud-detection.md
[servicebus-getstarted]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md
[use-rediscache]: ../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md
[functions-getstarted]: ../azure-functions/functions-create-first-azure-function.md

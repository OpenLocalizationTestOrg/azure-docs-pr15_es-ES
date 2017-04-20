<properties
   pageTitle="Topologías Apache tormenta con Visual Studio y C# | Microsoft Azure"
   description="Aprenda a crear topologías de tormenta en C# mediante la creación de una topología de recuento simple de word en Visual Studio mediante las herramientas de HDInsight para Visual Studio."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
   tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/27/2016"
   ms.author="larryfr"/>

# <a name="develop-c-topologies-for-apache-storm-on-hdinsight-using-hadoop-tools-for-visual-studio"></a>Desarrollar C# topologías para tormenta Apache en HDInsight con Hadoop tools para Visual Studio

Obtenga información sobre cómo crear una topología de tormenta C# mediante las herramientas de HDInsight para Visual Studio. Este tutorial le guía a través del proceso de creación de un nuevo proyecto de tormenta en Visual Studio, pruebas de forma local e implementarlo en una tormenta Apache en clúster HDInsight.

También aprenderá a crear topologías híbridas que utilizan los componentes de Java y C#.

> [AZURE.IMPORTANT] Mientras los pasos de este documento se basan en un entorno de desarrollo de Windows con Visual Studio, el proyecto compilado puede enviarse a clúster Linux o de HDInsight de Windows. Solo clústeres basados en Linux crean después de soporte técnico de 28/10/2016 SCP.NET topologías.
>
> Para usar una topología de C# con un clúster basado en Linux, deberá actualizar el paquete Microsoft.SCP.Net.SDK NuGet utilizado por su proyecto a la versión 0.10.0.6 o superior. La versión del paquete también debe coincidir con la versión principal de tormenta instalado en HDInsight. Por ejemplo, tormenta en HDInsight versiones 3.3 y 3.4 utilizar la versión de tormenta 0.10.x, mientras que HDInsight 3.5 utiliza tormenta 1.0. x.
> 
> C# topologías en clústeres basados en Linux deben utilizar .NET 4.5 y usar Mono para ejecutar en el clúster HDInsight. La mayoría de elementos funcionará, pero debe comprobar el documento [Mono compatibilidad](http://www.mono-project.com/docs/about-mono/compatibility/) para posible incompatibilidad.

## <a name="prerequisites"></a>Requisitos previos

- Una de las siguientes versiones de Visual Studio

    - Visual Studio 2012 con [4 de actualización](http://www.microsoft.com/download/details.aspx?id=39305)

    - 2013 de Visual Studio con la [Comunidad Visual Studio 2013](http://go.microsoft.com/fwlink/?LinkId=517284) o [Actualizar 4](http://www.microsoft.com/download/details.aspx?id=44921)

    - [Comunidad Visual Studio 2015](https://go.microsoft.com/fwlink/?LinkId=532606) o Visual Studio de 2015

- Azure SDK 2.9.5 o posterior

- HDInsight Tools para Visual Studio: vea [Introducción al uso HDInsight Tools para Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md) para instalar y configurar las herramientas de HDInsight para Visual Studio.

    > [AZURE.NOTE] HDInsight Tools para Visual Studio no se admiten en Visual Studio Express

-   Tormenta Apache en HDInsight clúster: vea la [Introducción de tormenta Apache en HDInsight](hdinsight-apache-storm-tutorial-get-started.md) para conocer los pasos para crear un clúster.

## <a name="templates"></a>Plantillas

Las herramientas de HDInsight para Visual Studio proporciona las siguientes plantillas::

| Tipo de proyecto | Se muestra |
| ------------ | ------------- |
| Aplicación de tormenta | Un proyecto de topología de tormenta vacío |
| Ejemplo de escritura SQL Azure tormenta | Cómo escribir en la base de datos de SQL Azure |
| Ejemplo de DocumentDB lector de tormenta | Cómo leer de DocumentDB de Azure |
| Ejemplo de DocumentDB escritor de tormenta | Cómo escribir en DocumentDB de Azure |
| Ejemplo de EventHub lector de tormenta | Cómo leer de Azure evento Hubs |
| Ejemplo de EventHub escritor de tormenta | Cómo escribir en Azure evento Hubs |
| Ejemplo de HBase lector de tormenta | Cómo leer de HBase en clústeres HDInsight |
| Ejemplo de HBase escritor de tormenta | Cómo escribir en HBase en HDInsight clústeres |
| Ejemplo de tormenta híbrido | Cómo usar un componente de Java |
| Ejemplo de tormenta | Una topología de recuento básicas de word |

> [AZURE.NOTE] Los ejemplos de lector y escritor de HBase usar la API de REST HBase para comunicarse con un HBase en HDInsight clúster, no la API de Java HBase.

En los pasos de este documento, usará el tipo de proyecto de aplicación de tormenta básico para crear una nueva topología.

## <a name="create-a-c-topology"></a>Crear una topología de C#

1. Si no ha instalado la versión más reciente de las herramientas de HDInsight para Visual Studio, vea [Introducción al uso HDInsight Tools para Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md).

2. Abra Visual Studio, seleccione **archivo** > **nuevo**y, a continuación, en **proyecto**.

3. En la pantalla **Nuevo proyecto** , expanda **instalados** > **plantillas**y seleccione **HDInsight**. En la lista de plantillas, seleccione la **Aplicación de tormenta**. En la parte inferior de la pantalla, escriba **WordCount** como el nombre de la aplicación.

    ![imagen](./media/hdinsight-storm-develop-csharp-visual-studio-topology/new-project.png)

4. Una vez creado el proyecto, debería tener los archivos siguientes:

    - **Program.cs**: define la topología de su proyecto. Tenga en cuenta que se crea una topología predeterminado que consta de un pitorro y uno pernos de forma predeterminada.

    - **Spout.cs**: un pitorro de ejemplo que emite números aleatorios.

    - **Bolt.cs**: un rayo de ejemplo que mantiene un recuento de números emitidos por pitorro.

    Como parte de la creación de un proyecto, se descargará la última [SCP.NET paquetes](https://www.nuget.org/packages/Microsoft.SCP.Net.SDK/) de NuGet.

    [AZURE.INCLUDE [scp.net version important](../../includes/hdinsight-storm-scpdotnet-version.md)]

En las siguientes secciones, se modificará este proyecto en una aplicación WordCount básica.

### <a name="implement-the-spout"></a>Implementar pitorro

1. Abra **Spout.cs**. Spouts se usan para leer los datos en una topología de un origen externo. Los componentes principales de un pitorro son:

    - **NextTuple**: llama tormenta cuando se permite pitorro emitir tuplas nuevo.

    - **ACK** (solo topología transacciones): controla confirmaciones iniciadas por otros componentes de la topología de tuplas que se envía desde esta pitorro. Reconocimiento de una tupla permite pitorro saber que se ha procesado correctamente los componentes de nivel inferior.

    - **Un error** (solo topología transacciones): controla tuplas que son fail-procesamiento de otros componentes de la topología. Esto proporciona la oportunidad de volver a emitir la tupla para que se puede procesar de nuevo.

2. Reemplace el contenido de la clase **chorros** con los siguientes elementos. Esto crea un pitorro que emite aleatoriamente una oración en la topología.

        private Context ctx;
        private Random r = new Random();
        string[] sentences = new string[] {
            "the cow jumped over the moon",
            "an apple a day keeps the doctor away",
            "four score and seven years ago",
            "snow white and the seven dwarfs",
            "i am at two with nature"
        };

        public Spout(Context ctx)
        {
            // Set the instance context
            this.ctx = ctx;

            Context.Logger.Info("Generator constructor called");

            // Declare Output schema
            Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
            // The schema for the default output stream is
            // a tuple that contains a string field
            outputSchema.Add("default", new List<Type>() { typeof(string) });
            this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));
        }

        // Get an instance of the spout
        public static Spout Get(Context ctx, Dictionary<string, Object> parms)
        {
            return new Spout(ctx);
        }

        public void NextTuple(Dictionary<string, Object> parms)
        {
            Context.Logger.Info("NextTuple enter");
            // The sentence to be emitted
            string sentence;

            // Get a random sentence
            sentence = sentences[r.Next(0, sentences.Length - 1)];
            Context.Logger.Info("Emit: {0}", sentence);
            // Emit it
            this.ctx.Emit(new Values(sentence));

            Context.Logger.Info("NextTuple exit");
        }

        public void Ack(long seqId, Dictionary<string, Object> parms)
        {
            // Only used for transactional topologies
        }

        public void Fail(long seqId, Dictionary<string, Object> parms)
        {
            // Only used for transactional topologies
        }
    
    Dedique un momento para leer los comentarios a comprender qué hace este código.

### <a name="implement-the-bolts"></a>Implementar los pernos

1. Elimine el archivo **Bolt.cs** existente del proyecto.

2. En el **Explorador de soluciones**, haga clic en el proyecto y seleccione **Agregar** > **nuevo elemento**. En la lista, seleccione **Tormenta rayo**y escriba **Splitter.cs** como el nombre. Repita el proceso para crear una segunda perno con nombre **Counter.cs**.

    - **Splitter.cs**: implementa un rayo que divide las frases en palabras individuales y emite un nuevo flujo de palabras.

    - **Counter.cs**: implementa un rayo que cuenta cada palabra y emite un nuevo flujo de palabras y el recuento de cada palabra.

    > [AZURE.NOTE] Estos pernos simplemente leer y escribir en secuencias, pero también puede usar un rayo para comunicarse con orígenes, como una base de datos o un servicio.

3. Abra **Splitter.cs**. Tenga en cuenta que tiene un solo método de forma predeterminada: **Ejecutar**. Esto se denomina cuando el rayo recibe una tupla para el procesamiento. Aquí, puede leer y procesar tuplas entrantes y emitir tuplas saliente.

4. Reemplace el contenido de la clase **divisor** con el siguiente código:

        private Context ctx;

        // Constructor
        public Splitter(Context ctx)
        {
            Context.Logger.Info("Splitter constructor called");
            this.ctx = ctx;

            // Declare Input and Output schemas
            Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
            // Input contains a tuple with a string field (the sentence)
            inputSchema.Add("default", new List<Type>() { typeof(string) });
            Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
            // Outbound contains a tuple with a string field (the word)
            outputSchema.Add("default", new List<Type>() { typeof(string) });
            this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
        }

        // Get a new instance of the bolt
        public static Splitter Get(Context ctx, Dictionary<string, Object> parms)
        {
            return new Splitter(ctx);
        }

        // Called when a new tuple is available
        public void Execute(SCPTuple tuple)
        {
            Context.Logger.Info("Execute enter");

            // Get the sentence from the tuple
            string sentence = tuple.GetString(0);
            // Split at space characters
            foreach (string word in sentence.Split(' '))
            {
                Context.Logger.Info("Emit: {0}", word);
                //Emit each word
                this.ctx.Emit(new Values(word));
            }

            Context.Logger.Info("Execute exit");
        }

    Dedique un momento para leer los comentarios a comprender qué hace este código.

5. Abra **Counter.cs** y reemplace el contenido de la clase con los siguientes elementos:

        private Context ctx;

        // Dictionary for holding words and counts
        private Dictionary<string, int> counts = new Dictionary<string, int>();

        // Constructor
        public Counter(Context ctx)
        {
            Context.Logger.Info("Counter constructor called");
            // Set instance context
            this.ctx = ctx;

            // Declare Input and Output schemas
            Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
            // A tuple containing a string field - the word
            inputSchema.Add("default", new List<Type>() { typeof(string) });

            Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
            // A tuple containing a string and integer field - the word and the word count
            outputSchema.Add("default", new List<Type>() { typeof(string), typeof(int) });
            this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
        }

        // Get a new instance
        public static Counter Get(Context ctx, Dictionary<string, Object> parms)
        {
            return new Counter(ctx);
        }

        // Called when a new tuple is available
        public void Execute(SCPTuple tuple)
        {
            Context.Logger.Info("Execute enter");

            // Get the word from the tuple
            string word = tuple.GetString(0);
            // Do we already have an entry for the word in the dictionary?
            // If no, create one with a count of 0
            int count = counts.ContainsKey(word) ? counts[word] : 0;
            // Increment the count
            count++;
            // Update the count in the dictionary
            counts[word] = count;

            Context.Logger.Info("Emit: {0}, count: {1}", word, count);
            // Emit the word and count information
            this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new List<SCPTuple> { tuple }, new Values(word, count));
            Context.Logger.Info("Execute exit");
        }

    Dedique un momento para leer los comentarios a comprender qué hace este código.

### <a name="define-the-topology"></a>Definir la topología

Spouts y pernos se organizan en un gráfico, que define cómo fluyen los datos entre los componentes. Para esta topología, el gráfico es como sigue:

![imagen de cómo organizar los componentes](./media/hdinsight-storm-develop-csharp-visual-studio-topology/wordcount-topology.png)

Las frases se emiten desde pitorro, que se distribuyen a las instancias de rayo de división. El rayo divisora separa las frases en palabras, que se distribuyen a rayo de contador.

Dado que el recuento de palabras se mantiene localmente en la instancia de contador, queremos que determinadas palabras fluyan a la misma instancia de rayo contador, por lo que tenemos que solo una instancia de seguimiento de una palabra específica. Pero, para el rayo divisor realmente no importa qué rayo recibe la oración, por lo que deseamos oraciones de equilibrio de carga en todas las instancias.

Abra **Program.cs**. El método importante es **GetTopologyBuilder**, que se usa para definir la topología que se envía a tormenta. Reemplazar el contenido de **GetTopologyBuilder** con el siguiente código para implementar la topología descrita anteriormente:

        // Create a new topology named 'WordCount'
        TopologyBuilder topologyBuilder = new TopologyBuilder("WordCount" + DateTime.Now.ToString("yyyyMMddHHmmss"));

        // Add the spout to the topology.
        // Name the component 'sentences'
        // Name the field that is emitted as 'sentence'
        topologyBuilder.SetSpout(
            "sentences",
            Spout.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"sentence"}}
            },
            1);
        // Add the splitter bolt to the topology.
        // Name the component 'splitter'
        // Name the field that is emitted 'word'
        // Use suffleGrouping to distribute incoming tuples
        //   from the 'sentences' spout across instances
        //   of the splitter
        topologyBuilder.SetBolt(
            "splitter",
            Splitter.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"word"}}
            },
            1).shuffleGrouping("sentences");

        // Add the counter bolt to the topology.
        // Name the component 'counter'
        // Name the fields that are emitted 'word' and 'count'
        // Use fieldsGrouping to ensure that tuples are routed
        //   to counter instances based on the contents of field
        //   position 0 (the word). This could also have been
        //   List<string>(){"word"}.
        //   This ensures that the word 'jumped', for example, will always
        //   go to the same instance
        topologyBuilder.SetBolt(
            "counter",
            Counter.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"word", "count"}}
            },
            1).fieldsGrouping("splitter", new List<int>() { 0 });

        // Add topology config
        topologyBuilder.SetTopologyConfig(new Dictionary<string, string>()
        {
            {"topology.kryo.register","[\"[B\"]"}
        });

        return topologyBuilder;

Dedique un momento para leer los comentarios a comprender qué hace este código.

## <a name="submit-the-topology"></a>Enviar la topología

1. En el **Explorador de soluciones**, haga clic en el proyecto y haga **clic en Enviar a tormenta en HDInsight**.

    > [AZURE.NOTE] Si se le solicita, escriba las credenciales de inicio de sesión para su suscripción de Azure. Si tiene más de una suscripción, inicie sesión en la tabla que contiene la tormenta en clúster HDInsight.

2. Seleccione su tormenta en clúster de HDInsight de la lista desplegable de **Tormenta clúster** y, a continuación, haga **clic en enviar**. Puede supervisar si la presentación es correcta mediante la ventana de **resultados** .

3. Cuando la topología se ha enviado correctamente, debe aparecer la **Tormenta topologías** para el clúster. Seleccione la topología **WordCount** de la lista para ver información sobre la topología de ejecución.

    > [AZURE.NOTE] También puede ver **Tormenta topologías** del **Explorador**de servidores: expandir **Azure** > **HDInsight**, haga una tormenta de clúster HDInsight y a continuación, seleccione **Vista tormenta topologías**.

    Para ver información sobre estos componentes, use los vínculos de la spouts o pernos. Se abrirá una ventana nueva para cada elemento seleccionado.

4. Desde la vista de **Resumen de topología** , haga clic en **Eliminar** para detener la topología.

    > [AZURE.NOTE] Topologías de tormenta continúen hasta que se elimine el clúster o están desactivadas.

## <a name="transactional-topology"></a>Topología de transacciones

La topología anterior es no transacciones. Los componentes de la topología de implementar ninguna funcionalidad para reproducir los mensajes si se produce un error de procesamiento de un componente en la topología. Un ejemplo de topología transacciones, cree un nuevo proyecto y seleccione **Tormenta muestra** como el tipo de proyecto.

Transacciones topologías implementan lo siguiente para admitir la reproducción de datos:

- **Almacenamiento en caché de metadatos**: pitorro debe almacenar metadatos acerca de los datos que se emite para que los datos se pueden recuperar y volver a emitir si se produce un error. Como los datos emitidos por la muestra están pequeños, los datos sin formato para cada tupla se almacenan en un diccionario de reproducción.

- **ACK**: pueden llamar cada pernos en la topología de `this.ctx.Ack(tuple)` a ack que tiene correctamente procesa una tupla. Cuando todos los pernos tienen realizados tupla, la `Ack` se invoca método del pitorro. Esto permite pitorro quitar los datos almacenados en caché para su reproducción porque los datos se ha procesado completamente.

- **Error**: puede llamar cada rayo `this.ctx.Fail(tuple)` para indicar que ha fallado procesamiento de una tupla. El error se propaga a la `Fail` método del pitorro, donde se puede reproducir la tupla mediante el uso de caché de metadatos.

- **Identificador de secuencia**: cuando se emiten una tupla, se puede especificar un identificador de secuencia. Debe ser un valor que identifica la tupla para el procesamiento de la reproducción (Ack y éxito). Por ejemplo, en el proyecto de **Ejemplo de tormenta** pitorro usa las siguientes cuando se emiten datos:

        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), lastSeqId);

    Esto emite una nueva tupla que contiene una oración a la secuencia de forma predeterminada, con el valor del identificador de secuencia contenido en **lastSeqId**. En este ejemplo, **lastSeqId** simplemente se incrementa para cada tupla emitido.

Como se muestra en el proyecto de **Ejemplo de tormenta** , si un componente es transacciones puede establecer en tiempo de ejecución en función de la configuración.

## <a name="hybrid-topology"></a>Topología de híbrido

HDInsight tools para Visual Studio también pueden utilizarse para crear topologías híbridas, donde algunos componentes son C# y otros son Java.

Para un ejemplo de topología de híbridos, cree un nuevo proyecto y seleccione **Tormenta Hybrid Sample**. Esto crea una muestra totalmente comentada que contiene varias topologías que muestran los procedimientos siguientes:

- **Chorros Java** y **C# rayo**: definido en **HybridTopology_javaSpout_csharpBolt**

    - Una versión de transacciones se define en **HybridTopologyTx_javaSpout_csharpBolt**

- **Rayo Java**y **C# chorros** : definido en **HybridTopology_csharpSpout_javaBolt**

    - Una versión de transacciones se define en **HybridTopologyTx_csharpSpout_javaBolt**

        > [AZURE.NOTE] Esta versión también muestra cómo usar el código de Clojure desde un archivo de texto como un componente de Java.

Para cambiar entre la topología que se usa cuando se envía el proyecto, simplemente mover el `[Active(true)]` instrucción a la topología que desea utilizar antes de enviarlo al clúster.

> [AZURE.NOTE] Todos los archivos de Java necesarios se proporcionan como parte de este proyecto en la carpeta **JavaDependency** .

Tenga en cuenta lo siguiente al crear y enviar una topología híbrido:

- **JavaComponentConstructor** debe usarse para crear una nueva instancia de la clase de Java para un pitorro o rayo.

- **microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer** se debe utilizar para serializar datos hacia o fuera de componentes de Java de objetos Java a JSON.

- Cuando envíe la topología en el servidor, debe usar la opción **configuraciones adicionales** para especificar las **rutas de acceso del archivo de Java**. La ruta especificada debe ser el directorio que contiene los archivos JAR que contienen las clases de Java.

### <a name="azure-event-hubs"></a>Evento Azure Hubs

Versión SCP.Net 0.9.4.203 presenta una nueva clase y método específicamente para trabajar con el evento concentrador chorros (un Java pitorro que se lee de evento concentrador.) Cuando se crea una topología que usa esta pitorro, utilice los métodos siguientes:

- Clase **EventHubSpoutConfig** : crea un objeto que contiene la configuración para el componente de pitorro

- Método de **TopologyBuilder.SetEventHubSpout** : agrega el componente de evento concentrador chorros a la topología

> [AZURE.NOTE] Mientras estos que sea más fácil trabajar en el Hub de evento chorros que otros componentes de Java, todavía debe utilizar el CustomizedInteropJSONSerializer serializar datos creados mediante pitorro.

## <a id="configurationmanager"></a>Usar ConfigurationManager

No utilice ConfigurationManager para recuperar los valores de configuración de rayo y chorros componentes; Esto provocará una excepción de puntero nulo. En su lugar, la configuración para el proyecto se pasa a la topología de tormenta como un par de clave y valor en el contexto de la topología. Cada componente que se basa en los valores de configuración debe recuperarlos desde el contexto durante la inicialización.

El código siguiente muestra cómo recuperar estos valores:

    public class MyComponent : ISCPBolt
    {
        // To hold configuration information loaded from context
        Configuration configuration;
        ...
        public MyComponent(Context ctx, Dictionary<string, Object> parms)
        {
            // Save a copy of the context for this component instance
            this.ctx = ctx;
            // If it exists, load the configuration for the component
            if(parms.ContainsKey(Constants.USER_CONFIG))
            {
                this.configuration = parms[Constants.USER_CONFIG] as System.Configuration.Configuration;
            }
            // Retrieve the value of "Foo" from configuration
            var foo = this.configuration.AppSettings.Settings["Foo"].Value;
        }
        ...
    }

Si usa un `Get` método para devolver una instancia de componente, debe asegurarse de que pasa la `Context` y `Dictionary<string, Object>` parámetros del constructor. El ejemplo siguiente es un basic `Get` método que pasa correctamente estos valores:

    public static MyComponent Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new MyComponent(ctx, parms);
    }

## <a name="how-to-update-scpnet"></a>Cómo actualizar SCP.NET

Versiones recientes de SCP.NET admiten la actualización de paquete a través de NuGet. Cuando esté disponible una nueva actualización, recibirá una notificación de actualización. Para comprobar manualmente una actualización, siga estos pasos:

1. En el **Explorador de soluciones**, haga clic en el proyecto y seleccione **Administrar paquetes de NuGet**.

2. En el Administrador de paquetes, seleccione **actualizaciones**. Si hay disponible una actualización, se mostrarán. Haga clic en el botón de **actualización** para el paquete instalarlo.

> [AZURE.IMPORTANT] Si el proyecto se creó con una de las versiones anteriores de SCP.NET que no se ha utilizado NuGet actualizaciones del paquete, debe realizar los siguientes pasos para actualizar a la nueva versión:
>
> 1. En el **Explorador de soluciones**, haga clic en el proyecto y seleccione **Administrar paquetes de NuGet**.
> 2. Utilizar el campo de **búsqueda** , buscar y, a continuación, agregar, **Microsoft.SCP.Net.SDK** al proyecto.

## <a name="troubleshooting"></a>Solución de problemas

### <a name="null-pointer-exceptions"></a>Excepciones de puntero nulo

Cuando se utiliza una topología de C# con un clúster de HDInsight basados en Linux, pernos y chorros componentes que utilizan ConfigurationManager para leer los valores de configuración en tiempo de ejecución pueden devolver excepciones de puntero nulo. Esto ocurre porque la configuración del dominio cargado no es de ensamblado que contiene el proyecto.

La configuración para el proyecto se pasa a la topología de tormenta como un par de clave y valor en el contexto de topología y se puede recuperar del objeto de diccionario que se pasa a los componentes cuando se hayan inicializado.

En el ejemplo siguiente se muestra cómo cargar los valores de configuración desde el contexto de topología, consulte la sección [ConfigurationManager](#configurationmanager) de este documento.

### <a name="systemtypeloadexception"></a>System.TypeLoadException

Cuando se utiliza una topología de C# con un clúster de HDInsight basados en Linux, puede aparecer el siguiente error:

    System.TypeLoadException: Failure has occurred while loading a type.

Normalmente se produce cuando se usa un archivo binario que no es compatible con la versión de .NET que admita mono.

Para clústeres basados en Linux HDInsight, debe asegurarse de que el proyecto utiliza código binario compilado para .NET 4.5.


### <a name="test-a-topology-locally"></a>Probar una topología localmente

Aunque es fácil implementar una topología en un clúster, en algunos casos, necesitará probar una topología localmente. Realice los pasos siguientes para ejecutar y probar la topología de ejemplo en este tutorial localmente en su entorno de desarrollo.

> [AZURE.WARNING] Pruebas locales solo funciona para básica, C# sola topologías. No debe utilizar pruebas local para topologías híbridas o topologías que usan varias secuencias, como recibirá errores.

1. En el **Explorador de soluciones**, haga clic en el proyecto y seleccione **Propiedades**. En las propiedades del proyecto, cambie el **tipo de resultado** aplicación de **Consola**.

    ![tipo de resultado](./media/hdinsight-storm-develop-csharp-visual-studio-topology/outputtype.png)

    > [AZURE.NOTE] Recuerde que debe cambiar el **tipo de resultado** de volver a la **Biblioteca de clases** antes de implementar la topología a un clúster.

2. En el **Explorador de soluciones**, haga clic en el proyecto, y luego seleccione **Agregar** > **Nuevo elemento**. Seleccione la **clase** y escriba **LocalTest.cs** como nombre de la clase. Por último, haga clic en **Agregar**.

3. Abra **LocalTest.cs** y agregue la siguiente instrucción **using** en la parte superior:

        using Microsoft.SCP;

4. Utilice la siguiente como el contenido de la clase **LocalTest** :

        // Drives the topology components
        public void RunTestCase()
        {
            // An empty dictionary for use when creating components
            Dictionary<string, Object> emptyDictionary = new Dictionary<string, object>();

            #region Test the spout
            {
                Console.WriteLine("Starting spout");
                // LocalContext is a local-mode context that can be used to initialize
                // components in the development environment.
                LocalContext spoutCtx = LocalContext.Get();
                // Get a new instance of the spout, using the local context
                Spout sentences = Spout.Get(spoutCtx, emptyDictionary);

                // Emit 10 tuples
                for (int i = 0; i < 10; i++)
                {
                    sentences.NextTuple(emptyDictionary);
                }
                // Use LocalContext to persist the data stream to file
                spoutCtx.WriteMsgQueueToFile("sentences.txt");
                Console.WriteLine("Spout finished");
            }
            #endregion

            #region Test the splitter bolt
            {
                Console.WriteLine("Starting splitter bolt");
                // LocalContext is a local-mode context that can be used to initialize
                // components in the development environment.
                LocalContext splitterCtx = LocalContext.Get();
                // Get a new instance of the bolt
                Splitter splitter = Splitter.Get(splitterCtx, emptyDictionary);


                // Set the data stream to the data created by the spout
                splitterCtx.ReadFromFileToMsgQueue("sentences.txt");
                // Get a batch of tuples from the stream
                List<SCPTuple> batch = splitterCtx.RecvFromMsgQueue();
                // Process each tuple in the batch
                foreach (SCPTuple tuple in batch)
                {
                    splitter.Execute(tuple);
                }
                // Use LocalContext to persist the data stream to file
                splitterCtx.WriteMsgQueueToFile("splitter.txt");
                Console.WriteLine("Splitter bolt finished");
            }
            #endregion

            #region Test the counter bolt
            {
                Console.WriteLine("Starting counter bolt");
                // LocalContext is a local-mode context that can be used to initialize
                // components in the development environment.
                LocalContext counterCtx = LocalContext.Get();
                // Get a new instance of the bolt
                Counter counter = Counter.Get(counterCtx, emptyDictionary);

                // Set the data stream to the data created by splitter bolt
                counterCtx.ReadFromFileToMsgQueue("splitter.txt");
                // Get a batch of tuples from the stream
                List<SCPTuple> batch = counterCtx.RecvFromMsgQueue();
                // Process each tuple in the batch
                foreach (SCPTuple tuple in batch)
                {
                    counter.Execute(tuple);
                }
                // Use LocalContext to persist the data stream to file
                counterCtx.WriteMsgQueueToFile("counter.txt");
                Console.WriteLine("Counter bolt finished");
            }
            #endregion
        }

    Dedique un momento para leer los comentarios de código. Este código usa **LocalContext** para ejecutar los componentes del entorno de desarrollo y conserva el flujo de datos entre los componentes de los archivos de texto en la unidad local.

5. Abra **Program.cs** y agregue lo siguiente al método **Main** :

        Console.WriteLine("Starting tests");
        System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-LocalTest");
        // Initialize the runtime
        SCPRuntime.Initialize();

        //If we are not running under the local context, throw an error
        if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
        {
            throw new Exception(string.Format("unexpected pluginType: {0}", Context.pluginType));
        }
        // Create test instance
        LocalTest tests = new LocalTest();
        // Run tests
        tests.RunTestCase();
        Console.WriteLine("Tests finished");
        Console.ReadKey();

6. Guardar los cambios, a continuación, haga clic en **F5** o seleccione **Depurar** > **Iniciar depuración** para iniciar el proyecto. Una ventana de consola debe aparecer y registrar estado como el progreso de las pruebas. Cuando **que haya terminado de pruebas** aparece, presione cualquier tecla para cerrar la ventana.

7. Usar **El Explorador de Windows** para localizar el directorio que contiene su proyecto, por ejemplo, **C:\Users\<suNombreDeUsuario > \Documents\Visual 2013\Projects\WordCount\WordCount Studio**. En este directorio, abrir la **Papelera**y, a continuación, haga clic en **Depurar**. Debería ver los archivos de texto que se han producido al ejecutar las pruebas: sentences.txt, counter.txt y splitter.txt. Abra cada archivo de texto e inspeccionar los datos.

    > [AZURE.NOTE] Datos de cadena se conservan como una matriz de valores decimales en estos archivos. Por ejemplo, \[[97,103,111]] en el **splitter.txt** archivo es la palabra 'y'.

Aunque probar una palabra básica contar local de aplicación es bastante fácil, que el valor real se muestra cuando tiene una topología compleja que se comunica con orígenes de datos externos o realiza análisis de datos complejos. Cuando está trabajando en un proyecto, debe establecer puntos de interrupción y recorrer el código de los componentes a aislar los problemas.

> [AZURE.NOTE] No olvide volver a establecer el **tipo de proyecto** en **Biblioteca de clases** antes de implementar para una tormenta en clúster HDInsight.

### <a name="log-information"></a>Información de registro

Fácilmente puede registrar información de los componentes de topología usando `Context.Logger`. Por ejemplo, el siguiente creará una entrada de registro de información:

    Context.Logger.Info("Component started");

Información registrada puede verse desde el **Registro del servicio de Hadoop**, que se encuentra en el **Explorador de servidores**. Expanda la entrada para su tormenta en clúster HDInsight y expanda **El registro del servicio de Hadoop**. Por último, seleccione el archivo de registro para ver.

> [AZURE.NOTE] Los registros se almacenan en la cuenta de almacenamiento de Azure que se usa en el clúster. Si se trata de una suscripción diferente a la que está conectado a con Visual Studio, debe iniciar sesión en la suscripción que contiene la cuenta de almacenamiento para ver esta información.

### <a name="view-error-information"></a>Ver información de error

Para ver los errores que se han producido en una topología de ejecución, siga estos pasos:

1. Desde el **Explorador de servidores**, haga la tormenta en clúster HDInsight y seleccione **vista tormenta topologías**.

2. Para los **chorros** y **pernos**, la columna **Último Error** tendrán información en el último error que se ha producido.

3. Seleccione el **Identificador de chorros** o **Pernos** para el componente que tiene un error que aparece. En la página de detalles que se muestra, se mostrará información adicional sobre errores en la sección de **errores** en la parte inferior de la página.

4. Para obtener más información, seleccione un **puerto** de la sección **elementos de ejecución** de la página para ver el registro de trabajo de tormenta de los últimos minutos.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido desarrollar e implementar topologías de tormenta de las herramientas de HDInsight para Visual Studio, obtenga información sobre cómo [procesar los eventos de concentrador de evento de Azure con tormenta en HDInsight](hdinsight-storm-develop-csharp-event-hub-topology.md).

Para obtener un ejemplo de una topología de C# que divide los datos de la secuencia en varias secuencias, vea [ejemplo de tormenta C#](https://github.com/Blackmist/csharp-storm-example).

Para obtener más información sobre la creación de C# topologías de descubrir, visite [SCP.NET GettingStarted.md](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/SCPNet-GettingStarted.md).

Para obtener más formas de trabajar con HDInsight y tormenta más muestras HDinsight, consulte lo siguiente:

**Microsoft SCP.NET**

* [Guía de programación de SCP](hdinsight-storm-scp-programming-guide.md)

**Tormenta Apache en HDInsight**

- [Implementar y supervisar topologías con Apache tormenta en HDInsight](hdinsight-storm-deploy-monitor-topology.md)

- [Topologías de ejemplo para tormenta en HDInsight](hdinsight-storm-example-topology.md)

**Apache Hadoop en HDInsight**

- [Usar subárbol con Hadoop en HDInsight](hdinsight-use-hive.md)

- [Usar cerdo con Hadoop en HDInsight](hdinsight-use-pig.md)

- [Usar MapReduce con Hadoop en HDInsight](hdinsight-use-mapreduce.md)

**Apache HBase en HDInsight**

- [Introducción a HBase en HDInsight](hdinsight-hbase-tutorial-get-started.md)

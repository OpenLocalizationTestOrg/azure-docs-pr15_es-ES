<properties
    pageTitle="Serializar datos con Microsoft Avro Library | Microsoft Azure"
    description="Obtenga información sobre cómo HDInsight de Azure usa Avro serializar datos grandes."
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian" 
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/14/2016"
    ms.author="jgao"/>


# <a name="serialize-data-in-hadoop-with-the-microsoft-avro-library"></a>Serializar datos en Hadoop con Microsoft Avro Library

Este tema muestra cómo usar la <a href="https://hadoopsdk.codeplex.com/wikipage?title=Avro%20Library" target="_blank">Biblioteca de Microsoft Avro</a> para serializar objetos y otras estructuras de datos en secuencias para guardarlos en memoria, una base de datos o un archivo y también cómo deserializar para recuperar los objetos originales.

[AZURE.INCLUDE [windows-only](../../includes/hdinsight-windows-only.md)]

##<a name="apache-avro"></a>Apache Avro
La <a href="https://hadoopsdk.codeplex.com/wikipage?title=Avro%20Library" target="_blank">Biblioteca de Microsoft Avro</a> implementa el sistema de serialización Apache Avro datos para el entorno de Microsoft.NET. Apache Avro proporciona un formato de intercambio de datos binarios compacta para serialización. Utiliza <a href="http://www.json.org" target="_blank">JSON</a> para definir un esquema independiente del idioma que emite interoperabilidad de idioma. Datos de serie en un idioma se pueden leer en otro. Actualmente se admiten C, C++, C#, Java, PHP, Python y Ruby. Encontrará información detallada sobre el formato de la <a href="http://avro.apache.org/docs/current/spec.html" target="_blank">Especificación de Avro Apache</a>. Tenga en cuenta que la versión actual de Microsoft Avro Library no admite la parte de llamadas (RPC) de procedimiento remoto de esta especificación.

La representación de número de serie de un objeto en el sistema de Avro consta de dos partes: esquema y el valor real. El esquema de Avro describe el modelo de datos independiente del idioma de los datos de serie con JSON. Es presentar en paralelo con una representación binaria de datos. Tener el esquema independiente de la representación binaria permite cada objeto escribirse con sin gastos por valor, realizar serialización rápida y la representación pequeña.

##<a name="the-hadoop-scenario"></a>El escenario de Hadoop
El formato de serialización Avro Apache se usa ampliamente en otros entornos de Apache Hadoop y HDInsight de Azure. Avro proporciona una forma conveniente para representar estructuras de datos complejos dentro de una tarea Hadoop MapReduce. El formato de archivos de Avro (archivo de contenedor de objeto de Avro) se ha diseñado para admitir el modelo de programación MapReduce distribuido. La función clave que permite la distribución es que los archivos "splittable" en el sentido que uno puede buscar cualquier punto de un archivo y empezar a leer desde un bloque en particular.

##<a name="serialization-in-avro-library"></a>Serialización de biblioteca Avro
La biblioteca de .NET Avro es compatible con dos maneras de serializar objetos:

- se generan automáticamente **reflexión** - esquema de la JSON para los tipos de los datos de atributos de contrato de los tipos de .NET para ser serie.
- **registro genérico** - esquema A JSON se especifica explícitamente en un registro representado por la clase [**AvroRecord**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx) cuando no hay tipos .NET están presentes para describir el esquema de los datos que se serie.

Cuando se conoce el esquema de datos para el sistema de escritura y el lector de la secuencia, los datos se pueden enviar sin su esquema. En casos cuando se utiliza un archivo de contenedor de objeto de Avro, el esquema se almacena en el archivo. Pueden especificar otros parámetros, como el códec utilizado para la compresión de datos. Estos escenarios figuran en más detalle y se muestra en los ejemplos de código siguientes.


## <a name="install-avro-library"></a>Instalar biblioteca Avro

Se requiere lo siguiente antes de instalar la biblioteca:

- <a href="http://www.microsoft.com/download/details.aspx?id=17851" target="_blank">Microsoft .NET Framework 4</a>
- <a href="http://james.newtonking.com/json" target="_blank">Newtonsoft Json.NET</a> (6.0.4 o posterior)

Tenga en cuenta que la dependencia Newtonsoft.Json.dll se descarga automáticamente con la instalación de Microsoft Avro Library. El procedimiento está disponible en la sección siguiente.


Microsoft Avro Library se distribuye como un paquete de NuGet que se puede instalar desde Visual Studio mediante el procedimiento siguiente:

1. Seleccione la pestaña **proyecto** -> **Administrar paquetes de NuGet...**
2. Busque "Microsoft.Hadoop.Avro" en el cuadro de **Búsqueda en línea** .
3. Haga clic en el botón **instalar** junto a la **Biblioteca de Microsoft Azure HDInsight Avro**.

Tenga en cuenta que el Newtonsoft.Json.dll (> = 6.0.4) dependencia también se descarga automáticamente con Microsoft Avro Library.

Desea visite la <a href="https://hadoopsdk.codeplex.com/wikipage?title=Avro%20Library" target="_blank">página de inicio de la biblioteca de Avro de Microsoft</a> para leer las notas de la versión actual.


El código fuente de Microsoft Avro Library está disponible en la <a href="https://hadoopsdk.codeplex.com/wikipage?title=Avro%20Library" target="_blank">página de inicio de la biblioteca de Avro de Microsoft</a>.

##<a name="compile-schemas-using-avro-library"></a>Compilar con Avro biblioteca de esquemas

Microsoft Avro Library contiene una utilidad de generación de código que permite crear tipos de C# automáticamente basados en el esquema JSON definido anteriormente. La herramienta de generación de código no se distribuye como un archivo ejecutable binario, pero se puede crear fácilmente mediante el procedimiento siguiente:

1. Descargue el archivo .zip con la última versión de código fuente de HDInsight SDK de <a href="http://hadoopsdk.codeplex.com/SourceControl/latest#" target="_blank">Microsoft .NET SDK para Hadoop</a>. (Haga clic en el icono de **descarga** , no en la ficha **descargas** ).

2. Extraer el SDK HDInsight en un directorio en el equipo con .NET Framework 4 instalado y conectado a Internet para descargar los paquetes de NuGet dependencia necesarios. A continuación consideraremos que el código fuente se extrae a C:\SDK.

3. Vaya a la carpeta C:\SDK\src\Microsoft.Hadoop.Avro.Tools y ejecutar build.bat. (El archivo llamará MSBuild desde la distribución de 32 bits de .NET Framework. Si desea usar la versión de 64 bits, editar build.bat, siguiendo los comentarios dentro del archivo). Asegúrese de que la compilación es correcta. (En algunos sistemas, MSBuild puede producir advertencias. Estas advertencias no afecta a la utilidad siempre y cuando no hay ningún error de generación).

4. La utilidad compilada se encuentra en C:\SDK\Bin\Unsigned\Release\Microsoft.Hadoop.Avro.Tools.


Para familiarizarse con la sintaxis de la línea de comandos, ejecute el siguiente comando de la carpeta donde se encuentra la utilidad de generación de código:`Microsoft.Hadoop.Avro.Tools help /c:codegen`

Para probar la utilidad, puede generar clases de C# del archivo de esquema JSON ejemplo proporcionado con el código fuente. Ejecute el siguiente comando:

    Microsoft.Hadoop.Avro.Tools codegen /i:C:\SDK\src\Microsoft.Hadoop.Avro.Tools\SampleJSON\SampleJSONSchema.avsc /o:

Esto se supone que produce dos C# archivos en el directorio actual: SensorData.cs y Location.cs.

Para conocer la lógica que esté usando la herramienta de generación de código al convertir el esquema JSON en tipos de C#, vea el archivo que generationverification.Feature ubicado en C:\SDK\src\Microsoft.Hadoop.Avro.Tools\Doc.

Tenga en cuenta que se extraen los espacios de nombres del esquema de JSON, utilizando la lógica que se describe en el archivo mencionado en el párrafo anterior. Espacios de nombres extraídas del esquema tienen prioridad sobre lo que se proporciona con el parámetro /n de la línea de comandos de utilidad. Si desea reemplazar los espacios de nombres contenidos en el esquema, use el parámetro /nf. Por ejemplo, para cambiar todos los espacios de nombres de la SampleJSONSchema.avsc a my.own.nspace, ejecute el siguiente comando:

    Microsoft.Hadoop.Avro.Tools codegen /i:C:\SDK\src\Microsoft.Hadoop.Avro.Tools\SampleJSON\SampleJSONSchema.avsc /o:. /nf:my.own.nspace

## <a name="samples"></a>Ejemplos
Seis ejemplos incluidos en este tema muestran diferentes escenarios compatibles con Microsoft Avro Library. Microsoft Avro Library está diseñado para funcionar con cualquier secuencia. En estos ejemplos, se manipulan datos a través de las secuencias de memoria en lugar de secuencias de archivos o bases de datos para simplificar y coherencia. El enfoque adoptado en un entorno de producción dependerá de los requisitos de escenario exacta, origen de datos y volumen, las restricciones de rendimiento y otros factores.

Los dos primeros ejemplos muestran cómo serializar y deserializar datos en búferes de secuencia de memoria mediante reflexión y registros genéricos. El esquema en estos dos casos se supone que deben compartirse entre los lectores y sistemas de escritura de banda.

Los ejemplos de terceros y cuarto muestran cómo serializar y deserializar datos mediante los archivos de contenedor de objeto de Avro. Cuando los datos se almacenan en un archivo de contenedor Avro, su esquema siempre se almacena con él porque el esquema debe estar compartido para la deserialización.

En el ejemplo que contiene los cuatro primeros ejemplos puede descargarse desde el sitio de <a href="http://code.msdn.microsoft.com/windowsazure/Serialize-data-with-the-86055923" target="_blank">ejemplos de código de Azure</a> .

El quinto ejemplo se muestra cómo usar un códec de compresión personalizada para archivos de contenedor de objeto de Avro. Un ejemplo que contiene el código de este ejemplo puede descargarse desde el sitio de <a href="http://code.msdn.microsoft.com/windowsazure/Serialize-data-with-the-67159111" target="_blank">ejemplos de código de Azure</a> .

Sexta se muestra cómo usar serialización Avro para cargar los datos con el almacenamiento de blobs de Windows Azure y, a continuación, analice con subárbol con un clúster de HDInsight (Hadoop). Puede descargarse desde el sitio de <a href="https://code.msdn.microsoft.com/windowsazure/Using-Avro-to-upload-data-ae81b1e3" target="_blank">ejemplos de código de Azure</a> .

Estos son los vínculos a los ejemplos de seis analizados en el tema:

 * Se generan automáticamente <a href="#Scenario1">**serialización con reflejos**</a> - esquema de la JSON para tipos de ser serie de los datos de atributos del contrato.
 * <a href="#Scenario2">**Serialización con registro genérico**</a> - esquema el JSON se especifica explícitamente en un registro de ningún tipo de .NET está disponible para reflexión.
 * <a href="#Scenario3">**Serialización con archivos de contenedor de objeto con reflejos**</a> - esquema JSON el integrado y compartido junto con los datos de serie a través de un archivo de contenedor de objeto de Avro automáticamente.
 * <a href="#Scenario4">**Serialización con archivos de contenedor de objeto con registro genérico**</a> - esquema JSON el explícitamente es especificado antes de la serialización y compartido junto con los datos a través de un archivo de contenedor de objeto de Avro.
 * <a href="#Scenario5">**Serialización con archivos de contenedor de objeto con un códec de compresión personalizada**</a> - en el ejemplo se muestra cómo crear un archivo de contenedor de objeto de Avro con una implementación de .NET personalizada del códec de compresión de datos Deflate.
 * <a href="#Scenario6">**Usar Avro para cargar los datos para el servicio de Microsoft Azure HDInsight**</a> - el ejemplo se muestra cómo Avro serialización interactúa con el servicio de HDInsight. Una suscripción activa a Azure y el acceso a un clúster de Azure HDInsight son necesarios para ejecutar este ejemplo.

###<a name="Scenario1"></a>Ejemplo 1: Serialización con reflejos

El esquema JSON para los tipos de puede crearse automáticamente por Microsoft Avro Library a través de reflexión de los datos de atributos de contrato de los objetos C# serializarse. Microsoft Avro Library crea una [**IAvroSeralizer<T> **](http://msdn.microsoft.com/library/dn627341.aspx) para identificar los campos que se serie.

En este ejemplo, se serializan objetos (una clase **SensorData** con una estructura de **ubicación** de miembro) en una secuencia de memoria y, a continuación, esta secuencia se deserializa por separado. El resultado se compara con la instancia inicial para confirmar que el objeto de **SensorData** recuperado es idéntico al original.

El esquema en este ejemplo se supone que deben compartirse entre los lectores y sistemas de escritura, para que el formato de contenedor de objeto de Avro no es necesario. Para obtener un ejemplo de cómo serializar y deserializar datos en búferes de memoria mediante la reflexión con el formato de contenedor de objeto cuando el esquema debe compartirse con los datos, vea <a href="#Scenario3">serialización con archivos de contenedor de objeto con reflejos</a>.

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro;

        //Sample class used in serialization samples
        [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
        internal class SensorData
        {
            [DataMember(Name = "Location")]
            public Location Position { get; set; }

            [DataMember(Name = "Value")]
            public byte[] Value { get; set; }
        }

        //Sample struct used in serialization samples
        [DataContract]
        internal struct Location
        {
            [DataMember]
            public int Floor { get; set; }

            [DataMember]
            public int Room { get; set; }
        }

        //This class contains all methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serialize and deserialize sample data set represented as an object using reflection.
            //No explicit schema definition is required - schema of serialized objects is automatically built.
            public void SerializeDeserializeObjectUsingReflection()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION\n");
                Console.WriteLine("Serializing Sample Data Set...");

                //Create a new AvroSerializer instance and specify a custom serialization strategy AvroDataContractResolver
                //for serializing only properties attributed with DataContract/DateMember
                var avroSerializer = AvroSerializer.Create<SensorData>();

                //Create a memory stream buffer
                using (var buffer = new MemoryStream())
                {
                    //Create a data set by using sample class and struct
                    var expected = new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } };

                    //Serialize the data to the specified stream
                    avroSerializer.Serialize(buffer, expected);


                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Deserialize data from the stream and cast it to the same type used for serialization
                    var actual = avroSerializer.Deserialize(buffer);

                    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

                    //Finally, verify that deserialized data matches the original one
                    bool isEqual = this.Equal(expected, actual);

                    Console.WriteLine("Result of Data Set Identity Comparison is {0}", isEqual);

                }
            }

            //
            //Helper methods
            //

            //Comparing two SensorData objects
            private bool Equal(SensorData left, SensorData right)
            {
                return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
            }



            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample Class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization to memory using reflection
                Sample.SerializeDeserializeObjectUsingReflection();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION
    //
    // Serializing Sample Data Set...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // Result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.


###<a name="sample-2-serialization-with-a-generic-record"></a>Ejemplo 2: Serialización con un registro genérico

Un esquema JSON puede especificarse explícitamente en un registro genérico cuando no se puede utilizar el reflejo porque no se puede representar los datos a través de clases de .NET con un contrato de datos. Este método es generalmente más lento que utilizar reflexión. En estos casos, el esquema de los datos también pueden dinámicos, es decir, no se conoce en tiempo de compilación. Datos representados como archivos de valores separados por comas (CSV) cuyo esquema se desconoce hasta que se transforma en el formato de Avro en tiempo de ejecución están un ejemplo de este tipo de escenario dinámico.

Este ejemplo muestra cómo crear y usar un [**AvroRecord**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx) para especificar explícitamente un esquema JSON, cómo rellenar con los datos y, a continuación, cómo serializar y deserializarla. El resultado se compara con la instancia inicial para confirmar que el registro recuperado es idéntico al original.

El esquema en este ejemplo se supone que deben compartirse entre los lectores y sistemas de escritura, para que el formato de contenedor de objeto de Avro no es necesario. Para obtener un ejemplo de cómo serializar y deserializar datos en búferes de memoria usando un registro genérico con el formato de contenedor de objeto cuando el esquema debe incluir con los datos de serie, vea el ejemplo de <a href="#Scenario4">serialización mediante archivos de contenedor de objeto con registro genérico</a> .


    namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;
    using Microsoft.Hadoop.Avro.Schema;
    using Microsoft.Hadoop.Avro;

    //This class contains all methods demonstrating
    //the usage of Microsoft Avro Library
    public class AvroSample
    {

        //Serialize and deserialize sample data set by using a generic record.
        //A generic record is a special class with the schema explicitly defined in JSON.
        //All serialized data should be mapped to the fields of the generic record,
        //which in turn will be then serialized.
        public void SerializeDeserializeObjectUsingGenericRecords()
        {
            Console.WriteLine("SERIALIZATION USING GENERIC RECORD\n");
            Console.WriteLine("Defining the Schema and creating Sample Data Set...");

            //Define the schema in JSON
            const string Schema = @"{
                                ""type"":""record"",
                                ""name"":""Microsoft.Hadoop.Avro.Specifications.SensorData"",
                                ""fields"":
                                    [
                                        {
                                            ""name"":""Location"",
                                            ""type"":
                                                {
                                                    ""type"":""record"",
                                                    ""name"":""Microsoft.Hadoop.Avro.Specifications.Location"",
                                                    ""fields"":
                                                        [
                                                            { ""name"":""Floor"", ""type"":""int"" },
                                                            { ""name"":""Room"", ""type"":""int"" }
                                                        ]
                                                }
                                        },
                                        { ""name"":""Value"", ""type"":""bytes"" }
                                    ]
                            }";

            //Create a generic serializer based on the schema
            var serializer = AvroSerializer.CreateGeneric(Schema);
            var rootSchema = serializer.WriterSchema as RecordSchema;

            //Create a memory stream buffer
            using (var stream = new MemoryStream())
            {
                //Create a generic record to represent the data
                dynamic location = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
                location.Floor = 1;
                location.Room = 243;

                dynamic expected = new AvroRecord(serializer.WriterSchema);
                expected.Location = location;
                expected.Value = new byte[] { 1, 2, 3, 4, 5 };

                Console.WriteLine("Serializing Sample Data Set...");

                //Serialize the data
                serializer.Serialize(stream, expected);

                stream.Seek(0, SeekOrigin.Begin);

                Console.WriteLine("Deserializing Sample Data Set...");

                //Deserialize the data into a generic record
                dynamic actual = serializer.Deserialize(stream);

                Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

                //Finally, verify the results
                bool isEqual = expected.Location.Floor.Equals(actual.Location.Floor);
                isEqual = isEqual && expected.Location.Room.Equals(actual.Location.Room);
                isEqual = isEqual && ((byte[])expected.Value).SequenceEqual((byte[])actual.Value);
                Console.WriteLine("Result of Data Set Identity Comparison is {0}", isEqual);
            }
        }

        static void Main()
        {

            string sectionDivider = "---------------------------------------- ";

            //Create an instance of AvroSample class and invoke methods
            //illustrating different serializing approaches
            AvroSample Sample = new AvroSample();

            //Serialization to memory using generic record
            Sample.SerializeDeserializeObjectUsingGenericRecords();

            Console.WriteLine(sectionDivider);
            Console.WriteLine("Press any key to exit.");
            Console.Read();
        }
    }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING GENERIC RECORD
    //
    // Defining the Schema and creating Sample Data Set...
    // Serializing Sample Data Set...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // Result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.


###<a name="sample-3-serialization-using-object-container-files-and-serialization-with-reflection"></a>Ejemplo 3: Serialización usar archivos de contenedor de objeto y serialización con reflejos

En este ejemplo es similar al escenario en el <a href="#Scenario1">primer ejemplo</a>, donde se especifica el esquema de forma implícita con reflejos. La diferencia es que aquí, el esquema no se supone que se conoce con el lector que lo deserializa. Los objetos **SensorData** serializarse y su esquema implícitamente especificado se almacenan en un archivo de contenedor de objeto de Avro representado por la clase [**AvroContainer**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.container.avrocontainer.aspx) .

Los datos se serializan en este ejemplo con [**SequentialWriter<SensorData> **](http://msdn.microsoft.com/library/dn627340.aspx) y deserializados con [**SequentialReader<SensorData>**](http://msdn.microsoft.com/library/dn627340.aspx). A continuación, el resultado se compara con las instancias iniciales para garantizar la identidad.

Los datos en el archivo del contenedor de objeto está comprimidos mediante el valor predeterminado [**Deflate**] [ deflate-100] códec de compresión de .NET Framework 4. Vea el <a href="#Scenario5">quinto ejemplo</a> en este tema para aprender a usar una versión más reciente y superior de la [**Deflate**] [ deflate-110] códec de compresión disponible en .NET Framework 4.5.

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro;

        //Sample class used in serialization samples
        [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
        internal class SensorData
        {
            [DataMember(Name = "Location")]
            public Location Position { get; set; }

            [DataMember(Name = "Value")]
            public byte[] Value { get; set; }
        }

        //Sample struct used in serialization samples
        [DataContract]
        internal struct Location
        {
            [DataMember]
            public int Floor { get; set; }

            [DataMember]
            public int Room { get; set; }
        }

        //This class contains all methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serializes and deserializes the sample data set by using reflection and Avro object container files.
            //Serialized data is compressed with the Deflate codec.
            public void SerializeDeserializeUsingObjectContainersReflection()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION AND AVRO OBJECT CONTAINER FILES\n");

                //Path for Avro object container file
                string path = "AvroSampleReflectionDeflate.avro";

                //Create a data set by using sample class and struct
                var testData = new List<SensorData>
                        {
                            new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
                            new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
                        };

                //Serializing and saving data to file.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData, which can serialize a sequence of SensorData objects to stream.
                    //Data will be compressed using the Deflate codec.
                    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
                    {
                        using (var writer = new SequentialWriter<SensorData>(w, 24))
                        {
                            // Serialize the data to stream by using the sequential writer
                            testData.ForEach(writer.Write);
                        }
                    }

                    //Save stream to file
                    Console.WriteLine("Saving serialized data to file...");
                    if (!WriteFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }
                }

                //Reading and deserializing data.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from object container file
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Create a SequentialReader instance for type SensorData, which will deserialize all serialized objects from the given stream.
                    //It allows iterating over the deserialized objects because it implements the IEnumerable<T> interface.
                    using (var reader = new SequentialReader<SensorData>(
                        AvroContainer.CreateReader<SensorData>(buffer, true)))
                    {
                        var results = reader.Objects;

                        //Finally, verify that deserialized data matches the original one
                        Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
                        int count = 1;
                        var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
                        foreach (var pair in pairs)
                        {
                            bool isEqual = this.Equal(pair.expected, pair.actual);
                            Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual);
                            count++;
                        }
                    }
                }

                //Delete the file
                RemoveFile(path);
            }

            //
            //Helper methods
            //

            //Comparing two SensorData objects
            private bool Equal(SensorData left, SensorData right)
            {
                return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
            }

            //Saving memory stream to a new file with the given path
            private bool WriteFile(MemoryStream InputStream, string path)
            {
                if (!File.Exists(path))
                {
                    try
                    {
                        using (FileStream fs = File.Create(path))
                        {
                            InputStream.Seek(0, SeekOrigin.Begin);
                            InputStream.CopyTo(fs);
                        }
                        return true;
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                        return false;
                    }
                }
                else
                {
                    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
                    return false;

                }
            }

            //Reading a file content by using the given path to a memory stream
            private bool ReadFile(MemoryStream OutputStream, string path)
            {
                try
                {
                    using (FileStream fs = File.Open(path, FileMode.Open))
                    {
                        fs.CopyTo(OutputStream);
                    }
                    return true;
                }
                catch (Exception e)
                {
                    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
                    Console.WriteLine(e.Message);
                    return false;
                }
            }

            //Deleting file by using given path
            private void RemoveFile(string path)
            {
                if (File.Exists(path))
                {
                    try
                    {
                        File.Delete(path);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                    }
                }
                else
                {
                    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
                }
            }

            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using reflection to Avro object container file
                Sample.SerializeDeserializeUsingObjectContainersReflection();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION AND AVRO OBJECT CONTAINER FILES
    //
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    // For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.


###<a name="sample-4-serialization-using-object-container-files-and-serialization-with-generic-record"></a>Ejemplo 4: Serialización usar archivos de contenedor de objeto y serialización con registro genérico

En este ejemplo es similar al escenario en el <a href="#Scenario2">segundo ejemplo</a>, en la que se especifica explícitamente el esquema con JSON. La diferencia es que aquí, el esquema no se supone que se conoce con el lector que lo deserializa.

El conjunto de datos de prueba recopila en una lista de objetos de [**AvroRecord**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx) a través de un esquema JSON definido explícitamente y, a continuación, se almacenan en un archivo de contenedor de objeto representado por la clase [**AvroContainer**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.container.avrocontainer.aspx) . Este archivo contenedor crea un escritor que se utiliza para serializar los datos, sin comprimir en una secuencia de memoria, a continuación, se guarda en un archivo. El parámetro [**Codec.Null**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.container.codec.null.aspx) para crear el lector especifica que los datos no serán comprimidos.

Los datos, a continuación, se lee desde el archivo y deserializarse en una colección de objetos. Esta colección se compara con la lista inicial de registros Avro para confirmar que son idénticos.


    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro.Schema;
        using Microsoft.Hadoop.Avro;

        //This class contains all methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serializes and deserializes a sample data set by using a generic record and Avro object container files.
            //Serialized data is not compressed.
            public void SerializeDeserializeUsingObjectContainersGenericRecord()
            {
                Console.WriteLine("SERIALIZATION USING GENERIC RECORD AND AVRO OBJECT CONTAINER FILES\n");

                //Path for Avro object container file
                string path = "AvroSampleGenericRecordNullCodec.avro";

                Console.WriteLine("Defining the Schema and creating Sample Data Set...");

                //Define the schema in JSON
                const string Schema = @"{
                                ""type"":""record"",
                                ""name"":""Microsoft.Hadoop.Avro.Specifications.SensorData"",
                                ""fields"":
                                    [
                                        {
                                            ""name"":""Location"",
                                            ""type"":
                                                {
                                                    ""type"":""record"",
                                                    ""name"":""Microsoft.Hadoop.Avro.Specifications.Location"",
                                                    ""fields"":
                                                        [
                                                            { ""name"":""Floor"", ""type"":""int"" },
                                                            { ""name"":""Room"", ""type"":""int"" }
                                                        ]
                                                }
                                        },
                                        { ""name"":""Value"", ""type"":""bytes"" }
                                    ]
                            }";

                //Create a generic serializer based on the schema
                var serializer = AvroSerializer.CreateGeneric(Schema);
                var rootSchema = serializer.WriterSchema as RecordSchema;

                //Create a generic record to represent the data
                var testData = new List<AvroRecord>();

                dynamic expected1 = new AvroRecord(rootSchema);
                dynamic location1 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
                location1.Floor = 1;
                location1.Room = 243;
                expected1.Location = location1;
                expected1.Value = new byte[] { 1, 2, 3, 4, 5 };
                testData.Add(expected1);

                dynamic expected2 = new AvroRecord(rootSchema);
                dynamic location2 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
                location2.Floor = 1;
                location2.Room = 244;
                expected2.Location = location2;
                expected2.Value = new byte[] { 6, 7, 8, 9 };
                testData.Add(expected2);

                //Serializing and saving data to file.
                //Create a MemoryStream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData, which can serialize a sequence of SensorData objects to stream.
                    //Data will not be compressed (Null compression codec).
                    using (var writer = AvroContainer.CreateGenericWriter(Schema, buffer, Codec.Null))
                    {
                        using (var streamWriter = new SequentialWriter<object>(writer, 24))
                        {
                            // Serialize the data to stream by using the sequential writer
                            testData.ForEach(streamWriter.Write);
                        }
                    }

                    Console.WriteLine("Saving serialized data to file...");

                    //Save stream to file
                    if (!WriteFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }
                }

                //Reading and deserializing the data.
                //Create a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from object container file
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Create a SequentialReader instance for type SensorData, which will deserialize all serialized objects from the given stream.
                    //It allows iterating over the deserialized objects because it implements the IEnumerable<T> interface.
                    using (var reader = AvroContainer.CreateGenericReader(buffer))
                    {
                        using (var streamReader = new SequentialReader<object>(reader))
                        {
                            var results = streamReader.Objects;

                            Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

                            //Finally, verify the results
                            var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = (dynamic)serialized, actual = (dynamic)deserialized });
                            int count = 1;
                            foreach (var pair in pairs)
                            {
                                bool isEqual = pair.expected.Location.Floor.Equals(pair.actual.Location.Floor);
                                isEqual = isEqual && pair.expected.Location.Room.Equals(pair.actual.Location.Room);
                                isEqual = isEqual && ((byte[])pair.expected.Value).SequenceEqual((byte[])pair.actual.Value);
                                Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
                                count++;
                            }
                        }
                    }
                }

                //Delete the file
                RemoveFile(path);
            }

            //
            //Helper methods
            //

            //Saving memory stream to a new file with the given path
            private bool WriteFile(MemoryStream InputStream, string path)
            {
                if (!File.Exists(path))
                {
                    try
                    {
                        using (FileStream fs = File.Create(path))
                        {
                            InputStream.Seek(0, SeekOrigin.Begin);
                            InputStream.CopyTo(fs);
                        }
                        return true;
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                        return false;
                    }
                }
                else
                {
                    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
                    return false;

                }
            }

            //Reading a file content by using the given path to a memory stream
            private bool ReadFile(MemoryStream OutputStream, string path)
            {
                try
                {
                    using (FileStream fs = File.Open(path, FileMode.Open))
                    {
                        fs.CopyTo(OutputStream);
                    }
                    return true;
                }
                catch (Exception e)
                {
                    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
                    Console.WriteLine(e.Message);
                    return false;
                }
            }

            //Deleting file by using the given path
            private void RemoveFile(string path)
            {
                if (File.Exists(path))
                {
                    try
                    {
                        File.Delete(path);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                    }
                }
                else
                {
                    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
                }
            }

            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of the AvroSample class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using generic record to Avro object container file
                Sample.SerializeDeserializeUsingObjectContainersGenericRecord();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING GENERIC RECORD AND AVRO OBJECT CONTAINER FILES
    //
    // Defining the Schema and creating Sample Data Set...
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    // For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.




###<a name="sample-5-serialization-using-object-container-files-with-a-custom-compression-codec"></a>Ejemplo 5: Serialización usar archivos de contenedor de objeto con un códec de compresión personalizada

El quinto ejemplo se muestra cómo usar un códec de compresión personalizada para archivos de contenedor de objeto de Avro. Un ejemplo que contiene el código de este ejemplo puede descargarse desde el sitio de [ejemplos de código de Azure](http://code.msdn.microsoft.com/windowsazure/Serialize-data-with-the-67159111) .

La [Especificación de Avro](http://avro.apache.org/docs/current/spec.html#Required+Codecs) permite el uso de un códec de compresión opcional (además de **Null** y **Deflate** de forma predeterminada). Este ejemplo está implementando un códec completamente nuevo como Snappy (mencionado como un códec opcional admitido en la [Especificación de Avro](http://avro.apache.org/docs/current/spec.html#snappy)). Muestra cómo usar la implementación de .NET Framework 4.5 de la [**Deflate**] [ deflate-110] códec, que proporciona un algoritmo de compresión mejor en función de la biblioteca de compresión [zlib](http://zlib.net/) que la versión de .NET Framework 4 de forma predeterminada.


    //
    // This code needs to be compiled with the parameter Target Framework set as ".NET Framework 4.5"
    // to ensure the desired implementation of the Deflate compression algorithm is used.
    // Ensure your C# project is set up accordingly.
    //

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.Diagnostics;
        using System.IO;
        using System.IO.Compression;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro;

        #region Defining objects for serialization
        //Sample class used in serialization samples
        [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
        internal class SensorData
        {
            [DataMember(Name = "Location")]
            public Location Position { get; set; }

            [DataMember(Name = "Value")]
            public byte[] Value { get; set; }
        }

        //Sample struct used in serialization samples
        [DataContract]
        internal struct Location
        {
            [DataMember]
            public int Floor { get; set; }

            [DataMember]
            public int Room { get; set; }
        }
        #endregion

        #region Defining custom codec based on .NET Framework V.4.5 Deflate
        //Avro.NET codec class contains two methods,
        //GetCompressedStreamOver(Stream uncompressed) and GetDecompressedStreamOver(Stream compressed),
        //which are the key ones for data compression.
        //To enable a custom codec, one needs to implement these methods for the required codec.

        #region Defining Compression and Decompression Streams
        //DeflateStream (class from System.IO.Compression namespace that implements Deflate algorithm)
        //cannot be directly used for Avro because it does not support vital operations like Seek.
        //Thus one needs to implement two classes inherited from stream
        //(one for compressed and one for decompressed stream)
        //that use Deflate compression and implement all required features.
        internal sealed class CompressionStreamDeflate45 : Stream
        {
            private readonly Stream buffer;
            private DeflateStream compressionStream;

            public CompressionStreamDeflate45(Stream buffer)
            {
                Debug.Assert(buffer != null, "Buffer is not allowed to be null.");

                this.compressionStream = new DeflateStream(buffer, CompressionLevel.Fastest, true);
                this.buffer = buffer;
            }

            public override bool CanRead
            {
                get { return this.buffer.CanRead; }
            }

            public override bool CanSeek
            {
                get { return true; }
            }

            public override bool CanWrite
            {
                get { return this.buffer.CanWrite; }
            }

            public override void Flush()
            {
                this.compressionStream.Close();
            }

            public override long Length
            {
                get { return this.buffer.Length; }
            }

            public override long Position
            {
                get
                {
                    return this.buffer.Position;
                }

                set
                {
                    this.buffer.Position = value;
                }
            }

            public override int Read(byte[] buffer, int offset, int count)
            {
                return this.buffer.Read(buffer, offset, count);
            }

            public override long Seek(long offset, SeekOrigin origin)
            {
                return this.buffer.Seek(offset, origin);
            }

            public override void SetLength(long value)
            {
                throw new NotSupportedException();
            }

            public override void Write(byte[] buffer, int offset, int count)
            {
                this.compressionStream.Write(buffer, offset, count);
            }

            protected override void Dispose(bool disposed)
            {
                base.Dispose(disposed);

                if (disposed)
                {
                    this.compressionStream.Dispose();
                    this.compressionStream = null;
                }
            }
        }

        internal sealed class DecompressionStreamDeflate45 : Stream
        {
            private readonly DeflateStream decompressed;

            public DecompressionStreamDeflate45(Stream compressed)
            {
                this.decompressed = new DeflateStream(compressed, CompressionMode.Decompress, true);
            }

            public override bool CanRead
            {
                get { return true; }
            }

            public override bool CanSeek
            {
                get { return true; }
            }

            public override bool CanWrite
            {
                get { return false; }
            }

            public override void Flush()
            {
                this.decompressed.Close();
            }

            public override long Length
            {
                get { return this.decompressed.Length; }
            }

            public override long Position
            {
                get
                {
                    return this.decompressed.Position;
                }

                set
                {
                    throw new NotSupportedException();
                }
            }

            public override int Read(byte[] buffer, int offset, int count)
            {
                return this.decompressed.Read(buffer, offset, count);
            }

            public override long Seek(long offset, SeekOrigin origin)
            {
                throw new NotSupportedException();
            }

            public override void SetLength(long value)
            {
                throw new NotSupportedException();
            }

            public override void Write(byte[] buffer, int offset, int count)
            {
                throw new NotSupportedException();
            }

            protected override void Dispose(bool disposing)
            {
                base.Dispose(disposing);

                if (disposing)
                {
                    this.decompressed.Dispose();
                }
            }
        }
        #endregion

        #region Define Codec
        //Define the actual codec class containing the required methods for manipulating streams:
        //GetCompressedStreamOver(Stream uncompressed) and GetDecompressedStreamOver(Stream compressed).
        //Codec class uses classes for compressed and decompressed streams defined above.
        internal sealed class DeflateCodec45 : Codec
        {

            //We merely use different IMPLEMENTATIONS of Deflate, so CodecName remains "deflate"
            public static readonly string CodecName = "deflate";

            public DeflateCodec45()
                : base(CodecName)
            {
            }

            public override Stream GetCompressedStreamOver(Stream decompressed)
            {
                if (decompressed == null)
                {
                    throw new ArgumentNullException("decompressed");
                }

                return new CompressionStreamDeflate45(decompressed);
            }

            public override Stream GetDecompressedStreamOver(Stream compressed)
            {
                if (compressed == null)
                {
                    throw new ArgumentNullException("compressed");
                }

                return new DecompressionStreamDeflate45(compressed);
            }
        }
        #endregion

        #region Define modified Codec Factory
        //Define modified codec factory to be used in the reader.
        //It will catch the attempt to use "Deflate" and provide  a custom codec.
        //For all other cases, it will rely on the base class (CodecFactory).
        internal sealed class CodecFactoryDeflate45 : CodecFactory
        {

            public override Codec Create(string codecName)
            {
                if (codecName == DeflateCodec45.CodecName)
                    return new DeflateCodec45();
                else
                    return base.Create(codecName);
            }
        }
        #endregion

        #endregion

        #region Sample Class with demonstration methods
        //This class contains methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serializes and deserializes sample data set by using reflection and Avro object container files.
            //Serialized data is compressed with the custom compression codec (Deflate of .NET Framework 4.5).
            //
            //This sample uses memory stream for all operations related to serialization, deserialization and
            //object container manipulation, though file stream could be easily used.
            public void SerializeDeserializeUsingObjectContainersReflectionCustomCodec()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION, AVRO OBJECT CONTAINER FILES AND CUSTOM CODEC\n");

                //Path for Avro object container file
                string path = "AvroSampleReflectionDeflate45.avro";

                //Create a data set by using sample class and struct
                var testData = new List<SensorData>
                        {
                            new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
                            new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
                        };

                //Serializing and saving data to file.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData, which can serialize a sequence of SensorData objects to stream.
                    //Here the custom codec is introduced. For convenience, the next commented code line shows how to use built-in Deflate.
                    //Note that because the sample deals with different IMPLEMENTATIONS of Deflate, built-in and custom codecs are interchangeable
                    //in read-write operations.
                    //using (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
                    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, new DeflateCodec45()))
                    {
                        using (var writer = new SequentialWriter<SensorData>(w, 24))
                        {
                            // Serialize the data to stream using the sequential writer
                            testData.ForEach(writer.Write);
                        }
                    }

                    //Save stream to file
                    Console.WriteLine("Saving serialized data to file...");
                    if (!WriteFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }
                }

                //Reading and deserializing data.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from object container file
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Because of SequentialReader<T> constructor signature, an AvroSerializerSettings instance is required
                    //when codec factory is explicitly specified.
                    //You may comment the line below if you want to use built-in Deflate (see next comment).
                    AvroSerializerSettings settings = new AvroSerializerSettings();

                    //Create a SequentialReader instance for type SensorData, which will deserialize all serialized objects from the given stream.
                    //It allows iterating over the deserialized objects because it implements the IEnumerable<T> interface.
                    //Here the custom codec factory is introduced.
                    //For convenience, the next commented code line shows how to use built-in Deflate
                    //(no explicit Codec Factory parameter is required in this case).
                    //Note that because the sample deals with different IMPLEMENTATIONS of Deflate, built-in and custom codecs are interchangeable
                    //in read-write operations.
                    //using (var reader = new SequentialReader<SensorData>(AvroContainer.CreateReader<SensorData>(buffer, true)))
                    using (var reader = new SequentialReader<SensorData>(
                        AvroContainer.CreateReader<SensorData>(buffer, true, settings, new CodecFactoryDeflate45())))
                    {
                        var results = reader.Objects;

                        //Finally, verify that deserialized data matches the original one
                        Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
                        bool isEqual;
                        int count = 1;
                        var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
                        foreach (var pair in pairs)
                        {
                            isEqual = this.Equal(pair.expected, pair.actual);
                            Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
                            count++;
                        }
                    }
                }

                //Delete the file
                RemoveFile(path);
            }
        #endregion

            #region Helper Methods

            //Comparing two SensorData objects
            private bool Equal(SensorData left, SensorData right)
            {
                return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
            }

            //Saving memory stream to a new file with the given path
            private bool WriteFile(MemoryStream InputStream, string path)
            {
                if (!File.Exists(path))
                {
                    try
                    {
                        using (FileStream fs = File.Create(path))
                        {
                            InputStream.Seek(0, SeekOrigin.Begin);
                            InputStream.CopyTo(fs);
                        }
                        return true;
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                        return false;
                    }
                }
                else
                {
                    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
                    return false;

                }
            }

            //Reading file content by using the given path to a memory stream
            private bool ReadFile(MemoryStream OutputStream, string path)
            {
                try
                {
                    using (FileStream fs = File.Open(path, FileMode.Open))
                    {
                        fs.CopyTo(OutputStream);
                    }
                    return true;
                }
                catch (Exception e)
                {
                    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
                    Console.WriteLine(e.Message);
                    return false;
                }
            }

            //Deleting file by using given path
            private void RemoveFile(string path)
            {
                if (File.Exists(path))
                {
                    try
                    {
                        File.Delete(path);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                    }
                }
                else
                {
                    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
                }
            }
            #endregion

            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample Class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using reflection to Avro object container file using custom codec
                Sample.SerializeDeserializeUsingObjectContainersReflectionCustomCodec();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION, AVRO OBJECT CONTAINER FILES AND CUSTOM CODEC
    //
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    //For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.

###<a name="sample-6-using-avro-to-upload-data-for-the-microsoft-azure-hdinsight-service"></a>Ejemplo 6: Usar Avro para cargar los datos para el servicio de Microsoft Azure HDInsight

El ejemplo sexto muestra algunas técnicas de programación relacionadas con la interacción con el servicio de HDInsight de Azure. Un ejemplo que contiene el código de este ejemplo puede descargarse desde el sitio de [ejemplos de código de Azure](https://code.msdn.microsoft.com/windowsazure/Using-Avro-to-upload-data-ae81b1e3) .

El ejemplo hace lo siguiente:

* Se conecta a un clúster de servicio de HDInsight existente.
* Serializa varios archivos CSV y el resultado se cargarán en el almacenamiento de blobs de Windows Azure. (Archivos CSV se distribuyen junto con los datos de ejemplo y representan un extracto de datos históricos American Express existencias distribuidos por [Infochimps](http://www.infochimps.com/) para el período de 1970-2010. El ejemplo lee los datos del archivo CSV, convierte los registros a instancias de la clase de **cotizaciones** y, a continuación, serializa mediante la reflexión. Definición de tipo de cotizaciones se crea un esquema de JSON a través de la herramienta de generación de código de Microsoft Avro Library.
* Crea una nueva tabla externa denominada **Stocks** de sección y vínculos a los datos cargado en el paso anterior.
* Ejecuta una consulta usando la sección sobre la tabla de **cotizaciones** .

Además, el ejemplo realiza un procedimiento de limpieza antes y después de realizar operaciones principales. Durante la limpieza, se quitan todos los datos relacionados de blobs de Windows Azure y carpetas y la tabla subárbol se elimina. También puede invocar el procedimiento de limpieza de la línea de comandos de ejemplo.

El ejemplo tiene los siguientes requisitos previos:

* Una suscripción a Microsoft Azure active y su identificador de suscripción.
* Un certificado de administración de la suscripción con la clave privada correspondiente. El certificado debe estar instalado en el almacenamiento privado de usuario actual en el equipo que se usa para ejecutar el ejemplo.
* Un clúster de HDInsight activo.
* Una cuenta de almacenamiento de Azure vinculado al clúster HDInsight desde el requisito previo anterior, junto con la tecla de acceso primaria o secundaria correspondiente.

Toda la información de los requisitos previos deben introducirse en el archivo de configuración de ejemplo antes de ejecutar el ejemplo. Hay dos formas posibles para hacerlo:

* Editar el archivo app.config en el directorio de raíz de ejemplo y, a continuación, generar el ejemplo
* En primer lugar generar el ejemplo y después editar AvroHDISample.exe.config en el directorio de compilación

En ambos casos, todas las modificaciones deben realizarse en el **<appSettings>** sección Configuración. Siga los comentarios en el archivo.
En el ejemplo se ejecuta desde la línea de comandos con el comando siguiente (donde se supone que el archivo .zip con los datos de ejemplo se extrae a C:\AvroHDISample; si lo contrario, utilice la ruta de acceso del archivo relevante):

    AvroHDISample run C:\AvroHDISample\Data

Para limpiar el clúster, ejecute el siguiente comando:

    AvroHDISample clean

[deflate-100]: http://msdn.microsoft.com/library/system.io.compression.deflatestream(v=vs.100).aspx
[deflate-110]: http://msdn.microsoft.com/library/system.io.compression.deflatestream(v=vs.110).aspx

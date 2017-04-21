<properties
    pageTitle="Usar C# con la sección y cerdo en Hadoop en HDInsight | Microsoft Azure"
    description="Aprenda a usarlas C# definidas por el usuario (UDF) con la sección y cerdo transmisión en HDInsight de Azure."
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/28/2016"
    ms.author="larryfr"/>


#<a name="use-c-user-defined-functions-with-hive-and-pig-streaming-on-hadoop-in-hdinsight"></a>Usar funciones definidas por el usuario de C# con la sección y cerdo transmisión en Hadoop en HDInsight

La sección y cerdo son una buena opción para trabajar con datos en Azure HDInsight, pero a veces es necesario un idioma de uso más general. La sección y cerdo permiten llamar a código externo a través de las funciones definidas por el usuario (UDF) o transmisión.

En este documento, obtenga información sobre cómo usar C# con la sección y cerdo.

##<a name="prerequisites"></a>Requisitos previos

* Windows 7 o posterior.

* Visual Studio con las versiones siguientes:

    * Visual Studio 2012 Professional o Premium o Ultimate con [4 de actualización](http://www.microsoft.com/download/details.aspx?id=39305)

    * Visual Studio 2013 Comunidad/Professional o Premium o Ultimate con [4 de actualización](https://www.microsoft.com/download/details.aspx?id=44921)

    * Visual Studio de 2015

* Hadoop en HDInsight clúster: vea [aprovisionar un clúster HDInsight](hdinsight-provision-clusters.md) para conocer los pasos para crear un clúster

* Hadoop Tools para Visual Studio. Para conocer los pasos sobre cómo instalar y configurar las herramientas, vea [Introducción al uso HDInsight Hadoop Tools para Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md) .

##<a name="net-on-hdinsight"></a>.NET en HDInsight

El common language runtime (CLR) de .NET y marcos se instalan de forma predeterminada en clústeres HDInsight basado en Windows. Esto le permite usar aplicaciones de C# con la sección y cerdo transmisión (datos se pasan entre subárbol/cerdo y la aplicación de C# mediante stdout/entrada estándar).

> [AZURE.NOTE] Actualmente no hay ninguna compatibilidad para ejecutar .NET Framework UDF en clústeres HDInsight basados en Linux. 

##<a name="net-and-streaming"></a>.NET y transmisión

Transmisión implica la sección y cerdo pasar datos a una aplicación externa stdout y recibir los resultados a través de stdin. Para las aplicaciones de C#, esto se consigue más fácilmente a través de `Console.ReadLine()` y `Console.WriteLine()`.

Desde la sección y cerdo necesitan invocar la aplicación en tiempo de ejecución, se debe utilizar la plantilla de **Aplicación de consola** para sus proyectos de C#.

##<a name="hive-and-c35"></a>& Subárbol y C# 35;

###<a name="create-the-c-project"></a>Crear el proyecto C#

1. Abra Visual Studio y cree una nueva solución. Para el tipo de proyecto, seleccione **Aplicación de consola**y asigne un nombre al nuevo proyecto **HiveCSharp**.

2. Reemplace el contenido de **Program.cs** con lo siguiente:

        using System;
        using System.Security.Cryptography;
        using System.Text;
        using System.Threading.Tasks;

        namespace HiveCSharp
        {
            class Program
            {
                static void Main(string[] args)
                {
                    string line;
                    // Read stdin in a loop
                    while ((line = Console.ReadLine()) != null)
                    {
                        // Parse the string, trimming line feeds
                        // and splitting fields at tabs
                        line = line.TrimEnd('\n');
                        string[] field = line.Split('\t');
                        string phoneLabel = field[1] + ' ' + field[2];
                        // Emit new data to stdout, delimited by tabs
                        Console.WriteLine("{0}\t{1}\t{2}", field[0], phoneLabel, GetMD5Hash(phoneLabel));
                    }
                }
                /// <summary>
                /// Returns an MD5 hash for the given string
                /// </summary>
                /// <param name="input">string value</param>
                /// <returns>an MD5 hash</returns>
                static string GetMD5Hash(string input)
                {
                    // Step 1, calculate MD5 hash from input
                    MD5 md5 = System.Security.Cryptography.MD5.Create();
                    byte[] inputBytes = System.Text.Encoding.ASCII.GetBytes(input);
                    byte[] hash = md5.ComputeHash(inputBytes);

                    // Step 2, convert byte array to hex string
                    StringBuilder sb = new StringBuilder();
                    for (int i = 0; i < hash.Length; i++)
                    {
                        sb.Append(hash[i].ToString("x2"));
                    }
                    return sb.ToString();
                }
            }
        }

3. Genere el proyecto.

###<a name="upload-to-storage"></a>Cargar al almacenamiento

1. En Visual Studio, abra el **Explorador de servidores**.

3. Expanda **Azure**y, a continuación, expanda **HDInsight**.

4. Si se le solicita, escriba sus credenciales de Azure suscripción y, a continuación, haga clic en **Iniciar sesión**.

5. Expanda el clúster de HDInsight que desea implementar esta aplicación en y, a continuación, expanda la **Cuenta predeterminada de almacenamiento**.

    ![Explorador de servidores que muestra la cuenta de almacenamiento para el clúster](./media/hdinsight-hadoop-hive-pig-udf-dotnet-csharp/storage.png)

6. Haga doble clic en el **Contenedor predeterminado** para el clúster. Se abrirá una nueva ventana que muestra el contenido del contenedor predeterminado.

7. Haga clic en el icono de carga y, a continuación, vaya a la carpeta **bin\debug** del proyecto **HiveCSharp** . Por último, seleccione el archivo **HiveCSharp.exe** y haga clic en **Aceptar**.

    ![icono de carga](./media/hdinsight-hadoop-hive-pig-udf-dotnet-csharp/upload.png)

8. Una vez finalizada la carga, podrá usar la aplicación de una consulta de la sección.

###<a name="hive-query"></a>Sección consulta

1. En Visual Studio, abra el **Explorador de servidores**.

2. Expanda **Azure**y, a continuación, expanda **HDInsight**.

5. Haga clic en el clúster que implementa la aplicación **HiveCSharp** y seleccione **escribir una consulta de sección**.

6. Utilice la siguiente de la consulta de la sección:

        add file wasbs:///HiveCSharp.exe;

        SELECT TRANSFORM (clientid, devicemake, devicemodel)
        USING 'HiveCSharp.exe' AS
        (clientid string, phoneLabel string, phoneHash string)
        FROM hivesampletable
        ORDER BY clientid LIMIT 50;

    Se selecciona el `clientid`, `devicemake`, y `devicemodel` campos de `hivesampletable`y pasa los campos a la aplicación de HiveCSharp.exe. La consulta espera que la aplicación para devolver tres campos, que se almacenan como `clientid`, `phoneLabel`, y `phoneHash`. La consulta también espera encontrar HiveCSharp.exe en la raíz del contenedor de almacenamiento predeterminada (`add file wasbs:///HiveCSharp.exe`).

5. Haga clic en **Enviar** para enviar el trabajo en el clúster HDInsight. Se abrirá la ventana de la **Sección de resumen de tarea** .

6. Haga clic en **Actualizar** para actualizar el resumen hasta que cambie **El estado del trabajo** **completado**. Para ver los resultados de la tarea, haga clic en **Salida de trabajo**.

##<a name="pig-and-c35"></a>Cerdo y C & #35;

###<a name="create-the-c-project"></a>Crear el proyecto C#

1. Abra Visual Studio y cree una nueva solución. Para el tipo de proyecto, seleccione **Aplicación de consola**y asigne un nombre al nuevo proyecto **PigUDF**.

2. Reemplace el contenido del archivo **Program.cs** con lo siguiente:

        using System;

        namespace PigUDF
        {
            class Program
            {
                static void Main(string[] args)
                {
                    string line;
                    // Read stdin in a loop
                    while ((line = Console.ReadLine()) != null)
                    {
                        // Fix formatting on lines that begin with an exception
                        if(line.StartsWith("java.lang.Exception"))
                        {
                            // Trim the error info off the beginning and add a note to the end of the line
                            line = line.Remove(0, 21) + " - java.lang.Exception";
                        }
                        // Split the fields apart at tab characters
                        string[] field = line.Split('\t');
                        // Put fields back together for writing
                        Console.WriteLine(String.Join("\t",field));
                    }
                }
            }
        }

    Esta aplicación analiza las líneas que se envía desde cerdo y líneas de formato que comienzan con `java.lang.Exception`.

3. Guardar **Program.cs**y, a continuación, cree el proyecto.

###<a name="upload-the-application"></a>Cargar la aplicación

1. Cerdo transmisión espera que la aplicación sea local en el sistema de archivos de clúster. Habilitar Escritorio remoto para el clúster HDInsight y, a continuación, conectarse a ella siguiendo las instrucciones en [conectarse a clústeres HDInsight con RDP](hdinsight-administer-use-management-portal.md#rdp).

2. Una vez conectado, copie **PigUDF.exe** desde el directorio de la **Papelera y depuración** para el proyecto PigUDF en el equipo local y pegarla en el directorio **% PIG_HOME %** en el clúster.

###<a name="use-the-application-from-pig-latin"></a>Usar la aplicación de cerdo latino

1. Desde la sesión de escritorio remoto, iniciar la línea de comandos de Hadoop mediante el icono de **línea de comandos de Hadoop** en el escritorio.

2. Utilice la siguiente para iniciar la línea de comandos de cerdo:

        cd %PIG_HOME%
        bin\pig

    Se presentará con una `grunt>` símbolo del sistema.

3. Escriba lo siguiente para ejecutar un trabajo cerdo simple mediante la aplicación de .NET Framework:

        DEFINE streamer `pigudf.exe` SHIP('pigudf.exe');
        LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
        LOG = FILTER LOGS by LINE is not null;
        DETAILS = STREAM LOG through streamer as (col1, col2, col3, col4, col5);
        DUMP DETAILS;

    La `DEFINE` instrucción crea un alias de `streamer` para las aplicaciones de pigudf.exe y `SHIP` distribuye entre los nodos en el clúster. Más adelante, `streamer` se usa con la `STREAM` operador para procesar las líneas individuales contenidas en el registro y devolver los datos como una serie de columnas.

> [AZURE.NOTE] El nombre de la aplicación que se usa para transmitir debe estar rodeado el \` (marca inversa) carácter cuándo un alias, y ' (comilla simple) cuando se utiliza con `SHIP`.

3. Después de escribir la última línea, debe iniciar el trabajo. Finalmente devolverá resultado similar al siguiente:

        (2012-02-03 20:11:56 SampleClass5 [WARN] problem finding id 1358451042 - java.lang.Exception)
        (2012-02-03 20:11:56 SampleClass5 [DEBUG] detail for id 1976092771)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1317358561)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1737534798)
        (2012-02-03 20:11:56 SampleClass7 [DEBUG] detail for id 1475865947)

##<a name="summary"></a>Resumen

En este documento, ha aprendido a usar una aplicación de .NET Framework desde la sección y cerdo en HDInsight. Si desea obtener información sobre cómo utilizar Python con la sección y cerdo, vea [Usar Python con la sección y cerdo en HDInsight](hdinsight-python.md).

Para que otras formas de usar cerdo y subárbol y para obtener información sobre cómo usar MapReduce, consulte lo siguiente:

* [Usar subárbol con HDInsight](hdinsight-use-hive.md)

* [Usar cerdo con HDInsight](hdinsight-use-pig.md)

* [Usar MapReduce con HDInsight](hdinsight-use-mapreduce.md)

<properties
    pageTitle="Tutorial de HBase: Introducción a HBase en Hadoop | Microsoft Azure"
    description="Siga este tutorial HBase para empezar a usar Apache HBase con Hadoop en HDInsight. Crear tablas a partir de la consola de HBase y consultarlos usando subárbol."
    keywords="Apache hbase, hbase, hbase shell, tutorial hbase"
    services="hdinsight"
    documentationCenter=""
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/21/2016"
    ms.author="jgao"/>



# <a name="hbase-tutorial-get-started-using-apache-hbase-with-windows-based-hadoop-in-hdinsight"></a>Tutorial de HBase: empezar a usar Apache HBase con basado en Windows Hadoop en HDInsight

[AZURE.INCLUDE [hbase-selector](../../includes/hdinsight-hbase-selector.md)]

Aprenda a crear clústeres HBase HDInsight, crear tablas de HBase y las tablas de consulta usando Apache subárbol. Para obtener información general de HBase, consulte [Introducción a HDInsight HBase][hdinsight-hbase-overview].

La información de este documento es específica para clústeres HDInsight basado en Windows. Para obtener información sobre clústeres basados en Windows, use el selector de tabulaciones en la parte superior de la página para cambiar.

> [AZURE.NOTE] Solo está disponible para su uso con clústeres HDInsight 3.1 HBase (versión 0.98.0) en HDInsight de Windows (basándose en Apache Hadoop e hilo 2.4.0). Para obtener información de versión, consulte [Novedades en las versiones de clúster Hadoop proporcionadas por HDInsight?][hdinsight-versions]

## <a name="before-you-begin"></a>Antes de empezar

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Antes de comenzar este tutorial HBase, debe tener el siguiente:

- **Suscripción A Microsoft Azure**. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Una estación de trabajo** con 2013 de Visual Studio o mayor: para obtener instrucciones, consulte [Instalar Visual Studio](http://msdn.microsoft.com/library/e2h7fzkw.aspx).

### <a name="access-control-requirements"></a>Requisitos de control de acceso

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-hbase-cluster"></a>Crear clúster HBase

[AZURE.INCLUDE [provisioningnote](../../includes/hdinsight-provisioning.md)]

**Para crear un clúster de HBase con el portal de Azure**

1. Inicie sesión en el [portal de Azure][azure-management-portal].
2. Haga clic en **nuevo** o **+** en la esquina superior izquierda de esquina y, a continuación, haga clic en **datos + análisis**, **HDInsight**.
3. Escriba los valores siguientes:

    - **Nombre de clúster** : escriba un nombre para identificar este clúster.
    - **Tipo de clúster** : seleccione **HBase**.
    - **Sistema operativo del clúster** : seleccione **Windows**.  Para crear HBase basados en Linux de clúster, consulte [HBase tutorial: empezar a usar Apache HBase con Hadoop en HDInsight (Linux)](hdinsight-hbase-tutorial-get-started-linux.md).
    - **Versión** : seleccione una versión de HBase.
    - **Suscripción** : seleccione la suscripción de Azure utilizada para crear este clúster.
    - **Grupo de recursos** : crear un nuevo grupo de recursos de Azure o seleccione uno existente. Para obtener más información, vea [Información general del Administrador de recursos de Azure](azure-resource-manager/resource-group-overview.md)
    - **Credenciales** : para clúster basado en Windows, puede crear un usuario de clúster (también denominado HTTP usuario, usuario del servicio web HTTP) y un usuario de escritorio remoto. Haga clic en **Habilitar Escritorio remoto** para agregar las credenciales de usuario de escritorio remoto. La siguiente sección requiere RDP.
    - **Origen de datos** - crear una nueva cuenta de almacenamiento de Azure o seleccione una cuenta de almacenamiento de Azure existente que se utilizará como el sistema de archivo predeterminado para el clúster. La ubicación predeterminada de la cuenta de almacenamiento determina la ubicación de la ubicación de clúster. La cuenta de almacenamiento predeterminada y el clúster deben ubicar en el mismo centro de datos.
    - **Niveles de precios de nodo** : seleccione el número de servidores de región para el clúster HBase

        > [AZURE.WARNING] Para alta disponibilidad de los servicios de HBase, debe crear un clúster que contiene al menos **tres** nodos. Esto garantiza que, si un nodo falla, las regiones de datos de HBase están disponibles en otros nodos.

        > Si va a aprender HBase, siempre elija 1 para el tamaño de clúster y eliminar el clúster después de cada uso para reducir el costo.

    - **Configuración opcional** : red virtual de Azure configurar, configurar acciones de secuencias de comandos y agregar cuentas de almacenamiento adicional.

4. Haga clic en **crear**.

>[AZURE.NOTE] Después de elimina un clúster de HBase, puede crear otro clúster HBase usando la misma cuenta de almacenamiento predeterminada y el contenedor de blobs de forma predeterminada. El nuevo clúster seleccionará las tablas de HBase que creó en el clúster original. Para evitar incoherencias, le recomendamos que desactive las tablas HBase antes de eliminar el clúster.

## <a name="create-tables-and-insert-data"></a>Crear tablas e insertar datos

Actualmente, hay dos formas para tener acceso a HBase. Esta sección se tratan con la consola de HBase. La siguiente sección trata el uso de .NET SDK.

Para la mayoría de las personas, aparecen los datos en el formato de tabla:

![datos tabulares de hdinsight hbase][img-hbase-sample-data-tabular]

En HBase que es una implementación de BigTable, los mismos datos es similar:

![hdinsight hbase bigtable datos][img-hbase-sample-data-bigtable]

Tendrá más sentido cuando termine el procedimiento siguiente.  

**Usar la consola de HBase**

1. Usar RDP para conectarse a su clúster de HBase en HDInsight. Las instrucciones de RDP, vea [Administrar Hadoop clústeres en HDInsight con el Portal de Azure][hdinsight-manage-portal].
2. En la sesión RDP, haga clic en el método abreviado de la **línea de comandos de Hadoop** ubicado en el escritorio.
3. Abre la consola de HBase:

        cd %HBASE_HOME%\bin
        hbase shell

4. Crear un HBase con dos familias de columna:

        create 'Contacts', 'Personal', 'Office'
        list
5. Insertar algunos datos:

        put 'Contacts', '1000', 'Personal:Name', 'John Dole'
        put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
        put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
        put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
        scan 'Contacts'

    ![hdinsight hadoop hbase shell][img-hbase-shell]

6. Obtener una sola fila

        get 'Contacts', '1000'

    Verá los mismos resultados como con el comando análisis porque no hay una única fila.

    Para obtener más información sobre el esquema de la tabla Hbase, vea [Introducción al diseño de esquema de HBase][hbase-schema]. Para ver más comandos de HBase, consulte la [Guía de referencia de Apache HBase][hbase-quick-start].


6. Salir del shell

        exit

**Cargar datos de forma masiva en la tabla de HBase de contactos**

HBase incluye varios métodos de carga de datos en tablas. Para obtener más información, vea [cargar de forma masiva](http://hbase.apache.org/book.html#arch.bulk.load).


Se ha cargado un archivo de datos de ejemplo en un contenedor de blob público, wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt. El contenido del archivo de datos es:

    8396    Calvin Raji     230-555-0191    230-555-0191    5415 San Gabriel Dr.
    16600   Karen Wu        646-555-0113    230-555-0192    9265 La Paz
    4324    Karl Xie        508-555-0163    230-555-0193    4912 La Vuelta
    16891   Jonn Jackson    674-555-0110    230-555-0194    40 Ellis St.
    3273    Miguel Miller   397-555-0155    230-555-0195    6696 Anchor Drive
    3588    Osa Agbonile    592-555-0152    230-555-0196    1873 Lion Circle
    10272   Julia Lee       870-555-0110    230-555-0197    3148 Rose Street
    4868    Jose Hayes      599-555-0171    230-555-0198    793 Crawford Street
    4761    Caleb Alexander 670-555-0141    230-555-0199    4775 Kentucky Dr.
    16443   Terry Chander   998-555-0171    230-555-0200    771 Northridge Drive

Puede crear un archivo de texto y cargar el archivo en su cuenta de almacenamiento, si lo desea. Para instrucciones, vea [cargar datos para trabajos de Hadoop en HDInsight][hdinsight-upload-data].

> [AZURE.NOTE] Este procedimiento utiliza la tabla de HBase contactos que creó en el último procedimiento.

1. En la sesión RDP, haga clic en el método abreviado de la **línea de comandos de Hadoop** ubicado en el escritorio.
2. Cambie el directorio:

        cd %HBASE_HOME%\bin

3. Ejecute el comando siguiente para transformar el archivo de datos a StoreFiles y el almacenamiento en una ruta de acceso relativa especificado por Dimporttsv.bulk.output:

        hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name, Personal:Phone, Office:Phone, Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

4. Ejecute el comando siguiente para cargar los datos de /example/data/storeDataFileOutput a la tabla HBase:

        hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts

5. Puede abrir la consola de HBase y use el comando de exploración para mostrar el contenido de la tabla.



## <a name="use-hive-to-query-hbase-tables"></a>Usar subárbol para tablas de consulta HBase

Puede consultar los datos almacenados en HBase mediante el uso de la sección. Esta sección crea una tabla subárbol que asigna a la tabla HBase y se utiliza para consultar los datos en la tabla HBase.

**Para abrir el panel de diseño**

1. Vaya a **https://<HDInsight Cluster Name>.azurehdinsight.net/**.
5. Escriba el nombre de usuario de cuenta de usuario Hadoop y la contraseña. El nombre de usuario predeterminado es **admin** y la contraseña es lo que especificó durante el proceso de creación. Se abrirá una nueva pestaña del explorador.
6. Haga clic en **Editor de sección** en la parte superior de la página. El Editor de subárbol es similar a esta:

    ![Panel de clúster HDInsight.][img-hdinsight-hbase-hive-editor]

**Ejecutar consultas de la sección**

1. Escriba la siguiente secuencia de comandos de HiveQL en Editor de sección y haga clic en **Enviar** para crear una tabla subárbol que asigna a la tabla HBase. Asegúrese de que ha creado la tabla de ejemplo mencionada anteriormente en este tutorial utilizando el shell de HBase antes de ejecutar esta declaración.

        CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
        STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
        WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
        TBLPROPERTIES ('hbase.table.name' = 'Contacts');

    Espere hasta que las actualizaciones de **estado** en **completado**.

2. Escriba la siguiente secuencia de comandos de HiveQL en Editor de sección y, a continuación, haga clic en **Enviar**. Sección consulta los datos de la tabla HBase:

        SELECT count(*) FROM hbasecontacts;

4. Para recuperar los resultados de la consulta de sección, haga clic en el vínculo **Ver detalles** en la ventana de la **Sesión de trabajo** cuando finalice el trabajo. Habrá un único archivo de salida de trabajo porque coloca un registro en la tabla HBase.




**Para examinar el archivo de salida**

1. En la consola de consulta, haga clic en **Explorador de archivos**.
2. Haga clic en la cuenta de almacenamiento de Azure que se usa como el sistema de archivo predeterminado para el clúster de HBase.
3. Haga clic en el nombre del clúster HBase. El contenedor de cuenta de Azure almacenamiento predeterminado utiliza el nombre de clúster.
4. Haga clic en el **usuario**y, a continuación, haga clic en **Administrador**. (Este es el nombre de usuario de Hadoop.)
6. Haga clic en el nombre del trabajo con la hora de **Última modificación** que coincida con el tiempo cuando ejecutó la consulta seleccione subárbol.
4. Haga clic en **stdout**. Guarde el archivo y abra el archivo con el Bloc de notas. Habrá un archivo de salida.

    ![Explorador de archivos de HDInsight HBase subárbol Editor][img-hdinsight-hbase-file-browser]

## <a name="use-the-net-hbase-rest-api-client-library"></a>Usar la biblioteca de cliente de la API de REST de .NET HBase

Debe descargar la biblioteca de cliente de la API de REST de HBase para .NET de GitHub y genere el proyecto para que pueda utilizar el SDK de .NET HBase. El siguiente procedimiento incluye las instrucciones para esta tarea.

1. Crear una nueva aplicación de Visual Studio Windows Desktop consola de C#.
2. Abra la consola del Administrador de paquetes de NuGet haciendo clic en **Herramientas de** > **Administrador de paquetes de NuGet** > **Consola del Administrador de paquetes**.
3. Ejecute el siguiente comando NuGet en la consola:

        Install-Package Microsoft.HBase.Client

5. Agregue las siguientes instrucciones **using** en la parte superior del archivo:

        using Microsoft.HBase.Client;
        using org.apache.hadoop.hbase.rest.protobuf.generated;

6. Reemplazar la función **principal** con los siguientes elementos:

        static void Main(string[] args)
        {
            string clusterURL = "https://<yourHBaseClusterName>.azurehdinsight.net";
            string hadoopUsername= "<yourHadoopUsername>";
            string hadoopUserPassword = "<yourHadoopUserPassword>";

            string hbaseTableName = "sampleHbaseTable";

            // Create a new instance of an HBase client.
            ClusterCredentials creds = new ClusterCredentials(new Uri(clusterURL), hadoopUsername, hadoopUserPassword);
            HBaseClient hbaseClient = new HBaseClient(creds);

            // Retrieve the cluster version.
            var version = hbaseClient.GetVersion();
            Console.WriteLine("The HBase cluster version is " + version);

            // Create a new HBase table.
            TableSchema testTableSchema = new TableSchema();
            testTableSchema.name = hbaseTableName;
            testTableSchema.columns.Add(new ColumnSchema() { name = "d" });
            testTableSchema.columns.Add(new ColumnSchema() { name = "f" });
            hbaseClient.CreateTable(testTableSchema);

            // Insert data into the HBase table.
            string testKey = "content";
            string testValue = "the force is strong in this column";
            CellSet cellSet = new CellSet();
            CellSet.Row cellSetRow = new CellSet.Row { key = Encoding.UTF8.GetBytes(testKey) };
            cellSet.rows.Add(cellSetRow);

            Cell value = new Cell { column = Encoding.UTF8.GetBytes("d:starwars"), data = Encoding.UTF8.GetBytes(testValue) };
            cellSetRow.values.Add(value);
            hbaseClient.StoreCells(hbaseTableName, cellSet);

            // Retrieve a cell by its key.
            cellSet = hbaseClient.GetCells(hbaseTableName, testKey);
            Console.WriteLine("The data with the key '" + testKey + "' is: " + Encoding.UTF8.GetString(cellSet.rows[0].values[0].data));
            // with the previous insert, it should yield: "the force is strong in this column"

            //Scan over rows in a table. Assume the table has integer keys and you want data between keys 25 and 35.
            Scanner scanSettings = new Scanner()
            {
                batch = 10,
                startRow = BitConverter.GetBytes(25),
                endRow = BitConverter.GetBytes(35)
            };

            ScannerInformation scannerInfo = hbaseClient.CreateScanner(hbaseTableName, scanSettings);
            CellSet next = null;
            Console.WriteLine("Scan results");

            while ((next = hbaseClient.ScannerGetNext(scannerInfo)) != null)
            {
                foreach (CellSet.Row row in next.rows)
                {
                    Console.WriteLine(row.key + " : " + Encoding.UTF8.GetString(row.values[0].data));
                }
            }

            Console.WriteLine("Press ENTER to continue ...");
            Console.ReadLine();
        }

7. Establezca las tres primeras variables de la función **principal** .
8. Presione **F5** para ejecutar la aplicación.

## <a name="check-cluster-status"></a>Comprobar el estado de clúster

HBase en HDInsight incluye una interfaz de usuario Web para supervisar clústeres. La interfaz de usuario de Web puede solicitar estadísticas o información acerca de las regiones.

Para abrir la interfaz de usuario de Web, debe RDP en el clúster y, a continuación, haga clic en el acceso directo de la interfaz de usuario de HMaster información Web en el escritorio o usar la siguiente URL en un explorador web:

    http://zookeeper[0-2]:60010/master-status

En un clúster de alta disponibilidad, encontrará un vínculo a active HBase maestro nodo actual que hospeda la interfaz de usuario de Web.

##<a name="delete-the-cluster"></a>Eliminar el clúster
Para evitar incoherencias, le recomendamos que desactive las tablas HBase antes de eliminar el clúster.
[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


## <a name="whats-next"></a>¿Qué es el siguiente?
En este tutorial HBase para HDInsight, ha aprendido cómo crear un clúster de HBase y cómo crear tablas y ver los datos de esas tablas desde la consola de HBase. Que además, ha aprendido cómo usar una consulta de la sección de datos de tablas de HBase y cómo usar las API de REST HBase C# para crear una tabla de HBase y recuperar datos de la tabla.

Para obtener más información, consulte:

- [Información general de HDInsight HBase][hdinsight-hbase-overview].
HBase es una base de datos de NoSQL de código abierto, Apache, integrada en Hadoop que proporciona acceso aleatorio y fuerte coherencia para grandes cantidades de datos estructurados y semistructured.
- [Crear HBase clústeres en una red Virtual Azure][hdinsight-hbase-provision-vnet].
Con la integración de red virtual HBase clústeres se pueden implementar a la misma red virtual que sus aplicaciones para que las aplicaciones puedan comunicarse con HBase directamente.
- [Configurar HBase replicación en HDInsight](hdinsight-hbase-geo-replication.md). Obtenga información sobre cómo configurar la replicación de HBase entre dos centros de datos de Azure.
- [Analizar la opinión de Twitter con HBase en HDInsight][hbase-twitter-sentiment].
Obtenga información sobre cómo hacer en tiempo real de [análisis de opinión](http://en.wikipedia.org/wiki/Sentiment_analysis) de datos grandes mediante HBase en un clúster de Hadoop en HDInsight.

[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hbase-reference]: http://hbase.apache.org/book.html#importtsv
[hbase-schema]: http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf
[hbase-quick-start]: http://hbase.apache.org/book.html#quickstart





[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-versions]: hdinsight-component-versioning.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/

[img-hdinsight-hbase-cluster-quick-create]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-quick-create.png
[img-hdinsight-hbase-hive-editor]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-hive-editor.png
[img-hdinsight-hbase-file-browser]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-file-browser.png
[img-hbase-shell]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-shell.png
[img-hbase-sample-data-tabular]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-contacts-tabular.png
[img-hbase-sample-data-bigtable]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-contacts-bigtable.png

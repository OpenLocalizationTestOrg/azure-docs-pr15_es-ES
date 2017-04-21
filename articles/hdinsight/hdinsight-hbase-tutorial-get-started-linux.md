<properties
    pageTitle="Tutorial de HBase: Introducción a clústeres basados en Linux HBase en Hadoop | Microsoft Azure"
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
    ms.topic="get-started-article"
    ms.date="10/19/2016"
    ms.author="jgao"/>



# <a name="hbase-tutorial-get-started-using-apache-hbase-with-linux-based-hadoop-in-hdinsight"></a>Tutorial de HBase: empezar a usar Apache HBase con Hadoop basados en Linux en HDInsight 

[AZURE.INCLUDE [hbase-selector](../../includes/hdinsight-hbase-selector.md)]

Aprenda a crear un clúster de HBase HDInsight, crear tablas HBase y tablas de la consulta mediante el uso de la sección. Para obtener información general de HBase, consulte [Introducción a HDInsight HBase][hdinsight-hbase-overview].

La información de este documento es específica para clústeres HDInsight basados en Linux. Para obtener información sobre clústeres basados en Windows, use el selector de tabulaciones en la parte superior de la página para cambiar.

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

##<a name="prerequisites"></a>Requisitos previos

Antes de comenzar este tutorial HBase, debe tener el siguiente:

- **Suscripción de un Azure**. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- [Proteger Shell(SSH)](hdinsight-hadoop-linux-use-ssh-unix.md). 
- [doblez](http://curl.haxx.se/download.html).

### <a name="access-control-requirements"></a>Requisitos de control de acceso

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-hbase-cluster"></a>Crear clúster HBase

El procedimiento siguiente usa una plantilla de administrador de recursos de Azure para crear un clúster de HBase basados en Linux versión 3.4 y la cuenta de Azure almacenamiento de predeterminada dependiente. Para conocer los parámetros que se utilizan en el procedimiento y otros métodos de creación de clúster, vea [clústeres basados en Linux crear Hadoop en HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

1. Haga clic en la imagen siguiente para abrir la plantilla en el portal de Azure. La plantilla se encuentra en un contenedor de blob público. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-cluster-in-hdinsight.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Desde el módulo de **implementación personalizada** , escriba lo siguiente:

    - **Suscripción**: seleccione la suscripción de Azure que se usará para crear el clúster.
    - **Grupo de recursos**: crear un nuevo grupo de administración de recursos de Azure o use una existente.
    - **Ubicación**: especifique la ubicación del grupo de recursos. 
    - **NombreDeClúster**: escriba un nombre para el clúster de HBase que se creará.
    - **Contraseña y nombre de inicio de sesión de clúster**: el nombre de inicio de sesión predeterminado es **admin**.
    - **SSH nombre de usuario y contraseña**: el nombre de usuario predeterminado es **sshuser**.  Puede cambiarlo.
     
    Otros parámetros son opcionales.  
    
    Cada clúster tiene una dependencia de cuenta de almacenamiento de blobs de Windows Azure. Después de eliminar un clúster, los datos se conservan en la cuenta de almacenamiento. El nombre de cuenta de almacenamiento de clúster predeterminado es el nombre de clúster con anexa "store". Está codificado en la sección de variables de plantilla.
        
3. Seleccione **acepto los términos y condiciones indicadas arriba**y, a continuación, haga clic en **comprar**. Tarda aproximadamente 20 minutos para crear un clúster.


>[AZURE.NOTE] Después de elimina un clúster de HBase, puede crear otro clúster HBase mediante el mismo contenedor de blobs de forma predeterminada. El nuevo clúster seleccionará las tablas de HBase que creó en el clúster original. Para evitar incoherencias, le recomendamos que desactive las tablas HBase antes de eliminar el clúster.

## <a name="create-tables-and-insert-data"></a>Crear tablas e insertar datos

Puede utilizar SSH para conectarse a clústeres HBase y luego use HBase Shell para crear tablas de HBase, insertar datos y los datos de la consulta. Para obtener información acerca de cómo utilizar SSH, vea [Usar SSH con basados en Linux Hadoop en HDInsight de Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md) y [Usar SSH con basados en Linux Hadoop en HDInsight de Windows](hdinsight-hadoop-linux-use-ssh-windows.md).
 

Para la mayoría de las personas, aparecen los datos en el formato de tabla:

![Datos tabulares de HDInsight HBase][img-hbase-sample-data-tabular]

En HBase que es una implementación de BigTable, los mismos datos es similar:

![HDInsight HBase bigtable datos][img-hbase-sample-data-bigtable]

Cuando termine el procedimiento siguiente que le será más sentido.  


**Usar la consola de HBase**

1. Desde SSH, ejecute el siguiente comando:

        hbase shell

4. Crear un HBase con familias de dos columnas:

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

    Para obtener más información sobre el esquema de la tabla HBase, vea [Introducción al diseño de esquema de HBase][hbase-schema]. Para ver más comandos de HBase, consulte la [Guía de referencia de Apache HBase][hbase-quick-start].

6. Salir del shell

        exit



**Cargar datos de forma masiva en la tabla de HBase de contactos**

HBase incluye varios métodos de carga de datos en tablas.  Para obtener más información, vea [cargar de forma masiva](http://hbase.apache.org/book.html#arch.bulk.load).


Se ha cargado un archivo de datos de ejemplo en un contenedor de blob público, *wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt*.  El contenido del archivo de datos es:

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

> [AZURE.NOTE] Este procedimiento utiliza la tabla de HBase contactos que haya creado en el último procedimiento.

1. Desde SSH, ejecute el comando siguiente para transformar el archivo de datos a StoreFiles y el almacenamiento en una ruta de acceso relativa especificado por Dimporttsv.bulk.output:.  Si está en el Shell de HBase, utilice el comando Salir para salir.

        hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name,Personal:Phone,Office:Phone,Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

4. Ejecute el comando siguiente para cargar los datos de /example/data/storeDataFileOutput a la tabla HBase:

        hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts

5. Puede abrir la consola de HBase y use el comando de exploración para mostrar el contenido de la tabla.



## <a name="use-hive-to-query-hbase"></a>Use la sección para consulta HBase

Puede consultar datos en tablas de HBase mediante subárbol. Esta sección crea una tabla subárbol que asigna a la tabla HBase y se utiliza para consultar los datos en la tabla HBase.

1. Abra **PuTTY**y conéctese al clúster.  Vea las instrucciones del procedimiento anterior.
2. Abra el shell de sección.

       hive
3. Ejecute el siguiente script de HiveQL para crear una tabla subárbol que asigna a la tabla HBase. Asegúrese de que ha creado la tabla de ejemplo mencionada anteriormente en este tutorial utilizando el shell de HBase antes de ejecutar esta declaración.

        CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
        STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
        WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
        TBLPROPERTIES ('hbase.table.name' = 'Contacts');

2. Ejecute el siguiente script de HiveQL para consultar los datos de la tabla HBase:

        SELECT count(*) FROM hbasecontacts;

## <a name="use-hbase-rest-apis-using-curl"></a>Usar las API de REST de HBase con rizo

> [AZURE.NOTE] Con doblez o cualquier otro tipo de comunicación resto WebHCat, debe autenticar las solicitudes al proporcionar el nombre de usuario y contraseña para el Administrador de clústeres HDInsight. También debe usar el nombre de clúster como parte del identificador de recursos uniforme (URI) utilizado para enviar las solicitudes en el servidor.
>
> Para los comandos de esta sección, reemplace el **nombre de usuario** con el usuario para autenticar al clúster y reemplace la **contraseña** con la contraseña de la cuenta de usuario. Reemplazar **NOMBREDECLÚSTER** con el nombre del clúster.
>
> La API de REST está protegida mediante [la autenticación básica](http://en.wikipedia.org/wiki/Basic_access_authentication). Siempre deben realizar solicitudes utilizando HTTP seguro (HTTPS) para ayudar a garantizar que sus credenciales se envían de forma segura en el servidor.

1. Desde una línea de comandos, use el comando siguiente para comprobar que puede conectarse a su clúster HDInsight:

        curl -u <UserName>:<Password> \
        -G https://<ClusterName>.azurehdinsight.net/templeton/v1/status

    Recibirá una respuesta similar a la siguiente:

        {"status":"ok","version":"v1"}

    Los parámetros que se utilizan en este comando son los siguientes:

    * **-u** - el nombre de usuario y contraseña que utiliza para autenticar la solicitud.
    * **-G** - indica que se trata de una solicitud GET.

2. Use el comando siguiente para mostrar las tablas de HBase existentes:

        curl -u <UserName>:<Password> \
        -G https://<ClusterName>.azurehdinsight.net/hbaserest/

3. Use el comando siguiente para crear una nueva tabla HBase con dos familias de columna:

        curl -u <UserName>:<Password> \
        -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/schema" \
        -H "Accept: application/json" \
        -H "Content-Type: application/json" \
        -d "{\"@name\":\"Contact1\",\"ColumnSchema\":[{\"name\":\"Personal\"},{\"name\":\"Office\"}]}" \
        -v

    El esquema está disponible en el formato JSon.

4. Use el comando siguiente para insertar algunos datos:

        curl -u <UserName>:<Password> \
        -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/false-row-key" \
        -H "Accept: application/json" \
        -H "Content-Type: application/json" \
        -d "{\"Row\":{\"key\":\"MTAwMA==\",\"Cell\":{\"column\":\"UGVyc29uYWw6TmFtZQ==\", \"$\":\"Sm9obiBEb2xl\"}}}" \
        -v

    Debe base 64 codificar los valores especificados en el modificador -d.  En el ejemplo:

    - MTAwMA ==: 1000
    - UGVyc29uYWw6TmFtZQ ==: nombre del Personal:
    - Sm9obiBEb2xl: John Dole

    [clave de fila falso](https://hbase.apache.org/apidocs/org/apache/hadoop/hbase/rest/package-summary.html#operation_cell_store_single) le permite insertar varias valor (por lotes).

5. Utilice el comando siguiente para obtener una fila:

        curl -u <UserName>:<Password> \
        -X GET "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/1000" \
        -H "Accept: application/json" \
        -v

Para obtener más información sobre el resto de HBase, consulte [Guía de referencia de HBase Apache](https://hbase.apache.org/book.html#_rest).

## <a name="check-cluster-status"></a>Comprobar el estado de clúster

HBase en HDInsight incluye una interfaz de usuario Web para supervisar clústeres. La interfaz de usuario de Web puede solicitar estadísticas o información acerca de las regiones.

SSH también puede usarse para peticiones locales, como las de web, en el clúster HDInsight de túnel. La solicitud, a continuación, se enruta al recurso solicitado como si hubiera se ha originado en el nodo principal del clúster de HDInsight. Para obtener más información, vea [Usar SSH con basados en Linux Hadoop en HDInsight de Windows](hdinsight-hadoop-linux-use-ssh-windows.md#tunnel).

**Para establecer una sesión de túnel de SSH**

1. Abra **PuTTY**.  
2. Si proporciona una clave SSH cuando creó su cuenta de usuario durante el proceso de creación, debe realizar el paso siguiente para seleccionar la clave privada debe utilizar al autenticar al clúster:

    En **categoría**, expanda la **conexión**, expanda **SSH**y seleccione **Auth**. Por último, haga clic en **Examinar** y seleccione el archivo .ppk que contiene su clave privada.

3. En **categoría**, haga clic en **sesión**.
4. Desde las opciones básicas de la pantalla de sesión masilla, escriba los valores siguientes:

    - **Nombre de host**: la dirección SSH del campo HDInsight server en el nombre de Host (o dirección IP). La dirección SSH es el nombre del clúster, a continuación, **-ssh.azurehdinsight.net**. Por ejemplo, *miClúster ssh.azurehdinsight.net*.
    - **Puerto**: 22. El puerto en el principal headnode ssh es 22.  
5. En la sección **categoría** a la izquierda del cuadro de diálogo, expanda la **conexión**, expanda **SSH**y, a continuación, haga clic en **túneles**.
6. En las opciones de control de formulario de reenvío de puerto SSH, complete la información siguiente:

    - **Puerto de origen** - el puerto en el cliente que desea reenviar. Por ejemplo, 9876.
    - **Dinámico** - proxy SOCKS dinámicas permite enrutamiento.
7. Haga clic en **Agregar** para agregar la configuración.
8. Haga clic en **Abrir** en la parte inferior del cuadro de diálogo para abrir una conexión SSH.
9. Cuando se le solicita, inicie sesión en el servidor con una cuenta SSH. Se establezca una sesión SSH y habilitar el túnel.

**Para buscar el nombre completo de la zookeepers con Ambari**

1. Vaya a https://<ClusterName>.azurehdinsight.net/.
2. Escriba sus credenciales de cuenta de usuario de clúster dos veces.
3. En el menú de la izquierda, haga clic en **zookeeper**.
4. Haga clic en uno de los tres vínculos **ZooKeeper servidor** de la lista de resumen.
5. Copie el **nombre de host**. Por ejemplo, zk0 CLUSTERNAME.xxxxxxxxxxxxxxxxxxxx.cx.internal.cloudapp.net.

**Para configurar un programa de cliente (Firefox) y comprobar el estado de clúster**

1. Abra Firefox.
2. Haga clic en el botón **Abrir menú** .
3. Haga clic en **Opciones**.
4. Haga clic en **Avanzadas**, haga clic en **red**y, a continuación, haga clic en **configuración**.
5. Seleccione **configuración Manual del proxy**.
6. Escriba los valores siguientes:

    - **Host Socks**: host local
    - **Puerto**: utilizar el mismo puerto configurado en el Putty SSH túnel.  Por ejemplo, 9876.
    - **V5 SOCKS**: (seleccionada)
    - **DNS remoto**: (seleccionada)
7. Haga clic en **Aceptar** para guardar los cambios.
8. Vaya a http://&lt;el FQDN de un ZooKeeper >: patrón/60010-estado.

En un clúster de alta disponibilidad, encontrará un vínculo a active HBase maestro nodo actual que hospeda la interfaz de usuario de Web.

##<a name="delete-the-cluster"></a>Eliminar el clúster

Para evitar incoherencias, le recomendamos que desactive las tablas HBase antes de eliminar el clúster.

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Pasos siguientes

En este tutorial HBase para HDInsight, ha aprendido cómo crear un clúster de HBase y cómo crear tablas y ver los datos de esas tablas desde la consola de HBase. Además, ha aprendido a usar una consulta de la sección de datos en tablas de HBase y cómo usar las API de REST HBase C# para crear una tabla de HBase y recuperar datos de la tabla.

Para obtener más información, consulte:

- [Información general de HDInsight HBase][hdinsight-hbase-overview]: HBase es una base de datos de NoSQL de código abierto, Apache, integrada en Hadoop que proporciona acceso aleatorio y fuerte coherencia para grandes cantidades de datos estructurados y semistructured.


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
[azure-portal]: https://portal.azure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/

[img-hdinsight-hbase-cluster-quick-create]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-quick-create.png
[img-hdinsight-hbase-hive-editor]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-hive-editor.png
[img-hdinsight-hbase-file-browser]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-file-browser.png
[img-hbase-shell]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-shell.png
[img-hbase-sample-data-tabular]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-tabular.png
[img-hbase-sample-data-bigtable]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-bigtable.png

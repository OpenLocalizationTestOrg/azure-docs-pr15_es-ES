<properties
 pageTitle="Usar Apache tormenta con Power BI | Microsoft Azure"
 description="Crear un informe de Power BI con datos de una topología de C# ejecutar en un clúster de tormenta Apache en HDInsight."
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="jhubbard"
 editor="cgronlun"
    tags="azure-portal"/>

<tags
 ms.service="hdinsight"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="big-data"
 ms.date="10/27/2016"
 ms.author="larryfr"/>

# <a name="use-power-bi-to-visualize-data-from-an-apache-storm-topology"></a>Usar Power BI para visualizar datos de una topología de tormenta Apache

Power BI le permite mostrar visualmente los datos como informes. Con las plantillas de Visual Studio para tormenta en HDInsight, puede usar fácilmente almacene los datos desde una topología que se ejecuta en una tormenta Apache en clúster de HDInsight en SQL Azure y, a continuación, visualizar los datos con Power BI.

En este documento, aprenderá cómo usar Power BI para crear un informe de datos genera una topología de tormenta Apache y almacenados en la base de datos de SQL Azure.

> [AZURE.NOTE] Mientras los pasos de este documento se basan en un entorno de desarrollo de Windows con Visual Studio, el proyecto compilado puede enviarse a clúster Linux o de HDInsight de Windows. Solo clústeres basados en Linux crean después de soporte técnico de 28/10/2016 SCP.NET topologías.
>
> Para usar una topología de C# con un clúster basado en Linux, deberá actualizar el paquete Microsoft.SCP.Net.SDK NuGet utilizado por su proyecto a la versión 0.10.0.6 o superior. La versión del paquete también debe coincidir con la versión principal de tormenta instalado en HDInsight. Por ejemplo, tormenta en HDInsight versiones 3.3 y 3.4 utilizar la versión de tormenta 0.10.x, mientras que HDInsight 3.5 utiliza tormenta 1.0. x.
> 
> C# topologías en clústeres basados en Linux deben utilizar .NET 4.5 y usar Mono para ejecutar en el clúster HDInsight. La mayoría de elementos funcionará, pero debe comprobar el documento [Mono compatibilidad](http://www.mono-project.com/docs/about-mono/compatibility/) para posible incompatibilidad.
>
> Para obtener una versión de Java de este proyecto, que también funcionarán en un clúster basado en Windows o Linux, vea [eventos del proceso de Azure evento Hubs con tormenta en HDInsight (Java)](hdinsight-storm-develop-java-event-hub-topology.md).

## <a name="prerequisites"></a>Requisitos previos

- Una suscripción de Azure. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Un usuario de Azure Active Directory con acceso de [Power BI](https://powerbi.com)

* Visual Studio (una de las siguientes versiones)

    * Visual Studio 2012 con [4 de actualización](http://www.microsoft.com/download/details.aspx?id=39305)

    * 2013 de Visual Studio con la [Comunidad de Visual Studio 2013](http://go.microsoft.com/fwlink/?linkid=517284&clcid=0x409) o [Actualizar 4](http://www.microsoft.com/download/details.aspx?id=44921)

    * [Visual Studio de 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)

* Las herramientas de HDInsight para Visual Studio: vea [Introducción al uso de las herramientas de HDInsight para Visual Studio](../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md) para obtener información sobre la información de instalación.

## <a name="how-it-works"></a>Cómo funciona

Este ejemplo contiene una topología de tormenta C# que genera aleatoriamente datos del registro de Internet Information Services (IIS). Estos datos se escriben en una base de datos de SQL y desde allí se usa para generar informes de Power BI.

A continuación se muestra una lista de los archivos que implementar la funcionalidad principal de este ejemplo.

* **SqlAzureBolt.cs**: escribe la información que se produce en la topología de tormenta a base de datos de SQL.

* **IISLogsTable.sql**: instrucciones Transact-SQL que se utilizan para generar la base de datos que se almacenan los datos en.

> [AZURE.WARNING] Debe crear la tabla de base de datos de SQL antes de iniciar la topología en el clúster HDInsight.

## <a name="download-the-example"></a>Descargar el ejemplo

Descargue el [ejemplo HDInsight C# tormenta Power BI](https://github.com/Azure-Samples/hdinsight-dotnet-storm-powerbi). Para descargarlo, ya sea horquilla o clonar mediante [git](http://git-scm.com/)o usar el vínculo **Descargar** para descargar un .zip del archivo.

## <a name="create-a-database"></a>Crear una base de datos

1. Siga los pasos en el documento de [tutorial de base de datos SQL](../sql-database/sql-database-get-started.md) para crear una nueva base de datos de SQL.

2. Conectarse a la base de datos siguiendo los pasos en el documento de [conectarse a una base de datos de SQL con Visual Studio](../sql-database/sql-database-connect-query.md) para conectarse a la base de datos.

4. Haga clic con el botón secundario en la base de datos en el Explorador de objetos y cree una __Nueva consulta__. Pegar el contenido del archivo __IISLogsTable.sql__ incluido en el proyecto descargado en la ventana de consulta y, a continuación, use Ctrl + Mayús + E para ejecutar la consulta. Recibirá un mensaje que los comandos se completó correctamente.

    Una vez se termine, habrá una nueva tabla denominada __IISLOGS__ en la base de datos.

## <a name="configure-the-sample"></a>Configurar los datos de ejemplo

1. Desde el [portal de Azure](https://portal.azure.com), seleccione la base de datos SQL. En la sección __Essentials__ del módulo de base de datos SQL, seleccione __mostrar cadenas de conexión de base de datos__. En la lista que aparece, copie la información de __ADO.NET (autenticación SQL)__ .

1. Abra el ejemplo en Visual Studio. Desde el **Explorador de soluciones**, abra el archivo **App.config** y, a continuación, busque la entrada siguiente:

        <add key="SqlAzureConnectionString" value="##TOBEFILLED##" />
    
    Reemplace el valor __TOBEFILLED ##__ por la cadena de conexión de base de datos que copió en el paso anterior. Reemplazar __{su\_username}__ y __{su\_contraseña}__ con el nombre de usuario y contraseña para la base de datos.

2. Guarde y cierre los archivos.

## <a name="deploy-the-sample"></a>Implementar los datos de ejemplo

1. Desde el **Explorador de soluciones**, haga clic en el proyecto **StormToSQL** y seleccione **Enviar a tormenta en HDInsight**. Seleccione el clúster HDInsight desde el cuadro de diálogo de la lista desplegable de **Clúster de tormenta** .

    > [AZURE.NOTE] Puede tardar unos segundos de la lista desplegable de **Tormenta clúster** para rellenar nombres de servidor.
    >
    > Si se le solicita, escriba las credenciales de inicio de sesión para su suscripción de Azure. Si tiene más de una suscripción, inicie sesión en la tabla que contiene la tormenta en clúster HDInsight.

2. Cuando la topología se ha enviado correctamente, debería aparecer el topologías tormenta para el clúster. Seleccione la entrada de SqlAzureWriterTopology de la lista para ver información sobre la topología de ejecución.

    ![Topologías, con la topología seleccionada](./media/hdinsight-storm-power-bi-topology/topologyview.png)

    Puede usar esta vista para ver información sobre la topología o haga doble clic en entradas (como SqlAzureBolt) para ver información específica de un componente en la topología.

3. Una vez que tenga la topología ejecutó unos minutos, vuelva a la ventana de consulta SQL usada para crear la base de datos. Reemplace las instrucciones existentes con los siguientes elementos.

        select * from iislogs;
    
    Utilice Ctrl + Mayús + E para ejecutar la consulta y debería recibir resultados similares a los siguientes.
    
        1   2016-05-27 17:57:14.797 255.255.255.255 /bar    GET 200
        2   2016-05-27 17:57:14.843 127.0.0.1   /spam/eggs  POST    500
        3   2016-05-27 17:57:14.850 123.123.123.123 /eggs   DELETE  200
        4   2016-05-27 17:57:14.853 127.0.0.1   /foo    POST    404
        5   2016-05-27 17:57:14.853 10.9.8.7    /bar    GET 200
        6   2016-05-27 17:57:14.857 192.168.1.1 /spam   DELETE  200

    Se trata de datos que se ha escrito de la topología de tormenta.

## <a name="create-a-report"></a>Crear un informe

1. Conectarse a la [base de datos de SQL Azure conector](https://app.powerbi.com/getdata/bigdata/azure-sql-database-with-live-connect) para Power BI.

2. Dentro de __las bases de datos__, seleccione __obtener__.

3. Seleccione la __Base de datos de SQL Azure__y, a continuación, seleccione __Conectar__.

4. Escriba la información para conectarse a la base de datos de SQL Azure. Puede encontrar esta visita el [portal de Azure](https://portal.azure.com) y seleccionando la base de datos SQL.

    > [AZURE.NOTE] También puede establecer el intervalo de actualización y filtros personalizados mediante __Habilitar opciones avanzadas__ del cuadro de diálogo Conectar.

5. Cuando se haya conectado, verá un conjunto de datos con el mismo nombre como conectado a una base de datos. Seleccione el conjunto de datos para comenzar a diseñar un informe.

3. En __campos__, expanda la entrada __IISLOGS__ . Seleccione la casilla de verificación para __URISTEM__. Se creará un nuevo informe que muestra las raíces URI (/ foo, / barra, etc.) ha iniciado sesión en la base de datos.

    ![Crear un informe](./media/hdinsight-storm-power-bi-topology/createreport.png)

5. A continuación, arrastre el __método__ al informe. El informe se actualizará para mostrar la raíces y el método HTTP correspondiente que se utiliza para la solicitud HTTP.

    ![agregar los datos del método](./media/hdinsight-storm-power-bi-topology/uristemandmethod.png)

4. En la columna de __visualizaciones__ , seleccione el icono de __campos__ y, a continuación, seleccione la flecha abajo junto al __método__ en la sección __valores__ . En la lista que aparece, seleccione __recuento__. Esto cambiará el informe para mostrar un recuento de cuántas veces se tiene acceso a un URI específico.

    ![Cambiar a un recuento de métodos](./media/hdinsight-storm-power-bi-topology/count.png)

6. A continuación, seleccione el __gráfico de columnas apiladas__ para cambiar cómo se muestra la información.

    ![Cambiar a un gráfico apilado](./media/hdinsight-storm-power-bi-topology/stackedcolumn.png)

7. Una vez que el informe cómo desea, utilice la entrada __Guardar__ en el menú para especificar un nombre y guardar el informe.

## <a name="stop-the-topology"></a>Detener la topología

La topología seguirá ejecutándose hasta detenerlo o eliminar la tormenta en clúster HDInsight. Realice los pasos siguientes para detener la topología.

1. En Visual Studio, vuelva al Visor de topología y seleccione la topología.

2. Seleccione el botón **Eliminar** para detener la topología.

    ![Botón de la topología de resumen de interrupción](./media/hdinsight-storm-power-bi-topology/killtopology.png)

## <a name="delete-your-cluster"></a>Eliminar el clúster

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Pasos siguientes

En este documento, ha aprendido cómo enviar datos de una topología de tormenta a la base de datos SQL, a continuación, visualizar los datos con Power BI. Para obtener información sobre cómo trabajar con otras tecnologías de Azure con tormenta en HDInsight, consulte lo siguiente:

* [Topologías de ejemplo para tormenta en HDInsight](hdinsight-storm-example-topology.md)

<properties
 pageTitle="Correlación de eventos con el tiempo con tormenta y HBase en HDInsight"
 description="Obtenga información sobre cómo relacionar los eventos que llegan en distintos momentos mediante tormenta y HBase en HDInsight."
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

# <a name="correlate-events-over-time-with-storm-and-hbase-on-hdinsight"></a>Correlación de eventos con el tiempo con tormenta y HBase en HDInsight

Si utiliza un almacén de datos persistente con Apache tormenta, puede relacionar entradas de datos que llegan en distintos momentos. Por ejemplo, los eventos de inicio de sesión y cierre de sesión para una sesión de usuario calcular cuánto tiempo pasado la sesión de vinculación.

En este documento, obtendrá información sobre cómo crear una topología de tormenta C# básica que realiza un seguimiento de eventos de inicio y cierre de sesión para sesiones de usuario y a continuación, calcula la duración de la sesión. La topología usa HBase como un almacén de datos persistente. HBase también le permite realizar consultas por lotes en los datos históricos para obtener información adicional, como el número de sesiones de usuario se ha comenzado o terminado durante un período de tiempo determinado.

## <a name="prerequisites"></a>Requisitos previos

- Visual Studio y las herramientas de HDInsight para Visual Studio: vea [Introducción al uso de las herramientas de HDInsight para Visual Studio](../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md) para obtener información de instalación.

- Tormenta Apache en HDInsight clúster (basado en Windows). Esta opción ejecuta la topología de tormenta, que procesa datos entrantes y lo almacena en HBase.

    > [AZURE.IMPORTANT] Mientras SCP.NET topologías son compatibles con clústeres basados en Linux tormenta creados después de 28/10/2016, el SDK HBase paquete .NET disponible a partir de 28/10/2016 no funciona correctamente en Linux.

- HBase Apache en clúster HDInsight (Linux o basado en Windows). Este es el almacén de datos en este ejemplo.

## <a name="architecture"></a>Arquitectura

![Diagrama del flujo de datos a través de la topología](./media/hdinsight-storm-correlation-topology/correlationtopology.png)

Correlación de eventos, requiere un identificador común para el origen de evento. Por ejemplo, un identificador de usuario, Id. de sesión u otro tipo de datos que es un) único y b) incluido en todos los datos enviados tormenta. Este ejemplo usa un valor GUID para representar un identificador de sesión.

Este ejemplo consta de dos clústeres de HDInsight:

-   HBase: el almacén de datos persistente para datos históricos

-   Tormenta: se usa para recopilar datos entrantes

Los datos se generan aleatoriamente por la topología de tormenta y consta de los siguientes elementos:

-   Id. de sesión: un GUID que identifica cada sesión

-   : Un inicio o finalización del evento. En este ejemplo, iniciar siempre tiene lugar antes de finalizar

-   Tiempo: hora del evento.

Estos datos se procesa y se almacenan en HBase.

### <a name="storm-topology"></a>Topología de tormenta

Cuando se inicia una sesión, un evento de **Inicio** se recibió por la topología y HBase ha iniciado sesión. Cuando se recibe un evento de **fin** , la topología recupera el evento de **Inicio** y calcula el tiempo entre dos eventos. Este valor de **duración** se almacena en HBase junto con la información del evento **final** .

> [AZURE.IMPORTANT] Aunque esta topología muestra el modelo básico, una solución de producción debería tener un diseño de las siguientes situaciones:
>
> - Eventos que proceden de orden
> - Eventos duplicados
> - Eventos perdidos

La topología de ejemplo se compone de los siguientes componentes:

-   Session.cs: simula una sesión de usuario mediante la creación de un ID aleatorio, inicio tiempo y cuánto durará la sesión

-   Spout.cs: crea 100 sesiones, emite un evento de inicio, espera el tiempo de espera aleatorio para cada sesión y, a continuación, emite un evento de fin. A continuación, reciclados finalización sesiones para generar nuevos.

-   HBaseLookupBolt.cs: se usa el identificador de sesión para buscar información de HBase sesión. Cuando se procesa un evento de fin, busca el correspondiente evento de inicio y calcula la duración de la sesión.

-   HBaseBolt.cs: Almacena información en HBase.

-   TypeHelper.cs: Ayuda con la conversión de tipos al leer o escribir en HBase.

### <a name="hbase-schema"></a>Esquema de HBase

En HBase, los datos se almacenan en una tabla con la siguiente configuración y esquema:

-   Clave de fila: la sesión se usa como clave para las filas de esta tabla

-   Familia de columna: el nombre de la familia es 'cf'. Columnas almacenadas en esta serie son:

    -   evento: inicio o fin

    -   tiempo: el tiempo en milisegundos que se produjo el evento

    -   duración: la duración entre eventos de inicio y finalización

-   VERSIONES: la familia 'cf' está establecida para conservar las 5 versiones de cada fila

    > [AZURE.NOTE] Versiones son un registro de los valores anteriores almacenados para una clave de la fila específica. De forma predeterminada, HBase sólo devuelve el valor de la versión más reciente de una fila. En este caso, la misma fila se usa para todos los eventos (inicio, fin.) se identifica cada versión de una fila mediante el valor de la marca de tiempo. Proporciona una vista del historial de eventos registrados para un identificador específico.

## <a name="download-the-project"></a>Descargar el proyecto

El proyecto de ejemplo puede descargarse desde [https://github.com/Azure-Samples/hdinsight-storm-dotnet-event-correlation](https://github.com/Azure-Samples/hdinsight-storm-dotnet-event-correlation).

Esta descarga contiene los siguientes proyectos de C#:

-   CorrelationTopology: Topología de tormenta C# que emite aleatoriamente eventos de inicio y finalización para sesiones de usuario. Cada sesión dura entre 1 y 5 minutos.

-   SessionInfo: Aplicación de consola de C# que crea la tabla HBase y proporciona consultas de ejemplo para obtener información sobre almacena datos de la sesión.

## <a name="create-the-table"></a>Crear la tabla

1. Abra el proyecto **SessionInfo** en Visual Studio.

2. En el **Explorador de soluciones**, haga clic en el proyecto **SessionInfo** y seleccione **Propiedades**.

    ![Captura de pantalla de menú con propiedades seleccionado](./media/hdinsight-storm-correlation-topology/selectproperties.png)

3. Seleccione **configuración**y luego establezca los siguientes valores:

    -   HBaseClusterURL: la dirección URL del clúster de HBase. Por ejemplo, https://myhbasecluster.azurehdinsight.net

    -   HBaseClusterUserName: la cuenta de administrador/HTTP usuario para el clúster

    -   HBaseClusterPassword: la contraseña de la cuenta de usuario de administración/HTTP

    -   HBaseTableName: el nombre de la tabla que desea usar con este ejemplo

    -   HBaseTableColumnFamily: El nombre de la familia de columna

    ![Imagen del cuadro de diálogo de configuración](./media/hdinsight-storm-correlation-topology/settings.png)

5. Ejecute la solución. Cuando se le solicite, seleccione la clave 'c' para crear la tabla en el clúster HBase.

## <a name="build-and-deploy-the-storm-topology"></a>Compile e implemente la topología de tormenta

1.  Abra la solución **CorrelationTopology** en Visual Studio.

2.  En el **Explorador de soluciones**, haga clic con el botón secundario del mouse en el proyecto **CorrelationTopology** y seleccione Propiedades.

3.  En la ventana Propiedades, seleccione **configuración** y proporcione la siguiente información. La primera 5 debe tener los mismos valores que utiliza el proyecto **SessionInfo** :

    -   HBaseClusterURL: la dirección URL del clúster de HBase. Por ejemplo, https://myhbasecluster.azurehdinsight.net

    -   HBaseClusterUserName: la cuenta de administrador/HTTP usuario para el clúster

    -   HBaseClusterPassword: la contraseña de la cuenta de usuario de administración/HTTP

    -   HBaseTableName: el nombre de la tabla que desea usar con este ejemplo. Debe contener el mismo nombre de tabla que se utiliza en el proyecto SessionInfo

    -   HBaseTableColumnFamily: El nombre de la familia de columna. Debe contener el mismo nombre de familia de la columna que se utiliza en el proyecto SessionInfo

    > [AZURE.IMPORTANT] No cambie la HBaseTableColumnNames, como los valores predeterminados de los nombres que se utiliza **SessionInfo** para recuperar datos.

4.  Guardar las propiedades y luego generar el proyecto.

5.  En el **Explorador de soluciones**, haga clic con el botón secundario del mouse en el proyecto y haga **clic en Enviar a tormenta en HDInsight**. Si se le solicita, escriba las credenciales para la suscripción de Azure.

    ![Imagen de las enviar al elemento de menú de tormenta](./media/hdinsight-storm-correlation-topology/submittostorm.png)

6.  En el cuadro de diálogo **Enviar topología** , seleccione el clúster de tormenta que se ejecutará esta topología.

    > [AZURE.NOTE] La primera vez que envíe una topología, puede tardar unos segundos para recuperar el nombre de los clústeres HDInsight.

7.  Una vez que se ha cargado la topología y se ha enviado al clúster, la **Vista de topología de tormenta** abrirá y mostrar la topología de ejecución. Seleccione el **CorrelationTopology** y use el botón Actualizar en la parte superior derecha de la página para actualizar la información de topología.

    ![Imagen de la vista de topología](./media/hdinsight-storm-correlation-topology/topologyview.png)

    Cuando la topología comienza la generación de datos, se incrementará el valor de la columna **emitida** .

    > [AZURE.NOTE] Si no se abre automáticamente la **Vista de topología de tormenta** , realice los pasos siguientes para abrirlo:
    >
    > 1. En el **Explorador de soluciones**, expanda **Azure**y, a continuación, expanda **HDInsight**.
    >
    > 2. Haga clic con el botón secundario del mouse en el clúster de tormenta que se está ejecutando la topología y, a continuación, seleccione **Vista tormenta topologías**

## <a name="query-the-data"></a>Los datos de la consulta

Una vez emitidos datos, realice los pasos siguientes para consultar los datos.

1. Volver al proyecto **SessionInfo** . Si no se ejecuta, inicie una nueva instancia de la misma.

2. Cuando se le solicite, seleccione **s** para buscar eventos de inicio. Le pedirá que introduzca una hora de inicio y finalización para definir un intervalo de tiempo - se devolverán sólo los eventos entre estos dos veces.

    Utilice el siguiente formato al introducir las horas de inicio y finalización: hh: mm y 'm' o 'p.m.'. Por ejemplo, 11:20 pm.

    Puesto que ha comenzado a la topología, use un inicio de tiempo de antes de que se implemente y una hora de finalización de ahora. Esto debe capturar la mayoría de los eventos de inicio generados cuando inicia. Cuando se ejecuta la consulta, verá una lista de entradas similares al siguiente:

        Session e6992b3e-79be-4991-afcf-5cb47dd1c81c started at 6/5/2015 6:10:15 PM. Timestamp = 1433527820737

Búsqueda de eventos de fin funciona del mismo modo eventos de inicio. Sin embargo, los eventos de finalización se generan aleatoriamente entre 1 y 5 minutos después del evento de inicio. Así que debe probar unos intervalos de tiempo para encontrar los eventos de finalización. Eventos de fin también contendrá la duración de la sesión: la diferencia entre la hora del evento de inicio y hora del evento final. Aquí tiene un ejemplo de datos para los eventos de finalización:

    Session fc9fa8e6-6892-4073-93b3-a587040d892e lasted 2 minutes, and ended at 6/5/2015 6:12:15 PM

> [AZURE.NOTE] Aunque son los valores de hora que escriba en la hora local, el tiempo que se devuelven de la consulta será UTC.

##<a name="stop-the-topology"></a>Detener la topología

Cuando esté listo para detener la topología, vuelva al proyecto **CorrelationTopology** en Visual Studio. En la **Vista de topología de tormenta**, seleccione la topología y, a continuación, use el botón **Eliminar** en la parte superior de la vista de topología.

##<a name="delete-your-cluster"></a>Eliminar el clúster

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

##<a name="next-steps"></a>Pasos siguientes

Para consultar más ejemplos de tormenta, vea [topologías de ejemplo para tormenta en HDInsight](hdinsight-storm-example-topology.md).
 

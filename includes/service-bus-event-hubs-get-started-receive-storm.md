## <a name="receive-messages-with-apache-storm"></a>Recibir mensajes con Apache tormenta

[**Tormenta Apache**](https://storm.incubator.apache.org) es un sistema de distribuidas de cálculo en tiempo real que simplifica confiable procesamiento de secuencias ilimitados de datos. Esta sección muestra cómo usar un pitorro tormenta Hubs de evento para recibir eventos de Hubs de evento. Tormenta Apache se pueden dividir eventos en varios procesos alojados en distintos nodos. La integración de evento Hubs con tormenta simplifica consumo de eventos por puntos de control transparente su progreso mediante la instalación de Zookeeper de tormenta, la administración de los controles persistentes y paralelo recibe de Hubs de evento.

Para obtener más información sobre el evento Hubs recibir tramas, vea la [información general de Hubs de evento][].

En este tutorial se usa una instalación de [Tormenta HDInsight][] , que se incluye con pitorro Hubs de evento ya están disponible.

1. Siga el procedimiento de [Tormenta HDInsight - Introducción](../articles/hdinsight/hdinsight-storm-overview.md) para crear un nuevo clúster de HDInsight y conectarse a través de escritorio remoto.

2. Copiar la `%STORM_HOME%\examples\eventhubspout\eventhubs-storm-spout-0.9-jar-with-dependencies.jar` archivo para su entorno de desarrollo local. Esta página contiene la pitorro de tormenta de eventos.

3. Use el comando siguiente para instalar el paquete en el almacén local de experto. Esto le permite agregar como una referencia en el proyecto de tormenta en un paso posterior.

        mvn install:install-file -Dfile=target\eventhubs-storm-spout-0.9-jar-with-dependencies.jar -DgroupId=com.microsoft.eventhubs -DartifactId=eventhubs-storm-spout -Dversion=0.9 -Dpackaging=jar

4. En Eclipse, cree un nuevo proyecto de experto (haga clic en **archivo**, a continuación, **nuevo**y **Project**).

    ![][12]

5. Seleccione la **ubicación de área de trabajo predeterminada de uso**y luego haga clic en **siguiente**

6. Seleccione el Arquetipo **Tutorial de Arquetipo experto en** y luego haga clic en **siguiente**

7. Insertar un **ID** y **ArtifactId**y luego haga clic en **Finalizar**

8. En **pom.xml**, agregue las siguientes dependencias en el `<dependency>` nodo.

        <dependency>
            <groupId>org.apache.storm</groupId>
            <artifactId>storm-core</artifactId>
            <version>0.9.2-incubating</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>com.microsoft.eventhubs</groupId>
            <artifactId>eventhubs-storm-spout</artifactId>
            <version>0.9</version>
        </dependency>
        <dependency>
            <groupId>com.netflix.curator</groupId>
            <artifactId>curator-framework</artifactId>
            <version>1.3.3</version>
            <exclusions>
                <exclusion>
                    <groupId>log4j</groupId>
                    <artifactId>log4j</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>org.slf4j</groupId>
                    <artifactId>slf4j-log4j12</artifactId>
                </exclusion>
            </exclusions>
            <scope>provided</scope>
        </dependency>

9. En la carpeta **src** , cree un archivo llamado **Config.properties** y copie el siguiente contenido, sustituir los valores siguientes:

        eventhubspout.username = ReceiveRule

        eventhubspout.password = {receive rule key}

        eventhubspout.namespace = ioteventhub-ns

        eventhubspout.entitypath = {event hub name}

        eventhubspout.partitions.count = 16

        # if not provided, will use storm's zookeeper settings
        # zookeeper.connectionstring=localhost:2181

        eventhubspout.checkpoint.interval = 10

        eventhub.receiver.credits = 10

    El valor de **eventhub.receiver.credits** determina cuántos eventos se procesan por lotes antes de publicarlas a la canalización de tormenta. Para simplificar, en este ejemplo se establece este valor a 10. En producción, debe suele establecerse a valores superiores; Por ejemplo, 1024.

10. Crear una nueva clase denominada **LoggerBolt** con el siguiente código:

        import java.util.Map;
        import org.slf4j.Logger;
        import org.slf4j.LoggerFactory;
        import backtype.storm.task.OutputCollector;
        import backtype.storm.task.TopologyContext;
        import backtype.storm.topology.OutputFieldsDeclarer;
        import backtype.storm.topology.base.BaseRichBolt;
        import backtype.storm.tuple.Tuple;

        public class LoggerBolt extends BaseRichBolt {
            private OutputCollector collector;
            private static final Logger logger = LoggerFactory
                      .getLogger(LoggerBolt.class);

            @Override
            public void execute(Tuple tuple) {
                String value = tuple.getString(0);
                logger.info("Tuple value: " + value);

                collector.ack(tuple);
            }

            @Override
            public void prepare(Map map, TopologyContext context, OutputCollector collector) {
                this.collector = collector;
                this.count = 0;
            }

            @Override
            public void declareOutputFields(OutputFieldsDeclarer declarer) {
                // no output fields
            }

        }

    Este rayo tormenta registra el contenido de los eventos recibidos. Esto puede ampliarse fácilmente para almacenar tuplas en un servicio de almacenamiento. El [tutorial de análisis de sensor HDInsight] usa este mismo método para almacenar los datos en HBase.

11. Crear una clase denominada **LogTopology** con el siguiente código:

        import java.io.FileReader;
        import java.util.Properties;
        import backtype.storm.Config;
        import backtype.storm.LocalCluster;
        import backtype.storm.StormSubmitter;
        import backtype.storm.generated.StormTopology;
        import backtype.storm.topology.TopologyBuilder;
        import com.microsoft.eventhubs.samples.EventCount;
        import com.microsoft.eventhubs.spout.EventHubSpout;
        import com.microsoft.eventhubs.spout.EventHubSpoutConfig;

        public class LogTopology {
            protected EventHubSpoutConfig spoutConfig;
            protected int numWorkers;

            protected void readEHConfig(String[] args) throws Exception {
                Properties properties = new Properties();
                if (args.length > 1) {
                    properties.load(new FileReader(args[1]));
                } else {
                    properties.load(EventCount.class.getClassLoader()
                            .getResourceAsStream("Config.properties"));
                }

                String username = properties.getProperty("eventhubspout.username");
                String password = properties.getProperty("eventhubspout.password");
                String namespaceName = properties
                        .getProperty("eventhubspout.namespace");
                String entityPath = properties.getProperty("eventhubspout.entitypath");
                String zkEndpointAddress = properties
                        .getProperty("zookeeper.connectionstring"); // opt
                int partitionCount = Integer.parseInt(properties
                        .getProperty("eventhubspout.partitions.count"));
                int checkpointIntervalInSeconds = Integer.parseInt(properties
                        .getProperty("eventhubspout.checkpoint.interval"));
                int receiverCredits = Integer.parseInt(properties
                        .getProperty("eventhub.receiver.credits")); // prefetch count
                                                                    // (opt)
                System.out.println("Eventhub spout config: ");
                System.out.println("  partition count: " + partitionCount);
                System.out.println("  checkpoint interval: "
                        + checkpointIntervalInSeconds);
                System.out.println("  receiver credits: " + receiverCredits);

                spoutConfig = new EventHubSpoutConfig(username, password,
                        namespaceName, entityPath, partitionCount, zkEndpointAddress,
                        checkpointIntervalInSeconds, receiverCredits);

                // set the number of workers to be the same as partition number.
                // the idea is to have a spout and a logger bolt co-exist in one
                // worker to avoid shuffling messages across workers in storm cluster.
                numWorkers = spoutConfig.getPartitionCount();

                if (args.length > 0) {
                    // set topology name so that sample Trident topology can use it as
                    // stream name.
                    spoutConfig.setTopologyName(args[0]);
                }
            }

            protected StormTopology buildTopology() {
                TopologyBuilder topologyBuilder = new TopologyBuilder();

                EventHubSpout eventHubSpout = new EventHubSpout(spoutConfig);
                topologyBuilder.setSpout("EventHubsSpout", eventHubSpout,
                        spoutConfig.getPartitionCount()).setNumTasks(
                        spoutConfig.getPartitionCount());
                topologyBuilder
                        .setBolt("LoggerBolt", new LoggerBolt(),
                                spoutConfig.getPartitionCount())
                        .localOrShuffleGrouping("EventHubsSpout")
                        .setNumTasks(spoutConfig.getPartitionCount());
                return topologyBuilder.createTopology();
            }

            protected void runScenario(String[] args) throws Exception {
                boolean runLocal = true;
                readEHConfig(args);
                StormTopology topology = buildTopology();
                Config config = new Config();
                config.setDebug(false);

                if (runLocal) {
                    config.setMaxTaskParallelism(2);
                    LocalCluster localCluster = new LocalCluster();
                    localCluster.submitTopology("test", config, topology);
                    Thread.sleep(5000000);
                    localCluster.shutdown();
                } else {
                    config.setNumWorkers(numWorkers);
                    StormSubmitter.submitTopology(args[0], config, topology);
                }
            }

            public static void main(String[] args) throws Exception {
                LogTopology topology = new LogTopology();
                topology.runScenario(args);
            }
        }


    Esta clase crea un nuevo Hubs evento pitorro, con las propiedades de la configuración del archivo a instatiate. Es importante que tenga en cuenta que este ejemplo crea tantas tareas spouts como el número de particiones en el Hub de evento, para poder usar el paralelismo máximo permitido por ese concentrador de evento.

<!-- Links -->
[Información general de Hubs de evento]: ../articles/event-hubs/event-hubs-overview.md
[HDInsight de tormenta]: ../articles/hdinsight/hdinsight-storm-overview.md
[Tutorial de análisis de sensor HDInsight]: ../articles/hdinsight/hdinsight-storm-sensor-data-analysis.md

<!-- Images -->

[12]: ./media/service-bus-event-hubs-get-started-receive-storm/create-storm1.png
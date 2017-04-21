<properties
   pageTitle="Calcular las opciones de contexto de servidor R en HDInsight (vista preliminar) | Microsoft Azure"
   description="Obtenga más información sobre las opciones de contexto de cálculo diferentes disponibles para los usuarios con el servidor de R de HDInsight (vista previa)"
   services="HDInsight"
   documentationCenter=""
   authors="jeffstokes72"
   manager="jhubbard"
   editor="cgronlun"
/>

<tags
   ms.service="HDInsight"
   ms.devlang="R"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-services"
   ms.date="10/18/2016"
   ms.author="jeffstok"
/>

# <a name="compute-context-options-for-r-server-on-hdinsight-preview"></a>Calcular las opciones de contexto de servidor R en HDInsight (vista previa)

Servidor R Microsoft Azure HDInsight (vista preliminar) proporciona las últimas capacidades de análisis de R. Utiliza datos almacenados en HDFS en un contenedor en el sistema de archivos de Linux local o en su cuenta de almacenamiento de [Blobs de Windows Azure](../storage/storage-introduction.md "almacenamiento de blobs de Windows Azure") . Puesto que servidor R se basa en Abrir origen R, pueden aprovechar las aplicaciones de R que generar cualquiera de los paquetes de Abrir origen R 8000 +. También pueden aprovechar las rutinas en [utilizar otros equipos](http://www.revolutionanalytics.com/revolution-r-enterprise-scaler "Utilizar otros equipos de revolución análisis"), paquete de análisis de datos grande de Microsoft que se incluye con el servidor de R.  

El nodo del borde de un clúster de Premium proporciona un lugar cómodo para conectarse al clúster y ejecutar las secuencias de comandos de R. Con un nodo del borde, tiene la opción de la ejecución de funciones distribuidas paralelizadas utilizar de otros equipos a través de las muestras del servidor de nodo de borde. También tiene la opción funcione en todos los nodos del clúster con Hadoop mapa reducir utilizar de otros equipos o motor calcular contextos.

## <a name="compute-contexts-for-an-edge-node"></a>Calcular contextos para un nodo del borde

En general, una secuencia de comandos de R que se ejecute en R Server en el nodo del borde se ejecuta en el intérprete R en ese nodo. La excepción es esos pasos que llaman a una función de utilizar otros equipos. Las llamadas de utilizar otros equipos que se ejecuten en un entorno de cálculo que depende de cómo haya configurado el contexto de cálculo de utilizar otros equipos.  Cuando se ejecuta la secuencia de comandos de R de un nodo del borde, los posibles valores del contexto cálculo son locales secuencial ('local'), paralelo local ('localpar'), reducir el mapa y motor.

Las opciones 'locales' y 'localpar' difieren solo en cómo se ejecutan llamadas rxExec. Ejecutan otras llamadas a función de recepción de forma paralela a través de núcleos disponibles a menos que especifique lo contrario mediante el uso de la opción de numCoresToUse utilizar otros equipos, por ejemplo, rxOptions(numCoresToUse=6). A continuación resumen las distintas opciones de contexto de cálculo

| Calcular contexto  | Cómo configurar                      | Contexto de ejecución                                                                     |
|------------------|---------------------------------|---------------------------------------------------------------------------------------|
| Local secuencial | rxSetComputeContext('local')    | Llamadas de ejecución paralelizada a través de las muestras del servidor de nodo del borde, excepto rxExec que se ejecutan en serie |
| Paralelo local   | rxSetComputeContext('localpar') | Ejecución paralelizada a través de las muestras de servidor perimetral de nodo                                 |
| Motor            | RxSpark()                       | Ejecutar en paralelo ejecución distribuida a través de motor en todos los nodos del clúster HDI      |
| Reducir el mapa       | RxHadoopMR()                    | Ejecutar en paralelo ejecución distribuida a través de reducir el mapa en todos los nodos del clúster HDI |


Suponiendo que le gustaría ejecución paralelizada los fines de rendimiento, hay tres opciones. Opción que elija depende de la naturaleza de su trabajo análisis y el tamaño y la ubicación de los datos.

## <a name="guidelines-for-deciding-on-a-compute-context"></a>Directrices para decidir en un contexto de cálculo

Actualmente, no hay ninguna fórmula que le indica que calcular contexto para usar. Sin embargo, existen algunas orientaciones que pueden ayudarle a tomar la decisión correcta o, al menos, le ayudarán a limitar las opciones antes de ejecutar una referencia. Estos principios directivos incluyen:

1.  El sistema de archivos de Linux local es más rápido que HDFS.
2.  Análisis repetidos son más rápidos si los datos son locales y si se encuentra en XDF.
3.  Es preferible transmita pequeñas cantidades de datos de un origen de datos de texto. Si la cantidad de datos es más grande, convertir en XDF antes del análisis.
4.  Se convierte en la sobrecarga de copiar o secuencias de datos para el nodo del borde para análisis tiene dificultades para grandes cantidades de datos.
5.  Motor es más rápido que reducir el mapa de análisis en Hadoop.

Dado que estos principios, algunas reglas general para seleccionar un contexto de cálculo son:

### <a name="local"></a>Local

- Si la cantidad de datos para analizar es pequeña y no requiere análisis repetidas, transmita directamente en la rutina de análisis y usar 'local' o 'localpar'.
- Si la cantidad de datos para analizar es pequeña o mediana y requiere análisis repetidas, cópiela en el sistema de archivos local, importar a XDF y analizar a través 'local' o 'localpar'.

### <a name="hadoop-spark"></a>Motor de Hadoop

- Si la cantidad de datos para analizar es grande, a continuación, importarla a XDF en HDFS (a menos que el almacenamiento es un problema) y analizar a través de 'Motor'.

### <a name="hadoop-map-reduce"></a>Reducir el mapa de Hadoop

- Usar solo si se produce un problema con el uso del motor insuperables calcular contexto, ya que suele ser más lento.  

## <a name="inline-help-on-rxsetcomputecontext"></a>Ayuda en línea en rxSetComputeContext

Para obtener más información y ejemplos de utilizar otros equipos cálculo contextos, consulte el en línea en R de ayuda en el método rxSetComputeContext, por ejemplo:

    > ?rxSetComputeContext

También puede hacer referencia a la "utilizar otros equipos distribuido informática guía" que está disponible en la biblioteca de(https://msdn.microsoft.com/library/mt674634.aspx "Servidor R en MSDN") [R Server MSDN].


## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido cómo crear un nuevo clúster de HDInsight que incluye servidor R. Además, ha aprendido los conceptos básicos del uso de la consola de R desde una sesión SSH. Ahora puede leer los artículos siguientes para conocer otras maneras de trabajar con el servidor de R en HDInsight:

- [Información general del servidor R Hadoop](hdinsight-hadoop-r-server-overview.md)
- [Introducción a servidor R para Hadoop](hdinsight-hadoop-r-server-get-started.md)
- [Agregar servidor de RStudio a HDInsight Premium](hdinsight-hadoop-r-server-install-r-studio.md)
- [Azure opciones de almacenamiento para servidor R en HDInsight Premium](hdinsight-hadoop-r-server-storage.md)

<properties
    pageTitle="¿Qué es R en HDInsight? Introducción al servidor de R en HDInsight (vista preliminar) | Microsoft Azure"
    description="¿Qué es R Server HDInsight (vista previa) y cómo usar R Server para crear aplicaciones para análisis de datos grande."
    services="hdinsight"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/17/2016"
   ms.author="jeffstok"/>


# <a name="overview-of-r-server-on-hdinsight-preview"></a>Información general del servidor R HDInsight \(vista previa\)

Con Microsoft Azure HDInsight Premium, Microsoft R Server ahora está disponible como opción cuando cree clústeres HDInsight de Azure. Esta nueva capacidad proporciona datos científicos, estadísticos y los programadores R con acceso a petición a scalable, distribuyen métodos de análisis en HDInsight.

Clústeres pueden tamaño a los proyectos y tareas en curso y se deshace cuando ya no son necesarios. Ya que son parte de HDInsight de Azure, estos clústeres vienen con soporte técnico de 24/7 de nivel empresarial, SLA de 99,9% de tiempo activo y la flexibilidad integrar con otros componentes en el ecosistema de Azure.

>[AZURE.NOTE] Servidor de R solo está disponible con HDInsight Premium. Actualmente, HDInsight Premium solo está disponible para clústeres Hadoop y motor. Actualmente por lo tanto, se puede utilizar R servidor únicamente con los clústeres de Hadoop y motor de HDInsight. Para obtener más información, vea [¿Cuáles son los diferentes niveles de servicio y los componentes de Hadoop disponibles con HDInsight?](hdinsight-component-versioning.md).

Servidor de R en HDInsight proporciona las últimas capacidades de análisis de R en grandes conjuntos de datos que se cargan al almacenamiento de blobs de Windows Azure. Puesto que R Server se basa en Abrir origen R, pueden aprovechar las aplicaciones de R que generar cualquiera de los paquetes de Abrir origen R 8000 +, así como las rutinas en utilizar otros equipos, paquete de análisis de datos grande de Microsoft que se incluye con el servidor de R.

El nodo del borde de clústeres Premium proporciona un lugar cómodo para conectarse al clúster y ejecutar las secuencias de comandos de R. Con un nodo del borde, tiene la opción de la ejecución de funciones distribuidas paralelizadas utilizar de otros equipos a través de las muestras del servidor de nodo de borde. También tiene la opción funcione en todos los nodos del clúster con Hadoop mapa reducir utilizar de otros equipos o motor calcular contextos.

Los modelos o predicciones que se puede descargar el resultado de análisis para usar local. Puede también se operationalized en otra aplicación en Azure, como a través de un [servicio web](../machine-learning/machine-learning-publish-a-machine-learning-web-service.md)de [Azure máquina aprendizaje Studio](http://studio.azureml.net) .

## <a name="get-started-with-r-on-hdinsight"></a>Introducción a R en HDInsight

Para incluir R Server en un clúster de HDInsight, debe crear un Hadoop o el motor clúster con la opción Premium cuando se crea un clúster a través del portal de Azure. Ambos tipos de clúster usan la misma configuración, que incluye R Server en los nodos de datos de un clúster y un nodo del borde como una zona de aterrizaje para análisis basadas en servidor R. Para un tutorial detallado sobre la creación de un clúster, vea [Introducción a R Server en HDInsight](hdinsight-hadoop-r-server-get-started.md) .

## <a name="learn-about-data-storage-options"></a>Obtenga más información sobre las opciones de almacenamiento de datos

Almacenamiento predeterminado para clústeres HDInsight es almacenamiento de blobs con el sistema de archivos HDFS asignado a un contenedor de blob. Esto garantiza que los datos se cargado en el almacenamiento de clúster o escritos en el almacenamiento de clúster durante el análisis, se hace persistente. Puede usar la utilidad [AzCopy](../storage/storage-use-azcopy.md) para copiar datos a y desde el blob.

Además de usar el almacenamiento de blobs, tiene la opción de uso de [Almacenamiento de lago de datos de Azure](https://azure.microsoft.com/services/data-lake-store/) con el clúster. Si usa lago de datos, puede usar el almacenamiento de blobs y lago de datos para el almacenamiento de HDFS.

También puede usar [Archivos de Azure](../storage/storage-how-to-use-files-linux.md) como una opción de almacenamiento para su uso en el nodo del borde. Archivos de Azure le permite montaje de un recurso compartido de archivos que se creó en el almacenamiento de Azure en el sistema de archivos de Linux. Para obtener más información acerca de las opciones de almacenamiento de datos para servidor de R en clúster HDInsight, consulte [Opciones de almacenamiento de servidor R en clústeres HDInsight](hdinsight-hadoop-r-server-storage.md).

## <a name="access-r-server-on-the-cluster"></a>Servidor de R de acceso en el clúster

Después de crear un clúster con servidor R, puede conectarse a la consola de R en el nodo del borde de clúster a través de SSH/PuTTY. También puede conectarse a través de un explorador si decide instalar el IDE de servidor RStudio opcional en el nodo del borde. Para obtener más información sobre la instalación RStudio Server, vea [Instalar RStudio Server en clústeres HDInsight](hdinsight-hadoop-r-server-install-r-studio.md).   

## <a name="develop-and-run-r-scripts"></a>Desarrollar y ejecutar secuencias de comandos de R

Las secuencias de comandos de R crear y ejecutar pueden utilizar cualquiera de los paquetes de Abrir origen R 8000 + además la paralelizada y distribuidas en relación en la biblioteca de utilizar otros equipos. En general, la secuencia de comandos que se ejecute en R Server en el nodo del borde se ejecuta en el intérprete R en ese nodo. La excepción es esos pasos que llaman a una utilizar otros equipos funcionarán en un contexto de cálculo que conjunto de reducción de mapa de Hadoop (RxHadoopMR) o motor (RxSpark).

En estos casos, la función ejecuta en modo distribuido a través de esos datos nodos (tareas) del clúster que están asociados a los datos de referencia. Para obtener más información acerca de las opciones de contexto de cálculo diferente, vea [calcular las opciones de contexto para servidor R HDInsight Premium](hdinsight-hadoop-r-server-compute-contexts.md).

## <a name="operationalize-a-model"></a>Controle un modelo

Una vez finalizado el modelado de datos, puede controle el modelo para realizar predicciones para los nuevos datos en Azure y local. Este proceso se conoce como puntuación. Estos son algunos ejemplos.

### <a name="score-in-hdinsight"></a>Puntuación de HDInsight

Para puntuar en HDInsight, escribir una función de R que llama el modelo para realizar predicciones para un nuevo archivo de datos que se ha cargado a su cuenta de almacenamiento. A continuación, guarde las predicciones volver a la cuenta de almacenamiento. Puede ejecutar la petición rutinaria en el nodo del borde del clúster o mediante un trabajo programado.  

### <a name="score-in-azure-machine-learning"></a>Puntuación de aprendizaje del equipo de Azure

Para puntuación mediante un servicio web de aprendizaje del equipo de Azure, utilice el paquete de Azure máquina aprendizaje R de Abrir origen conocido como [AzureML](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html) publicar su modelo como un servicio web de Azure. Para su comodidad, este paquete está preinstalado en el nodo del borde. A continuación, use las funciones de aprendizaje para crear una interfaz de usuario para el servicio web y, a continuación, llamar al servicio web según sea necesario para la puntuación.

Si elige esta opción, debe convertir los objetos de modelo de utilizar otros equipos a los objetos de modelo de código abierto equivalente para su uso con el servicio web. Esto puede hacerse mediante el uso de funciones de conversión de utilizar otros equipos, como por ejemplo `as.randomForest()` de modelos de conjunto.


### <a name="score-on-premises"></a>Puntuación local

Para puntuación local después de crear el modelo, serializar el modelo en R, descárguelo, anular su serialización y úsela para puntuación nuevos datos. La puntuación de nuevos datos mediante el procedimiento descrito anteriormente en [puntuación en HDInsight](#scoring-in-hdinsight) o mediante [DeployR](https://deployr.revolutionanalytics.com/).

## <a name="maintain-the-cluster"></a>Mantener el clúster

### <a name="install-and-maintain-r-packages"></a>Instalar y mantener paquetes R

La mayoría de los paquetes de R que usa deberán en el nodo del borde, puesto que la mayoría de las secuencias de comandos de R ejecutarán allí. Para instalar paquetes de R adicionales en el nodo del borde, puede usar la habitual `install.packages()` método en R.

En la mayoría de los casos, no es necesario instalar paquetes de R adicionales en los nodos de datos si solo utiliza rutinas de la biblioteca de utilizar otros equipos en el clúster. Sin embargo, es posible que tenga paquetes adicionales para admitir el uso de la ejecución de **RxDataStep** o **rxExec** en los nodos de datos.

En estos casos, se deben especificar los paquetes adicionales mediante el uso de una acción de secuencia de comandos después de crear el clúster. Para obtener más información, vea [crear un clúster de HDInsight con el servidor de R](hdinsight-hadoop-r-server-get-started.md).   

### <a name="change-hadoop-map-reduce-memory-settings"></a>Cambiar la configuración de memoria Hadoop reducir el mapa

Un clúster puede modificarse para cambiar la cantidad de memoria disponible en el servidor de R cuando se ejecuta un trabajo reducir el mapa. Para modificar un clúster, use la interfaz de usuario de Ambari Apache que está disponible mediante el módulo Azure portal para el clúster. Para obtener instrucciones sobre cómo obtener acceso a la Ambari UI en el clúster, consulte [clústeres de HDInsight administrar mediante la interfaz de usuario de Web Ambari](hdinsight-hadoop-manage-ambari.md).

También es posible cambiar la cantidad de memoria que está disponible en el servidor de R mediante modificadores Hadoop en la llamada a **RxHadoopMR** como sigue:

    hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### <a name="scale-your-cluster"></a>Ajustar el clúster

Un clúster existente se puede escalar hacia arriba o hacia abajo a través del portal. Al escalar, puede tener la capacidad adicional que necesita para las tareas de procesamiento de más grandes o puede escalar vuelva un clúster cuando está inactivo. Para obtener instrucciones sobre cómo ajustar el tamaño de un clúster, vea [Administrar HDInsight clústeres](hdinsight-administer-use-portal-linux.md).

### <a name="maintain-the-system"></a>Mantener el sistema

Mantenimiento se realiza en las máquinas virtuales de Linux subyacente en un clúster de HDInsight horario para aplicar revisiones del sistema operativo y otras actualizaciones. Normalmente, se hace mantenimiento 3:30 A.M. (según la hora local de la máquina virtual) cada el lunes y el jueves. Las actualizaciones se realizan de manera que no afecten a más de un cuarto de clúster en un momento.  

Puesto que los nodos cabezales son redundantes y se ven afectados no todos los nodos de datos, pueden ralentizar los trabajos que se están ejecutando durante este período. Debe ejecutar hasta su finalización, sin embargo. Cualquier software personalizado o datos locales que tiene se mantienen a través de estos eventos de mantenimiento a menos que se produce un error grave que requiere una regeneración de clúster.

## <a name="learn-about-ide-options-for-r-server-on-an-hdinsight-cluster"></a>Obtenga más información sobre opciones de IDE de R Server en un clúster de HDInsight

El nodo Linux del borde de un clúster de HDInsight Premium es la zona de inicio para el análisis de R. Después de conectar con el clúster, puede iniciar la interfaz de consola a servidor R escribiendo **R** en el símbolo del sistema de Linux. Uso de la interfaz de consola se ha mejorado si ejecuta un editor de texto para el desarrollo de secuencias de comandos de R en otra ventana y cortar y pegar las secciones de la secuencia de comandos en la consola de R según sea necesario.

Una herramienta más sofisticada para el desarrollo de la secuencia de comandos de R es el IDE basado en R para su uso en el escritorio, como Microsoft recientemente había anunciada [R Tools para Visual Studio](https://www.visualstudio.com/en-us/features/rtvs-vs.aspx) (RTV). Se trata de una familia de herramientas de servidor y de escritorio de [RStudio](https://www.rstudio.com/products/rstudio-server/). También puede usar basada en Eclipse de Walware [StatET](http://www.walware.de/goto/statet).

Otra opción es instalar un IDE en el propio nodo de borde de Linux.  Una opción popular es [RStudio Server](https://www.rstudio.com/products/rstudio-server/), que proporciona un IDE basada en explorador para su uso por los clientes remotos. Instalar RStudio Server en el nodo del borde de un clúster de HDInsight Premium ofrece una experiencia completa de IDE para el desarrollo y la ejecución de scripts de R con R servidor en el clúster. Puede ser mucho más productivo que la consola de R.  Si desea usar RStudio Server, vea [Instalar RStudio Server en clústeres HDInsight](hdinsight-hadoop-r-server-install-r-studio.md).

## <a name="learn-about-pricing"></a>Obtenga más información sobre precios

Las tarifas que están asociadas a un clúster de HDInsight Premium con servidor R se estructuran similar a las cuotas de los clústeres HDInsight estándares. Se basan en el tamaño de las VM subyacentes en el nombre, datos y nodos de borde, con la adición de un aumento de hora core Premium. Para obtener más información sobre HDInsight Premium precios, incluidos los precios durante la versión preliminar pública y la disponibilidad de una prueba gratuita de 30 días, consulte [HDInsight de precios](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>Pasos siguientes

Siga los siguientes vínculos para obtener más información sobre cómo usar R servidor con clústeres HDInsight.

- [Introducción a servidor R HDInsight](hdinsight-hadoop-r-server-get-started.md)

- [Agregar servidor de RStudio a HDInsight Premium](hdinsight-hadoop-r-server-install-r-studio.md)

- [Calcular las opciones de contexto de servidor R en HDInsight (vista previa)](hdinsight-hadoop-r-server-compute-contexts.md)

- [Azure opciones de almacenamiento para servidor R en HDInsight Premium](hdinsight-hadoop-r-server-storage.md)

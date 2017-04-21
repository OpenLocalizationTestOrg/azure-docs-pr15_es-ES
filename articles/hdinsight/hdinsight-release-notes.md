<properties
    pageTitle="Notas para los componentes Hadoop HDInsight de Azure | Microsoft Azure"
    description="Notas de la versión más recientes y las versiones de los componentes de Hadoop para HDInsight de Azure. Obtener sugerencias de desarrollo y detalles para Hadoop, tormenta Apache y HBase."
    services="hdinsight"
    documentationCenter=""
    editor="cgronlun"
    manager="jhubbard"
    authors="nitinme"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/27/2016"
    ms.author="nitinme"/>


# <a name="release-notes-for-hadoop-components-on-azure-hdinsight"></a>Notas de la versión de los componentes de Hadoop de HDInsight de Azure

## <a name="notes-for-10262016-release-of-r-server-on-hdinsight"></a>Notas de la versión de 26/10/2016 del servidor R HDInsight

- Se ha simplificado R servidor de aprovisionamiento de clúster de HDInsight.
- Servidor R HDInsight ahora está disponible como HDInsight normal "Servidor R" tipo de clúster y ya no se instala como una aplicación independiente de HDInsight. El nodo del borde y binarios R Server ahora aprovisionados como parte de la implementación de clúster de servidor R. Esto mejora la velocidad y confiabilidad de aprovisionamiento. Modelo de precios para R Server se actualiza en consecuencia.
- Precio de tipo de clúster de servidor R ahora se basa en el precio de nivel estándar más precio de servidor R suplemento. Nivel Premium ahora estará reservado para las características Premium disponibles a través de los tipos de clúster diferente y no se utilizará para el tipo de clúster de servidor de R. Este cambio no afecta a precios eficaz de servidor R, sólo cambia cómo se muestran los cargos en la factura. Todos los clústeres de servidor R existentes siguen funcionando y plantillas ARM continuarán funcionando hasta el aviso de amortización. **Se recomienda aunque actualizar las instalaciones de secuencias de comandos para usar la nueva plantilla ARM.**


## <a name="notes-for-08302016-release-of-r-server-on-hdinsight"></a>Notas para 30/08/2016 versión del servidor R HDInsight

Los números de versión completa para clústeres basados en Linux HDInsight implementados en esta versión:

|HDI |Versión de clúster HDI   |HDP |Generación HDP   |Generar Ambari |
|----|----------------------|----|------------|-------------|
|3.2 |3.2.1000.0.8268980    |2.2 |2.2.9.1-19  |2.2.1.12-4   |
|3.3 |3.3.1000.0.8268980    |2.3 |2.3.3.1-25  |2.2.1.12-4   |
|3.4 |3.4.1000.0.8269383    |2,4 |2.4.2.4-5   |2.2.1.12-4   |

Los números de versión completa para clústeres basados en Windows HDInsight implementados en esta versión:

|HDI |Versión de clúster HDI   |HDP |Generación HDP     |
|----|----------------------|----|--------------|
|2.1 |2.1.10.1033.2559206   |1.3 |1.3.12.0-01795|
|3.0 |3.0.6.1033.2559206    |2.0 |2.0.13.0-2117 |
|3.1 |3.1.4.1033.2559206    |2.1 |2.1.16.0-2374 |
|3.2 |3.2.7.1033.2559206    |2.2 |2.2.9.1-11    |
|3.3 |3.3.0.1033.2559206    |2.3 |2.3.3.1-25    |

## <a name="notes-for-08172016-release-of-r-server-on-hdinsight"></a>Notas para 17/08/2016 versión del servidor R HDInsight

- R servidor 8.0.5 – principalmente una versión de corregir el error. Vea las [Notas del producto R Server](https://msdn.microsoft.com/microsoft-r/notes/r-server-notes) para obtener más información. 
- Paquete de AzureML en el nodo del borde: [este paquete R](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html) permite modelos R poder publicar y consumida como un servicio web de Azure m.  Consulte la sección ["controle un modelo"](hdinsight-hadoop-r-server-overview.md#operationalize-a-model) de nuestro artículo de ["información general de R Server en HDInsight"](hdinsight-hadoop-r-server-overview.md) para obtener más información.
- Dependencias de Linux de los [principales paquetes de R más populares 100](https://github.com/metacran/cranlogs) – estos Linux dependencias de paquetes ahora están preinstaladas.  
- Opción para usar la repo CRAN al agregar R paquetes a los nodos de datos. Consulte la sección ["Paquetes de instalación R"](hdinsight-hadoop-r-server-get-started.md#install-r-packages) de nuestro artículo ["Empezar a usar Server R HDInsight"](hdinsight-hadoop-r-server-get-started.md) para obtener más información.
- Mejora la confiabilidad de aprovisionamiento de servidor R cuando se crean clústeres.


## <a name="notes-for-08012016-release-of-hdinsight"></a>Notas de versión de 01/08/2016 de HDInsight

Los números de versión completa para clústeres basados en Linux HDInsight implementados en esta versión:

|HDI |Versión de clúster HDI   |HDP |Generación HDP   |Generar Ambari |
|----|----------------------|----|------------|-------------|
|3.2 |3.2.1000.0.8028416    |2.2 |2.2.9.1-19  |2.2.1.12-4   |
|3.3 |3.3.1000.0.8028416    |2.3 |2.3.3.1-25  |2.2.1.12-4   |
|3.4 |3.4.1000.0.8053402    |2,4 |2.4.2.4-5   |2.2.1.12-4   |

Los números de versión completa para clústeres basados en Windows HDInsight implementados en esta versión:

|HDI |Versión de clúster HDI   |HDP |Generación HDP     |
|----|----------------------|----|--------------|
|2.1 |2.1.10.1005.2488842   |1.3 |1.3.12.0-01795|
|3.0 |3.0.6.1005.2488842    |2.0 |2.0.13.0-2117 |
|3.1 |3.1.4.1005.2488842    |2.1 |2.1.16.0-2374 |
|3.2 |3.2.7.1005.2488842    |2.2 |2.2.9.1-11    |
|3.3 |3.3.0.1005.2488842    |2.3 |2.3.3.1-25    |

Esta versión contiene las siguientes actualizaciones.

| Título                                           | Descripción                                          | Área afectada (por ejemplo, servicio, componente o SDK) | Tipo de clúster (por ejemplo motor, Hadoop, HBase o tormenta) | JIRA (si procede) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Cambios en clústeres 3.4 HDInsight | El valor predeterminado para las configuraciones de sección siguiente se cambian para mejorar el rendimiento <ul><li>`hive.vectorized.execution.reduce.enabled=true`</li><li>`hive.tez.min.partition.factor=1f`</li><li>`hive.tez.max.partition.factor=3f`</li><li>`tez.shuffle-vertex-manager.min-src-fraction=0.9`</li><li>`tez.shuffle-vertex-manager.max-src-fraction=0.95`</li><li>`tez.runtime.shuffle.connect.timeout= 30000`</li></ul>| Servicio    | Todos los| N/A.|
| Correcciones siguientes están incluidas en esta versión | SECCIÓN 13632, HIVE-12897,HIVE-12907,HIVE-12908,HIVE-12988,HIVE-13510,HIVE-13572,HIVE-13716,HIVE-13726,HIVE-12505,HIVE-13632,HIVE-13661,HIVE-13705,HIVE-13743,HIVE-13810,HIVE-13857,HIVE-13902,HIVE-13911,HIVE-13933| Servicio    | Todos los| N/A.

## <a name="notes-for-07142016-release-of-hdinsight"></a>Notas de la versión de 14/07/2016 de HDInsight

Los números de versión completa para clústeres basados en Linux HDInsight implementados en esta versión:

|HDI |Versión de clúster HDI   |HDP |Generación HDP   |Generar Ambari |
|----|----------------------|----|------------|-------------|
|3.2 |3.2.1000.0.7932505    |2.2 |2.2.9.1-11  |2.2.1.12-2   |
|3.3 |3.3.1000.0.7932505    |2.3 |2.3.3.1-18  |2.2.1.12-2   |
|3.4 |3.4.1000.0.7933003    |2,4 |2.4.2.0     |2.2.1.12-2   |

Los números de versión completa para clústeres basados en Windows HDInsight implementados en esta versión:

|HDI |Versión de clúster HDI   |HDP |Generación HDP     |
|----|----------------------|----|--------------|
|2.1 |2.1.10.989.2441725    |1.3 |1.3.12.0-01795|
|3.0 |3.0.6.989.2441725     |2.0 |2.0.13.0-2117 |
|3.1 |3.1.4.989.2441725     |2.1 |2.1.16.0-2374 |
|3.2 |3.2.7.989.2441725     |2.2 |2.2.9.1-11    |
|3.3 |3.3.0.989.2441725     |2.3 |2.3.3.1-21    |

## <a name="notes-for-07072016-release-of-hdinsight"></a>Notas de la versión de 07/07/2016 de HDInsight

Los números de versión completa para clústeres basados en Linux HDInsight implementados en esta versión:

|HDI |Versión de clúster HDI   |HDP |Generación HDP   |
|----|----------------------|----|------------|
|3.2 |3.2.1000.0.7864996    |2.2 |2.2.9.1-11  |
|3.3 |3.3.1000.0.7864996    |2.3 |2.3.3.1-18  |
|3.4 |3.4.1000.0.7861906    |2,4 |2.4.2.0     |

Los números de versión completa para clústeres basados en Windows HDInsight implementados en esta versión:

|HDI |Versión de clúster HDI   |HDP |Generación HDP     |
|----|----------------------|----|--------------|
|2.1 |2.1.10.977.2413853    |1.3 |1.3.12.0-01795|
|3.0 |3.0.6.977.2413853     |2.0 |2.0.13.0-2117 |
|3.1 |3.1.4.977.2413853     |2.1 |2.1.16.0-2374 |
|3.2 |3.2.7.977.2413853     |2.2 |2.2.9.1-11    |
|3.3 |3.3.0.977.2413853     |2.3 |2.3.3.1-21    |

Esta versión contiene las siguientes actualizaciones.

| Título                                           | Descripción                                          | Área afectada (por ejemplo, servicio, componente o SDK) | Tipo de clúster (por ejemplo motor, Hadoop, HBase o tormenta) | JIRA (si procede) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| [Herramientas de HDInsight para IntelliJ](hdinsight-apache-spark-intellij-tool-plugin.md) | Complemento IntelliJ IDEA para clústeres HDInsight Spark ahora se integra con el Kit de herramientas de Azure para IntelliJ. Admite Azure SDK v2.9.1, SDK de Java más reciente e incluye todas las características de la HDInsight Plugin para IntelliJ independiente.| Herramientas    | Motor| N/A.|
| [Herramientas de HDInsight para Eclipse](hdinsight-apache-spark-eclipse-tool-plugin.md) | Kit de herramientas de Azure para Eclipse ahora es compatible con clústeres HDInsight Spark. Permite las siguientes características. <ul><li>Crear y escribir una aplicación de motor fácilmente en Scala y Java con soporte técnico para IntelliSense, formato automático, comprobación de errores, etcetera de creación de primera clase.</li><li>Probar la aplicación de motor localmente.</li><li>Enviar trabajos a clúster HDInsight Spark y recuperar los resultados.</li><li>Inicie sesión en Azure y obtener acceso a todos los clústeres motor asociados con las suscripciones de Azure.</li><li>Vaya a todos los recursos asociados de almacenamiento del clúster HDInsight Spark.</li></ul>| Herramientas    | Motor| N/A.

Comenzando con esta versión, hemos cambiado la directiva de revisiones de sistema operativo invitado para clústeres HDInsight basados en Linux. El objetivo de la nueva directiva es reducir significativamente el número de reinicio debido a la aplicación de revisiones. La nueva directiva seguirá máquinas virtuales de revisión (VM) en Linux clústeres cada el lunes o el jueves empezando por 12 A.M. UTC de manera escalonada en todos los nodos en cualquier clúster determinado. Sin embargo, cualquier máquina virtual determinada solo se reinicia como máximo una vez cada 30 días debido a la aplicación de revisiones de sistema operativo de invitado. Además, el primer reinicio para un clúster recién creado no sucederá antes de 30 días desde la fecha de creación de clúster.

>[AZURE.NOTE] Estos cambios sólo se aplicarán a recién creados clústeres iguales o mayores que esta versión de lanzamiento.

## <a name="notes-for-06062016-release-of-hdinsight"></a>Notas de la versión de 06/06/2016 de HDInsight

Los números de la versión completa de clústeres HDInsight implementados en esta versión:

|HDP    |Versión HDI    |Versión de motor  |Número de compilación Ambari    |Número de compilación HDP|
|-------|---------------|---------------|-----------------------|----------------|
|2.3    |3.3.1000.0.7702215|    1.5.2|  2.2.1.8-2|  2.3.3.1-18|
|2,4    |3.4.1000.0.7702224|    1.6.1|  2.2.1.8-2|  2.4.2.0|


Esta versión contiene las siguientes actualizaciones.

| Título                                           | Descripción                                          | Área afectada (por ejemplo, servicio, componente o SDK) | Tipo de clúster (por ejemplo motor, Hadoop, HBase o tormenta) | JIRA (si procede) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Motor de HDInsight está disponible de forma general | Esta versión ofrece mejoras de disponibilidad, escalabilidad y productividad para abrir origen Apache motor en HDInsight. <ul><li>Disponibilidad SLA de 99,9%, lo que es adecuado para requieren cargas de trabajo empresariales de líderes de la industria.</li><li>Capa de almacenamiento Scalable mediante lago almacén de datos de Azure.</li><li>Herramientas de productividad para cada fase de desarrollo y la exploración de datos. Blocs de notas de Jupyter con kernel motor personalizado Habilitar exploración de datos interactivas, integración con paneles de BI como Power BI, una plantilla y Qlik es una buena idea para uso compartido de datos rápidos y reporting continuo, complemento de IntelliJ es opción confiable para el desarrollo de muestra de código a largo plazo y depurar.</li></ul>| Servicio    | Motor| N/A.|
| Herramientas de HDInsight para IntelliJ | Este es un complemento de la IDEA de IntelliJ para clústeres HDInsight Spark. Permite las siguientes características.<ul><li>Crear y escribir una aplicación de motor fácilmente en Scala y Java con soporte técnico para IntelliSense, formato automático, comprobación de errores, etcetera de creación de primera clase.</li><li>Probar la aplicación de motor localmente.</li><li>Enviar trabajos a clúster HDInsight Spark y recuperar los resultados.</li><li>Inicie sesión en Azure y obtener acceso a todos los clústeres motor asociados con las suscripciones de Azure.</li><li>Vaya a todos los recursos asociados de almacenamiento del clúster HDInsight Spark.</li><li>Vaya a toda la información de trabajo para el clúster HDInsight Spark y el historial de trabajos.</li><li>Depurar trabajos de motor remotamente desde el equipo de escritorio.</li></ul>| Herramientas    | Motor| N/A.

## <a name="notes-for-05132016-release-of-hdinsight"></a>Notas de versión de 13/05/2016 de HDInsight

Los números de la versión completa de clústeres HDInsight implementados en esta versión:

* HDInsight (Windows) 2.1.10.875.2159884 (HDP 1.3.12.0-01795 - sin cambios)
* HDInsight (Windows) 3.0.6.875.2159884 (HDP 2.0.13.0-2117 - sin cambios)
* HDInsight (Windows) 3.1.4.922.2266903 (HDP 2.1.15.0-2374 - sin cambios)
* HDInsight (Windows) 3.2.7.922.2266903 (HDP 2.2.9.1-11)
* HDInsight (Windows) 3.3.0.922.2266903 (HDP 2.3.3.1-18)
* HDInsight (Linux) 3.2.1000.0.7565644 (HDP 2.2.9.1-11)
* HDInsight (Linux) 3.3.1000.0.7565644 (HDP 2.3.3.1-18)
* HDInsight (Linux) 3.4.1000.0.7548380 (HDP 2.4.2.0)

Esta versión contiene las siguientes actualizaciones.

| Título                                           | Descripción                                          | Área afectada (por ejemplo, servicio, componente o SDK) | Tipo de clúster (por ejemplo motor, Hadoop, HBase o tormenta) | JIRA (si procede) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Generar la actualización de la versión y otras correcciones de errores | Esta versión actualiza la versión de motor de clúster HDInsight a 1.6.1 y corrige otros errores| Servicio    | Motor| N/A.

## <a name="notes-for-04112016-release-of-hdinsight"></a>Notas de versión de 04/11/2016 de HDInsight

Los números de la versión completa de clústeres HDInsight implementados en esta versión:

* HDInsight (Windows) 2.1.10.889.2191206 (HDP 1.3.12.0-01795 - sin cambios)
* HDInsight (Windows) 3.0.6.889.2191206 (HDP 2.0.13.0-2117 - sin cambios)
* HDInsight (Windows) 3.1.4.889.2191206 (HDP 2.1.15.0-2374 - sin cambios)
* HDInsight (Windows) 3.2.7.889.2191206 (HDP 2.2.9.1-10)
* HDInsight (Windows) 3.3.0.889.2191206 (HDP 2.3.3.1-16-sin cambios)
* HDInsight (Linux) 3.2.1000.0.7339916 (HDP 2.2.9.1-10)
* HDInsight (Linux) 3.3.1000.0.7339916 (HDP 2.3.3.1-16)
* HDInsight (Linux) 3.4.1000.0.7338911 (HDP 2.4.1.1-3)
* SDK 1.5.8

Esta versión contiene las siguientes actualizaciones.

| Título                                           | Descripción                                          | Área afectada (por ejemplo, servicio, componente o SDK) | Tipo (por ejemplo, Hadoop, HBase o tormenta) de clúster | JIRA (si procede) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Problemas de actualización de metastore personalizado HDI 3.4 | Creación de un clúster sería un error si ha usado un metastore personalizado, que se usó previamente en una versión anterior de otro clúster de HDInsight. Esto era debido a un error de secuencia de comandos de actualización que se ha resuelto| Creación de un clúster    | Todos los | N/A.
| Recuperación de Livio | Proporciona la resistencia de estado de trabajo para cualquier tarea que se envían a través de Livio | Confiabilidad | Motor de Linux| N/A.
| Contenido de Jupyter HA | Proporciona la capacidad de guardar y cargar el contenido del Bloc de notas de Jupyter a y desde la cuenta de almacenamiento asociado con el clúster. Para obtener más información, vea [Kernels disponibles para los blocs de notas de Jupyter](hdinsight-apache-spark-jupyter-notebook-kernels.md).| Blocs de notas | Motor de Linux| N/A.
| Eliminación de hiveContext en blocs de notas Jupter | Usar `%%sql` mágico en lugar de `%%hive` mágico. SqlContext es equivalente a hiveContext. Para obtener más información, vea [Kernels disponibles para los blocs de notas Jupyter](hdinsight-apache-spark-jupyter-notebook-kernels.md)| Blocs de notas    | Motor de clústeres en Linux| N/A.
| Tipo de proyectos de versiones anteriores de motor | Versión más antigua motor 1.3.1 se quitará el servicio en 31/5 | Servicio | Motor de clústeres en Windows | N/A.

## <a name="notes-for-03292016-release-of-hdinsight"></a>Notas de la versión de 29/03/2016 de HDInsight

Los números de la versión completa de clústeres HDInsight implementados en esta versión:

* HDInsight (Windows) 2.1.10.875.2159884 (HDP 1.3.12.0-01795 - sin cambios)
* HDInsight (Windows) 3.0.6.875.2159884 (HDP 2.0.13.0-2117 - sin cambios)
* HDInsight (Windows) 3.1.4.875.2159884 (HDP 2.1.15.0-2374 - sin cambios)
* HDInsight (Windows) 3.2.7.875.2159884 (HDP 2.2.9.1-7 - sin cambios)
* HDInsight (Windows) 3.3.0.875.2159884 (HDP 2.3.3.1-16)
* HDInsight (Linux) 3.2.1000.0.7193255 (HDP 2.2.9.1-8 - sin cambios)
* HDInsight (Linux) 3.3.1000.0.7193255 (HDP 2.3.3.1-7 - sin cambios)
* HDInsight (Linux) 3.4.1000.0.7195842 (HDP 2.4.1.0-327)
* SDK 1.5.8

Esta versión contiene las siguientes actualizaciones.

| Título                                           | Descripción                                          | Área afectada (por ejemplo, servicio, componente o SDK) | Tipo (por ejemplo, Hadoop, HBase o tormenta) de clúster | JIRA (si procede) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Agregado HDInsight 3.4 y versiones HDP actualizadas para todos los clústeres HDInsight | Con esta versión, se ha agregado v3.4 HDInsight (según 2,4 HDP) y se han actualizado también otras versiones HDP. Notas de la versión de HDP 2,4 están disponible [aquí](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.4.0/bk_HDP_RelNotes/content/ch_relnotes_v240.html) y obtener más información sobre HDInsight versiones pueden ser encontrado [aquí](hdinsight-component-versioning.md).| Servicio    | Todos los clústeres de Linux| N/A.
| HDInsight Premium | HDInsight ahora está disponible en dos categorías: estándar y Premium. HDInsight Premium está actualmente en la vista previa y está disponible sólo para Hadoop y motor clústeres en Linux. Para obtener más información, vea [aquí](hdinsight-component-versioning.md#hdinsight-standard-and-hdinsight-premium).| Servicio    | Hadoop y motor en Linux| N/A.
| Servidor de Microsoft R | HDInsight Premium proporciona Microsoft R Server que se puede incluir con los clústeres Hadoop y motor de Linux. Para obtener más información, vea [Información general de R Server en HDInsight](hdinsight-hadoop-r-server-overview.md).| Servicio    | Hadoop y motor en Linux| N/A.
| Motor 1.6.0 | 3.4 HDInsight clústeres ahora incluyen motor 1.6.0| Servicio    | Motor de clústeres en Linux| N/A.
| Mejoras en el Bloc de notas Jupyter | Blocs de notas de Jupyter disponibles con clústeres motor ahora proporcionan motor adicional kernels. También incluyen mejoras como uso de %% mágico, visualización automática y la integración con las bibliotecas de visualización de Python (como matplotlib). Para obtener más información, vea [Kernels disponibles para los blocs de notas de Jupyter](hdinsight-apache-spark-jupyter-notebook-kernels.md). | Servicio | Motor de clústeres en Linux | N/A.

## <a name="notes-for-03222016-release-of-hdinsight"></a>Notas de la versión del 22/03/2016 de HDInsight

Los números de la versión completa de clústeres HDInsight implementados en esta versión:

* HDInsight (Windows) 2.1.10.875.2159884 (HDP 1.3.12.0-01795 - sin cambios)
* HDInsight (Windows) 3.0.6.875.2159884 (HDP 2.0.13.0-2117 - sin cambios)
* HDInsight (Windows) 3.1.4.875.2159884 (HDP 2.1.15.0-2374 - sin cambios)
* HDInsight (Windows) 3.2.7.875.2159884 (HDP 2.2.9.1-7 - sin cambios)
* HDInsight (Windows) 3.3.0.875.2159884 (HDP 2.3.3.1-16)
* HDInsight (Linux) 3.2.1000.0.7193255 (HDP 2.2.9.1-8 - sin cambios)
* HDInsight (Linux) 3.3.1000.0.7193255 (HDP 2.3.3.1-7 - sin cambios)
* SDK 1.5.8

Esta versión contiene las siguientes actualizaciones.

| Título                                           | Descripción                                          | Área afectada (por ejemplo, servicio, componente o SDK) | Tipo (por ejemplo, Hadoop, HBase o tormenta) de clúster | JIRA (si procede) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Versiones de HDInsight actualizadas para todos los clústeres HDInsight | Con esta versión, hemos actualizado versiones HDInsight para todos los clústeres HDInsight| Servicio    | Todos los| N/A.


## <a name="notes-for-03102016-release-of-hdinsight"></a>Notas de la versión de 10/03/2016 de HDInsight

Los números de la versión completa de clústeres HDInsight implementados en esta versión:

* HDInsight (Windows) 2.1.10.859.2123216 (HDP 1.3.12.0-01795 - sin cambios)
* HDInsight (Windows) 3.0.6.859.2123216 (HDP 2.0.13.0-2117 - sin cambios)
* HDInsight (Windows) 3.1.4.859.2123216 (HDP 2.1.15.0-2374 - sin cambios)
* HDInsight (Windows) 3.2.7.859.2123216 (HDP 2.2.9.1-7)
* HDInsight (Windows) 3.3.0.859.2123216 (HDP 2.3.3.1-5 - sin cambios)
* HDInsight (Linux) 3.2.1000.7076817 (HDP 2.2.9.1-8)
* HDInsight (Linux) 3.3.1000.7076817 (HDP 2.3.3.1-7)
* SDK 1.5.8

Esta versión contiene las siguientes actualizaciones.

| Título                                           | Descripción                                          | Área afectada (por ejemplo, servicio, componente o SDK) | Tipo (por ejemplo, Hadoop, HBase o tormenta) de clúster | JIRA (si procede) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Versiones de HDInsight actualizadas para todos los clústeres HDInsight | Con esta versión, hemos actualizado versiones HDInsight para todos los clústeres HDInsight| Servicio    | Todos los| N/A.

## <a name="notes-for-01272016-release-of-hdinsight"></a>Notas de la versión de 27/01/2016 de HDInsight

Los números de la versión completa de clústeres HDInsight implementados en esta versión:

* HDInsight (Windows) 2.1.10.817.2028315 (HDP 1.3.12.0-01795 - sin cambios)
* HDInsight (Windows) 3.0.6.817.2028315 (HDP 2.0.13.0-2117 - sin cambios)
* HDInsight (Windows) 3.1.4.817.2028315 (HDP 2.1.15.0-2374 - sin cambios)
* HDInsight (Windows) 3.2.7.817.2028315 (HDP 2.2.9.1-1)
* HDInsight (Windows) 3.3.0.817.2028315 (HDP 2.3.3.1-5 - sin cambios)
* HDInsight (Linux) 3.2.1000.4072335 (HDP 2.2.9.1-1)
* HDInsight (Linux) 3.3.1000.4072335 (HDP 2.3.3.1-1)
* SDK 1.5.8

Esta versión contiene las siguientes actualizaciones.

| Título                                           | Descripción                                          | Área afectada (por ejemplo, servicio, componente o SDK) | Tipo (por ejemplo, Hadoop, HBase o tormenta) de clúster | JIRA (si procede) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Versiones de HDInsight actualizadas para todos los clústeres HDInsight | Con esta versión, hemos actualizado versiones HDInsight para todos los clústeres HDInsight| Servicio    | Todos los| N/A.

## <a name="notes-for-12022015-release-of-hdinsight"></a>Notas de la versión de 12/02/2015 de HDInsight

Los números de la versión completa de clústeres HDInsight implementados en esta versión:

* HDInsight (Windows) 2.1.10.763.1931434 (HDP 1.3.12.0-01795 - sin cambios)
* HDInsight (Windows) 3.0.6.763.1931434 (HDP 2.0.13.0-2117 - sin cambios)
* HDInsight (Windows) 3.1.4.763.1931434 (HDP 2.1.15.0-2374 - sin cambios)
* HDInsight (Windows) 3.2.7.763.1931434 (HDP 2.2.7.1-34 - sin cambios)
* HDInsight (Windows) 3.3.1000.0 (HDP 2.3.3.1-5)
* HDInsight (Linux) 3.2.1000.0.6392801 (HDP 2.2.7.1-34 - sin cambios)
* HDInsight (Linux) 3.3.1000.0 (HDP 2.3.3.0-3039)
* SDK 1.5.8

Esta versión contiene las siguientes actualizaciones.

| Título                                           | Descripción                                          | Área afectada (por ejemplo, servicio, componente o SDK) | Tipo (por ejemplo, Hadoop, HBase o tormenta) de clúster | JIRA (si procede) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Agregado HDInsight 3.3 y versiones HDP actualizadas para todos los clústeres HDInsight | Con esta versión, se ha agregado v3.3 HDInsight (según HDP 2.3) y se han actualizado también otras versiones HDP. Notas de la versión 2.3 HDP están disponible [aquí](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html) y obtener más información sobre HDInsight versiones pueden ser encontrado [aquí](hdinsight-component-versioning.md).| Servicio    | Todos los| N/A.

## <a name="notes-for-11302015-release-of-hdinsight"></a>Notas de la versión de 30/11/2015 de HDInsight

Los números de la versión completa de clústeres HDInsight implementados en esta versión:

* HDInsight (Windows) 2.1.10.757.1923908 (HDP 1.3.12.0-01795 - sin cambios)
* HDInsight (Windows) 3.0.6.757.1923908 (HDP 2.0.13.0-2117 - sin cambios)
* HDInsight (Windows) 3.1.4.757.1923908 (HDP 2.1.15.0-2374 - sin cambios)
* HDInsight (Windows) 3.2.7.757.1923908 (HDP 2.2.7.1-34)
* HDInsight (Linux) 3.2.1000.0.6392801 (HDP 2.2.7.1-34)
* SDK 1.5.8

Esta versión contiene las siguientes actualizaciones.

| Título                                           | Descripción                                          | Área afectada (por ejemplo, servicio, componente o SDK) | Tipo (por ejemplo, Hadoop, HBase o tormenta) de clúster | JIRA (si procede) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Versiones de HDInsight actualizadas para todas las versiones HDP para clústeres de HDInsight 3.2 (Windows y Linux) y clústeres HDInsight | Con esta versión, se han actualizado versiones HDInsight y HDP | Servicio    | Todos los| N/A.


## <a name="notes-for-10272015-release-of-hdinsight"></a>Notas de versión de 27/10/2015 de HDInsight

Los números de la versión completa de clústeres HDInsight implementados en esta versión:

* HDInsight (Windows) 2.1.10.726.1866228 (HDP 1.3.12.0-01795 - sin cambios)
* HDInsight (Windows) 3.0.6.726.1866228 (HDP 2.0.13.0-2117 - sin cambios)
* HDInsight (Windows) 3.1.4.726.1866228 (HDP 2.1.15.0-2374 - sin cambios)
* HDInsight (Windows) 3.2.7.726.1866228 (HDP 2.2.7.1-33)
* HDInsight (Linux) 3.2.1000.0.6035701 (HDP 2.2.7.1-33)
* SDK 1.5.8

Esta versión contiene las siguientes actualizaciones.

| Título                                           | Descripción                                          | Área afectada (por ejemplo, servicio, componente o SDK) | Tipo (por ejemplo, Hadoop, HBase o tormenta) de clúster | JIRA (si procede) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Versiones de HDInsight actualizadas para todos los clústeres HDInsight (Windows y Linux) | Con esta versión, se han actualizado versiones HDInsight y HDP | Servicio    | Todos los| N/A.
| Fijos clústeres Jupyter para Windows motor con clústeres letras mayúsculas | Clústeres que tenían nombres DNS especificados en letras mayúsculas tenían problemas con blocs de notas de Jupyter debido a una comprobación de solicitud de origen. La solución consiste en cambiar el nombre DNS para la configuración de Jupyter a minúsculas. | Servicio    | Motor de HDInsight (Windows)| N/A.


## <a name="notes-for-10202015-release-of-hdinsight"></a>Notas de la versión de 20/10/2015 de HDInsight

Los números de la versión completa de clústeres HDInsight implementados en esta versión:

* HDInsight 2.1.10.716.1846990 (Windows) (1.3.12.0-01795 HDP - sin cambios)
* HDInsight 3.0.6.716.1846990 (Windows) (2.0.13.0-2117 HDP - sin cambios)
* HDInsight 3.1.4.716.1846990 (Windows) (2.1.16.0-2374 HDP)
* HDInsight 3.2.7.716.1846990 (Windows) (2.2.7.1-0004 HDP)
* HDInsight 3.2.1000.0.5930166 (Linux) (HDP 2.2.7.1-0004)
* SDK 1.5.8

Esta versión contiene las siguientes actualizaciones.

| Título                                           | Descripción                                          | Área afectada (por ejemplo, servicio, componente o SDK) | Tipo (por ejemplo, Hadoop, HBase o tormenta) de clúster | JIRA (si procede) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Versión HDP predeterminado cambiado a HDP 2.2 | La versión predeterminada para clústeres HDInsight Windows se cambia a HDP 2.2. HDInsight versión 3.2 (2.2 HDP) está disponible de forma general en desde febrero de 2015. Este cambio solo voltea la versión de clúster de forma predeterminada, cuando no se realice una selección explícita mientras el clúster con el portal de Azure, cmdlets de PowerShell o el SDK de aprovisionamiento. | Servicio    | Todos los| N/A.                  |
|Cambios en el formato de nombre de la máquina virtual para implementar varios HDInsight en Linux clústeres en una única red Virtual | Se agrega compatibilidad para implementar varios clústeres HDInsight Linux en una única red virtual en esta versión. Como parte de esto, ha cambiado el formato de los nombres de máquina virtual en el clúster de headnode\*, workernode\* y zookeepernode\* a hn\*, wn\*y zk\* respectivamente. No es una práctica recomendada para tomar una dependencia directa en el formato de nombres de máquina virtual, ya que esto está sujeto a cambios. Utilice "hostname -f" en el equipo local o APIs Ambari para determinar la lista de hosts y la asignación de componentes de hosts. Puede encontrar más información en [https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/hosts.md](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/hosts.md) y [https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/host-components.md](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/host-components.md). | Servicio | HDInsight clústeres en Linux | N/A. |
| Cambios de configuración | 3.1 HDInsight clústeres, ahora se habilitan las configuraciones siguientes: <ul><li>tez.yarn.ATS.Enabled y yarn.log.server.url. Esto permite que el servidor de aplicaciones de la escala de tiempo y el servidor de registro poder servir los registros.</li></ul>HDInsight 3.2 clústeres, se han modificado las configuraciones siguientes: <ul><li>se ha establecido MapReduce.fileoutputcommitter.Algorithm.Version a 2. Esto permite el uso de la versión de V2 de la FileOutputCommitter.</li></ul> | Servicio | Todos los | N/A. |


## <a name="notes-for-09092015-release-of-hdinsight"></a>Notas de versión de 09/09/2015 de HDInsight

Los números de la versión completa de clústeres HDInsight implementados en esta versión:

* HDInsight 2.1.10.675.1768697 (HDP 1.3.12.0-01795 - sin cambios)
* HDInsight 3.0.6.675.1768697 (HDP 2.0.13.0-2117 - sin cambios)
* HDInsight 3.1.4.675.1768697 (HDP 2.1.15.0-2334 - sin cambios)
* HDInsight 3.2.6.675.1768697 (HDP 2.2.6.1-0012 - sin cambios)
* SDK 1.5.8

Esta versión contiene las siguientes actualizaciones.

| Título                                           | Descripción                                          | Área afectada (por ejemplo, servicio, componente o SDK) | Tipo (por ejemplo, Hadoop, HBase o tormenta) de clúster | JIRA (si procede) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Versiones de HDInsight actualizadas para todos los clústeres HDInsight | Con esta versión, se han actualizado HDInsight versiones | Servicio    | Todos los| N/A.                  |

## <a name="notes-for-07312015-release-of-hdinsight"></a>Notas de versión de 31/07/2015 de HDInsight

Los números de la versión completa de clústeres HDInsight implementados en esta versión:

* HDInsight 2.1.10.640.1695824 (HDP 1.3.12.0-01795 - sin cambios)
* HDInsight 3.0.6.640.1695824 (HDP 2.0.13.0-2117 - sin cambios)
* HDInsight 3.1.4.640.1695824 (HDP 2.1.15.0-2334 - sin cambios)
* HDInsight 3.2.6.640.1695824 (HDP 2.2.6.1-0012 - sin cambios)
* SDK 1.5.8

Esta versión contiene las siguientes actualizaciones.

| Título                                           | Descripción                                          | Área afectada (por ejemplo, servicio, componente o SDK) | Tipo (por ejemplo, Hadoop, HBase o tormenta) de clúster | JIRA (si procede) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Corregir motor de flujo de trabajo de clúster nodo volver a imágenes | Corrige un error que estaba causando motor nodos de clúster no recuperar después de crear una nueva imagen | Servicio    | Motor| N/A.                  |


## <a name="notes-for-07312015-release-of-hdinsight"></a>Notas de versión de 31/07/2015 de HDInsight

Los números de la versión completa de clústeres HDInsight implementados en esta versión:

* HDInsight 2.1.10.635.1684502 (HDP 1.3.12.0-01795 - sin cambios)
* HDInsight 3.0.6.635.1684502 (HDP 2.0.13.0-2117 - sin cambios)
* HDInsight 3.1.4.635.1684502 (HDP 2.1.15.0-2334 - sin cambios)
* HDInsight 3.2.6.635.1684502 (HDP 2.2.6.1-0012 - sin cambios)
* SDK 1.5.8

Esta versión contiene las siguientes actualizaciones.

| Título                                           | Descripción                                          | Área afectada (por ejemplo, servicio, componente o SDK) | Tipo (por ejemplo, Hadoop, HBase o tormenta) de clúster | JIRA (si procede) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Versiones de HDInsight actualizadas para todos los clústeres HDInsight | Con esta versión, se han actualizado HDInsight versiones | Servicio    | Todos los| N/A.                  |


## <a name="notes-for-07072015-release-of-hdinsight"></a>Notas de versión de 07/07/2015 de HDInsight

Los números de la versión completa de clústeres HDInsight implementados en esta versión:

* HDInsight 2.1.10.610.1630216 (HDP 1.3.12.0-01795 - sin cambios)
* HDInsight 3.0.6.610.1630216 (HDP 2.0.13.0-2117 - sin cambios)
* HDInsight 3.1.4.610.1630216 (HDP 2.1.15.0-2334 - sin cambios)
* HDInsight 3.2.4.610.1630216 (HDP 2.2.6.1-0012)
* SDK 1.5.8


Esta versión contiene las siguientes actualizaciones.

| Título                                           | Descripción                                          | Área afectada (por ejemplo, servicio, componente o SDK) | Tipo (por ejemplo, Hadoop, HBase o tormenta) de clúster | JIRA (si procede) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Versiones HDP actualizadas para clústeres 3,2 HDInsight | Con esta versión, 3,2 HDInsight implementa HDP 2.2.6.1-0012 | Servicio    | Todos los                                                 | N/A.                  |


## <a name="notes-for-06262015-release-of-hdinsight"></a>Notas de la versión de 26/06/2015 de HDInsight

Los números de la versión completa de clústeres HDInsight implementados en esta versión:

* HDInsight 2.1.10.601.1610731 (HDP 1.3.12.0-01795 - sin cambios)
* HDInsight 3.0.6.601.1610731 (HDP 2.0.13.0-2117 - sin cambios)
* HDInsight 3.1.4.601.1610731 (HDP 2.1.15.0-2334 - sin cambios)
* HDInsight 3.2.4.601.1610731 (HDP 2.2.6.1-0011)
* SDK 1.5.8


Esta versión contiene las siguientes actualizaciones.

<table border="1">
<tr>
<th>Título</th>
<th>Descripción</th>
<th>Área afectada (por ejemplo, servicio, componente o SDK)</p></th>
<th>Tipo (por ejemplo, Hadoop, HBase o tormenta) de clúster</th>
<th>JIRA (si procede)</th>
</tr>


<tr>
<td>Versiones HDP actualizadas para clústeres 3,2 HDInsight</td>
<td>Con esta versión, 3,2 HDInsight implementa HDP 2.2.6.1</td>
<td>Servicio</td>
<td>Todos los</td>
<td>N/A.</td>
</tr>

</table>

## <a name="notes-for-06182015-release-of-hdinsight"></a>Notas de la versión de 18/06/2015 de HDInsight

Los números de la versión completa de clústeres HDInsight implementados en esta versión:

* HDInsight 2.1.10.596.1601657 (HDP 1.3.12.0-01795 - sin cambios)
* HDInsight 3.0.6.596.1601657 (HDP 2.0.13.0-2117 - sin cambios)
* HDInsight 3.1.4.596.1601657 (HDP 2.1.15.0-2334)
* HDInsight 3.2.4.596.1601657 (HDP 2.2.6.1-0002)
* SDK 1.5.8


Esta versión contiene las siguientes actualizaciones.

<table border="1">
<tr>
<th>Título</th>
<th>Descripción</th>
<th>Área afectada (por ejemplo, servicio, componente o SDK)</p></th>
<th>Tipo (por ejemplo, Hadoop, HBase o tormenta) de clúster</th>
<th>JIRA (si procede)</th>
</tr>


<tr>
<td>Puertos HTTPS adicionales abiertos</td>
<td>El servicio de nube ahora abre 5 puertos 8001 a 8005 en el clúster E.g. en https://<clustername>.azurehdinsight.net:8001/. Las solicitudes de estas direcciones URL se autentican mediante el mismo mecanismo de contraseña de autenticación básica como puerto 443. Estos puertos enlazan al mismo puerto en el headnode activa. Acciones de script pueden usarse para que los servicios de atención al cliente escuchar en estos puertos en la ruta a fuera del clúster y headnode.</td>
<td>Servicio de nube</td>
<td>Todos los</td>
<td>N/A.</td>
</tr>

<tr>
<td>Problema de orden aleatorio MapReduce intermitente para HDInsight 3.2</td>
<td>Corregir una condición de carrera poco comunes, intermitente en orden aleatorio reducir de mapa en grandes clústeres ocasionales errores de tareas de. Para obtener más información, vea <a href="https://issues.apache.org/jira/browse/MAPREDUCE-6361" target="_blank">MAPREDUCE 6361</a> .</td>
<td>Hadoop principales</td>
<td>Todos los</td>
<td><a href="https://issues.apache.org/jira/browse/MAPREDUCE-6361" target="_blank">MAPREDUCE 6361</a></td>
</tr>

<tr>
<td>Mover a la última Java Azure SDK 2.2 para HDInsight 3.2</td>
<td>Se mueve a la última versión del SDK de Azure para Java utilizada el controlador WASB. El último SDK tiene unos correcciones y las notas de la misma versión están disponibles en https://github.com/Azure/azure-storage-java/blob/master/ChangeLog.txt.</td>
<td>Hadoop principales</td>
<td>Todos los</td>
<td><a href="https://issues.apache.org/jira/browse/HADOOP-11959" target="_blank">HADOOP 11959</a></td>
</tr>

<tr>
<td>Mover a HDP 2.1.15 para clústeres 3.1 HDInsight</td>
<td>Notas de la versión de Hortonworks la versión están disponibles <a href="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.15-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.15.html" target="_blank">aquí</a>.</td>
<td>HDP</td>
<td>Todos los</td>
<td>N/A.</td>
</tr>

</table>

## <a name="notes-for-06042015-release-of-hdinsight"></a>Notas de la versión 04/06/2015 de HDInsight

Los números de la versión completa de clústeres HDInsight implementados en esta versión:

* HDInsight 2.1.10.583.1575584 (HDP 1.3.12.0-01795 - sin cambios)
* HDInsight 3.0.6.583.1575584 (HDP 2.0.13.0-2117 - sin cambios)
* HDInsight 3.1.3.583.1575584 (HDP 2.1.12.1-0003 - sin cambios)
* HDInsight 3.2.4.583.1575584 (HDP 2.2.6.1-1)
* SDK 1.5.8


Esta versión contiene las siguientes actualizaciones.

<table border="1">
<tr>
<th>Título</th>
<th>Descripción</th>
<th>Área afectada (por ejemplo, servicio, componente o SDK)</p></th>
<th>Tipo (por ejemplo, Hadoop, HBase o tormenta) de clúster</th>
<th>JIRA (si procede)</th>
</tr>


<tr>
<td>Solucionar error de 502 puerta de enlace incorrecta para clústeres de tormenta</td>
<td>Esta versión corrige un error que afectan a la API que causaron el sitio Web sea abajo después de reiniciar el envío de trabajos.</td>
<td>Servicio</td>
<td>Tormenta</td>
<td>N/A.</td>
</tr>

</table>

## <a name="notes-for-06012015-release-of-hdinsight"></a>Notas de la versión 06/01/2015 de HDInsight

Los números de la versión completa de clústeres HDInsight implementados en esta versión:

* HDInsight 2.1.10.577.1563827 (HDP 1.3.12.0-01795 - sin cambios)
* HDInsight 3.0.6.577.1563827 (HDP 2.0.13.0-2117 - sin cambios)
* HDInsight 3.1.3.577.1563827 (HDP 2.1.12.1-0003 - sin cambios))
* HDInsight 3.2.4.577.1563827 (HDP 2.2.6.0-2800 - sin cambios)
* SDK 1.5.8


Esta versión contiene las siguientes actualizaciones.

<table border="1">
<tr>
<th>Título</th>
<th>Descripción</th>
<th>Área afectada (por ejemplo, servicio, componente o SDK)</p></th>
<th>Tipo (por ejemplo, Hadoop, HBase o tormenta) de clúster</th>
<th>JIRA (si procede)</th>
</tr>


<tr>
<td>Varias correcciones de errores</td>
<td>Esta versión corrige errores relacionados con el aprovisionamiento de clúster.</td>
<td>Servicio</td>
<td>Todos los tipos de clúster</td>
<td>N/A.</td>
</tr>

</table>

## <a name="notes-for-05272015-release-of-hdinsight"></a>Notas de versión de 27/05/2015 de HDInsight

Los números de la versión completa de clústeres HDInsight implementados en esta versión:

* HDInsight 3.2.4.570.1554102 (HDP 2.2.6.0-2800)
* Otras versiones de clúster y el SDK no se implementan como parte de esta versión.


Esta versión contiene las siguientes actualizaciones.

<table border="1">
<tr>
<th>Título</th>
<th>Descripción</th>
<th>Área afectada (por ejemplo, servicio, componente o SDK)</p></th>
<th>Tipo (por ejemplo, Hadoop, HBase o tormenta) de clúster</th>
<th>JIRA (si procede)</th>
</tr>


<tr>
<td>Actualización de HDP 2.2</td>
<td>Esta versión de 3,2 HDInsight contiene HDP 2.2.6 y aporta varias correcciones de errores importantes a HDInsight. Notas de la versión completa está disponible en <a href="http://dev.hortonworks.com.s3.amazonaws.com/HDPDocuments/HDP2/HDP-2.2.6/HDP_RelNotes_v226/index.html">HDP 2.2.6 notas de la versión</a>.</td>
<td>HDP</td>
<td>Todos los tipos de clúster</td>
<td>N/A.</td>
</tr>

<tr>
<td>Cambiar a la configuración de memoria predeterminada hilo contenedor</td>
<td>En la actualización, el predeterminado memoria disponible para los contenedores hilo (yarn.nodemanager.resource.memory- y yarn.scheduler.maximum-asignación-mb), iniciado por el Administrador de nodo aumenta 5632 MB. Previamente se ha reducido a MB 4608 pero en función de varias series de trabajo, el nuevo valor debe ofrecer mejor confiabilidad y el rendimiento de la mayoría de los trabajos, por lo tanto, es mejor predeterminado. Como habitual, si es un tiene dependencia crítica en la configuración de la memoria, establezca explícitamente al crear el clúster.</td>
<td>HDP</td>
<td>Todos los tipos de clúster</td>
<td>N/A.</td>
</tr>

<tr>
<td>Paridad de configuración predeterminada para clústeres HBase y tormenta</td>
<td>Esta actualización restaura Hbase y tormenta clústeres para usar los mismos valores de configuraciones de hilo como clústeres Hadoop. Esto se hace por paridad entre todos los tipos de clúster.</td>
<td>HDP</td>
<td>HBase, tormenta</td>
<td>N/A.</td>
</tr>

</table>

## <a name="notes-for-05202015-release-of-hdinsight"></a>Notas de la versión de 20/05/2015 de HDInsight

Los números de la versión completa de clústeres HDInsight implementados en esta versión:

* HDInsight 2.1.10.564.1542093 (HDP 1.3.12.0-01795 - sin cambios)
* HDInsight 3.0.6.564.1542093 (HDP 2.0.13.0-2117 - sin cambios)
* HDInsight 3.1.3.564.1542093 (HDP 2.1.12.1-0003)
* HDInsight 3.2.4.564.1542093 (HDP 2.2.4.6-2)
* SDK 1.5.8

Esta versión contiene las siguientes actualizaciones.

<table border="1">
<tr>
<th>Título</th>
<th>Descripción</th>
<th>Área afectada (por ejemplo, servicio, componente o SDK)</p></th>
<th>Tipo (por ejemplo, Hadoop, HBase o tormenta) de clúster</th>
<th>JIRA (si procede)</th>
</tr>


<tr>
<td>Soporte técnico de EventHub SCP.NET</td>
<td>Los paquetes de clúster actualizada de tormenta HDInsight Traer nuevas características al SCP.NET. Ahora, tendrá acceso a las API de nuevos en el generador de topología que hacen que sea más fácil de usar EventHubSpout o Java Spouts. Debe actualizar al cliente SCP.NET SDK funcione con nuevos clústeres como los contratos que se han actualizado. Para obtener más información sobre la nuevas API, uso y notas de versiones (incluyendo correcciones de errores), consulte el archivo Léame que se incluye en el paquete de nuget SCP.NET.</td>
<td>Herramientas de VS</td>
<td>Clústeres de 3,2 HDInsight de tormenta</td>
<td>N/A.</td>
</tr>

<tr>
<td>Actualización del controlador JDBC</td>
<td>Actualizar el controlador a la versión de SQL Server compatibles en sqljdbc_4.1.5605.100.</td>
<td>Metastore</td>
<td>Todos los</td>
<td>N/A.</td>
</tr>
</table>

## <a name="notes-for-04272015-release-of-hdinsight"></a>Notas de la versión 04/27/2015 de HDInsight

Los números de la versión completa de clústeres HDInsight implementados en esta versión:

* HDInsight 2.1.10.537.1486660 (HDP 1.3.12.0-01795 - sin cambios)
* HDInsight 3.0.6.537.1486660 (HDP 2.0.13.0-2117 - sin cambios)
* HDInsight 3.1.3.537.1486660 (HDP 2.1.12.0-2329 - sin cambios)
* HDInsight 3.2.3.537.1486660 (HDP 2.2.2.2-4)
* SDK 1.5.8

Esta versión contiene las siguientes actualizaciones.

<table border="1">
<tr>
<th>Título</th>
<th>Descripción</th>
<th>Área afectada (por ejemplo, servicio, componente o SDK)</p></th>
<th>Tipo (por ejemplo, Hadoop, HBase o tormenta) de clúster</th>
<th>JIRA (si procede)</th>
</tr>


<tr>
<td>Corregir la dependencia DLL</td>
<td>Elimina la dependencia de HDInsight de marco de pruebas unitarias.</td>
<td>SDK</td>
<td>Hadoop</td>
<td>N/A.</td>
</tr>

<tr>
<td>Corregir el error de condición</td>
<td>Un clúster de crear solicitud ahora espera se aceptan antes de sondeo en el estado de la solicitud de SUPERPONER</td>
<td>SDK</td>
<td>Hadoop</td>
<td>N/A.</td>
</tr>
</table>

## <a name="notes-for-04142015-release-of-hdinsight"></a>Notas de la versión de 14/04/2015 de HDInsight

Los números de la versión completa de clústeres HDInsight implementados en esta versión:

* HDInsight 2.1.10.521.1453250 (HDP 1.3.12.0-01795 - sin cambios)
* HDInsight 3.0.6.521.1453250 (HDP 2.0.13.0-2117 - sin cambios)
* HDInsight 3.1.3.521.1453250 (HDP 2.1.12.0-2329 - sin cambios)
* HDInsight 3.2.3.525.1459730 (HDP 2.2.2.2-2)
* SDK 1.5.6

Esta versión contiene las siguientes actualizaciones.

<table border="1">
<tr>
<th>Título</th>
<th>Descripción</th>
<th>Área afectada (por ejemplo, servicio, componente o SDK)</p></th>
<th>Tipo (por ejemplo, Hadoop, HBase o tormenta) de clúster</th>
<th>JIRA (si procede)</th>
</tr>


<tr>
<td>Correcciones de errores de Tez</td>
<td>Soluciones para Apache TEZ 2214 y TEZ 1923 se incluyen en esta versión de HDI 3.2. Específicamente son necesarios para determinadas consultas subárbol en Tez que requieren reorganización una gran cantidad de datos.
</td>
<td>HDP</td>
<td>Hadoop</td>
<td><a href="https://issues.apache.org/jira/browse/TEZ-2214">TEZ 2214</a></br><a href="https://issues.apache.org/jira/browse/TEZ-1923">TEZ 1923</a></td>
</tr>
</table>

## <a name="notes-for-04062015-release-of-hdinsight"></a>Notas de la versión 04/06/2015 de HDInsight

Los números de la versión completa de clústeres HDInsight implementados en esta versión:

* HDInsight 2.1.10.521.1453250 (HDP 1.3.12.0-01795 - sin cambios)
* HDInsight 3.0.6.521.1453250 (HDP 2.0.13.0-2117 - sin cambios)
* HDInsight 3.1.3.521.1453250 (HDP 2.1.12.0-2329 - sin cambios)
* HDInsight 3.2.3.521.1453250 (HDP 2.2.2.2-1)
* SDK 1.5.6

Esta versión contiene las siguientes actualizaciones.

<table border="1">
<tr>
<th>Título</th>
<th>Descripción</th>
<th>Área afectada (por ejemplo, servicio, componente o SDK)</p></th>
<th>Tipo (por ejemplo, Hadoop, HBase o tormenta) de clúster</th>
<th>JIRA (si procede)</th>
</tr>


<tr>
<td>HDInsight .NET SDK 1.5.6</td>
<td>Actualizaciones a quitar algunas clases internos para HDInsight en Linux.</td>
<td>SDK</td>
<td>Hadoop</td>
<td>N/A.</td>
</tr>

<tr>
<td>Biblioteca de Avro 1.5.6</td>
<td>Agregado <b>KnownTypeAttribute</b> método <b>GetAllKnownTypes</b>. NullReferenceException fijo cuando un tipo es null para GetAllKnownTypes método.</td>
<td>SDK</td>
<td>Hadoop</td>
<td>N/A.</td>
</tr>

<tr>
<td>Correcciones de errores</td>
<td>Varias correcciones de errores del servicio</td>
<td>Servicio</td>
<td>Todos los</td>
<td>N/A.</td>
</tr>

</table>
<br>

## <a name="notes-for-04012015-release-of-hdinsight"></a>Notas de la versión de 04/01/2015 de HDInsight

Los números de la versión completa de clústeres HDInsight implementados en esta versión:

* HDInsight 2.1.10.513.1431705 (HDP 1.3.12.0-01795)
* HDInsight 3.0.6.513.1431705 (HDP 2.0.13.0-2117)
* HDInsight 3.1.3.513.1431705 (HDP 2.1.12.0-2329)
* HDInsight 3.2.3.513.1431705 (HDP 2.2.2.1-2600)
* SDK 1.5.5

Esta versión contiene las siguientes actualizaciones.

<table border="1">
<tr>
<th>Título</th>
<th>Descripción</th>
<th>Área afectada (por ejemplo, servicio, componente o SDK)</p></th>
<th>Tipo (por ejemplo, Hadoop, HBase o tormenta) de clúster</th>
<th>JIRA (si procede)</th>
</tr>


<tr>
<td>Capacidad para habilitar o deshabilitar las credenciales de escritorio remotas en clústeres de Windows a través de .NET SDK</td>
<td>Soporte técnico de programación para habilitar o deshabilitar las credenciales RDP en clústeres de Windows.</td>
<td>SDK</td>
<td>Todos los</td>
<td>N/A.</td>
</tr>

<tr>
<td>Posibilidad de habilitar las credenciales de escritorio remotas en clústeres mientras se aprovisionados</td>
<td>Soporte técnico de programación para habilitar las credenciales de escritorio remotas mientras se crea el clúster. Esta acción quita el proceso de dos pasos para el clúster de aprovisionamiento en primer lugar y, a continuación, habilitar Escritorio remoto.</td>
<td>SDK</td>
<td>Todos los</td>
<td>N/A.</td>
</tr>

<tr>
<td>Python actualizado a 2.7.8</td>
<td>Soluciones de Python actualizado en clústeres HDInsight Python 2.7.8, que contiene algunos importantes de seguridad para HDInsight versiones 2.1, 3.0, 3.1 y 3.2</td>
<td>Servicio</td>
<td>Todos los</td>
<td>N/A.</td>
</tr>

<tr>
<td>Cambio de configuración de hilo</td>
<td>Modificado hilo configuración yarn.resourcemanager.max completado-aplicaciones a 1000 para todos los tipos de clúster HDInsight versiones 3.1 y 3.2. Este valor solo controla la lista de aplicaciones completadas en la interfaz de usuario del hilo. Para obtener información acerca de las aplicaciones que se han enviado antes de la lista de aplicaciones que se muestra en la interfaz de usuario, puede ir directamente al servidor de historial.</td>
<td>HILO</td>
<td>Todos los</td>
<td>N/A.</td>
</tr>

<tr>
<td>Cambiar el tamaño de los nodos en un clúster de HBase</td>
<td>Clústeres de HBase ahora permiten cambiar el tamaño de nodos (hacia arriba y abajo) para las versiones de HDInsight 3.1 y 3.2</td>
<td>Servicio</td>
<td>HBase</td>
<td>N/A.</td>
</tr>

<tr>
<td>Actualización JDBC</td>
<td>Controlador JDBC de SQL se actualizará a la versión sqljdbc_4.0.2206.100 para HDInsight versión 3.2. Esta versión contiene importantes mejoras de seguridad.</td>
<td>HDP</td>
<td>Todos los</td>
<td>N/A.</td>
</tr>

<tr>
<td>Actualización de la configuración de JVM</td>
<td>Actualizado JVM configuración networkaddress.cache.ttl a 300 segundos desde el valor predeterminado de -1 HDInsight versiones 3.1 y 3.2. Este valor de configuración controla la directiva de caché para las búsquedas de nombre correcta del servicio de nombres. Esto corrige un error relacionado con el aumento o reducción HBase clústeres.</td>
<td>Servicio</td>
<td>HBase</td>
<td>N/A.</td>
</tr>

<tr>
<td>Actualización de almacenamiento de Azure SDK de Java 2.0</td>
<td>HDInsight versión 3.2 se actualiza para usar la versión más reciente de Azure almacenamiento SDK para Java. Esta página contiene varias correcciones de errores importantes sobre la 0.6.0 actual versión.</td>
<td>HDP</td>
<td>Todos los</td>
<td>N/A.</td>
</tr>

<tr>
<td>Actualizado con código de origen Apache WASB</td>
<td>HDInsight versión 3.2 ahora utiliza la información más reciente de código para el controlador de sistema de archivos WASB de Hadoop Apache. Con este cambio, el controlador WASB ahora se presenta como un jar independiente. Este es puramente un cambio de embalaje y no contiene los cambios de comportamiento del controlador WASB. El nombre de este archivo JAR es 2.6.0.jar de azure de hadoop.</td>
<td>HDP</td>
<td>Todos los</td>
<td>N/A.</td>
</tr>

<tr>
<td>JAR actualizaciones de nombre de archivo en HDInsight 3.2</td>
<td>Esta actualización a HDInsight versión 3.2 contiene varias correcciones de errores y unos jars internos empaquetadas como parte de HDP se han actualizado. Tenga en cuenta que estos archivos JAR internos para el paquete HDP y no para su uso directo por aplicaciones cliente. Aplicaciones deben empaquetar su propia versión de la JARs para que una actualización a la JARs internos HDP no romper aplicaciones cliente.</td>
<td>HDP</td>
<td>Todos los</td>
<td>N/A.</td>
</tr>

</table>
<br>

## <a name="notes-for-03032015-release-of-hdinsight"></a>Notas de la versión de 03/03/2015 de HDInsight

Los números de la versión completa de clústeres HDInsight implementados en esta versión:

* HDInsight 2.1.10.488.1375841 (HDP 1.3.9.0-01351 - sin cambios)
* HDInsight 3.0.6.488.1375841 (HDP 2.0.9.0-2097 - sin cambios)
* HDInsight 3.1.3.488.1375841 (HDP 2.1.10.0-2290 - sin cambios)
* HDInsight 3.2.3.488.1375841 (HDP-2.2.10.0-2340 - sin cambios)
* SDK 1.5.0 (sin cambios)

Esta versión contiene la siguiente actualización.

<table border="1">
<tr>
<th>Título</th>
<th>Descripción</th>
<th>Área afectada (por ejemplo, servicio, componente o SDK)</p></th>
<th>Tipo (por ejemplo, Hadoop, HBase o tormenta) de clúster</th>
<th>JIRA</th>
</tr>


<tr>
<td>Mejoras en la confiabilidad</td>
<td>Hemos realizado revisiones que permiten el servicio escalar mejor con el aumento de carga con respecto a la creación de un clúster.</td>
<td>Servicio</td>
<td>Todos los</td>
<td>N/A.</td>
</tr>



</table>
<br>

## <a name="notes-for-02182015-release-of-hdinsight"></a>Notas de la versión de 18/02/2015 de HDInsight

Los números de la versión completa de clústeres HDInsight implementados en esta versión:

* HDInsight 2.1.10.471.1342507 (HDP 1.3.9.0-01351 - sin cambios)
* HDInsight 3.0.6.471.1342507 (HDP 2.0.9.0-2097 - sin cambios)
* HDInsight 3.1.3.471.1342507 (HDP 2.1.10.0-2290 - sin cambios)
* HDInsight 3.2.3.471.1342507 (HDP-2.2.10.0-2340)
* SDK 1.5.0

Esta versión contiene las siguientes actualizaciones.

<table border="1">
<tr>
<th>Título</th>
<th>Descripción</th>
<th>Área afectada (por ejemplo, servicio, componente o SDK)</p></th>
<th>Tipo (por ejemplo, Hadoop, HBase o tormenta) de clúster</th>
<th>JIRA (si procede)</th>
</tr>


<tr>
<td>HDInsight 3.2 clústeres</td>
<td>Hadoop 2.6/HDP2.2 está disponible con HDInsight 3.2 clústeres. Contiene actualizaciones principales para todos los componentes de código abierto. Para obtener más detalles, consulte Novedades de HDInsight y <a href ="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.0/HDP_2.2.0_Release_Notes_20141202_version/index.html" target="_blank">notas de la versión HDP 2.2.0.0</a>.</td>
<td>Software de código abierto</td>
<td>Todos los</td>
<td>N/A.</td>
</tr>

<tr>
<td>HDinsight en Linux (vista preliminar)</td>
<td>Se pueden implementar clústeres ejecutando en Ubuntu Linux. Para obtener más detalles, vea Introducción a HDInsight en Linux.</td>
<td>Servicio</td>
<td>Hadoop</td>
<td>N/A.</td>
</tr>

<tr>
<td>Disponibilidad General de tormenta</td>
<td>En general, existen clústeres Apache tormenta. Para obtener más detalles, vea Introducción al uso de tormenta en HDInsight.</td>
<td>Servicio</td>
<td>Tormenta</td>
<td>N/A.</td>
</tr>

<tr>
<td>Tamaños de máquina virtual</td>
<td>HDInsight de Azure está disponible en más tipos de máquina virtual y tamaños. HDInsight puede utilizar A2 a tamaños A7 integrados con fines generales. Nodos de serie D que unidades de estado sólidas (SSD) y procesadores más rápidos de 60%; tamaños de A8 y A9 que tienen InfiniBand soporte técnico y de una red rápida.</td>
<td>Servicio</td>
<td>Todos los</td>
<td>N/A.</td>
</tr>

<tr>
<td>Ajuste de escala de clúster</td>
<td>Puede cambiar el número de nodos de datos para un clúster de HDInsight ejecución sin tener que eliminar o volver a crearlo. Actualmente, solo los tipos de clúster Hadoop consulta y Apache tormenta tienen esta capacidad, pero la compatibilidad con HBase Apache tipo de clúster es pronto para seguir. Para obtener más información, vea Administrar HDInsight clústeres.</td>
<td>Servicio</td>
<td>Hadoop, tormenta</td>
<td>N/A.</td>
</tr>

<tr>
<td>Herramientas Visual Studio</td>
<td>Además de herramientas completa para Apache tormenta, las herramientas para Apache subárbol en Visual Studio se ha actualizado para incluir la finalización automática de instrucciones, validación locales y compatibilidad de depuración mejorada. Para obtener más información, vea Introducción a las herramientas de Hadoop HDInsight para Visual Studio.</td>
<td>Herramientas</td>
<td>Hadoop</td>
<td>N/A.</td>
</tr>

<tr>
<td>Conector de Hadoop para DocumentDB</td>
<td>Con Hadoop conector para DocumentDB, puede realizar agregaciones complejas, análisis y manipulación sobre sin esquemas JSON documentos almacenados en las colecciones de DocumentDB o en todas las cuentas de base de datos. Para obtener un tutorial y obtener más información, consulte usar DocumentDB y HDInsight de trabajos de ejecutar Hadoop.</td>
<td>Servicio</td>
<td>Hadoop</td>
<td>N/A.</td>
</tr>

<tr>
<td>Correcciones de errores</td>
<td>Hemos realizado varios secundarias correcciones de errores para servicios de HDInsight. No se esperan cambios de comportamiento orientados al cliente.</td>
<td>Servicio</td>
<td>Todos los</td>
<td>N/A.</td>
</tr>

</table>
<br>

## <a name="notes-for-02062015-release-of-hdinsight"></a>Notas de versión de 06/02/2015 de HDInsight

Los números de la versión completa de clústeres HDInsight implementados en esta versión:

* HDInsight 2.1.10.463.1325367 (HDP 1.3.9.0-01351 - sin cambios)
* HDInsight 3.0.6.463.1325367 (HDP 2.0.9.0-2097 - sin cambios)
* HDInsight 3.1.2.463.1325367 (HDP 2.1.10.0-2290)
* SDK N/A.

Esta versión contiene las siguientes actualizaciones.

<table border="1">
<tr>
<th>Título</th>
<th>Descripción</th>
<th>Área afectada (por ejemplo, servicio, componente o SDK)</p></th>
<th>Tipo (por ejemplo, Hadoop, HBase o tormenta) de clúster</th>
<th>JIRA (si procede)</th>
</tr>


<tr>
<td>Correcciones de errores</td>
<td>Hemos realizado varios secundarias correcciones de errores para servicios de HDInsight. No se esperan cambios de comportamiento orientados al cliente.</td>
<td>Servicio</td>
<td>Todos los</td>
<td>N/A.</td>
</tr>

<tr>
<td>Actualización de mantenimiento de HDP 2.1</td>
<td>HDInsight 3.1 se actualiza para implementar HDP 2.1.10.0. Para obtener más información, consulte <a href ="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.10/bk_releasenotes_hdp_2.1/content/ch_relnotes-HDP-2.1.10.html" target="_blank">notas HDP-2.1.10</a>. </td>
<td>Software de código abierto</td>
<td>Todos los</td>
<td>N/A.</td>
</tr>

<tr>
<td>Actualizaciones HDP binarias</td>
<td>Hay algunos archivos JAR en HBase para el archivo que se han actualizado nombres. Estos archivos JAR se utilizan internamente HBase, por lo que no se espera que los clientes tengan una dependencia en los nombres de estos archivos JAR. Se incluyen:
<ul>
<li>./lib/jetty-6.1.26.hwx.jar</li>
<li>./lib/jetty-sslengine-6.1.26.hwx.jar</li>
<li>./lib/jetty-util-6.1.26.hwx.jar</li>
</ul>
</td>
<td>Software de código abierto</td>
<td>HBase</td>
<td>N/A.</td>
</tr>

</table>
<br>

## <a name="notes-for-1292015-release-of-hdinsight"></a>Notas de versión de 29/1/2015 de HDInsight

Los números de la versión completa de clústeres HDInsight implementados en esta versión:

* HDInsight 2.1.10.455.1309616 (HDP 1.3.9.0-01351 - sin cambios)
* HDInsight 3.0.6.455.1309616 (HDP 2.0.9.0-2097 - sin cambios)
* HDInsight 3.1.2.455.1309616 (HDP 2.1.9.0-2196 - sin cambios)
* SDK N/A.

Esta versión contiene la siguiente actualización.

<table border="1">
<tr>
<th>Título</th>
<th>Descripción</th>
<th>Área afectada (por ejemplo, servicio, componente o SDK)</p></th>
<th>Tipo (por ejemplo, Hadoop, HBase o tormenta) de clúster</th>
<th>JIRA (si procede)</th>
</tr>


<tr>

<td>Correcciones de errores</td>
<td>Hemos realizado algunos importantes correcciones de errores que mejorar la confiabilidad de los clústeres HDInsight durante las actualizaciones de Azure.</td>
<td>Servicio</td>
<td>Todos los</td>
<td>N/A.</td>
</tr>



</table>
<br>

## <a name="notes-for-152015-release-of-hdinsight"></a>Notas de la versión 5/1/2015 de HDInsight

Los números de la versión completa de clústeres HDInsight implementados en esta versión:

* HDInsight 2.1.10.420.1246118 (HDP 1.3.9.0-01351 - sin cambios)
* HDInsight 3.0.6.420.1246118 (HDP 2.0.9.0-2097 - sin cambios)
* HDInsight 3.1.2.420.1246118 (HDP 2.1.9.0-2196 - sin cambios)


Esta versión contiene las siguientes actualizaciones.

<table border="1">
<tr>
<th>Título</th>
<th>Descripción</th>
<th>Componente</th>
<th>Tipo de clúster</th>
<th>JIRA (si procede)</th>
</tr>


<tr>
<td>Muestras de análisis de tendencias de Twitter y recomendaciones de película basado en Mahout</td>
<td><p>En esta versión, la consola de consulta HDInsight tiene dos muestras adicionales:</p>

<p><b>Análisis de tendencias de Twitter</b><br>
API públicas proporcionadas por sitios como Twitter son un origen de datos para analizar y comprender las tendencias populares útil. En este tutorial, obtenga información sobre cómo utilizar subárbol para obtener una lista de usuarios de Twitter que envía la mayoría tweets que contiene una palabra específica. </p>

<p><b>Recomendación de película Mahout</b><br>
Apache Mahout es una máquina de Apache Hadoop biblioteca de aprendizaje. Mahout contiene algoritmos para el procesamiento de datos (por ejemplo, filtrado, clasificación y clústeres). En este tutorial, utilice un motor de recomendación para generar recomendaciones de película basadas en películas que han visto sus amigos.</p></td>
<td>Consola de consulta</td>
<td>Hadoop</td>
<td>N/A.</td>
</tr>

<tr>
<td>Cambiar al valor predeterminado para la configuración de la sección: hive.auto.convert.join.noconditionaltask.size</td>
<td><p>Esta configuración de tamaño se aplica a las combinaciones de mapa convertido automáticamente. El valor representa la suma de los tamaños de las tablas que se pueden convertir a mapas de hash que caben en la memoria. En una versión anterior, este valor aumenta el valor predeterminado de 10 MB 128 MB. Sin embargo, el nuevo valor de 128 MB estaba causando trabajos de fallar por falta de memoria. Esta versión revierte el valor predeterminado, vuelva a 10 MB. Los clientes aún puede reemplazar este valor durante la creación de clúster, dado su tamaños de tablas y consultas. Para obtener más información sobre esta configuración y cómo reemplazarla, consulte <a href="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.0.2/ds_Hive/optimize-joins.html#JoinOptimization-OptimizeAutoJoinConversion" target="_blank">Optimizar la conversión de unirse a automático</a> en la documentación de Hortonworks. </p></td>
<td>Sección</td>
<td>Hadoop, Hbase</td>
<td>N/A.</td>
</tr>

</table>
<br>

## <a name="notes-for-12232014-release-of-hdinsight"></a>Notas de versión de 23/12/2014 de HDInsight

Los números de versión completa para clústeres HDInsight implementados en esta versión son:

* HDInsight 2.1.10.420.1246783 (versión HDP sin cambios)
* HDInsight 3.0.6.420.1246783 (versión HDP sin cambios)
* HDInsight 3.1.1.420.1246783 (versión HDP sin cambios)

Esta versión contiene la siguiente actualización.

<table border="1">
<tr>
<th>Título</th>
<th>Descripción</th>
<th>Componente</th>
<th>Tipo de clúster</th>
<th>JIRA (si procede)</th>
</tr>


<tr>
<td>Errores de creación de clúster intermitente debido a una carga excesiva</td>
<td><p>Algoritmo mejorado para descargar paquetes HDP durante la creación de clúster permite más rigurosa tratamiento de errores debido a una carga excesiva.</p></td>
<td>Servicio</td>
<td>Hadoop, Hbase, tormenta</td>
<td>N/A.</td>
</tr>



</table>
<br>

## <a name="notes-for-12182014-release-of-hdinsight"></a>Notas de la versión de 18/12/2014 de HDInsight

Esta versión contiene la actualización del componente siguiente.

<table border="1">
<tr>
<th>Título</th>
<th>Descripción</th>
<th>Componente</th>
<th>Tipo de clúster</th>
<th>JIRA (si procede)</th>
</tr>

<tr>
<td><a href = "hdinsight-hadoop-customize-cluster.md" target="_blank">Personalización de clúster General Avalability</a></td>
<td><p>Personalización proporciona la capacidad para personalizar los clústeres HDInsight de Azure con los proyectos que están disponibles desde el ecosistema Hadoop Apache. Con esta nueva característica, puede experimentar e implementar proyectos de Hadoop a HDInsight de Azure. Esto es posible mediante la característica de **Secuencia de comandos de acción** , que puede modificar Hadoop clústeres de maneras arbitrario mediante scripts personalizados. Esta personalización está disponible en todos los tipos de clústeres HDInsight incluidos Hadoop, HBase y tormenta. Para mostrar la potencia de esta función, nos hemos documentado el proceso para instalar el <a href = "hdinsight-hadoop-spark-install.md" target="_blank">motor</a>de populares, <a href = "hdinsight-hadoop-r-scripts.md" target="_blank">R</a>, <a href = "hdinsight-hadoop-solr-install.md" target="_blank">Solr</a>y <a href = "hdinsight-hadoop-giraph-install.md" target="_blank">Giraph</a> módulos. Esta versión también se agrega la capacidad de los clientes especificar su acción scripts personalizados mediante el portal de Azure proporciona directrices y recomendaciones sobre cómo crear acciones de script personalizado usando métodos auxiliares y proporciona instrucciones detalladas sobre cómo probar la acción de secuencia de comandos. </p></td>
<td>Disponibilidad General de la característica</td>
<td>Todos los</td>
<td>N/A.</td>
</tr>


</table>
<br>

## <a name="notes-for-12052014-release-of-hdinsight"></a>Notas de la versión 05/12/2014 de HDInsight

Los números de versión completa para clústeres HDInsight implementados en esta versión son:

* HDInsight 2.1.9.406.1221105 (HDP 1.3.9.0-01351)
* HDInsight 3.0.5.406.1221105 (HDP 2.0.9.0-2097)
* HDInsight 3.1.1.406.1221105 (HDP 2.1.9.0-2196)
* HDInsight SDK n/a.

Esta versión contiene las siguientes actualizaciones de componente.

<table border="1">
<tr>
<th>Título</th>
<th>Descripción</th>
<th>Componente</th>
<th>Tipo de clúster</th>
<th>JIRA (si procede)</th>
</tr>

<tr>
<td>Corregir el error: error intermitente al agregar un gran número de particiones a una tabla en un DDL subárbol </td>
<td><p>Si hay un error de conexión intermitentes con la base de datos de metastore subárbol al agregar una gran cantidad de particiones a una tabla subárbol, puede fallar el DDL subárbol. La siguiente instrucción se verán en el registro de errores de la sección si se produce este error: </p><p>"ERROR [main]: ql. Controlador (SessionState.java:printError(547)) - Error: Error de ejecución de código de retorno 1 desde org.apache.hadoop.hive.ql.exec.DDLTask. MetaException (message:java.lang.RuntimeException: commitTransaction se denominaba pero openTransactionCalls = 0. Probablemente indica que hay llamadas desequilibradas a openTransaction/commitTransaction)"</p></td>
<td>Sección</td>
<td>Hadoop, Hbase</td>
<td>SECCIÓN-482 (este es un JIRA interno, por lo que no puede estar entre comillas externamente. Se indica aquí para referencia.)</td>
</tr>

<tr>
<td>Corregir el error: ocasional se bloquea en la consola de consulta HDInsight</td>
<td>En este caso, la siguiente instrucción puede verse en el registro de WebHCat para el trabajo de selector de WebHCat: <p>"org.apache.hive.hcatalog.templeton.CatchallExceptionMapper | org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.yarn.exceptions.YarnRuntimeException): no se puede cargar el archivo de historial {wasb dirección url para el archivo de historial} "</p></td>
<td>WebHCat</td>
<td>Hadoop</td>
<td>SECCIÓN-482 (este es un JIRA interno, por lo que no puede estar entre comillas externamente. Se indica aquí para referencia.)</td>
</tr>

<tr>
<td>Corregir el error: punta ocasional en la latencia de consultas Hbase</td>
<td>Si esto sucede, los usuarios notarán una punta ocasional de 3 segundos en la latencia de consultas Hbase. </td>
<td>Puerta de enlace de clúster HDInsight</td>
<td>HBase</td>
<td>N/A.</td>
</tr>

<tr>
<td>Cambios de nombre de archivo JAR HDP</td>
<td>Versión 3.0, hay un par de cambios en los archivos JAR internos instalados por HDP clúster HDI. 6.1.26.jar embarcadero se reemplazó con embarcadero 6.1.26.hwx.jar. 6.1.26.jar de utilizada de embarcadero se reemplazó con embarcadero-utilizada-6.1.26.hwx.jar. Estos cambios se aplican a proyectos de Hadoop, Mahout, WebHCat y Oozie.</td>
<td>Hadoop, Mahout, WebHCat, Oozie</td>
<td>Hadoop, HBase</td>
<td>N/A.</td>
</tr>

</table>
<br>


## <a name="notes-for-11212014-release-of-hdinsight"></a>Notas de la versión de 21/11/2014 de HDInsight

Los números de versión completa para clústeres HDInsight implementados en esta versión son:

* HDInsight 2.1.9.382.1169709 (sin cambios de 14/11/2014)
* HDInsight 3.0.5.382.1169709 (sin cambios de 14/11/2014)
* HDInsight 3.1.1.382.1169709 (sin cambios de 14/11/2014)
* HDINsight SDK 1.4.0

Esta versión contiene las siguientes actualizaciones de componente.

<table border="1">
<tr>
<th>Título</th>
<th>Descripción</th>
<th>Componente</th>
<th>Tipo de clúster</th>
<th>JIRA (si procede)</th>
</tr>

<tr>
<td>Acceso a registros de la aplicación</td>
<td>Capacidad para enumerar mediante programación las aplicaciones que se han ejecutado en los clústeres y descargar los registros específicos de la aplicación o específico del contenedor relevantes para ayudarle a depurar aplicaciones problemáticas.</td>
<td>SDK</td>
<td>Hadoop</td>
<td>N/A.</td>
</tr>

<tr>
<td>Capacidad para especificar el nombre del área de IHdInsightClient.DeleteCluster </td>
<td>El SDK de HDInsight de Azure proporciona la capacidad para especificar un nombre de región al utilizar **DeleteCluster**. Esto le permite desbloquear a los clientes que tenían dos recursos con el mismo nombre en diferentes regiones y se hubiera no se puede eliminar cualquiera de ellas.</td>
<td>SDK</td>
<td>Todos los</td>
<td>N/A.</td>
</tr>

<tr>
<td>ClusterDetails.DeploymentId</td>
<td>El objeto **ClusterDetails** devuelve un campo **DeploymentID** que representa un identificador único para el clúster. Se garantiza que ser único en intentos de creación de clúster con los mismos nombres.</td>
<td>SDK</td>
<td>Todos los</td>
<td>N/A.</td>
</tr>
</table>
<br>

## <a name="notes-for-11142014-release-of-hdinsight"></a>Notas de la versión de 14/11/2014 de HDInsight

Los números de versión completa para clústeres HDInsight implementados en esta versión son:

* HDInsight 2.1.9.382.1169709
* HDInsight 3.0.5.382.1169709
* HDInsight 3.1.1.382.1169709

Esta versión contiene las siguientes características nuevas, actualizaciones de componentes y correcciones de errores.

<table border="1">
<tr>
<th>Título</th>
<th>Descripción</th>
<th>Componente</th>
<th>Tipo de clúster</th>
<th>JIRA (si procede)</th>
</tr>

<tr>
<td>Acción de secuencia de comandos (vista preliminar)</td>
<td>Vista previa de la característica de personalización de clúster que permite la modificación de Hadoop clústeres de maneras arbitrario mediante scripts personalizados. Con esta característica, los usuarios pueden experimentar con e implementar proyectos que están disponibles desde el ecosistema Apache Hadoop a HDInsight de Azure clústeres. Esta característica de personalización está disponible en todos los tipos de HDInsight clústeres, incluidos Hadoop, HBase y tormenta.</td>
<td>Nueva característica</td>
<td>Todos los</td>
<td>N/A.</td>
</tr>

<tr>
<td>Prediseñadas trabajos de análisis de registro de sitios Web y almacenamiento de Azure</td>
<td>La consola de consulta HDInsight tiene una galería de introducción que admita soluciones que funcionan en los datos o en los datos de ejemplo.
<p>**Soluciones que funcionan en los datos**:<br>
Hemos creado trabajos para algunos de los escenarios más comunes de análisis de datos para proporcionar un punto de partida para crear sus propias soluciones. Puede usar los datos con el trabajo para ver cómo funciona. A continuación, cuando esté listo, utilice lo que ha aprendido a crear una solución que se basa en el trabajo prediseñado.</p>
<p>**Soluciones que funcionan con los datos de ejemplo**:<br>
Obtenga información sobre cómo trabajar con HDInsight a través de algunos escenarios básicos (por ejemplo, para analizar datos de sensor y registros web). Aprenderá cómo usar HDInsight para analizar los datos y cómo puede conectar otras aplicaciones y servicios a los datos. Visualización de los datos mediante una conexión a Microsoft Excel proporciona un ejemplo de la eficacia de este enfoque.</p></td>
<td>Consola de consulta</td>
<td>Hadoop</td>
<td>N/A.</td>
</tr>

<tr>
<td>Corrección de pérdida de memoria en Templeton</td>
<td>Una pérdida de memoria en Templeton que afecta a los clientes que tenían largas en ejecución clústeres o se han enviar 100s de trabajo solicita que un segundo se ha tratado. El problema que manifiestan como Templeton 5xx errores y la solución alternativa era reiniciar el servicio. Ya no es necesaria la solución alternativa.</td>
<td>Templeton</td>
<td>Todos los</td>
<td>https://Issues.Apache.org/jira/Browse/HADOOP-11248</td>
</tr>
</table>
<br>


**Nota**: para demostrar las nuevas capacidades de ofrecerse personalización de clúster, se han documentado los procedimientos con la acción de secuencia de comandos para instalar el motor y R módulos en un clúster. Para obtener más información, consulte:

* [Instalar y usar motor 1.0 en clústeres HDInsight](hdinsight-hadoop-spark-install.md)
* [Instalar y usar R en clústeres HDInsight Hadoop](hdinsight-hadoop-r-scripts.md)




## <a name="notes-for-11072014-release-of-hdinsight"></a>Notas de la versión 11/07/2014 de HDInsight

Los números de versión completa para clústeres de HDInsight implementado en esta versión son:

* HDInsight 2.1 2.1.9.374.1153876
* HDInsight 3.0 3.0.5.374.1153876
* HDInsight 3.1 3.1.1.374.1153876

Esta versión contiene las siguientes actualizaciones de componente.

<table border="1">
<tr>
<th>Título</th>
<th>Descripción</th>
<th>Componente</th>
<th>Tipo de clúster</th>
<th>JIRA (si procede)</th>
</tr>

<tr>
<td>HDP 2.1.7</td>
<td>Esta versión se basa en Hortonworks datos plataforma (HDP) 2.1.7. Para obtener más información, consulte <a href="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html" target="_blank">Notas de HDP 2.1.7</a>.</td>
<td>HDP</td>
<td>Todos los</td>
<td>N/A.</td>
</tr>

<tr>
<td>Servidor de escala de tiempo del hilo</td>
<td>Se ha habilitado el servidor de escala de tiempo de hilo (también conocido como el genérico historial de servidor de aplicaciones) de forma predeterminada. El servidor de escala de tiempo proporciona información genérica sobre aplicaciones completadas (por ejemplo, el identificador de la aplicación, nombre de la aplicación, el estado de aplicación, hora de envío de la aplicación y hora de finalización de la aplicación).

Esta información de la aplicación se puede recuperar desde el nodo principal accediendo URI http://headnodehost:8188 o al ejecutar el comando hilo: hilo aplicación: lista – appStates todos.

Esta información también puede recuperarse remotamente aunque una API de REST en https://{ClusterDnsName}. azurehdinsight.NET/WS/v1/applicationhistory/.

Para obtener información detallada, vea <a href="http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html" target="_blank">Servidor de escala de tiempo del hilo</a>.</td>
<td>Servicio de hilo</td>
<td>Hadoop, HBase</td>
<td>N/A.</td>
</tr>

<tr>
<td>Id. de implementación de clúster</td>
<td>A partir de la versión 1.3.3.1.5426.29232 SDK, los usuarios pueden acceder a un identificador único para cada clúster, que se haya emitido HDInsight. Esto permite a los clientes averiguar instancias únicas de clústeres cuando se va a reutilizar un nombre DNS en crear o quitar escenarios.</td>
<td>SDK</td>
<td>Todos los</td>
<td>N/A.</td>
</tr>
</table>
<br>

**Nota**: se ha corregido el error impide el número de versión completa de mostrarse en el portal de o de que se devuelven mediante el SDK o Windows PowerShell en esta versión.

## <a name="notes-for-10152014-release"></a>Notas de la versión de 15/10/2014

Esta versión de revisión corrige la pérdida de memoria en Templeton que afecta a los usuarios grandes de Templeton. En algunos casos, los usuarios que había ejecutado Templeton intensamente verá errores manifiestan como 500 códigos de error porque las solicitudes no tendría memoria insuficiente para ejecutar. La solución alternativa para este problema fue reiniciar el servicio Templeton. Este problema se ha corregido.


## <a name="notes-for-1072014-release"></a>Notas de la versión 7 de 10/2014

* Cuando se usa el extremo de Ambari, "https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname}", el campo *host_name* devuelve el nombre de dominio completo (FQDN) de este nodo en lugar de solo el nombre de host. Por ejemplo, recibe el nombre de dominio completo en lugar de "**headnode0**", "**headnode0. { ClusterDNS} .azurehdinsight .net**". Este cambio se necesarias para facilitar escenarios donde se pueden implementar varios tipos de clúster (como HBase y Hadoop) en una red virtual. Esto sucede, por ejemplo, cuando se usa HBase como una plataforma de back-end para Hadoop.

* Proporcionamos la nueva configuración de la memoria para la implementación predeterminada del clúster HDInsight. Configuración predeterminada de memoria anterior no tuvo en cuenta adecuadamente de la guía para el número de núcleos de CPU está implementando. Estas nuevas opciones de memoria deben proporcionar mejores valores predeterminados (de acuerdo con las recomendaciones de Hortonworks). Para cambiar estas características, consulte la documentación de referencia acerca de cómo cambiar la configuración de clúster. En la siguiente tabla se detallada de la nueva configuración de memoria que usa el clúster de HDInsight predeterminado 4 CPU core (contenedor 8). (Parenthetically también se proporcionan los valores que se utiliza antes de esta versión.)

<table border="1">
<tr><th>Componente</th><th>Asignación de memoria</th></tr>
<tr><td> asignación de yarn.Scheduler.Minimum</td><td>768 MB (anteriormente, se recomienda 512 MB)</td></tr>
<tr><td> asignación de yarn.Scheduler.Maximum</td><td>6144 MB (sin cambios)</td></tr>
<tr><td>yarn.NodeManager.Resource.Memory</td><td>6144 MB (sin cambios)</td></tr>
<tr><td>MapReduce.Map.Memory</td><td>768 MB (anteriormente, se recomienda 512 MB)</td></tr>
<tr><td>MapReduce.Map.java.OPTS</td><td>opte =-Xmx512m (anteriormente - Xmx410m)</td></tr>
<tr><td>MapReduce.reduce.Memory</td><td>1536 MB (anteriormente 1024 MB)</td></tr>
<tr><td>MapReduce.reduce.java.OPTS</td><td>opte =-Xmx1024m (anteriormente - Xmx819m)</td></tr>
<tr><td>yarn.app.MapReduce.AM.Resource</td><td>768 MB (anteriormente 1024 MB)</td></tr>
<tr><td>yarn.app.MapReduce.AM.Command</td><td>opte =-Xmx512m (anteriormente - Xmx819m)</td></tr>
<tr><td>MapReduce.Task.IO.Sort</td><td>256 MB (anteriormente 200 MB)</td></tr>
<tr><td>tez.AM.Resource.Memory</td><td>1536 MB (sin cambios)</td></tr>

</table><br>

Para obtener más información sobre la configuración de memoria utilizada por hilo y MapReduce en la plataforma de datos de Hortonworks que utiliza HDInsight, consulte [Determinar opciones de configuración de memoria HDP](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1-latest/bk_installing_manually_book/content/rpm-chap1-11.html). Hortonworks también proporciona una herramienta para calcular la configuración correcta de la memoria.

Respecto a Azure PowerShell y el mensaje de error HDInsight SDK: "*clúster no está configurado para el acceso de servicios HTTP*":

* Este error es un [problema de compatibilidad](https://social.msdn.microsoft.com/Forums/azure/a7de016d-8de1-4385-b89e-d2e7a1a9d927/hdinsight-powershellsdk-error-cluster-is-not-configured-for-http-services-access?forum=hdinsight) conocido que puede ocurrir debido a una diferencia en la versión de SDK HDInsight o Azure PowerShell y la versión del clúster. Clústeres creados en 15/8 o posterior tienen compatibilidad para la nueva capacidad de aprovisionamiento en redes virtuales. Pero esta función no se interpreta correctamente las versiones anteriores de la HDInsight SDK o PowerShell de Azure. El resultado es un error en algunas operaciones de envío de trabajo. Si usa las API de SDK HDInsight o cmdlets de PowerShell de Azure (**AzureRmHDInsightCluster de uso** o **Invocar AzureRmHDInsightHiveJob**) para enviar trabajos, estas operaciones pueden producir el mensaje de error "*clúster <clustername> no está configurado para el acceso de servicios HTTP*." O (dependiendo de la operación), puede recibir otros mensajes de error, como "*no se puede conectar al clúster*".

* Estos problemas de compatibilidad se resuelven en las últimas versiones del SDK HDInsight y PowerShell de Azure. Recomendamos que actualice el SDK HDInsight versión 1.3.1.6 o herramientas de PowerShell de Azure y posteriores a la versión 0.8.8 o posterior. Puede obtener acceso a la última SDK HDInsight de [](http://nuget.codeplex.com/wikipage?title=Getting%20Started) y las herramientas de PowerShell de Azure a [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md).



## <a name="notes-for-9122014-release-of-hdinsight-31"></a>Notas de la versión 9/12/2014 de 3.1 HDInsight

* Esta versión se basa en Hortonworks datos plataforma (HDP) 2.1.5. Para obtener una lista de los errores corregidos en esta versión, consulte la página de [fijo en esta versión](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.5/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.5-fixed.html) en el sitio Hortonworks.
* En la carpeta de bibliotecas cerdo, el archivo "avro-mapred-1.7.4.jar" se ha cambiado a "avro-mapred-1.7.4-hadoop2.jar". El contenido de este archivo contiene corregir un error menor que es de no separación. Se recomienda que los clientes no realizan una dependencia directa en el nombre del archivo JAR para evitar saltos cuando se cambia el nombre de archivos.


## <a name="notes-for-8212014-release"></a>Notas de la versión 8/21 de 2014

* Agregamos la siguiente configuración de WebHCat (SUBÁRBOL-7155), que establece el límite de memoria predeterminado para un trabajo de controlador de Templeton en 1 GB. (El valor predeterminado anterior era 512 MB).

     templeton.Mapper.Memory.MB (= 1024)

    * Este cambio corrige el siguiente error que determinadas consultas subárbol tenían debido a los límites de memoria inferiores: "El contenedor está ejecutando más allá de los límites de memoria física".
    * Para volver a los valores predeterminados para los antiguos, puede establecer este valor de configuración en 512 a través de PowerShell de Azure en tiempo de creación de clúster mediante el comando siguiente:

        AzureRmHDInsightConfigValues agregar-Core@{"templeton.mapper.memory.mb"="512";}


* Se cambió el nombre de host de la función zookeeper a *zookeeper*. Esto afecta a la resolución de nombres en el clúster, pero no afecta a la API de REST externas. Si tiene componentes que utilizan el nombre de host de *zookeepernode* , debe actualizarlos para usar el nuevo nombre. Los nuevos nombres de los tres nodos de zookeeper son:
    * zookeeper0
    * zookeeper1
    * zookeeper2
* Matriz de soporte de HBase versión se actualiza. Solo HDInsight versión 3.1 (HBase versión 0,98) es compatible de producción para cargas de trabajo de HBase. Versión 3.0 (que estaba disponible para preview) no se admite mover hacia delante.

## <a name="notes-about-clusters-created-prior-to-8152014"></a>Notas acerca de los clústeres creados antes de 15/8/2014

Un mensaje de error de Azure PowerShell o HDInsight SDK "clúster <clustername> no está configurado para el acceso de servicios HTTP" (dependiendo de la operación, otro mensajes de error o como: "No se puede conectar al clúster") puede producirse debido a la diferencia entre Azure PowerShell o el SDK HDInsight y un clúster de versión. Clústeres creados en 15/8 o posterior tienen compatibilidad para la nueva capacidad de aprovisionamiento en redes virtuales. Esta función no se interpreta correctamente las versiones anteriores de Azure PowerShell o el SDK HDInsight, que da como resultado errores de las operaciones de envío de trabajo. Si usa HDInsight SDK API o Azure PowerShell cmdlets (por ejemplo, Use AzureRmHDInsightCluster o invocar AzureRmHDInsightHiveJob) para enviar trabajos, estas operaciones pueden fallar con uno de los mensajes de error descritos.

Estos problemas de compatibilidad se resuelven en las últimas versiones del SDK HDInsight y PowerShell de Azure. Recomendamos que actualice el SDK HDInsight versión 1.3.1.6 o herramientas de PowerShell de Azure y posteriores a la versión 0.8.8 o posterior. Puede obtener acceso a la última SDK HDInsight de [NuGet][nuget-link]. Puede acceder a las herramientas de PowerShell de Azure utilizando el [Instalador de plataforma Web de Microsoft][webpi-link].


## <a name="notes-for-7282014-release"></a>Notas de la versión de 28/7/2014

* **HDInsight disponible en nuevas áreas**: ampliamos HDInsight geográfica presencia a tres regiones. Los clientes de HDInsight pueden crear clústeres en estas áreas:
    * Asia oriental
    * Norte Central de EE.
    * Sur Central de EE.
* HDInsight versión 1,6 (HDP 1.1 y Hadoop 1.0.3) y HDInsight versión 2.1 (HDP1.3 y Hadoop 1.2) que se eliminan desde el portal de Azure. Puede seguir crear clústeres de Hadoop para estas versiones mediante el cmdlet de PowerShell de Azure [Nuevo AzureRmHDInsightCluster](http://msdn.microsoft.com/library/dn593744.aspx) o mediante el [SDK HDInsight](http://msdn.microsoft.com/library/azure/dn469975.aspx). Consulte la página de [control de versiones de componente HDInsight](hdinsight-component-versioning.md) para obtener más información.
* Cambios de plataforma de datos de Hortonworks (HDP) en esta versión:

<table border="1">
<tr><th>HDP</th><th>Cambios</th></tr>
<tr><td>HDP 1.3 / HDI 2.1</td><td>Sin cambios</td></tr>
<tr><td>HDP 2.0 / HDI 3.0</td><td>Sin cambios</td></tr>
<tr><td>HDP 2.1 / HDI 3.1</td><td>zookeeper: ['3.4.5.2.1.3.0-1948'] -> ['3.4.5.2.1.3.2-0002']</td></tr>


</table><br>

## <a name="notes-for-6242014-release"></a>Notas de la versión 24/6/2014

Esta versión contiene mejoras en el servicio de HDInsight:

* **Disponibilidad de HDP 2.1**: 3.1 HDInsight (que contiene HDP 2.1) está disponible, en general y la versión predeterminada para nuevos clústeres.
* **HBase: mejoras de portal Azure**: vamos a poner HBase clústeres disponible en vista previa. Puede crear clústeres HBase desde el portal con unos pocos clics. 

Con HBase, puede crear una variedad de cargas de trabajo en tiempo real en HDInsight, desde sitios Web interactivos que funcionan con grandes conjuntos de datos para almacenar los datos de telemetría y sensor de millones de puntos finales de servicios. El siguiente paso sería analizar los datos de estas cargas de trabajo con tareas de Hadoop y esto es posible en HDInsight a través de PowerShell de Azure y el panel de diseño de sección.

### <a name="apache-mahout-preinstalled-on-hdinsight-31"></a>Mahout Apache preinstalado en 3.1 HDInsight

 [Mahout](http://hortonworks.com/hadoop/mahout/) está preinstalado en clústeres HDInsight 3.1 Hadoop, para que pueda ejecutar trabajos Mahout sin necesidad de configuración de clúster adicional. Por ejemplo, puede remoto en un clúster de Hadoop mediante el protocolo de escritorio remoto (RDP) y sin pasos adicionales, puede ejecutar el comando Hola mundo Mahout siguientes:

        mahout org.apache.mahout.classifier.df.tools.Describe -p /user/hdp/glass.data -f /user/hdp/glass.info -d I 9 N L  

        mahout org.apache.mahout.classifier.df.BreimanExample -d /user/hdp/glass.data -ds /user/hdp/glass.info -i 10 -t 100

Para obtener una explicación más completa de este procedimiento, consulte la documentación del [Ejemplo Breiman](https://mahout.apache.org/users/classification/breiman-example.html) en el sitio Web de Apache Mahout.


### <a name="hive-queries-can-use-tez-in-hdinsight-31"></a>Las consultas de la sección pueden usar Tez en HDInsight 3.1

Sección 0,13 está disponible en HDInsight 3.1 y es capaz de ejecutar consultas con Tez, que puede aprovecharse de las mejoras de rendimiento significativas.
Tez no es habilitar las consultas de la sección de forma predeterminada. Para utilizarla, debe elegir en. Puede habilitar Tez ejecutando el fragmento de código siguiente:

        set hive.execution.engine=tez;
        select sc_status, count(*), histogram_numeric(sc_bytes,5) from website_logs_orc_local group by sc_status;

Hortonworks ha publicado un desglose detallado subárbol consulta las mejoras de rendimiento con Tez como entregados en las pruebas comparativas estándar. Para obtener más información, vea [pruebas Apache subárbol 13 para Enterprise Hadoop](http://hortonworks.com/blog/benchmarking-apache-hive-13-enterprise-hadoop/).

Para obtener más detalles sobre cómo usar subárbol con Tez, consulte la [sección en Tez](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez).

###<a name="global-availability"></a>Disponibilidad global
Con el lanzamiento de HDInsight en Hadoop 2.2, Microsoft ha realizado HDInsight disponible en todo el mundo principal donde Azure está disponible. Más concretamente, los centros de datos de Europa y Asia Sureste oeste han en línea. Esto permite a los clientes buscar clústeres en un centro de datos que está cerca y, posiblemente, en una zona de requisitos de cumplimiento similar.


###<a name="dos--donts-between-cluster-versions"></a>Cosas que debe y entre versiones de clúster

**Metastores Oozie que se usa con un clúster de HDInsight 3.1 son no compatibles con clústeres HDInsight 2.1, y no pueden utilizarse con esta versión anterior**.

No se puede reutilizar una Oozie metastore base de datos personalizada implementado con un clúster 3.1 HDInsight con un clúster de HDInsight 2.1. Este es el caso, incluso si se ha originado la metastore con un clúster de HDInsight 2.1. Este escenario no se admite porque el esquema metastore obtiene actualizado cuando se utiliza con un clúster HDInsight 3.1, por lo que ya no es compatible con la metastore necesario para que los clústeres HDInsight 2.1. Cualquier intento de volver a utilizar una metastore Oozie que se ha utilizado con un clúster de HDInsight 3.1 representará el clúster HDInsight 2.1 inútil.

**Oozie metastores no se puede compartir entre clústeres.**

Oozie metastores se adjuntan a clústeres específicos y no se puede compartir entre clústeres.

###<a name="breaking-changes"></a>Cambios importantes

**Prefijo sintaxis**: solo el "wasbs: / /" sintaxis es compatible con 3.0 clústeres y 3.1 HDInsight. La versión más antigua "asv: / /" sintaxis es compatible con HDInsight 2.1 y 1,6 clústeres, pero no se admite en HDInsight 3.1 o 3.0 clústeres. Esto significa que los trabajos enviados a un 3.1 HDInsight o clúster 3.0 explícitamente usar la "asv: / /" se producirá un error de sintaxis. La "wasbs: / /" sintaxis debe usarse en su lugar. Además, las tareas enviadas a cualquier 3.1 HDInsight o 3.0 clústeres que se crean con un metastore existente que contiene las referencias explícitas a recursos mediante la "asv: / /" se producirá un error de sintaxis. Estos metastores necesita volver a crearse con la "wasbs: / /" sintaxis a recursos de dirección.


**Puertos**: han cambiado los puertos utilizados por el servicio de HDInsight. Los números de puerto que se usaban estaban dentro del intervalo de puertos efímeros del sistema operativo Windows. Puertos se asignan automáticamente desde un intervalo predefinido efímero para las comunicaciones de basada en el protocolo de Internet de corta duración. El nuevo conjunto de números de puerto de servicio de plataforma de datos de Hortonworks (HDP) permitidos están fuera de este rango para evitar conflictos que pueden surgir con los puertos usados por los servicios que se ejecutan en el nodo principal. Los nuevos números de puerto no deberían causar ningún cambio importante. Los números que utiliza son las siguientes:

 **HDInsight 1,6 (HDP 1.1)**
<table border="1">
<tr><th>Nombre</th><th>Valor</th></tr>
<tr><td>DFS.http.Address</td><td>namenodehost:30070</td></tr>
<tr><td>DFS.datanode.Address</td><td>0.0.0.0:30010</td></tr>
<tr><td>DFS.datanode.http.Address</td><td>0.0.0.0:30075</td></tr>
<tr><td>DFS.datanode.Ipc.Address</td><td>0.0.0.0:30020</td></tr>
<tr><td>DFS.Secondary.http.Address</td><td>0.0.0.0:30090</td></tr>
<tr><td>mapred.job.Tracker.http.Address</td><td>jobtrackerhost:30030</td></tr>
<tr><td>mapred.Task.Tracker.http.Address</td><td>0.0.0.0:30060</td></tr>
<tr><td>MapReduce.History.Server.http.Address</td><td>0.0.0.0:31111</td></tr>
<tr><td>templeton.Port</td><td>30111</td></tr>
</table><br>

 **HDInsight 3.1 y 3.0 (HDP 2.1 y 2.0)**
<table border="1">
<tr><th>Nombre</th><th>Valor</th></tr>
<tr><td>dirección de DFS.namenode.http</td><td>namenodehost:30070</td></tr>
<tr><td>dirección de DFS.namenode.HTTPS</td><td>headnodehost:30470</td></tr>
<tr><td>DFS.datanode.Address</td><td>0.0.0.0:30010</td></tr>
<tr><td>DFS.datanode.http.Address</td><td>0.0.0.0:30075</td></tr>
<tr><td>DFS.datanode.Ipc.Address</td><td>0.0.0.0:30020</td></tr>
<tr><td>dirección de DFS.namenode.Secondary.http</td><td>0.0.0.0:30090</td></tr>
<tr><td>yarn.NodeManager.WebApp.Address</td><td>0.0.0.0:30060</td></tr>
<tr><td>templeton.Port</td><td>30111</td></tr>
</table><br>

###<a name="dependencies"></a>Dependencias

Se agregaron las siguientes dependencias en HDInsight 3.x (HDP2.x):

* servlet guice
* optiq principales
* javax.Inject
* activación
* jsr305
* geronimo-jaspic_1.0_spec
* Jul-slf4j
* xmlbuilder Java
* ant
* compilador Commons
* api de jdo
* Commons math3
* paranamer
* implementación del JAXB
* stringtemplate
* xom eigenbase
* servlet Jersey
* Commons ejecutivo
* api de JAXB
* servidor de embarcadero todo
* janino
* xercesImpl
* optiq avatica
* JTA
* propiedades de eigenbase
* estupendo todo
* hamcrest principales
* correo
* linq4j
* jpam
* cliente de Jersey
* aopalliance
* geronimo-annotation_1.0_spec
* Selector de ant
* Jersey guice
* API de XML
* api de stax
* ASM commons
* árbol ASM
* wadl
* geronimo-jta_1.1_spec
* guice
* leveldbjni todo
* velocidad de la
* deshacerse
* divertidos java
* embarcadero todo
* Commons dbcp

Las siguientes dependencias ya no existen en HDInsight 3.x (HDP2.x):

* jdeb
* KFS
* sqlline
* hiedra
* aspectjrt
* JSON
* principales
* api de jdo2
* avro mapred
* Realzador datanucleus
* JSP
* api de registro Commons
* Commons matemáticas
* JavaEWAH
* aspectjtools
* javolution
* hdfsproxy
* HBASE
* divertidos

###<a name="version-changes"></a>Cambios de versión

Los siguientes cambios de versión se realizaron entre HDInsight 2.x (HDP1.x) y HDInsight 3.x (HDP2.x):

* métricas principales: ['2.1.2'] -> ['3.0.0']
* derbynet: ['10.4.2.0'] -> ['10.10.1.1']
* datanucleus: ['rdbms-3.0.8'] -> ['rdbms-3.2.9']
* compilador JASPER: ['5.5.12'] -> ['5.5.23']
* Log4J: ['1.2.15', '1.2.16'] -> ['1.2.16', '1.2.17']
* derbyclient: ['10.4.2.0'] -> ['10.10.1.1']
* httpcore: ['4.2.4'] -> ['4.2.5']
* hsqldb: ['1.8.0.10'] -> ['2.0.0']
* jets3t: ['0.6.1'] -> ['0.9.0']
* protobuf java: ['2.4.1'] -> ['2.5.0']
* azules: ['10.4.2.0'] -> ['10.10.1.1']
* JASPER: ['runtime-5.5.12'] -> ['runtime-5.5.23']
* daemon Commons: ['1.0.1'] -> ['1.0.13']
* datanucleus principales: ['3.0.9'] -> ['3.2.10']
* api de datanucleus de jdo: ['3.0.7'] -> ['3.2.6']
* zookeeper: ['3.4.5.1.3.9.0-01320'] -> ['3.4.5.2.1.3.0-1948']
* bonecp: ['0.7.1.RELEASE'] -> ['
* 0.8.0.RELEASE']


### <a name="drivers"></a>Controladores
El controlador de base de datos Connnectivity JDBC (Java) para SQL Server HDInsight utiliza internamente y no se usa para operaciones externas. Si desea conectarse a HDInsight con conectividad abierta de base de datos (ODBC), utilice el controlador ODBC subárbol de Microsoft. Para obtener más información, vea [Conectar Excel a HDInsight con el controlador ODBC subárbol de Microsoft](hdinsight-connect-excel-hive-odbc-driver.md).


### <a name="bug-fixes"></a>Correcciones de errores

Con esta versión, nos hemos actualizado las siguientes versiones de HDInsight con varias correcciones de errores:

* HDInsight 2.1 (HDP 1,3)
* HDInsight 3.0 (HDP 2.0)
* HDInsight 3.1 (HDP 2.1)


## <a name="hortonworks-release-notes"></a>Notas de la versión de Hortonworks

Notas de la versión para las plataformas de datos de Hortonworks (HDPs) HDInsight versión clústeres utilizan están disponibles en las siguientes ubicaciones:

* HDInsight versión 3.1 usa una distribución de Hadoop que se basa en la [Plataforma de datos de Hortonworks 2.1.7][hdp-2-1-7]. Este es el clúster de Hadoop predeterminado creado al usar el portal de Azure después de 7/11/2014. Clústeres HDInsight 3.1 creados antes de 11/7/2014 estaban basados en la [Plataforma de datos de Hortonworks 2.1.1][hdp-2-1-1]

* HDInsight versión 3.0 usa una distribución de Hadoop que se basa en la versión [2.0 de plataforma de datos de Hortonworks][hdp-2-0-8].

* HDInsight versión 2.1 utiliza una distribución de Hadoop que se basa en la [plataforma 1.3 de Hortonworks datos][hdp-1-3-0].

* HDInsight versión 1,6 usa una distribución de Hadoop que se basa en el [Hortonworks datos plataforma 1.1][hdp-1-1-0].

[hdp-2-1-7]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html

[nuget-link]: https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.HDInsight/

[webpi-link]: http://go.microsoft.com/?linkid=9811175&clcid=0x409

[hdinsight-install-spark]: ../hdinsight-hadoop-spark-install/
[hdinsight-r-scripts]: ../hdinsight-hadoop-r-scripts/
 

<properties 
    pageTitle="Problemas de motor de Apache en HDInsight | Microsoft Azure" 
    description="Problemas conocidos de motor de Apache en HDInsight." 
    services="hdinsight" 
    documentationCenter="" 
    authors="mumian" 
    manager="jhubbard" 
    editor="cgronlun"
    tags="azure-portal"/>

<tags 
    ms.service="hdinsight" 
    ms.workload="big-data" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/25/2016" 
    ms.author="nitinme"/>

# <a name="known-issues-for-apache-spark-cluster-on-hdinsight-linux"></a>Problemas conocidos de clúster de motor de Apache en HDInsight Linux

Este documento realiza un seguimiento de todos los problemas conocidos para la vista previa de HDInsight Spark público.  

##<a name="livy-leaks-interactive-session"></a>Livio pérdidas sesión interactiva
 
Cuando se reinicia Livio con una sesión interactiva (desde Ambari o debido a reinicio de la máquina virtual de headnode 0) sigue activa, se perderá una sesión interactiva de tareas. Por este motivo, nuevos trabajos pueden que permanece en el estado aceptado y no se puede iniciar.

**Mitigación:**

Use el siguiente procedimiento para solucionar el problema:

1. SSH en headnode. 
2. Ejecute el comando siguiente para buscar los identificadores de aplicación de los trabajos interactivos iniciados mediante Livio. 

        yarn application –list

    El trabajo predeterminado nombres serán Livio si las tareas se han iniciado con una sesión interactiva Livio sin nombre explícito especificado, inicia sesión de para Livio el Bloc de notas de Jupyter, el nombre del trabajo se iniciará con remotesparkmagics_ *. 

3. Ejecute el comando siguiente para eliminar los trabajos. 

        yarn application –kill <Application ID>

Tareas nuevas se iniciará ejecutando. 

##<a name="spark-history-server-not-started"></a>No se inició el servidor de historial de motor 

Motor historial Server no se inicia automáticamente después de crea un clúster.  

**Mitigación:** 

Inicie manualmente el servidor de historial desde Ambari.

## <a name="permission-issue-in-spark-log-directory"></a>Problema de permisos en el directorio de registro del motor 

Cuando se hdiuser envía un trabajo con spark-submit, hay un error java.io.FileNotFoundException: /var/log/spark/sparkdriver_hdiuser.log (permiso denegado) y el registro del controlador no se ha escrito. 

**Mitigación:**
 
1. Agregar hdiuser al grupo Hadoop. 
2. Proporcionar 777 permisos en /var/log/spark después de la creación de clúster. 
3. Actualizar la ubicación del registro motor utilizando Ambari para que sea un directorio con 777 permisos.  
4. Ejecutar motor-submit como sudo.  

## <a name="issues-related-to-jupyter-notebooks"></a>Problemas relacionados con los blocs de notas Jupyter

Siguientes son algunos problemas conocidos relacionados con los blocs de notas de Jupyter.


### <a name="notebooks-with-non-ascii-characters-in-filenames"></a>Blocs de notas con caracteres no ASCII en los nombres de archivo

Los blocs de notas de Jupyter pueden usarse en clústeres motor HDInsight no deben tener caracteres no ASCII en los nombres de archivo. Si intenta cargar un archivo a través de la UI Jupyter que tiene un nombre de archivo no ASCII, se producirá un silenciosamente (es decir, Jupyter no le permiten cargar el archivo, pero no iniciará un error visible bien). 

### <a name="error-while-loading-notebooks-of-larger-sizes"></a>Error al cargar los blocs de notas de mayor tamaño

Es posible que vea un error **`Error loading notebook`** cuando se cargan blocs de notas que tienen un mayores tamaño.  

**Mitigación:**

Si recibe este error, no significa que los datos están dañado o perdido.  Los blocs de notas están todavía en disco en `/var/lib/jupyter`, y puede SSH en el clúster para acceder a ellos. Puede copiar los blocs de notas desde el clúster en el equipo local (mediante SCP o WinSCP) como una copia de seguridad para evitar la pérdida de datos importantes en el Bloc de notas. A continuación, puede túnel SSH en su headnode puerto 8001 para tener acceso a Jupyter sin pasar por la puerta de enlace.  Desde allí, puede borrar el resultado del Bloc de notas y vuelva a guardarla para reducir el tamaño del Bloc de notas.

Para evitar que ocurra en el futuro este error, debe seguir algunos procedimientos recomendados:

* Es importante mantener reducido el tamaño del Bloc de notas. Los resultados de los trabajos de motor que se envían a Jupyter se conservan en el Bloc de notas.  Es un procedimiento recomendado con Jupyter en general para evitar que se `.collect()` en RDD grandes o dataframes; en su lugar, si desea inspeccionar de contenido de la RDD, considere la posibilidad de ejecutar `.take()` o `.sample()` para que el resultado no se transmite demasiado grande.
* Además, cuando se guarda un bloc de notas, desactive todos los de salida celdas para reducir el tamaño.

### <a name="notebook-initial-startup-takes-longer-than-expected"></a>Inicio del Bloc de notas se tarda más de lo esperado 

Primera instrucción de código en Bloc de notas de Jupyter con mágico motor podría tardar más de un minuto.  

**Explicación:**
 
Esto ocurre porque cuando se ejecuta la primera celda de código. En segundo plano Esto inicia la configuración de la sesión y motor, SQL y, a continuación, se establecen los contextos de sección. Después de que se establecen estos contextos, se ejecuta la primera instrucción y esto le da la impresión de la instrucción tardó mucho tiempo en completarse.

### <a name="jupyter-notebook-timeout-in-creating-the-session"></a>Tiempo de espera de Jupyter Bloc de notas en la creación de la sesión

Cuando clúster motor carece de recursos, el kernel motor y Pyspark en el Bloc de notas de Jupyter tendrá tiempo de espera intentando crear la sesión. 

**Su eliminación:** 

1. Libere recursos en el clúster de motor por:

    - Detener otros blocs de notas de motor en el menú Cerrar y detener o haciendo clic en Cerrar en el Explorador de Bloc de notas.
    - Detener otras aplicaciones de motor de hilo.

2. Reinicie el Bloc de notas que intenta iniciar. Suficiente recursos deben estar disponibles para crear una sesión ahora.

##<a name="see-also"></a>Vea también

* [Información general: Apache motor en HDInsight de Azure](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Escenarios

* [Motor con BI: realizar análisis de datos interactivas con motor en HDInsight con herramientas de inteligencia empresarial](hdinsight-apache-spark-use-bi-tools.md)

* [Motor con Machine aprendizaje: motor de uso en HDInsight para analizar usando HVAC datos de temperatura de creación](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Motor con Machine aprendizaje: motor de uso en HDInsight de predecir los resultados de la inspección de comida](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Motor de flujo de datos: Motor de uso en HDInsight para la creación de aplicaciones de transmisión en tiempo real](hdinsight-apache-spark-eventhub-streaming.md)

* [Análisis de registro de sitio Web con el motor en HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Crear y ejecutar aplicaciones

* [Crear una aplicación independiente de Scala](hdinsight-apache-spark-create-standalone-application.md)

* [Ejecutar trabajos de forma remota en un clúster de motor mediante Livio](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Herramientas y extensiones

* [Usar el complemento de herramientas de HDInsight para IntelliJ IDEA para crear y enviar el motor Scala aplicaciones](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Usar el complemento de herramientas de HDInsight para IntelliJ IDEA depurar aplicaciones del motor de forma remota](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

* [Utilizar blocs de notas de globo dirigible con un clúster de motor en HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Kernels disponibles para el Bloc de notas de Jupyter en clúster de motor de HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Usar paquetes externos con blocs de notas Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Instalar Jupyter en su equipo y conectarse a un clúster de HDInsight Spark](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Administrar recursos

* [Administrar recursos para el clúster de motor de Apache en HDInsight de Azure](hdinsight-apache-spark-resource-manager.md)

* [Realizar el seguimiento y depuración trabajos que se ejecutan en un clúster de motor de Apache en HDInsight](hdinsight-apache-spark-job-debugging.md)

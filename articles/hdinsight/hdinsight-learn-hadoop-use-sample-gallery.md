<properties
   pageTitle="Obtener Hadoop en HDInsight con la Galería de ejemplo | Microsoft Azure"
   description="Aprender más rápido Hadoop al ejecutar aplicaciones de ejemplo de la Galería de introducción de introducción de HDInsight. Usar datos de ejemplo o proporcionar su propio."
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
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

# <a name="learn-hadoop-by-using-the-azure-hdinsight-getting-started-gallery"></a>Obtenga información sobre Hadoop mediante la Galería de introducción de introducción de HDInsight de Azure

La Galería de introducción de introducción sólo está disponible para clústeres HDInsight basado en Windows. La Galería proporciona una sencilla y de forma rápida de obtener Hadoop ejecutando aplicaciones de ejemplo de HDInsight. Algunos de los ejemplos vienen con datos de ejemplo. Puede proporcionar sus propios datos para las muestras restantes. Hay seis ejemplos siguientes (con más próximo):

- Soluciones con los datos de Azure
    - Análisis de registro del sitio Web de Microsoft Azure
    - Análisis de almacenamiento de Microsoft Azure
- Soluciones con datos de ejemplo
    - Análisis de datos de sensor
    - Análisis de tendencias de Twitter
    - Análisis de registro de sitio Web
    - Recomendación de película Mahout

![Soluciones de HDInsight Hadoop, tormenta y Galería de introducción de introducción de HBase como datos de ejemplo.][hdinsight.sample.gallery]

El siguiente vídeo muestra cómo ejecutar el ejemplo de análisis de tendencias de Twitter:

<center><a href="https://www.youtube.com/embed/7ePbHot1SN4">https://www.YouTube.com/EMBED/7ePbHot1SN4></a></center>

El panel se puede acceder a través de http://<YourHDInsightClusterName>.azurehdinsight.net/ o desde el portal de Azure.

**Para ejecutar un ejemplo de la Galería de introducción de introducción**

1. Inicie sesión en el [portal de Azure][azure.portal].
2. Haga clic en **Examinar** desde el menú de la izquierda, haga clic en **HDInsight clústeres**y, a continuación, haga clic en el nombre del clúster.
3. En el menú superior, haga clic en **panel** .
4. Escriba el nombre de usuario y contraseña para el usuario HTTP (también llamado el usuario de clúster).
6. Haga clic en **Galería de introducción de introducción** en la parte superior de la página.
7. Haga clic en uno de los ejemplos. Cada ejemplo proporciona los pasos detallados para ejecutarlo. La imagen siguiente muestra el ejemplo de análisis de tendencias de Twitter:

    ![Ejemplo de análisis de tendencias de HDInsight Twitter][hdinsight.twitter.sample]

## <a name="next-steps"></a>Pasos siguientes
Otras maneras de obtener información sobre HDInsight incluyen:

- [Mapa de aprendizaje de HDInsight][hdinsight.learn.map]
- [HDInsight infographic][hdinsight.infographic]

<!--Image references-->
[hdinsight.sample.gallery]: ./media/hdinsight-learn-hadoop-use-sample-gallery/HDInsight-Getting-Started-Gallery.png
[hdinsight.twitter.sample]: ./media/hdinsight-learn-hadoop-use-sample-gallery/HDInsight-Twitter-Trend-Analysis-sample.png

<!--Link references-->
[hdinsight.learn.map]: https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/
[hdinsight.infographic]: http://go.microsoft.com/fwlink/?linkid=523960
[azure.portal]:https://portal.azure.com

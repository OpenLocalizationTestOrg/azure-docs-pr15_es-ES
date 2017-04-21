<properties 
    pageTitle="Usar subárbol con Hadoop para análisis de registro de sitio Web | Microsoft Azure" 
    description="Obtenga información sobre cómo usar subárbol con HDInsight para analizar los registros de sitio Web. Deberá usar un archivo de registro como entrada en una tabla HDInsight y use HiveQL para consultar los datos." 
    services="hdinsight" 
    documentationCenter="" 
    authors="nitinme" 
    manager="jhubbard" 
    editor="cgronlun"
    tags="azure-portal"/>

<tags 
    ms.service="hdinsight" 
    ms.workload="big-data" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/17/2016" 
    ms.author="nitinme"/>

# <a name="use-hive-with-hdinsight-to-analyze-logs-from-websites"></a>Usar subárbol con HDInsight para analizar los registros de los sitios Web

Obtenga información sobre cómo usar HiveQL con HDInsight para analizar los registros de un sitio Web. Análisis de registro del sitio Web puede utilizarse para segmentar su audiencia basándose en actividades similares, clasificar los visitantes del sitio por segmentos y buscar contenido ver, proceden de sitios Web y así sucesivamente.

En este ejemplo, usará un clúster HDInsight para analizar los archivos de registro de sitio Web para obtener información sobre la frecuencia de visitas al sitio Web de sitios Web externos en un día. También deberá generar un resumen de los errores de sitio Web que la experiencia de los usuarios. Obtendrá información sobre cómo:

- Conectarse a un almacenamiento de blobs de Windows Azure, que contiene los archivos de registro de sitio Web.
- Crear tablas de SUBÁRBOL para los registros de la consulta.
- Crear consultas de SUBÁRBOL para analizar los datos.
- Utilizar Microsoft Excel para conectarse a HDInsight (con conectividad abierta de base de datos (ODBC) para recuperar los datos analizados.

![HDI. Samples.Website.Log.Analysis][img-hdi-weblogs-sample]

##<a name="prerequisites"></a>Requisitos previos

- Debe haya proporcionado un clúster de Hadoop en HDInsight de Azure. Para obtener instrucciones, consulte [Clústeres de HDInsight de aprovisionamiento][hdinsight-provision]. 
- Debe tener Microsoft Excel 2013 o Excel 2010 instalado.
- Debe tener el [Controlador ODBC subárbol de Microsoft](http://www.microsoft.com/download/details.aspx?id=40886) para importar datos desde subárbol en Excel.


##<a name="to-run-the-sample"></a>Para ejecutar el ejemplo

1. Desde el [Portal de Azure](https://portal.azure.com/), de Startboard (si anclados clúster hay), haga clic en el mosaico de clúster en el que desea ejecutar el ejemplo.

2. Desde el módulo de clúster en **Vínculos rápidos**, haga clic en **Panel de clúster**y, a continuación, en el módulo de **Clúster paneles** , haga clic en **Panel de clúster HDInsight**. Como alternativa, puede abrir el panel directamente utilizando la siguiente URL:

        https://<clustername>.azurehdinsight.net
    
    Cuando se le solicite, autenticar mediante el nombre de usuario administrador y la contraseña que utilizó cuando el clúster de aprovisionamiento.
  
2. Desde la página web que se abre, haga clic en la pestaña de la **Galería de introducción de introducción** y, a continuación, en la categoría **soluciones con datos de ejemplo** , haga clic en la muestra de **Análisis de registro del sitio Web** .

3. Siga las instrucciones proporcionadas en la página web para finalizar la muestra.

##<a name="next-steps"></a>Pasos siguientes
Pruebe el siguiente ejemplo: [analizar datos de sensor mediante subárbol con HDInsight](hdinsight-hive-analyze-sensor-data.md).


[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-sensor-data-sample]: ../hdinsight-use-hive-sensor-data-analysis.md

[img-hdi-weblogs-sample]: ./media/hdinsight-hive-analyze-website-log/hdinsight-weblogs-sample.png
 

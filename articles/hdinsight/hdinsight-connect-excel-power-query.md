<properties
    pageTitle="Conectar Excel a Hadoop con Power Query | Microsoft Azure"
    description="Aprenda a aprovechar las ventajas de los componentes de inteligencia empresarial y usar Power Query para Excel para obtener acceso a los datos almacenados en Hadoop en HDInsight."
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
    ms.date="10/19/2016"
    ms.author="jgao"/>


#<a name="connect-excel-to-hadoop-by-using-power-query"></a>Conectar Excel a Hadoop mediante Power Query

Una característica clave de la solución de grande de datos de Microsoft es la integración de componentes de business intelligence (BI) de Microsoft con clústeres de Hadoop en HDInsight de Azure. Un ejemplo principal de esta integración es la capacidad de conectar Excel a la cuenta de almacenamiento de Azure que contiene los datos asociados con el clúster Hadoop usando Microsoft Power Query para el complemento de Excel. Este artículo describe cómo configurar y usar Power Query para consultar los datos asociados a un clúster de Hadoop administrado con HDInsight.

> [AZURE.NOTE] Aunque los pasos de este artículo se pueden utilizar con clúster Linux o de HDInsight de Windows, Windows se requiere para la estación de trabajo de cliente.

### <a name="prerequisites"></a>Requisitos previos

Antes de comenzar este artículo, debe tener el siguiente:

- **HDInsight un clúster**. Para configurar uno, consulte [Introducción a Azure HDInsight][hdinsight-get-started].
- **Estación de trabajo A** que se está ejecutando Windows 7, Windows Server 2008 R2 o un sistema operativo posterior.
- **Office 2013 Professional Plus, Office 365 ProPlus, Excel 2013 independiente o Office 2010 Professional Plus**.


## <a name="install-power-query"></a>Instalar Power Query

Power Query puede usarse para importar datos desde una variedad de orígenes en Microsoft Excel, donde de power herramientas de inteligencia empresarial como PowerPivot y Power View. En particular, Power Query puede importar datos que se han de salida o que se ha generado un trabajo Hadoop que se ejecuta en un clúster de HDInsight.

Descargar Microsoft Power Query para Excel desde el [Centro de descarga de Microsoft] [ powerquery-download] e instálelo.

## <a name="import-hdinsight-data-into-excel"></a>Importar datos de HDInsight en Excel

El complemento de Power Query para Excel facilita importar datos desde el clúster de HDInsight en Excel, en herramientas de inteligencia empresarial como PowerPivot y Power Map que pueden utilizarse para inspeccionar, analizar y presentan los datos.

**Importar datos desde un clúster de HDInsight**

1. Abra Excel.

2. Crear un nuevo libro en blanco.

3. Haga clic en el menú de **Power Query** , haga clic en **De Azure**y, a continuación, haga clic en **Desde Microsoft Azure HDInsight**.

    ![HDI. PowerQuery.SelectHdiSource][image-hdi-powerquery-hdi-source]

    **Nota:** Si no ve el menú de **Power Query** , vaya al **archivo** > **Opciones** > **Complementos**y seleccione **complementos COM** desde el cuadro de lista desplegable **Administrar** en la parte inferior de la página. Seleccione el botón **Ir...** y compruebe que la casilla de la consulta de Power para el complemento de Excel se revisó.

    **Nota:** Power Query permite importar datos desde HDFS haciendo clic en **Desde otros orígenes**.

3. **Nombre de la cuenta**, escriba el nombre de la cuenta de almacenamiento de blobs de Windows Azure asociado con el clúster y, a continuación, haga clic en **Aceptar**. Esto puede ser la [cuenta de almacenamiento predeterminada](hdinsight-administer-use-management-portal.md#find-the-default-storage-account) o una cuenta de almacenamiento vinculado.  El formato es *https://<StorageAccountName>.blob.core.windows.net/*.

4. **Clave de cuenta**, escriba la clave para la cuenta de almacenamiento de blobs de Windows y, a continuación, haga clic en **Guardar**. (Debe hacer esto solo la primera vez que se tiene acceso a este almacén).

5. En el panel de **navegación** a la izquierda del Editor de consultas, haga doble clic en el nombre del contenedor de almacenamiento de blobs de Windows. De forma predeterminada, el nombre del contenedor es el mismo nombre que el nombre del clúster.

6. Busque **(hivesampledata.txt)** en la columna **nombre** (la ruta de acceso de la carpeta está **... subárbol y almacén/hivesampletable/**) y, a continuación, haga clic en **binario** situada a la izquierda (hivesampledata.txt). (Hivesampledata.txt) se suministra con todo el clúster. Si lo desea, puede usar su propio archivo.

    ![HDI. PowerQuery.ImportData][image-hdi-powerquery-importdata]

7. Si lo desea, puede cambiar los nombres de columna. Cuando esté listo, haga clic en **Cerrar y cargar**.  Se ha cargado los datos con el libro:

    ![HDI. PowerQuery.ImportedTable][image-hdi-powerquery-imported-table]

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido cómo usar Power Query para recuperar datos desde HDInsight en Excel. Asimismo, puede recuperar datos desde HDInsight en la base de datos de SQL Azure. También es posible cargar los datos en HDInsight. Para obtener más información, consulte los artículos siguientes:

* [Conectar Excel a HDInsight con el controlador ODBC de Microsoft subárbol][hdinsight-ODBC]
* [Cargar datos a HDInsight][hdinsight-upload-data]

[hdinsight-ODBC]: hdinsight-connect-excel-hive-odbc-driver.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[image-hdi-powerquery-hdi-source]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.SelectHdiSource.png
[image-hdi-powerquery-importdata]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportData.png
[image-hdi-powerquery-imported-table]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportedTable.PNG

[powerquery-download]: http://go.microsoft.com/fwlink/?LinkID=286689

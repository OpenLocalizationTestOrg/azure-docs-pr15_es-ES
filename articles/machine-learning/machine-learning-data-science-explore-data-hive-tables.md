<properties
    pageTitle="Explorar datos en tablas de la sección con las consultas de la sección | Microsoft Azure"
    description="Explorar datos en tablas de la sección uso de consultas de la sección."
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun"  />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="bradsev" />

# <a name="explore-data-in-hive-tables-with-hive-queries"></a>Explorar datos en tablas de la sección con las consultas de la sección

Este documento proporciona las secuencias de comandos de sección de ejemplo que se utilizan para explorar datos en tablas de sección en un clúster de HDInsight Hadoop.

Los siguientes vínculos de **menú** a temas que describen cómo usar herramientas para explorar los datos desde diversos entornos de almacenamiento.

[AZURE.INCLUDE [cap-explore-data-selector](../../includes/cap-explore-data-selector.md)]

## <a name="prerequisites"></a>Requisitos previos
En este artículo se supone que tiene:

* Crear una cuenta de almacenamiento de Azure. Si necesita instrucciones, vea [crear una cuenta de almacenamiento de Azure](../storage/storage-create-storage-account.md#create-a-storage-account)
* Aprovisiona un clúster de Hadoop personalizado con el servicio de HDInsight. Si necesita instrucciones, vea [Personalizar Azure HDInsight Hadoop clústeres para análisis avanzado](machine-learning-data-science-customize-hadoop-cluster.md).
* Se ha cargado los datos a la tabla subárbol en Azure HDInsight Hadoop clústeres. Si no es así, siga las instrucciones en [crear y cargar datos en tablas de subárbol](machine-learning-data-science-move-hive-tables.md) para cargar datos en tablas de subárbol en primer lugar.
* Habilitar el acceso remoto al clúster. Si necesita instrucciones, consulte [acceso a la cabeza de Hadoop clúster de nodo](machine-learning-data-science-customize-hadoop-cluster.md#headnode).
* Si necesita instrucciones sobre cómo enviar las consultas de la sección, consulte [cómo enviar consultas subárbol](machine-learning-data-science-move-hive-tables.md#submit)

## <a name="example-hive-query-scripts-for-data-exploration"></a>Sección consulta secuencias de comandos para la exploración de datos

1. Obtener el recuento de observaciones por partición `SELECT <partitionfieldname>, count(*) from <databasename>.<tablename> group by <partitionfieldname>;`

2. Obtener el recuento de observaciones por día `SELECT to_date(<date_columnname>), count(*) from <databasename>.<tablename> group by to_date(<date_columnname>);`

3. Obtenga los niveles en una columna de categorías  
    `SELECT  distinct <column_name> from <databasename>.<tablename>`

4. Obtener el número de niveles de combinación de dos columnas por categorías `SELECT <column_a>, <column_b>, count(*) from <databasename>.<tablename> group by <column_a>, <column_b>`

5. Obtener la distribución de columnas numéricas  
    `SELECT <column_name>, count(*) from <databasename>.<tablename> group by <column_name>`

6. Extraer registros de combinar dos tablas

        SELECT
            a.<common_columnname1> as <new_name1>,
            a.<common_columnname2> as <new_name2>,
            a.<a_column_name1> as <new_name3>,
            a.<a_column_name2> as <new_name4>,
            b.<b_column_name1> as <new_name5>,
            b.<b_column_name2> as <new_name6>
        FROM
            (
            SELECT <common_columnname1>,
                <common_columnname2>,
                <a_column_name1>,
                <a_column_name2>,
            FROM <databasename>.<tablename1>
            ) a
            join
            (
            SELECT <common_columnname1>,
                <common_columnname2>,
                <b_column_name1>,
                <b_column_name2>,
            FROM <databasename>.<tablename2>
            ) b
            ON a.<common_columnname1>=b.<common_columnname1> and a.<common_columnname2>=b.<common_columnname2>

## <a name="additional-query-scripts-for-taxi-trip-data-scenarios"></a>Secuencias de comandos de consulta adicionales para escenarios de datos de viajes de taxi

También se proporcionan ejemplos de consultas que son específicos de escenarios de [Nueva York Taxi viaje de datos](http://chriswhong.com/open-data/foil_nyc_taxi/) en [el repositorio de Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Estas consultas ya dispone de esquema de datos especificado y están listos para enviar a ejecutar.

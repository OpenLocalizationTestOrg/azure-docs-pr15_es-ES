<properties
    pageTitle="Ejemplo de datos en tablas de Azure HDInsight subárbol | Microsoft Azure"
    description="Hacia abajo muestreo de datos en tablas de subárbol HDInsight de Azure (Hadopop)"
    services="machine-learning,hdinsight"
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
    ms.date="09/19/2016"
    ms.author="hangzh;bradsev" />

# <a name="sample-data-in-azure-hdinsight-hive-tables"></a>Datos de ejemplo en tablas de la sección de HDInsight de Azure

En este artículo se describe cómo los datos almacenados en tablas Azure HDInsight subárbol mediante consultas de la sección de ejemplo de abajo. Trataremos tres métodos de muestreo usados normalmente:

* Muestreo aleatorio uniforme
* Sondeo por grupos
* Muestreo stratified

**¿Por qué los datos de ejemplo?**
Si el conjunto de datos que se va a analizar es grande, suele ser una buena idea abajo-muestra los datos para reducir a un tamaño menor pero representante y más manejable. Esto facilita la comprensión de datos, de exploración y de ingeniería de la característica. Su rol en el proceso de ciencias de datos de equipo es habilitar la creación de prototipos rápida de las funciones de procesamiento de datos y modelos de aprendizaje.

El **menú** debajo de vínculos a temas que describen cómo datos desde diversos entornos de almacenamiento de ejemplo.

[AZURE.INCLUDE [cap-sample-data-selector](../../includes/cap-sample-data-selector.md)]

Esta tarea de muestreo es un paso en el [Proceso de ciencias de datos de equipo (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).


## <a name="how-to-submit-hive-queries"></a>Cómo enviar las consultas de la sección
Se pueden enviar las consultas de la sección desde la consola de línea de comandos de Hadoop en el nodo principal del clúster Hadoop. Para ello, inicie sesión en el nodo principal del clúster Hadoop, abra la consola de línea de comandos de Hadoop y enviar las consultas de la sección desde allí. Para obtener instrucciones sobre el envío de consultas de la sección en la consola de línea de comandos de Hadoop, consulte [cómo enviar consultas de la sección](machine-learning-data-science-move-hive-tables.md#submit).

## <a name="uniform"></a>Muestreo aleatorio uniforme
Muestreo aleatorio uniforme significa que cada fila del conjunto de datos tiene una oportunidad de muestreo igual. Se puede implementar agregando una RAND campo adicional al conjunto de datos en la consulta interna "seleccionar" y, en la consulta externa "seleccionar" condición en ese campo aleatorio.

Esto es un ejemplo de consulta:

    SET sampleRate=<sample rate, 0-1>;
    select
        field1, field2, …, fieldN
    from
        (
        select
            field1, field2, …, fieldN, rand() as samplekey
        from <hive table name>
        )a
    where samplekey<='${hiveconf:sampleRate}'

Aquí, `<sample rate, 0-1>` especifica la proporción de registros que desean que los usuarios de ejemplo.

## <a name="group"></a>Sondeo por grupos

Cuando muestrear datos por categorías, es aconsejable incluir o excluir todas las instancias de algún valor concreto de una variable por categorías. Esto es lo que se entiende por "muestras por grupo".
Por ejemplo, si tiene una variable por categorías "Estado", que tiene valores NY, MA, entidad emisora de certificados, NJ, PA, etcetera, que quiere registros del mismo estado siempre juntas, si la se muestrean o no.

Aquí es un ejemplo de consulta que muestras por grupo:

    SET sampleRate=<sample rate, 0-1>;
    select
        b.field1, b.field2, …, b.catfield, …, b.fieldN
    from
        (
        select
            field1, field2, …, catfield, …, fieldN
        from <table name>
        )b
    join
        (
        select
            catfield
        from
            (
            select
                catfield, rand() as samplekey
            from <table name>
            group by catfield
            )a
        where samplekey<='${hiveconf:sampleRate}'
        )c
    on b.catfield=c.catfield

## <a name="stratified"></a>Muestreo stratified

Sondeo es stratified con respecto a una variable por categorías cuando tienen los ejemplos que se obtienen los valores que por categoría están en la misma relación como en la población primario desde el que se obtuvieron los ejemplos. Con el mismo ejemplo como anterior, supongamos que los datos tienen subcarpetas población Estados, por ejemplo NJ tiene 100 observaciones, NY tiene 60 observaciones y WA tiene 300 observaciones. Si especifica la tasa de muestreo stratified ser 0,5, a continuación, en el ejemplo que se obtienen debería tener aproximadamente 50, 30 y 150 observaciones de NJ, Nueva York y WA respectivamente.

Esto es un ejemplo de consulta:

    SET sampleRate=<sample rate, 0-1>;
    select
        field1, field2, field3, ..., fieldN, state
    from
        (
        select
            field1, field2, field3, ..., fieldN, state,
            count(*) over (partition by state) as state_cnt,
            rank() over (partition by state order by rand()) as state_rank
        from <table name>
        ) a
    where state_rank <= state_cnt*'${hiveconf:sampleRate}'


Para obtener información sobre los métodos de muestreo más avanzados que están disponibles en la sección, vea [LanguageManual muestreo](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Sampling).

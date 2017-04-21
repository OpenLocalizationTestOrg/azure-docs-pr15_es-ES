<properties 
    pageTitle="Introducción al generador de datos, un servicio de integración de datos | Microsoft Azure" 
    description="Obtenga información sobre qué es el generador de datos de Azure: un servicio de integración de datos de nube que organiza y automatiza movimiento y transformación de datos." 
    keywords="integración de datos, integración de datos de la nube, ¿qué es el generador de datos de azure"
    services="data-factory" 
    documentationCenter="" 
    authors="sharonlo101" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="09/22/2016" 
    ms.author="shlo"/>

# <a name="introduction-to-azure-data-factory-service-a-data-integration-service-in-the-cloud"></a>Introducción al servicio de generador de datos de Azure, un servicio de integración de datos en la nube

## <a name="what-is-azure-data-factory"></a>¿Qué es el generador de datos de Azure? 
Generador de datos es un servicio de integración de datos basados en la nube que organiza y automatiza el **movimiento** y la **transformación** de datos. Puede crear soluciones de integración de datos mediante el servicio de generador de datos que pueden recopilar datos de varios almacena datos, proceso o transformar los datos y publicar los datos de resultado en el almacena datos. 

Servicio de generador de datos le permite crear canalizaciones de datos que se mueven y transforman datos y luego ejecute el canalizaciones con una programación especificada (cada hora, diariamente, semanalmente, etcetera). También proporciona eficaces visualizaciones para mostrar el linaje y las dependencias entre las canalizaciones de datos y supervisar su de canalizaciones de datos desde una única vista unificada identificar fácilmente problemas y configurar las alertas de supervisión.

![Diagrama: Información general del generador de datos, un servicio de integración de datos](./media/data-factory-introduction/what-is-azure-data-factory.png)
**ilustración 1.** Recopilar datos de varios orígenes de datos, preparar, transformar y analizar los datos y, a continuación, publicar datos listos para usar para su consumo.

## <a name="pipelines-and-activities"></a>Las actividades y canalizaciones
En una solución generador de datos, crear uno o varios datos **canalizaciones**. Una canalización es una agrupación lógica de actividades. Se utilizan para actividades de grupo en una unidad que juntos realizar una tarea. 

**Actividades** definir las acciones que puede realizar en los datos. Por ejemplo, puede usar una actividad de copiar para copiar los datos del almacén de datos de uno a otro almacén de datos. Asimismo, puede usar una actividad de sección, que se ejecuta una consulta de sección en un clúster de Azure HDInsight transformar o analizar los datos. Generador de datos es compatible con dos tipos de actividades: las actividades de movimiento de datos y las actividades de transformación de datos. 
  
## <a name="data-movement-activities"></a>Actividades de movimiento de datos 
[AZURE.INCLUDE [data-factory-supported-data-stores](../../includes/data-factory-supported-data-stores.md)]

Consulte el artículo de [Actividades de movimiento de datos](data-factory-data-movement-activities.md) para obtener más detalles. 

## <a name="data-transformation-activities"></a>Actividades de transformación de datos
[AZURE.INCLUDE [data-factory-transformation-activities](../../includes/data-factory-transformation-activities.md)]

Consulte el artículo de [Actividades de transformación de datos](data-factory-data-transformation-activities.md) para obtener más detalles.

Si necesita mover datos de una datos almacenan que copiar actividad no admiten, o transformar datos mediante su propia lógica, crear una **actividad de .NET personalizada**. Para obtener más información sobre cómo crear y usar una actividad personalizada, consulte [usar las actividades personalizadas de una canalización de generador de datos de Azure](data-factory-use-custom-activities.md).

## <a name="linked-services"></a>Servicios vinculados
Servicios vinculados definen la información necesaria para que el generador de datos para conectarse a los recursos externos (ejemplos: el almacenamiento de Azure, SQL Server, HDInsight de Azure local). Servicios vinculados se usan dos fines en el generador de datos:

- Para representar un **almacén de datos** incluyendo, pero no se limita a, un servidor local SQL Server, base de datos de Oracle, archivo compartido o una cuenta de almacenamiento de blobs de Windows Azure. Consulte la sección de [actividades de movimiento de datos](data-factory-data-movement-activities.md) para obtener una lista de comercios de datos compatibles. 
- Para representar una **calcular recursos** que puede hospedar la ejecución de una actividad. Por ejemplo, la actividad de HDInsightHive se ejecuta en un clúster de HDInsight Hadoop. Vea la sección de [las actividades de transformación de datos](data-factory-data-transformation-activities.md) para obtener una lista de los entornos de cálculo compatibles. 

## <a name="datasets"></a>Conjuntos de datos 
Vincular datos almacena de servicios vinculados a un generador de datos de Azure. Conjuntos de datos representan estructuras de datos con en la almacena datos. Por ejemplo, un servicio de almacenamiento de Azure vinculado proporciona información de conexión de datos de fábrica para conectarse a una cuenta de almacenamiento de Azure. Un conjunto de datos de blobs de Windows Azure especifica el contenedor de blobs de Windows y la carpeta en el almacenamiento de blobs de Windows Azure desde la que la canalización debe leer los datos. Del mismo modo, un servicio de SQL Azure vinculado proporciona información de conexión para una base de datos de SQL Azure y un conjunto de datos de SQL Azure especifica la tabla que contiene los datos.   

## <a name="relationship-between-data-factory-entities"></a>Relación entre entidades de generador de datos
Generador de datos tiene unas entidades claves a las que trabajan conjuntamente para definir entrada y salida de datos, procesamiento de eventos y la programación y los recursos necesarios para ejecutar el flujo de datos que desee.

![Diagrama: Generador de datos, un servicio de integración de datos de nube - conceptos clave](./media/data-factory-introduction/data-integration-service-key-concepts.png)
**figura 2.** Relaciones entre el servicio de conjunto de datos, actividad, canalización y vinculado

Con los cuatro conceptos simples de servicios vinculados, los conjuntos de datos, las actividades y canalizaciones, está listo para empezar. Puede [crear su primer canalización](data-factory-build-your-first-pipeline.md). 

## <a name="supported-regions"></a>Regiones admitidas
Actualmente, puede crear generadores de datos en las regiones **Oeste de Estados Unidos**, **Oriental de Estados Unidos**y **Europa del Norte** . Sin embargo, un generador de datos puede tener acceso a almacena datos y calcular los servicios en las demás regiones Azure para mover datos entre almacena datos o datos de proceso con servicios de proceso. 

Azure generador de datos propia no almacenar los datos. Le permite crear flujos de controlados por datos para coordinar el movimiento de datos entre [compatibles almacena datos](data-factory-data-movement-activities.md#supported-data-stores) y tratamiento de datos con [Servicios de proceso](data-factory-compute-linked-services.md) de otras regiones o en un entorno local. También permite para [supervisar y administrar flujos de trabajo](data-factory-monitor-manage-pipelines.md) con ambos programación y mecanismos de interfaz de usuario. 

Aunque el generador de datos de Azure está disponible en solo **Oeste de Estados Unidos**, **US oriental**y regiones de **Europa del Norte** , el servicio alimentar el movimiento de datos en el generador de datos está disponible [globalmente](data-factory-data-movement-activities.md#global) en varias regiones. En caso de un almacén de datos encuentra detrás de un firewall, a continuación, una [Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md) instalado en su entorno local mueve los datos en su lugar. 

Por ejemplo, supongamos que se están ejecutando los entornos de cálculo como clúster de HDInsight de Azure y Azure aprendizaje fuera de la región de Europa occidental. Puede crear y utilizar una instancia de generador de datos de Azure en Europa del Norte y usarlo para programar trabajos en los entornos de cálculo en Europa occidental. Se necesitan unos cuantos milisegundos para el generador de datos desencadenar la tarea en su entorno de cálculo pero no cambia el tiempo de ejecución del trabajo en su entorno.

Se pretende tiene el generador de datos de Azure en todos los lugares compatible con Azure en el futuro.
  
## <a name="next-steps"></a>Pasos siguientes
Para obtener información sobre cómo crear generadores de datos con canalizaciones de datos, siga las instrucciones paso a paso en los siguientes tutoriales. 

Tutorial | Descripción
-------- | -----------
[Crear una canalización de datos que procesa datos con clúster Hadoop](data-factory-build-your-first-pipeline.md) | En este tutorial, que crear su primer generador de datos de Azure con una canalización de datos que **procesa datos** ejecutando el script de sección en un clúster de Azure HDInsight (Hadoop). |
[Crear una canalización de datos para mover datos entre dos almacena datos de nube](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) | En este tutorial, se crea un generador de datos con una canalización que **mueve los datos** desde el almacenamiento de blobs a base de datos SQL.
[Crear una canalización de datos para mover datos entre un almacén de datos local y un almacén de datos de nube mediante Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md) | En este tutorial, cree un generador de datos con una canalización que **mueve los datos** desde una base de datos de SQL Server **local** a un blobs de Windows Azure. Como parte del tutorial, instalar y configurar Data Management Gateway en su equipo. 

<properties
    pageTitle="Resultado JSON para el análisis de secuencia | Microsoft Azure"
    description="Obtenga información sobre cómo el análisis de secuencia pueden destinar Azure DocumentDB para la salida JSON, para archivar datos y baja latencia consultas de datos JSON no estructurados."
    keywords="Resultado JSON"
    documentationCenter=""
    services="stream-analytics,documentdb"
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>

# <a name="target-azure-documentdb-for-json-output-from-stream-analytics"></a>DocumentDB de Azure de destino para el resultado JSON de análisis de secuencia

Análisis de secuencia pueden destinar [Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) para JSON de salida, habilitar las consultas de archivado y baja latencia de datos en datos JSON no estructurados. Obtenga información sobre cómo implementar mejor esta integración.

Para aquellos que no están familiarizados con DocumentDB, eche un vistazo a [DocumentDB la ruta de aprendizaje](https://azure.microsoft.com/documentation/learning-paths/documentdb/) para empezar.

## <a name="basics-of-documentdb-as-an-output-target"></a>Conceptos básicos de DocumentDB como un destino de salida
El resultado de Azure DocumentDB en secuencia Analytics permite grabar la secuencia de procesamiento de resultados como resultado JSON en su colecciones DocumentDB. Análisis de secuencia no crear colecciones en la base de datos en su lugar que requieren que se crean por adelantado. Esto es para que los costos de facturación de colecciones de DocumentDB son transparentes para usted y para que puede ajustar el rendimiento, la coherencia y la capacidad de las colecciones de directamente con las [API de DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx). Se recomienda utilizar una base de datos de DocumentDB por trabajo streaming separar las colecciones de un trabajo de transmisión de forma lógica.

A continuación se detallan algunas de las opciones de la colección de DocumentDB.

## <a name="tune-consistency-availability-and-latency"></a>Ajustar la coherencia, la disponibilidad y la latencia

Para que coincidan con los requisitos de la aplicación, DocumentDB permite bien ajustar la base de datos y colecciones y realizar compensaciones entre coherencia, la disponibilidad y la latencia. Dependiendo de qué niveles de coherencia de lectura sus necesidades de escenario contra lectura y escritura latencia, que puede elegir un nivel de coherencia en la cuenta de base de datos. También de forma predeterminada, DocumentDB permite la indización sincrónico en cada operación CRUD para la colección. Esta es otra opción útil para controlar el rendimiento de lectura y escritura en DocumentDB. Para obtener más información sobre este tema, consulte el artículo de [cambiar los niveles de coherencia de base de datos y la consulta](../documentdb/documentdb-consistency-levels.md) .

## <a name="choose-a-performance-level"></a>Elija un nivel de rendimiento

Colecciones de DocumentDB pueden crearse en 3 niveles de rendimiento diferentes (S1, S2 o S3), que determinan el rendimiento disponible para CRUDs a esa colección. Además, el rendimiento se ve afectado por los niveles de coherencia/indización en la colección. Consulte [este artículo](../documentdb/documentdb-performance-levels.md) para comprender estos niveles de rendimiento en detalle.

## <a name="upserts-from-stream-analytics"></a>Upserts de análisis de secuencia

Integración de análisis de secuencia con DocumentDB le permite insertar o actualizar registros en la colección de DocumentDB basándose en una columna de identificador de documento determinada. También se conoce como una *Upsert*.

Análisis de secuencia utiliza un enfoque Upsert optimista, donde las actualizaciones se efectúa cuando se produce un error en la inserción debido a un conflicto de identificador de documento. Análisis de secuencia realiza esta actualización como una revisión, por lo que le permite actualizaciones parciales al documento, es decir, la adición de nuevas propiedades o reemplazar que una propiedad existente se realiza incrementalmente. Tenga en cuenta que los cambios en los valores de propiedades de la matriz en el resultado de documento JSON en toda la matriz obtener sobrescriben, es decir, la matriz no se combinan.

## <a name="data-partitioning-in-documentdb"></a>Partición en DocumentDB de datos

Colecciones de DocumentDB le permiten dividir los datos según las necesidades de rendimiento de la aplicación y los patrones de consulta. Cada colección puede contener un máximo de 10GB de datos (máximo) y actualmente no hay ninguna manera para una colección de escalar (o en desbordamiento). Para escalar, análisis de secuencia le permite escribir varias colecciones con un prefijo determinado (consulte los detalles de uso). Análisis de secuencia, utiliza la estrategia de [Resolución de la partición de Hash](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx) coherente basada en el usuario de columna PartitionKey para dividir sus registros de salida. El número de colecciones con el prefijo especificado en el inicio del trabajo streaming se usa como el recuento de partición de salida, en el que el trabajo escribe en paralelo (colecciones DocumentDB = particiones de salida). Para una sola colección S3 con hacerlo indización diferida sólo inserta, sobre 0,4 rendimiento de escritura de MB/s se puede esperar. Usar varias colecciones le permite lograr un mayor rendimiento y mayor capacidad.

Si desea aumentar el número de partición en el futuro, debe detener el trabajo, crear particiones los datos de sus colecciones existentes en colecciones de nuevas y, a continuación, reinicie el trabajo de análisis de secuencia. Más información sobre cómo utilizar PartitionResolver y volver a crear particiones junto con el código de ejemplo, se incluirán en una publicación de seguimiento. El artículo [particiones en DocumentDB](../articles/documentdb-partition-data.md#developing-a-partitioned-application) , también se proporciona detalles sobre este.

## <a name="documentdb-settings-for-json-output"></a>Configuración de DocumentDB de salida JSON

Crear DocumentDB como salida en secuencia Analytics genera una solicitud de confirmación para obtener más información, tal como se muestra a continuación. Esta sección proporciona una explicación de la definición de propiedades.

![pantalla de salida de análisis de secuencia de documentdb](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output.png)  

-   **Alias de salida** : un alias para hacer referencia a este resultado de la consulta ASA  
-   **Nombre de cuenta** : el nombre o el URI de la cuenta DocumentDB del extremo.  
-   **Clave de cuenta** : la tecla de acceso compartido para la cuenta de DocumentDB.  
-   **Base de datos** : DocumentDB el nombre de base de datos.  
-   **Patrón de nombre de la colección** : el modelo de nombre de la colección de las colecciones de usarse. El formato de nombre de la colección puede crearse mediante el token opcional {partición}, donde iniciar particiones de 0. A continuación se muestran las entradas válidas de ejemplo:  
   1\) MyCollection: debe existir una colección denominada "MyCollection".  
   2\) MyCollection {partición}-deben existir dichas colecciones – "MyCollection0", "MyCollection1", "MyCollection2" y así sucesivamente.  
-   **Clave de partición** : el nombre del campo en eventos de salida que se utiliza para especificar la clave de partición de salida entre colecciones. Para la salida de la colección, puede ser cualquier columna de salida arbitrario usado por ejemplo PartitionId.  
-   **Identificador de documento** : opcional. El nombre del campo en eventos de salida que se utiliza para especificar la clave principal basadas en qué insertar o actualizar las operaciones.  

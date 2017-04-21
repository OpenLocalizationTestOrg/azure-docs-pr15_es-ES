<properties
    pageTitle="Mover datos mediante copia actividad | Microsoft Azure"
    description="Obtenga más información sobre el movimiento de datos en datos fábrica canalizaciones: migración de datos entre nube almacena y un almacén local y el almacenamiento de una nube. Use la actividad de copia."
    keywords="copiar datos, movimiento de datos, migración de datos, de transferencia de datos"
    services="data-factory"
    documentationCenter=""
    authors="linda33wj"
    manager="jhubbard"
    editor="monicar"/>

<tags
    ms.service="data-factory"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/22/2016"
    ms.author="jingwang"/>

# <a name="move-data-by-using-copy-activity"></a>Mover datos mediante el uso de la actividad de copia

## <a name="overview"></a>Información general
En el generador de datos de Azure, puede usar copiar actividad para copiar los datos de formas diferentes de diversos local y orígenes de datos de Azure en la nube. Después de copiar datos, se puede aún más transforma y analizar. También puede usar copiar actividad Publicar transformación y los resultados del análisis de inteligencia empresarial (BI) y consumo de aplicación.

![Función de la actividad de copia](media/data-factory-data-movement-activities/copy-activity.png)

Copiar actividad funciona con seguro, fiables y ampliables y [servicio disponible globalmente](#global). Este artículo proporciona detalles sobre el movimiento de datos en el generador de datos y la actividad de copia.

En primer lugar, vamos a ver cómo se produce la migración de datos entre dos almacena datos de nube y entre un almacén de datos local y un almacén de datos de la nube.

> [AZURE.NOTE] Para obtener información sobre las actividades en general, vea [las actividades y canalizaciones descripción](data-factory-create-pipelines.md).

### <a name="copy-data-between-two-cloud-data-stores"></a>Copiar datos entre dos almacena datos de nube
Cuando almacena datos de origen y el receptor en la nube, copiar actividad recorre las fases siguientes para copiar los datos desde el origen al receptor. El servicio que potencia copia actividad:

1. Lee datos desde el almacén de datos de origen.
2. Realiza serialización/deserialización, compresión y descompresión, la asignación de columnas y a continuación, escriba conversión. Hace que estas operaciones en función de las configuraciones de la entrada del conjunto de datos, el conjunto de datos de salida y la actividad de copia.
3.  Escribe datos en el almacén de datos de destino.

El servicio elige automáticamente la región óptima para realizar el movimiento de datos. Esta área suele ser la más cercana al almacén de datos Lavabo uno.

![Copia de la nube de nube](./media/data-factory-data-movement-activities/cloud-to-cloud.png)


### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>Copiar datos entre un almacén de datos local y un almacén de datos de la nube
Para mover datos entre un almacén de datos local y un almacén de datos de nube de forma segura, instalar Data Management Gateway en su equipo local. Data Management Gateway es un agente que permite el procesamiento y movimiento de datos híbrido. Puede instalarlo en el mismo equipo, como el almacén de datos propio o en otro equipo que tiene acceso al almacén de datos.

En este escenario, Data Management Gateway realiza la serialización o deserialización, compresión y descompresión, la asignación de columnas y conversión de tipos. No flujo de datos a través del servicio de generador de datos de Azure. En su lugar, Data Management Gateway escribe directamente los datos en el almacén de destino.

![Copiar en local-to cloud](./media/data-factory-data-movement-activities/onprem-to-cloud.png)

Consulte [mover datos entre local y la nube almacena datos](data-factory-move-data-between-onprem-and-cloud.md) para una introducción y un tutorial. Consulte [Data Management Gateway](data-factory-data-management-gateway.md) para obtener información detallada sobre este agente.

También puede mover almacena datos de datos de compatibles que se hospeda en máquinas virtuales de Azure IaaS (VM) mediante el uso de Data Management Gateway. En este caso, puede instalar Data Management Gateway en la misma VM como el almacén de datos propio o en una máquina virtual independiente tiene acceso al almacén de datos.

## <a name="supported-data-stores-and-formats"></a>Almacena datos compatibles y formatos
[AZURE.INCLUDE [data-factory-supported-data-stores](../../includes/data-factory-supported-data-stores.md)]

Si necesita mover datos de una datos almacenan que copiar actividad no admite, usar una **actividad personalizada** en el generador de datos con su propia lógica para copiar o mover los datos. Para obtener más información sobre cómo crear y usar una actividad personalizada, consulte [usar las actividades personalizadas de una canalización de generador de datos de Azure](data-factory-use-custom-activities.md).

### <a name="supported-file-formats"></a>Formatos de archivo admitidos
Puede utilizar Copiar actividad para copiar archivos como-entre almacena dos datos basados en archivos, como blobs de Windows Azure, el sistema de archivos y HDFS. Para ello, puede omitir la [sección formato](data-factory-create-datasets.md) en las definiciones de conjunto de datos de entrada y salida. Los datos se copian eficazmente sin ningún serialización o deserialización.

Actividad de copiar también lee y escribe en archivos de formatos especificados: texto, Avro, ORC, parqués y JSON. Puede realizar las siguientes actividades de copia, por ejemplo:

-   Copiar datos en formato de texto (CSV) de blobs de Windows Azure y escribir en la base de datos de SQL Azure.
-   Copiar archivos en formato de texto (CSV) desde el sistema de archivos locales y escribir blobs de Windows Azure en formato Avro.
-   Copie datos de la base de datos de SQL Azure y escritura a HDFS local en formato ORC.



## <a name="global"></a>Movimiento de datos disponibles globalmente
Generador de datos de Azure sólo está disponible en las regiones oeste de Estados Unidos, oriental de Estados Unidos y Europa del Norte. Sin embargo, el servicio que potencia copia actividad está disponible globalmente en las siguientes regiones y zonas geográficas. La topología disponible globalmente garantiza movimiento de datos eficiente en cuanto a que generalmente evita saltos entre región. Disponibilidad de datos de fábrica y movimiento de datos en una región, vea [servicios por región](https://azure.microsoft.com/regions/#services) .

### <a name="copy-data-between-cloud-data-stores"></a>Copiar datos entre almacena datos de nube
Cuando almacena datos de origen y el receptor en la nube, el generador de datos utiliza una implementación de servicio en la región más cercana al receptor de la misma geografía para mover los datos. Consulte la tabla siguiente para la asignación:

Región del almacén de datos de destino | Región utilizado para el movimiento de datos
:----------------------------------- | :----------------------------
Estados Unidos oriental | Estados Unidos oriental
Estados Unidos oriental 2 | Estados Unidos oriental 2
Estados Unidos occidental | Estados Unidos occidental
Estados Unidos oeste 2 | Estados Unidos occidental
Central de EE. | Central de EE.
Oeste Central de EE. | Central de EE.
Norte Central de EE. | Norte Central de EE.
Sur Central de EE. | Sur Central de EE.
Europa del Norte | Europa del Norte
Europa occidental | Europa occidental
Sudeste asiático | Sudeste asiático
Asia oriental | Sudeste asiático
Japón oriental | Japón oriental
Japón oeste | Japón oriental
Sur de Brasil | Sur de Brasil
Australia Oriental | Australia Oriental
Australia sureste | Australia sureste
India central | India central
India sur | India central
India occidental | India central


> [AZURE.NOTE] Si la región del almacén de datos de destino no está en la lista anterior, se produce un error en la actividad de copia en vez de hacerlo a través de una región alternativo.

### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>Copiar datos entre un almacén de datos local y un almacén de datos de la nube
Cuando se copian datos entre local (o máquinas virtuales de Windows Azure o IaaS) y nube stores, [Data Management Gateway](data-factory-data-management-gateway.md) realiza movimientos de datos en un equipo local o una máquina virtual. Los datos no fluyen a través del servicio en la nube, a menos que use la capacidad de [copia de prueba](data-factory-copy-activity-performance.md#staged-copy) . En este caso, los datos fluyen en el almacenamiento de blobs de Windows Azure provisional antes de escribirlo en el almacén de datos de receptor.

## <a name="create-a-pipeline-with-copy-activity"></a>Crear una canalización con la actividad de copia
Puede crear una canalización con la actividad de copia de un par de maneras:

### <a name="by-using-the-copy-wizard"></a>Mediante el Asistente para la copia
El Asistente para la copia del generador de datos le ayuda a crear una canalización con actividad de copia. Esta canalización le permite copiar datos desde orígenes compatibles destinos *sin tener que escribir JSON* las definiciones de servicios vinculados, conjuntos de datos y canalizaciones. Vea [Asistente para la copia de generador de datos](data-factory-copy-wizard.md) para obtener información detallada sobre el asistente.  

### <a name="by-using-json-scripts"></a>Mediante secuencias de comandos JSON
Puede usar el Editor del generador de datos en el portal de Azure, Visual Studio o Azure PowerShell para crear una definición de JSON para una canalización (mediante copia actividad). A continuación, se puede implementar para crear la canalización en el generador de datos. Consulte [Tutorial: uso de actividad de copia de una canalización de generador de datos de Azure](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obtener un tutorial con instrucciones paso a paso.    

Propiedades JSON (como nombre, descripción, entrada y salida tablas y directivas) están disponibles para todos los tipos de actividades. Propiedades que están disponibles en la `typeProperties` sección de la actividad varían en función de cada tipo de actividad.

Para copiar actividad, la `typeProperties` sección varía en función de los tipos de orígenes y receptores. Haga clic en un receptor de origen o en la sección [orígenes y receptores compatibles](#supported-data-stores) para obtener información sobre las propiedades de tipo que admite la actividad de copia de ese almacén de datos.   

Esto es una definición de JSON de ejemplo:

    {
      "name": "ADFTutorialPipeline",
      "properties": {
        "description": "Copy data from Azure blob to Azure SQL table",
        "activities": [
          {
            "name": "CopyFromBlobToSQL",
            "type": "Copy",
            "inputs": [
              {
                "name": "InputBlobTable"
              }
            ],
            "outputs": [
              {
                "name": "OutputSQLTable"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "SqlSink",
                "writeBatchSize": 10000,
                "writeBatchTimeout": "60:00:00"
              }
            },
            "Policy": {
              "concurrency": 1,
              "executionPriorityOrder": "NewestFirst",
              "retry": 0,
              "timeout": "01:00:00"
            }
          }
        ],
        "start": "2016-07-12T00:00:00Z",
        "end": "2016-07-13T00:00:00Z"
      }
    }

La programación que se define en el conjunto de datos de salida determina cuándo se ejecuta la actividad (por ejemplo: **diariamente**, frecuencia como **día**e intervalo como **1**). La actividad copia datos de un conjunto de datos de entrada (de**origen**) a un conjunto de datos de salida (**receptor**).

Puede especificar más de un conjunto de datos de entrada a la actividad de copia. Se utilizan para comprobar las dependencias antes de ejecuta la actividad. Sin embargo, solo los datos desde el primer conjunto de datos se copian en el conjunto de datos de destino. Para obtener más información, consulte [programar y ejecución](data-factory-scheduling-and-execution.md).  

## <a name="performance-and-tuning"></a>Rendimiento y optimización
Consulte la [Guía de optimización y rendimiento de la actividad de copia](data-factory-copy-activity-performance.md), que describe los factores clave que afectan al rendimiento de movimiento de datos (copiar actividad) en el generador de datos de Azure. También se muestran el rendimiento observado durante las pruebas internas y se trata diversas maneras para optimizar el rendimiento de la actividad de copia.

## <a name="scheduling-and-sequential-copy"></a>Copiar secuencial y programación
Para obtener información detallada sobre cómo funciona la programación y ejecución en el generador de datos, vea [programación y ejecución](data-factory-scheduling-and-execution.md) . Es posible ejecutar varias operaciones de copia consecutiva de forma secuencial o encargado. Vea la sección de [copia de pedido](data-factory-scheduling-and-execution.md#ordered-copy) .

## <a name="type-conversions"></a>Conversiones de tipo
Almacena datos distintos tiene sistemas de otro tipo nativo. Copiar actividad realiza las conversiones de tipo automática de tipos de origen para recibir tipos con el enfoque de dos pasos siguientes:

1. Convertir de tipos de origen nativo a un tipo de .NET.
2. Convertir un tipo de .NET a un tipo de lavabo nativa.

La asignación de un sistema de tipo nativo a un tipo de .NET para un almacén de datos está en el artículo de almacén de datos correspondientes. (Haga clic en el vínculo de la tabla [compatibles almacena datos](#supported-data-stores) ). Puede usar estas asignaciones para determinar tipos adecuados al crear las tablas, de modo que copia actividad realiza las conversiones derecha.


## <a name="next-steps"></a>Pasos siguientes
- Para obtener más información sobre la actividad de copia, vea [Copiar datos de almacenamiento de blobs de Windows Azure a la base de datos de SQL Azure](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
- Para obtener información acerca de cómo mover los datos de un almacén de datos local a un almacén de datos de la nube, consulte [para la nube datos almacena en local de mover los datos desde](data-factory-move-data-between-onprem-and-cloud.md).

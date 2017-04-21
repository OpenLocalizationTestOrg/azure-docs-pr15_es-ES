<properties
    pageTitle="Agregar una entrada de datos a los trabajos de análisis de secuencia | Microsoft Azure"
    description="Obtenga información sobre cómo conectar un origen de datos a su trabajo de análisis de secuencia como transmisión de entrada de datos de evento Hubs o referencia de datos de almacenamiento de Blog."
    keywords="datos de entrada, transmisión de datos"
    documentationCenter=""
    services="stream-analytics"
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"
/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"
/>


# <a name="add-a-streaming-data-input-or-reference-data-to-a-stream-analytics-job"></a>Agregar una transmisión de datos de entrada o referencia de datos a una tarea de análisis de secuencia

Obtenga información sobre cómo conectar un origen de datos a su trabajo de análisis de secuencia como entrada de datos de evento Hubs o referencia datos de almacenamiento de blobs de transmisión.

Azure trabajos de análisis de secuencia pueden estar conectados a la entrada de datos de uno o más, cada una de las cuales define una conexión a un origen de datos existente. Como se envían datos a ese origen de datos, es consumido por el trabajo de análisis de secuencia y se procesan en tiempo real como la transmisión de datos. Análisis de la secuencia tiene integración de primera clase con [Azure evento Hubs](https://azure.microsoft.com/services/event-hubs/) y el [almacenamiento de blobs de Windows Azure](../storage/storage-dotnet-how-to-use-blobs.md) dentro y fuera de la suscripción de la tarea.

En este artículo es un paso en la [ruta de aprendizaje de análisis de la secuencia](/documentation/learning-paths/stream-analytics/).

## <a name="data-input-streaming-data-and-reference-data"></a>Entrada de datos: transmisión de datos y datos de referencia

Existen dos tipos distintos de entradas en el análisis de secuencia: flujos de datos y datos de referencia.

- **Flujos de datos**: trabajos de análisis de secuencia deben incluir datos al menos una entrada de la secuencia para ser consumida y transformación de la tarea. Almacenamiento de blobs de Azure y Azure evento Hubs se admiten como orígenes de entrada de flujo de datos. Azure Hubs de eventos se usan para recopilar secuencias de eventos de los dispositivos conectados, aplicaciones y servicios. Almacenamiento de blobs de Windows Azure se puede utilizar como un origen de entrada para recopilar datos de forma masiva como una secuencia.  
- **Datos de referencia**: análisis de secuencia admite un segundo tipo de datos de referencia llamado entrada auxiliar.  En lugar de datos en movimiento, estos datos están estático o ralentizar el cambio.  Normalmente se usa para realizar búsquedas y correlación con flujos de datos para crear un conjunto de datos más rico.  Almacenamiento de blobs de Windows Azure actualmente es el único origen de entrada admitido para los datos de referencia.  

Para agregar una entrada de la tarea de análisis de secuencia:

1. En el portal de Azure, haga clic en **entradas** y, a continuación, haga clic en **Agregar una entrada** en el trabajo de análisis de secuencia.

    ![Portal clásica Azure - agregar una entrada.](./media/stream-analytics-add-inputs/1-stream-analytics-add-inputs.png)  

    Haga clic en el mosaico de **entradas** en el trabajo de análisis de secuencia en el portal de Azure.  

    ![Portal de Azure - agregar la entrada de datos.](./media/stream-analytics-add-inputs/7-stream-analytics-add-inputs.png)  

2. Especificar el tipo de entrada: **flujo de datos** o **datos de referencia**.

    ![Agregar los datos correctos de entrada, transmitieron o referencia](./media/stream-analytics-add-inputs/2-stream-analytics-add-inputs.png)  

    ![Agregar los datos correctos de entrada, transmitieron o referencia](./media/stream-analytics-add-inputs/8-stream-analytics-add-inputs.png)  

3. Si crea una entrada de la secuencia de datos, especifique el tipo de origen de la entrada.  Puede omitir este paso durante la creación de datos de referencia como solo Blob se admite el almacenamiento en este momento.

    ![Agregar la entrada de datos de flujo de datos](./media/stream-analytics-add-inputs/3-stream-analytics-add-inputs.png)  

    ![Agregar la entrada de datos de flujo de datos](./media/stream-analytics-add-inputs/9-stream-analytics-add-inputs.png)  

4. Proporcione un nombre descriptivo para esta entrada en el cuadro Alias de entrada.  Este nombre se utilizará en la consulta de la tarea más adelante para hacer referencia a la entrada.

    Rellene el resto de las propiedades de conexión necesaria para conectarse a su origen de datos. Estos campos varían según el tipo de entrada y origen y se definen en detalle [aquí](stream-analytics-create-a-job.md).  

    ![Agregar la entrada de datos del concentrador de evento](./media/stream-analytics-add-inputs/4-stream-analytics-add-inputs.png)  

5. Especificar la configuración de serialización de los datos de entrada:
    - Para asegurarse de que las consultas funcionan según lo esperado, especifique el **Formato de serialización de evento** de datos entrantes.  Formatos de serialización compatibles son JSON, CSV y Avro.
    - Compruebe la **codificación** para los datos.  UTF-8 es el formato de codificación compatible solo en este momento.

    ![Configuración de serialización de datos para los datos de entrada](./media/stream-analytics-add-inputs/5-stream-analytics-add-inputs.png)  

    ![Configuración de serialización de datos para los datos de entrada](./media/stream-analytics-add-inputs/10-stream-analytics-add-inputs.png)  

6. Después de completar la creación de entrada, análisis de secuencia de comprobar que puede conectarse al origen de entrada.  Puede ver el estado de la operación de conexión de prueba en el hub de notificación.

    ![Probar la conexión de datos de secuencias de entrada](./media/stream-analytics-add-inputs/6-stream-analytics-add-inputs.png)  

    ![Probar la conexión de datos de secuencias de entrada](./media/stream-analytics-add-inputs/11-stream-analytics-add-inputs.png)  

## <a name="get-help-with-streaming-data-inputs"></a>Obtener ayuda con la transmisión de entradas de datos
Para obtener más ayuda, pruebe nuestro [foro de análisis de secuencia de Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Pasos siguientes

- [Introducción a análisis de secuencia de Azure](stream-analytics-introduction.md)
- [Empezar a usar el análisis de secuencia de Azure](stream-analytics-get-started.md)
- [Escala trabajos de análisis de secuencia de Azure](stream-analytics-scale-jobs.md)
- [Referencia del lenguaje de consulta de análisis de secuencia de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referencia de la API de REST de administración de secuencia de Azure análisis](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<properties 
    pageTitle="Cómo configurar datos salidas para trabajos de análisis de secuencia | Microsoft Azure" 
    description="Configurar salidas para trabajos de análisis de secuencia | segmento de ruta de aprendizaje."
    keywords="datos de salida, el movimiento de datos"
    documentationCenter=""
    services="stream-analytics"
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

# <a name="how-to-configure-data-outputs-for-stream-analytics-jobs"></a>Cómo configurar datos salidas para trabajos de análisis de secuencia

Azure trabajos de análisis de secuencia pueden estar conectados a uno o varios resultados de datos que definen una conexión a un receptor de datos existente. Como el trabajo de análisis de secuencia procesa y transforma datos entrantes, se escribe una secuencia de eventos de salida de datos en los resultados de la tarea.

Datos de análisis de secuencia salidas pueden usarse para origen paneles en tiempo real o alertas, desencadenar flujos de trabajo de movimiento de datos, o simplemente archivar datos para proceso por lotes más adelante. Análisis de la secuencia tiene integración de primera clase con varios servicios de Azure, que se describen aquí detalladamente.

Para agregar un resultado a la tarea de análisis de secuencia:

1. En el Portal de Azure clásica, haga clic en **resultados** y, a continuación, haga clic en **Agregar resultados** en su trabajo de análisis de la secuencia.

    ![Agregar resultados](./media/stream-analytics-add-outputs/1-stream-analytics-add-outputs.png)  

    Haga clic en el mosaico de **resultados** de la tarea de análisis de secuencia en el portal de Azure.

    ![Portal de Azure agregar resultados](./media/stream-analytics-add-outputs/5-stream-analytics-add-outputs.png)

2. Especifique el tipo del resultado:

    ![Elija el tipo de movimiento de datos](./media/stream-analytics-add-outputs/2-stream-analytics-add-outputs.png)  

    ![Portal de Azure Elegir tipo de movimiento de datos](./media/stream-analytics-add-outputs/6-stream-analytics-add-outputs.png)

3. Proporcione un nombre descriptivo para este resultado en el cuadro **Alias de salida** . Este nombre puede usarse en la consulta de la tarea más adelante para hacer referencia a los resultados.  
    
    Rellene el resto de las propiedades de conexión necesaria para conectarse a su resultado.  Estos campos varían según el tipo de salida y se definen en detalle.  

    ![Agregar propiedades de salida de datos](./media/stream-analytics-add-outputs/3-stream-analytics-add-outputs.png)  

4. Según el tipo de salida, debe especificar cómo se serie o con formato de los datos. La configuración de serialización específicos para cada tipo de resultado se describen aquí.

    Rellene el resto de las propiedades de conexión necesaria para conectarse a su origen de datos. Estos campos varían según el tipo de entrada y origen y se definen en detalle [aquí](stream-analytics-create-a-job.md).  

    ![Agregar datos resultado concentrador de evento](./media/stream-analytics-add-outputs/4-stream-analytics-add-outputs.png)  

    ![Resultados de datos del portal Azure a concentrador de evento](./media/stream-analytics-add-outputs/7-stream-analytics-add-outputs.png)  

> [Azure.Note] Cualquier elemento de salida que se agrega a la tarea, debe existir antes de que la tarea se inicia y eventos Iniciar flujo. Por ejemplo, si utiliza el almacenamiento de blobs como salida, el trabajo no creará una cuenta de almacenamiento automáticamente. Debe ser creados por el usuario antes de que se inicia el trabajo ASA.

## <a name="get-help"></a>Obtener ayuda
Para obtener más ayuda, pruebe nuestro [foro de análisis de secuencia de Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Pasos siguientes

- [Introducción a análisis de secuencia de Azure](stream-analytics-introduction.md)
- [Empezar a usar el análisis de secuencia de Azure](stream-analytics-get-started.md)
- [Escala trabajos de análisis de secuencia de Azure](stream-analytics-scale-jobs.md)
- [Referencia del lenguaje de consulta de análisis de secuencia de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referencia de la API de REST de administración de secuencia de Azure análisis](https://msdn.microsoft.com/library/azure/dn835031.aspx)

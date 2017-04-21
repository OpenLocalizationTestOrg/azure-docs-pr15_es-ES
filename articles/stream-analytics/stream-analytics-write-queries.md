<properties 
    pageTitle="Cómo escribir consultas en el análisis de secuencia | Microsoft Azure" 
    description="Escribir consultas en el análisis de secuencia y los datos de la consulta | segmento de ruta de aprendizaje."
    keywords="cómo escribir consultas de datos de la consulta, escribir una consulta, escribir consultas"
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

# <a name="how-to-write-queries-in-stream-analytics"></a>Cómo escribir consultas en el análisis de secuencia

Escribir consultas de secuencia de procesamiento de la lógica de análisis de secuencia de Azure se implementa como una "consulta permanente" definido antes de un trabajo se inicia y ejecuta en los datos como tenga el trabajo. La transformación de datos se expresa en un lenguaje de consulta SQL similares, que es en gran medida un subconjunto de SQL T con algunas extensiones de idioma agregado como [ventanas](https://msdn.microsoft.com/library/azure/dn835019.aspx) que se utilizan para expresar semántica temporal.

## <a name="writing-queries"></a>Escribir consultas: ##

1. En el trabajo de análisis de secuencia en el portal de administración de Azure, haga clic en **consulta**.

    ![Consulta de selección](./media/stream-analytics-write-queries/1-stream-analytics-write-queries.png)  

    En el Portal de Azure, haga clic en **consulta**.

    ![Seleccione la vista previa de consulta](./media/stream-analytics-write-queries/query-preview-portal.png)  

2.  Tareas nuevas tienen una plantilla de consulta para ayudar a empezar. La plantilla de consulta, realiza una consulta "paso" que proyectos todos los campos de eventos de entrada en el resultado.  

    - Si ha definido al menos una entrada y salida para el trabajo, puede reemplazar el marcador de posición "[YourOutputAlias]" y "[YourInputAlias]" campos con los alias de la entrada y salida que desea usar en primer lugar. Además, puede crear y probar la consulta en el Portal de clásico de Azure sin definir entradas y los resultados en el trabajo.
    - Si desea realizar el procesamiento de más de un paso simple, puede editar la definición de consulta. Para empezar con la creación de consultas, eche un vistazo a algunos consulta comunes que se capturan tramas [aquí](stream-analytics-stream-analytics-query-patterns.md).  
  
    ![Ventana datos de la consulta](./media/stream-analytics-write-queries/2-stream-analytics-write-queries.png)  

## <a name="to-validate-query-data-is-working"></a>Está trabajando validar los datos de la consulta: ##

Puede comprobar que la consulta se comporta según lo esperado, ejecute en el explorador en uno o más archivos JSON locales que contiene los datos de prueba. No se inicia el trabajo ni tener repercusiones facturación.

> [AZURE.NOTE] Actualmente pruebas de consultas en el explorador no es compatible en el Portal de Azure.  

1.  Asegúrese de que no hay ningún error en la consulta (en caso contrario, el botón de prueba se desactivará) y, a continuación, haga clic en el botón de prueba.  

    ![Consultar los datos de prueba](./media/stream-analytics-write-queries/3-stream-analytics-write-queries.png)  

2.  Se le pedirá para especificar los archivos para cada una de las entradas que se hace referencia en la consulta. En este ejemplo, la consulta de la plantilla se deja como-es, por lo que el cuadro de diálogo que solicita la entrada denominada "yourinputalias".  

    ![Consulta de datos de prueba](./media/stream-analytics-write-queries/4-stream-analytics-write-queries.png)  

3.  Ir a un archivo de prueba. Varios archivos de ejemplo están disponibles en [github](https://github.com/Azure/azure-stream-analytics/tree/master/Sample Data) y también puede recuperar datos de ejemplo desde sus propio entradas de flujo de datos a través de la función de datos de ejemplo en la pestaña entradas.  

    ![Entrada de consulta](./media/stream-analytics-write-queries/5-stream-analytics-write-queries.png)  

4.  Después de cerrar el cuadro de diálogo, los datos de prueba se ejecutará la consulta y verá los resultados de la parte inferior de la página de consulta.  

    ![Resumen de la consulta](./media/stream-analytics-write-queries/6-stream-analytics-write-queries.png)  

## <a name="get-help"></a>Obtener ayuda
Para obtener más ayuda, pruebe nuestro [foro de análisis de secuencia de Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Pasos siguientes

- [Introducción a análisis de secuencia de Azure](stream-analytics-introduction.md)
- [Empezar a usar el análisis de secuencia de Azure](stream-analytics-get-started.md)
- [Escala trabajos de análisis de secuencia de Azure](stream-analytics-scale-jobs.md)
- [Referencia del lenguaje de consulta de análisis de secuencia de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referencia de la API de REST de administración de secuencia de Azure análisis](https://msdn.microsoft.com/library/azure/dn835031.aspx)

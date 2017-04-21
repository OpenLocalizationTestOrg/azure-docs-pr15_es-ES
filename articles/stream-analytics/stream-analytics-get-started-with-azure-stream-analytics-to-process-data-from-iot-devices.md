<properties
    pageTitle="Introducción a análisis de secuencia de Azure para procesar datos desde dispositivos IoT. | Microsoft Azure"
    description="Flujos de datos y etiquetas de sensor IoT con análisis de secuencia y procesamiento de datos en tiempo real"
    keywords="solución IOT, empezar a trabajar con iot"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"
/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="10/19/2016"
    ms.author="jeffstok"
/>

# <a name="get-started-with-azure-stream-analytics-to-process-data-from-iot-devices"></a>Introducción a análisis de secuencia de Azure para procesar datos desde dispositivos IoT

En este tutorial, aprenderá a crear la lógica de procesamiento de secuencias para recopilar datos de los dispositivos de Internet de cosas (IoT). Vamos a utilizar un caso de uso de Internet de cosas (IoT) reales, para demostrar cómo crear su solución rápidamente y económico.

## <a name="prerequisites"></a>Requisitos previos

-   [Suscripción de Azure](https://azure.microsoft.com/pricing/free-trial/)
-   Archivos de consultas y datos de ejemplo que se pueden descargar desde [GitHub](https://aka.ms/azure-stream-analytics-get-started-iot)

## <a name="scenario"></a>Escenario

Contoso, que es una empresa en el espacio de automatización industriales, ha automatizado completamente su proceso de fabricación. La maquinaria en esta planta tiene sensores que son capaces de emisión de flujos de datos en tiempo real. En este escenario, un administrador de planta de producción desea tener perspectivas en tiempo real de los datos de sensor para buscar tramas y realizar acciones en ellos. Vamos a utilizar el lenguaje de consulta de análisis de secuencia (SAQL) sobre los datos de sensor para buscar modelos interesantes de la secuencia de datos entrante.

Aquí se generan datos desde un dispositivo de etiqueta de sensor instrumentos Texas.

![Etiqueta de sensor instrumentos Texas](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-01.jpg)

La carga de los datos está en formato JSON y el siguiente aspecto:


    {
        "time": "2016-01-26T20:47:53.0000000",  
        "dspl": "sensorE",  
        "temp": 123,  
        "hmdt": 34  
    }  

En un escenario real, podría tener cientos de estos sensores generar eventos como una secuencia. Lo ideal es que, un dispositivo de puerta de enlace podría ejecutar código para insertar estos eventos en [Azure evento Hubs](https://azure.microsoft.com/services/event-hubs/) o [Azure IoT Hubs](https://azure.microsoft.com/services/iot-hub/). El trabajo de análisis de secuencia ¿recopile estos eventos de evento Hubs y ejecutar consultas de análisis en tiempo real en las secuencias. A continuación, puede enviar los resultados a una de las [compatibles salidas](stream-analytics-define-outputs.md).

Para facilitar su uso, esta guía de Introducción proporciona un archivo de datos de ejemplo, que se ha capturado desde dispositivos de etiqueta de sensor real. Puede ejecutar consultas en los datos de ejemplo y ver los resultados. En los tutoriales subsiguientes, obtendrá información sobre cómo conectar su trabajo con entradas y salidas y distribuye en el servicio de Azure.

## <a name="create-a-stream-analytics-job"></a>Crear un análisis de flujo de trabajo

1. En el [portal de Azure](http://portal.azure.com), haga clic en el signo más y, a continuación, escriba **El análisis de flujo** en la ventana de texto a la derecha. A continuación, seleccione **el análisis de flujo de trabajo** en la lista de resultados.

    ![Crear un nuevo análisis de flujo de trabajo](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-02.png)

2. Escriba un nombre único trabajo y compruebe que la suscripción es la correcta para su trabajo. A continuación, cree un nuevo grupo de recursos o seleccione uno existente en la suscripción.

3. A continuación, seleccione una ubicación para el trabajo. Se recomienda transferencia seleccionando la misma ubicación como el grupo de recursos y de la cuenta de almacenamiento previsto para velocidad de procesamiento y reducción de costos de datos.

    ![Crear una nueva tarea de análisis de secuencia de detalles](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03.png)

    > [AZURE.NOTE] Debe crear esta cuenta de almacenamiento solo una vez por región. Este almacenamiento se compartirán entre todos los trabajos de análisis de flujo que se crean en esa región.

4. Active la casilla para colocar el trabajo en el escritorio y, a continuación, haga clic en **crear**.

    ![creación de trabajo en curso](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03a.png)

5. Debería ver una 'implementación iniciada...' se muestra en la parte superior derecha de la ventana del explorador. Pronto cambiará a una ventana completa tal como se muestra a continuación.

    ![creación de trabajo en curso](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03b.png)

### <a name="create-an-azure-stream-analytics-query"></a>Crear una consulta de análisis de secuencia de Azure

Una vez creado el trabajo es el momento para abrirlo y diseñar una consulta. Tener fácil acceso a su trabajo, haga clic en el mosaico para el mismo.

![Mosaico de trabajo](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-04.png)

En el panel de la **Topología de trabajo** , haga clic en el cuadro de **consulta** para ir al Editor de consultas. El editor de **consultas** permite especificar una consulta SQL T que realiza la transformación de los datos entrantes del evento.

![Cuadro de consulta](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-05.png)

### <a name="query-archive-your-raw-data"></a>Consulta: Archivar los datos sin formato

La forma más sencilla de consulta es una consulta de paso que almacena todos los datos de entrada a su resultado designado. Descargar el archivo de datos de ejemplo de [GitHub](https://aka.ms/azure-stream-analytics-get-started-iot) a una ubicación en el equipo. 

1. Pegue la consulta desde el archivo PassThrough.txt. 

    ![Secuencia de entrada de prueba.](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-06.png)

2. Haga clic en los suspensivos puntos junto a su entrada y seleccione el cuadro **cargar los datos de ejemplo de archivo** .

    ![Secuencia de entrada de prueba.](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-06a.png)

3. Se abrirá un panel a la derecha como resultado, en Seleccione el archivo de datos HelloWorldASA InputStream.json desde la ubicación de descarga y haga clic en **Aceptar** en la parte inferior del panel.

    ![Secuencia de entrada de prueba.](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-06b.png)

4. A continuación, haga clic en el engranaje **probar** en la parte superior izquierda de área de la ventana y procesar la consulta de prueba en el conjunto de datos de ejemplo. Se abrirá una ventana de resultados por debajo de la consulta como se complete el proceso.

    ![Resultados de pruebas](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-07.png)

### <a name="query-filter-the-data-based-on-a-condition"></a>Consulta: Filtrar los datos según una condición

Probar filtrar los resultados según una condición. Vamos a mostrar los resultados para los eventos que proceden de "sensorA". La consulta se encuentra en el archivo Filtering.txt.

![Filtrado de un flujo de datos](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-08.png)

Tenga en cuenta que la consulta distingue mayúsculas de minúsculas compara un valor de cadena. Haga clic en el engranaje de **prueba** para ejecutar la consulta. La consulta debe devolver 389 filas fuera 1860 eventos.

![Segundo resultados de prueba de consulta](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-09.png)

### <a name="query-alert-to-trigger-a-business-workflow"></a>Consulta: Alerta desencadenar un flujo de trabajo

Vamos a hacer nuestra consulta más detallada. Para cada tipo de sensor, queremos supervisar la temperatura media por la ventana de 30 segundos y mostrar resultados solo si la temperatura media es superior a 100 grados. Que se escribe la consulta siguiente y, a continuación, haga clic en **prueba** para ver los resultados. La consulta se encuentra en el archivo ThresholdAlerting.txt.

![consulta de filtro de 30 segundos](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-10.png)

Ahora debe ver los resultados que contienen solo 245 filas y nombres de sensores donde el promedio templado es mayor que 100. Esta consulta agrupa la secuencia de eventos por **dspl**, que es el nombre de sensor sobre una **Ventana de rotación** de 30 segundos. Consultas temporales deben indicar cómo queremos vez al progreso. Uso de la cláusula **Por la marca de tiempo** , hemos especificado la columna **OUTPUTTIME** para asociar horas con todos los cálculos temporales. Para obtener información detallada, lea los artículos MSDN sobre [Administración del tiempo](https://msdn.microsoft.com/library/azure/mt582045.aspx) y [funciones de ventanas](https://msdn.microsoft.com/library/azure/dn835019.aspx).

### <a name="query-detect-absence-of-events"></a>Consulta: Detectar ausencia de eventos

¿Cómo podemos escribimos una consulta para encontrar una falta de eventos de entrada? Vamos a encontrar la última vez que un sensor envía los datos y, a continuación, no ha enviado eventos durante el minuto siguiente. La consulta se encuentra en el archivo AbsenseOfEvent.txt.

![Detectar ausencia de eventos](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-11.png)

Aquí usamos una combinación **Externa izquierda** en el mismo flujo de datos (autocombinación). Para una combinación **interna** , se devuelve un resultado solo cuando se encuentra una coincidencia.  Para una combinación **Externa izquierda** , si un evento desde el lado izquierdo de la combinación es no coincidentes, se devuelve una fila que tenga NULL para todas las columnas de la derecha. Esta técnica es muy útil para encontrar una ausencia de eventos. Consulte la documentación de MSDN para obtener más información acerca de la [combinación](https://msdn.microsoft.com/library/azure/dn835026.aspx).

## <a name="conclusion"></a>Conclusión

El propósito de este tutorial es demostrar cómo escribir consultas de lenguaje de consulta de análisis de secuencia diferentes y ver resultados en el explorador. Sin embargo, esto está comenzando a. Puede hacer muchas más cosas con el análisis de secuencia. Análisis de secuencia es compatible con una amplia variedad de entradas y salidas y puede incluso usar funciones en aprendizaje de Azure máquina para que sea una sólida herramienta para analizar los flujos de datos. Puede comenzar a explorar más información sobre el análisis de flujo con nuestro [mapa de aprendizaje](https://azure.microsoft.com/documentation/learning-paths/stream-analytics/). Para obtener más información acerca de cómo escribir consultas, lea el artículo acerca de los [patrones de consulta comunes](./stream-analytics-stream-analytics-query-patterns.md).

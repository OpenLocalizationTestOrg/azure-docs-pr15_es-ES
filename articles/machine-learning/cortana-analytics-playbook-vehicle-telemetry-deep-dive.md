<properties 
    pageTitle="Guía de solución de análisis de telemetría de vehículo: análisis detallado en la solución | Microsoft Azure" 
    description="Usar las capacidades de inteligencia de Cortana para obtener perspectivas en tiempo real y predictivas en estado de vehículo y conducción hábitos." 
    services="machine-learning" 
    documentationCenter="" 
    authors="bradsev" 
    manager="jhubbard" 
    editor="cgronlun" />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/12/2016" 
    ms.author="bradsev" />


# <a name="vehicle-telemetry-analytics-solution-playbook-deep-dive-into-the-solution"></a>Guía de solución de análisis de telemetría de vehículo: análisis detallado en la solución

Este **menú** se vincula a las secciones de esta guía: 

[AZURE.INCLUDE [cap-vehicle-telemetry-playbook-selector](../../includes/cap-vehicle-telemetry-playbook-selector.md)]

Aumenta el esta sección detalle en cada una de las fases descritas en la arquitectura de la solución con instrucciones y punteros para la personalización. 

## <a name="data-sources"></a>Orígenes de datos

La solución usa dos distintos orígenes de datos:

- **señales de vehículo simulada y conjunto de datos de diagnóstico** y 
- **catálogo de vehículo**

Un simulador de telemáticas vehículo se incluye como parte de esta solución. Envía información de diagnóstico y señales correspondiente al estado del vehículo y en el patrón de conducción en un momento determinado en el tiempo. Haga clic en [Vehículo telemáticas Simulator](http://go.microsoft.com/fwlink/?LinkId=717075) para descargar la **Solución de Visual Studio de vehículo telemáticas Simulator** para personalizaciones en función de sus necesidades. El catálogo de vehículo contiene un conjunto de datos de referencia con un número de bastidor asignación modelo.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig2-vehicle-telematics-simulator.png)

*Ilustración 2: vehículo telemáticas Simulator*

Se trata de un conjunto de datos con formato JSON que contiene el esquema siguiente.

Columna | Descripción | Valores 
 ------- | ----------- | --------- 
NÚMERO DE BASTIDOR | Número de identificación generado de forma aleatoria de vehículo | Se obtiene a partir de una lista de números de identificación de vehículo generado de forma aleatoria 10.000 principal.
Temperatura exterior | La temperatura exterior donde está dictando vehículo | Número generado de forma aleatoria de 0 a 100
Temperatura del motor | La temperatura del motor de vehículo | Número generado de forma aleatoria de 0-500
Velocidad | La velocidad del motor en el que está dictando vehículo | Número generado de forma aleatoria de 0 a 100
Combustible | El nivel de combustible del vehículo | Número generado de forma aleatoria de 0 a 100 (indica el porcentaje de nivel de combustible)
EngineOil | El nivel de oil motor del vehículo | Número generado de forma aleatoria de 0 a 100 (indica el porcentaje de nivel de motor oil)
Presión neumático | La presión del vehículo | Aleatoriamente generados por el número de 0-50 (indica el porcentaje de nivel de presión neumático)
Odómetro | La lectura del cuentakilómetros del vehículo | Número generado de forma aleatoria de 0-200000
Accelerator_pedal_position | La posición del pedal de aceleración del vehículo | Número generado de forma aleatoria de 0 a 100 (indica el porcentaje de nivel de aceleración)
Parking_brake_status | Indica si el vehículo está estacionado o no | True o False
Headlamp_status | Indica que el proyector está o no en | True o False
Brake_pedal_status | Indica si se presiona el pedal de freno o no | True o False
Transmission_gear_position | La posición de engranaje de transmisión de vehículo | Estados: en primer lugar, segundo, tercero, cuarto, quinto, sexto, séptimo, octava
Ignition_status | Indica si el vehículo está ejecutando o ha dejado de | True o False
Windshield_wiper_status | Indica si el limpiaparabrisas está activado o no | True o False
ABS | Indica si el ABS está ocupado o no | True o False
Marca de tiempo | La marca de tiempo cuando se crea el punto de datos | Fecha
Ciudad | La ubicación del vehículo | 4 ciudades en esta solución: Santoña, Redmond, Sammamish, Seattle


El conjunto de datos de referencia de modelo de vehículo contiene bastidor para la asignación del modelo. 

NÚMERO DE BASTIDOR | Modelo |
--------------|------------------
FHL3O1SA4IEHB4WU1 | Turismo |
8J0U8XCPRGW4Z3NQE | Híbrido |
WORG68Z2PLTNZDBI7 | Familia Berlina |
JTHMYHQTEPP4WBMRN | Turismo |
W9FTHG27LZN1YWO0Y | Híbrido |
MHTP9N792PHK08WJM | Familia Berlina |
EI4QXI2AXVQQING4I | Turismo |
5KKR2VB4WHQH97PF8 | Híbrido |
W9NSZ423XZHAONYXB | Familia Berlina |
26WJSGHX4MA5ROHNL | Puede convertir |
GHLUB6ONKMOSI7E77 | Familiar |
9C2RHVRVLMEJDBXLP | Coche compacta |
BRNHVMZOUJ6EOCP32 | SUV pequeña |
VCYVW0WUZNBTM594J | Coche deportivo |
HNVCE6YFZSA5M82NY | SUV medio |
4R30FOR7NUOBL05GJ | Familiar |
WYNIIY42VKV6OQS1J | SUV grande |
8Y5QKG27QET1RBK7I | SUV grande |
DF6OX2WSRA6511BVG | Cupé |
Z2EOZWZBXAEW3E60T | Turismo |
M4TV6IEALD5QDS3IR | Híbrido |
VHRA1Y2TGTA84F00H | Familia Berlina |
R0JAUHT1L1R3BIKI0 | Turismo |
9230C202Z60XX84AU | Híbrido |
T8DNDN5UDCWL7M72H | Familia Berlina |
4WPYRUZII5YV7YA42 | Turismo |
D1ZVY26UV2BFGHZNO | Híbrido |
XUF99EW9OIQOMV7Q7 | Familia Berlina
8OMCL3LGI7XNCC21U | Puede convertir |
…….  |   |


### <a name="to-generate-simulated-data"></a>Para generar datos simuladas
1.  Para descargar el paquete de simulator de datos, haga clic en la flecha situada en la esquina superior derecha del nodo vehículo telemáticas Simulator. Guardar y extraer los archivos localmente en el equipo. ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig3-vehicle-telemetry-blueprint.png)*Figura 3-Guía de solución de análisis de telemetría de vehículo*

2.  En el equipo local, vaya a la carpeta donde se extrae el paquete de vehículo telemáticas Simulator. ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig4-vehicle-telematics-simulator-folder.png)*Figura 4: carpeta Simulator telemáticas de vehículo*

3.  Ejecute la aplicación **CarEventGenerator.exe**.

### <a name="references"></a>Referencias

[Solución de vehículo telemáticas Simulator Visual Studio](http://go.microsoft.com/fwlink/?LinkId=717075) 

[Hub de evento de Azure](https://azure.microsoft.com/services/event-hubs/)

[Generador de datos de Azure](https://azure.microsoft.com/documentation/learning-paths/data-factory/)


## <a name="ingestion"></a>Recopilación
Combinaciones de Azure evento Hubs, análisis de secuencia y generador de datos son aprovechar para recopilar las señales de vehículo, los eventos de diagnósticos y en tiempo real y el análisis de lote. Todos estos componentes se crean y está configurados como parte de la implementación de soluciones. 

### <a name="real-time-analysis"></a>Análisis en tiempo real
Los eventos generados por el simulador telemáticas de vehículo se publican en el Hub de evento mediante el SDK de concentrador de evento. El trabajo de análisis de secuencia recopila estos eventos desde el Hub de evento y procesa los datos en tiempo real para analizar el estado de un vehículo. 

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig5-vehicle-telematics-event-hub-dashboard.png) 

*Figura 5 - panel de concentrador de evento*

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig6-vehicle-telematics-stream-analytics-job-processing-data.png) 

*Figura 6 - trabajo de análisis de secuencia procesamiento de datos*

El trabajo de análisis de secuencia;

- recopila los datos desde el Hub de evento 
- realiza una combinación con los datos de referencia para asignar el número de bastidor del vehículo al modelo correspondiente 
- ellos se conserva en el almacenamiento de blobs de Windows Azure para análisis enriquecidos lote. 

La siguiente consulta de análisis de secuencia se utiliza para conservar los datos en el almacenamiento de blobs de Windows Azure. 

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig7-vehicle-telematics-stream-analytics-job-query-for-data-ingestion.png) 

*Figura 7 - consulta de trabajo de análisis de secuencia de recopilación de datos*

### <a name="batch-analysis"></a>Análisis por lotes
También que estamos generando un volumen adicional de vehículo simulada señales y conjunto de datos de diagnóstico para el análisis de lote más rico. Esto es necesario para garantizar un volumen de datos representante de proceso por lotes. Para ello, usamos una canalización denominada "PrepareSampleDataPipeline" en el flujo de trabajo del generador de datos de Azure para generar completo un año de las señales de vehículo simulada y conjunto de datos de diagnóstico. Haga clic en [Generador de datos de actividad personalizada](http://go.microsoft.com/fwlink/?LinkId=717077) para descargar la actividad de DotNet solución de Visual Studio para personalizaciones en función de sus necesidades de generador de datos personalizada. 

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig8-vehicle-telematics-prepare-sample-data-for-batch-processing.png) 

*Figura 8 - preparar datos de ejemplo de flujo de trabajo de procesamiento por lotes*

La canalización consta de .net personalizada ADF actividad, mostrar aquí:

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig9-vehicle-telematics-prepare-sample-data-pipeline.png) 

*Figura 9 - PrepareSampleDataPipeline*

Una vez que la canalización se ejecuta correctamente y conjunto de datos de "RawCarEventsTable" se marca como "Listo", un año patrimonio de vehículo simulada señales y diagnóstico se generan datos. Vea la siguiente carpeta y archivo creado en su cuenta de almacenamiento en el contenedor "connectedcar":

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig10-vehicle-telematics-prepare-sample-data-pipeline-output.png) 

*Figura 10 - salida PrepareSampleDataPipeline*

### <a name="references"></a>Referencias

[Azure SDK de concentrador de evento para recopilación de secuencia](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

[Capacidades de movimiento de datos de Azure datos fábrica](../data-factory/data-factory-data-movement-activities.md)
[Actividad de DotNet de generador de datos de Azure](../data-factory/data-factory-use-custom-activities.md)

[Solución de visual studio de actividad de Azure DotNet del generador de datos para preparar datos de ejemplo](http://go.microsoft.com/fwlink/?LinkId=717077) 


## <a name="partition-the-dataset"></a>Dividir el conjunto de datos

La señales de vehículo semiestructurados bruto y el conjunto de datos de diagnóstico se dividen en el paso de preparación de datos en un formato de año y mes. Esta división promueve consultar más eficaz y almacenamiento a largo plazo scalable habilitando sobre de errores de cuenta de un blob al siguiente como llene la primera cuenta. 

>[AZURE.NOTE] Este paso en la solución sólo es aplicable a proceso por lotes.

Entrada y salida administración de datos de datos:

- Los **datos de salida** (denominado *PartitionedCarEventsTable*) es mantenerse durante un largo período de tiempo en el formulario básico / "rawest" de los datos en el cliente "lago de datos". 
- Los **datos de entrada** a esta canalización normalmente se descartarán como los datos de salida tienen plena fidelidad la entrada - solo se almacena (particiones) mejor para su uso posterior.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig11-vehicle-telematics-partition-car-events-workflow.png)

*Ilustración 11: flujo de trabajo de eventos de coches de partición*

Los datos sin formato se dividen en una actividad de subárbol HDInsight "PartitionCarEventsPipeline". Los datos de ejemplo generados en el paso 1 para un año se dividen por año y mes. Las particiones se utilizan para generar señales de vehículo y datos de diagnóstico para cada mes (total 12 particiones) de un año. 

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig12-vehicle-telematics-partition-car-events-pipeline.png)

*Figura 12 - PartitionCarEventsPipeline*

El siguiente script de sección, denominado "partitioncarevents.hql", se utiliza para crear particiones y se encuentra en la carpeta "\demo\src\connectedcar\scripts" de la zip descargado. 


    SET hive.exec.dynamic.partition=true;
    SET hive.exec.dynamic.partition.mode = nonstrict;
    set hive.cli.print.header=true;

    DROP TABLE IF EXISTS RawCarEvents; 
    CREATE EXTERNAL TABLE RawCarEvents 
    (
                vin                             string,
                model                           string,
                timestamp                       string,
                outsidetemperature              string,
                enginetemperature               string,
                speed                           string,
                fuel                            string,
                engineoil                       string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position      string,
                parking_brake_status            string,
                headlamp_status                 string,
                brake_pedal_status              string,
                transmission_gear_position      string,
                ignition_status                 string,
                windshield_wiper_status         string,
                abs                             string,
                gendate                         string
                
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:RAWINPUT}'; 

    DROP TABLE IF EXISTS PartitionedCarEvents; 
    CREATE EXTERNAL TABLE PartitionedCarEvents 
    (
                vin                             string,
                model                           string,
                timestamp                       string,
                outsidetemperature              string,
                enginetemperature               string,
                speed                           string,
                fuel                            string,
                engineoil                       string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position      string,
                parking_brake_status            string,
                headlamp_status                 string,
                brake_pedal_status              string,
                transmission_gear_position      string,
                ignition_status                 string,
                windshield_wiper_status         string,
                abs                             string,
                gendate                         string
    ) partitioned by (YearNo int, MonthNo int) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDOUTPUT}';

    DROP TABLE IF EXISTS Stage_RawCarEvents; 
    CREATE TABLE IF NOT EXISTS Stage_RawCarEvents 
    (
                vin                             string,
                model                           string,
                timestamp                       string,
                outsidetemperature              string,
                enginetemperature               string,
                speed                           string,
                fuel                            string,
                engineoil                       string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position      string,
                parking_brake_status            string,
                headlamp_status                 string,
                brake_pedal_status              string,
                transmission_gear_position      string,
                ignition_status                 string,
                windshield_wiper_status         string,
                abs                             string,
                gendate                         string,
                YearNo                          int,
                MonthNo                         int) 
    ROW FORMAT delimited fields terminated by ',' LINES TERMINATED BY '10';

    INSERT OVERWRITE TABLE Stage_RawCarEvents
    SELECT
        vin,            
        model,
        timestamp,
        outsidetemperature,
        enginetemperature,
        speed,
        fuel,
        engineoil,
        tirepressure,
        odometer,
        city,
        accelerator_pedal_position,
        parking_brake_status,
        headlamp_status,
        brake_pedal_status,
        transmission_gear_position,
        ignition_status,
        windshield_wiper_status,
        abs,
        gendate,
        Year(gendate),
        Month(gendate)

    FROM RawCarEvents WHERE Year(gendate) = ${hiveconf:Year} AND Month(gendate) = ${hiveconf:Month}; 

    INSERT OVERWRITE TABLE PartitionedCarEvents PARTITION(YearNo, MonthNo) 
    SELECT
        vin,            
        model,
        timestamp,
        outsidetemperature,
        enginetemperature,
        speed,
        fuel,
        engineoil,
        tirepressure,
        odometer,
        city,
        accelerator_pedal_position,
        parking_brake_status,
        headlamp_status,
        brake_pedal_status,
        transmission_gear_position,
        ignition_status,
        windshield_wiper_status,
        abs,
        gendate,
        YearNo,
        MonthNo
    FROM Stage_RawCarEvents WHERE YearNo = ${hiveconf:Year} AND MonthNo = ${hiveconf:Month};

*Figura 13 - PartitionConnectedCarEvents subárbol Script*

Una vez que se ejecuta la canalización correctamente, verá las siguientes particiones generadas en su cuenta de almacenamiento en el contenedor "connectedcar".

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig14-vehicle-telematics-partitioned-output.png)

*Figura 14 - salida dividida*

Los datos ahora está optimizado, está listo para el procesamiento de más para obtener perspectivas lote enriquecidos y más manejable. 

## <a name="data-analysis"></a>Análisis de datos

En esta sección, vea cómo combinar análisis de secuencia de Azure, el aprendizaje Azure, generador de datos de Azure y Azure HDInsight para enriquecido análisis avanzados sobre el mantenimiento de vehículo y conducción hábitos. Hay tres subsecciones aquí:

1.  **Aprendizaje**: este apartado contiene información sobre el ensayo de detección de anomalías que hemos utilizado en esta solución predecir vehículos que requieren mantenimiento de mantenimiento y que requieren recupera debido a problemas de seguridad.
2.  **Análisis en tiempo real**: este apartado contiene información sobre el análisis en tiempo real con el lenguaje de consulta de análisis de secuencia y el ensayo el aprendizaje en tiempo real mediante una aplicación personalizada en marcha.
3.  **Análisis por lotes**: este apartado contiene información sobre la transformación y el procesamiento de los datos del lote con HDInsight de Azure y Azure máquina aprendizaje operationalized generador de datos de Azure.

### <a name="machine-learning"></a>Aprendizaje de equipo

Nuestro objetivo es predecir los vehículos que requieren mantenimiento o la recuperación en función de ciertas estadísticas de estado. Asegúrese de lo siguiente

- Si una de las siguientes tres condiciones es verdadera, los vehículos requieren **Mantenimiento de servicio**:
    - Presión neumático es baja
    - Motor oil nivel es bajo
    - Temperatura motor es alta

- Si una de las siguientes condiciones es verdadera, los vehículos podrán tiene un **problema de seguridad** y **recuperación**se requiere:
    - Temperatura del motor es alta pero temperatura exterior es baja
    - Temperatura del motor es baja pero temperatura exterior es alta

En función de los requisitos anteriores, hemos creado dos modelos independientes para detectar anomalías, uno para la detección de mantenimiento de vehículo y otro para la detección de recuperación de vehículo. En ambos estos modelos, se utiliza el algoritmo de análisis de componente Principal (PCA) integrados para la detección de anomalías. 

**Modelo de detección de mantenimiento**

Si uno de los tres indicadores de presión neumático, oil motor o temperatura motor - satisface su respectivo condición, el modelo de detección de mantenimiento informes una anomalía. Como resultado, solo es necesario tener en cuenta estos tres variables en generar el modelo. En nuestra prueba en aprendizaje de máquina de Azure, usamos primero un módulo de **Seleccionar columnas de conjunto de datos** para extraer estos tres variables. A continuación, usamos el módulo de detección de anomalías basado en PCA para generar el modelo de detección de anomalías. 

Análisis de componente principal (PCA) es una técnica establecida en aprendizaje de equipo que se puede aplicar a la selección de características, clasificación y detección de anomalías. PCA convierte un conjunto de caja que contiene las variables relacionadas posiblemente, en un conjunto de valores denominados componentes principales. La idea clave de modelado de PCA es datos de proyecto en un espacio inferior multidimensionales para características y anomalías pueden identificarse más fácilmente.
 
Para cada nueva entrada al modelo de detección, el detector de anomalías primero calcula su proyección en la eigenvectors y, a continuación, calcula el error reconstruir normalizado. Este error normalizado es la puntuación anomalías. Cuanto mayor sea el error, más irregulares es la instancia. 

En el problema de detección de mantenimiento, cada registro puede considerarse un punto en un espacio de 3 dimensiones definida por presión neumático, oil motor y temperatura motor coordenadas. Para capturar estas anomalías, nos podemos proyectar los datos originales en el espacio de 3 dimensiones en un espacio de 2 dimensiones usando PCA. Por lo tanto, se establece el parámetro número de componentes que utilice en PCA como 2. Este parámetro reproduce una función importante en la aplicación de detección de anomalías basado en PCA. Después de la proyección de los datos con PCA, podemos identificar estas anomalías más fácilmente.

**Recuperar el modelo de detección de anomalías** En el modelo de detección de anomalías de recuperación, usamos seleccionar columnas de conjunto de datos y basado en PCA anomalías módulos de detección de manera similar. Más concretamente, nos extraer tres variables - temperatura del motor, la temperatura exterior y la velocidad - uso del módulo de **Seleccionar columnas de conjunto de datos** . También incluimos la variable de velocidad desde la temperatura motor normalmente está asociada a la velocidad. A continuación usamos módulo de detección de anomalías basado en PCA para proyectar los datos desde el espacio de 3 dimensiones en un espacio 2 dimensiones. Se cumplen los criterios de recuperación y lo vehículo requiere recuperación cuando temperatura motor y temperatura exterior altamente negativo están relacionadas. Usa el algoritmo de detección de anomalías basado en PCA, podemos capturar las anomalías después de realizar PCA. 

Cualquier modelo de aprendizaje, es necesario utilizar datos normales, que no requieren mantenimiento o recuperar los datos de entrada para entrenar el modelo de detección de anomalías basado en PCA. En la misma puntuación, usamos el modelo de detección de anomalías cualificados para detectar si el vehículo requiere mantenimiento o recuperar o no. 


### <a name="real-time-analysis"></a>Análisis en tiempo real

La siguiente consulta de SQL de análisis de secuencia se usa para obtener el promedio de todos los parámetros de vehículo importantes como velocidad del vehículo, el nivel de combustible, temperatura del motor, cuentakilómetros, presión neumático, nivel del motor oil y otros usuarios. Los promedios se usan para detectar anomalías, emita alertas y determinar las condiciones generales de mantenimiento de vehículos ofrecidos en región específica y, a continuación, se relacionar a segmentos. 

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig15-vehicle-telematics-stream-analytics-query-for-real-time-processing.png)

Figura 15-consulta de análisis de secuencia de procesamiento en tiempo real

Todos los promedios se calculan durante un TumblingWindow de 3 segundos. En este caso estamos usando TubmlingWindow puesto que necesitamos intervalos de tiempo contiguos y no superpuestos. 

Para obtener más información acerca de todas las capacidades de "Ventanas" en el análisis de secuencia de Azure, haga clic en [ventanas (análisis de secuencia de Azure)](https://msdn.microsoft.com/library/azure/dn835019.aspx).

**Predicción en tiempo real**

Una aplicación se incluye como parte de la solución que controle el modelo de aprendizaje en tiempo real. Esta aplicación denominada "RealTimeDashboardApp" se crea y está configurada como parte de la implementación de soluciones. La aplicación realiza lo siguiente:

1.  Escucha una instancia de concentrador de evento donde el análisis de secuencia está publicando continuamente los eventos en un patrón. ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig16-vehicle-telematics-stream-analytics-query-for-publishing.png)*Figura 16: consulta de análisis de secuencia para publicar los datos en un resultado de la instancia de concentrador de evento* 

2.  Para todos los eventos que recibe de esta aplicación: 

    - Procesa los datos con Machine aprendizaje solicitud respuesta puntuación (RR) extremo. El extremo de RR se publica automáticamente como parte de la implementación.
    - El resultado de RR se publica en un conjunto de datos de PowerBI con la API de inserción.

Este modelo también es aplicable a escenarios en los que desea integrar una aplicación de línea de negocio (LoB) con el flujo de análisis en tiempo real para escenarios como alertas, notificaciones y mensajería.

Haga clic en [Descargar RealtimeDashboardApp](http://go.microsoft.com/fwlink/?LinkId=717078) para descargar la solución de RealtimeDashboardApp Visual Studio para personalizaciones. 

**Para ejecutar la aplicación de escritorio en tiempo real**

1.  Haga clic en el nodo PowerBI en la vista de diagrama y haga clic en el vínculo "Descargar la aplicación de escritorio de en tiempo real" en el panel de propiedades. ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig17-vehicle-telematics-powerbi-dashboard-setup.png)*Figura 17: las instrucciones de configuración del panel de PowerBI*
2.  Extraer y guardar localmente ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig18-vehicle-telematics-realtimedashboardapp-folder.png) *figura 18: RealtimeDashboardApp carpeta*
3.  Ejecutar la aplicación RealtimeDashboardApp.exe
4.  Proporcione credenciales válidas de Power BI, inicio de sesión y haga clic en Aceptar ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19a-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png)![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19b-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png) 

*Figura 19: RealtimeDashboardApp: Iniciar sesión en PowerBI*

>[AZURE.NOTE] Si desea vaciar el conjunto de datos PowerBI, ejecute el RealtimeDashboardApp con el parámetro "flushdata": 

    RealtimeDashboardApp.exe -flushdata

### <a name="batch-analysis"></a>Análisis por lotes

El objetivo es mostrar cómo motores de Contoso utiliza las capacidades de cálculo Azure aprovechar datos grandes para obtener perspectivas enriquecidos en impulsar trama, el comportamiento de uso y la salud de vehículo. Esto permite:

- Mejorar la experiencia del cliente y que sea más económico proporcionando perspectivas en coche hábitos y comportamientos de conducción eficaz de combustible
- Obtenga información sobre proactiva acerca de los clientes y sus patrones de conducción rigen decisiones empresariales y proporcionar la mejor en productos y servicios

En esta solución, nuestro destino son las medidas siguientes:

1.  **Comportamiento de conducción agresivo**: identifica la tendencia de los modelos, ubicaciones, conducción condiciones y hora del año para obtener información sobre patrones de conducción agresivas. Motor de Contoso puede usar estas perspectivas para campañas de marketing, conducción nuevas características personalizadas y seguros basados en uso.
2.  **Comportamiento de conducción eficaz de combustible**: identifica la tendencia de los modelos, ubicaciones, conducción condiciones y hora del año para obtener información sobre patrones de conducción eficaz de combustible. Motor de Contoso puede usar estas perspectivas para las campañas de marketing conducción nuevas características y proactiva informar a los controladores de costos efectivas y entorno hábitos de conducción descriptivos. 
3.  **Recuperar modelos**: identifica modelos que requieren recupera por el equipo de detección de anomalías ensayo de aprendizaje en marcha

Echemos un vistazo a los detalles de cada una de estas métricas


**Trama de conducción agresiva**

Los datos de diagnósticos y señales de vehículo divididas se procesan en la canalización denominada "AggresiveDrivingPatternPipeline" utilizando subárbol para determinar los modelos, ubicación, vehículo, condiciones de conducción y otros parámetros que se comporta el patrón de conducción agresiva.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig20-vehicle-telematics-aggressive-driving-pattern.png) 
*Figura 20 – dinámico conducción flujo de trabajo de trama*

La secuencia de comandos de la sección denominada "aggresivedriving.hql" usado para analizar tan agresiva conducción trama de condición se encuentra en la carpeta "\demo\src\connectedcar\scripts" de la zip descargado. 

    DROP TABLE IF EXISTS PartitionedCarEvents; 
    CREATE EXTERNAL TABLE PartitionedCarEvents
    (
                vin                             string,
                model                           string,
                timestamp                       string,
                outsidetemperature              string,
                enginetemperature               string,
                speed                           string,
                fuel                            string,
                engineoil                       string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position      string,
                parking_brake_status            string,
                headlamp_status                 string,
                brake_pedal_status              string,
                transmission_gear_position      string,
                ignition_status                 string,
                windshield_wiper_status         string,
                abs                             string,
                gendate                         string
                                
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDINPUT}';

    DROP TABLE IF EXISTS CarEventsAggresive; 
    CREATE EXTERNAL TABLE CarEventsAggresive
    (
                vin                         string, 
                model                       string,
                timestamp                   string,
                city                        string,
                speed                       string,
                transmission_gear_position  string,
                brake_pedal_status          string,
                Year                        string,
                Month                       string
                                
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:AGGRESIVEOUTPUT}';



    INSERT OVERWRITE TABLE CarEventsAggresive
    select
    vin,
    model,
    timestamp,
    city,
    speed,
    transmission_gear_position,
    brake_pedal_status,
    "${hiveconf:Year}" as Year,
    "${hiveconf:Month}" as Month
    from PartitionedCarEvents
    where transmission_gear_position IN ('fourth', 'fifth', 'sixth', 'seventh', 'eight') AND brake_pedal_status = '1' AND speed >= '50'

*Figura 21-dinámico conducción consultas de la sección de modelo*

Utiliza la combinación de posición de engranaje de transmisión del vehículo, estado del pedal de freno y velocidad para detectar comportamientos de conducción sentido agresiva en función de frenado trama a alta velocidad. 

Una vez que se ejecuta la canalización correctamente, verá las siguientes particiones generadas en su cuenta de almacenamiento en el contenedor "connectedcar".

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig22-vehicle-telematics-aggressive-driving-pattern-output.png) 

*Figura 22 – AggressiveDrivingPatternPipeline resultados*


**Trama de conducción eficaz de combustible**

Los datos de diagnósticos y señales de vehículo divididas se procesan en la canalización denominada "FuelEfficientDrivingPatternPipeline". Sección se utiliza para determinar los modelos, ubicación, vehículo, condiciones de conducción y otras propiedades que muestren modelo conducción eficaz de combustible.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig23-vehicle-telematics-fuel-efficient-driving-pattern.png) 

*Figura 23: combustible eficaz conducción trama flujo de trabajo*

La secuencia de comandos de la sección denominada "fuelefficientdriving.hql" usado para analizar tan agresiva conducción trama de condición se encuentra en la carpeta "\demo\src\connectedcar\scripts" de la zip descargado. 

    DROP TABLE IF EXISTS PartitionedCarEvents; 
    CREATE EXTERNAL TABLE PartitionedCarEvents
    (
                vin                             string,
                model                           string,
                timestamp                       string,
                outsidetemperature              string,
                enginetemperature               string,
                speed                           string,
                fuel                            string,
                engineoil                       string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position      string,
                parking_brake_status            string,
                headlamp_status                 string,
                brake_pedal_status              string,
                transmission_gear_position      string,
                ignition_status                 string,
                windshield_wiper_status         string,
                abs                             string,
                gendate                         string
                                
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDINPUT}';

    DROP TABLE IF EXISTS FuelEfficientDriving; 
    CREATE EXTERNAL TABLE FuelEfficientDriving
    (
                vin                         string, 
                model                       string,
                city                        string,
                speed                       string,
                transmission_gear_position  string,                
                brake_pedal_status          string,            
                accelerator_pedal_position  string,                             
                Year                        string,
                Month                       string
                                
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:FUELEFFICIENTOUTPUT}';



    INSERT OVERWRITE TABLE FuelEfficientDriving
    select
    vin,
    model,
    city,
    speed,
    transmission_gear_position,
    brake_pedal_status,
    accelerator_pedal_position,
    "${hiveconf:Year}" as Year,
    "${hiveconf:Month}" as Month
    from PartitionedCarEvents
    where transmission_gear_position IN ('fourth', 'fifth', 'sixth', 'seventh', 'eight') AND parking_brake_status = '0' AND brake_pedal_status = '0' AND speed <= '60' AND accelerator_pedal_position >= '50'


*Figura 24: combustible conducción trama subárbol consulta eficaz*

Utiliza la combinación de posición de engranaje de transmisión del vehículo, estado del pedal de freno, la velocidad y la aceleración pedal posición para detectar comportamientos de conducción eficaz de combustible en función de aceleración, frenado y velocidad de patrones. 

Una vez que se ejecuta la canalización correctamente, verá las siguientes particiones generadas en su cuenta de almacenamiento en el contenedor "connectedcar".

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig25-vehicle-telematics-fuel-efficient-driving-pattern-output.png) 

*Figura 25 – FuelEfficientDrivingPatternPipeline resultados*


**Recuperar predicciones**

El equipo de ensayo de aprendizaje es aprovisionado y publicar como un servicio web como parte de la implementación de soluciones. El lote de puntuación punto final se aprovecha este flujo de trabajo, registrado como un servicio de datos vinculado de fábrica y operationalized utilizando datos fábrica lote puntuación actividad.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig26-vehicle-telematics-machine-learning-endpoint.png) 

*Figura 26: aprendizaje extremo registrada como un servicio en el generador de datos vinculado*

El servicio registrado vinculado se usa en la DetectAnomalyPipeline a la puntuación de los datos mediante el modelo de detección de anomalías. 

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig27-vehicle-telematics-aml-batch-scoring.png) 

*Figura 27 – Azure máquina aprendizaje lote puntuación actividad de generador de datos* 

Hay algunos pasos que se realizan en esta canalización de preparación de datos para que se puede operationalized con el lote de servicio web de resultados. 

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig28-vehicle-telematics-pipeline-predicting-recalls.png) 

*Figura 28 – DetectAnomalyPipeline para predecir vehículos que requieren recupera* 

Una vez completada la puntuación, una actividad de HDInsight se usa para procesar y agregar los datos que se clasifica como anomalías por el modelo con una calificación de probabilidad de 0,60 o superior.

    DROP TABLE IF EXISTS CarEventsAnomaly; 
    CREATE EXTERNAL TABLE CarEventsAnomaly 
    (
                vin                         string,
                model                       string,
                gendate                     string,
                outsidetemperature          string,
                enginetemperature           string,
                speed                       string,
                fuel                        string,
                engineoil                   string,
                tirepressure                string,
                odometer                    string,
                city                        string,
                accelerator_pedal_position  string,
                parking_brake_status        string,
                headlamp_status             string,
                brake_pedal_status          string,
                transmission_gear_position  string,
                ignition_status             string,
                windshield_wiper_status     string,
                abs                         string,
                maintenanceLabel            string,
                maintenanceProbability      string,
                RecallLabel                 string,
                RecallProbability           string
                                
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:ANOMALYOUTPUT}';

    DROP TABLE IF EXISTS RecallModel; 
    CREATE EXTERNAL TABLE RecallModel 
    (

                vin                         string,
                model                       string,
                city                        string,
                outsidetemperature          string,
                enginetemperature           string,
                speed                       string,
                Year                        string,
                Month                       string              
                                
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:RECALLMODELOUTPUT}';

    INSERT OVERWRITE TABLE RecallModel
    select
    vin,
    model,
    city,
    outsidetemperature,
    enginetemperature,
    speed,
    "${hiveconf:Year}" as Year,
    "${hiveconf:Month}" as Month
    from CarEventsAnomaly
    where RecallLabel = '1' AND RecallProbability >= '0.60'


Una vez que se ejecuta la canalización correctamente, verá las siguientes particiones generadas en su cuenta de almacenamiento en el contenedor "connectedcar".

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig30-vehicle-telematics-detect-anamoly-pipeline-output.png) 

*Figura 30 figura 30 DetectAnomalyPipeline resultados*


## <a name="publish"></a>Publicar

### <a name="real-time-analysis"></a>Análisis en tiempo real

Una de las consultas en el trabajo de análisis de secuencia publica los eventos al resultado de un instancia de concentrador de evento. 

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig31-vehicle-telematics-stream-analytics-job-publishes-output-event-hub.png)

*Figura 31: flujo de trabajo de análisis que se publica en un resultado de la instancia de concentrador de evento*

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig32-vehicle-telematics-stream-analytics-query-publish-output-event-hub.png)

*Figura 32-secuencia de análisis de la consulta para publicar en los resultados de la instancia de concentrador de evento*

Esta secuencia de eventos consume el RealTimeDashboardApp incluido en la solución. Esta aplicación aprovecha el servicio web de aprendizaje de petición de respuesta del equipo de puntuación en tiempo real y los datos resultantes publica en un conjunto de datos de PowerBI para su consumo. 

### <a name="batch-analysis"></a>Análisis por lotes

Los resultados del lote y procesamiento en tiempo real se publican en las tablas de base de datos de SQL Azure para su consumo. El servidor de SQL Azure, base de datos y las tablas se crean automáticamente como parte de la secuencia de comandos de instalación. 

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig33-vehicle-telematics-batch-processing-results-copy-to-data-mart.png)

*Figura 33: copia de los resultados al flujo de trabajo de puesto de datos de procesamiento por lotes*

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig34-vehicle-telematics-stream-analytics-job-publishes-to-data-mart.png)

*Figura 34: flujo de trabajo de análisis publica en puesto de datos*

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig35-vehicle-telematics-data-mart-setting-in-stream-analytics-job.png)

*Figura 35: configuración de trabajo de análisis de secuencia de puesto de datos*


## <a name="consume"></a>Consumir

Power BI le ofrece esta solución un panel enriquecido para datos en tiempo real y visualizaciones de análisis predictiva. 

Haga clic aquí para obtener instrucciones detalladas sobre cómo configurar los informes de PowerBI y el escritorio. El panel final es similar a esta:

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig36-vehicle-telematics-powerbi-dashboard.png)

*Figura 36 - PowerBI paneles*

## <a name="summary"></a>Resumen

Este documento contiene un desglose detallada de la solución de análisis de telemetría de vehículo. Esto presenta un modelo de arquitectura de lambda para en tiempo real y por lotes análisis con predicciones y acciones. Este patrón se aplica a una amplia gama de casos de uso que requieren la ruta de acceso rápido (en tiempo real) y el análisis de ruta fría (lote). 

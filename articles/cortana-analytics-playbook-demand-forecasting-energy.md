<properties
    pageTitle="Guía de plantilla de solución de inteligencia de Cortana para realizar una previsión de demanda de energía | Microsoft Azure"
    description="Una plantilla de solución con Microsoft Cortana Intelligence que ayuda a petición de una compañía de la utilidad de energía de previsión."
    services="cortana-analytics"
    documentationCenter=""
    authors="ilanr9"
    manager="ilanr9"
    editor="yijichen"/>

<tags
    ms.service="cortana-analytics"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="01/24/2016"
    ms.author="ilanr9;yijichen;garye"/>

# <a name="cortana-intelligence-solution-template-playbook-for-demand-forecasting-of-energy"></a>Guía de plantilla de solución de inteligencia de Cortana para realizar una previsión de demanda de energía  

## <a name="executive-summary"></a>Resumen ejecutivo  

En los últimos años, Internet de cosas (IoT), las fuentes de energía alternativo y datos grandes se combinan para crear oportunidades grandes en el dominio de utilidad y de energía. Al mismo tiempo, la utilidad y el sector de la energía todo han visto consumo acoplar consumidores requieren mejores formas de controlar el uso de energía. Por lo tanto, las empresas de cuadrícula inteligente y utilidad son necesita para innovar y renovar a sí mismos. Además, muchos cuadrículas de utilidad y potencia se vuelven obsoleta y muy costosas mantener y administrar. Durante el último año, el equipo ha estado trabajando en un número de compromisos dentro del dominio de energía. Durante estos compromisos, hemos detectado muchos casos en los que la utilidades o proveedores de software independientes (proveedores de Software independientes) han estado viendo en la previsión de demanda de energía futuras. Estas previsiones desempeñan una función importante en su empresa actual y futuro y se han convertido en la base de varios casos de uso. Incluyen la previsión de carga power corto y a largo plazo, cotización, equilibrio de carga, optimización de cuadrícula etcetera. Big data y métodos de análisis avanzadas (g) como equipo aprendizaje (v) son las razones fundamentales para producir previsiones exactos y fiables.  

En esta guía, hemos reunido en la empresa y analítica instrucciones necesarias para un desarrollo correcto y solución de previsión de implementación de petición de energía. Estas directrices propuestas pueden ayudar a utilidades, científicos de datos e ingenieros de datos en el establecimiento de soluciones totalmente operationalized, basado en la nube, realizar una previsión de demanda. Para las compañías que están empezando sus datos grandes y viaje de análisis avanzadas, esta solución puede representar el envío inicial en su estrategia de cuadrícula inteligente a largo plazo.

>[AZURE.TIP] Para descargar un diagrama que proporciona una introducción a la arquitectura de esta plantilla, consulte [arquitectura de la plantilla de solución de inteligencia de Cortana para realizar una previsión de demanda de energía](cortana-analytics-architecture-demand-forecasting-energy.md).  

## <a name="overview"></a>Información general  

Este documento trata sobre la empresa, datos y aspectos técnicos de utilizar Cortana inteligencia y en particular Azure máquina aprendizaje (AML) para la aplicación y la implementación de soluciones de previsión de energía. El documento está compuesto por tres partes principales:  

1. Descripción de la empresa  
2. Descripción de datos  
3. Implementación técnica

El elemento de **Conocimiento de la empresa** describe el aspecto de empresas deben comprender y tener en cuenta antes de tomar una decisión de inversión. Se explica cómo calificar el problema empresarial disponible para asegurarse de que el análisis de predicción y aprendizaje de máquina son realmente eficaz y aplicable. Aún más el documento explica los conceptos básicos de aprendizaje de equipo y cómo se utiliza para solucionar problemas de previsión de energía. Describe los requisitos previos y los criterios de calificación de un caso de uso. Algunos ejemplo usar casos y rentabilidad también se proporcionan escenarios.

Datos están el ingrediente principal cualquier solución de aprendizaje del equipo. El elemento de **Descripción de datos** de este documento trata sobre algunos aspectos importantes de los datos. Describe el tipo de datos que se necesitan para realizar una previsión de energía, requisitos de calidad de datos y normalmente existen qué orígenes de datos. También se explica cómo los datos sin formato se usan para preparar las características de datos que realmente conducir el elemento de modelado.

La tercera parte del documento trata sobre el aspecto de **Implementación técnica** de una solución. Ingeniería de la característica y modelado son el núcleo del proceso de ciencias de datos y, por tanto, que se describen en detalle. Cubra el concepto de servicios web, que son un vehículo importante para su implementación de nube de soluciones de análisis de predicción. También, describimos una arquitectura típica de una solución de operationalized-to-end.

Además, en el documento se incluye material de referencia que puede usar para comprender aún más el dominio y tecnología.

Es importante que tenga en cuenta no desea cubrir en este documento, el proceso de ciencias datos más profundo, sus aspectos técnicos y matemáticos. Estos detalles se pueden encontrar en la [documentación de Azure m](http://azure.microsoft.com/services/machine-learning/) y [blogs](http://blogs.microsoft.com/blog/tag/azure-machine-learning/).

### <a name="target-audience"></a>Audiencia de destino   
Los destinatarios de este documento son business y personal técnico que le gustaría conocer y comprensión de aprendizaje de máquina basada en soluciones y cómo estos se utilizan específicamente dentro del dominio de previsión de energía.

Científicos de datos también pueden beneficiarse de la lectura de este documento para comprender mejor el proceso de alto nivel que controla la implementación de una solución de previsión de energía. En este contexto también se pueden utilizar para establecer una buena base y punto de partida para obtener más información detallada y avanzadas de material.

### <a name="industry-trends"></a>Tendencias de la industria  
En los últimos años, IoT, las fuentes de energía alternativo y datos grandes se combinan para crear oportunidades grandes en el espacio de utilidad y de energía. Al mismo tiempo, la utilidad y los sectores de energía todo han visto consumo acoplar consumidores requieren mejores formas de controlar el uso de energía.

Muchas utilidad y las empresas de energía inteligente se ha pionero en la [cuadrícula inteligente](https://en.wikipedia.org/wiki/Smart_grid) implementando un número de uso casos que hacen uso de los datos generados por la cuadrícula. Muchos de los casos de uso giran en torno a las características inherentes de electricidad: no puede ser acumulado ni reservar almacenado como inventario. Por lo tanto, se produce lo que debe usarse. Utilidades que desean transformar en más eficaz que necesite consumo de energía de previsión simplemente debido a que se les proporcionan mayor capacidad de **Saldo suministro y demanda**, evitando pérdidas de energía, **reducir emisiones de gas de efecto invernadero**y de coste de control.

Cuando se trata de costos, hay otro aspecto importante, que es el precio. Nuevas capacidades de comercio power entre utilidades han en gran necesidad de **previsión demanda futura y precio futuro de electricidad**. Esto puede ayudar a las empresas determinar sus volúmenes de producción.

Cuando se usa la palabra 'inteligente', nos referimos a una cuadrícula que puede obtener y luego realice predicciones realmente. Pueden prever estacionales cambios en consumo, así como **prever situaciones de sobrecarga temporal y ajustar automáticamente para el mismo**. Mediante el control de forma remota consumo (con la Ayuda de estos medidores inteligentes), pueden corregirse situaciones de sobrecarga localizados. **Primero predecir y, a continuación, se insertan**, la cuadrícula pone mejor con el tiempo.

Para el resto de este documento nos centraremos en una familia de casos de uso que abarcan previsión de futuro, específica a corto plazo y a largo plazo petición de energía. Estamos trabajando en estas áreas unos meses y ha obtenido algunos conocimientos y aptitudes que nos permiten producir resultados de calificación de la industria. Otros usos cubrirá también en el documento en un futuro próximo.

## <a name="business-understanding"></a>Descripción de la empresa

### <a name="business-goals"></a>Objetivos empresariales
Es el objetivo de **Demostración de energía** demostrar un típicos analíticas de predicción y aprendizaje solución que puede implementar en un marco de tiempo muy breve. Específicamente, nos centraremos actual es acerca de cómo habilitar soluciones de previsión de demanda de energía para que puedan realizan rápidamente su valor para el negocio y aprovechar al. La información de esta guía puede ayudar a los clientes llevar a cabo los siguientes objetivos:
-   Solución basada en poco tiempo al valor de aprendizaje de equipo
-   Caso a otros casos de uso o a un ámbito más amplio en función de sus necesidades comerciales de uso de la capacidad para expandir una prueba piloto
-   Obtener rápidamente la información de producto de conjunto de aplicaciones de inteligencia de Cortana

Teniendo en cuenta estos objetivos, esta guía pretende exposición de la empresa y conocimientos técnicos que le ayudarán a lograr estos objetivos.

### <a name="power-load-and-demand-forecasting"></a>Carga Power y realizar una previsión de demanda
En el sector de energía, puede haber muchas formas en demandas previsión puede ayudar a resolver problemas empresariales relevantes. De hecho, realizar una previsión de demanda se puede considerar la base de muchos casos de uso principales de la industria. En general, se tenga en cuenta dos tipos de previsión de la demanda de energía: corto y a largo plazo. Cada uno de ellos puede servir para un propósito diferente y utilizar un enfoque diferente. La diferencia principal entre los dos es la previsión horizonte, lo que significa que el intervalo de tiempo en el futuro para el que se debería de previsión.

#### <a name="short-term-load-forecasting"></a>Corto plazo carga previsión
En el contexto de la demanda de energía, corto plazo cargar previsión (STLF) se define como la carga agregada que se prevé en un futuro próximo en distintas partes de la cuadrícula (o como un todo). En este contexto, a corto plazo se define como horizonte comprendido entre 1 hora a 24 horas. En algunos casos, también es posible un horizonte de 48 horas. Por lo tanto, STLF es muy común en el caso de uso de la cuadrícula. Estos son algunos ejemplos de STLF controlados por casos de uso:
-   Suministro y demanda equilibrio
-   Compatibilidad con comercial de energía
-   Efectúen de mercado (precio de energía de configuración)
-   Optimización de operaciones de cuadrícula
-   [Petición respuesta](https://en.wikipedia.org/wiki/Demand_response)
-   Espacio máximo petición previsión
-   Administración de propuestas lado
-   Equilibrio de carga y la prevención de sobrecarga
-   Largo plazo previsión de carga
-   Detección de errores y anomalías
-   Reducción o redistribución de recursos asignadas 

Modelo STLF principalmente se basan en el pasado cercano (último día o semana) datos de consumo y uso previsión temperatura como una predicción importante. Obtención de temperatura precisa de previsión de hora y el 24 horas será menor de un reto días ahora. Estos modelos son menos sensibles a estacionales patrones o tendencias de consumo a largo plazo.

Soluciones SLTF también están probable que se generan gran volumen de llamadas de predicción (solicitudes de servicio) desde el se está llamando cada hora y, en algunos casos incluso con mayor frecuencia. También es muy común para ver implantación donde cada subestación individual o transformador se representa como un modelo independiente y, por tanto, están aún más el volumen de solicitudes de predicción.

#### <a name="long-term-load-forecasting"></a>Largo plazo previsión de carga
El objetivo de largo plazo carga previsión (LTLF) es petición de energía de previsión con un horizonte comprendido entre 1 semana a varios meses (y en algunos casos de un número de años). Este intervalo de horizonte principalmente es aplicable para planear y casos de uso de la inversión.

Escenarios a largo plazo, es importante tener datos de alta calidad que cubre un período de varios años (mínimos 3 años). Estos modelos normalmente extraerán tramas estacionalidad desde los datos históricos y asegúrese de usar de predicators externos como patrones de tiempo y clima.

Es importante tener en cuenta que más el horizonte de previsión, menos precisa puede ser la previsión. Por lo tanto, es importante generar algunos intervalos de confianza junto con la previsión real que permita hombre al factor de variación posible en el proceso de planeación.

Dado que el escenario de consumo para LTLF principalmente está planificando, se puede prever cuánto volúmenes de predicción inferiores (en comparación con STLF). Vemos haría normalmente estas predicciones incrustadas en herramientas de visualización, como Excel o PowerBI y se invoca manualmente por el usuario.

### <a name="short-term-vs-long-term-prediction"></a>Predicción de corto plazo frente a largo plazo
La siguiente tabla compara STLF y LTLF con respecto a los atributos más importantes:

|Atributo|Carga a corto plazo de previsión|Cargar la previsión a largo plazo|
|---|---|---|
|Horizonte de previsión|Desde 1 hora a 48 horas|De 1 a 6 meses o más|
|Detalle de datos|Por hora|Cada hora o diariamente|
|Usos típicos|<ul><li>Equilibrio de demanda/suministro</li><li>Seleccione la previsión de horas</li><li>Petición respuesta</li></ul>|<ul><li>Planificación a largo plazo</li><li>Planificación de activos de cuadrícula</li><li>Planeación de recursos</li></ul>|
|Indicadores de típica|<ul><li>Día o semana</li><li>Hora del día</li><li>Temperatura por hora</li></ul>|<ul><li>Mes del año</li><li>Día del mes</li><li>Clima y temperatura a largo plazo</li></ul>|
|Rango de datos históricos|Valor de dos o tres años de datos|Años cinco a 10 de datos|
|Precisión típica|MAPE * del 5% o inferior|MAPE * de 25% o inferior|
|Frecuencia de previsión|Fabrican cada hora o cada 24 horas|Producido una vez mensual, trimestral o anual|
\*[MAPE](https://en.wikipedia.org/wiki/Mean_absolute_percentage_error) : Error de porcentaje promedio Media

Como puede verse de esta tabla, es muy importante distinguir entre el corto y largo plazo previsión escenarios, ya que representan diferentes necesidades empresariales y puede tener diferentes implementación y patrones de consumo.

### <a name="example-use-case-1-esmart-systems--overload-optimization"></a>Caso de uso del ejemplo 1: eSmart sistemas: optimización de sobrecarga
Una función importante de una [cuadrícula inteligente](https://en.wikipedia.org/wiki/Smart_grid) es dinámicamente y constantemente optimizar y ajuste de los patrones de consumo cambiantes. Consumo de energía puede verse afectado por los cambios a corto plazo que principalmente son causados por las variaciones de temperatura (*por ejemplo*, más potencia se utiliza para calefacción o condición de aire). Al mismo tiempo, consumo de energía también está influido por tendencias a largo plazo. Estos pueden incluir efectos estacionalidad, festivos, crecimiento a largo plazo de consumo e incluso económicos factores como el índice de consumidor, el precio y PIB.

En este caso de uso, [eSmart](http://www.esmartsystems.com/) deseaba implementar una solución basada en nube que permite predecir la tendencia de una situación de sobrecarga en cualquier subestación determinado de la cuadrícula. En particular, eSmart deseaba identificar subestaciones que es probable que sobrecarga en una hora, por lo que se puede tomar una acción inmediata para evitar o resolver esta situación.

Un precisa y efectuar rápidamente predicción requiere la implementación de tres modelos predictivos:
-   Cuánto tiempo modelo de término que permite la previsión de consumo de energía en cada subestación durante la siguiente algunas semanas o meses
-   Modelo de corto plazo que permite la predicción de situación de sobrecarga en un determinado subestación durante la próxima hora
-   Modelo de temperatura que proporciona la previsión de temperatura futuro sobre varios escenarios

El objetivo del modelo a largo plazo es clasificar las subestaciones por su tendencia a sobrecarga (dada su capacidad de transmisión power) durante la próxima semana o mes. Esto permite la creación de una lista breve de subestaciones que podría servir como una entrada de predicción a corto plazo. Como temperatura una predicción importante para el modelo a largo plazo, es necesario generar las temperaturas escenario múltiples constantemente y fuente de como entrada en el modelo a largo plazo. A continuación, se invoca la previsión a corto plazo para predecir qué subestación es probable que sobrecarga de hora.

Los modelos de corto y largo plazo se implementan individualmente por cada subestación. Por lo tanto, la ejecución de estos modelos de práctica requiere orquestación extensiva. Para obtener una mayor precisión de predicción a corto plazo, un modelo más detallado está dedicado para cada hora del día. Todos estos modelos se ejecutan cada hora y finalización la ejecución dentro de unos minutos para permitir tiempo suficiente para responder y realizar acciones preventivos si es necesario. Esta colección de modelos se actualizan periódicos reconversión utilizando los datos más recientes.

Puede encontrar más información acerca de este caso de uso [aquí](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18945).

#### <a name="use-case-qualification-criteria--prerequisites"></a>Usar criterios de calificación de mayúsculas y minúsculas: requisitos previos
La intensidad de inteligencia Cortana principal se encuentra en su capacidad para implementar y escalar máquina aprendizaje centrado en soluciones eficaces. Se ha diseñado para admitir miles de predicciones que se ejecutan simultáneamente. Puede escalar automáticamente para cumplir un patrón de consumo cambiantes. Enfoque de la solución por lo tanto, se encuentra en precisión y rendimiento de cálculo. Por ejemplo, una empresa de utilidad está interesada en la creación de previsión durante la próxima hora y para cada hora del día de la demanda de energía precisos. Por otro lado, nos interesan menos responder a la pregunta de por qué la demanda es pronosticada ser tal y como es (el modelo en sí se ocupará de que).

Por lo tanto, es importante tener en cuenta que no todos los casos de uso y problemas de empresa pueden solucionarse eficaz con el aprendizaje.

Inteligencia de Cortana y aprendizaje de máquina podrían ser muy eficaces para solucionar un problema empresarial dado cuando se cumplen los criterios siguientes:
-   El problema de negocio en mano es **predictiva** de naturaleza. Un ejemplo de uso predictiva es una empresa de utilidad que le gustaría predecir carga power en un determinado subestación durante la hora siguiente. Por otro lado, analizar y clasificar controladores de petición histórico sería **descriptivo** naturaleza y, por tanto, menos aplicable.
-   Hay una **ruta de acceso de la acción** de borrar que realizará una vez que la predicción está disponible. Por ejemplo, las predicciones una sobrecarga en un subestación durante la próxima hora pueden desencadenar una acción proactiva de reducir la carga que está asociado a ese subestación y evitando potencialmente una sobrecarga.
-   El caso de uso representa un **tipo normal de problema** tal que cuando soluciona puede preparar la forma a otra similar de resolución de casos de uso.
-   El cliente puede establecer **objetivos cuantitativos y calidad** para demostrar una implementación de la solución correcta. Por ejemplo, un buen objetivo cuantitativo para la previsión de la demanda de energía sería el umbral de precisión necesarios (*por ejemplo*, se permite hasta el error del 5%) o si subestación la predicción de sobrecarga, a continuación, la precisión (tasa de positivos true) y recuperación (alcance de positivos true) debe estar por encima de un determinado umbral. Estos objetivos deben deriva de los objetivos del cliente empresas.
-   Hay un **escenario de integración** de borrar con el flujo de trabajo de la empresa. Por ejemplo, la previsión de carga de subestación puede integrarse en el centro de control de la cuadrícula para permitir que las actividades de prevención de sobrecarga.
-   El cliente tiene listos para usar **datos con calidad suficiente** para admitir el caso de uso (consulte más información en la siguiente sección, **Calidad de los datos**, de esta guía).
-   El cliente adopta la arquitectura de centrados en datos de nube o **equipo basado en la nube de aprendizaje**, incluidos m de Azure y otros componentes del conjunto de aplicaciones de inteligencia de Cortana.
-   El cliente está dispuesto a establecer **un flujo de datos de principio a fin** de que las instalaciones de la distribución de datos en la nube de forma periódica y está dispuesto a **hacer operativa** la solución.
-   El cliente está preparado para **dedique recursos** que se atención conocidas durante la implementación piloto inicial para que pueden transferir conocimientos y la propiedad de la solución al cliente cuando finalice.
-   Recursos de cliente deben ser un **capacitados datos profesionales**, preferentemente un científico de datos.

Calificación de un caso de uso en función de los criterios descritos en gran medida puede mejorar las tasas de éxito de un caso de uso y establecer una buena beachhead para la implementación de usos futuros.

### <a name="cloud-based-solutions"></a>Soluciones basadas en la nube
Conjunto de aplicaciones de inteligencia de Cortana en Azure es un entorno integrado que reside en la nube. La implementación de una solución de análisis avanzadas en un entorno de nube contiene ventajas significativas para empresas y al mismo tiempo, quizá gran cambio para empresas que usan aún soluciones de TI local. En el sector de la energía, hay una línea de tendencia borrar de la migración gradual de las operaciones en la nube. Esta tendencia va de la mano junto con el desarrollo de la cuadrícula inteligente como se indicó anteriormente, en **Tendencias de la industria**. Como esta guía se centra en una solución basada en la nube en el dominio de energía, es importante explicar las ventajas y otras consideraciones de implementación de una solución basada en la nube.

Quizás la mayor ventaja de una solución basada en nube es el coste. Como solución utiliza componentes implementado a la nube, no hay ningún planificaciones costes o CV (coste de bienes vendidos) componente asociada. Esto significa que no es necesario invertir en hardware, software y mantenimiento de TI y, por tanto, hay una considerable reducción de los riesgos del negocio.

Otra ventaja importante es la estructura de costo de pago de soluciones basadas en la nube. Servidores basados en la nube para informática o almacenamiento se pueden implementar y escalar según un solo como necesario. Esto representa la ventaja de eficiencia de costo de una solución basada en la nube.

Por último, no es necesario para invertir en mantenimiento de TI o el desarrollo de infraestructura futuras como todo esto es parte de la oferta en la nube. Hasta ese punto, conjunto de aplicaciones de inteligencia de Cortana incluye lo mejor en servicios de clase y sigue evolucionando su guía básica. Capacidades de componentes y características nuevas constantemente se presentan y evolucionan.

Para una empresa que se está iniciando su transición a la nube, altamente recomendamos para tomar un enfoque progresivo implementando una guía básica de migración de nube. Creemos que para empresas en el dominio de energía y utilidades, los casos de uso que se tratan en esta guía representan una excelente oportunidad para piloto soluciones análisis predictiva en la nube.

#### <a name="business-case-justification-considerations"></a>Consideraciones de rentabilidad profesionales
En muchos casos, el cliente puede estar interesado en realizar una justificación comercial para un caso de uso determinado en la que una solución basada en la nube y aprendizaje de máquina son componentes importantes. A diferencia de una solución local, en el caso de una solución basada en la nube, el componente de costos planificaciones es mínimo y la mayoría de los elementos de costo está asociada con el uso real. Cuando se trata de implementar una solución en conjunto de aplicaciones de inteligencia de Cortana de previsión de energía, varios servicios pueden integrarse con una sola estructura de costo comunes. Por ejemplo, las bases de datos (*por ejemplo*, SQL Azure) pueden utilizarse para almacenar los datos sin formato y, a continuación, las previsiones reales Azure m se utiliza para hospedar los servicios de previsión. En este ejemplo, la estructura de costo puede incluir almacenamiento y componentes de transacciones.

Por otro lado, uno debe tener una buena comprensión del valor empresarial de operativo una petición de energía previsión (término largo o corto). De hecho, es importante tener en cuenta el valor de negocio de cada operación de previsión. Por ejemplo, con precisión previsión carga power para las próximas 24 horas puede evitar overproduction o puede ayudar a evitar sobrecargas en la cuadrícula y esto puede cuantificación en términos de ahorro diariamente.

Una fórmula básica para calcular el beneficio financiero de la demanda de previsión solución sería: ![solución de previsión de la fórmula básica para calcular los beneficios financieros de petición](media/cortana-analytics-playbook-demand-forecasting-energy/financial-benefit-formula.png)

Dado que Cortana inteligencia Suite proporciona un modelo de precios de pago, no es necesario para provocar un componente de costo fijo para esta fórmula. Puede calcular esta fórmula en una base de diaria, mensual o anual.

Actual conjunto de aplicaciones de inteligencia de Cortana y v Azure planes de precios pueden encontrarse [aquí](http://azure.microsoft.com/pricing/details/machine-learning/).

### <a name="solution-development-process"></a>Proceso de desarrollo de soluciones
Use el ciclo de desarrollo de previsión solución normalmente implica 4 fases, en el que se realice una petición de energía de tecnologías basadas en la nube y servicios incluidos en el conjunto de inteligencia Cortana.

Esto se muestra en el diagrama siguiente:

![Ciclo de cuadrícula inteligente](media/cortana-analytics-playbook-demand-forecasting-energy/smart-grid-cycle.png)

El párrafo siguiente describe este proceso 4 paso:

1.  **Recopilación de datos** : cualquier solución analíticas avanzadas según se basa en datos (vea la **Descripción de datos**). En concreto, cuando se trata de análisis predictivo y previsión, nos dependen de flujo en curso, dinámico de datos. En el caso de previsión de petición de energía, estos datos pueden conseguirse directamente desde metros inteligentes o ya se ha agregado en una base de datos local. Nos también dependen de otros orígenes de datos como el tiempo y temperatura externos. Este flujo de datos en curso se debe coordinado, programada y almacenado. [Generador de datos de Azure](http://azure.microsoft.com/services/data-factory/) (ADF) es nuestra potente principal para realizar esta tarea.
2.  **Modelado** : de energía exacta y fiable previsiones, uno debe desarrollar (tren) y mantener un excelente modelo que utiliza los datos históricos y extrae los patrones significativos y predicción de los datos. El área de máquina aprendizaje (v) ha ido aumentando rápidamente con algoritmos más avanzados que se desarrolla con frecuencia. Azure Studio m ofrece una experiencia de usuario excelente que le ayuda a utilizar los algoritmos m más avanzados dentro de un flujo de trabajo completado. Flujo de trabajo se muestra en un diagrama de flujo intuitivo e incluye la preparación de datos, la extracción de la característica, modelado y evaluación del modelo. El usuario puede extraer cientos de varios modelos que se incluyen en este entorno. Al final de esta fase un científico datos tendrá un modelo de trabajo está totalmente evaluada y listo para su implementación.

    El siguiente diagrama es una ilustración de un flujo de trabajo habituales:

    ![Modelado del flujo de trabajo](media/cortana-analytics-playbook-demand-forecasting-energy/modeling-workflow.png)

3.  **Implementación** : con un modelo de trabajo de la mano, el siguiente paso es su implementación. A continuación el modelo se convierte en un servicio web que expone una API de REST que se puede llamar simultáneamente a través de Internet desde varios clientes de consumo. M Azure proporciona una forma sencilla de implementar un modelo de directamente de Azure m Studio con un solo clic de botón. El proceso de implementación todo sucede en Mostrar opciones avanzadas. Esta solución puede escalar automáticamente para satisfacer el consumo necesario.

4.  **Consumo** : en esta fase, realmente ofrecemos usar del modelo de previsión para generar predicciones. El consumo puede controlar desde una aplicación de usuario (*por ejemplo*, el panel) o directamente desde un sistema operativo como demanda/suministro equilibrio de sistema o una solución de optimización de cuadrícula. Pueden controlar varios casos de uso de un único modelo.

## <a name="data-understanding"></a>Descripción de datos
Después de cubrir las consideraciones de negocio (consulte la **Descripción de la empresa**) de una solución de previsión de la demanda de energía, estamos listos hablar sobre el elemento de datos. Cualquier solución de analítica predictiva depende de datos confiable. Para la previsión de demanda de energía, confiar en los datos de consumo históricos con varios niveles de detalle. Que se utilizan datos históricos como la prima. Experimentará un análisis detallado en el que el científico datos identificarán predicción (también conocida como características) que puede colocar en un modelo que finalmente generará las previsiones necesarias.

En el resto de esta sección, describiremos los distintos pasos y consideraciones para comprender los datos y cómo conectar a un formulario utilizable.

### <a name="the-model-development-cycle"></a>El ciclo de desarrollo de modelo
Generar buena previsión modelos requiere cuidado de preparación y planificación. Desglosar el proceso de modelado en varios pasos y centrarse en un paso a la vez pueden aumentar considerablemente el resultado de todo el proceso.

El siguiente diagrama muestra cómo el proceso de modelado puede dividirse en varios pasos:

![Ciclo de desarrollo de modelo](media/cortana-analytics-playbook-demand-forecasting-energy/model-development-cycle.png)

Como puede verse en que el ciclo consta de seis pasos:
-   Formulación de problema
-   Recopilación de datos y la exploración de datos
-   Ingeniería de la característica y preparación de datos
-   Modelado
-   Evaluación de modelo
-   Desarrollo

En el resto de esta sección describiremos los pasos individuales y elementos para tener en cuenta en cada paso.

### <a name="problem-formulation"></a>Formulación de problema
Podemos tener en cuenta la formulación problema como el paso más importante es necesario tomar antes de implementar cualquier solución analítica predictiva. Aquí se transformar el problema empresarial y descomponer a elementos específicos que se pueden solucionar con datos y técnicas de modelado. Es una buena práctica formular el problema como un conjunto de preguntas que le gustaría responder. Estas son algunas posibles preguntas que podrían ser aplicables en el ámbito de la previsión de demanda de energía:
-   ¿Qué es la carga esperada en un subestación individual en la hora o el día siguiente?
-   ¿En qué momento del día experimentará mi cuadrícula petición de recursos asignadas?
-   ¿Cómo es probable que es mi cuadrícula para mantener la carga máxima esperada?
-   ¿Cuánto power debe generar la estación power durante cada hora del día?

Formular estas preguntas nos permite centrarse en obtener los datos correctos e implementar una solución que totalmente está alineada con el problema de empresa a mano. Además, a continuación, podemos establecer parámetros clave que nos permiten evaluar el rendimiento del modelo. ¿Por ejemplo, precisa cómo debe ser la previsión y cuál es el rango de error que sería aceptable para la empresa?

### <a name="data-sources"></a>Orígenes de datos
La cuadrícula inteligente moderna recopila datos de varias partes y componentes de la cuadrícula. Estos datos representan varios aspectos de la operación y el uso de la cuadrícula de energía. En el ámbito de la previsión de la demanda de energía, nos estamos limitar la discusión en orígenes de datos que reflejen el consumo demanda real. Un origen importante de consumo de energía son metros inteligentes. Utilidades de todo el mundo están implementando rápidamente metros inteligentes para sus clientes. Metros inteligentes grabar el consumo de energía real y de retransmisión constantemente estos datos a la empresa de utilidad. Datos recopila y envía en un intervalo fijo, comprendido entre cada 5 minutos en 1 hora. Metros inteligentes más avanzados pueden programarse remotamente para controlar y equilibrar el consumo real dentro de una casa. Datos de contador inteligente es relativamente confiables e incluyen una marca de tiempo. Que hace que un paso importante hacia demanda de previsión. Datos de contador se pueden agregar (sumados hacia arriba) en varios niveles dentro de la topología de cuadrícula: transformador, subestación, región, *etcetera*. A continuación, podemos elegir el nivel de agregación necesarios para crear un modelo de previsión para el mismo. Por ejemplo, si la empresa de utilidad le gustaría previsión carga futura en cada uno de sus subestaciones de cuadrícula, a continuación, datos todos de los metros pueden ser agregados para cada subestación individual y utilizados como entrada para el modelo de previsión. Nos referimos a metros inteligentes como un origen de datos interno.

Una previsión de la demanda de energía fiable también se dependen de otros orígenes de datos externos. Un factor importante que afecta a consumo de energía es el tiempo, o con mayor precisión la temperatura. Datos históricos muestran correlación entre la temperatura exterior y consumo de energía. Durante los días de verano cálido, asegúrese de los consumidores de uso de su aire acondicionado y durante la potencia de invierno en sistemas de calefacción. Por lo tanto, un origen de confianza de temperaturas históricos en la ubicación de la cuadrícula es clave. Además, nos también se basan en pronóstico preciso de temperatura como una predicción de consumo de energía.

Otros orígenes de datos externos también pueden ayudar a generar modelos de previsión de petición de energía. Estos pueden incluir a largo plazo cambios del clima económicos índices (*por ejemplo*, PIB) y otros usuarios. En este documento no incluimos estos otros orígenes de datos.

### <a name="data-structure"></a>Estructura de datos
Después de identificar los orígenes de datos necesarios, vamos a asegurarse de que los datos sin formato que se ha recopilado incluyen las características de datos correctos. Para crear un modelo de previsión de demanda confiable, nos necesarias para asegurarse de que los datos recopilados incluyen los elementos de datos que pueden ayudarle a predecir la demanda futura. Estos son algunos requisitos básicos sobre la estructura de datos (esquema) de los datos sin formato.

Los datos sin formato constan de filas y columnas. Cada medida se representa como una sola fila de datos. Cada fila de datos incluye varias columnas (también contempladas como campos o funciones).

1.  **Marca de tiempo** : el campo de marca de tiempo representa el tiempo real cuando se registre la medición. Deben cumplir uno de los formatos de fecha y hora comunes. Elementos de fecha y hora en que se deben incluidos. En la mayoría de los casos, no es necesario para el tiempo que se registren hasta el segundo nivel de detalle. Es importante especificar la zona horaria en la que se registran los datos.
2.  **Contador de ID** - este campo identifica el medidor o el dispositivo de medición. Es una variable por categorías y puede ser una combinación de dígitos y caracteres.
3.  **Valor de consumo** : este es el consumo real en una fecha y hora determinada. Se puede medir el consumo en kWh (hora-kilowatt) o cualquier otro preferida unidades. Es importante que tenga en cuenta que la unidad de medida debe mantenerse coherente en todas las medidas de los datos. En algunos casos, consumo puede proporcionar más de 3 fases de energía. En ese caso, necesitamos recopilar todas las fases de consumo independientes.
4.  **Temperatura** : la temperatura normalmente se recopila desde una fuente independiente. Sin embargo, debe ser compatible con los datos de consumo. Debe incluir una marca de hora como se describió anteriormente que le permitirá se sincronizan con los datos de consumo real. El valor de temperatura se puede especificar en grados Celsius o Fahrenheit, pero debe mantenerse coherente en todas las mediciones.
5.  **Ubicación:** El campo ubicación está normalmente asociado con el lugar donde se ha recopilado los datos de temperatura. Se puede representar como un número de código postal o en formato de latitud y longitud (latitud y longitud).

Las siguientes tablas se muestran ejemplos de un formato de datos de consumo y temperatura bueno:

|**Fecha**|**Hora**|**Identificador de contador**|**Fase 1**|**Fase 2**|**Fase 3**|
|--------|--------|------------|-----------|-----------|-----------|
|1/7/2015|10:00:00|ABC1234     |7.0        |2.1        |5.3        |
|1/7/2015|10:00:01|ABC1234     |7.1        |2.2        |4.3        |
|1/7/2015|10:00:02|ABC1234     |6.0        |2.1        |4.0        |

|**Fecha**|**Hora**|**Ubicación**|**Temperatura**|
|--------|--------|-------------|---------------|
|1/7/2015|10:00:00|11242        |24.4           |
|1/7/2015|10:00:01|11242        |24.4           |
|1/7/2015|10:00:02|11242        |24,5           |

Como puede verse encima, este ejemplo incluye 3 valores diferentes para su consumo asociado 3 fases power. Además, tenga en cuenta que se separan los campos de fecha y hora, pero también se pueden combinar en una sola columna. En este caso, la columna ubicación se representa en un formato de código postal de 5 dígitos y la temperatura en un formato de grado Celsius.

### <a name="data-format"></a>Formato de datos
Conjunto de aplicaciones de inteligencia de Cortana puede compatible con los formatos de datos más comunes como CSV, TSV, JSON, *etcetera*. Es importante que el formato de datos sea coherente para el ciclo de vida del proyecto.

### <a name="data-ingestion"></a>Recopilación de datos
Dado que la previsión de la demanda de energía es pronosticada constantemente y con frecuencia, debemos asegurar que los datos sin formato fluye por medio de un proceso de recopilación de datos sólido y confiable. El proceso de recopilación debe garantiza que los datos sin formato están disponibles para el proceso de previsión en el tiempo necesario. Que implica que la frecuencia de recopilación de datos debe ser mayor que la frecuencia de previsión.

Por ejemplo: si nuestra demanda previsión solución generará una previsión nueva a 8:00 A.M. diariamente, a continuación, se necesita para asegurarse de que todos los datos que se ha recopilado durante las últimas 24 horas han sido ingestión totalmente hasta ese punto y tiene incluso incluyan la última hora de datos.

Para ello, Cortana inteligencia Suite ofrece varias formas para admitir un proceso de recopilación de datos confiable. Esto se explicará más en la sección de **implementación** de este documento.

### <a name="data-quality"></a>Calidad de los datos
El origen de datos sin formato es necesario para realizar la previsión de demanda confiable y precisa debe cumplir algunos criterios de calidad de datos básico. Aunque pueden utilizarse métodos estadísticos avanzados a compensa la para algún problema de calidad de datos posibles, es necesario asegurarse de que estamos paso algunos umbral de calidad de la base de datos cuando se recopila datos nuevos. Estas son algunas consideraciones sobre la calidad de los datos sin formato:
-   **Falta el valor** : se refiere a la situación cuando no se recopiló medida específica. El requisito básico aquí es que el tipo de valor que faltan no deben superior al 10% durante un período de tiempo determinado. En caso de que un valor único falta se indicará mediante un valor predefinido (por ejemplo: '9999') y no '0' que podría ser una medida válida.
-   **Precisión de medida** : el valor real de consumo o la temperatura se registrarán con precisión. Medidas incorrectas generarán previsiones incorrectas. Normalmente, el error de medición debe ser inferior a 1% en relación con el valor true.
-   **Tiempo de medida** , es necesario que la marca de tiempo real de los datos recopilados se desvían no más de 10 segundos en relación con la hora real de la medida real.
-   **Sincronización** : cuando se utilizan varios orígenes de datos (*por ejemplo*, el consumo y temperatura) debemos asegurar que hay son no problemas sincronización de tiempo entre ellas. Esto significa que la diferencia de tiempo entre la marca de tiempo recopilado los dos orígenes de datos independientes no debe superar más de 10 segundos.
-   **Latencia** - como se indicó anteriormente, en la **Recopilación de datos**, dependen de un proceso de flujo y recopilación de datos confiable. Para controlar debemos asegurar que se controla la latencia de datos. Esto se especifica como la diferencia de tiempo entre el momento en que se realizó la medida real y la hora en que se ha cargado en el almacenamiento de conjunto de aplicaciones de inteligencia de Cortana y está listo para su uso. Para cargar a corto plazo previsión la latencia total no debe superar 30 minutos. Para cargar a largo plazo previsión la latencia total no debe superar 1 día.

### <a name="data-preparation-and-feature-engineering"></a>Ingeniería de la característica y preparación de datos
Una vez que los datos sin formato ha sido integrado (vea la **Recopilación de datos**) y se ha almacenado de forma segura, está listo para procesarse. La fase de preparación de datos básicamente es tomar los datos sin formato y convertir (transformar reforma) en un formulario de la fase de modelado. Que pueden incluir operaciones sencillas, como el uso de la columna de datos sin formato como con el valor medido real, valores normalizados, más complejas operaciones como [tiempo de retraso](https://en.wikipedia.org/wiki/Lag_operator)y otros. Las columnas de datos recién creado se denominan características de datos y el proceso de generación estos se conoce como ingeniería de la característica. Al final de este proceso tendríamos un nuevo conjunto de datos que se ha derivado de los datos sin formato y se puede utilizar para un modelado. Además, la fase de preparación de datos debe ocuparse de los valores que faltan (consulte la **Calidad de los datos**) y compensa la para ellos. En algunos casos, ¿necesitamos normalizar los datos para asegurarse de que todos los valores se representan en la misma escala.

En esta sección que se enumeran algunas de las características comunes de datos que se incluyen en la energía petición de modelos de previsión.

**Controlados por las características de tiempo:** Estas características derivan de los datos de marca de fecha/hora. Estos se extraen y se convierten en categorías características como:
-   Hora del día: esta es la hora del día que toma valores de 0 a 23
-   Día de la semana: Esto representa el día de la semana y toma valores de 1 (domingo) a 7 (sábado)
-   Día del mes: Esto representa la fecha real y puede tomar los valores de 1 a 31
-   Mes del año: Esto representa el mes y toma valores de 1 (enero) a 12 (diciembre)
-   Fin de semana: esta es una característica de valor binario que tarda los valores de 0 para los días laborables o 1 semana
-   Vacaciones - esta es una característica de valor binario que tarda los valores de 0 para un día normal o 1 día festivo
-   Términos de Fourier: los términos de Fourier son pesos que se derivan de la marca de tiempo y se usan para capturar la estacionalidad (ciclos) en los datos. Dado que debamos realizar varias estaciones en nuestros datos podemos debemos varios términos de Fourier. Por ejemplo, es podrán que los valores de petición tenga diarios, semanales y anuales estaciones/ciclos que se traducirán en términos de Fourier 3.

**Características de medición independiente:** Las características independientes incluyen todos los elementos de datos que se desea usar como indicadores en nuestro modelo. Aquí se excluir la característica dependiente que necesitamos predecir.
-   Característica de posposición: se trata de tiempo desplazado a valores de la demanda real. Por ejemplo, características de posposición 1 contendrá el valor de petición de la hora anterior (suponiendo que los datos por hora) en relación con la marca de tiempo actual. Del mismo modo, se podemos agregar posposición 2, 3, de posposición *etcetera*. Evaluación de los resultados de modelo determina la combinación real de características de posposición que se usan durante la fase de modelado.
-   Tendencias a largo plazo: esta característica representa el crecimiento lineal en petición entre años.

**Característica dependiente:** La característica dependiente es la columna de datos que le gustaría nuestro modelo de predecir. Con el [equipo controlado de aprendizaje](https://en.wikipedia.org/wiki/Supervised_learning), debemos primero entrenar el modelo mediante las características dependientes (que también se conoce como etiquetas). Esto permite que el modelo obtener información sobre los patrones de los datos asociados con la característica de dependiente. En la previsión de la demanda de energía normalmente queremos predecir la demanda real y, por tanto, se usaría como la característica dependiente.

**Tratamiento de valores que faltan:** Durante la fase de preparación de datos, ¿debemos determinar la mejor estrategia para controlar los valores que faltan. Esto se hace principalmente mediante el uso de los distintos [métodos de imputación de datos](https://en.wikipedia.org/wiki/Imputation_(statistics))de estadísticas. En el caso de previsión de petición de energía, se suele separar valores que faltan mediante el uso de la media móvil de puntos de datos disponibles anterior.

**Normalización de datos:** Normalización de datos es otro tipo de transformación que se usa para pasar todos los datos numéricos como previsión de la demanda a una escala similar. Normalmente esto ayuda a mejorar la precisión del modelo y la precisión. Nos normalmente haría esto divide el valor real por el rango de los datos.
Esto ajustará el valor original hacia abajo en un rango menor, normalmente entre -1 y 1.

## <a name="modeling"></a>Modelado
La fase de modelado es donde lleve a cabo la conversión de los datos en un modelo. En el centro de este proceso hay son avanzados algoritmos que analizar los datos históricos (datos de formación), extraer tramas y a continuación, cree un modelo. Ese modelo se puede usar más tarde predecir en nuevos datos que no se ha usado para generar el modelo.

Una vez que tenemos un modelo confiable de trabajo se puede utilizar para puntuación nuevos datos estructurados para incluir las características requeridas (X). El proceso de puntuación hará que utiliza el modelo conservado (objeto de la fase de aprendizaje) y predecir la variable de destino que se denota con Ŷ.

### <a name="demand-forecasting-modeling-techniques"></a>Petición previsión técnicas de modelado
En el caso de petición previsión ofrecemos el uso de datos históricos que se ordenan por hora. Generalmente nos referimos a los datos que incluyen la dimensión de tiempo como [serie de tiempo](https://en.wikipedia.org/wiki/Time_series). El objetivo de modelado de la serie de tiempo es encontrar tendencias relacionadas, estacionalidad, correlación automática (correlación lo largo del tiempo), de tiempo y formular en un modelo.

En los últimos años ha desarrollado algoritmos avanzados para ajustar la previsión de series y para mejorar la precisión de previsión. Se analizan brevemente algunas de ellas aquí.

> [AZURE.NOTE] Esta sección no está pensada para usarlo como una máquina de aprendizaje y descripción general de previsión sino como una breve encuesta técnicas que se utilizan para realizar una previsión de petición de modelado. Para obtener más información y material educativo acerca de la previsión de series, recomendamos el libro en línea [pronóstico: principios y práctica](https://www.otexts.org/book/fpp).

#### <a name="ma-moving-averagehttpswwwotextsorgfpp62"></a>[**MA (Media móvil)**](https://www.otexts.org/fpp/6/2)
Media móvil es una de las técnicas analíticas primera que ha usado para la previsión de series y es aún una de las más utilizadas técnicas a fecha de hoy. También es la base de más avanzadas técnicas de previsión. Con la media móvil nos estamos previsión el siguiente punto de datos por el promedio de los puntos más recientes K, donde K indica el orden de la media móvil.

La técnica de Media móvil tiene el efecto de suavizado la previsión y, por tanto, no puede encargarse de volatilidad bien grande en los datos.

#### <a name="ets-exponential-smoothinghttpswwwotextsorgfpp75"></a>[**ETS (suavizado exponencial)**](https://www.otexts.org/fpp/7/5)
Suavizado exponencial (HTA) es una familia de diversos métodos que use un promedio ponderado de puntos de datos recientes para predecir el siguiente punto de datos. La idea es asignar cargas superiores a los valores más recientes y disminuir gradualmente este peso para valores medidos anteriores. Existen varios métodos diferentes con esta familia, algunos de ellos incluyen el control de estacionalidad en los datos como [Método de temporada Holt deporte de invierno](https://www.otexts.org/fpp/7/5).

Algunos de estos métodos factor también en la estacionalidad de los datos.

#### <a name="arima-auto-regression-integrated-moving-averagehttpswwwotextsorgfpp8"></a>[**ARIMA (Auto regresión integrado Media móvil)**](https://www.otexts.org/fpp/8)
Automático regresión integrado mover promedio (ARIMA) es otra familia de métodos que se usa generalmente para realizar una previsión de serie de tiempo. Prácticamente combina métodos de regresión automática con Media móvil. Métodos de regresión automática usan modelos de regresión tomando los valores de la serie de tiempo anterior para calcular el siguiente punto de fecha. Métodos ARIMA también aplican los métodos de comparación que incluyan calcular la diferencia entre puntos de datos y con los que aparecen en lugar del valor medido original. Por último, ARIMA también utiliza las técnicas de Media móvil que se ha descrito anteriormente. La combinación de todos estos métodos de distintas maneras es lo que crea la familia de métodos ARIMA.

ETS y ARIMA son ampliamente utilizado para realizar una previsión de demanda de energía y otros problemas de previsión. En muchos casos estas se combinan para ofrecer resultados muy precisos.

**Regresión múltiplo general** Los modelos de regresión podrían ser el enfoque de modelado más importante dentro del dominio de aprendizaje y estadísticas. En el contexto de la serie temporal usamos regresión predecir los valores futuros (*por ejemplo*, de petición). De regresión estamos tomar una combinación de lineal de la predicción y obtenga información sobre los pesos (también contemplados como coeficientes) de los indicadores de durante el proceso de aprendizaje. El objetivo es generar una línea de regresión que se previsión nuestro valor previsto. Métodos de regresión son adecuadas cuando la variable de destino es numérica y, por tanto, también se ajusta a previsión de series. Hay una amplia variedad de métodos de regresión incluidos modelos de regresión muy simple como [Regresión lineal](https://en.wikipedia.org/wiki/Linear_regression) y las más avanzadas como árboles de decisión, [Bosques aleatorio](https://en.wikipedia.org/wiki/Random_forest), [Redes neurales](https://en.wikipedia.org/wiki/Artificial_neural_network)y aumenta árboles de decisión.

Creación de petición de energía previsión como un problema de regresión nos ofrece una gran cantidad de flexibilidad en la selección de los datos de serie de tiempo de demanda real y factores externos, como la temperatura de nuestras características de datos que se pueden combinar. Más información sobre las características seleccionadas se tratan en la característica de ingeniería (consulte **preparación de datos y de ingeniería de característica**) la sección de esta guía.

Desde nuestra experiencia con e implementación de piloto de previsiones de petición de energía, hemos encontrado que los modelos de regresión avanzadas que están disponibles en Azure m tienden a fin de generar los mejores resultados y ofrecemos utilizarlos.

## <a name="model-evaluation"></a>Evaluación de modelo
Evaluación de modelo tiene una función crítica dentro del **Ciclo de desarrollo de modelo**. En este paso esperamos en validar el modelo y su rendimiento con datos reales. Durante el paso de modelado usamos una parte de los datos disponibles para el modelo de aprendizaje. Durante la fase de evaluación echamos el resto de los datos para probar el modelo. Prácticamente significa que nos estamos alimentar los datos del nuevo modelo que han sido reestructurar y contiene las mismas características que el conjunto de datos de aprendizaje. Sin embargo, durante el proceso de validación, usamos el modelo de predecir la variable de destino en lugar de proporcionar la variable de destino disponibles. A menudo, nos referimos a este proceso como puntuación del modelo. A continuación, se podría emplean los valores del objetivo es true y compararlos con los previstos. El objetivo aquí es medir y minimizar el error de predicción, lo que significa que la diferencia entre las predicciones y el valor es true. Cuantificar la medida de error es clave, ya que le gustaría ajustar el modelo y validar si realmente disminuye el error. Ajuste del modelo puede hacerse mediante la modificación de parámetros del modelo que controlan el proceso de aprendizaje, o agregando o quitando características de datos (denominadas [parámetros limpiar](https://channel9.msdn.com/Blogs/Windows-Azure/Data-Science-Series-Building-an-Optimal-Model-With-Parameter-Sweep)). Prácticamente eso significa que podemos necesitamos trabajemos entre la ingeniería de la característica, modelado y modelo fases de evaluación varias veces hasta que podemos reducir el error en el nivel requerido.

Es importante que el error de predicción nunca será cero como de énfasis nunca es un modelo que puede predecir perfectamente cada resultado. Sin embargo, hay una determinada magnitud de error que es aceptable para la empresa. Durante el proceso de validación, vamos a comprobar que es nuestro error de predicción de modelo en el nivel o mejor que el nivel de tolerancia de la empresa. Por lo tanto, es importante establecer el nivel del error tolerable al principio del ciclo durante la fase de **Formulación de problema** .

### <a name="typical-evaluation-techniques"></a>Técnicas de evaluación típica
Existen diversas maneras en que la predicción de error se puede medir y cuantificación. En esta sección se tratará la discusión en las técnicas de evaluación correspondientes a la serie de tiempo y en específicos de previsión de la demanda de energía.

#### <a name="mapehttpsenwikipediaorgwikimeanabsolutepercentageerror"></a>[**MAPE**](https://en.wikipedia.org/wiki/Mean_absolute_percentage_error)
MAPE significa significa absoluta Error de porcentaje. Con MAPE nos estamos calcular la diferencia entre cada una previsión punto y el valor real de ese punto. A continuación, nos cuantificar el error por punto calculando la proporción de la diferencia en relación con el valor real. En el último paso es calcular el promedio de estos valores. La fórmula matemática usada para MAPE es la siguiente:

![Fórmula de MAPE](media/cortana-analytics-playbook-demand-forecasting-energy/mape-formula.png)
*una<sub>t</sub> es el valor real, F<sub>t</sub> es el valor pronosticado y n es el horizonte de previsión.*

## <a name="deployment"></a>Implementación
Una vez que hemos dedicados hacia abajo de la fase de modelado y validar el rendimiento del modelo que está listos para ir a la fase de implementación. En este contexto, la implementación, permitiendo al cliente consumir el modelo ejecutando predicciones reales en él en gran escala. El concepto de implementación es clave en Azure m desde nuestro objetivo principal es constantemente invocar predicciones en lugar de obtener simplemente la perspectiva de los datos. La fase de implementación es el elemento donde se habilita el modelo de consumir a gran escala.

En el contexto de la previsión de la demanda de energía, nuestro objetivo es invocar previsiones periódicas y continuadas mientras asegura que están disponibles para el modelo de datos actualizados y que los datos de previsión se envían al cliente consumir.

### <a name="web-services-deployment"></a>Implementación de servicios Web
El bloque de creación de implementación principal en Azure m es el servicio web. Esta es la manera más eficaz para habilitar el consumo de un modelo de predicción en la nube. El servicio Web encapsula el modelo y concluye con [RESTful](http://www.restapitutorial.com/) API (interfaz de programación de aplicaciones). La API de puede usarse como parte de cualquier código de cliente, como se muestra en el diagrama siguiente.

![Se consumo e implementación de servicio](media/cortana-analytics-playbook-demand-forecasting-energy/web-service-deployment-and-consumption.png)

Como puede verse, el servicio web se implementa en la nube de conjunto de aplicaciones de inteligencia de Cortana y se puede invocar sobre su extremo de la API de REST expuesto. Otro tipo de clientes en varios dominios puede invocar el servicio a través de la API de Web simultáneamente. También puede escalar el servicio web para admitir miles de llamadas simultáneas.

### <a name="a-typical-solution-architecture"></a>Una arquitectura de solución típica
Al implementar una solución de previsión de la demanda de energía, estamos interesados en implementar una solución de llevar a cabo que vaya más allá del servicio web de predicción y facilita el flujo de datos completo. En el momento de que invocación de una nueva previsión, ¿debemos para asegurarse de que el modelo se suministra con las características de datos actualizados. Que implica que los datos sin formato recién recopilados es constantemente ingestión, procesa y transforma en el conjunto en que se creó el modelo de características necesarios. Al mismo tiempo, vamos a hacer que los datos previstos esté disponible para los clientes de consumo final. En el diagrama siguiente se muestra un ciclo de flujo de datos de ejemplo (o canalización de datos):

![Flujo de datos de principio a fin de previsión de la demanda de energía](media/cortana-analytics-playbook-demand-forecasting-energy/energy-demand-forecase-end-data-flow.png)

Estos son los pasos que se producirá como parte del ciclo de previsión de petición de energía:
1.  Millones de metros de implementada datos constantemente generan datos de consumo de energía en tiempo real.
2.  Estos datos que se recopila y cargado en un repositorio de nube (*por ejemplo*, blobs de Windows Azure).
3.  Antes de que se procesan, los datos sin formato se agregan a una subestación o nivel regional definida por la empresa.
4.  El procesamiento de la característica (consulte **preparación de datos y la característica de procesamiento**), a continuación, lleve a cabo y genera los datos que se requiere para modelar resultados o formación: los datos del conjunto de características se almacenan en una base de datos (*por ejemplo*, SQL Azure).
5.  Se llama al servicio formación volver a formar el modelo de previsión a que la versión actualizada del modelo se almacena para que se puede usar el servicio web puntuación.
6.  El servicio web de resultados se invoca en una programación que se ajuste a la frecuencia de previsión necesaria.
7.  Los datos previstos se almacenan en una base de datos que se puede acceder por el cliente de consumo final.
8.  El cliente de consumo recupera las previsiones, aplica a la cuadrícula y consume según el caso de uso.

Es importante que tenga en cuenta que este ciclo todo está totalmente automatizada y se ejecuta en una programación. La orquestación completa de este ciclo de datos puede realizarse mediante herramientas como [Generador de datos de Azure](http://azure.microsoft.com/services/data-factory/).

### <a name="end-to-end-deployment-architecture"></a>Arquitectura de implementación de principio a fin
Para prácticamente implementar una solución de previsión de demanda de energía en Cortana inteligencia, es necesario para asegurarse de que los componentes necesarios están establecidos y configurados correctamente.

El siguiente diagrama muestra una arquitectura de inteligencia Cortana según normal que implementa y organiza el ciclo de flujo de datos descrito anteriormente:

![Arquitectura de implementación de principio a fin](media/cortana-analytics-playbook-demand-forecasting-energy/architecture.png)

Para obtener más información acerca de cada uno de los componentes y toda la arquitectura, consulte la plantilla de solución de energía.

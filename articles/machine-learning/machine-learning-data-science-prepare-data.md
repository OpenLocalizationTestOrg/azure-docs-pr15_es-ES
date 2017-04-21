<properties
    pageTitle="Tareas para preparar los datos para mejorar el aprendizaje de máquina | Microsoft Azure"
    description="Procesar previamente y limpiar los datos a prepararse para el aprendizaje."
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
    ms.date="09/19/2016" 
    ms.author="bradsev" />


# <a name="tasks-to-prepare-data-for-enhanced-machine-learning"></a>Tareas para preparar los datos para mejorar el aprendizaje de máquina

Preprocesamiento y limpieza de datos son tareas importantes que normalmente deben llevarse a cabo antes de conjunto de datos se puede utilizar eficazmente para el aprendizaje. Datos sin formato suele ruido y poco confiables y le falte valores. Usar este tipo de datos para un modelado, puede producir resultados confusos. Estas tareas forman parte del proceso de ciencias de datos (TDSP) de equipo y normalmente siguen una exploración inicial de un conjunto de datos utilizado para descubrir y planear el procesamiento previo necesario. Para obtener instrucciones detalladas sobre el proceso TDSP, consulte los pasos indicados en el [Proceso de ciencias de datos de equipo](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

Preprocesamiento y limpieza de tareas, como la tarea de exploración de datos, pueden llevarse a cabo en una amplia variedad de entornos, como SQL o subárbol o Studio de aprendizaje del equipo de Azure y con diversas herramientas y lenguajes como R o Python, según dónde se almacenan los datos y cómo se aplica formato. Puesto que TDSP es iterativo de naturaleza, estas tareas pueden realizar en varios pasos del flujo de trabajo del proceso.

Este artículo presenta varios conceptos de procesamiento de datos y las tareas que pueden llevarse a cabo antes o después de recopilar datos en Azure el aprendizaje.

Para obtener un ejemplo de exploración de datos y preprocesamiento terminado dentro de aprendizaje del equipo de Azure studio, vea el vídeo de [preprocesamiento datos en Azure máquina aprendizaje Studio](https://azure.microsoft.com/documentation/videos/preprocessing-data-in-azure-ml-studio/) .


## <a name="why-pre-process-and-clean-data"></a>¿Por qué procesamiento previo y limpiar los datos?

Datos del mundo real se reunió de varios orígenes y procesos y pueden contener irregularidad o datos dañados peligro la calidad del conjunto de datos. Los problemas de calidad de datos típico que puedan aparecer son:

* **Incompleto**: datos carece de atributos o que contiene los valores que faltan.
* **Noisy**: datos contienen registros incorrectos o valores atípicos.
* **Incoherente**: datos contienen registros en conflicto o discrepancias.

Datos de calidad están un requisito previo para modelos de predicción de calidad. Para evitar "basura en basura fuera" y mejorar la calidad de los datos y, por tanto, del modelo de rendimiento, es fundamental para llevar a cabo una pantalla de mantenimiento de datos para detectar problemas de datos temprano y decidir sobre el procesamiento de datos correspondiente y los pasos de limpieza.

## <a name="what-are-some-typical-data-health-screens-that-are-employed"></a>¿Cuáles son algunas pantallas de mantenimiento de datos típico que se usan?

Podemos comprobar la calidad general de datos, consulte:

* El número de **registros**.
* El número de **atributos** (o **características**).
* El atributo **tipos de datos** (nominal, ordinal o continua).
* El número de **faltan los valores**.
* **Corrección** de los datos.
    * Si los datos están en TSV o CSV, compruebe que los separadores de línea y separadores de columna siempre correctamente independientes en columnas y líneas.
    * Si los datos están en formato HTML o XML, compruebe si los datos sea correcto basándose en sus respectivos estándares.
    * Analizar también puede ser necesario para extraer información estructurada de datos estructurados o semiestructurados.
* **Registros de datos incoherente**. Compruebe el rango de valores permitidos. Por ejemplo, si los datos contienen Nota Media de estudiantes, compruebe si la nota Media está en el intervalo designado, por ejemplo, 0 ~ 4.

Cuando encuentre problemas con los datos, **pasos de procesamiento** son necesarias que a menudo implica limpiar los valores que faltan, normalización de datos, discretización, proceso para quitar o reemplazar caracteres incrustados que pueden afectar a la alineación de los datos de texto, tipos de datos de mixtos en común campos y otros usuarios.

**Aprendizaje del equipo de azure consume datos tabulares correctos**.  Si los datos ya están en formato tabular, preprocesamiento de datos puede realizarse directamente con Azure el aprendizaje en Studio de aprendizaje de equipo.  Si los datos no están en formato tabular, supongamos que se encuentra en XML, el análisis podría ser necesario para convertir los datos en formato tabular.  

## <a name="what-are-some-of-the-major-tasks-in-data-pre-processing"></a>¿Cuáles son algunas de las tareas más importantes de preprocesamiento de datos?

* **Limpieza de datos**: rellenar o que faltan valores, detectar y eliminar datos ruidos y valores atípicos.
* **Transformación de datos**: Normalizar datos para reducir el ruido y dimensiones.
* **Reducción de datos**: los registros de datos o atributos de tratamiento de datos sea más fácil de ejemplo.
* **Discretización de datos**: convertir atributos continuos en los atributos de por categorías para facilitar su uso con determinados métodos de aprendizaje del equipo.
* **Texto de limpieza**: quitar caracteres incrustados que pueden causar el error de alineación de datos para por ejemplo, pestañas incrustadas en un archivo de datos separados por tabulaciones, incrustados nuevas líneas que podrían desactivar los registros, etcetera.

Las siguientes secciones detallan algunos de estos pasos de procesamiento de datos.

## <a name="how-to-deal-with-missing-values"></a>¿Cómo tratar los valores que faltan?

Para trabajar con los valores que faltan, es mejor primero identificar el motivo de los valores que faltan para administrar mejor el problema. Métodos de tratamiento de valor falta típicos son:

* **Eliminación**: quitar registros con valores que faltan
* **Sustitución de ficticio**: reemplazar valores que faltan con un valor ficticio: por ejemplo, _desconocido_ de 0 o por categorías para valores numéricos.
* **La intención de sustitución**: si los datos que faltan no están numéricos, reemplace los valores que faltan por la Media.
* **Sustitución frecuentes**: si los datos que faltan por categorías, cambie los valores que faltan con el elemento más frecuente
* **Sustitución de regresión**: usar un método de regresión para reemplazar los valores que faltan con valores retomó.  

## <a name="how-to-normalize-data"></a>¿Cómo normalizar los datos?

Normalización de datos volver a escala valores numéricos en un rango especificado. Métodos de normalización de datos más conocidos incluyen:

* **Cantidad mínima y máxima normalización**: lineal transformar los datos a un rango, por ejemplo entre 0 y 1, donde se ajusta el valor de min 0 y Máx valor a 1.
* **Puntuación Z normalización**: escalar datos en función de la media y desviación estándar: dividir la diferencia entre los datos y la media por la desviación estándar.
* **Escala decimal**: ajustar el tamaño de los datos al mover el punto decimal del valor del atributo.  

## <a name="how-to-discretize-data"></a>¿Cómo puedo discretizar datos?

Datos se pueden discretizar al convertir valores continuos en atributos nominales o intervalos. Se muestran algunas formas de hacerlo:

* **Binning de igual ancho**: dividir el rango de todos los valores posibles de un atributo en grupos de N del mismo tamaño y a continuación, asignar los valores que se encuentran en una ubicación con el número de la Papelera.
* **Binning de igual alto**: dividir el rango de todos los valores posibles de un atributo en grupos de N, que contienen el mismo número de instancias de cada uno y luego asignar los valores que se encuentran en una ubicación con el número de la Papelera.  

## <a name="how-to-reduce-data"></a>¿Cómo puedo reducir los datos?

Existen varios métodos para reducir el tamaño de los datos para un control de datos sea más fáciles. Según el tamaño de los datos y el dominio, se pueden aplicar los métodos siguientes:

* **Muestras de registro**: los registros de datos de ejemplo y elija solo representante subconjunto de los datos.
* **Atributo muestreo**: seleccione únicamente un subconjunto de los atributos más importantes de los datos.  
* **Agregación**: dividir los datos en grupos y almacenar los números para cada grupo. Por ejemplo, los números de los ingresos diaria de una cadena de restaurante durante los últimos años 20 pueden agregarse a ingresos mensuales para reducir el tamaño de los datos.  

## <a name="how-to-clean-text-data"></a>¿Cómo puedo limpiar los datos de texto?

**Campos de texto en datos tabulares** puede incluir caracteres que afectan a los límites de alineación o registro de columnas. Por ejemplo, incrustados pestañas en un error de alineación de columna de archivo delimitado por tabulaciones causa e incrustados caracteres de nueva línea saltos de línea de registro. Control de codificación de texto incorrecto al escribir o leer texto lleva a pérdida accidental introducción de caracteres no se puede leer, por ejemplo, valores nulos y puede también afectan al texto de análisis de información. Analizar cuidado y edición podrán ser necesarios para limpiar los campos de texto para alineación adecuada o a datos extraer estructurado de datos de texto no estructurados y semiestructurados.

**Exploración de datos** ofrece una vista inicial de los datos. Un número de problemas de datos puede ser descubierto durante este paso y se pueden aplicar métodos correspondientes para solucionar dichos problemas.  Es importante hacer preguntas como ¿cuál es el origen del problema y cómo el problema que se han introducido. Esto también le ayuda a decidir sobre los pasos de procesamiento de datos que deben realizarse para resolverlos. El tipo de información uno pretende derivan de los datos también puede usarse para priorizar el esfuerzo de procesamiento de datos.

## <a name="references"></a>Referencias

>*Minería de datos: conceptos y técnicas*, tercera edición, Morgan Kaufmann, 2011, Jiawei Han, Micheline Kamber y Jian Pei

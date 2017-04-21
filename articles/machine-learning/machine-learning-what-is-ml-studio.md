<properties 
    pageTitle="¿Qué es Studio de aprendizaje del equipo de Azure? | Microsoft Azure"
    description="Descripción general de Azure m Studio, una herramienta de arrastrar y colocar para crear rápidamente modelos de una biblioteca listos para usar de algoritmos y módulos."
    keywords="Azure máquina de aprendizaje, m azure, studio m"
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/09/2016"
    ms.author="garye"/>

# <a name="what-is-azure-machine-learning-studio"></a>¿Qué es Studio de aprendizaje del equipo de Azure?

Aprendizaje de Microsoft Azure máquina Studio es una herramienta de colaboración, arrastrar y colocar que puede usar para crear, probar e implementar soluciones de análisis predictiva en los datos. Máquina aprendizaje Studio publica modelos como servicios web que se pueden utilizar fácilmente por aplicaciones personalizadas o herramientas de BI como Excel.

Máquina aprendizaje Studio es donde se encuentran ciencias de datos, análisis predictivo, recursos de la nube y sus datos.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="the-machine-learning-studio-interactive-workspace"></a>El área de trabajo interactiva de máquina aprendizaje Studio

Para desarrollar un modelo de análisis predictiva, utiliza normalmente datos de uno o más orígenes, transformar y analizar los datos a través de varias manipulación de datos y funciones estadísticas y genera un conjunto de resultados. Desarrollar un modelo similar a este es un proceso iterativo. Modifique las varias funciones y sus parámetros, los resultados convergen hasta que esté satisfecho tiene un modelo de formación y eficaces.

**Azure máquina aprendizaje Studio** le ofrece un área de trabajo interactiva y visual para generar, probar y trabajemos en un modelo de análisis predictiva fácilmente. Puede arrastrar y colocar ***conjuntos de datos*** y análisis ***módulos*** un lienzo interactivos, conectarlos para formar una ***experimentar***, que se ejecuta en el equipo Studio de aprendizaje. Para repetir en el diseño del modelo, editar el ensayo, guardar una copia, si lo desea y vuelva a ejecutar. Cuando esté listo, puede convertir su ***aprendizaje experimentar*** en un ***ensayo predictiva***y, después, publicarlo como un ***servicio web*** para que el modelo se puede tener acceso a otros usuarios.

>[AZURE.TIP] Para descargar e imprimir un diagrama que le ofrece una descripción general de las capacidades de máquina aprendizaje Studio, consulte el [diagrama de información general de las capacidades de Azure máquina aprendizaje Studio](machine-learning-studio-overview-diagram.md).

No hay ninguna programación necesaria, simplemente visualmente conectar conjuntos de datos y módulos para crear un modelo de análisis de predicción.

![Diagrama de Azure m Studio: experimentación de crear, leer los datos de muchos orígenes, escribir datos puntuados, escribir modelos.][ml-studio-overview]

## <a name="get-started-with-machine-learning-studio"></a>Empezar a trabajar con Machine aprendizaje Studio

Cuando primero escribir [Máquina aprendizaje Studio](https://studio.azureml.net) verá la página de **Inicio** . Desde aquí puede ver la documentación, vídeos, seminarios Web y buscar otros recursos valiosos.

Hay tres fichas en la parte superior: **Inicio** (donde iniciar), **Studio**y **Galería**.

### <a name="studio"></a>Studio

Haga clic en la ficha **Studio** y le pedirá que inicie sesión con su cuenta de Microsoft o su cuenta profesional o educativa. Una vez que haya iniciado sesión, verá las siguientes pestañas a la izquierda:

- **Proyectos** - colecciones de experimentación, conjuntos de datos, los blocs de notas y otros recursos que representa un solo proyecto
- **Experimentación** - experimentación que se han creado, ejecutar y guardar como borrador
- **Servicios WEB** : servicios Web que se han implementado desde su experimentación
- **Blocs de notas** : blocs de notas de Jupyter que ha creado
- **Conjuntos de datos** - conjuntos de datos que ha cargado en Studio
- **Modelos CAPACITADO** - modelos que ha preparado en experimentación y guardado en Studio
- **Configuración** : una colección de configuración que puede usar para configurar su cuenta y recursos.

### <a name="gallery"></a>Galería

Haga clic en la pestaña de la **Galería** y se le dirigirá a la Galería de inteligencia Cortana. La galería es el lugar donde una comunidad de desarrolladores y científicos de datos puede compartir las soluciones creadas con componentes de la serie de inteligencia Cortana.

Para obtener más información acerca de la galería, vea [Compartir y descubrir soluciones en la Galería de inteligencia Cortana](machine-learning-gallery-how-to-use-contribute-publish.md).

## <a name="components-of-an-experiment"></a>Componentes de un ensayo

Un ensayo consta de los conjuntos de datos que proporcionan datos a los módulos analítica, conectar entre sí para crear un modelo de análisis de predicción. Más concretamente, un ensayo válido tiene estas características:

- El ensayo tiene al menos un conjunto de datos y un módulo
- Conjuntos de datos que esté conectado a los módulos
- Módulos que esté conectados a conjuntos de datos o en otros módulos
- Todos los puertos de entrada para los módulos deben tener algunos conexión para el flujo de datos
- Todos los requeridos se deben establecer parámetros para cada módulo

Puede crear un ensayo desde cero, o puede usar un ensayo de ejemplo existente como plantilla. Para obtener más información, vea [experimentación de ejemplo de uso para crear nuevos experimentación](machine-learning-sample-experiments.md).

Para obtener un ejemplo de la creación de un ensayo simple, vea [crear un ensayo simple en Azure máquina aprendizaje Studio](machine-learning-create-experiment.md).

Para obtener un tutorial más completado de la creación de una solución de análisis predictiva, vea [desarrollar una solución predictiva con Azure el aprendizaje](machine-learning-walkthrough-develop-predictive-solution.md).

### <a name="datasets"></a>Conjuntos de datos

Un conjunto de datos es los datos que se ha cargado Studio de aprendizaje de máquina para que se puede usar en el proceso de modelado. Se incluye con Machine aprendizaje Studio para experimentar con una serie de conjuntos de datos de ejemplo y puede cargar varios conjuntos de datos cuando lo necesite. Estos son algunos ejemplos de conjuntos de datos incluyen:

- **Datos MPG para diversos automóviles** - millas por valores de galón (MPG) para automóviles identificadas por el número de botellas, caballo, etcetera.
- **Datos de cáncer de seno** - datos de diagnóstico de cáncer de seno.
- **Datos del bosque se activa** - tamaños de fire bosque en Portugal nordeste.

Al crear un ensayo puede elegir de la lista de conjuntos de datos disponibles a la izquierda del lienzo.

Para obtener una lista de conjuntos de datos de ejemplo incluidos en máquina aprendizaje Studio, vea [usar los conjuntos de datos de ejemplo en Azure máquina aprendizaje Studio](machine-learning-use-sample-datasets.md).

### <a name="modules"></a>Módulos

Un módulo es un algoritmo que puede realizar en los datos. Máquina aprendizaje Studio tiene un número de módulos comprendido entre las funciones de entrada de datos y procesos de validación, puntuación y aprendizaje. Estos son algunos ejemplos de módulos incluyen:

- [Convertir en ARFF] [ convert-to-arff] -convierte a formato de archivo de relación entre el atributo (ARFF) de un conjunto de datos de .NET serializada.
- [Calcular estadísticas primaria] [ elementary-statistics] -calcula estadísticas primaria como Media, desviación estándar, etcetera.
- [Regresión lineal] [ linear-regression] -crea un modelo en línea degradado basado en descenso regresión lineal.
- [Puntuación modelo] [ score-model] -alcanza un modelo de clasificación o regresión capacitado.

Al crear un ensayo puede elegir de la lista de módulos disponibles a la izquierda del lienzo.  

Un módulo puede tener un conjunto de parámetros que puede usar para configurar algoritmos internos del módulo. Cuando se selecciona un módulo en el lienzo, se muestran los parámetros del módulo en el panel de **Propiedades** a la derecha del lienzo. Puede modificar los parámetros de ese panel para ajustar el modelo.

Para algunos ayuda para desplazarse por la biblioteca grande de algoritmos de aprendizaje de máquina disponibles, consulte [cómo elegir algoritmos para el equipo de aprendizaje de Microsoft Azure](machine-learning-algorithm-choice.md).

## <a name="deploying-a-predictive-analytics-web-service"></a>Implementar un servicio de web analytics predictiva

Una vez que el modelo de análisis predictiva esté listo, puede implementar como un servicio web directamente desde el equipo Studio de aprendizaje. Para obtener más detalles sobre este proceso, vea [implementar un servicio web de aprendizaje del equipo de Azure](machine-learning-publish-a-machine-learning-web-service.md).

[ml-studio-overview]:./media/machine-learning-what-is-ml-studio/azure-ml-studio-diagram.jpg

<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/

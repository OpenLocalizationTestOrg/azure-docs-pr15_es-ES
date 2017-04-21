<properties 
    pageTitle="Ejecutar secuencias de comandos de aprendizaje de Python equipo | Microsoft Azure" 
    description="Principios subyacentes compatibilidad para las secuencias de comandos de Python en Azure el aprendizaje y escenarios de uso básico, capacidades y limitaciones de diseño de esquemas." 
    keywords="máquina Python de aprendizaje, pandas, pandas python, secuencias de comandos de python, ejecutar secuencias de comandos de python"
    services="machine-learning"
    documentationCenter="" 
    authors="bradsev" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/12/2016" 
    ms.author="bradsev" />


# <a name="execute-python-machine-learning-scripts-in-azure-machine-learning-studio"></a>Ejecutar secuencias de comandos de aprendizaje de máquina de Python en Azure máquina aprendizaje Studio

Este tema describe los principios de diseño subyacente de la compatibilidad con las secuencias de comandos de Python en Azure el aprendizaje. También se describen las funciones principales, incluyendo compatibilidad para importar el código existente, exportación de visualizaciones y, por último, se analizan algunas de las limitaciones y el trabajo en curso.

[Python](https://www.python.org/) es una herramienta imprescindible en el pecho herramienta de muchas científicos de datos. Tiene:

-  una sintaxis elegante y concisa, 
-  compatibilidad entre plataformas, 
-  una gran colección de bibliotecas eficaces, y 
-  herramientas de desarrollo para adultos. 

Python que se usa en todas las fases del flujo de trabajo se usan normalmente en modelado de aprendizaje del equipo, desde datos recopile y procesamiento para construcción de característica y formación del modelo, validación e implementación de los modelos. 

Studio de aprendizaje de máquina Azure es compatible con secuencias de comandos de Python incrustación en distintas partes de un equipo de ensayo de aprendizaje y también perfecta publicarlos como servicios web scalable, operationalized en Microsoft Azure.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


## <a name="design-principles-of-python-scripts-in-machine-learning"></a>Principios de diseño de secuencias de comandos de Python en aprendizaje de equipo
La interfaz principal para Python en Azure máquina aprendizaje Studio es a través de la [Secuencia de comandos de ejecutar Python] [ execute-python-script] módulo que se muestra en la figura 1.

![Image1](./media/machine-learning-execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![imagen2](./media/machine-learning-execute-python-scripts/embedded-machine-learning-python-script.png)

Figura 1. El módulo de **Ejecutar Script de Python** .

La [Secuencia de comandos de ejecutar Python] [ execute-python-script] módulo acepta hasta tres entradas y produce hasta dos salidas (que se describen a continuación), como su analógico R, la [Secuencia de comandos de ejecutar R] [ execute-r-script] módulo. Ejecutar el código Python se introduce en el cuadro de parámetro como un nombre función llamada de punto de entrada `azureml_main`. Estos son los principios clave en el diseño utilizados para implementar este módulo:

1.  *Debe ser idiomáticas para usuarios de Python.* La mayoría de los usuarios de Python factor de su código, como funciones en los módulos, por lo que es poco la colocación de una gran cantidad de instrucciones ejecutables en un módulo de nivel superior. Como resultado, el cuadro de secuencia de comandos también tiene una función de Python especialmente con nombre en lugar de solo una secuencia de instrucciones. Los objetos expuestos en la función son los tipos de biblioteca Python estándar como tramas de datos de [Pandas](http://pandas.pydata.org/) y [NumPy](http://www.numpy.org/) matrices.
2.  *Debe tener alta fidelidad entre local y la nube ejecuciones.* El servidor que usa para ejecutar el código Python se basa en [Anaconda](https://store.continuum.io/cshop/anaconda/) 2.1, una distribución de Python científica de utilizado entre plataformas. Incluye cerca de 200 de los paquetes de Python más comunes. Por lo tanto, científicos de datos pueden depurar y calificar su código en su entorno de Anaconda compatibles con el aprendizaje de Azure equipo local. Utilice entornos de desarrollo, como el Bloc de notas de [IPython](http://ipython.org/) o [Python Tools para Visual Studio](http://aka.ms/ptvs) para ejecutar como parte de un ensayo de aprendizaje del equipo de Azure con confianza alta. Además, la `azureml_main` punto de entrada es una función de Python vainilla y puede crearse sin código específico de aprendizaje del equipo de Azure o el SDK instalado.
3.  *Debe ser ajustable sin problemas con otros módulos de aprendizaje del equipo de Azure.* La [Secuencia de comandos de ejecutar Python] [ execute-python-script] módulo acepta como entradas y salidas, conjuntos de datos de Azure aprendizaje estándar. El marco subyacente transparente y eficaz relaciona los tiempos de ejecución de aprendizaje del equipo de Azure y Python (compatible con características como valores que faltan). Python, por tanto, puede usarse junto con el aprendizaje Azure flujos de trabajo, incluidos aquellos que obtengan R y SQLite. Por lo tanto, uno puede prever flujos de trabajo que:
  * usar Python y Pandas para datos preprocesamiento y limpieza, 
  * fuente de los datos a una transformación de SQL, unirse a varios conjuntos de datos a las características de formulario 
  * entrenar modelos utilizando la amplia colección de algoritmos en Azure aprendizaje del equipo, y 
  * evaluar y proceso posterior de los resultados mediante R.


## <a name="basic-usage-scenarios-in-machine-learning-for-python-scripts"></a>Escenarios de uso básico en aprendizaje de secuencias de comandos de Python
En esta sección, nos encuesta algunos de los usos básicos del [Script de Python ejecutar] [ execute-python-script] módulo.
Como se mencionó anteriormente, las entradas en el módulo de Python se exponen como tramas de datos de Pandas. Occidental McKinney encontrará más información sobre Pandas Python y cómo se puede utilizar para manipular los datos y efectiva en *Python para análisis de datos* (o ' Reilly, 2012). La función debe devolver una única trama de datos de Pandas empaquetada dentro de una [secuencia](https://docs.python.org/2/c-api/sequence.html) de Python como una tupla, lista o matriz de NumPy. A continuación, se devuelve el primer elemento de esta secuencia en el primer puerto de salida del módulo. Esta combinación se muestra en la figura 2.

![image3](./media/machine-learning-execute-python-scripts/map-of-python-script-inputs-outputs.png)

Figura 2. Asignación de puertos a los parámetros de entrada y valor devuelto al puerto de salida.

Obtener más semántica de ¿cómo se asignan los puertos de entrada a los parámetros de la `azureml_main` función se muestran en la tabla 1:

![image1T](./media/machine-learning-execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

Tabla 1. Asignación de puertos de entrada para los parámetros de función.

La asignación entre puertos de entrada y los parámetros de función es posición. Se asigna el primer puerto de entrada conectado al primer parámetro de la función y la segunda entrada (si está conectado) se asigna al segundo parámetro de la función.

## <a name="translation-of-input-and-output-types"></a>Conversión de tipos de entrada y salidos
Como se explica anteriormente, conjuntos de datos de entrada en el aprendizaje Azure se convierten en marcos en Pandas y salida datos se convierten a conjuntos de datos de Azure el aprendizaje de datos. Se realizan las conversiones siguientes:

1.  Las columnas de cadena y numéricos se convierten como-está y los valores que faltan en un conjunto de datos se convierten en valores 'ND' en Pandas. Pasa la misma conversión de vuelta (valores que faltan en Azure aprendizaje convierte los valores de ND en Pandas).
2.  Índice vectores en Pandas no son compatibles con el aprendizaje Azure. Todas las tramas de datos de entrada de la función Python siempre tienen un índice numérico de 64 bits comprendido entre 0 y el número de filas menos 1. 
3.  Conjuntos de datos de Azure aprendizaje de máquina no pueden tener nombres de columna duplicados y nombres de columna que no son cadenas. Si un marco de datos de salida contiene columnas no es numérico, el marco llama `str` en los nombres de columna. Asimismo, los nombres de columna duplicados se alteran automáticamente para asegurarse de que los nombres son únicos. El sufijo (2) se agrega a la primera duplicado, (3) a la segunda etcetera duplicado.

## <a name="operationalizing-python-scripts"></a>Las secuencias de comandos de Python en marcha
Cualquier [Secuencia de comandos de ejecutar Python] [ execute-python-script] se denominan módulos utilizados en un ensayo puntuación cuando se publica como un servicio web. Por ejemplo, la figura 3 muestra un ensayo puntuación que contiene el código para evaluar una expresión Python. 

![image4](./media/machine-learning-execute-python-scripts/figure3a.png)

![image5](./media/machine-learning-execute-python-scripts/python-script-with-python-pandas.png)

Figura 3. Servicio Web para evaluar una expresión de Python.

Un servicio web creado a partir de este ensayo se toma como una expresión de Python (como una cadena), se envía al intérprete Python y devuelve una tabla que contiene la expresión y el resultado de la entrada.

## <a name="importing-existing-python-script-modules"></a>Importar módulos de secuencia de comandos de Python existentes
Un caso de uso común para muchos científicos de datos es incorporar secuencias de comandos de Python existentes en el aprendizaje Azure experimentación. En lugar de concatenar y pegar todo el código en el cuadro de una única secuencia de comandos, la [Secuencia de comandos de ejecutar Python] [ execute-python-script] módulo acepta un tercer puerto de entrada a la que se pueden conectar un archivo zip que contiene los módulos Python. El archivo, a continuación, se descomprimir el marco de ejecución en tiempo de ejecución y el contenido se agrega a la ruta de biblioteca del intérprete de Python. La `azureml_main` función, a continuación, puede importar estos módulos directamente el punto de entrada.

Por ejemplo, considere el archivo Hello.py que contiene una función simple "Hola, mundo".

![image6](./media/machine-learning-execute-python-scripts/figure4.png)

Figura 4. Función definida por el usuario.

A continuación, creamos un archivo Hello.zip que contiene Hello.py:

![image7](./media/machine-learning-execute-python-scripts/figure5.png)

Figura 5. Archivo de ZIP que contiene el código de Python definidas por el usuario.

A continuación, cargar esto como un conjunto de datos en Azure máquina aprendizaje Studio. Crear y ejecutar un ensayo simple que usa el código Python en el archivo Hello.zip adjuntando para el tercer puerto de entrada de la secuencia de comandos de la Python ejecutar, tal como se muestra en esta ilustración.

![image8](./media/machine-learning-execute-python-scripts/figure6a.png)

![image9](./media/machine-learning-execute-python-scripts/figure6b.png)

Figura 6. Experimentar de ejemplo con código de Python definidas por el usuario cargados como un archivo zip.

El módulo de salida muestra que el archivo zip se ha desempaquetadas y la función `print_hello` en realidad se ha ejecutado.
 
![image10](./media/machine-learning-execute-python-scripts/figure7.png)
 
Figura 7. Función definida por el usuario en uso dentro de la [Secuencia de comandos de ejecutar Python] [ execute-python-script] módulo.

## <a name="working-with-visualizations"></a>Trabajar con visualizaciones
Trazados creados con MatplotLib que pueden visualizarse en el explorador pueden ser devueltas por la [Secuencia de comandos de ejecutar Python][execute-python-script]. Pero la trazados no se redireccionan automáticamente a las imágenes que aparecen al usar R. Así que el usuario debe guardar explícitamente cualquier trazados a archivos PNG si son que se devolverá a Azure el aprendizaje. 

Con el fin de generar imágenes de MatplotLib, debe competir el procedimiento siguiente:

* cambiar el back-end "AGG" desde el procesador basado en Qt predeterminado 
* crear un nuevo objeto de figura 
* obtener el eje y generar todos los trazados en él 
* guardar la ilustración a un archivo PNG 

Este proceso se muestra en la siguiente figura 8 que crea una matriz de trazado de dispersión con la función scatter_matrix en Pandas.
 
![image1v](./media/machine-learning-execute-python-scripts/figure-v1-8.png)

Figura 8. Guardar MatplotLib ilustraciones en imágenes.



Figura 9 muestra un ensayo que usa el script descrito anteriormente para devolver traza a través del segundo puerto de salida.

![image2v](./media/machine-learning-execute-python-scripts/figure-v2-9a.png) 
     
![image2v](./media/machine-learning-execute-python-scripts/figure-v2-9b.png) 

Figura 9. Visualización de trazados generados a partir de código Python.

Es posible devolver varias figuras guardándolos en imágenes diferentes, el tiempo de ejecución de aprendizaje del equipo de Azure recoge todas las imágenes y los concatena para visualización.


## <a name="advanced-examples"></a>Ejemplos avanzados
El entorno de Anaconda instalado en el equipo de Azure aprendizaje contiene paquetes comunes como NumPy, SciPy y aprenda Scikits y estos se pueden utilizar eficazmente para varias tareas de procesamiento de datos en una canalización de aprendizaje de equipo típico. Por ejemplo, el siguiente procedimiento y el script muestra el uso de los alumnos de conjunto de obtener Scikits para calcular la puntuación de importancia característica para un conjunto de datos. Los resultados, a continuación, pueden utilizarse para realizar la selección de características controlados antes de alimentación en modelo de aprendizaje de otro equipo.

A continuación se muestra la función Python para calcular las puntuaciones de importancia y el orden de las características que se basan en:

![image11](./media/machine-learning-execute-python-scripts/figure8.png)

Figura 10. Función JERARQUIA características por la puntuación.
 A continuación, el siguiente procedimiento calcula y devuelve los resultados de la importancia de características en el conjunto de datos "Pima hindú Diabetes" en el aprendizaje Azure:

![image12](./media/machine-learning-execute-python-scripts/figure9a.png)
![image13](./media/machine-learning-execute-python-scripts/figure9b.png)    
    
Figura 11. Pruebe las características de jerarquía del conjunto de datos Pima hindú Diabetes.

## <a name="limitations"></a>Limitaciones 
La [Secuencia de comandos de ejecutar Python] [ execute-python-script] actualmente tiene las limitaciones siguientes:

1.  *Ejecución de espacio aislada.* El tiempo de ejecución de Python está actualmente en espacio aislado y, por tanto, no permite el acceso a la red o al sistema de archivos local de manera permanente. Todos los archivos que se guardan localmente son aislados y eliminar una vez que finalice el módulo. El código Python no puede acceder a la mayoría de directorios en el equipo que se ejecuta en, la excepción es el directorio actual y sus subdirectorios.
2.  *Falta de desarrollo sofisticadas y compatibilidad de depuración.* El módulo de Python actualmente no admite características del IDE como intellisense y la depuración. Además, si se produce un error en el módulo en tiempo de ejecución, el seguimiento de pila de Python completo está disponible, pero debe verse en el registro de salida para el módulo. Actualmente, se recomienda desarrollar y depurar sus secuencias de comandos de Python en un entorno como IPython y, a continuación, importar el código en el módulo.
3.  *Resultado de marco de datos único.* El punto de entrada de Python solo puede devolver un marco de datos como resultado. No es posible volver arbitrario Python objetos, como modelos capacitados directamente a la ejecución de aprendizaje del equipo de Azure actualmente. Como [Ejecutar Script de R][execute-r-script], que tiene la misma limitación, resulta sin embargo posible en muchos casos pickle objetos en una matriz de bytes y, a continuación, volver ese interior de un marco de datos.
4.  *No puede personalizar la instalación de Python*. Actualmente, la única manera de agregar módulos Python personalizados es mediante el mecanismo de archivo zip que se ha descrito anteriormente. Aunque esto es factible para módulos pequeños, es complejo para grandes módulos (especialmente las DLL nativas) o una gran cantidad de módulos. 


##<a name="conclusions"></a>Conclusiones
La [Secuencia de comandos de ejecutar Python] [ execute-python-script] módulo permite un científico de datos para incorporar código Python existente en flujos de trabajo de aprendizaje de máquina hospedada en la nube en aprendizaje de máquina de Azure y que controle sin problemas como parte de un servicio web. El módulo de script de Python naturalmente interactúa con otros módulos de aprendizaje del equipo de Azure y puede utilizarse para un rango de las tareas de preprocesamiento, para la extracción de la característica, evaluación y posterior al procesamiento de los resultados de exploración de datos. El tiempo de ejecución de back-end utilizado para ejecución se basa en Anaconda, una distribución de Python comprobada y utiliza. Esto facilita que integrada activos del código existente en la nube.

Esperamos proporcionar funcionalidad adicional al [Ejecutar secuencia de comandos de Python] [ execute-python-script] módulo, como la capacidad para formar y operativa modelos en Python y agregar una mejor compatibilidad para el desarrollo y depuración de código en Azure máquina aprendizaje Studio.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información, consulte el [Centro para desarrolladores de Python](/develop/python/).

<!-- Module References -->
[execute-python-script]: https://msdn.microsoft.com/library/azure/cdb56f95-7f4c-404d-bde7-5bb972e6f232/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/

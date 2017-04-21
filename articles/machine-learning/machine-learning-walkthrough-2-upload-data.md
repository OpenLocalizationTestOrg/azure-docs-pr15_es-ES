<properties
    pageTitle="Paso 2: Cargar datos en un ensayo de aprendizaje de equipo | Microsoft Azure"
    description="Paso 2 de la desarrollar soluciones predictiva Tutorial: cargar almacena datos públicos en Azure máquina aprendizaje Studio."
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016" 
    ms.author="garye"/>


# <a name="walkthrough-step-2-upload-existing-data-into-an-azure-machine-learning-experiment"></a>Tutorial paso 2: Cargar datos existentes en un ensayo de aprendizaje del equipo de Azure

Este es el segundo paso del tutorial, [desarrollar una solución de analítica predictiva en aprendizaje de máquina de Azure](machine-learning-walkthrough-develop-predictive-solution.md)


1.  [Crear un área de trabajo de aprendizaje de equipo](machine-learning-walkthrough-1-create-ml-workspace.md)
2.  **Cargar datos existentes**
3.  [Crear un nuevo ensayo](machine-learning-walkthrough-3-create-new-experiment.md)
4.  [Entrenar y evaluar los modelos](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.  [Implementar el servicio web](machine-learning-walkthrough-5-publish-web-service.md)
6.  [Acceso al servicio web](machine-learning-walkthrough-6-access-web-service.md)

----------

Para desarrollar un modelo de predicción de riesgo, debemos datos que podemos usar para formar a y, a continuación, pruebe el modelo. En este tutorial, usaremos el "conjunto de datos UCI Statlog (datos de crédito alemán)" desde el repositorio de aprendizaje de máquina UCI. Puede encontrar aquí:  
<a href="http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">http://Archive.ICS.uci.edu/ml/datasets/Statlog+(German+Credit+Data)</a>

Usaremos el archivo denominado **german.data**. Descargue este archivo en la unidad de disco dura local.  

Este conjunto de datos contiene filas de 20 variables de 1000 candidatos pasados de crédito. Estas 20 variables representan vector de característica del conjunto de datos, que proporciona características de identificación para cada candidato de crédito. Una columna adicional en cada fila representa calculado riesgo del candidato, con los 700 candidatos identificados como un riesgo bajo y 300 como un alto riesgo.

El sitio Web de UCI proporciona una descripción de los atributos del vector característica, que incluyen información financiera, historial de crédito, el estado de empleo e información personal. Para cada candidato una clasificación binaria se ha determinado que indica si son un bajo o alto riesgo de crédito.  

Usaremos estos datos para formar un modelo de analítica predictiva. Cuando hemos terminado, nuestro modelo debería poder aceptar información para personas nuevas y predecir si son un riesgo de crédito alta o baja.  

Aquí tiene un giro interesante. La descripción del conjunto de datos explica clasifique mal a una persona como un riesgo de crédito baja cuando son en realidad un riesgo alto es 5 veces más costoso de la institución financiera que clasifique mal un riesgo baja como alta. Duplicar (5 veces) a las entradas que representan alguien con un alto riesgo es una forma sencilla de tener esto en cuenta en nuestra prueba. A continuación, si el modelo misclassifies un alto riesgo como baja, lo hará que misclassification 5 veces, una vez por cada duplicado. Esto incrementará el costo de este error en los resultados de aprendizaje.  

##<a name="convert-the-dataset-format"></a>Convertir el formato del conjunto de datos
El conjunto de datos original utiliza un formato delimitado por el espacio en blanco. Máquina aprendizaje Studio funciona mejor con un archivo de valores separados por comas (CSV), por lo que deberá convertir el conjunto de datos reemplazando espacios con comas.  

Hay muchas formas de convertir los datos. Una forma es mediante el siguiente comando de Windows PowerShell:   

    cat german.data | %{$_ -replace " ",","} | sc german.csv  

Otra manera es mediante el comando sed Unix:  

    sed 's/ /,/g' german.data > german.csv  

En cualquier caso, se ha creado una versión de valores separados por comas de los datos en un archivo llamado **german.csv** que usaremos en nuestra prueba.

##<a name="upload-the-dataset-to-machine-learning-studio"></a>Cargar el conjunto de datos en el equipo aprendizaje Studio

Una vez que los datos se ha convertido en un archivo CSV, es necesario cargar en Studio de aprendizaje del equipo. Para obtener más información sobre cómo empezar con Machine aprendizaje Studio, vea [Microsoft Azure máquina aprendizaje Studio hogar](https://studio.azureml.net/).

1.  Abra el aprendizaje Studio ([https://studio.azureml.net](https://studio.azureml.net)). Cuando se le solicite para iniciar sesión, use la cuenta especificada como propietario del área de trabajo.
1.  Haga clic en la ficha **Studio** en la parte superior de la ventana.
1.  Haga clic en **+ nuevo** en la parte inferior de la ventana.
1.  Seleccione el **conjunto de datos**.
1.  Seleccione **archivo LOCAL de FROM**.
1.  En el cuadro de diálogo **cargar un nuevo conjunto de datos** , haga clic en **Examinar** y busque el archivo **german.csv** que creó.
1.  Escriba un nombre para el conjunto de datos. En este tutorial, se deberá llamar "Datos de tarjeta de crédito de UCI alemán".
1.  Tipo de datos, seleccione **archivo CSV genérico sin encabezado (. nh.csv)**.
1.  Agregar una descripción si lo desea.
1.  Haga clic en **Aceptar**.  

![Cargar el conjunto de datos][1]  


Esto carga los datos en un módulo de conjunto de datos que se puede usar en una prueba.

> [AZURE.TIP] Para administrar conjuntos de datos que haya cargado en Studio, haga clic en la pestaña de **conjuntos de datos** a la izquierda de la ventana Studio.

Para obtener más información sobre cómo importar varios tipos de datos en un ensayo, vea [Importar datos formación en Azure máquina aprendizaje Studio](machine-learning-data-science-import-data.md).

**[Crear un nuevo ensayo](machine-learning-walkthrough-3-create-new-experiment.md) del siguiente:**

[1]: ./media/machine-learning-walkthrough-2-upload-data/upload1.png

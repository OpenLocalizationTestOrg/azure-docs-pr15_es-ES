<properties
    pageTitle="Paso 3: Crear un nuevo ensayo de aprendizaje de equipo | Microsoft Azure"
    description="Paso 3 de la desarrollar soluciones predictiva Tutorial: crear un nuevo ensayo formación en Azure máquina aprendizaje Studio."
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
    ms.topic="article"
    ms.date="10/05/2016" 
    ms.author="garye"/>


# <a name="walkthrough-step-3-create-a-new-azure-machine-learning-experiment"></a>Tutorial paso 3: Crear un nuevo ensayo de aprendizaje del equipo de Azure

Este es el tercer paso del tutorial, [desarrollar una solución de analítica predictiva en aprendizaje de máquina de Azure](machine-learning-walkthrough-develop-predictive-solution.md)


1.  [Crear un área de trabajo de aprendizaje de equipo](machine-learning-walkthrough-1-create-ml-workspace.md)
2.  [Cargar datos existentes](machine-learning-walkthrough-2-upload-data.md)
3.  **Crear un nuevo ensayo**
4.  [Entrenar y evaluar los modelos](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.  [Implementar el servicio web](machine-learning-walkthrough-5-publish-web-service.md)
6.  [Acceso al servicio web](machine-learning-walkthrough-6-access-web-service.md)

----------

El siguiente paso en este tutorial es crear un ensayo en equipo Studio de aprendizaje que usa el conjunto de datos que se cargó.  

1.  Studio, haga clic en **+ nuevo** en la parte inferior de la ventana.
2.  Seleccione **ensayo**y, a continuación, seleccione "Ensayo en blanco". Seleccione el nombre de ensayo de forma predeterminada en la parte superior del lienzo y asígnele un nombre significativo

    > [AZURE.TIP] Es una buena práctica para rellenar **Resumen** y una **Descripción** para el ensayo en el panel **Propiedades** . Estas propiedades proporcionan la oportunidad para el ensayo del documento para que cualquier persona que mira más adelante comprendan los objetivos y la metodología.

3.  En la paleta de módulo a la izquierda de la ensayo lienzo, expanda **Guardado conjuntos de datos**.
4.  Busque el conjunto de datos creado en **Conjuntos de datos de mi** y arrástrelo hasta el lienzo. También puede encontrar el conjunto de datos escribiendo el nombre en el cuadro de **búsqueda** situado encima de la paleta.  

##<a name="prepare-the-data"></a>Preparar los datos
Puede ver las primeros 100 filas de los datos e información estadística para todo el conjunto de datos haciendo clic en el puerto de salida del conjunto de datos (el círculo pequeño en la parte inferior) y seleccionando **Ver**.  

Dado que el archivo de datos no incluye los encabezados de columna, Studio ha proporcionado encabezados genéricos (Col1, Col2, *etc.*). Buenos encabezados no son fundamentales para crear un modelo, pero hacen que sea más fácil trabajar con los datos en la prueba. Además, cuando se publica finalmente este modelo en un servicio web, los encabezados de le ayudará a identificar las columnas que el usuario del servicio.  

Podemos agregar encabezados de columna con los [Metadatos de editar] [ edit-metadata] módulo.
Usar los [Metadatos de editar] [ edit-metadata] módulo para cambiar los metadatos asociados con un conjunto de datos. En este caso, puede proporcionar más nombres descriptivos para los encabezados de columna. 

Uso de [Metadatos de editar][edit-metadata], primero especificar las columnas que desea modificar (en este caso, todos ellos.) A continuación, puede especificar la acción que se realicen en esas columnas (en este caso, cambiar los encabezados de columna.)

1.  En la paleta de módulo, escriba "metadatos" en el cuadro de **búsqueda** . Verá [Editar metadatos] [ edit-metadata] aparecen en la lista módulo.
2.  Haga clic y arrastre los [Metadatos de editar] [ edit-metadata] módulo en el lienzo y colóquelo debajo del conjunto de datos que agregó anteriormente.
3.  Conectar el conjunto de datos a los [Metadatos de editar][edit-metadata]: haga clic en el puerto de salida del conjunto de datos (el círculo pequeño en la parte inferior del conjunto de datos). A continuación, arrastre hasta el puerto de entrada de [Editar los metadatos] [ edit-metadata] (círculo pequeño en la parte superior del módulo), a continuación, suelte el botón del mouse. El conjunto de datos y el módulo permanecen conectados, incluso si se mueven ya sea en el lienzo.

    El ensayo debe ahora el siguiente aspecto:  

    ![Adición de metadatos de edición][2]
    
    El signo de exclamación rojo indica que se no hemos debe establecer las propiedades para este módulo todavía. Haremos que siguiente.
    
    > [AZURE.TIP] Puede agregar un comentario a un módulo haciendo doble clic en el módulo y escribiendo texto. Esto puede ayudarle a ver de un vistazo lo que hace el módulo en la prueba. En este caso, haga doble clic en los [Metadatos de editar] [ edit-metadata] módulo y escriba el comentario "Agregar encabezados de columna". Haga clic en cualquier lugar en el lienzo para cerrar el cuadro de texto. Para mostrar el comentario, haga clic en la flecha abajo en el módulo.

4.  Seleccione [Editar metadatos][edit-metadata], a continuación, en el panel de **Propiedades** a la derecha del lienzo, haga clic en **iniciar el selector de columna**.
5.  En el cuadro de diálogo **Seleccionar columnas** , seleccione todas las filas en **Columnas disponibles** y haga clic en > para moverlos a las **Columnas seleccionadas**.
El cuadro de diálogo debe tener un aspecto similar a este: ![Selector de columna con todas las columnas seleccionadas][4]
7.  Haga clic en la marca de verificación **Aceptar** .
8.  En el panel **Propiedades** , busque el parámetro **nuevos nombres de columna** . En este campo, escriba una lista de nombres para las 21 columnas en el conjunto de datos, separados por comas y, en el orden de las columnas. Puede obtener los nombres de las columnas de la documentación del conjunto de datos en el sitio Web UCI o para mayor comodidad puede copiar y pegar en la lista siguiente:  

          Status of checking account, Duration in months, Credit history, Purpose, Credit amount, Savings account/bond, Present employment since, Installment rate in percentage of disposable income, Personal status and sex, Other debtors, Present residence since, Property, Age in years, Other installment plans, Housing, Number of existing credits, Job, Number of people providing maintenance for, Telephone, Foreign worker, Credit risk  

    El panel Propiedades tendrá este aspecto:

    ![Propiedades de los metadatos de edición][1]

> [AZURE.TIP] Si desea comprobar los encabezados de columna, ejecute el ensayo (haga clic en **Ejecutar** debajo del lienzo de ensayo). Al finalizar la ejecución (aparecerá una marca de verificación verde en [Editar metadatos][edit-metadata]), haga clic en el puerto de salida de los [Metadatos de editar] [ edit-metadata] módulo y seleccione **Ver**. Puede ver el resultado de cualquier módulo de la misma forma para ver el progreso de los datos mediante el ensayo.

##<a name="create-training-and-test-datasets"></a>Crear formación y probar los conjuntos de datos

El paso siguiente de la ensayo es generar conjuntos de datos independiente que usaremos para formación y probar nuestro modelo.

Para ello, usamos los [Datos divididos] [ split] módulo.  

1.  Buscar los [Datos divididos] [ split] módulo, arrástrelo hasta el lienzo y conectarse a los últimos [Metadatos editar] [ edit-metadata] módulo.
2.  De forma predeterminada, la relación de división es 0,5 y se establece el parámetro **Randomized dividir** . Esto significa que aleatorio medio de los datos es resultados a través de un puerto de los [Datos divididos] [ split] módulo y la mitad a través de la otra. Puede ajustar estos, así como el parámetro de **valor de inicialización aleatorio** , para cambiar la división entre el aprendizaje y la comprobación de datos. En este ejemplo, deberá dejar como-es.
    
    > [AZURE.TIP] La **fracción de filas del primer conjunto de datos de salida** de la propiedad determina la cantidad de los datos de salida a través del puerto de salida izquierda. Por ejemplo, si establece la relación de 0,7, a continuación, 70% de los datos es imprimir mediante el puerto izquierdo y 30% mediante el puerto de la derecha.  
    
3. Haga doble clic en los [Datos divididos] [ split] módulo y escriba el comentario, "datos de formación y prueba dividen 50%". 

Podemos utilizar los resultados de los [Datos divididos] [ split] módulo sin embargo, como, pero vamos a decide utilizar los resultados de la izquierda los datos como pruebas de salida de datos de entrenamiento y a la derecha.  

Como se mencionó en el sitio Web UCI, el coste de clasifique mal un alto riesgo como baja es cinco veces mayor que el coste de clasifique mal un riesgo baja como alta. Para la cuenta, se genera un nuevo conjunto de datos que refleja esta función de costo. En el nuevo conjunto de datos, cada ejemplo de alto riesgo se duplica cinco veces, mientras que no se duplica cada ejemplo bajo riesgo.   

Podemos hacer esta replicación usando código R:  

1.  Buscar y arrastre el [Script de R ejecutar] [ execute-r-script] módulo en el ensayo lienzo y conecte el puerto de salida izquierda de los [Datos divididos] [ split] módulo en el primer puerto de entrada ("Dataset1") de la [Secuencia de comandos de ejecutar R] [ execute-r-script] módulo.
2. Haga doble clic en la [Secuencia de comandos de ejecutar R] [ execute-r-script] módulo y escriba el comentario "Conjunto de ajuste de costes".
2.  En el panel **Propiedades** , elimine el texto predeterminado en el parámetro de **Secuencia de comandos de R** e introduzca esta secuencia de comandos:

          dataset1 <- maml.mapInputPort(1)
          data.set<-dataset1[dataset1[,21]==1,]
          pos<-dataset1[dataset1[,21]==2,]
          for (i in 1:5) data.set<-rbind(data.set,pos)
          maml.mapOutputPort("data.set")


Es necesario realizar esta operación de replicación mismo para cada salida de los [Datos divididos] [ split] módulo para que los datos de pruebas y formación tienen el mismo ajuste de costo.

1.  Haga clic en la [Secuencia de comandos de ejecutar R] [ execute-r-script] módulo y seleccione **Copiar**.
2.  Haga clic en el lienzo de ensayo y seleccione **Pegar**.
3.  Conecte el primer puerto de entrada de esta [Secuencia de comandos de ejecutar R] [ execute-r-script] módulo a la derecha de salida puerto de los [Datos divididos] [ split] módulo. 
4.  En la parte inferior del lienzo, haga clic en **Ejecutar**. 

> [AZURE.TIP] La copia del módulo ejecutar R Script contiene la misma secuencia de comandos que el módulo original. Cuando copia y pega un módulo en el lienzo, la copia conserva todas las propiedades de la celda original.  

Nuestra ensayo ahora tiene un aspecto similar a este:

![Agregar módulo de división y secuencias de comandos de R][3]

Para obtener más información sobre el uso de secuencias de comandos de R en su experimentación, vea [extender su experimentar con R](machine-learning-extend-your-experiment-with-r.md).

**Siguiente: [tren y evaluar los modelos](machine-learning-walkthrough-4-train-and-evaluate-models.md)**


[1]: ./media/machine-learning-walkthrough-3-create-new-experiment/create1.png
[2]: ./media/machine-learning-walkthrough-3-create-new-experiment/create2.png
[3]: ./media/machine-learning-walkthrough-3-create-new-experiment/create3.png
[4]: ./media/machine-learning-walkthrough-3-create-new-experiment/columnselector.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

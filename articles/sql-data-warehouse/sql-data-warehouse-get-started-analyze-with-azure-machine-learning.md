<properties
   pageTitle="Analizar los datos con el aprendizaje Azure | Microsoft Azure"
   description="Use el aprendizaje del equipo de Azure para crear una máquina predictiva aprendizaje modelo basado en los datos almacenados en el almacén de datos de SQL Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="kevinvngo"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/14/2016"
   ms.author="kevin;barbkess;sonyama"/>

# <a name="analyze-data-with-azure-machine-learning"></a>Analizar los datos con el aprendizaje Azure

> [AZURE.SELECTOR]
- [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
- [Aprendizaje del equipo de Azure](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
- [Visual Studio](sql-data-warehouse-query-visual-studio.md)
- [Sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 

Este tutorial utiliza Azure máquina aprendizaje para generar una máquina predictiva aprendizaje modelo basado en los datos almacenados en el almacén de datos de SQL Azure. Más concretamente, esto crea una campaña de marketing concreta de Adventure Works, la tienda de bicicletas predecir si un cliente es probable que comprar una bicicleta o no.

> [AZURE.VIDEO integrating-azure-machine-learning-with-azure-sql-data-warehouse]


## <a name="prerequisites"></a>Requisitos previos
Paso a través de este tutorial, necesita:

- Un almacén de datos de SQL se ha cargado previamente con datos de ejemplo AdventureWorksDW. Para aprovisionar esto, vea [crear un almacén de datos de SQL][] y elija cargar los datos de ejemplo. Si ya tiene un almacén de datos, pero no tiene datos de ejemplo, puede [cargar los datos de ejemplo manualmente][].

## <a name="1-get-data"></a>1. obtener datos
Los datos están en la vista de dbo.vTargetMail en la base de datos de AdventureWorksDW. Para leer estos datos:

1. Inicie sesión en [el aprendizaje Azure studio][] y haga clic en mi experimentación.
2. Haga clic en **+ nuevo** y seleccione **Ensayo en blanco**.
3. Escriba un nombre para el ensayo: Marketing de destino.
4. Arrastre el módulo de **lector** desde el panel de módulos en el lienzo.
5. En el panel Propiedades, especifique los detalles de la base de datos de almacén de datos de SQL.
6. Especifique la **consulta** de base de datos para leer los datos de interés.

```sql
SELECT [CustomerKey]
  ,[GeographyKey]
  ,[CustomerAlternateKey]
  ,[MaritalStatus]
  ,[Gender]
  ,cast ([YearlyIncome] as int) as SalaryYear
  ,[TotalChildren]
  ,[NumberChildrenAtHome]
  ,[EnglishEducation]
  ,[EnglishOccupation]
  ,[HouseOwnerFlag]
  ,[NumberCarsOwned]
  ,[CommuteDistance]
  ,[Region]
  ,[Age]
  ,[BikeBuyer]
FROM [dbo].[vTargetMail]
```

Ejecutar el ensayo haciendo clic en **Ejecutar** en el lienzo de ensayo.
![Ejecutar el ensayo][1]


Cuando el ensayo termine ejecutando correctamente, haga clic en el puerto de salida en la parte inferior del módulo lector y seleccione **Ver** para ver los datos importados.
![Ver datos importados][3]


## <a name="2-clean-the-data"></a>2. limpiar los datos
Para limpiar los datos, quitar algunas columnas que no son relevantes para el modelo. Para hacer esto:

1. Arrastre el módulo de **Columnas de proyecto** en el lienzo.
2. Haga clic en **selector de columna de inicio** en el panel de propiedades para especificar las columnas que desea eliminar.
![Columnas de proyecto][4]

3. Excluir dos columnas: CustomerAlternateKey y GeographyKey.
![Quitar las columnas innecesarias][5]


## <a name="3-build-the-model"></a>3. crear el modelo
Dividimos el 80-20 de datos: 80% para formar una máquina modelo y el 20% para probar el modelo de aprendizaje. Haremos que utilice los algoritmos "Dos clase" para este problema binario clasificación.

1. Arrastre el módulo de **división** en el lienzo.
2. Escriba 0,8 de fracción de filas en el primer conjunto de datos de salida en el panel Propiedades.
![Dividir datos en el conjunto de prueba y formación][6]
3. Arrastre el módulo de **Clase de dos aumenta árbol de decisión** en el lienzo.
4. Arrastre el módulo de **Tren modelo** en el lienzo y especificar las entradas. A continuación, haga clic en **iniciar el selector de columna** en el panel Propiedades.
      - Primera entrada: algoritmo de m.
      - Segunda entrada: datos para formar el algoritmo de.
![Conecte el módulo de tren modelo][7]
5. Seleccione la columna **BikeBuyer** como la columna predecir.
![Seleccione la columna a predecir][8]


## <a name="4-score-the-model"></a>4. el modelo de la puntuación
Ahora, nos probar cómo se realiza el modelo de datos de prueba. Se compara el algoritmo de nuestra elección con un algoritmo diferente para ver que funciona mejor.

1. Arrastre el módulo de **Modelo de puntuación** en el lienzo.
    Primera entrada: capacitado modelo segunda entrada: probar datos ![puntuación del modelo][9]
2. Arrastre la **Clase dos Bayes punto máquina** el lienzo de ensayo. Se compara cómo realiza este algoritmo en comparación con el árbol de decisión de clase de dos aumenta.
3. Copie y pegue los módulos tren modelo y puntuación en el lienzo.
4. Arrastre el módulo de **Modelo evaluar** en el lienzo para comparar los dos algoritmos.
5. **Ejecutar** el ensayo.
![Ejecutar el ensayo][10]
6. Haga clic en el puerto de salida en la parte inferior del módulo evaluar modelo y haga clic en ver.
![Visualizar los resultados de la evaluación][11]

Las mediciones proporcionadas son la curva ROC, curva de diagrama y elevación de recuperación de precisión. Mirar estas métricas, podemos ver que el primer modelo realiza mejor que la segunda. Para ver qué el primer modelo pronosticado, haga clic en el puerto de salida del modelo de puntuación y haga clic en ver.
![Visualizar los resultados de puntuación][12]

Verá dos columnas más agregadas al conjunto de datos de prueba.

- Probabilidades puntuadas: la probabilidad de que un cliente es un comprador de bicicleta.
- Etiquetas puntuadas: la clasificación realizada por el modelo: comprador de bicicleta (1) o no (0). Este umbral de probabilidad para etiquetar se establece en 50% y se puede ajustar.

Comparación de la columna BikeBuyer (real) con las etiquetas de puntuación (predicción), puede ver cómo se ha realizado el modelo. Como los pasos siguientes, puede usar este modelo para realizar predicciones para los nuevos clientes y publicar este modelo como un servicio web o para escribir resultados en el almacén de datos de SQL.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre cómo crear modelos de aprendizaje de máquina predictiva, consulte [Introducción al equipo de aprendizaje en Azure][].

<!--Image references-->
[1]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img1_reader.png
[2]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img2_visualize.png
[3]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img3_readerdata.png
[4]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img4_projectcolumns.png
[5]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img5_columnselector.png
[6]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img6_split.png
[7]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img7_train.png
[8]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img8_traincolumnselector.png
[9]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img9_score.png
[10]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img10_evaluate.png
[11]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img11_evalresults.png
[12]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img12_scoreresults.png


<!--Article references-->
[Azure studio de aprendizaje de equipo]:https://studio.azureml.net/
[Introducción al equipo de aprendizaje en Azure]:https://azure.microsoft.com/documentation/articles/machine-learning-what-is-machine-learning/
[cargar datos de ejemplo manualmente]: sql-data-warehouse-load-sample-databases.md
[Crear un almacén de datos SQL]: sql-data-warehouse-get-started-provision.md

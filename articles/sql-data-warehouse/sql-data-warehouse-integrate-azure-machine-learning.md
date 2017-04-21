<properties
   pageTitle="Usar el aprendizaje Azure con almacén de datos SQL | Microsoft Azure"
   description="Tutorial para usar el aprendizaje Azure con el almacén de datos de SQL Azure para desarrollar soluciones."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="kevinvngo"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/16/2016"
   ms.author="kevin;barbkess;sonyama"/>

# <a name="use-azure-machine-learning-with-sql-data-warehouse"></a>Usar el aprendizaje Azure con el almacén de datos SQL

El aprendizaje Azure es un servicio de análisis predictiva totalmente administrado que se puede usar para crear modelos de predicción frente a los datos en el almacén de datos de SQL y, a continuación, publicar como servicios web listos para consumir. Aprenda los conceptos básicos de analíticas predicción y aprendizaje lea [Introducción al equipo de aprendizaje en Azure][].  A continuación, puede aprender a crear, entrenar, puntuación y prueba un modelo de aprendizaje de equipo mediante la [creación experimentar tutorial][].

En este artículo, aprenderá a hacer lo siguiente con [Azure máquina aprendizaje Studio][]:

- Leer los datos de la base de datos para crear, entrenar y la puntuación de un modelo de predicción
- Escribir datos en la base de datos


## <a name="read-data-from-sql-data-warehouse"></a>Leer los datos de almacén de datos SQL

Lee datos de la tabla Product en la base de datos de AdventureWorksDW.

### <a name="step-1"></a>Paso 1

Iniciar un nuevo ensayo haciendo clic en + nuevo en la parte inferior de la ventana de la máquina aprendizaje Studio, seleccione ensayo y, a continuación, seleccione experimentar en blanco. Seleccione el nombre de ensayo de forma predeterminada en la parte superior del lienzo y asígnele un nombre significativo, por ejemplo, la predicción de bicicleta precio.

### <a name="step-2"></a>Paso 2

Busque el módulo lector en la paleta de conjuntos de datos y módulos de la izquierda del lienzo de ensayo. Arrastre el módulo en el lienzo de ensayo.
![][drag_reader]

### <a name="step-3"></a>Paso 3

Seleccione el módulo de lector y rellene el panel Propiedades.

1. Seleccione la base de datos SQL Azure como origen de datos.
2. Nombre del servidor de base de datos: escriba el nombre del servidor. Puede usar el [portal de Azure][] para buscar lo siguiente.

![][server_name]

3. Nombre de la base de datos: escriba el nombre de una base de datos en el servidor que acaba de especificar.
4. Nombre de cuenta de usuario de servidor: escriba el nombre de usuario de una cuenta que tenga permisos de acceso para la base de datos.
5. Contraseña de cuenta de usuario de servidor: proporcione la contraseña para la cuenta de usuario.
6. Aceptar cualquier certificado de servidor: Utilice esta opción (menos segura) si desea omitir revisar el certificado de sitio antes de leer los datos.
7. Consulta de base de datos: escriba una instrucción SQL que describe los datos que quiere leer. En este caso, se leerá datos de la tabla producto con la siguiente consulta.


```SQL
SELECT ProductKey, EnglishProductName, StandardCost,
        ListPrice, Size, Weight, DaysToManufacture,
        Class, Style, Color
FROM dbo.DimProduct;
```

![][reader_properties]

### <a name="step-4"></a>Paso 4

1. Ejecutar el ensayo haciendo clic en ejecutar en el lienzo de ensayo.
2. Cuando finalice el ensayo, el módulo de lector tendrá una marca de verificación verde para indicar que se ha completado correctamente. Observe también la terminado ejecutando el estado en la esquina superior derecha.

![][run]

3. Para ver los datos importados, haga clic en el puerto de salida en la parte inferior del conjunto de datos automóvil y seleccione Ver.


## <a name="create-train-and-score-a-model"></a>Crear, entrenar y puntuación un modelo

Ahora puede usar este conjunto de datos:

- Crear un modelo: procesar datos y definir características
- Entrenar el modelo: elegir y aplicar un algoritmo de aprendizaje
- Puntuación y probar el modelo: predecir nuevo precio de bicicleta


![][model]

Para obtener más información sobre cómo crear, entrenar, puntuación y pruebe una máquina de uso del modelo de aprendizaje el [tutorial de experimentar de crear][].

## <a name="write-data-to-azure-sql-data-warehouse"></a>Escribir datos en el almacén de datos de SQL Azure

Escribimos el resultado de la tabla en la base de datos de AdventureWorksDW ProductPriceForecast.

### <a name="step-1"></a>Paso 1

Busque el módulo escritor en la paleta de conjuntos de datos y módulos de la izquierda del lienzo de ensayo. Arrastre el módulo en el lienzo de ensayo.

![][drag_writer]

### <a name="step-2"></a>Paso 2

Seleccione el módulo de escritor y rellene el panel Propiedades.

1. Seleccione la base de datos SQL Azure como el destino de datos.
2. Nombre del servidor de base de datos: escriba el nombre del servidor. Puede usar el [portal de Azure][] para buscar lo siguiente.
3. Nombre de la base de datos: escriba el nombre de una base de datos en el servidor que acaba de especificar.
4. Nombre de cuenta de usuario de servidor: escriba el nombre de usuario de una cuenta que tenga permisos de escritura para la base de datos.
5. Contraseña de cuenta de usuario de servidor: proporcione la contraseña para la cuenta de usuario.
6. Acepte cualquier certificado de servidor (inseguro): seleccione esta opción si no desea ver el certificado.
7. Lista de valores separados por comas de columnas que se guardarán: proporcione una lista de las columnas de resultado o conjunto de datos que desea enviar.
8. Nombre de la tabla de datos: especifique el nombre de la tabla de datos.
9. Lista de valores separados por comas de las columnas de tabla de datos: especifique los nombres de columna para usar en la nueva tabla. Los nombres de columna pueden ser diferentes de los que en el conjunto de datos de origen, pero debe incluir el mismo número de columnas aquí que defina para la tabla de resultados.
10. Número de filas escritas por operación de SQL Azure: puede configurar el número de filas que se escriben en una base de datos SQL en una operación.

![][writer_properties]

### <a name="step-3"></a>Paso 3

1. Ejecutar el ensayo haciendo clic en ejecutar en el lienzo de ensayo.
2. Cuando finalice el ensayo, todos los módulos tendrá una marca de verificación verde para indicar que completó correctamente.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más sugerencias de desarrollo, vea [información general sobre el desarrollo de almacén de datos de SQL][].

<!--Image references-->

[drag_reader]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-drag-reader.png
[server_name]: ./media/sql-data-warehouse-integrate-azure-machine-learning/dw-server-name.png
[reader_properties]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-reader-properties.png
[run]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-finished-running.png
[model]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-create-train-score-model.png
[drag_writer]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-drag-writer.png
[writer_properties]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-writer-properties.png

<!--Article references-->

[Información general sobre el desarrollo de almacén de datos SQL]: ./sql-data-warehouse-overview-develop.md
[Crear tutorial de ensayo]: https://azure.microsoft.com/documentation/articles/machine-learning-create-experiment/
[Introducción al equipo de aprendizaje en Azure]: https://azure.microsoft.com/documentation/articles/machine-learning-what-is-machine-learning/
[Máquina Azure aprendizaje Studio]: https://studio.azureml.net/Home
[Portal de Azure]: https://portal.azure.com/

<!--MSDN references-->

<!--Other Web references-->

[Azure Machine Learning documentation]: http://azure.microsoft.com/documentation/services/machine-learning/

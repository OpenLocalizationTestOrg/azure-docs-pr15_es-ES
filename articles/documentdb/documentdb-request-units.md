<properties 
    pageTitle="Solicitar unidades en DocumentDB | Microsoft Azure" 
    description="Obtenga información sobre cómo entender, especifique y estimar los requisitos de la unidad de solicitud de DocumentDB." 
    services="documentdb" 
    authors="syamkmsft" 
    manager="jhubbard" 
    editor="mimig" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="06/29/2016" 
    ms.author="syamk"/>

#<a name="request-units-in-documentdb"></a>Solicitar unidades en DocumentDB
Ahora disponible: DocumentDB [Calculadora de unidades de la solicitud](https://www.documentdb.com/capacityplanner). Más información en [Estimating necesita el rendimiento](documentdb-request-units.md#estimating-throughput-needs).

![Calculadora de rendimiento][5]

##<a name="introduction"></a>Introducción
Este artículo proporciona una descripción general de unidades de solicitud de [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/). 

Después de leer este artículo, podrá responder a las preguntas siguientes:  

-   ¿Qué son solicitar unidades y solicitar cargos?
-   ¿Cómo se puede especificar la capacidad de unidad de solicitud para una colección de?
-   ¿Cómo se puede estimar que necesidades de unidad de solicitud de la aplicación?
-   ¿Qué ocurre si se excede la capacidad de unidad de solicitud para una colección de?


##<a name="request-units-and-request-charges"></a>Solicitar unidades y solicitar cargos
DocumentDB ofrece un rendimiento rápido y predecible *reservar* recursos para satisfacer que necesidades de rendimiento de la aplicación.  Dado que la aplicación cargar y patrones de acceso cambian con el tiempo, DocumentDB le permite fácilmente aumentar o reducir la cantidad de rendimiento reservado disponible para su aplicación.

Con DocumentDB, rendimiento reservado se especifica en términos de unidades de solicitud de procesamiento por segundo.  Puede considerar que las unidades de solicitud como moneda de rendimiento, según la cual garantiza que *reservar* una cantidad de unidades de solicitud disponibles para su aplicación en por segundo.  Cada operación en DocumentDB - escribir un documento, realice una consulta, actualizar un documento - consume CPU, memoria y IOPS.  Es decir, cada operación incurre en un *cargo de solicitud*, que se expresa en *unidades de la solicitud*.  Descripción de los factores que afectan a los cargos de unidad de solicitud, junto con los requisitos de rendimiento de la aplicación, le permite ejecutar la aplicación como costo eficaz como sea posible. 

##<a name="specifying-request-unit-capacity"></a>Especificar la capacidad de unidad de solicitud
Al crear una colección de DocumentDB, especifique el número de unidades de la solicitud por segundo (RUs) que desea reservado para la colección.  Una vez creada la colección, la asignación completa de RUs especificado está reservada para su uso de la colección.  Cada colección se garantiza que ha dedicado y aislado las características de rendimiento.  

Es importante que tenga en cuenta que DocumentDB funciona en un modelo de reserva; es decir, se cargarán para la cantidad de rendimiento *reservado* para la colección, independientemente de la cantidad de que el rendimiento es conocidas *utilizado*.  Tenga en cuenta, sin embargo, como la carga de la aplicación, datos y cambio de patrones de uso que se puede escalar fácilmente arriba y abajo de la cantidad de reserva RUs a través de DocumentDB SDK o con el [Portal de Azure](https://portal.azure.com).  Para más información sobre el rendimiento de escala hacia arriba y hacia abajo, vea [niveles de rendimiento DocumentDB](documentdb-performance-levels.md).

##<a name="request-unit-considerations"></a>Consideraciones de unidad de solicitud
Al estimar el número de unidades de solicitud para reservar para la colección de DocumentDB, es importante tener las siguientes variables en cuenta:

- **Tamaño del documento**. Como los tamaños de documento aumentan las unidades consumidas para leer o escribir que los datos también aumentará.
- **Recuento de propiedades del documento**. Suponiendo que la indización predeterminado de todas las propiedades, las unidades consumidas para escribir un documento aumentará como aumenta de recuento de la propiedad.
- **Coherencia de los datos**. Al utilizar niveles de coherencia de datos de texto en negrita o antigüedad limitada, se consume unidades adicionales para leer documentos.
- **Propiedades indizadas**. Una directiva de índice en cada colección determina las propiedades que se indizan de manera predeterminada. Puede reducir el consumo de unidades solicitud limitando el número de propiedades indizadas o Habilitar indización diferida.
- La **indexación de documentos**. De forma predeterminada, que todos los documentos se indizan automáticamente, se utilizará menos unidades de solicitud si decide no indizar algunos de los documentos.
- **Patrones de consulta**. La complejidad de una consulta afecta cuántas unidades solicitar se consumen para una operación. El número de predicados, naturaleza de los predicados, proyecciones, número de UDF y el tamaño de todo el conjunto de datos de origen influir en el costo de operaciones de consulta.
- **Uso de la secuencia de comandos**.  Al igual que con las consultas, procedimientos almacenados y desencadenadores consuman unidades de solicitud en función de la complejidad de las operaciones que se realicen. Desarrollar la aplicación, inspeccionar la cabecera de documento de solicitud para comprender mejor cómo cada operación consume capacidad de unidad de solicitud.

##<a name="estimating-throughput-needs"></a>Estimación necesidades de rendimiento
Una unidad de solicitud es una medición normalizada de costo de procesamiento de solicitud. Una unidad única solicitud representa la capacidad de procesamiento necesaria para leer (a través de vínculo automático o el identificador) de un único documento de 1KB JSON formada por 10 valores de propiedad únicos (excepto las propiedades del sistema). Una solicitud para crear (Insertar), reemplazar o eliminar el mismo documento consume más procesamiento del servicio y, con ello, más unidades de la solicitud.   

> [AZURE.NOTE] La línea de base de la unidad de 1 solicitud para un documento de 1KB corresponde al obtener simple en vínculo automático o el identificador del documento.

###<a name="use-the-request-unit-calculator"></a>Utilice la calculadora de unidades de la solicitud
Para ayudar a clientes bien ajustar sus estimaciones de rendimiento, hay una [Calculadora de unidades de solicitud](https://www.documentdb.com/capacityplanner) de basada en web para ayudar a calcular los requisitos de la unidad de solicitud para operaciones habituales, incluidos:

- Documento crea (escribe)
- Lecturas de documento
- Elimina el documento
- Actualizaciones de documentos

La herramienta también incluye compatibilidad para estimar las necesidades de almacenamiento de datos basándose en los documentos de ejemplo que se proporciona.

Con la herramienta es sencillo:

1. Cargar uno o varios documentos JSON representantes.

    ![Cargar documentos en la calculadora de unidades de la solicitud][2]

2. Para calcular los requisitos de almacenamiento de datos, escriba el número total de documentos que se espera almacenar.

3. Escriba el número de documento crear, leer, actualizar y eliminar operaciones requieren (en una base por segundo). Para calcular los gastos de la unidad de solicitud de las operaciones de actualización de documentos, cargar una copia del documento muestra desde el paso 1 anterior que incluye las actualizaciones de campo típico.  Por ejemplo, si las actualizaciones de documentos normalmente modificar dos propiedades denominadas lastLogin y userVisits, a continuación, simplemente copiar el documento de muestra, actualizar los valores de esas dos propiedades y cargar el documento copiado.

    ![Especifique los requisitos de rendimiento en la calculadora de unidad de solicitud][3]

4. Haga clic en calcular y examinar los resultados.

    ![Solicitar resultados de calculadora de unidad][4]

>[AZURE.NOTE]Si dispone de los tipos de documentos que variarán considerablemente en términos de tamaño y el número de propiedades indizadas, a continuación, cargar una muestra de cada *tipo* de documento normal en la herramienta y, a continuación, calcular los resultados.

###<a name="use-the-documentdb-request-charge-response-header"></a>Usar el encabezado de respuesta de cargo de solicitud de DocumentDB
Cada respuesta del servicio de DocumentDB incluye un encabezado personalizado (x ms-solicitud cargo) que contiene las unidades de solicitud consumidas para la solicitud. Este encabezado también está accesible a través de los SDK DocumentDB. En el SDK de .NET RequestCharge es una propiedad del objeto ResourceResponse.  Para las consultas, el Explorador de consulta DocumentDB en el portal de Azure proporciona información de cargo de solicitud para consultas ejecutadas.

![Examinar los cargos RU en el Explorador de consulta][1]

Con esto en mente, un método para calcular la cantidad de rendimiento reservado requerido por la aplicación es grabar el gasto de unidad de solicitud asociado con la ejecución de las operaciones típicas con un representante de documento usado por la aplicación y, a continuación, calcular el número de operaciones previsto realizar cada segundo.  Asegúrese de medir e incluir las consultas típicas y el uso de secuencias de comandos de DocumentDB también.

>[AZURE.NOTE]Si dispone de los tipos de documentos que variarán considerablemente en términos de tamaño y el número de propiedades indizadas, anote el gasto de unidad de solicitud de operación aplicable asociado con cada *tipo* de documento normal.

Por ejemplo:

1. Registrar el cargo de unidad de solicitud de creación (inserción) un documento normal. 
2. Registrar el gasto de unidad de solicitud de lectura de un documento normal.
3. Registrar el gasto de unidad de solicitud de actualización de un documento normal.
3. Registrar el gasto de unidad de solicitud de consultas de documento típicas, comunes.
4. Registrar el cargo de unidad solicitud de las secuencias de comandos (procedimientos almacenados, desencadenadores, funciones definidas por el usuario) de la aplicación
5. Calcular las unidades de la solicitud requiere dada el número estimado de operaciones que anticipar para ejecutar cada segundo.

##<a name="a-request-unit-estimation-example"></a>Un ejemplo de estimación de unidad de solicitud
Tenga en cuenta el siguiente documento ~ 1KB:

    {
     "id": "08259",
    "description": "Cereals ready-to-eat, KELLOGG, KELLOGG'S CRISPIX",
    "tags": [
        {
        "name": "cereals ready-to-eat"
        },
        {
        "name": "kellogg"
        },
        {
        "name": "kellogg's crispix"
        }
    ],
    "version": 1,
    "commonName": "Includes USDA Commodity B855",
    "manufacturerName": "Kellogg, Co.",
    "isFromSurvey": false,
    "foodGroup": "Breakfast Cereals",
    "nutrients": [
        {
        "id": "262",
        "description": "Caffeine",
        "nutritionValue": 0,
        "units": "mg"
        },
        {
        "id": "307",
        "description": "Sodium, Na",
        "nutritionValue": 611,
        "units": "mg"
        },
        {
        "id": "309",
        "description": "Zinc, Zn",
        "nutritionValue": 5.2,
        "units": "mg"
        }
    ],
    "servings": [
        {
        "amount": 1,
        "description": "cup (1 NLEA serving)",
        "weightInGrams": 29
        }
    ]
    }

>[AZURE.NOTE]Documentos están reducir en DocumentDB, de modo que el sistema calcule el tamaño del documento anterior es ligeramente menor que 1KB.


La siguiente tabla muestra solicitud aproximada cargos de unidad para operaciones habituales en este documento (el gasto de unidad aproximada solicitud se supone que el nivel de coherencia de cuenta se establece en "Sesión" y que todos los documentos estén indizados automáticamente):

Operación|Solicitud de cargo por unidad 
---|---
Crear documento|~ 15 RU 
Leer el documento|~ 1 RU
Documento de consulta por Id.|~2.5 RU

Además, esta tabla muestra solicitud aproximada cargos de unidades para las consultas típicas usadas en la aplicación:

Consulta|Solicitud de cargo por unidad|# de documentos devueltos
---|---|--- 
Seleccione comida por Id.|~2.5 RU|1 
Seleccione comidas por fabricante|~ 7 RU|7
Seleccionar grupo de comida y orden en peso|~ 70 RU|100
Seleccione superiores 10 alimentos en un grupo de comida|~ 10 RU|10

>[AZURE.NOTE]Cargos RU varían en función del número de documentos devueltos.

Con esta información, podemos hacer una estimación los requisitos de RU para esta aplicación dado el número de operaciones y consultas que esperamos por segundo:

Operación o consulta|Número estimado por segundo|RUs necesarios 
---|---|--- 
Crear documento|10|150 
Leer el documento|100|100 
Seleccione comidas por fabricante|25|175 
Seleccione por grupo de alimentos|10|700 
Seleccione 10 principales|15|150 total|155|1275

En este caso, esperamos que un requisito de rendimiento medio de RU/s 1,275.  Redondeo hasta el 100 más cercano, se podría aprovisionar 1.300 RU/s para esta colección de la aplicación.

##<a id="RequestRateTooLarge"></a>Superior a los límites de rendimiento reservado
Recuerde que el consumo de unidades de la solicitud se evalúa como una tasa por segundo. Para las aplicaciones que superan la tasa de unidad de solicitud de preparación para una colección, convocatorias a esa colección reducirá el hasta que la tasa cae por debajo del nivel reservado. Cuando se produce un acelerador, el servidor IntruShield finalizar la solicitud con RequestRateTooLargeException (código de estado HTTP 429) y devolver el encabezado x intentos ms después de ms que indica la cantidad de tiempo en milisegundos, que el usuario debe esperar antes de volviendo a intentar la solicitud.

    HTTP Status 429
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100

Si está utilizando las consultas .NET SDK del cliente y LINQ, a continuación, en la mayoría de las veces que nunca tendrá que tratar esta excepción, como la versión actual del SDK de cliente .NET detecta implícitamente esta respuesta, respeta el encabezado especificado por el servidor después de reintento y vuelve a intentar la solicitud. A menos que su cuenta está accediendo simultáneamente varios clientes, el siguiente reintento se realizará correctamente.

Si tiene más de un cliente de forma acumulativa funcionando por encima de la velocidad de la solicitud, el comportamiento predeterminado de reintento no basta y el cliente, producirá una DocumentClientException con código de estado 429 a la aplicación. En casos como esta, puede controlar el comportamiento de intentos y lógica de error en la aplicación rutinas de tratamiento o aumentar el rendimiento reservado para la colección.

##<a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre el rendimiento reservado con bases de datos de Azure DocumentDB, explore estos recursos:
 
- [Precios DocumentDB](https://azure.microsoft.com/pricing/details/documentdb/)
- [Administrar la capacidad de DocumentDB](documentdb-manage.md) 
- [Modelado de datos en DocumentDB](documentdb-modeling-data.md)
- [Niveles de rendimiento DocumentDB](documentdb-partition-data.md)

Para obtener más información sobre DocumentDB, consulte la [documentación](https://azure.microsoft.com/documentation/services/documentdb/)de Azure DocumentDB. 

Para empezar con escala y pruebas con DocumentDB de rendimiento, consulte [rendimiento y escala pruebas con DocumentDB de Azure](documentdb-performance-testing.md).


[1]: ./media/documentdb-request-units/queryexplorer.png 
[2]: ./media/documentdb-request-units/RUEstimatorUpload.png
[3]: ./media/documentdb-request-units/RUEstimatorDocuments.png
[4]: ./media/documentdb-request-units/RUEstimatorResults.png
[5]: ./media/documentdb-request-units/RUCalculator2.png

<properties 
    pageTitle="Sintaxis SQL y SQL de la consulta para DocumentDB | Microsoft Azure" 
    description="Conozca sintaxis SQL, los conceptos de base de datos y consultas SQL para DocumentDB, una base de datos NoSQL. Puede utilizar SQL como un lenguaje de consulta JSON en DocumentDB." 
    keywords="sintaxis SQL, consulta sql, consultas sql, lenguaje de consulta de json, conceptos de base de datos y consultas de sql, funciones de agregado"
    services="documentdb" 
    documentationCenter="" 
    authors="arramac" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/27/2016" 
    ms.author="arramac"/>

# <a name="sql-query-and-sql-syntax-in-documentdb"></a>Consulta SQL y la sintaxis SQL DocumentDB
Microsoft Azure DocumentDB admite consultar documentos usando SQL (lenguaje de consulta estructurado) como un lenguaje de consulta JSON. DocumentDB es realmente libre de esquema. Gracias a su compromiso al modelo de datos JSON directamente en el motor de base de datos, proporciona la indización automática de documentos JSON sin necesidad de esquema explícito o la creación de índices secundarios. 

Al diseñar el lenguaje de consulta para DocumentDB tenemos dos objetivos:

-   En lugar de crear un nuevo lenguaje de consulta JSON, queremos admite SQL. SQL es uno de los idiomas de consulta más familiar y populares. DocumentDB SQL proporciona un modelo de programación formal para consultas avanzadas sobre documentos JSON.
-   Como JSON documento base de datos puede ejecutar JavaScript directamente en el motor de base de datos, queremos usar el modelo de programación de JavaScript como base para nuestro lenguaje de consulta. El código SQL DocumentDB proviene de sistema de tipos de JavaScript, evaluar una expresión y una llamada a función. Este activar proporciona un modelo de programación natural para proyecciones relacionales, exploración jerárquica en documentos JSON, combinaciones automáticas, consultas espaciales e invocación de funciones definidas por el usuario (UDF) escritos completamente en JavaScript, entre otras características. 

Creemos que estas capacidades son clave para reducir la fricción entre la aplicación y la base de datos y son fundamentales para la productividad del programador.

Se recomienda Introducción observando el siguiente vídeo, donde Aravind Ramachandran muestra funciones de consultas de DocumentDB, y visitando el [Animación de consulta](http://www.documentdb.com/sql/demo), donde puede probar DocumentDB y ejecutar consultas SQL contra nuestro conjunto de datos.

> [AZURE.VIDEO dataexposedqueryingdocumentdb]

A continuación, vuelva a este artículo, donde comenzaremos con un tutorial de consulta SQL que le guiará por algunos documentos JSON simples y comandos SQL.

## <a name="getting-started-with-sql-commands-in-documentdb"></a>Introducción a los comandos SQL en DocumentDB
Para ver DocumentDB SQL en el trabajo, vamos a empezar con unos pocos documentos JSON simples y recorra algunas consultas sencillas. Tenga en cuenta estos dos documentos JSON sobre dos familias. Tenga en cuenta que con DocumentDB, no es necesario crear esquemas o los índices secundarios explícitamente. Simplemente debemos insertar los documentos JSON para una colección de DocumentDB y posteriormente la consulta. Aquí tenemos un JSON simple del documento para la familia Andersen, los elementos primarios, los niños (y sus animales), información de dirección y el registro. El documento tiene cadenas, números, valores booleanos, matrices y propiedades anidadas. 

**Documento**  

    {
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }


Aquí es un segundo documento con una ligera diferencia: `givenName` y `familyName` se usan en lugar de `firstName` y `lastName`.

**Documento**  

    {
        "id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam", 
                "givenName": "Jesse", 
                "gender": "female", "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            { 
                "familyName": "Miller", 
                 "givenName": "Lisa", 
                 "gender": "female", 
                 "grade": 8 }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "creationDate": 1431620462,
        "isRegistered": false
    }



Ahora vamos a intentar algunas de las consultas en los datos a comprender algunos de los aspectos clave de SQL DocumentDB. Por ejemplo, la siguiente consulta devuelve los documentos que coincida con el campo identificador `AndersenFamily`. Dado que es un `SELECT *`, el resultado de la consulta es el documento completo de JSON:

**Consulta**

    SELECT * 
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultados**

    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]


Ahora, considere el caso donde se necesita volver a formatear la salida JSON en una forma diferente. Esta consulta proyectos un nuevo objeto JSON con dos campos seleccionados, nombre y ciudad, cuando la ciudad de la dirección tiene el mismo nombre que el estado. En este caso, coincide con "Nueva York, Nueva York".

**Consulta**   

    SELECT {"Name":f.id, "City":f.address.city} AS Family 
    FROM Families f 
    WHERE f.address.city = f.address.state

**Resultados**

    [{
        "Family": {
            "Name": "WakefieldFamily", 
            "City": "NY"
        }
    }]


La siguiente consulta devuelve todos los nombres especificados de los niños en la familia cuyo identificador coincide con `WakefieldFamily` ordenados por la ciudad de residencia.

**Consulta**

    SELECT c.givenName 
    FROM Families f 
    JOIN c IN f.children 
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.address.city ASC

**Resultados**

    [
      { "givenName": "Jesse" }, 
      { "givenName": "Lisa"}
    ]


Nos gustaría destacar algunos aspectos importantes del lenguaje de consulta DocumentDB los ejemplos que hemos visto hasta ahora:  
 
-   Puesto que DocumentDB SQL funciona en los valores JSON, ocupa de árbol en forma de entidades en lugar de filas y columnas. Por lo tanto, el idioma le permite hacer referencia a los nodos del árbol a cualquier profundidad arbitrario, como `Node1.Node2.Node3…..Nodem`, similar a relacional SQL que hacen referencia a la referencia de dos partes de `<table>.<column>`.   
-   Lenguaje de consulta estructurado funciona con menos de esquema de datos. Por lo tanto, el sistema de tipo debe estar enlazado dinámicamente. La misma expresión podría producir tipos diferentes en los distintos documentos. El resultado de una consulta es un valor válido de JSON, pero no se garantiza que es de un esquema fijo.  
-   DocumentDB sólo admite estrictos documentos JSON. Esto significa que el sistema de tipos y expresiones están limitadas a tratar sólo con tipos JSON. Consulte la [especificación de JSON](http://www.json.org/) para obtener más detalles.  
-   Una colección de DocumentDB es un contenedor libre de esquema de documentos JSON. Las relaciones de entidades de datos dentro y entre los documentos en una colección implícitamente se capturan contenido y no de clave principal y las relaciones de clave externas. Se trata de un aspecto importante destacar en las combinaciones de dentro de un documento que se describe más adelante en este artículo.

## <a name="documentdb-indexing"></a>Indización DocumentDB

Antes de entrar en la sintaxis SQL DocumentDB, conviene explorar el diseño de indización en DocumentDB. 

El propósito de índices de la base de datos es atender las consultas en sus varios formularios y formas con consumo de recursos mínimo (por ejemplo, CPU y entrada y salida) mientras proporciona buen rendimiento y latencia baja. A menudo, la opción del índice correcto para consultar una base de datos requiere mucho planeamiento y experimentación. Este enfoque presenta un reto para bases de datos sin esquemas donde los datos no se ajustan a un esquema estricto y evolucionar rápidamente. 

Por lo tanto, para diseñar la indización subsistema de DocumentDB, se establecen los objetivos siguientes:

-   Indizar documentos sin necesidad de esquema: el subsistema de indización no requiere ninguna información de esquema o hacer ninguna suposición sobre el esquema de los documentos. 

-   Soporte técnico para las consultas eficientes, enriquecidas jerárquicas y relacionales: el índice es compatible con el lenguaje de consulta DocumentDB eficazmente, incluido el soporte técnico para proyecciones jerárquicas y relacionales.

-   Compatibilidad con las consultas coherentes en caso de un volumen constante de escritura: para cargas de trabajo de rendimiento de escritura alta con consultas coherentes, el índice se actualiza incrementalmente y eficaz en línea ante un volumen constante de escritura. La actualización del índice coherente es fundamental para atender las consultas en el nivel de coherencia en el que el usuario configura el servicio de documentos.

-   Compatibilidad con varios usuarios: dado el modelo de reserva en función de gobernanza de recursos a través de inquilinos, se realizan las actualizaciones de índice dentro del presupuesto de recursos del sistema (CPU, memoria y operaciones de entrada y salida por segundo) asignadas por réplica. 

-   Eficiencia de almacenamiento: de la eficacia de costo, la sobrecarga de almacenamiento en disco del índice es limitada y predecible. Esto es crucial porque DocumentDB permite al desarrollador realizar compensaciones en función de costo entre índice sobrecarga en relación con el rendimiento de la consulta.  

Consulte los [ejemplos de DocumentDB](https://github.com/Azure/azure-documentdb-net) en MSDN para ejemplos que muestran cómo configurar la directiva para una colección de indización. Ahora comencemos con los detalles de la sintaxis SQL DocumentDB.


## <a name="basics-of-a-documentdb-sql-query"></a>Conceptos básicos de una consulta SQL DocumentDB
Cada consulta consta de una cláusula SELECT y FROM opcional y cláusulas WHERE por estándares ANSI SQL. Normalmente, cada consulta, se enumera el origen en la cláusula FROM. A continuación, se aplica el filtro en la cláusula WHERE en el origen de para recuperar un subconjunto de los documentos JSON. Por último, la cláusula SELECT se usa para los valores JSON solicitados en la lista de selección del proyecto.
    
    SELECT [TOP <top_expression>] <select_list> 
    [FROM <from_specification>] 
    [WHERE <filter_condition>]
    [ORDER BY <sort_specification]    


## <a name="from-clause"></a>Cláusula FROM
La `FROM <from_specification>` cláusula es opcional, a menos que el origen está filtrado o proyectado más adelante en la consulta. El propósito de esta cláusula es especificar el origen de datos en el que debe trabajar con la consulta. Normalmente toda la colección es el origen, pero uno en su lugar, puede especificar un subconjunto de la colección. 

Una consulta como `SELECT * FROM Families` indica que toda la colección de familias es el origen en los que enumerar. Un identificador especial raíz se puede utilizar para representar la colección en lugar de usar el nombre de la colección. La siguiente lista contiene las reglas que se aplican por consulta:

- La colección puede ser un alias, como por ejemplo `SELECT f.id FROM Families AS f` o simplemente `SELECT f.id FROM Families f`. Aquí `f` es equivalente a `Families`. `AS`es el identificador de una palabra clave opcional alias.

-   No se puede enlazar la nota que una vez que un alias, la fuente original. Por ejemplo, `SELECT Families.id FROM Families f` sintaxis no es válida desde el identificador "Familias" no se puede resolver ya.

-   Todas las propiedades que necesitan hacer referencia a deben ser completos. Falta de cumplimiento de esquema estricto, esto se aplica para evitar los enlaces ambiguos. Por lo tanto, `SELECT id FROM Families f` no es válida sintácticamente desde la propiedad `id` no está enlazado.
    
### <a name="sub-documents"></a>Subdirección de documentos
También se puede reducir el origen a un subconjunto más pequeño. Por ejemplo, para enumerar solo un subárbol en cada documento, la raíz de la subcarpeta, a continuación, podría ser el origen, tal como se muestra en el ejemplo siguiente.

**Consulta**

    SELECT * 
    FROM Families.children

**Resultados**  

    [
      [
        {
            "firstName": "Henriette Thaulow",
            "gender": "female",
            "grade": 5,
            "pets": [
              {
                  "givenName": "Fluffy"
              }
            ]
        }
      ],
      [
        {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        },
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]

Mientras que el ejemplo anterior utiliza una matriz como origen, un objeto puede utilizarse también como el origen, que es lo que se muestra en el ejemplo siguiente. Se considera cualquier JSON válido (no sin definir) que se encuentra en el origen de para su inclusión en el resultado de la consulta. Si no tienen algunas familias de un `address.state` valor, se van a excluir en el resultado de la consulta.

**Consulta**

    SELECT * 
    FROM Families.address.state

**Resultados**

    [
      "WA", 
      "NY"
    ]


## <a name="where-clause"></a>Cláusula WHERE
La cláusula WHERE (**`WHERE <filter_condition>`**) es opcional. Especifica las condiciones que los documentos JSON proporcionados por el origen deben cumplir para que se incluye como parte del resultado. Cualquier documento JSON debe evaluar las condiciones especificadas en "true" para tener en cuenta para el resultado. La cláusula WHERE se utiliza el nivel de índice para determinar el subconjunto más pequeño absoluto de documentos de origen que pueden ser parte del resultado. 

La siguiente consulta solicita documentos que contienen una propiedad nombre cuyo valor se `AndersenFamily`. Cualquier otro documento que no tiene una propiedad name, o que no coinciden con el valor `AndersenFamily` se excluyen. 

**Consulta**

    SELECT f.address
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultados**

    [{
      "address": {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }
    }]


El ejemplo anterior mostraba una consulta simple de igualdad. DocumentDB SQL también es compatible con una amplia variedad de expresiones escalares. Las más utilizadas son binarios y unario expresiones. Referencias de propiedades del objeto de origen JSON también son expresiones válidas. 

Los siguientes operadores binarios actualmente son compatibles y pueden usarse en las consultas como se muestra en los ejemplos siguientes:  
<table>
<tr>
<td>Aritmética</td> 
<td>+,-,*,/,%</td>
</tr>
<tr>
<td>Bit a bit</td>    
<td>|, &, ^, <<, >>, >>> (desplazamiento derecha relleno de cero) </td>
</tr>
<tr>
<td>Lógica</td>
<td>Y, O NO</td>
</tr>
<tr>
<td>Comparación</td> 
<td>=, !=, &lt;, &gt;, &lt;=, &gt;=, <></td>
</tr>
<tr>
<td>Cadena</td> 
<td>|| (concatenar)</td>
</tr>
</table>  

Echemos un vistazo a algunas consultas con operadores binarios.

    SELECT * 
    FROM Families.children[0] c
    WHERE c.grade % 2 = 1     -- matching grades == 5, 1
    
    SELECT * 
    FROM Families.children[0] c
    WHERE c.grade ^ 4 = 1    -- matching grades == 5
    
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade >= 5     -- matching grades == 5


Los operadores unario +,-, ~ también son compatibles y no se puede utilizar dentro de las consultas como se muestra en el ejemplo siguiente:

    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1
    
    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5



Además de los operadores binarios y unario, también se permiten referencias de propiedad. Por ejemplo, `SELECT * FROM Families f WHERE f.isRegistered` devuelve el documento JSON que contiene la propiedad `isRegistered` donde el valor de propiedad es igual a la JSON `true` valor. Otros valores (false, null, sin definir, `<number>`, `<string>`, `<object>`, `<array>`, etc.) para el documento de origen que se excluyen del resultado de clientes potenciales. 

### <a name="equality-and-comparison-operators"></a>Operadores de comparación e igualdad
La siguiente tabla muestra el resultado de las comparaciones de igualdad en SQL DocumentDB entre los dos tipos JSON.
<table style = "width:300px">
   <tbody>
      <tr>
         <td valign="top">
            <strong>OP</strong>
         </td>
         <td valign="top">
            <strong>Sin definir</strong>
         </td>
         <td valign="top">
            <strong>Null</strong>
         </td>
         <td valign="top">
            <strong>Valor booleano</strong>
         </td>
         <td valign="top">
            <strong>Número</strong>
         </td>
         <td valign="top">
            <strong>Cadena</strong>
         </td>
         <td valign="top">
            <strong>Objeto</strong>
         </td>
         <td valign="top">
            <strong>Matriz</strong>
         </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Sin definir<strong>
         </td>
         <td valign="top">
Sin definir </td>
         <td valign="top">
Sin definir </td>
         <td valign="top">
Sin definir </td>
         <td valign="top">
Sin definir </td>
         <td valign="top">
Sin definir </td>
         <td valign="top">
Sin definir </td>
         <td valign="top">
Sin definir </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Null<strong>
         </td>
         <td valign="top">
Sin definir </td>
         <td valign="top">
            <strong>Vale</strong>
         </td>
         <td valign="top">
Sin definir </td>
         <td valign="top">
Sin definir </td>
         <td valign="top">
Sin definir </td>
         <td valign="top">
Sin definir </td>
         <td valign="top">
Sin definir </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Valor booleano<strong>
         </td>
         <td valign="top">
Sin definir </td>
         <td valign="top">
Sin definir </td>
         <td valign="top">
            <strong>Vale</strong>
         </td>
         <td valign="top">
Sin definir </td>
         <td valign="top">
Sin definir </td>
         <td valign="top">
Sin definir </td>
         <td valign="top">
Sin definir </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Número<strong>
         </td>
         <td valign="top">
Sin definir </td>
         <td valign="top">
Sin definir </td>
         <td valign="top">
Sin definir </td>
         <td valign="top">
            <strong>Vale</strong>
         </td>
         <td valign="top">
Sin definir </td>
         <td valign="top">
Sin definir </td>
         <td valign="top">
Sin definir </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Cadena<strong>
         </td>
         <td valign="top">
Sin definir </td>
         <td valign="top">
Sin definir </td>
         <td valign="top">
Sin definir </td>
         <td valign="top">
Sin definir </td>
         <td valign="top">
            <strong>Vale</strong>
         </td>
         <td valign="top">
Sin definir </td>
         <td valign="top">
Sin definir </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Objeto<strong>
         </td>
         <td valign="top">
Sin definir </td>
         <td valign="top">
Sin definir </td>
         <td valign="top">
Sin definir </td>
         <td valign="top">
Sin definir </td>
         <td valign="top">
Sin definir </td>
         <td valign="top">
            <strong>Vale</strong>
         </td>
         <td valign="top">
Sin definir </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Matriz<strong>
         </td>
         <td valign="top">
Sin definir </td>
         <td valign="top">
Sin definir </td>
         <td valign="top">
Sin definir </td>
         <td valign="top">
Sin definir </td>
         <td valign="top">
Sin definir </td>
         <td valign="top">
Sin definir </td>
         <td valign="top">
            <strong>Vale</strong>
         </td>
      </tr>
   </tbody>
</table>

Para otros operadores de comparación como >, > =,! =, < y < =, las reglas siguientes se aplican:   

-   Comparación entre los tipos de resultado no definido.
-   Comparación entre dos objetos o dos matrices resultados en no definido.   

Si el resultado de la expresión escalar en el filtro es sin definir, el documento correspondiente no se incluiría en el resultado, ya que no definido lógicamente no equivale a "true".

### <a name="between-keyword"></a>ENTRE palabras clave
También puede usar la palabra clave BETWEEN para expresar consultas en rangos de valores como en ANSI SQL. ENTRE puede utilizarse con cadenas o números.

Por ejemplo, esta consulta devuelve todos los documentos de familia en los que calificaciones del primer secundario está entre 1-5 (ambos inclusive). 

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5

A diferencia de ANSI-SQL, también puede usar la cláusula entre en la cláusula FROM, como en el ejemplo siguiente.

    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c

Para los tiempos de ejecución de consulta, no olvide crear una directiva de indexación que usa un índice rango contra cualquier numéricos propiedades o rutas de acceso que se filtran en la cláusula entre. 

La diferencia principal entre utilizar entre en DocumentDB y ANSI SQL es que puede expresar consultas de rango en Propiedades de tipos mixtos, por ejemplo, es posible que tenga que "categoría" ser un número (5) en algunos documentos y cadenas de otras personas ("grade4"). En estos casos, como en JavaScript, una comparación entre dos resultados diferentes tipos de "no definido" y el documento se omitirá.

### <a name="logical-and-or-and-not-operators"></a>Lógico (AND, OR y no) operadores
Trabajar con operadores lógicos en valores booleanos. Las tablas de verdad lógicas para estos operadores se muestran en las tablas siguientes.

OR|True|Falso|Sin definir
---|---|---|---
True|True|True|True
Falso|True|Falso|Sin definir
Sin definir|True|Sin definir|Sin definir

Y|True|Falso|Sin definir
---|---|---|---
True|True|Falso|Sin definir
Falso|Falso|Falso|Falso
Sin definir|Sin definir|Falso|Sin definir

NO|  |
---|---
True|Falso
Falso|True
Sin definir|Sin definir

### <a name="in-keyword"></a>Palabra clave
La palabra clave IN puede usarse para comprobar si un valor especificado coincide con algún valor en una lista. Por ejemplo, esta consulta devuelve todos los documentos de familia donde el identificador es una de las "WakefieldFamily" o "AndersenFamily". 
 
    SELECT *
    FROM Families 
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')

Este ejemplo devuelve todos los documentos donde el estado es cualquiera de los valores especificados.

    SELECT *
    FROM Families 
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")

### <a name="ternary--and-coalesce--operators"></a>Ternario (?) y los operadores de combinación (?)
Los operadores ternario y combinación pueden utilizarse para crear expresiones condicionales, similares al lenguaje de programación C# y JavaScript. 

El operador ternario (?) puede ser muy útil al crear nuevas propiedades JSON sobre la marcha. Por ejemplo, ahora puede escribir consultas para clasificar los niveles de clase en un formato legible humano como principiante o intermedio/avanzado tal como se muestra a continuación.
 
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel 
     FROM Families.children[0] c

También puede anidar las llamadas al operador como en la siguiente consulta.
 
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high")  AS gradeLevel 
    FROM Families.children[0] c

Como con otros operadores de consulta, si faltan las propiedades que se hace referencia en la expresión condicional en cualquier documento, o si los tipos comparados son diferentes, a continuación, dichos documentos se excluye en los resultados de la consulta.

El operador de unión (?) puede usarse para comprobar eficazmente la presencia de una propiedad (también conocido como está definida) en un documento. Esto es útil cuando la consulta a semiestructurados o datos de tipos mixtos. Por ejemplo, esta consulta devuelve el "apellido" Si está presente, o "apellido" Si no lo está.

    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f

### <a name="quoted-property-accessor"></a>Descriptor de acceso entre comillas (propiedad)
También puede acceder a propiedades mediante el operador entre comillas propiedad `[]`. Por ejemplo, `SELECT c.grade` y `SELECT c["grade"]` son equivalentes. Esta sintaxis es útil cuando se necesita una propiedad que contiene espacios, caracteres especiales, o pasa a compartir el mismo nombre que una palabra clave SQL o palabra reservada de escape.

    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"


## <a name="select-clause"></a>Cláusula SELECT
La cláusula SELECT (**`SELECT <select_list>`**) es obligatorio y especifica cuáles serán los valores recuperados de la consulta, igual que en ANSI SQL. El subconjunto que se han filtrado encima de los documentos de origen se pasan a la fase de proyección, donde se recuperan los valores JSON especificados y se crea un nuevo objeto JSON, para cada entrada que pasa en él. 

En el ejemplo siguiente se muestra una consulta de selección típica. 

**Consulta**

    SELECT f.address
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultados**

    [{
      "address": {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }
    }]


### <a name="nested-properties"></a>Propiedades anidadas
En el ejemplo siguiente, nos estamos proyectar dos propiedades anidadas `f.address.state` y `f.address.city`.

**Consulta**

    SELECT f.address.state, f.address.city
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultados**

    [{
      "state": "WA", 
      "city": "seattle"
    }]


Proyección también admite expresiones de JSON, tal como se muestra en el ejemplo siguiente.

**Consulta**

    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultados**

    [{
      "$1": {
        "state": "WA", 
        "city": "seattle", 
        "name": "AndersenFamily"
      }
    }]


Echemos un vistazo a la función de `$1` aquí. La `SELECT` cláusula debe crear un objeto JSON y como no se proporciona ninguna clave, usamos nombres de variable de argumento implícito comenzando con `$1`. Por ejemplo, esta consulta devuelve dos variables de argumento implícito, denominadas `$1` y `$2`.

**Consulta**

    SELECT { "state": f.address.state, "city": f.address.city }, 
           { "name": f.id }
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultados**

    [{
      "$1": {
        "state": "WA", 
        "city": "seattle"
      }, 
      "$2": {
        "name": "AndersenFamily"
      }
    }]


### <a name="aliasing"></a>Asignación de alias
Ahora vamos a ampliar el ejemplo anterior con alias explícitas de valores. COMO es la palabra clave utilizada para el alias. Tenga en cuenta que es opcional, tal como se muestra al proyectar el segundo valor como `NameInfo`. 

En caso de una consulta tiene dos propiedades con el mismo nombre, se deben utiliza un alias para cambiar el nombre de uno o ambos de las propiedades para que se elimina la ambigüedad en el resultado previsto.

**Consulta**

    SELECT 
           { "state": f.address.state, "city": f.address.city } AS AddressInfo, 
           { "name": f.id } NameInfo
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultados**

    [{
      "AddressInfo": {
        "state": "WA", 
        "city": "seattle"
      }, 
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]


### <a name="scalar-expressions"></a>Expresiones escalares
Además de las referencias de propiedad, la cláusula SELECT también es compatible con expresiones escalares como constantes, expresiones aritméticas, expresiones lógicas, etcetera. Por ejemplo, aquí es una consulta de "Hola a todos" sencilla.

**Consulta**

    SELECT "Hello World"

**Resultados**

    [{
      "$1": "Hello World"
    }]


Este es un ejemplo más complejo que utiliza una expresión escalar.

**Consulta**

    SELECT ((2 + 11 % 7)-2)/3   

**Resultados**

    [{
      "$1": 1.33333
    }]


En el ejemplo siguiente, el resultado de la expresión escalar es un valor Boolean.

**Consulta**

    SELECT f.address.city = f.address.state AS AreFromSameCityState
    FROM Families f 

**Resultados**

    [
      {
        "AreFromSameCityState": false
      }, 
      {
        "AreFromSameCityState": true
      }
    ]


### <a name="object-and-array-creation"></a>Creación de objeto y matriz
Otra característica clave de SQL DocumentDB es la creación de objetos o matriz. En el ejemplo anterior, tenga en cuenta que hemos creado un nuevo objeto JSON. Del mismo modo, se pueden crear matrices de tal como se muestra en los ejemplos siguientes.

**Consulta**

    SELECT [f.address.city, f.address.state] AS CityState 
    FROM Families f 

**Resultados**  

    [
      {
        "CityState": [
          "seattle", 
          "WA"
        ]
      }, 
      {
        "CityState": [
          "NY", 
          "NY"
        ]
      }
    ]

### <a name="value-keyword"></a>Palabra clave de valor
La palabra clave **VALUE** proporciona una manera de JSON valor devuelto. Por ejemplo, la consulta se muestra a continuación devuelve el valor escalar `"Hello World"` en lugar de `{$1: "Hello World"}`.

**Consulta**

    SELECT VALUE "Hello World"

**Resultados**

    [
      "Hello World"
    ]


La siguiente consulta devuelve el valor JSON sin la `"address"` etiqueta en los resultados.

**Consulta**

    SELECT VALUE f.address
    FROM Families f 

**Resultados**  

    [
      {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }, 
      {
        "state": "NY", 
        "county": "Manhattan", 
        "city": "NY"
      }
    ]

En el ejemplo siguiente se extiende esta opción para mostrar cómo devolver valores simples de JSON (el nivel de hoja del árbol JSON). 

**Consulta**

    SELECT VALUE f.address.state
    FROM Families f 

**Resultados**

    [
      "WA",
      "NY"
    ]


###<a name="-operator"></a>* Operador
Se admite el operador especial (*) para el documento como de project-es. Cuando se utiliza, debe ser el único campo proyectado. Aunque una consulta como `SELECT * FROM Families f` es válida, `SELECT VALUE * FROM Families f ` y `SELECT *, f.id FROM Families f ` no son válidos.

**Consulta**

    SELECT * 
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultados**

    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]

###<a name="top-operator"></a>Operador superior
La palabra clave TOP puede usarse para limitar el número de valores de una consulta. Al principio se usa junto con la cláusula ORDER BY, el conjunto de resultados se limita al primer número N de valores ordenados; en caso contrario, devuelve el primer número N de resultados en un orden definido. Como práctica recomendada, en una instrucción SELECT, utilice siempre una cláusula ORDER BY con la cláusula TOP. Esta es la única manera predecible indicar las filas que se ven afectadas por parte superior. 


**Consulta**

    SELECT TOP 1 * 
    FROM Families f 

**Resultados**

    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]

Parte superior puede usarse con un valor constante (como se indicó anteriormente) o con el valor de una variable con consultas parametrizadas. Para obtener más detalles, consulte consultas parametrizadas a continuación.

## <a name="order-by-clause"></a>Cláusula ORDER BY
Al igual que en ANSI-SQL, puede incluir una cláusula Order By opcional al consultar. La cláusula puede incluir un argumento ASC o DESC opcional para especificar el orden en que se deben recuperar resultados. Para ver más en Ordenar por, consulte [DocumentDB orden por tutorial](documentdb-orderby.md).

Por ejemplo, aquí es una consulta que recupera familias en orden de nombre de la ciudad residentes.

**Consulta**

    SELECT f.id, f.address.city
    FROM Families f 
    ORDER BY f.address.city
    
**Resultados**
    
    [
      {
        "id": "WakefieldFamily",
        "city": "NY"
      },
      {
        "id": "AndersenFamily",
        "city": "Seattle"   
      }
    ]

Y aquí una consulta que recupera familias en orden de la fecha de creación, que se almacena como un número que representa el tiempo de tiempo, es decir, el tiempo transcurrido desde el 1 de enero de 1970 en segundos.

**Consulta**

    SELECT f.id, f.creationDate
    FROM Families f 
    ORDER BY f.creationDate DESC
    
**Resultados**
    
    [
      {
        "id": "WakefieldFamily",
        "creationDate": 1431620462
      },
      {
        "id": "AndersenFamily",
        "creationDate": 1431620472  
      }
    ]
    
## <a name="advanced-database-concepts-and-sql-queries"></a>Conceptos avanzados de la base de datos y consultas SQL
### <a name="iteration"></a>Iteración
Se ha agregado una nueva construcción a través de la palabra clave **IN** en SQL DocumentDB para proporcionar soporte técnico iteración matrices JSON. El origen de proporciona soporte técnico para iteración. Comencemos con el siguiente ejemplo:

**Consulta**

    SELECT * 
    FROM Families.children

**Resultados**  

    [
      [
        {
          "firstName": "Henriette Thaulow", 
          "gender": "female", 
          "grade": 5, 
          "pets": [{ "givenName": "Fluffy"}]
        }
      ], 
      [
        {
            "familyName": "Merriam", 
            "givenName": "Jesse", 
            "gender": "female", 
            "grade": 1
        }, 
        {
            "familyName": "Miller", 
            "givenName": "Lisa", 
            "gender": "female", 
            "grade": 8
        }
      ]
    ]

Ahora vamos a ver otra consulta que realiza iteración sobre elementos secundarios de la colección. Tenga en cuenta la diferencia en la matriz de salida. Este ejemplo se divide `children` y acopla los resultados en una sola matriz.  

**Consulta**

    SELECT * 
    FROM c IN Families.children

**Resultados**  

    [
      {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy" }]
      },
      {
          "familyName": "Merriam",
          "givenName": "Jesse",
          "gender": "female",
          "grade": 1
      },
      {
          "familyName": "Miller",
          "givenName": "Lisa",
          "gender": "female",
          "grade": 8
      }
    ]

Esto puede usarse más para filtrar los datos de cada entrada individual de la matriz tal como se muestra en el ejemplo siguiente.

**Consulta**

    SELECT c.givenName
    FROM c IN Families.children
    WHERE c.grade = 8

**Resultados**  

    [{
      "givenName": "Lisa"
    }]

### <a name="joins"></a>Combinaciones
En una base de datos relacional, es muy importante la necesidad de unirse a través de tablas. Es el resultado lógico de diseño de esquemas normalizados. Por el contrario, DocumentDB trata el modelo de datos no normalizados de documentos de libre de esquema. Esto es el equivalente lógico de un "autocombinación".

La sintaxis compatible con el idioma es la combinación de combinación < from_source2 > < from_source1 >... Unirse a < from_sourceN >. En general, se devuelve un conjunto de tuplas **N**(tupla con valores de **N** ). Cada tupla tiene valores generados por todos los alias de la colección en iteración sus respectivos conjuntos. En otras palabras, se trata de un producto cruzado completo de los conjuntos de participar en la combinación.

Los siguientes ejemplos muestran cómo funciona la cláusula de combinación. En el ejemplo siguiente, el resultado es vacío desde el producto cruzado de todos los documentos de origen y un conjunto vacío está vacío.

**Consulta**

    SELECT f.id
    FROM Families f
    JOIN f.NonExistent

**Resultados**  

    [{
    }]


En el ejemplo siguiente, la combinación es entre la raíz del documento y la `children` raíz subcarpetas. Es un producto cruzado entre dos objetos JSON. El hecho de que los niños es una matriz no es eficaz en la combinación ya estamos trabajando con una sola raíz que es la matriz de elementos secundarios. Por lo tanto, el resultado contiene dos resultados, ya que el producto cruzado de todos los documentos con la matriz da como resultado exactamente un único documento.

**Consulta**

    SELECT f.id
    FROM Families f
    JOIN f.children
 
**Resultados**

    [
      {
        "id": "AndersenFamily"
      }, 
      {
        "id": "WakefieldFamily"
      }
    ]


En el ejemplo siguiente se muestra una combinación más convencional:

**Consulta**

    SELECT f.id
    FROM Families f
    JOIN c IN f.children 

**Resultados**

    [
      {
        "id": "AndersenFamily"
      }, 
      {
        "id": "WakefieldFamily"
      }, 
      {
        "id": "WakefieldFamily"
      }
    ]



Lo primero que se tenga en cuenta que es el `from_source` de la **combinación** de la cláusula es un iterador. Por lo tanto, el flujo en este caso es como sigue:  

-   Expanda cada elemento secundario **c** en la matriz.
-   Aplicar un producto cruzado con la raíz del documento **f** con cada elemento secundario **c** que se acopla en el primer paso.
-   Por último, la propiedad del nombre del objeto **f** raíz independiente del proyecto. 

El primer documento (`AndersenFamily`) contiene un solo elemento secundario, por lo que el conjunto de resultados contiene un único objeto correspondiente a este documento. El segundo documento (`WakefieldFamily`) contiene dos elementos secundarios. Por lo tanto, el producto cruzado genera un objeto independiente para cada elemento secundario, lo que resulta en dos objetos, uno para cada elemento secundario correspondiente a este documento. Observe que los campos de raíz de ambos estos documentos será mismos, como cabría esperar en un producto cruzado.

La utilidad real de la combinación es tuplas del formulario del producto cruzado en una forma que en caso contrario, es difícil de proyecto. Además, como veremos en el ejemplo siguiente, puede filtrar en la combinación de una tupla que permite al usuario eligió una condición satisfacer por las tuplas en general.

**Consulta**

    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName 
    FROM Families f 
    JOIN c IN f.children 
    JOIN p IN c.pets
 
**Resultados**

    [
      {
        "familyName": "AndersenFamily", 
        "childFirstName": "Henriette Thaulow", 
        "petName": "Fluffy"
      }, 
      {
        "familyName": "WakefieldFamily", 
        "childGivenName": "Jesse", 
        "petName": "Goofy"
      }, 
      {
       "familyName": "WakefieldFamily", 
       "childGivenName": "Jesse", 
       "petName": "Shadow"
      }
    ]



Este ejemplo es una extensión natural del ejemplo anterior y realiza una combinación de dos. Por lo tanto, se pueden visualizar el producto cruzado como el siguiente código.

    for-each(Family f in Families)
    {   
        for-each(Child c in f.children)
        {
            for-each(Pet p in c.pets)
            {
                return (Tuple(f.id AS familyName, 
                  c.givenName AS childGivenName, 
                  c.firstName AS childFirstName,
                  p.givenName AS petName));
            }
        }
    }

`AndersenFamily`tiene un elemento secundario que tenga una mascota. Por lo tanto, el producto cruzado da como resultado una fila (1*1*1) de esta serie. Sin embargo, WakefieldFamily tiene dos elementos secundarios, pero sólo un elemento secundario "Jesse" tiene animales. Jesse tiene aunque 2 animales. Por lo tanto, el producto cruzado da como resultado 1 filas*1*2 = 2 de esta serie.

En el ejemplo siguiente, hay un filtro adicional en `pet`. Se excluyen todas las tuplas donde el nombre de mascota no es "Sombra". Observe que se pueden crear tuplas de matrices, filtro en cualquiera de los elementos de la tupla y cualquier combinación de los elementos del proyecto. 

**Consulta**

    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName 
    FROM Families f 
    JOIN c IN f.children 
    JOIN p IN c.pets
    WHERE p.givenName = "Shadow"

**Resultados**

    [
      {
       "familyName": "WakefieldFamily", 
       "childGivenName": "Jesse", 
       "petName": "Shadow"
      }
    ]


## <a name="javascript-integration"></a>Integración de JavaScript
DocumentDB proporciona un modelo de programación para ejecutar lógica JavaScript en función de la aplicación directamente en las colecciones en términos de procedimientos almacenados y desencadenadores. Esto permite ambas:

-   Capacidad de realizar operaciones de CRUD transacciones de alto rendimiento y consultas en documentos en una colección de gracias a la integración de ejecución de JavaScript directamente en el motor de base de datos. 
-   Un modelo natural de flujo de control, variable de ámbito y asignación e integración de excepciones fundamentos con las transacciones de la base de datos. Para obtener más detalles sobre la compatibilidad con DocumentDB integración de JavaScript, consulte la documentación de programación del lado de servidor de JavaScript.

###<a name="user-defined-functions-udfs"></a>Definidas por el usuario (UDF) de funciones
Junto con los tipos ya definidos en este artículo, DocumentDB SQL proporciona compatibilidad para las funciones definidas por el usuario (UDF). En particular, se admiten UDF escalares donde los desarrolladores pueden pasar en cero o varios argumentos y devolver un resultado único argumento. Cada uno de estos argumentos se comprueban los valores de JSON legal.  

La sintaxis SQL DocumentDB se ha ampliado para admitir la lógica de aplicación personalizada con estas funciones de definidas por el usuario. UDF se pueden registrar con DocumentDB y, a continuación, se hace referencia como parte de una consulta SQL. De hecho, la UDF exquisitely está diseñado para invocar las consultas. Resultado de esta opción, UDF no tiene acceso al objeto de contexto que tienen otros tipos de JavaScript (procedimientos almacenados y desencadenadores). Dado que las consultas ejecutan como de solo lectura, pueden ejecutar en principal o en réplicas secundarias. Por lo tanto, UDF están diseñadas para ejecutarse en réplicas secundarias a diferencia de otros tipos de JavaScript.

A continuación se muestra un ejemplo de cómo se puede registrar una UDF a la base de datos de DocumentDB, específicamente en una colección de documentos.

   
       UserDefinedFunction regexMatchUdf = new UserDefinedFunction
       {
           Id = "REGEX_MATCH",
           Body = @"function (input, pattern) { 
                       return input.match(pattern) !== null;
                   };",
       };
       
       UserDefinedFunction createdUdf = client.CreateUserDefinedFunctionAsync(
           UriFactory.CreateDocumentCollectionUri("testdb", "families"), 
           regexMatchUdf).Result;  
                                                                             
En el ejemplo anterior, se crea una UDF cuyo nombre es `REGEX_MATCH`. Acepta dos valores de cadena JSON `input` y `pattern` y comprueba si el modelo de las primeras coincidencias especificado en el segundo usando la función de String.Match de JavaScript.


Ahora podemos usar este UDF en una consulta en una proyección. UDF deben estar cualificadas con el prefijo distingue mayúsculas de minúsculas "udf". Cuando se llama desde dentro de las consultas. 

>[AZURE.NOTE] Antes de 17/3/2015, DocumentDB compatibles UDF llamadas sin "udf". prefijo como seleccione REGEX_MATCH(). Este patrón de llamada está desusado.  

**Consulta**

    SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
    FROM Families

**Resultados**

    [
      {
        "$1": true
      }, 
      {
        "$1": false
      }
    ]

El archivo UDF también se puede utilizar dentro de un filtro como se muestra en el ejemplo siguiente, también calificado con "udf". prefijo:

**Consulta**

    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")

**Resultados**

    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]


Básicamente, UDF están escalar válido y pueden usarse en proyecciones y filtros. 

Para ampliar la potencia de UDF, echemos un vistazo a otro ejemplo con lógica condicional:

       UserDefinedFunction seaLevelUdf = new UserDefinedFunction()
       {
           Id = "SEALEVEL",
           Body = @"function(city) {
                switch (city) {
                    case 'seattle':
                        return 520;
                    case 'NY':
                        return 410;
                    case 'Chicago':
                        return 673;
                    default:
                        return -1;
                    }"
            };

            UserDefinedFunction createdUdf = await client.CreateUserDefinedFunctionAsync(
                UriFactory.CreateDocumentCollectionUri("testdb", "families"), 
                seaLevelUdf);
    
    
A continuación se muestra un ejemplo que utiliza el archivo UDF.

**Consulta**

    SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
    FROM Families f 

**Resultados**

     [
      {
        "city": "seattle", 
        "seaLevel": 520
      }, 
      {
        "city": "NY", 
        "seaLevel": 410
      }
    ]


Como los ejemplos anteriores showcase, UDF integran la potencia de lenguaje JavaScript con SQL DocumentDB para proporcionar una interfaz programable enriquecida para realizar lógica compleja de procedimientos, condicional con la Ayuda de capacidades de tiempo de ejecución de JavaScript integradas.

DocumentDB SQL proporciona los argumentos a la UDF para cada documento de origen en la fase actual de procesamiento UDF (cláusula WHERE o cláusula SELECT). El resultado se incorpore sin problemas en la canalización de ejecución general. Si las propiedades mencionados UDF parámetros no están disponibles en el valor JSON, el parámetro se considera como no definido y, por tanto, por completo se omite la invocación UDF. De forma similar si el resultado de la UDF está definido, no se incluye en el resultado. 

En resumen, UDF son excelentes herramientas para realizar lógica de negocios complejos como parte de la consulta.

### <a name="operator-evaluation"></a>Evaluación de operador
DocumentDB, en la virtud de ser una base de datos JSON dibuja paralelas con operadores de JavaScript y su semántica de evaluación. Mientras DocumentDB intenta conservar semántica de JavaScript en términos de soporte JSON, la evaluación de la operación se desvíe en algunos casos.

En SQL DocumentDB, a diferencia de en SQL tradicional, los tipos de valores a menudo no se conocen hasta que realmente se recuperan los valores de base de datos. Para ejecutar consultas de manera eficaz, la mayoría de los operadores tiene requisitos de tipo estricto. 

DocumentDB SQL no realizar conversiones implícitas, a diferencia de JavaScript. Por ejemplo, una consulta como `SELECT * FROM Person p WHERE p.Age = 21` coincide con documentos que contienen una propiedad de edad cuyo valor es 21. Al igual que cualquier otro documento cuya propiedad edad coincide con variantes de cadena "21" u otros posiblemente infinito "021", "21,0", "0021", "00021", etc. no se asociarán. Esto es a diferencia de JavaScript donde los valores de cadena son implícitamente convertir a los números (en función de operador, ex: ==). Esta opción es fundamental para índices eficaz coincidentes en SQL DocumentDB. 

## <a name="parameterized-sql-queries"></a>Consultas SQL parametrizadas
DocumentDB admite consultas con parámetros expresadas con el familiar @ notación. SQL parametrizada proporciona control sólida y escape del usuario de entrada, impidiendo la exposición accidental de datos a través de inserción de SQL. 

Por ejemplo, puede escribir una consulta que toma como parámetros apellido y el estado de la dirección y, a continuación, se ejecutan los distintos valores de apellidos y dirección estado basado en la entrada del usuario.

    SELECT * 
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState

Esta solicitud puede, a continuación, enviarse a DocumentDB como una consulta con parámetros de JSON como se muestra a continuación.

    {      
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",     
        "parameters": [          
            {"name": "@lastName", "value": "Wakefield"},         
            {"name": "@addressState", "value": "NY"},           
        ] 
    }

El argumento al principio puede establecerse mediante consultas parametrizadas como se muestra a continuación.

    {      
        "query": "SELECT TOP @n * FROM Families",     
        "parameters": [          
            {"name": "@n", "value": 10},         
        ] 
    }

Valores de parámetro pueden ser cualquier JSON válido (cadenas, números, valores booleanos, null, matrices de par o anidada JSON). También como DocumentDB es menos de esquema, parámetros no se validan frente a cualquier tipo.

##<a name="built-in-functions"></a>Funciones integradas
DocumentDB también es compatible con un número de funciones integradas para operaciones comunes, que se pueden usar dentro de las consultas como las funciones definidas por el usuario (UDF).

<table>
<tr>
<td>Funciones matemáticas</td> 
<td>ABS, CEILING, EXP, FLOOR, LOG, LOG10, POWER, redondear, inicio de sesión, SQRT, cuadrado, truncar, ACOS, ASIN, ATAN, ATN2, COS, COT, grados, PI, radianes, SIN y TAN</td>
</tr>
<tr>
<td>Tipo de comprobación de funciones</td>    
<td>IS_ARRAY, IS_BOOL, IS_NULL, IS_NUMBER, IS_OBJECT, IS_STRING, IS_DEFINED y IS_PRIMITIVE</td>
</tr>
<tr>
<td>Funciones de cadenas</td>   
<td>CONCATENACIÓN, CONTAINS, ENDSWITH, INDEX_OF, izquierda, longitud, inferior, LTRIM, reemplazar, REPLICAR, inversa, derecha, RTRIM, STARTSWITH, SUBCADENA y superior</td>
</tr>
<tr>
<td>Funciones de matriz</td>    
<td>ARRAY_CONCAT, ARRAY_CONTAINS, ARRAY_LENGTH y ARRAY_SLICE</td>
</tr>
<tr>
<td>Funciones espaciales</td>  
<td>ST_DISTANCE, ST_WITHIN, ST_ISVALID y ST_ISVALIDDETAILED</td>
</tr>
</table>  

Si actualmente usa una función definida por el usuario (UDF) para que una función integrada ahora está disponible, debe usar la función integrada correspondiente como que va a resultar más rápido ejecutar y más eficaz. 

### <a name="mathematical-functions"></a>Funciones matemáticas
Las funciones matemáticas realizan un cálculo, normalmente basado en valores de entrada que se proporcionan como argumentos y devuelven un valor numérico. Aquí tiene una tabla de funciones matemáticas integradas admitidas.

<table>
<tr>
<td><strong>Uso</strong></td>
<td><strong>Descripción</strong></td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_abs">ABS (num_expr)</a></td> 
<td>Devuelve el valor absoluto (positivo) de la expresión numérica especificada.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_ceiling">CEILING (num_expr)</a></td> 
<td>Devuelve el menor valor entero mayor o igual que la expresión numérica especificada.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_floor">FLOOR (num_expr)</a></td> 
<td>Devuelve al mayor número entero menor o igual que la expresión numérica especificada.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_exp">EXP (num_expr)</a></td> 
<td>Devuelve al exponente de la expresión numérica especificada.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_log">LOG (num_expr [, base])</a></td> 
<td>Devuelve el logaritmo natural de la expresión numérica especificada o el logaritmo en la base especificada.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_log10">LOG10 (num_expr)</a></td> 
<td>Devuelve el valor logarítmico base 10 de la expresión numérica especificada.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_round">REDONDEAR (num_expr)</a></td> 
<td>Devuelve un valor numérico, redondeado al valor entero más cercano.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_trunc">TRUNCAR (num_expr)</a></td> 
<td>Devuelve un valor numérico, que se trunca al valor entero más cercano.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_sqrt">SQRT (num_expr)</a></td>   
<td>Devuelve la raíz cuadrada de la expresión numérica especificada.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_square">CUADRADO (num_expr)</a></td>   
<td>Devuelve el cuadrado de la expresión numérica especificada.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_power">POWER (num_expr, num_expr)</a></td>   
<td>La potencia de la expresión numérica especificada se devuelve el valor especificado.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_sign">Inicio de sesión (num_expr)</a></td>   
<td>Devuelve el valor de inicio de sesión (-1, 0, 1) de la expresión numérica especificada.</td>
</tr>
<tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_acos">ACOS (num_expr)</a></td>   
<td>Devuelve el ángulo en radianes, cuyo coseno es la expresión numérica especificada; también denominado arcocoseno.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_asin">ASIN (num_expr)</a></td>   
<td>Devuelve el ángulo en radianes, cuyo seno es la expresión numérica especificada. También se conoce como arco seno.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_atan">ATAN (num_expr)</a></td>   
<td>Devuelve el ángulo en radianes, cuya tangente es la expresión numérica especificada. También se conoce como arco tangente.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_atn2">ATN2 (num_expr)</a></td>   
<td>Devuelve el ángulo en radianes, entre el eje x positivo y el radio desde el origen al punto (y, x), donde x e y son los valores de las dos expresiones de punto flotante especificado.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_cos">COS (num_expr)</a></td> 
<td>Devuelve el coseno de un ángulo especificado en radianes, en la expresión especificada.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_cot">COT (num_expr)</a></td> 
<td>Devuelve la cotangente trigonométrica de un ángulo especificado en radianes, en la expresión numérica especificada.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_degrees">GRADOS (num_expr)</a></td> 
<td>Devuelve el ángulo correspondiente en grados de un ángulo especificado en radianes.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_pi">PI)</a></td>   
<td>Devuelve el valor constante de PI.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_radians">RADIANES (num_expr)</a></td> 
<td>Devuelve radianes cuando se introduce una expresión numérica en grados.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_sin">SENO (num_expr)</a></td> 
<td>Devuelve el seno trigonométrico de un ángulo especificado en radianes, en la expresión especificada.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_tan">TG (num_expr)</a></td> 
<td>Devuelve la tangente de la expresión de entrada, en la expresión especificada.</td>
</tr>

</table> 

Por ejemplo, ahora puede ejecutar consultas como la siguiente:

**Consulta**

    SELECT VALUE ABS(-4)

**Resultados**

    [4]

La principal diferencia entre las funciones de DocumentDB en comparación con ANSI SQL es que están diseñados para funcionar bien con los datos de esquema de menos de esquema y mixtas. Por ejemplo, si tiene un documento donde la propiedad tamaño falta o tiene un valor no numérico, como "desconocida", se omite el documento, en lugar de un error.

### <a name="type-checking-functions"></a>Tipo de comprobación de funciones
Las funciones de comprobación de tipo permiten comprobar el tipo de una expresión dentro de las consultas SQL. Funciones de comprobación de tipo pueden usarse para determinar el tipo de propiedades en los documentos sobre la marcha cuando se variable o desconocido. Aquí tiene una tabla de funciones de comprobación de tipo integrado compatible.

<table>
<tr>
  <td><strong>Uso</strong></td>
  <td><strong>Descripción</strong></td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_array">IS_ARRAY (expr)</a></td>
  <td>Devuelve un valor Boolean que indica si el tipo del valor es una matriz.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_bool">IS_BOOL (expr)</a></td>
  <td>Devuelve un valor Boolean que indica si el tipo del valor es un valor Boolean.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_null">IS_NULL (expr)</a></td>
  <td>Devuelve un valor Boolean que indica si el tipo del valor es null.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_number">IS_NUMBER (expr)</a></td>
  <td>Devuelve un valor Boolean que indica si el tipo del valor es un número.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_object">IS_OBJECT (expr)</a></td>
  <td>Devuelve un valor Boolean que indica si el tipo del valor es un objeto JSON.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_string">IS_STRING (expr)</a></td>
  <td>Devuelve un valor Boolean que indica si el tipo del valor es una cadena.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_defined">IS_DEFINED (expr)</a></td>
  <td>Devuelve un valor Boolean que indica si la propiedad se ha asignado un valor.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_primitive">IS_PRIMITIVE (expr)</a></td>
  <td>Devuelve un valor Boolean que indica si el tipo del valor es una cadena, número, booleano o null.</td>
</tr>

</table>

Con estas funciones, ahora puede ejecutar consultas como la siguiente:

**Consulta**

    SELECT VALUE IS_NUMBER(-4)

**Resultados**

    [true]

### <a name="string-functions"></a>Funciones de cadenas
Las siguientes funciones escalares realizan una operación en un valor de entrada de cadena y devolución una cadena, un valor numérico o booleano. Aquí presentamos una tabla de funciones de cadena integradas:

Uso|Descripción
---|---
[LONGITUD (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_length)|Devuelve el número de caracteres de la expresión de cadena especificada
[CONCATENAR (str_expr, str_expr [, str_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_concat)|Devuelve una cadena que es el resultado de concatenar dos o más valores de cadena.
[SUBCADENA (str_expr, num_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_substring)|Devuelve la parte de una expresión de cadena.
[STARTSWITH (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_startswith)|Devuelve un valor Boolean que indica si la primera expresión de cadena termina con el segundo
[ENDSWITH (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_endswith)|Devuelve un valor Boolean que indica si la primera expresión de cadena termina con el segundo
[CONTAINS (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_contains)|Devuelve un valor Boolean que indica si la primera expresión de cadena contiene el segundo.
[INDEX_OF (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_index_of)|Devuelve la posición inicial de la primera aparición de la segunda cadena expresión dentro de la primera expresión de cadena especificada, o -1 si no se encuentra la cadena.
[LEFT (str_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_left)|Devuelve la parte izquierda de una cadena con el número especificado de caracteres.
[RIGHT (str_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_right)|Devuelve la parte derecha de una cadena con el número especificado de caracteres.
[LTRIM (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_ltrim)|Devuelve una expresión de cadena después de quitar los espacios iniciales en blanco.
[RTRIM (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_rtrim)|Devuelve una expresión de cadena después de truncar todos los espacios en blanco.
[INFERIOR (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_lower)|Devuelve una expresión de cadena después de convertir datos de caracteres en mayúsculas a minúsculas.
[SUPERIOR (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_upper)|Devuelve una expresión de cadena después de convertir datos en minúsculas a mayúsculas.
[Reemplazar (str_expr, str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_replace)|Reemplaza todas las apariciones de un valor de cadena especificado por otro valor de cadena.
[REPLICAR (str_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_replicate)|Repite un valor de cadena de un número determinado de veces.
[INVERSA (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_reverse)|Devuelve el inverso de un valor de cadena.

Con estas funciones, ahora puede ejecutar consultas como el siguiente. Por ejemplo, puede devolver el nombre de la familia en mayúsculas como sigue:

**Consulta**

    SELECT VALUE UPPER(Families.id)
    FROM Families

**Resultados**

    [
        "WAKEFIELDFAMILY", 
        "ANDERSENFAMILY"
    ]

O concatenar cadenas como en este ejemplo:

**Consulta**

    SELECT Families.id, CONCAT(Families.address.city, ",", Families.address.state) AS location
    FROM Families

**Resultados**

    [{
      "id": "WakefieldFamily",
      "location": "NY,NY"
    },
    {
      "id": "AndersenFamily",
      "location": "seattle,WA"
    }]


Funciones de cadenas también pueden utilizarse en la cláusula WHERE para filtrar los resultados, como en el ejemplo siguiente:

**Consulta**

    SELECT Families.id, Families.address.city
    FROM Families
    WHERE STARTSWITH(Families.id, "Wakefield")

**Resultados**

    [{
      "id": "WakefieldFamily",
      "city": "NY"
    }]

### <a name="array-functions"></a>Funciones de matriz
Las siguientes funciones escalares realizan una operación en un valor de entrada de la matriz y retorno numérico, valor booleano o matriz. Aquí presentamos una tabla de funciones integradas de matriz:

Uso|Descripción
---|---
[ARRAY_LENGTH (arr_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_length)|Devuelve el número de elementos de la expresión de matriz especificada.
[ARRAY_CONCAT (arr_expr, arr_expr [, arr_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_concat)|Devuelve una matriz que es el resultado de concatenar dos o más valores de la matriz.
[ARRAY_CONTAINS (arr_expr, expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_contains)|Devuelve un valor Boolean que indica si la matriz contiene el valor especificado.
[ARRAY_SLICE (arr_expr, num_expr [, num_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_slice)|Devuelve la parte de una expresión de matriz.

Funciones de matriz se pueden utilizar para manipular matrices en JSON. Por ejemplo, aquí es una consulta que devuelve todos los documentos donde uno de los elementos primarios es "Robin Wakefield". 

**Consulta**

    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin", familyName: "Wakefield" })

**Resultados**

    [{
      "id": "WakefieldFamily"
    }]

Este es otro ejemplo que usa ARRAY_LENGTH para obtener el número de hijos por familia.

**Consulta**

    SELECT Families.id, ARRAY_LENGTH(Families.children) AS numberOfChildren
    FROM Families 

**Resultados**

    [{
      "id": "WakefieldFamily",
      "numberOfChildren": 2
    },
    {
      "id": "AndersenFamily",
      "numberOfChildren": 1
    }]

### <a name="spatial-functions"></a>Funciones espaciales

DocumentDB es compatible con las siguientes funciones integradas de abrir geoespaciales Consortium (OGC) para consultar geoespaciales. Para obtener más detalles sobre geoespaciales soporte técnico en DocumentDB, vea [trabajar con datos geoespaciales en DocumentDB de Azure](documentdb-geospatial.md). 

<table>
<tr>
  <td><strong>Uso</strong></td>
  <td><strong>Descripción</strong></td>
</tr>
<tr>
  <td>ST_DISTANCE (point_expr, point_expr)</td>
  <td>Devuelve la distancia entre las dos expresiones de punto de GeoJSON.</td>
</tr>
<tr>
  <td>ST_WITHIN (point_expr, polygon_expr)</td>
  <td>Devuelve una expresión booleana que indica si el punto de GeoJSON especificado en el primer argumento es dentro del polígono GeoJSON en el segundo argumento.</td>
</tr>
<tr>
  <td>ST_ISVALID</td>
  <td>Devuelve un valor Boolean que indica si la expresión de punto o polígono GeoJSON especificada es válida.</td>
</tr>
<tr>
  <td>ST_ISVALIDDETAILED</td>
  <td>Devuelve un valor JSON que contiene un valor Boolean valor si la expresión de punto o polígono GeoJSON especificada es válida y no válidos, además el motivo como un valor de cadena.</td>
</tr>
</table>

Funciones espaciales pueden utilizarse para realizar proximidad querries contra datos espaciales. Por ejemplo, aquí es una consulta que devuelve todos los documentos de familia que están dentro de 30 km de la ubicación especificada utilizando la función integrada de ST_DISTANCE. 

**Consulta**

    SELECT f.id 
    FROM Families f 
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000

**Resultados**

    [{
      "id": "WakefieldFamily"
    }]

Si incluye la indización espacial en la directiva de indización, a continuación, "consultas distancia" se servirá eficaz a través del índice. Para obtener más detalles sobre la indización espacial, consulte la sección siguiente. Si no tiene un índice espacial para rutas especificadas, aún puede realizar consultas espaciales especificando `x-ms-documentdb-query-enable-scan` encabezado de la solicitud con el valor establecido en "true". En. NET, esto puede hacerse pasando el argumento opcional de **FeedOptions** a las consultas con [EnableScanInQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.enablescaninquery.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.EnableScanInQuery) establecida en true. 

ST_WITHIN puede utilizarse para comprobar si un punto se encuentra dentro de un polígono. Polígonos se usan generalmente para representar los límites como códigos postales, los límites de estado o formaciones naturales. Nuevo si incluye la indización espacial en la directiva de indización, a continuación, "dentro de" consultas se servirá eficaz a través del índice. 

Argumentos de polígono en ST_WITHIN pueden contener solo un único timbre, es decir, los polígonos no deben contener taladros en ellos. Compruebe los [límites de DocumentDB](documentdb-limits.md) para el número máximo de puntos que se permiten en un polígono para una consulta de ST_WITHIN.

**Consulta**

    SELECT * 
    FROM Families f 
    WHERE ST_WITHIN(f.location, {
        'type':'Polygon', 
        'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
    })

**Resultados**

    [{
      "id": "WakefieldFamily",
    }]
    
>[AZURE.NOTE] De forma similar a cómo discrepancia funciona de tipos de consulta DocumentDB, si el valor de la ubicación especificada en bien argumento es incorrecto o no válidos, evaluará **definido** y el documento evaluado se omite desde los resultados de la consulta. Si la consulta no devuelve ningún resultado, ejecute ST_ISVALIDDETAILED a depurar, ¿por qué el tipo de spatail no es válido.     

ST_ISVALID y ST_ISVALIDDETAILED pueden usarse para comprobar si un objeto espacial es válido. Por ejemplo, la siguiente consulta comprueba la validez de un punto con una fuera del valor de rango latitud (-132.8). ST_ISVALID devuelve un valor de tipo Boolean y ST_ISVALIDDETAILED devuelve el valor booleano y una cadena que contiene el motivo por qué se considera no válido.

**Consulta**

    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })

**Resultados**

    [{
      "$1": false
    }]

Estas funciones también pueden usarse para validar polígonos. Por ejemplo, aquí usamos ST_ISVALIDDETAILED para validar un polígono que no está cerrado. 

**Consulta**

    SELECT ST_ISVALIDDETAILED({ "type": "Polygon", "coordinates": [[ 
        [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] 
        ]]})

**Resultados**

    [{
       "$1": { 
          "valid": false, 
          "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points." 
        }
    }]
    
Que se ajusta el espaciales funciones y la sintaxis SQL para DocumentDB. Ahora veamos cómo LINQ consultar works y cómo interactúa con la sintaxis que hemos visto hasta ahora.

## <a name="linq-to-documentdb-sql"></a>LINQ to SQL DocumentDB
LINQ es un modelo de programación de .NET que exprese cálculo como consultas en secuencias de objetos. DocumentDB proporciona un cliente de biblioteca de lado a interfaz con LINQ al facilitar una conversión entre objetos JSON y .NET y una asignación de un subconjunto de LINQ consulta a las consultas de DocumentDB. 

La imagen siguiente muestra la arquitectura de admitir consultas LINQ con DocumentDB.  Con el cliente de DocumentDB, los desarrolladores pueden crear un objeto de **IQueryable** que consulta directamente el proveedor de consulta DocumentDB, que se traduce la consulta LINQ una consulta DocumentDB. A continuación, se pasa la consulta en el servidor DocumentDB para recuperar un conjunto de resultados en formato JSON. Los resultados devueltos son deserializarse en una secuencia de objetos de .NET en el cliente.

![Arquitectura de admitir consultas LINQ con DocumentDB - sintaxis SQL, lenguaje de consulta JSON, conceptos de base de datos y consultas SQL][1]
 


### <a name="net-and-json-mapping"></a>Asignación de JSON y .NET
La asignación entre objetos .NET y documentos JSON es natural: cada campo de miembro de datos se asigna a un objeto JSON, donde el nombre de campo se asigna a la parte de "clave" del objeto y la parte de "valor" es recursiva asignado a la parte del valor del objeto. Considere el ejemplo siguiente. El objeto de familia creado se asigna al documento JSON tal como se muestra a continuación. Y viceversa, el documento JSON está asignado a un objeto .NET.

**Clase de C#**

    public class Family
    {
        [JsonProperty(PropertyName="id")]
        public string Id;
        public Parent[] parents;
        public Child[] children;
        public bool isRegistered;
    };
    
    public struct Parent
    {
        public string familyName;
        public string givenName;
    };
    
    public class Child
    {
        public string familyName;
        public string givenName;
        public string gender;
        public int grade;
        public List<Pet> pets;
    };
    
    public class Pet
    {
        public string givenName;
    };
    
    public class Address
    {
        public string state;
        public string county;
        public string city;
    };
    
    // Create a Family object.
    Parent mother = new Parent { familyName= "Wakefield", givenName="Robin" };
    Parent father = new Parent { familyName = "Miller", givenName = "Ben" };
    Child child = new Child { familyName="Merriam", givenName="Jesse", gender="female", grade=1 };
    Pet pet = new Pet { givenName = "Fluffy" };
    Address address = new Address { state = "NY", county = "Manhattan", city = "NY" };
    Family family = new Family { Id = "WakefieldFamily", parents = new Parent [] { mother, father}, children = new Child[] { child }, isRegistered = false };


**JSON**  

    {
        "id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam", 
                "givenName": "Jesse", 
                "gender": "female", 
                "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            { 
              "familyName": "Miller", 
              "givenName": "Lisa", 
              "gender": "female", 
              "grade": 8 
            }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
    };



### <a name="linq-to-sql-translation"></a>LINQ traducción de SQL
El proveedor de consultas DocumentDB realiza una mejor asignación de esfuerzo de una consulta LINQ en la consulta SQL DocumentDB. En la siguiente descripción, se supone que el lector tiene un conocimiento básico de LINQ.

En primer lugar, para el sistema de tipo, se admiten todos los tipos simples de JSON – tipos numéricos, boolean, string y null. Solo estos tipos JSON son compatibles. Se admiten las siguientes expresiones escalares.

-   Valores constantes: estos incluye valores constantes de los tipos de datos simples en el momento en que se evalúa la consulta.

-   Expresiones de índice de propiedad o matriz: estas expresiones hacen referencia a la propiedad de un objeto o un elemento de matriz.

        family.Id;
        family.children[0].familyName;
        family.children[0].grade;
        family.children[n].grade; //n is an int variable

-   Las expresiones aritméticas - estos incluyen expresiones aritméticas comunes en valores numéricos y booleanos. Para obtener la lista completa, consulte la especificación de SQL.

        2 * family.children[0].grade;
        x + y;

-   Expresión de comparación de cadena - incluyen comparación de un valor de cadena con algún valor de cadena constante.  
 
        mother.familyName == "Smith";
        child.givenName == s; //s is a string variable

-   Objeto o matriz de expresión de creación - estas expresiones devuelto un objeto de tipo de valor compuesto o anónima o una matriz de dichos objetos. Estos valores se pueden anidar.

        new Parent { familyName = "Smith", givenName = "Joe" };
        new { first = 1, second = 2 }; //an anonymous type with 2 fields              
        new int[] { 3, child.grade, 5 };

### <a name="list-of-supported-linq-operators"></a>Lista de operadores LINQ compatibles
Aquí tiene una lista de operadores LINQ compatibles en el proveedor LINQ con el SDK de .NET DocumentDB.

-   **Seleccionar**: proyecciones traducen a SQL SELECT incluidos construcción de objetos
-   **Donde**: filtros traducir a donde SQL y compatible con la traducción entre & &, || y! a los operadores SQL
-   **SelectMany**: permite anulación de matrices a la cláusula SQL JOIN. Puede usarse para cadena/anidar expresiones para filtrar los elementos de la matriz
-   **OrderBy y OrderByDescending**: se traduce a ordenar por orden ascendente o descendente:
-   **CompareTo**: se traduce a comparaciones de rango. Más utilizadas en cadenas ya que no son comparables en .NET
-   **Tomar**: se traduce a la parte superior de SQL para limitar los resultados de una consulta
-   **Funciones matemáticas**: admite traducción desde. Abs, Acos, Asin de red, Atan, Ceiling Cos, Exp, Floor, Log, Log10, Pow, redondear, inicio de sesión, Sin, Sqrt, Tan, truncar a las funciones integradas de SQL equivalentes.
-   **Funciones de cadena**: admite traducción desde. Concatenación, Contains, EndsWith de red, IndexOf, contar, ToLower, TrimStart, reemplazar, inversa, TrimEnd, StartsWith, subcadena, ToUpper a las funciones integradas de SQL equivalentes.
-   **Funciones de matriz**: admite traducción desde. De NET concatenación, contiene y contar a las funciones integradas de SQL equivalentes.
-   **Funciones de extensión geoespaciales**: admite traducción de métodos de código auxiliar distancia dentro IsValid y IsValidDetailed a las funciones integradas de SQL equivalentes.
-   **Función de extensión de función definida por el usuario**: función definida por el traducción es compatible con el método de código auxiliar UserDefinedFunctionProvider.Invoke al usuario correspondiente.
-   **Varios**: admite traducción de la combinación y operadores condicionales. Puede traducir contiene cadena contiene, ARRAY_CONTAINS o en SQL según el contexto.

### <a name="sql-query-operators"></a>Operadores de consulta SQL
Aquí se muestran algunos ejemplos que ilustran cómo algunos de los operadores de consulta estándares de LINQ se convierten a las consultas de DocumentDB.

#### <a name="select-operator"></a>Seleccione el operador
La sintaxis es `input.Select(x => f(x))`, donde `f` es una expresión escalar.

**Expresión de lambda LINQ**

    input.Select(family => family.parents[0].familyName);

**SQL** 

    SELECT VALUE f.parents[0].familyName
    FROM Families f



**Expresión de lambda LINQ**

    input.Select(family => family.children[0].grade + c); // c is an int variable


**SQL** 

    SELECT VALUE f.children[0].grade + c
    FROM Families f 



**Expresión de lambda LINQ**

    input.Select(family => new
    {
        name = family.children[0].familyName,
        grade = family.children[0].grade + 3
    });


**SQL** 

    SELECT VALUE {"name":f.children[0].familyName, 
                  "grade": f.children[0].grade + 3 }
    FROM Families f



#### <a name="selectmany-operator"></a>Operador SelectMany
La sintaxis es `input.SelectMany(x => f(x))`, donde `f` es una expresión escalar que devuelve un tipo de la colección.

**Expresión de lambda LINQ**

    input.SelectMany(family => family.children);

**SQL** 

    SELECT VALUE child
    FROM child IN Families.children



#### <a name="where-operator"></a>Donde operador
La sintaxis es `input.Where(x => f(x))`, donde `f` es una expresión escalar que devuelve un valor Boolean.

**Expresión de lambda LINQ**

    input.Where(family=> family.parents[0].familyName == "Smith");

**SQL** 

    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith" 



**Expresión de lambda LINQ**

    input.Where(
        family => family.parents[0].familyName == "Smith" && 
        family.children[0].grade < 3);

**SQL** 

    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith"
    AND f.children[0].grade < 3


### <a name="composite-sql-queries"></a>Consultas SQL compuestas
Los operadores anteriores pueden estar compuestos para formar consultas más eficaces. Puesto que DocumentDB admite colecciones anidadas, la composición puede ser concatena o anidada.

#### <a name="concatenation"></a>Concatenación 

La sintaxis es `input(.|.SelectMany())(.Select()|.Where())*`. Puede iniciar una consulta concatenada con una función opcional `SelectMany` consulta seguido de varios `Select` o `Where` operadores.


**Expresión de lambda LINQ**

    input.Select(family=>family.parents[0])
        .Where(familyName == "Smith");

**SQL**

    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith"



**Expresión de lambda LINQ**

    input.Where(family => family.children[0].grade > 3)
        .Select(family => family.parents[0].familyName);

**SQL** 

    SELECT VALUE f.parents[0].familyName
    FROM Families f
    WHERE f.children[0].grade > 3



**Expresión de lambda LINQ**

    input.Select(family => new { grade=family.children[0].grade}).
        Where(anon=> anon.grade < 3);
            
**SQL** 

    SELECT *
    FROM Families f
    WHERE ({grade: f.children[0].grade}.grade > 3)



**Expresión de lambda LINQ**

    input.SelectMany(family => family.parents)
        .Where(parent => parents.familyName == "Smith");

**SQL** 

    SELECT *
    FROM p IN Families.parents
    WHERE p.familyName = "Smith"



#### <a name="nesting"></a>Anidar

La sintaxis es `input.SelectMany(x=>x.Q())` donde Q es un `Select`, `SelectMany`, o `Where` operador.

En una consulta anidada, la consulta interna se aplica a cada elemento de la colección exterior. Una característica importante es que la consulta interna puede hacer referencia a los campos de los elementos de la colección exterior como autocombinaciones.

**Expresión de lambda LINQ**

    input.SelectMany(family=> 
        family.parents.Select(p => p.familyName));

**SQL** 

    SELECT VALUE p.familyName
    FROM Families f
    JOIN p IN f.parents


**Expresión de lambda LINQ**

    input.SelectMany(family => 
        family.children.Where(child => child.familyName == "Jeff"));
            
**SQL** 

    SELECT *
    FROM Families f
    JOIN c IN f.children
    WHERE c.familyName = "Jeff"



**Expresión de lambda LINQ**
            
    input.SelectMany(family => family.children.Where(
        child => child.familyName == family.parents[0].familyName));

**SQL** 

    SELECT *
    FROM Families f
    JOIN c IN f.children
    WHERE c.familyName = f.parents[0].familyName


## <a name="executing-sql-queries"></a>Ejecutar consultas SQL
DocumentDB expone recursos a través de una API de REST puede llamar a cualquier idioma capaz de realizar las solicitudes HTTP/HTTPS. Además, DocumentDB ofrece bibliotecas de programación para varios idiomas populares como .NET, Node.js, JavaScript y Python. Soportan de la API de REST y las diversas bibliotecas consultar a través de SQL. El SDK de .NET admite LINQ consultar además de SQL.

Los siguientes ejemplos muestra cómo crear una consulta y enviar con una cuenta de base de datos de DocumentDB.

### <a name="rest-api"></a>API DE REST
DocumentDB ofrece un modelo de programación REST abierto sobre HTTP. Se pueden aprovisionar cuentas de base de datos usando una suscripción de Azure. El modelo de recursos de DocumentDB consta de un conjuntos de recursos en una cuenta de base de datos, cada una de las cuales es accesible mediante un URI estable y lógico. Un conjunto de recursos se conoce como una fuente de este documento. Una cuenta de base de datos consta de un conjunto de bases de datos, cada una con varias colecciones, cada uno de los que a su vez contienen documentos, UDF y otros tipos de recursos.

El modelo de interacción básica con estos recursos es a través de los verbos HTTP GET, SUPERPONER, publicar y eliminar con su interpretación estándar. El verbo POST se usa para la creación de un nuevo recurso, para ejecutar un procedimiento almacenado o para realizar consultas DocumentDB. Consultas siempre se leen sólo operaciones no tiene efectos secundarios.

Los siguientes ejemplos muestra una publicación de una consulta de DocumentDB realizada en una colección que contiene los documentos de muestra dos que hemos revisado hasta ese momento. La consulta tiene un filtro simple en la propiedad del nombre JSON. Observe el uso de la `x-ms-documentdb-isquery` y tipo de contenido: `application/query+json` encabezados para indicar que la operación es una consulta.


**Solicitar**

    POST https://<REST URI>/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {      
        "query": "SELECT * FROM Families f WHERE f.id = @familyId",     
        "parameters": [          
            {"name": "@familyId", "value": "AndersenFamily"}         
        ] 
    }
    

**Resultados**

    HTTP/1.1 200 Ok
    x-ms-activity-id: 8b4678fa-a947-47d3-8dd3-549a40da6eed
    x-ms-item-count: 1
    x-ms-request-charge: 0.32
    
    <indented for readability, results highlighted>
    
    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "id":"AndersenFamily",
             "lastName":"Andersen",
             "parents":[  
                {  
                   "firstName":"Thomas"
                },
                {  
                   "firstName":"Mary Kay"
                }
             ],
             "children":[  
                {  
                   "firstName":"Henriette Thaulow",
                   "gender":"female",
                   "grade":5,
                   "pets":[  
                      {  
                         "givenName":"Fluffy"
                      }
                   ]
                }
             ],
             "address":{  
                "state":"WA",
                "county":"King",
                "city":"seattle"
             },
             "_rid":"u1NXANcKogEcAAAAAAAAAA==",
             "_ts":1407691744,
             "_self":"dbs\/u1NXAA==\/colls\/u1NXANcKogE=\/docs\/u1NXANcKogEcAAAAAAAAAA==\/",
             "_etag":"00002b00-0000-0000-0000-53e7abe00000",
             "_attachments":"_attachments\/"
          }
       ],
       "count":1
    }


El segundo ejemplo muestra una consulta más compleja que devuelve varios resultados de la combinación.

**Solicitar**

    POST https://<REST URI>/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json
    
    {      
        "query": "SELECT 
                     f.id AS familyName, 
                     c.givenName AS childGivenName, 
                     c.firstName AS childFirstName, 
                     p.givenName AS petName 
                  FROM Families f 
                  JOIN c IN f.children 
                  JOIN p in c.pets",     
        "parameters": [] 
    }


**Resultados**

    HTTP/1.1 200 Ok
    x-ms-activity-id: 568f34e3-5695-44d3-9b7d-62f8b83e509d
    x-ms-item-count: 1
    x-ms-request-charge: 7.84
    
    <indented for readability, results highlighted>
    
    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "familyName":"AndersenFamily",
             "childFirstName":"Henriette Thaulow",
             "petName":"Fluffy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Goofy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Shadow"
          }
       ],
       "count":3
    }


Si los resultados de la consulta no caben en una sola página de resultados, la API de REST devuelve un token de continuación a través de la `x-ms-continuation-token` encabezado de respuesta. Los clientes pueden paginar resultados incluyendo el encabezado en los resultados siguientes. También puede controlar el número de resultados por página mediante la `x-ms-max-item-count` encabezado número.

Para administrar la directiva de coherencia de datos de consultas, utilice el `x-ms-consistency-level` encabezado como todas las solicitudes de API de REST. Coherencia de sesión, es necesario también la información más reciente de eco `x-ms-session-token` encabezado Cookie en la solicitud de consulta. Tenga en cuenta que Directiva de indización de la colección consultado también puede influir en la coherencia de los resultados de la consulta. Con el predeterminado de la configuración de la directiva de indización para colecciones el índice es siempre actual con el contenido del documento y resultados de la consulta se coincide con la coherencia elegida para los datos. Si la directiva de indexación es más flexible a Lazy, las consultas pueden devolver resultados obsoletos. Para obtener más información, consulte [Niveles de coherencia de DocumentDB] [consistency-levels].

Si la directiva de indización configurada en la colección no es compatible con la consulta especificada, el servidor de DocumentDB devuelve 400 "solicitud incorrecta". Esto se devuelve para consultas de rango en rutas de acceso configuradas para las búsquedas de hash (igualdad) así como rutas explícitamente excluidos de la indización. La `x-ms-documentdb-query-enable-scan` encabezado puede especificarse para permitir la consulta realizar un análisis cuando un índice no está disponible.

### <a name="c-net-sdk"></a>C# (. NET) SDK
El SDK de .NET admite LINQ y SQL consultar. En el ejemplo siguiente se muestra cómo realizar la consulta de filtro simple presentada anteriormente en este documento.


    foreach (var family in client.CreateDocumentQuery(collectionLink, 
        "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }
    
    SqlQuerySpec query = new SqlQuerySpec("SELECT * FROM Families f WHERE f.id = @familyId");
    query.Parameters = new SqlParameterCollection();
    query.Parameters.Add(new SqlParameter("@familyId", "AndersenFamily"));

    foreach (var family in client.CreateDocumentQuery(collectionLink, query))
    {
        Console.WriteLine("\tRead {0} from parameterized SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery(collectionLink)
        where f.Id == "AndersenFamily"
        select f))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }
    
    foreach (var family in client.CreateDocumentQuery(collectionLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }


En este ejemplo se compara dos propiedades de igualdad dentro de cada documento y usa proyecciones anónimas. 


    foreach (var family in client.CreateDocumentQuery(collectionLink,
        @"SELECT {""Name"": f.id, ""City"":f.address.city} AS Family 
        FROM Families f 
        WHERE f.address.city = f.address.state"))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }
    
    foreach (var family in (
        from f in client.CreateDocumentQuery<Family>(collectionLink)
        where f.address.city == f.address.state
        select new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }
    
    foreach (var family in
        client.CreateDocumentQuery<Family>(collectionLink)
        .Where(f => f.address.city == f.address.state)
        .Select(f => new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }


En el ejemplo siguiente muestra las combinaciones, expresadas mediante LINQ SelectMany.


    foreach (var pet in client.CreateDocumentQuery(collectionLink,
          @"SELECT p
            FROM Families f 
                 JOIN c IN f.children 
                 JOIN p in c.pets 
            WHERE p.givenName = ""Shadow"""))
    {
        Console.WriteLine("\tRead {0} from SQL", pet);
    }
    
    // Equivalent in Lambda expressions
    foreach (var pet in
        client.CreateDocumentQuery<Family>(collectionLink)
        .SelectMany(f => f.children)
        .SelectMany(c => c.pets)
        .Where(p => p.givenName == "Shadow"))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", pet);
    }



El cliente de .NET recorre automáticamente en todas las páginas de resultados de la consulta en los bloques de foreach como se indicó anteriormente. Las opciones de consulta que introdujo en la sección API de REST también están disponibles en el SDK de .NET utilizando la `FeedOptions` y `FeedResponse` las clases en el método CreateDocumentQuery. Puede controlar el número de páginas con la `MaxItemCount` configuración. 

Explícitamente también puede controlar la paginación mediante la creación de `IDocumentQueryable` mediante la `IQueryable` objeto, a continuación, mediante la lectura de la` ResponseContinuationToken` valores y pasarlos nuevamente como `RequestContinuationToken` en `FeedOptions`. `EnableScanInQuery`se puede establecer para habilitar análisis cuando la consulta no sea compatible con la directiva de indización configurada. Para las colecciones divididas, puede usar `PartitionKey` para ejecutar la consulta en una sola partición (aunque DocumentDB puede extraer automáticamente esto desde el texto de consulta), y `EnableCrossPartitionQuery` para ejecutar consultas que necesitan ejecutarse en varias particiones. 

Para ver más ejemplos que contiene las consultas, consulte [ejemplos de .NET DocumentDB](https://github.com/Azure/azure-documentdb-net) . 

### <a name="javascript-server-side-api"></a>API de JavaScript del servidor 
DocumentDB proporciona un modelo de programación para ejecutar lógica JavaScript en función de la aplicación directamente en las colecciones de usar desencadenadores y procedimientos almacenados. La lógica de JavaScript registrada en un nivel de la colección puede ejecutar operaciones de base de datos en las operaciones en los documentos de la colección dada. Estas operaciones se agrupan en ambientes transacciones ACID.

En el ejemplo siguiente se muestra cómo usar la queryDocuments en el servidor de JavaScript API para realizar consultas desde interior procedimientos almacenados y desencadenadores.


    function businessLogic(name, author) {
        var context = getContext();
        var collectionManager = context.getCollection();
        var collectionLink = collectionManager.getSelfLink()
    
        // create a new document.
        collectionManager.createDocument(collectionLink,
            { name: name, author: author },
            function (err, documentCreated) {
                if (err) throw new Error(err.message);
    
                // filter documents by author
                var filterQuery = "SELECT * from root r WHERE r.author = 'George R.'";
                collectionManager.queryDocuments(collectionLink,
                    filterQuery,
                    function (err, matchingDocuments) {
                        if (err) throw new Error(err.message);
    context.getResponse().setBody(matchingDocuments.length);
    
                        // Replace the author name for all documents that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].author = "George R. R. Martin";
                            // we don't need to execute a callback because they are in parallel
                            collectionManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);
                        }
                    })
            });
    }

## <a name="aggregate-functions"></a>Funciones de agregado

Compatibilidad para funciones de agregado se encuentra en la obra, pero si necesita contar o sumar funcionalidad mientras tanto, se puede conseguir el mismo resultado con diferentes métodos.  

En la ruta de acceso de lectura:

- Para realizar las funciones de agregado de recuperar los datos y realizar un recuento localmente. Que es aconsejable para usar una proyección de consulta económico como `SELECT VALUE 1` en lugar de un documento completo como `SELECT * FROM c`. Esto le permite maximizar el número de documentos que se procesan en cada página de resultados, lo que evita adicionales de ida y vuelta al servicio si es necesario.
- También puede usar un procedimiento almacenado para minimizar la latencia de red de ida y vuelta repetidas. Para un procedimiento almacenado de ejemplo que calcula el recuento de una consulta de filtro, vea [Count.js](https://github.com/Azure/azure-documentdb-js-server/blob/master/samples/stored-procedures/Count.js). El procedimiento almacenado puede habilitar los usuarios combinar lógica de negocios enriquecidos junto con realizando agregaciones de forma eficaz.

En la ruta de acceso de escritura:

- Otro patrón común es agregar previamente los resultados en la ruta de acceso "escribir". Esto es especialmente atractivo cuando el volumen de solicitudes de "lectura" es mayor que el de solicitudes de "escribir". Una vez agregadas previamente, los resultados están disponibles con un solo punto solicitud de lectura.  Configurar un desencadenador que se invoca con cada "escritura" y actualizar un documento de metadatos que tiene los últimos resultados de la consulta que se está materializa es la mejor manera de agregar previamente en DocumentDB. Por ejemplo, consulte el ejemplo [UpdateaMetadata.js](https://github.com/Azure/azure-documentdb-js-server/blob/master/samples/triggers/UpdateMetadata.js) , que actualiza la minSize, tamaño máximo y totalSize del documento para la colección de metadatos. El ejemplo puede ampliarse para actualizar un contador, suma, etcetera.

##<a name="references"></a>Referencias
1.  [Introducción a Azure DocumentDB][introduction]
2.  [Especificación de DocumentDB SQL](http://go.microsoft.com/fwlink/p/?LinkID=510612)
3.  [Ejemplos de .NET DocumentDB](https://github.com/Azure/azure-documentdb-net)
4.  [Niveles de coherencia de DocumentDB][consistency-levels]
5.  ANSI SQL 2011 [http://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681](http://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
6.  JSON [http://json.org/](http://json.org/)
7.  Especificación de JavaScript [http://www.ecma-international.org/publications/standards/Ecma-262.htm](http://www.ecma-international.org/publications/standards/Ecma-262.htm) 
8.  LINQ [http://msdn.microsoft.com/library/bb308959.aspx](http://msdn.microsoft.com/library/bb308959.aspx) 
9.  Técnicas de evaluación de consulta para grandes bases de datos [http://dl.acm.org/citation.cfm?id=152611](http://dl.acm.org/citation.cfm?id=152611)
10. Procesamiento de consultas en paralelo de base de datos relacional sistemas, IEEE Computer Society Press, 1994
11. Lu, Ooi, Tan, procesamiento de consultas en paralelo de base de datos relacional sistemas, IEEE Computer Society Press, 1994.
12. Juan Olston, Benjamin Reed, Utkarsh Srivastava, Ravi Kumar, Andrew Tomkins: latino cerdo: un no para otros idiomas para el procesamiento de datos, SIGMOD 2008.
13.     G. Graefe. El marco en cascada para optimizar la consulta. Datos de IEEE ing. Bull., 18(3): 1995.


[1]: ./media/documentdb-sql-query/sql-query1.png
[introduction]: documentdb-introduction.md
[consistency-levels]: documentdb-consistency-levels.md
 

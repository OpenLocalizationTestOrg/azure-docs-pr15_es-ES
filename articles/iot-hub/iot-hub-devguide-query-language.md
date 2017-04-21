<properties
 pageTitle="Guía de programador - lenguaje de consulta | Microsoft Azure"
 description="Guía de programador de Azure IoT concentrador - descripción del lenguaje de consulta que se utiliza para recuperar información sobre gemelos, métodos y trabajos de su centro IoT"
 services="iot-hub"
 documentationCenter=".net"
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="elioda"/>

# <a name="reference---query-language-for-twins-and-jobs"></a>Referencia: lenguaje de consulta para twins y trabajos

## <a name="overview"></a>Información general

Concentrador de IoT proporciona un lenguaje similar a SQL eficaz para recuperar información sobre [twins dispositivo] [ lnk-twins] y [trabajos][lnk-jobs]. Este artículo presenta:

* Introducción a las principales características de lenguaje de consulta del concentrador IoT, y
* La descripción detallada del idioma.

## <a name="getting-started-with-twin-queries"></a>Introducción a las consultas de doble

[Twins dispositivo] [ lnk-twins] puede contener objetos JSON arbitrarios como etiquetas y propiedades. Concentrador IoT permite twins de dispositivo de consulta como un único documento JSON que contiene toda la información de doble.
Por ejemplo, supongamos que su twins de concentrador IoT tienen la estructura siguiente:

        {                                                                      
            "deviceId": "myDeviceId",                                            
            "etag": "AAAAAAAAAAc=",                                              
            "tags": {                                                            
                "location": {                                                      
                    "region": "US",                                                  
                    "plant": "Redmond43"                                             
                }                                                                  
            },                                                                   
            "properties": {                                                      
                "desired": {                                                       
                    "telemetryConfig": {                                             
                        "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",            
                        "sendFrequencyInSecs": 300                                          
                    },                                                               
                    "$metadata": {                                                   
                    ...                                                     
                    },                                                               
                    "$version": 4                                                    
                },                                                                 
                "reported": {                                                      
                    "connectivity": {                                                
                        "type": "cellular"                            
                    },                                                               
                    "telemetryConfig": {                                             
                        "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",            
                        "sendFrequencyInSecs": 300,                                         
                        "status": "Success"                                            
                    },                                                               
                    "$metadata": {                                                   
                    ...                                                
                    },                                                               
                    "$version": 7                                                    
                }                                                                  
            }                                                                    
        }

Concentrador IoT expone a twins de dispositivo como una colección de documento denominada **dispositivos**.
Por lo tanto, la siguiente consulta recupera todo el conjunto de twins del dispositivo:

        SELECT * FROM devices

> [AZURE.NOTE] [IoT concentrador SDK] [ lnk-hub-sdks] admite la paginación de resultados de gran tamaño.

Concentrador IoT permite recuperar twins filtrado con condiciones arbitrarias. Por ejemplo,

        SELECT * FROM devices
        WHERE tags.location.region = 'US'

recupera a la twins con la etiqueta **location.region** establecer **nos**.
Operadores booleanos y comparaciones aritméticas también son compatibles, p. ej.

        SELECT * FROM devices
        WHERE tags.location.region = 'US'
            AND properties.reported.telemetryConfig.sendFrequencyInSecs >= 60

recupera a todos los twins ubicados en los Estados Unidos configurado para enviar telemetría menos frecuencia que cada minuto. Para mayor comodidad, también es posible usar constantes matriciales junto con las **de** y los operadores **NEN** (no en). Por ejemplo,

        SELECT * FROM devices
        WHERE property.reported.connectivity IN ['wired', 'wifi']

recupera a todos los twins que notifica Wi-Fi o por cable conectividad. Hacer referencia a la [cláusula WHERE] [ lnk-query-where] sección para la referencia completa de las capacidades de filtrado.

Agrupación y agregaciones también son compatibles. Por ejemplo,

        SELECT properties.reported.telemetryConfig.status AS status,
            COUNT() AS numberOfDevices
        FROM devices
        GROUP BY properties.reported.telemetryConfig.status

Devuelve el recuento de los dispositivos de cada estado de la configuración de telemetría.

        [
            {
                "numberOfDevices": 3,
                "status": "Success"
            },
            {
                "numberOfDevices": 2,
                "status": "Pending"
            },
            {
                "numberOfDevices": 1,
                "status": "Error"
            }
        ]

El ejemplo anterior ilustra una situación donde tres dispositivos informaron de configuración correcta, dos aplican la configuración y uno un error.

### <a name="c-example"></a>Ejemplo de C#

La funcionalidad de consulta se expone por el [servicio de C# SDK] [ lnk-hub-sdks] en la clase de **RegistryManager** .
Aquí tiene un ejemplo de una consulta simple:

        var query = registryManager.CreateQuery("SELECT * FROM devices", 100);
        while (query.HasMoreResults)
        {
            var page = await query.GetNextAsTwinAsync();
            foreach (var twin in page) 
            {
                // do work on twin object
            }
        }

Observe cómo se crea una instancia del objeto de la **consulta** con un tamaño de página (hasta 1000) y, a continuación, se pueden recuperar varias páginas mediante una llamada a los métodos **GetNextAsTwinAsync** varias veces.
Es importante que tenga en cuenta que el objeto de consulta expone varios **siguiente\***, según la opción de deserialización requerida por la consulta, es decir, dos u objetos o sin formato Json para usar cuando se utiliza proyecciones de trabajo.

### <a name="node-example"></a>Ejemplo de nodo

La funcionalidad de consulta se expone por el [servicio de nodo SDK] [ lnk-hub-sdks] en el objeto de **registro** .
Aquí tiene un ejemplo de una consulta simple:

        var query = registry.createQuery('SELECT * FROM devices', 100);
        var onResults = function(err, results) {
            if (err) {
                console.error('Failed to fetch the results: ' + err.message);
            } else {
                // Do something with the results
                results.forEach(function(twin) {
                    console.log(twin.deviceId);
                });

                if (query.hasMoreResults) {
                    query.nextAsTwin(onResults);
                }
            }
        };
        query.nextAsTwin(onResults);

Observe cómo se crea una instancia del objeto de la **consulta** con un tamaño de página (hasta 1000) y, a continuación, se pueden recuperar varias páginas mediante una llamada a los métodos **nextAsTwin** varias veces.
Es importante que tenga en cuenta que el objeto de consulta expone varios **siguiente\***, según la opción de deserialización requerida por la consulta, es decir, dos u objetos o sin formato Json para usar cuando se utiliza proyecciones de trabajo.

### <a name="limitations"></a>Limitaciones

Actualmente, proyecciones solo son compatibles con agregaciones, es decir, solo pueden usar las consultas no agregado `SELECT *`. Además, agregación solo son compatibles con junto con la agrupación.

## <a name="getting-started-with-jobs-queries"></a>Introducción a las consultas de trabajos

[Trabajos] [ lnk-jobs] proporciona una manera de ejecutar acciones en conjuntos de dispositivos. Cada dos dispositivo con la información de los trabajos de la que forma parte de una colección de **trabajos**.
De forma lógica

        {                                                                      
            "deviceId": "myDeviceId",                                            
            "etag": "AAAAAAAAAAc=",                                              
            "tags": {                                                            
                ...                                                              
            },                                                                   
            "properties": {                                                      
                ...                                                                 
            },
            "jobs": [
                { 
                    "deviceId": "myDeviceId",
                    "jobId": "myJobId",    
                    "jobType": "scheduleTwinUpdate",            
                    "status": "completed",                    
                    "startTimeUtc": "2016-09-29T18:18:52.7418462",
                    "endTimeUtc": "2016-09-29T18:20:52.7418462",
                    "createdDateTimeUtc": "2016-09-29T18:18:56.7787107Z",
                    "lastUpdatedDateTimeUtc": "2016-09-29T18:18:56.8894408Z",
                    "outcome": {
                        "deviceMethodResponse": null   
                    }                                         
                },
                ...
            ]                                                             
        }

Actualmente, esta colección es que se pueden consultar como **devices.jobs** en el lenguaje de consulta IoT concentrador.

Por ejemplo, para obtener todos los trabajos (anteriores y programados) que afectan a un único dispositivo, puede usar la siguiente consulta:

        SELECT * FROM devices.jobs
        WHERE devices.jobs.deviceId = 'myDeviceId'

Observe cómo esta consulta proporciona el estado específico del dispositivo (y posiblemente la respuesta del método directo) de cada tarea devuelto.
También es posible filtrar con condiciones booleanas arbitrarias en todas las propiedades de los objetos de la colección de **devices.jobs** .
Por ejemplo, la siguiente consulta:

        SELECT * FROM devices.jobs
        WHERE devices.jobs.deviceId = 'myDeviceId'
            AND devices.jobs.jobType = 'scheduleTwinUpdate'
            AND devices.jobs.status = 'completed'
            AND devices.jobs.createdTimeUtc > '2016-09-01'

Recupera todos los trabajos de actualización de doble completadas de dispositivo **myDeviceId** que se crearon después de septiembre de 2016.

También es posible recuperar los resultados por dispositivo de una sola tarea.

        SELECT * FROM devices.jobs
        WHERE devices.jobs.jobId = 'myJobId'

### <a name="limitations"></a>Limitaciones
Actualmente, no se admiten consultas en **devices.jobs** :

* Salientes, es decir, solo `SELECT *` es posible;
* Condiciones que hacen referencia a los dos dispositivos además de las propiedades de trabajo, como se muestra arriba;
* Realizar agregaciones, por ejemplo, contar y promedio, agrupar por.

## <a name="basics-of-an-iot-hub-query"></a>Conceptos básicos de una consulta de concentrador IoT

Cada consulta de concentrador IoT consta de una instrucción SELECT y cláusulas FROM y WHERE opcional y GROUP BY cláusulas. Cada consulta se ejecuta en una colección de documentos JSON, por ejemplo, gemelos de dispositivo. La cláusula FROM indica la colección de documentos que se repite en (**dispositivos** o **devices.jobs**). A continuación, se aplica el filtro en la cláusula WHERE. En el caso de agregaciones, los resultados de este paso se agrupan como se especifica en la cláusula GROUP BY y, para cada grupo, se genera una fila como se especifica en la cláusula SELECT.

        SELECT <select_list>
        FROM <from_specification>
        [WHERE <filter_condition>]
        [GROUP BY <group_specification>]

## <a name="from-clause"></a>Cláusula FROM

La cláusula **FROM < from_specification >** puede suponer sólo dos valores: **desde dispositivos**twins de dispositivo de consulta o **devices.jobs de**detalles de consulta trabajo por dispositivo.

## <a name="where-clause"></a>Cláusula WHERE

La **donde < filter_condition >** cláusula es opcional. Especifica las condiciones que los documentos JSON en la colección de deben cumplir para que se incluye como parte del resultado. Cualquier documento JSON debe evaluar las condiciones especificadas "true" para incluir en el resultado.

Las condiciones permitidas se describen en la sección [expresiones y condiciones][lnk-query-expressions].

## <a name="select-clause"></a>Cláusula SELECT

La cláusula SELECT (**Seleccionar < listaDeSelección >**) es obligatoria y especifica cuál será valores recuperados de la consulta. Especifica los valores JSON se usa para generar nuevos objetos JSON para cada elemento del subconjunto filtrado (y, opcionalmente, agrupado) de la colección de la fase de proyección genera un nuevo objeto JSON, la construcción con los valores especificados en la cláusula SELECT.

Esta es la gramática de la cláusula SELECT:

        SELECT [TOP <max number>] <projection list>

        <projection_list> ::=
            '*'
            | <projection_element> AS alias [, <projection_element> AS alias]+

        <projection_element> :==
            attribute_name
            | <projection_element> '.' attribute_name
            | <aggregate>

        <aggregate> :==
            count(<projection_element>) | count()
            | avg(<projection_element>) | avg()
            | sum(<projection_element>) | sum()
            | min(<projection_element>) | min()
            | max(<projection_element>) | max()

donde **nombre_de_atributo** hace referencia a cualquier propiedad del documento en la colección de JSON. Algunos ejemplos de las cláusulas SELECT pueden encontrarse en la [Introducción a las consultas de doble] [ lnk-query-getstarted] sección.

Actualmente, cláusulas de selección diferentes **seleccione \* ** sólo se admiten en consultas de agregado en twins.

## <a name="group-by-clause"></a>Cláusula GROUP BY

La cláusula **GROUP BY < group_specification >** es un paso opcional que se puede ejecutar después del filtro especificado en la cláusula WHERE y antes de la proyección especificada en la selección. Agrupa los documentos basados en el valor de un atributo. Estos grupos se utilizan para generar valores agregados como se especifica en la cláusula SELECT.

Un ejemplo de una consulta con GROUP BY es:

        SELECT properties.reported.telemetryConfig.status AS status,
            COUNT() AS numberOfDevices
        FROM devices
        GROUP BY properties.reported.telemetryConfig.status

La sintaxis formal GROUP BY es:

        GROUP BY <group_by_element>
        <group_by_element> :==
            attribute_name
            | < group_by_element > '.' attribute_name

donde **nombre_de_atributo** hace referencia a cualquier propiedad del documento en la colección de JSON. 

Actualmente, solo es compatible con la cláusula GROUP BY al consultar a twins.

## <a name="expressions-and-conditions"></a>Expresiones y condiciones

En un nivel alto, una *expresión*:

* Se evalúa como una instancia de un tipo JSON (es decir, booleano, número, cadena, matriz u objeto), y
* Definido por manipular los datos procedentes de los documentos JSON de dispositivo y las constantes usar funciones y operadores integrados.

*Las condiciones* son expresiones que dan como resultado un valor Boolean, es decir, cualquier constante diferente booleana **true** se considera como **false** (incluido **null**, **sin definir**, cualquier instancia de objeto o matriz, cualquier cadena y claramente el valor booleano **false**).

La sintaxis de expresiones es:

        <expression> ::=
            <constant> |
            attribute_name |
            unary_operator <expression> |
            <expression> binary_operator <expression> |
            <create_array_expression> |
            '(' <expression> ')'

        <constant> ::=
            <undefined_constant>
            | <null_constant> 
            | <number_constant> 
            | <string_constant> 
            | <array_constant> 

        <undefined_constant> ::= undefined
        <null_constant> ::= null
        <number_constant> ::= decimal_literal | hexadecimal_literal
        <string_constant> ::= string_literal
        <array_constant> ::= '[' <constant> [, <constant>]+ ']'

donde:

| Símbolo | Definición |
| -------------- | -----------------|
| nombre_de_atributo | Cualquier propiedad del documento en la colección de JSON. |
| UNARY_OPERATOR | Cualquier operador unario según la sección operadores. |
| binary_operator | Cualquier operador binario según la sección operadores. |
| decimal_literal | Un flotante expresado en notación decimal. |
| hexadecimal_literal | Un número expresado por la cadena '0 x' seguido de una cadena de dígitos hexadecimal. |
| literal de cadena | Literales de cadena son cadenas Unicode representadas por una secuencia de cero o más caracteres Unicode o secuencias de escape. Literales de cadena están entre comillas simples (apóstrofo: ') o comillas dobles (comillas: "). Permite caracteres de escape: `\'`, `\"`, `\\`, `\uXXXX` caracteres Unicode definida por 4 dígitos hexadecimal. |

### <a name="operators"></a>Operadores

Se admiten los siguientes operadores:

| Familia | Operadores |
| -------------- | -----------------|
| Aritmética | +,-,*,/,% |
| Lógica | Y, O NO |
| Comparación |  =,! =, <>,, < =, > = <> |

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre cómo ejecutar consultas en sus aplicaciones con [IoT concentrador SDK][lnk-hub-sdks].

[lnk-query-where]: iot-hub-devguide-query-language.md#where-clause
[lnk-query-expressions]: iot-hub-devguide-query-language.md#expressions-and-conditions
[lnk-query-getstarted]: iot-hub-devguide-query-language.md#getting-started-with-twin-queries

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
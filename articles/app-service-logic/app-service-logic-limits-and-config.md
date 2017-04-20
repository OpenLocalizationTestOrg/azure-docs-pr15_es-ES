<properties
    pageTitle="Límites de la aplicación de lógica y configuración | Microsoft Azure"
    description="Información general sobre los límites de servicio y los valores de configuración disponibles para las aplicaciones de lógica."
    services="logic-apps"
    documentationCenter=".net,nodejs,java"
    authors="jeffhollan"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="jehollan"/>

# <a name="logic-app-limits-and-configuration"></a>Límites de la aplicación de lógica y configuración

A continuación encontrará información sobre los límites actuales y detalles de configuración para las aplicaciones de lógica de Azure.

## <a name="limits"></a>Límites

### <a name="http-request-limits"></a>Límites de solicitud HTTP

Estos son los límites de una única llamada de solicitud o conector HTTP

#### <a name="timeout"></a>Tiempo de espera

|Nombre|Límite|Notas|
|----|----|----|
|Tiempo de espera de solicitud|1 minuto|Un [patrón asincrónico](app-service-logic-create-api-app.md) o [hasta que el bucle](app-service-logic-loops-and-scopes.md) puede compensa la según sea necesario|

#### <a name="message-size"></a>Tamaño del mensaje

|Nombre|Límite|Notas|
|----|----|----|
|Tamaño del mensaje|50 MB|Algunos conectores y las API no admiten 50MB.  Solicitud de desencadenador admite hasta 25MB|
|Límite de evaluación de expresión|131.072 caracteres|`@concat()`, `@base64()`, `string` no puede ser más larga|

#### <a name="retry-policy"></a>Vuelva a intentar la directiva

|Nombre|Límite|Notas|
|----|----|----|
|Reintentos|4|Puede configurar con la [Reintentar parámetro de directiva](https://msdn.microsoft.com/en-us/library/azure/mt643939.aspx)|
|Retraso máximo de reintento|1 hora|Puede configurar con la [Reintentar parámetro de directiva](https://msdn.microsoft.com/en-us/library/azure/mt643939.aspx)|
|Min retraso de reintento|20 min|Puede configurar con la [Reintentar parámetro de directiva](https://msdn.microsoft.com/en-us/library/azure/mt643939.aspx)|

### <a name="run-duration-and-retention"></a>Retención y la duración de ejecución

Estos son los límites de una aplicación de lógica única ejecutar.

|Nombre|Límite|Notas|
|----|----|----|
|Duración de la ejecución|90 días||
|Retención de almacenamiento|90 días|Se trata de la hora de inicio de ejecución|
|Intervalo de periodicidad min|15 s||
|Intervalo de periodicidad Max|500 días||


### <a name="looping-and-debatching-limits"></a>Bucle y debatching límites

Estos son los límites de una aplicación de lógica única ejecutar.

|Nombre|Límite|Notas|
|----|----|----|
|Elementos de ForEach|5.000|Puede usar la [acción de consulta](../connectors/connectors-native-query.md) para filtrar matrices más grandes según sea necesario|
|Hasta iteraciones|10.000||
|Elementos de SplitOn|5.000||
|Paralelismo ForEach|20|Puede configurar para foreach secuencial agregando `"operationOptions": "Sequential"` a la `foreach` acción|


### <a name="throughput-limits"></a>Límites de rendimiento

Estos son los límites de una instancia de aplicación única lógica. 

|Nombre|Límite|Notas|
|----|----|----|
|Desencadenadores por segundo|100|Puede distribuir los flujos de trabajo entre varias aplicaciones según sea necesario|

### <a name="definition-limits"></a>Límites de definición

Estos son los límites de una definición de aplicación única lógica.

|Nombre|Límite|Notas|
|----|----|----|
|Acciones en ForEach|1|Puede agregar flujos de trabajo anidadas para ampliar esta según sea necesario|
|Acciones de flujo de trabajo|60|Puede agregar flujos de trabajo anidadas para ampliar esta según sea necesario|
|Permite niveles de anidamiento de acción|5|Puede agregar flujos de trabajo anidadas para ampliar esta según sea necesario|
|Flujos por región por suscripción|1000||
|Desencadenadores por el flujo de trabajo|10||
|Número máximo de caracteres por expresión|8.192||
|Max `trackedProperties` tamaño de caracteres|16.000|
|`action`/`trigger`límite de nombre|80||
|`description`límite de longitud|256||
|`parameters`límite|50||
|`outputs`límite|10||

## <a name="configuration"></a>Configuración

### <a name="ip-address"></a>Dirección IP

Llamadas realizadas desde un [conector](../connectors/apis-list.md) proceden de la dirección IP especificados a continuación.

Llamadas realizadas desde una aplicación de lógica directamente (es decir, a través de [HTTP](../connectors/connectors-native-http.md) o [HTTP + Swagger](../connectors/connectors-native-http-swagger.md)) pueden proceder de cualquiera de los [Intervalos de IP del centro de datos de Azure](https://www.microsoft.com/en-us/download/details.aspx?id=41653).

|Región de la aplicación de lógica|IP saliente|
|-----|----|
|Australia Oriental|40.126.251.213|
|Australia sureste|40.127.80.34|
|Sur de Brasil|191.232.38.129|
|India central|104.211.98.164|
|Central de EE.|40.122.49.51|
|Asia oriental|23.99.116.181|
|Estados Unidos oriental|191.237.41.52|
|Estados Unidos oriental 2|104.208.233.100|
|Japón oriental|40.115.186.96|
|Japón oeste|40.74.130.77|
|Norte Central de EE.|65.52.218.230|
|Europa del Norte|104.45.93.9|
|Sur Central de EE.|104.214.70.191|
|Sudeste asiático|13.76.231.68|
|India sur|104.211.227.225|
|Europa occidental|40.115.50.13|
|India occidental|104.211.161.203|
|Estados Unidos occidental|104.40.51.248|


## <a name="next-steps"></a>Pasos siguientes  

- Para empezar con la lógica de aplicaciones, siga el tutorial de [crear una aplicación de lógica](app-service-logic-create-a-logic-app.md) .  
- [Ver ejemplos y escenarios comunes](app-service-logic-examples-and-scenarios.md)
- [Puede automatizar procesos empresariales con aplicaciones de lógica](http://channel9.msdn.com/Events/Build/2016/T694) 
- [Obtenga información sobre cómo integrar sus sistemas con aplicaciones de lógica](http://channel9.msdn.com/Events/Build/2016/P462)
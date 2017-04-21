<properties
    pageTitle="DocumentDB Node.js API y SDK | Microsoft Azure"
    description="Infórmese acerca de la API Node.js y el SDK, incluidas las fechas de publicación, fechas de jubilación y los cambios realizados entre cada versión del SDK Node.js DocumentDB."
    services="documentdb"
    documentationCenter="nodejs"
    authors="rnagpal"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="rnagpal"/>

# <a name="documentdb-apis-and-sdks"></a>DocumentDB API y SDK

> [AZURE.SELECTOR]
- [.NET](documentdb-sdk-dotnet.md)
- [Node.js](documentdb-sdk-node.md)
- [Java](documentdb-sdk-java.md)
- [Python](documentdb-sdk-python.md)
- [REST](https://go.microsoft.com/fwlink/?LinkId=402413)
- [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)

##<a name="documentdb-nodejs-api-and-sdk"></a>SDK y API de DocumentDB Node.js

<table>
<tr><td>**Descargar el SDK**</td><td>[NPM](https://www.npmjs.com/package/documentdb)</td></tr>
<tr><td>**Documentación de la API**</td><td>[Documentación de referencia de la API de Node.js](http://azure.github.io/azure-documentdb-node/DocumentClient.html)</td></tr>
<tr><td>**Instrucciones de instalación de SDK**</td><td>[Instrucciones de instalación](http://azure.github.io/azure-documentdb-node/)</td></tr>
<tr><td>**Contribuir a SDK**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-node/tree/master/source)</td></tr>
<tr><td>**Ejemplos**</td><td>[Ejemplos de código Node.js](documentdb-nodejs-samples.md)</td></tr>
<tr><td>**Tutorial de introducción de Get**</td><td>[Empezar a trabajar con el SDK de Node.js](documentdb-nodejs-get-started.md)</td></tr>
<tr><td>**Tutorial de aplicación Web**</td><td>[Crear una aplicación web de Node.js con DocumentDB](documentdb-nodejs-application.md)</td></tr>
<tr><td>**Plataformas compatibles actual**</td><td>[Node.js v0.10](https://nodejs.org/en/blog/release/v0.10.0/)<br/>[Node.js v0.12](https://nodejs.org/en/blog/release/v0.12.0/)<br/>[Node.js v4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)</td></tr>
</table></br>

##<a name="release-notes"></a>Notas de la versión

###<a name="1.10.0"/>1.10.0</a>

- Compatibilidad agregada con cruzada partición las consultas en paralelo.
- Compatibilidad agregada con consultas superior/ORDER BY para colecciones divididas.

###<a name="1.9.0"/>1.9.0</a>

- Reintentar agregado compatibilidad de la directiva solicitudes limitadas. (Limitadas solicitudes reciban una excepción de demasiado grande de tasa solicitud, código de error 429). De forma predeterminada, DocumentDB reintentos nueve horas para cada solicitud cuando se encuentra el código de error 429, teniendo en cuenta el tiempo de retryAfter en el encabezado de respuesta. Un intervalo de tiempo de reintento fijo ahora pueden configurarse como parte de la propiedad RetryOptions en el objeto ConnectionPolicy si desea omitir la hora de retryAfter devuelta por servidor entre los reintentos. DocumentDB ahora espera un máximo de 30 segundos para cada solicitud que se está limitando (independientemente del número de reintentos) y devuelve la respuesta con código de error 429. En este momento también se puede reemplazar en la propiedad RetryOptions ConnectionPolicy objeto.

- DocumentDB devuelve ahora x ms acelerador intentos contar y x-ms-throttle-retry-wait-time-ms como los encabezados de respuesta en cada solicitud para indicar el límite de reintento contar y la hora de cummulative la solicitud esperada entre los reintentos.

- Se ha agregado la clase RetryOptions, exposición de la propiedad RetryOptions en la clase ConnectionPolicy que se pueden usar para invalidar algunas de las opciones predeterminadas de reintento.

###<a name="1.8.0"/>1.8.0</a>

 - Agrega la compatibilidad con las cuentas de base de datos de múltiples región.

###<a name="1.7.0"/>1.7.0</a>

- Agrega la compatibilidad para la característica de tiempo para vida (TTL) para los documentos.

###<a name="1.6.0"/>1.6.0</a>

- Implementado [colecciones divididas](documentdb-partition-data.md) y [niveles de rendimiento definidas por el usuario](documentdb-performance-levels.md).

###<a name="1.5.6"/>1.5.6</a>

- Error de RangePartitionResolver.resolveForRead corregido donde no se devuelve vínculos debido a una concatenación de resultados incorrecto.

###<a name="1.5.5"/>1.5.5</a>

- Fijo hashParitionResolver resolveForRead(): cuando ninguna clave de partición proporcionada era iniciar excepción, en lugar de una lista de todos los vínculos registrados.

###<a name="1.5.4"/>1.5.4</a>

- Correcciones de problemas de [100 #](https://github.com/Azure/azure-documentdb-node/issues/100) - dedicado agente de HTTPS: evitar modificar el agente global para fines de DocumentDB. Utilizar a un agente dedicado para todas las solicitudes de biblioteca.

###<a name="1.5.3"/>1.5.3</a>

- Las soluciones de asuntos [#81](https://github.com/Azure/azure-documentdb-node/issues/81) - correctamente el controlador de guiones en los identificadores de medios.

###<a name="1.5.2"/>1.5.2</a>

- Soluciones de asuntos [95 #](https://github.com/Azure/azure-documentdb-node/issues/95) - EventEmitter escucha perder advertencia.

###<a name="1.5.1"/>1.5.1</a>

- Las soluciones de asuntos [92 #](https://github.com/Azure/azure-documentdb-node/issues/90) - cambiar nombre de carpeta Hash hash para sistemas entre mayúsculas y minúsculas.

### <a name="1.5.0"/>1.5.0</a>

- Soporte de sharding de implementación agregando hash & rango resoluciones de partición.

### <a name="1.4.0"/>1.4.0</a>

- Implementar Upsert. Nuevos métodos de upsertXXX en documentClient.

### <a name="1.3.0"/>1.3.0</a>

- Omitido para acercar los números de versión de alineación con otros SDK.

### <a name="1.2.2"/>1.2.2</a>

- Preguntas de división va contenedor al repositorio nuevo.
- Actualizar archivo de paquete para npm registro.

### <a name="1.2.1"/>1.2.1</a>

- Implementa identificador enrutamiento.
- Corrige el problema [Nº 49](https://github.com/Azure/azure-documentdb-node/issues/49) - actual propiedad entra en conflicto con el método current().

### <a name="1.2.0"/>1.2.0</a>

- Compatibilidad agregada con geoespaciales índice.
- Valide la propiedad id para todos los recursos. No pueden contener los identificadores de recursos?, /, # & #47; & #47; caracteres o terminar con un espacio.
- Agrega el nuevo encabezado "índice transformación progreso" a ResourceResponse.

### <a name="1.1.0"/>1.1.0</a>

- Implementa la directiva de indización de V2.

### <a name="1.0.3"/>1.0.3</a>

- Problema [Nº 40] (https://github.com/Azure/azure-documentdb-node/issues/40) - implementado eslint gustan las configuraciones y en los principales y promesa SDK.

### <a name="1.0.2"/>1.0.2</a>

- Problema [Nº 45](https://github.com/Azure/azure-documentdb-node/issues/45) - contenedor promesa no incluye encabezado con error.

### <a name="1.0.1"/>1.0.1</a>

- Capacidad de implementada para consultar los conflictos agregando readConflicts, readConflictAsync y queryConflicts.
- Documentación de API actualizada.
- Problema [Nº 41](https://github.com/Azure/azure-documentdb-node/issues/41) - client.createDocumentAsync error.

### <a name="1.0.0"/>1.0.0</a>

- GA SDK.

## <a name="release--retirement-dates"></a>Versión & fechas de retirada
Microsoft le proporcionará notificación al menos **12 meses** antes de retirar un SDK para equilibrar la transición a una versión más reciente o compatibles.

Nuevas características y funcionalidades y las optimizaciones solo se agregan en el SDK de actual, como por ejemplo, es recomendable que siempre actualice a la versión más reciente SDK tan pronto como sea posible.

Cualquier solicitud de DocumentDB mediante un SDK retirado será rechazado por el servicio.

> [AZURE.WARNING]
Todas las versiones de Azure DocumentDB SDK para Node.js antes de la versión **1.0.0** se retirará en **29 de febrero de 2016**.

<br/>

| Versión | Fecha de lanzamiento | Fecha de retirada
| ---     | ---          | ---
| [1.10.0](#1.10.0) | 03 de octubre de 2016 |---
| [1.9.0](#1.9.0) | 07 de julio de 2016 |---
| [1.8.0](#1.8.0) | 14 de junio de 2016 |---
| [1.7.0](#1.7.0) | 26 de abril de 2016 |---
| [1.6.0](#1.6.0) | 29 de marzo de 2016 |---
| [1.5.6](#1.5.6) | 08 de marzo de 2016 |---
| [1.5.5](#1.5.5) | 02 de febrero de 2016 |---
| [1.5.4](#1.5.4) | 01 de febrero de 2016 |---
| [1.5.2](#1.5.2) | 26 de enero de 2016 |---
| [1.5.2](#1.5.2) | 22 de enero de 2016 |---
| [1.5.1](#1.5.1) | 4 de enero de 2016 |---
| [1.5.0](#1.5.0) | 31 de diciembre de 2015 |---
| [1.4.0](#1.4.0) | 06 de octubre de 2015 |---
| [1.3.0](#1.3.0) | 06 de octubre de 2015 |---
| [1.2.2](#1.2.2) | 10 de septiembre de 2015 |---
| [1.2.1](#1.2.1) | 15 de agosto de 2015 |---
| [1.2.0](#1.2.0) | 05 de agosto de 2015 |---
| [1.1.0](#1.1.0) | 09 de julio de 2015 |---
| [1.0.3](#1.0.3) | 04 de junio de 2015 |---
| [1.0.2](#1.0.2) | 23 de mayo de 2015 |---
| [1.0.1](#1.0.1) | 15 de mayo de 2015 |---
| [1.0.0](#1.0.0) | 08 de abril de 2015 |---
| 0.9.4-Prerelease | 06 de abril de 2015 | 29 de febrero de 2016
| 0.9.3-Prerelease | 14 de enero de 2015 | 29 de febrero de 2016
| 0.9.2-Prerelease | 18 de diciembre de 2014 | 29 de febrero de 2016
| 0.9.1-Prerelease | 22 de agosto de 2014 | 29 de febrero de 2016
| 0.9.0-Prerelease | 21 de agosto de 2014 | 29 de febrero de 2016


## <a name="faq"></a>Preguntas más frecuentes
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## <a name="see-also"></a>Vea también

Para obtener más información sobre DocumentDB, consulte la página de servicio de [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) .

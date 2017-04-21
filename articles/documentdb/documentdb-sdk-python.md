<properties 
    pageTitle="API de Python DocumentDB & SDK | Microsoft Azure" 
    description="Obtenga información acerca de la API de Python y SDK, incluidas las fechas de lanzamiento, fechas de jubilación y los cambios realizados entre cada versión del SDK de Python DocumentDB." 
    services="documentdb" 
    documentationCenter="python" 
    authors="rnagpal" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="python" 
    ms.topic="article" 
    ms.date="09/29/2016" 
    ms.author="rnagpal"/>

# <a name="documentdb-apis-and-sdks"></a>DocumentDB API y SDK

> [AZURE.SELECTOR]
- [.NET](documentdb-sdk-dotnet.md)
- [Node.js](documentdb-sdk-node.md)
- [Java](documentdb-sdk-java.md)
- [Python](documentdb-sdk-python.md)
- [REST](https://go.microsoft.com/fwlink/?LinkId=402413)
- [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)

## <a name="documentdb-python-api-and-sdk"></a>SDK y API de Python DocumentDB

<table>
<tr><td>**Descargar el SDK**</td><td>[PyPI](https://pypi.python.org/pypi/pydocumentdb)</td></tr>
<tr><td>**Documentación de la API**</td><td>[Documentación de referencia de la API de Python](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.html)</td></tr>
<tr><td>**Instrucciones de instalación de SDK**</td><td>[Instrucciones de instalación de SDK Python](http://azure.github.io/azure-documentdb-python/)</td></tr>
<tr><td>**Contribuir a SDK**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-python)</td></tr>
<tr><td>**Introducción**</td><td>[Empezar a trabajar con el SDK de Python](documentdb-python-application.md)</td></tr>
<tr><td>**Plataformas compatibles actual**</td><td>[Python 2.7](https://www.python.org/downloads/) y [Python 3.5](https://www.python.org/downloads/)</td></tr>
</table></br>

## <a name="release-notes"></a>Notas de la versión

### <a name="a-name200200httpspypipythonorgpypipydocumentdb200"></a><a name="2.0.0"/>[2.0.0](https://pypi.python.org/pypi/pydocumentdb/2.0.0)
- Compatibilidad agregada con Python 3.5.
- Compatibilidad agregada con mediante un módulo de solicitudes de agrupación de conexiones.
- Compatibilidad agregada con coherencia de sesión.
- Compatibilidad agregada con consultas de arriba/ORDERBY para colecciones divididas.


### <a name="a-name190190httpspypipythonorgpypipydocumentdb190"></a><a name="1.9.0"/>[1.9.0](https://pypi.python.org/pypi/pydocumentdb/1.9.0)
- Reintentar agregado compatibilidad de la directiva solicitudes limitadas. (Limitadas solicitudes reciban una excepción de demasiado grande de tasa solicitud, código de error 429). De forma predeterminada, DocumentDB reintentos nueve horas para cada solicitud cuando se encuentra el código de error 429, teniendo en cuenta el tiempo de retryAfter en el encabezado de respuesta. Un intervalo de tiempo de reintento fijo ahora pueden configurarse como parte de la propiedad RetryOptions en el objeto ConnectionPolicy si desea omitir la hora de retryAfter devuelta por servidor entre los reintentos. DocumentDB ahora espera un máximo de 30 segundos para cada solicitud que se está limitando (independientemente del número de reintentos) y devuelve la respuesta con código de error 429. En este momento también se puede reemplazar en la propiedad RetryOptions ConnectionPolicy objeto.

- DocumentDB devuelve ahora x ms acelerador intentos contar y x-ms-throttle-retry-wait-time-ms como los encabezados de respuesta en cada solicitud para indicar el límite de reintento contar y la hora de cummulative la solicitud esperada entre los reintentos.

- Elimina la clase RetryPolicy y la propiedad correspondiente (retry_policy) que se exponen en la clase document_client y en su lugar presentó una clase RetryOptions exposición de la propiedad RetryOptions en la clase ConnectionPolicy que se pueden usar para invalidar algunas de las opciones predeterminadas de reintento.

### <a name="a-name180180httpspypipythonorgpypipydocumentdb180"></a><a name="1.8.0"/>[1.8.0](https://pypi.python.org/pypi/pydocumentdb/1.8.0)
  - Agrega la compatibilidad con las cuentas de base de datos de múltiples región.

### <a name="a-name170170httpspypipythonorgpypipydocumentdb170"></a><a name="1.7.0"/>[1.7.0](https://pypi.python.org/pypi/pydocumentdb/1.7.0)
- Agrega la compatibilidad para la característica de tiempo para vida (TTL) para los documentos.

### <a name="a-name161161httpspypipythonorgpypipydocumentdb161"></a><a name="1.6.1"/>[1.6.1](https://pypi.python.org/pypi/pydocumentdb/1.6.1)
- Correcciones de errores relacionados con partición del lado servidor para permitir que los caracteres especiales en la ruta de acceso de partitionkey.

### <a name="a-name160160httpspypipythonorgpypipydocumentdb160"></a><a name="1.6.0"/>[1.6.0](https://pypi.python.org/pypi/pydocumentdb/1.6.0)
- Implementado [colecciones divididas](documentdb-partition-data.md) y [niveles de rendimiento definidas por el usuario](documentdb-performance-levels.md). 

### <a name="a-name150150httpspypipythonorgpypipydocumentdb150"></a><a name="1.5.0"/>[1.5.0](https://pypi.python.org/pypi/pydocumentdb/1.5.0)
- Agregar Hash & rango partición resoluciones para ayudar con las aplicaciones de sharding entre varias particiones.

### <a name="a-name142142httpspypipythonorgpypipydocumentdb142"></a><a name="1.4.2"/>[1.4.2](https://pypi.python.org/pypi/pydocumentdb/1.4.2)
- Implementar Upsert. Nuevos métodos UpsertXXX agregados a admite la característica Upsert.
- Implementar el enrutamiento según el ID. Sin cambios en la API públicas, todos los cambios internos.

### <a name="a-name120120httpspypipythonorgpypipydocumentdb120"></a><a name="1.2.0"/>[1.2.0](https://pypi.python.org/pypi/pydocumentdb/1.2.0)
- Índice de geoespaciales admite.
- Valide la propiedad id para todos los recursos. Identificadores de recursos no pueden contener ?, /, #, \, caracteres o terminar con un espacio.
- Agrega el nuevo encabezado "índice transformación progreso" a ResourceResponse.

### <a name="a-name110110httpspypipythonorgpypipydocumentdb110"></a><a name="1.1.0"/>[1.1.0](https://pypi.python.org/pypi/pydocumentdb/1.1.0)
- Implementa la directiva de indización de V2.

### <a name="a-name101101httpspypipythonorgpypipydocumentdb101"></a><a name="1.0.1"/>[1.0.1](https://pypi.python.org/pypi/pydocumentdb/1.0.1)
- Es compatible con la conexión de servidor proxy.

### <a name="a-name100100httpspypipythonorgpypipydocumentdb100"></a><a name="1.0.0"/>[1.0.0](https://pypi.python.org/pypi/pydocumentdb/1.0.0)
- GA SDK.

## <a name="release--retirement-dates"></a>Fechas de lanzamiento y retirada
Microsoft le proporcionará notificación al menos **12 meses** antes de retirar un SDK para equilibrar la transición a una versión más reciente o compatibles.

Nuevas características y funcionalidades y las optimizaciones solo se agregan en el SDK de actual, como por ejemplo, es recomendable que siempre actualice a la versión más reciente SDK tan pronto como sea posible. 

Cualquier solicitud de DocumentDB mediante un SDK retirado será rechazado por el servicio.

> [AZURE.WARNING]
Todas las versiones de Azure DocumentDB SDK para Python antes de la versión **1.0.0** se retirará en **29 de febrero de 2016**. 

<br/>

| Versión | Fecha de lanzamiento | Fecha de retirada 
| ---     | ---          | ---
| [2.0.0](#2.0.0) | 29 de septiembre de 2016 |---
| [1.9.0](#1.9.0) | 07 de julio de 2016 |---
| [1.8.0](#1.8.0) | 14 de junio de 2016 |---
| [1.7.0](#1.7.0) | 26 de abril de 2016 |---
| [1.6.1](#1.6.1) | 08 de abril de 2016 |---
| [1.6.0](#1.6.0) | 29 de marzo de 2016 |---
| [1.5.0](#1.5.0) | 03 de enero de 2016 |---
| [1.4.2](#1.4.2) | 06 de octubre de 2015 |---
| [1.4.1](#1.4.1) | 06 de octubre de 2015 |---
| [1.2.0](#1.2.0) | 06 de agosto de 2015 |---
| [1.1.0](#1.1.0) | 09 de julio de 2015 |---
| [1.0.1](#1.0.1) | 25 de mayo de 2015 |---
| [1.0.0](#1.0.0) | 07 de abril de 2015 |---
| 0.9.4-prelease | 14 de enero de 2015 | 29 de febrero de 2016
| 0.9.3-prelease | 09 de diciembre de 2014 | 29 de febrero de 2016
| 0.9.2-prelease | 25 de noviembre de 2014 | 29 de febrero de 2016
| 0.9.1-prelease | 23 de septiembre de 2014 | 29 de febrero de 2016
| 0.9.0-prelease | 21 de agosto de 2014 | 29 de febrero de 2016

## <a name="faq"></a>Preguntas más frecuentes
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## <a name="see-also"></a>Vea también

Para obtener más información sobre DocumentDB, consulte la página de servicio de [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) . 

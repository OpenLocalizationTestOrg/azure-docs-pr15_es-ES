
<properties
    pageTitle="API de DocumentDB Java & SDK | Microsoft Azure"
    description="Obtenga información acerca de la API de Java y SDK, incluidas las fechas de publicación, fechas de jubilación y los cambios realizados entre cada versión del SDK de Java DocumentDB."
    services="documentdb"
    documentationCenter="java"
    authors="rnagpal"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="java"
    ms.topic="article"
    ms.date="10/28/2016"
    ms.author="rnagpal"/>

# <a name="documentdb-apis-and-sdks"></a>DocumentDB API y SDK

> [AZURE.SELECTOR]
- [.NET](documentdb-sdk-dotnet.md)
- [Node.js](documentdb-sdk-node.md)
- [Java](documentdb-sdk-java.md)
- [Python](documentdb-sdk-python.md)
- [REST](https://go.microsoft.com/fwlink/?LinkId=402413)
- [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)

## <a name="documentdb-java-api-and-sdk"></a>SDK y API de DocumentDB Java

<table>
<tr><td>**Descarga de SDK**</td><td>[Experto](http://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.microsoft.azure%22%20AND%20a%3A%22azure-documentdb%22)</td></tr>
<tr><td>**Documentación de la API**</td><td>[Documentación de referencia de la API de Java](http://azure.github.io/azure-documentdb-java/)</td></tr>
<tr><td>**Contribuir a SDK**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-java/)</td></tr>
<tr><td>**Introducción**</td><td>[Empezar a trabajar con el SDK de Java](documentdb-java-application.md)</td></tr>
<tr><td>**Tiempo de ejecución compatible actual**</td><td>[JDK 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)</td></tr>
</table></br>

## <a name="release-notes"></a>Notas de la versión

### <a name="a-name191191httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb191"></a><a name="1.9.1"/>[1.9.1](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.9.1)

  - Ha agregado compatibilidad para nivel de coherencia de BoundedStaleness.
  - Compatibilidad agregada con conectividad directa para las operaciones CRUD para colecciones divididas.
  - Corregir un error en la consulta de una base de datos con SQL.
  - Corregir un error en la caché de sesión donde token de sesión puede estar configurado incorrectamente.

### <a name="a-name190190httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb190"></a><a name="1.9.0"/>[1.9.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.9.0)

  - Compatibilidad agregada con cruzada partición las consultas en paralelo.
  - Compatibilidad agregada con consultas superior/ORDER BY para colecciones divididas.
  - Compatibilidad agregada con coherencia seguro.
  - Compatibilidad agregada con nombre según las solicitudes cuando se utiliza una conexión directa.
  - Fijo realizar ActivityId mantenerse coherente en todos los reintentos de solicitud.
  - Corregir un error relacionado con la caché de sesión al volver a crear una colección con el mismo nombre.
  - Agregado polígono y LineString DataTypes especificando la colección de directiva de barrera geo espaciales consultas de indización.
  - Problemas corregidos con Java Doc para Java 1,8.

### <a name="a-name181181httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb181"></a><a name="1.8.1"/>[1.8.1](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.8.1)
  - Corregir un error en PartitionKeyDefinitionMap colecciones de una única partición de caché y no realice capturar adicional dividir las solicitudes clave.
  - Corregir un error no Reintentar cuando se proporciona un valor de clave de partición incorrecto.

### <a name="a-name180180httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb180"></a><a name="1.8.0"/>[1.8.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.8.0)
  - Agrega la compatibilidad con las cuentas de base de datos de múltiples región.
  - Compatibilidad agregada con reintento automático en solicitudes limitadas con opciones para personalizar el número máximo de reintentos y el tiempo de espera máximo de reintentos.  Consulte RetryOptions y ConnectionPolicy.getRetryOptions().
  - Desusados IPartitionResolver según código particiones personalizado. Utilice colecciones divididas de almacenamiento y rendimiento más altos.

### <a name="a-name171171httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb171"></a><a name="1.7.1"/>[1.7.1](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.7.1)
- Reintentar agregado compatibilidad de la directiva limitación.  

### <a name="a-name170170httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb170"></a><a name="1.7.0"/>[1.7.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.7.0)
- Tiempo agregado a soporte de live (TTL) para los documentos.

### <a name="a-name160160httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb160"></a><a name="1.6.0"/>[1.6.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.6.0)
- Implementado [colecciones divididas](documentdb-partition-data.md) y [niveles de rendimiento definidas por el usuario](documentdb-performance-levels.md).

### <a name="a-name151151httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb151"></a><a name="1.5.1"/>[1.5.1](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.5.1)
- Corregir un error en HashPartitionResolver para generar los valores de hash en littleEndian para que sea coherente con otros SDK.

### <a name="a-name150150httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb150"></a><a name="1.5.0"/>[1.5.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.5.0)
- Agregar Hash & rango partición resoluciones para ayudar con las aplicaciones de sharding entre varias particiones.

### <a name="a-name140140httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb140"></a><a name="1.4.0"/>[1.4.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.4.0)
- Implementar Upsert. Nuevos métodos upsertXXX agregados a admite la característica Upsert.
- Implementar el enrutamiento según el ID. Sin cambios en la API públicas, todos los cambios internos.

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0
- Versión omitido para que aparezca el número de versión de alineación con otros SDK

### <a name="a-name120120httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb120"></a><a name="1.2.0"/>[1.2.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.2.0)
- Es compatible con geoespaciales índice
- Valide la propiedad id para todos los recursos. Identificadores de recursos no pueden contener ?, /, #, \, caracteres o terminar con un espacio.
- Agrega el nuevo encabezado "índice transformación progreso" a ResourceResponse.

### <a name="a-name110110httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb110"></a><a name="1.1.0"/>[1.1.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.1.0)
- Implementa la directiva de indización de V2

### <a name="a-name100100httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb100"></a><a name="1.0.0"/>[1.0.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.0.0)
- SDK GA

## <a name="release--retirement-dates"></a>Versión & fechas de retirada
Microsoft le proporcionará notificación al menos **12 meses** antes de retirar un SDK para equilibrar la transición a una versión más reciente o compatibles.

Nuevas características y funcionalidades y las optimizaciones solo se agregan en el SDK de actual, como por ejemplo, es recomendable que siempre actualice a la versión más reciente SDK tan pronto como sea posible.

Cualquier solicitud de DocumentDB mediante un SDK retirado será rechazado por el servicio.

> [AZURE.WARNING]
Todas las versiones de Azure DocumentDB SDK para Java antes de la versión **1.0.0** se retirará en **29 de febrero de 2016**.

<br/>

| Versión | Fecha de lanzamiento | Fecha de retirada
| ---     | ---          | ---
| [1.9.1](#1.9.1) | 28 de octubre de 2016 |---
| [1.9.0](#1.9.0) | 03 de octubre de 2016 |---
| [1.8.1](#1.8.1) | 30 de junio de 2016 |---
| [1.8.0](#1.8.0) | 14 de junio de 2016 |---
| [1.7.1](#1.7.1) | 30 de abril de 2016 |---
| [1.7.0](#1.7.0) | 27 de abril de 2016 |---
| [1.6.0](#1.6.0) | 29 de marzo de 2016 |---
| [1.5.1](#1.5.1) | 31 de diciembre de 2015 |---
| [1.5.0](#1.5.0) | 04 de diciembre de 2015 |---
| [1.4.0](#1.4.0) | 05 de octubre de 2015 |---
| [1.3.0](#1.3.0) | 05 de octubre de 2015 |---
| [1.2.0](#1.2.0) | 05 de agosto de 2015 |---
| [1.1.0](#1.1.0) | 09 de julio de 2015 |---
| [1.0.1](#1.0.1) | 12 de mayo de 2015 |---
| [1.0.0](#1.0.0) | 07 de abril de 2015 |---
| 0.9.5-prelease | 09 de marzo de 2015 | 29 de febrero de 2016
| 0.9.4-prelease | 17 de febrero de 2015 | 29 de febrero de 2016
| 0.9.3-prelease | 13 de enero de 2015 | 29 de febrero de 2016
| 0.9.2-prelease | 19 de diciembre de 2014 | 29 de febrero de 2016
| 0.9.1-prelease | 19 de diciembre de 2014 | 29 de febrero de 2016
| 0.9.0-prelease | 10 de diciembre de 2014 | 29 de febrero de 2016

## <a name="faq"></a>Preguntas más frecuentes
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## <a name="see-also"></a>Vea también

Para obtener más información sobre DocumentDB, consulte la página de servicio de [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) .

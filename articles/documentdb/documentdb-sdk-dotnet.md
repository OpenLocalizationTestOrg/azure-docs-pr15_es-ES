<properties 
    pageTitle="API de .NET DocumentDB & SDK | Microsoft Azure" 
    description="Obtenga información acerca de la API de .NET y el SDK, incluidas las fechas de lanzamiento, fechas de jubilación y los cambios realizados entre cada versión del SDK de .NET DocumentDB." 
    services="documentdb" 
    documentationCenter=".net" 
    authors="rnagpal" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="10/27/2016" 
    ms.author="rnagpal"/>

# <a name="documentdb-apis-and-sdks"></a>DocumentDB API y SDK 

> [AZURE.SELECTOR]
- [.NET](documentdb-sdk-dotnet.md)
- [Node.js](documentdb-sdk-node.md)
- [Java](documentdb-sdk-java.md)
- [Python](documentdb-sdk-python.md)
- [REST](https://go.microsoft.com/fwlink/?LinkId=402413)
- [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)

## <a name="documentdb-net-api-and-sdk"></a>SDK y API de .NET DocumentDB

<table>
<tr><td>**Descarga de SDK**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)</td></tr>
<tr><td>**Documentación de la API**</td><td>[Documentación de referencia de la API de .NET](https://msdn.microsoft.com/library/azure/dn948556.aspx)</td></tr>
<tr><td>**Ejemplos**</td><td>[Ejemplos de código .NET](documentdb-dotnet-samples.md)</td></tr>
<tr><td>**Introducción**</td><td>[Empezar a trabajar con el SDK de .NET DocumentDB](documentdb-get-started.md)</td></tr>
<tr><td>**Tutorial de aplicación Web**</td><td>[Desarrollo de aplicaciones Web con DocumentDB](documentdb-dotnet-application.md)</td></tr>
<tr><td>**Marco actual de admitidos**</td><td>[Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</td></tr>
</table></br>

## <a name="release-notes"></a>Notas de la versión

> [AZURE.IMPORTANT] A partir de la versión 1.9.2, puede recibir System.NotSupportedException al consultar colecciones divididas. Para evitar este error, asegúrese de que el proceso host es 64 bits. Para los proyectos ejecutables, esto puede hacerse desactivando la opción "Prefiere 32 bits" en la ventana de propiedades del proyecto, en la pestaña de compilación.

### <a name="a-name11001100httpswwwnugetorgpackagesmicrosoftazuredocumentdb1100"></a><a name="1.10.0"/>[1.10.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.10.0)

  - Conectividad directa agregado compatibilidad con colecciones divididas.
  - Mejorar el rendimiento para el nivel de coherencia antigüedad limitada.
  - Agregado polígono y LineString DataTypes especificando la colección de directiva de barrera geo espaciales consultas de indización.
  - Compatibilidad agregada LINQ con StringEnumConverter, IsoDateTimeConverter y UnixDateTimeConverter al traducir predicados.
  - Varios SDK correcciones de errores.

### <a name="a-name195195httpswwwnugetorgpackagesmicrosoftazuredocumentdb195"></a><a name="1.9.5"/>[1.9.5](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.5)

  - Fija un problema que provoca el siguiente NotFoundException: la sesión de lectura no está disponible para el símbolo de sesión de entrada. Esta excepción en algunos casos cuando la consulta de la región de lectura de una cuenta distribuido geo.
  - Exponer la propiedad ResponseStream en la clase ResourceResponse, que permite el acceso directo a la secuencia subyacente de una respuesta.

### <a name="a-name194194httpswwwnugetorgpackagesmicrosoftazuredocumentdb194"></a><a name="1.9.4"/>[1.9.4](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.4)

  - Modificar las clases ResourceResponse, FeedResponse, StoredProcedureResponse y MediaResponse para implementar la interfaz pública correspondiente para que puede simuladas de prueba controlados por implementación (TDD).
  - Corregir un problema que provoca un encabezado de clave de partición con formato incorrecto cuando se usa un objeto JsonSerializerSettings personalizado para serializar datos.

### <a name="a-name193193httpswwwnugetorgpackagesmicrosoftazuredocumentdb193"></a><a name="1.9.3"/>[1.9.3](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.3)

  - Fija un problema que provoca consultas a un error con error: token de autorización no es válida en la hora actual.
  - Corregir un problema que quitó SqlParameterCollection original de cruzada partición superior/orden por las consultas.

### <a name="a-name192192httpswwwnugetorgpackagesmicrosoftazuredocumentdb192"></a><a name="1.9.2"/>[1.9.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.2)

  - Ha agregado compatibilidad para las consultas en paralelo para las colecciones divididas.
  - Compatibilidad agregada con cruzada partición consultas de ORDER BY y superior para las colecciones divididas.
  - Corregir las referencias que faltan DocumentDB.Spatial.Sql.dll y Microsoft.Azure.Documents.ServiceInterop.dll que son necesarias cuando se hace referencia a un proyecto DocumentDB con una referencia al paquete DocumentDB Nuget.
  - Corrige la capacidad de usar parámetros de tipos diferentes al usar funciones definidas por el usuario en LINQ. 
  - Corrige un error para las cuentas duplicadas globalmente donde se llegado Upsert llamadas para leer ubicaciones en lugar de ubicaciones de escritura.
  - Métodos agregados a la interfaz IDocumentClient que faltan: 
      - Método UpsertAttachmentAsync que toma mediaStream y opciones como parámetros
      - Método CreateAttachmentAsync que toma opciones como un parámetro
      - Método de CreateOfferQuery que toma querySpec como un parámetro.
  - Clases públicas no selladas que se exponen en la interfaz de IDocumentClient.

### <a name="a-name180180httpswwwnugetorgpackagesmicrosoftazuredocumentdb180"></a><a name="1.8.0"/>[1.8.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.8.0)
  - Agrega la compatibilidad con las cuentas de base de datos de múltiples región.
  - Compatibilidad agregada con intentos en solicitudes limitadas.  Usuario puede personalizar el número de reintentos y el tiempo de espera máximo configurando la propiedad ConnectionPolicy.RetryOptions.
  - Agregar una nueva interfaz de IDocumentClient que define las firmas de todos los métodos y propiedades de DocumenClient.  Como parte de este cambio, también cambiará métodos de extensión que crear IQueryable y IOrderedQueryable a métodos en la propia clase DocumentClient.
  - Agregar opción de configuración para establecer el ServicePoint.ConnectionLimit para un extremo de DocumentDB Uri determinado.  Use ConnectionPolicy.MaxConnectionLimit para cambiar el valor predeterminado, que está establecido en 50.
  - IPartitionResolver desusado y su implementación.  Compatibilidad con IPartitionResolver ahora es obsoleto. Se recomienda que use colecciones de particiones para el rendimiento y almacenamiento superior.


### <a name="a-name171171httpswwwnugetorgpackagesmicrosoftazuredocumentdb171"></a><a name="1.7.1"/>[1.7.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.7.1)
  - Agrega que una sobrecarga URI según el método ExecuteStoredProcedureAsync que toma RequestOptions como un parámetro.
  
### <a name="a-name170170httpswwwnugetorgpackagesmicrosoftazuredocumentdb170"></a><a name="1.7.0"/>[1.7.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.7.0)
  - Tiempo agregado a soporte de live (TTL) para los documentos.

### <a name="a-name163163httpswwwnugetorgpackagesmicrosoftazuredocumentdb163"></a><a name="1.6.3"/>[1.6.3](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.6.3)
  - Corrige un error en el paquete de Nuget de .NET SDK para empaquetar como parte de una solución de servicio de nube de Azure.
  
### <a name="a-name162162httpswwwnugetorgpackagesmicrosoftazuredocumentdb162"></a><a name="1.6.2"/>[1.6.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.6.2)
  - Implementado [colecciones divididas](documentdb-partition-data.md) y [niveles de rendimiento definidas por el usuario](documentdb-performance-levels.md). 

### <a name="a-name153153httpswwwnugetorgpackagesmicrosoftazuredocumentdb153"></a><a name="1.5.3"/>[1.5.3](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.3)
  - **[Fijo]** Consultar DocumentDB inicia de extremo: ' System.Net.Http.HttpRequestException: Error al copiar el contenido en una secuencia.

### <a name="a-name152152httpswwwnugetorgpackagesmicrosoftazuredocumentdb152"></a><a name="1.5.2"/>[1.5.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.2)
  - LINQ expandido soporte incluyendo nuevos operadores en las expresiones condicionales, paginación y comparación de rango.
    - Tomar el operador para habilitar el comportamiento de la parte superior seleccione en LINQ
    - Operador de CompareTo para habilitar las comparaciones de cadenas rango
    - Condicional (?) y combinación de operadores (?)
  - **[Fijo]** ArgumentOutOfRangeException al combinar proyección de modelo con Where en consulta linq.  [#81](https://github.com/Azure/azure-documentdb-dotnet/issues/81)

### <a name="a-name151151httpswwwnugetorgpackagesmicrosoftazuredocumentdb151"></a><a name="1.5.1"/>[1.5.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.1)
 - **[Fijo]** Si seleccione no es la última expresión el proveedor LINQ no supone ninguna proyección y fabrican SELECT * incorrectamente.  [#58](https://github.com/Azure/azure-documentdb-dotnet/issues/58)

### <a name="a-name150150httpswwwnugetorgpackagesmicrosoftazuredocumentdb150"></a><a name="1.5.0"/>[1.5.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.0)
 - Upsert implementada, métodos UpsertXXXAsync agregado
 - Mejoras de rendimiento para todas las solicitudes
 - Compatibilidad del proveedor de LINQ con condicionales, de unión y métodos CompareTo cadenas de
 - **[Fijo]** Proveedor LINQ--> implementación contiene método en la lista para generar la misma SQL como en IEnumerable y matriz
 - **[Fijo]** BackoffRetryUtility utiliza el mismo HttpRequestMessage nuevamente en lugar de crear uno nuevo en reintento
 - **[Obsoleto]** UriFactory.CreateCollection--> ahora debe utilizar UriFactory.CreateDocumentCollection
 
### <a name="a-name141141httpswwwnugetorgpackagesmicrosoftazuredocumentdb141"></a><a name="1.4.1"/>[1.4.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.4.1)
 - **[Fijo]** Problemas de localización cuando se usa la información de referencia cultural no corto como nl-NL etcetera. 
 
### <a name="a-name140140httpswwwnugetorgpackagesmicrosoftazuredocumentdb140"></a><a name="1.4.0"/>[1.4.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.4.0)
  - Enrutamiento basado en Id.
    - Nuevo auxiliar UriFactory para ayudar a construir ID según vínculos a recursos
    - Nuevas sobrecargas en DocumentClient a tomar en URI
  - Se han agregado IsValid() y IsValidDetailed() en LINQ para geoespaciales
  - Compatibilidad con el proveedor de LINQ mejorada
    - Truncar **matemáticas** - Abs, Acos, Asin, Atan, Ceiling, Cos, Exp, Floor, Log, Log10, Pow, redondear, inicio de sesión, Sin, Sqrt Tan,
    - **Cadena** - concatenación, contiene EndsWith, IndexOf, contar, ToLower, TrimStart, reemplazar, invertir, TrimEnd, StartsWith, subcadena, ToUpper
    - **Matriz** - concatenación, contiene, contar
    - Operador **IN**

### <a name="a-name130130httpswwwnugetorgpackagesmicrosoftazuredocumentdb130"></a><a name="1.3.0"/>[1.3.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.3.0)
  - Soporte técnico agregado para modificar las directivas de indexación
    - Nuevo método de ReplaceDocumentCollectionAsync en DocumentClient
    - Nueva propiedad IndexTransformationProgress en ResourceResponse<T> para realizar el seguimiento de progreso del porcentaje de cambios de la directiva de índice
    - DocumentCollection.IndexingPolicy ahora es modificable
  - Ha agregado compatibilidad para consulta e indización espacial
    - Nuevo espacio de nombres Microsoft.Azure.Documents.Spatial para serializar y deserializar tipos espaciales como punto y polígono
    - Nueva clase SpatialIndex para la indización de datos de GeoJSON almacenados en DocumentDB
  - **[Fijo]** : consulta SQL incorrecta generado a partir de expresión linq [#38](https://github.com/Azure/azure-documentdb-net/issues/38)

### <a name="a-name120120httpswwwnugetorgpackagesmicrosoftazuredocumentdb120"></a><a name="1.2.0"/>[1.2.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.2.0)
- Dependencia de Newtonsoft.Json v5.0.7 
- Cambios para admitir Order By
  - Compatibilidad con el proveedor LINQ OrderBy() o OrderByDescending()
  - IndexingPolicy para admitir Order By 
  
        **NB: Possible breaking change** 
  
        If you have existing code that provisions collections with a custom indexing policy, then your existing code will need to be updated to support the new IndexingPolicy class. If you have no custom indexing policy, then this change does not affect you.

### <a name="a-name110110httpswwwnugetorgpackagesmicrosoftazuredocumentdb110"></a><a name="1.1.0"/>[1.1.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.1.0)
- Compatibilidad para las particiones de datos mediante las clases HashPartitionResolver y RangePartitionResolver nuevas y la IPartitionResolver
- Serialización DataContract
- Soporte técnico de GUID de proveedor LINQ
- Compatibilidad con UDF en LINQ

### <a name="a-name100100httpswwwnugetorgpackagesmicrosoftazuredocumentdb100"></a><a name="1.0.0"/>[1.0.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.0.0)
- SDK GA

> [AZURE.NOTE]
Se ha producido un cambio de nombre de paquete NuGet entre la vista previa y la disponibilidad general. Se cambió de **Microsoft.Azure.Documents.Client** a **Microsoft.Azure.DocumentDB**
<br/>


### <a name="a-name09x-preview09x-previewhttpswwwnugetorgpackagesmicrosoftazuredocumentsclient"></a><a name="0.9.x-preview"/>[0.9.x-Preview](https://www.nuget.org/packages/Microsoft.Azure.Documents.Client)
- Vista previa SDK [obsoleto]

## <a name="release--retirement-dates"></a>Versión & fechas de retirada
Microsoft le proporcionará notificación al menos **12 meses** antes de retirar un SDK para equilibrar la transición a una versión más reciente o compatibles.

Nuevas características y funcionalidades y las optimizaciones solo se agregan en el SDK de actual, como por ejemplo, se recomienda que siempre actualice a la versión más reciente SDK tan pronto como sea posible. 

Cualquier solicitud de DocumentDB mediante un SDK retirado será rechazado por el servicio.

> [AZURE.WARNING]
Todas las versiones de Azure DocumentDB SDK para .NET antes de la versión **1.0.0** se retirará en **29 de febrero de 2016**. 
 
<br/>
 
| Versión | Fecha de lanzamiento | Fecha de retirada 
| ---     | ---          | ---
| [1.10.0](#1.10.0) | 27 de septiembre de 2016 |---
| [1.9.5](#1.9.5) | 01 de septiembre de 2016 |---
| [1.9.4](#1.9.4) | 24 de agosto de 2016 |---
| [1.9.3](#1.9.3) | 15 de agosto de 2016 |---
| [1.9.2](#1.9.2) | 23 de julio de 2016 |---
| 1.9.1 | Obsoleto |---
| 1.9.0 | Obsoleto |---
| [1.8.0](#1.8.0) | 14 de junio de 2016 |---
| [1.7.1](#1.7.1) | 06 de mayo de 2016 |---
| [1.7.0](#1.7.0) | 26 de abril de 2016 |---
| [1.6.3](#1.6.3) | 08 de abril de 2016 |---
| [1.6.2](#1.6.2) | 29 de marzo de 2016 |---
| [1.5.3](#1.5.3) | 19 de febrero de 2016 |---
| [1.5.2](#1.5.2) | 14 de diciembre de 2015 |---
| [1.5.1](#1.5.1) | 23 de noviembre de 2015 |---
| [1.5.0](#1.5.0) | 05 de octubre de 2015 |---
| [1.4.1](#1.4.1) | 25 de agosto de 2015 |---
| [1.4.0](#1.4.0) | 13 de agosto de 2015 |---
| [1.3.0](#1.3.0) | 05 de agosto de 2015 |---
| [1.2.0](#1.2.0) | 06 de julio de 2015 |---
| [1.1.0](#1.1.0) | 30 de abril de 2015 |---
| [1.0.0](#1.0.0) | 08 de abril de 2015 |---
| [0.9.3-prelease](#0.9.x-preview) | 12 de marzo de 2015 | 29 de febrero de 2016
| [0.9.2-prelease](#0.9.x-preview) | Enero de 2015 | 29 de febrero de 2016
| [.9.1-prelease](#0.9.x-preview) | 13 de octubre de 2014 | 29 de febrero de 2016
| [0.9.0-prelease](#0.9.x-preview) | 21 de agosto de 2014 | 29 de febrero de 2016

## <a name="faq"></a>Preguntas más frecuentes
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## <a name="see-also"></a>Vea también

Para obtener más información sobre DocumentDB, consulte la página de servicio de [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) . 

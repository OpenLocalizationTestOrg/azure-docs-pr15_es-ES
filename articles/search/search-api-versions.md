<properties
   pageTitle="Versiones de la API de búsqueda de Azure | Microsoft Azure | API de búsqueda"
   description="Directiva de versión de la API de REST de búsqueda de Azure y la biblioteca de cliente en el SDK de .NET."
   services="search"
   documentationCenter=""
   authors="brjohnstmsft"
   manager="pablocas"
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="dotnet"
   ms.workload="search"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.date="08/16/2016"
   ms.author="brjohnst"/>

# <a name="api-versions-in-azure-search"></a>Versiones API de búsqueda de Azure

Búsqueda de Azure implementa actualizaciones de características de forma regular. A veces, pero no siempre, estas actualizaciones requieren nos publicar una versión nueva de nuestra API para conservar la compatibilidad con versiones anteriores. Publicación de una versión nueva le permite controlar cuándo y cómo integrar actualizaciones de servicio de búsqueda en el código.

Una regla, tratamos publicar nuevas versiones cuando es necesario, ya que puede implicar algún esfuerzo para actualizar el código para usar una nueva versión de la API. Solo se publicará una nueva versión si es necesario cambiar algún aspecto de la API de manera que se interrumpe la compatibilidad con versiones anteriores. Esto puede ocurrir debido a las soluciones a las características existentes o debido a nuevas características que cambian la superficie de API existente.

Seguimos la misma regla SDK actualizaciones. El SDK de búsqueda de Azure sigue las reglas de [control de versiones semántico](http://semver.org/) , lo que significa que su versión tiene tres partes: principal, secundaria y generar número (por ejemplo, 1.1.0). Se publicará una nueva versión principal del SDK sólo en caso de cambios que interrumpir la compatibilidad con versiones anteriores. Actualizaciones de características de no separación, se incrementará la versión secundaria y de correcciones de errores se aumentará solo la versión de compilación.

##<a name="snapshot-of-current-versions"></a>Instantánea de versiones actuales 

Debajo de es una instantánea de las versiones actuales de todas las interfaces de programación para búsqueda de Azure. Planes y otros detalles pueden encontrarse en las siguientes secciones de este documento.

Interfaces|Versión principal más reciente|Estado
----------|-------------------------|------
[.NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx)|1.1|Generalmente disponibles, lanzado febrero de 2016
[Vista previa de .NET SDK](https://msdn.microsoft.com/library/mt761536%28v=azure.103%29.aspx)|vista previa de 2.0|Vista previa, que se lanzaron agosto de 2016
[API de REST de servicio](https://msdn.microsoft.com/library/azure/dn798935.aspx)|28 de 02 de 2015|Disponible de forma general
[Vista previa de API de REST de servicio](search-api-2015-02-28-preview.md)|Vista previa de 2015-02-28|Vista previa
[API de REST de administración](https://msdn.microsoft.com/library/azure/dn832684.aspx)|19 de 08 de 2015|Disponible de forma general

Para las API de REST, incluidas las `api-version` en cada llamada es necesario. Esto facilita una versión específica, como una API de vista previa. En el ejemplo siguiente se muestra cómo el `api-version` se especifica el parámetro:

    GET https://adventure-works.search.windows.net/indexes/bikes?api-version=2015-02-28

> [AZURE.NOTE] Aunque cada solicitud tiene un `api-version`, le recomendamos que use la misma versión para todas las solicitudes de API. Esto es cierto cuando nuevas versiones de API introducen atributos u operaciones que no se reconocen en versiones anteriores. Mezcla de versiones de API puede tener consecuencias no deseadas y se debe evitar.
> 
La API de REST de servicio y las API de REST de administración tienen versiones independientemente de los demás. Cualquier semejanza en números de versión es daños incidentales compañeros.

Disponible de forma general (o GA) API puede usarse en producción y están sujetos a contratos de nivel de servicio de Azure. Versiones de vista previa tienen experimentación características que no se migran siempre la versión GA. **Le recomendamos encarecidamente que frente a usar la vista previa de la API en aplicaciones de producción.**

##<a name="sdk-version-roadmap"></a>Guía de la versión SDK

Cada versión de .NET SDK está destinado a una versión específica de la API de REST de servicio. Características son implementadas en la API de REST primero y, a continuación, se implementa en el SDK.

.NET SDK está ahora disponible de forma general y trabajo ya está en curso en la siguiente versión. En la tabla siguiente busca anticipada en futuras versiones de SDK para que tenga una idea de lo que viene a continuación.

Versión de .NET SDK|Versión de la API de REST|Características|ETA
----------------|----------------|--------|---
1.1|28 de 02 de 2015|Sintaxis de la consulta de Lucene|Febrero de 2016
vista previa de 2.0|Vista previa de 2015-02-28|Analizadores personalizados, blobs de Windows Azure e indizadores de tabla, las asignaciones de campos, ETags|Agosto de 2016
2.x|Nueva versión de la API de GA|Igual que la vista previa de 2.0|Anticipado 2016 Q4

##<a name="about-preview-and-generally-available-versions"></a>Acerca de las versiones de vista previa y disponible de forma general

Búsqueda de Azure siempre previamente libera experimentación características a través de la API de REST en primer lugar, a continuación, a través de las versiones preliminares de .NET SDK.

Características de vista previa no se garantiza que deben migrarse a una versión GA. Que se consideran características de una versión GA estable y es probable que cambie con la excepción de las mejoras y correcciones pequeñas compatibles con versiones anteriores, están disponibles para probar y experimentación con el objetivo de recopilar comentarios en el diseño de la característica e implementación de características de vista previa. 

Sin embargo, debido a las características de vista previa están sujetos a cambios, se recomienda frente a escribir el código de producción que realiza una dependencia en versiones de vista previa. Si está utilizando una versión anterior de la vista previa, se recomienda migrar a la versión (GA) disponible de forma general. 

Para .NET SDK: Guía de migración de código puede encontrarse en [el SDK de .NET de actualización](search-dotnet-sdk-migration.md).

Disponibilidad general significa que la búsqueda de Azure está ahora en el acuerdo de nivel de servicio (SLA). El SLA se puede encontrar en [Contratos de nivel de servicio de búsqueda de Azure](https://azure.microsoft.com/support/legal/sla/search/v1_0/).


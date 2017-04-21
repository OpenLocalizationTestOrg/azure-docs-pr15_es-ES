<properties
    pageTitle="Crear un índice de búsqueda de Azure | Microsoft Azure | Servicio de nube hospedado de búsqueda"
    description="¿Qué es un índice de búsqueda de Azure y cómo se usa?"
    services="search"
    manager="jhubbard"
    documentationCenter=""
    authors="ashmaka"
/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# <a name="create-an-azure-search-index"></a>Crear un índice de búsqueda de Azure
> [AZURE.SELECTOR]
- [Información general](search-what-is-an-index.md)
- [Portal](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [REST](search-create-index-rest-api.md)

## <a name="what-is-an-index"></a>¿Qué es un índice?

Un *índice* es un almacén persistente de *documentos* y otras construcciones utilizadas un servicio de búsqueda de Azure. Un documento es una sola unidad de datos que se pueden buscar en el índice. Por ejemplo, un minorista de comercio electrónico que tenga un documento para cada elemento que vende, una organización de noticias puede tener un documento para cada artículo, etcetera. Asignación de estos conceptos más familiar equivalentes de base de datos: un *índice* es conceptualmente similar a una *tabla*y *documentos* equivalen aproximadamente a las *filas* de una tabla.

Al agregar o cargar documentos y enviar las consultas de búsqueda de búsqueda de Azure, enviar las solicitudes de un índice específico en el servicio de búsqueda.

## <a name="field-types-and-attributes-in-an-azure-search-index"></a>Tipos de campo y atributos en un índice de búsqueda de Azure

Al definir el esquema, debe especificar el nombre, el tipo y los atributos de cada campo en el índice. El tipo de campo clasifica los datos que se almacenan en ese campo. Atributos se configuran en campos individuales para especificar cómo se utiliza el campo. Las tablas siguientes enumerar los tipos y los atributos que puede especificar.


### <a name="field-types"></a>Tipos de campo
|Tipo|Descripción|
|------------|-----------|
|*Edm.String*|Texto que opcionalmente se acorta para la búsqueda de texto completo (división de palabras, derivados, etcetera).|
|*Collection(EDM.String)*|Una lista de cadenas que opcionalmente se acorta para la búsqueda de texto completo. No hay ningún límite superior teórico del número de elementos de una colección, pero el límite de 16 MB en tamaño de carga se aplica a las colecciones.|
|*Edm.Boolean*|Contiene valores true o false.|
|*Edm.Int32*|valores de entero de 32 bits.|
|*Edm.Int64*|valores de entero de 64 bits.|
|*Edm.Double*|Datos numéricos de precisión doble.|
|*Edm.DateTimeOffset*|Los valores de hora representados en el formato OData V4 de fecha (por ejemplo, `yyyy-MM-ddTHH:mm:ss.fffZ` o `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm`).|
|*Edm.GeographyPoint*|Un punto que representa una ubicación geográfica en el globo.|

Puede encontrar información más detallada acerca [admiten tipos de datos en MSDN de la búsqueda de Azure](https://msdn.microsoft.com/library/azure/dn798938.aspx).



### <a name="field-attributes"></a>Atributos de campo
|Atributo|Descripción|
|------------|-----------|
|*Clave*|Una cadena que proporciona el identificador único de cada documento en el que se utiliza para buscar documentos. Todos los índices deben tener una clave. Un solo campo puede ser la clave y su tipo debe estar establecida en Edm.String.|
|*Recuperable*|Especifica si se puede devolver un campo en un resultado de búsqueda.|
|*Puede filtrar*|Permite que el campo que se usará en las consultas de filtro.|
|*Puede ordenar*|Permite que una consulta ordenar los resultados de búsqueda con este campo.|
|*Facetable*|Permite a un campo usarlo en una estructura de [exploración facetas](search-faceted-navigation.md) para filtrar directos automática de usuario. Normalmente los campos que contienen valores repetitivos que puede usar para agrupar varios documentos (por ejemplo, varios documentos que se encuentran en una sola marca o categoría de servicio) funcionan mejor como facetas|
|*Para búsquedas*|Marca el campo como texto de búsqueda.|

Puede encontrar información más detallada acerca de la búsqueda de Azure [atributos de índice en MSDN](https://msdn.microsoft.com/library/azure/dn798941.aspx).



## <a name="guidance-for-defining-an-index-schema"></a>Instrucciones para definir un esquema de índice

Al diseñar el índice, tomar su tiempo en la fase de planificación pensar en cada decisión. Es importante tener sus necesidades de negocio y experiencia de usuario de búsqueda en cuenta al diseñar el índice como cada campo debe tener asignado los [atributos adecuados](https://msdn.microsoft.com/library/azure/dn798941.aspx). Cambiar un índice cuando se implementa implica reconstruir y volver a cargar los datos.


Si cambian los requisitos de almacenamiento de datos con el tiempo, puede aumentar o disminuir la capacidad agregando o quitando particiones. Para obtener más información, vea [administrar el servicio de búsqueda en Azure](search-manage.md) o [Los límites del servicio](search-limits-quotas-capacity.md).

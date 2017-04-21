<properties
    pageTitle="Cómo orígenes de datos de perfil de datos"
    description="Artículo de procedimiento para resaltar cómo incluir perfiles de datos de nivel de tabla y columna para registrar los orígenes de datos en el catálogo de datos de Azure y cómo usar los perfiles de datos para comprender los orígenes de datos."
    services="data-catalog"
    documentationCenter=""
    authors="spelluru"
    manager="NA"
    editor=""
    tags=""/>
<tags
    ms.service="data-catalog"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-catalog"
    ms.date="09/13/2016"
    ms.author="spelluru"/>

# <a name="data-profile-data-sources"></a>Orígenes de datos de perfil de datos

## <a name="introduction"></a>Introducción

**Catálogo de datos de Microsoft Azure** es un servicio de nube totalmente administrado que sirve como un sistema de registro y sistema de detección de orígenes de datos de la empresa. En otras palabras, **El catálogo de datos de Azure** consiste en ayudar a las personas descubrir, comprender y usar orígenes de datos y ayudar a las organizaciones a obtener más valor de sus datos. Cuando se registra un origen de datos con el **Catálogo de datos de Azure**, sus metadatos se copia e indizado por el servicio, pero la historia no termina aquí.

La característica de **Perfiles de datos** de **Catálogo de datos de Azure** examina los datos de orígenes de datos admitidos en el catálogo y recopila estadísticas e información sobre esos datos. Es fácil incluir un perfil de los activos de datos. Cuando se registre un activo de datos, elija **Incluir perfil de datos** en la herramienta de registro de origen de datos.

## <a name="what-is-data-profiling"></a>¿Qué es perfiles de datos

Perfiles de datos examina los datos del origen de datos que se registra y recopila estadísticas e información sobre esos datos. Durante la detección de origen de datos, estas estadísticas pueden ayudarle a determinar la idoneidad de los datos a resolver su problema.

<!-- In [How to discover data sources](data-catalog-how-to-discover.md), you learn about **Azure Data Catalog's** extensive search capabilities including searching for data assets that have a profile. See [How to include a data profile when registering a data source](#howto). -->

Perfiles de datos es compatible con los siguientes orígenes de datos:

- Vistas y tablas de SQL Server (incluidos DB de SQL Azure y almacenamiento de datos de SQL Azure)
- Vistas y tablas de oracle
- Vistas y tablas de Teradata
- Tablas de sección

Como perfiles de datos al registro de activos de datos ayuda a los usuarios responder a preguntas sobre los orígenes de datos, incluidos:

-   ¿Puede usarse para solucionar el problema de mi empresa?
-   ¿Los datos que se ajustan a estándares en particular o patrones?
-   ¿Cuáles son algunas de las anomalías del origen de datos?
-   ¿Cuáles son los posibles retos de integración de datos en mi aplicación?

> [AZURE.NOTE] También puede agregar documentación a un activo para describir cómo se pueden integrar datos en una aplicación. Aprenda [a orígenes de datos del documento](data-catalog-how-to-documentation.md).


<a name="howto"/>
## <a name="how-to-include-a-data-profile-when-registering-a-data-source"></a>Cómo puede incluir un perfil de datos al registrar un origen de datos

Es fácil incluir un perfil del origen de datos. Cuando se registre un origen de datos, en el panel **para registrar los objetos** de la herramienta de registro de origen de datos, elija **Incluir perfil de datos**.

![](media\data-catalog-data-profile\data-catalog-register-profile.png)

Para obtener más información sobre cómo registrar los orígenes de datos, vea [cómo registrar los orígenes de datos](data-catalog-how-to-register.md) y [empezar a trabajar con el catálogo de datos de Azure](data-catalog-get-started.md).


## <a name="filtering-on-data-assets-that-include-data-profiles"></a>Filtrado de activos de datos que incluyen perfiles de datos
Para descubrir activos de datos que incluyen un perfil de datos, puede incluir `has:tableDataProfiles` o `has:columnsDataProfiles` como uno de los términos de búsqueda.

> [AZURE.NOTE] Selección de **Incluir datos de perfil** en la herramienta de registro de origen de datos incluye la tabla y la información de perfil de nivel de columna. Sin embargo, la API de catálogo de datos permite activos de datos para registrar con solo un conjunto de información del perfil incluido.

## <a name="viewing-data-profile-information"></a>Ver la información de perfil de datos

Cuando encuentre un origen de datos adecuado con un perfil, puede ver los detalles del perfil de datos. Para ver el perfil de datos, seleccione un activo de datos y elija **Perfil de datos** en la ventana de portal de catálogo de datos.

![](media\data-catalog-data-profile\data-catalog-view.png)

Un perfil de datos en el **Catálogo de datos de Azure** muestra la tabla y la información de perfil de columna, que incluye:

### <a name="object-data-profile"></a>Perfil de datos de objeto

-   Número de filas
-   Tamaño de tabla
-   Cuando se actualizó por última vez el objeto

### <a name="column-data-profile"></a>Perfil de datos de columna

- Tipo de datos de columna
- Número de valores distintos
- Número de filas con valores nulos
- Mínimo, máximo, Media y desviación estándar de los valores de columna

## <a name="summary"></a>Resumen
Perfiles de datos proporcionan estadísticas e información sobre los activos de datos registrados para ayudarle a determinar la idoneidad de los datos para resolver problemas empresariales. Junto con anotaciones y documentar orígenes de datos, perfiles de datos pueden dar a los usuarios un conocimiento más profundo de los datos.


## <a name="see-also"></a>Vea también
-   [Cómo registrar los orígenes de datos](data-catalog-how-to-register.md)
-   [Empezar a trabajar con el catálogo de datos de Azure](data-catalog-get-started.md)

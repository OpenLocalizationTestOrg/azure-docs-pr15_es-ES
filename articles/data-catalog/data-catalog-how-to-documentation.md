<properties
    pageTitle="Cómo documentar orígenes de datos | Microsoft Azure"
    description="Artículo de procedimiento para resaltar cómo documentar activos de datos en el catálogo de datos de Azure."
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

# <a name="document-data-sources"></a>Orígenes de datos de documento

## <a name="introduction"></a>Introducción

**Catálogo de datos de Microsoft Azure** es un servicio de nube totalmente administrado que sirve como un sistema de registro y sistema de detección de orígenes de datos de la empresa. En otras palabras, **El catálogo de datos de Azure** es todo acerca de las personas de ayudar a detectar, *comprender*y usar orígenes de datos y ayudar a las organizaciones a obtener más valor de sus datos.

Cuando se registra un origen de datos con el **Catálogo de datos de Azure**, sus metadatos se copia e indizado por el servicio, pero la historia no termina aquí. **Catálogo de datos de Azure** también permite a los usuarios proporcionar su propia documentación completa que puede describir el uso y escenarios comunes para el origen de datos.

[Cómo hacer anotaciones en orígenes de datos](data-catalog-how-to-annotate.md), sabrá que expertos que conocen el origen de datos pueden realizar anotaciones con etiquetas y una descripción. El portal de **Catálogo de datos de Azure** incluye un editor de texto enriquecido para que los usuarios pueden documentar completamente contenedores y activos de datos. El editor incluye el formato de párrafo, como encabezados, formato de texto, listas con viñetas, listas numeradas y tablas.

Etiquetas y descripciones son una buena opción para anotaciones simples. Sin embargo, para ayudar a los consumidores de datos a comprender mejor el uso de un origen de datos y escenarios empresariales para un origen de datos, un experto puede proporcionar la documentación completa y detallada. Es fácil a un origen de datos del documento. Seleccionar un activo de datos o el contenedor y elija **documentación**.

![](media\data-catalog-documentation\data-catalog-documentation.png)

## <a name="documenting-data-assets"></a>Documentar los activos de datos

La ventaja de documentación de **Catálogo de datos de Azure** le permite usar el catálogo de datos como un repositorio de contenido para crear una descripción completa de los activos de datos. Puede explorar contenido detallada que describa contenedores y tablas. Si ya dispone de contenido en otro repositorio de contenido, como SharePoint o un archivo compartido, puede agregar los vínculos de la documentación de activos haga referencia a este contenido existente. Esta característica hace que los documentos existentes más reconocible.

> [AZURE.NOTE] Documentación no se incluye en el índice de búsqueda.

![](media\data-catalog-documentation\data-catalog-documentation2.png)

El nivel de documentación puede ir desde que describe las características y el valor de un contenedor de activos de datos para obtener una descripción detallada del esquema de tabla en un contenedor. El nivel de documentación depende de sus necesidades empresariales. Pero en general, estas son algunas ventajas y desventajas de documentar los activos de datos:

-   Un contenedor de documentos: todo el contenido está en un solo lugar, pero carece de información necesaria para los usuarios tomar una decisión informada.
-   Solo las tablas de documentos: contenido específica del objeto, pero los usuarios tienen varios lugares para documentos.
-   Contenedores y tablas de documentos: enfoque más completa, pero puede introducir más de mantenimiento de los documentos.

## <a name="summary"></a>Resumen

Documentación de orígenes de datos con el **Catálogo de datos de Azure** puede crear una narración sobre los activos de datos en tantos detalles como sea necesario.  Mediante los vínculos, puede vincular al contenido almacenado en un repositorio de contenido existente, que reúne los activos de datos y documentos existentes. Una vez que los usuarios determinar los activos de datos apropiado, pueden tener un conjunto completo de documentación.

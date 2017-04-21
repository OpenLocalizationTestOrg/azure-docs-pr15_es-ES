<properties
    pageTitle="¿Qué es la búsqueda de Azure | Microsoft Azure | Servicio de nube hospedado de búsqueda"
    description="Búsqueda de Azure es un servicio de búsqueda de nube hospedado totalmente administrados. Más información en esta introducción a las características."
    services="search"
    manager="jhubbard"
    authors="ashmaka"
    documentationCenter=""/>

<tags
    ms.service="search"
    ms.devlang="NA"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# <a name="what-is-azure-search"></a>¿Qué es la búsqueda de Azure?

Búsqueda de Azure es una solución de búsqueda como servicio de nube que delega el servidor y administración de infraestructuras a Microsoft, lo que con un servicio de listos para usar que se puede rellenar con los datos y, a continuación, use para agregar búsqueda a la web o la aplicación móvil. Búsqueda de Azure le permite agregar fácilmente una experiencia de búsqueda eficaces a sus aplicaciones con una simple [API de REST](https://msdn.microsoft.com/library/azure/dn798935.aspx) o [.NET SDK](search-howto-dotnet-sdk.md) sin administración de infraestructura de búsqueda o convertirse en un experto en búsqueda.

## <a name="give-your-users-a-powerful-search-experience"></a>Dar a los usuarios una experiencia de búsqueda eficaz

Puede formular **consultas eficaces** utilizando la [sintaxis de la consulta simple](https://msdn.microsoft.com/library/azure/dn798920.aspx), que ofrece operadores lógicos, operadores de frase de búsqueda, operadores de sufijo, operadores de prioridad. Además, puede habilitar la [sintaxis de la consulta Lucene](https://msdn.microsoft.com/library/azure/mt589323.aspx) búsqueda parcial, búsqueda de proximidad, incremento de términos y expresiones regulares. Búsqueda de Azure también es compatible con analizadores léxicos personalizados para permitir que su aplicación para administrar consultas de búsqueda compleja con coincidencia fonética y expresiones regulares.

**Compatibilidad con idiomas** es [incluido para 56 distintos idiomas](https://msdn.microsoft.com/library/azure/dn879793.aspx). Con los analizadores Lucene y analizadores de Microsoft (restringidos por años de lenguaje natural procesamiento en Office y Bing), búsqueda de Azure puede analizar texto en el cuadro de búsqueda de la aplicación inteligente manejar lingüístico específicos de idioma incluidos verbales, género, sustantivos plurales irregulares (por ejemplo, ' mouse' frente a 'mouse'), un interés compuesto anule la palabra, división de palabras (para los idiomas sin espacios) y más.

**Sugerencias de búsqueda** puede estar habilitada para completar automáticamente las barras de búsqueda y consultas con escritura anticipada. [Se sugieren real documentos en el índice](https://msdn.microsoft.com/library/azure/dn798936.aspx) como usuarios escriba búsqueda parcial de entrada.

**Resaltado** [permite](https://msdn.microsoft.com/library/azure/dn798927.aspx) a los usuarios para ver el fragmento de texto en cada resultado que contiene a las coincidencias para su consulta. Se puede elegir qué campos devuelven fragmentos resaltados.

**Navegación facetas** fácilmente se agrega a la página de resultados de búsqueda con búsqueda de Azure. Con [solo un solo parámetro de consulta](https://msdn.microsoft.com/library/azure/dn798927.aspx), búsqueda de Azure devolverá toda la información necesaria para crear una experiencia de búsqueda facetas en la interfaz de usuario de la aplicación para permitir que los usuarios a explorar en profundidad y filtrar los resultados de búsqueda (por ejemplo, filtrar elementos del catálogo por rango de precio o marca).

Soporte técnico **geoespaciales** le permite procesar inteligente, filtrar y mostrar ubicaciones geográficas. Búsqueda de Azure permite a los usuarios a explorar datos en función de la proximidad de un resultado de búsqueda a una ubicación especificada o de una región geográfica específico. Este vídeo explica cómo funciona: [canal 9: los datos de búsqueda de Azure y geoespaciales](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data).

**Filtros de** puede usarse para incorporar facetas navegación en la interfaz de usuario de la aplicación con facilidad, mejorar formulación de consulta y filtro basado en o programador-especificado por el usuario criterios. Crear filtros eficaces utilizando la [sintaxis de OData](https://msdn.microsoft.com/library/azure/dn798921.aspx).

## <a name="empower-your-developers-with-an-easy-to-use-service"></a>Permiten a los desarrolladores con un servicio de fácil de usar

**Alta disponibilidad** garantiza una experiencia de servicio de búsqueda muy fiable. Cuando escala correctamente, [búsqueda de Azure ofrece un SLA 99,9%](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

No **totalmente administrados** como una solución de llevar a cabo, búsqueda de Azure requiere estrictamente ninguna administración de la infraestructura. Su servicio puede personalizarse fácilmente a sus necesidades, ajustando en dos dimensiones manejar más espacio de almacenamiento de documentos, mayor cargas de consultas o ambos.

**Integración de datos** mediante [indizadores](https://msdn.microsoft.com/library/azure/dn946891.aspx) permite Azure búsqueda rastrear automáticamente la base de datos de SQL Azure, DocumentDB de Azure o [almacenamiento de blobs de Windows Azure](search-howto-indexing-azure-blob-storage.md) para sincronizar el contenido del índice de la búsqueda con el almacén de datos principal.

**Descifrado del documento** es disponible (actualmente en la vista previa) [para leer y los formatos de archivos de índice](search-howto-indexing-azure-blob-storage.md) incluidos Microsoft Office, así como documentos PDF y HTML.

**Análisis de tráfico de búsqueda** son [fácilmente recopila y analiza](search-traffic-analytics.md) desbloquear perspectivas de usuarios que están escribiendo en el cuadro de búsqueda.

**Puntuación simple** es una ventaja clave de búsqueda de Azure. [Puntuación perfiles](https://msdn.microsoft.com/library/azure/dn798928.aspx) se usan para permitir que las organizaciones relevancia modelo como una función de valores en los documentos en Sí. Por ejemplo, podría querer productos más recientes o un descuento productos aparecen superior en los resultados de búsqueda. También puede crear perfiles de puntuación con etiquetas para puntuación personalizada basada en las preferencias de búsqueda de cliente ha controlan y almacenan por separado.

**Ordenar** se ofrece para varios campos a través del esquema de índice y alternar, a continuación, en el momento de consulta con un parámetro de búsqueda única.

**Paginación** y limitar los resultados de búsqueda es [sencillo con el control precisas](search-pagination-page-layout.md) que búsqueda de Azure ofrece a los resultados de búsqueda.  

**Explorador de búsqueda** permite emitir consultas frente a todos los índices derecha desde el portal de Azure de su cuenta para probar consultas y refinar los perfiles de puntuación fácilmente.

## <a name="how-it-works"></a>Cómo funciona

### <a name="1-provision-service"></a>1. servicio de aprovisionamiento de
Puede girar un servicio de búsqueda de Azure con el [Portal de Azure](https://portal.azure.com/) o la [API de administración de recursos de Azure](https://msdn.microsoft.com/library/azure/dn832684.aspx).

Dependiendo de cómo configurar el servicio de búsqueda, deberá usar el nivel de servicio que se comparte con otros suscriptores de búsqueda de Azure gratuito, o bien un [nivel de pago](https://azure.microsoft.com/pricing/details/search/) que dedica recursos que solo se utiliza el servicio. Al hacer el aprovisionamiento de su servicio, también elegir la región del centro de datos que hospeda el servicio.

Dependiendo de qué nivel de servicio que elija, puede escalar el servicio en dos dimensiones: 1) agregar réplicas a aumentar su capacidad para manejar las cargas de gran consulta y (2) agregar particiones para agregar almacenamiento a más documentos. Controlar el rendimiento de almacenamiento y consulta de documento por separado, puede personalizar el servicio de búsqueda a sus necesidades.

### <a name="2-create-index"></a>2. Crear índice
Antes de cargar el contenido en el servicio de búsqueda de Azure, primero debe definir un índice de búsqueda de Azure. Un índice es similar a una tabla de base de datos que contiene los datos y puede aceptar consultas de búsqueda. Definir el esquema de índice para asignar a la estructura de los documentos que desea buscar similares a los campos en una base de datos.

El esquema de estos índices puede crearse en el Portal de Azure, o mediante programación [utilizando .NET SDK](search-howto-dotnet-sdk.md) o [API de REST](https://msdn.microsoft.com/library/azure/dn798941.aspx). Una vez que se define el índice, a continuación, puede cargar los datos del servicio de búsqueda de Azure donde se indiza posteriormente.

### <a name="3-index-data"></a>3. datos de índice
Una vez que ha definido los campos y los atributos del índice, está listo para cargar el contenido en el índice. Puede usar un modelo de inserción o de extracción para cargar los datos en el índice.

El modelo de extracción se proporciona a través de indizadores que se pueden configurar para a petición o las actualizaciones programadas (consulte [las operaciones de indizador (API de REST de servicio de búsqueda de Azure)](https://msdn.microsoft.com/library/azure/dn946891.aspx)), que permite a recopilar fácilmente los datos y los cambios de datos de Azure DocumentDB, base de datos de SQL Azure, el almacenamiento de blobs de Windows Azure o SQL Server alojado en una máquina virtual de Azure.

El modelo de inserción se proporciona a través de las API de REST usado para enviar documentos actualizados a un índice o el SDK. Puede insertar datos desde prácticamente cualquier conjunto de datos con el formato JSON. Consulte [Agregar, actualizar, o eliminar documentos](https://msdn.microsoft.com/library/azure/dn798930.aspx) o [cómo usar .NET SDK)](search-howto-dotnet-sdk.md) para obtener instrucciones sobre cómo cargar datos.

### <a name="4-search"></a>4. búsqueda de
Una vez que haya rellenado el índice de búsqueda de Azure, puede ahora [emitir consultas de búsqueda](https://msdn.microsoft.com/library/azure/dn798927.aspx) en el extremo de servicio mediante solicitudes HTTP simples con la API de REST o el SDK de .NET.

## <a name="try-it-now-for-free"></a>Pruébelo ahora (gratis!)
¡Puede intentar búsqueda de Azure hoy! Si ya tiene una cuenta de Azure, puede [aprovisionar un servicio en el nivel gratuito](search-create-service-portal.md).

Si no tiene una cuenta de Azure que puede probar una sesión gratuita, 60 minutos con no registrarse necesarios. Vaya al [Servicio de aplicación de Azure probar](http://go.microsoft.com/fwlink/p/?LinkId=618214) y seleccione "Web App". A continuación, seleccione la plantilla de "Búsqueda de ASP.NET + Azure" para empezar.

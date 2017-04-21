<properties
    pageTitle="Importar datos a la búsqueda de Azure utilizar indizadores en el Portal de Azure | Microsoft Azure | Servicio de nube hospedado de búsqueda"
    description="Usar al Asistente Importar datos de Azure búsqueda en el Portal de Azure para rastrear datos desde almacenamiento de blobs de Windows Azure, almacenamiento de la tabla, base de datos de SQL y SQL Server en máquinas virtuales de Azure."
    services="search"
    documentationCenter=""
    authors="HeidiSteen"
    manager="jhubbard"
    editor=""
    tags="Azure Portal"/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="heidist"/>

# <a name="import-data-to-azure-search-using-the-portal"></a>Importar datos con el portal de búsqueda de Azure

El portal de Azure proporciona a un asistente **Importar datos** en el panel de búsqueda de Azure para cargar datos en un índice. 

  ![Importar datos en la barra de comandos][1]

Internamente, el asistente configura e invoca un *indizador*, automatizar varios pasos del proceso de indización: 

- Conectarse a un origen de datos externos en la suscripción actual de Azure
- Generar un esquema de índice basado en la estructura de datos de origen
- Crear documentos basados en un conjunto de filas recuperado del origen de datos
- Cargar documentos en el índice en el servicio de búsqueda

Puede probar este flujo de trabajo con datos de ejemplo en DocumentDB. Para obtener instrucciones, visite [Introducción a Azure búsqueda en el Portal de Azure](search-get-started-portal.md) .

## <a name="data-sources-supported-by-the-import-data-wizard"></a>Orígenes de datos admitidos por el Asistente Importar datos

El Asistente para importar datos es compatible con los siguientes orígenes de datos: 

- Base de datos SQL Azure
- Datos relacionales de SQL Server en una máquina virtual de Azure
- DocumentDB de Azure
- Almacenamiento de blobs de Windows Azure (en vista previa)
- Almacenamiento de tablas de Azure (en vista previa)

Un conjunto de datos plana es una entrada necesaria. Solo puede importar desde una única tabla, la vista de base de datos o la estructura de datos equivalentes. Debe crear la estructura de datos antes de ejecutar al asistente.

Observe que algunos de los indizadores están aún en vista previa, lo que significa que la definición del indizador se copia la versión de vista previa de la API. Vea [información general del indizador](search-indexer-overview.md) para obtener más información y vínculos.

## <a name="connect-to-your-data"></a>Conectar a los datos

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com) y los paneles de servicio abiertas. Puede hacer clic en **Servicios de búsqueda** en la barra de salto para mostrar los servicios existentes en la suscripción actual. 

2. En la barra de comandos para abrir la hoja de datos de importación de diapositivas, haga clic en **Importar datos** .  

3. Haga clic en **conectarse a los datos** para especificar una definición de origen de datos utilizada por un indizador. Para los orígenes de datos dentro de la suscripción, el Asistente normalmente puede detectar y leer la información de conexión, minimizar los requisitos de configuración global.

| | |
|--------|------------|
|**Origen de datos existente** | Si ya tiene indizadores definidos en el servicio de búsqueda, puede seleccionar una definición de origen de datos existente para importar de otro.|
|**Base de datos SQL Azure** | Nombre de servicio, las credenciales para un usuario de la base de datos con permiso de lectura y un nombre de base de datos se pueden especificar en la página o a través de una cadena de conexión de ADO.NET. Elija la opción de cadena de conexión para ver o personalizar las propiedades. <br/><br/>La tabla o vista que proporciona el conjunto de filas se debe especificar en la página. Esta opción aparece cuando la conexión se complete correctamente, que le da una lista desplegable para que pueda hacer una selección.|
|**SQL Server en máquina virtual de Azure** | Especifique un nombre de servicio completo, el identificador de usuario y la contraseña y la base de datos como una cadena de conexión. Para usar este origen de datos, debe tener previamente instalado un certificado en el almacén local que cifra la conexión. <br/><br/>La tabla o vista que proporciona el conjunto de filas se debe especificar en la página. Esta opción aparece cuando la conexión se complete correctamente, que le da una lista desplegable para que pueda hacer una selección.
|**DocumentDB** |Requisitos de incluyen la cuenta, la base de datos y la colección. Todos los documentos de la colección se incluirán en el índice. Puede definir una consulta para acoplar o filtrar el conjunto de filas, o para detectar documentos modificados para operaciones de actualización de datos subsiguientes.|
|**Almacenamiento de blobs de Windows Azure** | Requisitos de incluyen la cuenta de almacenamiento y un contenedor. De manera opcional, si blob nombres seguir una convención de nomenclatura virtual fines de agrupamiento, puede especificar la parte del directorio virtual del nombre como una carpeta en el contenedor. Para obtener más información, vea [Almacenamiento de blobs de indización (vista preliminar)](search-howto-indexing-azure-blob-storage.md) . |
|**Almacenamiento de tablas de Azure** | Requisitos de incluyen la cuenta de almacenamiento y un nombre de tabla. Si lo desea, puede especificar una consulta para recuperar un subconjunto de las tablas. Para obtener más información, vea [Almacenamiento de tablas de indización (vista preliminar)](search-howto-indexing-azure-tables.md) . |

## <a name="customize-target-index"></a>Personalizar el índice de destino

Normalmente se deduce un índice preliminar del conjunto de datos. Agregar, editar o eliminar campos para completar el esquema. Además, puede establecer atributos en el nivel de campo para determinar los comportamientos de búsqueda subsiguientes.

1. En **Personalizar índice de destino**, especifique el nombre y una **clave** que se utiliza para identificar cada documento. La clave debe ser una cadena. Si los valores de campo incluyen espacios o guiones, asegúrese de establecer las opciones avanzadas de **importar los datos** para suprimir la comprobación de validación para estos caracteres.

2. Revise y revisar los campos restantes. Tipo y el nombre de campo se rellenan normalmente por usted. Puede cambiar el tipo de datos.

3. Establecer los atributos de índice para cada campo:

 - Puede recuperar devuelve el campo en los resultados de búsqueda.
 - Pueden filtrarse permite el campo al que se hace referencia en las expresiones de filtro.
 - Puede ordenar permite el campo usarlo con un criterio de ordenación.
 - Facetable habilita el campo para la navegación facetas.
 - Permite buscar la búsqueda de texto completo.
  
4. Si desea especificar un analizador de idioma en el nivel de campo, haga clic en la pestaña **Analyzer** . Analizadores de idioma solo se pueden especificar en este momento. Usar un analizador personalizado o un analizador de idioma no como palabra clave, trama y así sucesivamente, necesitarán código.

 - Haga clic en **Searchable** para designar la búsqueda de texto en el campo y habilitar la lista desplegable de analizador.
 - Elija el analizador que desee. Para obtener información detallada, vea [crear un índice para documentos en varios idiomas](search-language-support.md) .

5. Haga clic en el **Suggester** para habilitar las sugerencias de consulta con escritura anticipada en los campos seleccionados.


## <a name="import-your-data"></a>Importar los datos

1. En **Importar datos**, proporcione un nombre para el indizador. Recuerde que el producto del Asistente para importar datos es un indizador. Más adelante, si desea verlo o editarlo, deberá selecciónela desde el portal en lugar de volver a ejecutar el asistente. 

2. Especifique la programación, que se basa en la zona horaria regional en la que se le proporciona el servicio.

3. Establecer las opciones avanzadas para especificar los umbrales de si puede continuar la indización si se elimina un documento. Además, puede especificar si los campos de **clave** pueden contener espacios y barras diagonales.  

## <a name="edit-an-existing-indexer"></a>Editar un indizador existente

En el panel de servicio, haga doble clic en el mosaico de indizador a la diapositiva de una lista de todos los indizadores creado para la suscripción. Haga doble clic en uno de los indizadores para ejecutar, modificar o eliminar. Puede reemplazar el índice con otra existente, cambiar el origen de datos y establecer las opciones de los umbrales de error durante la indización.

## <a name="edit-an-existing-index"></a>Editar un índice existente

En búsqueda de Azure, estructurales actualizaciones a un índice requieren una regeneración de índice, que consta de eliminar el índice, volver a crear el índice y volver a cargar datos. Actualizaciones estructurales incluyen cambiar un tipo de datos y cambiar el nombre o eliminar un campo.

Las modificaciones que no requieran una regeneración incluyen la adición de un nuevo campo, cambiar los perfiles de puntuación, cambiar suggesters o cambiar los analizadores de idioma. Para obtener más información, vea [Actualizar índice](https://msdn.microsoft.com/library/azure/dn800964.aspx) .

## <a name="next-step"></a>Siguiente paso

Revise estos vínculos para obtener más información sobre indizadores:

- [Indización de base de datos SQL Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)
- [Indización DocumentDB](../documentdb/documentdb-search-indexer.md)
- [Almacenamiento de blobs de indización (vista preliminar)](search-howto-indexing-azure-blob-storage.md)
- [Almacenamiento de tablas de indización (vista preliminar)](search-howto-indexing-azure-tables.md)



<!--Image references-->
[1]: ./media/search-import-data-portal/search-import-data-command.png


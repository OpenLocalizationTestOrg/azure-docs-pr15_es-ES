<properties
    pageTitle="Crear un índice de búsqueda de Azure con el Portal de Azure | Microsoft Azure | Servicio de nube hospedado de búsqueda"
    description="Crear un índice con el Portal de Azure."
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

# <a name="create-an-azure-search-index-using-the-azure-portal"></a>Crear un índice de búsqueda de Azure con el Portal de Azure
> [AZURE.SELECTOR]
- [Información general](search-what-is-an-index.md)
- [Portal](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [REST](search-create-index-rest-api.md)

En este artículo le guiará a través del proceso de creación de una búsqueda de Azure [índice](search-what-is-an-index.md) con el Portal de Azure.

Antes de seguir esta guía y crear un índice, debe tener ya [creó un servicio de búsqueda de Azure](search-create-service-portal.md).


## <a name="i-go-to-your-azure-search-blade"></a>Puedo. Vaya a su módulo de búsqueda de Azure
1. Haga clic en "Todos los recursos" en el menú en el lado izquierdo del [Portal de Azure](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)
2. Seleccione el servicio de búsqueda de Azure

## <a name="ii-add-and-name-your-index"></a>II. Agregar y asigne un nombre a su índice
1. Haga clic en el botón "Agregar índice"
2. Nombre el índice de búsqueda de Azure. Estamos creando un índice para buscar hoteles en esta guía, nos hemos denominado nuestro índice "hoteles".
  * El nombre del índice debe comenzar con una letra y contener sólo letras minúsculas, dígitos o guiones ("-").
  * Similar a su nombre de servicio, el nombre del índice que elegir también será parte de la dirección URL del extremo donde le enviará las solicitudes HTTP para la API de búsqueda de Azure
3. Haga clic en la entrada de "Campos" para abrir un nuevo módulo

![](./media/search-create-index-portal/add-index.png)


## <a name="iii-create-and-define-the-fields-of-your-index"></a>III. Crear y definir los campos del índice
1. Seleccionando la entrada de "Campos", se abrirá un nuevo módulo con un formulario para introducir la definición de índice.
2. Agregar campos a su índice usando el formulario.

  * Un campo de *clave* de tipo Edm.String es obligatorio para cada índice de búsqueda de Azure. Este campo clave se crea de manera predeterminada con el nombre "id". Hemos cambiado lo a "hotelId" en nuestro índice.
  * Algunas propiedades de su esquema de índice sólo se pueden establecer una vez y no se pueden actualizar en el futuro. Por este motivo, las actualizaciones de esquema que se requeriría reindización como cambiar los tipos de campo no son posibles actualmente después de la configuración inicial.
  * Hemos elegido cuidadosamente los valores de propiedad para cada campo en función de cómo creemos que se van a utilizar en una aplicación. Mantener sus necesidades de negocio y experiencia de usuario de búsqueda en cuenta al diseñar el índice como cada campo se debe asignar a las [propiedades apropiadas](https://msdn.microsoft.com/library/azure/dn798941.aspx). Estos control de propiedades que buscar características (filtrado, faceting, ordenación, la búsqueda de texto, etc.) que se aplica a los campos. Por ejemplo, es probable que personas buscando hoteles interesado en coincidencias de palabra clave en el campo "Descripción", para habilitar la búsqueda de texto completo de ese campo estableciendo la propiedad "Searchable".
    * También puede establecer el [Analizador de idioma](https://msdn.microsoft.com/en-us/library/azure/dn879793.aspx) para cada campo haciendo clic en la pestaña "Analizador" en la parte superior de la hoja. A continuación puede ver que hemos seleccionado un analizador francés para un campo en nuestro índice para texto en francés.

3. Haga clic en **Aceptar** en el módulo de "Campos" para confirmar las definiciones de campo
4. Haga clic en **Aceptar** en el módulo de "Agregar índice" para guardar y volver a crear el índice que acaba de definir.

En las capturas de pantalla a continuación, puede ver cómo hemos denominado y definido los campos para nuestro índice "hoteles".

![](./media/search-create-index-portal/field-definitions.png)

![](./media/search-create-index-portal/set-analyzer.png)

## <a name="next"></a>Siguiente
Después de crear un índice de búsqueda de Azure, estará listo para [cargar el contenido en el índice](search-what-is-data-import.md) para poder empezar a buscar los datos.

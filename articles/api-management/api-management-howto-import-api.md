<properties 
    pageTitle="Conceptos básicos de administración de la API" 
    description="Obtenga información sobre las API, productos, roles, grupos y otros conceptos clave de administración de la API." 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-import-the-definition-of-an-api-with-operations-in-azure-api-management"></a>Cómo importar la definición de una API con las operaciones de administración de la API de Azure

En administración de la API, se pueden crear nuevas API y se puede importar las operaciones de agregado manualmente o la API junto con las operaciones en un solo paso.

Es posible importar API y sus operaciones con los siguientes formatos.

-   WADL
-   Swagger

Esta guía muestra cómo crear una nueva API e importar sus operaciones en un solo paso. Para obtener información sobre cómo crear manualmente una API y agregar operaciones, vea [cómo crear API][] y [cómo agregar acciones a una API][].

## <a name="import-api"> </a>Importar una API

API se crean y se configuran en el portal de publisher. Para acceder al portal de publisher, haga clic en **Administrar** en el Portal de Azure clásico de su servicio de administración de la API. Si aún no ha creado una instancia de servicio de administración de la API, vea [crear una instancia de servicio de administración de la API][] en el tutorial de [Introducción a la administración de la API de Azure][] .

![Portal de Publisher][api-management-management-console]

Haga clic en **las API de** desde el menú de **Administración de la API** de la izquierda y, a continuación, haga clic en **Importar API**.

![API de importación][api-management-import-apis]

La ventana de la **API de importación** tiene tres pestañas que corresponden a las tres formas para proporcionar la especificación de la API.

-   **Desde el Portapapeles** le permite pegar la especificación de la API en el cuadro de texto designado.
-   **Desde archivo** permite examinar y seleccione el archivo que contiene la especificación de la API.
-   **De dirección URL** le permite proporcionar la dirección URL para la especificación de la API.

![Formato de importación API][api-management-import-api-clipboard]

Después de proporcionar la especificación de la API, use los botones de la derecha para indicar el formato de especificación. Se admiten los siguientes formatos.

-   WADL
-   Swagger

A continuación, escriba un **sufijo de dirección URL de Web API**. Esto se anexa a la dirección URL base para el servicio de administración de la API. La base de la dirección URL es común para todas las API hospedadas en cada instancia de un servicio de administración de la API. Administración de la API distingue API por su sufijo y, por tanto, el sufijo debe ser único para cada API en una instancia de servicio de administración de API específica.

Una vez que se especifican todos los valores, haga clic en **Guardar** para crear la API y las operaciones asociadas. 

>[AZURE.NOTE] Para obtener un tutorial de importación una calculadora básica API en formato Swagger, consulte [administrar su primera API de administración de la API de Azure](api-management-get-started.md).

## <a name="export-api"></a> Exportar una API

Además de importar las API de nuevas, puede exportar las definiciones de la API desde el portal de publisher. Para ello, haga clic en **Exportar API** en la **pestaña Resumen** de la **API**.

![API de exportación][api-management-export-api]

API pueden exportarse con WADL o Swagger. Seleccione el formato deseado, haga clic en **Guardar**y elija la ubicación donde desea guardar el archivo.

![Formato de la API de exportación][api-management-export-api-format]

## <a name="next-steps"> </a>Pasos siguientes

Una vez que se crea una API y las operaciones que se importan, puede revisar y configurar ajustes adicionales, agregue la API a un producto y publicarlo para que esté disponible para los programadores. Para obtener más información, consulte a las guías siguientes.

-   [Cómo configurar opciones de API][]
-   [Cómo crear y publicar un producto][]




[api-management-management-console]: ./media/api-management-howto-import-api/api-management-management-console.png
[api-management-import-apis]: ./media/api-management-howto-import-api/api-management-api-import-apis.png
[api-management-import-api-clipboard]: ./media/api-management-howto-import-api/api-management-import-api-wizard.png
[api-management-export-api]: ./media/api-management-howto-import-api/api-management-export-api.png
[api-management-export-api-format]: ./media/api-management-howto-import-api/api-management-export-api-format.png

[Import an API]: #import-api
[Export an API]: #export-api
[Configure API settings]: #configure-api-settings
[Next steps]: #next-steps

[Introducción a la administración de la API de Azure]: api-management-get-started.md
[Crear una instancia de servicio de administración de la API]: api-management-get-started.md#create-service-instance

[Cómo agregar acciones a una API]: api-management-howto-add-operations.md
[Cómo crear y publicar un producto]: api-management-howto-add-products.md
[Cómo crear API]: api-management-howto-create-apis.md
[Cómo configurar opciones de API]: api-management-howto-create-apis.md#configure-api-settings

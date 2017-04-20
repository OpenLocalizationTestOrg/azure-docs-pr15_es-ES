<properties 
    pageTitle="Cómo personalizar el portal de programadores de administración de la API de Azure con plantillas | Microsoft Azure" 
    description="Obtenga información sobre cómo personalizar el portal de programadores de administración de la API de Azure con plantillas." 
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


# <a name="how-to-customize-the-azure-api-management-developer-portal-using-templates"></a>Cómo personalizar el portal de programadores de administración de la API de Azure con plantillas

Administración de Azure API proporciona varias características de personalización que permiten a los administradores [Personalizar la apariencia del portal del desarrollador](api-management-customize-portal.md), así como para personalizar el contenido de las páginas del portal programador mediante un conjunto de plantillas que configure el contenido de las páginas. Con sintaxis [DotLiquid](http://dotliquidmarkup.org/) y un conjunto de recursos de cadena localizados, iconos y controles de la página proporcionado, tiene gran flexibilidad para configurar el contenido de las páginas como le convenga con estas plantillas.

## <a name="developer-portal-templates-overview"></a>Información general de plantillas del portal de desarrollador

Los administradores de la instancia de servicio de administración de la API administra plantillas del portal de desarrollador en el portal de programadores. Para administrar plantillas de desarrollador, vaya a la instancia de servicio de administración de la API en el Portal de clásico de Azure y haga clic en **Examinar**.

![Portal de programadores][api-management-browse]

Si ya están en el portal de publisher, puede acceder al portal de desarrollador haciendo clic en el **portal de programadores**.

![Menú portal de desarrollador][api-management-developer-portal-menu]

Para obtener acceso a las plantillas del portal de desarrollador, haga clic en el icono de personalización de la izquierda para mostrar el menú de personalización y haga clic en **plantillas**.

![Plantillas del portal de desarrollador][api-management-customize-menu]

La lista de plantillas muestra diversas categorías de plantillas cubierta por las distintas páginas en el portal de programadores. Cada plantilla es diferente, pero los pasos para editar y publicar los cambios son iguales. Para editar una plantilla, haga clic en el nombre de la plantilla.

![Plantillas del portal de desarrollador][api-management-templates-menu]

Haga clic en una plantilla le lleva a la página del portal de desarrollador personalizable por esa plantilla. En este ejemplo, la **lista de productos** de la plantilla se muestra. La plantilla de **lista de productos** controla el área de la pantalla indicado por el rectángulo rojo. 

![Plantilla de lista de productos][api-management-developer-portal-templates-overview]

Algunas plantillas, como las plantillas de **Perfil de usuario** , personalizar distintas partes de la misma página. 

![Plantillas de perfil de usuario][api-management-user-profile-templates]

El editor para cada plantilla de portal programador tiene dos secciones que se muestran en la parte inferior de la página. La parte izquierda muestra el panel de edición de la plantilla y la parte derecha muestra el modelo de datos para la plantilla. 

El panel de edición de plantillas contiene el marcado que controla la apariencia y comportamiento de la página correspondiente en el portal de programadores. El marcado en la plantilla usa la sintaxis de la [DotLiquid](http://dotliquidmarkup.org/) . Un editor popular para DotLiquid es [DotLiquid para los diseñadores](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers). Se muestran los cambios realizados en la plantilla durante la edición en tiempo real en el explorador, pero no son visibles para los clientes hasta [Guardar](#to-save-a-template) y [Publicar](#to-publish-a-template) la plantilla.

![Formato de plantilla][api-management-template]

El panel de **datos de la plantilla** proporciona a una guía para el modelo de datos para las entidades que están disponibles para su uso en una plantilla determinada. Proporciona a esta guía mostrando los datos que se muestran actualmente en el portal de programadores. Los paneles de la plantilla se pueden expandir haciendo clic en el rectángulo en la esquina superior derecha del panel de **datos de la plantilla** .

![Modelo de datos de plantilla][api-management-template-data]

En el ejemplo anterior, hay dos productos muestra en el portal de programadores recuperados de los datos que aparecen en el panel de **datos de la plantilla** , tal como se muestra en el ejemplo siguiente.

    {
        "Paging": {
            "Page": 1,
            "PageSize": 10,
            "TotalItemCount": 2,
            "ShowAll": false,
            "PageCount": 1
        },
        "Filtering": {
            "Pattern": null,
            "Placeholder": "Search products"
        },
        "Products": [
            {
                "Id": "56ec64c380ed850042060001",
                "Title": "Starter",
                "Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",
                "Terms": "",
                "ProductState": 1,
                "AllowMultipleSubscriptions": false,
                "MultipleSubscriptionsCount": 1
            },
            {
                "Id": "56ec64c380ed850042060002",
                "Title": "Unlimited",
                "Description": "Subscribers have completely unlimited access to the API. Administrator approval is required.",
                "Terms": null,
                "ProductState": 1,
                "AllowMultipleSubscriptions": false,
                "MultipleSubscriptionsCount": 1
            }
        ]
    }

El marcado en la plantilla de **lista de productos** procesa los datos para proporcionar los resultados deseados al recorrer la colección de productos para mostrar información y un vínculo a cada producto individual. Nota la `<search-control>` y `<page-control>` elementos en el marcado. Estos controlan la visualización de la búsqueda y controles de la página de paginación. `ProductsStrings|PageTitleProducts`es una referencia de cadena adaptada que contiene el `h2` texto del encabezado de la página. Para obtener una lista de cadena de recursos, los controles de la página y los iconos disponibles para usar en plantillas del portal de programador, vea [referencia de plantillas del portal de administración de la API programador](https://msdn.microsoft.com/library/azure/mt697540.aspx).

    <search-control></search-control>
    <div class="row">
        <div class="col-md-9">
            <h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>
        </div>
    </div>
    <div class="row">
        <div class="col-md-12">
        {% if products.size > 0 %}
        <ul class="list-unstyled">
        {% for product in products %}
            <li>
                <h3><a href="/products/{{product.id}}">{{product.title}}</a></h3>
                {{product.description}}
            </li>   
        {% endfor %}
        </ul>
        <paging-control></paging-control>
        {% else %}
        {% localized "CommonResources|NoItemsToDisplay" %}
        {% endif %}
        </div>
    </div>

## <a name="to-save-a-template"></a>Guardar una plantilla

Para guardar una plantilla, haga clic en Guardar en el editor de plantilla.

![Guardar plantilla][api-management-save-template]

Los cambios guardados no están activos en el portal de programadores hasta que se publiquen.

## <a name="to-publish-a-template"></a>Publicar una plantilla

Guardado plantillas se pueden publicar en forma individual o todo el conjunto. Para publicar una plantilla de individual, haga clic en publicar en el editor de plantilla.

![Publicar la plantilla][api-management-publish-template]

Haga clic en **Sí** para confirmar y hacer que la plantilla directo en el portal de programadores.

![Confirmar publicar][api-management-publish-template-confirm]

Para publicar todas las versiones de plantilla actualmente no publicados, haga clic en **Publicar** en la lista de plantillas. Plantillas no publicadas se hayan designado por un asterisco después del nombre de la plantilla. En este ejemplo, se publica las plantillas de **producto** y **lista de productos** .

![Publicar plantillas][api-management-publish-templates]

Haga clic en **publicar personalizaciones** para confirmar.

![Confirmar publicar][api-management-publish-customizations]

Las plantillas recién publicadas son efectivas inmediatamente en el portal de programadores.

## <a name="to-revert-a-template-to-the-previous-version"></a>Para revertir una plantilla a la versión anterior

Para revertir una plantilla a la versión publicada anterior, haga clic en revertir en el editor de plantilla.

![Revertir plantilla][api-management-revert-template]

Haga clic en **Sí** para confirmar.

![Confirmar][api-management-revert-template-confirm]

La versión publicada anteriormente de una plantilla está activa en el portal de programadores una vez completada la operación de revertir.

## <a name="to-restore-a-template-to-the-default-version"></a>Para restaurar una plantilla a la versión predeterminada

La restauración de plantillas a la versión predeterminada es un proceso de dos pasos. En primer lugar deben restaurar las plantillas y, a continuación, se deben publicar las versiones restauradas.

Para restaurar una única plantilla a la versión predeterminada, haga clic en restaurar en el editor de plantilla.

![Revertir plantilla][api-management-reset-template]

Haga clic en **Sí** para confirmar.

![Confirmar][api-management-reset-template-confirm]

Para restaurar todas las plantillas de las versiones de forma predeterminada, haga clic en **Restaurar plantillas predeterminadas** , en la lista de plantillas.

![Restaurar plantillas][api-management-restore-templates]

A continuación, se deben publicar las plantillas restauradas individualmente o a la vez siguiendo los pasos de [publicar una plantilla](#to-publish-a-template).

## <a name="developer-portal-templates-reference"></a>Referencia del programador de plantillas del portal

Para obtener información de referencia para desarrolladores portal plantillas, los recursos de cadena, iconos y controles de la página, vea la [referencia del programador plantillas del portal de administración de la API](https://msdn.microsoft.com/library/azure/mt697540.aspx).

## <a name="watch-a-video-overview"></a>Ver un vídeo de introducción

Vea el siguiente vídeo para ver cómo agregar un panel de discusión y clasificaciones a las páginas de la API y operación en el portal de programadores con plantillas.

> [AZURE.VIDEO adding-developer-portal-functionality-using-templates-in-azure-api-management]


[api-management-customize-menu]: ./media/api-management-developer-portal-templates/api-management-customize-menu.png
[api-management-templates-menu]: ./media/api-management-developer-portal-templates/api-management-templates-menu.png
[api-management-developer-portal-templates-overview]: ./media/api-management-developer-portal-templates/api-management-developer-portal-templates-overview.png
[api-management-template]: ./media/api-management-developer-portal-templates/api-management-template.png
[api-management-template-data]: ./media/api-management-developer-portal-templates/api-management-template-data.png
[api-management-developer-portal-menu]: ./media/api-management-developer-portal-templates/api-management-developer-portal-menu.png
[api-management-browse]: ./media/api-management-developer-portal-templates/api-management-browse.png
[api-management-user-profile-templates]: ./media/api-management-developer-portal-templates/api-management-user-profile-templates.png
[api-management-save-template]: ./media/api-management-developer-portal-templates/api-management-save-template.png
[api-management-publish-template]: ./media/api-management-developer-portal-templates/api-management-publish-template.png
[api-management-publish-template-confirm]: ./media/api-management-developer-portal-templates/api-management-publish-template-confirm.png
[api-management-publish-templates]: ./media/api-management-developer-portal-templates/api-management-publish-templates.png
[api-management-publish-customizations]: ./media/api-management-developer-portal-templates/api-management-publish-customizations.png
[api-management-revert-template]: ./media/api-management-developer-portal-templates/api-management-revert-template.png
[api-management-revert-template-confirm]: ./media/api-management-developer-portal-templates/api-management-revert-template-confirm.png
[api-management-reset-template]: ./media/api-management-developer-portal-templates/api-management-reset-template.png
[api-management-reset-template-confirm]: ./media/api-management-developer-portal-templates/api-management-reset-template-confirm.png
[api-management-restore-templates]: ./media/api-management-developer-portal-templates/api-management-restore-templates.png








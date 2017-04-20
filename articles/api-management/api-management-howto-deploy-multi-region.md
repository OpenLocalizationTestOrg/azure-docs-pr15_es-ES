<properties
    pageTitle="Cómo implementar una instancia de servicio de administración de la API de Azure en varias áreas de Azure"
    description="Obtenga información sobre cómo implementar una instancia de servicio de administración de la API de Azure en varias áreas de Azure." 
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

# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>Cómo implementar una instancia de servicio de administración de la API de Azure en varias áreas de Azure

Administración de la API admite la implementación de región múltiples que permite editores de API distribuir un único servicio de administración de API en cualquier número de áreas de Azure deseadas. Esto ayuda a reducir la solicitud de latencia observar geográfico distribuye los consumidores de API y también mejora la disponibilidad de servicio si se desconecta una región. 

Cuando se crea inicialmente un servicio de administración de la API, contiene solo una [unidad][] y se encuentra en una sola región Azure está designada como principal región. Pueden agregarse fácilmente regiones adicionales a través del Portal de Azure clásico. Servidor de administración de la API de puerta de enlace se implementa en cada región y se enrutar el tráfico de llamada a la puerta de enlace más cercano. Si una región se desconecta, el tráfico es automáticamente redirigen a la siguiente puerta de enlace más cercano. 

> [AZURE.IMPORTANT] Implementación de múltiples región solo está disponible en el nivel **[Premium][]** .

## <a name="add-region"> </a>Implementar una instancia de servicio de administración de la API a una nueva área

Para empezar, haga clic en **Administrar** en el Portal de Azure clásico de su servicio de administración de la API. Se abrirá el portal de administración de la API de publisher.

![Portal de Publisher][api-management-management-console]

>Si aún no ha creado una instancia de servicio de administración de la API, vea [crear una instancia de servicio de administración de la API][] en el tutorial de [Introducción a la administración de la API de Azure][] .

Vaya a la pestaña **escala** en Azure clásica Portal de la instancia de servicio de administración de la API. 

![Pestaña escala][api-management-scale-service]

Para implementar en una región nueva, haga clic en la lista desplegable situada debajo de la región principal y elija una región de la lista.

![Agregar región][api-management-add-region]

Una vez seleccionado el área, seleccione el número de unidades para la nueva área de la lista desplegable de unidad.

![Especificar las unidades][api-management-select-units]

Una vez que se haya configurado las regiones que quiera y las unidades, haga clic en **Guardar**.

## <a name="remove-region"> </a>Eliminar una instancia de servicio de administración de la API de una región

Para quitar una instancia de servicio de administración de la API de una región, vaya a la pestaña **escala** en Azure clásica Portal de la instancia de servicio de administración de la API. 

![Pestaña escala][api-management-scale-service]

Haga clic en la **X** a la derecha de la región que desee quitar.  

![Quitar área][api-management-remove-region]

Una vez que se quitan las regiones que desee, haga clic en **Guardar**.


[api-management-management-console]: ./media/api-management-howto-deploy-multi-region/api-management-management-console.png

[api-management-scale-service]: ./media/api-management-howto-deploy-multi-region/api-management-scale-service.png
[api-management-add-region]: ./media/api-management-howto-deploy-multi-region/api-management-add-region.png
[api-management-select-units]: ./media/api-management-howto-deploy-multi-region/api-management-select-units.png
[api-management-remove-region]: ./media/api-management-howto-deploy-multi-region/api-management-remove-region.png

[Crear una instancia de servicio de administración de la API]: api-management-get-started.md#create-service-instance
[Introducción a la administración de la API de Azure]: api-management-get-started.md

[Deploy an API Management service instance to a new region]: #add-region
[Delete an API Management service instance from a region]: #remove-region

[unidad]: http://azure.microsoft.com/pricing/details/api-management/
[Premium]: http://azure.microsoft.com/pricing/details/api-management/


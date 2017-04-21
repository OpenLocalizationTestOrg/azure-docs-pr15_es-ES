<properties
    pageTitle="Crear un servicio de búsqueda de Azure con el Portal de Azure | Microsoft Azure | Servicio de nube hospedado de búsqueda"
    description="Obtenga información sobre cómo aprovisionar un servicio de búsqueda de Azure con el Portal de Azure."
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

# <a name="create-an-azure-search-service-using-the-azure-portal"></a>Crear un servicio de búsqueda de Azure con el Portal de Azure

Esta guía le guiará en el proceso de creación (o aprovisionamiento) un servicio de búsqueda de Azure con el [Portal de Azure](https://portal.azure.com/).

Esta guía se supone que ya tiene una suscripción de Azure y puede iniciar sesión en el Portal de Azure.

## <a name="find-azure-search-in-the-azure-portal"></a>Buscar búsqueda Azure en el Portal de Azure
1. Vaya al [Portal de Azure](https://portal.azure.com/) e inicie sesión.
1. Haga clic en el signo más ("+") en la esquina superior izquierda.
2. Seleccionar **datos + almacenamiento**.
3. Seleccione **Buscar Azure**.

![](./media/search-create-service-portal/find-search.png)

## <a name="pick-a-service-name-and-url-endpoint-for-your-service"></a>Elija un nombre de servicio y el punto final de la dirección URL del servicio
1. Nombre de su servicio será parte de la dirección URL del extremo del servicio de búsqueda de Azure en el que hará las llamadas de API para administrar y usar el servicio de búsqueda.
2. Escriba su nombre de servicio en el campo **URL** . El nombre de servicio:
  * solo debe contener letras minúsculas, números o guiones ("-")
  * no se puede utilizar un guión ("-") como el primero 2 caracteres o el último carácter
  * no pueden contener guiones consecutivos ("--")
  * se limita entre 2 y 60 caracteres de longitud


## <a name="select-a-subscription-where-you-will-keep-your-service"></a>Seleccione una suscripción donde guardará su servicio
Si tiene más de una suscripción, puede seleccionar cuál incluirá este servicio de búsqueda de Azure.

## <a name="select-a-resource-group-for-your-service"></a>Seleccione un grupo de recursos para el servicio
Crear un nuevo grupo de recursos o seleccione uno existente. Un grupo de recursos es una colección de recursos que se usan de manera conjunta y servicios de Azure. Por ejemplo, si está utilizando búsqueda de Azure indizar una base de datos SQL, ambos servicios deben ser parte del mismo grupo de recursos.

## <a name="select-the-location-where-your-service-will-be-hosted"></a>Seleccione la ubicación donde se va a hospedar el servicio
Como un servicio de Azure, búsqueda de Azure está disponible para estar hospedadas en centros de datos de todo el mundo. Tenga en cuenta que [los precios pueden ser distintas](https://azure.microsoft.com/pricing/details/search/) por región geográfica.

## <a name="select-your-pricing-tier"></a>Seleccione el nivel de precios
[Búsqueda de Azure se ofrece actualmente en varios niveles de precios](https://azure.microsoft.com/pricing/details/search/): libre, básica o estándar. Cada nivel tiene su propia [capacidad y límites](search-limits-quotas-capacity.md). Vea [Elegir un nivel de precios o SKU](search-sku-tier.md) para obtener instrucciones.

En este caso, hemos elegido el nivel estándar para nuestro servicio.

## <a name="select-the-create-button-to-provision-your-service"></a>Seleccione el botón "Crear" proporcionando su servicio

![](./media/search-create-service-portal/create-service.png)

## <a name="scale-your-service"></a>Ajustar el tamaño de su servicio

Después de que se suministra su servicio, puede escalar para satisfacer sus necesidades. Si ha elegido el nivel estándar de su servicio de búsqueda de Azure, puede ajustar su servicio en dos dimensiones: réplicas y particiones. Si ha elegido el nivel básico, sólo puede agregar réplicas.

*__Las particiones__* permiten su servicio almacenar y buscar en documentos más.

*__Réplicas__* permitir que el servicio controle una carga mayor de consultas de búsqueda - [un servicio requiere 2 réplicas para lograr un SLA de solo lectura y 3 réplicas para lograr un SLA de lectura y escritura](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

1. Vaya a módulo de administración del servicio de búsqueda de Azure en el Portal de Azure.
2. En el módulo de **configuración** , seleccione **escala**.
3. Puede ajustar el tamaño de su servicio agregando réplicas o particiones.
  * No puede escalar su servicio más allá de la búsqueda 36 unidades. El número total de unidades de búsqueda es el producto de sus réplicas y particiones (réplicas * particiones = Total de unidades de búsqueda).
  * Si ha elegido el nivel básico, solo puede escalar a 3 réplicas. Servicios básicos se enlazan a una sola partición.

![](./media/search-create-service-portal/scale-service.png)

## <a name="next"></a>Siguiente
Si ya dispone de un servicio de búsqueda de Azure, estará listo para [definir un índice de búsqueda de Azure](search-what-is-an-index.md) para poder cargar y buscar los datos.

Para obtener un tutorial rápido, vea [Introducción a la búsqueda de Azure en el portal](search-get-started-portal.md) .

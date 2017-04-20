<properties
    pageTitle="Introducción exhaustiva de planes de servicio de aplicación de Azure | Microsoft Azure"
    description="Obtenga información sobre cómo planes de servicio de aplicaciones para el trabajo del servicio de aplicación de Azure y cómo estas benefician a su experiencia de administración."
    keywords="aplicación de servicio, azure aplicación servicio, plan de servicios de aplicación scalable, escala, costo de servicio de aplicación"
    services="app-service"
    documentationCenter=""
    authors="btardif"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/13/2016"
    ms.author="byvinyal"/>

# <a name="azure-app-service-plans-in-depth-overview"></a>Introducción exhaustiva de planes de servicio de aplicación de Azure#

Un plan de servicio de aplicación representa un conjunto de características y la capacidad que puede compartir entre varias aplicaciones. Aplicaciones Web, aplicaciones móviles, aplicaciones de la función o aplicaciones de la API, en todos los [Servicios de aplicación de Azure](http://go.microsoft.com/fwlink/?LinkId=529714) ejecutan en un plan de servicio de aplicación. Estos planes de soporte técnico de cinco niveles de precios: *libre*, *compartido*, *básico*, *estándar*y *Premium*. Cada nivel tiene su propio posibilidades y la capacidad. Aplicaciones de la misma suscripción y la ubicación geográfica pueden compartir un plan. Pueden utilizar todas las aplicaciones que comparten un plan de todas las funciones y características que están definidas por el nivel del plan. Todas las aplicaciones que están asociadas a un plan de ejecutan en los recursos que define el plan.

Por ejemplo, si su plan está configurado para usar dos instancias "pequeñas" en el nivel de servicio estándar, todas las aplicaciones que están asociadas a ese plan ejecutan en ambos casos y tienen acceso a la funcionalidad de nivel de servicio estándar. Instancias de plan en la que se están ejecutando aplicaciones son totalmente administrados y altamente disponibles.

En este artículo trata las características claves, como nivel y escala, de un plan de servicio de la aplicación y cómo entran en juego mientras se administran las aplicaciones.

## <a name="apps-and-app-service-plans"></a>Planes de servicio de aplicaciones y aplicaciones

Una aplicación de servicio de la aplicación se puede asociar con solo un plan de servicio de aplicaciones en un momento dado.

Aplicaciones y planes están contenidos en un grupo de recursos. Un grupo de recursos sirve como el límite de ciclo de vida para cada recurso que se encuentra dentro de ella. Puede usar grupos de recursos para administrar todas las partes de una aplicación de forma conjunta.

Como un único grupo de recursos puede tener varios planes de servicio de la aplicación, puede asignar diferentes aplicaciones de recursos físicos diferentes. Por ejemplo, puede separar los recursos entre entornos de desarrollo, prueba y de producción. Tener entornos independientes de producción y desarrollo/prueba le permite aislar los recursos. De esta forma, pruebas de carga con una versión nueva de las aplicaciones no competir por los mismos recursos que las aplicaciones de producción, que son servir a clientes reales.

Cuando haya varios planes en un único grupo de recursos, también puede definir una aplicación que se extiende por regiones geográficas. Por ejemplo, una aplicación altamente disponible que se ejecuta en dos regiones incluye al menos dos planes, uno para cada región y asociados con cada plan de una aplicación. En este caso, todas las copias de la aplicación, a continuación, se encuentran en un único grupo de recursos. Tener un grupo de recursos con varios planes y varias aplicaciones facilita administrar, controlar y ver el estado de la aplicación.

## <a name="create-an-app-service-plan-or-use-existing-one"></a>Crear un plan de servicio de aplicación o usar uno existente

Cuando se crea una aplicación, considere la posibilidad de crear un grupo de recursos. Por otro lado, si la aplicación que va a crear es un componente de una aplicación de mayor tamaño, esta aplicación debe crearse dentro del grupo de recursos asignado para la aplicación más grande.

Si la nueva aplicación es una aplicación totalmente nueva o parte de una más grande, puede usar un plan de servicio de aplicación existente para hospedar, o cree uno nuevo. Esta decisión es más una pregunta de capacidad y la carga esperada.

Si esta nueva aplicación se va a usar muchos recursos y disponen de diferentes factores desde otras aplicaciones de escala alojado en un plan existente, se recomienda aislarla en su propio plan.

Al crear un plan, puede asignar un nuevo conjunto de recursos de la aplicación y tener mayor control sobre la asignación de recursos porque cada plan obtiene su propio conjunto de instancias.

Dado que puede mover aplicaciones a través de planes, puede cambiar la manera en que se asignan los recursos a través de la aplicación más grande.

Por último, si desea crear una aplicación en una región diferente y esa región no tiene un plan existente, cree un plan de esa región para poder alojar su aplicación allí.

## <a name="create-an-app-service-plan"></a>Crear un plan de servicio de aplicación

>[AZURE.TIP] Si tiene un entorno de servicio de la aplicación puede consultar la documentación específica de aplicación de servicio entornos aquí: [crear un Plan de servicio de la aplicación en un entorno de servicio de aplicación](../app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan)

Puede crear un plan de servicio de aplicación vacío de la experiencia de examinar del plan de servicio de aplicaciones o como parte de la creación de la aplicación.

En el [portal de Azure](https://portal.azure.com), haga clic en **nuevo** > **Web + mobile**y, a continuación, seleccione **Web App** u otro tipo de aplicación de servicio de aplicación.
![Crear una aplicación en el portal de Azure.][createWebApp]

A continuación, puede seleccionar o crear el plan de servicio de aplicaciones para la nueva aplicación.

 ![Crear un plan de servicio de aplicación.][createASP]

Para crear un nuevo plan de servicio de aplicaciones, haga clic en **[+] Crear nueva**, escriba el nombre del **plan de servicio de aplicaciones** y, a continuación, seleccione una **ubicación**de adecuada. Haga clic en **el nivel de precios**y, a continuación, seleccione un nivel de precio adecuado para el servicio. Seleccione **Ver todos** para ver más opciones de precios, como **Free** y **compartido**. Una vez que haya seleccionado el nivel de precios, haga clic en el botón **Seleccionar** .

## <a name="move-an-app-to-a-different-app-service-plan"></a>Mover una aplicación a otro plan de servicio de aplicaciones

Puede mover una aplicación a un plan de servicios de aplicación diferentes en el [portal de Azure](https://portal.azure.com). Puede mover aplicaciones entre planes como los planes están en el mismo grupo de recursos y la región geográfica.

Para mover una aplicación a otro plan, vaya a la aplicación que desea mover. En el menú **configuración** , busque el **Plan de cambio de aplicación de servicio**.

**Cambio de Plan de servicios de aplicación** abre el selector de **plan de servicios de aplicación** . En este momento, puede elegir un plan existente o cree uno nuevo. Se muestran solo válidos planes (en el mismo grupo de recursos y la ubicación geográfica).

![Selector de plan de servicio de aplicación.][change]

Cada plan tiene su propio nivel de precios. Por ejemplo, al mover un sitio desde un nivel gratuito a un nivel estándar, la aplicación ahora puede utilizar todas las características y recursos de la capa estándar.

## <a name="clone-an-app-to-a-different-app-service-plan"></a>Clonar una aplicación a otro plan de servicio de aplicaciones
Si desea mover la aplicación a una región diferente, una alternativa es la aplicación clonar. Clonar hace una copia de la aplicación en un entorno de servicio de la aplicación en cualquier región o el plan de servicio de la aplicación nuevo o existente.

 ![Duplicar una aplicación.][appclone]

Puede encontrar la **Aplicación de clonar** en el menú **Herramientas** .

Clonar tiene algunas limitaciones que puede leer sobre en la [aplicación de servicio de aplicación de Azure clonar con Azure portal](../app-service-web/app-service-web-app-cloning-portal.md).

## <a name="scale-an-app-service-plan"></a>Ajustar el tamaño de un plan de servicio de aplicación

Hay tres maneras de ajustar el tamaño de un plan:

- **Cambiar el plan de precios nivel**. Por ejemplo, un plan en el nivel básico se puede convertir a un nivel estándar o Premium y todas las aplicaciones que están asociadas a ese plan ahora pueden usar las características que ofrece el nuevo nivel de servicio.
- **Cambiar el tamaño de la instancia del plan**. Por ejemplo, un plan en el nivel básico que usa instancias pequeñas puede cambiarse para utilizar instancias de gran tamaño. Todas las aplicaciones que están asociadas a ese plan ahora pueden usar la memoria adicional y recursos de la CPU que ofrece el tamaño de la instancia más grande.
- **Cambiar el número de instancias del plan**. Por ejemplo, un plan estándar que se ha escalado a tres instancias puede ampliarse a 10 instancias. Un plan de Premium puede escalar a 20 instancias (sujeto a disponibilidad). Todas las aplicaciones que están asociadas a ese plan ahora pueden usar la memoria adicional y recursos de la CPU que ofrece el recuento de la instancia más grande.

Puede cambiar el tamaño de nivel e instancia precio haciendo clic en el elemento **De la escala** en configuración de la aplicación o el plan de servicios de aplicación. Los cambios se aplican al plan de servicio de la aplicación y afecta a todas las aplicaciones que aloja.

 ![Establecer los valores para escalar una aplicación.][pricingtier]

## <a name="app-service-plan-cleanup"></a>Limpieza de Plan de servicio de aplicación
**Planes de servicio de aplicación** que no tienen aplicaciones asociadas a ellos aún incurre en gastos ya que siguen reservar la capacidad de cálculo configurada en las propiedades de escala del plan de servicio de aplicación.
Para evitar gastos inesperados, cuando se elimina la última aplicación alojada en un plan de servicio de la aplicación, también se elimina el plan de servicios de aplicación vacío resultante.


## <a name="summary"></a>Resumen

Planes de servicio de aplicación representan un conjunto de características y la capacidad que puede compartir entre las aplicaciones. Planes de servicio de la aplicación le ofrecen flexibilidad para asignar aplicaciones específicas a un conjunto de recursos y optimizar aún más el uso de recursos de Azure. De este modo, si desea guardar el dinero en su entorno de prueba, puede compartir un plan en varias aplicaciones. También puede maximizar el rendimiento en su entorno de producción escala entre distintas regiones y planes.

## <a name="whats-changed"></a>¿Qué ha cambiado

* Para obtener una guía para el cambio de sitios Web a la aplicación de servicio, vea: [servicio de aplicación de Azure y su influencia sobre los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)

[pricingtier]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/appserviceplan-pricingtier.png
[assign]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/assing-appserviceplan.png
[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
[appclone]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/app-clone.png

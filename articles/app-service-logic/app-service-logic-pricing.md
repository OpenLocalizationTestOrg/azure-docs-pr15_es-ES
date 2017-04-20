<properties 
    pageTitle="Modelo de precios de aplicaciones de lógica | Microsoft Azure" 
    description="Detalles sobre el funcionamiento de precios en las aplicaciones de lógica" 
    authors="kevinlam1" 
    manager="dwrede" 
    editor="" 
    services="logic-apps" 
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article" 
    ms.date="10/12/2016"
    ms.author="klam"/>

# <a name="logic-apps-pricing-model"></a>Modelo de precios de aplicaciones de lógica

Aplicaciones de lógica de Azure le permite escalar y ejecutar flujos de trabajo de integración en la nube.  A continuación encontrará detalles sobre los planes de precios para aplicaciones de lógica y la facturación.

## <a name="consumption-pricing"></a>Precios de consumo

Recién creado aplicaciones lógica usar un plan de consumo. Con el modelo de precios de consumo de aplicaciones de lógica, solo paga por lo que usa.  Aplicaciones de lógica no están limitadas cuando se usa un plan de consumo.
Todas las acciones que se ejecutan de una instancia de aplicación lógica se uso medidas.

### <a name="what-are-action-executions"></a>¿Cuáles son las ejecuciones acción?

Cada paso en una definición de aplicación lógica es una acción.  Esto incluye desencadenadores, los pasos de flujo de control igual condiciones, ámbitos para cada bucles hasta bucles, llamadas a conectores y acciones nativa.
Desencadenadores son acciones solo especiales que están diseñados para crear una nueva instancia de una aplicación de lógica cuando se produce un evento determinado.  Hay una serie de distintos comportamientos de desencadenadores que podrían afectar a cómo se mide la aplicación lógica.

-   **Sondeo de desencadenador** : este desencadenador continuamente explora un extremo hasta que recibe un mensaje que cumpla los criterios para crear una nueva instancia de una aplicación de lógica.  Puede configurar el intervalo de sondeo del desencadenador en el Diseñador de aplicaciones de lógica.  Cada solicitud de sondeo, incluso si no se creará una nueva instancia de una aplicación de la lógica se cuenta como la ejecución de una acción.

-   **Webhook desencadenador** : este desencadenador que espera un cliente enviar una solicitud en un extremo concreto.  Cada solicitud enviado al extremo webhook cuenta como la ejecución de una acción. La solicitud y el desencadenador HTTP Webhook son ambos desencadenadores webhook.

-   **Desencadenador de periodicidad** : este desencadenador creará una nueva instancia de la aplicación de lógica basada en el intervalo de repetición configurado en el desencadenador.  Por ejemplo, un desencadenador de periodicidad puede estar configurado para ejecutar cada 3 días o incluso cada minuto.

Desencadenador ejecuciones pueden verse en el módulo de recursos de aplicaciones de lógica en el elemento del historial de desencadenador.

Todas las acciones que se han ejecutado, si fueran éxito o error se mide como la ejecución de una acción.  Acciones que se omitieron debido a una condición que no se cumplen o que no se ejecute debido a la aplicación de lógica finalizó antes de completarse no se cuentan como ejecuciones de acción.

Acciones que se ejecuta dentro de bucles se cuentan por iteración del bucle.  Por ejemplo, una sola acción en una para cada iteración de bucle a través de una lista de los 10 elementos se van a contar como el recuento de elementos de la lista (10) multiplicado por el número de acciones en el bucle (1) y otro para el inicio del bucle que, en este ejemplo, sería (10 * 1) + 1 = 11 ejecuciones de acción.

Lógica de aplicaciones que están deshabilitados no puede tener instancias que se crearon y, por tanto, durante el tiempo que están deshabilitados no obtener cobrará.  Tenga en cuenta que después de deshabilitar una aplicación lógica puede tardar un poco tiempo para las instancias de poner en modo inactivo antes de que se han deshabilitado completamente.

## <a name="app-service-plans"></a>Planes de servicio de aplicación

Planes de servicio de aplicación ya no son necesarios para crear una aplicación de lógica.  También puede hacer referencia a un Plan de servicio de la aplicación con una aplicación de lógica existente.  Aplicaciones de lógica creadas previamente con un Plan de servicio de aplicación seguirá se comportan de modo que antes cuando, según el plan elegida, se recibe acelerado después de un número de ejecuciones diarias se supera y no se cargará con el indicador de ejecución de acción.

Planes de servicio de aplicación y sus diarias ejecuciones permitido:

| |Liberar, compartido o Basic|Estándar|Premium|
|---|---|---|---|
|Ejecuciones de acción por día| 200|10.000|50.000|

### <a name="convert-from-consumption-to-app-service-plan-pricing"></a>Convertir de consumo a precios Plan de servicio de aplicación

Para hacer referencia a un Plan de servicio de aplicación para un aplicación de lógica de consumo, puede simplemente [ejecutar el debajo de script de PowerShell](https://github.com/logicappsio/ConsumptionToAppServicePlan).  Asegúrese de que tiene instaladas las [Herramientas de PowerShell de Azure](https://github.com/Azure/azure-powershell) en primer lugar.

``` powershell
Param(
    [string] $AppService_RG = '<app-service-resource-group>',
    [string] $AppService_Name = '<app-service-name>',
    [string] $LogicApp_RG = '<logic-app-resource-group>',
    [string] $LogicApp_Name = '<logic-app-name>',
    [string] $subscriptionId = '<azure-subscription-id>'
)

Login-AzureRmAccount 
$subscription = Get-AzureRmSubscription -SubscriptionId $subscriptionId
$appserviceplan = Get-AzureRmResource -ResourceType "Microsoft.Web/serverFarms" -ResourceGroupName $AppService_RG -ResourceName $AppService_Name
$logicapp = Get-AzureRmResource -ResourceType "Microsoft.Logic/workflows" -ResourceGroupName $LogicApp_RG -ResourceName $LogicApp_Name

$sku = @{
    "name" = $appservicePlan.Sku.tier;
    "plan" = @{
      "id" = $appserviceplan.ResourceId;
      "type" = "Microsoft.Web/ServerFarms";
      "name" = $appserviceplan.Name  
    }
}

$updatedProperties = $logicapp.Properties | Add-Member @{sku = $sku;} -PassThru

$updatedLA = Set-AzureRmResource -ResourceId $logicapp.ResourceId -Properties $updatedProperties -ApiVersion 2015-08-01-preview
```

### <a name="convert-from-app-service-plan-pricing-to-consumption"></a>Convertir de Plan de servicio de aplicación precios al consumo

Para cambiar una aplicación de lógica que tiene un Plan de servicio de aplicación asociado a un modelo de consumo quite la referencia para el Plan de servicio de aplicación en la definición de aplicación de la lógica.  Esto puede hacerse con una llamada a un cmdlet de PowerShell:

`Set-AzureRmLogicApp -ResourceGroupName ‘rgname’ -Name ‘wfname’ –UseConsumptionModel -Force`

## <a name="pricing"></a>Precios

Para detalles de los precios, consulte [Precios de lógica de aplicaciones](https://azure.microsoft.com/pricing/details/logic-apps/).

## <a name="next-steps"></a>Pasos siguientes

- [Información general sobre aplicaciones de lógica][whatis]
- [Crear su primera aplicación de lógica][create]

[pricing]: https://azure.microsoft.com/pricing/details/logic-apps/
[whatis]: app-service-logic-what-are-logic-apps.md
[create]: app-service-logic-create-a-logic-app.md


<properties 
    pageTitle="Mover los recursos al nuevo grupo de recursos | Microsoft Azure" 
    description="Use el Administrador de recursos de Azure para mover los recursos a un nuevo grupo de recursos o suscripción." 
    services="azure-resource-manager" 
    documentationCenter="" 
    authors="tfitzmac" 
    manager="timlt" 
    editor="tysonn"/>

<tags 
    ms.service="azure-resource-manager" 
    ms.workload="multiple" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/21/2016" 
    ms.author="tomfitz"/>

# <a name="move-resources-to-new-resource-group-or-subscription"></a>Mover los recursos al nuevo grupo de recursos o suscripción

En este tema se muestra cómo mover recursos a una nueva suscripción o un nuevo grupo de recursos en la misma suscripción. Puede usar el portal, PowerShell, Azure CLI o la API de REST para mover los recursos. Las operaciones de movimiento en este tema están disponibles para usted sin asistencia del soporte técnico de Azure.

Normalmente, mover recursos cuando decide que:

- Fines de facturación, un recurso necesita live en una suscripción diferente.
- Un recurso ya no comparte el ciclo de vida del mismo como los recursos con que previamente agrupado. Desea mover a un nuevo grupo de recursos para que pueda administrar dicho recurso por separado de los otros recursos.

Al mover los recursos, tanto los grupos de origen y el destino se bloquean durante la operación. Escribir y eliminar operaciones están bloqueados en los grupos hasta que se complete el movimiento.

No puede cambiar la ubicación del recurso. Mover un recurso solo lo mueve a un nuevo grupo de recursos. El nuevo grupo de recursos puede tener una ubicación diferente, pero no cambia la ubicación del recurso.

> [AZURE.NOTE] Este artículo describe cómo mover recursos dentro de una existente Azure cuenta oferta. Si realmente desea cambiar la cuenta de Azure ofreciendo (por ejemplo, la actualización de pago por uso para pagar por adelantado) mientras trabaja con los recursos existentes, consulte [cambiar la suscripción a otra oferta Azure](billing-how-to-switch-azure-offer.md). 

## <a name="checklist-before-moving-resources"></a>Lista de comprobación antes de mover los recursos

Hay algunos pasos importantes que debe realizar antes de mover un recurso. Al comprobar estas condiciones, puede evitar errores.

1. El servicio debe habilitar la capacidad de mover los recursos. Vea la siguiente lista para obtener información sobre qué [Servicios Habilitar movimiento de recursos](#services-that-enable-move).
1. Las suscripciones de origen y de destino deben existir en el mismo [inquilino de Active Directory](./active-directory/active-directory-howto-tenant.md). Para mover a un nuevo inquilino, llame al soporte técnico.
2. La suscripción de destino debe estar registrada para el proveedor de recursos del recurso que se mueve. Si no es así, recibe un error que indica que la **suscripción no está registrada para un tipo de recurso**. Este problema puede que encuentre al mover un recurso a una nueva suscripción, pero que nunca se ha usado suscripción con ese tipo de recurso. Para obtener información sobre cómo comprobar el estado de registro y registrar proveedores de recursos, consulte [tipos y proveedores de recursos](../resource-manager-supported-services.md#resource-providers-and-types).
4. Si se mueve la aplicación de servicio de aplicaciones, revisar [las limitaciones de la aplicación de servicio](#app-service-limitations).
4. Si está moviendo recursos asociados con los servicios de recuperación, revisar las [limitaciones de los servicios de recuperación](#recovery-services-limitations)
5. Si está moviendo recursos implementados a través de modelo clásico, revisar [las limitaciones de implementación de clásico](#classic-deployment-limitations).

## <a name="when-to-call-support"></a>Al llamar al soporte técnico

Puede mover la mayoría de los recursos a través de las operaciones de autoservicio que se muestra en este tema. Utilizar las operaciones de autoservicio para:

- Mover los recursos de administrador de recursos.
- Mover recursos clásicos según las [limitaciones de implementación clásico](#classic-deployment-limitations). 

Llame al soporte técnico cuando necesite:

- Mover los recursos a una nueva cuenta de Azure (y el inquilino de Active Directory).
- Mover recursos clásicos, pero tiene problemas con las limitaciones.

## <a name="services-that-enable-move"></a>Servicios que permiten mover

Por ahora, los servicios que permiten mover a un nuevo grupo de recursos y la suscripción son:

- Administración de la API
- Aplicaciones de servicio de la aplicación (aplicaciones web) - vea [limitaciones de la aplicación de servicio](#app-service-limitations)
- Automatización
- Proceso por lotes
- CDN
- Servicios de nube: consulte [limitaciones de implementación estándar](#classic-deployment-limitations)
- Generador de datos
- DNS
- DocumentDB
- HDInsight clústeres
- IoT Hubs
- Depósito clave 
- Media Services
- Compromiso de móvil
- Notificación Hubs
- Perspectivas operativas
- Redis caché
- Programador
- Búsqueda
- Bus de servicio
- Almacenamiento de información
- Almacenamiento (clásico): vea [limitaciones de la implementación de clásico](#classic-deployment-limitations)
- Servidor de base de datos de SQL: la base de datos y el servidor debe residen en el mismo grupo de recursos. Al mover un servidor SQL server, también se mueven todas sus bases de datos.
- Máquinas virtuales - sin embargo, sirve no admite mover a una nueva suscripción cuando sus certificados se almacenan en un depósito de clave
- Máquinas virtuales (clásico): vea [limitaciones de la implementación de clásico](#classic-deployment-limitations)
- Redes virtuales

## <a name="services-that-do-not-enable-move"></a>Servicios que no se permiten mover

Los servicios que actualmente no se permiten mover un recurso son:

- Puerta de enlace de aplicación
- Información de la aplicación
- Ruta de Express
- Servicios de recuperación de depósito - también no mover los recursos de proceso, red y almacenamiento asociados con la cámara de servicios de recuperación, vea [limitaciones de los servicios de recuperación](#recovery-services-limitations).
- Máquinas virtuales con almacenados en depósito de clave de certificado
- Conjuntos de escala de máquinas virtuales
- Redes virtuales (clásico): vea [limitaciones de la implementación de clásico](#classic-deployment-limitations)
- Puerta de enlace VPN

## <a name="app-service-limitations"></a>Limitaciones de servicio de aplicaciones

Al trabajar con aplicaciones de servicio de aplicaciones, no puede mover solo un plan de servicio de aplicación. Para mover las aplicaciones de servicio de aplicación, las opciones son:

- Mover el plan de servicio de aplicaciones y otros recursos de servicio de la aplicación de ese grupo de recursos a un nuevo grupo de recursos que ya no tiene recursos del servicio de aplicación. Este requisito significa que debe mover incluso los recursos de servicio de aplicaciones que no están asociados con el plan de servicios de aplicación. 
- Mover las aplicaciones a otro grupo de recursos, pero mantener todos los planes de servicio de aplicaciones en el grupo de recursos original.

Si el grupo de recursos original también incluye un recurso de información de la aplicación, no se puede mover ese recurso porque perspectivas de aplicación no permite la operación de movimiento actualmente. Si incluye el recurso perspectivas de aplicación al mover aplicaciones de servicio de aplicación, se produce un error en la operación de mover todo. Sin embargo, el plan de servicio de aplicaciones y perspectivas de aplicación no es necesario residen en el mismo grupo de recursos que la aplicación para la aplicación funcione correctamente.

Por ejemplo, si el grupo de recursos contiene:

- **un Web** que está asociado con **un plan** y **aplicación perspectivas a**
- **web-b** que está asociado con el **plan b** y **b de perspectivas de aplicación**

Las opciones son:

- Mover **a la web**, **un plan**, **web-b**y **b de plan**
- Mover **web a** y **b de web**
- Mover **a la web**
- Mover **web-b**

Todas las demás combinaciones implican movimiento un tipo de recurso que no se puede mover (aplicación perspectivas) o dejando el tipo de recurso que no se quede al mover un plan de servicio de la aplicación (cualquier tipo de recurso de servicio de aplicaciones).

Si la aplicación web reside en otro grupo de recursos que su plan de servicio de aplicaciones, pero desea mover a un nuevo grupo de recursos, debe realizar el movimiento en dos pasos. Por ejemplo:

- **un Web** reside en **grupo en la web**
- **un plan** reside en **grupo de plan**
- Desea **un web** y **un plan** a residen en el **grupo combinar**

Para realizar este cambio, realizar dos operaciones independientes mover en la siguiente secuencia:

1. Mover la **web, un** **plan de** grupo
2. Mover **a la web** y **un plan** a **grupo combinado**.

Puede mover un certificado de servicio de aplicación a un nuevo grupo de recursos o suscripción sin ningún problema. Sin embargo, si la aplicación web incluye un certificado SSL que compró externamente y cargado en la aplicación, debe eliminar el certificado antes de pasar a la aplicación web. Por ejemplo, puede realizar los siguientes pasos:

1. Eliminar el certificado de Subir desde la aplicación web
2. Mover la aplicación web
3. Cargar el certificado en la aplicación web

## <a name="recovery-services-limitations"></a>Limitaciones de los servicios de recuperación

Mover no está habilitado para el almacenamiento de red, o calcular los recursos que se usó para configurar la recuperación de recuperación de sitios de Azure. 

Por ejemplo, suponga que ha configurado la replicación de sus equipos locales para una cuenta de almacenamiento: (Storage1) y desea que el equipo protegido para dar después de la migración a Azure como una máquina virtual (VM1) conectada a una red virtual (red1). No se puede mover cualquiera de estos recursos Azure: Storage1, VM1 y red1 - entre los grupos de recursos en la misma suscripción o a través de suscripciones.

## <a name="classic-deployment-limitations"></a>Limitaciones de implementación clásica

Las opciones para mover recursos implementados a través del modelo clásico varían en función de si está moviendo los recursos en una suscripción o a una nueva suscripción. 

### <a name="same-subscription"></a>Misma suscripción

Al mover recursos de un grupo de recursos a otro grupo de recursos en la misma suscripción, se aplican las siguientes restricciones:

- No se puede mover redes virtuales (clásicas).
- Debe mover máquinas virtuales de Windows (clásico) con el servicio de nube. 
- Servicio de nube solo se puede mover cuando el movimiento incluye todas sus máquinas virtuales.
- Servicio de solo nube se puede mover a la vez.
- Solo una cuenta de almacenamiento (clásica) se puede mover a la vez.
- No se puede mover la cuenta de almacenamiento (clásico) en la misma operación con una máquina virtual o un servicio de nube.

Para mover recursos clásicos a un nuevo grupo de recursos en la misma suscripción, utilice las operaciones de movimiento estándar a través del [portal](#use-portal), [Azure PowerShell](#use-powershell), [CLI de Azure](#use-azure-cli)o [API de REST](#use-rest-api). Utilizar las operaciones de la misma manera que para mover los recursos de administrador de recursos.

### <a name="new-subscription"></a>Nueva suscripción

Al mover recursos a una nueva suscripción, se aplican las siguientes restricciones:

- Deben mover todos los recursos clásicos de la suscripción en la misma operación.
- La suscripción de destino no debe contener todos los recursos clásicos.
- Sólo puede solicitar el cambio a través de una API de REST independiente para movimientos clásicos. Los comandos de mover el Administrador de recursos estándar no funcionan al mover recursos clásicos a una nueva suscripción.

Para mover recursos clásicos a una nueva suscripción, debe utilizar operaciones resto específicas a recursos clásicos. Realice los pasos siguientes para mover recursos clásicos a una nueva suscripción.

1. Compruebe si la suscripción de origen puede participar en una transición entre suscripción. Usar la siguiente operación:

         POST https://management.azure.com/subscriptions/{sourceSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
    
     En el cuerpo de la solicitud, incluyen:

         {
           "role": "source"
         }

     La respuesta de la operación de validación está en el siguiente formato:

         {
           "status": "{status}",
           "reasons": [
             "reason1",
             "reason2"
           ]
         }

2. Compruebe si la suscripción de destino puede participar en una transición entre suscripción. Usar la siguiente operación:

         POST https://management.azure.com/subscriptions/{destinationSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01

     En el cuerpo de la solicitud, incluyen:

         {
           "role": "target"
         }

     La respuesta está en el mismo formato que la validación de suscripción de origen.

3. Si ambas suscripciones pasan la validación, mueva todos los recursos clásicos de una suscripción a otra suscripción con la operación siguiente:

         POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01

    En el cuerpo de la solicitud, incluyen:

         {
           "target": "/subscriptions/{target-subscription-id}"
         }

Puede ejecutar la operación de unos minutos. 

## <a name="use-portal"></a>Usar el portal

Para mover recursos a un nuevo grupo de recursos en la **misma suscripción**, seleccione el grupo de recursos que contiene los recursos y, a continuación, seleccione el botón **mover** .

![mover los recursos](./media/resource-group-move-resources/edit-rg-icon.png)

O bien, para mover recursos a una **nueva suscripción**, seleccione el grupo de recursos que contiene los recursos y, a continuación, seleccione el icono de edición de la suscripción.

![mover los recursos](./media/resource-group-move-resources/change-subscription.png)

Seleccione los recursos para mover y el grupo de recursos de destino. Confirmar que necesite actualizar las secuencias de comandos para estos recursos y haga clic en **Aceptar**. Si ha seleccionado el icono de edición de la suscripción en el paso anterior, también debe seleccionar la suscripción de destino.

![Seleccionar destino](./media/resource-group-move-resources/select-destination.png)

En **las notificaciones**, verá que se está ejecutando la operación de movimiento.

![Mostrar el estado de movimiento](./media/resource-group-move-resources/show-status.png)

Cuando se haya completado, se le notificará del resultado.

![Mostrar el resultado de mover](./media/resource-group-move-resources/show-result.png)

## <a name="use-powershell"></a>Usar PowerShell

Para mover los recursos existentes a otro grupo de recursos o suscripción, utilice el comando **Mover AzureRmResource** .

El primer ejemplo muestra cómo mover un recurso a un nuevo grupo de recursos.

    $resource = Get-AzureRmResource -ResourceName ExampleApp -ResourceGroupName OldRG
    Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $resource.ResourceId

El segundo ejemplo muestra cómo mover varios recursos a un nuevo grupo de recursos.

    $webapp = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExampleSite
    $plan = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExamplePlan
    Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId

Para mover a una nueva suscripción, incluya un valor para el parámetro **DestinationSubscriptionId** .

Le pedirá que confirme que desea mover los recursos especificados.

    Confirm
    Are you sure you want to move these resources to the resource group
    '/subscriptions/{guid}/resourceGroups/newRG' the resources:

    /subscriptions/{guid}/resourceGroups/destinationgroup/providers/Microsoft.Web/serverFarms/exampleplan
    /subscriptions/{guid}/resourceGroups/destinationgroup/providers/Microsoft.Web/sites/examplesite
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y

## <a name="use-azure-cli"></a>Utilizar CLI Azure

Para mover los recursos existentes a otro grupo de recursos o suscripción, utilice el comando **mover recursos azure** . Debe proporcionar los identificadores de recursos de los recursos para mover. Puede obtener los identificadores de recursos con el siguiente comando:

    azure resource list -g sourceGroup --json

Que devuelve el siguiente formato:

    [
      {
        "id": "/subscriptions/{guid}/resourceGroups/sourceGroup/providers/Microsoft.Storage/storageAccounts/storagedemo",
        "name": "storagedemo",
        "type": "Microsoft.Storage/storageAccounts",
        "location": "southcentralus",
        "tags": {},
        "kind": "Storage",
        "sku": {
          "name": "Standard_RAGRS",
          "tier": "Standard"
        }
      }
    ]

En el ejemplo siguiente se muestra cómo mover una cuenta de almacenamiento a un nuevo grupo de recursos. En el parámetro **-i** , proporcione que una lista de valores separados por comas de los identificadores de recursos está mover.

    azure resource move -i "/subscriptions/{guid}/resourceGroups/sourceGroup/providers/Microsoft.Storage/storageAccounts/storagedemo" -d "destinationGroup"
    
Le pedirá que confirme que desea mover el recurso especificado.

## <a name="use-rest-api"></a>Usar la API de REST

Para mover los recursos existentes a otro grupo de recursos o suscripción, ejecute:

    POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version} 

En el cuerpo de la solicitud, especifique el grupo de recursos de destino y los recursos para mover. Para obtener más información sobre la operación resto, vea [mover los recursos](https://msdn.microsoft.com/library/azure/mt218710.aspx).


## <a name="next-steps"></a>Pasos siguientes
- Para obtener información sobre los cmdlets de PowerShell para administrar la suscripción, vea [con Azure con el Administrador de recursos](powershell-azure-resource-manager.md).
- Para obtener información sobre los comandos de Azure CLI para administrar la suscripción, vea [utilizar CLI Azure con el Administrador de recursos](xplat-cli-azure-resource-manager.md).
- Para obtener información sobre las características de portal de administración de la suscripción, consulte [con el portal de Azure para administrar los recursos](./azure-portal/resource-group-portal.md).
- Para obtener información sobre cómo aplicar una organización lógica para los recursos, vea [usar etiquetas para organizar los recursos](resource-group-using-tags.md).

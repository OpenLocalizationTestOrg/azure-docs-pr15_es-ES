<properties
   pageTitle="Crear y modificar un circuito ExpressRoute mediante el Administrador de recursos y el portal de Azure | Microsoft Azure"
   description="En este artículo se describe cómo crear, aprovisionar, comprobar, actualizar, eliminar y activar un circuito ExpressRoute."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="cherylmc"/>

# <a name="create-and-modify-an-expressroute-circuit"></a>Crear y modificar un circuito ExpressRoute

> [AZURE.SELECTOR]
[Portal de Azure - Administrador de recursos](expressroute-howto-circuit-portal-resource-manager.md)
[PowerShell - Administrador de recursos](expressroute-howto-circuit-arm.md)
[PowerShell - clásica](expressroute-howto-circuit-classic.md)

En este artículo se describe cómo crear un circuito de Azure ExpressRoute mediante el portal de Azure y el modelo de implementación de administrador de recursos de Azure. Los pasos siguientes muestran cómo comprobar el estado del circuito, actualizar, o eliminar y anular.

**Acerca de los modelos de implementación de Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 


## <a name="before-you-begin"></a>Antes de empezar


- Revise los [requisitos previos](expressroute-prerequisites.md) y [flujos de trabajo](expressroute-workflows.md) antes de comenzar la configuración.
- Asegurarse de que tiene acceso al [portal de Azure](https://portal.azure.com).
- Asegurarse de que tiene permisos para crear nuevos recursos de red. Si no tiene los permisos adecuados, póngase en contacto con el Administrador de la cuenta.

## <a name="create-and-provision-an-expressroute-circuit"></a>Crear y aprovisionar un circuito ExpressRoute

### <a name="1-sign-in-to-the-azure-portal"></a>1. Inicie sesión en el portal de Azure

Desde un explorador, vaya al [portal de Azure](http://portal.azure.com) e inicie sesión con su cuenta de Azure.

### <a name="2-create-a-new-expressroute-circuit"></a>2. Cree un nuevo circuito ExpressRoute

>[AZURE.IMPORTANT] Su circuito ExpressRoute se cargará desde el momento en que se envía una clave de servicio. Asegúrese de realizar esta operación cuando el proveedor de servicios de conectividad está listo para aprovisionar el circuito.

1. Puede crear un circuito ExpressRoute seleccionando la opción para crear un nuevo recurso. Haga clic en **nuevo** > **redes** > **ExpressRoute**, tal como se muestra en la siguiente imagen:

    ![Crear un circuito ExpressRoute](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit1.png)

2. Después de hacer clic en **ExpressRoute**, verá el módulo de **circuito ExpressRoute crear** . Cuando está rellena los valores en este módulo, asegúrese de que se especifique el nivel SKU correcto y disponibilidad de datos.

    - **Nivel** determina si se habilita un estándar de ExpressRoute o un complemento de premium ExpressRoute. Puede especificar **estándar** para obtener el SKU o **Premium** estándar para el complemento premium.

    - **Datos de medición** determina el tipo de facturación. Puede especificar **uso medido** para un plan de datos de uso medido e **ilimitado** a un plan de datos ilimitado. Tenga en cuenta que puede cambiar el tipo de facturación de **uso medido** a **ilimitado**, pero no puede cambiar el tipo de **ilimitado** **uso medido**.

    ![Configurar el nivel de SKU y disponibilidad de datos](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit2.png)

>[AZURE.IMPORTANT] Tenga en cuenta que la ubicación de interconexión indica la [ubicación física](expressroute-locations.md) donde análisis detallado con Microsoft. Esto **no** está vinculada a la propiedad de "Ubicación", que hace referencia a la ubicación donde se encuentra el proveedor de recursos de la red de Azure. Aunque no están relacionadas, es una buena práctica para elegir un proveedor de recursos de red geográfico cerca de la ubicación de interconexión del circuito. 

### <a name="3-view-the-circuits-and-properties"></a>3. ver los circuitos y las propiedades

**Ver todos los circuitos**

Puede ver todos los circuitos que ha creado, seleccione **todos los recursos** en el menú de la izquierda.
    
![Circuitos de vista](./media/expressroute-howto-circuit-portal-resource-manager/listresource.png)

**Ver las propiedades**

    You can view the properties of the circuit by selecting it. On this blade, note the service key for the circuit. You must copy the circuit key for your circuit and pass it down to the service provider to complete the provisioning process. The circuit key is specific to your circuit.

![Ver propiedades](./media/expressroute-howto-circuit-portal-resource-manager/listproperties1.png)


### <a name="4-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>4. enviar la clave de servicio a su proveedor de servicios de conectividad de aprovisionamiento

En este módulo, el **estado de proveedor** proporciona información sobre el estado actual de aprovisionamiento en el lado del proveedor de servicios. **Estado de circuito** proporciona el estado en el lado de Microsoft. Para obtener más información acerca de los Estados de aprovisionamiento de circuito, vea el artículo de [flujos de trabajo](expressroute-workflows.md#expressroute-circuit-provisioning-states) .

Cuando se crea un nuevo circuito de ExpressRoute, el circuito estarán en el estado de los siguiente:

Estado de proveedor: no se aprovisione<BR>
Estado de circuito: habilitado

![Iniciar proceso de aprovisionamiento](./media/expressroute-howto-circuit-portal-resource-manager/viewstatus.png)

El circuito cambiará al siguiente estado cuando es el proveedor de servicios de conectividad en el proceso de habilitación para usted:

Estado de proveedor: aprovisionamiento<BR>
Estado de circuito: habilitado

Para que pueda usar un circuito ExpressRoute, debe estar en el estado de los siguiente:

Estado de proveedor: se aprovisione<BR>
Estado de circuito: habilitado


### <a name="5-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>5. periódicamente comprobar el estado y el estado de la tecla de circuito

Puede ver las propiedades del circuito que está interesado en seleccionándolo. Comprobar el **estado del proveedor** y asegúrese de que se ha movido a **Provisioned** antes de continuar.


![Estado de circuito y proveedor](./media/expressroute-howto-circuit-portal-resource-manager/viewstatusprovisioned.png)


### <a name="6-create-your-routing-configuration"></a>6. crear la configuración de enrutamiento

Para obtener instrucciones detalladas, consulte el artículo de la [configuración de enrutamiento de circuito ExpressRoute](expressroute-howto-routing-portal-resource-manager.md) para crear y modificar circuito peerings.

>[AZURE.IMPORTANT] Estas instrucciones solo se aplican a circuitos que se crean con proveedores de servicios que ofrecen servicios de conectividad 2 de capa. Si está usando un proveedor de servicios que ofrece administrado nivel 3 servicios (generalmente una IP VPN, como MPLS), configurará y administrar el enrutamiento para su proveedor de servicios de conectividad.

### <a name="7-link-a-virtual-network-to-an-expressroute-circuit"></a>7. vincular una red virtual a un circuito ExpressRoute

A continuación, vincular una red virtual a su circuito ExpressRoute. Utilice el artículo de [redes virtuales de vinculación a circuitos ExpressRoute](expressroute-howto-linkvnet-arm.md) cuando se trabaja con el modelo de implementación de administrador de recursos.

## <a name="getting-the-status-of-an-expressroute-circuit"></a>Obtener el estado de un circuito ExpressRoute

Puede ver el estado de un circuito seleccionándolo. 

![Estado de un circuito ExpressRoute](./media/expressroute-howto-circuit-portal-resource-manager/listproperties1.png)


## <a name="modifying-an-expressroute-circuit"></a>Modificar un circuito ExpressRoute

Puede modificar determinadas propiedades de un circuito ExpressRoute sin afectar a la conectividad. En este momento, no puede modificar propiedades de circuito ExpressRoute mediante el portal de Azure. Sin embargo, puede usar PowerShell para modificar las propiedades de circuito. Para obtener más información, vea la sección [modificar un circuito ExpressRoute con PowerShell](expressroute-howto-circuit-arm.md#modify).

Puede hacer lo siguiente sin tiempo de inactividad:

- Habilitar o deshabilitar un complemento de premium ExpressRoute para su circuito ExpressRoute.

- Aumentar el ancho de banda de su circuito ExpressRoute. Tenga en cuenta que no se admite degradar el ancho de banda de un circuito. 

- Cambiar el plan de medición de datos de uso medido a datos ilimitado. Tenga en cuenta que no será posible cambiar el plan de medición de datos ilimitado a los datos de uso medido.

-  Puede habilitar y deshabilitar **Permitir operaciones clásico**.

Para obtener más información sobre los límites y limitaciones, consulte las [Preguntas más frecuentes de ExpressRoute](expressroute-faqs.md).


## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a>Baja y eliminar un circuito ExpressRoute

Puede eliminar el circuito ExpressRoute seleccionando el icono de **Eliminar** . Tenga en cuenta lo siguiente:

- Debe desvincular todas las redes virtuales desde el circuito ExpressRoute. Si esta operación falla, compruebe si las redes virtuales están vinculadas al circuito.

- Si el estado aprovisionamiento del proveedor de servicio de ExpressRoute circuito es **aprovisionamiento** o **Provisioned** debe trabajar con su proveedor de servicio para activar el circuito por su parte. Seguimos reservar recursos y bill hasta que el proveedor de servicios completa baja el circuito y nos notifica.

- Si el proveedor de servicios ha aprovisionamiento anulado el circuito (el estado de aprovisionamiento del proveedor de servicio está establecido en **no se aprovisione**), a continuación, puede eliminar el circuito. Esto dejará de facturación para el circuito

## <a name="next-steps"></a>Pasos siguientes

Después de crear su circuito, asegúrese de que lo siguiente:

- [Crear y modificar el enrutamiento de su circuito ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
- [Vínculo de su red virtual a su circuito ExpressRoute](expressroute-howto-linkvnet-arm.md)

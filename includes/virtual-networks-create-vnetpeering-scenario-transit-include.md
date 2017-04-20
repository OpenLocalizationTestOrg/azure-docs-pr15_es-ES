## <a name="service-chaining---transit-through-peered-vnet"></a>Servicio Chaining - tránsito por VNet peered

Aunque el uso de las rutas de sistema facilita tráfico automáticamente para su implementación, hay casos en los que desea controlar el enrutamiento de paquetes a través de un dispositivo virtual.
En este escenario, hay dos VNets en una suscripción, HubVNet y VNet1 como se describe en el diagrama siguiente. Implemente Appliance(NVA) Virtual de red en VNet HubVNet. Después de establecer VNet interconexión entre HubVNet y VNet1, puede configurar las rutas de definidas por el usuario y especificar el salto NVA en la HubVNet.

![Tránsito NVA](./media/virtual-networks-create-vnetpeering-scenario-transit-include/figure01.PNG)

> [AZURE.NOTE] Para simplificar, se supone que aquí VNets todos están en la misma suscripción. Pero también funciona para el escenario entre suscripción.

La propiedad clave para habilitar el enrutamiento tránsito es el parámetro de "Permitir el tráfico reenviado". Esto permite aceptar y enviar tráfico desde y hacia la NVA por la VNet peered.  

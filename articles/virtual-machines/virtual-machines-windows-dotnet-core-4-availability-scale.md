<properties
   pageTitle="Disponibilidad y escala en las plantillas de administrador de recursos de Azure | Microsoft Azure"
   description="Tutorial de DotNet principales de Azure Máquina Virtual"
   services="virtual-machines-windows"
   documentationCenter="virtual-machines"
   authors="neilpeterson"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="10/21/2016"
   ms.author="nepeters"/>

# <a name="availability-and-scale-in-azure-resource-manager-templates"></a>Disponibilidad y escala en las plantillas de administrador de recursos de Azure

Disponibilidad y escala hacen referencia a la actividad y la capacidad para satisfacer la demanda. Si una aplicación debe ser el 99,9% del tiempo, necesita tener una arquitectura que permite que varios de los recursos de cálculo simultáneas. Por ejemplo, en lugar de un solo sitio Web, una configuración con un nivel superior de disponibilidad incluye varias instancias del mismo sitio, con tecnología delante de equilibrio. En esta configuración, una instancia de la aplicación puede seguir hacia abajo para mantenimiento, mientras el restante seguirán funcionando. Escala por otro lado se refiere a una capacidad de aplicaciones para atender a petición. Con una carga equilibrada aplicación, agregando o quitando instancias del grupo permite una aplicación para satisfacer la petición.

Este documento describe cómo se configura la implementación de ejemplo de la tienda de música de disponibilidad y escala. Se resaltan todas las dependencias y configuraciones únicas. Para obtener la mejor experiencia, implementar previamente una instancia de la solución a su suscripción de Azure y funcionan junto con la plantilla de administrador de recursos de Azure. La plantilla completa puede encontrarse aquí: [Implementación de almacenamiento de música en Windows](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

## <a name="availability-set"></a>Conjunto de disponibilidad

Un conjunto de disponibilidad lógicamente abarca máquinas virtuales de Azure hosts físicos y otros componentes de infraestructura como fuentes de alimentación y hardware de red física. Conjuntos de disponibilidad garantizar que durante el mantenimiento, error del dispositivo u otro tiempo de inactividad, no todos los equipos virtuales se haya realizado. Un conjunto de disponibilidad pueden agregarse a una plantilla de administrador de recursos de Azure utilizando al Asistente Visual Studio agregar nuevos recursos o insertar JSON válido en una plantilla.

Siga este vínculo para ver el ejemplo JSON dentro de la plantilla de administrador de recursos: [Configurar la disponibilidad](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L368).


```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Compute/availabilitySets",
  "name": "[variables('availabilitySetName')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "availability-set"
  },
  "properties": {
  }
}
```

Un conjunto de disponibilidad se declara como una propiedad de un recurso de máquina Virtual. 

Siga este vínculo para ver el ejemplo JSON dentro de la plantilla de administrador de recursos: [Configurar la disponibilidad de asociación con Máquina Virtual](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L302).

```none
"properties": {
  "availabilitySet": {
    "id": "[resourceId('Microsoft.Compute/availabilitySets', variables('availabilitySetName'))]"
  }
```
Disponibilidad de establecer como se ve desde el portal de Azure. Cada máquina virtual y los detalles sobre la configuración se detallan a continuación.

![Conjunto de disponibilidad](./media/virtual-machines-windows-dotnet-core/ase-win.png)

Para obtener información detallada sobre los conjuntos de disponibilidad, vea [Administrar disponibilidad de máquinas virtuales](./virtual-machines-windows-manage-availability.md). 

## <a name="network-load-balancer"></a>Equilibrador de carga de red

Mientras que un conjunto de disponibilidad proporciona tolerancia a errores, un equilibrador de carga hace varias instancias de la aplicación esté disponible en una sola dirección de red. Varias instancias de una aplicación pueden estar alojados en muchas máquinas virtuales, cada uno conectado a un equilibrador de carga. Como se tiene acceso a la aplicación, el equilibrador de carga enruta la solicitud entrante en todos los miembros adjuntos. Un equilibrador de carga se pueden agregar mediante el Asistente Visual Studio agregar nuevos recursos o insertando correctamente con formato de recursos JSON en la plantilla de administrador de recursos de Azure.

Siga este vínculo para ver el ejemplo JSON dentro de la plantilla de administrador de recursos: [Equilibrador de carga de red](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L198).

```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/loadBalancers",
  "name": "[variables('loadBalancerName')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "load-balancer"
  },
  ........<truncated>
}
```

Dado que la aplicación de ejemplo se expone a internet con una dirección IP pública, esta dirección está asociada con el equilibrador de carga. 

Siga este vínculo para ver el ejemplo JSON dentro de la plantilla de administrador de recursos: [asociación equilibrador de carga de red con la dirección IP pública](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L211).

```none
"frontendIPConfigurations": [
  {
    "properties": {
      "publicIPAddress": {
        "id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicIpAddressName'))]"
      }
    },
    "name": "LoadBalancerFrontend"
  }
]
```

Desde el portal de Azure, la información general del equilibrador de carga de red muestra la asociación con la dirección IP pública.

![Equilibrador de carga de red](./media/virtual-machines-windows-dotnet-core/nlb-win.png)

## <a name="load-balancer-rule"></a>Regla de equilibrador de carga

Cuando se utiliza un equilibrador de carga, se configuran reglas que controlan cómo se equilibra el tráfico a través de los recursos previstos. Con la aplicación de almacenamiento de música de ejemplo, el tráfico llega en el puerto 80 de la dirección IP pública y se distribuye en el puerto 80 de todos los equipos virtuales. 

Siga este vínculo para ver el ejemplo JSON dentro de la plantilla de administrador de recursos: [Regla del equilibrador de carga](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L226).


```none
"loadBalancingRules": [
  {
    "name": "[variables('loadBalencerRule')]",
    "properties": {
      "frontendIPConfiguration": {
        "id": "[concat(resourceId('Microsoft.Network/loadBalancers', variables('loadBalancerName')), '/frontendIPConfigurations/LoadBalancerFrontend')]"
      },
      "backendAddressPool": {
        "id": "[variables('lbPoolID')]"
      },
      "protocol": "Tcp",
      "frontendPort": 80,
      "backendPort": 80,
      "enableFloatingIP": false,
      "idleTimeoutInMinutes": 5,
      "probe": {
        "id": "[variables('lbProbeID')]"
      }
    }
  }
]
```

Vista de la regla de equilibrador de carga de red desde el portal.

![Regla de equilibrador de carga de red](./media/virtual-machines-windows-dotnet-core/lbrule-win.png)

## <a name="load-balancer-probe"></a>Sondeo de equilibrador de carga

El equilibrador de carga también debe supervisar cada máquina virtual para que puedan servir solicitudes solamente a sistemas en ejecución. Esta supervisión lleve a cabo mediante sondeo constante de un puerto predefinido. La implementación de almacenamiento de música está configurada para sondeo puerto 80 en todos los equipos virtuales incluyen. 

Siga este vínculo para ver el ejemplo JSON dentro de la plantilla de administrador de recursos: [Sondeo de equilibrador de carga](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L247).


```none
"probes": [
  {
    "properties": {
      "protocol": "Tcp",
      "port": 80,
      "intervalInSeconds": 15,
      "numberOfProbes": 2
    },
    "name": "lbprobe"
  }
]
```

Sondeo de equilibrador de carga visto desde el portal de Azure.

![Sondeo de equilibrador de carga de red](./media/virtual-machines-windows-dotnet-core/lbprobe-win.png)

## <a name="inbound-nat-rules"></a>NAT reglas de entrada

Cuando se utiliza un equilibrador de carga, necesitan reglas poner en marcha que proporcionan acceso de equilibrio de carga no a cada máquina Virtual. Por ejemplo, al crear una conexión RDP con cada máquina virtual, este tráfico no debería equilibrio de carga, en su lugar se debe configurar una ruta de acceso predeterminada. Rutas de acceso predeterminados se configuran usando un recurso de regla NAT de entrada. Con este recurso, comunicación entrante se puede asignar a máquinas virtuales individuales. 

Con la aplicación de almacenamiento de música, un puerto comenzando en 5000 se asigna al puerto 3389 en cada máquina Virtual para el acceso RDP. La `copyindex()` función se usa para incrementar el puerto entrante, tal que la segunda Máquina Virtual recibe un puerto entrante de 5001, la tercera 5002 y así sucesivamente.

Siga este vínculo para ver el ejemplo JSON dentro de la plantilla de administrador de recursos: [Reglas NAT de entrada](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L260). 

```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/loadBalancers/inboundNatRules",
  "name": "[concat(variables('loadBalancerName'), '/', 'RDP-VM', copyIndex())]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "load-balancer-nat-rule"
  },
  "copy": {
    "name": "lbNatLoop",
    "count": "[parameters('numberOfInstances')]"
  },
  "dependsOn": [
    "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]"
  ],
  "properties": {
    "frontendIPConfiguration": {
      "id": "[variables('ipConfigID')]"
    },
    "protocol": "tcp",
    "frontendPort": "[copyIndex(5000)]",
    "backendPort": 3389,
    "enableFloatingIP": false
  }
}
```

Un ejemplo de entrada de reglas NAT tal como se muestra en el portal de Azure. Se crea una regla de RDP NAT para cada máquina virtual de la implementación.

![Regla NAT de entrada](./media/virtual-machines-windows-dotnet-core/natrule-win.png)

Para obtener información detallada sobre el equilibrador de carga de red de Azure, vea [Equilibrio de carga de servicios de infraestructura de Azure](./virtual-machines-windows-load-balance.md).

## <a name="deploy-multiple-vms"></a>Implementar máquinas virtuales múltiples

Por último, para que un conjunto de disponibilidad o equilibrador de carga funcione de manera eficaz, se requieren varios equipos virtuales. Varias VM se pueden implementar mediante la función de copia de la plantilla de administrador de recursos de Azure. Con la función copiar, no es necesario definir un número limitado de máquinas virtuales de Windows, en su lugar este valor se puede proporcionar dinámicamente en el momento de la implementación. La función copiar consume el número de instancias que se creen y los controladores de implementar el número correcto de máquinas virtuales y recursos asociados.

En la plantilla de ejemplo de la tienda de música, se define un parámetro que se tarda en un recuento de la instancia. Este número se usa en toda la plantilla al crear máquinas virtuales y recursos relacionados.

```none
"numberOfInstances": {
  "type": "int",
  "minValue": 1,
  "defaultValue": 2,
  "metadata": {
    "description": "Number of VM instances to be created behind load balancer."
  }
},
```

En el recurso de máquina Virtual, se expresa el bucle copia un nombre y el número de instancias parámetro que se usa para controlar el número de copias resultantes.

Siga este vínculo para ver el ejemplo JSON dentro de la plantilla de administrador de recursos: la [Función de copia de la máquina Virtual](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L290). 


```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[concat(variables('vmName'),copyindex())]",
  "location": "[resourceGroup().location]",
  "copy": {
    "name": "virtualMachineLoop",
    "count": "[parameters('numberOfInstances')]"
  }
```

Puede tener acceso a la iteración actual de la función copiar con el `copyIndex()` función. El valor de la función de índice de copia puede usarse para asignar un nombre a máquinas virtuales y otros recursos. Por ejemplo, si se implementan dos instancias de una máquina virtual, que necesitan nombres diferentes. La `copyIndex()` función se puede usar como parte del nombre de la máquina virtual para crear un nombre único. Un ejemplo de la `copyindex()` utilizado para la asignación de nombres de función puede verse en el recurso de máquina Virtual. En este caso, el nombre del equipo es una concatenación de la `vmName` parámetro y el `copyIndex()` función. 

Siga este vínculo para ver el ejemplo JSON dentro de la plantilla de administrador de recursos: [Función INDICE de copia](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L309). 


```none
"osProfile": {
  "computerName": "[concat(variables('vmName'),copyindex())]",
  "adminUsername": "[parameters('adminUsername')]",
  "adminPassword": "[parameters('adminPassword')]"
}
```

La `copyIndex` varias veces se usa una función en la plantilla de ejemplo de la tienda de música. Recursos y utilizar las funciones `copyIndex` incluye algo específico para una única instancia de la máquina virtual tal y la interfaz de red, las reglas del equilibrador de carga, y cualquier depende de funciones. 

Para obtener más información sobre la función copiar, vea [crear varias instancias de recursos en el Administrador de recursos de Azure](../resource-group-create-multiple.md).

## <a name="next-step"></a>Siguiente paso

<hr>

[Paso 4: implementación de la aplicación con las plantillas de administrador de recursos de Azure](./virtual-machines-windows-dotnet-core-5-app-deployment.md)
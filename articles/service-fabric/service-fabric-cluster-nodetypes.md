<properties
   pageTitle="Tipos de nodo de servicio tela y conjuntos de escala de VM | Microsoft Azure"
   description="Describe cómo los tipos de nodo de servicio tela relacionan con conjuntos de escala de VM y cómo remoto conectan a una instancia de VM escala configurada o un nodo de clúster."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/09/2016"
   ms.author="chackdan"/>


# <a name="the-relationship-between-service-fabric-node-types-and-virtual-machine-scale-sets"></a>La relación entre los tipos de nodo de servicio tela y conjuntos de escala de máquina Virtual

Conjuntos de escala de máquina virtual son un recurso de Azure calcular que puede usar para implementar y administrar una colección de máquinas virtuales como un conjunto. Todos los tipos de nodo se definen en un clúster de servicio tela está configurado como un conjunto de escala VM independiente. Cada tipo de nodo puede ampliarse hacia arriba o hacia abajo por separado, tener diferentes conjuntos de puertos abiertos y puede tener métricas de capacidad diferente.

La siguiente captura de pantalla muestra un clúster que tiene dos tipos de nodo: front-end y back-end.  Cada tipo de nodo tiene cinco nodos.

![Captura de pantalla de un clúster que tiene dos tipos de nodo][NodeTypes]

## <a name="mapping-vm-scale-set-instances-to-nodes"></a>Asignación de instancias VM escala establece a nodos

Como puede ver por encima, las instancias de VM escala establece iniciar de instancia 0 y, a continuación, pasa hacia arriba. La numeración se refleja en los nombres. Por ejemplo, BackEnd_0 es 0 de la instancia del conjunto de escala de máquina virtual de back-end. Este conjunto de escala VM determinado tiene cinco instancias, denominados BackEnd_0, BackEnd_1, BackEnd_2, BackEnd_3 y BackEnd_4.

Al aumentar la escala de un conjunto de escala de VM se crea una nueva instancia. El nuevo nombre de instancia Configurar escala de VM suele ser el nombre del conjunto de escala de VM + el número de instancia siguiente. En nuestro ejemplo, es BackEnd_5.


## <a name="mapping-vm-scale-set-load-balancers-to-each-node-typevm-scale-set"></a>Asignación de escala VM establece equilibradores de carga para cada tipo de nodo/VM conjunto de escala

Si ha implementado el clúster desde el portal o ha utilizado la plantilla de administrador de recursos de ejemplo proporcionada, a continuación, cuando se obtiene una lista de todos los recursos en un grupo de recursos verá los equilibradores de carga para cada tipo de máquina virtual escala configurada o nodo.

El nombre sería algo parecido a: **kg -&lt;nombre NodeType&gt;**. Por ejemplo, Libras-sfcluster4doc-0, tal como se muestra en esta captura de pantalla:


![Recursos][Resources]


## <a name="remote-connect-to-a-vm-scale-set-instance-or-a-cluster-node"></a>Remoto conectarse a una instancia de VM escala configurada o un nodo de clúster
Todos los tipos de nodo se definen en un clúster está configurado como un conjunto de escala VM independiente.  Esto significa que se pueden escalar los tipos de nodo arriba o abajo por separado y pueden realizarse de diferentes SKU de máquina virtual. A diferencia de máquinas virtuales de instancia única, las instancias de VM escala configurada no obtener una dirección IP virtual de sus propios. Así que puede ser un poco difícil cuando está buscando una dirección IP y puerto que puede usar para remoto conectarse a una instancia específica.

Estos son los pasos que puede seguir para detectarlos.

### <a name="step-1-find-out-the-virtual-ip-address-for-the-node-type-and-then-inbound-nat-rules-for-rdp"></a>Paso 1: Averiguar la dirección IP virtual para el tipo de nodo y, a continuación, en reglas de entrada NAT para RDP

Para obtener, debe obtener los valores de las reglas NAT entrantes que se han definido como parte de la definición de recursos para **Microsoft.Network/loadBalancers**.

En el portal, desplácese hasta el módulo de equilibrador de carga y luego en **configuración**.

![LBBlade][LBBlade]


En **configuración**, haga clic en **reglas de entrada NAT**. Esto ahora ofrece la dirección IP y puerto que puede usar para remoto conectarse a la primera instancia Configurar escala de máquina virtual. En la captura de pantalla siguiente, es **104.42.106.156** y **3389**

![NATRules][NATRules]

### <a name="step-2-find-out-the-port-that-you-can-use-to-remote-connect-to-the-specific-vm-scale-set-instancenode"></a>Paso 2: Averiguar el puerto que se puede usar para remoto conectarse a la escala de VM establece instancia/nodo específico

Más adelante en este documento, analizado acerca de cómo se asignan los nodos de las instancias de VM escala configurada. Se usará para calcular el puerto exacto.

Los puertos se asignan en orden ascendente de la instancia Configurar escala de máquina virtual. por lo tanto, en mi ejemplo para el tipo de nodo de front-end, los puertos para cada una de las instancias de cinco son las siguientes. debe realizar la misma asignación para la instancia del conjunto de escala de VM.

|**Instancia del conjunto de escala VM**|**Puerto**|
|-----------------------|--------------------------|
|FrontEnd_0|3389|
|FrontEnd_1|3390|
|FrontEnd_2|3391|
|FrontEnd_3|3392|
|FrontEnd_4|3393|
|FrontEnd_5|3394|


### <a name="step-3-remote-connect-to-the-specific-vm-scale-set-instance"></a>Paso 3: Remoto conectarse a la instancia específica de conjunto de escala de VM

En la siguiente captura de pantalla Usar conexión a Escritorio remoto para conectarse a la FrontEnd_1:

![RDP][RDP]

## <a name="how-to-change-the-rdp-port-range-values"></a>Cómo cambiar los valores de rango de puerto RDP

### <a name="before-cluster-deployment"></a>Antes de la implementación de clúster

Cuando configura el clúster mediante una plantilla de administrador de recursos, puede especificar el rango en la **inboundNatPools**.

Ir a la definición de recursos para **Microsoft.Network/loadBalancers**. En los que buscar la descripción de **inboundNatPools**.  Reemplace los valores *frontendPortRangeStart* y *frontendPortRangeEnd* .

![InboundNatPools][InboundNatPools]


### <a name="after-cluster-deployment"></a>Después de la implementación de clúster
Esto es más complicado y puede traducirse en máquinas virtuales de introducción recicladas. Ahora tendrá que establecer nuevos valores con PowerShell de Azure. Asegúrese de que Azure PowerShell 1.0 o posterior está instalado en su equipo. Si no ha hecho antes, se recomienda que siga los pasos descritos en [cómo instalar y configurar Azure PowerShell.](../powershell-install-configure.md)

Inicie sesión en su cuenta de Azure. Si este comando PowerShell falla por algún motivo, debe comprobar si tiene instalada correctamente de PowerShell de Azure.

```
Login-AzureRmAccount
```

Ejecute el siguiente para obtener detalles sobre el equilibrador de carga y ver los valores de la descripción de **inboundNatPools**:

```
Get-AzureRmResource -ResourceGroupName <RGname> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load balancer name>
```

Ahora, establezca *frontendPortRangeEnd* y *frontendPortRangeStart* en los valores que desee.

```
$PropertiesObject = @{
    #Property = value;
}
Set-AzureRmResource -PropertyObject $PropertiesObject -ResourceGroupName <RG name> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load Balancer name> -ApiVersion <use the API version that get returned> -Force
```


## <a name="next-steps"></a>Pasos siguientes

- [Información general sobre la característica "Implementar cualquier lugar" y una comparación con clústeres administradas de Azure](service-fabric-deploy-anywhere.md)
- [Seguridad de clúster](service-fabric-cluster-security.md)
- [Servicio tela SDK e introducción](service-fabric-get-started.md)


<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
[LBBlade]: ./media/service-fabric-cluster-nodetypes/LBBlade.png
[NATRules]: ./media/service-fabric-cluster-nodetypes/NATRules.png
[RDP]: ./media/service-fabric-cluster-nodetypes/RDP.png

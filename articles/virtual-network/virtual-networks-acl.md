<properties
   pageTitle="¿Qué es una lista de Control de acceso (ACL) de red?"
   description="Obtenga más información sobre las ACL"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="jdial" />

# <a name="what-is-an-endpoint-access-control-list-acls"></a>¿Qué es un punto final de la lista de Control de acceso (ACL)?

Un extremo de la lista de Control de acceso (ACL) es una mejora de seguridad disponible para su implementación de Azure. Una ACL proporciona la capacidad de permitir o denegar el tráfico de un extremo de la máquina virtual selectivamente. Esta capacidad filtrado de paquetes proporciona un nivel de seguridad adicional. Puede especificar ACL de red para solo los extremos. No puede especificar una ACL de una red virtual o subred específica incluida en una red virtual.

> [AZURE.IMPORTANT] Se recomienda usar los grupos de seguridad de red (NSGs) en lugar de ACL siempre que sea posible. Para obtener más información sobre NSGs, consulte [¿Qué es un grupo de seguridad de red?](virtual-networks-nsg.md).

Las ACL pueden configurarse con PowerShell o en el Portal de administración. Para configurar una red ACL con PowerShell, consulte [Administrar Control listas de acceso (ACL) para extremos con PowerShell.](virtual-networks-acl-powershell.md) Para configurar una red ACL mediante el Portal de administración, vea [cómo establecer extremos a una máquina Virtual](../virtual-machines/virtual-machines-windows-classic-setup-endpoints.md).

Con las ACL de red, puede hacer lo siguiente:

- Permitir o denegar el tráfico entrante en función de intervalo de direcciones IPv4 a un extremo de entrada de la máquina virtual de subred remota selectivamente.

- Direcciones IP de lista negra

- Crear varias reglas por extremo de máquina virtual

- Especificar las reglas ACL hasta 50 por extremo de máquina virtual

- Orden de usar reglas para asegurarse de que el conjunto correcto de reglas se aplican en un extremo determinado máquina virtual (menor a mayor)

- Especifique una ACL para una dirección IPv4 de subred remota específica.

## <a name="how-acls-work"></a>¿Cómo funcionan las ACL

Una ACL es un objeto que contiene una lista de reglas. Al crear una ACL y aplicarlo a un extremo de la máquina virtual, el filtrado de paquetes lleve a cabo en el nodo de host de la máquina virtual. Esto significa que el tráfico de direcciones IP remotas está filtrado por el nodo de host de reglas ACL coincidentes en lugar de en la máquina virtual. Impide que la VM gastos valiosos ciclos de la CPU en el filtrado de paquetes.

Cuando se crea una máquina virtual, una ACL predeterminada se coloca en su lugar para bloquear todo el tráfico entrante. Sin embargo, si se crea un extremo para (puerto 3389), a continuación, el valor predeterminado es modificada ACL para permitir que todo el tráfico entrante de dicho extremo. A continuación, se permite el tráfico entrante de cualquier subred remota a ese extremo y el aprovisionamiento de firewall no es necesario. Todos los demás puertos están bloqueados para el tráfico entrante a menos que se crean extremos para esos puertos. El tráfico saliente está permitido de forma predeterminada.

**Ejemplo de tabla ACL predeterminada**

| **Nº de regla** | **Subred remota** | **Punto final** | **Acepte o rechace** |
|--------|---------------|----------|-------------|
| 100    | 0.0.0.0/0     | 3389     | Permitir      |

## <a name="permit-and-deny"></a>Permitir y denegar

Selectivamente puede permitir o denegar el tráfico de red de un extremo de entrada de la máquina virtual mediante la creación de reglas que especifican "permitir" o "Denegar". Es importante que tenga en cuenta que de forma predeterminada, cuando se crea un punto final, todo el tráfico está permitido al extremo. Por este motivo, es importante comprender cómo crear reglas o permitir y colocarlos en el orden correcto de prioridad si desea que el control concreto sobre el tráfico de red que elige permitir para alcanzar el extremo de la máquina virtual.

Puntos para tener en cuenta:

1. **Sin ACL:** De forma predeterminada cuando se crea un extremo, nos permite todo para el extremo.

1. **Permitir-** Al agregar uno o más "permitan" rangos, son rechazar todos los demás rangos de forma predeterminada. Solo los paquetes desde el intervalo IP permitido podrán comunicarse con el punto final de la máquina virtual.

1. **Denegar-** Al agregar uno o más "denegación" rangos, está permitiendo todas las demás rangos de tráfico de forma predeterminada.

1. **Combinación de permitir y denegar-** Puede usar una combinación de "permitir" y "Denegar" cuando desee dividir fuera de un intervalo específico de IP permitido o denegado.

## <a name="rules-and-rule-precedence"></a>Reglas y la prioridad de la regla

ACL de red se pueden configurar en los extremos de la máquina virtual específica. Por ejemplo, puede especificar una red ACL para un extremo RDP creado en una máquina virtual que bloquea el acceso para determinadas direcciones IP. La siguiente tabla muestra una manera de conceder acceso a público virtual direcciones IP (VIP) de un intervalo concreto para permitir el acceso RDP. Todas las direcciones IP de remota se denegado. Se siguen un orden de regla *tiene menor prioridad* .

### <a name="multiple-rules"></a>Varias reglas

En el ejemplo siguiente, si desea permitir el acceso al extremo RDP únicamente de dos público IPv4 intervalos de direcciones (65.0.0.0/8 y 159.0.0.0/8), puede conseguirlo especificando dos reglas *de permiso* . En este caso, como RDP se crea de forma predeterminada para una máquina virtual, es podrán que desee bloquear el acceso al puerto RDP basado en una subred remota. El ejemplo siguiente muestra una forma para conceder acceso a público virtual direcciones IP (VIP) de un intervalo concreto para permitir el acceso RDP. Todas las direcciones IP de remota se denegado. Esto funciona porque ACL de red se puede configurar un extremo de la máquina virtual específica y acceso denegado de forma predeterminada.

**Ejemplo: varias reglas**

| **Nº de regla** | **Subred remota** | **Punto final** | **Acepte o rechace** |
|--------|---------------|----------|-------------|
| 100    | 65.0.0.0/8    | 3389     | Permitir      |
| 200    | 159.0.0.0/8   | 3389     | Permitir      |

### <a name="rule-order"></a>Orden de las reglas

Dado que pueden especificar varias reglas para un extremo, debe ser una forma de organizar reglas para determinar qué regla tiene prioridad. El orden de las reglas especifica prioridad. ACL de red siguen un orden de regla *tiene menor prioridad* . En el ejemplo siguiente, el extremo en el puerto 80 selectivamente tiene acceso a determinados intervalos de direcciones IP. Para configurar esto, tenemos una regla de denegar (regla \# 100) para las direcciones en el espacio de 175.1.0.1/24. A continuación, se especifica una segunda regla con prioridad 200 que permite el acceso a todas las demás direcciones en 175.0.0.0/8.

**Ejemplo: prioridad de la regla**

| **Nº de regla** | **Subred remota** | **Punto final** | **Acepte o rechace** |
|--------|---------------|----------|-------------|
| 100    | 175.1.0.1/24  | 80       | Denegar        |
| 200    | 175.0.0.0/8   | 80       | Permitir      |

## <a name="network-acls-and-load-balanced-sets"></a>ACL de red y conjuntos de equilibrio de carga

ACL de red se pueden especificar en un extremo de equilibrio de carga establezca (Libras). Si se especifica una ACL para un conjunto de Libras, las ACL de red se aplica a todas las máquinas virtuales de ese conjunto de Libras. Por ejemplo, si un conjunto de Libras se crea con "Puerto 80" y el conjunto de Libras contiene máquinas 3 virtuales, las ACL de red creado en el extremo "Port 80" de una que máquina virtual se aplicará automáticamente a las otras máquinas virtuales.

![ACL de red y conjuntos de equilibrio de carga](./media/virtual-networks-acl/IC674733.png)

## <a name="next-steps"></a>Pasos siguientes

[Cómo administrar listas de Control de acceso (ACL) para extremos con PowerShell](virtual-networks-acl-powershell.md)


<properties
   pageTitle="Crear un entorno de Linux completo con la CLI de Azure | Microsoft Azure"
   description="Crear almacenamiento, una VM Linux, una red virtual y subred, un equilibrador de carga, una NIC, una dirección IP pública y un grupo de seguridad de la red, todos los conceptos básicos usando CLI Azure."
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="iainfoulds"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="10/24/2016"
   ms.author="iainfou"/>

# <a name="create-a-complete-linux-environment-by-using-the-azure-cli"></a>Crear un entorno de Linux completo mediante la CLI de Azure

En este artículo, creamos una red sencilla con un equilibrador de carga y un par de máquinas virtuales que son útiles para el desarrollo e informática simple. Le guiaremos a través del proceso por comando, hasta que tenga dos laborables, seguro Linux máquinas virtuales al que puede conectarse desde cualquier lugar de Internet. A continuación, puede mover a redes y entornos más complejos.

En el camino conocer la jerarquía de dependencia que proporciona el modelo de implementación de administrador de recursos y acerca de la cantidad de energía proporciona. Después de ver cómo se crea el sistema, puede generarlo mucho más rápidamente con [las plantillas de administrador de recursos de Azure](../resource-group-authoring-templates.md). Además, después de obtener información sobre cómo encajan las partes de su entorno, crear plantillas para automatizarlas resulta más fácil.

El entorno contiene:

- Dos máquinas virtuales dentro de un conjunto de disponibilidad.
- Equilibrador de carga con una regla de equilibrio de carga en el puerto 80.
- Reglas de grupo (GSN) de seguridad de red para proteger su máquina virtual de tráfico no deseado.

![Información general del entorno básico](./media/virtual-machines-linux-create-cli-complete/environment_overview.png)

Para crear este entorno personalizado, necesita la última [Azure CLI](../xplat-cli-install.md) en modo de administrador de recursos (`azure config mode arm`). También necesita una herramienta de análisis de JSON. Este ejemplo usa [jq](https://stedolan.github.io/jq/).

## <a name="quick-commands"></a>Comandos rápidos
Si necesita realizar rápidamente la tarea, los detalles de la sección siguiente de la base de comandos para cargar una máquina virtual en Azure. Obtener más información y el contexto de cada paso se encuentra en el resto del documento, inicial [aquí](#detailed-walkthrough).

Asegúrese de que tiene [La CLI Azure](../xplat-cli-install.md) inició sesión y usar el modo de administrador de recursos:

```bash
azure config mode arm
```

En los ejemplos siguientes, reemplace los nombres de parámetro de ejemplo con sus propios valores. Incluyen nombres de parámetro de ejemplo `myResourceGroup`, `mystorageaccount`, y `myVM`.

Crear el grupo de recursos. En el ejemplo siguiente se crea un grupo de recursos denominado `myResourceGroup` en la `westeurope` ubicación:

```bash
azure group create -n myResourceGroup -l westeurope
```

Comprobar el grupo de recursos con el analizador JSON:

```bash
azure group show myResourceGroup --json | jq '.'
```

Crear la cuenta de almacenamiento. En el ejemplo siguiente se crea una cuenta de almacenamiento denominada `mystorageaccount` (el nombre de la cuenta de almacenamiento debe ser único, así que proporcione su propio nombre único):

```bash
azure storage account create -g myResourceGroup -l westeurope \
  --kind Storage --sku-name GRS mystorageaccount
```

Compruebe la cuenta de almacenamiento mediante el analizador JSON:

```bash
azure storage account show -g myResourceGroup mystorageaccount --json | jq '.'
```

Crear la red virtual. En el ejemplo siguiente se crea una red virtual denominada `myVnet`:

```bash
azure network vnet create -g myResourceGroup -l westeurope\
  -n myVnet -a 192.168.0.0/16
```

Crear una subred. En el ejemplo siguiente se crea una subred denominada `mySubnet`"

```bash
azure network vnet subnet create -g myResourceGroup \
  -e myVnet -n mySubnet -a 192.168.1.0/24
```

Compruebe la red virtual y subred mediante el analizador JSON:


```bash
azure network vnet show myResourceGroup myVnet --json | jq '.'
```

Crear una dirección IP pública. En el ejemplo siguiente se crea una dirección IP pública denominada `myPublicIP` con el nombre DNS de `mypublicdns` (el nombre DNS deben ser únicos, así que proporcione su propio nombre único):

```bash
azure network public-ip create -g myResourceGroup -l westeurope \
  -n myPublicIP  -d mypublicdns -a static -i 4
```

Crear el equilibrador de carga. En el ejemplo siguiente se crea un equilibrador de carga denominado `myLoadBalancer`:

```bash
azure network lb create -g myResourceGroup -l westeurope -n myLoadBalancer
```

Crear un grupo IP front-end para el equilibrador de carga y asociar la dirección IP pública. En el ejemplo siguiente se crea un grupo IP front-end denominado `mySubnetPool`:

```bash
azure network lb frontend-ip create -g myResourceGroup -l myLoadBalancer \
  -i myPublicIP -n myFrontEndPool 
```

Crear el grupo IP de back-end para el equilibrador de carga. En el ejemplo siguiente se crea un grupo IP de back-end denominado `myBackEndPool`:

```bash
azure network lb address-pool create -g myResourceGroup -l myLoadBalancer \
  -n myBackEndPool
```

Crear red entrantes de SSH reglas de traducción (NAT) de dirección para el equilibrador de carga. En el ejemplo siguiente se crea dos reglas de equilibrador de carga, `myLoadBalancerRuleSSH1` y `myLoadBalancerRuleSSH2`:

```bash
azure network lb inbound-nat-rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleSSH1 -p tcp -f 4222 -b 22
azure network lb inbound-nat-rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleSSH2 -p tcp -f 4223 -b 22
```

Crear la web reglas NAT para el equilibrador de carga de entrada. En el ejemplo siguiente se crea una regla de equilibrador de carga con nombre`myLoadBalancerRuleWeb`

```bash
azure network lb rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleWeb -p tcp -f 80 -b 80 \
  -t myFrontEndPool -o myBackEndPool
```

Crear el sondeo de estado del equilibrador de carga. En el ejemplo siguiente se crea un sondeo TCP denominado `myHealthProbe`:

```bash
azure network lb probe create -g myResourceGroup -l myLoadBalancer \
  -n myHealthProbe -p "tcp" -i 15 -c 4
```

Comprobar el equilibrador de carga, grupos IP y las reglas NAT mediante el analizador JSON:

```bash
azure network lb show -g myResourceGroup -n myLoadBalancer --json | jq '.'
```

Crear la primera tarjeta de interfaz de red (NIC). Reemplazar el `#####-###-###` secciones con su propio identificador de suscripción de Azure. Su suscripción ID se indica en el resultado de `jq` al examinar los recursos que va a crear. También puede ver el identificador de la suscripción con `azure account list`. 

En el ejemplo siguiente se crea una NIC denominada `myNic1`:

```bash
azure network nic create -g myResourceGroup -l westeurope \
  -n myNic1 -m myVnet -k mySubnet \
  -d "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool" \
  -e "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
```

Crear la segunda NIC. En el ejemplo siguiente se crea una NIC denominada `myNic2`:

```bash
azure network nic create -g myResourceGroup -l westeurope \
  -n myNic2 -m myVnet -k mySubnet \
  -d "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool" \
  -e "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2"
```

Compruebe las dos NIC mediante el analizador JSON:

```bash
azure network nic show myResourceGroup myNic1 --json | jq '.'
azure network nic show myResourceGroup myNic2 --json | jq '.'
```

Crear el grupo de seguridad de la red. En el ejemplo siguiente se crea un grupo de seguridad de red denominado `myNetworkSecurityGroup`:

```bash
azure network nsg create -g myResourceGroup -l westeurope \
  -n myNetworkSecurityGroup
```

Agregue dos reglas de entrada para el grupo de seguridad de la red. En el ejemplo siguiente se crea dos reglas, `myNetworkSecurityGroupRuleSSH` y `myNetworkSecurityGroupRuleHTTP`:

```bash
azure network nsg rule create -p tcp -r inbound -y 1000 -u 22 -c allow \
  -g myResourceGroup -a myNetworkSecurityGroup -n myNetworkSecurityGroupRuleSSH
azure network nsg rule create -p tcp -r inbound -y 1001 -u 80 -c allow \
  -g myResourceGroup -a myNetworkSecurityGroup -n myNetworkSecurityGroupRuleHTTP
```

Comprobar el grupo de seguridad de la red y las reglas de entrada mediante el analizador JSON:

```bash
azure network nsg show -g myResourceGroup -n myNetworkSecurityGroup --json | jq '.'
```

Enlazar el grupo de seguridad de red con dos NIC:

```bash
azure network nic set -g myResourceGroup -o myNetworkSecurityGroup -n myNic1
azure network nic set -g myResourceGroup -o myNetworkSecurityGroup -n myNic2
```

Cree el conjunto de disponibilidad. En el ejemplo siguiente se crea una conjunto con nombre de disponibilidad `myAvailabilitySet`:

```bash
azure availset create -g myResourceGroup -l westeurope -n myAvailabilitySet
```

Crear la primera VM Linux. En el ejemplo siguiente se crea una máquina virtual denominada `myVM1`:

```bash
azure vm create \
    --resource-group myResourceGroup \
    --name myVM1 \
    --location westeurope \
    --os-type linux \
    --availset-name myAvailabilitySet \
    --nic-name myNic1 \
    --vnet-name myVnet \
    --vnet-subnet-name mySubnet \
    --storage-account-name mystorageaccount \
    --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

Crear la segunda VM Linux. En el ejemplo siguiente se crea una máquina virtual denominada `myVM2`:

```bash
azure vm create \
    --resource-group myResourceGroup \
    --name myVM2 \
    --location westeurope \
    --os-type linux \
    --availset-name myAvailabilitySet \
    --nic-name myNic2 \
    --vnet-name myVnet \
    --vnet-subnet-name mySubnet \
    --storage-account-name mystorageaccount \
    --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

Usar el analizador JSON para comprobar que todo lo que se creó:

```bash
azure vm show -g myResourceGroup -n myVM1 --json | jq '.'
azure vm show -g myResourceGroup -n myVM2 --json | jq '.'
```

Exportar su nuevo entorno a una plantilla para volver a crear rápidamente nuevas instancias:

```bash
azure group export myResourceGroup
```

## <a name="detailed-walkthrough"></a>Tutorial detallado
Siga los pasos detallados explican lo que hace cada comando como generación fuera de su entorno. Estos conceptos son útiles cuando cree sus propio personalizados entornos de desarrollo o de producción.

Asegúrese de que tiene [La CLI Azure](../xplat-cli-install.md) inició sesión y usar el modo de administrador de recursos:

```bash
azure config mode arm
```

En los ejemplos siguientes, reemplace los nombres de parámetro de ejemplo con sus propios valores. Incluyen nombres de parámetro de ejemplo `myResourceGroup`, `mystorageaccount`, y `myVM`.

## <a name="create-resource-groups-and-choose-deployment-locations"></a>Crear grupos de recursos y elija ubicaciones de implementación

Grupos de Azure recursos son entidades de implementación lógica que contienen información de configuración y metadatos para habilitar la administración de implementaciones de recursos lógica. En el ejemplo siguiente se crea un grupo de recursos denominado `myResourceGroup` en la `westeurope` ubicación:

```bash
azure group create --name myResourceGroup --location westeurope
```

Resultado:

```bash                        
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Creating resource group myResourceGroup
info:    Created resource group myResourceGroup
data:    Id:                  /subscriptions/guid/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westeurope
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

## <a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento

Necesita cuentas de almacenamiento para los discos VM y para los discos de datos adicionales que desea agregar. Crear cuentas de almacenamiento casi inmediatamente después de crear grupos de recursos.

Aquí usamos la `azure storage account create` comando, pasando la ubicación de la cuenta, el grupo de recursos que controla la base de datos y el tipo de soporte de almacenamiento de información que desee. En el ejemplo siguiente se crea una cuenta de almacenamiento denominada `mystorageaccount`:

```bash
azure storage account create \  
  --location westeurope \
  --resource-group myResourceGroup \
  --kind Storage --sku-name GRS \
  mystorageaccount
```

Resultado:

```bash
info:    Executing command storage account create
+ Creating storage account
info:    storage account create command OK
```

Para examinar nuestro grupo de recursos usando la `azure group show` comando, vamos a usar la herramienta de [jq](https://stedolan.github.io/jq/) junto con la `--json` opción CLI de Azure. (Puede utilizar **jsawk** o en cualquier biblioteca de idioma que prefiera para analizar el JSON).

```bash
azure group show myResourceGroup --json | jq '.'
```

Resultado:

```bash
{
  "tags": {},
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "name": "myResourceGroup",
  "provisioningState": "Succeeded",
  "location": "westeurope",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "resources": [
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
      "name": "mystorageaccount",
      "type": "storageAccounts",
      "location": "westeurope",
      "tags": null
    }
  ],
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": []
    }
  ]
}
```

Para investigar la cuenta de almacenamiento mediante el uso de la CLI, debe establecer las teclas y nombres de cuenta. Reemplazar el nombre de la cuenta de almacenamiento en el siguiente ejemplo con un nombre que elija:

```
AZURE_STORAGE_CONNECTION_STRING="$(azure storage account connectionstring show mystorageaccount --resource-group myResourceGroup --json | jq -r '.string')"
```

A continuación, puede ver la información de almacenamiento fácilmente:

```bash
azure storage container list
```

Resultado:

```bash
info:    Executing command storage container list
+ Getting storage containers
data:    Name  Public-Access  Last-Modified
data:    ----  -------------  -----------------------------
data:    vhds  Off            Sun, 27 Sep 2015 19:03:54 GMT
info:    storage container list command OK
```

## <a name="create-a-virtual-network-and-subnet"></a>Crear una red virtual y subred

A continuación va a necesitar crear una red virtual que se ejecuta en Azure y una subred en la que puede crear sus máquinas virtuales. En el ejemplo siguiente se crea una red virtual denominada `myVnet` con el `192.168.0.0/16` prefijo de dirección:

```bash
azure network vnet create --resource-group myResourceGroup --location westeurope \
  --name myVnet --address-prefixes 192.168.0.0/16 
```

Resultado:

```bash
info:    Executing command network vnet create
+ Looking up virtual network "myVnet"
+ Creating virtual network "myVnet"
+ Loading virtual network state
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet
data:    Name                            : myVnet
data:    Type                            : Microsoft.Network/virtualNetworks
data:    Location                        : westeurope
data:    ProvisioningState               : Succeeded
data:    Address prefixes:
data:      192.168.0.0/16
info:    network vnet create command OK
```

De nuevo, vamos a usar la opción--json de `azure group show` y **jq** para ver cómo que estamos construyendo nuestros recursos. Ahora tenemos un `storageAccounts` recursos y un `virtualNetworks` recursos.  

```bash
azure group show myResourceGroup --json | jq '.'
```

Resultado:

```bash
{
  "tags": {},
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "name": "myResourceGroup",
  "provisioningState": "Succeeded",
  "location": "westeurope",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "resources": [
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
      "name": "myVnet",
      "type": "virtualNetworks",
      "location": "westeurope",
      "tags": null
    },
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
      "name": "mystorageaccount",
      "type": "storageAccounts",
      "location": "westeurope",
      "tags": null
    }
  ],
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": []
    }
  ]
}
```

Ahora vamos a crear una subred en el `myVnet` red virtual en el que se implementan las máquinas virtuales. Usamos el `azure network vnet subnet create` comando, junto con los recursos que ya hemos creado: el `myResourceGroup` grupo de recursos y la `myVnet` red virtual. En el ejemplo siguiente, agregamos la subred denominada `mySubnet` con el prefijo de dirección de subred `192.168.1.0/24`:

```bash
azure network vnet subnet create --resource-group myResourceGroup \
  --vnet-name myVnet --name mySubnet --address-prefix 192.168.1.0/24
```

Resultado:

```bash
info:    Executing command network vnet subnet create
+ Looking up the subnet "mySubnet"
+ Creating subnet "mySubnet"
+ Looking up the subnet "mySubnet"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet
data:    Type                            : Microsoft.Network/virtualNetworks/subnets
data:    ProvisioningState               : Succeeded
data:    Name                            : mySubnet
data:    Address prefix                  : 192.168.1.0/24
data:
info:    network vnet subnet create command OK
```

Dado que la subred es lógicamente dentro de la red virtual, esperamos para obtener la información de subred con un comando ligeramente distinto. El comando usamos está `azure network vnet show`, pero continuar examinar el resultado JSON usando **jq**.

```bash
azure network vnet show myResourceGroup myVnet --json | jq '.'
```

Resultado:

```bash
{
  "subnets": [
    {
      "ipConfigurations": [],
      "addressPrefix": "192.168.1.0/24",
      "provisioningState": "Succeeded",
      "name": "mySubnet",
      "etag": "W/\"974f3e2c-028e-4b35-832b-a4b16ad25eb6\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
    }
  ],
  "tags": {},
  "addressSpace": {
    "addressPrefixes": [
      "192.168.0.0/16"
    ]
  },
  "dhcpOptions": {
    "dnsServers": []
  },
  "provisioningState": "Succeeded",
  "etag": "W/\"974f3e2c-028e-4b35-832b-a4b16ad25eb6\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
  "name": "myVnet",
  "location": "westeurope"
}
```

## <a name="create-a-public-ip-address-pip"></a>Crear una dirección IP pública (puntos)

Ahora vamos a crear la dirección IP pública (puntos) que se asigna a un equilibrador de carga. Le permite conectarse a sus máquinas virtuales de Internet usando el `azure network public-ip create` comando. Dado que la dirección predeterminada es dinámica, creamos una entrada DNS con nombre en el dominio **cloudapp.azure.com** utilizando la `--domain-name-label` opción. En el ejemplo siguiente se crea una dirección IP pública denominada `myPublicIP` con el nombre DNS de `mypublicdns`. Como el nombre DNS debe ser único, especifique su propio nombre único de DNS:

```bash
azure network public-ip create --resource-group myResourceGroup \
  --location westeurope --name myPublicIP --domain-name-label mypublicdns
```

Resultado:

```bash
info:    Executing command network public-ip create
+ Looking up the public ip "myPublicIP"
+ Creating public ip address "myPublicIP"
+ Looking up the public ip "myPublicIP"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP
data:    Name                            : myPublicIP
data:    Type                            : Microsoft.Network/publicIPAddresses
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Allocation method               : Dynamic
data:    Idle timeout                    : 4
data:    Domain name label               : mypublicdns
data:    FQDN                            : mypublicdns.westeurope.cloudapp.azure.com
info:    network public-ip create command OK
```

La dirección IP pública también es un recurso de nivel superior, para que pueda verlo con `azure group show`.

```bash
azure group show myResourceGroup --json | jq '.'
```

Resultado:

```bash
{
"tags": {},
"id": "/subscriptions/guid/resourceGroups/myResourceGroup",
"name": "myResourceGroup",
"provisioningState": "Succeeded",
"location": "westeurope",
"properties": {
    "provisioningState": "Succeeded"
},
"resources": [
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
    "name": "myPublicIP",
    "type": "publicIPAddresses",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
    "name": "myVnet",
    "type": "virtualNetworks",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "name": "mystorageaccount",
    "type": "storageAccounts",
    "location": "westeurope",
    "tags": null
    }
],
"permissions": [
    {
    "actions": [
        "*"
    ],
    "notActions": []
    }
]
}
```

Puede investigar más detalles del recurso, incluidos el nombre de dominio completo (FQDN) de subdominio, usando el completado `azure network public-ip show` comando. Ha asignado el recurso de dirección IP público lógicamente, pero aún no se ha asignado una dirección específica. Para obtener una dirección IP, va a necesitar un equilibrador de carga, no hemos creado aún.

```bash
azure network public-ip show myResourceGroup myPublicIP --json | jq '.'
```

Resultado:

```bash
{
"tags": {},
"publicIpAllocationMethod": "Dynamic",
"dnsSettings": {
    "domainNameLabel": "mypublicdns",
    "fqdn": "mypublicdns.westeurope.cloudapp.azure.com"
},
"idleTimeoutInMinutes": 4,
"provisioningState": "Succeeded",
"etag": "W/\"c63154b3-1130-49b9-a887-877d74d5ebc5\"",
"id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
"name": "myPublicIP",
"location": "westeurope"
}
```

## <a name="create-a-load-balancer-and-ip-pools"></a>Crear un equilibrador de carga y grupos de IP
Cuando se crea un equilibrador de carga, le permite distribuir el tráfico entre varias VM. También proporciona redundancia a la aplicación ejecutando varias máquinas virtuales que responden a solicitudes de usuario en caso de mantenimiento o cargas. En el ejemplo siguiente se crea un equilibrador de carga denominado `myLoadBalancer`:

```bash
azure network lb create --resource-group myResourceGroup --location westeurope \
  --name myLoadBalancer
```

Resultado:

```bash
info:    Executing command network lb create
+ Looking up the load balancer "myLoadBalancer"
+ Creating load balancer "myLoadBalancer"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer
data:    Name                            : myLoadBalancer
data:    Type                            : Microsoft.Network/loadBalancers
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
info:    network lb create command OK
```

Nuestra equilibrador de carga es bastante vacía, así que vamos a crear algunos grupos IP. Desea crear dos grupos IP para nuestro equilibrador de carga: uno para el front-end y otro para el back-end. El grupo IP front-end está visible públicamente. También es la ubicación a la que se asignan los puntos que hemos creado anteriormente. A continuación, usamos la agrupación de back-end como una ubicación para nuestras máquinas virtuales para conectarse a. De esta forma, el tráfico de a través del equilibrador de carga para las máquinas virtuales.

En primer lugar, vamos a crear nuestro grupo IP front-end. En el ejemplo siguiente se crea un grupo de servidores front-end denominado `myFrontEndPool`:

```bash
azure network lb frontend-ip create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --public-ip-name myPublicIP \
  --name myFrontEndPool 
```

Resultado:

```bash
info:    Executing command network lb frontend-ip create
+ Looking up the load balancer "myLoadBalancer"
+ Looking up the public ip "myPublicIP"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myFrontEndPool
data:    Provisioning state              : Succeeded
data:    Private IP allocation method    : Dynamic
data:    Public IP address id            : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP
info:    network lb mySubnet-ip create command OK
```

Observe cómo hemos usado el `--public-ip-name` conmutador para pasar de la `myPublicIP` que hemos creado anteriormente. Asignar la dirección IP pública al equilibrador de carga le permite llegar sus máquinas virtuales a través de Internet.

A continuación, vamos a crear nuestro segundo grupo IP, esta vez para el tráfico de back-end. En el ejemplo siguiente se crea un grupo de back-end denominado `myBackEndPool`:

```bash
azure network lb address-pool create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myBackEndPool
```

Resultado:

```bash
info:    Executing command network lb address-pool create
+ Looking up the load balancer "myLoadBalancer"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myBackEndPool
data:    Provisioning state              : Succeeded
info:    network lb address-pool create command OK
```

Podemos ver cómo nuestra equilibrador de carga está realizando mirando con `azure network lb show` y examinando los resultados JSON:

```bash
azure network lb show myResourceGroup myLoadBalancer --json | jq '.'
```

Resultado:

```bash
{
  "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
  "provisioningState": "Succeeded",
  "resourceGuid": "f1446acb-09ba-44d9-b8b6-849d9983dc09",
  "outboundNatRules": [],
  "inboundNatPools": [],
  "inboundNatRules": [],
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer",
  "name": "myLoadBalancer",
  "type": "Microsoft.Network/loadBalancers",
  "location": "westeurope",
  "mySubnetIPConfigurations": [
    {
      "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
      "name": "myFrontEndPool",
      "provisioningState": "Succeeded",
      "publicIPAddress": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP"
      },
      "privateIPAllocationMethod": "Dynamic",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
    }
  ],
  "backendAddressPools": [
    {
      "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
      "name": "myBackEndPool",
      "provisioningState": "Succeeded",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
    }
  ],
  "loadBalancingRules": [],
  "probes": []
}
```

## <a name="create-load-balancer-nat-rules"></a>Crear reglas NAT de equilibrador de carga
Para obtener el tráfico que fluye a través de nuestro equilibrador de carga, es necesario crear reglas de traducción (NAT) que especifican las acciones de entrada o salidas de dirección de red. Puede especificar el protocolo de usar y luego asignar puertos externos a puertos internos como desee. Para nuestro entorno, vamos a crear algunas reglas para permitir SSH a través de nuestro equilibrador de carga a nuestras máquinas virtuales. Puertos TCP 4222 y 4223 se configure para dirigir al puerto TCP 22 en nuestras VM (que creamos más adelante). En el ejemplo siguiente se crea una regla denominada `myLoadBalancerRuleSSH1` para asignar el puerto TCP 4222 al puerto 22:

```bash
azure network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH1 \
  --protocol tcp --frontend-port 4222 --backend-port 22
```

Resultado:

```bash
info:    Executing command network lb inbound-nat-rule create
+ Looking up the load balancer "myLoadBalancer"
warn:    Using default enable floating ip: false
warn:    Using default idle timeout: 4
warn:    Using default mySubnet IP configuration "myFrontEndPool"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myLoadBalancerRuleSSH1
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    mySubnet port                   : 4222
data:    Backend port                    : 22
data:    Enable floating IP              : false
data:    Idle timeout in minutes         : 4
data:    mySubnet IP configuration id    : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool
info:    network lb inbound-nat-rule create command OK
```

Repita el procedimiento para la segunda regla NAT para SSH. En el ejemplo siguiente se crea una regla denominada `myLoadBalancerRuleSSH2` para asignar el puerto TCP 4223 al puerto 22:

```bash
azure network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH2 --protocol tcp \
  --frontend-port 4223 --backend-port 22
```

También vamos a continuar y crear una regla NAT para el puerto TCP 80 para el tráfico de web, el enlace de la regla hasta nuestros grupos IP. Si se conectar la regla al grupo IP, en lugar de enlazar la regla a nuestras máquinas virtuales de forma individual, podemos agregar o quitar máquinas virtuales al grupo de IP. El equilibrador de carga ajusta automáticamente el flujo del tráfico. En el ejemplo siguiente se crea una regla denominada `myLoadBalancerRuleWeb` para asignar el puerto TCP 80 al puerto 80:

```bash
azure network lb rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleWeb --protocol tcp \
  --frontend-port 80 --backend-port 80 --frontend-ip-name myFrontEndPool \
  --backend-address-pool-name myBackEndPool
```

Resultado:

```bash
info:    Executing command network lb rule create
+ Looking up the load balancer "myLoadBalancer"
warn:    Using default idle timeout: 4
warn:    Using default enable floating ip: false
warn:    Using default load distribution: Default
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myLoadBalancerRuleWeb
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    mySubnet port                   : 80
data:    Backend port                    : 80
data:    Enable floating IP              : false
data:    Load distribution               : Default
data:    Idle timeout in minutes         : 4
data:    mySubnet IP configuration id    : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool
data:    Backend address pool id         : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool
info:    network lb rule create command OK
```

## <a name="create-a-load-balancer-health-probe"></a>Crear un sondeo de estado del equilibrador de carga

Un estado periódicamente sondeo comprobaciones en las máquinas virtuales que están detrás de nuestro equilibrador de carga para asegurarse de que están operativo y responder a solicitudes definida. Si no es así, se quitan de la operación para asegurarse de que los usuarios no se dirigen a ellos. Puede definir las comprobaciones personalizadas para el sondeo de estado, junto con los intervalos y valores de tiempo de espera. Para obtener más información acerca de salud sondeos, consulte [sondeos equilibrador de carga](../load-balancer/load-balancer-custom-probe-overview.md). En el ejemplo siguiente se crea un TCP estado busca con nombre `myHealthProbe`:

```bash
azure network lb probe create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myHealthProbe --protocol "tcp" \
  --interval 15 --count 4
```

Resultado:

```bash
info:    Executing command network lb probe create
warn:    Using default probe port: 80
+ Looking up the load balancer "myLoadBalancer"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myHealthProbe
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    Port                            : 80
data:    Interval in seconds             : 15
data:    Number of probes                : 4
info:    network lb probe create command OK
```

En este caso, hemos especificado un intervalo de 15 segundos para nuestras comprobaciones de estado. Nos podemos pierda un máximo de cuatro sondeos (un minuto) antes de que el equilibrador de carga se considera que el host ya no funciona.

## <a name="verify-the-load-balancer"></a>Comprobar el equilibrador de carga
Ahora se realiza la configuración del equilibrador de carga. Estos son los pasos que realizó:

1. Se crea por primera vez un equilibrador de carga.
2. A continuación, crea un grupo IP front-end y le asigna una dirección IP pública.
3. A continuación crea un grupo IP de back-end que pueden conectar máquinas virtuales.
4. Después de eso, crea reglas NAT que permiten SSH para las máquinas virtuales de administración, junto con una regla que permita el puerto TCP 80 nuestra aplicación web.
5. Finalmente agrega un sondeo de estado para comprobar periódicamente las máquinas virtuales. Este sondeo de mantenimiento garantiza que los usuarios no intentan obtener acceso a una máquina virtual que ya no está funcionando o servir contenido.

Vamos a revisar el equilibrador de carga aspecto ahora:

```bash
azure network lb show --resource-group myResourceGroup \
  --name myLoadBalancer --json | jq '.'
```

Resultado:

```bash
{
  "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
  "provisioningState": "Succeeded",
  "resourceGuid": "f1446acb-09ba-44d9-b8b6-849d9983dc09",
  "outboundNatRules": [],
  "inboundNatPools": [],
  "inboundNatRules": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleSSH1",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "protocol": "Tcp",
      "mySubnetPort": 4222,
      "backendPort": 22,
      "idleTimeoutInMinutes": 4,
      "enableFloatingIP": false,
      "provisioningState": "Succeeded"
    },
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleSSH2",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "protocol": "Tcp",
      "mySubnetPort": 4223,
      "backendPort": 22,
      "idleTimeoutInMinutes": 4,
      "enableFloatingIP": false,
      "provisioningState": "Succeeded"
    }
  ],
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer",
  "name": "myLoadBalancer",
  "type": "Microsoft.Network/loadBalancers",
  "location": "westeurope",
  "mySubnetIPConfigurations": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myFrontEndPool",
      "provisioningState": "Succeeded",
      "publicIPAddress": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP"
      },
      "privateIPAllocationMethod": "Dynamic",
      "loadBalancingRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb"
        }
      ],
      "inboundNatRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
        },
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2"
        }
      ],
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
    }
  ],
  "backendAddressPools": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myBackEndPool",
      "provisioningState": "Succeeded",
      "loadBalancingRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb"
        }
      ],
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
    }
  ],
  "loadBalancingRules": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleWeb",
      "provisioningState": "Succeeded",
      "enableFloatingIP": false,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "backendAddressPool": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
      },
      "protocol": "Tcp",
      "loadDistribution": "Default",
      "mySubnetPort": 80,
      "backendPort": 80,
      "idleTimeoutInMinutes": 4
    }
  ],
  "probes": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myHealthProbe",
      "provisioningState": "Succeeded",
      "numberOfProbes": 4,
      "intervalInSeconds": 15,
      "port": 80,
      "protocol": "Tcp",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/probes/myHealthProbe"
    }
  ]
}
```

## <a name="create-an-nic-to-use-with-the-linux-vm"></a>Crear una NIC para usar con Linux VM

NIC están disponibles mediante programación porque puede aplicar reglas a su uso. También puede tener más de uno. En el siguiente `azure network nic create` comando conectar la NIC al grupo IP de back-end de carga y asociar con reglas para permitir el tráfico SSH NAT.
 
Reemplazar el `#####-###-###` secciones con su propio identificador de suscripción de Azure. Su suscripción ID se indica en el resultado de `jq` al examinar los recursos que va a crear. También puede ver el identificador de la suscripción con `azure account list`. 

En el ejemplo siguiente se crea una NIC denominada `myNic1`:

```bash
azure network nic create --resource-group myResourceGroup --location westeurope \
  --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic1 \
  --lb-address-pool-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool \
  --lb-inbound-nat-rule-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1
```

Resultado:

```bash
info:    Executing command network nic create
+ Looking up the subnet "mySubnet"
+ Looking up the network interface "myNic1"
+ Creating network interface "myNic1"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1
data:    Name                            : myNic1
data:    Type                            : Microsoft.Network/networkInterfaces
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Enable IP forwarding            : false
data:    IP configurations:
data:      Name                          : Nic-IP-config
data:      Provisioning state            : Succeeded
data:      Private IP address            : 192.168.1.4
data:      Private IP allocation method  : Dynamic
data:      Subnet                        : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet
data:      Load balancer backend address pools:
data:        Id                          : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool
data:      Load balancer inbound NAT rules:
data:        Id                          : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1
data:
info:    network nic create command OK
```

Puede ver los detalles examinando el recurso directamente. Examinar el recurso mediante la `azure network nic show` comando:

```bash
azure network nic show myResourceGroup myNic1 --json | jq '.'
```

Resultado:

```bash
{
  "etag": "W/\"fc1eaaa1-ee55-45bd-b847-5a08c7f4264a\"",
  "provisioningState": "Succeeded",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1",
  "name": "myNic1",
  "type": "Microsoft.Network/networkInterfaces",
  "location": "westeurope",
  "ipConfigurations": [
    {
      "etag": "W/\"fc1eaaa1-ee55-45bd-b847-5a08c7f4264a\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1/ipConfigurations/Nic-IP-config",
      "loadBalancerBackendAddressPools": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
        }
      ],
      "loadBalancerInboundNatRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
        }
      ],
      "privateIPAddress": "192.168.1.4",
      "privateIPAllocationMethod": "Dynamic",
      "subnet": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
      },
      "provisioningState": "Succeeded",
      "name": "Nic-IP-config"
    }
  ],
  "dnsSettings": {
    "appliedDnsServers": [],
    "dnsServers": []
  },
  "enableIPForwarding": false,
  "resourceGuid": "a20258b8-6361-45f6-b1b4-27ffed28798c"
}
```

Ahora creamos la segunda NIC, conectar en nuestro grupo de IP de back-end. Esta regla NAT tiempo la segunda permite el tráfico SSH. En el ejemplo siguiente se crea una NIC denominada `myNic2`:

```bash
azure network nic create --resource-group myResourceGroup --location westeurope \
  --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic2 \
  --lb-address-pool-ids  /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool \
  --lb-inbound-nat-rule-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2
```

## <a name="create-a-network-security-group-and-rules"></a>Crear un grupo de seguridad de red y reglas

Ahora crear un grupo de seguridad de la red y las reglas de entrada que rigen el acceso a la NIC. Un grupo de seguridad de red se puede aplicar a una NIC o subred. Definir reglas para controlar el flujo del tráfico dentro y fuera de sus máquinas virtuales. En el ejemplo siguiente se crea un grupo de seguridad de red denominado `myNetworkSecurityGroup`:

```bash
azure network nsg create --resource-group myResourceGroup --location westeurope \
  --name myNetworkSecurityGroup
```

Vamos a agregar la regla de entrada de la GSN permitir las conexiones entrantes en el puerto 22 (para admitir SSH). En el ejemplo siguiente se crea una regla denominada `myNetworkSecurityGroupRuleSSH` para permitir que el puerto TCP 22:

```bash
azure network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --protocol tcp --direction inbound \
  --priority 1000 --destination-port-range 22 --access allow \
  --name myNetworkSecurityGroupRuleSSH
```

Ahora vamos a agregar la regla de entrada de la GSN permitir las conexiones entrantes en el puerto 80 (para admitir el tráfico web). En el ejemplo siguiente se crea una regla denominada `myNetworkSecurityGroupRuleHTTP` para permitir que el puerto TCP 80:

```bash
azure network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --protocol tcp --direction inbound \
  --priority 1001 --destination-port-range 80 --access allow \
  --name myNetworkSecurityGroupRuleHTTP
```

> [AZURE.NOTE] La regla de entrada es un filtro para conexiones de red entrantes. En este ejemplo, hemos enlazar el GSN a la NIC virtual máquinas virtuales, lo que significa que cualquier solicitud de puerto 22 se pasa a través de la NIC en nuestra VM. Esta regla entrante consiste en una conexión de red y no sobre un extremo, que es lo que sería sobre en implementaciones clásicas. Para abrir un puerto, debe dejar el `--source-port-range` establecida en '\*' (valor predeterminado) para aceptar las solicitudes entrantes de **cualquier** solicitud de puerto. Puertos son normalmente dinámicos.

## <a name="bind-to-the-nic"></a>Enlazar al NIC

Enlazar el GSN a las NIC. Es necesario conectar nuestras NIC con nuestro grupo de seguridad de la red. Ejecutar ambos comandos enlazar de nuestras NIC:

```bash
azure network nic set --resource-group myResourceGroup --name myNic1 \
  --network-security-group-name myNetworkSecurityGroup
```

```bash
azure network nic set --resource-group myResourceGroup --name myNic2 \
  --network-security-group-name myNetworkSecurityGroup
```

## <a name="create-an-availability-set"></a>Crear un conjunto de disponibilidad
Disponibilidad conjuntos de expansión de ayuda sus máquinas virtuales de dominios de error y actualización de dominios. Vamos a crear una disponibilidad para sus máquinas virtuales. En el ejemplo siguiente se crea una conjunto con nombre de disponibilidad `myAvailabilitySet`:

```bash
azure availset create --resource-group myResourceGroup --location westeurope
  --name myAvailabilitySet
```

Dominios de error definen una agrupación de máquinas virtuales que comparten un modificador de origen y de red power comunes. De forma predeterminada, las máquinas virtuales que están configuradas en el conjunto de disponibilidad están separadas por hasta tres dominios de error. La idea es que un problema de hardware en uno de estos dominios de error no afecta a cada VM que se ejecuta la aplicación. Azure distribuye automáticamente máquinas virtuales en los dominios de error cuando se coloca en un conjunto de disponibilidad.

Actualización dominios indican grupos de máquinas virtuales y hardware físico subyacente que puede reiniciar al mismo tiempo. El orden en que se reinician la actualización de dominios no puede ser secuencial durante el mantenimiento planeado, pero se reinicia solo una actualización a la vez. Vuelva a Azure distribuye automáticamente sus máquinas virtuales en dominios actualización al colocarlas en un sitio de disponibilidad.

Más información sobre [la administración de la disponibilidad de VM](./virtual-machines-linux-manage-availability.md).

## <a name="create-the-linux-vms"></a>Crear las máquinas virtuales de Linux

Ha creado los recursos de almacenamiento y de red para admitir máquinas virtuales de acceso a Internet. Ahora vamos a crearlas máquinas virtuales y protegerlos con una clave SSH que no tiene una contraseña. En este caso, vamos a crear un Ubuntu VM basándose en los LTADOS más reciente. Nos encontrar información de la imagen mediante `azure vm image list`, como se describe en la [búsqueda de imágenes de máquina virtual de Azure](virtual-machines-linux-cli-ps-findimage.md).

Seleccionamos una imagen mediante el comando `azure vm image list westeurope canonical | grep LTS`. En este caso, usamos `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`. Para el último campo, se pasa `latest` para que en el futuro obtenemos siempre la versión más reciente. (Es la cadena usamos `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`).

Este paso siguiente es familiar para cualquiera que ya ha creado un ssh claves públicas y privadas de rsa emparejar en Mac o Linux utilizando **ssh keygen-t rsa -b 2048**. Si no tiene ningún pares de claves de certificado su `~/.ssh` directorio, puede crearlos:

- Automáticamente, mediante el uso de la `azure vm create --generate-ssh-keys` opción.
- Manualmente, siguiendo [las instrucciones para crear usted mismo](virtual-machines-linux-mac-create-ssh-keys.md).

Como alternativa, puede usar el `--admin-password` método para autenticar las conexiones de SSH después de crea la máquina virtual. Este método es normalmente menos seguro.

Crear la máquina virtual poniendo todos nuestros recursos e información junto con el `azure vm create` comando:

```bash
azure vm create \
  --resource-group myResourceGroup \
  --name myVM1 \
  --location westeurope \
  --os-type linux \
  --availset-name myAvailabilitySet \
  --nic-name myNic1 \
  --vnet-name myVnet \
  --vnet-subnet-name mySubnet \
  --storage-account-name mystorageaccount \
  --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username ops
```

Resultado:

```bash
info:    Executing command vm create
+ Looking up the VM "myVM1"
info:    Verifying the public key SSH file: /home/ahmet/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_DS1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account mystorageaccount
+ Looking up the availability set "myAvailabilitySet"
info:    Found an Availability set "myAvailabilitySet"
+ Looking up the NIC "myNic1"
info:    Found an existing NIC "myNic1"
info:    Found an IP configuration with virtual network subnet id "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet" in the NIC "myNic1"
info:    This is an NIC without publicIP configured
info:    The storage URI 'https://mystorageaccount.blob.core.windows.net/' will be used for boot diagnostics settings, and it can be overwritten by the parameter input of '--boot-diagnostics-storage-uri'.
info:    vm create command OK
```

Puede conectarse a su máquina virtual inmediatamente mediante las claves SSH predeterminado. Asegúrese de que especifique el puerto adecuado ya que estamos pasando a través del equilibrador de carga. (Para nuestra primera VM, se configura la NAT regla para reenviar puerto 4222 a nuestra VM):

```bash
 ssh ops@mypublicdns.westeurope.cloudapp.azure.com -p 4222
```

Resultado:

```bash
The authenticity of host '[mypublicdns.westeurope.cloudapp.azure.com]:4222 ([xx.xx.xx.xx]:4222)' can't be established.
ECDSA key fingerprint is 94:2d:d0:ce:6b:fb:7f:ad:5b:3c:78:93:75:82:12:f9.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '[mypublicdns.westeurope.cloudapp.azure.com]:4222,[xx.xx.xx.xx]:4222' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 16.04.1 LTS (GNU/Linux 4.4.0-34-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.

ops@myVM1:~$
```

Continuar y crear la segunda VM de la misma manera:

```bash
azure vm create \
  --resource-group myResourceGroup \
  --name myVM2 \
  --location westeurope \
  --os-type linux \
  --availset-name myAvailabilitySet \
  --nic-name myNic2 \
  --vnet-name myVnet \
  --vnet-subnet-name mySubnet \
  --storage-account-name mystorageaccount \
  --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username ops
```

Y ahora puede utilizar el `azure vm show myResourceGroup myVM1` comando Examinar que haya creado. En este momento, está ejecutando sus máquinas virtuales de Ubuntu detrás de un equilibrador de carga en Azure que puede iniciar sesión en solo con par de claves SSH (porque se deshabilitan las contraseñas). Puede instalar nginx o httpd, implementar una aplicación web y ver el tráfico flujo a través del equilibrador de carga a ambos las máquinas virtuales.

```bash
azure vm show --resource-group myResourceGroup --name myVM1
```

Resultado:

```bash
info:    Executing command vm show
+ Looking up the VM "TestVM1"
+ Looking up the NIC "myNic1"
data:    Id                              :/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM1
data:    ProvisioningState               :Succeeded
data:    Name                            :myVM1
data:    Location                        :westeurope
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_DS1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :16.04.0-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clib45a8b650f4428a1-os-1471973896525
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://mystorageaccount.blob.core.windows.net/vhds/clib45a8b650f4428a1-os-1471973896525.vhd
data:
data:    OS Profile:
data:      Computer Name                 :myVM1
data:      User Name                     :ops
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-24-D4-AA
data:          Provisioning State        :Succeeded
data:          Name                      :LmyNic1
data:          Location                  :westeurope
data:
data:    AvailabilitySet:
data:      Id                            :/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/availabilitySets/myAvailabilitySet
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://mystorageaccount.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm show command OK

```


## <a name="export-the-environment-as-a-template"></a>Exportar el entorno como una plantilla
Ahora que ha creado este entorno, ¿qué ocurre si desea crear un entorno de desarrollo adicionales con los mismos parámetros o un entorno de producción que coincida? Administrador de recursos usa plantillas JSON que definen todos los parámetros para su entorno. Crear entornos todo al hacer referencia a esta plantilla JSON. Puede [crear plantillas JSON manualmente](../resource-group-authoring-templates.md) o exportar un entorno existente para crear la plantilla JSON para usted:

```bash
azure group export --name myResourceGroup
```

Este comando crea la `myResourceGroup.json` archivo en el directorio de trabajo actual. Cuando se crea un entorno de esta plantilla, se le pedirá que todos los nombres de recursos, incluidos los nombres de equilibrador de carga, interfaces de red o máquinas virtuales. Puede rellenar estos nombres en el archivo de plantilla agregando el `-p` o `--includeParameterDefaultValue` parámetro la `azure group export` comando mostrado anteriormente. Editar la plantilla JSON para especificar los nombres de los recursos o [crear un archivo parameters.json](../resource-group-authoring-templates.md#parameters) que especifica los nombres de recursos.

Para crear un entorno de la plantilla:

```bash
azure group deployment create --resource-group myNewResourceGroup \
  --template-file myResourceGroup.json
```

Desea leer [más sobre cómo implementar de plantillas](../resource-group-template-deploy-cli.md). Obtenga información sobre cómo actualizar entornos incrementalmente, usar el archivo de parámetros y tener acceso a plantillas desde una única ubicación de almacenamiento.

## <a name="next-steps"></a>Pasos siguientes

Ahora ya está listo para empezar a trabajar con varios componentes de red y máquinas virtuales. Puede utilizar este entorno de ejemplo para generar la aplicación con los componentes principales introducidos aquí.


<properties
   pageTitle="Crear una puerta de enlace de la aplicación con las plantillas de administrador de recursos de Azure | Microsoft Azure"
   description="Esta página contiene instrucciones para crear una puerta de enlace de Azure aplicación con la plantilla de administrador de recursos de Azure"
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="gwallace"/>


# <a name="create-an-application-gateway-by-using-the-azure-resource-manager-template"></a>Crear una puerta de enlace de la aplicación con la plantilla de administrador de recursos de Azure

> [AZURE.SELECTOR]
- [Portal de Azure](application-gateway-create-gateway-portal.md)
- [Azure PowerShell de administrador de recursos](application-gateway-create-gateway-arm.md)
- [Azure PowerShell clásica](application-gateway-create-gateway.md)
- [Azure plantilla de administrador de recursos](application-gateway-create-gateway-arm-template.md)
- [CLI de Azure](application-gateway-create-gateway-cli.md)

La puerta de enlace de aplicación Azure es un equilibrador de carga de nivel 7. Proporciona migración tras error, las solicitudes HTTP enrutamiento de rendimiento entre diferentes servidores, independientemente de si están en la nube o local. Puerta de enlace de aplicación proporciona muchas características de controlador de entrega de la aplicación (ADC) incluye Equilibrio de carga HTTP, afinidad sesión basada en cookies, descargar Secure Sockets Layer (SSL), sondeos de estado personalizados, compatibilidad con varios sitios y muchas otras. Para obtener una lista completa de características compatibles, visite [Introducción de puerta de enlace de aplicaciones](application-gateway-introduction.md)

Aprenda a descargar y modificar una plantilla de administrador de recursos de Azure existente desde GitHub e implementar la plantilla de GitHub, PowerShell y CLI Azure.

Si va a implementar simplemente la plantilla de administrador de recursos de Azure directamente desde GitHub sin cambios, omita para implementar una plantilla de GitHub.

## <a name="scenario"></a>Escenario

En este escenario, podrá:

- Crear una puerta de enlace de la aplicación con dos instancias.
- Crear una red virtual denominada VirtualNetwork1 con un bloque CIDR reservado de 10.0.0.0/16.
- Crear una subred denominada Appgatewaysubnet que utiliza 10.0.0.0/28 como su bloque CIDR.
- Configurar dos configurado previamente direcciones IP de back-end para los servidores web que desee cargar saldo el tráfico. En este ejemplo de plantilla, las direcciones IP de back-end son 10.0.1.10 y 10.0.1.11.

>[AZURE.NOTE] Los valores son los parámetros de esta plantilla. Para personalizar la plantilla, puede cambiar las reglas, la escucha y el SSL que se abre la azuredeploy.json.

![Escenario](./media/application-gateway-create-gateway-arm-template/scenario.png)

## <a name="download-and-understand-the-azure-resource-manager-template"></a>Descargar y comprender la plantilla de administrador de recursos de Azure

Puede descargar la plantilla de administrador de recursos de Azure existente para crear una red virtual y dos subredes desde GitHub, realice los cambios que desee y volver a usarla. Para ello, realice los siguientes pasos:

1. Vaya a [crear la puerta de enlace de aplicación](https://github.com/Azure/azure-quickstart-templates/tree/master/101-application-gateway-create).
2. Haga clic en **azuredeploy.json**y, a continuación, haga clic en **materia**.
3. Guarde el archivo en una carpeta local en el equipo.
4. Si está familiarizado con las plantillas de administrador de recursos de Azure, vaya al paso 7.
5. Abra el archivo que ha guardado y ver el contenido en los **parámetros** en la línea 5. Parámetros de plantilla de administrador de recursos Azure proporcionan un marcador de posición para los valores que se pueden rellenar durante la implementación.

  	| Parámetro | Descripción |
  	|---|---|
  	| **ubicación** | Azure región donde se crea la puerta de enlace de aplicación |
  	| **VirtualNetwork1** | Nombre para la nueva red virtual |
  	| **addressPrefix** | Espacio de direcciones de la red virtual, en formato CIDR |
  	| **ApplicationGatewaysubnet** | Nombre de la subred de puerta de enlace de aplicación |
  	| **subnetPrefix** | Bloque CIDR para la subred de puerta de enlace de aplicación |
  	| **SKUName** | Tamaño de la instancia SKU |
  	| **capacidad** | Número de instancias de |
  	| **backendaddress1** | Dirección IP del primer servidor web |
  	| **backendaddress2** | Dirección IP del segundo servidor web |


    >[AZURE.IMPORTANT] Plantillas de administrador de recursos Azure mantenidas en GitHub pueden cambiar con el tiempo. Asegúrese de que la plantilla antes de usarla.

6. Compruebe el contenido en **recursos** y observe lo siguiente:

    - **tipo**. Tipo de recurso se ha creado por la plantilla. En este caso, el tipo es **Microsoft.Network/applicationGateways**, que representa una puerta de enlace de aplicación.
    - **nombre**. Nombre para el recurso. Observe el uso de **[parameters('applicationGatewayName')]**, lo que significa que el nombre se proporciona como entrada por usted o por un archivo de parámetro durante la implementación.
    - **Propiedades**. Lista de propiedades para el recurso. Esta plantilla utiliza la red virtual y la dirección IP pública durante la creación de la puerta de enlace de aplicación.

7. Vaya a [https://github.com/Azure/azure-quickstart-templates/blob/master/101-application-gateway-create/](https://github.com/Azure/azure-quickstart-templates/blob/master/101-application-gateway-create).
8. Haga clic en **azuredeploy paremeters.json**y, a continuación, haga clic en **materia**.
9. Guarde el archivo en una carpeta local en el equipo.
10. Abra el archivo que ha guardado y modifique los valores de los parámetros. Para implementar la puerta de enlace de aplicación que se describe en este escenario, utilice los siguientes valores.

        {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        {
        "location" : {
        "value" : "West US"
        },
        "addressPrefix": {
        "value": "10.0.0.0/16"
        },
        "subnetPrefix": {
        "value": "10.0.0.0/24"
        },
        "skuName": {
        "value": "Standard_Small"
        },
        "capacity": {
        "value": 2
        },
        "backendIpAddress1": {
        "value": "10.0.1.10"
        },
        "backendIpAddress2": {
        "value": "10.0.1.11"
        }
        }

11. Guarde el archivo. Puede probar la plantilla JSON y parámetro mediante herramientas de validación de JSON en línea como [JSlint.com](http://www.jslint.com/).

## <a name="deploy-the-azure-resource-manager-template-by-using-powershell"></a>Implementar la plantilla de administrador de recursos de Azure con PowerShell

Si nunca ha usado Azure PowerShell, vea [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) y siga las instrucciones para iniciar sesión en Azure y seleccione la suscripción.

### <a name="step-1"></a>Paso 1

    Login-AzureRmAccount

### <a name="step-2"></a>Paso 2

Compruebe las suscripciones para la cuenta.

    Get-AzureRmSubscription

Se le solicite para autenticar con sus credenciales.

### <a name="step-3"></a>Paso 3

Elija cuál de las suscripciones de Azure usar.


    Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### <a name="step-4"></a>Paso 4


Si es necesario, cree un grupo de recursos mediante el cmdlet **AzureResourceGroup de nuevo** . En el ejemplo siguiente, cree un grupo de recursos denominado AppgatewayRG en la ubicación estadounidense oriental.

    New-AzureRmResourceGroup -Name AppgatewayRG -Location "East US"

Ejecute el cmdlet **New-AzureRmResourceGroupDeployment** para implementar la nueva red virtual mediante la plantilla anterior y archivos de parámetro que ha descargado y modificado.

    New-AzureRmResourceGroupDeployment -Name TestAppgatewayDeployment -ResourceGroupName AppgatewayRG `
        -TemplateFile C:\ARM\azuredeploy.json -TemplateParameterFile C:\ARM\azuredeploy-parameters.json

## <a name="deploy-the-azure-resource-manager-template-by-using-the-azure-cli"></a>Implementar la plantilla de administrador de recursos de Azure mediante la CLI de Azure

Para implementar la plantilla de administrador de recursos de Azure que ha descargado mediante CLI de Azure, siga los pasos siguientes:

### <a name="step-1"></a>Paso 1

Si nunca ha usado CLI de Azure, consulte [instalar y configurar la CLI Azure](../xplat-cli-install.md) y siga las instrucciones hasta el punto donde seleccionar su cuenta de Azure y suscripción.

### <a name="step-2"></a>Paso 2

Ejecutar el comando de **azure config modo** para cambiar al modo de administrador de recursos, como se muestra a continuación.

    azure config mode arm

Éste es el resultado esperado para el comando anterior:

    info:   New mode is arm

### <a name="step-3"></a>Paso 3

Si es necesario, ejecute el comando **Crear grupo azure** para crear un nuevo grupo de recursos, como se muestra a continuación. Observe que el resultado del comando. La lista que aparece después de la salida explica los parámetros que se utiliza. Para obtener más información acerca de los grupos de recursos, visite [Introducción al administrador de recursos de Azure](../azure-resource-manager/resource-group-overview.md).

    azure group create -n appgatewayRG -l eastus

**-n (o--nombre)**. Nombre del nuevo grupo de recursos. Para nuestro escenario es *appgatewayRG*.

**-l (o--ubicación)**. Región de Azure donde se crea el nuevo grupo de recursos. Para nuestro escenario es *eastus*.

### <a name="step-4"></a>Paso 4

Ejecute el cmdlet de **implementación de azure grupo crear** para implementar la nueva red virtual con los archivos de plantilla y parámetro descargado y modificado por encima. La lista que aparece después de la salida explica los parámetros que se utiliza.

    azure group deployment create -g appgatewayRG -n TestAppgatewayDeployment -f C:\ARM\azuredeploy.json -e C:\ARM\azuredeploy-parameters.json

## <a name="deploy-the-azure-resource-manager-template-by-using-click-to-deploy"></a>Implementar la plantilla de administrador de recursos de Azure mediante haga clic en desplegar

Haga clic en desplegar es otra forma de usar las plantillas de administrador de recursos de Azure. Es una forma sencilla de usar plantillas con el portal de Azure.

### <a name="step-1"></a>Paso 1

Vaya a [crear una puerta de enlace de la aplicación con IP pública](https://azure.microsoft.com/documentation/templates/101-application-gateway-public-ip/).

### <a name="step-2"></a>Paso 2

Haga clic en **desplegar en Azure**.

![Implementar en Azure](./media/application-gateway-create-gateway-arm-template/deploytoazure.png)

### <a name="step-3"></a>Paso 3

Rellene los parámetros de la plantilla de implementación en el portal y haga clic en **Aceptar**.

![Parámetros](./media/application-gateway-create-gateway-arm-template/ibiza1.png)

### <a name="step-4"></a>Paso 4

Seleccione las **condiciones legales** y haga clic en **comprar**.

### <a name="step-5"></a>Paso 5

En el módulo de implementación personalizada, haga clic en **crear**.

## <a name="next-steps"></a>Pasos siguientes

Si desea configurar la descarga SSL, vea [Configurar una puerta de enlace de aplicación para SSL de descarga](application-gateway-ssl.md).

Si desea configurar una puerta de enlace de la aplicación para usar con un equilibrador de carga interno, consulte [crear una puerta de enlace de la aplicación con un equilibrador de carga interno (ILB)](application-gateway-ilb.md).

Si desea obtener más información sobre opciones de equilibrio de carga en general, visite:

- [Equilibrador de carga de Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Administrador de tráfico de Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)

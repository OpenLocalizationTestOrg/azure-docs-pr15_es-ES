<properties
   pageTitle="Crear un opuestas equilibrador de carga en el Administrador de recursos con una plantilla de Internet | Microsoft Azure"
   description="Aprenda a crear un opuestas equilibrador de carga en el Administrador de recursos con una plantilla de Internet"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="sewhee" />

# <a name="creating-an-internet-facing-load-balancer-using-a-template"></a>Crear un opuestas equilibrador de carga con una plantilla de Internet

[AZURE.INCLUDE [load-balancer-get-started-internet-arm-selectors-include.md](../../includes/load-balancer-get-started-internet-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]En este artículo trata sobre el modelo de implementación de administrador de recursos. También puede [obtener información sobre cómo crear un opuestas equilibrador de carga con el modelo de implementación clásica de Internet](load-balancer-get-started-internet-classic-portal.md)


[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="deploy-the-template-by-using-click-to-deploy"></a>Implementar la plantilla mediante hacer clic para implementar

La plantilla de ejemplo disponible en el repositorio público utiliza un archivo de parámetro que contiene el valor predeterminado valores que se usan para generar el escenario descrito anteriormente. Para implementar esta plantilla con clic para implementar, siga [este vínculo](http://go.microsoft.com/fwlink/?LinkId=544801), haga clic en **implementar a Azure**, reemplace los valores de parámetro de forma predeterminada, si es necesario y siga las instrucciones en el portal.

## <a name="deploy-the-template-by-using-powershell"></a>Implementar la plantilla con PowerShell

Para implementar la plantilla que descargó con PowerShell, siga los pasos siguientes.

1. Si nunca ha usado Azure PowerShell, vea [cómo instalar y configurar Azure PowerShell](../../articles/powershell-install-configure.md) y siga las instrucciones hasta el final para iniciar sesión en Azure y seleccione la suscripción.

2. Ejecute el cmdlet **AzureRmResourceGroupDeployment de nuevo** para crear un grupo de recursos con la plantilla.

        New-AzureRmResourceGroupDeployment -Name TestRG -Location uswest `
            -TemplateFile 'https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json' `
            -TemplateParameterFile 'https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.parameters.json'

## <a name="deploy-the-template-by-using-the-azure-cli"></a>Implementar la plantilla mediante la CLI de Azure

Para implementar la plantilla mediante la CLI de Azure, siga los pasos siguientes.

1. Si nunca ha usado CLI de Azure, vea [instalar y configurar la CLI de Azure](../../articles/xplat-cli-install.md) y siga las instrucciones hasta el punto donde seleccionar su cuenta de Azure y suscripción.
2. Ejecutar el comando de **azure config modo** para cambiar al modo de administrador de recursos, como se muestra a continuación.

        azure config mode arm

    Éste es el resultado esperado para el comando anterior:

        info:    New mode is arm

3. Desde el explorador, vaya a [La plantilla de tutorial rápido](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-loadbalancer-lbrules), copie el contenido del archivo json y pegar en un nuevo archivo en el equipo. En este escenario, ¿copiando los valores que aparecen a continuación en un archivo denominado **c:\lb\azuredeploy.parameters.json**.
4. Ejecute el cmdlet de **implementación de azure grupo crear** para implementar el nuevo equilibrador de carga con los archivos de plantilla y parámetro descargado y modificado por encima. La lista que aparece después de la salida explica los parámetros que se utiliza.

        azure group create -n TestRG -l westus -f 'https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json' -e 'c:\lb\azuredeploy.parameters.json'

## <a name="next-steps"></a>Pasos siguientes

[Empezar a configurar un equilibrador de carga interno](load-balancer-get-started-ilb-arm-ps.md)

[Configurar un modo de distribución del equilibrador de carga](load-balancer-distribution-mode.md)

[Configurar los valores de tiempo de espera TCP inactivas para el equilibrador de carga](load-balancer-tcp-idle-timeout.md)

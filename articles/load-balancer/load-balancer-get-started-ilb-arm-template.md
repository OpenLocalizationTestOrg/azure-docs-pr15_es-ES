<properties
   pageTitle="Crear un equilibrador de carga interno usando una plantilla en el Administrador de recursos | Microsoft Azure"
   description="Aprenda a crear un equilibrador de carga interno usando una plantilla en el Administrador de recursos"
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

# <a name="create-an-internal-load-balancer-using-a-template"></a>Crear un equilibrador de carga interno usando una plantilla

[AZURE.INCLUDE [load-balancer-get-started-ilb-arm-selectors-include.md](../../includes/load-balancer-get-started-ilb-arm-selectors-include.md)]
<BR>
[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][modelo de implementación clásico](load-balancer-get-started-ilb-classic-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="deploy-the-template-by-using-click-to-deploy"></a>Implementar la plantilla mediante hacer clic para implementar

La plantilla de ejemplo disponible en el repositorio público utiliza un archivo de parámetro que contiene el valor predeterminado valores que se usan para generar el escenario descrito anteriormente. Para implementar esta plantilla con clic para implementar, siga [este vínculo](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-internal-load-balancer), haga clic en **implementar a Azure**, reemplace los valores de parámetro de forma predeterminada, si es necesario y siga las instrucciones en el portal.

## <a name="deploy-the-template-by-using-powershell"></a>Implementar la plantilla con PowerShell

Para implementar la plantilla que descargó con PowerShell, siga los pasos siguientes.

1. Si nunca ha usado Azure PowerShell, vea [cómo instalar y configurar Azure PowerShell](../../articles/powershell-install-configure.md) y siga las instrucciones hasta el final para iniciar sesión en Azure y seleccione la suscripción.
2. Descargar el archivo de parámetros en el disco local.
3. Editar el archivo y guárdelo.
4. Ejecute el cmdlet **AzureRmResourceGroupDeployment de nuevo** para crear un grupo de recursos con la plantilla.

        New-AzureRmResourceGroupDeployment -Name TestRG -Location westus `
            -TemplateFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json' `
            -TemplateParameterFile 'C:\temp\azuredeploy.parameters.json'

## <a name="deploy-the-template-by-using-the-azure-cli"></a>Implementar la plantilla mediante la CLI de Azure

Para implementar la plantilla mediante la CLI de Azure, siga los pasos siguientes.

1. Si nunca ha usado CLI de Azure, vea [instalar y configurar la CLI de Azure](../../articles/xplat-cli-install.md) y siga las instrucciones hasta el punto donde seleccionar su cuenta de Azure y suscripción.
2. Ejecutar el comando de **azure config modo** para cambiar al modo de administrador de recursos, como se muestra a continuación.

        azure config mode arm

    Éste es el resultado esperado para el comando anterior:

        info:    New mode is arm

3. Abra el archivo de parámetro, seleccione su contenido y guardarlo en un archivo en su equipo. En este ejemplo, hemos guardado el archivo de parámetros *parameters.json*.

4. Ejecute el comando **Crear implementación de azure grupo** para implementar el nuevo equilibrador de carga interno mediante los archivos de plantilla y parámetro descargado y modificado por encima. La lista que aparece después de la salida explica los parámetros que se utiliza.

        azure group create --name TestRG --location westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json --parameters-file parameters.json

## <a name="next-steps"></a>Pasos siguientes

[Configurar un modo de distribución del equilibrador de carga mediante la afinidad IP de origen](load-balancer-distribution-mode.md)

[Configurar los valores de tiempo de espera TCP inactivas para el equilibrador de carga](load-balancer-tcp-idle-timeout.md)




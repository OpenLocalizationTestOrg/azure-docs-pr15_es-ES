<properties 
   pageTitle="Implementar una máquina virtual con una dirección IP pública estática con el portal de Azure en el Administrador de recursos | Microsoft Azure"
   description="Obtenga información sobre cómo implementar máquinas virtuales con una dirección IP pública estática con el portal de zure en el Administrador de recursos"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/04/2016"
   ms.author="jdial" />

# <a name="deploy-a-vm-with-a-static-public-ip-using-the-azure-portal"></a>Implementar una máquina virtual con una dirección IP pública estática con el portal de Azure

[AZURE.INCLUDE [virtual-network-deploy-static-pip-arm-selectors-include.md](../../includes/virtual-network-deploy-static-pip-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásico.

[AZURE.INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## <a name="create-a-vm-with-a-static-public-ip"></a>Crear una máquina virtual con una dirección IP pública estática 

Para crear una máquina virtual con una dirección IP pública estática en el portal de Azure, siga los pasos siguientes.

1. Desde un explorador, vaya al [portal de Azure](https://portal.azure.com) y, si es necesario, inicie sesión con su cuenta de Azure.
2. En la esquina superior izquierda del portal, haga clic en **nuevo**>>**calcular**>**Centro de datos de Windows Server 2012 R2**.
3. En la lista **Seleccionar un modelo de implementación** , seleccione **Administrador de recursos** y haga clic en **crear**.
4. En el módulo de **conceptos básicos** , escriba la información de la máquina virtual tal como se muestra a continuación y, a continuación, haga clic en **Aceptar**.

    ![Portal de Azure - conceptos básicos](./media/virtual-network-deploy-static-pip-arm-portal/figure1.png)

5. En el módulo de **Elegir un tamaño** , tal como se muestra a continuación, haga clic en **Estándar de A1** y, a continuación, haga clic en **Seleccionar**.

    ![Portal de Azure - elegir un tamaño](./media/virtual-network-deploy-static-pip-arm-portal/figure2.png)

6. En el módulo de **configuración** , haga clic en **dirección IP pública**, en el módulo de la **dirección IP pública de crear** **asignaciones**, haga clic en **estática** tal como se muestra a continuación. Y, a continuación, haga clic en **Aceptar**.

    ![Portal de Azure - Crear dirección IP pública](./media/virtual-network-deploy-static-pip-arm-portal/figure3.png)

7. En el módulo de **configuración** , haga clic en **Aceptar**.
8. Revise el módulo de **Resumen** , tal como se muestra a continuación y, a continuación, haga clic en **Aceptar**.

    ![Portal de Azure - Crear dirección IP pública](./media/virtual-network-deploy-static-pip-arm-portal/figure4.png)

9. Observe el icono nuevo en el panel.

    ![Portal de Azure - Crear dirección IP pública](./media/virtual-network-deploy-static-pip-arm-portal/figure5.png)

10. Una vez creada la máquina virtual, se mostrará el módulo de **configuración** como se muestra a continuación

    ![Portal de Azure - Crear dirección IP pública](./media/virtual-network-deploy-static-pip-arm-portal/figure6.png)
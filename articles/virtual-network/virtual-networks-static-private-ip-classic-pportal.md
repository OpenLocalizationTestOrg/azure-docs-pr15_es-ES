<properties 
   pageTitle="Cómo establecer una dirección IP privada estática en modo clásico con el Portal de Azure | Microsoft Azure"
   description="Descripción de direcciones IP estática de privado y cómo se administran en modo clásico con el portal de Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-service-management"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/04/2016"
   ms.author="jdial" />

# <a name="how-to-set-a-static-private-ip-address-classic-in-the-azure-portal"></a>Cómo configurar una dirección IP privada estática (clásico) en el portal de Azure

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]En este artículo trata sobre el modelo de implementación clásico. También puede [administrar una dirección IP privada estática en el modelo de implementación de administrador de recursos](virtual-networks-static-private-ip-arm-pportal.md).

[AZURE.INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Los siguientes pasos de ejemplo esperan un entorno simple ya ha creado. Si desea ejecutar los pasos que se muestran en este documento, cree primero el entorno de prueba que se describe en [crear un vnet](virtual-networks-create-vnet-classic-pportal.md).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Cómo especificar una dirección IP privada estática al crear una máquina virtual
Para crear una máquina virtual denominada *DNS01* en la subred *front-end* de un VNet denominado *TestVNet* con una dirección IP privada estática de *192.168.1.101*, siga los pasos siguientes:

1. Desde un explorador, vaya a http://portal.azure.com y, si es necesario, inicie sesión con su cuenta de Azure.
2. Haga clic en **nuevo** > **calcular** > **Centro de datos de Windows Server 2012 R2**, observe que la lista **Seleccionar un modelo de implementación** ya muestra **clásica**y, a continuación, haga clic en **crear**.

    ![Crear VM en el portal de Azure](./media/virtual-networks-static-ip-classic-pportal/figure01.png)

3. En el módulo **VM crear** , escriba el nombre de la máquina virtual que se cree (*DNS01* en nuestro ejemplo), la cuenta de administrador local y la contraseña.

    ![Crear VM en el portal de Azure](./media/virtual-networks-static-ip-classic-pportal/figure02.png)

4. Haga clic en **Configuración opcional** > **red** > **Red Virtual**y, a continuación, haga clic en **TestVNet**. Si **TestVNet** no está disponible, asegúrese de que está utilizando la ubicación *Central de Estados Unidos* y ha creado el entorno de prueba descrito al principio de este artículo.

    ![Crear VM en el portal de Azure](./media/virtual-networks-static-ip-classic-pportal/figure03.png)

5. En el módulo de **red** , asegúrese de que la subred seleccionada es *front-end*, a continuación, haga clic en **las direcciones IP**, en la **asignación de direcciones IP** , haga clic en **estático**y, a continuación, escriba *192.168.1.101* dirección **IP** , tal como se muestra a continuación.

    ![Crear VM en el portal de Azure](./media/virtual-networks-static-ip-classic-pportal/figure04.png)   

6. Haga clic en **Aceptar** en el módulo de **direcciones IP** , a continuación, haga clic en **Aceptar** en el módulo de **red** y haga clic en **Aceptar** en el módulo **opcional config** .
7. En el módulo de **Máquina virtual de crear** , haga clic en **crear**. Observe el mosaico siguiente aparece en el panel.

    ![Crear VM en el portal de Azure](./media/virtual-networks-static-ip-classic-pportal/figure05.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Cómo recuperar información de dirección IP privada estática de una máquina virtual

Para ver la información de dirección IP privada estática de la máquina virtual creada con los pasos anteriores, ejecute los siguientes pasos.

1. Desde el portal de Azure Azure, haga clic en **Examinar todos** > **máquinas virtuales de Windows (clásico)** > **DNS01** > **toda la configuración** > **direcciones IP** y observe la asignación de direcciones IP y la dirección IP tal como se muestra a continuación.

    ![Crear VM en el portal de Azure](./media/virtual-networks-static-ip-classic-pportal/figure06.png)

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Cómo quitar una dirección IP privada estática de una máquina virtual
Para quitar la dirección IP privada de la máquina virtual creada anteriormente, siga los pasos siguientes.
    
1. Desde el módulo de **direcciones IP** mostrado anteriormente, haga clic en **dinámicos** a la derecha de la **asignación de direcciones IP**, a continuación, haga clic en **Guardar**y, a continuación, haga clic en **Sí**.

    ![Crear VM en el portal de Azure](./media/virtual-networks-static-ip-classic-pportal/figure07.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Cómo agregar una dirección IP privada estática a una máquina virtual existente
Para agregar una dirección IP privada estática VM creado con los pasos anteriores, siga los pasos siguientes:

1. En el módulo de **direcciones IP** mostrado anteriormente, haga clic en **estático** a la derecha de la **asignación de direcciones IP**.
2. Escriba *192.168.1.101* para **la dirección IP**, a continuación, haga clic en **Guardar**y, a continuación, haga clic en **Sí**.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre las direcciones [IP públicas reservada](virtual-networks-reserved-public-ip.md) .
- Obtenga información sobre las direcciones [IP públicas (ILPIP) de nivel de instancia](virtual-networks-instance-level-public-ip.md) .
- Consulte las [API REST de IP reservada](https://msdn.microsoft.com/library/azure/dn722420.aspx).
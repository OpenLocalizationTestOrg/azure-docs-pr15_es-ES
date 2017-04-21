<properties 
   pageTitle="Cómo configurar una dirección IP privada estática en modo ARM con el portal de Azure | Microsoft Azure"
   description="Descripción de direcciones IP privada (DIP) y cómo se administran en modo ARM con el portal de Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
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

# <a name="how-to-set-a-static-private-ip-address-in-the-azure-portal"></a>Cómo configurar una dirección IP privada estática en el portal de Azure

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-arm-include](../../includes/virtual-networks-static-private-ip-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]En este artículo trata sobre el modelo de implementación de administrador de recursos. También puede [Administrar direcciones IP privado en el modelo de implementación clásico](virtual-networks-static-private-ip-classic-pportal.md).

[AZURE.INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Los siguientes pasos de ejemplo esperan un entorno simple ya ha creado. Si desea ejecutar los pasos que se muestran en este documento, cree primero el entorno de prueba que se describe en [crear un vnet](virtual-networks-create-vnet-arm-pportal.md).

## <a name="how-to-create-a-vm-for-testing-static-private-ip-addresses"></a>Cómo crear una máquina virtual para probar las direcciones IP privadas

No puede establecer una dirección IP privada estática durante la creación de una máquina virtual en el modo de implementación de administrador de recursos a través del portal de Azure. Debe crear la máquina virtual en primer lugar, tehn establecer su IP privada sea estático.

Para crear una máquina virtual denominada *DNS01* en la subred *front-end* de un VNet denominado *TestVNet*, siga los pasos siguientes.

1. Desde un explorador, vaya a http://portal.azure.com y, si es necesario, inicie sesión con su cuenta de Azure.
2. Haga clic en **nuevo** > **calcular** > **Centro de datos de Windows Server 2012 R2**, observe que la lista **Seleccionar un modelo de implementación** ya muestra **El Administrador de recursos**y, a continuación, haga clic en **crear**, tal como se muestra en la figura siguiente.

    ![Crear VM en el portal de Azure](./media/virtual-networks-static-ip-arm-pportal/figure01.png)

3. En el módulo de **conceptos básicos** , escriba el nombre de la máquina virtual que se cree (*DNS01* en nuestro ejemplo), la cuenta de administrador local y la contraseña, tal como se muestra en la figura siguiente.

    ![Módulo de conceptos básicos](./media/virtual-networks-static-ip-arm-pportal/figure02.png)

4. Asegúrese de que la **ubicación** seleccionada es *Central de Estados Unidos*, a continuación, haga clic en **Seleccionar existente** , en el **grupo de recursos**, a continuación, de nuevo, haga clic en **grupo de recursos** , a continuación, haga clic en *TestRG*y, a continuación, haga clic en **Aceptar**.

    ![Módulo de conceptos básicos](./media/virtual-networks-static-ip-arm-pportal/figure03.png)

5. En el módulo de **Elegir un tamaño** , seleccione **A1 estándar**y, a continuación, haga clic en **Seleccionar**.

    ![Elija un módulo de tamaño](./media/virtual-networks-static-ip-arm-pportal/figure04.png) 

6. En el módulo de **configuración** , asegúrese de que se establecen las siguientes propiedades se configuran con los valores siguientes y, a continuación, haga clic en **Aceptar**.

    -**Cuenta de almacenamiento**: *vnetstorage*
    - **Red**: *TestVNet*
    - **Subred**: *front-end*

    ![Elija un módulo de tamaño](./media/virtual-networks-static-ip-arm-pportal/figure05.png)  

7. En el módulo de **Resumen** , haga clic en **Aceptar**. Observe el mosaico siguiente aparece en el panel.

    ![Crear VM en el portal de Azure](./media/virtual-networks-static-ip-arm-pportal/figure06.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Cómo recuperar información de dirección IP privada estática de una máquina virtual

Para ver la información de dirección IP privada estática de la máquina virtual creada con los pasos anteriores, ejecute los siguientes pasos.

1. Desde el portal de Azure Azure, haga clic en **Examinar todos** > **máquinas virtuales** > **DNS01** > **toda la configuración** > **interfaces de red** y, a continuación, haga clic en la única interfaz de red enumeran.

    ![Implementar mosaico VM](./media/virtual-networks-static-ip-arm-pportal/figure07.png)

2. En el módulo de **interfaz de red** , haga clic en **todas las configuraciones** > **direcciones IP** y observe los valores de la **asignación** y la **dirección IP** .

    ![Implementar mosaico VM](./media/virtual-networks-static-ip-arm-pportal/figure08.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Cómo agregar una dirección IP privada estática a una máquina virtual existente
Para agregar una dirección IP privada estática VM creado con los pasos anteriores, siga los pasos siguientes:

1. Desde el módulo de **direcciones IP** mostrado anteriormente, haga clic en **estática** **asignación**.
2. Escriba *192.168.1.101* dirección **IP**y, a continuación, haga clic en **Guardar**.

    ![Crear VM en el portal de Azure](./media/virtual-networks-static-ip-arm-pportal/figure09.png)

>[AZURE.NOTE] Si, después de hacer clic en **Guardar** Observe que la asignación aún se establece en **dinámica**, significa que la dirección IP que escribió ya está en uso. Pruebe una dirección IP diferente.

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Cómo quitar una dirección IP privada estática de una máquina virtual
Para quitar la dirección IP privada de la máquina virtual creada anteriormente, siga el paso siguiente.
    
1. Desde el módulo de **direcciones IP** mostrado anteriormente, haga clic en **dinámicos** en **asignación**y, a continuación, haga clic en **Guardar**.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre las direcciones [IP públicas reservada](virtual-networks-reserved-public-ip.md) .
- Obtenga información sobre las direcciones [IP públicas (ILPIP) de nivel de instancia](virtual-networks-instance-level-public-ip.md) .
- Consulte las [API REST de IP reservada](https://msdn.microsoft.com/library/azure/dn722420.aspx).
<properties 
   pageTitle="Cómo crear NSGs en modo ARM con el portal de Azure | Microsoft Azure"
   description="Obtenga información sobre cómo crear e implementar NSGs en el BRAZO con el portal de Azure"
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

# <a name="how-to-manage-nsgs-using-the-azure-portal"></a>Cómo administrar NSGs con el portal de Azure

[AZURE.INCLUDE [virtual-networks-create-nsg-selectors-arm-include](../../includes/virtual-networks-create-nsg-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]En este artículo trata sobre el modelo de implementación de administrador de recursos. También puede [crear NSGs en el modelo de implementación clásico](virtual-networks-create-nsg-classic-ps.md).

[AZURE.INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

El ejemplo PowerShell comandos que esperan un entorno simple ya ha creado se basa en el ejemplo anterior. Si desea ejecutar los comandos que se muestran en este documento, primero implementar [esta plantilla](http://github.com/telmosampaio/azure-templates/tree/master/201-IaaS-WebFrontEnd-SQLBackEnd)para crear el entorno de prueba, haga clic en **implementar a Azure**, reemplace los valores de parámetro de forma predeterminada, si es necesario y siga las instrucciones en el portal. Los pasos siguientes use **C GSN** como el nombre de la plantilla se implementa en el grupo de recursos.

## <a name="create-the-nsg-frontend-nsg"></a>Crear la GSN GSN-front-end

Para crear el **Front-end de GSN** GSN tal como se muestra en el ejemplo anterior, siga los pasos siguientes.

1. Desde un explorador, vaya a http://portal.azure.com y, si es necesario, inicie sesión con su cuenta de Azure.
2. Haga clic en **Examinar >** > **grupos de seguridad de red**.

    ![Portal de Azure - NSGs](./media/virtual-networks-create-nsg-arm-pportal/figure11.png)

3. En el módulo de **grupos de seguridad de red** , haga clic en **Agregar**.
  
    ![Portal de Azure - NSGs](./media/virtual-networks-create-nsg-arm-pportal/figure12.png)

4. En el módulo **Crear grupo de seguridad de red** , cree un GSN denominado *GSN cliente* en el grupo de recursos de *C GSN* y, a continuación, haga clic en **crear**.

    ![Portal de Azure - NSGs](./media/virtual-networks-create-nsg-arm-pportal/figure13.png)

## <a name="create-rules-in-an-existing-nsg"></a>Crear reglas en un GSN existente

Para crear reglas en un GSN existente desde el portal de Azure, siga los pasos siguientes.

2. Haga clic en **Examinar >** > **grupos de seguridad de red**.

3. En la lista de NSGs, haga clic en **Front-end de GSN** > **reglas de seguridad entrante**

    ![Portal de Azure - GSN-front-end](./media/virtual-networks-create-nsg-arm-pportal/figure2.png)

4. En la lista de **reglas**de seguridad de entrada, haga clic en **Agregar**.

    ![Portal de Azure - Agregar regla](./media/virtual-networks-create-nsg-arm-pportal/figure3.png)

5. En el módulo **Agregar regla de seguridad entrante** , crear una regla con el nombre de *regla en la web* con prioridad de *200* permitir el acceso a través de *TCP* al puerto *80* a cualquier VM desde cualquier origen y, a continuación, haga clic en **Aceptar**. Observe que la mayoría de estos parámetros son valores predeterminados ya.

    ![Portal de Azure - configuración de regla](./media/virtual-networks-create-nsg-arm-pportal/figure4.png)

6. Después de unos segundos, verá la nueva regla en el GSN.

    ![Portal de Azure - nueva regla](./media/virtual-networks-create-nsg-arm-pportal/figure5.png)

7. Repita los pasos 6 para crear una regla de entrada con el nombre de *regla rdp* con una prioridad de *250* permitir el acceso a través de *TCP* al puerto *3389* a cualquier VM desde cualquier origen.

## <a name="associate-the-nsg-to-the-frontend-subnet"></a>Asociar el GSN a la subred front-end

1. Haga clic en **Examinar >** > **grupos de recursos** > **C GSN**.
2. En el módulo **GSN c** , haga clic en **...**  >  **TestVNet**.

    ![Portal de Azure - TestVNet](./media/virtual-networks-create-nsg-arm-pportal/figure14.png)

3. En el módulo de **configuración** , haga clic en **subredes** > **front-end** > **grupo de seguridad de red** > **GSN-front-end**.

    ![Portal de Azure - configuración de subred](./media/virtual-networks-create-nsg-arm-pportal/figure15.png)

4. En el módulo de **front-end** , haga clic en **Guardar**.

    ![Portal de Azure - configuración de subred](./media/virtual-networks-create-nsg-arm-pportal/figure16.png)

## <a name="create-the-nsg-backend-nsg"></a>Crear la GSN GSN-back-end

Para crear el **Back-end de GSN** GSN y asociar a la subred **back-end** , siga los pasos siguientes.

1. Repita los pasos de [crear la GSN front-end de GSN](#Create-the-NSG-FrontEnd-NSG) para crear un GSN denominado *GSN-back-end*
2. Repita los pasos de [crear reglas en un GSN existente](#Create-rules-in-an-existing-NSG) para crear las reglas de **entrada** en la tabla siguiente.

  	|Regla de entrada|Regla de salida|
  	|---|---|
  	|![Portal de Azure - regla de entrada](./media/virtual-networks-create-nsg-arm-pportal/figure17.png)|![Portal de Azure - regla de salida](./media/virtual-networks-create-nsg-arm-pportal/figure18.png)|

3. Repita los pasos de [asociar la GSN a la subred front-end](#Associate-the-NSG-to-the-FrontEnd-subnet) para asociar el **Back-end de GSN** GSN a la subred **back-end** .

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre cómo [Administrar NSGs existentes](virtual-network-manage-nsg-arm-portal.md)
- [Habilitar el registro](virtual-network-nsg-manage-log.md) para NSGs.
<properties 
   pageTitle="Administrar con el portal de vista previa en el Administrador de recursos de NSGs | Microsoft Azure"
   description="Obtenga información sobre cómo administrar existente NSGs con el portal de vista previa en el Administrador de recursos"
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
   ms.date="03/14/2016"
   ms.author="jdial" />

# <a name="manage-nsgs-using-the-preview-portal"></a>Administrar NSGs con el portal de vista previa

> [AZURE.SELECTOR]
- [Portal](virtual-network-manage-nsg-arm-portal.md)
- [PowerShell](virtual-network-manage-nsg-arm-ps.md)
- [CLI de Azure](virtual-network-manage-nsg-arm-cli.md)

[AZURE.INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásico.

[AZURE.INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

## <a name="retrieve-information"></a>Recuperar información

Puede ver su NSGs existentes, recuperar reglas para un GSN existente y averigüe qué recursos un GSN está asociado a.

### <a name="view-existing-nsgs"></a>Ver NSGs existentes
Para ver todas las NSGs existentes en una suscripción, siga los pasos siguientes.

1. Desde un explorador, vaya a http://portal.azure.com y, si es necesario, inicie sesión con su cuenta de Azure.
2. Haga clic en **Examinar >** > **grupos de seguridad de red**.

![Portal de Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure1.png)

3. Compruebe la lista de NSGs en el módulo de **grupos de seguridad de red** .

![Portal de Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure2.png)

Para ver la lista de NSGs en el grupo de recursos **GSN c** , siga los pasos siguientes. 

1. Haga clic en **grupos de recursos >** > **GSN c** > **...**.

![Portal de Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure3.png)

2. En la lista de recursos, busque los elementos que muestra el icono GSN, como se muestra en la siguiente hoja de **recursos** .

![Portal de Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure4.png)
         
### <a name="list-all-rules-for-an-nsg"></a>Lista de todas las reglas para un GSN

Para ver las reglas de un GSN denominado **GSN-front-end**, siga los pasos siguientes. 

1. En el módulo de **grupos de seguridad de red** , o el módulo de **recursos** mostrado anteriormente, haga clic en **GSN-front-end**.
2. En la pestaña **configuración** , haga clic en **reglas de seguridad entrante**.

![Portal de Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure5.png)

3. El módulo de **reglas de seguridad de entrada** se muestra como se muestra a continuación.

![Portal de Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure6.png)

4. En la pestaña **configuración** , haga clic en **reglas de seguridad saliente** para ver las reglas de salida.

>[AZURE.NOTE] Para ver las reglas de forma predeterminada, haga clic en el icono de **reglas de forma predeterminada** en la parte superior de la hoja que muestra las reglas.

### <a name="view-nsgs-associations"></a>Ver las asociaciones de NSGs

Para ver qué recursos que es la GSN **Front-end de GSN** asociar con, siga los pasos siguientes.

1. En el módulo de **grupos de seguridad de red** , o el módulo de **recursos** mostrado anteriormente, haga clic en **GSN-front-end**.
2. En la pestaña **configuración** , haga clic en **subredes** para ver qué subredes están asociadas a la GSN.

![Portal de Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure7.png)

3. En la pestaña **configuración** , haga clic en **interfaces de red** para ver qué están asociadas a la GSN NIC.

## <a name="manage-rules"></a>Administrar reglas

Puede agregar reglas a un GSN existente, editar las reglas existentes y quitar las reglas.

### <a name="add-a-rule"></a>Agregar una regla

Para agregar una regla que permita **el tráfico** al puerto **443** desde cualquier equipo para el **Cliente GSN** GSN, siga los pasos siguientes.

1. En el módulo de **grupos de seguridad de red** , o el módulo de **recursos** mostrado anteriormente, haga clic en **GSN-front-end**.
2. En la pestaña **configuración** , haga clic en **reglas de seguridad entrante**.
3. En el módulo de **reglas de seguridad de entrada** , haga clic en **Agregar**. A continuación, en el módulo de **Agregar regla de seguridad entrante** , rellenar los valores tal y como se muestra a continuación y, a continuación, haga clic en **Aceptar**.

![Portal de Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure8.png)

4. Después de unos segundos, observe que la nueva regla en el módulo de **reglas de seguridad entrante** .

![Portal de Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure9.png)

### <a name="change-a-rule"></a>Cambiar una regla

Para cambiar la regla que creó anteriormente para permitir el tráfico de **Internet** , siga los pasos siguientes.

1. En el módulo de **grupos de seguridad de red** , o el módulo de **recursos** mostrado anteriormente, haga clic en **GSN-front-end**.
2. En la pestaña **configuración** , haga clic en la regla que creó anteriormente.
3. En el módulo **https permitir** , cambie la propiedad **origen** tal como se muestra a continuación y, a continuación, haga clic en **Guardar**.

![Portal de Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure10.png)

### <a name="delete-a-rule"></a>Eliminar una regla

Para eliminar la regla que creó anteriormente, siga los pasos siguientes.

1. En el módulo de **grupos de seguridad de red** , o el módulo de **recursos** mostrado anteriormente, haga clic en **GSN-front-end**.
2. En la pestaña **configuración** , haga clic en la regla que creó anteriormente.
3. En el módulo **https permitir** , haga clic en **Eliminar**y, a continuación, haga clic en **Sí**.

![Portal de Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure11.png)

## <a name="manage-associations"></a>Administrar las asociaciones

Puede asociar una GSN a subredes y NIC. También puede desasociar una GSN de cualquier recurso a que está asociado.

### <a name="associate-an-nsg-to-a-nic"></a>Asociar un GSN a una NIC

Para asociar el **Front-end de GSN** GSN a **TestNICWeb1** NIC, siga los pasos siguientes.

1. En el módulo de **grupos de seguridad de red** , o el módulo de **recursos** mostrado anteriormente, haga clic en **GSN-front-end**.
2. En la pestaña **configuración** , haga clic en **interfaces de red** > **asociar** > **TestNICWeb1**.

![Portal de Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure12.png)

### <a name="dissociate-an-nsg-from-a-nic"></a>Desvincular un GSN desde una NIC

Para eliminar la asociación de **Front-end de GSN** GSN de **TestNICWeb1** NIC, siga los pasos siguientes.

1. Desde el portal de Azure, haga clic en **grupos de recursos >** > **GSN c** > **...**  >  **TestNICWeb1**.
2. En el módulo **TestNICWeb1** , haga clic en **seguridad de cambio...**  > **None**.

![Portal de Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure13.png)

>[AZURE.NOTE] También puede usar este módulo para asociar la NIC para cualquier GSN existente.

### <a name="dissociate-an-nsg-from-a-subnet"></a>Desvincular un GSN desde una subred

Para desvincular el **Front-end de GSN** GSN desde la subred **front-end** , siga los pasos siguientes.

1. Desde el portal de Azure, haga clic en **grupos de recursos >** > **GSN c** > **...**  >  **TestVNet**.
2. En el módulo de **configuración** , haga clic en **subredes** > **front-end** > **grupo de seguridad de red** > **Ninguno**.

![Portal de Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure14.png)

3. En el módulo de **front-end** , haga clic en **Guardar**.

![Portal de Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure15.png)

### <a name="associate-an-nsg-to-a-subnet"></a>Asociar un GSN a una subred

Para asociar el **Front-end de GSN** GSN a la subred **FronEnd** nuevamente, siga los pasos siguientes.

1. Desde el portal de Azure, haga clic en **grupos de recursos >** > **GSN c** > **...**  >  **TestVNet**.
2. En el módulo de **configuración** , haga clic en **subredes** > **front-end** > **grupo de seguridad de red** > **GSN-front-end**.
3. En el módulo de **front-end** , haga clic en **Guardar**.

>[AZURE.NOTE] También puede asociar una GSN a una subred del módulo de **configuración** de thh GSN.

## <a name="delete-an-nsg"></a>Eliminar un GSN

Solo puede eliminar un GSN si no está asociada a ningún recurso. Para eliminar un GSN, siga los pasos siguientes.

1. Desde el portal de Azure, haga clic en **grupos de recursos >** > **GSN c** > **...**  >  **GSN-front-end**.
2. En el módulo de **configuración** , haga clic en **interfaces de red**.
3. Si hay cualquier NIC que aparece, haga clic en la NIC y siga el paso 2 en [Dissociate un GSN desde una NIC](#Dissociate-an-NSG-from-a-NIC).
4. Repita el paso 3 para cada NIC.
5. En el módulo de **configuración** , haga clic en **subredes**.
6. Si hay ninguna subred que aparece, haga clic en la subred y siga los pasos 2 y 3 en [Dissociate un GSN desde una subred](#Dissociate-an-NSG-from-a-subnet).
7. Se desplaza hacia la izquierda para el módulo **GSN-front-end** , a continuación, haga clic en **Eliminar** > **Sí**.

[Portal de Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure16.png)

## <a name="next-steps"></a>Pasos siguientes

- [Habilitar el registro](virtual-network-nsg-manage-log.md) para NSGs.

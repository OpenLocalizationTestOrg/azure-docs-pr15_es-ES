## <a name="how-to-create-a-classic-vnet-in-the-azure-portal"></a>Cómo crear un VNet clásico en el portal de Azure

Para crear un VNet clásico según el escenario anterior, siga los pasos siguientes.

1. Desde un explorador, vaya a http://portal.azure.com y, si es necesario, inicie sesión con su cuenta de Azure.
2. Haga clic en **nuevo** > **redes** > **red Virtual**, observe que la lista **Seleccionar un modelo de implementación** ya muestra **clásica**y, a continuación, haga clic en **crear**, tal como se muestra en la figura siguiente.

    ![Crear VNet en el portal de Azure](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure1.gif)

3. En el módulo de **red Virtual** , escriba el **nombre** de la VNet y, a continuación, haga clic en **el espacio de direcciones**. Configurar la configuración de espacio de dirección para el VNet y su primera subred y luego haga clic en **Aceptar**. La figura siguiente muestra la configuración de bloqueo CIDR para nuestro escenario.

    ![Módulo de espacio de dirección](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure2.png)

4. Haga clic en el **Grupo de recursos** y seleccione un grupo de recursos para agregar el VNet a o haga clic en **Crear nuevo grupo de recursos** para agregar el VNet a un nuevo grupo de recursos. La figura siguiente muestra el recurso de configuración de grupo para un nuevo grupo de recursos denominado **TestRG**. Para obtener más información acerca de los grupos de recursos, visite [Información general del Administrador de recursos de Azure](../articles/virtual-network/resource-group-overview.md#resource-groups).

    ![Crear el módulo de grupo de recursos](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure3.png)

5. Si es necesario, cambie la configuración de **suscripción** y la **ubicación** de su VNet. 

6. Si no desea ver el VNet como un mosaico en el **Startboard**, deshabilitar **Pin a Startboard**. 

7. Haga clic en **crear** y observe el mosaico denominado **red Virtual crear** tal como se muestra en la figura siguiente.

    ![Crear VNet en el portal](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure4.png)

8. Espere a que el VNet que se cree y cuando vea el mosaico a continuación, haga clic en él para agregar más subredes.

    ![Crear VNet en el portal](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure5.png)

9. Debe ver la **configuración** para su VNet tal como se muestra a continuación. 

    ![Crear VNet en el portal](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure6.png)

10. Haga clic en **subredes** > **Agregar**, a continuación, escriba un **nombre** y especifique un **intervalo de direcciones (bloque CIDR)** para su subred y, a continuación, haga clic en **Aceptar**. La figura siguiente muestra la configuración de nuestro escenario actual.

    ![Crear VNet en el portal de Azure](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure7.gif)
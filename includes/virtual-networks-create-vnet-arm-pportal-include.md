## <a name="how-to-create-a-vnet-in-the-azure-portal"></a>Cómo crear un VNet en el portal de Azure

Para crear un VNet basado en el escenario anterior mediante el portal de vista previa de Azure, siga los pasos siguientes.

1. Desde un explorador, vaya a http://portal.azure.com y, si es necesario, inicie sesión con su cuenta de Azure.
2. Haga clic en **nuevo** > **redes** > **red Virtual**, a continuación, haga clic en **Administrador de recursos** de la lista **Seleccionar un modelo de implementación** y, a continuación, haga clic en **crear**, tal como se muestra en la figura siguiente.

    ![Crear VNet en el portal de Azure](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure1.gif)

3. En el módulo de **crear una red virtual** , la configuración VNet tal como se muestra en la figura siguiente.

    ![Crear el módulo de red virtual](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure2.png)

4. Haga clic en el **grupo de recursos** y seleccione un grupo de recursos para agregar el VNet a o haga clic en **Crear nuevo** para agregar el VNet a un nuevo grupo de recursos. La figura siguiente muestra el recurso de configuración de grupo para un nuevo grupo de recursos denominado **TestRG**. Para obtener más información acerca de los grupos de recursos, visite [Información general del Administrador de recursos de Azure](../articles/resource-group-overview.md#resource-groups).

    ![Grupo de recursos](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure3.png)

5. Si es necesario, cambie la configuración de **suscripción** y la **ubicación** de su VNet. 

6. Si no desea ver el VNet como un mosaico en el **Startboard**, deshabilitar **Pin a Startboard**. 

7. Haga clic en **crear** y observe el mosaico denominado **red Virtual crear** tal como se muestra en la figura siguiente.

    ![Crear el mosaico de una red virtual](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure4.png)

8. Espere a que la VNet creación y en el módulo de **red Virtual** , haga clic en **todas las configuraciones** > **subredes** > **Agregar** tal como se muestra a continuación.

    ![Agregar subred en el portal de Azure](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure5.gif)

9. Especificar la configuración de subred para la subred *back-end* , como se muestra a continuación y, a continuación, haga clic en **Aceptar**. 

    ![Configuración de subred](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure6.png)

10. Observe la lista de subredes, como se muestra en la siguiente ilustración.

    ![Lista de subredes en VNet](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure7.png)

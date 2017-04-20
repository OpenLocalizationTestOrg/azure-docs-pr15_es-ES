## <a name="deploy-the-arm-template-by-using-click-to-deploy"></a>Implementar la plantilla ARM mediante hacer clic para implementar

Puede reutilizar carga de plantillas ARM predefinida a un repositorio de github mantenida Microsoft y abra a la Comunidad. Estas plantillas pueden implementadas directamente fuera github, o descargar y modificar para adaptarlos a sus necesidades. Para implementar una plantilla que crea una VNet con dos subredes, siga los pasos siguientes.

1. Desde un explorador, vaya a [https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates).
2. Desplácese por la lista de plantillas y haga clic en **subredes-101-vnet-dos**. Compruebe el archivo **README.md** , tal como se muestra a continuación.

    ![Archivo de READEME.md en github](./media/virtual-networks-create-vnet-arm-template-click-include/figure1.png)

3. Haga clic en **desplegar en Azure**. Si es necesario, escriba sus credenciales de inicio de sesión de Azure. 
4. En el módulo de **parámetros** , escriba los valores que desea usar para crear su nuevo VNet y, a continuación, haga clic en **Aceptar**. La ilustración siguiente muestra los valores de nuestro escenario.

    ![Parámetros de plantilla ARM](./media/virtual-networks-create-vnet-arm-template-click-include/figure2.png)

4. Haga clic en el **grupo de recursos** y seleccione un grupo de recursos para agregar el VNet a o haga clic en **Crear nuevo** para agregar el VNet a un nuevo grupo de recursos. La figura siguiente muestra el recurso de configuración de grupo para un nuevo grupo de recursos denominado **TestRG**.

    ![Grupo de recursos](./media/virtual-networks-create-vnet-arm-template-click-include/figure3.png)

5. Si es necesario, cambie la configuración de **suscripción** y la **ubicación** de su VNet.
6. Si no desea ver el VNet como un mosaico en el **Startboard**, deshabilitar **Pin a Startboard**.
5. Haga clic en **términos de Leagl**, lea los términos y haga clic en **comprar** para que coincidan. 
6. Haga clic en **crear** para crear la VNet.

    ![Envío implementación de mosaico en el portal de vista previa](./media/virtual-networks-create-vnet-arm-template-click-include/figure4.png)

7. Una vez realizada la implementación, haga clic en **TestVNet** > **toda la configuración** > **subredes** para ver las propiedades de subred, como se muestra a continuación.

    ![Crear VNet en el portal de vista previa](./media/virtual-networks-create-vnet-arm-template-click-include/figure5.gif)
Para crear un VNet en el modelo de implementación de administrador de recursos con el portal de Azure, siga los pasos siguientes. Las capturas de pantalla se proporcionan ejemplos. Asegúrese de reemplazar los valores con su propio. Para obtener más información sobre cómo trabajar con las redes virtuales, vea la [Información general de red Virtual](../articles/virtual-network/virtual-networks-overview.md).

1. Desde un explorador, vaya al [portal de Azure](http://portal.azure.com) y, si es necesario, inicie sesión con su cuenta de Azure.

2. Haga clic en **nuevo**. En el campo **el catálogo de soluciones de búsqueda** , escriba "Red Virtual". Busque **Una red Virtual** de la lista devuelta y haga clic para abrir el módulo de **Red Virtual** .

    ![Módulo de recursos de encontrar una red Virtual] (./media/vpn-gateway-basic-vnet-rm-portal-include/newvnetportal700.png "Módulo de recursos de una red virtual de buscar")

3. En la parte inferior del módulo de red Virtual, en la lista **Seleccionar un modelo de implementación** , seleccione **Administrador de recursos**y, a continuación, haga clic en **crear**.


    ![Seleccione el Administrador de recursos] (./media/vpn-gateway-basic-vnet-rm-portal-include/resourcemanager250.png "Seleccione el Administrador de recursos")

4. En el módulo de **crear una red virtual** , configure las opciones de VNet. Cuando rellena los campos, el signo de exclamación rojo se convertirá en una marca de verificación verde cuando los caracteres especificados en el campo son válidos.

    ![Validación de campo] (./media/vpn-gateway-basic-vnet-rm-portal-include/checkmark300.png "Validación de campo")

5. El módulo de **crear una red virtual** es similar al siguiente ejemplo. Es posible que los valores que se rellena automáticamente. Si es así, reemplace los valores con su propio.

    ![Módulo de crear una red virtual] (./media/vpn-gateway-basic-vnet-rm-portal-include/createvnet300.png "Módulo de crear una red virtual")

6. **Nombre**: escriba el nombre de su red Virtual.

7. **Espacio de direcciones**: indique el espacio de direcciones. Si tiene varios espacios de direcciones para agregar, agregue el primer espacio de dirección. Puede agregar espacios de direcciones adicionales más tarde, después de crear la VNet.
 
8. **Nombre de subred**: agregar el nombre de subred y el intervalo de direcciones de subred. Puede agregar subredes adicionales más tarde, después de crear la VNet.

10. **Suscripción**: Compruebe que la suscripción que aparece es correcta. Puede cambiar las suscripciones mediante la lista desplegable.

11. **Grupo de recursos**: seleccione un grupo de recursos existente o cree uno nuevo, escriba un nombre para el nuevo grupo de recursos. Si va a crear un grupo nuevo, el nombre del grupo de recursos según sus valores de configuración planeada. Para obtener más información acerca de los grupos de recursos, visite [Información general del Administrador de recursos de Azure](resource-group-overview.md#resource-groups).

12. **Ubicación**: seleccione la ubicación de su VNet. La ubicación determina dónde se guardarán los recursos que se implementación en este VNet.

13. Seleccione **Anclar al panel** si desea que puedan encontrar su VNet fácilmente en el panel y, a continuación, haga clic en **crear**.
    
    ![Anclar a paneles] (./media/vpn-gateway-basic-vnet-rm-portal-include/pintodashboard150.png "Anclar a paneles")

14. Después de hacer clic en **crear**, verá un icono en el panel que refleje el progreso de su VNet. El mosaico cambia a medida que se va a crear la VNet.

    ![Crear una red virtual de mosaico] (./media/vpn-gateway-basic-vnet-rm-portal-include/deploying150.png "Crear una red virtual de mosaico")
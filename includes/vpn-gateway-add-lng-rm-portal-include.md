1. En el portal de **todos los recursos**, haga clic en **+ Add**. En la **todo** placa cuadro de búsqueda, escriba la **puerta de enlace de red Local**, haga clic en para buscar. Esto devolverá una lista. Haga clic en la **puerta de enlace de red Local** para abrir el módulo, haga clic en **crear** para abrir el módulo de **puerta de enlace de crear red local** .

    ![crear la puerta de enlace de red local](./media/vpn-gateway-add-lng-rm-portal-include/addlng250.png)

2. En el **módulo de puerta de enlace de red local de crear**, especifique un **nombre** para el objeto de la puerta de enlace de red local.
 
3. Especifique una **dirección IP** pública válida para el dispositivo VPN o puerta de enlace de red virtual al que desea conectarse.<br>Si esta red local representa una ubicación local, esta es la dirección IP pública del dispositivo VPN que desea conectarse. No puede estar detrás de NAT y tiene que ser accesible para Azure.<br>Si esta red local representa otra VNet, especificará la dirección IP pública que se ha asignado a la puerta de enlace de red virtual para ese VNet.<br>

4. **Espacio de direcciones** se refiere a los intervalos de direcciones de la red que representa la red local. Puede agregar varios intervalos de espacio de direcciones. Asegúrese de que los rangos que especifique aquí no se superpone con rangos de otras redes que desea conectarse.
 
5. Para la **suscripción**, compruebe que se muestra la suscripción correcta.

6. Para el **Grupo de recursos**, seleccione el grupo de recursos que desea usar. Puede crear un nuevo grupo de recursos o seleccionar una que ya ha creado.

7. **Ubicación**, seleccione la ubicación que este objeto se creará en. Desea seleccionar la misma ubicación que reside el VNet, pero no es necesario para hacerlo.

8. Haga clic en **crear** para crear la puerta de enlace de red local.

1. En el portal, vaya a **nuevo**. Escriba "Puerta de enlace de red Virtual" en la búsqueda. Busque la **puerta de enlace de red Virtual** en la búsqueda de devolución y haga clic en la entrada. Se abrirá el módulo de **puerta de enlace de red virtual de crear** .
2. Haga clic en **crear** en la parte inferior de la hoja de **puerta de enlace de red Virtual** . Se abrirá el módulo de **puerta de enlace de red virtual de crear** . Rellene los valores de la puerta de enlace de red virtual.

    ![Campos de hoja de puerta de enlace de crear una red virtual] (./media/vpn-gateway-add-gw-rm-portal-include/createvnetgw300.png "Campos de hoja de puerta de enlace de crear una red virtual")

3. **Nombre**: el nombre de la puerta de enlace. No es igual a una subred de puerta de enlace de nombres. Es el nombre del objeto de puerta de enlace que se va a crear.

4. **Tipo de puerta de enlace**: seleccione **VPN**. Puertas de enlace VPN utilizan el tipo de puerta de enlace de red virtual **VPN**. 

5. **Tipo de VPN**: seleccione el tipo VPN que se especifica para su configuración. La mayoría de las configuraciones requieren un tipo VPN basada en la ruta.

6. **SKU**: seleccione la puerta de enlace SKU de la lista desplegable. Las SKU que aparece en la lista desplegable dependen del tipo VPN que se selecciona.

7. **Ubicación**: ajustar el campo **ubicación** para que apunten a la ubicación donde se encuentra su red virtual.
 
8. Elija la red virtual al que desea agregar esta puerta de enlace. Haga clic en **red Virtual** para abrir el módulo de **Elegir una red virtual** . Seleccione el VNet. Si no ve su VNet, asegúrese de que el campo **ubicación** apunta a la región donde se encuentra su red virtual.

9. Elija una dirección IP pública. Haga clic en **dirección IP pública** para abrir el módulo de **dirección IP pública de elegir** . Haga clic en **+ Crear nuevo** para abrir el **módulo de dirección IP pública de crear**. Introduzca un nombre para la dirección IP pública. Este módulo crea un objeto de direcciones IP público a la que una dirección IP pública se asignará dinámicamente.<br>Haga clic en **Aceptar** para guardar los cambios a este módulo.

10. **Suscripción**: Compruebe que está seleccionada la suscripción correcta.

11. **Grupo de recursos**: esta configuración depende de la red Virtual que seleccione. 

12. No ajustar la **ubicación** después de especificar la configuración anterior.

13. Compruebe la configuración. **Anclar al panel** en la parte inferior de la hoja puede seleccionar si desea que aparezca en el panel de la puerta de enlace.

14. Haga clic en **crear** para empezar a crear la puerta de enlace. La configuración se valida y se verá "Virtual de implementación de puerta de enlace de red" en el panel de mosaico. Creación de una puerta de enlace, puede tardar hasta 45 minutos. Debe actualizar su página de portal para ver el estado completado.

    ![Implementar Virtual puerta de enlace de red] (./media/vpn-gateway-add-gw-rm-portal-include/deployvnetgw150.png "Implementar Virtual puerta de enlace de red")

11. Después de crea la puerta de enlace, puede ver la dirección IP que se le ha asignado consultando la red virtual en el portal. La puerta de enlace aparecerá como un dispositivo conectado. Puede hacer clic en el dispositivo conectado (la puerta de enlace de red virtual) para obtener más información.




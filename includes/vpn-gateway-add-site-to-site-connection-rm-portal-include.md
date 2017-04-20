1. Busque la puerta de enlace de red virtual y haga clic en **todas las opciones** para abrir la hoja de **configuración** .

2. En el módulo de **configuración** , haga clic en **conexiones**y, a continuación, haga clic en **Agregar** en la parte superior de la hoja para abrir el módulo de **Agregar conexión** .

    ![Crear la conexión de sitio a sitio](./media/vpn-gateway-add-site-to-site-connection-rm-portal-include/addconnection250.png)

3. En el módulo de **Agregar conexión** , en el **nombre de** la conexión. 

4. **Tipo de conexión**, seleccione **Site-to-site(IPSec)**.

5. De **puerta de enlace de red Virtual**, el valor se fija porque se conecta desde esta puerta de enlace.

6. De **puerta de enlace de red Local**, haga clic en **Elegir una puerta de enlace de red local** y seleccione la puerta de enlace de red local que desea usar. 

7. **Clave compartida**, aquí el valor debe coincidir con el valor que se utiliza para su dispositivo VPN local. Si el dispositivo VPN en su red local no proporciona una clave compartida, puede realizar uno hacia arriba e introducirlo aquí y en el dispositivo local. Lo importante es que ambos coinciden.

8. Se corrigen los valores restantes para la **suscripción**, el **Grupo de recursos**y la **ubicación** .

9. Haga clic en **Aceptar** para crear la conexión. Verá *Crear conexión* en la pantalla.

10. Una vez finalizada la conexión, verá aparecen en el módulo de **conexiones** de la puerta de enlace.

    ![Crear la conexión de sitio a sitio](./media/vpn-gateway-add-site-to-site-connection-rm-portal-include/connectionstatus450.png)


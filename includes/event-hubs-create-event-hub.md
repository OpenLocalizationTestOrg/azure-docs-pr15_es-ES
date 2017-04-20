## <a name="create-an-event-hub"></a>Crear un concentrador de evento

1. Inicie sesión en el [portal de Azure][]y a continuación, haga clic en **nuevo** en la parte superior izquierda de la pantalla.

2. Haga clic en **datos + análisis**, haga clic en **Evento Hubs**.

    ![](./media/event-hubs-create-event-hub/create-event-hub9.png)

3. En el módulo de **espacio de nombres de crear** , escriba un nombre de espacio de nombres. El sistema inmediatamente comprueba si el nombre está disponible.

    ![](./media/event-hubs-create-event-hub/create-event-hub1.png)

4. Una vez que asegurarse de que el espacio de nombres está disponible, elija el nivel de precios (Basic o estándar). Además, elija una suscripción Azure, el grupo de recursos y la ubicación en la que desea crear el recurso. 

2. Haga clic en **crear** para crear el espacio de nombres.

6. En la lista de nombres de evento Hubs, haga clic en el espacio de nombres recién creado.      

    ![](./media/event-hubs-create-event-hub/create-event-hub2.png)

7. En el módulo de espacio de nombres, haga clic en **Evento Hubs**.

    ![](./media/event-hubs-create-event-hub/create-event-hub3.png)

8. En la parte superior de la hoja, haga clic en **Agregar concentrador de evento**.

    ![](./media/event-hubs-create-event-hub/create-event-hub4.png)

3. Escriba un nombre para su centro de evento y luego haga clic en **crear**.

    ![](./media/event-hubs-create-event-hub/create-event-hub5.png)

4. En la lista de Hubs de evento, haga clic en el nombre de evento concentrador recién creado. 

    ![](./media/event-hubs-create-event-hub/create-event-hub6.png)

5. En el módulo de espacio de nombres (no el módulo de concentrador de evento específico), haga clic en **directivas de acceso compartido**y, a continuación, haga clic en **RootManageSharedAccessKey**.

    ![](./media/event-hubs-create-event-hub/create-event-hub7.png)

5. Haga clic en el botón Copiar para copiar la cadena de conexión de **RootManageSharedAccessKey** en el Portapapeles. Guardar esta cadena de conexión para usar más adelante en el tutorial.

    ![](./media/event-hubs-create-event-hub/create-event-hub8.png)

Ahora se crea su centro de eventos y tiene las cadenas de conexión que debe enviar y recibir eventos.

[Portal de Azure]: https://portal.azure.com/
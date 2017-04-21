
El Administrador de conexión híbrido permite a su equipo local conectar con Azure y retransmisión de tráfico TCP. Debe instalar el Administrador de un equipo local que puede conectarse a la la instancia de SQL Server.

1. La conexión que acaba de crear debería tener un **estado** de **configuración incompleta en premesis**. Haga clic en esta conexión y haga clic en **el programa de instalación local**.

    ![Instalación local](./media/hybrid-connections-install-connection-manager/5-1.png)

2. Haga clic en **instalar y configurar**.

    Instala una instancia personalizada de Connection Manager, que ya está preconfigurado para que funcione con la conexión de híbrido que acaba de crear.

3. Complete el resto de los pasos de configuración para el Administrador de conexión.

    Una vez completada la instalación, cambiará el estado de conexión híbrido **1**conectado de instancia. Debe actualizar el explorador y espere unos minutos. 

Configuración de la conexión de híbrido está ahora completa.

1. En el módulo de **conexiones híbrido** , haga clic en la conexión de híbrido que acaba de crear, haga clic en **Configuración de escucha**.
    
    ![Haga clic en configuración de escucha](./media/app-service-hybrid-connections-manager-install/D04ClickListenerSetup.png)
    
4. Se abre la hoja de **Propiedades de conexión híbrido** . En **El Administrador de conexión de local híbrido**, elija **descargar y configurar manualmente**, guardar el paquete de HybridConnectionManager.msi descargado y copie la cadena de conexión de la puerta de enlace.
    
    ![Haga clic aquí para instalar](./media/app-service-hybrid-connections-manager-install/D05ClickToInstallHCM.png)
    
5. Desde un símbolo del sistema de administrador, escriba el comando siguiente para iniciar al instalador:

        start HybridConnectionManager.msi
 
7. Después de ejecuta el programa de instalación, haga clic en **no ahora**, a continuación, vaya a la carpeta %ProgramFiles%\Microsoft\HybridConnectionManager, ejecute HCMConfigWizard.exe y haga clic en **Sí** en el cuadro de diálogo **Control de cuentas de usuario** .
        
7. Pegue la cadena de conexión híbrido que copió anteriormente y haga clic en **Aceptar**. 
    
    ![Instalar](./media/app-service-hybrid-connections-manager-install/D08aHCMInstallManual.png)
    
8. Cuando finalice la instalación, haga clic en **Cerrar**.
    
    ![Haga clic en Cerrar](./media/app-service-hybrid-connections-manager-install/D09HCMInstallComplete.png)
    
    En el módulo de **conexiones híbrido** , la columna **estado** muestra ahora **conectado**. 
    
    ![Estado conectado](./media/app-service-hybrid-connections-manager-install/D10HCStatusConnected.png)
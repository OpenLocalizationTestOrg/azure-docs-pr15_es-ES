
1. Visite el [Portal de Azure]. Haga clic en **Examinar todos** > **Aplicaciones Mobile** > el back-end que acaba de crear. En la configuración de la aplicación móvil, haga clic en **Tutorial rápido** > **Cordova**. En **Configurar la aplicación de cliente**, seleccione **crear una nueva aplicación**y después haga clic en **Descargar**. Esto descarga un proyecto completo de Cordova para una aplicación preconfigurado para conectarse a su back-end.

2. Descomprimir el archivo ZIP descargado en un directorio en el disco duro, desplácese hasta el archivo de solución (sln) y abrirlo con Visual Studio.

5. En Visual Studio, elija la plataforma de soluciones (Android, iOS o Windows) de la lista desplegable situada junto a la flecha de inicio y luego seleccione un dispositivo de implementación específica o emulador haciendo clic en la lista desplegable en la flecha verde. Tenga en cuenta que puede usar la plataforma de Android predeterminada y emulador de onda. Tutoriales más avanzadas requieren que se seleccione un dispositivo admitido o emulador. 

6. Presione F5 o haga clic en la flecha verde para generar y y ejecute la aplicación Cordova. Si ve un cuadro de diálogo de seguridad en el emulador solicita el acceso a la red, aceptarla.   

7. Después de la se inicia la aplicación en el dispositivo o emulador, escriba texto significativo en **Escriba el texto nuevo**, como _completado el tutorial_ y, a continuación, haga clic en el botón **Agregar** .  
Envía una petición POST a Azure copia back-end que ha implementado una versión anterior. Los datos de inserciones de back-end de la solicitud está en la tabla TodoItem en la base de datos de SQL y devuelve información acerca de los elementos recién almacenados volver a la aplicación móvil. La aplicación móvil muestra estos datos en la lista.

    ![](./media/app-service-mobile-cordova-quickstart/quickstart-startup.png)
    
8. Repita los tres pasos anteriores para cada plataforma de dispositivo que vaya a soporte técnico.

[Portal de Azure]: https://portal.azure.com/

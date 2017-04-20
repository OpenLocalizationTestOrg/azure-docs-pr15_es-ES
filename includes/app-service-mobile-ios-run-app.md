
1. En el Mac, visite el [Portal de Azure]. Haga clic en **Examinar todos** > **Aplicaciones Mobile** > el back-end que acaba de crear. En la configuración de la aplicación móvil, haga clic en **Tutorial rápido** > **iOS (objetivo-C)**. Si prefiere Swift, haga clic en **Tutorial rápido** > **iOS (Swift)** en su lugar. **Descargar y ejecuta el proyecto de iOS**, haga clic en **Descargar**. Esto descarga un proyecto completo de Xcode para una aplicación preconfigurado para conectarse a su back-end. Abra el proyecto mediante Xcode.

2. Presione el botón **Ejecutar** para generar el proyecto e inicie la aplicación en el simulador iOS.

3. En la aplicación, escriba el texto descriptivo, como _completado el tutorial_ y, a continuación, haga clic en el signo más (**+**) icono. Envía una petición POST a Azure copia back-end que ha implementado una versión anterior. Los datos de inserciones de back-end de la solicitud está en la tabla de SQL TodoItem y devuelve información acerca de los elementos recién almacenados volver a la aplicación móvil. La aplicación móvil muestra estos datos en la lista. 

    ![](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[Portal de Azure]: https://portal.azure.com/

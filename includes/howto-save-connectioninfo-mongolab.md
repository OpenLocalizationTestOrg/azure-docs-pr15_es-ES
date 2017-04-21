Si es posible pegar un URI MongoLab en su código, recomendamos configurar en el entorno de facilidad de administración. De esta forma, si cambia el URI, puede actualizar él a través del Portal de Azure sin ir al código.


1. En el Portal de Azure, seleccione **Aplicaciones Web**.
1. Haga clic en el nombre de la aplicación web en la lista de aplicaciones Web.  
![WebAppEntry][entry-website]  
Muestra el panel de la aplicación Web.

1. En la barra de menús, haga clic en **Configurar** .  
![WebAppDashboardConfig][focus-mongolab-websitedashboard-config]

1. Desplácese hacia abajo hasta la sección de cadenas de conexión.  
![WebAppConnectionStrings][focus-mongolab-websiteconnectionstring]

1. En **nombre**, escriba MONGOLAB_URI.
1. **Valor**, pegue la cadena de conexión que se obtienen en la sección anterior.
1. Seleccione **personalizado** en la lista desplegable de **tipo** (en lugar del predeterminado **SQLAzure**).
1. En la barra de herramientas, haga clic en **Guardar** .  
![SaveWebApp][button-website-save]

**Nota:** Azure agrega la **CUSTOMCONNSTR\_ ** prefijo a esta variable, que es el motivo por el código anterior referencias **CUSTOMCONNSTR\_MONGOLAB_URI.**

[entry-website]: ./media/howto-save-connectioninfo-mongolab/entry-website.png
[focus-mongolab-websitedashboard-config]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websitedashboard-config.png
[focus-mongolab-websiteconnectionstring]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websiteconnectionstring.png
[button-website-save]: ./media/howto-save-connectioninfo-mongolab/button-website-save.png

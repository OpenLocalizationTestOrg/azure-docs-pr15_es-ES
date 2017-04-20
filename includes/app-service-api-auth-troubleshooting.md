Resultado de la mayoría de los errores de autenticación de tiempo de configuración incorrecta o incoherente. Estas son algunas sugerencias para las siguientes comprobaciones específicas.

* Asegúrese de que no pierda el botón **Guardar** en cualquier lugar. Esto es muy fácil de hacer, y el resultado es que se utilizan en los valores correctos en una página de portal pero realmente no se han guardado en el entorno de Azure o una aplicación de Azure AD.
* Para valores configurados en el módulo de **Configuración de la aplicación** del portal de Azure, asegúrese de que la aplicación correcta de API o web app se ha seleccionado cuando se introduce la configuración.  También asegúrese de que la configuración se ha especificado como la **configuración de la aplicación** y no **las cadenas de conexión**, como el formato de las dos secciones es similar.
* Para la autenticación de un front-end de JavaScript, descargue el manifiesto para comprobar que `oauth2AllowImplicitFlow` se cambió correctamente a `true`.
* Compruebe que utiliza HTTPS donde esté configurado direcciones URL:

    * En el código del proyecto
    * En CORS
    * En configuración de la aplicación de Azure entorno para cada aplicación de la API y la aplicación web
    * En configuración de la aplicación de Azure AD.
    
    Tenga en cuenta que si copia la dirección URL de una aplicación de API desde el portal, a menudo `http://` y tendrá que cambiar manualmente a `https://`.

* Asegúrese de que los cambios de código se implementaron correctamente. Por ejemplo, en una solución de varios proyectos es posible cambiar el código de un proyecto y elija accidentalmente uno de los demás si va a implementar el cambio.
* Asegúrese de que se va a las direcciones URL HTTPS en el explorador, no direcciones URL de HTTP. De forma predeterminada, Visual Studio crea perfiles con direcciones URL de HTTP de publicación, y eso es lo que se abre en el explorador después de implementar un proyecto.
* Para la autenticación de un front-end de JavaScript, asegúrese de que CORS está configurado correctamente en la aplicación de la API que llama el código de JavaScript. En caso de duda sobre si el problema está relacionado con el CORS, intente "*" como la dirección URL de origen permitidos. 
* Un front-end de JavaScript, abra la pestaña de la consola de herramientas de desarrollador de su explorador para obtener más información de error y examinar las solicitudes de HTTP en la red. Sin embargo, los mensajes de error de consola pueden ser confusos. Si recibe un mensaje que indica un error CORS, el problema real puede ser la autenticación. Puede comprobar si este es el caso, ejecute la aplicación con la autenticación deshabilitada temporalmente temporalmente.
* Para una aplicación de la API de .NET, asegúrese de que obtenga toda la información en los mensajes de error como sea posible mediante la configuración de [modo customErrors en desactivado](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remoteview).
* Para una aplicación de la API de .NET iniciar una [sesión de depuración remota](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug)y examinar los valores de las variables que se pasan a código que utiliza ADAL para adquirir un token portador o código que comprueba reclamaciones contra el identificador de servicio previsto principal. Tenga en cuenta que el código puede tomar los valores de configuración de muchos orígenes distintos, por lo que es posible encontrar sorpresas de esta manera. Por ejemplo, si escribe incorrectamente `ida:ClientId` como `ida:ClientID` al configurar opciones del entorno de servicio de aplicación de Azure, el código que se produzca el `ida:ClientId` valor que está buscando en el archivo Web.config, pasando por alto la configuración de servicio de aplicaciones de Azure. 
* Si las cosas no funcionan en una ventana de Internet Explorer normal, en un registro existente puede estar interfiriendo; InPrivate e inténtelo de Chrome o Firefox.


De forma predeterminada, API en una aplicación móvil de back-end pueden invocar de forma anónima. A continuación, debe restringir el acceso a sólo los clientes autenticados.  

+ **Back-end de Node.js (a través del portal)** :  
    
    En **configuración**de la aplicación móvil, haga clic en **Tablas fácil** y seleccione la tabla. Haga clic en **Cambiar permisos**, seleccione **acceso autenticado sólo** para todos los permisos y haga clic en **Guardar**. 

+ **Back-end de .NET (C#)**:  

    En el proyecto de servidor, vaya a los **controladores de** > **TodoItemController.cs**. Agregar la `[Authorize]` atributo a la clase **TodoItemController** , como se explica a continuación. Para restringir el acceso únicamente a los métodos específicos, también puede aplicar este atributo a dichos métodos en lugar de la clase. Volver a publicar el proyecto de servidor.


        [Authorize]
        public class TodoItemController : TableController<TodoItem>

+ **Back-end de Node.js (a través del código de Node.js)** :  
    
    Para requerir autenticación para el acceso a la tabla, agregue la línea siguiente a la secuencia de comandos del servidor Node.js:


        table.access = 'authenticated';

    Para obtener más detalles, consulte [Cómo: requerir autenticación para el acceso a tablas](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-auth). Para obtener información sobre cómo descargar el proyecto de código de tutorial rápido desde su sitio, vea [Cómo: descargar el proyecto de código del tutorial rápido de back-end Node.js con Git](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart).


1. Iniciar sesión en el [Portal de Azure].

2. Haga clic en **+ nuevo** > **Web + Mobile** > **Aplicación móvil**, a continuación, proporcione un nombre para la aplicación móvil de back-end.

3. Para el **Grupo de recursos**, seleccione un grupo de recursos existente o cree uno nuevo (con el mismo nombre que su aplicación.) 
 
    Puede seleccionar otro plan de servicio de aplicación o cree uno nuevo. Para obtener más información acerca de los servicios de aplicación planes y cómo crear un nuevo plan en diferentes precios de nivel y en la ubicación deseada, vea [introducción exhaustiva de planes de servicio de aplicaciones de Azure](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

4. Para el **plan de servicios de aplicación**, se selecciona el plan predeterminado (en el [nivel estándar](https://azure.microsoft.com/pricing/details/app-service/)). También puede seleccionar un plan diferente o [cree uno nuevo](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md#create-an-app-service-plan). Configuración del plan de servicio de aplicación determina la [ubicación, características, costo y recursos de cálculo](https://azure.microsoft.com/pricing/details/app-service/) asociado a la aplicación. 

    Después de decidir el plan, haga clic en **crear**. Esto crea la aplicación móvil de back-end. 
    
6. En el módulo de **configuración** para el servidor de la aplicación móvil nuevo, haga clic en **Inicio rápido** > la plataforma de la aplicación cliente > **Conectar una base de datos**. 

    ![](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-data-connection.png)

7. En el módulo de **Agregar conexión de datos** , haga clic en **Base de datos SQL** > **crear una nueva base de datos**, escriba el **nombre**de la base de datos, elija un nivel de precios y haga clic en el **servidor**.  Puede reutilizar esta base de datos nueva. Si ya tiene una base de datos en la misma ubicación, también puede **usar una base de datos existente**. No se recomienda el uso de una base de datos en una ubicación diferente debido a los costos de ancho de banda y latencia mayor.
 
    ![](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png)

8. En el módulo de **servidor de nuevo** , escriba un nombre de servidor únicos en el campo **nombre de servidor** , proporcione un inicio de sesión y una contraseña, compruebe los **Servicios de azure permitir acceso a servidor**y haga clic en **Aceptar**. Esto crea la nueva base de datos.

9. En el módulo de **Agregar conexión de datos** , haga clic en la **cadena de conexión**, escriba los valores de inicio de sesión y la contraseña para la base de datos y haga clic en **Aceptar**. Espere unos minutos para la base de datos se desarrollan correctamente antes de continuar.

<!-- URLs. -->
[Portal de Azure]: https://portal.azure.com/

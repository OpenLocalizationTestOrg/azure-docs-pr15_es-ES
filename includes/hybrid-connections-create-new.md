
1. En el equipo local, inicie sesión en el [Portal de administración de Azure](http://manager.windowsazure.com) (es decir, el portal antiguo).

2. En la parte inferior del panel de navegación, seleccione **+ nuevo** > **Servicios de aplicación** > **Servicio BizTalk** > **Crear personalizado**.

3. Proporcione un **Nombre de servicio de BizTalk** y seleccione una **Edición**. 

    Este tutorial usa **mobile1**. Debe proporcionar un nombre único para el nuevo BizTalk Service.

4. Una vez que se ha creado BizTalk Service, seleccione la pestaña **Conexiones híbrido** y luego haga clic en **Agregar**.

    ![Agregar conexión híbrido](./media/hybrid-connections-create-new/3.png)

    Esto crea una nueva conexión híbrido.

5. Proporcione un **nombre** y el **Nombre de Host** para la conexión de híbrido y establezca el **puerto** en `1433`. 
  
    ![Configurar conexión híbrido](./media/hybrid-connections-create-new/4.png)

    El nombre de host es el nombre del servidor local. Esto configura la conexión híbrido para tener acceso a SQL Server en el puerto 1433. Si está utilizando una instancia de SQL Server con nombre, use en su lugar el puerto estático definido anteriormente.

6. Después de crear la nueva conexión, el estado de la de la nueva conexión muestra **local de configuración incompleta**.

7. Volver a su servicio móvil, haga clic en **Configurar**, desplácese hasta **conexiones híbrido** y haga clic en **Agregar conexión de híbridos**, a continuación, seleccione la conexión de híbrido que acaba de crear y haga clic en **Aceptar**.

    Esto permite que el servicio móvil de usar su nueva conexión híbrido.

A continuación, deberá instalar al administrador de conexión híbrido en el equipo local.
Mientras se propagarán los registros de su nombre de dominio, debe usar el explorador para comprobar que se puede usar su nombre de dominio personalizado para tener acceso a la aplicación web de la aplicación de servicio de Azure.

> [AZURE.NOTE] Puede tardar algún tiempo para su CNAME en propagarse a través del sistema DNS. Puede usar un servicio como <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> para comprobar que el registro CNAME está disponible.

Si aún no ha agregado la aplicación web como un extremo de administrador de tráfico, debe hacer esto para que funcione la resolución de nombres, como las rutas de nombre de dominio personalizado para el Administrador de tráfico. A continuación, enruta el tráfico administrador a su aplicación web. Use la información en [Agregar o eliminar extremos](../articles/traffic-manager/traffic-manager-endpoints.md) para agregar la aplicación web como un extremo en el perfil de administrador de tráfico.

> [AZURE.NOTE] Si la aplicación web no aparece al agregar un punto final, compruebe que está configurado para el modo de plan de servicio de aplicación **estándar** . Debe usar el modo **estándar** para la aplicación web para poder trabajar con el Administrador de tráfico.

1. En el explorador, abra el [Portal de Azure](https://portal.azure.com).

1. En la pestaña de **Aplicaciones Web** , haga clic en el nombre de la aplicación web, seleccione **configuración**y, a continuación, seleccione **dominios personalizados**

    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)

1. En el módulo de **dominios personalizados** , haga clic en **Agregar nombre de host**.
    
1. Usar los cuadros de texto **nombre de host** para escribir el nombre de dominio de administrador de tráfico para asociarla con esta aplicación web.

    ![](./media/custom-dns-web-site/dncmntask-cname-8.png)

1. Haga clic en **Validar** para guardar la configuración de nombres de dominio.

7.  Al hacer clic en **Validar** Azure se lanzará el flujo de trabajo de comprobación de dominio. Esto buscará propiedad del dominio, así como Hostname disponibilidad e informe detallada error o con orienteción normativa sobre cómo corregir el error.    

8.  Tras **Agregar hostname** una validación correcta botón estará activo y podrá para el nombre de host de asignar. Ahora, vaya a su nombre de dominio personalizado en un explorador. Ahora debe ver la ejecución de la aplicación utilizando su nombre de dominio personalizado. 

    Una vez completada la configuración, se mostrará el nombre de dominio personalizado en la sección de **nombres de dominio** de la aplicación web.

En este momento, debería poder escribir el nombre de dominio de administrador de tráfico en el explorador y ver que se tarda correctamente a su aplicación web.

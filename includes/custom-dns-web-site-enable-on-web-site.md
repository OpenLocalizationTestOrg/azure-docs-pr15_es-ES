Una vez hayan propagado los registros de su nombre de dominio, debe asociarlos con la aplicación Web. Siga los pasos siguientes para habilitar los nombres de dominio mediante el explorador web.

> [AZURE.NOTE] Puede tardar algún tiempo registros TXT creado en los pasos anteriores para propagarse a través del sistema DNS. No puede agregar el nombre de dominio a su aplicación web hasta que haya propagado el registro TXT. Si está utilizando un registro, no puede agregar el nombre de dominio registro A para la aplicación web hasta que haya propagado el registro TXT que creó en el paso anterior.
>
> Puede usar un servicio como <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> para comprobar que el registro TXT está disponible.

1. En el explorador, abra el [Portal de Azure](https://portal.azure.com).

2. En la pestaña de **Aplicaciones Web** , haga clic en el nombre de la aplicación web y, a continuación, seleccione **dominios personalizados**

    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)

3. En el módulo de **dominios personalizados** , haga clic en **Agregar nombre de host**.
    
4. Usar los cuadros de texto **nombre de host** para escribir los nombres de dominio para asociarla con esta aplicación web.

    ![](./media/custom-dns-web-site/add-custom-domain.png)

6.  Haga clic en **Validar**.

7.  Al hacer clic en **Validar** Azure se lanzará el flujo de trabajo de comprobación de dominio. Esto buscará propiedad del dominio, así como Hostname disponibilidad e informe detallada error o con orienteción normativa sobre cómo corregir el error.    

En este momento, debería poder escribir el nombre de dominio personalizado en el explorador y ver que se tarda correctamente a su aplicación web.

Azure Marketplace hace que esté disponible una amplia gama de aplicaciones web populares desarrollado por Microsoft, compañías de terceros e iniciativas de software de código abierto. Aplicaciones Web creadas a partir de Azure Marketplace no requieren la instalación de cualquier software que no sea el explorador usado para conectarse al [Portal de vista previa de Azure](http://go.microsoft.com/fwlink/?LinkId=529715). 

En este tutorial, aprenderá:

- Cómo crear una nueva aplicación web a través de Azure Marketplace.

- Cómo implementar la aplicación web a través del Portal de vista previa de Azure.
 
Podrá crear un blog WordPress que usa una plantilla predeterminada. La siguiente ilustración muestra la aplicación completa:


![Blog de WordPress][13]

>[AZURE.NOTE] Si desea empezar a trabajar con el servicio de aplicación de Azure antes de suscribirse para una cuenta de Azure, vaya a [Probar el servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde puede crear inmediatamente una aplicación web de starter corta duración en la aplicación de servicio. Sin tarjetas de crédito obligatoria; sin compromisos.

## <a name="create-a-web-app-in-the-portal"></a>Crear una aplicación web en el portal

1. Inicie sesión en el Portal de vista previa de Azure.

2. Abrir haciendo clic en el icono de **catálogo de soluciones de** Azure Marketplace:

    ![Icono de Marketplace][marketplace]

    O haciendo clic en el icono de **nuevo** en la parte superior derecha del panel y seleccionando el **catálogo de soluciones** en la bottow de la lista.
    
    ![Crear nuevo][5]
    
3. Seleccione **Web + Mobile**. Buscar **WordPress** y haga clic en el icono de **WordPress** .

    ![WordPress de lista][7]
    
5. Después de leer la descripción de la aplicación de WordPress, seleccione **crear**.

6. Haga clic en la **aplicación Web**y proporcionar los valores necesarios para configurar la aplicación web.
    
    ![configurar la aplicación][8]

7. Haga clic en **base de datos**y proporcionar los valores necesarios para configurar la base de datos MySQL. 

    ![configurar la base de datos][database]

8. Proporcione un nombre para un nuevo grupo de recursos.

    ![Establecer el grupo de recursos][groupname]

8. Si es necesario, haga clic en la **suscripción**y especifique la suscripción para que utilice. 

7. Cuando haya terminado de definir la aplicación web, haga clic en **crear**y espere mientras se crea la nueva aplicación web.

   Cuando se ha creado la aplicación, se muestra el grupo de recursos que contiene la base de datos y web app.

   ![Mostrar grupo][resourcegroup]

## <a name="launch-and-manage-your-wordpress-web-app"></a>Iniciar y administrar la aplicación web de WordPress
    
1. Haga clic en la nueva aplicación web para ver detalles sobre la aplicación.

    ![panel de inicio][10]

2. En la página **Essentials** , haga clic en **Examinar** o el vínculo en la **dirección Url** para abrir la página de bienvenida de la aplicación web.

    ![dirección URL del sitio][browse]

3. Si no ha instalado WordPress, escriba la información de configuración adecuada requerida WordPress y haga clic en **Instalar WordPress** para finalizar la configuración y abrir la página de inicio de sesión de la aplicación web.

4. Haga clic en **Inicio de sesión** e introduzca sus credenciales.  

5. Tendrá una nueva aplicación web de WordPress similar a la aplicación web a continuación.    

    ![el sitio de WordPress][13]






[5]: ./media/website-from-gallery/start-marketplace.png
[6]: ./media/website-from-gallery/wordpressgallery-02.png
[7]: ./media/website-from-gallery/search-web-app.png
[8]: ./media/website-from-gallery/set-web-app.png
[9]: ./media/website-from-gallery/wordpressgallery-05.png
[10]: ./media/website-from-gallery/select-web.png
[13]: ./media/website-from-gallery/wordpressgallery-09.png
[webapps]: ./media/website-from-gallery/selectwebapps.png
[database]: ./media/website-from-gallery/set-db.png
[resourcegroup]: ./media/website-from-gallery/show-rg.png
[browse]: ./media/website-from-gallery/browse-web.png
[marketplace]: ./media/website-from-gallery/marketplace-icon.png
[groupname]: ./media/website-from-gallery/set-rg.png

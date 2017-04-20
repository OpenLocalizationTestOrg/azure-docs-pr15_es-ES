Ahora que ha agregado un desencadenador, su tiempo de hacer algo interesante con los datos que se generan el desencadenador. Siga estos pasos para agregar la acción de **SharePoint Online: crear el archivo** . Esta acción creará un archivo en SharePoint Online cada vez que se activa el desencadenador elemento nuevo. 

Para configurar esta acción, deberá proporcionar la siguiente información. Proporcione esta información, verá que es fácil de usar datos generados por el desencadenador como entrada para algunas de las propiedades para el nuevo archivo:

|Crear propiedades de archivo|Descripción|
|---|---|
|Dirección URL del sitio|Esta es la dirección URL del sitio de SharePoint Online donde desea crear el nuevo archivo. Seleccione el sitio de la lista que se presentan.|
|Ruta de la carpeta|Esta es la carpeta (en la dirección URL del sitio seleccionado en el paso anterior) donde se colocará el nuevo archivo. Busque y seleccione la carpeta.|
|Nombre de archivo|Este es el nombre del archivo que se creó.|
|Contenido del archivo|El contenido que se escribe en el archivo.|

1. Seleccione **+ nuevo paso** para agregar la acción.  
![Imagen de SharePoint online acción 1](./media/connectors-create-api-sharepointonline/action-1.png)  
- Seleccione el vínculo **Agregar una acción** . Se abre el cuadro de búsqueda donde puede buscar cualquier acción le gustaría tener. En este ejemplo, las acciones de SharePoint son de interés.    
![Imagen de SharePoint online acción 2](./media/connectors-create-api-sharepointonline/action-2.png)    
- Escriba *sharepoint* para buscar acciones relacionadas con SharePoint.
- Seleccione **SharePoint Online: crear el archivo** como la acción que se ejecuta.   **Nota**: se le pedirá para autorizar su aplicación lógica para acceder a su cuenta de SharePoint si no ha creado previamente una conexión a SharePoint Online.    
![Imagen de SharePoint online acción 3](./media/connectors-create-api-sharepointonline/action-3.png)    
- Se abre el control **crea un archivo** .   
![Imagen de SharePoint online acción 4](./media/connectors-create-api-sharepointonline/action-4.png)     
- Seleccione la **Dirección URL del sitio** y busque el sitio donde desea crear el archivo.     
![Imagen de SharePoint online acción 5](./media/connectors-create-api-sharepointonline/action-5.png)  
- Seleccione la **ruta de la carpeta** y examinar para buscar la carpeta donde se colocará el nuevo archivo.  
![Imagen de SharePoint online acción 6](./media/connectors-create-api-sharepointonline/action-6.png)  
- Seleccione el control de **nombre de archivo** y escriba el nombre del archivo que desea crear. Aquí, puede escribir el nombre de archivo directamente o puede utilizar cualquiera de las propiedades desde el desencadenador creado previamente. Para ello, seleccione Propiedades de la lista de **resultados de cuando se crea un nuevo elemento**. Esta lista es mostrar solo después de seleccionar el control de **nombre de archivo** . En este tutorial, se seleccionó ID (ID de nuevo elemento de lista) como el nombre del archivo se ha creado por la acción de **SharePoint Online: crear el archivo** .    
![Imagen de SharePoint online acción 7](./media/connectors-create-api-sharepointonline/action-7.png)  
- Seleccione el control de **contenido del archivo** y escriba el contenido que se escriben en el archivo que se creará. Para el contenido del archivo, tenga en cuenta que puede utilizar cualquiera de las propiedades desde el desencadenador creado previamente. Simplemente seleccione las propiedades de la lista que se presentan. Como alternativa, puede escribir el **contenido del archivo** de texto directamente en el control. En este ejemplo, seleccionaron algunas propiedades y se agregan espacios y un guión entre cada propiedad.        
![Imagen de SharePoint online acción 8](./media/connectors-create-api-sharepointonline/action-8.png)  
- Guardar los cambios en el flujo de trabajo  
- Enhorabuena, ahora tiene una aplicación de lógica totalmente funcional que se activa cuando se agrega un nuevo elemento a una lista de SharePoint Online. La aplicación creará un archivo, con algunas de las propiedades de nuevo elemento de lista.  Ahora puede probar mediante la creación de un nuevo elemento en la lista de SharePoint. 

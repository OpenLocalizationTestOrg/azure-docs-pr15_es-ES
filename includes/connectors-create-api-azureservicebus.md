Ahora que ha agregado un desencadenador, su tiempo de hacer algo interesante con los datos que se generan el desencadenador. Siga estos pasos para agregar una acción de **SharePoint Online: crear el archivo** . Esta acción creará un archivo en SharePoint Online cada vez que se activa el desencadenador elemento nuevo. 

Para configurar esta acción, deberá proporcionar la siguiente información. Verá que es fácil de usar datos generados por el desencadenador como entrada para algunas de las propiedades para el nuevo archivo:

|Crear propiedades de archivo|Descripción|
|---|---|
|Dirección URL del sitio|Esta es la dirección URL del sitio de SharePoint Online donde desea crear el nuevo archivo. Seleccione el sitio de la lista que se presentan.|
|Ruta de la carpeta|Esta es la carpeta (en la dirección URL del sitio) donde se colocará el nuevo archivo. Busque y seleccione la carpeta.|
|Nombre de archivo|Este es el nombre del archivo que se creó.|
|Contenido del archivo|El contenido que se escribe en el archivo.|

1. Seleccione **+ nuevo paso** para agregar la acción.  
![](./media/connectors-create-api-sharepointonline/action-1.png)  
- Seleccione el vínculo **Agregar una acción** . Se abre el cuadro de búsqueda donde puede buscar cualquier acción le gustaría tener. En este ejemplo, las acciones de SharePoint son de interés.    
![](./media/connectors-create-api-sharepointonline/action-2.png)    
- Escriba *sharepoint* para buscar acciones relacionadas con SharePoint.
- Seleccione **SharePoint Online: crear el archivo** como la acción que se ejecuta.   **Nota**: se le pedirá para autorizar su aplicación lógica para acceder a su cuenta de SharePoint si no ha hecho previamente.    
![](./media/connectors-create-api-sharepointonline/action-3.png)    
- Se abre el control **crea un archivo** .   
![](./media/connectors-create-api-sharepointonline/action-4.png)     
- Seleccione la **Dirección URL del sitio** y busque el sitio donde desea crear el archivo.     
![](./media/connectors-create-api-sharepointonline/action-5.png)  
- Seleccione la **ruta de la carpeta** y examinar para buscar la carpeta donde se colocará el nuevo archivo.  
![](./media/connectors-create-api-sharepointonline/action-6.png)  
- Seleccione el control de **nombre de archivo** y escriba el nombre del archivo que desea crear. Nombre de archivo, observe que puede utilizar cualquiera de las propiedades desde el desencadenador creado previamente simplemente seleccionándolo en la lista presentada.     
![](./media/connectors-create-api-sharepointonline/action-7.png)  
- Seleccione el control de **contenido del archivo** y escriba el contenido que se escriben en el archivo que se creará. Para el contenido del archivo, tenga en cuenta que puede utilizar cualquiera de las propiedades desde el desencadenador creado previamente. Simplemente seleccione las propiedades de la lista que se presentan. Como alternativa, puede escribir el **contenido del archivo** de texto directamente en el control. En este ejemplo, seleccionaron algunas propiedades y se agregan espacios y un guión entre cada propiedad.        
![](./media/connectors-create-api-sharepointonline/action-8.png)  
- Guardar los cambios en el flujo de trabajo  

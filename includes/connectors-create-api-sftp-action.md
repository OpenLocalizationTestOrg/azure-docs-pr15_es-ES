Ahora que ha agregado un desencadenador, su tiempo de hacer algo interesante con los datos que se generan el desencadenador. Siga estos pasos para agregar un **SFTP - extraer carpeta** a la acción. Esta acción extrae el contenido de un archivo si se cumplen las condiciones definidas. 

Para configurar esta acción, deberá proporcionar la siguiente información. Verá que es fácil de usar datos generados por el desencadenador como entrada para algunas de las propiedades para el nuevo archivo:

|SFTP - extraer propiedades de carpeta|Descripción|
|---|---|
|Ruta de archivo del archivo de origen|Esta es la ruta de acceso del archivo que se extraen. Puede seleccionar uno de los símbolos de una acción anterior o examinar el servidor SFTP para buscar la ruta de acceso del archivo.|
|Ruta de la carpeta de destino|Esta es la ruta de acceso donde se colocarán los archivos extraídos. Puede seleccionar uno de los tokens desde una acción anterior como la ruta de destino o examinar el servidor SFTP y seleccione una ruta de acceso.|
|¿Sobrescribir?|Indica si se encuentra un archivo con el mismo nombre que el archivo extraído en la ruta de la carpeta de destino si se debe sobrescribir el archivo existente o no.|

Vamos a empezar a agregar la acción para extraer los archivos si la condición definida anteriormente se evalúa como *Verdadero*. 

1. Seleccione **Agregar una acción**.        
![Imagen de la condición de la acción SFTP 6](./media/connectors-create-api-sftp/condition-6.png)   
- Seleccione la acción **SFTP - carpeta extracto**      
![Imagen de la condición de la acción SFTP 7](./media/connectors-create-api-sftp/condition-7.png)   
- Seleccione la **ruta de archivo del archivo de origen**              
![Imagen de la condición de la acción SFTP 9](./media/connectors-create-api-sftp/condition-9.png)   
- Seleccione el símbolo de la **ruta de acceso del archivo** . Esto indica que la ruta de acceso del archivo que se encuentra el desencadenador que usa como la ruta de archivo del archivo de origen.           
![Imagen de la condición de la acción SFTP 10](./media/connectors-create-api-sftp/condition-10.png)   
- Seleccione la **ruta de la carpeta de destino**           
![Imagen de la condición de la acción SFTP 11](./media/connectors-create-api-sftp/condition-11.png)   
- Seleccione el símbolo de la **ruta de acceso del archivo** . Esto indica que la ruta de acceso del archivo que se encuentra el desencadenador que usa como la ruta de destino para los archivos extraídos.   
- Escriba *\ExtractedFile* en el control de la **ruta de la carpeta de destino** . Haga esto justo después el token de ruta de acceso del archivo en el control de ruta de acceso de carpeta de destino.         
![Imagen de la condición de la acción SFTP 12](./media/connectors-create-api-sftp/condition-12.png)   
- Escriba *Verdadero* en el **Sobrescribir?* control para indicar que se deben sobrescribir archivos existentes si tienen el mismo nombre que los archivos extraídos.      
![Imagen de la condición de la acción SFTP 13](./media/connectors-create-api-sftp/condition-13.png)   
- Guardar los cambios en el flujo de trabajo  

Vamos a agregar un desencadenador.

1. Escriba *sftp* en el cuadro de búsqueda en el Diseñador de aplicaciones de lógica, a continuación, seleccione el desencadenador **SFTP - cuando se agrega o modifica un archivo**   
![Imagen de activación SFTP 1](./media/connectors-create-api-sftp/trigger-1.png)  
- Abre el control **cuando se agrega o modifica un archivo**  
![Imagen de activación SFTP 2](./media/connectors-create-api-sftp/trigger-2.png)  
- Seleccione **...** ubicado en el lado derecho del control. Se abrirá el control de selector de carpeta  
![Imagen de activación SFTP 3](./media/connectors-create-api-sftp/action-1.png)  
- Seleccione la **SFTP** para seleccionar la carpeta raíz como la carpeta supervisar los archivos nuevos o modificados. Observe que ahora se muestra la carpeta raíz en el control de la **carpeta** .  
![Imagen de activación SFTP 4](./media/connectors-create-api-sftp/action-2.png)   

En este momento, la aplicación de la lógica se ha configurado con un desencadenador que comience una ejecución de los otros desencadenadores y acciones en el flujo de trabajo cuando se modifica o crea en la carpeta específica de SFTP un archivo. 

>[AZURE.NOTE]Para que una aplicación de la lógica que sea completamente funcional, debe contener al menos un desencadenador y una acción. Siga los pasos de la siguiente sección para agregar una acción.  
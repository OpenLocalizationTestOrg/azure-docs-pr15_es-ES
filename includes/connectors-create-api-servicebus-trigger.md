Aquí puede ver cómo utilizar el desencadenador de **Bus de servicio: cuando se recibe un mensaje de una cola** para iniciar un flujo de trabajo de la aplicación lógica cuando se envía un nuevo elemento a una cola de Bus de servicio.  

>[AZURE.NOTE]Se le pedirá que inicie sesión con la cadena de conexión de Bus de servicio si aún no ha creado una conexión de Bus de servicio.  

1. En el cuadro de búsqueda en el Diseñador de aplicaciones de lógica, escriba **bus de servicio**. A continuación, seleccione el desencadenador de **Bus de servicio: cuando se recibe un mensaje de una cola** .  
![Imagen de activación de Bus de servicio 1](./media/connectors-create-api-servicebus/trigger-1.png)   
- Se muestra el cuadro de diálogo **cuando se recibe un mensaje de una cola** .  
![Imagen de activación de Bus de servicio 2](./media/connectors-create-api-servicebus/trigger-2.png)   
- Escriba el nombre de la cola de Bus de servicio que le gustaría supervisar el desencadenador.   
![Imagen de activación de Bus de servicio 3](./media/connectors-create-api-servicebus/trigger-3.png)   

En este momento, la aplicación de la lógica se ha configurado con un desencadenador. Cuando se recibe un nuevo elemento en la cola seleccionada, el desencadenador comenzará una ejecución de los otros desencadenadores y acciones en el flujo de trabajo.    

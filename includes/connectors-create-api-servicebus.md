### <a name="prerequisites"></a>Requisitos previos

Debe tener una cuenta de [Bus de servicio](https://azure.microsoft.com/services/service-bus/) .  

Para poder usar su cuenta de Bus de servicio de Azure en una aplicación de lógica, debe autorizar la aplicación de lógica para conectarse a su cuenta de bus de servicio. Afortunadamente, puede hacerlo fácilmente desde dentro de la aplicación de lógica en el portal de Azure.  

Estos son los pasos para autorizar su aplicación lógica para conectarse a su cuenta de Bus de servicio:  

1. Para crear una conexión de Bus de servicio, en el Diseñador de lógica aplicación, seleccione **Mostrar Microsoft managed API** en la lista desplegable. A continuación, escriba **bus de servicio** en el cuadro de búsqueda. Seleccione el desencadenador o la acción que desea usar.  
    ![Imagen de la conexión de Bus de servicio 1](./media/connectors-create-api-servicebus/servicebus-1.png)  

2. Si todavía no lo ha creado todas las conexiones a Bus de servicio antes, le pedirá que proporcione sus credenciales de Bus de servicio. Estas credenciales se utilizan para autorizar su aplicación lógica para conectarse y obtener acceso a los datos de su cuenta de Bus de servicio. El conector de Bus de servicio necesita la cadena de conexión para el espacio de nombres de Bus de servicio. También se requieren **Administrar** permisos. Una buena forma de saber si la cadena de conexión es para el espacio de nombres o una entidad específica está si contiene el `EntityPath` parámetro. Si es así, no es la cadena de conexión adecuada para una aplicación de lógica.  
    ![Cadena de conexión de Bus de servicio](./media/connectors-create-api-servicebus/connectionstring.png)

1. Después de haber recibido la cadena de conexión para el espacio de nombres, puede usar para la conexión de la API en las aplicaciones de lógica.  
    ![Imagen de la conexión de Bus de servicio 2](./media/connectors-create-api-servicebus/servicebus-2.png)  

3. Observe que la conexión se ha creado y ahora está libre para continuar con el resto de los pasos en su aplicación de lógica.  
    ![Imagen de la conexión de Bus de servicio 3](./media/connectors-create-api-servicebus/servicebus-3.png)   

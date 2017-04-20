### <a name="prerequisites"></a>Requisitos previos
- Una cuenta de [SendGrid](https://www.SendGrid.com/) 

Para poder usar su cuenta de SendGrid en una aplicación de lógica, debe autorizar la aplicación de lógica para conectarse a su cuenta de SendGrid. Afortunadamente, puede hacerlo fácilmente desde dentro de la aplicación de lógica en el Portal de Azure. 

Estos son los pasos para autorizar su aplicación lógica para conectarse a su cuenta de SendGrid:

1. Para crear una conexión a SendGrid, en el Diseñador de lógica aplicación, seleccione **Mostrar Microsoft managed API** en la lista desplegable, a continuación, escriba *SendGrid* en el cuadro de búsqueda. Seleccione la acción que desea utilizar o el desencadenador:  
  ![Paso 1 de SendGrid](./media/connectors-create-api-sendgrid/sendgrid-1.png)
2. Si todavía no lo ha creado todas las conexiones a SendGrid antes, obtener le pedirá que proporcione sus credenciales de SendGrid. Estas credenciales se usará para autorizar su aplicación lógica para conectarse a y tener acceso a datos de la cuenta de SendGrid:  
  ![Paso 2 de SendGrid](./media/connectors-create-api-sendgrid/sendgrid-2.png)
3. Observe la conexión se ha creado y ahora está libre para continuar con el resto de los pasos en su aplicación de lógica:  
  ![Paso 3 de SendGrid](./media/connectors-create-api-sendgrid/sendgrid-3.png)   

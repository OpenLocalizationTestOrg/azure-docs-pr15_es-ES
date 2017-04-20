#### <a name="prerequisites"></a>Requisitos previos
- Una cuenta de Azure puede crear una [cuenta gratuita](https://azure.microsoft.com/free)
- Una cuenta de [Dynamics CRM Online](https://www.microsoft.com/en-us/dynamics/crm-free-trial-overview.aspx) 

Antes de usar su cuenta de Dynamics en una aplicación de lógica, autorizar la aplicación de lógica para conectarse a su cuenta de CRM Online. Puede hacerlo fácilmente dentro de la aplicación de lógica en el portal de Azure. 

Autorizar su aplicación lógica para conectarse a su cuenta de CRM Online con los pasos siguientes:

1. Crear una aplicación de lógica. En el Diseñador de lógica aplicaciones, seleccione **Mostrar Microsoft managed API** en la lista desplegable y, a continuación, escriba "dynamics" en el cuadro de búsqueda. Seleccione una de las acciones o desencadenadores:  
  ![](./media/connectors-create-api-crmonline/dynamics-triggers.png)
2. Si todavía no lo ha creado previamente todas las conexiones con Dynamics, se le pide que inicie sesión con sus credenciales de Dynamics:  
  ![](./media/connectors-create-api-crmonline/dynamics-signin.png)
3. Seleccione **iniciar sesión**y escriba su nombre de usuario y contraseña. Seleccione **iniciar sesión**. 

    Estas credenciales se utilizan para autorizar su aplicación lógica para conectarse y obtener acceso a los datos de su cuenta de Dynamics. 
4. Observe que se ha creado la conexión. Ahora, continúe con el resto de los pasos en su aplicación de lógica:  
  ![](./media/connectors-create-api-crmonline/dynamics-properties.png)

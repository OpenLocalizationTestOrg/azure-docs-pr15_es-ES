#### <a name="prerequisites"></a>Requisitos previos
- Una cuenta de Azure puede crear una [cuenta gratuita](https://azure.microsoft.com/free)
- Una cuenta de [Office 365](https://office365.com)  

Antes de usar su cuenta de Office 365 en una aplicación de lógica, autorizar la aplicación de lógica para conectarse a su cuenta de Office 365. Puede hacerlo fácilmente dentro de la aplicación de lógica en el portal de Azure.  

Autorizar su aplicación lógica para conectarse a su cuenta de Office 365 mediante los siguientes pasos:

1. Crear una aplicación de lógica. En el Diseñador de lógica aplicaciones, seleccione **Mostrar Microsoft managed API** en la lista desplegable y, a continuación, escriba "office 365" en el cuadro de búsqueda. Seleccione una de las acciones o desencadenadores:  
    ![Paso de creación de conexión de Office 365](./media/connectors-create-api-office365-outlook/office365-sendemail.png)  

2. Si todavía no lo ha creado previamente todas las conexiones a Office 365, se le pide que inicie sesión con sus credenciales de Office 365:  
    ![Paso de creación de conexión de Office 365](./media/connectors-create-api-office365-outlook/office365-signin.png)  

3. Seleccione **iniciar sesión**y escriba su nombre de usuario y contraseña. Seleccione **iniciar sesión en**:  
    ![Paso de creación de conexión de Office 365](./media/connectors-create-api-office365-outlook/office365-usernamepassword.png)

    Estas credenciales se utilizan para autorizar su aplicación lógica para conectarse y acceder a su cuenta de Office 365. 

4. Observe que se ha creado la conexión. Ahora, continúe con el resto de los pasos en su aplicación de lógica:   
    ![Paso de creación de conexión de Office 365](./media/connectors-create-api-office365-outlook/office365-sendemailproperties.png)  
  
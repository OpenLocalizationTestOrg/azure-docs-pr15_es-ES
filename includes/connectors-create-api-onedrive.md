#### <a name="prerequisites"></a>Requisitos previos
- Una cuenta de Azure puede crear una [cuenta gratuita](https://azure.microsoft.com/free)
- Una cuenta de [OneDrive](https://www.microsoft.com/store/apps/onedrive/9wzdncrfj1p3) 

Antes de poder usar su cuenta de OneDrive en una aplicación de lógica, autorizar la aplicación de lógica para conectarse a su cuenta de OneDrive.  Puede hacerlo fácilmente dentro de la aplicación de lógica en el portal de Azure. 

Autorizar su aplicación lógica para conectarse a su cuenta de OneDrive con los pasos siguientes:

1. Crear una aplicación de lógica. En el Diseñador de lógica aplicaciones, seleccione **Mostrar Microsoft managed API** en la lista desplegable y, a continuación, escriba "onedrive" en el cuadro de búsqueda. Seleccione una de las acciones o desencadenadores:  
  ![](./media/connectors-create-api-onedrive/onedrive-1.png)
2. Si todavía no lo ha creado previamente las conexiones a OneDrive, se le pide que inicie sesión con sus credenciales de OneDrive:  
  ![](./media/connectors-create-api-onedrive/onedrive-2.png)
3. Seleccione **iniciar sesión**y escriba su nombre de usuario y contraseña. Seleccione **iniciar sesión en**:  
  ![](./media/connectors-create-api-onedrive/onedrive-3.png)   

    Estas credenciales se utilizan para autorizar su aplicación lógica para conectarse y obtener acceso a los datos de su cuenta de OneDrive. 
4. Seleccione **Sí** para autorizar la aplicación de la lógica para usar su cuenta de OneDrive:  
  ![](./media/connectors-create-api-onedrive/onedrive-4.png)   
5. Observe que se ha creado la conexión. Ahora, continúe con el resto de los pasos en su aplicación de lógica:  
  ![](./media/connectors-create-api-onedrive/onedrive-5.png)

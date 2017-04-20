### <a name="prerequisites"></a>Requisitos previos
- Una cuenta de Azure puede crear una [cuenta gratuita](https://azure.microsoft.com/free)
- Una [cuenta de almacenamiento de blobs de Windows Azure](../articles/storage/storage-create-storage-account.md) incluido el nombre de la cuenta de almacenamiento y su clave de acceso. Esta información se muestra en las propiedades de la cuenta de almacenamiento en el portal de Azure. Más información sobre [El almacenamiento de Azure](../articles/storage/storage-introduction.md).

Antes de usar su cuenta de almacenamiento de blobs de Windows Azure en una aplicación de lógica, conectarse a su cuenta de almacenamiento de blobs de Windows Azure. Puede hacerlo fácilmente dentro de la aplicación de lógica en el portal de Azure.  

Conectarse a su cuenta de almacenamiento de blobs de Windows Azure con los pasos siguientes:  

1. Crear una aplicación de lógica. En el Diseñador de aplicaciones de lógica, agregar un desencadenador y, a continuación, agregue una acción. Seleccione **Mostrar Microsoft managed API** en la lista desplegable y, a continuación, escriba "blob" en el cuadro de búsqueda. Seleccione una de las acciones:  

    ![Paso de creación de conexión de almacenamiento de blobs de Windows Azure](./media/connectors-create-api-azureblobstorage/azureblobstorage-1.png)  

2. Si todavía no lo ha creado previamente todas las conexiones con el almacenamiento de Azure, se le pedirá que especifique los detalles de la conexión:   

    ![Paso de creación de conexión de almacenamiento de blobs de Windows Azure](./media/connectors-create-api-azureblobstorage/connection-details.png)  

3. Escriba los detalles de la cuenta de almacenamiento. Propiedades con un asterisco son necesarias.

    | (Propiedad) | Detalles |
|---|---|
| Nombre de la conexión * | Escriba un nombre para la conexión. |
| Nombre de la cuenta de almacenamiento de Azure * | Escriba el nombre de la cuenta de almacenamiento. Se muestra el nombre de la cuenta de almacenamiento en las propiedades de almacenamiento en el portal de Azure. |
| Tecla de acceso de almacenamiento de Azure cuenta * | Escriba la clave de cuenta de almacenamiento. Las teclas de acceso se muestran en las propiedades de almacenamiento en el portal de Azure. |

    Estas credenciales se utilizan para autorizar su aplicación lógica para conectarse y obtener acceso a los datos. 

4. Seleccione **crear**.

5. Observe que se ha creado la conexión. Ahora, continúe con el resto de los pasos en su aplicación de lógica: 

    ![Paso de creación de conexión de almacenamiento de blobs de Windows Azure](./media/connectors-create-api-azureblobstorage/azureblobstorage-3.png)  

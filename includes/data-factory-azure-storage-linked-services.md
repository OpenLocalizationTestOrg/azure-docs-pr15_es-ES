## <a name="azure-storage-linked-service"></a>Almacenamiento de Azure vinculado servicio

El **almacenamiento de Azure vinculado servicio** le permite vincular una cuenta de almacenamiento de Azure a un generador de datos de Azure mediante la **clave de cuenta**. Esto proporciona el generador de datos con acceso global para el almacenamiento de Azure. La tabla siguiente proporciona la descripción de los elementos JSON específicos al servicio de almacenamiento de Azure vinculado.

| (Propiedad) | Descripción | Obligatorio |
| :-------- | :----------- | :-------- |
| tipo | La propiedad tipo debe estar establecida en: **AzureStorage** | Sí |
| cadena de conexión | Especifique la información necesaria para conectarse al almacenamiento de Azure para la propiedad de cadena de conexión. | Sí |

Consulte el siguiente artículo para conocer los pasos para ver o copiar la clave de cuenta para el almacenamiento de Azure: [vista, copiar y las teclas de acceso de almacenamiento regenerar](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).

**Ejemplo:**  
  
    {  
        "name": "StorageLinkedService",  
        "properties": {  
            "type": "AzureStorage",  
            "typeProperties": {  
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"  
            }  
        }  
    }  


## <a name="azure-storage-sas-linked-service"></a>Almacenamiento de Azure SA vinculado servicio  
Una firma de acceso compartido (SA) proporciona acceso delegado a los recursos en su cuenta de almacenamiento. Esto significa que puede conceder a que un cliente de permisos limitados a objetos de su cuenta de almacenamiento durante un período específico de tiempo y con un conjunto determinado de permisos, sin tener que compartir su teclas de acceso de la cuenta. Las asociaciones de seguridad es un URI que abarca en sus parámetros de consulta toda la información necesaria para el acceso a un recurso de almacenamiento autenticado. Para obtener acceso a recursos de almacenamiento con las asociaciones de seguridad, el cliente sólo necesita pasar las asociaciones de seguridad para el método o constructor adecuado. Para obtener información detallada sobre SA, consulte [firmas de acceso compartido: descripción del modelo de SA](../articles/storage/storage-dotnet-shared-access-signature-part-1.md)
  
El servicio de Azure almacenamiento SA vinculado le permite vincular una cuenta de almacenamiento de Azure a un generador de datos de Azure usando una firma de acceso compartido (SA). El generador de datos se proporcionan acceso restringido o de límite de tiempo específicas o todos los recursos (blob/contenedor) en el almacenamiento. La tabla siguiente proporciona la descripción de los elementos JSON específicos al servicio de Azure almacenamiento SA vinculado. 

| (Propiedad) | Descripción | Obligatorio |
| :-------- | :----------- | :-------- |
| tipo | La propiedad tipo debe estar establecida en: **AzureStorageSas**  | Sí |
| sasUri | Especifique compartido URI de firma de acceso a los recursos de almacenamiento de Azure como blobs, contenedor o tabla. Vea las notas siguientes para obtener información detallada. | Sí | 


**Ejemplo:**
  
    {  
        "name": "StorageSasLinkedService",  
        "properties": {  
            "type": "AzureStorageSas",  
            "typeProperties": {  
                "sasUri": "<storageUri>?<sasToken>"   
            }  
        }  
    }  

Al crear un **URI de SA**, teniendo en cuenta lo siguiente:  

- Generador de datos de Azure admite solo **SA de servicio**, no cuenta SA. Para obtener más información acerca de estos dos tipos, vea [Tipos de firmas de acceso compartido](../articles/storage/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures) .
- **Permisos** de lectura y escritura adecuados necesario establecer en los objetos en función de cómo se utilizará el servicio vinculado (lectura, escritura, lectura y escritura) en el generador de datos.
- **Fecha de caducidad** debe establecerse correctamente. Asegúrese de que el acceso a objetos de almacenamiento de Azure no caduca dentro del período activo de la canalización.
- URI debe crearse en el nivel de tabla en función de la necesidad o el contenedor/blob derecha. Uri SA a un blobs de Windows Azure permite el servicio de generador de datos tener acceso a ese blob determinado. Uri SA a un contenedor de blobs de Windows Azure permite el servicio de generador de datos recorrer BLOB en ese contenedor. Si necesita proporcionar acceso más o menos objetos más adelante, o actualizar el URI SAS, no olvide actualizar el servicio vinculado con el nuevo URI.   

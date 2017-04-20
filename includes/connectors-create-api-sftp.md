### <a name="prerequisites"></a>Requisitos previos

- Una cuenta [SFTP](https://en.wikipedia.org/wiki/SSH_File_Transfer_Protocol)  


Para poder usar su cuenta SFTP en una aplicación de lógica, debe autorizar la aplicación de lógica para conectarse a su cuenta SFTP. Afortunadamente, puede hacerlo fácilmente desde dentro de la aplicación de lógica en el Portal de Azure.  

Estos son los pasos para autorizar su aplicación lógica para conectarse a su cuenta SFTP:  
1. Para crear una conexión a SFTP, en el Diseñador de lógica aplicación, seleccione **Mostrar Microsoft managed API** en la lista desplegable, a continuación, escriba *SFTP* en el cuadro de búsqueda. Seleccione el desencadenador **SFTP - cuando se agrega o modifica un archivo** :  
![Imagen de conexión SFTP 1](./media/connectors-create-api-sftp/sftp-1.png)  
2. Si todavía no lo ha creado todas las conexiones a SFTP antes, obtener le pedirá que proporcione sus credenciales SFTP. Estas credenciales se usará para autorizar su aplicación lógica para conectarse a y tener acceso a datos de la cuenta SFTP:  
![Imagen de conexión SFTP 2](./media/connectors-create-api-sftp/sftp-2.png)  
3. Observe la conexión se ha creado y ahora está libre para continuar con el resto de los pasos en su aplicación de lógica:   
 ![Imagen de conexión SFTP 3](./media/connectors-create-api-sftp/sftp-3.png) 

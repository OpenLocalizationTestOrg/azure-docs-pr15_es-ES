### <a name="prerequisites"></a>Requisitos previos

- Una cuenta de [SMTP](https://wikipedia.org/wiki/Simple_Mail_Transfer_Protocol)  


Para poder usar su cuenta de SMTP en una aplicación de lógica, debe autorizar la aplicación de lógica para conectarse a su cuenta de SMTP. Afortunadamente, puede hacerlo fácilmente desde dentro de la aplicación de lógica en el Portal de Azure.  

Estos son los pasos para autorizar su aplicación lógica para conectarse a su cuenta de SMTP:  
1. Para crear una conexión a SMTP, en el Diseñador de lógica aplicación, seleccione **Mostrar Microsoft managed API** en la lista desplegable, a continuación, escriba *SMTP* en el cuadro de búsqueda. Seleccione la acción que desea utilizar o el desencadenador:  
![](./media/connectors-create-api-smtp/smtp-1.png)  
2. Si todavía no lo ha creado todas las conexiones SMTP antes, obtener le pedirá que proporcione sus credenciales de SMTP. Estas credenciales se usará para autorizar su aplicación lógica para conectarse a y tener acceso a datos de la cuenta de SMTP:  
![](./media/connectors-create-api-smtp/smtp-2.png)  
3. Observe la conexión se ha creado y ahora está libre para continuar con el resto de los pasos en su aplicación de lógica:  
 ![](./media/connectors-create-api-smtp/smtp-3.png)  


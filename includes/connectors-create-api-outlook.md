## <a name="connect-to-outlookcom"></a>Conectarse a Outlook.com

### <a name="prerequisites"></a>Requisitos previos
- Una cuenta de Outlook.com

Antes de poder usar su cuenta de Outlook.com en una aplicación de lógica, debe autorizar la aplicación de lógica para conectarse a su cuenta de Outlook.com. Afortunadamente, puede hacerlo fácilmente desde dentro de la aplicación de lógica en el Portal de Azure. 

Estos son los pasos para autorizar su aplicación lógica para conectarse a su cuenta de Outlook.com:

1. Todas las aplicaciones de lógica necesitan para que se inicie un desencadenador para después de crear la aplicación de la lógica, se abre el diseñador y muestra una lista de desencadenadores a los que puede usar para iniciar la aplicación de lógica:

  ![](./media/connectors-create-api-outlook/office365-outlook-0.png)
2. En el cuadro Buscar, escriba "outlook". Observe que la lista se filtra para mostrar todos los desencadenadores con "Outlook" en el nombre:![](./media/connectors-create-api-outlook/office365-outlook-0-5.png)
3. Seleccione **Office 365 Outlook - en nuevo correo electrónico**.   
  Si todavía no lo ha creado todas las conexiones a Outlook antes, obtener le pedirá que proporcione sus credenciales de Outlook.com. Estas credenciales se usará para autorizar para conectarse a la aplicación de lógica y obtener acceso a datos de su cuenta de Outlook.com:![](./media/connectors-create-api-outlook/office365-outlook-1.png)
4. Proporcionar sus credenciales para Outlook e inicie sesión en:![](./media/connectors-create-api-outlook/office365-outlook-2.png)  
  Eso es todo. Ahora que ha creado una conexión a Outlook. Esta conexión estará disponible para su uso en cualquier otra aplicación de lógica que cree.



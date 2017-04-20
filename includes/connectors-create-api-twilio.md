### <a name="prerequisites"></a>Requisitos previos
- Una cuenta de Twilio
- Un número de teléfono Twilio comprobado que pueda recibir SMS
- Un número de teléfono Twilio comprobado que puede enviar SMS

>[AZURE.NOTE] Si está utilizando una cuenta de prueba de Twilio, sólo puede enviar SMS a **Comprobar** números de teléfono.  

Para poder usar su cuenta de Twilio en una aplicación de lógica, debe autorizar la aplicación de lógica para conectarse a su cuenta de Twilio. Afortunadamente, puede hacerlo fácilmente desde dentro de la aplicación de lógica en el Portal de Azure. 

Estos son los pasos para autorizar su aplicación lógica para conectarse a su cuenta de Twilio:

1. Para crear una conexión a Twilio, en el Diseñador de lógica aplicación, seleccione **Mostrar Microsoft managed API** en la lista desplegable, a continuación, escriba *Twilio* en el cuadro de búsqueda. Seleccione la acción que desea utilizar o el desencadenador:  
  ![](./media/connectors-create-api-twilio/twilio-0.png)
2. Si todavía no lo ha creado todas las conexiones a Twilio antes, obtener le pedirá que proporcione sus credenciales de Twilio. Estas credenciales se usará para autorizar su aplicación lógica para conectarse a y tener acceso a datos de la cuenta de Twilio:  
  ![](./media/connectors-create-api-twilio/twilio-1.png)  
3. Necesitará el **id de cuenta de Twilio** y el **token de acceso de Twilio** desde el panel de Twilio, así que inicie sesión en su cuenta de Twilio ahora para captar estos dos elementos de información:  
  ![](./media/connectors-create-api-twilio/twilio-2.png)  
4. Aplicaciones Twilio y lógica use nombres diferentes para identificar estos dos elementos de información. Le mostramos cómo debe asignar al cuadro de diálogo aplicaciones de lógica:![](./media/connectors-create-api-twilio/twilio-3.png)  
5. Seleccione el botón **crear una conexión** :  
  ![](./media/connectors-create-api-twilio/twilio-4.png)
6. Observe la conexión se ha creado y ahora está libre para continuar con el resto de los pasos en su aplicación de lógica:  
  ![](./media/connectors-create-api-twilio/twilio-5.png)
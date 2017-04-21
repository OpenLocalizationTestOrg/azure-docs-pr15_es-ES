<properties
    pageTitle="Agregar Office 365 Outlook connector en las aplicaciones de lógica | Microsoft Azure"
    description="Crear aplicaciones de lógica con el conector de Office 365 para habilitar la interacción con Office 365. Por ejemplo: crear, editar y actualizar elementos de calendario y contactos."
    services=""    
    documentationCenter=""     
    authors="MandiOhlinger"    
    manager="anneta"    
    editor="" 
    tags="connectors" />

<tags
ms.service="logic-apps"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="10/18/2016"
ms.author="mandia"/>

# <a name="get-started-with-the-office-365-outlook-connector"></a>Introducción a Office 365 Outlook connector 

Office 365 Outlook connector habilita la interacción con Outlook en Office 365. Use este conector para crear, editar y actualizar los contactos y elementos de calendario y también obtener, enviar y responder a correo electrónico.

Con Office 365 Outlook,:

- Crear un flujo de trabajo con las características de correo electrónico y calendario en Office 365. 
- Usar desencadenadores para iniciar un flujo de trabajo cuando hay un correo electrónico nuevo, cuando se actualiza un elemento de calendario y mucho más.
- Usar acciones para enviar un correo electrónico, cree un nuevo evento de calendario y mucho más. Por ejemplo, cuando hay un nuevo objeto de Salesforce (un desencadenador), enviar un correo electrónico a Office 365 Outlook (una acción). 

Este tema muestra cómo usar Office 365 Outlook connector en una aplicación de lógica y también enumera los desencadenadores y acciones.

>[AZURE.NOTE] Esta versión de este artículo se aplica a la disponibilidad de aplicaciones de lógica general (GA).

Para obtener más información acerca de la lógica de aplicaciones, vea [¿qué aplicaciones de lógica](../app-service-logic/app-service-logic-what-are-logic-apps.md) y [crear una aplicación de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="connect-to-office-365"></a>Conectarse a Office 365

Antes de la aplicación de lógica puede tener acceso a cualquier servicio, primero crear una *conexión* al servicio. Una conexión proporciona conectividad entre una aplicación de lógica y otro servicio. Por ejemplo, para conectarse a Office 365 Outlook, primero tendrá una *conexión*de Office 365. Para crear una conexión, escriba las credenciales que normalmente se utilizan para tener acceso al servicio que desea conectarse. Con Office 365 Outlook, indique las credenciales de su cuenta de Office 365 para crear la conexión.


## <a name="create-the-connection"></a>Crear la conexión

>[AZURE.INCLUDE [Steps to create a connection to Office 365](../../includes/connectors-create-api-office365-outlook.md)]

## <a name="use-a-trigger"></a>Use un desencadenador

Un desencadenador es un evento que se pueden usar para iniciar el flujo de trabajo que se definen en una aplicación de lógica. Desencadenadores "sondean" el servicio en un intervalo y la frecuencia con la que desee. [Obtenga más información acerca de desencadenadores](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

1. En la aplicación lógica, escriba "office 365" para obtener una lista de los desencadenadores:  

    ![](./media/connectors-create-api-office365-outlook/office365-trigger.png)

2. Seleccione **Office 365 Outlook - cuando un evento próximo es comienzan pronto**. Si ya existe una conexión, a continuación, seleccione un calendario en la lista desplegable.

    ![](./media/connectors-create-api-office365-outlook/sample-calendar.png)

    Si se le pide que inicie sesión en, a continuación, escriba el signo de detalles para crear la conexión. [Crear la conexión](connectors-create-api-office365-outlook.md#create-the-connection) en este tema se enumeran los pasos. 

    > [AZURE.NOTE] En este ejemplo, la aplicación de la lógica se ejecuta cuando se actualice un evento de calendario. Para ver los resultados de este desencadenador, agregar otra acción que envía un mensaje de texto. Por ejemplo, agregue la acción Twilio *Enviar mensaje de* ese texto cuando se inicia el evento de calendario en 15 minutos. 

3. Seleccione el botón **Editar** y establecer los valores de **frecuencia** y el **intervalo** . Por ejemplo, si desea que el desencadenador para sondear cada 15 minutos, a continuación, establezca la **frecuencia** en **minuto**y establecer el **intervalo de** **15**. 

    ![](./media/connectors-create-api-office365-outlook/calendar-settings.png)

4. **Guardar** los cambios (esquina superior izquierda de la barra de herramientas). La aplicación de la lógica se guarda y se puede activar automáticamente.


## <a name="use-an-action"></a>Usar una acción

Una acción es una operación que lleva a cabo por el flujo de trabajo que se definen en una aplicación de lógica. [Más información acerca de las acciones](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

1. Seleccione el signo más. Ver varias opciones: **Agregar una acción**, **Agregar una condición**o una de las opciones **más** .

    ![](./media/connectors-create-api-office365-outlook/add-action.png)

2. Elija **Agregar una acción**.

3. En el cuadro de texto, escriba "office 365" para obtener una lista de todas las acciones disponibles.

    ![](./media/connectors-create-api-office365-outlook/office365-actions.png) 

4. En nuestro ejemplo, elija **Office 365 Outlook - crear contacto**. Si ya existe una conexión, elija la **Carpeta Id.**, **nombre**y otras propiedades:  

    ![](./media/connectors-create-api-office365-outlook/office365-sampleaction.png)

    Si se le pide la información de conexión, a continuación, escriba los detalles para crear la conexión. [Crear la conexión](connectors-create-api-office365-outlook.md#create-the-connection) en este tema se describen las propiedades. 

    > [AZURE.NOTE] En este ejemplo, creamos un nuevo contacto en Outlook de Office 365. Puede usar la salida de otro desencadenador para crear el contacto. Por ejemplo, agregar el desencadenador de SalesForce *cuando se crea un objeto* . A continuación, agregue la acción *Crear contacto* de Outlook de Office 365 que usa los campos de SalesForce para crear el nuevo contacto en Office 365. 

5. **Guardar** los cambios (esquina superior izquierda de la barra de herramientas). La aplicación de la lógica se guarda y se puede activar automáticamente.


## <a name="technical-details"></a>Detalles técnicos

Estos son los detalles acerca de los desencadenadores, acciones y las respuestas que admite esta conexión:

## <a name="office-365-triggers"></a>Desencadenadores de Office 365

|Desencadenador | Descripción|
|--- | ---|
|[Cuando un evento próximo es comienzan pronto](connectors-create-api-office365-outlook.md#when-an-upcoming-event-is-starting-soon)|Esta operación activa un flujo cuando se inicia un evento de calendario de próximos eventos.|
|[Cuando llega un nuevo correo electrónico](connectors-create-api-office365-outlook.md#when-a-new-email-arrives)|Esta operación provoca un flujo cuando reciba un nuevo correo electrónico|
|[Cuando se crea un nuevo evento](connectors-create-api-office365-outlook.md#when-a-new-event-is-created)|Esta operación activa un flujo cuando se crea un nuevo evento en un calendario.|
|[Cuando se modifica un evento](connectors-create-api-office365-outlook.md#when-an-event-is-modified)|Esta operación activa un flujo cuando se modifica un evento en un calendario.|


## <a name="office-365-actions"></a>Acciones de Office 365

|Acción|Descripción|
|--- | ---|
|[Obtener mensajes de correo electrónico](connectors-create-api-office365-outlook.md#get-emails)|Esta operación obtiene los mensajes de correo electrónico de una carpeta.|
|[Enviar un correo electrónico](connectors-create-api-office365-outlook.md#send-an-email)|Esta operación envía un mensaje de correo electrónico.|
|[Eliminar correo electrónico](connectors-create-api-office365-outlook.md#delete-email)|Esta operación elimina un correo electrónico por id.|
|[Marcar como leído](connectors-create-api-office365-outlook.md#mark-as-read)|Esta operación marca un correo electrónico que ha leído.|
|[Responder a correo electrónico](connectors-create-api-office365-outlook.md#reply-to-email)|Esta operación se responde a un correo electrónico.|
|[Obtener datos adjuntos](connectors-create-api-office365-outlook.md#get-attachment)|Esta operación Obtiene un archivo adjunto de correo electrónico por id.|
|[Enviar correo electrónico con opciones](connectors-create-api-office365-outlook.md#send-email-with-options)|Esta operación envía un correo electrónico con varias opciones y espera al destinatario que responda con una de las opciones.|
|[Enviar correo electrónico de aprobación](connectors-create-api-office365-outlook.md#send-approval-email)|Esta operación envía un correo electrónico de aprobación y espera una respuesta del destinatario.|
|[Obtener los calendarios](connectors-create-api-office365-outlook.md#get-calendars)|Esta operación enumera los calendarios disponibles.|
|[Obtenga los eventos](connectors-create-api-office365-outlook.md#get-events)|Esta operación obtiene los eventos de calendario.|
|[Crear evento](connectors-create-api-office365-outlook.md#create-event)|Esta operación crea un nuevo evento en un calendario.|
|[Obtener evento](connectors-create-api-office365-outlook.md#get-event)|Esta operación Obtiene un evento específico de un calendario.|
|[Eliminar evento](connectors-create-api-office365-outlook.md#delete-event)|Esta operación elimina un evento en un calendario.|
|[Evento de actualización](connectors-create-api-office365-outlook.md#update-event)|Esta operación actualiza un evento en un calendario.|
|[Obtener las carpetas de contactos](connectors-create-api-office365-outlook.md#get-contact-folders)|Esta operación enumera las carpetas de contactos disponibles.|
|[Obtener contactos](connectors-create-api-office365-outlook.md#get-contacts)|Esta operación obtiene los contactos de una carpeta de contactos.|
|[Crear contacto](connectors-create-api-office365-outlook.md#create-contact)|Esta operación crea un nuevo contacto en una carpeta de contactos.|
|[Obtener el contacto](connectors-create-api-office365-outlook.md#get-contact)|Esta operación Obtiene un contacto específico de una carpeta de contactos.|
|[Eliminar contacto](connectors-create-api-office365-outlook.md#delete-contact)|Esta operación elimina un contacto de una carpeta de contactos.|
|[Actualizar contacto](connectors-create-api-office365-outlook.md#update-contact)|Esta operación actualiza un contacto en una carpeta de contactos.|

### <a name="trigger-and-action-details"></a>Detalles de desencadenador y acción

En esta sección, vea los detalles específicos acerca de cada desencadenador y la acción, incluidas las propiedades de entrada opcionales o requeridas y ningún resultado correspondiente asociado con el conector.

#### <a name="when-an-upcoming-event-is-starting-soon"></a>Cuando un evento próximo es comienzan pronto
Esta operación activa un flujo cuando se inicia un evento de calendario de próximos eventos. 

|Nombre de propiedad| Nombre para mostrar|Descripción|
| ---|---|---|
|tabla *|Id. de calendario|Identificador único del calendario|
|lookAheadTimeInMinutes|Tiempo de aspecto anticipada|Tiempo (en minutos) para buscar anticipada próximos eventos|

Un asterisco (*) significa que la propiedad es necesaria.

##### <a name="output-details"></a>Detalles de salida
CalendarItemsList: La lista de elementos de calendario

| Nombre de propiedad | Tipo de datos | Descripción |
|---|---|---|
|valor|matriz|Lista de elementos de calendario|


#### <a name="get-emails"></a>Obtener mensajes de correo electrónico
Esta operación obtiene los mensajes de correo electrónico de una carpeta. 

|Nombre de propiedad| Nombre para mostrar|Descripción|
| ---|---|---|
|folderPath|Ruta de la carpeta|Ruta de acceso de la carpeta para recuperar mensajes de correo electrónico (predeterminado: 'Buzón')|
|Arriba|Arriba|Número de mensajes de correo electrónico para recuperar (predeterminado: 10)|
|fetchOnlyUnread|Capturar sólo los mensajes no leídos|¿Recuperar solo los correos electrónicos no leídos?|
|includeAttachments|Incluir datos adjuntos|Si el conjunto de datos adjuntos es true, también se va a recuperar junto con el correo electrónico|
|searchQuery|Consulta de búsqueda|Consulta de búsqueda para filtrar los mensajes de correo electrónico|
|saltar|Saltar|Número de mensajes de correo electrónico para omitir (valor predeterminado: 0)|
|skipToken|Saltar Token|Omitir token a capturar nueva página|

Un asterisco (*) significa que la propiedad es necesaria.

##### <a name="output-details"></a>Detalles de salida
ReceiveMessage: Recibe el mensaje de correo electrónico

| Nombre de propiedad | Tipo de datos | Descripción |
|---|---|---|
|De|cadena|De|
|Para|cadena|Para|
|Asunto|cadena|Asunto|
|Cuerpo|cadena|Cuerpo|
|Importancia|cadena|Importancia|
|HasAttachment|valor booleano|Tiene datos adjuntos|
|Id.|cadena|Id. de mensaje|
|Estáleído|valor booleano|Se lee|
|DateTimeReceived|cadena|Fecha hora recibido|
|Datos adjuntos|matriz|Datos adjuntos|
|CC|cadena|Especificar las direcciones de correo electrónico separadas por punto y coma comosomeone@contoso.com|
|CCO|cadena|Especificar las direcciones de correo electrónico separadas por punto y coma comosomeone@contoso.com|
|IsHtml|valor booleano|Es Html|


#### <a name="send-an-email"></a>Enviar un correo electrónico
Esta operación envía un mensaje de correo electrónico. 

|Nombre de propiedad| Nombre para mostrar|Descripción|
| ---|---|---|
|emailMessage *|Correo electrónico|Correo electrónico|

Un asterisco (*) significa que la propiedad es necesaria.

##### <a name="output-details"></a>Detalles de salida
Ninguno.

#### <a name="delete-email"></a>Eliminar correo electrónico
Esta operación elimina un correo electrónico por id. 

|Nombre de propiedad| Nombre para mostrar|Descripción|
| ---|---|---|
|Id. de mensaje *|Id. de mensaje|Id. del correo electrónico para eliminar|

Un asterisco (*) significa que la propiedad es necesaria.

##### <a name="output-details"></a>Detalles de salida
Ninguno.

#### <a name="mark-as-read"></a>Marcar como leído
Esta operación marca un correo electrónico que ha leído. 

|Nombre de propiedad| Nombre para mostrar|Descripción|
| ---|---|---|
|Id. de mensaje *|Id. de mensaje|Identificador del correo electrónico para marcar como leído|

Un asterisco (*) significa que la propiedad es necesaria.

##### <a name="output-details"></a>Detalles de salida
Ninguno.


#### <a name="reply-to-email"></a>Responder a correo electrónico
Esta operación se responde a un correo electrónico. 

|Nombre de propiedad| Nombre para mostrar|Descripción|
| ---|---|---|
|Id. de mensaje *|Id. de mensaje|Id. del correo electrónico para responder a|
|comentario *|Comentario|Comentario de respuesta|
|responder a todos|Responder a todos|Responder a todos los destinatarios|

Un asterisco (*) significa que la propiedad es necesaria.

##### <a name="output-details"></a>Detalles de salida
Ninguno.


#### <a name="get-attachment"></a>Obtener datos adjuntos
Esta operación Obtiene un archivo adjunto de correo electrónico por id. 

|Nombre de propiedad| Nombre para mostrar|Descripción|
| ---|---|---|
|Id. de mensaje *|Id. de mensaje|Id. del correo electrónico|
|attachmentId *|Id. de datos adjuntos|Id. del archivo adjunto para descargar|

Un asterisco (*) significa que la propiedad es necesaria.

##### <a name="output-details"></a>Detalles de salida
Ninguno.


#### <a name="when-a-new-email-arrives"></a>Cuando llega un nuevo correo electrónico
Esta operación activa un flujo cuando reciba un nuevo correo electrónico.

|Nombre de propiedad| Nombre para mostrar|Descripción|
| ---|---|---|
|folderPath|Ruta de la carpeta|Carpeta de correo electrónico para recuperar (predeterminado: Bandeja de entrada)|
|Para|Para|Direcciones de correo electrónico del destinatario|
|De|De|De dirección|
|importancia|Importancia|Importancia del correo electrónico (alta, Normal, baja) (valor predeterminado: Normal)|
|fetchOnlyWithAttachment|Tiene datos adjuntos|Recuperar solo los correos electrónicos con datos adjuntos|
|includeAttachments|Incluir datos adjuntos|Incluir datos adjuntos|
|subjectFilter|Filtro de asunto|Cadena para buscar en el asunto|

Un asterisco (*) significa que la propiedad es necesaria.

##### <a name="output-details"></a>Detalles de salida
TriggerBatchResponse [ReceiveMessage]

| Nombre de propiedad | Tipo de datos |
|---|---|
|valor|matriz|


#### <a name="send-email-with-options"></a>Enviar correo electrónico con opciones
Esta operación envía un correo electrónico con varias opciones y espera al destinatario que responda con una de las opciones. 

|Nombre de propiedad| Nombre para mostrar|Descripción|
| ---|---|---|
|optionsEmailSubscription *|Solicitud de suscripción para el correo electrónico de opciones|Solicitud de suscripción para el correo electrónico de opciones|

Un asterisco (*) significa que la propiedad es necesaria.

##### <a name="output-details"></a>Detalles de salida
SubscriptionResponse: Modelo de suscripción de correo electrónico de aprobación

| Nombre de propiedad | Tipo de datos | Descripción |
|---|---|---|
|Id.|cadena|Identificador de la suscripción|
|recursos|cadena|Recursos de la solicitud de suscripción|
|notificationType|cadena|Tipo de notificación|
|notificationUrl|cadena|Dirección Url de notificación|


#### <a name="send-approval-email"></a>Enviar correo electrónico de aprobación
Esta operación envía un correo electrónico de aprobación y espera una respuesta del destinatario. 

|Nombre de propiedad| Nombre para mostrar|Descripción|
| ---|---|---|
|approvalEmailSubscription *|Solicitud de suscripción para el correo electrónico de aprobación|Solicitud de suscripción para el correo electrónico de aprobación|

Un asterisco (*) significa que la propiedad es necesaria.

##### <a name="output-details"></a>Detalles de salida
SubscriptionResponse: Modelo de suscripción de correo electrónico de aprobación

| Nombre de propiedad | Tipo de datos | Descripción |
|---|---|---|
|Id.|cadena|Identificador de la suscripción|
|recursos|cadena|Recursos de la solicitud de suscripción|
|notificationType|cadena|Tipo de notificación|
|notificationUrl|cadena|Dirección Url de notificación|


#### <a name="get-calendars"></a>Obtener los calendarios
Esta operación enumera los calendarios disponibles. 

No hay parámetros para esta llamada.

##### <a name="output-details"></a>Detalles de salida
TablesList

| Nombre de propiedad | Tipo de datos |
|---|---|
|valor|matriz|


#### <a name="get-events"></a>Obtenga los eventos
Esta operación obtiene los eventos de calendario. 

|Nombre de propiedad| Nombre para mostrar|Descripción|
| ---|---|---|
|tabla *|Id. de calendario|Seleccione un calendario|
|$filter|Consulta de filtro|Una consulta de filtro ODATA para restringir las entradas devueltas|
|$orderby|Ordenar por|Una consulta ODATA orderBy para especificar el orden de las entradas.|
|$skip|Número de saltos|Número de entradas para omitir (predeterminado = 0)|
|$top|Recuento de obtener máximo|Número máximo de entradas para recuperar (predeterminado = 256)|

Un asterisco (*) significa que la propiedad es necesaria.

##### <a name="output-details"></a>Detalles de salida
CalendarEventList: La lista de elementos de calendario

| Nombre de propiedad | Tipo de datos | Descripción |
|---|---|---|
|valor|matriz|Lista de elementos de calendario|


#### <a name="create-event"></a>Crear evento
Esta operación crea un nuevo evento en un calendario. 

|Nombre de propiedad| Nombre para mostrar|Descripción|
| ---|---|---|
|tabla *|Id. de calendario|Seleccione un calendario|
|elemento *|Elemento|Evento para crear|

Un asterisco (*) significa que la propiedad es necesaria.

##### <a name="output-details"></a>Detalles de salida
CalendarEvent: Clase de modelo de eventos de calendario específico conector.

| Nombre de propiedad | Tipo de datos | Descripción |
|---|---|---|
|Id.|cadena|Identificador único del evento.|
|Asistentes|matriz|Lista de asistentes para el evento.|
|Cuerpo|no definido|Cuerpo del mensaje asociado con el evento.|
|BodyPreview|cadena|La vista previa del mensaje asociado con el evento.|
|Categorías|matriz|Las categorías asociadas con el evento.|
|ChangeKey|cadena|Identifica la versión del objeto de evento. Cada vez que se cambia el evento, ChangeKey cambia también.|
|DateTimeCreated|cadena|La fecha y hora en que se creó el evento.|
|DateTimeLastModified|cadena|La fecha y hora en que se modificó por última vez el evento.|
|Final|cadena|Hora de finalización del evento.|
|EndTimeZone|cadena|Especifica la zona horaria de la reunión hora de finalización. Este valor debe ser según se define en Windows (ejemplo: 'Hora estándar del Pacífico').|
|Tieneadjuntos|valor booleano|Se establece en true si el evento tiene datos adjuntos.|
|Importancia|cadena|La importancia del evento: baja, Normal o alta.|
|IsAllDay|valor booleano|Se establece en true si el evento dura todo el día.|
|IsCancelled|valor booleano|Se establece en true si se ha cancelado el evento.|
|IsOrganizer|valor booleano|Se establece en true si el remitente del mensaje también es el organizador.|
|Ubicación|no definido|La ubicación del evento.|
|Organizador|no definido|El organizador del evento.|
|Periodicidad|no definido|El patrón de periodicidad para el evento.|
|Aviso|entero|Tiempo en minutos antes del inicio de evento para recordar.|
|ResponseRequested|valor booleano|Se establece en true si el remitente desea una respuesta cuando el evento es aceptado o rechazado.|
|ResponseStatus|no definido|Indica el tipo de respuesta enviado en respuesta a un mensaje de evento.|
|SeriesMasterId|cadena|Identificador único para el tipo de evento de patrón de serie.|
|ShowAs|cadena|Muestra de disponibilidad.|
|Inicio|cadena|Hora de inicio del evento.|
|StartTimeZone|cadena|Especifica la hora a la hora de inicio de la zona de la reunión. Este valor debe ser según se define en Windows (ejemplo: "Hora estándar del Pacífico").|
|Asunto|cadena|Asunto del evento.|
|Tipo|cadena|El tipo de evento: instancia única, repetición, excepción o patrón de serie.|
|Vínculo Web|cadena|La vista previa del mensaje asociado con el evento.|


#### <a name="get-event"></a>Obtener evento
Esta operación Obtiene un evento específico de un calendario. 

|Nombre de propiedad| Nombre para mostrar|Descripción|
| ---|---|---|
|tabla *|Id. de calendario|Seleccione un calendario|
|Id *|Id. de elemento|Seleccione un evento|

Un asterisco (*) significa que la propiedad es necesaria.

##### <a name="output-details"></a>Detalles de salida
CalendarEvent: Clase de modelo de eventos de calendario específico conector.

| Nombre de propiedad | Tipo de datos | Descripción |
|---|---|---|
|Id.|cadena|Identificador único del evento.|
|Asistentes|matriz|Lista de asistentes para el evento.|
|Cuerpo|no definido|Cuerpo del mensaje asociado con el evento.|
|BodyPreview|cadena|La vista previa del mensaje asociado con el evento.|
|Categorías|matriz|Las categorías asociadas con el evento.|
|ChangeKey|cadena|Identifica la versión del objeto de evento. Cada vez que se cambia el evento, ChangeKey cambia también.|
|DateTimeCreated|cadena|La fecha y hora en que se creó el evento.|
|DateTimeLastModified|cadena|La fecha y hora en que se modificó por última vez el evento.|
|Final|cadena|Hora de finalización del evento.|
|EndTimeZone|cadena|Especifica la zona horaria de la reunión hora de finalización. Este valor debe ser según se define en Windows (ejemplo: 'Hora estándar del Pacífico').|
|Tieneadjuntos|valor booleano|Se establece en true si el evento tiene datos adjuntos.|
|Importancia|cadena|La importancia del evento: baja, Normal o alta.|
|IsAllDay|valor booleano|Se establece en true si el evento dura todo el día.|
|IsCancelled|valor booleano|Se establece en true si se ha cancelado el evento.|
|IsOrganizer|valor booleano|Se establece en true si el remitente del mensaje también es el organizador.|
|Ubicación|no definido|La ubicación del evento.|
|Organizador|no definido|El organizador del evento.|
|Periodicidad|no definido|El patrón de periodicidad para el evento.|
|Aviso|entero|Tiempo en minutos antes del inicio de evento para recordar.|
|ResponseRequested|valor booleano|Se establece en true si el remitente desea una respuesta cuando el evento es aceptado o rechazado.|
|ResponseStatus|no definido|Indica el tipo de respuesta enviado en respuesta a un mensaje de evento.|
|SeriesMasterId|cadena|Identificador único para el tipo de evento de patrón de serie.|
|ShowAs|cadena|Muestra de disponibilidad.|
|Inicio|cadena|Hora de inicio del evento.|
|StartTimeZone|cadena|Especifica la hora a la hora de inicio de la zona de la reunión. Este valor debe ser según se define en Windows (ejemplo: "Hora estándar del Pacífico").|
|Asunto|cadena|Asunto del evento.|
|Tipo|cadena|El tipo de evento: instancia única, repetición, excepción o patrón de serie.|
|Vínculo Web|cadena|La vista previa del mensaje asociado con el evento.|


#### <a name="delete-event"></a>Eliminar evento
Esta operación elimina un evento en un calendario. 

|Nombre de propiedad| Nombre para mostrar|Descripción|
| ---|---|---|
|tabla *|Id. de calendario|Seleccione un calendario|
|Id *|Id.|Seleccione un evento|

Un asterisco (*) significa que la propiedad es necesaria.

##### <a name="output-details"></a>Detalles de salida
Ninguno.


#### <a name="update-event"></a>Evento de actualización
Esta operación actualiza un evento en un calendario. 

|Nombre de propiedad| Nombre para mostrar|Descripción|
| ---|---|---|
|tabla *|Id. de calendario|Seleccione un calendario|
|Id *|Id.|Seleccione un evento|
|elemento *|Elemento|Evento para actualizar|

Un asterisco (*) significa que la propiedad es necesaria.

##### <a name="output-details"></a>Detalles de salida
CalendarEvent: Clase de modelo de eventos de calendario específico conector.

| Nombre de propiedad | Tipo de datos | Descripción |
|---|---|---|
|Id.|cadena|Identificador único del evento.|
|Asistentes|matriz|Lista de asistentes para el evento.|
|Cuerpo|no definido|Cuerpo del mensaje asociado con el evento.|
|BodyPreview|cadena|La vista previa del mensaje asociado con el evento.|
|Categorías|matriz|Las categorías asociadas con el evento.|
|ChangeKey|cadena|Identifica la versión del objeto de evento. Cada vez que se cambia el evento, ChangeKey cambia también.|
|DateTimeCreated|cadena|La fecha y hora en que se creó el evento.|
|DateTimeLastModified|cadena|La fecha y hora en que se modificó por última vez el evento.|
|Final|cadena|Hora de finalización del evento.|
|EndTimeZone|cadena|Especifica la zona horaria de la reunión hora de finalización. Este valor debe ser según se define en Windows (ejemplo: 'Hora estándar del Pacífico').|
|Tieneadjuntos|valor booleano|Se establece en true si el evento tiene datos adjuntos.|
|Importancia|cadena|La importancia del evento: baja, Normal o alta.|
|IsAllDay|valor booleano|Se establece en true si el evento dura todo el día.|
|IsCancelled|valor booleano|Se establece en true si se ha cancelado el evento.|
|IsOrganizer|valor booleano|Se establece en true si el remitente del mensaje también es el organizador.|
|Ubicación|no definido|La ubicación del evento.|
|Organizador|no definido|El organizador del evento.|
|Periodicidad|no definido|El patrón de periodicidad para el evento.|
|Aviso|entero|Tiempo en minutos antes del inicio de evento para recordar.|
|ResponseRequested|valor booleano|Se establece en true si el remitente desea una respuesta cuando el evento es aceptado o rechazado.|
|ResponseStatus|no definido|Indica el tipo de respuesta enviado en respuesta a un mensaje de evento.|
|SeriesMasterId|cadena|Identificador único para el tipo de evento de patrón de serie.|
|ShowAs|cadena|Muestra de disponibilidad.|
|Inicio|cadena|Hora de inicio del evento.|
|StartTimeZone|cadena|Especifica la hora a la hora de inicio de la zona de la reunión. Este valor debe ser según se define en Windows (ejemplo: "Hora estándar del Pacífico").|
|Asunto|cadena|Asunto del evento.|
|Tipo|cadena|El tipo de evento: instancia única, repetición, excepción o patrón de serie.|
|Vínculo Web|cadena|La vista previa del mensaje asociado con el evento.|


#### <a name="when-a-new-event-is-created"></a>Cuando se crea un nuevo evento
Esta operación activa un flujo cuando se crea un nuevo evento en un calendario. 

|Nombre de propiedad| Nombre para mostrar|Descripción|
| ---|---|---|
|tabla *|Id. de calendario|Seleccione un calendario|
|$filter|Consulta de filtro|Una consulta de filtro ODATA para restringir las entradas devueltas|
|$orderby|Ordenar por|Una consulta ODATA orderBy para especificar el orden de las entradas.|
|$skip|Número de saltos|Número de entradas para omitir (predeterminado = 0)|
|$top|Recuento de obtener máximo|Número máximo de entradas para recuperar (predeterminado = 256)|

Un asterisco (*) significa que la propiedad es necesaria.

##### <a name="output-details"></a>Detalles de salida
CalendarItemsList: La lista de elementos de calendario

| Nombre de propiedad | Tipo de datos | Descripción |
|---|---|---|
|valor|matriz|Lista de elementos de calendario|


#### <a name="when-an-event-is-modified"></a>Cuando se modifica un evento
Esta operación activa un flujo cuando se modifica un evento en un calendario. 

|Nombre de propiedad| Nombre para mostrar|Descripción|
| ---|---|---|
|tabla *|Id. de calendario|Seleccione un calendario|
|$filter|Consulta de filtro|Una consulta de filtro ODATA para restringir las entradas devueltas|
|$orderby|Ordenar por|Una consulta ODATA orderBy para especificar el orden de las entradas.|
|$skip|Número de saltos|Número de entradas para omitir (predeterminado = 0)|
|$top|Recuento de obtener máximo|Número máximo de entradas para recuperar (predeterminado = 256)|

Un asterisco (*) significa que la propiedad es necesaria.

##### <a name="output-details"></a>Detalles de salida
CalendarItemsList: La lista de elementos de calendario


| Nombre de propiedad | Tipo de datos | Descripción |
|---|---|---|
|valor|matriz|Lista de elementos de calendario|


#### <a name="get-contact-folders"></a>Obtener las carpetas de contactos
Esta operación enumera las carpetas de contactos disponibles. 

No hay parámetros para esta llamada.

##### <a name="output-details"></a>Detalles de salida
TablesList

| Nombre de propiedad | Tipo de datos |
|---|---|
|valor|matriz|


#### <a name="get-contacts"></a>Obtener contactos
Esta operación obtiene los contactos de una carpeta de contactos. 

|Nombre de propiedad| Nombre para mostrar|Descripción|
| ---|---|---|
|tabla *|Id. de carpeta|Identificador único de la carpeta de contactos para recuperar|
|$filter|Consulta de filtro|Una consulta de filtro ODATA para restringir las entradas devueltas|
|$orderby|Ordenar por|Una consulta ODATA orderBy para especificar el orden de las entradas.|
|$skip|Número de saltos|Número de entradas para omitir (predeterminado = 0)|
|$top|Recuento de obtener máximo|Número máximo de entradas para recuperar (predeterminado = 256)|

Un asterisco (*) significa que la propiedad es necesaria.

##### <a name="output-details"></a>Detalles de salida
ContactList: La lista de contactos

| Nombre de propiedad | Tipo de datos | Descripción |
|---|---|---|
|valor|matriz|Lista de contactos|


#### <a name="create-contact"></a>Crear contacto
Esta operación crea un nuevo contacto en una carpeta de contactos. 

|Nombre de propiedad| Nombre para mostrar|Descripción|
| ---|---|---|
|tabla *|Id. de carpeta|Seleccione una carpeta de contactos|
|elemento *|Elemento|Contacto para crear|

Un asterisco (*) significa que la propiedad es necesaria.

##### <a name="output-details"></a>Detalles de salida
Contacto: contacto

| Nombre de propiedad | Tipo de datos | Descripción |
|---|---|---|
|Id.|cadena|Identificador único del contacto.|
|Id|cadena|El identificador de la carpeta del contacto principal|
|Cumpleaños|cadena|Cumpleaños del contacto.|
|Archivar como|cadena|El nombre del contacto está archivado.|
|DisplayName|cadena|Nombre para mostrar del contacto.|
|GivenName|cadena|Nombre del contacto.|
|Iniciales|cadena|Las iniciales del contacto.|
|MiddleName|cadena|El nombre del contacto medio.|
|Alias|cadena|El alias del contacto.|
|Apellido|cadena|Apellido del contacto.|
|Título|cadena|Título del contacto.|
|Generación|cadena|Generación del contacto.|
|EmailAddresses|matriz|Direcciones de correo electrónico del contacto.|
|ImAddresses|matriz|Las direcciones del contacto instantáneas mensajería (IM).|
|Puesto temporal|cadena|Puesto del contacto.|
|Nombredelaempresa|cadena|El nombre de la empresa del contacto.|
|Departamento|cadena|Departamento del contacto.|
|OfficeLocation|cadena|La ubicación de la oficina del contacto.|
|Profesión|cadena|La profesión del contacto.|
|BusinessHomePage|cadena|La página de inicio de negocios del contacto.|
|AssistantName|cadena|El nombre del Ayudante del contacto.|
|Administrador|cadena|El nombre del jefe del contacto.|
|HomePhones|matriz|Números de teléfono particular del contacto.|
|BusinessPhones|matriz|Números de teléfono del contacto empresarial|
|MobilePhone1|cadena|Número de teléfono móvil del contacto.|
|Dirección particular|no definido|La dirección del contacto principal.|
|Dirección trabajo|no definido|Dirección del trabajo del contacto.|
|OtherAddress|no definido|Otras direcciones para el contacto.|
|YomiCompanyName|cadena|El nombre de la empresa de japoneses fonética del contacto.|
|YomiGivenName|cadena|Fonético japonés dado nombre (nombre) del contacto.|
|YomiSurname|cadena|El japonés fonética apellido (apellido) del contacto|
|Categorías|matriz|Las categorías asociadas con el contacto.|
|ChangeKey|cadena|Identifica la versión del objeto de evento|
|DateTimeCreated|cadena|La hora en que se creó el contacto.|
|DateTimeLastModified|cadena|El tiempo que se ha modificado el contacto.|


#### <a name="get-contact"></a>Obtener el contacto
Esta operación Obtiene un contacto específico de una carpeta de contactos. 

|Nombre de propiedad| Nombre para mostrar|Descripción|
| ---|---|---|
|tabla *|Id. de carpeta|Seleccione una carpeta de contactos|
|Id *|Id. de elemento|Identificador único de un contacto para recuperar|

Un asterisco (*) significa que la propiedad es necesaria.

##### <a name="output-details"></a>Detalles de salida
Contacto: contacto

| Nombre de propiedad | Tipo de datos | Descripción |
|---|---|---|
|Id.|cadena|Identificador único del contacto.|
|Id|cadena|El identificador de la carpeta del contacto principal|
|Cumpleaños|cadena|Cumpleaños del contacto.|
|Archivar como|cadena|El nombre del contacto está archivado.|
|DisplayName|cadena|Nombre para mostrar del contacto.|
|GivenName|cadena|Nombre del contacto.|
|Iniciales|cadena|Las iniciales del contacto.|
|MiddleName|cadena|El nombre del contacto medio.|
|Alias|cadena|El alias del contacto.|
|Apellido|cadena|Apellido del contacto.|
|Título|cadena|Título del contacto.|
|Generación|cadena|Generación del contacto.|
|EmailAddresses|matriz|Direcciones de correo electrónico del contacto.|
|ImAddresses|matriz|Las direcciones del contacto instantáneas mensajería (IM).|
|Puesto temporal|cadena|Puesto del contacto.|
|Nombredelaempresa|cadena|El nombre de la empresa del contacto.|
|Departamento|cadena|Departamento del contacto.|
|OfficeLocation|cadena|La ubicación de la oficina del contacto.|
|Profesión|cadena|La profesión del contacto.|
|BusinessHomePage|cadena|La página de inicio de negocios del contacto.|
|AssistantName|cadena|El nombre del Ayudante del contacto.|
|Administrador|cadena|El nombre del jefe del contacto.|
|HomePhones|matriz|Números de teléfono particular del contacto.|
|BusinessPhones|matriz|Números de teléfono del contacto empresarial|
|MobilePhone1|cadena|Número de teléfono móvil del contacto.|
|Dirección particular|no definido|La dirección del contacto principal.|
|Dirección trabajo|no definido|Dirección del trabajo del contacto.|
|OtherAddress|no definido|Otras direcciones para el contacto.|
|YomiCompanyName|cadena|El nombre de la empresa de japoneses fonética del contacto.|
|YomiGivenName|cadena|Fonético japonés dado nombre (nombre) del contacto.|
|YomiSurname|cadena|El japonés fonética apellido (apellido) del contacto|
|Categorías|matriz|Las categorías asociadas con el contacto.|
|ChangeKey|cadena|Identifica la versión del objeto de evento|
|DateTimeCreated|cadena|La hora en que se creó el contacto.|
|DateTimeLastModified|cadena|El tiempo que se ha modificado el contacto.|


#### <a name="delete-contact"></a>Eliminar contacto
Esta operación elimina un contacto de una carpeta de contactos. 

|Nombre de propiedad| Nombre para mostrar|Descripción|
| ---|---|---|
|tabla *|Id. de carpeta|Seleccione una carpeta de contactos|
|Id *|Id.|Identificador único del contacto para eliminar|

Un asterisco (*) significa que la propiedad es necesaria.

##### <a name="output-details"></a>Detalles de salida
Ninguno.


#### <a name="update-contact"></a>Actualizar contacto
Esta operación actualiza un contacto en una carpeta de contactos. 

|Nombre de propiedad| Nombre para mostrar|Descripción|
| ---|---|---|
|tabla *|Id. de carpeta|Seleccione una carpeta de contactos|
|Id *|Id.|Identificador único del contacto para la actualización|
|elemento *|Elemento|Actualizar elemento de contacto|

Un asterisco (*) significa que la propiedad es necesaria.

##### <a name="output-details"></a>Detalles de salida
Contacto: contacto

| Nombre de propiedad | Tipo de datos | Descripción |
|---|---|---|
|Id.|cadena|Identificador único del contacto.|
|Id|cadena|El identificador de la carpeta del contacto principal|
|Cumpleaños|cadena|Cumpleaños del contacto.|
|Archivar como|cadena|El nombre del contacto está archivado.|
|DisplayName|cadena|Nombre para mostrar del contacto.|
|GivenName|cadena|Nombre del contacto.|
|Iniciales|cadena|Las iniciales del contacto.|
|MiddleName|cadena|El nombre del contacto medio.|
|Alias|cadena|El alias del contacto.|
|Apellido|cadena|Apellido del contacto.|
|Título|cadena|Título del contacto.|
|Generación|cadena|Generación del contacto.|
|EmailAddresses|matriz|Direcciones de correo electrónico del contacto.|
|ImAddresses|matriz|Las direcciones del contacto instantáneas mensajería (IM).|
|Puesto temporal|cadena|Puesto del contacto.|
|Nombredelaempresa|cadena|El nombre de la empresa del contacto.|
|Departamento|cadena|Departamento del contacto.|
|OfficeLocation|cadena|La ubicación de la oficina del contacto.|
|Profesión|cadena|La profesión del contacto.|
|BusinessHomePage|cadena|La página de inicio de negocios del contacto.|
|AssistantName|cadena|El nombre del Ayudante del contacto.|
|Administrador|cadena|El nombre del jefe del contacto.|
|HomePhones|matriz|Números de teléfono particular del contacto.|
|BusinessPhones|matriz|Números de teléfono del contacto empresarial|
|MobilePhone1|cadena|Número de teléfono móvil del contacto.|
|Dirección particular|no definido|La dirección del contacto principal.|
|Dirección trabajo|no definido|Dirección del trabajo del contacto.|
|OtherAddress|no definido|Otras direcciones para el contacto.|
|YomiCompanyName|cadena|El nombre de la empresa de japoneses fonética del contacto.|
|YomiGivenName|cadena|Fonético japonés dado nombre (nombre) del contacto.|
|YomiSurname|cadena|El japonés fonética apellido (apellido) del contacto|
|Categorías|matriz|Las categorías asociadas con el contacto.|
|ChangeKey|cadena|Identifica la versión del objeto de evento|
|DateTimeCreated|cadena|La hora en que se creó el contacto.|
|DateTimeLastModified|cadena|El tiempo que se ha modificado el contacto.|



## <a name="http-responses"></a>Respuestas HTTP

Las acciones y desencadenadores anteriores pueden devolver uno o varios de los siguientes códigos de estado HTTP: 

|Nombre|Descripción|
|---|---|
|200|Vale|
|202|Aceptado|
|400|Solicitud incorrecta|
|401|No autorizado|
|403|Prohibido|
|404|No se encontró|
|500|Error de servidor interno. Error desconocido|
|predeterminado|Error en la operación.|


## <a name="next-steps"></a>Pasos siguientes

[Crear una aplicación de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md). Explore los otros conectores disponibles en las aplicaciones de lógica en nuestra [lista de API](apis-list.md).
<properties
   pageTitle="Crear aplicaciones de lógica de EAI con VETR en las aplicaciones de la lógica de servicio de la aplicación de Azure | Microsoft Azure"
   description="Validar, codificar y transformar las características de servicios de BizTalk XML"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="rajeshramabathiran"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/20/2016"
   ms.author="rajram"/>


# <a name="create-eai-logic-app-using-vetr"></a>Crear aplicaciones de lógica EAI con VETR

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

La mayoría de los escenarios de integración de aplicación de empresa (EAI) mediar datos entre un origen y destino. Estas situaciones suelen tienen un conjunto común de requisitos:

- Compruebe que los datos procedentes de distintos sistemas son formato correctamente.
- Realizar "búsqueda" en los datos entrantes a tomar decisiones.
- Convertir datos de un formato a otro. Por ejemplo, convertir datos de formato de los datos de un sistema CRM al formato de datos de un sistema ERP.
- Enrutar datos a la aplicación que desee o sistema.

En este artículo se muestra un modelo de integración común: "mediación mensaje unidireccional" o VETR (validar, Enrich, transformación, ruta). El modelo VETR Media datos entre una entidad de origen y un destino. Normalmente, el origen y destino son orígenes de datos.

Considere la posibilidad de un sitio Web que acepta pedidos. Los usuarios publican pedidos para el sistema utilizando HTTP. En segundo plano, el sistema valida los datos entrantes son correctos, normaliza y continúa de una cola de Bus de servicio para la transformación. El sistema le lleva a la cola, esperando en un formato concreto de ordenación. Por lo tanto, el flujo de llevar a cabo es:

**HTTP** → **Validar** → **transformar** → **Bus de servicio**

![Flujo VETR básicas][1]

Las siguientes aplicaciones de API de BizTalk generar este patrón:

* **Desencadenador de HTTP** - origen de evento de mensaje de desencadenador
* **Validar** - valida la exactitud de los datos entrantes
* **Transformar** - transformaciones datos entrantes a formato necesarios para que el sistema de nivel inferior
* **Conector de Bus de servicio** - entidad de destino que se envían los datos


## <a name="constructing-the-basic-vetr-pattern"></a>Crear el modelo VETR básico
### <a name="the-basics"></a>Conceptos básicos

En el portal de Azure, seleccione **+ nuevo**, seleccione **Web + móvil**y, a continuación, seleccione la **Aplicación de la lógica**. Elija un nombre, la ubicación, la suscripción, el grupo de recursos y la ubicación adecuada. Grupos de recursos actúan como contenedores para las aplicaciones; todos los recursos de la aplicación vaya al mismo grupo de recursos.

A continuación, vamos a agregar desencadenadores y acciones.


## <a name="add-http-trigger"></a>Agregar desencadenador de HTTP
1. **Lógica de las plantillas de aplicaciones**, seleccione **crear desde cero**.
1. Seleccione **Escucha HTTP** de la Galería para crear una nueva escucha. Llamar **HTTP1**.
2. Establecer el **Enviar respuesta automáticamente?** el valor false. Configurar la acción de desencadenador estableciendo _Método HTTP_ _POST_ y configuración de la _Dirección URL relativa_ a _/OneWayPipeline_:  
    ![Desencadenador de HTTP][2]
3. Seleccione la marca de verificación verde para completar el desencadenador.

## <a name="add-validate-action"></a>Agregar validar acción

Ahora, vamos a especificar acciones que se ejecutan cada vez que se activa el desencadenador, es decir, cada vez que se recibe una llamada en el extremo HTTP.

1. Agregar **Validación de BizTalk XML** de la galería y asígnele el nombre _(Validate1)_ para crear una instancia.
2. Configurar un esquema XSD para validar los mensajes entrantes de XML. Seleccione la acción _Validar_ y _triggers('httplistener').outputs. Contenido_ como el valor del parámetro _inputXml_ .

Ahora, la acción validar es la primera después escucha HTTP: 

![Validación de XML de BizTalk][3]

Asimismo, vamos a agregar el resto de las acciones. 

## <a name="add-transform-action"></a>Agregar acción de transformación
Vamos a configurar transformaciones para normalizar los datos entrantes.

1. Agregar **El servicio de transformación de BizTalk** de la galería.
2. Para configurar una transformación para transformar los mensajes entrantes de XML, seleccione la acción **transformar** como la acción para llevar a cabo cuando se llama a esta API. Seleccione ```triggers(‘httplistener’).outputs.Content``` como el valor de _inputXml_. *Mapa* es un parámetro opcional, ya que los datos entrantes coincide con todas las transformaciones configuradas y se aplican sólo aquellos que coincidan con el esquema.
3. Por último, se ejecuta la transformación solo si se realiza correctamente validar. Para configurar esta condición, seleccione el icono de engranaje en la parte superior derecha y seleccione _Agregar que se cumpla una condición_. Establezca la condición en ```equals(actions('xmlvalidator').status,'Succeeded')```:  

![Transformaciones de BizTalk][4]


## <a name="add-service-bus-connector"></a>Agregar conector de Bus de servicio
A continuación, vamos a agregar el destino: una cola de Bus de servicio, que se escriben datos.

1. Agregar un **Conector de Bus de servicio** de la galería. Establecer el **nombre** a _Servicebus1_, establezca la **Cadena de conexión** en la cadena de conexión a su instancia de bus de servicio, establezca el **Nombre de la entidad** en _cola_y omitir **nombre de suscripción**.
2. Seleccione la acción **Enviar mensaje** y establezca el campo de **contenido** de la acción en _actions('transformservice').outputs. OutputXml_.
3. Establecer el campo **Tipo de contenido** en la *aplicación/xml*:  

![Bus de servicio][5]


## <a name="send-http-response"></a>Enviar respuesta HTTP
Una vez que haya terminado el procesamiento de canalización, volver a enviar una respuesta HTTP para tener éxito y error con los siguientes pasos:

1. Agregar un **Escucha HTTP** de la galería y seleccione la acción **Enviar respuesta de HTTP** .
2. Establecer el **identificador de respuesta** para enviar el *mensaje*.
2. Establecer **El contenido de respuesta** al *procesamiento de canalización completado*.
3. **Código de estado de respuesta** a *200* para indicar HTTP 200 OK.
4. Seleccione el menú desplegable en la parte superior derecha y seleccione **Agregar que se cumpla una condición**.  Establecer la condición en la siguiente expresión:  
    ```@equals(actions('azureservicebusconnector').status,'Succeeded')```  <br/>
5. Repita estos pasos para enviar una respuesta HTTP error también. Cambie la **condición** en la siguiente expresión:  
```@not(equals(actions('azureservicebusconnector').status,'Succeeded'))``` <br/>
6. Seleccione **Aceptar** y luego **crear**.



## <a name="completion"></a>Finalización
Cada vez que alguien le envía un mensaje al extremo HTTP, se activa la aplicación y se ejecuta las acciones que acaba de crear. Para administrar dichas aplicaciones de lógica crear, seleccione **Examinar** en el Portal de Azure y seleccione **Lógica de aplicaciones**. Seleccione la aplicación para obtener más información.

Algunos temas:

[Administrar y supervisar las aplicaciones de API y los conectores](app-service-logic-monitor-your-connectors.md)  <br/>
[Supervisar las aplicaciones de lógica](app-service-logic-monitor-your-logic-apps.md)

<!--image references -->
[1]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BasicVETR.PNG
[2]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/HTTPListener.PNG
[3]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BizTalkXMLValidator.PNG
[4]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BizTalkTransforms.PNG
[5]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/AzureServiceBus.PNG

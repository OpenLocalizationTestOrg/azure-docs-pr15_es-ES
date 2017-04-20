## <a name="send-messages-to-event-hubs"></a>Enviar mensajes a Hubs de evento

La biblioteca de cliente de Java para Hubs de evento está disponible para usarlo en proyectos de experto del [Repositorio Central de experto](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22)y se puede hacer referencia mediante la siguiente declaración de dependencia dentro de su archivo de proyecto experto:    

``` XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>{VERSION}</version>
</dependency>
```
 
Para diferentes tipos de entornos de compilación, puede obtener explícitamente los archivos JAR más recientes lanzamiento del [Repositorio Central de experto](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22) o desde [el punto de distribución de la versión en GitHub](https://github.com/Azure/azure-event-hubs/releases).  

Para un editor de evento simple, importar el *com.microsoft.azure.eventhubs* para las clases de cliente Hubs de evento y el paquete *com.microsoft.azure.servicebus* para las clases de utilidad como excepciones comunes que están compartidas con el cliente de mensajería de Bus de servicio de Azure. 

Para el ejemplo siguiente, primero cree un nuevo proyecto de experto para una aplicación de consola/shell en su entorno de desarrollo de Java favorito. Se llamará a la clase ```Send```.     

``` Java

import java.io.IOException;
import java.nio.charset.*;
import java.util.*;
import java.util.concurrent.ExecutionException;

import com.microsoft.azure.eventhubs.*;
import com.microsoft.azure.servicebus.*;

public class Send
{
    public static void main(String[] args) 
            throws ServiceBusException, ExecutionException, InterruptedException, IOException
    {
```

Reemplazar el espacio de nombres y los nombres de evento concentrador con los valores que utilizó cuando creó el Hub de evento.

``` Java
    final String namespaceName = "----ServiceBusNamespaceName-----";
    final String eventHubName = "----EventHubName-----";
    final String sasKeyName = "-----SharedAccessSignatureKeyName-----";
    final String sasKey = "---SharedAccessSignatureKey----";
    ConnectionStringBuilder connStr = new ConnectionStringBuilder(namespaceName, eventHubName, sasKeyName, sasKey);
```

A continuación, cree un evento singular convertir una cadena en su codificación UTF-8 de bytes. A continuación, se crea una nueva instancia de cliente de Hubs de evento de la cadena de conexión y envíe el mensaje.   

``` Java 
                
    byte[] payloadBytes = "Test AMQP message from JMS".getBytes("UTF-8");
    EventData sendEvent = new EventData(payloadBytes);
    
    EventHubClient ehClient = EventHubClient.createFromConnectionStringSync(connStr.toString());
    ehClient.sendSync(sendEvent);
    }
}

``` 

<properties 
    pageTitle="Información general del modelo de autenticación y seguridad de evento Hubs | Microsoft Azure"
    description="Evento Hubs autenticación y seguridad Introducción al modelo."
    services="event-hubs"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="event-hubs"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="08/16/2016"
    ms.author="sethm;clemensv" />

# <a name="event-hubs-authentication-and-security-model-overview"></a>Introducción del modelo de autenticación y seguridad de Hubs eventos

El modelo de seguridad de evento Hubs cumple los siguientes requisitos:

- Sólo dispositivos que presentan credenciales válidas pueden enviar datos a un concentrador de evento.
- Un dispositivo no puede representar otro dispositivo.
- Se pueden bloquear para un dispositivo dudoso enviar datos a un concentrador de evento.

## <a name="device-authentication"></a>Autenticación de dispositivo

El modelo de seguridad de evento Hubs se basa en una combinación de símbolos de [Firma de acceso compartido (SA)](../service-bus-messaging/service-bus-shared-access-signature-authentication.md) y *editores de evento*. Un editor de evento define un extremo virtual para un concentrador de evento. El Editor sólo puede utilizarse para enviar mensajes a un concentrador de evento. No es posible recibir mensajes de un editor.

Normalmente, un concentrador de evento emplea a un editor por dispositivo. Todos los mensajes que se envían a cualquiera de los editores de un concentrador de evento están en cola en ese concentrador de evento. Editores de habilitar el control de acceso detallado y límite.

Cada dispositivo se le asigna un único token, que se ha cargado en el dispositivo. Los tokens producidos por ejemplo, que cada token único conceda acceso a un único editor diferente. Sólo puede enviar un dispositivo que tiene un símbolo de un editor, pero ningún otro editor. Si hay varios dispositivos comparten el mismo token, cada uno de estos dispositivos comparte un editor.

Aunque no se recomienda, es posible equipamiento dispositivos con tokens que concesión acceso directo a un concentrador de evento. Cualquier dispositivo que guarda este token puede enviar mensajes directamente a ese concentrador de evento. Este tipo de dispositivo no estará sujeto a limitación. Además, el dispositivo no puede estar en la lista negra de enviar a ese concentrador de evento.

Todos los tokens iniciado sesión con una clave de SA. Normalmente, todos los tokens iniciado sesión con la misma clave. Dispositivos no tienen constancia de la clave; impide que los dispositivos de fabricación tokens.

### <a name="create-the-sas-key"></a>Crear la clave de SA

Al crear un espacio de nombres de evento Hubs, Azure evento Hubs genera una clave de SA de 256 bits denominada **RootManageSharedAccessKey**. Esta clave concede enviar, escucha y administrar derechos para el espacio de nombres. Puede crear teclas adicionales. Se recomienda que producen una clave que concede permisos Enviar al concentrador de evento específico. Para el resto de este tema, se supone que denominados esta clave `EventHubSendKey`.

En el ejemplo siguiente se crea una clave de enviar al crear el Hub de evento:

```
// Create namespace manager.
string serviceNamespace = "YOUR_NAMESPACE";
string namespaceManageKeyName = "RootManageSharedAccessKey";
string namespaceManageKey = "YOUR_ROOT_MANAGE_SHARED_ACCESS_KEY";
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, string.Empty);
TokenProvider td = TokenProvider.CreateSharedAccessSignatureTokenProvider(namespaceManageKeyName, namespaceManageKey);
NamespaceManager nm = new NamespaceManager(namespaceUri, namespaceManageTokenProvider);

// Create Event Hub with a SAS rule that enables sending to that Event Hub
EventHubDescription ed = new EventHubDescription("MY_EVENT_HUB") { PartitionCount = 32 };
string eventHubSendKeyName = "EventHubSendKey";
string eventHubSendKey = SharedAccessAuthorizationRule.GenerateRandomKey();
SharedAccessAuthorizationRule eventHubSendRule = new SharedAccessAuthorizationRule(eventHubSendKeyName, eventHubSendKey, new[] { AccessRights.Send });
ed.Authorization.Add(eventHubSendRule); 
nm.CreateEventHub(ed);
```

### <a name="generate-tokens"></a>Generar tokens

Puede generar tokens con la clave de SA. Debe generar un único token por dispositivo. Tokens, a continuación, se fabrican con el método siguiente. Todos los tokens se generan mediante la tecla **EventHubSendKey** . Cada token se asigna un URI único.

```
public static string SharedAccessSignatureTokenProvider.GetSharedAccessSignature(string keyName, string sharedAccessKey, string resource, TimeSpan tokenTimeToLive)
```

Al llamar a este método, se debe especificar el URI como `//<NAMESPACE>.servicebus.windows.net/<EVENT_HUB_NAME>/publishers/<PUBLISHER_NAME>`. Para todos los tokens, el URI es idéntico, con la excepción de `PUBLISHER_NAME`, que debe ser distintos para cada símbolo. Lo ideal es que, `PUBLISHER_NAME` representa el identificador del dispositivo que recibe ese token.

Este método genera un token con la estructura siguiente:

```
SharedAccessSignature sr={URI}&sig={HMAC_SHA256_SIGNATURE}&se={EXPIRATION_TIME}&skn={KEY_NAME}
```

La fecha de expiración de token especificada en segundos desde el 1 de enero de 1970. El siguiente es un ejemplo de un símbolo:

```
SharedAccessSignature sr=contoso&sig=nPzdNN%2Gli0ifrfJwaK4mkK0RqAB%2byJUlt%2bGFmBHG77A%3d&se=1403130337&skn=RootManageSharedAccessKey
```

Normalmente, los tokens tienen una duración que se asemeja a o supera el ciclo de vida del dispositivo. Si el dispositivo tiene la posibilidad de obtener un nuevo símbolo, se pueden utilizar tokens con una duración más corta.

### <a name="devices-sending-data"></a>Enviar datos de dispositivos

Una vez que se han creado los tokens, cada dispositivo se aprovisiona con su propio token único.

Cuando el dispositivo envía los datos a un concentrador de evento, el dispositivo etiquetas su token con la solicitud de envío. Para evitar que un intruso espionaje y robo el token, debe aparecer la comunicación entre el dispositivo y el concentrador de eventos a través de un canal cifrado.

### <a name="blacklisting-devices"></a>Dispositivos confidencial

Si un símbolo desaparece intruso, el intruso puede representar el dispositivo ha sido robe cuyo token. Lista negra un dispositivo inutilizable el dispositivo hasta que el dispositivo se expresa un nuevo token que utiliza un editor diferente.

## <a name="authentication-of-back-end-applications"></a>Autenticación de aplicaciones de back-end

Para autenticar aplicaciones de back-end que consumen los datos generados por dispositivos, Hubs de evento emplea un modelo de seguridad que es similar al modelo de los temas de Bus de servicio. Un grupo de consumidor de evento Hubs es equivalente a una suscripción a un tema de Bus de servicio. Un cliente puede crear un grupo de consumidor si la solicitud para crear el grupo de consumidor se acompaña de un símbolo que concede administrar privilegios para el concentrador de eventos o para el espacio de nombres al que pertenece el Hub de evento. Un cliente puede consumir datos de un grupo de consumidor si se acompaña un símbolo que concede derechos de recepción en ese grupo de consumidor, el concentrador de eventos o el espacio de nombres al que pertenece el concentrador de eventos de la solicitud de recepción.

La versión actual de Bus de servicio no admite reglas de SA para suscripciones individuales. Lo mismo sucede evento Hubs grupos de consumidores. Soporte técnico de SA se agregará para ambas características en el futuro.

Falta de autenticación SA grupos de consumidores individuales, puede usar claves de SA para proteger todos los grupos de consumidores con una clave común. Este enfoque permite una aplicación para consumir datos desde cualquiera de los grupos de consumidores de un concentrador de evento.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre el evento Hubs, visite los siguientes temas:

- [Información general de Hubs de evento]
- Una [solución de mensajería en la cola] mediante colas de Bus de servicio.
- Una [aplicación de ejemplo que usa el evento Hubs]completa.

[Información general de Hubs de evento]: event-hubs-overview.md
[aplicación de ejemplo que usa Hubs de evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[solución de mensajería en la cola]: ../service-bus-messaging/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
 

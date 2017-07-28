<properties 
    pageTitle="Excepciones de retransmisión | Microsoft Azure"
    description="Lista de excepciones de retransmisión y las acciones sugeridas."
    services="service-bus"
    documentationCenter="na"
    authors="jtaubensee"
    manager="timlt"
    editor="tysonn" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/28/2016"
    ms.author="jotaub" />

# <a name="relay-exceptions"></a>Excepciones de retransmisión

Este artículo enumeran algunas excepciones generadas por las API de retransmisión de Microsoft Azure. Esta referencia está sujeto a cambios, así que compruebe si hay actualizaciones.

## <a name="exception-categories"></a>Categorías de excepción

Las API de retransmisión generar las excepciones que pueden se dividen en las siguientes categorías, junto con la acción asociada que puede realizar para solucionarlos.

1.  Usuarios de código de error ([System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx)). Acción general: probar para solucionar el código antes de continuar.

2.  Error de instalación y configuración ([Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingentitynotfoundexception.aspx), [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Acción general: revisar la configuración y cambiar si es necesario.

3.  Excepciones transitorias ([Microsoft.ServiceBus.Messaging.MessagingException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.aspx), [Microsoft.ServiceBus.Messaging.ServerBusyException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.serverbusyexception.aspx), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingcommunicationexception.aspx)). Acción general: vuelva a intentar la operación o notificar a los usuarios.

4.  Otras excepciones ([System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx), [Microsoft.ServiceBus.Messaging.MessageLockLostException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagelocklostexception.aspx), [Microsoft.ServiceBus.Messaging.SessionLockLostException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sessionlocklostexception.aspx)). Acción general: específica del tipo de excepción; Consulte la tabla en la sección siguiente. 

## <a name="exception-types"></a>Tipos de excepción

En la siguiente tabla enumera los tipos de excepción mensajería y sus causas y acción sugerida de notas que puede realizar.

| **Tipo de excepción**                                                                                                                                                                                                                                                                                | **Causa de descripción y ejemplos**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | **Acción sugerida**                                                                                                                                                                                                                                                                                                                                                                                                          | **Tenga en cuenta en automático o reintentos**                                                                                             |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------|
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx)                                                                                                                                                                                                           | El servidor no respondió a la operación dentro de la hora especificada que está controlada por [OperationTimeout](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx). Puede que el servidor haya completado la operación. Esto puede ocurrir debido a la red o en otros retrasos de infraestructura.                                                                                                                                                                                                                                                                   | Comprobar el estado del sistema para mantener la coherencia y vuelva a intentar si es necesario. Vea [las excepciones de tiempo de espera](#timeoutexception).                                                                                                                                                                                                                                                                                                                                                           | Reintentar podrían ser de ayuda en algunos casos; agregar lógica de reintento al código.                                                                      |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx)                                                                                                                                                                                         | No se permite la operación de usuario solicitado dentro del servidor o el servicio. Vea el mensaje de excepción para obtener información detallada. Por ejemplo, [completado](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) generará esta excepción si ha recibido el mensaje en el modo de **ReceiveAndDelete** .                                                                                                                                                                                                                                                                                                     | Compruebe el código y la documentación. Asegúrese de que la operación solicitada es válida.                                                                                                                                                                                                                                                                                                                                         | Reintentar no le ayudará a.                                                                                                          |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx)                                                                                                                                                                                       | Intenta llamar a una operación en un objeto que ya se ha cerrado, anulado o eliminado. En algunas ocasiones, ya se elimina la transacción de ambiente.                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | Compruebe el código y asegúrese de que no invoca operaciones en un objeto eliminado.                                                                                                                                                                                                                                                                                                                                          | Reintentar no le ayudará a.                                                                                                          |
| [UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx)                                                                                                                                                                                     | El objeto [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) no puede adquirir un símbolo, el token no es válido o el token no contiene las notificaciones necesarias para ejecutar la operación.                                                                                                                                                                                                                                                                                                                                                                                                  | Asegúrese de que el proveedor de tokens se crea con los valores correctos. Compruebe la configuración del servicio de Control de acceso.                                                                                                                                                                                                                                                                                                   | Reintentar podrían ser de ayuda en algunos casos; agregar lógica de reintento al código.                                                                      |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) | Uno o varios de los argumentos proporcionados al método no son válidos.<br /> El URI proporcionado a [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) o [crear](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.create.aspx) contiene los segmentos de ruta de acceso.<br /> El esquema URI proporcionado a [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) o [crear](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.create.aspx) no es válido. <br />El valor de la propiedad es mayor que 32KB. | Compruebe el código de llamada y asegúrese de que los argumentos son correctos.                                                                                                                                                                                                                                                                                                                                                           | Reintentar no le ayudará a.                                                                                                          |
| [ServerBusyException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.serverbusyexception.aspx)                                                                                                                                                                       | Servicio no está disponible procesar la solicitud en este momento.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | Cliente puede esperar durante un período de tiempo y luego vuelva a intentar la operación.                                                                                                                                                                                                                                                                                                                                                           | Cliente puede reintentar después intervalo determinado. Si un reintento da como resultado una excepción diferente, compruebe el comportamiento de intentos de esa excepción. |
| [QuotaExceededException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.quotaexceededexception.aspx)                                                                                                                                                                 | La entidad mensajería alcanzó su tamaño máximo permitido.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | Recibir mensajes de la entidad o sus subcarpetas colas para crear espacio en la entidad. Vea [QuotaExceededException](#quotaexceededexception).                                                                                                                                                                                                                                                                                                                                      | Reintentar puede ser útil si se han eliminado mientras tanto mensajes.                                                               |
| [MessageSizeExceededException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesizeexceededexception.aspx)                                                                                                                                                     | Carga del mensaje supera el límite de 256K. Observe que el límite de 256 k es el tamaño total del mensaje, que puede incluir cualquier sobrecarga de .NET y de propiedades del sistema.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | Reducir el tamaño de la carga del mensaje y luego vuelva a intentar la operación.                                                                                                                                                                                                                                                                                                                                                         | Reintentar no le ayudará a.                                                                                                          |

## <a name="quotaexceededexception"></a>QuotaExceededException

[QuotaExceededException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.quotaexceededexception.aspx) indica que se ha superado una cuota para una entidad específica.

Retransmisión, esta excepción ajusta la [System.ServiceModel.QuotaExceededException](https://msdn.microsoft.com/library/system.servicemodel.quotaexceededexception.aspx), que indica que se superó el número máximo de agentes de escucha para este extremo. Esto se indica en el valor de **MaximumListenersPerEndpoint** del mensaje de excepción.

## <a name="timeoutexception"></a>TimeoutException 

[TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) indica que una operación iniciada por el usuario está llevando más larga que el tiempo de espera de la operación. 

Debe comprobar el valor de la propiedad [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) , como visitar esta puede límite también causa un [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx).

Retransmisión, puede que reciba excepciones de tiempo de espera cuando se abre una conexión de remitente de retransmisión. Hay dos causas habituales de esta excepción:

1. El valor de [OpenTimeout](https://msdn.microsoft.com/library/wcf.opentimeout.aspx) podría ser demasiado pequeño (incluso una fracción de un segundo).
2. La listener(s) de retransmisión local no responde (o puede surgir problemas de reglas de firewall prohibir los agentes de escucha acepte nuevas conexiones de cliente), y el valor de [OpenTimeout](https://msdn.microsoft.com/library/wcf.opentimeout.aspx) es menor que unos 20 segundos.

Por ejemplo:

```
'System.TimeoutException’: The operation did not complete within the allotted timeout of 00:00:10. The time allotted to this operation may have been a portion of a longer timeout.
```

### <a name="common-causes"></a>Causas comunes

Hay dos causas comunes de este error: configuración incorrecta o un error de servicio transitorias.

1. **Configuración incorrecta** 
    el tiempo de espera de la operación podría ser demasiado pequeño para la condición operativa. El valor predeterminado para el tiempo de espera de la operación en el SDK de cliente es 60 segundos. Compruebe si su código tiene el valor establecido en algo demasiado pequeño. Tenga en cuenta que la condición de la red y el uso de CPU puede afectar al tiempo que se tarda de una determinada operación completar, por lo que no se debe establecer el tiempo de espera de la operación en un valor muy pequeño.

2. **Error de servicio transitorias** 
    retransmisión a veces puede sufrir retrasos en el procesamiento de las solicitudes; Por ejemplo, durante los períodos de tráfico alto. En estos casos, puede volver a intentar la operación después de un retraso hasta que la operación es correcta. Si aún no la misma operación después de varios intentos, visite el [sitio de estado del servicio de Azure](https://azure.microsoft.com/status/) para ver si hay interrupciones del servicio conocido.

## <a name="next-steps"></a>Pasos siguientes:

- [Retransmisión de preguntas más frecuentes](relay-faq.md)
- [Crear un espacio de nombres](relay-create-namespace-portal.md)
- [Introducción a .NET](relay-hybrid-connections-dotnet-get-started.md)
- [Introducción a nodo](relay-hybrid-connections-node-get-started.md)
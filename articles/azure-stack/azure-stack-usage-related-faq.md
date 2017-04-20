<properties
    pageTitle="Preguntas frecuentes relacionadas con el uso | Microsoft Azure"
    description="Lista de metros de pila de Azure, en comparación con la API de uso de Azure, tiempo de uso y tiempo notificado, códigos de error."
    services="azure-stack"
    documentationCenter=""
    authors="AlfredoPizzirani"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="alfredop"/>

# <a name="azure-stack-usage-api-faqs"></a>Preguntas más frecuentes de uso de la API de pila de Azure
En este artículo se da respuesta a algunas preguntas frecuentes acerca de la API de uso de pila de Azure.

## <a name="what-meter-ids-can-i-see"></a>¿Qué contador identificadores puedo ver?

Actualmente, se notifica el uso de la red, almacenamiento y proveedores de recursos de cálculo.

| **Proveedor de recursos** | **Identificador de contador** |**Nombre de contador** | **Unidad** | **Información adicional** |
| --------------------------- | --------------------------------------- | -------------------------- | ---------------------------- | ----------------------------------------- |
| **Red** | f114cb19-ea64-40b5-bcd7-aee474b62853 | Uso de la dirección IP pública | Dirección IP |                    
| **Almacenamiento de información**  | B4438D5D-453B-4EE1-B42A-DC72E377F1E4 | TableCapacity | GB\*horas | Capacidad total consumida por tablas |
|              | B5C15376-6C94-4FDD-B655-1A69D138ACA3 | PageBlobCapacity | GB\*horas | Capacidad total consumida por blobs de página |
|              | B03C6AE7-B080-4BFA-84A3-22C800F315C6 | QueueCapacity  | GB\*horas  | Capacidad total consumida por cola |
| | 09F8879E-87E9-4305-A572-4B7BE209F857 | BlockBlobCapacity | GB\*horas  | Capacidad total consumida por BLOB de bloque |
| | B9FF3CD0-28AA-4762-84BB-FF8FBAEA6A90 | TableTransactions  | Recuento de solicitud de 10,000s   | Solicitudes de servicio de tabla (en 10,000s) |
| | 50A1AEAF-8ECA-48A0-8973-A5B3077FEE0D | TableDataTransIn | Datos de entrada en GB | Entrada de datos de servicio de tabla en GB |
| | 1B8C1DEC-EE42-414B-AA36-6229CF199370 | TableDataTransOut | Outgress en GB | Salida de datos de servicio de tabla en GB |
| | 43DAF82B-4618-444A-B994-40C23F7CD438 | BlobTransactions | Contar las solicitudes de 10,000s | Solicitudes de servicio de blobs (en 10,000s) |
| | 9764F92C-E44A-498E-8DC1-AAD66587A810   | BlobDataTransIn    | Datos de entrada en GB          | Entrada de datos de servicio de blobs de GB 
| | 3023FEF4-ECA5-4D7B-87B3-CFBC061931E8   | BlobDataTransOut   | Outgress en GB              | Salida de datos de servicio de blobs de GB 
| | EB43DD12-1AA6-4C4B-872C-FAF15A6785EA   | QueueTransactions  | Contar las solicitudes de 10,000s   | Solicitudes de servicio de cola (en 10,000s) 
| | E518E809-E369-4A45-9274-2017B29FFF25   | QueueDataTransIn          | Datos de entrada en GB         | Entrada de datos de servicio de cola en GB 
| | DD0A10BA-A5D6-4CB6-88C0-7D585CEF9FC2   | QueueDataTransOut         | Outgress en GB  | Salida de datos de servicio de cola en GB 
| **Calcular** | 6DAB500F-A4FD-49C4-956D-229BB9C8C793 | Horas de tamaño VM | Tamaño de la máquina virtual |



## <a name="how-do-the-azure-stack-usage-apis-compare-to-the-azure-usage-apihttpsmsdnmicrosoftcomlibraryazure1ea5b323-54bb-423d-916f-190de96c6a3c-currently-in-public-preview"></a>¿Cómo se comparan las API de uso de pila de Azure a la [API de uso de Azure](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) (actualmente en versión preliminar pública)?

-   La API de uso del inquilino es coherente con la API de Azure, con una excepción: la marca de *showDetails* actualmente no es compatible con la pila de Azure.

-   La API de uso del proveedor se aplica solo a la pila de Azure.

-   Actualmente, la [API de RateCard](https://msdn.microsoft.com/en-us/library/azure/mt219004.aspx) que está disponible en Azure no está disponible en la pila de Azure.

## <a name="what-is-the-difference-between-usage-time-and-reported-time"></a>¿Cuál es la diferencia entre la hora de uso y registrado?

Informes de uso de datos tienen dos valores de hora principal:

-   **Tiempo notificado**. La hora cuando el evento de uso especifica el sistema de uso

-   **Tiempo de uso**. Tiempo cuando se ha consumido el recurso de pila de Azure

Es posible que vea una discrepancia en los valores de tiempo de uso y tiempo notificado para un evento de uso específico. El retraso puede tener hasta varias horas en cualquier entorno.

Actualmente, puede crear una consulta *solo por tiempo notificado*.

## <a name="what-do-these-usage-api-error-codes-mean"></a>¿Qué significan estos códigos de error de la API de uso?

| **Código de estado HTTP** | **Código de error** | **Descripción** |
| ---------------------- | ------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------ |
| Solicitud incorrecta/400        | *NoApiVersion*     | Falta el parámetro de consulta de la *versión de la api* .
| Solicitud incorrecta/400        | *InvalidProperty*  | Falta una propiedad o tiene un valor no válido. El mensaje en el código de error en el cuerpo de respuesta identifica la propiedad que falta.
| Solicitud incorrecta/400        | *RequestEndTimeIsInFuture*  | El valor de *ReportedEndTime* es en el futuro. En el futuro no se permiten valores para este argumento.
| Solicitud incorrecta/400        | *SubscriberIdIsNotDirectTenant*    | Una llamada del proveedor API utiliza un identificador de suscripción no es un inquilino válido del llamador.
| Solicitud incorrecta/400        | *SubscriptionIdMissingInRequest*   | Falta el identificador de suscripción de la persona que llama.
| Solicitud incorrecta/400        | *InvalidAggregationGranularity*   | Se ha solicitado un detalle agregado no válido. Valores válidos son diarias y cada hora.
| 503                    | *ServiceUnavailable*   | Se ha producido un error recuperable porque el servicio está ocupado o se está limitando la llamada. |

## <a name="next-steps"></a>Pasos siguientes
[Cliente de facturación y cargo al usuario en pila de Azure](azure-stack-billing-and-chargeback.md)

[Uso de recursos de proveedor API](azure-stack-provider-resource-api.md)

[Uso de recursos API de inquilinos](azure-stack-tenant-resource-usage-api.md)

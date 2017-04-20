<properties
    pageTitle="Uso de recursos API de inquilinos | Microsoft Azure"
    description="Referencia de uso de recursos API, que recuperar información de uso de la pila de Azure."
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

# <a name="tenant-resource-usage-api"></a>Uso de recursos API de inquilinos

Un inquilino puede usar la API de inquilinos para ver los datos de uso de recursos del inquilino. Esta API es coherente con la API de uso de Azure (actualmente en versión preliminar de privada).

Puede usar el cmdlet **Get-UsageAggregates** de Windows PowerShell para obtener datos de uso como en Azure.

## <a name="api-call"></a>Llamada a la API

### <a name="request"></a>Solicitar

La solicitud obtiene detalles de consumo para las suscripciones solicitadas y para el período de tiempo solicitado. No hay ningún cuerpo de la solicitud.

| **Método**  | **URI de la solicitud** |
| ------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Obtener         | https://{armendpoint}/subscriptions/{subId}/providers/Microsoft.Commerce/usageAggregates?reportedStartTime={reportedStartTime}&reportedEndTime={reportedEndTime}&aggregationGranularity={granularity}&api-version=2015-06-01-preview&continuationToken={token-value} |

### <a name="arguments"></a>Argumentos

| **Argumento**             | **Descripción** |
| -------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| *Armendpoint*             | Azure extremo del Administrador de recursos de su entorno de pila de Azure. |
| *subId*                   | Identificador de la suscripción del usuario que realiza la llamada. Puede utilizar esta API solo a la consulta para el uso de una sola suscripción. Proveedores pueden utilizar la API de uso de recursos de proveedor para el uso de consultas para todos los inquilinos. |
| *reportedStartTime*       | Hora de inicio de la consulta. El valor de *DateTime* debe ser en UTC y al principio de la hora, por ejemplo, 13:00. Para la agregación diaria, establezca este valor en medianoche UTC. El formato es *un carácter de escape* ISO 8601, por ejemplo, 2015-06-16T18% 3a53% 3a11% 2b00% 3a00Z, donde dos puntos situado a % 3a y además se convierte en % 2 mil millones para que sea descriptivo URI. |
| *reportedEndTime*         | Hora de finalización de la consulta. Las restricciones que se aplican a *reportedStartTime* también se aplicarán a este argumento. El valor de *reportedEndTime* no puede estar en el futuro. |
| *aggregationGranularity*  | Parámetro opcional que tiene dos valores posibles discretos: diariamente y cada hora. Como sugieren los valores, uno devuelve los datos de detalle diaria y la otra es una resolución por hora. La opción diaria es el valor predeterminado. |
| *subscriberId*            | Identificador de suscripción. Para obtener los datos filtrados, el identificador de la suscripción de un inquilino directo del proveedor es necesario. Si no se especifica ningún parámetro de identificador de la suscripción, la llamada devuelve datos de uso para los inquilinos directa de la de su proveedor. |
| *versión de la API*             | Versión del protocolo que se usa para realizar esta solicitud. Debe utilizar la vista previa de 2015-06-01. |
| *continuationToken*       | Símbolo de recupera desde la última llamada del proveedor de la API de uso. Esto es necesario cuando una respuesta es superior a 1.000 líneas. Esto es el marcador de progreso. Si no está presente, se recuperan los datos desde el principio del día u hora, según el nivel de detalle que se han pasado. |

### <a name="response"></a>Respuesta

OBTENER /subscriptions/sub1/providers/Microsoft.Commerce/UsageAggregates?reportedStartTime=reportedStartTime=2014-05-01T00%3a00%3a00%2b00%3a00 & reportedEndTime = 2015-06-01T00% 3a00% 3a00% 2b00% 3a00 & aggregationGranularity = diaria & versión api = 1.0

{

"valor":\[

{

"identificador": "/ subscriptions/sub1/providers/Microsoft.Commerce/UsageAggregate/sub1-meterID1",

"nombre": "sub1-meterID1"

"tipo": "Microsoft.Commerce/UsageAggregate"

"propiedades de": {

"subscriptionId": "sub1"

"usageStartTime": "2015-03-03T00:00:00 + 00:00",

"usageEndTime": "2015-03-04T00:00:00 + 00:00",

"instanceData": "{\\" Microsoft.Resources\\": {\\" resourceUri\\":\\" resourceUri1\\",\\" ubicación\\":\\" Alaska\\",\\" etiquetas\\": null,\\" additionalInfo\\": null}}",

"cantidad":2.4000000000,

"meterId": "meterID1"

}

},

…

### <a name="response-details"></a>Detalles de la respuesta

| **Argumento**      | **Descripción** |
| ------------------ | ------------------------------------------------------------------------------------------------------------- |
| *Id.*              | Identificador exclusivo de agregado de uso |
| *nombre*            | Nombre del agregado de uso |
| *tipo*            | Definición de recursos |
| *subscriptionId*  | Identificador de la suscripción del usuario Azure |
| *usageStartTime*  | UTC hora de inicio del depósito de uso al que pertenece este agregado de uso |
| *usageEndTime*    | Hora de finalización de UTC del depósito de uso al que pertenece este agregado de uso |
| *instanceData*    | Pares de valor de clave de detalles de la instancia (en un nuevo formato):<br>  *resourceUri*: completo identificador del recurso, incluidos el nombre de la instancia y grupos de recursos <br>  *ubicación*: región en el que se ejecute este servicio <br>  *etiquetas*: etiquetas de recursos que especifica el usuario <br>  *additionalInfo*: más detalles acerca de los recursos que consume, por ejemplo, tipo de imagen o la versión del SO |
| *cantidad*        | Cantidad de consumo de los recursos que ocurrieron en este período de tiempo |
| *meterId*         | Identificador exclusivo para el recurso que se ha consumido (también llamados *ResourceID*) |

## <a name="next-steps"></a>Pasos siguientes

[P+F de uso](azure-stack-usage-related-faq.md)

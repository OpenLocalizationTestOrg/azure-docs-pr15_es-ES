<properties
    pageTitle="Solución de Azure depósito de clave en el análisis de registro | Microsoft Azure"
    description="Puede usar la solución de Azure clave depósito de análisis de registro de revisar los registros del depósito de clave de Azure."
    services="log-analytics"
    documentationCenter=""
    authors="richrundmsft"
    manager="jochan"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/12/2016"
    ms.author="richrund"/>

# <a name="azure-key-vault-preview-solution-in-log-analytics"></a>Azure solución clave depósito (Preview) en el análisis de registro

>[AZURE.NOTE] Esta es una [solución de vista previa](log-analytics-add-solutions.md#log-analytics-preview-solutions-and-features).

Puede usar la solución de Azure clave depósito en análisis de registro para revisar los registros de Azure clave depósito AuditEvent.

Puede habilitar el registro de eventos de auditoría para depósito de clave de Azure. Estos registros se escriben en donde pueden, a continuación, indizar por registro análisis para realizar búsquedas y análisis de almacenamiento de blobs de Windows Azure.

## <a name="install-and-configure-the-solution"></a>Instalar y configurar la solución

Utilice las instrucciones siguientes para instalar y configurar la solución de Azure depósito de clave:

1.  Habilitar el [registro de diagnóstico para depósito de clave de](../key-vault/key-vault-logging.md) recursos que desea supervisar
2.  Configurar el análisis de registro para leer los registros de almacenamiento de blobs mediante el proceso descrito en [archivos JSON en el almacenamiento de blobs](../log-analytics/log-analytics-azure-storage-json.md).
3.  Habilitar la solución de Azure clave depósito mediante el proceso descrito en [soluciones de agregar el registro de análisis de la Galería de soluciones](log-analytics-add-solutions.md).  

## <a name="review-azure-key-vault-data-collection-details"></a>Revise los detalles de colección de datos de Azure clave depósito

Azure solución clave depósito recopila registros de diagnósticos de almacenamiento de blobs de Windows Azure para depósito de clave de Azure.
No se necesita para la recopilación de datos agente.

La siguiente tabla muestra los métodos de conjunto de datos y otros detalles acerca de cómo se recopilan los datos de Azure clave depósito.

| Plataforma | Agente directo | Agente de Systems Center Operations Manager (SCOM) | Almacenamiento de Azure | ¿SCOM obligatorio? | Datos de agente SCOM enviadas por grupo de administración | Frecuencia de la colección |
|---|---|---|---|---|---|---|
|Azure|![No](./media/log-analytics-azure-keyvault/oms-bullet-red.png)|![No](./media/log-analytics-azure-keyvault/oms-bullet-red.png)|![Sí](./media/log-analytics-azure-keyvault/oms-bullet-green.png)|            ![No](./media/log-analytics-azure-keyvault/oms-bullet-red.png)|![No](./media/log-analytics-azure-keyvault/oms-bullet-red.png)| 10 minutos|

## <a name="use-azure-key-vault"></a>Usar Azure depósito clave

Después de instalar la solución, puede ver el resumen de solicitud de mosaico Estados a lo largo del tiempo para la supervisadas depósitos clave mediante el uso de la **Cámara de clave de Azure** en la página de **información general** de análisis de registro.

![imagen del icono de cámara de clave de Azure](./media/log-analytics-azure-keyvault/log-analytics-keyvault-tile.png)

Después de hacer clic en el icono de **información general** , puede ver resúmenes de los registros y explora detalles para las siguientes categorías:

- Volumen de todas las operaciones de clave depósito con el tiempo
- No se pudo volúmenes de operación a lo largo del tiempo
- Latencia promedio de operaciones por operación
- Calidad de servicio para operaciones con el número de las operaciones que tienen más de 1000 ms y una lista de las operaciones que tienen más de 1000 ms

![imagen del panel de la cámara de clave de Azure](./media/log-analytics-azure-keyvault/log-analytics-keyvault01.png)

![imagen del panel de la cámara de clave de Azure](./media/log-analytics-azure-keyvault/log-analytics-keyvault02.png)

### <a name="to-view-details-for-any-operation"></a>Ver detalles de cualquier operación

1. En la página **Introducción** , haga clic en el icono de **Cámara de clave de Azure** .
2. En el panel de la **Cámara de clave de Azure** , revise la información de resumen en uno de los módulos y, a continuación, haga clic en uno para ver información detallada sobre él en la página de búsqueda de registro.

    En cualquiera de las páginas de búsqueda de registro, puede ver los resultados de tiempo, resultados detallados y el historial de búsqueda de registro. También puede filtrar por facetas y limitar los resultados.

## <a name="log-analytics-records"></a>Análisis de registros

La solución de Azure clave depósito analiza registros que tienen un tipo de **KeyVaults** que se recopilan desde [AuditEvent registros](../key-vault/key-vault-logging.md) de diagnósticos de Azure.  Propiedades de estos registros se encuentran en la tabla siguiente.  

| (Propiedad) | Descripción |
|:--|:--|
| Tipo | *KeyVaults* |
| SourceSystem | *AzureStorage* |
| CallerIpAddress | Dirección IP del cliente que realizó la solicitud |
| Categoría      | Para los registros del depósito de clave, AuditEvent es el valor único, disponible.|
| CorrelationId | GUID de opcional que el cliente puede pasar para relacionar registros del cliente con los registros del servicio (depósito de clave). |
| DurationMs | Tiempo que tardó la solicitud de la API de REST, en milisegundos. No incluye la latencia de red, por lo que el tiempo que se mide en el cliente no puede ajustarse a esta vez. |
| HttpStatusCode_d | Código de estado HTTP devuelto por la solicitud |
| Id_s       | Identificador exclusivo de la solicitud |
| Identity_o | Identidad del token que se presenta al realizar la solicitud de la API de REST. Suele ser un "usuario", "principales de servicio" o una combinación "usuario + ID" como en el caso de una solicitud es el resultado de un cmdlet de PowerShell de Azure. |
| OperationName      | Nombre de la operación, tal como se describe en [El registro de depósito de la clave de Azure](../key-vault/key-vault-logging.md)|
| OperationVersion      | Versión de la API de REST solicitado por el cliente|
| RemoteIPLatitude | Latitud del cliente que realizó la solicitud |
| RemoteIPLongitude | Longitud del cliente que realizó la solicitud |
| RemoteIPCountry | País del cliente que realizó la solicitud  |
| RequestUri_s | URI de la solicitud |
| Recursos   | Nombre del depósito clave |
| ResourceGroup | Grupo de recursos del depósito clave |
| ResourceId | Id. de recurso de administrador de recursos de Azure Para los registros del depósito de clave, siempre es el identificador de recurso de clave depósito. |
| ResourceProvider | *MICROSOFT. KEYVAULT* |
| ResultSignature  | Estado HTTP|
| ResultType      | Resultado de la solicitud de la API de REST|
| SubscriptionId | Id. de suscripción Azure de la suscripción que contiene el depósito de clave |


## <a name="next-steps"></a>Pasos siguientes

- Usar [búsquedas de registro de análisis de registro](log-analytics-log-searches.md) para ver datos detallados de Azure clave depósito.

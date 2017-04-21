<properties
    pageTitle="Usar la interfaz de línea de comandos de varias plataformas (CLI) para crear alertas para los servicios de Azure | Microsoft Azure"
    description="Usar la interfaz de línea de comandos para crear alertas de Azure, que pueden desencadenar automatización o notificaciones cuando se cumplen las condiciones especificadas."
    authors="rboucher"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/24/2016"
    ms.author="robb"/>

# <a name="use-the-cross-platform-command-line-interface-cli-to-create-alerts-for-azure-services"></a>Usar la interfaz de línea de comandos de varias plataformas (CLI) para crear alertas para los servicios de Azure

> [AZURE.SELECTOR]
- [Portal](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [CLI](insights-alerts-command-line-interface.md)

## <a name="overview"></a>Información general

Este artículo le muestra cómo configurar las alertas de Azure utilizando la línea de comandos (CLI).

>[AZURE.NOTE] Monitor de Azure es el nuevo nombre de lo que se denomina "Azure perspectivas" hasta 25 de septiembre de 2016. Sin embargo, los espacios de nombres y, por lo tanto, los comandos siguientes contengan "información".

Puede recibir una alerta basada en supervisión métricas para o eventos en los servicios de Azure.

- **Valores de métrica** : desencadenadores de alerta cuando el valor de una métrica especificado cruza un umbral que asignar en cualquier dirección. Es decir, active ambas cuando la condición se cumple en primer lugar y, a continuación, posteriormente cuando condición que ya no se cumple.    
- **Eventos de registro de actividad** : puede desencadenar una alerta en *todos* los eventos o, solo cuando se produce un número determinado de eventos.

Puede configurar una alerta para cuando Active hacer lo siguiente:

- enviar notificaciones de correo electrónico al administrador del servicio y coadministradores
- enviar correo electrónico a mensajes de correo electrónico adicionales que especifique.
- llamar a un webhook
- iniciar la ejecución de un runbook Azure (solo desde el portal de Azure en este momento)

Puede configurar y obtener información sobre cómo usar reglas de alertas

- [Portal de Azure](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [línea de comandos de interfaz](insights-alerts-command-line-interface.md)
- [API de REST de Azure Monitor](https://msdn.microsoft.com/library/azure/dn931945.aspx)


Siempre puede recibir ayuda para comandos escribiendo un comando y poner - ayuda al final. Por ejemplo:

    ```console
    azure insights alerts -help
    azure insights alerts actions email create -help
    ```

## <a name="create-alert-rules-using-the-cli"></a>Crear reglas de alerta con la CLI

1. Realizar los requisitos previos y el inicio de sesión de Azure. Vea [ejemplos de Azure Monitor CLI](insights-cli-samples.md). En resumen, instalar CLI y ejecutar estos comandos. Lo que le ha iniciado sesión, mostrar qué suscripción está usando y preparar ejecutar comandos Monitor de Azure.


    ```console
    azure login
    azure account show
    azure config mode arm

    ```

2.  Para mostrar las reglas existentes en un grupo de recursos, utilice el siguiente formulario **azure perspectivas lista de reglas de alertas** *[opciones] &lt;resourceGroup&gt; *

    ```console
    azure insights alerts rule list myresourcegroupname

    ```
3. Para crear una regla, debe tener varios datos importantes en primer lugar.
    - El **Identificador de recursos** para el recurso que desea establecer una alerta para
    - Las **definiciones de métrica** disponible para ese recurso

    Una forma de obtener el identificador del recurso es usar el portal de Azure. Suponiendo que ya se ha creado el recurso, selecciónela en el portal. A continuación, en el módulo siguiente, seleccione *Propiedades* en la sección *configuración* . El *Identificador de recurso* es un campo en el módulo siguiente. Otra forma utilizar el [Explorador de recursos de Azure](https://resources.azure.com/).

    Es un identificador de recursos de ejemplo para una aplicación web

    ```console
    /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
    ```

    Para obtener una lista de las estadísticas disponibles y las unidades de esas métricas para el ejemplo anterior de recursos, use el siguiente comando CLI:  

    ```console
    azure insights metrics list /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename PT1M
    ```

    _PT1M_ es el nivel de detalle de la medida disponible (de 1 minuto). Usar diferentes granularidades le da opciones métricas diferentes.


4. Para crear una regla de alerta de métrica, use un comando de la forma siguiente:

    **métrica de la regla de alertas perspectivas Azure establecer** *[opciones] &lt;nombreregla&gt; &lt;ubicación&gt; &lt;resourceGroup&gt; &lt;tamañoDeVentana&gt; &lt;operador&gt; &lt;umbral&gt; &lt;targetResourceId&gt; &lt;metricName&gt; &lt;timeAggregationOperator&gt; *

    En el ejemplo siguiente se establece una alerta en un recurso de sitio web. Desencadenadores de alerta cada vez que recibe constantemente el tráfico de 5 minutos y cuando no reciba tráfico durante 5 minutos.

    ```console
    azure insights alerts rule metric set myrule eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total

    ```

5. Para crear webhook o enviar correo electrónico cuando se desencadene una alerta, cree primero el correo electrónico o el webhooks. A continuación, cree la regla inmediatamente después. No se puede asociar webhook o correos electrónicos con ya ha creado reglas mediante la CLI.

    ```console
    azure insights alerts actions email create --customEmails myemail@contoso.com

    azure insights alerts actions webhook create https://www.contoso.com

    azure insights alerts rule metric set myrulewithwebhookandemail eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total
    ```


6. Para crear una alerta que se activa en una condición específica en el registro de actividad, use el formulario:

    **regla de alertas de perspectivas registrar conjunto** *[opciones] &lt;nombreregla&gt; &lt;ubicación&gt; &lt;resourceGroup&gt; &lt;operationName&gt; *

    Por ejemplo

    ```console
    azure insights alerts rule log set myActivityLogRule eastus myresourceGroupName Microsoft.Storage/storageAccounts/listKeys/action
    ```

    La operationName corresponde a un tipo de evento para una entrada en el registro de actividad. Por ejemplo, *Microsoft.Compute/virtualMachines/delete* y *microsoft.insights/diagnosticSettings/write*.

    Puede usar el comando PowerShell [Get-AzureRmProviderOperation](https://msdn.microsoft.com/library/mt603720.aspx) para obtener una lista de posibles operationNames. Como alternativa, puede usar el portal de Azure para el registro de actividad de la consulta y encontrar específicos más allá de las operaciones que desea crear una alerta para. Las operaciones que se muestra en la vista de gráfico de registro de los nombres descriptivos. Buscar en el JSON para la entrada y saque el valor OperationName.   

7. Puede comprobar que se han creado correctamente las alertas consultando una regla individual.

    ```console
    azure insights alerts rule list myresourcegroup --ruleName myrule
    ```

8. Para eliminar reglas, use un comando del formulario:

    **Eliminar regla de alertas de perspectivas** [opciones] &lt;resourceGroup&gt; &lt;nombreregla&gt;

    Estos comandos eliminar las reglas que creó anteriormente en este artículo.

    ```console
    azure insights alerts rule delete myresourcegroup myrule
    azure insights alerts rule delete myresourcegroup myrulewithwebhookandemail
    azure insights alerts rule delete myresourcegroup myActivityLogRule
    ```



## <a name="next-steps"></a>Pasos siguientes

* [Obtenga una visión general de supervisión Azure](monitoring-overview.md) incluidos los tipos de información puede recopilar y supervisar.
* Más información sobre la [configuración webhooks en alertas](insights-webhooks-alerts.md).
* Más información sobre [Runbooks de automatización de Azure](..\automation\automation-starting-a-runbook.md).
* Obtenga una [Visión general de recopilación de registros de diagnóstico](monitoring-overview-of-diagnostic-logs.md) para recopilar métricas detalladas de alta frecuencia en el servicio.
* Obtenga una [Visión general de la colección de métricas](insights-how-to-customize-monitoring.md) para asegurarse de que el servicio está disponible y capacidad de respuesta.

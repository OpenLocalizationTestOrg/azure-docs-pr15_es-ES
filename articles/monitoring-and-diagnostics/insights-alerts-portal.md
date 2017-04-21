<properties
    pageTitle="Usar el portal de Azure para crear alertas para los servicios de Azure | Microsoft Azure"
    description="Usar el portal de Azure para crear alertas de Azure, que pueden desencadenar automatización o notificaciones cuando se cumplen las condiciones especificadas."
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
    ms.date="09/23/2016"
    ms.author="robb"/>

# <a name="use-azure-portal-to-create-alerts-for-azure-services"></a>Usar el portal de Azure para crear alertas para los servicios de Azure

> [AZURE.SELECTOR]
- [Portal](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [CLI](insights-alerts-command-line-interface.md)

## <a name="overview"></a>Información general

Este artículo le muestra cómo configurar las alertas de Azure con el portal de Azure.   

Puede recibir una alerta basada en supervisión métricas para o eventos en los servicios de Azure.

- **Valores de métrica** : desencadenadores de alerta cuando el valor de una métrica especificado cruza un umbral que asignar en cualquier dirección. Es decir, active ambas cuando la condición se cumple en primer lugar y, a continuación, posteriormente cuando condición que ya no se cumple.    
- **Eventos de registro de actividad** : puede desencadenar una alerta en *todos* los eventos o, solo cuando se produce un número determinado de eventos.


Puede configurar una alerta para cuando Active hacer lo siguiente:

- enviar notificaciones de correo electrónico al administrador del servicio y coadministradores
- enviar correo electrónico a mensajes de correo electrónico adicionales que especifique.
- llamar a un webhook
- iniciar la ejecución de un runbook Azure (solo desde el portal de Azure)

Puede configurar y obtener información sobre cómo usar reglas de alertas

- [Portal de Azure](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [línea de comandos de interfaz](insights-alerts-command-line-interface.md)
- [API de REST de Azure Monitor](https://msdn.microsoft.com/library/azure/dn931945.aspx)


## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Crear una regla de alerta de una métrica con el portal de Azure

1. En el [portal](https://portal.azure.com/), busque el recurso que le interesa supervisión y selecciónelo.

2. Seleccione **alertas** o **las reglas de alerta** en la sección de supervisión. El texto y el icono pueden variar ligeramente para recursos diferentes.  

    ![Supervisión](./media/insights-alerts-portal/AlertRulesButton.png)


3. Seleccione el comando **Agregar aviso** y rellene los campos.

    ![Agregar alerta](./media/insights-alerts-portal/AddAlertOnlyParamsPage.png)

4. **Nombre de** la alerta de la regla y a continuación, elija una **Descripción**, que también se muestra en los mensajes de correo electrónico de notificación.
5. Seleccione la **métrica** que desea supervisar y luego elija un valor de **condición** y **umbral** de la métrica. También eligió el **período** de tiempo que debe satisfacer la regla métrica antes de la alerta desencadenadores. Por ejemplo, si usa el período "PT5M" y la alerta busca CPU superior al 80%, la alerta activa cuando la CPU ha estado constantemente anterior 80% durante 5 minutos. Una vez que se produce el primer desencadenador, vuelva a activa cuando la CPU permanece por debajo de 80% durante 5 minutos. La medida de CPU se produce cada minuto.   

6. Compruebe **los propietarios de correo electrónico...** si desea que los administradores y compañeros para recibir un mensaje cuando se activa la alerta.

7. Si desea que los mensajes de correo electrónico adicionales para recibir una notificación cuando se activa la alerta, puede agregarlos en el campo **email(s) Administrador adicionales** . Separar varios mensajes de correo electrónico con punto y coma:*email@contoso.com;email2@contoso.com*

8. Poner en un URI válido en el campo **Webhook** si desea que se le llama cuando se activa la alerta.

9. Si utiliza Automatización de Azure, puede seleccionar un Runbook que se ejecuta cuando se activa la alerta.

10. Seleccione **Aceptar** cuando haya terminado para crear la alerta.   

En unos minutos, la alerta está activa y activa como se describió anteriormente.

## <a name="managing-your-alerts"></a>Administrar las alertas

Una vez que haya creado una alerta, puede seleccionar y:

- Ver un gráfico que muestra el umbral de métrica y los valores reales del día anterior.
- Editar o eliminar.
- **Deshabilitar** o **Habilitar** si desea temporalmente detener o reanudar recibir notificaciones que.



## <a name="next-steps"></a>Pasos siguientes

* [Obtenga una visión general de supervisión Azure](monitoring-overview.md) incluidos los tipos de información puede recopilar y supervisar.
* Más información sobre la [configuración webhooks en alertas](insights-webhooks-alerts.md).
* Más información sobre [Runbooks de automatización de Azure](..\automation\automation-starting-a-runbook.md).
* Obtenga una [Visión general de los registros de diagnóstico](monitoring-overview-of-diagnostic-logs.md) y recopilar métricas detalladas de alta frecuencia en el servicio.
* Obtenga una [Visión general de la colección de métricas](insights-how-to-customize-monitoring.md) para asegurarse de que el servicio está disponible y capacidad de respuesta.

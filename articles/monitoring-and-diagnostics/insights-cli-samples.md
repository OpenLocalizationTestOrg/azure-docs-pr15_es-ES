<properties
    pageTitle="Ejemplos de inicio rápido de Azure CLI Monitor. | Microsoft Azure"
    description="Comandos CLI de ejemplo para características de Monitor de Azure. Monitor de Azure es un servicio de Microsoft Azure que permite enviar notificaciones de alerta, llame a direcciones URL de web basadas en los valores de los datos de telemetría configurado y servicios de nube Autoescala, máquinas virtuales y aplicaciones Web."
    authors="kamathashwin"
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
    ms.date="09/08/2016"
    ms.author="ashwink"/>

# <a name="azure-monitor--cross-platform-cli-quick-start-samples"></a>Ejemplos de inicio rápido de Azure Monitor entre plataformas CLI

Este artículo le muestra ejemplo línea de comandos de interfaz de comandos para ayudarle a tener acceso a características de Azure Monitor. Monitor de Azure le permite Autoescala Cloud Services, máquinas virtuales y aplicaciones Web y para enviar alertas o llamar según los valores de los datos de telemetría configurado con las direcciones URL de web.

>[AZURE.NOTE] Monitor de Azure es el nuevo nombre de lo que se denomina "Azure perspectivas" hasta 25 de septiembre de 2016. Sin embargo, los espacios de nombres y, por lo tanto, los comandos siguientes contengan "información".


## <a name="prerequisites"></a>Requisitos previos

Si ya no ha instalado la CLI de Azure, consulte [instalar CLI Azure](../xplat-cli-install.md). Si no está familiarizado con Azure CLI, encontrará más información en [uso CLI para Mac, Linux y con el Administrador de recursos de Azure de Windows Azure](../xplat-cli-azure-resource-manager.md).


En Windows, instale npm desde el [sitio Web de Node.js](https://nodejs.org/). Después de completar la instalación, usando CMD.exe con privilegios de ejecutar como administrador, ejecute el siguiente de la carpeta donde está instalado npm:

```console
npm install azure-cli --global
```

A continuación, vaya a cualquier carpeta o ubicación que desee y escriba en la línea de comandos:

```console
azure help
```

## <a name="log-in-to-azure"></a>Inicie sesión en Azure

El primer paso es iniciar sesión en su cuenta de Azure.

```console
azure login
```

Después de ejecutar este comando, debe iniciar sesión a través de las instrucciones de la pantalla. Cuando termine, verá su cuenta, TenantId y predeterminado identificador de suscripción. Todos los comandos funcionan en el contexto de la suscripción de forma predeterminada.

Para mostrar los detalles de su suscripción actual, utilice el siguiente comando.

```console
azure account show
```

Para cambiar el contexto de trabajo a una suscripción diferente, use el siguiente comando.

```console
azure account set "subscription ID or subscription name"
```

Para usar los comandos de administrador de recursos de Azure y Azure Monitor, debe estar en modo de administrador de recursos de Azure.

```console
azure config mode arm
```

Para ver una lista de todos los comandos Monitor de Azure compatibles, realice lo siguiente.

```console
azure insights
```

## <a name="view-audit-logs-for-a-subscription"></a>Ver registros de auditoría para una suscripción

Para ver una lista de registros de auditoría, realice lo siguiente.

```console
azure insights logs list [options]
```

Intente lo siguiente para ver todas las opciones disponibles.

```console
azure insights logs list -help
```

Aquí tenemos un ejemplo a registros de la lista por un resourceGroup

```console
azure insights logs list --resourceGroup "myrg1"
```

Ejemplo de registros de la lista por llamador

```console
azure insights logs list --caller "myname@company.com"
```

Ejemplo de los registros de la lista por llamador en un tipo de recurso, dentro de una fecha de inicio y finalización

```console
azure insights logs list --resourceProvider "Microsoft.Web" --caller "myname@company.com" --startTime 2016-03-08T00:00:00Z --endTime 2016-03-16T00:00:00Z
```

## <a name="work-with-alerts"></a>Trabajar con alertas
Puede usar la información en la sección para trabajar con alertas.

### <a name="get-alert-rules-in-a-resource-group"></a>Obtener las reglas de alertas en un grupo de recursos

```console
azure insights alerts rule list abhingrgtest123
azure insights alerts rule list abhingrgtest123 --ruleName andy0323
```

### <a name="create-a-metric-alert-rule"></a>Crear una regla de alerta métrica

```console
azure insights alerts actions email create --customEmails foo@microsoft.com
azure insights alerts actions webhook create https://someuri.com
azure insights alerts rule metric set andy0323 eastus abhingrgtest123 PT5M GreaterThan 2 /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Web-EastUS/providers/Microsoft.Web/serverfarms/Default1 BytesReceived Total
```

### <a name="create-a-log-alert-rule"></a>Crear una regla de alerta de registro

```console
azure insights alerts rule log set ruleName eastus resourceGroupName someOperationName
```

### <a name="create-webtest-alert-rule"></a>Crear regla de alerta de prueba Web

```console
azure insights alerts rule webtest set leowebtestr1-webtestr1 eastus Default-Web-WestUS PT5M 1 GSMT_AvRaw /subscriptions/b67f7fec-69fc-4974-9099-a26bd6ffeda3/resourcegroups/Default-Web-WestUS/providers/microsoft.insights/webtests/leowebtestr1-webtestr1
```

### <a name="delete-an-alert-rule"></a>Eliminar una regla de alerta

```console
azure insights alerts rule delete abhingrgtest123 andy0323
```

## <a name="log-profiles"></a>Perfiles de registro
Use la información de esta sección para trabajar con los perfiles de registro.

### <a name="get-a-log-profile"></a>Obtener un perfil de registro

```console
azure insights logprofile list
azure insights logprofile get -n default
```


### <a name="add-a-log-profile-without-retention"></a>Agregar un perfil de registro sin retención

```console
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### <a name="remove-a-log-profile"></a>Quitar un perfil de registro

```console
azure insights logprofile delete --name default
```

### <a name="add-a-log-profile-with-retention"></a>Agregar un perfil de registro con retención

```console
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia --retentionInDays 90
```

### <a name="add-a-log-profile-with-retention-and-eventhub"></a>Agregar un perfil de registro con EventHub y retención

```console
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --serviceBusRuleId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/testshoeboxeastus/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia --retentionInDays 90
```


## <a name="diagnostics"></a>Diagnósticos
Use la información de esta sección para trabajar con la configuración de diagnóstico.

### <a name="get-a-diagnostic-setting"></a>Obtener la configuración de diagnóstico

```console
azure insights diagnostic get --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp
```

### <a name="disable-a-diagnostic-setting"></a>Deshabilitar la configuración de diagnóstico

```console
azure insights diagnostic set --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp --storageId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/shibanitesting --enabled false
```

### <a name="enable-a-diagnostic-setting-without-retention"></a>Habilitar a una opción de diagnóstico sin retención

```console
azure insights diagnostic set --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp --storageId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/shibanitesting --enabled true
```


## <a name="autoscale"></a>Autoescala
Use la información de esta sección para trabajar con la configuración de ajustar automáticamente. Necesita modificar estos ejemplos.

### <a name="get-autoscale-settings-for-a-resource-group"></a>Obtener Autoescala configuración para un grupo de recursos

```console
azure insights autoscale setting list montest2
```

### <a name="get-autoscale-settings-by-name-in-a-resource-group"></a>Obtener configuración Autoescala por su nombre en un grupo de recursos

```console
azure insights autoscale setting list montest2 -n setting2
```


### <a name="set-auotoscale-settings"></a>Establecer la configuración de auotoscale

```console
azure insights autoscale setting set montest2 -n setting2 --settingSpec
```

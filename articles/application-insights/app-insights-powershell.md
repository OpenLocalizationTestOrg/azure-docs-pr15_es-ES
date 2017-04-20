<properties 
    pageTitle="Crear recursos aplicación perspectivas con PowerShell" 
    description="Crear mediante programación recursos perspectivas de aplicación como parte de la compilación." 
    services="application-insights" 
    documentationCenter=""
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="03/02/2016" 
    ms.author="awills"/>
 
# <a name="create-application-insights-resources-using-powershell"></a>Crear recursos aplicación perspectivas con PowerShell

Este artículo le muestra cómo crear un recurso de [Aplicación perspectivas](app-insights-overview.md) en Azure automáticamente. Por ejemplo, puede hacerlo como parte de un proceso de generación. Junto al recurso de aplicación información básico, puede crear [pruebas de web de disponibilidad](app-insights-monitor-web-app-availability.md), [Configure alertas](app-insights-alerts.md)y crear otros recursos de Azure.

La clave para crear estos recursos es las plantillas JSON para [El Administrador de recursos de Azure](../powershell-azure-resource-manager.md). En resumen, el procedimiento es: descargar las definiciones de JSON de los recursos existentes agregar parámetros determinados valores como nombres; y, a continuación, ejecute la plantilla siempre que desee crear un nuevo recurso. Puede empaquetar varios recursos, crearlos todo en un ir - por ejemplo, un monitor de la aplicación con pruebas de disponibilidad, alertas y almacenamiento para exportar continuo. Hay algunos matices a algunas de las parametrizaciones, que se explican aquí.

## <a name="one-time-setup"></a>Configuración de una sola vez

Si todavía no lo ha usado PowerShell con la suscripción antes de Azure:

Instalar el módulo de Powershell de Azure en el equipo donde desea ejecutar las secuencias de comandos:

1. Instalar [Instalador de plataforma Web de Microsoft (v5 o superior)](http://www.microsoft.com/web/downloads/platform.aspx).
2. Puede usarlo para instalar Microsoft Azure Powershell.

## <a name="copy-the-json-for-existing-resources"></a>Copiar el JSON para los recursos existentes

1. Configurar una [Aplicación perspectivas](app-insights-overview.md) para un proyecto similares a los que desea generar automáticamente. Si lo desea, agregue pruebas web y alertas.
2. Crear un nuevo archivo de .json: vamos a llamarlo `template1.json` en este ejemplo. Copie este contenido en él:


    ```JSON

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "appName": { "type": "string" },
            "webTestName": { "type": "string" },
            "url": { "type": "string" },
            "text": { "type" : "string" }
          },
          "variables": {
            "testName": "[concat(parameters('webTestName'), 
               '-', toLower(parameters('appName')))]"
            "alertRuleName": "[concat(parameters('webTestName'), 
               '-', toLower(parameters('appName')), 
               '-', subscription().subscriptionId)]"
          },
          "resources": [
            {
              // component JSON file contents
            },
            {
              //web test JSON file contents
            },
            {
              //alert rule JSON file contents
            }
 
            // Any other resources go here
          ]
        }
    
    ```

    Esta plantilla configurará una disponibilidad prueba además del recurso principal.


2. Abra el [Administrador de recursos de Azure](https://resources.azure.com/). Desplazarse hacia abajo por `subscriptions/resourceGroups/<your resource group>/providers/Microsoft.Insights/components`, para los recursos de la aplicación. 

    ![Navegación en el Explorador de recursos de Azure](./media/app-insights-powershell/01.png)

    *Componentes* son los recursos de aplicación perspectivas básicos para mostrar aplicaciones. Hay recursos independientes para las reglas de alerta asociadas disponibilidad pruebas y web.

3. Copie el JSON del componente en el lugar adecuado en `template1.json`.
6. Eliminar estas propiedades:
  * `id`
  * `InstrumentationKey`
  * `CreationDate`
4. Abrir las secciones pruebas Web y alertrules y copie el JSON para elementos individuales en la plantilla. (No copie de los nodos de pruebas Web o alertrules: vaya a los elementos en ellos.)

    Cada prueba web tiene una regla de alerta asociada, para que tenga que copiar los dos.

    La prueba web debe ir antes de la regla de alerta.

5. Para cumplir con el esquema, inserte esta línea en cada recurso:

    `"apiVersion": "2014-04-01",`

    (El esquema también se queja sobre el uso de mayúsculas de los nombres de tipo de recurso `Microsoft.Insights/*` --pero *no* cambiarlos.)


## <a name="parameterize-the-template"></a>Agregar parámetros a la plantilla

Ahora debe reemplazar los nombres específicos con parámetros. Para [Agregar parámetros a una plantilla](../resource-group-authoring-templates.md), escribir expresiones mediante un [conjunto de funciones auxiliares](../resource-group-template-functions.md). 

No puede agregar parámetros sólo una parte de una cadena, así que utilice `concat()` para generar cadenas.

Estos son algunos ejemplos de las sustituciones que querrá asegurarse. Existen varias instancias de cada sustitución. Es posible que tenga otras personas en la plantilla. Estos ejemplos usan los parámetros y variables definido en la parte superior de la plantilla.

Buscar | Reemplazar por
---|---
`"hidden-link:/subscriptions/.../components/MyAppName"`| `"[concat('hidden-link:',`<br/>` resourceId('microsoft.insights/components',` <br/> ` parameters('appName')))]"`
`"/subscriptions/.../alertrules/myAlertName-myAppName-subsId",` | `"[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]",`
`"/subscriptions/.../webtests/myTestName-myAppName",` | `"[resourceId('Microsoft.Insights/webtests', parameters('webTestName'))]",`
`"myWebTest-myAppName"` | `"[variables(testName)]"'`
`"myTestName-myAppName-subsId"` | `"[variables('alertRuleName')]"`
`"myAppName"` | `"[parameters('appName')]"`
`"myappname"`(minúscula) | `"[toLower(parameters('appName'))]"`
`"<WebTest Name=\"myWebTest\" ...`<br/>` Url=\"http://fabrikam.com/home\" ...>"`|`[concat('<WebTest Name=\"',` <br/> `parameters('webTestName'),` <br/> `'\" ... Url=\"', parameters('Url'),` <br/> `'\"...>')]" `



## <a name="set-dependencies-between-the-resources"></a>Establecer dependencias entre los recursos

Azure debe configurar los recursos en un orden estricto. Para asegurarse de que el programa de instalación se completa antes de que comience la siguiente, agregar líneas de dependencia:

* Recurso de prueba en la web:

    `"dependsOn": ["[resourceId('Microsoft.Insights/components', parameters('appName'))]"],`

* En el recurso de alerta:

    `"dependsOn": ["[resourceId('Microsoft.Insights/webtests', variables('testName'))]"],`

## <a name="create-application-insights-resources"></a>Crear perspectivas de aplicación recursos

1. En PowerShell, inicie sesión en Azure

    `Login-AzureRmAccount`

2. Ejecutar un comando similar a esta:

    ```PS

        New-AzureRmResourceGroupDeployment -ResourceGroupName Fabrikam `
               -templateFile .\template1.json `
               -appName myNewApp `
               -webTestName aWebTest `
               -Url http://myapp.com `
               -text "Welcome!"
               -siteName "MyAzureSite"

    ``` 

    * -ResourceGroupName es el grupo donde desea crear nuevos recursos.
    * -templateFile debe aparecer antes de los parámetros personalizados.
    * -Aplicación el nombre del recurso que se va a crear.
    * -webTestName el nombre de la prueba web para crear.
    * -Dirección Url de la dirección url de la aplicación web.
    * -texto una cadena que aparece en su página web.
    * -Sitio - usado si se trata de un sitio Web de Azure


## <a name="define-metric-alerts"></a>Definir métricas alertas

Existe un [método de PowerShell de establecimiento de alertas](app-insights-alerts.md#set-alerts-by-using-powershell).


## <a name="an-example"></a>Un ejemplo

Aquí es el componente completo, prueba web y web prueba plantilla de alerta que he creado:

``` JSON

{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "webTestName": { "type": "string" },
    "appName": { "type": "string" },
    "URL": { "type": "string" },
    "text": { "type" : "string" }
  },
  "variables": {
    "alertRuleName": "[concat(parameters('webTestName'), '-', toLower(parameters('appName')), '-', subscription().subscriptionId)]",
    "testName": "[concat(parameters('webTestName'), '-', toLower(parameters('appName')))]"
  },
  "resources": [
    {
      //"id": "[resourceId('Microsoft.Insights/components', parameters('appName'))]",
      "apiVersion": "2014-04-01",
      "kind": "web",
      "location": "Central US",
      "name": "[parameters('appName')]",
      "properties": {
        "TenantId": "9122605a-471fc50f8438",
        "Application_Type": "web",
        "Flow_Type": "Brownfield",
        "Request_Source": "VSIX3.3.1.0",
        "Name": "[parameters('appName')]",
        //"CreationDate": "2015-10-14T15:55:10.0917441+00:00",
        "PackageId": null,
        "ApplicationId": "[parameters('appName')]"
      },
      "tags": { },
      "type": "microsoft.insights/components"
    },
    {
      //"id": "[resourceId('Microsoft.Insights/webtests', variables('testName'))]",
      "name": "[variables('testName')]",
      "apiVersion": "2014-04-01",
      "type": "microsoft.insights/webtests",
      "location": "Central US",
      "tags": {
        "[concat('hidden-link:', resourceId('microsoft.insights/components', parameters('appName')))]": "Resource"
      },
      "properties": {
        "provisioningState": "Succeeded",
        "Name": "[parameters('webTestName')]",
        "Description": "",
        "Enabled": true,
        "Frequency": 900,
        "Timeout": 120,
        "Kind": "ping",
        "RetryEnabled": true,
        "Locations": [
          {
            "Id": "us-va-ash-azr"
          },
          {
            "Id": "emea-nl-ams-azr"
          },
          {
            "Id": "emea-gb-db3-azr"
          }
        ],
        "Configuration": {
          "WebTest": "[concat(
             '<WebTest   Name=\"', 
                parameters('webTestName'), 
              '\"  Id=\"32cfc791-aaad-4b50-9c8d-993c21beb218\"   Enabled=\"True\"         CssProjectStructure=\"\"    CssIteration=\"\"  Timeout=\"120\"  WorkItemIds=\"\"         xmlns=\"http://microsoft.com/schemas/VisualStudio/TeamTest/2010\"         Description=\"\"  CredentialUserName=\"\"  CredentialPassword=\"\"         PreAuthenticate=\"True\"  Proxy=\"default\"  StopOnError=\"False\"         RecordedResultFile=\"\"  ResultsLocale=\"\">  <Items>  <Request Method=\"GET\"         Guid=\"a6f2c90b-61bf-b28hh06gg969\"  Version=\"1.1\"  Url=\"', 
              parameters('Url'), 
              '\" ThinkTime=\"0\"  Timeout=\"300\" ParseDependentRequests=\"True\"         FollowRedirects=\"True\" RecordResult=\"True\" Cache=\"False\"         ResponseTimeGoal=\"0\"  Encoding=\"utf-8\"  ExpectedHttpStatusCode=\"200\"         ExpectedResponseUrl=\"\" ReportingName=\"\" IgnoreHttpStatusCode=\"False\" />        </Items>  <ValidationRules> <ValidationRule  Classname=\"Microsoft.VisualStudio.TestTools.WebTesting.Rules.ValidationRuleFindText, Microsoft.VisualStudio.QualityTools.WebTestFramework, Version=10.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a\" DisplayName=\"Find Text\"         Description=\"Verifies the existence of the specified text in the response.\"         Level=\"High\"  ExectuionOrder=\"BeforeDependents\">  <RuleParameters>        <RuleParameter Name=\"FindText\" Value=\"', 
              parameters('text'), 
              '\" />  <RuleParameter Name=\"IgnoreCase\" Value=\"False\" />  <RuleParameter Name=\"UseRegularExpression\" Value=\"False\" />  <RuleParameter Name=\"PassIfTextFound\" Value=\"True\" />  </RuleParameters> </ValidationRule>  </ValidationRules>  </WebTest>')]"
        },
        "SyntheticMonitorId": "[variables('testName')]"
      }
    },
    {
      //"id": "[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]",
      "name": "[variables('alertRuleName')]",
      "apiVersion": "2014-04-01",
      "type": "microsoft.insights/alertrules",
      "location": "East US",
      "dependsOn": [
        "[resourceId('Microsoft.Insights/components', parameters('appName'))]",
        "[resourceId('Microsoft.Insights/webtests', variables('testName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource",
        "[concat('hidden-link:', resourceId('Microsoft.Insights/webtests', variables('testName')))]": "Resource"
      },
      "properties": {
        "name": "[variables('alertRuleName')]",
        "description": "",
        "isEnabled": true,
        "condition": {
          "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.LocationThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
          "odata.type": "Microsoft.Azure.Management.Insights.Models.LocationThresholdRuleCondition",
          "dataSource": {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
            "resourceUri": "[resourceId('microsoft.insights/webtests', variables('testName'))]",
            "metricName": "GSMT_AvRaW"
          },
          "windowSize": "PT15M",
          "failedLocationCount": 2
        },
        "action": {
          "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
          "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
          "sendToServiceOwners": true,
          "customEmails": [ ]
        },
        "provisioningState": "Succeeded",
        "actions": [ ]
      }

    }
  ]
}

```

## <a name="see-also"></a>Vea también

Otros artículos de automatización:

* [Crear un recurso de aplicación perspectivas](app-insights-powershell-script-create-resource.md) - método rápido sin usar una plantilla.
* [Configurar alertas](app-insights-powershell-alerts.md)
* [Crear pruebas web](https://azure.microsoft.com/blog/creating-a-web-test-alert-programmatically-with-application-insights/)
* [Enviar diagnóstico de Azure a perspectivas de aplicación](app-insights-powershell-azure-diagnostics.md)
* [Crear anotaciones de lanzamiento](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)

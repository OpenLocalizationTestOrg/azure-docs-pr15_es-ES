### <a name="app-service-plan"></a>Plan de servicios de aplicación

Crea el plan de servicio para la aplicación web de hospedaje. Proporcione el nombre del plan a través del parámetro **hostingPlanName** . La ubicación del plan es la misma ubicación utilizada para el grupo de recursos. El tamaño de nivel y trabajo precio se especifican en los parámetros **sku** y **workerSize**

    {
      "apiVersion": "2015-08-01",
      "name": "[parameters('hostingPlanName')]",
      "type": "Microsoft.Web/serverfarms",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "[parameters('sku')]",
        "capacity": "[parameters('workerSize')]"
      },
      "properties": {
        "name": "[parameters('hostingPlanName')]"
      }
    },


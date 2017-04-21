<properties 
    pageTitle="Solucionar problemas del generador de datos de Azure" 
    description="Obtenga información sobre cómo solucionar problemas relacionados con el generador de datos de Azure." 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/31/2016" 
    ms.author="spelluru"/>

# <a name="troubleshoot-data-factory-issues"></a>Solucionar problemas del generador de datos
Este artículo proporciona consejos para solucionar problemas al usar el generador de datos de Azure. Este artículo no enumeran los posibles problemas al utilizar el servicio pero cubre algunos problemas y sugerencias de solución de problemas generales.   

## <a name="troubleshooting-tips"></a>Consejos para solucionar problemas

### <a name="error-the-subscription-is-not-registered-to-use-namespace-microsoftdatafactory"></a>Error: La suscripción no está registrada para usar el espacio de nombres 'Microsoft.DataFactory'
Si recibe este error, el proveedor de recursos del generador de datos de Azure no registrado en su equipo. Haga lo siguiente: 

1. Iniciar PowerShell Azure. 
2. Inicie sesión en su cuenta de Azure mediante el siguiente comando.
        Inicio de sesión AzureRmAccount 
3. Ejecute el comando siguiente para registrar el proveedor del generador de datos de Azure.
        Registrar AzureRmResourceProvider - ProviderNamespace Microsoft.DataFactory

### <a name="problem-unauthorized-error-when-running-a-data-factory-cmdlet"></a>Problema: No autorizado error cuando se ejecuta un cmdlet de generador de datos
Probablemente no está utilizando la suscripción o una cuenta de Azure hacia la derecha con Azure PowerShell. Use los siguientes cmdlets para seleccionar la cuenta de Azure hacia la derecha y la suscripción para usar con Azure PowerShell. 

1. Inicio de sesión AzureRmAccount - usar el identificador de usuario correcto y la contraseña
2. Get-AzureRmSubscription - ver todas las suscripciones para la cuenta. 
3. Seleccione AzureRmSubscription &lt;nombre de suscripción&gt; -seleccione la suscripción a la derecha. Usar el mismo que se utiliza para crear un generador de datos en el portal de Azure.

### <a name="problem-fail-to-launch-data-management-gateway-express-setup-from-azure-portal"></a>Problema: Error al iniciar la instalación Express de Data Management Gateway desde el portal de Azure
El programa de instalación Express de Data Management Gateway requiere Internet Explorer o un explorador web compatible de Microsoft ClickOnce. Si no se puede iniciar el programa de instalación Express, siga uno de estos procedimientos: 

- Usar Internet Explorer o un explorador web compatible de Microsoft ClickOnce.

    Si está utilizando Chrome, vaya a la [tienda web de Chrome](https://chrome.google.com/webstore/), buscar con la palabra clave "ClickOnce", elija una de las extensiones de ClickOnce e instalarlo. 
    
    Haga lo mismo para Firefox (instalar complemento). Haga clic en Abrir menú en la barra de herramientas (tres líneas horizontales en la esquina superior derecha), haga clic en complementos, buscar con la palabra clave "ClickOnce", elija una de las extensiones de ClickOnce e instalarlo. 

- Utilice el vínculo **Configuración Manual** que se muestra en el mismo módulo en el portal. Use este método para descargar el archivo de instalación y ejecutar de forma manual. Después de la instalación es correcta, verá el cuadro de diálogo Configuración de puerta de enlace de administración de datos. Copie la **clave** de la pantalla portal y usarlo en el Administrador de configuración para registrar manualmente la puerta de enlace con el servicio.  

### <a name="problem-fail-to-connect-to-on-premises-sql-server"></a>Problema: No se pueda conectar a la implementación local de SQL Server 
Inicie el **Administrador de configuración de Data Management Gateway** en el equipo de puerta de enlace y use la pestaña de la **solución de problemas** para probar la conexión a SQL Server desde el equipo de puerta de enlace. Consulte [problemas de puerta de enlace de solución de problemas](data-factory-data-management-gateway.md#troubleshoot-gateway-issues) para obtener sugerencias sobre solución de problemas de conexión o puerta de enlace problemas relacionados con.   
 

### <a name="problem-input-slices-are-in-waiting-state-for-ever"></a>Problema: Sectores de entrada están en estado de espera para siempre

Los sectores podrían estar en estado de **espera** por diversos motivos. Uno de los motivos habituales es que la propiedad **externos** no está establecida en **true**. Cualquier conjunto de datos que se produce fuera del ámbito del generador de datos de Azure debe marcarse con la propiedad **externa** . Esta propiedad indica que los datos son externos y no con copia por cualquier canalizaciones en el generador de datos. Las segmentaciones de datos se marcan como **Listo** cuando los datos están disponibles en la tienda respectiva. 

Vea el siguiente ejemplo para el uso de la propiedad **externa** . Opcionalmente, puede especificar **externalData*** al establecer externos true.

Consulte el artículo de [conjuntos de datos](data-factory-create-datasets.md) para obtener más detalles acerca de esta propiedad.
    
    {
      "name": "CustomerTable",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "MyLinkedService",
        "typeProperties": {
          "folderPath": "MyContainer/MySubFolder/",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ",",
            "rowDelimiter": ";"
          }
        },
        "external": true,
        "availability": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          }
        }
      }
    }

Para resolver el error, agregue la propiedad **externa** y la sección opcional **externalData** a la definición de JSON de la tabla de entrada y vuelva a crear la tabla. 

### <a name="problem-hybrid-copy-operation-fails"></a>Problema: Operación de copia de híbridos se produce un error
Vea [solucionar problemas de la puerta de enlace](data-factory-data-management-gateway.md#troubleshoot-gateway-issues) para conocer los pasos para solucionar problemas con la copia de un almacén de datos local con Data Management Gateway. 

### <a name="problem-on-demand-hdinsight-provisioning-fails"></a>Problema: A petición HDInsight se produce un error de aprovisionamiento
Cuando se usa un servicio de tipo HDInsightOnDemand vinculado, debe especificar un linkedServiceName que apunta a un almacenamiento de blobs de Windows Azure. Servicio de generador de datos utiliza este almacenamiento para almacenar los registros y los archivos auxiliares para el clúster de HDInsight a petición.  A veces el aprovisionamiento de un clúster de HDInsight a petición se produce el siguiente error:

        Failed to create cluster. Exception: Unable to complete the cluster create operation. Operation failed with code '400'. Cluster left behind state: 'Error'. Message: 'StorageAccountNotColocated'.

Este error suele indicar que la ubicación de la cuenta de almacenamiento especificada en el linkedServiceName no está en la misma ubicación de centro de datos donde el aprovisionamiento de HDInsight está ocurriendo. Ejemplo: si el generador de datos que se encuentra en Estados Unidos oeste y el almacenamiento de Azure en Estados Unidos oriental, el aprovisionamiento a petición no funciona en Estados Unidos oeste.

Además, hay una segunda additionalLinkedServiceNames de propiedad JSON donde puede especificar cuentas de almacenamiento adicional en HDInsight a petición. Estas cuentas adicionales de almacenamiento vinculado deben estar en la misma ubicación que el clúster HDInsight o se produce un error con el mismo error.

### <a name="problem-custom-net-activity-fails"></a>Problema: Personalizado se produce un error en la actividad de .NET
Para conocer los pasos detallados, vea [Depurar una canalización con actividad personalizada](data-factory-use-custom-activities.md#debug-the-pipeline) . 

## <a name="use-azure-portal-to-troubleshoot"></a>Usar el portal de Azure para solucionar 

### <a name="using-portal-blades"></a>Uso de portal aspas
Vea la [canalización de Monitor](data-factory-build-your-first-pipeline-using-editor.md#monitor-pipeline) para conocer los pasos. 

### <a name="using-monitor-and-manage-app"></a>Con el Monitor y administrar aplicaciones
Consulte [Monitor y administrar datos fábrica canalizaciones con Monitor y administrar aplicación](data-factory-monitor-manage-app.md) para obtener más detalles. 

## <a name="use-azure-powershell-to-troubleshoot"></a>Usar PowerShell de Azure para solucionar problemas

### <a name="use-azure-powershell-to-troubleshoot-an-error"></a>Usar PowerShell Azure para solucionar un error  
Para obtener información detallada, vea [canalizaciones Monitor datos fábrica con PowerShell de Azure](data-factory-build-your-first-pipeline-using-powershell.md#monitor-pipeline) . 


[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456
[json-scripting-reference]: http://go.microsoft.com/fwlink/?LinkId=516971

[azure-portal]: https://portal.azure.com/

[image-data-factory-troubleshoot-with-error-link]: ./media/data-factory-troubleshoot/DataFactoryWithErrorLink.png

[image-data-factory-troubleshoot-datasets-with-errors-blade]: ./media/data-factory-troubleshoot/DatasetsWithErrorsBlade.png

[image-data-factory-troubleshoot-table-blade-with-problem-slices]: ./media/data-factory-troubleshoot/TableBladeWithProblemSlices.png

[image-data-factory-troubleshoot-activity-run-with-error]: ./media/data-factory-troubleshoot/ActivityRunDetailsWithError.png

[image-data-factory-troubleshoot-dataslice-blade-with-active-runs]: ./media/data-factory-troubleshoot/DataSliceBladeWithActivityRuns.png

[image-data-factory-troubleshoot-walkthrough2-with-errors-link]: ./media/data-factory-troubleshoot/Walkthrough2WithErrorsLink.png

[image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]: ./media/data-factory-troubleshoot/Walkthrough2DataSetsWithErrors.png

[image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]: ./media/data-factory-troubleshoot/Walkthrough2TableProblemSlices.png

[image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]: ./media/data-factory-troubleshoot/Walkthrough2DataSliceActivityRuns.png

[image-data-factory-troubleshoot-activity-run-details]: ./media/data-factory-troubleshoot/Walkthrough2ActivityRunDetails.png
 
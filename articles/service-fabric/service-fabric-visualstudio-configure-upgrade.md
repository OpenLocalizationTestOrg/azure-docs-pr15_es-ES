<properties
   pageTitle="Configurar la actualización de una aplicación de servicio tela | Microsoft Azure"
   description="Obtenga información sobre cómo configurar las opciones para actualizar una aplicación de servicio tela mediante Microsoft Visual Studio."
   services="service-fabric"
   documentationCenter="na"
   authors="cawaMS"
   manager="paulyuk"
   editor="tglee" />
<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="07/29/2016"
   ms.author="cawa" />

# <a name="configure-the-upgrade-of-a-service-fabric-application-in-visual-studio"></a>Configurar la actualización de una aplicación de servicio tela en Visual Studio

Visual Studio tools para Azure servicio tela proporcionan soporte de actualización para la publicación para clústeres locales o remotas. Hay dos ventajas de actualizar la aplicación a una versión más reciente en lugar de reemplazar la aplicación durante las pruebas y depuración:

- Datos de la aplicación no se perderán durante la actualización.
- Disponibilidad permanece alto para no habrá ninguna interrupción del servicio durante la actualización, si hay que suficiente instancias del servicio se extienden desde los dominios de actualización.

Pruebas se pueden ejecutar en una aplicación mientras se está actualizando.

## <a name="parameters-needed-to-upgrade"></a>Parámetros necesarios para actualizar

Puede elegir entre dos tipos de implementación: normal o actualización. Una implementación normal borra cualquier información de implementación anterior y datos en el clúster, mientras conserva una implementación de actualización. Al actualizar una aplicación de servicio tela en Visual Studio, debe proporcionar directivas de verificación de salud y parámetros de actualización de la aplicación. Parámetros de actualización de la aplicación ayudan a controlar la actualización, mientras que las directivas de comprobación de estado determinan si la actualización se realizó correctamente. Consulte [actualización de la aplicación de servicio tela: actualizar parámetros](service-fabric-application-upgrade-parameters.md) para obtener más detalles.

Existen tres modos de actualización: *supervisadas*, *UnmonitoredAuto*y *UnmonitoredManual*.

  - Una actualización de la supervisadas automatiza la actualización y la comprobación del estado de aplicación.

  - Una actualización UnmonitoredAuto automatiza la actualización, pero omite la comprobación de estado de la aplicación.

  - Al realizar una actualización de UnmonitoredManual, debe actualizar manualmente cada dominio de actualización.

Cada modo actualización requiere diferentes conjuntos de parámetros. Vea [parámetros de actualización de la aplicación](service-fabric-application-upgrade-parameters.md) para obtener más información sobre las opciones de actualización disponibles.

## <a name="upgrade-a-service-fabric-application-in-visual-studio"></a>Actualizar una aplicación de servicio tela en Visual Studio

Si está usando las herramientas de tela de servicio de Visual Studio para actualizar una aplicación de servicio tela, puede especificar un proceso de publicación para que sea una actualización en lugar de una implementación normal, active la casilla de verificación **actualizar la aplicación** .

### <a name="to-configure-the-upgrade-parameters"></a>Para configurar los parámetros de actualización

1. Haga clic en el botón **configuración** junto a la casilla de verificación. Aparece el cuadro de diálogo **Editar parámetros de actualización** . El cuadro de diálogo **Editar parámetros de actualización** es compatible con los modos de actualización supervisadas, UnmonitoredAuto y UnmonitoredManual.

2. Seleccione el modo de actualización que desea usar y, a continuación, rellene la cuadrícula de parámetros.

    Cada parámetro tiene valores predeterminados. El parámetro opcional *DefaultServiceTypeHealthPolicy* le lleva a una entrada de tabla hash. Aquí tiene un ejemplo de formato de entrada de la tabla hash para *DefaultServiceTypeHealthPolicy*:

    ```
    @{ ConsiderWarningAsError = "false"; MaxPercentUnhealthyDeployedApplications = 0; MaxPercentUnhealthyServices = 0; MaxPercentUnhealthyPartitionsPerService = 0; MaxPercentUnhealthyReplicasPerPartition = 0 }
    ```

    *ServiceTypeHealthPolicyMap* es otra parámetros opcionales que se lleve a una entrada de tabla de hash en el siguiente formato:

    ```    
    @ {"ServiceTypeName" : "MaxPercentUnhealthyPartitionsPerService,MaxPercentUnhealthyReplicasPerPartition,MaxPercentUnhealthyServices"}
    ```

    Este es un ejemplo de la vida real:

    ```
    @{ "ServiceTypeName01" = "5,10,5"; "ServiceTypeName02" = "5,5,5" }
    ```

3. Si selecciona el modo de actualización de UnmonitoredManual, debe iniciar manualmente una consola de PowerShell para continuar y finalizar el proceso de actualización. Consulte [actualización de la aplicación de servicio tela: temas avanzados](service-fabric-application-upgrade-advanced.md) para obtener información sobre la actualización manual cómo funciona.

## <a name="upgrade-an-application-by-using-powershell"></a>Actualizar una aplicación con PowerShell

Puede usar cmdlets de PowerShell para actualizar una aplicación de servicio tela. Para obtener información detallada, vea [Actualizar tutorial de aplicación de servicio tela](service-fabric-application-upgrade-tutorial.md) y [ServiceFabricApplicationUpgrade inicio](https://msdn.microsoft.com/library/mt125975.aspx) .

## <a name="specify-a-health-check-policy-in-the-application-manifest-file"></a>Especificar una directiva de comprobación de estado en el archivo de manifiestos de aplicación

Todos los servicios en una aplicación de servicio tela pueden tener sus propios parámetros de directiva de mantenimiento que reemplacen a los valores predeterminados. Puede proporcionar estos valores de parámetro en el archivo de manifiestos de aplicación.

En el ejemplo siguiente se muestra cómo aplicar una directiva de comprobación de estado único para cada servicio del manifiesto de aplicación.

```
<Policies>
    <HealthPolicy ConsiderWarningAsError="false" MaxPercentUnhealthyDeployedApplications="20">
        <DefaultServiceTypeHealthPolicy MaxPercentUnhealthyServices="20"               
                MaxPercentUnhealthyPartitionsPerService="20"
                MaxPercentUnhealthyReplicasPerPartition="20" />
        <ServiceTypeHealthPolicy ServiceTypeName="ServiceTypeName1"
                MaxPercentUnhealthyServices="20"
                MaxPercentUnhealthyPartitionsPerService="20"
                MaxPercentUnhealthyReplicasPerPartition="20" />      
    </HealthPolicy>
</Policies>
```
## <a name="next-steps"></a>Pasos siguientes
Para obtener más información sobre cómo implementar una aplicación, vea [implementar una aplicación existente en tela de servicio de Azure](service-fabric-deploy-existing-app.md).

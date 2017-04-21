<properties
   pageTitle="Eliminar un clúster de Azure y sus recursos | Microsoft Azure"
   description="Obtenga información sobre cómo eliminar completamente una estructura de servicio clúster de eliminación de grupo de recursos que contenga el clúster o eliminando selectivamente recursos."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/09/2016"
   ms.author="chackdan"/>

# <a name="delete-a-service-fabric-cluster-on-azure-and-the-resources-it-uses"></a>Eliminar un clúster de servicio tela en Azure y los recursos que utiliza

Un clúster de tela de servicio se compone de otros recursos de Azure además del recurso de clúster propiamente dicho. Para eliminar completamente un clúster de tela de servicio, de que también debe eliminar todos los recursos que se trate.
Tiene dos opciones:, elimine el grupo de recursos que se encuentra en el clúster (que elimina el recurso de clúster y cualquier otro recurso en el grupo de recursos) o eliminar específicamente el recurso y asociados recursos (pero no otros recursos en el grupo de recursos).

>[AZURE.NOTE] Eliminar el recurso de clúster **no** eliminar todos los otros recursos que está formado por el clúster de servicio tela.

## <a name="delete-the-entire-resource-group-rg-that-the-service-fabric-cluster-is-in"></a>Eliminar el grupo de recursos completa (c) que el clúster de tela de servicio

Esta es la manera más sencilla de asegurarse de que elimina todos los recursos asociados con el clúster, incluido el grupo de recursos. Puede eliminar el grupo de recursos con PowerShell o a través del portal de Azure. Si el grupo de recursos tiene recursos que no están relacionados con el clúster de tela de servicio, puede eliminar recursos específicos.

### <a name="delete-the-resource-group-using-azure-powershell"></a>Eliminar el grupo de recursos con PowerShell de Azure

También puede eliminar el grupo de recursos, ejecute los siguientes cmdlets de PowerShell de Azure. Asegúrese de que Azure PowerShell 1.0 o posterior instalado en su equipo. Si no ha hecho antes, siga los pasos descritos en [cómo instalar y configurar Azure PowerShell.](../powershell-install-configure.md)

Abra una ventana de PowerShell y ejecute los siguientes cmdlets PS:

```powershell
Login-AzureRmAccount

Remove-AzureRmResourceGroup -Name <name of ResouceGroup> -Force
```

Recibirá un mensaje para confirmar la eliminación si no ha utilizado el *-Forzar* opción. En la confirmación se eliminan el c y todos los recursos que contiene.

### <a name="delete-a-resource-group-in-the-azure-portal"></a>Eliminar un grupo de recursos en el portal de Azure  

1. Inicie sesión en el [portal de Azure](https://portal.azure.com).
2. Desplácese hasta el clúster de tela de servicio que desea eliminar.
3. Haga clic en el nombre del grupo de recursos en la página de elementos esenciales de clúster.
4. Se abrirá la página de **Elementos esenciales de grupo de recursos** .
5. Haga clic en **Eliminar**.
6. Siga las instrucciones de esa página para completar la eliminación del grupo de recursos.

![Eliminar grupo de recursos][ResourceGroupDelete]


## <a name="delete-the-cluster-resource-and-the-resources-it-uses-but-not-other-resources-in-the-resource-group"></a>Eliminar el recurso de clúster y los recursos que utiliza, pero no otros recursos en el grupo de recursos

Si el grupo de recursos tiene solo los recursos relacionados con el clúster de tela de servicio que desea eliminar, es más fácil eliminar el grupo de recursos completa. Si desea eliminar de forma selectiva los recursos uno a uno en el grupo de recursos, a continuación, siga estos pasos.

Si implementa el clúster con el portal o con una de las plantillas de administrador de recursos de servicio tela desde la Galería de plantillas, todos los recursos que usa el clúster están etiquetados con las siguientes dos etiquetas. Puede usarlos para decidir qué recursos que desea eliminar.

***Etiqueta #1:*** Clave = nombreDeClúster, valor = 'nombre del clúster'

***Etiqueta #2:*** Clave = resourceName, valor = ServiceFabric

### <a name="delete-specific-resources-in-the-azure-portal"></a>Eliminar recursos específicos en el portal de Azure

1. Inicie sesión en el [portal de Azure](https://portal.azure.com).
2. Desplácese hasta el clúster de tela de servicio que desea eliminar.
3. Vaya a **todas las opciones** en el módulo de essentials.
4. Haga clic en **etiquetas** en la **Administración de recursos** en el módulo de configuración.
5. Haga clic en una de las **etiquetas** en la hoja de etiquetas para obtener una lista de todos los recursos con esa etiqueta.

    ![Etiquetas de recursos][ResourceTags]

6. Una vez que la lista de recursos con etiquetas, haga clic en cada uno de los recursos y elimínelas.

    ![Etiquetado recursos][TaggedResources]

### <a name="delete-the-resources-using-azure-powershell"></a>Eliminar los recursos con PowerShell de Azure

Puede eliminar los recursos uno a uno, ejecute los siguientes cmdlets de PowerShell de Azure. Asegúrese de que Azure PowerShell 1.0 o posterior instalado en su equipo. Si no ha hecho antes, siga los pasos descritos en [cómo instalar y configurar Azure PowerShell.](../powershell-install-configure.md)

Abra una ventana de PowerShell y ejecute los siguientes cmdlets PS:

```powershell
Login-AzureRmAccount
```
Para cada uno de los recursos que desea eliminar, ejecute lo siguiente:

```powershell
Remove-AzureRmResource -ResourceName "<name of the Resource>" -ResourceType "<Resource Type>" -ResourceGroupName "<name of the resource group>" -Force
```

Para eliminar el recurso de clúster, ejecute lo siguiente:

```powershell
Remove-AzureRmResource -ResourceName "<name of the Resource>" -ResourceType "Microsoft.ServiceFabric/clusters" -ResourceGroupName "<name of the resource group>" -Force
```

## <a name="next-steps"></a>Pasos siguientes
Lea lo siguiente también obtener información sobre la actualización de un clúster y partición de servicios:

- [Obtenga más información sobre las actualizaciones de clúster](service-fabric-cluster-upgrade.md)
- [Obtenga más información sobre particiones services con estado para escala máxima](service-fabric-concepts-partitioning.md)


<!--Image references-->
[ResourceGroupDelete]: ./media/service-fabric-cluster-delete/ResourceGroupDelete.PNG

[ResourceTags]: ./media/service-fabric-cluster-delete/ResourceTags.png

[TaggedResources]: ./media/service-fabric-cluster-delete/TaggedResources.PNG

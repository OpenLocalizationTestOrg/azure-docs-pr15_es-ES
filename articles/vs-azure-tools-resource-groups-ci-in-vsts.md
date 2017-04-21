<properties
    pageTitle="Integración continua en VS Team Services utilizando proyectos de grupo de recursos de Azure | Microsoft Azure"
    description="Describe cómo configurar la integración continua en Visual Studio Team Services mediante proyectos de implementación de grupo de recursos de Azure en Visual Studio."
    services="visual-studio-online"
    documentationCenter="na"
    authors="mlearned"
    manager="erickson-doug"
    editor="" />

 <tags
    ms.service="azure-resource-manager"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="08/01/2016"
    ms.author="mlearned" />

# <a name="continuous-integration-in-visual-studio-team-services-using-azure-resource-group-deployment-projects"></a>Integración continua en Visual Studio Team Services con los proyectos de implementación de grupo de recursos de Azure

Para implementar una plantilla de Azure, debe realizar las tareas para ir a través de las diversas fases: diseñar, prueba, copiar a Azure (también denominado "Ensayo") y una plantilla de implementar.  Hay dos maneras de hacerlo en Visual Studio Team Services (VS Team Services). Ambos métodos proporcionan los mismos resultados, así que seleccione la opción que mejor se adapte a su flujo de trabajo.

-   Agregar un paso a paso para la definición de compilación que se ejecuta la secuencia de comandos de PowerShell que se incluye en el proyecto de implementación de grupo de recursos de Azure (implementar AzureResourceGroup.ps1). La secuencia de comandos copia artefactos y, a continuación, implementa la plantilla.
-   Agregar que varias VS Team Services pasos de generación, cada una de ellas realizando una tarea de fase.

En este artículo se muestra cómo usar la primera opción (utilice una definición de compilación para ejecutar la secuencia de comandos de PowerShell). Una ventaja de esta opción es que la secuencia de comandos que utilizan los desarrolladores de Visual Studio es la misma secuencia de comandos que se usa servicios de grupo de VS. Este procedimiento supone que ya tiene un proyecto de implementación de Visual Studio incorporan VS Team Services.

## <a name="copy-artifacts-to-azure"></a>Copiar artefactos en Azure 

Independientemente del escenario, si tiene cualquier artefacto que es necesarios para la implementación de plantilla, debe dar acceso de administrador de recursos de Azure. Estos artefactos pueden incluir archivos como:

-   Plantillas anidadas
-   Secuencias de comandos de configuración y DSC
-   Archivos binarios de aplicación

### <a name="nested-templates-and-configuration-scripts"></a>Las plantillas anidadas y secuencias de comandos de configuración
Al utilizar las plantillas de Visual Studio (o integrado con fragmentos de código de Visual Studio), la secuencia de comandos de PowerShell fases no sólo los artefactos, también parametriza el URI de los recursos para implementaciones diferentes. La secuencia de comandos copia los artefactos en un contenedor seguro en Azure, crea un token de SA para ese contenedor y, a continuación, pasa esa información a la implementación de la plantilla. Consulte [crear una plantilla de implementación](https://msdn.microsoft.com/library/azure/dn790564.aspx) para obtener más información acerca de las plantillas anidadas.

## <a name="set-up-continuous-deployment-in-vs-team-services"></a>Configurar la implementación continua en VS Team Services

Para llamar a la secuencia de comandos de PowerShell en VS Team Services, deberá actualizar la definición de compilación. En resumen, los pasos son: 

1.  Editar la definición de compilación.
1.  Configurar la autorización de Azure en VS Team Services.
1.  Agregar un paso de compilación de PowerShell de Azure que hace referencia a la secuencia de comandos de PowerShell en el proyecto de implementación de grupo de recursos de Azure.
1.  Establezca el valor del parámetro *- ArtifactsStagingDirectory* para trabajar con un proyecto generado en VS Team Services.

### <a name="detailed-walkthrough"></a>Tutorial detallado

Los siguientes pasos le guiará por los pasos necesarios para configurar la implementación continua en VS Team Services 

1.  Editar la definición de compilación de VS Team Services y agregue un paso de compilación de PowerShell de Azure. Elija la definición de compilación en la categoría de **las definiciones de compilación** y, a continuación, elija **Editar** vínculo.

    ![][0]

1.  Agregar un paso de compilación de **Azure PowerShell** nuevo a la definición de compilación y, a continuación, elija **Agregar generar paso...** botón.

    ![][1]

1.  Elija la categoría de **tarea de implementación** , seleccione la tarea de **PowerShell de Azure** y, a continuación, elija el botón **Agregar** .

    ![][2]

1.  Elija el paso de compilación de **PowerShell de Azure** y luego rellene sus valores.

    1.  Si ya tiene un extremo de servicio de Azure agregado a VS Team Services, seleccione la suscripción en el cuadro de lista desplegable de **Azure suscripción** y, a continuación, vaya a la sección siguiente. 

        Si no tiene un extremo de servicio de Azure en VS Team Services, deberá agregar una. Esta subsección le lleva a través del proceso. Si su cuenta de Azure usa una cuenta de Microsoft (como Hotmail), debe realizar los pasos siguientes para obtener una autenticación Principal del servicio.

    1.  Elija el vínculo **Administrar** junto a la **Suscripción de Azure** desplegable del cuadro de lista.

        ![][3]

    1. Elija **Azure** en el cuadro de lista desplegable de **Nuevo extremo de servicio** .

        ![][4]

    1.  En el cuadro de diálogo **Agregar Azure suscripción** , seleccione la opción de **Servicio Principal** .

        ![][5]

    1.  Agregar información de su suscripción de Azure en el cuadro de diálogo **Agregar suscripción de Azure** . Debe proporcionar los siguientes elementos:
        -   Id. de suscripción
        -   Nombre de suscripción
        -   Identificador Principal del servicio
        -   Clave Principal de servicio
        -   Id. de inquilinos

    1.  Agregue un nombre de su elección en el cuadro Nombre de **suscripción** . Este valor se mostrará más adelante en la lista en VS Team Services desplegable de **Azure suscripción** . 

    1.  Si no conoce el identificador de suscripción de Azure, puede usar uno de los siguientes comandos obtenerlo.
        
        Para los scripts de PowerShell, use:

        `Get-AzureRmSubscription`

        Para CLI de Azure, use:

        `azure account show`
    

    1.  Para obtener un identificador de servicio Principal, clave Principal de servicio e ID de inquilinos, siguen el procedimiento de la [aplicación de crear Active Directory y servicio principal con portal](resource-group-create-service-principal-portal.md) o [autenticar a un principal de servicio con el Administrador de recursos de Azure](resource-group-authenticate-service-principal.md).

    1.  Agregue los valores de ID de servicio Principal, clave Principal de servicio e ID de inquilino al cuadro de diálogo **Agregar Azure suscripción** y, a continuación, elija el botón **Aceptar** .

        Ahora tiene una entidad de seguridad válida de servicio se utiliza para ejecutar la secuencia de comandos de PowerShell de Azure.

1.  Editar la definición de compilación y elija el paso de compilación de **PowerShell de Azure** . Seleccione la suscripción en el cuadro de lista desplegable de **Azure suscripción** . (Si no aparece la suscripción, seleccione el botón **Actualizar** siguiente el vínculo **Administrar** .) 

    ![][8]

1.  Proporcione una ruta de acceso a la secuencia de comandos de PowerShell de implementar AzureResourceGroup.ps1. Para ello, elija el botón de puntos suspensivos (...) junto al cuadro **Ruta de acceso de secuencia de comandos** , vaya a la secuencia de comandos de PowerShell de implementar AzureResourceGroup.ps1 en la carpeta de **Scripts** de su proyecto, selecciónelo y, a continuación, elija el botón **Aceptar** . 

    ![][9]

1. Después de seleccionar la secuencia de comandos, actualice la ruta de acceso a la secuencia de comandos para que se ejecuta desde la Build.StagingDirectory (el mismo directorio que *ArtifactsLocation* se establece en). Puede hacerlo agregando "$(Build.StagingDirectory)/"al principio de la ruta de acceso.

    ![][10]

1.  En el cuadro **Argumentos de secuencia de comandos** , escriba los siguientes parámetros (en una sola línea). Cuando se ejecuta la secuencia de comandos en Visual Studio, puede ver cómo VS usa los parámetros en la ventana de **resultados** . Puede utilizar como punto de partida para configurar los valores de parámetro en el paso de compilación.

  	| Parámetro | Descripción|
  	|---|---|
  	| -ResourceGroupLocation           | El valor de la ubicación geográfica en el grupo de recursos se encuentra, como **eastus** o **' EE oriental '**. (Agregar comillas simples si hay un espacio en el nombre). Para obtener más información, vea [Regiones de Azure](https://azure.microsoft.com/en-us/regions/) .|                                                                                                                                                                                                                              |
  	| -ResourceGroupName               | El nombre del grupo de recursos utilizado para esta implementación.|                                                                                                                                                                                                                                                                                                                                                                                                                |
  	| -UploadArtifacts                 | Este parámetro, si está presente, especifica que artefactos necesitan cargarse en Azure del sistema local. Solo es necesario configurar este modificador si su implementación de plantilla requiere artefactos adicionales que desee fase mediante la secuencia de comandos de PowerShell (como secuencias de comandos de configuración o las plantillas anidadas).                                                                                                                                                                 |
  	| -StorageAccountName              | El nombre de la cuenta de almacenamiento utilizada para artefactos de la etapa de esta implementación. Este parámetro es necesario únicamente si está copiando artefactos en Azure. Esta cuenta de almacenamiento no se creará automáticamente por la implementación, ya debe existir.|                                                                                                                                                                                                                     |
  	| -StorageAccountResourceGroupName | El nombre del grupo de recursos asociado con la cuenta de almacenamiento. Este parámetro es necesario únicamente si está copiando artefactos en Azure.|                                                                                                                                                                                                                                                                                                                               |
  	| -TemplateFile                    | La ruta de acceso al archivo de plantilla en el proyecto de implementación de grupo de recursos de Azure. Para mejorar la flexibilidad, utilice una ruta de acceso para este parámetro relativa a la ubicación de la secuencia de comandos de PowerShell en lugar de una ruta absoluta.|
  	| -TemplateParametersFile          | La ruta de acceso al archivo de parámetros en el proyecto de implementación de grupo de recursos de Azure. Para mejorar la flexibilidad, utilice una ruta de acceso para este parámetro relativa a la ubicación de la secuencia de comandos de PowerShell en lugar de una ruta absoluta.|
  	| -ArtifactStagingDirectory        | Este parámetro permite el script saber la carpeta desde donde se deben copiar los archivos del proyecto binario de PowerShell. Este valor reemplaza el valor predeterminado que se usa la secuencia de comandos de PowerShell. Para usar VS Team Services, establezca el valor en: $(Build.StagingDirectory) - ArtifactStagingDirectory                                                                                                                                                                                              |

    Este es un ejemplo de argumentos de secuencia de comandos (línea rompe para mejorar la legibilidad):

    ``` 
    -ResourceGroupName 'MyGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\azuredeploy.json' 
    -TemplateParametersFile '..\templates\azuredeploy.parameters.json' -UploadArtifacts -StorageAccountName 'mystorageacct' 
    –StorageAccountResourceGroupName 'Default-Storage-EastUS' -ArtifactStagingDirectory '$(Build.StagingDirectory)' 
    ```

    Cuando haya terminado, el cuadro **Argumentos de secuencia de comandos** debería parecerse al siguiente.

    ![][11]

1.  Una vez haya agregado que todos los elementos necesarios a Azure PowerShell generación paso, elija la **cola** generar botón para generar el proyecto. La pantalla **crear** muestra el resultado de la secuencia de comandos de PowerShell.

## <a name="next-steps"></a>Pasos siguientes

Lea la [información general del Administrador de recursos de Azure](azure-resource-manager/resource-group-overview.md) para obtener más información sobre el Administrador de recursos de Azure y grupos de recursos de Azure.


[0]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough1.png
[1]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough2.png
[2]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough3.png
[3]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough4.png
[4]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough5.png
[5]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough6.png
[8]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough9.png
[9]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough10.png
[10]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough11b.png
[11]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough12.png

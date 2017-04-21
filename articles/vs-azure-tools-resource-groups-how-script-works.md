<properties
    pageTitle="Información general sobre el script de implementación de proyecto de grupo de recursos de Azure | Microsoft Azure"
    description="Describe cómo funciona la secuencia de comandos de PowerShell en el proyecto de implementación de grupo de recursos de Azure."
    services="visual-studio-online"
    documentationCenter="na"
    authors="tfitzmac"
    manager="timlt"
    editor="" />

 <tags
    ms.service="azure-resource-manager"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="07/26/2016"
    ms.author="tomfitz" />

# <a name="overview-of-the-azure-resource-group-project-deployment-script"></a>Información general sobre el script de implementación de proyecto de grupo de recursos de Azure

Implementación de grupo de recursos de Azure proyectos ayuda fase e implemente archivos y otros defectos Azure. Cuando se crea un proyecto de implementación de administrador de recursos de Azure en Visual Studio, se agrega un script de PowerShell denominado **Implementar AzureResourceGroup.ps1** al proyecto. Este tema proporciona información detallada sobre lo que hace esta secuencia de comandos y cómo ejecutarlo dentro y fuera de Visual Studio.

## <a name="what-does-the-script-do"></a>¿Qué hace la secuencia de comandos?

La secuencia de comandos de implementar AzureResourceGroup.ps1 hace dos cosas importantes para el flujo de trabajo de implementación.

- Cargar todos los archivos o elementos necesarios para la implementación de plantilla
- Implementar la plantilla

La primera parte de la secuencia de comandos carga los archivos y artefactos para su implementación y el último cmdlet en el script de implementar la plantilla. Por ejemplo, si una máquina virtual debe estar configurado con una secuencia de comandos, el script de implementación primero segura carga la secuencia de comandos de configuración para una cuenta de almacenamiento de Azure. Esto hace que esté disponible para el Administrador de recursos de Azure para configurar la máquina virtual durante el aprovisionamiento.

Dado que no todas las implementaciones de plantilla necesitan tener artefactos adicionales que debe cargarse, se evalúa un parámetro del modificador denominado *uploadArtifacts* . Si cualquier artefacto debe cargarse, establezca el modificador *uploadArtifacts* al llamar a la secuencia de comandos. Tenga en cuenta que no es necesario que va a cargar el archivo de plantilla principal y el archivo de parámetros. Solo otros archivos, como las secuencias de comandos de configuración, anidar plantillas de implementación y tienen que cargar archivos de la aplicación.

## <a name="detailed-script-description"></a>Descripción detallada de la secuencia de comandos

A continuación se muestra una descripción de qué seleccionar secciones de la no script de PowerShell de Azure implementar AzureResourceGroup.ps1.

>[AZURE.NOTE] Describe la versión 1.0 de la secuencia de comandos de implementar AzureResourceGroup.ps1.

1.  Declarar parámetros necesarios para el proyecto de implementación de administrador de recursos de Azure. Algunos parámetros tienen valores predeterminados que se establecieron cuando se creó el proyecto. Puede cambiar los valores predeterminados de la secuencia de comandos o agregar distintos valores de parámetro antes de ejecutar la secuencia de comandos.

    ```
    Param(
      [string] [Parameter(Mandatory=$true)] $ResourceGroupLocation,
      [string] $ResourceGroupName = 'AzureResourceGroup1',
      [switch] $UploadArtifacts,
      [string] $StorageAccountName,
      [string] $StorageAccountResourceGroupName,
      [string] $StorageContainerName = $ResourceGroupName.ToLowerInvariant() + '-stageartifacts',
      [string] $TemplateFile = '..\Templates\azuredeploy.json',
      [string] $TemplateParametersFile = '..\Templates\azuredeploy.parameters.json',
      [string] $ArtifactStagingDirectory = '..\bin\Debug\staging',
      [string] $AzCopyPath = '..\Tools\AzCopy.exe',
      [string] $DSCSourceFolder = '..\DSC'
    )
    ```

  	|Parámetro|Descripción|
  	|---|---|
  	|$ResourceGroupLocation|La ubicación de centro de datos o la región del grupo de recursos, como **US oeste** o de **Asia oriental**.|
  	|$ResourceGroupName|El nombre del grupo de recursos de Azure.|
  	|$UploadArtifacts|Valor binario que indica si debe cargarse en Azure desde el sistema de artefactos.|
  	|$StorageAccountName|El nombre de su cuenta de almacenamiento de Azure donde se cargan los artefactos.|
  	|$StorageAccountResourceGroupName|El nombre del grupo de recursos de Azure que contiene la cuenta de almacenamiento.|
  	|$StorageContainerName|El nombre del contenedor de almacenamiento usado para cargar artefactos.|
  	|$TemplateFile|La ruta de acceso al archivo de implementación (`<app name>.json`) en el proyecto del grupo de recursos de Azure.|
  	|$TemplateParametersFile|La ruta de acceso al archivo de parámetros (`<app name>.parameters.json`) en el proyecto del grupo de recursos de Azure.|
  	|$ArtifactStagingDirectory|La ruta de acceso en el sistema donde artefactos localmente cargados, incluida la carpeta raíz de secuencia de comandos de PowerShell. Esta ruta puede ser absoluta o relativa a la ubicación de la secuencia de comandos.|
  	|$AzCopyPath|La ruta de acceso donde la herramienta de AzCopy.exe copia sus archivos .zip, incluyendo la carpeta raíz de secuencia de comandos de PowerShell. Esta ruta puede ser absoluta o relativa a la ubicación de la secuencia de comandos.|
  	|$DSCSourceFolder|La ruta de acceso a la carpeta de origen DSC (configuración de estado deseado), incluida la carpeta raíz de secuencia de comandos de PowerShell. Esta ruta puede ser absoluta o relativa a la ubicación de la secuencia de comandos. Vea [Introducción a la extensión de Azure PowerShell DSC (configuración de estado deseado)](http://blogs.msdn.com/b/powershell/archive/2014/08/07/introducing-the-azure-powershell-dsc-desired-state-configuration-extension.aspx), si procede, para obtener más información.|

1.  Compruebe si necesitan artefactos cargarse en Azure. Si no es así, vaya al paso 11. En caso contrario, realice los pasos siguientes.

1.  Convertir las variables con rutas de acceso relativas a rutas de acceso absolutas. Por ejemplo, cambiar una ruta de acceso como `..\Tools\AzCopy.exe` a `C:\YourFolder\Tools\AzCopy.exe`. Además, inicializar las variables *ArtifactsLocationName* y *ArtifactsLocationSasTokenName* nulo. *ArtifactsLocation* y *SaSToken* pueden parámetros a la plantilla. Si sus valores son nulos después de leer el archivo de parámetros, la secuencia de comandos genera valores para ellos.

    Las herramientas de Azure usar el parámetro valores *_artifactsLocation* y *_artifactsLocationSasToken* en la plantilla para administrar artefactos. Si la secuencia de comandos de PowerShell encuentra parámetros con los nombres, pero no proporciona los valores de parámetro, la secuencia de comandos los artefactos las cargas y devuelve los valores apropiados para los parámetros. A continuación, pasa al cmdlet a través de `@OptionsParameters`.

  	|Variable|Descripción|
  	|---|---|
  	|ArtifactsLocationName|La ruta de acceso donde se encuentran los artefactos Azure.|
  	|ArtifactsLocationSasTokenName|El nombre de token de SA (firma de acceso compartido) que se utiliza la secuencia de comandos para autenticar al Bus de servicio. Para obtener más información, vea [Autenticación de firma de acceso compartida con Bus de servicio](service-bus-shared-access-signature-authentication.md) .|

    ```
    if ($UploadArtifacts) {
    # Convert relative paths to absolute paths if needed
    $AzCopyPath = [System.IO.Path]::Combine($PSScriptRoot, $AzCopyPath)
    $ArtifactStagingDirectory = [System.IO.Path]::Combine($PSScriptRoot, $ArtifactStagingDirectory)
    $DSCSourceFolder = [System.IO.Path]::Combine($PSScriptRoot, $DSCSourceFolder)

    Set-Variable ArtifactsLocationName '_artifactsLocation' -Option ReadOnly
    Set-Variable ArtifactsLocationSasTokenName '_artifactsLocationSasToken' -Option ReadOnly

    $OptionalParameters.Add($ArtifactsLocationName, $null)
    $OptionalParameters.Add($ArtifactsLocationSasTokenName, $null)
    ```

1.  Esta sección comprueba si la <app name>. parameters.json el archivo (denominado "archivo de parámetros") tiene un nodo primario **parámetros** con nombre (en el `else` bloque). En caso contrario, tiene ningún nodo principal. Cualquier formato es aceptable.
    
    ```
    if ($JsonParameters -eq $null) {
            $JsonParameters = $JsonContent
        }
        else {
            $JsonParameters = $JsonContent.parameters
        }
    ```

1.  Recorrer la colección de parámetros JSON. Si se ha asignado un valor de parámetro para *_artifactsLocation* o *_artifactsLocationSasToken*, a continuación, establezca la variable *$OptionalParameters* con esos valores. Esto impide la secuencia de comandos accidentalmente sobrescriba cualquier valor de parámetro que proporcione.

    ```
    $JsonParameters | Get-Member -Type NoteProperty | ForEach-Object {
        $ParameterValue = $JsonParameters | Select-Object -ExpandProperty $_.Name

        if ($_.Name -eq $ArtifactsLocationName -or $_.Name -eq $ArtifactsLocationSasTokenName) {
            $OptionalParameters[$_.Name] = $ParameterValue.value
        }
    }
    ```

1.  Obtener la clave de cuenta de almacenamiento y el contexto para el recurso de la cuenta de almacenamiento utilizado para mantener los artefactos para su implementación.

    ```
    $StorageAccountKey = (Get-AzureRMStorageAccountKey -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Key1

    $StorageAccountContext = (Get-AzureRmStorageAccount -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Context
    ```

1.  Si está usando DSC de PowerShell para configurar una máquina virtual, la extensión DSC requiere los artefactos en un solo archivo zip. Por lo tanto, cree un archivo de almacenamiento .zip para la configuración de DSC. Para ello, compruebe si existe $DSCSourceFolder. Si existe una configuración de DSC, quitarlo y, a continuación, crear un nuevo archivo comprimido denominado dsc.zip.

    ```
    # Create DSC configuration archive
    if (Test-Path $DSCSourceFolder) {
    Add-Type -Assembly System.IO.Compression.FileSystem
        $ArchiveFile = Join-Path $ArtifactStagingDirectory "dsc.zip"
        Remove-Item -Path $ArchiveFile -ErrorAction SilentlyContinue
        [System.IO.Compression.ZipFile]::CreateFromDirectory($DSCSourceFolder, $ArchiveFile)
    }
    ```

1.  Si no se proporciona ninguna ruta de acceso de artefactos Azure en el archivo de parámetros, establezca una ruta de acceso de la secuencia de comandos de PowerShell usar al cargar artefactos. Para ello, cree una ruta de acceso mediante una combinación de ruta de acceso de extremo de la cuenta de almacenamiento y el nombre de contenedor de almacenamiento. A continuación, actualice el archivo de parámetros con esta ruta nueva.

    ```
    # Generate the value for artifacts location if it is not provided in the parameter file
    $ArtifactsLocation = $OptionalParameters[$ArtifactsLocationName]
    if ($ArtifactsLocation -eq $null) {
        $ArtifactsLocation = $StorageAccountContext.BlobEndPoint + $StorageContainerName
        $OptionalParameters[$ArtifactsLocationName] = $ArtifactsLocation
    }
    ```

1.  Use la utilidad **AzCopy** (incluida en la carpeta de **Herramientas** de su proyecto de implementación de grupo de recursos de Azure) para copiar los archivos de la ruta de acceso de colocación de almacenamiento local en su cuenta de Azure almacenamiento en línea. Si se produce un error en este paso, salga de la secuencia de comandos ya que la implementación no es probable que lleve a cabo correctamente sin los artefactos necesarios.

    ```
    # Use AzCopy to copy files from the local storage drop path to the storage account container
    & $AzCopyPath """$ArtifactStagingDirectory""", $ArtifactsLocation, "/DestKey:$StorageAccountKey", "/S", "/Y", "/Z:$env:LocalAppData\Microsoft\Azure\AzCopy\$ResourceGroupName"
    if ($LASTEXITCODE -ne 0) { return }
    ```

1.  Si no se proporciona un símbolo de SA para la ubicación de artefactos en el archivo de parámetros, crear uno para proporcionar acceso de solo lectura temporal para el contenedor de almacenamiento en línea. A continuación, pase ese token SA en la línea de comandos como "optionalParameter". Tenga en cuenta que los parámetros que se pasan en la línea de comandos tiene prioridad sobre valores proporcionados en el archivo de parámetros.

    ```
    # Generate the value for artifacts location SAS token if it is not provided in the parameter file
    $ArtifactsLocationSasToken = $OptionalParameters[$ArtifactsLocationSasTokenName]
    if ($ArtifactsLocationSasToken -eq $null) {
       # Create a SAS token for the storage container - this gives temporary read-only access to the container
       $ArtifactsLocationSasToken = New-AzureStorageContainerSASToken -Container $StorageContainerName -Context $StorageAccountContext -Permission r -ExpiryTime (Get-Date).AddHours(4)
       $ArtifactsLocationSasToken = ConvertTo-SecureString $ArtifactsLocationSasToken -AsPlainText -Force
       $OptionalParameters[$ArtifactsLocationSasTokenName] = $ArtifactsLocationSasToken
    }
    ```

1.  Crear el grupo de recursos si ya no existe y busque el archivo de plantilla y los parámetros de los errores de validación que evitará la implementación con éxito.

    ```
    # Create or update the resource group using the specified template file and template parameters file
    New-AzureRMResourceGroup -Name $ResourceGroupName -Location $ResourceGroupLocation -Verbose -Force -ErrorAction Stop

    Test-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile $TemplateFile -TemplateParameterFile $TemplateParametersFile @OptionalParameters -ErrorAction Stop
    ```

1. Por último, implemente la plantilla. Este código crea un nombre único para la implementación mediante una marca de tiempo.

    ```
    New-AzureRMResourceGroupDeployment -Name ((Get-ChildItem $TemplateFile).BaseName + '-' + ((Get-Date).ToUniversalTime()).ToString('MMdd-HHmm')) `
        -ResourceGroupName $ResourceGroupName `
        -TemplateFile $TemplateFile `
        -TemplateParameterFile $TemplateParametersFile `
        @OptionalParameters `
        -Force -Verbose
    ```

## <a name="deploy-the-resource-group"></a>Implementar el grupo de recursos

### <a name="to-deploy-the-resource-group-in-visual-studio"></a>Para implementar el grupo de recursos en Visual Studio

1. En el menú contextual del proyecto del grupo de recursos de Azure, elija **implementar** > **Implementación de nuevo**.

    ![][0]

1. En el cuadro de diálogo **implementar al grupo de recursos** , en Elija un grupo de recursos existente en el cuadro de lista desplegable para implementar o elija ** &lt;crear nueva... &gt;** Para crear un nuevo grupo de recursos.

    ![][1]

1. Si se le solicita, escriba un nombre de grupo de recursos y la ubicación en el cuadro de diálogo **Crear grupo de recursos** y, a continuación, elija el botón **crear** .

    ![][2]

1. Seleccione el botón **Editar parámetros** para ver el cuadro de diálogo **Editar parámetros** y, a continuación, escriba los valores de parámetro que falta.

    ![][3]

    >[AZURE.NOTE] Si los parámetros requeridos necesitan valores, este cuadro de diálogo aparece automáticamente cuando se implementa.

    ![][4]

1. Cuando haya terminado introducir valores de parámetro, seleccione el botón **Guardar** y, a continuación, elija el botón de **implementar** .

    Se ejecuta el script de implementación (implementar AzureResourceGroup.ps1) y su plantilla, junto con los efectos que se implementa en Azure.

### <a name="to-deploy-the-resource-group-by-using-powershell"></a>Para implementar el grupo de recursos con PowerShell

Si desea ejecutar la secuencia de comandos sin usar el comando de implementación de Visual Studio y la interfaz de usuario, en el menú contextual para la secuencia de comandos, elija **Abrir con PowerShell ISE**.

![][5]


## <a name="command-deployment-examples"></a>Ejemplos de implementación de comando

### <a name="deploy-using-default-values"></a>Implementar con valores predeterminados

Este ejemplo muestra cómo ejecutar la secuencia de comandos con los valores de parámetro predeterminados. (Porque el parámetro de ubicación no tiene un valor predeterminado, debe proporcionar uno.)

`.\Deploy-AzureResourceGroup.ps1 -ResourceGroupLocation eastus`

### <a name="deploy-overriding-the-default-values"></a>Implementar reemplazar los valores predeterminados

Este ejemplo muestra cómo ejecutar la secuencia de comandos para implementar los archivos de plantilla y parámetros distintos de los valores predeterminados.

```
.\Deploy-AzureResourceGroup.ps1 -ResourceGroupLocation eastus –TemplateFile ..\templates\AnotherTemplate.json –TemplateParametersFile ..\templates\AnotherTemplate.parameters.json
```

### <a name="deploy-using-uploadartifacts-for-staging"></a>Implementar con UploadArtifacts para ensayo

Este ejemplo muestra cómo ejecutar la secuencia de comandos para cargar artefactos desde la carpeta release e implementar plantillas de no predeterminada.

```
.\Deploy-AzureResourceGroup.ps1 -StorageAccountName 'mystorage' -StorageAccountResourceGroupName 'Default-Storage-EastUS' -ResourceGroupName 'myResourceGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\windowsvirtualmachine.json' -TemplateParametersFile '..\templates\windowsvirtualmachine.parameters.json' -UploadArtifacts -ArtifactStagingDirectory ..\bin\release\staging
```

Este ejemplo muestra cómo ejecutar la secuencia de comandos en una tarea de PowerShell de Azure en Visual Studio Online.

```
$(Build.StagingDirectory)/AzureResourceGroup1/Scripts/Deploy-AzureResourceGroup.ps1 -StorageAccountName 'mystorage' -StorageAccountResourceGroupName 'Default-Storage-EastUS' -ResourceGroupName 'myResourceGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\windowsvirtualmachine.json' -TemplateParametersFile '..\templates\windowsvirtualmachine.parameters.json' -UploadArtifacts -ArtifactStagingDirectory $(Build.StagingDirectory)
```

## <a name="next-steps"></a>Pasos siguientes
Más información sobre el Administrador de recursos de Azure lea la [información general del Administrador de recursos de Azure](azure-resource-manager/resource-group-overview.md).

Para obtener más ejemplos de trabajar con proyectos de grupo de recursos de Azure, consulte [implementar y administrar recursos Azure](https://github.com/Microsoft/HealthClinic.biz/wiki/Deploy-and-manage-Azure-resources) desde la conexión de [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 [demostración](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/). Para más tutoriales de la demostración HealthClinic.biz, vea [Tutoriales de herramientas de desarrollador de Azure](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts).

[0]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy1c.png
[1]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy2bc.png
[2]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy3bc.png
[3]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy4bc.png
[4]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy5c.png
[5]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy6c.png

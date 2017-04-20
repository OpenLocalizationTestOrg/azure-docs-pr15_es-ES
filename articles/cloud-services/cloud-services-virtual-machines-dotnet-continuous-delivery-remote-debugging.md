<properties
    pageTitle="Habilitar la depuración entrega continuo | Microsoft Azure"
    description="Aprenda a habilitar depuración remota cuando se utiliza la entrega continua para implementar Azure"
    services="cloud-services"
    documentationCenter=".net"
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="tarcher"/>

# <a name="enable-remote-debugging-when-using-continuous-delivery-to-publish-to-azure"></a>Habilitar la depuración remota al usar entrega continua para publicar en Azure

Puede habilitar depuración remota en Azure, para los servicios de nube o máquinas virtuales, al utilizar [entrega continua](cloud-services-dotnet-continuous-delivery.md) para publicar en Azure siguiendo estos pasos.

## <a name="enabling-remote-debugging-for-cloud-services"></a>Habilitar la depuración remota de servicios de nube

1. En el agente de compilación, configurar el entorno inicial para Azure tal como se indica en [La compilación de línea de comandos para Azure](http://msdn.microsoft.com/library/hh535755.aspx).
2. Ya que el tiempo de ejecución de depuración remota (msvsmon.exe) es necesaria para el paquete, instale las [Herramientas remotas de Visual Studio de 2015](http://www.microsoft.com/en-us/download/details.aspx?id=48155) (o las [Herramientas remotas para 5 de actualización de Microsoft Visual Studio 2013](https://www.microsoft.com/en-us/download/details.aspx?id=48156) si usa 2013 de Visual Studio). Como alternativa, puede copiar los archivos binarios de depuración remota de un sistema que tiene instalado Visual Studio.
3. Crear un certificado como se describe en [Introducción a los certificados para servicios de nube de Azure](cloud-services-certs-create.md). Mantener la .pfx y la huella digital de certificado RDP y cargar el certificado en el servicio de nube de destino.
4. Use las siguientes opciones en la línea de comandos de MSBuild para generar y empaquetar con depuración remota habilitada. (Sustituya reales rutas de acceso a sus archivos de proyecto y del sistema para los elementos entre corchetes angulares).

        msbuild /TARGET:PUBLISH /PROPERTY:Configuration=Debug;EnableRemoteDebugger=true;VSX64RemoteDebuggerPath="<remote tools path>";RemoteDebuggerConnectorCertificateThumbprint="<thumbprint of the certificate added to the cloud service>";RemoteDebuggerConnectorVersion="2.7" "<path to your VS solution file>"

    `VSX64RemoteDebuggerPath`es la ruta de acceso a la carpeta que contiene msvsmon.exe en las herramientas remotas para Visual Studio.
    `RemoteDebuggerConnectorVersion`es la versión de SDK de Azure en el servicio de nube. También debe coincidir con la versión instalada con Visual Studio.

5. Publicar en el servicio de nube de destino mediante el archivo de paquete y .cscfg generado en el paso anterior.
6. Importar el certificado (archivo .pfx) en el equipo que tiene Visual Studio con Azure SDK para .NET instalado. Asegúrese de importar a la `CurrentUser\My` almacén de certificados, en caso contrario, asociar la depuración en Visual Studio se producirá un error.

## <a name="enabling-remote-debugging-for-virtual-machines"></a>Habilitar la depuración remota para máquinas virtuales

1. Crear una máquina virtual Azure. Consulte [crear una máquina Virtual ejecuta Windows Server](../virtual-machines/virtual-machines-windows-hero-tutorial.md) o [crear y administrar Azure máquinas virtuales de Windows en Visual Studio](../virtual-machines/virtual-machines-windows-classic-manage-visual-studio.md).
2. En la [página del portal de Azure clásica](http://go.microsoft.com/fwlink/p/?LinkID=269851), ver paneles de la máquina virtual para ver la **Huella digital de certificado de RDP**de la máquina virtual. Este valor se utiliza para la `ServerThumbprint` valor en la configuración de extensión.
3. Crear un certificado de cliente, como se describe en [Introducción a los certificados para servicios de nube de Azure](cloud-services-certs-create.md) (mantener la .pfx y la huella digital de certificado RDP).
4. Instalar Azure Powershell (versión 0.7.4 o posterior) como se describe en [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md).
5. Ejecute el siguiente script para habilitar la extensión de RemoteDebug. Reemplace las rutas de acceso y los datos personales con sus propios, como su nombre de suscripción, el nombre de servicio y la huella digital.

    >[AZURE.NOTE] Esta secuencia de comandos está configurado para Visual Studio de 2015. Si está usando 2013 de Visual Studio, modifique la `$referenceName` y `$extensionName` asignaciones siguientes para utilizar `RemoteDebugVS2013` (en lugar de `RemoteDebugVS2015`).

    <pre>
   AzureAccount agregar

    Seleccione-AzureSubscription "Mi suscripción a Microsoft"

    $vm = AzureVM get - ServiceName "mytestvm1"-mytestvm1"nombre"

    $endpoints = @( ,@{Name="RDConnVS2013"; PublicPort = 30400; PrivatePort = 30398} ,@{Name="RDFwdrVS2013"; PublicPort = 31400; PrivatePort = 31398})  

    foreach ($endpoint en $endpoints) {AzureEndpoint Add - VM $vm-$endpoint el nombre. Name - protocolo tcp PublicPort - $endpoint. PublicPort - puerto $endpoint. PrivatePort}

    $referenceName = "Microsoft.VisualStudio.WindowsAzure.RemoteDebug.RemoteDebugVS2015" $publisher = "Microsoft.VisualStudio.WindowsAzure.RemoteDebug" $extensionName = "RemoteDebugVS2015" $version = "1.* no" $publicConfiguration = "<PublicConfig>< Connector.Enabled > true < /Connector.Enabled ><ClientThumbprint>56D7D1B25B472268E332F7FC0C87286458BFB6B2</ClientThumbprint><ServerThumbprint>E7DCB00CB916C468CC3228261D6E4EE45C8ED3C6</ServerThumbprint><ConnectorPort>30398</ConnectorPort><ForwarderPort>31398</ForwarderPort></PublicConfig>"

    $vm | Establecer AzureVMExtension `
    -ReferenceName $referenceName ` 
    -Publisher $publisher `
    -ExtensionName $extensionName ` 
    -versión $version ' - PublicConfiguration $publicConfiguration

    foreach ($extension de $vm. MÁQUINA VIRTUAL. ResourceExtensionReferences) {si (($extension. Nombre de referencia - EC $referenceName) `
    -and ($extension.Publisher -eq $publisher) ` 
    - y ($extension. Nombre - EC $extensionName) '- y ($extension. Versión - EC $version)) {$extension. ResourceExtensionParameterValues [0]. Clave = 'config.txt' salto}}

    $vm | Actualización AzureVM </pre>

6. Importar el certificado (.pfx) en el equipo que tiene Visual Studio con Azure SDK para .NET instalado.

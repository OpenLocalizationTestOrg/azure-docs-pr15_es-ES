<properties
    pageTitle="Usar la redirección de RemoteApp de Azure | Microsoft Azure"
    description="Obtenga información sobre cómo configurar y utilizar la redirección de RemoteApp"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />

# <a name="using-redirection-in-azure-remoteapp"></a>Usar la redirección de RemoteApp de Azure

> [AZURE.IMPORTANT]
> RemoteApp Azure es que se utilizan. Lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) para obtener información detallada.

Redirección del dispositivo permite a los usuarios interactuar con aplicaciones remotas con los dispositivos conectados a su equipo local, el teléfono o la tableta. Por ejemplo, si ha proporcionado Skype a través de RemoteApp de Azure, el usuario necesita la cámara instalada en su equipo para que funcione con Skype. Esto también es verdadero un periféricos conectados por el rango de USB, altavoces, monitores e impresoras.

RemoteApp aprovecha el protocolo de escritorio remoto (RDP) y RemoteFX para proporcionar redirección.

## <a name="what-redirection-is-enabled-by-default"></a>¿Qué redirección está habilitada de forma predeterminada?
Al usar RemoteApp, los redireccionamientos siguientes están habilitados de forma predeterminada. La información de paréntesis muestra la configuración de RDP.

- Reproducir sonidos en el equipo local (**Reproducir en este equipo**). (audiomode:i:0)
- Capturar audio desde el equipo local y enviar al equipo remoto (**registro de este equipo**). (audiocapturemode:i:1)
- Imprimir en impresoras locales (redirectprinters:i:1)
- Puertos COM (redirectcomports:i:1)
- Dispositivo de tarjeta inteligente (redirectsmartcards:i:1)
- Portapapeles (capacidad de copiar y pegar) (redirectclipboard:i:1)
- Borrar suavizado de fuentes de tipo (permitir suavizado de fuentes: i:1)
- Redirigir Plug and Play compatibles con todos los dispositivos. (devicestoredirect:s: *)

## <a name="what-other-redirection-is-available"></a>¿Qué otra redirección está disponible?
Dos opciones de redireccionamiento están deshabilitadas de forma predeterminada:

- Redirección de unidades (asignación de unidad): unidades del equipo local se convierten en las unidades asignadas en la sesión remota. Esto le permite guardar o abrir archivos de las unidades locales mientras trabaja en la sesión remota.
- Redireccionamiento USB: puede usar los dispositivos USB conectados a su equipo local dentro de la sesión remota.

## <a name="change-your-redirection-settings-in-remoteapp"></a>Cambiar la configuración de redireccionamiento de RemoteApp
Puede cambiar la configuración de redirección de dispositivo para una colección de mediante Microsoft Azure PowerShell con SDK. Después de instalar las nuevas PowerShell y SDK, primero configurarlo para administrar su suscripción, como se describe en [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md).

A continuación, use un comando similar al siguiente para establecer las propiedades RDP personalizadas:

    Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "drivestoredirect:s:*`nusbdevicestoredirect:s:*"

(Tenga en cuenta que *' n* se utiliza como un delimitador entre propiedades individuales).

Para obtener una lista de están configuradas las propiedades personalizadas de RDP, ejecute el cmdlet siguiente. Tenga en cuenta que las propiedades personalizadas solo se muestran como resultados de salida y no las propiedades predeterminadas:  

    Get-AzureRemoteAppCollection -CollectionName <collection name>

Al establecer propiedades personalizadas debe especificar todas las propiedades personalizadas cada vez; en caso contrario, la configuración vuelve a desactivado.   

### <a name="common-examples"></a>Ejemplos comunes
Para habilitar la redirección de unidades, use el siguiente cmdlet:  

    Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "drivestoredirect:s:*”

Use este cmdlet para habilitar USB y unidad redirección:

    Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "drivestoredirect:s:*`nusbdevicestoredirect:s:*"

Use este cmdlet para deshabilitar el uso compartido del Portapapeles:  

    Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "redirectclipboard:i:0”

> [AZURE.IMPORTANT] No olvide cerrar completamente todos los usuarios de la colección (y no solo desconectarlos) antes de probar el cambio. Para asegurarse de que los usuarios han salido completamente, vaya a la pestaña **sesiones** de la colección en el portal de Azure y cierre todos los usuarios que se desconecta o se ha iniciado sesión en la sesión. A veces puede tardar unos segundos para las unidades locales para mostrar en el Explorador de dentro de la sesión.

## <a name="change-usb-redirection-settings-on-your-windows-client"></a>Cambiar la configuración de redireccionamiento USB en el cliente de Windows

Si desea usar la redirección de USB en un equipo que se conecta a RemoteApp, hay 2 acciones que deben ocurrir. 1 - su administrador debe habilitar la redirección de USB en el nivel de colección con PowerShell de Azure. 2 - en cada dispositivo donde desea usar la redirección de USB, debe habilitar una directiva de grupo que lo permita. Este paso se debe realizar para cada usuario que desea usar la redirección de USB.

> [AZURE.NOTE] Redireccionamiento USB con Azure RemoteApp solo es compatible con equipos con Windows.

### <a name="enable-usb-redirection-for-the-remoteapp-collection"></a>Habilitar la redirección de USB para la colección de RemoteApp
Para habilitar la redirección de USB en el nivel de colección, use el siguiente cmdlet:

    Set-AzureRemoteAppCollection -CollectionName <collection_name> -CustomRdpProperty "nusbdevicestoredirect:s:*"

### <a name="enable-usb-redirection-for-the-client-computer"></a>Habilitar la redirección de USB para el equipo cliente

Para configurar opciones de redireccionamiento de USB en su equipo:

1. Abra el Editor de directivas de grupo Local (GPEDIT. MSC). (Ejecutar gpedit.msc desde un símbolo del sistema).
2. Abra el **equipo equipo\Directivas\Plantillas administrativas\Componentes Windows\Servicios Escritorio remoto\Host conexión a escritorio Client\RemoteFX redirección de dispositivo USB**.
3. Haga doble clic en **Permitir RDP redireccionamiento de otros dispositivos USB RemoteFX compatibles desde este equipo**.
4. Seleccione **habilitado**y, a continuación, seleccione **administradores y usuarios de los derechos de acceso de redirección de RemoteFX USB**.
5. Abra un símbolo del sistema con permisos de administrador y ejecute el siguiente comando:

        gpupdate /force
6. Reinicie el equipo.

También puede usar la herramienta de administración de directivas de grupo para crear y aplicar la directiva de redireccionamiento USB para todos los equipos de su dominio:

1. Inicie sesión en el controlador de dominio como administrador de dominio.
2. Abra la consola de administración de directivas de grupo. (Haga clic en **Inicio > Herramientas administrativas > Administración de directiva de grupo**.)
3. Navegue hasta el dominio o unidad organizativa para el que desea crear la directiva.
4. Haga clic en la **Directiva predeterminada de dominio**y, a continuación, haga clic en **Editar**.
5. Abra el **equipo equipo\Directivas\Plantillas administrativas\Componentes Windows\Servicios Escritorio remoto\Host conexión a escritorio Client\RemoteFX redirección de dispositivo USB**.
6. Haga doble clic en **Permitir RDP redireccionamiento de otros dispositivos USB RemoteFX compatibles desde este equipo**.
7. Seleccione **habilitado**y, a continuación, seleccione **administradores y usuarios de los derechos de acceso de redirección de RemoteFX USB**.
8. Haga clic en **Aceptar**.  

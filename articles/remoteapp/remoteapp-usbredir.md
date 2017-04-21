<properties 
    pageTitle="¿Cómo se redirigir dispositivos USB en RemoteApp de Azure? | Microsoft Azure" 
    description="Obtenga información sobre cómo usar la redirección para dispositivos USB en Azure RemoteApp." 
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



# <a name="how-do-you-redirect-usb-devices-in-azure-remoteapp"></a>¿Cómo se redirigir dispositivos USB en RemoteApp de Azure?

> [AZURE.IMPORTANT]
> RemoteApp Azure es que se utilizan. Lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) para obtener información detallada.

Redirección del dispositivo permite a los usuarios utilizar los dispositivos USB vinculados a su equipo o tableta con las aplicaciones de Azure RemoteApp. Por ejemplo, si comparte Skype a través de RemoteApp de Azure, su necesitan los usuarios puedan usar sus cámaras del dispositivo.

Antes de proseguir, asegúrese de que leer la información de redirección USB en la [redirección de uso en Azure RemoteApp](remoteapp-redirection.md). Sin embargo, el recomendado nusbdevicestoredirect:s: * no funcionará para cámaras web USB y no funcionen para algunas impresoras USB o dispositivos multifuncionales de USB. Por diseño y por motivos de seguridad, el Administrador de RemoteApp de Azure tiene que habilitar la redirección GUID de clase de dispositivo o Id. de instancia de dispositivo antes de que los usuarios pueden usar los dispositivos.

Aunque este artículo trata sobre redirección de cámara web, puede utilizar un enfoque similar para redirigir impresoras USB y otros dispositivos multifuncionales USB que no se redirigen por el **nusbdevicestoredirect:s:*** comando.

## <a name="redirection-options-for-usb-devices"></a>Opciones de redirección de dispositivos USB
Azure RemoteApp utiliza mecanismos muy similares para redirigir dispositivos USB como los que están disponibles para servicios de escritorio remoto. La tecnología subyacente le permite elegir el método de redireccionamiento correcto para un dispositivo determinado, para sacar el máximo de ambos alto nivel y RemoteFX USB redireccionamiento usando el **usbdevicestoredirect:s:** comando. Hay cuatro elementos a este comando:

| Orden de procesamiento | Parámetro           | Descripción                                                                                                                |
|------------------|---------------------|----------------------------------------------------------------------------------------------------------------------------|
| 1                | *                   | Selecciona todos los dispositivos que no están recogidos por redireccionamiento de alto nivel. Nota: por diseño, * no funciona para cámaras web USB.  |
|                  | {GUID de clase de dispositivo} | Selecciona todos los dispositivos que coinciden con la clase de configuración de dispositivo especificado.                                                           |
|                  | USB\InstanceID      | Selecciona un dispositivo USB especificado para el Id.                                                                  |
| 2                | -ID USB\Instance    | Elimina la configuración de redireccionamiento para el dispositivo especificado.                                                                 |

## <a name="redirecting-a-usb-device-by-using-the-device-class-guid"></a>Redirigir un dispositivo USB mediante la GUID de clase de dispositivo
Hay dos formas de encontrar la clase GUID que se pueden usar para la redirección de dispositivos. 

La primera opción es utilizar la [Definidas dispositivo configuración clases disponibles a proveedores](https://msdn.microsoft.com/library/windows/hardware/ff553426.aspx). Elija la clase que mejor coincida con el dispositivo conectado al equipo local. Para cámaras digitales Esto podría ser una clase de dispositivo Imaging o la clase de dispositivo de captura de vídeo. Debe hacer algunas experimentación con las clases de dispositivos para buscar la clase correcta GUID que funciona con el dispositivo USB conectado localmente (en nuestro caso, la cámara web).

Una manera mejor o la segunda opción, es siga estos pasos para encontrar el GUID de clase de dispositivo específicos:

1. Abrir el Administrador de dispositivos, busque el dispositivo que se desvíen y haga clic en él y, a continuación, abra las propiedades.
![Abrir el Administrador de dispositivos](./media/remoteapp-usbredir/ra-devicemanager.png)
2. En la pestaña **Detalles** , seleccione la propiedad **Guid de clase**. El valor que aparece es el GUID de la clase para ese tipo de dispositivo.
![Propiedades de cámara](./media/remoteapp-usbredir/ra-classguid.png)
3. Use el valor de la clase Guid redirigir los dispositivos que coincidan con ellos.

Por ejemplo:

        Set-AzureRemoteAppCollection -CollectionName <collection name> -CustomRdpProperty "nusbdevicestoredirect:s:<Class Guid value>"

Puede combinar varios redireccionamientos de dispositivo en el mismo cmdlet. Por ejemplo: para redirigir el almacenamiento local y una cámara web USB, cmdlet es similar a esta:

        Set-AzureRemoteAppCollection -CollectionName <collection name> -CustomRdpProperty "drivestoredirect:s:*`nusbdevicestoredirect:s:<Class Guid value>"

Cuando se establece la redirección de dispositivos de clase GUID se redirigen todos los dispositivos que coinciden con ese GUID de clase de la colección especificada. Por ejemplo, si hay varios equipos en la red local que tienen el mismas cámaras de web USB, puede ejecutar un solo cmdlet para desviar todas las cámaras de web.

## <a name="redirecting-a-usb-device-by-using-the-device-instance-id"></a>Redirigir un dispositivo USB mediante el identificador de instancia de dispositivo

Si desea un control más detallado y controlar redirección por dispositivo, puede usar el parámetro de redirección **USB\InstanceID** .

La parte más difícil de este método es encontrar el identificador de instancia de dispositivo USB. Tendrá acceso al equipo y el dispositivo USB específico. A continuación, siga estos pasos:

1. Habilitar la redirección de dispositivo en la sesión de escritorio remoto, como se describe en [¿cómo usar Mis dispositivos y recursos en una sesión de escritorio remoto?](http://windows.microsoft.com/en-us/windows7/How-can-I-use-my-devices-and-resources-in-a-Remote-Desktop-session)
2. Abra una conexión a Escritorio remoto y haga clic en **Mostrar opciones**.
3. Haga clic en **Guardar como** para guardar la configuración de conexión actual a un archivo RDP.  
    ![Guardar la configuración como un archivo RDP](./media/remoteapp-usbredir/ra-saveasrdp.png)
4. Elija un nombre de archivo y una ubicación, por ejemplo, "MyConnection.rdp" y "Este PC\Documents" y guarde el archivo.
5. Abra el archivo MyConnection.rdp con un editor de texto y busque el identificador de instancia del dispositivo que desee redirigir.

Ahora puede utilizar el identificador de instancia en el siguiente cmdlet:

    Set-AzureRemoteAppCollection -CollectionName <collection name> -CustomRdpProperty "nusbdevicestoredirect:s: USB\<Device InstanceID value>"



### <a name="help-us-help-you"></a>Ayúdenos a ayudarle 
¿Sabía que, además de valoración de este artículo y realizar comentarios hacia abajo, a continuación, puede realizar cambios en el artículo? ¿Falta algo? ¿Algún error? ¿Escribir algo que es simplemente confuso? Desplazarse hacia arriba y haga clic en **Editar en GitHub** para realizar cambios: los vaya a nosotros para revisión y, a continuación, una vez se aprobar en ellas, verá los cambios y mejoras aquí.
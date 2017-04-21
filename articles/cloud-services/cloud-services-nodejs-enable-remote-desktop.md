<properties 
    pageTitle="Habilitar Escritorio remoto para servicios de nube (Node.js)" 
    description="Obtenga información sobre cómo habilitar el acceso de escritorio remoto en los equipos virtuales aloja su aplicación Node.js de Azure." 
    services="cloud-services" 
    documentationCenter="nodejs" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="cloud-services" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

# <a name="enabling-remote-desktop-in-azure"></a>Habilitar Escritorio remoto en Azure

Escritorio remoto le permite tener acceso a la versión de escritorio de una instancia de función que se ejecuta en Azure. Puede usar una conexión a Escritorio remoto para configurar la máquina virtual o solucionar problemas con la aplicación.

> [AZURE.NOTE] En este artículo se aplica a aplicaciones Node.js hospedadas como un servicio de nube de Azure.


## <a name="prerequisites"></a>Requisitos previos

- Instalar y configurar [Azure Powershell](../powershell-install-configure.md).
- Implementar una aplicación de Node.js a un servicio de nube de Azure. Para obtener más información, vea [crear e implementar una aplicación de Node.js a un servicio de nube de Azure](cloud-services-nodejs-develop-deploy-app.md).


## <a name="step-1-use-azure-powershell-to-configure-the-service-for-remote-desktop-access"></a>Paso 1: Usar Azure PowerShell para configurar el servicio para el acceso de escritorio remoto

Para utilizar Escritorio remoto, deberá actualizar la definición del servicio de Azure y configuración con un nombre de usuario, contraseña y certificado. 

Realice los pasos siguientes desde un equipo que contiene los archivos de origen de la aplicación.

1. Ejecutar **Windows PowerShell** como administrador. (Desde el **Menú Inicio** o la **Pantalla de inicio**, busque **Windows PowerShell**.)

2.  Vaya al directorio que contiene la definición del servicio (.csdef) y archivos de configuración (.cscfg) de servicio.

3. Escriba el siguiente cmdlet de PowerShell:

        Enable-AzureServiceProjectRemoteDesktop

4. Cuando se le solicite, escriba un nombre de usuario y contraseña.

    ![Habilitar azureserviceprojectremotedesktop][enable-rdp]

3.  Escriba el siguiente cmdlet de PowerShell para publicar los cambios:

        Publish-AzureServiceProject

    ![azureserviceproject publicar][publish-project]

## <a name="step-2-connect-to-the-role-instance"></a>Paso 2: Conectar con la instancia de función

Después de publicar la definición del servicio de actualización, puede conectarse a la instancia de rol.

1.  En el [portal de clásica Azure], seleccione **Servicios en la nube** y, a continuación, seleccione el servicio.

    ![Portal de clásico de Azure][cloud-services]

2.  Haga clic en **casos**y, a continuación, haga clic en **producción** o **ensayo** para ver las instancias del servicio. Seleccione una instancia y, a continuación, haga clic en **Conectar** en la parte inferior de la página.

    ![La página de instancias][3]

2.  Al hacer clic en **Conectar**, el explorador web le pregunta si desea guardar un archivo RDP. Abrir este archivo. (Por ejemplo, si está usando Internet Explorer, haga clic en **Abrir**.)

    ![Preguntar si desea abrir o guardar el archivo RDP][4]

3.  Cuando se abre el archivo, aparece el siguiente aviso de seguridad:

    ![Indicador de seguridad de Windows][5]

4.  Haga clic en **Conectar**, y se mostrará un mensaje de seguridad para introducir las credenciales para obtener acceso a la instancia. Escriba la contraseña creada en [paso 1] [paso 1: configurar el servicio para el acceso de escritorio remoto con PowerShell Azure] y, a continuación, haga clic en **Aceptar**.

    ![símbolo del sistema de nombre de usuario y contraseña][6]

Cuando se realiza la conexión, conexión a Escritorio remoto muestra el escritorio de la instancia de Azure. 

![Sesión de escritorio remoto][7]

## <a name="step-3-configure-the-service-to-disable-remote-desktop-access"></a>Paso 3: Configurar el servicio para deshabilitar el acceso de escritorio remoto 

Cuando ya no necesite conexiones a Escritorio remoto para las instancias de la función en la nube, deshabilitar el acceso de escritorio remoto con [PowerShell de Azure].

1.  Escriba el siguiente cmdlet de PowerShell:

        Disable-AzureServiceProjectRemoteDesktop

2.  Escriba el siguiente cmdlet de PowerShell para publicar los cambios:

        Publish-AzureServiceProject

## <a name="additional-resources"></a>Recursos adicionales

- [Acceso remoto a instancias de la función de Azure] 
- [Uso de escritorio remoto con las funciones de Azure]
- [Centro para desarrolladores de Node.js](/develop/nodejs/)

  [Azure PowerShell]: http://go.microsoft.com/?linkid=9790229&clcid=0x409

[Portal de clásico de Azure]: http://manage.windowsazure.com
[publish-project]: ./media/cloud-services-nodejs-enable-remote-desktop/publish-rdp.png
[enable-rdp]: ./media/cloud-services-nodejs-enable-remote-desktop/enable-rdp.png
[cloud-services]: ./media/cloud-services-nodejs-enable-remote-desktop/cloud-services-remote.png
[3]: ./media/cloud-services-nodejs-enable-remote-desktop/cloud-service-instance.png
[4]: ./media/cloud-services-nodejs-enable-remote-desktop/rdp-open.png
[5]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-12.png
[6]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-13.png
[7]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-14.png
  
[Acceso remoto a instancias de la función de Azure]: http://msdn.microsoft.com/library/windowsazure/hh124107.aspx
[Uso de escritorio remoto con las funciones de Azure]: http://msdn.microsoft.com/library/windowsazure/gg443832.aspx
 
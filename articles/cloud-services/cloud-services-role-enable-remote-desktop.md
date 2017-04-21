<properties 
pageTitle="Habilitar la conexión a Escritorio remoto para un rol de servicios de nube de Azure" 
description="Cómo configurar la aplicación de servicio de nube azure para permitir las conexiones de escritorio remotas" 
services="cloud-services" 
documentationCenter="" 
authors="sbtron" 
manager="timlt" 
editor=""/>
<tags 
ms.service="cloud-services" 
ms.workload="tbd" 
ms.tgt_pltfrm="na" 
ms.devlang="na" 
ms.topic="article" 
ms.date="02/17/2016" 
ms.author="saurabh"/>

# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services"></a>Habilitar la conexión a Escritorio remoto para un rol de servicios de nube de Azure

>[AZURE.SELECTOR]
- [Portal de clásico de Azure](cloud-services-role-enable-remote-desktop.md)
- [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
- [Visual Studio](../vs-azure-tools-remote-desktop-roles.md)


Escritorio remoto le permite tener acceso a la versión de escritorio de una función que se ejecuta en Azure. Puede usar una conexión a Escritorio remoto para solucionar problemas y diagnóstico de problemas con la aplicación mientras se está ejecutando. 

Puede habilitar una conexión a Escritorio remoto en su rol durante el desarrollo incluyendo los módulos de escritorio remoto en la definición de servicio, o puede elegir habilitar Escritorio remoto a través de la extensión de escritorio remoto. El método preferido es utilizar la extensión de escritorio remoto como puede habilitar Escritorio remoto incluso después de que la aplicación se implementa sin tener que volver a instalar la aplicación. 


## <a name="configure-remote-desktop-from-the-azure-classic-portal"></a>Configurar Escritorio remoto desde el portal de clásico de Azure
El portal clásico Azure utiliza el enfoque de extensión de escritorio remoto para poder habilitar Escritorio remoto incluso después de que la aplicación se implementa. **La página de su servicio de nube** le permite habilitar el escritorio remoto, cambie la cuenta de administrador local que se usa para conectarse a los equipos virtuales, el certificado había utilizado en la autenticación y establece la fecha de expiración. 


1. Haga clic en **Servicios en la nube**, haga clic en el nombre del servicio de nube y, a continuación, haga clic en **Configurar**.

2. Haga clic en **remoto**.
    
    ![Servicios de nube remoto](./media/cloud-services-role-enable-remote-desktop/CloudServices_Remote.png)
    
    > [AZURE.WARNING] Todas las instancias de la función se iniciará cuando primero Habilitar Escritorio remoto y haga clic en Aceptar (marca). Para evitar que reiniciar el equipo, debe estar instalado el certificado usado para cifrar la contraseña de la función. Para evitar reiniciar el equipo, [cargue un certificado para el servicio de nube](cloud-services-how-to-create-deploy/#how-to-upload-a-certificate-for-a-cloud-service) y, a continuación, volver a este cuadro de diálogo.
    

3. En **Roles**, seleccione el rol que desea actualizar o seleccione **todo** para todos los roles.

4. Realice uno de los cambios siguientes:
    
    - Para habilitar Escritorio remoto, active la casilla de verificación **Habilitar Escritorio remoto** . Para deshabilitar Escritorio remoto, desactive la casilla de verificación.
    
    - Crear una cuenta para utilizar en las conexiones de escritorio remoto a las instancias de la función.
    
    - Actualizar la contraseña de la cuenta existente.
    
    - Seleccione un certificado cargado a usar para la autenticación (cargar el certificado mediante la opción **cargar** en la página **certificados** ) o crear un nuevo certificado. 
    
    - Cambiar la fecha de expiración de la configuración de escritorio remoto.

5. Cuando termine de las actualizaciones de configuración, haga clic en **Aceptar** (marca).


## <a name="remote-into-role-instances"></a>Remoto en instancias de función
Una vez habilitado para las funciones de escritorio remoto puede remoto en una instancia de roles a través de distintas herramientas.

Para conectarse a una instancia de funciones desde el portal de clásico Azure:
    
  1.   Haga clic en **instancias** para abrir la página de **instancias** .
  2.   Seleccione una instancia de la función que tiene configurado de escritorio remoto.
  3.   Haga clic en **Conectar**y a continuación, siga las instrucciones para abrir el escritorio. 
  4.   Haga clic en **Abrir** y, a continuación, **Conectar** para iniciar la conexión a Escritorio remoto. 


### <a name="use-visual-studio-to-remote-into-a-role-instance"></a>Usar Visual Studio para remoto a una instancia de funciones

En Visual Studio, el Explorador de servidores:

1. Expanda la **Azure\\servicios de nube\\[nombre de servicio de nube]** nodo.
2. Expandir **provisional** o **producción**.
3. Expanda la función individual.
4. Haga una de las instancias de la función, haga clic en **Conectar utilizando Escritorio remoto...**y, a continuación, escriba el nombre de usuario y contraseña. 

![Escritorio remoto del explorador de servidor](./media/cloud-services-role-enable-remote-desktop/ServerExplorer_RemoteDesktop.png)


### <a name="use-powershell-to-get-the-rdp-file"></a>Usar PowerShell para obtener el archivo RDP
Puede usar el cmdlet [Get-AzureRemoteDesktopFile](https://msdn.microsoft.com/library/azure/dn495261.aspx) para recuperar el archivo RDP. A continuación, puede usar el archivo RDP con conexión a Escritorio remoto para tener acceso al servicio de nube.

### <a name="programmatically-download-the-rdp-file-through-the-service-management-rest-api"></a>Mediante programación descargar el archivo RDP a través de la API de REST de administración de servicio
Puede usar la operación de resto de [Descarga del archivo RDP](https://msdn.microsoft.com/library/jj157183.aspx) para descargar el archivo RDP. 



## <a name="to-configure-remote-desktop-in-the-service-definition-file"></a>Configurar Escritorio remoto en el archivo de definición de servicio

Este método le permite habilitar el escritorio remoto para la aplicación durante el desarrollo. Este enfoque requiere contraseñas cifradas se almacena en la configuración del servicio de archivo y las actualizaciones de la configuración de escritorio remota, se requeriría una nueva implementación de la aplicación. Si desea evitar estos inconvenientes debe usar el enfoque de extensión de escritorio remoto según que se indica más arriba.  

Puede usar Visual Studio para [Habilitar una conexión a Escritorio remoto](../vs-azure-tools-remote-desktop-roles.md) con el enfoque de archivo de definición de servicio.  
Los pasos siguientes describen los cambios necesarios en los archivos de modelo de servicio para habilitar Escritorio remoto. Visual Studio se realiza automáticamente los cambios al publicar.

### <a name="set-up-the-connection-in-the-service-model"></a>Configurar la conexión del modelo de servicio 
Use el elemento de **importación** para importar el módulo de **acceso remoto** y el módulo de **RemoteForwarder** el archivo [ServiceDefinition.csdef](cloud-services-model-and-package.md#csdef) .

El archivo de definición de servicio debe ser similar al siguiente ejemplo con la `<Imports>` elemento agregado.

```xml
<ServiceDefinition name="<name-of-cloud-service>" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2013-03.2.0">
    <WebRole name="WebRole1" vmsize="Small">
        <Sites>
            <Site name="Web">
                <Bindings>
                    <Binding name="Endpoint1" endpointName="Endpoint1" />
                </Bindings>
            </Site>
        </Sites>
        <Endpoints>
            <InputEndpoint name="Endpoint1" protocol="http" port="80" />
        </Endpoints>
        <Imports>
            <Import moduleName="Diagnostics" />
            <Import moduleName="RemoteAccess" />
            <Import moduleName="RemoteForwarder" />
        </Imports>
    </WebRole>
</ServiceDefinition>
```
El archivo [ServiceConfiguration.cscfg](cloud-services-model-and-package.md#cscfg) debe ser similar al siguiente ejemplo, tenga en cuenta la `<ConfigurationSettings>` y `<Certificates>` elementos. El certificado especificado debe ser [cargado en el servicio de nube](../cloud-services-how-to-create-deploy.md#how-to-upload-a-certificate-for-a-cloud-service).

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceConfiguration serviceName="<name-of-cloud-service>" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="3" osVersion="*" schemaVersion="2013-03.2.0">
    <Role name="WebRole1">
        <Instances count="2" />
        <ConfigurationSettings>
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.Enabled" value="true" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountUsername" value="[name-of-user-account]" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountEncryptedPassword" value="[base-64-encrypted-user-password]" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountExpiration" value="[certificate-expiration]" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteForwarder.Enabled" value="true" />
        </ConfigurationSettings>
        <Certificates>
            <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption" thumbprint="[certificate-thumbprint]" thumbprintAlgorithm="sha1" />
        </Certificates>
    </Role>
</ServiceConfiguration>
```


## <a name="additional-resources"></a>Recursos adicionales

[Cómo configurar los servicios de nube](cloud-services-how-to-configure.md)
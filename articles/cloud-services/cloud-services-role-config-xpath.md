<properties 
pageTitle="Hoja de referencia rápida de nube rol Servicios config XPath | Microsoft Azure" 
description="Las diferentes configuraciones de XPath puede usar en la configuración de rol de servicio de nube para exponer la configuración como una variable de entorno." 
services="cloud-services" 
documentationCenter="" 
authors="Thraka" 
manager="timlt" 
editor=""/>
<tags 
ms.service="cloud-services" 
ms.workload="tbd" 
ms.tgt_pltfrm="na" 
ms.devlang="na" 
ms.topic="article" 
ms.date="08/10/2016" 
ms.author="adegeo"/>

# <a name="expose-role-configuration-settings-as-an-environment-variable-with-xpath"></a>Exponer los valores de configuración de la función como una variable de entorno con XPath

En el trabajo del servicio de nube o el archivo de definición del servicio web rol, puede exponer los valores de configuración de tiempo de ejecución como variables de entorno. Se admiten los siguientes valores de XPath (que corresponden a los valores de la API).

Estos valores XPath también están disponibles a través de la biblioteca [Microsoft.WindowsAzure.ServiceRuntime](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.aspx) . 

## <a name="app-running-in-emulator"></a>Aplicación que se ejecuta en el emulador

Indica que la aplicación se ejecuta en el emulador.

| Tipo  | Ejemplo |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/Deployment/@emulated" |
| Código  | var x = RoleEnvironment.IsEmulated; |


## <a name="deployment-id"></a>Id. de implementación

Recupera el identificador de implementación de la instancia.

| Tipo  | Ejemplo |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/Deployment/@id" |
| Código  | var deploymentId = RoleEnvironment.DeploymentId; |


## <a name="role-id"></a>Identificador de función 

Recupera el identificador de rol actual para la instancia.

| Tipo  | Ejemplo |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/@id" |
| Código  | Id. de var = RoleEnvironment.CurrentRoleInstance.Id; |


## <a name="update-domain"></a>Actualizar dominio

Recupera el dominio de actualización de la instancia.

| Tipo  | Ejemplo |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/@updateDomain" |
| Código  | var Ud. = RoleEnvironment.CurrentRoleInstance.UpdateDomain; |


## <a name="fault-domain"></a>Dominio de error

Recupera el dominio de error de la instancia.

| Tipo  | Ejemplo |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/@faultDomain" |
| Código  | var fd = RoleEnvironment.CurrentRoleInstance.FaultDomain; |


## <a name="role-name"></a>Nombre de función

Recupera el nombre de la función de las instancias.

| Tipo  | Ejemplo |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/@roleName" |
| Código  | var rname = RoleEnvironment.CurrentRoleInstance.Role.Name;  |


## <a name="config-setting"></a>Opción de configuración

Recupera el valor de la configuración especificada.

| Tipo  | Ejemplo |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/ConfigurationSettings/ConfigurationSetting[@name='Setting1']/@value" |
| Código  | configuración de var = RoleEnvironment.GetConfigurationSettingValue("Setting1"); |
 
## <a name="local-storage-path"></a>Ruta de acceso de almacenamiento local

Recupera la ruta de acceso de almacenamiento local para la instancia.

| Tipo  | Ejemplo |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='LocalStore1']/@path" |
| Código  | var localResourcePath = RoleEnvironment.GetLocalResource("LocalStore1"). Ruta_raíz; |


## <a name="local-storage-size"></a>Tamaño de almacenamiento local

Recupera el tamaño del almacenamiento local para la instancia.

| Tipo  | Ejemplo |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='LocalStore1']/@sizeInMB" |
| Código  | var localResourceSizeInMB = RoleEnvironment.GetLocalResource("LocalStore1"). MaximumSizeInMegabytes; |

## <a name="endpoint-protocol"></a>Protocolo de extremo 

Recupera el protocolo de punto final de la instancia.

| Tipo  | Ejemplo |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@protocol" |
| Código  | Protocolo var = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"]. Protocolo; |

## <a name="endpoint-ip"></a>Extremo IP

Obtiene la dirección del extremo especificado.

| Tipo | Ejemplo |
| ----- | ---- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@address" |
| Código  | dirección de var = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"]. IPEndpoint.Address |

## <a name="endpoint-port"></a>Puerto de extremo 

Recupera el puerto de punto final de la instancia.

| Tipo  | Ejemplo |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@port" |
| Código  | puerto de var = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"]. IPEndpoint.Port; |





## <a name="example"></a>Ejemplo

Aquí tenemos un ejemplo de una función de trabajo que crea una tarea de inicio con una variable de entorno denominada `TestIsEmulated` establecido en el [ @emulated valor xpath](#app-running-in-emulator). 

```xml
<WorkerRole name="Role1">
    <ConfigurationSettings>
      <Setting name="Setting1" />
    </ConfigurationSettings>
    <LocalResources>
      <LocalStorage name="LocalStore1" sizeInMB="1024"/>
    </LocalResources>
    <Endpoints>
      <InternalEndpoint name="Endpoint1" protocol="tcp" />
    </Endpoints>
    <Startup>
      <Task commandLine="example.cmd inputParm">
        <Environment>
          <Variable name="TestConstant" value="Constant"/>
          <Variable name="TestEmptyValue" value=""/>
          <Variable name="TestIsEmulated">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated"/>
          </Variable>
          ...
        </Environment>
      </Task>
    </Startup>
    <Runtime>
      <Environment>
        <Variable name="TestConstant" value="Constant"/>
        <Variable name="TestEmptyValue" value=""/>
        <Variable name="TestIsEmulated">
          <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated"/>
        </Variable>
        ...
      </Environment>
    </Runtime>
    ...
</WorkerRole>
```

## <a name="next-steps"></a>Pasos siguientes

Más información sobre el archivo [ServiceConfiguration.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) .

Crear un paquete de [ServicePackage.cspkg](cloud-services-model-and-package.md#servicepackagecspkg) .

Habilitar [escritorio remoto](cloud-services-role-enable-remote-desktop.md) para un rol.

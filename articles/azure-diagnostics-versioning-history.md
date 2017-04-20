<properties
    pageTitle="Historial de versiones de diagnóstico de Azure"
    description="Explicación de los cambios en las distintas versiones de diagnóstico de Azure como enviado con diferentes versiones de Microsoft Azure SDK."
    services="multiple"
    documentationCenter=".net"
    authors="rboucher"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="02/12/2016"
    ms.author="robb"/>


# <a name="microsoft-azure-diagnostics-version-history"></a>Historial de versiones de diagnósticos de Microsoft Azure

¿Nuevo en diagnóstico de Azure? Vea [información general de diagnóstico de Azure](azure-diagnostics.md).

Normalmente, cada versión de Azure SDK se distribuye con una versión nueva de diagnósticos de Azure. La siguiente tabla describe las versiones de diagnósticos de Azure y Azure SDK asociadas con la versión SDK.



Versión de Azure SDK | Versión de diagnósticos de Azure | Modelo
--- | --- | ---
1.x      | 1.0 | complemento
2.0 a 2,4| 1.0 | "
2,5      | 1.2 | extensión
2.6      | 1.3 | "
2.7      | 1.4 | "
2,8      | 1,5 | "


La versión más reciente es 1,5, que se incluye con Azure SDK 2,8. La versión de extensión de diagnósticos de Azure que se incluye con el SDK solo se usa para escenarios de emulador local. Cualquier aplicación implementada usa automáticamente la versión más reciente cuando se ejecuta en Azure, con independencia de que la versión del SDK de la aplicación se ha creado con. Sin embargo, a menos que instale el SDK de Azure más reciente, puede que no tenga todas las herramientas que ayudan a utilizan las nuevas características.

Varias características y cambios que se describen a continuación.

## <a name="azure-sdk-28"></a>Azure SDK 2,8
Azure 2,8 SDK agrega la capacidad para enviar datos de diagnósticos de [Aplicación perspectivas](./application-insights/app-insights-cloudservices.md) facilitará diagnosticar problemas de la aplicación, así como el nivel de sistema y la infraestructura.

## <a name="azure-26-diagnostics-changes"></a>Cambios de diagnósticos de Azure 2.6

Para los proyectos de servicio de nube de Azure SDK 2.6 en Visual Studio, se han realizado los cambios siguientes. (Estos cambios también se aplican a las versiones posteriores de Azure SDK.)

- El emulador local admite ahora diagnósticos. Esto significa que puede recopilar datos de diagnóstico y asegúrese de que la aplicación está creando los seguimientos derecha mientras está desarrollar y probar en Visual Studio. La cadena de conexión `UseDevelopmentStorage=true` permite la recopilación de datos de diagnóstico mientras se ejecuta el proyecto de servicio de nube en Visual Studio por uso el almacenamiento de Azure. Todos los datos de diagnóstico está almacenada en la cuenta de almacenamiento (almacenamiento de desarrollo).

- La cadena de conexión de cuenta de almacenamiento de información de diagnóstico (Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString) se almacena de nuevo en el archivo de configuración (.cscfg) de servicio. En Azure SDK 2,5 la cuenta de almacenamiento de diagnósticos se especificó en el archivo diagnostics.wadcfgx.

Existen algunas diferencias importantes entre cómo ha funcionado la cadena de conexión en Azure SDK 2,4 y versiones anteriores y cómo funciona en Azure SDK 2.6 y versiones posteriores.

- En Azure SDK 2,4 y anteriores, la cadena de conexión utilizó como un tiempo de ejecución el complemento de diagnóstico para obtener la información de cuenta de almacenamiento para la transferencia de registros de diagnósticos.

- En Azure SDK 2.6 y versiones posteriores, se utiliza la cadena de conexión de diagnósticos Visual Studio para configurar la extensión de diagnósticos con la información de cuenta de almacenamiento apropiado durante la publicación. La cadena de conexión le permite definir las cuentas de almacenamiento diferentes configuraciones de servicio diferente que usará Visual Studio al publicar. Sin embargo, porque el complemento de diagnósticos ya no está disponible (después de Azure SDK 2.5), el archivo .cscfg por sí mismo no puede habilitar la extensión de diagnósticos. Debe habilitar la extensión por separado por medio de herramientas como Visual Studio o PowerShell.

- Para simplificar el proceso de configuración de la extensión de diagnósticos con PowerShell, el resultado de paquete de Visual Studio también contiene el público XML de configuración para la extensión de diagnósticos de cada rol. Visual Studio utiliza la cadena de conexión de diagnóstico para rellenar la información de cuenta de almacenamiento presente en la configuración del pública. Los archivos de configuración público se crean en la carpeta Extensiones y sigan el patrón PaaSDiagnostics. <RoleName>. PubConfig.xml. Las implementaciones de PowerShell según pueden usar este patrón para asignar cada configuración a un rol.

- La cadena de conexión en el archivo .cscfg también se utiliza el portal de Azure accediendo a los datos de diagnóstico para que aparezca en la ficha **supervisión** . La cadena de conexión es necesaria para configurar el servicio para mostrar los datos detallados de supervisión en el portal.

### <a name="migrating-projects-to-azure-sdk-26-and-later"></a>Migración de proyectos a Azure SDK 2.6 y versiones posteriores

Cuando se migra de Azure SDK 2,5 a Azure SDK 2.6 o versiones posteriores, si tiene una cuenta de almacenamiento de diagnósticos especificada en el archivo .wadcfgx, a continuación, permanecerá allí. Para aprovechar las ventajas de la flexibilidad de usar cuentas de almacenamiento diferentes configuraciones de almacenamiento diferentes, debe agregar manualmente la cadena de conexión a su proyecto. Si está migrando un proyecto de Azure SDK 2,4 o una versión anterior a Azure SDK 2.6, se conservan las cadenas de conexión de diagnósticos. Sin embargo, tenga en cuenta los cambios en cómo se tratan las cadenas de conexión en Azure SDK 2.6 tal como se especifica en la sección anterior.

### <a name="how-visual-studio-determines-the-diagnostics-storage-account"></a>¿Cómo Visual Studio determina la cuenta de almacenamiento de información de diagnóstico

- Si se especifica una cadena de conexión de diagnóstico en el archivo .cscfg, en Visual Studio se utiliza para configurar la extensión de diagnósticos al publicar y al generar los archivos xml de configuración de pública durante el embalaje.

- Si no se especifica ninguna cadena de conexión de diagnóstico en el archivo .cscfg, a continuación, Visual Studio utiliza con la cuenta de almacenamiento especificada en el archivo .wadcfgx para configurar la extensión de diagnósticos al publicar y generar los archivos xml de configuración público al empaquetar.

- La cadena de conexión de diagnóstico en el archivo .cscfg tiene prioridad sobre la cuenta de almacenamiento en el archivo .wadcfgx. Si se especifica una cadena de conexión de diagnóstico en el archivo .cscfg, usa Visual Studio y pasa por alto la cuenta de almacenamiento en .wadcfgx.

### <a name="what-does-the-update-development-storage-connection-strings-checkbox-do"></a>¿Qué significa la "actualización desarrollo almacenamiento cadenas de conexión..." ¿casilla de verificación hacer?

La casilla de verificación **actualizar cadenas de conexión de almacenamiento de desarrollo de diagnósticos y almacenamiento en caché con credenciales de cuenta de Microsoft Azure almacenamiento cuando se publica en Microsoft Azure** proporciona una manera conveniente actualizar las cadenas de conexión de cuenta de almacenamiento de desarrollo con la cuenta de almacenamiento de Azure especificada durante la publicación.

Por ejemplo, supongamos que selecciona esta casilla de verificación y especifica la cadena de conexión de diagnósticos `UseDevelopmentStorage=true`. Cuando se publique el proyecto en Azure, Visual Studio se actualizará automáticamente la cadena de conexión de diagnósticos con la cuenta de almacenamiento que especificó en el Asistente para la publicación. Sin embargo, si se ha especificado una cuenta de almacenamiento real como la cadena de conexión de diagnósticos, se utiliza en su lugar la cuenta.

## <a name="diagnostics-functionality-differences-between-azure-sdk-24-and-earlier-and-azure-sdk-25-and-later"></a>Diferencias de funcionalidad de diagnósticos entre Azure SDK 2,4 y anterior y Azure SDK 2.5 y posterior

Si está actualizando el proyecto de Azure SDK 2,4 a Azure SDK 2.5 o posterior, debe tener en cuenta las siguientes diferencias funcionalidad de diagnósticos.

- **API de configuración están obsoletos** : configuración mediante programación de diagnósticos está disponible en Azure SDK 2,4 o versiones anteriores, pero es obsoleto en Azure SDK 2,5 y versiones posteriores. Si la configuración de diagnósticos está definida actualmente en el código, deberá configurar desde cero en el proyecto migrado para diagnósticos para seguir trabajando. El archivo de configuración de diagnósticos de Azure SDK 2,4 es diagnostics.wadcfg y diagnostics.wadcfgx Azure SDK 2,5 y versiones posteriores.

- **Diagnósticos de aplicaciones de servicio de nube sólo se puede configurar en el nivel de la función, no en el nivel de instancia.**

- **Cada vez implemente la aplicación, se actualiza la configuración de diagnósticos** : Esto puede provocar problemas de paridad si cambia la configuración de diagnósticos desde el Explorador de servidor y, a continuación, volver a instalar la aplicación.

- **En Azure SDK 2,5 y posteriores, colapsar volcados están configurados en el archivo de configuración de diagnósticos, no en el código** : si tiene volcados configurados en el código, deberá transferir manualmente la configuración de código al archivo de configuración, porque la volcados no se transfieren durante la migración a Azure SDK 2.6.

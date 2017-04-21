<properties
    pageTitle="Prácticas recomendadas de Azure RemoteApp | Microsoft Azure"
    description="Prácticas recomendadas para configurar y utilizar RemoteApp de Azure."
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

# <a name="best-practices-for-configuring-and-using-azure-remoteapp"></a>Prácticas recomendadas para configurar y utilizar RemoteApp de Azure

> [AZURE.IMPORTANT]
> RemoteApp Azure es que se utilizan. Lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) para obtener información detallada.

La siguiente información puede ayudarle a configurar y usar RemoteApp de Azure productiva.

## <a name="connectivity"></a>Conectividad


- Usar siempre la última versión de cliente. Con clientes antiguos puede provocar problemas de conectividad y otras experiencias degradados. Habilitar actualizaciones automáticas de aplicaciones de su dispositivo se asegurará de que el cliente más reciente siempre está instalado.
- Usar siempre la conexión a internet más estable y confiable disponible para usted.  
- Usar solo compatible con conexiones de servidor proxy para obtener un rendimiento óptimo de conectividad.  No se admite el proxy SOCKS.

## <a name="applications"></a>Aplicaciones


- Guarde y cierre aplicaciones RemoteApp cuando haya terminado con la aplicación. No cierre la aplicación pueden ocasionar una pérdida de datos.
- Validar aplicaciones personalizadas antes de usarlas en Azure RemoteApp. Esto incluye asegurarse de que funcionan en una plataforma de múltiples sesiones y no consumen recursos innecesarios como memoria y CPU que podría dejar sin recursos de otro usuario en la misma colección. Para obtener más información, descargue y revise los [Procedimientos recomendados de compatibilidad de aplicaciones para servicios de escritorio remoto](http://www.dabcc.com/resources/Application%20Compatibility%20Best%20Practices%20for%20Remote%20Desktop%20Services.pdf).

## <a name="configuration-and-management"></a>Configuración y administración


- Mantener las imágenes de plantilla actualizado, instalar actualizaciones de software y otras revisiones críticas según sea necesario. Así se garantiza que como RemoteApp de Azure automática-escalas para satisfacer su capacidad es revisar cada instancia.  
- Asegúrese de que la implementación de los servicios de federación de Active Directory (AD FS) es segura y confiable. En caso contrario autenticaciones del cliente pueden producir un error, para evitar que los usuarios accedan a RemoteApp de Azure.
- Configurar imágenes de plantilla con las aplicaciones instaladas, roles o características que son independientes. No debe confiar en todas las instancias de las máquinas virtuales de un servicio de RemoteApp está en un estado persistente.
    - Almacenar todos los datos de usuario en perfiles de usuario o en otras ubicaciones de almacenamiento externos al servicio, como local de archivos compartidos o OneDrive.
    - Almacenar datos compartidos en ubicaciones de almacenamiento externos al servicio, como local de archivos compartidos o OneDrive.
    - Configurar todo el sistema en la imagen de plantilla en lugar de en máquinas virtuales individuales en un servicio.
    - Deshabilitar las actualizaciones de software automática para las aplicaciones publicadas: en su lugar aplique manualmente a la imagen de la plantilla y probarlas antes de implementar de la plantilla.

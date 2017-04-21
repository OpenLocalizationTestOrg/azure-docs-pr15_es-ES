
<properties
    pageTitle="Requisitos de la aplicación para Azure RemoteApp | Microsoft Azure"
    description="Obtenga más información sobre los requisitos para las aplicaciones que desea usar en RemoteApp de Azure"
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



# <a name="app-requirements"></a>Requisitos de la aplicación

> [AZURE.IMPORTANT]
> RemoteApp Azure es que se utilizan. Lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) para obtener información detallada.

Azure RemoteApp admite transmisiones 32 bits o 64 bits basado en Windows aplicaciones desde una imagen de Windows Server 2012 R2. La mayoría 32 bits o 64 bits basado en Windows aplicaciones existentes ejecutarán "tal cual" en Azure RemoteApp (servicios de escritorio remoto o anteriormente conocido como Terminal Services) entorno. Sin embargo, hay una diferencia entre ejecuta y también - algunas aplicaciones funcionan correctamente y funcionan correctamente, mientras que otras personas no. La información siguiente proporciona pautas para desarrollar aplicaciones en un entorno de servicios de escritorio remoto y probar para garantizar la compatibilidad.

Sugerencia: Estamos trabajando en crear algunos ejemplos de trabajo de aplicaciones. Verá los temas nuevos que tratar con Microsoft Access, QuickBooks y App-V en RemoteApp.

## <a name="requirements"></a>Requisitos
Estos tres requisitos, si seguido, Ayuda de la aplicación ejecuten correctamente en RemoteApp:

1.  Aplicaciones que cumplan todos los [requisitos de certificación para aplicaciones de escritorio de Windows](https://msdn.microsoft.com/library/windows/desktop/hh749939.aspx) y siga [las directrices de programación de servicios de escritorio remoto](https://msdn.microsoft.com/library/aa383490.aspx) tendrá compatibilidad completa con RemoteApp.
2.  Aplicaciones nunca deben almacenar datos localmente en la imagen o instancias de RemoteApp que se pueden perder.  Después de crear una colección de RemoteApp, las instancias se duplican y no tienen estadas y solo deben contener aplicaciones. Almacenar los datos en un origen externo o en el perfil de usuario.
3.  Imágenes personalizadas nunca deben contener datos que se pueden perder.  

## <a name="testing-your-apps"></a>Probar las aplicaciones
Utilice estos pasos para probar aplicaciones:

1.  Instalar Windows Server 2012 R2 y la aplicación
2.  Habilitar Escritorio remoto
3.  Cree dos cuentas de usuario, el usuario y el usuario b, agregar las cuentas de usuario al grupo de seguridad de escritorio remoto.
4.  Comprobar la compatibilidad de múltiples sesiones estableciendo dos sesiones simultáneas de RDS al equipo al iniciar la aplicación.
5.  Validar el comportamiento de la aplicación

## <a name="application-development-guidelines"></a>Instrucciones de desarrollo de aplicaciones
Use las siguientes pautas para desarrollar aplicaciones para RemoteApp.

### <a name="multiple-users"></a>Varios usuarios

- Instalar una [aplicación de un único usuario ](https://msdn.microsoft.com/library/aa380661.aspx)puede crear problemas en un entorno multiusuario.
- Aplicaciones deben [almacenar información específica de usuario](https://msdn.microsoft.com/library/aa383452.aspx) en ubicaciones específicas del usuario, independientemente de la información global que se aplica a todos los usuarios.
- RemoteApp usa varios [espacios de nombres para los objetos del núcleo](https://msdn.microsoft.com/library/aa382954.aspx); un espacio de nombres global se utiliza principalmente por los servicios de aplicaciones cliente/servidor.
- No es seguro suponer que el nombre del equipo o la [dirección IP](https://msdn.microsoft.com/library/aa382942.aspx) asignada al equipo están asociados con un solo usuario porque varios usuarios pueden iniciar sesión simultáneamente en un servidor de escritorio remoto (RD Session Host).

### <a name="performance"></a>Rendimiento
- Deshabilitar [los efectos de gráficos](https://msdn.microsoft.com/library/aa380822.aspx) antes de agregar la aplicación a RemoteApp.
- Para maximizar la disponibilidad de la CPU para todos los usuarios, deshabilite las [tareas en segundo plano](https://msdn.microsoft.com/library/aa380665.aspx) o crear tareas en segundo plano eficaz que no son recursos.
- Debe ajustar y equilibrar el [uso de subprocesos](https://msdn.microsoft.com/library/aa383520.aspx) de la aplicación para un entorno multiusuario y varios procesadores.
- Para optimizar el rendimiento, es conveniente para las aplicaciones para [detectar](https://msdn.microsoft.com/library/aa380798.aspx) si se están ejecutando en una sesión de cliente.

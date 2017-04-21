<properties 
   pageTitle="Administración del servicio de administrador de StorSimple | Microsoft Azure"
   description="Obtenga información sobre cómo administrar el dispositivo StorSimple mediante el servicio Administrador de StorSimple en el portal de clásico de Azure."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/21/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-administer-your-storsimple-device"></a>Utilizar el servicio Administrador de StorSimple para administrar el dispositivo StorSimple

## <a name="overview"></a>Información general

En este artículo se describe la interfaz de servicio de StorSimple Manager, incluido cómo conectarse a ella, las diversas opciones disponibles y vínculos a los flujos de trabajo específicos que se pueden realizar a través de esta interfaz de usuario. Esta guía es aplicable a ambos; StorSimple físico y el dispositivo virtual.

Después de leer este artículo, aprenderá a:

- Conectar con el servicio Administrador de StorSimple
- Vaya a la interfaz de usuario del Administrador de StorSimple
- Administrar el dispositivo de StorSimple a través del servicio de administrador de StorSimple


## <a name="connect-to-storsimple-manager-service"></a>Conectar con el servicio Administrador de StorSimple

El servicio Administrador de StorSimple se ejecuta en Microsoft Azure y se conecta a varios dispositivos de StorSimple. Use un portal de Microsoft Azure clásico central ejecutando en un explorador para administrar los dispositivos. Para conectar con el servicio de StorSimple Manager, haga lo siguiente.

#### <a name="to-connect-to-the-service"></a>Conectar con el servicio

1. Vaya a [https://manage.windowsazure.com/](https://manage.windowsazure.com/).

1. Con sus credenciales de cuenta de Microsoft, inicie sesión en el portal de Microsoft Azure clásico (que se encuentra en la parte superior derecha del panel).

1. Desplácese por el panel de navegación izquierdo para obtener acceso al servicio de administrador de StorSimple.


## <a name="navigate-storsimple-manager-service-ui"></a>Desplácese StorSimple Manager servicio interfaz de usuario

La jerarquía de navegación para el servicio Administrador de StorSimple interfaz de usuario se muestra en la tabla siguiente.

- Página de inicio del **Administrador de StorSimple** le lleva a las páginas de nivel de servicio de la interfaz de usuario aplicables a todos los dispositivos de un servicio.

- Página de **dispositivos** le lleva a las páginas de la interfaz de usuario de nivel del dispositivo aplicables a un dispositivo específico.

- Página **Contenedores de volumen** le lleva a la página de volumen que muestra todos los volúmenes asociados con un dispositivo.


#### <a name="storsimple-manager-service-navigational-hierarchy"></a>Jerarquía de navegación de servicio de StorSimple Manager

|Página de inicio|Páginas de nivel de servicio|Páginas de nivel de dispositivo|Páginas de nivel de dispositivo|
|---|---|---|---|
|Servicio de administrador de StorSimple|Paneles de servicio|Panel de dispositivo||
||Dispositivos →|Monitor|
||Catálogo de copia de seguridad|Containers→ de volumen|Volúmenes|
||Configurar (servicio)|Directivas de copia de seguridad||
||Trabajos|Configurar (dispositivo)|
||Alertas|Mantenimiento|

![Vídeo disponible](./media/storsimple-manager-service-administration/Video_icon.png) **vídeo disponible**

Para ver un vídeo que le guiará a través de la interfaz de usuario del servicio de StorSimple Manager, haga clic en [aquí](https://azure.microsoft.com/documentation/videos/storsimple-manager-service-overview/).

## <a name="administer-storsimple-device-using-storsimple-manager-service"></a>Administrar el dispositivo de StorSimple con el servicio de administrador de StorSimple

La siguiente tabla muestra un resumen de todas las tareas comunes de administración y flujos de trabajo complejos que pueden realizarse en el servicio de administrador de StorSimple interfaz de usuario. Estas tareas se organizan en función de las páginas de la interfaz de usuario en el que se inician.

Para obtener más información acerca de cada flujo de trabajo, haga clic en el procedimiento adecuado en la tabla.

#### <a name="storsimple-manager-workflows"></a>Administrador de StorSimple flujos de trabajo

|Si desea hacer esto...|Ir a esta página de la interfaz de usuario...|Use este procedimiento.|
|---|---|---|
|Crear un servicio</br>Eliminar un servicio</br>Obtener la clave de registro de servicio</br>Regenerar la clave de registro de servicio|Servicio de administrador de StorSimple|[Implementar un servicio de administrador de StorSimple](storsimple-manage-service.md)
|Cambiar la clave de cifrado de datos de servicio</br>Ver los registros de operación|Panel de StorSimple → del servicio de administrador|[Usar el panel de servicio de administrador de StorSimple](storsimple-service-dashboard.md)|
|Desactivar un dispositivo</br>Eliminar un dispositivo|Administrador de StorSimple servicio → dispositivos|[Desactivar o eliminar un dispositivo](storsimple-deactivate-and-delete-device.md)|
|Obtenga más información sobre migración tras de error de dispositivo y la recuperación de desastres</br>Migración tras error en un dispositivo físico</br>Migración tras error a un dispositivo virtual</br>Empresa continuidad recuperación (BCDR)|Administrador de StorSimple servicio → dispositivos|[Migración tras error y recuperación de desastres de su dispositivo StorSimple](storsimple-device-failover-disaster-recovery.md)|
|Lista copias de seguridad de un volumen</br>Seleccione un conjunto de copia de seguridad</br>Eliminar un conjunto de copia de seguridad|Catálogo de copia de seguridad de StorSimple → del servicio de administrador|[Administrar las copias de seguridad](storsimple-manage-backup-catalog.md)|
|Clonar un volumen|Catálogo de copia de seguridad de StorSimple → del servicio de administrador|[Clonar un volumen](storsimple-clone-volume.md)|
|Restaurar un conjunto de copia de seguridad|Catálogo de copia de seguridad de StorSimple → del servicio de administrador|[Restaurar un conjunto de copia de seguridad](storsimple-restore-from-backup-set.md)|
|Acerca de las cuentas de almacenamiento</br>Agregar una cuenta de almacenamiento</br>Editar una cuenta de almacenamiento</br>Eliminar una cuenta de almacenamiento</br>Rotación de clave de cuentas de almacenamiento|Administrador de StorSimple servicio → configurar|[Administrar cuentas de almacenamiento](storsimple-manage-storage-accounts.md)|
|Acerca de las plantillas de ancho de banda</br>Agregar una plantilla de ancho de banda</br>Editar una plantilla de ancho de banda</br>Eliminar una plantilla de ancho de banda</br>Usar una plantilla de ancho de banda predeterminada</br>Crear una plantilla de ancho de banda de todo el día que comienza en un momento determinado|Administrador de StorSimple servicio → configurar|[Administrar plantillas de ancho de banda](storsimple-manage-bandwidth-templates.md)|
|Acerca de los registros de control de acceso</br>Crear un registro de control de acceso</br>Editar un registro de control de acceso</br>Eliminar un registro de control de acceso|Administrador de StorSimple servicio → configurar|[Administrar los registros de control de acceso](storsimple-manage-acrs.md)|
|Ver detalles del trabajo</br>Cancelar un trabajo|Administrador de StorSimple servicio → trabajos|[Administrar trabajos](storsimple-manage-jobs.md)
|Recibir notificaciones de alerta</br>Administrar alertas</br>Revise las alertas|Administrador de StorSimple servicio → alertas|[Ver y administrar alertas StorSimple](storsimple-manage-alerts.md)
|Ver iniciadores conectados</br>Buscar el número de serie de dispositivo</br>Encontrar el destino IQN|Administrador de StorSimple servicio → dispositivos → paneles|[Usar el panel de dispositivo StorSimple](storsimple-device-dashboard.md)|
|Crear gráficos de supervisión|Administrador de StorSimple servicio → dispositivos → supervisar|[Supervisar el dispositivo StorSimple](storsimple-monitor-device.md)|
|Agregar un contenedor de volumen</br>Modificar un contenedor de volumen</br>Eliminar un contenedor de volumen|StorSimple Manager servicio → dispositivos → volumen contenedores|[Administrar contenedores de volumen](storsimple-manage-volume-containers.md)|
|Agregar un volumen</br>Modificar un volumen</br>Poner un volumen sin conexión</br>Eliminar un volumen</br>Supervisar un volumen|StorSimple Manager servicio → dispositivos → contenedores de volumen → volúmenes|[Administrar volúmenes](storsimple-manage-volumes.md)|
|Modificar la configuración de dispositivo</br>Modificar la configuración de hora</br>Modificar la configuración de DNS.md</br>Configurar interfaces de red|Dispositivos de StorSimple Manager servicio → configurar →|[Modificar la configuración de dispositivo de su dispositivo StorSimple](storsimple-modify-device-config.md)|
|Ver la configuración de proxy de web|Dispositivos de StorSimple Manager servicio → configurar →|[Configurar a proxy de web de su dispositivo](storsimple-configure-web-proxy.md)|
|Modificar la contraseña de administrador de dispositivos</br>Modificar contraseña de administrador de instantáneas StorSimple|Dispositivos de StorSimple Manager servicio → configurar →|[Cambiar las contraseñas de StorSimple](storsimple-change-passwords.md)|
|Configurar la administración remota|Dispositivos de StorSimple Manager servicio → configurar →|[Conectarse a su dispositivo StorSimple de forma remota](storsimple-remote-connect.md)|
|Configurar las alertas|Dispositivos de StorSimple Manager servicio → configurar →|[Ver y administrar alertas StorSimple](storsimple-manage-alerts.md)|
|Configure el CHAP para el dispositivo StorSimple|Dispositivos de StorSimple Manager servicio → configurar →|[Configure el CHAP para el dispositivo StorSimple](storsimple-configure-chap.md)|
|Agregar una directiva de copia de seguridad</br>Agregar o modificar una programación</br>Eliminar una directiva de copia de seguridad</br>Realice una copia de seguridad manual</br>Crear una directiva de copia de seguridad personalizada con varios volúmenes y programaciones|Directivas de copia de seguridad de administrador de StorSimple servicio → dispositivos →|[Administrar directivas de copia de seguridad](storsimple-manage-backup-policies.md)|
|Detener controladores de dispositivo</br>Reinicie los controladores de dispositivo</br>Apagar controladores de dispositivo</br>Reinicie el dispositivo valores predeterminados</br>(Anteriormente son solamente al dispositivo local)|Administrador de StorSimple servicio → dispositivos → mantenimiento|[Administrar el controlador de dispositivo StorSimple](storsimple-manage-device-controller.md)|
|Obtenga más información sobre componentes de hardware de StorSimple</br>Supervisar el estado de hardware</br>(Anteriormente son solamente al dispositivo local)|Administrador de StorSimple servicio → dispositivos → mantenimiento|[Componentes de hardware del Monitor](storsimple-monitor-hardware-status.md)|
|Crear un paquete de compatibilidad|Administrador de StorSimple servicio → dispositivos → mantenimiento|[Crear y administrar un paquete de compatibilidad](storsimple-create-manage-support-package.md)|
|Instalar actualizaciones de software|Administrador de StorSimple servicio → dispositivos → mantenimiento|[Actualizar el dispositivo](storsimple-update-device.md)|


##<a name="next-steps"></a>Pasos siguientes
Si experimenta problemas con la operación diaria de su dispositivo StorSimple o con cualquiera de sus componentes de hardware, consulte:

- [Solucionar problemas de un dispositivo operativo](storsimple-troubleshoot-operational-device.md)
- [Usar StorSimple LED del indicador de supervisión](storsimple-monitoring-indicators.md)

Si no puede resolver los problemas y que necesita para crear una solicitud de servicio, consulte [Soporte técnico de Microsoft del contacto](storsimple-contact-microsoft-support.md).

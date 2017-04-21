<properties 
   pageTitle="Administración de la matriz Virtual de StorSimple Manager | Microsoft Azure"
   description="Obtenga información sobre cómo administrar la matriz Virtual de StorSimple local mediante el servicio Administrador de StorSimple en el portal de clásico de Azure."
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
   ms.date="10/11/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-administer-your-storsimple-virtual-array"></a>Utilizar el servicio Administrador de StorSimple para administrar la matriz Virtual de StorSimple

![flujo del proceso de instalación](./media/storsimple-ova-manager-service-administration/manage4.png)

## <a name="overview"></a>Información general

En este artículo se describe la interfaz de servicio de StorSimple Manager, incluido cómo conectarse a él y las diversas opciones disponibles y se proporciona vínculos a los flujos de trabajo específicos que se pueden realizar a través de esta interfaz de usuario. 

Después de leer este artículo, sabrá cómo:

- Conectar con el servicio Administrador de StorSimple
- Vaya a la interfaz de usuario del Administrador de StorSimple
- Administrar la matriz Virtual StorSimple a través del servicio de administrador de StorSimple

> [AZURE.NOTE] Para ver las opciones de administración disponibles para el dispositivo de la serie 8000 StorSimple, vaya a [usar el servicio de administrador de StorSimple para administrar el dispositivo de StorSimple](storsimple-manager-service-administration.md).

## <a name="connect-to-the-storsimple-manager-service"></a>Conectar con el servicio Administrador de StorSimple

El servicio Administrador de StorSimple se ejecuta en Microsoft Azure y se conecta a varias matrices Virtual de StorSimple. Use un portal de Microsoft Azure clásico central ejecutando en un explorador para administrar los dispositivos. Para conectar con el servicio de StorSimple Manager, haga lo siguiente.

#### <a name="to-connect-to-the-service"></a>Conectar con el servicio

1. Vaya a [https://manage.windowsazure.com/](https://manage.windowsazure.com/).

2. Con sus credenciales de cuenta de Microsoft, inicie sesión en el portal de Microsoft Azure clásico (que se encuentra en la parte superior derecha del panel).

3. Desplácese por el panel de navegación izquierdo para obtener acceso al servicio de administrador de StorSimple.

    ![Desplácese al servicio](./media/storsimple-ova-manager-service-administration/admin-scroll.png)

## <a name="navigate-the-storsimple-manager-service-ui"></a>Vaya a la interfaz de usuario de servicio de administrador de StorSimple

La jerarquía de navegación para el servicio Administrador de StorSimple interfaz de usuario se muestra en la tabla siguiente.

- La página de inicio del **Administrador de StorSimple** le lleva a las páginas de nivel de servicio de la interfaz de usuario aplicables a todas las matrices Virtual dentro de un servicio.

- La página **dispositivos** le lleva a las páginas de la interfaz de usuario de nivel del dispositivo aplicables a una matriz Virtual específico.

#### <a name="storsimple-manager-service-navigational-hierarchy"></a>Jerarquía de navegación de servicio de StorSimple Manager

|Página de inicio|Páginas de nivel de servicio|Páginas de nivel de dispositivo|
|---|---|---|
|Servicio de administrador de StorSimple|Panel (servicio)|Panel (dispositivo)|
||Dispositivos →|Monitor|
||Catálogo de copia de seguridad|Recursos compartidos (servidor de archivos) o </br>Volúmenes (iSCSI server)|
||Configurar (servicio)|Configurar (dispositivo)|
||Trabajos|Mantenimiento|
||Alertas|

## <a name="use-the-storsimple-manager-service-to-perform-management-tasks"></a>Usar el servicio de administrador de StorSimple para realizar tareas de administración

La siguiente tabla muestra un resumen de todas las tareas comunes de administración y flujos de trabajo complejos que pueden realizarse en el servicio de administrador de StorSimple interfaz de usuario. Estas tareas se organizan en función de las páginas de la interfaz de usuario en el que se inician.

Para obtener más información acerca de cada flujo de trabajo, haga clic en el procedimiento adecuado en la tabla.

#### <a name="storsimple-manager-workflows"></a>Administrador de StorSimple flujos de trabajo

|Si desea hacer esto...|Ir a esta página de la interfaz de usuario...|Use este procedimiento|
|---|---|---|
|Crear un servicio</br>Eliminar un servicio</br>Obtener la clave de registro de servicio</br>Regenerar la clave de registro de servicio|Servicio de administrador de StorSimple|[Implementar el servicio Administrador de StorSimple](storsimple-ova-manage-service.md)|
|Cambiar la clave de cifrado de datos de servicio</br>Ver los registros de operaciones|Panel de StorSimple → del servicio de administrador|[Usar el panel de servicio de StorSimple](storsimple-ova-service-dashboard.md)|
|Desactivar una matriz Virtual</br>Eliminar una matriz Virtual|Administrador de StorSimple servicio → dispositivos|[Desactivar o eliminar una matriz Virtual](storsimple-ova-deactivate-and-delete-device.md)|
|Failover de dispositivos y la recuperación de desastres</br>Requisitos previos de migración tras error</br>Migración tras error a un dispositivo virtual</br>Empresa continuidad recuperación (BCDR)</br>Errores durante la recuperación|Administrador de StorSimple servicio → dispositivos|[Recuperación de errores de desastres dispositivo y recuperación para su matriz Virtual de StorSimple](storsimple-ova-failover-dr.md)|
|Realizar copias de seguridad de volúmenes y recursos compartidos</br>Realice una copia de seguridad manual</br>Cambiar la programación de copia de seguridad</br>Ver las copias de seguridad existentes|Catálogo de copia de seguridad de administrador de StorSimple servicio →|[Realizar copias de seguridad de la matriz Virtual de StorSimple](storsimple-ova-backup.md)|
|Restaurar los recursos compartidos de un conjunto de copia de seguridad</br>Restaurar volúmenes de un conjunto de copia de seguridad</br>Recuperación de nivel de elemento (servidor de archivos solo)|Catálogo de copia de seguridad de StorSimple → del servicio de administrador|[Restaurar a partir de una copia de seguridad de la matriz de StorSimple Virtual.](storsimple-ova-restore.md)|
|Acerca de las cuentas de almacenamiento</br>Agregar una cuenta de almacenamiento</br>Editar una cuenta de almacenamiento</br>Eliminar una cuenta de almacenamiento|Administrador de StorSimple servicio → configurar|[Administrar cuentas de almacenamiento para la matriz Virtual StorSimple](storsimple-ova-manage-storage-accounts.md)|
|Acerca de los registros de control de acceso</br>Agregar o modificar un registro de control de acceso </br>Eliminar un registro de control de acceso|Administrador de StorSimple servicio → configurar|[Administrar los registros de control de acceso de la matriz Virtual StorSimple](storsimple-ova-manage-acrs.md)|
|Ver detalles del trabajo|Administrador de StorSimple servicio → trabajos| [Administrar trabajos de matriz Virtual de StorSimple](storsimple-ova-manage-jobs.md)|
|Configurar las alertas</br>Recibir notificaciones de alerta</br>Administrar alertas</br>Revise las alertas|Administrador de StorSimple servicio → alertas|[Ver y administrar alertas para la matriz Virtual StorSimple](storsimple-ova-manage-alerts.md)|
|Modificar la contraseña de administrador de dispositivos|Dispositivos de StorSimple Manager servicio → configurar →|[Cambiar la contraseña de administrador del dispositivo StorSimple Virtual matriz](storsimple-ova-change-device-admin-password.md)|
|Instalar actualizaciones de software|Administrador de StorSimple servicio → dispositivos → mantenimiento|[Actualizar la matriz Virtual](storsimple-ova-install-update-01.md)|

>[AZURE.NOTE] Debe usar la [interfaz de usuario de web local](storsimple-ova-web-ui-admin.md) para las siguientes tareas:
>
>- [Recuperar la clave de cifrado de datos de servicio](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)
>- [Crear un paquete de compatibilidad](storsimple-ova-web-ui-admin.md#generate-a-log-package)
>- [Detener y reiniciar una matriz Virtual](storsimple-ova-web-ui-admin.md#shut-down-and-restart-your-device)

##<a name="next-steps"></a>Pasos siguientes
Para que obtener información acerca de la interfaz de usuario de web y cómo usarla, vaya a [usar la interfaz de usuario para administrar la matriz Virtual de StorSimple de web StorSimple](storsimple-ova-web-ui-admin.md).

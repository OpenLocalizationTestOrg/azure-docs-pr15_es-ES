<properties 
   pageTitle="Dispositivo virtual de StorSimple actualización 2 | Microsoft Azure"
   description="Aprenda a crear, implementar y administrar un dispositivo virtual de StorSimple en una red virtual de Microsoft Azure. (Se aplica a la actualización de StorSimple 2)."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/23/2016"
   ms.author="alkohli" />

# <a name="deploy-and-manage-a-storsimple-virtual-device-in-azure"></a>Implementar y administrar un dispositivo virtual StorSimple en Azure


##<a name="overview"></a>Información general
El dispositivo virtual de serie 8000 StorSimple es una capacidad adicional que se incluye con la solución de Microsoft Azure StorSimple. El dispositivo virtual StorSimple se ejecuta en una máquina virtual en una red virtual de Microsoft Azure y puede usar para realizar copias de seguridad y clonar datos de su host. Este tutorial describe cómo implementar y administrar un dispositivo virtual en Azure y es aplicable a todos los dispositivos virtuales ejecutando una versión 2 de actualización de software e inferior.


#### <a name="virtual-device-model-comparison"></a>Comparación del modelo de dispositivo virtual

El dispositivo virtual StorSimple está disponible en dos modelos, un 8010 estándar (anteriormente conocida como la 1100) y una prima 8020 (introducida en actualización 2). Una comparación de los dos modelos se tabular debajo.


| Modelo de dispositivo          | 8010<sup>1</sup>                                                                     | 8020                                                                                                                               |
|-----------------------|---------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|
| **Capacidad máxima**      | 30 TB                                                                     | 64 TB                                                                                                                                |
| **Azure VM**              | Standard_A3 (4 núcleos, 7 GB de memoria)                                                                      | Standard_DS3 (4 núcleos, 14 GB de memoria)                                                                                                                          |
| **Compatibilidad de versiones** | Versiones ejecutando previamente actualización 2 o posterior                                             | Versiones con la actualización 2 o posterior                                                                                                  |
| **Disponibilidad de región**   | Todas las áreas de Azure                                                         | Azure regiones que admiten el almacenamiento de Premium<br></br>Para obtener una lista de regiones, vea [admitidos regiones para 8020](#supported-regions-for-8020) |
| **Tipo de almacenamiento**          | Usa el almacenamiento de Azure estándar para discos locales<br></br> Obtenga información sobre cómo [crear una cuenta de almacenamiento estándar]() | Usa el almacenamiento de Azure Premium para discos locales<sup>2</sup> <br></br>Obtenga información sobre cómo [crear una cuenta de almacenamiento Premium](storage-premium-storage.md#create-and-use-a-premium-storage-account-for-a-virtual-machine-data-disk)                                                               |
| **Guía de carga de trabajo**     | Recuperación de nivel de elemento de archivos de copias de seguridad                                              | Desarrollo de la nube y probar escenarios, baja latencia, mayores cargas de trabajo de rendimiento <br></br>Dispositivo secundario para recuperación                                                                                            |
 
<sup>1</sup> *anteriormente conocida como la 1100*.

<sup>2</sup> *la 8010 y 8020 usar el almacenamiento de Azure estándar para el nivel de la nube. La diferencia sólo existe en el nivel local en el dispositivo*.

#### <a name="supported-regions-for-8020"></a>Áreas compatibles para 8020

Las áreas de almacenamiento de Premium que actualmente se admiten para 8020 se hayan publicado por debajo. Esta lista se actualizará continuamente como almacenamiento Premium esté disponible en más regiones. 

| S. No.                                                  | En estos momentos en regiones |
|---------------------------------------------------------|--------------------------------|
| 1                                                       | Central de EE.                     |
| 2                                                       |  Estados Unidos oriental                       |
| 3                                                       |  Estados Unidos oriental 2                     |
| 4                                                       | Estados Unidos occidental                        |
| 5                                                       | Europa del Norte                   |
| 6                                                       | Europa occidental                    |
| 7                                                       | Sudeste asiático                 |
| 8                                                       | Japón oriental                     |
| 9                                                       | Japón oeste                     |
| 10                                                      | Australia Oriental                 |
| 11                                                      | Australia sureste *           |
| 12                                                      | Asia oriental *                     |
| 13                                                      | Sur Central US *              |

* Almacenamiento de premium se inició recientemente en estas zonas.

En este artículo se describe el proceso paso a paso de implementación de un dispositivo virtual StorSimple en Azure. Después de leer este artículo, podrá:

- Comprender cómo el dispositivo virtual difiere del dispositivo físico.

- Podrá crear y configurar el dispositivo virtual.

- Conectar con el dispositivo virtual.

- Obtenga información sobre cómo trabajar con el dispositivo virtual.

En este tutorial se aplica a todos los dispositivos virtuales StorSimple ejecutar Update 2 o superior. 

## <a name="how-the-virtual-device-differs-from-the-physical-device"></a>El dispositivo virtual las diferencias entre el dispositivo físico

El dispositivo virtual de StorSimple es una versión de software de StorSimple que se ejecute en un único nodo en una máquina Virtual de Microsoft Azure. El dispositivo virtual es compatible con la recuperación de desastres escenarios en los que el dispositivo físico no está disponible y es adecuado para su uso en la recuperación de nivel de elemento de las copias de seguridad, recuperación local y escenarios de prueba y de desarrollo de nube.

#### <a name="differences-from-the-physical-device"></a>Diferencias del dispositivo físico

La siguiente tabla muestran algunas diferencias clave entre el dispositivo virtual de StorSimple y el dispositivo físico StorSimple.

|                             | Dispositivo físico                                          | Dispositivo virtual                                                                            |
|-----------------------------|----------------------------------------------------------|-------------------------------------------------------------------------------------------|
| **Ubicación**                    | Reside en el centro de datos.                               | Se ejecuta en Azure.                                                                            |
| **Interfaces de red**          | Tiene seis interfaces de red: datos de 0 a 5 de datos.                  | Tiene solo una interfaz de red: datos 0.                                        |
| **Registro**                | Registrado durante el paso de configuración.                | Registro es una tarea diferente.                                                          |
| **Clave de cifrado de datos de servicio** | Volver a crear en el dispositivo físico y después actualice el dispositivo virtual con la clave nueva.           | No puede volver a crear desde el dispositivo virtual. |


## <a name="prerequisites-for-the-virtual-device"></a>Requisitos previos para el dispositivo virtual

Las siguientes secciones explican los requisitos previos de configuración de su dispositivo virtual de StorSimple. Antes de implementar un dispositivo virtual, revise las [Consideraciones de seguridad para usar un dispositivo virtual](storsimple-security.md#storsimple-virtual-device-security).

#### <a name="azure-requirements"></a>Requisitos de Azure

Antes de aprovisionar el dispositivo virtual, debe realizar los siguientes preparativos en su entorno de Azure:

- Para el dispositivo virtual, [Configurar una red virtual en Azure](../virtual-network/virtual-networks-create-vnet-classic-portal.md). Si usa almacenamiento Premium, debe crear una red virtual en un área de Azure que admite el almacenamiento de Premium. Más información sobre [las áreas que actualmente se admiten para 8020](#supported-regions-for-8020).
- Es aconsejable usar el servidor DNS predeterminado proporcionado por Azure en lugar de especificar su propio nombre de servidor DNS. Si el nombre del servidor DNS no es válido o si el servidor DNS no puede resolver direcciones IP correctamente, se producirá un error en la creación del dispositivo virtual.
- Punto al sitio y a sitios son opcionales, pero no es necesario. Si lo desea, puede configurar estas opciones para escenarios más avanzados. 
- Puede crear [máquinas virtuales de Azure](../virtual-machines/virtual-machines-linux-about.md) (servidores de host) en la red virtual que puede usar los volúmenes expuestos por el dispositivo virtual. Estos servidores deben cumplir los requisitos siguientes:                            
    - Ser Windows o máquinas virtuales de Linux con iSCSI software iniciador instalado.
    - Ejecutar en la misma red virtual como el dispositivo virtual.
    - Podrá conectar con el destino iSCSI del dispositivo virtual a través de la dirección IP interna del dispositivo virtual.

- Asegúrese de que ha configurado el soporte técnico para el tráfico iSCSI y la nube en la misma red virtual.


#### <a name="storsimple-requirements"></a>Requisitos de StorSimple

Realice las siguientes actualizaciones en su servicio de Azure StorSimple antes de crear un dispositivo virtual:


- Agregar [registros de control de acceso](storsimple-manage-acrs.md) para las máquinas virtuales que van a ser servidores de host de su dispositivo virtual.

- Usar una [cuenta de almacenamiento](storsimple-manage-storage-accounts.md#add-a-storage-account) en la misma región como el dispositivo virtual. Cuentas de almacenamiento en las distintas regiones pueden traducirse en un rendimiento deficiente. Puede usar una cuenta estándar o almacenamiento Premium con el dispositivo virtual. Para obtener más información sobre cómo crear una [cuenta de almacenamiento estándar]() o una [cuenta de almacenamiento Premium](storage-premium-storage.md#create-and-use-a-premium-storage-account-for-a-virtual-machine-data-disk)

- Usar una cuenta de almacenamiento diferente para la creación de dispositivo virtual de utilizado para sus datos. Usar la misma cuenta de almacenamiento puede traducirse en un rendimiento deficiente.

Asegúrese de que tiene la información siguiente antes de comenzar:

- Su cuenta de portal clásica Azure con credenciales de acceso.

- Una copia de la clave de cifrado de datos de servicio desde el dispositivo físico.


## <a name="create-and-configure-the-virtual-device"></a>Crear y configurar el dispositivo virtual

Antes de realizar estos procedimientos, asegúrese de que cumple los [requisitos previos para el dispositivo virtual](#prerequisites-for-the-virtual-device). 

Después de que creó una red virtual, configuró un servicio de administrador de StorSimple y registrado su dispositivo StorSimple físico con el servicio, puede usar los siguientes pasos para crear y configurar un dispositivo virtual de StorSimple. 

### <a name="step-1-create-a-virtual-device"></a>Paso 1: Crear un dispositivo virtual

Realice los pasos siguientes para crear el dispositivo virtual StorSimple.

[AZURE.INCLUDE [Create a virtual device](../../includes/storsimple-create-virtual-device-u2.md)]

Si se produce un error en la creación del dispositivo virtual en este paso, no tenga conectividad a Internet. Para obtener más información, vaya a [solucionar problemas de conectividad de Internet](#troubleshoot-internet-connectivity-errors) cuando creatig un dispositivo virtual.


### <a name="step-2-configure-and-register-the-virtual-device"></a>Paso 2: Configurar y registrar el dispositivo virtual

Antes de comenzar este procedimiento, asegúrese de que tiene una copia de la clave de cifrado de datos de servicio. La clave de cifrado de datos de servicio se creó al configurar su dispositivo StorSimple primera y que se indique a guardar en una ubicación segura. Si no tiene una copia de la clave de cifrado de datos de servicio, deberá ponerse en contacto con Microsoft Support para obtener ayuda.

Siga estos pasos para configurar y registrar su dispositivo virtual de StorSimple.
[AZURE.INCLUDE [Configure and register a virtual device](../../includes/storsimple-configure-register-virtual-device.md)]

### <a name="step-3-optional-modify-the-device-configuration-settings"></a>Paso 3: (Opcional) modificar la configuración de dispositivo

En la sección siguiente describe las opciones de configuración de dispositivo necesarios para el StorSimple dispositivo virtual si desea usar CHAP, Administrador de instantáneas StorSimple o cambiar la contraseña de administrador del dispositivo.

#### <a name="configure-the-chap-initiator"></a>Configurar el iniciador CHAP

Este parámetro contiene las credenciales de su dispositivo virtual (destino) que se espera de iniciadores (servidores) que están intentando tener acceso a los volúmenes. Los iniciadores le proporcionará un nombre de usuario CHAP y una contraseña CHAP a identificarse en el dispositivo durante esta autenticación. Para conocer los pasos detallados, vaya a [Configurar la configuración de su dispositivo](storsimple-configure-chap.md#unidirectional-or-one-way-authentication).

#### <a name="configure-the-chap-target"></a>Configurar el destino CHAP

Este parámetro contiene las credenciales de su dispositivo virtual usa cuando un iniciador CHAP habilitado solicita autenticación mutua o bidireccional. El dispositivo virtual usará CHAP invertir el nombre de usuario y contraseña invertir CHAP para identificar al iniciador durante el proceso de autenticación. Observe que la configuración de CHAP de destino es la configuración global. Cuando se aplica, todos los volúmenes conectados al dispositivo virtual de almacenamiento usará autenticación CHAP. Para conocer los pasos detallados, vaya a [Configurar la configuración de su dispositivo](storsimple-configure-chap.md#bidirectional-or-mutual-authentication).

#### <a name="configure-the-storsimple-snapshot-manager-password"></a>Configurar la contraseña de administrador de instantáneas StorSimple

Software de administrador de instantáneas StorSimple reside en el host de Windows y permite a los administradores administrar las copias de seguridad de su dispositivo StorSimple en forma de local y la nube instantáneas.

>[AZURE.NOTE] El dispositivo virtual, el host de Windows es una máquina virtual Azure.

Al configurar un dispositivo en el Administrador de instantáneas de StorSimple, le pedirá que proporcione la dirección IP del dispositivo StorSimple y la contraseña para autenticar el dispositivo de almacenamiento. Para conocer los pasos detallados, vaya a [Configurar el Administrador de instantánea StorSimple contraseña](storsimple-change-passwords.md#change-the-storsimple-snapshot-manager-password).

#### <a name="change-the-device-administrator-password"></a>Cambiar la contraseña de administrador de dispositivos 

Cuando utiliza la interfaz de Windows PowerShell para tener acceso al dispositivo virtual, se le pedirá que escriba una contraseña de administrador de dispositivo. Para la seguridad de los datos, es necesario cambiar la contraseña antes de poder usar el dispositivo virtual. Para conocer los pasos detallados, vaya a [Configurar contraseña de administrador de dispositivos](storsimple-change-passwords.md#change-the-device-administrator-password).

## <a name="connect-remotely-to-the-virtual-device"></a>Conectarse al dispositivo virtual de forma remota
Acceso remoto al dispositivo virtual a través de la interfaz de Windows PowerShell no está habilitado de forma predeterminada. Debe habilitar la gestión remota en el dispositivo virtual en primer lugar y, después, habilitarlo en el cliente que se usará para acceder a su dispositivo virtual.

A continuación se detalla el proceso de dos pasos para conectar de forma remota.

### <a name="step-1-configure-remote-management"></a>Paso 1: Configurar la administración remota

Siga estos pasos para configurar la administración remota para su dispositivo virtual de StorSimple.

[AZURE.INCLUDE [Configure remote management via HTTP for virtual device](../../includes/storsimple-configure-remote-management-http-device.md)]

### <a name="step-2-remotely-access-the-virtual-device"></a>Paso 2: Obtener acceso remoto al dispositivo virtual

Después de habilitar la gestión remota en la página de configuración de dispositivo de StorSimple, puede usar Windows PowerShell remoto para conectar el dispositivo virtual desde otra máquina virtual dentro de la misma red virtual. Por ejemplo, puede conectar el host VM que configurado y utilizado para conectar iSCSI. En la mayoría de las implementaciones, ya habrá abierto un extremo público para tener acceso a su host VM que puede usar para tener acceso al dispositivo virtual.

>[AZURE.WARNING] **Para mejorar la seguridad, se recomienda encarecidamente que utilizar HTTPS al conectar con los extremos y, a continuación, elimine los extremos después de completar la sesión remota de PowerShell.**

Debe seguir los procedimientos en [conectar remotamente con su dispositivo StorSimple](storsimple-remote-connect.md) para configurar el acceso remoto de su dispositivo virtual.

## <a name="connect-directly-to-the-virtual-device"></a>Conectar directamente al dispositivo virtual

También puede conectar directamente al dispositivo virtual. Si desea conectarse directamente al dispositivo virtual desde otro equipo fuera de la red virtual o fuera del entorno de Microsoft Azure, debe crear extremos adicionales como se describe en el procedimiento siguiente. 

Realice los pasos siguientes para crear un extremo público en el dispositivo virtual.

[AZURE.INCLUDE [Create public endpoints on a virtual device](../../includes/storsimple-create-public-endpoints-virtual-device.md)]

Le recomendamos que se conecte desde otro equipo virtual dentro de la misma red virtual porque esta práctica minimiza el número de puntos finales de públicos en su red virtual. Cuando utiliza este método, que simplemente conectarse a la máquina virtual a través de una sesión de escritorio remoto y a continuación, configure la máquina virtual para su uso como lo haría con cualquier otro cliente de Windows en una red local. No es necesario agregar el número de puerto público porque ya se le conoce el puerto.

## <a name="work-with-the-storsimple-virtual-device"></a>Trabajar con el dispositivo virtual StorSimple

Ahora que ha creado y configurado el dispositivo virtual StorSimple, está listo para comenzar a trabajar con él. Puede trabajar con contenedores de volumen, volúmenes y directivas de copia de seguridad en un dispositivo virtual como lo haría en un dispositivo StorSimple físico; la única diferencia es que necesita para asegurarse de que selecciona el dispositivo virtual de la lista de dispositivos. Consulte [usar el servicio de administrador de StorSimple para administrar un dispositivo virtual](storsimple-manager-service-administration.md) de los procedimientos paso a paso de las distintas tareas de administración para el dispositivo virtual.

Las secciones siguientes describen algunas de las diferencias que encontrará cuando trabaje con el dispositivo virtual.

### <a name="maintain-a-storsimple-virtual-device"></a>Mantener un dispositivo virtual de StorSimple

Dado que es un dispositivo de software, mantenimiento del dispositivo virtual es mínima cuando se comparan con mantenimiento para el dispositivo físico. Tiene las siguientes opciones:

- **Actualizaciones de software** : puede ver la fecha en que se actualizó por última vez el software, junto con cualquier actualización de los mensajes de estado. Puede usar el botón **Examinar actualizaciones** en la parte inferior de la página para realizar un análisis manual si desea buscar actualizaciones nuevas. Si hay actualizaciones disponibles, haga clic en **Instalar actualizaciones** para instalar. Porque hay sólo una única interfaz en el dispositivo virtual, esto significa que habrá una interrupción de servicio ligera cuando se aplican las actualizaciones. El dispositivo virtual se cierre y reinicie (si es necesario) para aplicar las actualizaciones que se han publicado. Para obtener un procedimiento paso a paso, vaya a [actualizar el dispositivo](storsimple-update-device.md#install-regular-updates-via-the-azure-classic-portal).
- **Paquete de soporte técnico** : puede crear y cargar un paquete de soporte técnico para ayudar a Microsoft Support solucionar problemas con el dispositivo virtual. Para obtener un procedimiento paso a paso, vaya a [crear y administrar un paquete de compatibilidad](storsimple-create-manage-support-package.md).

### <a name="storage-accounts-for-a-virtual-device"></a>Cuentas de almacenamiento de un dispositivo virtual

Cuentas de almacenamiento se crean para usar el servicio Administrador de StorSimple, el dispositivo virtual y el dispositivo físico. Cuando cree las cuentas de almacenamiento, se recomienda usar un identificador de región en el nombre descriptivo para ayudar a asegurar que la región es coherente en todos los componentes del sistema. Para un dispositivo virtual, es importante que todos los componentes de estar en la misma región para evitar problemas de rendimiento.

Para obtener un procedimiento paso a paso, vaya a [Agregar una cuenta de almacenamiento](storsimple-manage-storage-accounts.md#add-a-storage-account).

### <a name="deactivate-a-storsimple-virtual-device"></a>Desactivar un dispositivo virtual de StorSimple

Desactivar un dispositivo virtual elimina la máquina virtual y los recursos que se crean cuando se ha configurado. Después de que el dispositivo virtual se desactiva, no se puede restaurar a su estado anterior. Antes de desactivar el dispositivo virtual, asegúrese de detener o eliminar clientes y hosts que dependen de él.

Desactivar un dispositivo virtual de resultados de las siguientes acciones:

- Se quita el dispositivo virtual.

- El disco de sistema operativo y los discos de datos creados para el dispositivo virtual se quitan.

- El servicio hospedado y creados durante la configuración de red virtual se conservan. Si no está utilizando ellos, debe eliminarlos manualmente.

- Se conservan instantáneas de nube creadas para el dispositivo virtual.

Para obtener un procedimiento paso a paso, vaya a [desactivar y eliminar el dispositivo de StorSimple](storsimple-deactivate-and-delete-device.md).

Tan pronto como se muestra el dispositivo virtual como desactivados en la página de servicio de StorSimple administrador, puede eliminar el dispositivo virtual de la lista de dispositivos en la página de **dispositivos** .


### <a name="start-stop-and-restart-a-virtual-device"></a>Iniciar, detener y reiniciar el dispositivo virtual
A diferencia de dispositivo físico StorSimple, no hay ningún power activado o power desactivar el botón de inserción en un dispositivo virtual de StorSimple. Sin embargo, puede haber ocasiones dónde debe detener y reiniciar el dispositivo virtual. Por ejemplo, algunas actualizaciones pueden requerir que se reinicie la máquina virtual para terminar el proceso de actualización. La manera más sencilla para iniciar, detener y reiniciar un dispositivo virtual es utilizar la consola de administración de máquinas virtuales de Windows.

Cuando examine la consola de administración, el estado del dispositivo virtual está **ejecutando** porque se inicie de forma predeterminada, después de crearlo. Puede iniciar, detener y reiniciar una máquina virtual en cualquier momento.

[AZURE.INCLUDE [Stop and restart virtual device](../../includes/storsimple-stop-restart-virtual-device.md)]

### <a name="reset-to-factory-defaults"></a>Restablecer valores predeterminados

Si decide que desea volver a empezar con el dispositivo virtual, simplemente desactivar y elimínelo y, a continuación, cree uno nuevo. Al igual que cuando se restablece el dispositivo físico, el nuevo dispositivo virtual no tendrán las actualizaciones instaladas; por lo tanto, asegúrese de comprobar si hay actualizaciones antes de usarla.


## <a name="fail-over-to-the-virtual-device"></a>Conmutar el dispositivo virtual

Recuperación (DR) es uno de los escenarios clave que el dispositivo virtual StorSimple está diseñado para. En este escenario, el dispositivo StorSimple físico o un centro de datos completo podría no estar disponible. Afortunadamente, puede usar un dispositivo virtual para restaurar las operaciones en una ubicación alternativa. Durante la DR, los contenedores de volumen desde el dispositivo de origen cambiar la propiedad y se transfieren al dispositivo virtual. Los requisitos previos para DR son que se ha creado y configurado el dispositivo virtual, todos los volúmenes dentro del contenedor de volumen se han desconectado y el contenedor de volumen tiene asociada una instantánea de la nube.

>[AZURE.NOTE] 
>
> - Cuando usa un dispositivo virtual como el dispositivo secundario para DR, tenga en cuenta que la 8010 tiene 30 TB de almacenamiento estándar y 8020 64 TB de almacenamiento de Premium. El dispositivo virtual de mayor capacidad 8020 puede ser más adecuado para un escenario de DR.
> - No puede usar la migración tras error o clonar desde un dispositivo ejecuta 2 a un dispositivo anteriores a la actualización de software 1. Sin embargo puede conmutar un dispositivo ejecuta 2 en un dispositivo con Update 1 (1.1 o 1.2)

Para obtener un procedimiento paso a paso, vaya a la [migración tras error a un dispositivo virtual](storsimple-device-failover-disaster-recovery.md#fail-over-to-a-storsimple-virtual-device).
 

## <a name="shut-down-or-delete-the-virtual-device"></a>Cerrar o eliminar el dispositivo virtual

Si previamente configurado y usa un StorSimple dispositivo virtual pero ahora desea dejar de acumulan cargos de cálculo para su uso, puede cerrar el dispositivo virtual. Apagar el dispositivo virtual no elimina su sistema operativo o los discos de datos de almacenamiento. Dejar de cargo acumulado en la suscripción, pero seguirán cargos de almacenamiento de los discos del sistema operativo y datos.

Si elimina o apagar el dispositivo virtual, aparecerá como **sin conexión** en la página de dispositivos del servicio de administrador de StorSimple. Puede desactivar o eliminar el dispositivo si desea eliminar las copias de seguridad creadas por el dispositivo virtual. Para obtener más información, vea [desactivar y eliminar un dispositivo StorSimple](storsimple-deactivate-and-delete-device.md).

[AZURE.INCLUDE [Shut down a virtual device](../../includes/storsimple-shutdown-virtual-device.md)]

[AZURE.INCLUDE [Delete a virtual device](../../includes/storsimple-delete-virtual-device.md)]

   
## <a name="troubleshoot-internet-connectivity-errors"></a>Solucionar problemas de conectividad de Internet 

Durante la creación de un dispositivo virtual, si no hay ninguna conexión a Internet, el paso de creación se producirá un error. Para solucionar problemas si el error es debido a la conexión a Internet, siga estos pasos en el portal de clásico Azure:

1. Crear una máquina virtual de Windows server 2012 en Azure. Esta máquina virtual debe usar la misma cuenta de almacenamiento, VNet y subred que usa el dispositivo virtual. Si ya tiene un host de Windows Server existente en Azure con la misma cuenta de almacenamiento, Vnet y subred, también puede usar para solucionar problemas de conectividad de Internet.
2. Registro remoto en la máquina virtual que creó en el paso anterior. 
3. Abra una ventana de comandos en la máquina virtual (Win + R y luego escriba `cmd`).
4. Ejecute el siguiente cmd en el símbolo del sistema.

    `nslookup windows.net`

5. Si `nslookup` falla, a continuación, falla de conectividad de Internet impide que el dispositivo virtual se registren para el servicio Administrador de StorSimple. 
6. Realice los cambios necesarios a su red virtual para garantizar que el dispositivo virtual puedo tener acceso a sitios Azure como "windows.net".

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre cómo [usar el servicio de administrador de StorSimple para administrar un dispositivo virtual](storsimple-manager-service-administration.md).
 
- Comprender cómo [restaurar un volumen StorSimple de un conjunto de copia de seguridad](storsimple-restore-from-backup-set.md). 


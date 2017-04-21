<properties 
   pageTitle="Implementar el dispositivo StorSimple (actualización 2) | Microsoft Azure"
   description="Describe los pasos y procedimientos recomendados para implementar el servicio y el dispositivo de actualización de StorSimple 2."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/24/2016"
   ms.author="alkohli" />

# <a name="deploy-your-on-premises-storsimple-device-update-2"></a>Implementar el dispositivo de StorSimple local (actualización 2)

> [AZURE.SELECTOR]
- [Actualización 2 y versiones posterior](../articles/storsimple/storsimple-deployment-walkthrough-u2.md)
- [Actualización 1](../articles/storsimple/storsimple-deployment-walkthrough-u1.md)
- [Versión GA](../articles/storsimple/storsimple-deployment-walkthrough.md)

## <a name="overview"></a>Información general

Bienvenido a implementación de dispositivos de Microsoft Azure StorSimple. Estos tutoriales de implementación que se aplican a StorSimple 8000 Series Update 2. Esta serie de tutoriales incluye una lista de comprobación de configuración, los requisitos previos de configuración y los pasos de configuración detalladas para su dispositivo StorSimple.

La información en estos tutoriales se supone que tienen revisan las precauciones de seguridad y desempaquetado, bastidor y con el cable de su dispositivo StorSimple. Si necesita realizar dichas tareas, empiece por revisar las [precauciones](storsimple-safety.md). Siga las instrucciones específicas de dispositivo descomprimir, montaje de bastidores y cable de su dispositivo.

- [Descomprimir, montaje de bastidores y cable su 8100](storsimple-8100-hardware-installation.md)
- [Descomprimir, montaje de bastidores y cable su 8600](storsimple-8600-hardware-installation.md)

Necesitará privilegios de administrador para completar el proceso de instalación y configuración. Le recomendamos que revise la lista de comprobación de configuración antes de empezar. El proceso de implementación y configuración puede tardar algún tiempo en completarse.

> [AZURE.NOTE] La información de implementación de StorSimple publicada en el sitio Web de Microsoft Azure se aplica a los dispositivos de la serie 8000 StorSimple solo. Para obtener información acerca de los dispositivos de 7000 serie, vaya a: [http://onlinehelp.storsimple.com/](http://onlinehelp.storsimple.com). Para obtener información de implementación de serie de 7000, consulte la [Guía de inicio rápido de StorSimple sistema](http://onlinehelp.storsimple.com/111_Appliance/).

## <a name="deployment-steps"></a>Pasos de implementación

Siga estos pasos necesarios para configurar el dispositivo de StorSimple y conectarse a su servicio de administrador de StorSimple. Además de los pasos necesarios, hay pasos opcionales y es posible que tenga durante la implementación de procedimientos. Las instrucciones paso a paso de implementación indican cuándo debe llevar a cabo cada uno de estos pasos opcionales.


| Paso                                                                                   | Descripción                                                                                                                                                   |
|----------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **REQUISITOS PREVIOS**                                                                      | Estos deben realizarse en preparación para la implementación próxima.                                                                                        |
| [Lista de comprobación de configuración de implementación](#deployment-configuration-checklist)                                                     | Use esta lista de comprobación para recopilar y registrar información antes y durante la implementación.                                                                       |
| [Requisitos previos de implementación](#deployment-prerequisites)                                                               | Estos validación el entorno está listo para su implementación.                                                                                                     |
|                                                                                        |                                                                                                                                                               |
| **IMPLEMENTACIÓN PASO A PASO**                                                                   | Estos pasos son necesarios para implementar el dispositivo StorSimple en producción.                                                                                      |
| [Paso 1: Crear un nuevo servicio](#step-1-create-a-new-service)                                                         | Configurar el almacenamiento de su dispositivo StorSimple y la administración de la nube. *Omitir este paso si tiene un servicio existente para otros dispositivos StorSimple*.                |
| [Paso 2: Obtener la clave de registro de servicio](#step-2-get-the-service-registration-key)                                               | Use esta clave para registrar y conectar el dispositivo StorSimple con el servicio de administración.                                                                         |
| [Paso 3: Configurar y registrar el dispositivo a través de Windows PowerShell para StorSimple](#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)    | Conecte el dispositivo a su red y registrar con Azure para completar la configuración usando el servicio de administración.                                            |
| [Paso 4: Completar la configuración de dispositivo mínimos](#step-4-complete-minimum-device-setupd)</br>[Opcional: Actualizar su dispositivo StorSimple](#scan-for-and-apply-updates)      | Usar el servicio de administración para completar la configuración de dispositivo y le permiten proporcionar almacenamiento.                                                                      |
| [Paso 5: Crear un contenedor de volumen](#step-5-create-a-volume-container)                                                      | Crear un contenedor para volúmenes de aprovisionamiento. Un contenedor de volumen tiene cuenta de almacenamiento, ancho de banda y configuración de cifrado para todos los volúmenes contenidos en ella.    |
| [Paso 6: Crear un volumen](#step-6-create-a-volume)                                                                | Aprovisionar volúmenes de almacenamiento en el dispositivo StorSimple para los servidores.                                                                                        |
| [Paso 7: Montaje, inicializar y dar formato a un volumen](#step-7-mount-initialize-and-format-a-volume)</br>[Opcional: Configurar MPIO](storsimple-configure-mpio-windows-server.md)            | Conecte los servidores al almacenamiento iSCSI proporcionado por el dispositivo. También puede configurar MPIO para asegurarse de que los servidores pueden tolerar vínculo, la red y la interfaz de error.                                                                                                                                                              |
| [Paso 8: Realizar una copia de seguridad](#step-8-take-a-backup)                                                                  | Configurar la directiva de copia de seguridad para proteger los datos                                                                                                                 |
|                                                                                        |                                                                                                                                                               |
| **OTROS PROCEDIMIENTOS**                                                                   | Debe hacer referencia a estos procedimientos como implementar la solución.                                                                                        |
| [Configurar una nueva cuenta de almacenamiento para el servicio](#configure-a-new-storage-account-for-the-service)                                      |                                                                                                                                                               |
| [Usar PuTTY para conectarse a la consola de serie de dispositivo](#use-putty-to-connect-to-the-device-serial-console)                                    |                                                                                                                                                               |
| [Obtener el IQN de un host de Windows Server](#get-the-iqn-of-a-windows-server-host)                                                   |                                                                                                                                                               |
| [Crear una copia de seguridad manual](#create-a-manual-backup)                                                                 | 


## <a name="deployment-configuration-checklist"></a>Lista de comprobación de configuración de implementación

Antes de implementar el dispositivo, debe recopilar información para configurar el software en el dispositivo StorSimple. Parte de esta información antes de tiempo preparación le ayudará a simplificar el proceso de implementación del dispositivo de StorSimple en su entorno. Descargar y utilizar esta lista de comprobación para anote los detalles de configuración como implementa el dispositivo.

- [Descargar la lista de comprobación de configuración de implementación de StorSimple](http://www.microsoft.com/download/details.aspx?id=49159)


## <a name="deployment-prerequisites"></a>Requisitos previos de implementación

Las siguientes secciones explican los requisitos previos de configuración del servicio de administrador de StorSimple y el dispositivo de StorSimple.

### <a name="for-the-storsimple-manager-service"></a>Para el servicio Administrador de StorSimple

Antes de empezar, asegúrese de:

- Tiene su cuenta de Microsoft con las credenciales de acceso.

- Tiene su cuenta de almacenamiento de Microsoft Azure con credenciales de acceso.

- Su suscripción de Microsoft Azure está habilitada para el servicio Administrador de StorSimple. Debe compró su suscripción a través del [Contrato Enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/).

- Tener acceso a software de emulación de terminal como PuTTY.

### <a name="for-the-device-in-the-datacenter"></a>Para el dispositivo en el centro de datos

Antes de configurar el dispositivo, asegúrese de que el dispositivo está totalmente desempaquetó, montaje en un bastidor y totalmente cableado para power, la red y acceso serie tal como se describe en:

-  [Descomprimir, montaje de bastidores y cable de su dispositivo 8100](storsimple-8100-hardware-installation.md)
-  [Descomprimir, montaje de bastidores y cable de su dispositivo 8600](storsimple-8600-hardware-installation.md)


### <a name="for-the-network-in-the-datacenter"></a>Para la red en el centro de datos

Antes de empezar, asegúrese de:

- Se abren los puertos en el centro de datos y el firewall para permitir para iSCSI y tráfico como se describe en [redes requisitos para su dispositivo StorSimple](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device)en la nube.


## <a name="step-by-step-deployment"></a>Implementación paso a paso

Utilice las siguientes instrucciones paso a paso para implementar el dispositivo StorSimple en el centro de datos.

## <a name="step-1-create-a-new-service"></a>Paso 1: Crear un nuevo servicio

Un servicio de StorSimple administrador puede administrar varios dispositivos StorSimple. Realice los pasos siguientes para crear una nueva instancia del servicio de administrador de StorSimple.

[AZURE.INCLUDE [storsimple-create-new-service](../../includes/storsimple-create-new-service.md)]

> [AZURE.IMPORTANT] Si no habilitó la creación automática de una cuenta de almacenamiento con el servicio, debe crear al menos una cuenta de almacenamiento después de haber creado correctamente un servicio. Esta cuenta de almacenamiento se utilizará cuando se crea un contenedor de volumen. 
>
> * Si no ha creado una cuenta de almacenamiento automáticamente, vaya a [Configurar una nueva cuenta de almacenamiento para el servicio](#configure-a-new-storage-account-for-the-service) para obtener instrucciones detalladas. 
> * Si habilita la creación automática de una cuenta de almacenamiento, vaya a [paso 2: obtener la clave de registro del servicio](#step-2-get-the-service-registration-key).

## <a name="step-2-get-the-service-registration-key"></a>Paso 2: Obtener la clave de registro de servicio

Después de configurar y ejecutar el servicio Administrador de StorSimple, debe obtener la clave de registro de servicio. Esta clave se usa para registrar y conectar el dispositivo de StorSimple con el servicio.

Realice los pasos siguientes en el Portal de administración.

[AZURE.INCLUDE [storsimple-get-service-registration-key](../../includes/storsimple-get-service-registration-key.md)]


## <a name="step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple"></a>Paso 3: Configurar y registrar el dispositivo a través de Windows PowerShell para StorSimple

Usar Windows PowerShell para StorSimple para completar la configuración inicial del dispositivo StorSimple como se explica en el procedimiento siguiente. Debe utilizar software de emulación de terminal para completar este paso. Para obtener más información, vea [Usar PuTTY para conectarse a la consola de serie de dispositivo](#use-putty-to-connect-to-the-device-serial-console).

[AZURE.INCLUDE [storsimple-configure-and-register-device-u1](../../includes/storsimple-configure-and-register-device-u1.md)]

## <a name="step-4-complete-minimum-device-setup"></a>Paso 4: Completar la configuración de dispositivo mínimos

Para la configuración de dispositivo mínimos del dispositivo StorSimple, es necesario que: 

- Configurar el servidor DNS secundario.
- Habilitar iSCSI en al menos una interfaz de red.
- Asignar direcciones IP fijas a los controladores de ambos.

Realice los pasos siguientes en el Portal de administración para completar la configuración de dispositivo mínimos.

[AZURE.INCLUDE [storsimple-complete-minimum-device-setup](../../includes/storsimple-complete-minimum-device-setup-u1.md)]

## <a name="step-5-create-a-volume-container"></a>Paso 5: Crear un contenedor de volumen

Un contenedor de volumen tiene cuenta de almacenamiento, ancho de banda y configuración de cifrado para todos los volúmenes contenidos en ella. Debe crear un contenedor de volumen antes de empezar el aprovisionamiento de volúmenes en el dispositivo StorSimple. 

Realice los pasos siguientes en el Portal de administración para crear un contenedor de volumen.

[AZURE.INCLUDE [storsimple-create-volume-container](../../includes/storsimple-create-volume-container.md)]

## <a name="step-6-create-a-volume"></a>Paso 6: Crear un volumen

Después de crear un contenedor de volumen, puede aprovisionar un volumen de almacenamiento en el dispositivo StorSimple para los servidores. Realice los pasos siguientes en el Portal de administración para crear un volumen.

> [AZURE.IMPORTANT] Administrador de StorSimple puede crear ambos fina y totalmente aprovisionados volúmenes. Sin embargo, no puede crear volúmenes aprovisionados parcialmente. 

[AZURE.INCLUDE [storsimple-create-volume](../../includes/storsimple-create-volume-u2.md)]

## <a name="step-7-mount-initialize-and-format-a-volume"></a>Paso 7: Montaje, inicializar y dar formato a un volumen

Los siguientes pasos se realizan en el host de Windows Server. 


> [AZURE.IMPORTANT]

> - Para la alta disponibilidad de la solución StorSimple, le recomendamos que configure MPIO en los servidores de host (opcionales) antes de configurar iSCSI. Configuración MPIO en servidores host se asegurará de que los servidores pueden tolerar un vínculo, la red o el error de la interfaz.

> - Para MPIO y iSCSI instalación y configuración de las instrucciones de host del servidor de Windows, vaya a [Configurar MPIO para su dispositivo StorSimple](storsimple-configure-mpio-windows-server.md). También incluirá los pasos para montaje, inicializar y dar formato a volúmenes de StorSimple.

> - Para MPIO e iSCSI instalación y configuración de instrucciones en un host Linux, vaya a [Configurar MPIO para su host StorSimple Linux](storsimple-configure-mpio-on-linux.md)

Si decide no configurar MPIO, realice los pasos siguientes para montaje, inicializar y dar formato a los volúmenes StorSimple en un host de Windows Server.

[AZURE.INCLUDE [storsimple-mount-initialize-format-volume](../../includes/storsimple-mount-initialize-format-volume.md)]

## <a name="step-8-take-a-backup"></a>Paso 8: Realizar una copia de seguridad

Realizar copias de seguridad proporcionan protección de punto en el tiempo de volúmenes y mejorar la capacidad de recuperación minimizando tiempos de restauración. Puede realizar dos tipos de copia de seguridad en el dispositivo StorSimple: instantáneas locales y copias instantáneas de la nube. Cada uno de estos tipos de copia de seguridad puede ser **programada** o **Manual**. 

Realice los pasos siguientes en el Portal de administración para crear una copia de seguridad programada.

[AZURE.INCLUDE [storsimple-take-backup](../../includes/storsimple-take-backup.md)]

Puede realizar una copia de seguridad manual en cualquier momento. Para obtener información, vaya a [crear una copia de seguridad manual](#create-a-manual-backup). 

## <a name="configure-a-new-storage-account-for-the-service"></a>Configurar una nueva cuenta de almacenamiento para el servicio

Se trata de un paso opcional que debe llevar a cabo sólo si no habilitó la creación automática de una cuenta de almacenamiento con el servicio. Se requiere una cuenta de almacenamiento de Microsoft Azure para crear un contenedor de volumen StorSimple.

Si necesita crear una cuenta de almacenamiento de Azure en una región diferente, consulte [Acerca de cuentas de almacenamiento de Azure](../storage/storage-create-storage-account.md) para obtener instrucciones paso a paso.

Realice los pasos siguientes en el Portal de administración, en la página **Administrador de StorSimple servicio** .

[AZURE.INCLUDE [storsimple-configure-new-storage-account-u1](../../includes/storsimple-configure-new-storage-account-u1.md)]


## <a name="use-putty-to-connect-to-the-device-serial-console"></a>Usar PuTTY para conectarse a la consola de serie de dispositivo

Para conectarse a Windows PowerShell para StorSimple, debe usar el software de emulación de terminal como PuTTY. Puede usar PuTTY al acceder al dispositivo directamente a través de la consola serie o al abrir una sesión de telnet desde un equipo remoto.

[AZURE.INCLUDE [Use PuTTY to connect to the device serial console](../../includes/storsimple-use-putty.md)]


## <a name="scan-for-and-apply-updates"></a>Buscar y aplicar actualizaciones

Actualizar el dispositivo puede tardar varias horas. Siga estos pasos para buscar y aplicar actualizaciones en su dispositivo.
<!--can take 1-4 hours--> 

<!--If you have a gateway configured on a network interface other than Data 0, you will need to disable Data 2 and Data 3 network interfaces before installing the update. Go to **Devices > Configure** and disable Data 2 and Data 3 interfaces. You should re-enable these interfaces after the device is updated.-->

#### <a name="to-update-your-device"></a>Para actualizar el dispositivo

1.  En la página de **Inicio rápido** de dispositivos, haga clic en **dispositivos**. Seleccione el dispositivo físico, haga clic en **Mantenimiento** y, a continuación, haga clic en **Examinar actualizaciones**.  

2.  Se crea una tarea para buscar actualizaciones disponibles. Si hay actualizaciones disponibles, las **Actualizaciones de análisis** cambia a **Instalar actualizaciones**. Haga clic en **instalar actualizaciones**. 

3.  Se creará un trabajo de actualización. Supervisar el estado de la actualización, vaya a **tareas**.

    > [AZURE.NOTE] Cuando se inicia el trabajo de actualización, inmediatamente muestra el estado como 50 por ciento. El estado cambia a 100 por cien solo después de completar el trabajo de actualización. No hay ningún estado en tiempo real para el proceso de actualización.

4.  Después de que el dispositivo se actualiza correctamente, habilitar interfaces de red de datos 2 y 3 de datos si estas se habían deshabilitadas.

<!-- In step 2, you may be requested to disable Data 2 and Data 3 prior to installing the updates. You must disable these network interfaces or the updates may fail.-->

## <a name="get-the-iqn-of-a-windows-server-host"></a>Obtener el IQN de un host de Windows Server

Realice los pasos siguientes para obtener el iSCSI nombre completo (IQN) de un host de Windows que se está ejecutando Windows Server® 2012.

[AZURE.INCLUDE [Create a manual backup](../../includes/storsimple-get-iqn.md)]

## <a name="create-a-manual-backup"></a>Crear una copia de seguridad manual

Realice los pasos siguientes en el Portal de administración para crear una copia de seguridad manual a petición un único volumen en el dispositivo StorSimple.

[AZURE.INCLUDE [Create a manual backup](../../includes/storsimple-create-manual-backup.md)]


## <a name="next-steps"></a>Pasos siguientes

- Configurar un [dispositivo virtual](storsimple-virtual-device-u2.md).

- Usar el [servicio Administrador de StorSimple](storsimple-manager-service-administration.md) para administrar el dispositivo de StorSimple.
 

<properties
   pageTitle="Implementar un dispositivo de StorSimple local | Microsoft Azure"
   description="Describe los pasos y procedimientos recomendados para implementar el servicio y el dispositivo de StorSimple. (Se aplica a Microsoft Azure StorSimple versión.3 y versiones anteriores.)"
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
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="deploy-your-on-premises-storsimple-device"></a>Implementar el dispositivo de StorSimple local

> [AZURE.SELECTOR]
- [Actualización 2](../articles/storsimple/storsimple-deployment-walkthrough-u2.md)
- [Actualización 1](../articles/storsimple/storsimple-deployment-walkthrough-u1.md)
- [Versión GA](../articles/storsimple/storsimple-deployment-walkthrough.md)

## <a name="overview"></a>Información general

Bienvenido a implementación de dispositivos de Microsoft Azure StorSimple. Estos tutoriales de implementación que aplican a StorSimple 8000 Series versión, actualización de la serie StorSimple 8000 0,1, actualización de la serie 8000 StorSimple 0,2 y actualización de la serie StorSimple 8000 0,3. Esta serie de tutoriales describe cómo configurar el dispositivo de StorSimple e incluye una lista de comprobación de configuración, los requisitos previos de configuración y configuración detallada pasos.


La información en estos tutoriales se supone que tienen revisan las precauciones de seguridad y desempaquetado, bastidor y con el cable de su dispositivo StorSimple. Si necesita realizar dichas tareas, empiece por revisar las [precauciones](storsimple-safety.md). Dependiendo del modelo de su dispositivo, a continuación, puede descomprimir, montaje en bastidor y cable siguiendo las instrucciones en:

- [Descomprimir, montaje de bastidores y cable su 8100](storsimple-8100-hardware-installation.md)
- [Descomprimir, montaje de bastidores y cable su 8600](storsimple-8600-hardware-installation.md)

Necesitará privilegios de administrador para completar el proceso de instalación y configuración. Le recomendamos que revise la lista de comprobación de configuración antes de empezar. El proceso de implementación y configuración puede tardar algún tiempo en completarse.

> [AZURE.NOTE] La información de implementación de StorSimple publicada en el sitio Web de Microsoft Azure se aplica a los dispositivos de la serie 8000 StorSimple solo. Para obtener información acerca de los dispositivos de serie 5000 y 7000, vaya a: [http://onlinehelp.storsimple.com/](http://onlinehelp.storsimple.com). Información de implementación de las series 5000 y 7000, consulte la [Guía de inicio rápido de StorSimple sistema](http://onlinehelp.storsimple.com/111_Appliance/).

## <a name="deployment-steps"></a>Pasos de implementación

Siga estos pasos necesarios para configurar el dispositivo de StorSimple y conectarse a su servicio de administrador de StorSimple. Además de los pasos necesarios, hay pasos opcionales y es posible que tenga durante la implementación de procedimientos. Las instrucciones paso a paso de implementación indican cuándo debe llevar a cabo cada uno de estos pasos opcionales.


| Paso                                                                                   | Descripción                                                                                                                                                   |
|----------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **REQUISITOS PREVIOS**                                                                      | Estos deben realizarse en preparación para la implementación próxima.                                                                                        |
| Lista de comprobación de configuración de implementación.                                                     | Use esta lista de comprobación para recopilar y registrar información antes y durante la implementación.                                                                       |
| Requisitos previos de implementación.                                                               | Estos validación el entorno está listo para su implementación.                                                                                                     |
|                                                                                        |                                                                                                                                                               |
| **IMPLEMENTACIÓN PASO A PASO**                                                                   | Estos pasos son necesarios para implementar el dispositivo StorSimple en producción.                                                                                      |
| Paso 1: Crear un nuevo servicio.                                                         | Configurar el almacenamiento de su dispositivo StorSimple y la administración de la nube. Omitir este paso si tiene un servicio existente para otros dispositivos StorSimple.                |
| Paso 2: Obtener la clave de registro de servicio.                                               | Use esta clave para registrar y conectar el dispositivo StorSimple con el servicio de administración.                                                                         |
| Paso 3: Configurar y registrar el dispositivo a través de Windows PowerShell para StorSimple.    | Conecte el dispositivo a su red y registrar con Azure para completar la configuración usando el servicio de administración.                                            |
| Paso 4: Completar la configuración de dispositivo mínimos</br>Opcional: Actualizar su dispositivo StorSimple.      | Usar el servicio de administración para completar la configuración de dispositivo y le permiten proporcionar almacenamiento.                                                                      |
| Paso 5: Crear un contenedor de volumen.                                                      | Crear un contenedor para volúmenes de aprovisionamiento. Un contenedor de volumen tiene cuenta de almacenamiento, ancho de banda y configuración de cifrado para todos los volúmenes contenidos en ella.    |
| Paso 6: Crear un volumen.                                                                | Aprovisionar volúmenes de almacenamiento en el dispositivo StorSimple para los servidores.                                                                                        |
| Paso 7: Montaje, inicializar y dar formato a un volumen.</br>Opcional: Configurar MPIO.            | Conecte los servidores al almacenamiento iSCSI proporcionado por el dispositivo. También puede configurar MPIO para asegurarse de que los servidores pueden tolerar vínculo, la red y el error de la interfaz.                                                                                                                                                              |
| Paso 8: Realizar una copia de seguridad.                                                                  | Configurar la directiva de copia de seguridad para proteger los datos                                                                                                                 |
|                                                                                        |                                                                                                                                                               |
| **OTROS PROCEDIMIENTOS**                                                                   | Debe hacer referencia a estos procedimientos como implementar la solución.                                                                                        |
| Configurar una nueva cuenta de almacenamiento para el servicio.                                      |                                                                                                                                                               |
| Usar PuTTY para conectarse a la consola de serie de dispositivo.                                    |                                                                                                                                                               |
| Obtener el IQN de un host de Windows Server.                                                   |                                                                                                                                                               |
| Crear una copia de seguridad manual.                                                                 |


## <a name="deployment-configuration-checklist"></a>Lista de comprobación de configuración de implementación

La siguiente lista de comprobación de configuración de implementación describe la información que necesita para recopilar antes y después de configurar el software en el dispositivo StorSimple. Parte de esta información antes de tiempo preparación le ayudará a simplificar el proceso de implementación del dispositivo de StorSimple en su entorno. Use esta lista de comprobación para también anote los detalles de configuración, tal y como se implementa el dispositivo.

| Fase                                  | Parámetro                                         | Detalles                                                                                                                                                                | Valores |
|----------------------------------------|---------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------|
| **Cable de su dispositivo**                      | Acceso serie                                     | Configuración inicial del dispositivo                                                                  | Sí/No |
|   |   |  |  |
| **Configurar y registrar dispositivo**          | Configuración de la red de datos 0                           | Dirección IP de datos 0:</br>Máscara de subred:</br>Puerta de enlace:</br>Servidor DNS principal:</br>Servidor NTP primario:</br>Servidor proxy de Web IP/FQDN (opcional):</br>Puerto del servidor proxy de Web:|        |
|                                        | Contraseña de administrador de dispositivos                     | Contraseña debe tener entre 8 y 15 caracteres que contiene minúsculas, mayúsculas, numéricos y caracteres especiales. |        |
|                                        | Contraseña de administrador de instantáneas StorSimple              | Contraseña debe tener 14 o 15 caracteres que contiene minúsculas, mayúsculas, numéricos y caracteres especiales.|        |
|                                        | Clave de registro de servicio                          | Esta clave se genera desde el portal de clásico de Azure.    |        |
|                                        | Clave de cifrado de datos de servicio                       | Esta clave se crea cuando el dispositivo está registrado con el servicio de administración a través de Windows PowerShell para StorSimple. Copie esta clave y guárdelo en una ubicación segura.|  |
|   |   |  |  |
| **Configuración de dispositivo mínimo completo**          | Nombre descriptivo para el dispositivo                     | Se trata de un nombre descriptivo para el dispositivo. |        |
|                                        | Zona horaria                                          | El dispositivo usará esta zona horaria para todas las operaciones programadas.  |        |
|                                        | Servidor DNS secundario                              | Esta es una configuración necesaria.                                  |        |
|                                        | Interfaz de red: controlador de datos 0 fijo direcciones IP                                     | Estos IP debería enrutables a Internet.</br>Controlador 0 fijo IP dirección:</br>Controlador de 1 fijo IP dirección:|
|   |   |  |  |
| **Configuración de la interfaz de red adicionales**  | Interfaz de red: datos 1</br>Si está habilitado iSCSI, no configure la puerta de enlace.      | Propósito: ISCSI de nube o no usa</br>Dirección IP:</br>Máscara de subred:</br>Puerta de enlace:|
|                                        | Interfaz de red: datos 2</br>Si está habilitado iSCSI, no configure la puerta de enlace.      | Propósito: ISCSI de nube o no usa</br>Dirección IP:</br>Máscara de subred:</br>Puerta de enlace:|
|                                        | Interfaz de red: datos 3</br>Si está habilitado iSCSI, no configure la puerta de enlace.      | Propósito: ISCSI de nube o no usa</br>Dirección IP:</br>Máscara de subred:</br>Puerta de enlace:|
|                                        | Interfaz de red: datos 4</br>Si está habilitado iSCSI, no configure la puerta de enlace.      | Propósito: ISCSI de nube o no usa</br>Dirección IP:</br>Máscara de subred:</br>Puerta de enlace:|
|                                        | Interfaz de red: datos 5</br>Si está habilitado iSCSI, no configure la puerta de enlace.      | Propósito: ISCSI de nube o no usa</br>Dirección IP:</br>Máscara de subred:</br>Puerta de enlace:|
|   |   |  |  |
| **Crear un contenedor de volumen**                      | Nombre del contenedor de volumen:                            | Nombre para el contenedor                                                                                                                                                 |        |
|                                        | Cuenta de almacenamiento de Azure:                            | Clave de acceso y nombre de cuenta de almacenamiento para asociar con este contenedor de volumen                                                                                              |        |
|                                        | Clave de cifrado de almacenamiento de nube:                     | Clave de cifrado de almacenamiento en cada contenedor                                                                                                                           |        |
|   |   |  |  |
| **Crear un volumen**                        | Detalles para cada volumen                           | Nombre de volumen:                                                                                                                                                           |        |
|                                        |                                                   | Tamaño:                                                                                                                                                                  |        |
|                                        |                                                   | Tipo de uso:                                                                                                                                                            |        |
|                                        |                                                   | Nombre ACR:                                                                                                                                                              |        |
|                                        |                                                   | Directiva de copia de seguridad predeterminada:                                                                                                                                                 |        |
|   |   |  |  |
| **Montaje, inicializar y dar formato a un volumen** | Detalles para cada servidor host conectarse al almacenamiento de información | Nombre del servidor de Windows:                                                                                                                                                   |        |
|                                        |                                                   | Windows Server IQN:                                                                                                                                                    |        |
|                                        |                                                   | Nombre de volumen de Windows Server:                                                                                                                                                   |        |
|                                        |                                                   | NTFS montaje punto letra:                                                                                                                                      |        |

## <a name="deployment-prerequisites"></a>Requisitos previos de implementación

Las siguientes secciones explican los requisitos previos de configuración del servicio de administrador de StorSimple, el dispositivo de StorSimple y la red de su centro de datos.

### <a name="for-the-storsimple-manager-service"></a>Para el servicio Administrador de StorSimple

Antes de empezar, asegúrese de:

- Tiene su cuenta de Microsoft con las credenciales de acceso.

- Tiene su cuenta de almacenamiento de Microsoft Azure con credenciales de acceso.

- Su suscripción de Microsoft Azure está habilitada para el servicio Administrador de StorSimple. Debe compró su suscripción a través del [Contrato Enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/).

- Tener acceso a software de emulación de terminal como PuTTY.

### <a name="for-the-device-in-the-datacenter"></a>Para el dispositivo en el centro de datos

Antes de configurar el dispositivo, asegúrese de:

- El dispositivo es totalmente desempaquetó, montaje en un bastidor y cableado totalmente para power, la red y el acceso de serie, como se describe en:

    -  [Descomprimir, montaje de bastidores y cable de su dispositivo 8100](storsimple-8100-hardware-installation.md)
    -  [Descomprimir, montaje de bastidores y cable de su dispositivo 8600](storsimple-8600-hardware-installation.md)


### <a name="for-the-network-in-the-datacenter"></a>Para la red en el centro de datos

Antes de empezar, asegúrese de:

- Se abren los puertos en el centro de datos y el firewall para permitir para iSCSI y tráfico como se describe en [redes requisitos para su dispositivo StorSimple](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device)en la nube.
- El dispositivo en el centro de datos puede conectarse a la red externa. Ejecute los siguientes cmdlets de [Windows PowerShell 4.0](http://www.microsoft.com/download/details.aspx?id=40855) (tabulados a continuación) para validar la conectividad a la red externa. Validar en un equipo (en la red del centro de datos) que tiene conectividad a Azure y donde va a implementar el dispositivo StorSimple.  

| Para este parámetro...       | Para comprobar la validez...                                                                                                                                                                                | Ejecute los cmdlets de comandos.                                                                                                                                                             |
|---------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **IP**</br>**Subred**</br>**Puerta de enlace** | ¿Es una dirección IPv4 o IPv6 válida?</br>¿Es una subred válida?</br>¿Es una puerta de enlace válida?</br>¿Es una IP duplicada en red?                                                                          | `ping ip`</br>`arp -a`</br>La `ping` y `arp` comandos deberían producir un error que indica que no hay ningún dispositivo en la red de centro de datos que está usando esta dirección IP.
|                           |                                                                                                                                                                    |                                                                                                                                                                                         |
| **DNS**                       | ¿Esto es un DNS válido y puede resolver direcciones URL de Azure?                                                                                                                    | `Resolve-DnsName -Name www.bing.com -Server <DNS server IP address>` </br>Es un comando alternativo que se pueden usar:</br>`nslookup --dns-ip=<DNS server IP address> www.bing.com`      |
|                           | Comprobar si el puerto 53 está abierto. Esto solo es aplicable si está utilizando un DNS externo para su dispositivo. DNS interno debe resolver automáticamente las direcciones URL externas.  | `Test-Port -comp dc1 -port 53 -udp -UDPtimeout 10000`  </br>[Obtener más información sobre este cmdlet](http://learn-powershell.net/2011/02/21/querying-udp-ports-with-powershell/)|
|                           |                                                                                                                                                                    |                                                                                                                                                                                         |
| **NTP**                       | Nos desencadenar una sincronización de tiempo tan pronto como se introduce servidor NTP. Puerto UDP comprobar 123 está abierto cuando escribe `time.windows.com` o servidores horarios públicos). | [Descargar y usar esta secuencia de comandos](https://gallery.technet.microsoft.com/scriptcenter/Get-Network-NTP-Time-with-07b216ca).                                                                                                                                                           |
|                           |                                                                                                                                                                    |                                                                                                                                                                                         |
| **Proxy (opcional)**          | ¿Esto es un proxy válido URI y puerto? </br> ¿Es el modo de autenticación correcta?                                                                                                                                | <code>wget http://bing.com &#124; % {$_.StatusCode}</code></br>Este comando se debe ejecutar inmediatamente después de la configuración de proxy de web. Si se devuelve un código de estado de 200, indica que la conexión es correcta.                                                                                                                                 |
|                           | ¿Es el tráfico enrutables a través de proxy?                                                                                                                                 | Ejecute la validación de DNS, NTP activar o HTTP una vez después de configurar el servidor proxy en su dispositivo. Esto le dará una imagen borrar si se bloquea el tráfico al servidor proxy o cualquier otro sitio.                                                                                                                      |
|                           |                                                                                                                                                                    |                                                                                                                                                                                         |
| **Registro**              | Compruebe si salidos puertos TCP 443, 80, 9354 están abiertos.                                                                                                                |  `Test-NetConnection -Port   443 -InformationLevel Detailed`</br>[Más información para probar NetConnection cmdlet](https://technet.microsoft.com/library/dn372891.aspx)                                                                           |

## <a name="step-by-step-deployment"></a>Implementación paso a paso

Utilice las siguientes instrucciones paso a paso para implementar el dispositivo StorSimple en el centro de datos.

## <a name="step-1-create-a-new-service"></a>Paso 1: Crear un nuevo servicio

Un servicio de StorSimple administrador puede administrar varios dispositivos StorSimple. Implementación del primer dispositivo StorSimple, debe crear un nuevo servicio de administrador de StorSimple.

> [AZURE.IMPORTANT] Omitir este paso si tiene un servicio de StorSimple administrador existente y que desee distribuir el dispositivo StorSimple con dicho servicio.

Realice los pasos siguientes para crear una nueva instancia del servicio de administrador de StorSimple.

[AZURE.INCLUDE [storsimple-create-new-service](../../includes/storsimple-create-new-service.md)]

> [AZURE.IMPORTANT] Si no habilitó la creación automática de una cuenta de almacenamiento con el servicio, debe crear al menos una cuenta de almacenamiento después de haber creado correctamente un servicio. Esta cuenta de almacenamiento se utilizará cuando se crea un contenedor de volumen.
>
> Si no ha creado una cuenta de almacenamiento automáticamente, vaya a [Configurar una nueva cuenta de almacenamiento para el servicio](#configure-a-new-storage-account-for-the-service) para obtener instrucciones detalladas.
> Si habilita la creación automática de una cuenta de almacenamiento, vaya a [paso 2: obtener la clave de registro del servicio](#step-2:-get-the-service-registration-key).

## <a name="step-2-get-the-service-registration-key"></a>Paso 2: Obtener la clave de registro de servicio

Después de configurar y ejecutar el servicio Administrador de StorSimple, debe obtener la clave de registro de servicio. Esta clave se usa para registrar y conectar el dispositivo de StorSimple con el servicio.

Realice los pasos siguientes en el portal de clásico de Azure.

[AZURE.INCLUDE [storsimple-get-service-registration-key](../../includes/storsimple-get-service-registration-key.md)]


## <a name="step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple"></a>Paso 3: Configurar y registrar el dispositivo a través de Windows PowerShell para StorSimple

> [AZURE.IMPORTANT] Antes de realizar esta configuración, desconecte todas las interfaces de red que no sean datos 0 en (activo y pasivo) los controladores.

Usar Windows PowerShell para StorSimple para completar la configuración inicial del dispositivo StorSimple como se explica en el procedimiento siguiente. Debe utilizar software de emulación de terminal para completar este paso. Para obtener más información, vea [Usar PuTTY para conectarse a la consola de serie de dispositivo](#use-putty-to-connect-to-the-device-serial-console).

[AZURE.INCLUDE [storsimple-configure-and-register-device](../../includes/storsimple-configure-and-register-device.md)]

## <a name="step-4-complete-minimum-device-setup"></a>Paso 4: Completar la configuración de dispositivo mínimos

Para la configuración de dispositivo mínimos del dispositivo StorSimple, es necesario que:

- Configurar el servidor DNS secundario.
- Habilitar iSCSI en al menos una interfaz de red.
- Asignar direcciones IP fijas a los controladores de ambos.

Realice los pasos siguientes en el portal de Azure clásico para completar la configuración de dispositivo mínimos.

[AZURE.INCLUDE [storsimple-complete-minimum-device-setup](../../includes/storsimple-complete-minimum-device-setup.md)]

Una vez completada la configuración del dispositivo, debe buscar actualizaciones y si está disponible, instalar actualizaciones. Las actualizaciones pueden tardar varias horas en completarse. Siga las instrucciones en [Buscar y aplicar actualizaciones](#scan-for-and-apply-updates).


## <a name="step-5-create-a-volume-container"></a>Paso 5: Crear un contenedor de volumen

Un contenedor de volumen tiene cuenta de almacenamiento, ancho de banda y configuración de cifrado para todos los volúmenes contenidos en ella. Debe crear un contenedor de volumen antes de empezar el aprovisionamiento de volúmenes en el dispositivo StorSimple.

Realice los pasos siguientes en el portal de Azure clásico para crear un contenedor de volumen.

[AZURE.INCLUDE [storsimple-create-volume-container](../../includes/storsimple-create-volume-container.md)]

## <a name="step-6-create-a-volume"></a>Paso 6: Crear un volumen

Después de crear un contenedor de volumen, puede aprovisionar un volumen de almacenamiento en el dispositivo StorSimple para los servidores. Realice los pasos siguientes en el portal de Azure clásico para crear un volumen.

> [AZURE.IMPORTANT] Administrador de StorSimple puede crear volúmenes de thin aprovisionados solo.  No puede crear volúmenes aprovisionados completa o parcial.

[AZURE.INCLUDE [storsimple-create-volume](../../includes/storsimple-create-volume.md)]

## <a name="step-7-mount-initialize-and-format-a-volume"></a>Paso 7: Montaje, inicializar y dar formato a un volumen

> [AZURE.IMPORTANT]

> - Para la alta disponibilidad de la solución StorSimple, le recomendamos que configure MPIO en el host de Windows Server (opcional) antes de configurar iSCSI en su host de Windows Server. Configuración MPIO en servidores host se asegurará de que los servidores pueden tolerar un vínculo, la red o el error de la interfaz.

> - Para MPIO e iSCSI instrucciones de instalación y configuración, vaya a [Configurar MPIO para su dispositivo StorSimple](storsimple-configure-mpio-windows-server.md). También incluirá los pasos para montaje, inicializar y dar formato a volúmenes de StorSimple.

Si decide no configurar MPIO, realice los pasos siguientes para montaje, inicializar y dar formato a los volúmenes StorSimple.

[AZURE.INCLUDE [storsimple-mount-initialize-format-volume](../../includes/storsimple-mount-initialize-format-volume.md)]

## <a name="step-8-take-a-backup"></a>Paso 8: Realizar una copia de seguridad

Realizar copias de seguridad proporcionan protección de punto en el tiempo de volúmenes y mejorar la capacidad de recuperación minimizando tiempos de restauración. Puede realizar dos tipos de copia de seguridad en el dispositivo StorSimple: instantáneas locales y copias instantáneas de la nube. Cada uno de estos tipos de copia de seguridad puede ser **programada** o **Manual**.

Realice los pasos siguientes en el portal de Azure clásico para crear una copia de seguridad programada.

[AZURE.INCLUDE [storsimple-take-backup](../../includes/storsimple-take-backup.md)]

Puede realizar una copia de seguridad manual en cualquier momento. Para obtener información, vaya a [crear una copia de seguridad manual](#Create-a-manual-backup).

## <a name="configure-a-new-storage-account-for-the-service"></a>Configurar una nueva cuenta de almacenamiento para el servicio

Se trata de un paso opcional que debe llevar a cabo sólo si no habilitó la creación automática de una cuenta de almacenamiento con el servicio. Se requiere una cuenta de almacenamiento de Microsoft Azure para crear un contenedor de volumen StorSimple.

Si necesita crear una cuenta de almacenamiento de Azure en una región diferente, consulte [Acerca de cuentas de almacenamiento de Azure](../storage/storage-create-storage-account.md) para obtener instrucciones paso a paso.

Realice los pasos siguientes en el portal de clásico Azure, en la página **Administrador de StorSimple servicio** .

[AZURE.INCLUDE [storsimple-configure-new-storage-account](../../includes/storsimple-configure-new-storage-account.md)]


## <a name="use-putty-to-connect-to-the-device-serial-console"></a>Usar PuTTY para conectarse a la consola de serie de dispositivo

Para conectarse a Windows PowerShell para StorSimple, debe usar el software de emulación de terminal como PuTTY. Puede usar PuTTY al acceder al dispositivo directamente a través de la consola serie o al abrir una sesión de telnet desde un equipo remoto.

[AZURE.INCLUDE [Use PuTTY to connect to the device serial console](../../includes/storsimple-use-putty.md)]

## <a name="scan-for-and-apply-updates"></a>Buscar y aplicar actualizaciones

Actualizar el dispositivo puede tardar entre 1 y 4 horas. Siga estos pasos para buscar y aplicar actualizaciones en su dispositivo.

> [AZURE.NOTE] Si tiene una puerta de enlace configurado en una interfaz de red que no sean datos 0, debe deshabilitar las interfaces de red de datos 2 y 3 de datos antes de instalar la actualización. Vaya a **dispositivos > Configurar** y deshabilitar las interfaces de datos 2 y 3 de datos. Deberá volver a habilitar estas interfaces después de actualiza el dispositivo.

#### <a name="to-update-your-device"></a>Para actualizar el dispositivo
1.  En la página de **Inicio rápido** de dispositivos, haga clic en **dispositivos**. Seleccione el dispositivo físico, haga clic en **Mantenimiento** y, a continuación, haga clic en **Examinar actualizaciones**.  
2.  Se crea una tarea para buscar actualizaciones disponibles. Si hay actualizaciones disponibles, las **Actualizaciones de análisis** cambia a **Instalar actualizaciones**. Haga clic en **instalar actualizaciones**. Se le pide, para deshabilitar datos 2 y 3 de datos antes de instalar las actualizaciones. Debe desactivar estas interfaces de red o pueden fallar las actualizaciones.
3.  Se creará un trabajo de actualización. Supervisar el estado de la actualización, vaya a **tareas**.

    > [AZURE.NOTE] Cuando se inicia el trabajo de actualización, inmediatamente muestra el estado como 50 por ciento. A continuación, el estado cambia al 100 por cien solo después de completar el trabajo de actualización. No hay ningún estado en tiempo real para el proceso de actualizaciones.

4.  Después de que el dispositivo se actualiza correctamente, habilitar interfaces de red de datos 2 y 3 de datos si estas se habían deshabilitadas.



## <a name="get-the-iqn-of-a-windows-server-host"></a>Obtener el IQN de un host de Windows Server

Realice los pasos siguientes para obtener el iSCSI nombre completo (IQN) de un host de Windows que se está ejecutando Windows Server 2012.

[AZURE.INCLUDE [Create a manual backup](../../includes/storsimple-get-iqn.md)]


## <a name="create-a-manual-backup"></a>Crear una copia de seguridad manual

Realice los pasos siguientes en el portal de Azure clásico para crear una copia de seguridad manual a petición un único volumen en el dispositivo StorSimple.

[AZURE.INCLUDE [Create a manual backup](../../includes/storsimple-create-manual-backup.md)]


## <a name="next-steps"></a>Pasos siguientes

- Configurar un [dispositivo virtual](storsimple-virtual-device-u2.md).

- Usar el [servicio Administrador de StorSimple](https://msdn.microsoft.com/library/azure/dn772396.aspx) para administrar el dispositivo de StorSimple.

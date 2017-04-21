<properties 
   pageTitle="Seguridad StorSimple | Microsoft Azure" 
   description="Describe las características de seguridad y privacidad que protegen su servicio StorSimple, dispositivo y datos de forma local y en la nube." 
   services="storsimple" 
   documentationCenter="NA" 
   authors="SharS" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="05/03/2016"
   ms.author="v-sharos"/>

# <a name="storsimple-security-and-data-protection"></a>StorSimple seguridad y protección de datos

## <a name="overview"></a>Información general

La seguridad es más importante para cualquier persona que está adoptando una nueva tecnología, especialmente cuando se utiliza la tecnología con datos confidenciales o propios. Evaluar tecnologías diferentes, debe considerar mayores riesgos y costos de protección de datos. Microsoft Azure StorSimple proporciona tanto una solución de seguridad y privacidad de protección de datos, lo que ayuda a garantizar: 

- **Confidencialidad** : solo entidades autorizadas pueden ver los datos. 
- **Integridad** : sólo autorizados entidades puede modificar o eliminar los datos.

La solución de Microsoft Azure StorSimple consta de cuatro componentes principales que interactúan entre sí:

- **Servicio de administrador de StorSimple alojado en Microsoft Azure** : el servicio de administración que usar para configurar y proporcionar el dispositivo StorSimple.
- **Dispositivo de StorSimple** – un dispositivo físico instalado en su centro de datos. Todos los clientes que generan datos y hosts conectan al dispositivo StorSimple, y el dispositivo administra los datos y mueve a la nube de Azure según corresponda.
- **Clientes/hosts conectados al dispositivo** – los clientes en la infraestructura de conectar el dispositivo StorSimple y genere los datos que se deben proteger.
- **Almacenamiento de la nube** , la ubicación en la nube de Azure donde se almacenan los datos.

Las secciones siguientes describen las características de seguridad de StorSimple que ayudan a proteger cada uno de estos componentes y los datos almacenados en ellas. También incluye una lista de las preguntas que es posible que tenga sobre seguridad de Microsoft Azure StorSimple y las respuestas correspondientes.

## <a name="storsimple-manager-service-protection"></a>Protección del servicio de administrador de StorSimple

El servicio Administrador de StorSimple es un servicio de administración alojado en Microsoft Azure y se usa para administrar todos los dispositivos de StorSimple que su organización ha adquirido. Puede acceder al servicio Administrador de StorSimple usando sus credenciales de la organización para iniciar sesión en el portal de clásico Azure a través de un explorador web. 

Acceso al servicio de administrador de StorSimple requiere que su organización tiene una suscripción de Azure que incluye StorSimple. La suscripción rige las características que puede tener acceso en el portal de clásico de Azure. Si su organización no tiene una suscripción de Azure y desea obtener más información acerca de ellos, consulte [registrarse para Azure como una organización](../active-directory/sign-up-organization.md). 

Dado que el servicio Administrador de StorSimple está alojado en Azure, está protegido por las características de seguridad de Azure. Para obtener más información sobre las características de seguridad proporcionados por Microsoft Azure, vaya al [Centro de confianza de Microsoft Azure](https://azure.microsoft.com/support/trust-center/security/).

## <a name="storsimple-device-protection"></a>Protección de dispositivos StorSimple

El dispositivo de StorSimple es un dispositivo de almacenamiento de híbrida local que contiene las unidades de estado sólido (SSD) y unidades de disco duro (disco duro), junto con controladores redundantes y las capacidades de migración tras error automática. Los controladores de administran almacenamiento interconexión, colocando actualmente datos utilizados (o activos) en almacenamiento local (en la StorSimple dispositivo o local servidores), al mover menos datos utilizados con frecuencia en la nube.

Sólo autorizados StorSimple dispositivos tiene permiso para unirse al servicio de administrador de StorSimple que creó en su suscripción de Azure. Para autorizar un dispositivo, debe registrar con el servicio Administrador de StorSimple al proporcionar la clave de registro de servicio. La clave de registro del servicio es una clave de 128 bits aleatoria generada en el portal de clásico de Azure. 

![Clave de registro de servicio](./media/storsimple-security/ServiceRegistrationKey.png)

Para obtener información sobre cómo obtener una clave de registro de servicio, vaya a [paso 2: obtener la clave de registro del servicio](storsimple-deployment-walkthrough.md#step-2-get-the-service-registration-key).

La clave de registro del servicio es una clave larga que contiene 100 + caracteres. Puede copiar la clave y guardarlo en un archivo de texto en una ubicación segura de modo que puede usar para autorizar dispositivos adicionales según sea necesario. Si la clave de registro de servicio se pierde después de registrar el primer dispositivo, puede generar una nueva clave desde el servicio Administrador de StorSimple. Esto no afectará a la operación de dispositivos existentes. 

Una vez registrado un dispositivo, usa tokens para comunicarse con Microsoft Azure. No se utiliza la clave de registro de servicio después del registro del dispositivo.

> [AZURE.NOTE] Le recomendamos que vuelva a generar la clave de registro de servicio después de cada uso.

## <a name="protect-your-storsimple-solution-via-passwords"></a>Proteger la solución StorSimple a través de contraseñas

Las contraseñas son un aspecto importante de seguridad del equipo y se utilizan ampliamente en la solución StorSimple para ayudar a asegurar que los datos están accesibles sólo a usuarios autorizados. StorSimple le permite configurar las contraseñas siguientes:

- Contraseña de administrador de dispositivos de StorSimple
- Desafío contraseñas de iniciador y el destino del protocolo de autenticación mutuo (CHAP)
- Contraseña de administrador de instantáneas StorSimple

### <a name="windows-powershell-for-storsimple-and-the-storsimple-device-administrator-password"></a>Windows PowerShell para StorSimple y la contraseña de administrador del dispositivo StorSimple

Windows PowerShell para StorSimple es una interfaz de línea de comandos que puede usar para administrar el dispositivo de StorSimple. Windows PowerShell para StorSimple tiene características que le permiten registrar su dispositivo, configure la interfaz de red en el dispositivo, instalar a ciertos tipos de actualizaciones, obtener acceso a la sesión de soporte técnico para solucionar el dispositivo y a continuación, cambiar el estado del dispositivo. Puede obtener acceso a Windows PowerShell para StorSimple mediante una conexión a la consola de serie en el dispositivo o mediante Windows PowerShell remoto.

PowerShell remoto puede hacerse sobre HTTPS o HTTP. Si está habilitada la administración remota sobre HTTPS, debe descargar el certificado de administración remota desde el dispositivo e instálelo en el cliente remoto. Para obtener más información acerca de PowerShell remoto, vaya a [conectar remotamente con su dispositivo de StorSimple](storsimple-remote-connect.md).

Después de usar Windows PowerShell para StorSimple para conectar el dispositivo, debe proporcionar la contraseña de administrador del dispositivo para iniciar sesión en el dispositivo.

![Contraseña de administrador de dispositivos](./media/storsimple-security/DeviceAdminPW.png)

Las siguientes prácticas recomendadas se tenga en cuenta:

- Administración remota está desactivada de forma predeterminada. Puede usar el servicio Administrador de StorSimple habilitarlo. Como práctica recomendada de seguridad, debe estar habilitado el acceso remoto solo durante el período de tiempo que lo necesite.
- Si cambia la contraseña, asegúrese de notificar a todos los usuarios de acceso remoto para que no experimentan una pérdida de conectividad inesperado.
- El servicio Administrador de StorSimple no puede recuperar las contraseñas existentes: solo pueden restablecer. Se recomienda almacenar todas las contraseñas en un lugar seguro para que no tiene que restablecer una contraseña si se está olvidado. Si necesita restablecer una contraseña, asegúrese de notificar a todos los usuarios antes de que la restablezca. 

Puede obtener acceso a la interfaz de Windows PowerShell mediante una conexión serie al dispositivo. También puede tener acceso remotamente mediante HTTP o HTTPS, que proporcionan seguridad adicional. HTTPS proporciona un mayor nivel de seguridad que una serie o una conexión HTTP. Sin embargo, para usar HTTPS, debe instalar primero un certificado en el equipo cliente que tenga acceso a los dispositivos. Puede descargar el certificado de acceso remoto de la página de configuración de dispositivo en el servicio Administrador de StorSimple. Si se pierde el certificado para el acceso remoto, debe descargar un nuevo certificado y propagarse a todos los clientes que están autorizados a utilizar administración remota.

### <a name="challenge-handshake-authentication-protocol-chap-initiator-and-target-passwords"></a>Desafío contraseñas de iniciador y el destino del protocolo de autenticación mutuo (CHAP)

CHAP es un esquema de autenticación utilizado por el dispositivo StorSimple para validar la identidad de los clientes remotos. La comprobación se basa en una contraseña compartida. CHAP puede ser unidireccional (unidireccional) o mutuo (bidireccional). Con CHAP unidireccional, el destino (el dispositivo StorSimple) autentica un iniciador (host). CHAP mutuo o inverso requiere que el destino autenticar el iniciador y, a continuación, el iniciador autentique el destino. Su StorSimple puede estar configurado para usar cualquiera de los métodos.

Tenga en cuenta lo siguiente al configurar CHAP:

- El nombre de usuario CHAP debe contener menos de 233 caracteres.
- La contraseña CHAP debe estar entre 12 y 16 caracteres. Al intentar utilizar un nombre de usuario más largo o contraseña dará como resultado un error de autenticación en el host de Windows.
- No puede usar la misma contraseña para el iniciador CHAP y el destino CHAP.
- Después de establecer la contraseña, se puede cambiar, pero no se pueden recuperar. Si cambia la contraseña, asegúrese de notificar a todos los usuarios de acceso remoto para que puedan conectarse correctamente el dispositivo de StorSimple.

Para obtener más información acerca de CHAP y cómo configurar para la solución de StorSimple, vaya a [Configurar la configuración de su dispositivo StorSimple](storsimple-configure-chap.md).

### <a name="storsimple-snapshot-manager-password"></a>Contraseña de administrador de instantáneas StorSimple

Administrador de instantáneas StorSimple es un complemento de consola de administración de Microsoft (MMC) que usa el servicio de copia de instantáneas de volumen de Windows y grupos de volumen para generar copias de seguridad coherentes para la aplicación. Además, puede usar el Administrador de instantáneas StorSimple crear clonar y programaciones de copia de seguridad o restaurar volúmenes.

Al configurar un dispositivo para usar el Administrador de instantánea StorSimple, se le pedirá que proporcione la contraseña de administrador de instantáneas StorSimple. Esta contraseña está establecida en primer lugar en Windows PowerShell para StorSimple durante el registro. También se puede establecer y cambiada desde el servicio Administrador de StorSimple la contraseña. Esta contraseña autentica el dispositivo con el Administrador de instantáneas StorSimple.

![Contraseña de administrador de instantáneas StorSimple](./media/storsimple-security/SnapshotMgrPassword.png)

La contraseña de administrador de instantáneas de StorSimple debe ser 14 a 15 caracteres y debe contener 3 o más de una combinación de caracteres en mayúsculas, minúsculas, numéricos y especiales. Después de establecer la contraseña de administrador de instantáneas StorSimple, se puede cambiar, pero no se pueden recuperar. Si cambia la contraseña, asegúrese de notificar a todos los usuarios remotos.

Para obtener más información sobre el Administrador de instantáneas StorSimple, vaya a [¿Qué es el Administrador de instantáneas StorSimple?](storsimple-what-is-snapshot-manager.md)

### <a name="password-best-practices"></a>Procedimientos recomendados para contraseñas

Le recomendamos que use las siguientes pautas para ayudar a garantizar que las contraseñas de StorSimple son seguros y bien protegidos:

- Cambiar las contraseñas cada tres meses. Cambiar las contraseñas se aplica al año.
- Utilice contraseñas seguras. Para obtener más información, vaya a [crear contraseñas más seguras y protegerlas](http://blogs.microsoft.com/cybertrust/2014/08/25/create-stronger-passwords-and-protect-them/).
- Utilizar siempre contraseñas diferentes mecanismos de acceso diferente; cada una de las contraseñas que especifique debe ser única.
- No comparta contraseñas con cualquier persona que no está autorizado para tener acceso al dispositivo StorSimple.
- No habla sobre una contraseña delante de otras personas o sugerencia en el formato de la contraseña.
- Si sospecha que se ha comprometido una cuenta o contraseña, informar del incidente a su departamento de seguridad de la información.
- Tratar todas las contraseñas como confidencial información confidencial. 

## <a name="storsimple-data-protection"></a>Protección de datos de StorSimple

Esta sección describen las características de seguridad de StorSimple que protegen los datos al transmitirlos y los datos almacenados.

Como se describe en otras secciones, las contraseñas se utilizan para autorizar y autenticar usuarios antes de que puede obtener acceso a la solución StorSimple. Otra consideración de seguridad es cómo proteger los datos de usuarios no autorizados mientras se transfiere entre sistemas de almacenamiento y mientras se almacena. Las secciones siguientes describen las características de protección de datos proporcionadas con StorSimple.

> [AZURE.NOTE] Desduplicación proporciona protección adicional para los datos almacenados en el dispositivo StorSimple y el almacenamiento de Microsoft Azure. Cuando se deduplicar los datos, los objetos de datos se almacenan independientemente de los metadatos que se usa para asignar y acceder a ellos: no hay ningún contexto de nivel de almacenamiento disponible para reconstruir los datos en función de la estructura de volumen, el sistema de archivos o el nombre de archivo.

## <a name="protect-data-flowing-through-the-service"></a>Proteger los datos que pasan a través del servicio

La finalidad principal del servicio de administrador de StorSimple es administrar y configurar el dispositivo de StorSimple. Se ejecuta el servicio Administrador de StorSimple en Microsoft Azure. Usar el portal de clásico Azure para escribir los datos de configuración de dispositivo y, a continuación, Microsoft Azure usa el servicio Administrador de StorSimple para enviar los datos al dispositivo. StorSimple utiliza un sistema de pares de claves asimétricos para ayudar a asegurar que un compromiso del servicio Azure no dará como resultado un compromiso de la información almacenada. 

![Cifrado de datos de vuelo](./media/storsimple-security/DataEncryption.png)

El sistema de claves asimétrico ayuda a proteger los datos que fluyen a través del servicio como sigue:

1. Certificado de cifrado de datos que utiliza una clave pública y privada asimétrica par se genera en el dispositivo y se usa para proteger los datos. Las claves se generan cuando se registra el primer dispositivo. 
2. Las claves de certificado de cifrado de datos se exportan a un archivo de intercambio de información Personal (.pfx) que está protegido por la clave de cifrado de datos de servicio, que es una clave de 128 bits segura generada aleatoriamente por el primer dispositivo durante el registro.
3. La clave pública del certificado de forma segura estará disponible para el servicio Administrador de StorSimple y la clave privada permanece con el dispositivo.
4. Introducir el servicio de datos se cifran con la clave pública y descifra con la clave privada almacenada en el dispositivo, asegurarse de que el servicio de Azure no puede descifrar los datos que fluye en el dispositivo.

Se genera la clave de cifrado de datos de servicio en solo el primer dispositivo registrado con el servicio. Todos los dispositivos posteriores que se registran con el servicio deben utilizar la misma clave de cifrado de datos de servicio. 

> [AZURE.IMPORTANT] 
> 
> Es muy importante hacer una copia de la clave de cifrado de datos de servicio y guardar en una ubicación segura. Una copia de la clave de cifrado de datos de servicio debe almacenarse de manera que puede tener acceso a una persona autorizada y se puede comunicar con facilidad para el Administrador de dispositivos.
>
> Si se pierde la clave de cifrado de datos de servicio, una persona de soporte técnico de Microsoft puede ayudarle a recuperarla siempre que tenga al menos un dispositivo en un estado en línea. Se recomienda cambiar la clave de cifrado de datos de servicio después de recuperarla. Para obtener instrucciones, vaya a [cambiar la clave de cifrado de datos de servicio](storsimple-service-dashboard.md#change-the-service-data-encryption-key).

Puede cambiar la clave de cifrado de datos de servicio y el certificado de cifrado de datos correspondientes seleccionando la opción **cambiar la clave de cifrado de datos de servicio** en el panel de servicio. Para asegurarse de que no comprometa la seguridad de datos, debe usar un dispositivo StorSimple físico para cambiar la clave de cifrado de datos de servicio. Cambiar las claves de cifrado requiere que todos los dispositivos se pueden actualizar con la nueva clave. Por lo tanto, se recomienda cambiar la clave cuando todos los dispositivos que están en línea. Si los dispositivos son sin conexión, sus claves pueden cambiarse a una hora distinta. Los dispositivos con claves actualizadas aún podrán ejecutar copias de seguridad, pero no podrá restaurar datos hasta que se actualice la clave. Para obtener más información, vaya a [usar el panel de servicio de administrador de StorSimple](storsimple-service-dashboard.md).

La clave de cifrado de datos de servicio y el certificado de cifrado de datos no expiran. Sin embargo, se recomienda que cambie la clave de cifrado de datos de servicio anualmente para ayudar a evitar compromiso de clave.

## <a name="protect-data-at-rest"></a>Proteger los datos almacenados

El dispositivo StorSimple administra datos almacenando en niveles localmente y en la nube, dependiendo de la frecuencia de uso. Todos los equipos host que están conectados al dispositivo envían datos en el dispositivo, que mueve los datos, a continuación, en la nube, según corresponda. Datos se transfieren desde el dispositivo a la nube de forma segura por Internet. Cada dispositivo tiene un destino de iSCSI que todos los volúmenes compartidos en ese dispositivo. Todos los datos se cifran antes de enviarlo a la nube de almacenamiento. 

![Clave de cifrado de almacenamiento de nube](./media/storsimple-security/CloudStorageEncryption.png)

Para ayudar a garantizar la seguridad y la integridad de datos que se mueven a la nube, StorSimple le permite definir claves de cifrado de almacenamiento de nube como sigue:

- Especifique la clave de cifrado de almacenamiento de nube al crear un contenedor de volumen. La clave no se pueden modificar ni agregar más adelante. 
- Todos los volúmenes en un contenedor de volumen comparten la misma clave de cifrado. Si desea que un formulario distinto de cifrado para un volumen específico, recomendamos que cree un nuevo contenedor de volumen para hospedar ese volumen.
- Cuando escriba la clave de cifrado de almacenamiento de nube en el servicio Administrador de StorSimple, la clave se cifra utilizando la parte pública de la clave de cifrado de datos de servicio y, a continuación, se envía al dispositivo.
- La clave de cifrado de almacenamiento de nube no se almacena en el servicio y se conoce solo al dispositivo.
- Especificar una clave de cifrado de almacenamiento de nube es opcional. Puede enviar los datos que han sido cifrados en el host al dispositivo.

### <a name="additional-security-best-practices"></a>Prácticas recomendadas de seguridad adicional

- Dividir el tráfico: aislar su Ethernet del tráfico de usuario en una LAN corporativa de implementación de una red totalmente separada y usando VLAN donde aislamiento físico no es una opción. Una red dedicada para el almacenamiento iSCSI garantizará la seguridad y el rendimiento de los datos empresariales. Mezcla de usuarios y el almacenamiento de tráfico LAN corporativa no es aconsejable y puede aumentar la latencia y provocar errores en la red.

- Para la seguridad de red del host, use las interfaces de red que admite el motor de descarga de TCP/IP (TOE). PIE reduce la carga de la CPU mediante el procesamiento de TCP del adaptador de red.

## <a name="protect-data-via-storage-accounts"></a>Proteger los datos a través de cuentas de almacenamiento

Cada suscripción de Microsoft Azure puede crear una o varias cuentas de almacenamiento. (Una cuenta de almacenamiento proporciona un único espacio de nombres para trabajar con datos almacenados en la nube de Azure). Acceso a una cuenta de almacenamiento se controla mediante las teclas de acceso y de suscripción asociadas con esa cuenta de almacenamiento. 

Cuando se crea una cuenta de almacenamiento, Microsoft Azure genera dos teclas de acceso de almacenamiento de 512 bits, uno de los cuales se usa para la autenticación cuando el dispositivo de StorSimple tiene acceso a la cuenta de almacenamiento. Tenga en cuenta que sólo una de estas teclas está en uso. La otra clave se mantiene en reserva, lo que le permite rotar las claves de forma periódica. Para girar las teclas, activar la clave secundaria y, a continuación, elimine la clave principal. A continuación, puede crear una nueva clave para usar durante el giro siguiente. (Por motivos de seguridad, muchos centros de datos requieren la rotación de clave). 

Le recomendamos que siga estos procedimientos recomendados para la rotación de clave:

- Debe girar claves de la cuenta de almacenamiento periódicamente para asegurarse de que su cuenta de almacenamiento no tiene acceso a los usuarios no autorizados.
- Periódicamente, el Administrador de Azure debe cambiar o regenerar la clave principal o secundaria mediante el uso de la sección de almacenamiento del portal clásica Azure acceso directo a la cuenta de almacenamiento.


## <a name="protect-data-via-encryption"></a>Proteger los datos a través de cifrado

StorSimple usa los siguientes algoritmos de cifrado para proteger los datos almacenados en o de viaje entre los componentes de la solución StorSimple.

| Algoritmo de | Longitud de la clave | Protocolos, aplicaciones o comentarios |
| --------- | ---------- | ------------------------------- |
| RSA       | 2048       | El portal clásico Azure utiliza RSA PKCS 1 v1.5 para cifrar datos de configuración que se envían al dispositivo: por ejemplo, almacenamiento credenciales, configuración de dispositivo de StorSimple y las claves de cifrado de almacenamiento en la nube. |
| AES       | 256        | AES con CBC se usa para cifrar la parte pública de la clave de cifrado de datos de servicio antes de enviarlo al portal clásico Azure desde el dispositivo de StorSimple. También se usa el dispositivo StorSimple para cifrar datos antes de que los datos se envían a la cuenta de almacenamiento de la nube. |


## <a name="storsimple-virtual-device-security"></a>Seguridad de dispositivo virtual StorSimple

[AZURE.INCLUDE [storsimple virtual device security](../../includes/storsimple-virtual-device-security.md)]

## <a name="frequently-asked-questions-faq"></a>Preguntas más frecuentes (P+F)

Las siguientes son algunas preguntas y respuestas acerca de Microsoft Azure StorSimple y seguridad.

**Q:** Mi está comprometido. ¿Qué debería mi pasos siguientes?

**A:** Inmediatamente, debe cambiar la clave de cifrado de datos de servicio y las teclas de la cuenta de almacenamiento para la cuenta de almacenamiento que se utiliza para los niveles de datos. Para obtener instrucciones, vaya a: 

- [Cambiar la clave de cifrado de datos de servicio](storsimple-service-dashboard.md#change-the-service-data-encryption-key)
- [Rotación de clave de cuentas de almacenamiento](storsimple-manage-storage-accounts.md#key-rotation-of-storage-accounts)

**Q:** Tengo un nuevo dispositivo StorSimple que solicita la clave de registro de servicio. ¿Cómo se puede recuperar?

**A:** Esta clave se creó cuando se crea por primera vez el servicio Administrador de StorSimple. Al utilizar el servicio Administrador de StorSimple para conectar el dispositivo, puede usar la página de inicio rápido de servicio para ver o regenerar la clave de registro de servicio. Generar una nueva clave de registro de servicio no afectará a los dispositivos registrados existentes. Para obtener instrucciones, vaya a:

- [Ver o regenerar la clave de registro de servicio](storsimple-service-dashboard.md#view-or-regenerate-the-service-registration-key)

**Q:** He perdido la clave de cifrado de datos de servicio. ¿Qué debo hacer?

**A:** Póngase en contacto con soporte técnico de Microsoft. Puede iniciar sesión en una sesión de asistencia en el dispositivo y ayuda para recuperar la clave (siempre que al menos un dispositivo está en línea). Inmediatamente después de obtener la clave de cifrado de datos de servicio, debe cambiar para asegurarse de que la clave nueva se conoce solo para usted. Para obtener instrucciones, vaya a:

- [Cambiar la clave de cifrado de datos de servicio](storsimple-service-dashboard.md#change-the-service-data-encryption-key)

**Q:**  Había autorizado un dispositivo para un cambio de clave de cifrado de datos de servicio, pero no se ha iniciado el proceso de cambio de clave. ¿Qué debo hacer?

**A:** Si ha caducado el período de tiempo de espera, debe autorizar el dispositivo para que el cambio de clave de cifrado de datos de servicio y vuelva a iniciar el proceso.

**Q:**  He cambiado la clave de cifrado de datos de servicio, pero no pudo actualizar los otros dispositivos dentro de 4 horas. ¿Tengo para volver a empezar?

**A:** El período de tiempo de 4 horas es solo para iniciar el cambio. Después de iniciar el proceso de actualización en el dispositivo StorSimple autorizado, la autorización es válida hasta que todos los dispositivos estén actualizados.

**Q:** El Administrador de StorSimple ha dejado la empresa. ¿Qué debo hacer?

**A:** Cambiar y restablecer las contraseñas para permitir el acceso al dispositivo StorSimple y cambiar la clave de cifrado de datos de servicio para asegurarse de que no se conoce la nueva información a personas no autorizadas. Para obtener instrucciones, vaya a:

- [Usar el servicio Administrador de StorSimple para cambiar las contraseñas de storsimple](storsimple-change-passwords.md)
- [Cambiar la clave de cifrado de datos de servicio](storsimple-service-dashboard.md#change-the-service-data-encryption-key)
- [Configure el CHAP para el dispositivo StorSimple](storsimple-configure-chap.md)

**Q:** Deseo proporcionar la contraseña de administrador de instantáneas StorSimple a un host que se está conectando el dispositivo StorSimple, pero la contraseña no está disponible. ¿Qué puedo hacer?

**A:** Si ha olvidado la contraseña, debe crear uno nuevo. A continuación, asegúrese de informar a todos los usuarios existentes a que la contraseña se ha cambiado y que deben actualizar sus clientes para usar la nueva contraseña. Para obtener instrucciones, vaya a:

- [Cambiar la contraseña de administrador de instantáneas StorSimple](storsimple-change-passwords.md#change-the-storsimple-snapshot-manager-password)
- [Autenticar un dispositivo](storsimple-snapshot-manager-manage-devices.md#authenticate-a-device)

**Q:** Ha cambiado el certificado para el acceso remoto a Windows PowerShell para StorSimple en el dispositivo. ¿Cómo actualizar mis clientes de acceso remoto?

**A:** Puede descargar el nuevo certificado desde el servicio Administrador de StorSimple y, a continuación, proporcionar a estar instalada en el almacén de certificados de los clientes de acceso remoto. Para obtener instrucciones, vaya a:

- [Cmdlet del certificado de importación](https://technet.microsoft.com/library/hh848630.aspx)

**Q:** ¿Es el Administrador de StorSimple está comprometido de mi if protegida de datos?

**A:** Datos de configuración de servicio siempre se cifran con la clave pública cuando se ve en un explorador web. Dado que el servicio no tiene acceso a la clave privada, el servicio no podrá ver los datos. Si el servicio Administrador de StorSimple está comprometido, no hay ningún efecto, como no han claves almacenadas en el servicio Administrador de StorSimple.

**Q:** ¿Si alguien tiene acceso al certificado de cifrado de datos, se pueden comprometidos Mis datos?

**A:** Microsoft Azure almacena la clave de cifrado de datos del cliente (archivo .pfx) en un formato de cifrado. Dado que el archivo .pfx se cifra y el servicio StorSimple no tiene la clave de cifrado de datos de servicio para descifrar el archivo .pfx, simplemente obtener acceso al archivo .pfx no exponen ningún secreto.

**Q:** ¿Qué sucede si una entidad gubernamental le pide Microsoft Mis datos?

**A:** Dado que todos los datos se cifran en el servicio y la clave privada que se mantiene con el dispositivo, la entidad gubernamental debe solicitar al cliente los datos. 

## <a name="next-steps"></a>Pasos siguientes

[Implementar el dispositivo StorSimple](storsimple-deployment-walkthrough.md).
 

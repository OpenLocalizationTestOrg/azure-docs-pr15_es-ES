<properties
    pageTitle="Solución de problemas de almacenamiento de archivos de Azure | Microsoft Azure"
    description="Solución de problemas de almacenamiento de archivos de Azure"
    services="storage"
    documentationCenter=""
    authors="genlin"
    manager="felixwu"
    editor="na"
    tags="storage"/>

<tags
    ms.service="storage"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/24/2016"
    ms.author="genli"/>

# <a name="troubleshooting-azure-file-storage-problems"></a>Solucionar problemas de almacenamiento de archivos de Azure

Este artículo enumeran los problemas más comunes relacionados con el almacenamiento de archivos de Microsoft Azure cuando se conecte de clientes de Windows y Linux. También proporciona las posibles causas y resoluciones para estos problemas.

**Problemas generales (que se producen en clientes de Windows y Linux)**

- [Error de cuota al intentar abrir un archivo](#quotaerror)

- [Rendimiento lento cuando tiene acceso a almacenamiento de Azure archivos de Windows o de Linux](#slowboth)

**Problemas de cliente de Windows**

- [Rendimiento lento cuando tiene acceso a almacenamiento de archivos de Azure desde Windows 8.1 o Windows Server 2012 R2](#windowsslow)

- [Error 53 intentando montaje de un recurso compartido de archivos de Azure](#error53)

- [Uso de red se realizó correctamente pero no puedo ver el archivo Azure compartir montada en el Explorador de Windows](#netuse)

- [Mi cuenta de almacenamiento contiene "/" y la red use el comando correctamente](#slashfails)

- [Mi aplicación o servicio no puede acceder a la unidad de archivos de Azure montada.](#accessfiledrive)

- [Recomendaciones adicionales para optimizar el rendimiento](#additional)

**Problemas del cliente Linux**

- [Error "Está copiando un archivo a un destino que no admite el cifrado" al cargar o copiar archivos a archivos de Azure](#encryption)

- [Comparte "Host está apagado" error en el archivo existente o el shell se bloquea al realizar la lista de comandos en el punto de montaje](#errorhold)

- [Error de montaje 115 al intentar montaje Azure archivos en la VM Linux](#error15)

- [VM Linux experimentando retrasos aleatorios en comandos, como "ls"](#delayproblem)

## <a name="general-problems"></a>Problemas generales
<a id="quotaerror"></a>
### <a name="quota-error-when-trying-to-open-a-file"></a>Error de cuota al intentar abrir un archivo

En Windows, recibe mensajes de error similares a los siguientes:

**1816 ERROR_NOT_ENOUGH_QUOTA <> – 0xc0000044**

**STATUS_QUOTA_EXCEEDED**

**No hay suficiente cuota disponible para procesar este comando**

**Valor de identificador no válido GetLastError: 53**

En Linux, mensajes de error similares a los siguientes:

**<filename>[permiso denegado]**

**Cuota de disco excedida**

#### <a name="cause"></a>Causa

El problema se produce porque se ha alcanzado el límite de identificadores abiertos simultáneas permitidos para un archivo.

#### <a name="solution"></a>Solución

Reduzca el número de identificadores abiertos simultáneas cerrando algunos controladores y vuelva a intentar. Para obtener más información, vea [Microsoft Azure almacenamiento de rendimiento y escalabilidad lista de comprobación](storage-performance-checklist.md).

<a id="slowboth"></a>
### <a name="slow-performance-when-accessing-file-storage-from-windows-or-linux"></a>Rendimiento lento al acceder a almacenamiento de archivos de Windows o Linux

- Si no tiene un requisito mínimo de tamaño i/OS específico, le recomendamos que utilice 1 MB como el tamaño de i/OS para un rendimiento óptimo.

- Si conoce el tamaño final de un archivo que va a ampliar con escribe y el software no tiene problemas de compatibilidad cuando la cola no se han escrito en el archivo que contiene los ceros a la izquierda, a continuación, establezca el tamaño de archivo por adelantado en lugar de cada escritura ser una extensión de escritura.

## <a name="windows-client-problems"></a>Problemas de cliente de Windows
<a id="windowsslow"></a>
### <a name="slow-performance-when-accessing-the-file-storage-from-windows-81-or-windows-server-2012-r2"></a>Rendimiento lento al acceder a almacenamiento de archivos de Windows 8.1 o Windows Server 2012 R2

Para los clientes que ejecutan Windows 8.1 o Windows Server 2012 R2, asegúrese de que la revisión [KB3114025](https://support.microsoft.com/kb/3114025) está instalada. Esta revisión mejora la crear y cerrar el controlador de rendimiento.

Puede ejecutar la siguiente secuencia de comandos para comprobar si se ha instalado la revisión en:

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

Si está instalada la revisión, se mostrará el resultado siguiente:

**HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies**

**{96c345ef-3cac-477b-8fcd-bea1a564241c} REG_DWORD 0 x 1**

> [AZURE.NOTE]  Imágenes de Windows Server 2012 R2 en Azure Marketplace tienen la revisión KB3114025 instalado de forma predeterminada a partir de diciembre de 2015.

<a id="additional"></a>
### <a name="additional-recommendations-to-optimize-performance"></a>Recomendaciones adicionales para optimizar el rendimiento

Nunca cree o abra un archivo para i/OS en caché que solicita acceso de escritura, pero no el acceso de lectura. Es decir, cuando se llama a **CreateFile()**, nunca especificar solo **GENERIC_WRITE**, pero siempre especificar **GENERIC_READ | GENERIC_WRITE**. Un controlador de sólo escritura no caché pequeña escribe localmente, incluso cuando es el identificador abierto solo para el archivo. Esto impone una reducción del rendimiento graves en escribe pequeña. Observe que el modo de "a" para CRT **fopen()** abre un controlador de escritura.

<a id="error53"></a>
### <a name="error-53-when-you-try-to-mount-or-unmount-an-azure-file-share"></a>"Error 53" cuando intenta montaje o desmontaje de un recurso compartido de archivos de Azure

Este problema puede estar causado por las condiciones siguientes:

#### <a name="cause-1"></a>Causa 1

"53 error del sistema. Acceso denegado". Por motivos de seguridad, conexiones a los recursos compartidos de archivos de Azure se bloquean si el canal de comunicación no se cifra y el intento de conexión no se realiza desde el mismo centro de datos donde residen los recursos compartidos de archivos de Azure. No se proporciona cifrado de canal de comunicación si el sistema operativo del cliente del usuario no admite el cifrado de SMB. Esto se indica mediante un "error de sistema 53 se ha producido. Acceso denegado"mensaje de Error cuando un usuario intenta un recurso compartido de archivos de montaje de local o de un centro de datos diferente. Windows 8, Windows Server 2012 y versiones posteriores de cada solicitud de negociar que incluye 3.0 SMB, que admite el cifrado.

#### <a name="solution-for-cause-1"></a>Solución para la causa 1

Conectarse desde un cliente que cumpla los requisitos de Windows 8, Windows Server 2012 o versiones posteriores, o que se conectan desde una máquina virtual que se encuentra en el centro de datos de la misma que la cuenta de almacenamiento de Azure que se usa para el recurso compartido de archivos de Azure.

#### <a name="cause-2"></a>Causa 2

"Error del sistema 53" cuando montaje de un recurso compartido de archivos de Azure puede ocurrir si se bloquean el puerto 445 comunicaciones salientes al centro de datos de archivos de Azure. Haga clic [aquí](http://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx) para ver el resumen de ISP que permitir o no permitir el acceso desde el puerto 445.

Algunas organizaciones de TI y Comcast bloquean este puerto. Para conocer si esta es la razón detrás del mensaje de "Error del sistema 53", puede usar Portqry para consultar el extremo TCP:445. Si el extremo de TCP:445 se muestra como filtrado, se bloquea el puerto TCP. Esto es un ejemplo de consulta:

`g:\DataDump\Tools\Portqry>PortQry.exe -n [storage account name].file.core.windows.net -p TCP -e 445`

Si TCP 445 está bloqueado por una regla a lo largo de la ruta de acceso de red, verá el siguiente resultado:

**El puerto TCP 445 (servicio de microsoft-ds): FILTRADA**

Para obtener más información acerca de cómo utilizar Portqry, consulte [Descripción de la herramienta de línea de comandos Portqry.exe](https://support.microsoft.com/kb/310099).

#### <a name="solution-for-cause-2"></a>Solución para la causa 2

Trabajar con su organización de TI para abrir el puerto 445 salientes a [intervalos de IP de Azure](https://www.microsoft.com/download/details.aspx?id=41653).

#### <a name="cause-3"></a>Causa 3

También se pueden recibir "Error del sistema 53" si se habilita la comunicación de NTLMv1 en el cliente. Tener NTLMv1 habilitado, crea a un cliente menos seguras. Por lo tanto, se bloqueará la comunicación de archivos de Azure. Para comprobar si esta es la causa del error, compruebe que la siguiente subclave del registro se establece en un valor de 3:

HKLM\System\CurrentControlSet\Control\LSA > LmCompatibilityLevel.

Para obtener más información, vea el tema de [LmCompatibilityLevel](https://technet.microsoft.com/library/cc960646.aspx) en TechNet.

#### <a name="solution-for-cause-3"></a>Solución para la causa 3

Para resolver este problema, revertir el valor LmCompatibilityLevel en la clave de registro HKLM\SYSTEM\CurrentControlSet\Control\Lsa al valor predeterminado de 3.

Archivos de Azure admite únicamente la autenticación NTLMv2. Asegúrese de que la directiva de grupo se aplica a los clientes. Esto impedirá que este error se produzca. Esto también se considera una práctica recomendada de seguridad. Para obtener más información, vea [cómo configurar los clientes para utilizar NTLMv2 mediante Directiva de grupo](https://technet.microsoft.com/library/jj852207(v=ws.11).aspx)

La configuración de directiva recomendado es **Enviar sólo respuesta NTLMv2**. Esto corresponde a un valor de registro de 3. Los clientes utilizar únicamente la autenticación NTLMv2 y utilizan la seguridad de sesión NTLMv2 si el servidor lo admite. Controladores de dominio aceptan autenticación LM, NTLM y NTLMv2.

<a id="netuse"></a>
### <a name="net-use-was-successful-but-dont-see-the-azure-file-share-mounted-in-windows-explorer"></a>Uso de red se realizó correctamente pero no ve el archivo Azure compartir montada en el Explorador de Windows

#### <a name="cause"></a>Causa

De forma predeterminada, el Explorador de Windows no se ejecuta como administrador. Si ejecuta **net use** desde un símbolo del sistema de administrador, asigne la unidad de red "como"administrador. Dado que las unidades asignadas son centrado en el usuario, la cuenta de usuario que está conectada no muestra las unidades si se instala en otra cuenta de usuario.

#### <a name="solution"></a>Solución

Montaje el recurso compartido de una línea de comandos sin permisos de administrador. Como alternativa, puede seguir [este tema de TechNet](https://technet.microsoft.com/library/ee844140.aspx) para configurar **el valor EnableLinkedConnections del registro** .

<a id="slashfails"></a>
### <a name="my-storage-account-contains--and-the-net-use-command-fails"></a>Mi cuenta de almacenamiento contiene "/" y la red use el comando correctamente

#### <a name="cause"></a>Causa

Cuando se ejecuta el comando **net use** en el símbolo del sistema (cmd.exe), se analiza agregando "/" como una opción de línea de comandos. Hace que la asignación de unidad no se realice correctamente.

#### <a name="solution"></a>Solución

Puede usar cualquiera de los siguientes pasos para solucionar este problema:

• Use el siguiente comando PowerShell:

`New-SmbMapping -LocalPath y: -RemotePath \\server\share  -UserName acountName -Password "password can contain / and \ etc"`

Desde un archivo por lotes Esto puede hacerse como

`Echo new-smbMapping ... | powershell -command –`

Coloque entre comillas dobles la clave para evitar este problema, a menos que "/" es el primer carácter. Si es así, utilice el modo interactivo y escriba su contraseña por separado o regenerar las claves para obtener una clave que no empiece por el carácter de barra diagonal (/).

<a id="accessfiledrive"></a>
### <a name="my-applicationservice-cannot-access-mounted-azure-files-drive"></a>Mi aplicación o servicio no tiene acceso a la unidad de archivos de Azure montada

#### <a name="cause"></a>Causa

Unidades son montaje por usuario. Si su aplicación o servicio se está ejecutando en otra cuenta de usuario, los usuarios no ven la unidad.

#### <a name="solution"></a>Solución

Montaje de unidad de la misma cuenta de usuario en ¿qué es la aplicación. Esto se puede hacer con herramientas como psexec.

Como alternativa, puede crear un nuevo usuario que tiene los mismos privilegios que la cuenta de servicio o sistema de red y vuelva a ejecutar **cmdkey** y **usar neto** en esa cuenta. El nombre de usuario debe ser el nombre de la cuenta de almacenamiento y contraseña debe ser la clave de cuenta de almacenamiento. Otra opción para **usar neto** es pasar el nombre de la cuenta de almacenamiento y la clave de los parámetros de nombre y contraseña de usuario del comando **net use** .

Después de seguir estas instrucciones, puede recibir el siguiente mensaje de error: "1312 error del sistema. No existe un inicio de sesión especificado. Se puede que haya terminado"cuando se ejecuta **neto usar** para la cuenta de servicio de red del sistema. Si es así, asegúrese de que el nombre de usuario que se pasa a **usar neto** incluye información de dominio (por ejemplo: "[nombre de la cuenta de almacenamiento]. file.core.windows .net").

## <a name="linux-client-problems"></a>Problemas del cliente Linux

<a id="encryption"></a>
### <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>Error "Está copiando un archivo a un destino que no admite el cifrado"

#### <a name="cause"></a>Causa

Archivos cifrados BitLocker se pueden copiar a archivos de Azure. Sin embargo, el almacenamiento de archivos no admite NTFS EFS. Por lo tanto, es probable que está utilizando EFS en este caso. Si tiene archivos cifrados EFS, una operación de copia para el almacenamiento de archivos puede fallar a menos que el comando Copiar descifra un archivo copiado.

#### <a name="workaround"></a>Solución alternativa

Para copiar un archivo en el almacenamiento de archivos, primero debe descifrar él. Puede hacer esto mediante uno de los métodos siguientes:

• **Use/d de copia**.

• Establecer la clave del registro siguiente:

- Ruta de acceso = HKLM\Software\Policies\Microsoft\Windows\System
- Tipo de valor = DWORD
- Nombre = CopyFileAllowDecryptedRemoteDestination
- Valor = 1

Sin embargo, tenga en cuenta que la configuración de la clave del registro afecta a todas las operaciones de copia a los recursos compartidos.

<a id="errorhold"></a>
### <a name="host-is-down-error-on-existing-file-shares-or-the-shell-hangs-when-you-run-list-commands-on-the-mount-point"></a>Comparte "Host está apagado" error en el archivo existente o la consola deja de responder cuando se inicia la lista de comandos en el punto de montaje

#### <a name="cause"></a>Causa

Este error se produce en el cliente Linux cuando el cliente ha estado inactivo durante un período de tiempo prolongado. Cuando se produce este error, el cliente se desconecta y el tiempo de espera de la conexión del cliente.

#### <a name="solution"></a>Solución

Este problema se corrige ahora en el núcleo Linux como parte del [conjunto de cambios](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/fs/cifs?id=4fcd1813e6404dd4420c7d12fb483f9320f0bf93), pendiente de atrás en la distribución de Linux.

Works resolver este problema, respaldar la conexión y evitar entrar en un estado inactivo, mantener un archivo en el recurso compartido de archivos de Azure que escribe periódicamente. Esto debe ser una operación de escritura, como volver a escribir la fecha creado o modificado en el archivo. En caso contrario, puede obtener resultados almacenados en caché y la operación podría desencadenar la conexión.

<a id="error15"></a>
### <a name="mount-error-115-when-you-try-to-mount-azure-files-on-the-linux-vm"></a>"Error 115 de montaje" cuando intenta montaje archivos de Azure en la VM Linux

#### <a name="cause"></a>Causa

Distribuciones Linux todavía no admiten la característica de cifrado en SMB 3.0. En algunas distribuciones, el usuario puede recibir un mensaje de error "115" si intentan montaje Azure archivos usando 3.0 SMB debido a una característica que falta.

#### <a name="solution"></a>Solución

Si el cliente Linux SMB que se utiliza no admite el cifrado, montaje Azure archivos con SMB 2.1 desde una VM Linux en el centro de datos de la misma que la cuenta de almacenamiento de archivos.

<a id="delayproblem"></a>
### <a name="linux-vm-experiencing-random-delays-in-commands-like-ls"></a>VM Linux experimentando retrasos aleatorios en comandos, como "ls"

#### <a name="cause"></a>Causa

Esto puede ocurrir cuando el comando de montaje no incluye la opción **serverino** . Sin **serverino**, el comando ls inicia un **estado de** todos los archivos.

#### <a name="solution"></a>Solución

Compruebe el **serverino** en su entrada "/ etcetera/fstab":

azureuser.File.Core.Windows.NET/WMS/comer en/home/sampledir tipo cifs (rw, nodev, relatime, ver = 2.1, s = ntlmssp, caché = estricto, nombre de usuario = largo y LARGOB, dominio = X, file_mode = 0755, dir_mode = 0755, serverino, rsize = 65536, wsize = 65536, actimeo = 1)

Si la opción de **serverino** no está presente, desmontar y montaje Azure nuevo archivos haciendo que la opción **serverino** seleccionada.

## <a name="learn-more"></a>Aprende más

- [Introducción a almacenamiento de archivos de Azure en Windows](storage-dotnet-how-to-use-files.md)

- [Introducción a almacenamiento de archivos de Azure en Linux](storage-how-to-use-files-linux.md)


<properties 
    pageTitle="¿Cómo RemoteApp de Azure guardar la configuración y datos de usuario? | Microsoft Azure"
    description="Obtenga información sobre cómo RemoteApp de Azure guarda los datos de usuario mediante el disco de perfil de usuario."
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

# <a name="how-does-azure-remoteapp-save-user-data-and-settings"></a>¿Cómo RemoteApp de Azure guardar la configuración y datos de usuario?

> [AZURE.IMPORTANT]
> RemoteApp Azure es que se utilizan. Lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) para obtener información detallada.

Azure RemoteApp guarda la identidad de usuario y las personalizaciones en dispositivos y sesiones. Estos datos de usuario se almacenan en un disco de cada colección por usuario, conocido como un disco de perfil de usuario (UDP). El disco sigue al usuario y garantiza que el usuario tiene una experiencia coherente, independientemente de dónde iniciar sesión en. guarda 

Discos de perfil de usuario son completamente transparentes para el usuario, los usuarios guardar documentos en su carpeta de documentos (de lo que parece ser una unidad local) y cambiar su configuración de aplicación como de costumbre. Al mismo tiempo, toda la configuración personal se mantiene al conectarse a Azure RemoteApp desde cualquier dispositivo. Todo el usuario ve es sus datos en el mismo lugar.

Cada UDP 50GB de almacenamiento persistente y contiene ambas configuraciones de aplicaciones y los datos de usuario. 

Siga leyendo para obtener información específica sobre los datos de perfil de usuario.

>[AZURE.NOTE] ¿Necesita deshabilitar el UDP? Puede hacer que ahora: consulte Entrada de blog de Pavithra, [Deshabilitar usuario perfil discos (UPDs) de RemoteApp de Azure](https://blogs.technet.microsoft.com/enterprisemobility/2015/11/11/disable-user-profile-disks-upds-in-azure-remoteapp/), para obtener información detallada.


## <a name="how-can-an-admin-get-to-the-data"></a>¿Cómo puede obtener un administrador a los datos?

Si necesita acceso a los datos de uno de los usuarios (de recuperación o si el usuario deja la empresa), póngase en contacto con soporte técnico de Azure y proporcione la información de suscripción para la colección y la identidad del usuario. El equipo de RemoteApp de Azure le proporcionará una dirección URL en el disco duro virtual. Descargar ese disco duro virtual y recuperar todos los documentos o archivos que necesita. Tenga en cuenta que el disco duro virtual es 50GB, por lo que tardará un poco para descargarlo.


## <a name="is-the-data-backed-up"></a>¿Es la copia de datos?

Sí, se guarda una copia de seguridad de los datos de usuario por ubicación geográfica. Los datos es de solo lectura y se puede acceder de la misma manera, como los datos normales sería (contacto Azure RemoteApp obtenerlo), si el centro de datos principal hacia abajo. Los datos se copian en tiempo real a la ubicación de copia de seguridad y no se guarde copias de las distintas versiones. Por lo tanto, en daños en los datos, no podremos restaurar la versión buena conocida anteriormente, pero si el centro de datos principal no funciona, podrá obtener datos de usuario de la otra ubicación.

## <a name="how-do-users-see-the-upd-on-the-server-side"></a>¿Cómo los usuarios ven la UDP en el servidor?

Cada usuario tendrá su propio directorio en el servidor que se asigna a su UDP: c:\Users\username.

## <a name="whats-the-best-way-to-use-outlook-and-upd"></a>¿Qué es la mejor manera de usar Outlook y UDP?

Azure RemoteApp guarda el estado de Outlook (buzones de correo, PST) entre sesiones. Para habilitar esta opción, debemos PST que almacenará en los datos de perfil de usuario (c:\users\<nombre de usuario >). Esta es la ubicación predeterminada de los datos, por lo tanto, siempre y cuando no cambiar la ubicación, se conservarán los datos entre sesiones.

También se recomienda que use el modo "en caché" en Outlook y utilizar "server o modo en línea" para realizar búsquedas.

Consulte [este artículo](remoteapp-outlook.md) para obtener más información sobre el uso de Outlook y RemoteApp de Azure.

## <a name="what-about-redirection"></a>¿Qué información sobre el redireccionamiento?
Puede configurar RemoteApp de Azure para permitir a los usuarios acceso a dispositivos locales mediante la configuración de [redireccionamiento](remoteapp-redirection.md). Dispositivos locales, a continuación, podrá tener acceso a los datos en el UDP.

## <a name="can-i-use-my-upd-as-a-network-share"></a>¿Puedo usar mi UDP como un recurso compartido de red?
No. UPDs no se puede utilizar como un recurso compartido de red. Un UDP solo está disponible para el usuario cuando el usuario activa está conectado a RemoteApp de Azure.

## <a name="if-i-delete-a-user-from-a-collection-is-their-upd-deleted"></a>¿Si se elimina un usuario de una colección, se elimina su UDP?

No, cuando se elimina un usuario, se elimina automáticamente la UDP: en su lugar, se almacenan los datos hasta que se elimina de la colección. 90 días después de eliminar la colección eliminamos UPDs todos. 

Si necesita eliminar un UDP de una colección, póngase en contacto con Azure RemoteApp - se puede eliminar UDP nuestra lado.

## <a name="can-i-access-my-users-upds-either-current-or-deleted-users"></a>¿Puedo tener acceso a UPDs de Mis usuarios (usuarios actuales o eliminados)?

Sí, si el contacto [RemoteApp de Azure](mailto:remoteappforum@microsoft.com), podemos configurar, con una dirección URL para tener acceso a los datos. Deberá unos 10 horas descargar los datos o archivos de la UDP antes de que caduque el acceso.

## <a name="are-upds-available-offline"></a>¿Están UPDs disponibles sin conexión?

En estos momentos no ofrecemos acceso sin conexión a UPDs, fuera de la ventana de access 10 horas descrita anteriormente. Esto significa que no tenemos una manera de proporcionar acceso durante mucho tiempo suficiente para completar las tareas más complicadas, como ejecutar software antivirus en el UPDs o acceso a los datos para una auditoría.

## <a name="do-registry-key-settings-persist"></a>¿Conservar los valores de clave del registro?
Sí, que se escribió HKEY_Current_User algo es parte de la UDP.

## <a name="can-i-disable-upds-for-a-collection"></a>¿Puedo deshabilitar UPDs para una colección de?

Sí, puede solicitar RemoteApp de Azure para deshabilitar UPDs para una suscripción, pero no puede hacer que usted mismo. Esto significa que se desactivarán UPDs para todas las colecciones de la suscripción.

Desea deshabilitar UPDs en cualquiera de las siguientes situaciones: 

- Necesita completar acceso y control de datos de usuario (para auditoría y revise los fines como instituciones financieras).
- Parte 3 º usuario tiene perfil administración soluciones locales y desea continuar usándolas durante la implementación de Azure RemoteApp unido al dominio. Esto requeriría el agente de perfil que debe cargarse en la imagen de oro. 
- No es necesario ningún almacenamiento de datos locales o tener todos los datos en la nube o archivo compartido y le gustaría control almacenamiento de datos local usando RemoteApp de Azure.

Para obtener más información, consulte [Deshabilitar usuario perfil discos (UPDs) en Azure RemoteApp](https://blogs.technet.microsoft.com/enterprisemobility/2015/11/11/disable-user-profile-disks-upds-in-azure-remoteapp/) .

## <a name="can-i-restrict-users-from-saving-data-to-the-system-drive"></a>¿Puedo impedir que los usuarios guardar los datos en la unidad de sistema?

Sí, pero deberá configurar que en la imagen de plantilla antes de crear la colección. Siga los pasos siguientes para bloquear el acceso a la unidad del sistema:

1. Ejecutar **gpedit.msc** en la imagen de la plantilla.
2. Vaya a **configuración de usuario > plantillas administrativas > componentes de Windows > Explorer**.
3. Seleccione las opciones siguientes:
    - **Ocultar estas unidades especificadas en Mi PC**
    - **Evitar el acceso a las unidades desde Mi PC**

## <a name="can-i-seed-upds-i-want-to-put-some-data-in-the-upd-thats-available-the-first-time-the-user-signs-in"></a>¿Iniciar UPDs? Quiero poner algunos datos en el UDP que está disponible la primera vez que el usuario inicia sesión.

Sí, cuando se crea la imagen de la plantilla, puede agregar información al perfil predeterminado. Esta información, a continuación, se agrega a la UDP.

## <a name="can-i-change-the-size-of-the-upd-depending-on-how-much-data-i-want-to-store"></a>¿Puedo cambiar el tamaño de la UDP según la cantidad de datos desea almacenar?

No, todos los UPDs tienen 50 GB de almacenamiento. Si desea almacenar diferentes cantidades de datos, intente lo siguiente:

1. Deshabilitar UPDs para la colección.
2. Configurar un recurso compartido de archivos para los usuarios tengan acceso.
3. Cargar el recurso compartido de archivos mediante un script de inicio. Consulte a continuación para obtener más información sobre las secuencias de comandos de inicio de RemoteApp de Azure.
4. Indique a los usuarios guardar todos los datos en el recurso compartido de archivos.


## <a name="how-do-i-run-a-startup-script-in-azure-remoteapp"></a>¿Cómo se puede ejecutar un script de inicio de RemoteApp de Azure?

Si desea ejecutar una secuencia de comandos de inicio, comience generando una tarea programada en la imagen de la plantilla que se va a usar para la colección. (Este *antes de* ejecutar sysprep.) 

![Crear una tarea del sistema](./media/remoteapp-upd/upd1.png)

![Crear una tarea de sistema que se ejecuta cuando un usuario inicia sesión](./media/remoteapp-upd/upd2.png)

En la pestaña **General** , asegúrese de cambiar la **Cuenta de usuario** en seguridad a "BUILTIN\Users".

![Cambiar la cuenta de usuario a un grupo](./media/remoteapp-upd/upd4.png)

La tarea iniciará la secuencia de comandos de inicio, con las credenciales del usuario. Programar la tarea para que se ejecute cada vez que un usuario inicia sesión.

![Establecer el desencadenador para la tarea como "al inicio de sesión"](./media/remoteapp-upd/upd3.png)

También puede usar [las secuencias de comandos de inicio de directiva de grupo](https://technet.microsoft.com/library/cc779329%28v=ws.10%29.aspx). 

## <a name="what-about-placing-a-startup-script-in-the-start-menu-would-that-work"></a>¿Se pueden colocar un script de inicio en el menú Inicio? ¿Funcionarían?

En otras palabras, ¿crear un archivo bat que se ejecuta una secuencia de comandos de la ventana de configuración y guardarla en la carpeta de Inicio\Programas\Inicio c:\ProgramData\Microsoft\Windows\Start y hacer que esa secuencia de comandos que se ejecute cada vez que un usuario inicia una sesión de RemoteApp?

No, que no es compatible con RemoteApp de Azure, que utiliza RDSH, que también no admite las secuencias de comandos de inicio en el menú Inicio.

## <a name="can-i-use-mstscexe-the-remote-desktop-program-to-configure-logon-scripts"></a>¿Puedo usar mstsc.exe (el programa de escritorio remoto) para configurar secuencias de comandos de inicio de sesión?

No, no admite RemoteApp de Azure.

## <a name="can-i-store-data-on-the-vm-locally"></a>¿Puedo almacenar datos en la máquina virtual de forma local?

NO, se perderán los datos almacenados en cualquier lugar en la máquina virtual distinto en el UDP. Hay muchas posibilidades el usuario no obtendrá la misma máquina virtual de la próxima vez que inicie sesión en Azure RemoteApp. No se mantiene la persistencia VM de usuario, por lo que el usuario no se inicie sesión en el misma VM y se perderán los datos. Además, cuando se actualiza la colección, las máquinas virtuales existentes se reemplazan por un nuevo conjunto de VM - que significa que se perderán los datos almacenados en la máquina virtual propiamente dicho. Se recomienda almacenar datos en el UDP, almacenamiento compartido como archivos de Azure, un servidor de archivos dentro de un VNET o en la nube mediante un sistema de almacenamiento de nube como DropBox.

## <a name="how-do-i-mount-an-azure-file-share-on-a-vm-using-powershell"></a>¿Cómo montaje de un recurso compartido de archivos de Azure en una máquina virtual, con PowerShell?

Puede usar el cmdlet de red PSDrive montaje la unidad, como sigue:

    New-PSDrive -Name <drive-name> -PSProvider FileSystem -Root \\<storage-account-name>.file.core.windows.net\<share-name> -Credential :<storage-account-name>


También puede guardar sus credenciales, ejecute lo siguiente:

    cmdkey /add:<storage-account-name>.file.core.windows.net /user:<storage-account-name> /pass:<storage-account-key>


Que le permite omitir parámetro - Credential en el cmdlet PSDrive de nuevo.

<properties
    pageTitle="Mensajes de error específicos de RDP para máquinas virtuales de Azure | Microsoft Azure"
    description="Comprender los mensajes de error específicos que pueden aparecer al intentar usar conexión a Escritorio remoto a una máquina virtual de Windows en Azure"
    keywords="Error de escritorio remoto, error de conexión a Escritorio remoto, no puede conectarse a máquina virtual, solución de problemas de escritorio remoto"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="support-article"
    ms.date="10/14/2016"
    ms.author="iainfou"/>

# <a name="troubleshooting-specific-rdp-error-messages-to-a-windows-vm-in-azure"></a>Solución de problemas de mensajes de error específicos de RDP a una máquina virtual de Windows en Azure
Puede recibir un mensaje de error específico cuando se usa conexión a Escritorio remoto a una máquina virtual (VM) de Windows Azure. Este artículo describe algunos de los mensajes de error más comunes que encuentra, junto con la solución de problemas de pasos para resolverlos. Si tiene problemas para conectarse a su máquina virtual con RDP pero no encuentran un mensaje de error específico, consulte la [Guía para escritorio remoto](virtual-machines-windows-troubleshoot-rdp-connection.md).

Para obtener información sobre los mensajes de error específico, consulte lo siguiente:

- [La sesión remota se ha desconectado porque no hay ningún servidor de licencias de escritorio remoto disponibles para proporcionar una licencia](#rdplicense).
- [Escritorio remoto no puede encontrar el equipo "nombre"](#rdpname).
- Se ha producido error [una autenticación. No puede ponerse en contacto con la autoridad de seguridad Local](#rdpauth).
- [Error de seguridad de Windows: las credenciales no funcionaron](#wincred).
- [Este equipo no puede conectarse al equipo remoto](#rdpconnect).

<a id="rdplicense"></a>
## <a name="the-remote-session-was-disconnected-because-there-are-no-remote-desktop-license-servers-available-to-provide-a-license"></a>La sesión remota se ha desconectado porque no hay ningún servidor de licencias de escritorio remoto disponibles para proporcionar una licencia.

Causa: El período de gracia de licencias de 120 días del rol de servidor de escritorio remoto ha caducado y necesita instalar licencias.

Como solución alternativa, guarde una copia local del archivo RDP desde el portal y ejecutar este comando en el símbolo del sistema de PowerShell para conectarse. Este paso deshabilita licencias para que acaba de conexión:

        mstsc <File name>.RDP /admin

Si realmente no necesita más de dos conexiones de escritorio remoto simultáneas VM, puede usar el administrador del servidor para quitar el rol de servidor de escritorio remoto.

Para obtener más información, consulte el blog [Azure VM se produce un error "No hay servidores de licencias de escritorio remoto disponibles"](https://blogs.msdn.microsoft.com/mast/2014/01/21/rdp-to-azure-vm-fails-with-no-remote-desktop-license-servers-available/).

<a id="rdpname"></a>
## <a name="remote-desktop-cant-find-the-computer-name"></a>Escritorio remoto no puede encontrar el equipo "nombre".

Causa: El cliente de escritorio remoto en su equipo no puede resolver el nombre del equipo en la configuración del archivo RDP.

Posibles soluciones:

- Si se encuentra en la intranet de la organización, asegúrese de que el equipo tiene acceso al servidor proxy y puede enviar tráfico HTTPS a él.

- Si está usando un archivo RDP almacenado localmente, intente usar que generados por el portal. Este paso asegura que tiene el nombre correcto de DNS para la máquina virtual, o el servicio de nube y el puerto de extremo de la máquina virtual. Aquí es un archivo RDP de ejemplo generado por el portal:

        full address:s:tailspin-azdatatier.cloudapp.net:55919
        prompt for credentials:i:1

La parte de la dirección de este archivo RDP tiene:
- El nombre de dominio completo del servicio de nube que contiene la máquina virtual ("tailspin-azdatatier.cloudapp.net" en este ejemplo).

- El puerto TCP externo del extremo para el tráfico de escritorio remoto (55919).

<a id="rdpauth"></a>
## <a name="an-authentication-error-has-occurred-the-local-security-authority-cannot-be-contacted"></a>Se ha producido un error de autenticación. No se puede conectar con la autoridad de seguridad Local.

Causa: La máquina virtual de destino no encuentra la entidad de seguridad en la parte del nombre de usuario de las credenciales.

Cuando es el nombre de usuario en el formulario *SecurityAuthority*\\*nombre de usuario* (ejemplo: CORP\User1), la parte de *SecurityAuthority* es el nombre del equipo de la máquina virtual (para la autoridad de seguridad local) o un nombre de dominio de Active Directory.

Posibles soluciones:

- Si la cuenta es local en la máquina virtual, asegúrese de que el nombre de la máquina virtual está escrito correctamente.

- Si la cuenta se encuentra en un dominio de Active Directory, compruebe la ortografía del nombre de dominio.

- Si es una cuenta de dominio de Active Directory y el nombre de dominio está escrito correctamente, compruebe que un controlador de dominio esté disponible en ese dominio. Es un problema común en Azure redes virtuales que contienen los controladores de dominio que un controlador de dominio no está disponible porque no se ha iniciado. Como solución alternativa, puede usar una cuenta de administrador local en lugar de una cuenta de dominio.

<a id="wincred"></a>
## <a name="windows-security-error-your-credentials-did-not-work"></a>Error de seguridad de Windows: las credenciales no funcionaron.

Causa: El destino VM no puede validar su nombre de cuenta y la contraseña.

Un equipo basado en Windows puede validar las credenciales de una cuenta local o una cuenta de dominio.

- Para las cuentas locales, use el *nombreDeEquipo*\\sintaxis de*nombre de usuario* (ejemplo: SQL1\Admin4798).
- Para cuentas de dominio, use el *nombre de dominio*\\sintaxis de*nombre de usuario* (ejemplo: CONTOSO\peterodman).

Si ha promovido el VM a un controlador de dominio en un nuevo bosque de Active Directory, la cuenta de administrador local que ha iniciado sesión en con se convierte en una cuenta equivalente con la misma contraseña en el nuevo bosque y el dominio. A continuación, se elimina la cuenta local.

Por ejemplo, si ha iniciado sesión con la cuenta local DC1\DCAdmin y convertidos la máquina virtual de controlador de dominio en un nuevo bosque para el dominio corp.contoso.com, se elimina la cuenta local DC1\DCAdmin y se crea una nueva cuenta de dominio (CORP\DCAdmin) con la misma contraseña.

Asegúrese de que el nombre de cuenta es un nombre de la máquina virtual puede comprobar como una cuenta válida y que la contraseña sea correcta.

Si necesita cambiar la contraseña de la cuenta de administrador local, consulte [cómo restablecer una contraseña o el servicio de escritorio remoto máquinas virtuales de Windows](virtual-machines-windows-reset-rdp.md).

<a id="rdpconnect"></a>
## <a name="this-computer-cant-connect-to-the-remote-computer"></a>Este equipo no puede conectarse al equipo remoto.

Causa: La cuenta que se usa para conectarse no tiene derechos de inicio de sesión de escritorio remoto.

Cada equipo con Windows tiene un grupo local de los usuarios de escritorio remoto, que contiene las cuentas y los grupos que pueden iniciar sesión en él de forma remota. Los miembros del grupo de administradores locales también tienen acceso, aunque estas cuentas no aparecen en el grupo local de usuarios de escritorio remoto. Para equipos unidos a un dominio, el grupo de administradores locales también contiene los administradores de dominio para el dominio.

Asegúrese de que la cuenta que usa para conectarse con tiene derechos de inicio de sesión de escritorio remoto. Como solución alternativa, use un dominio o una cuenta de administrador local para conectarse a través de escritorio remoto. Para agregar la cuenta que desee al grupo local de usuarios de escritorio remoto, use el complemento de consola de administración de Microsoft (**Herramientas del sistema > usuarios locales y grupos > grupos > usuarios de escritorio remoto**).


## <a name="next-steps"></a>Pasos siguientes
Si ninguno de estos errores ocurrieron y tiene un problema desconocido con conexión mediante RDP, consulte la [Guía para escritorio remoto](virtual-machines-windows-troubleshoot-rdp-connection.md).

- [Paquete de diagnósticos de Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)
- Para pasos para tener acceso a aplicaciones que se ejecutan en una máquina virtual de solución de problemas, consulte [solucionar problemas de acceso a una aplicación que se ejecuta en una máquina virtual de Azure](virtual-machines-linux-troubleshoot-app-connection.md).
- Si está teniendo problemas con Secure Shell (SSH) para conectarse a una VM Linux en Azure, consulte [solucionar problemas de SSH conexiones a una máquina virtual de Linux en Azure](virtual-machines-linux-troubleshoot-ssh-connection.md).
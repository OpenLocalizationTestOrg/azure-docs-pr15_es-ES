<properties
    pageTitle="Crear claves SSH en Linux y Mac | Microsoft Azure"
    description="Generar y usar claves SSH en Linux y Mac para el Administrador de recursos y modelos de implementación clásico de Azure."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="vlivech"
    manager="timlt"
    editor=""
    tags="" />

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/25/2016"
    ms.author="v-livech"/>

# <a name="create-ssh-keys-on-linux-and-mac-for-linux-vms-in-azure"></a>Cree claves SSH en Linux y Mac máquinas virtuales de Linux en Azure

Con un par de claves SSH puede crear máquinas virtuales en Azure utilizando SSH claves para la autenticación, de forma predeterminada sin necesidad de contraseñas iniciar sesión.  Las contraseñas pueden ser adivinar y abrir sus máquinas virtuales hasta constante fuerza bruta intenta adivinar la contraseña. Máquinas virtuales creadas con plantillas de Azure o los `azure-cli` pueden incluir su clave pública de SSH como parte de la implementación, quitar una configuración de implementación de la publicación.  Si se está conectando a una VM Linux desde Windows, vea [este documento.](virtual-machines-linux-ssh-from-windows.md)

El artículo requiere:

- una cuenta de Azure ([obtener una prueba gratuita](https://azure.microsoft.com/pricing/free-trial/)).

- [Azure CLI](../xplat-cli-install.md) iniciado sesión`azure login`

- el modo de administrador de recursos de Azure _debe estar en_ CLI de Azure`azure config mode arm`

## <a name="quick-commands"></a>Comandos rápidos

En los siguientes comandos, reemplace los ejemplos con sus propias opciones.

SSH claves son de forma predeterminada, se mantiene la `.ssh` directorio.  

```bash
cd ~/.ssh/
```

Si no tiene un `~/.ssh` directorio la `ssh-keygen` comando creará automáticamente con los permisos correctos.

```bash
ssh-keygen -t rsa -b 2048 -C "myusername@myserver"
```

Escriba el nombre del archivo que se guarda en el `~/.ssh/` directorio:

```bash
id_rsa
```

Escriba la frase de contraseña de id_rsa:

```bash
correct horse battery staple
```

Ahora hay una `id_rsa` y `id_rsa.pub` SSH de par de claves de la `~/.ssh` directorio.

```bash
ls -al ~/.ssh
```

Agregar la clave recién creada a `ssh-agent` en Linux y Mac (también se agrega al OSX llaves):

```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_rsa
```

Copie la clave pública SSH al servidor Linux:

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub myusername@myserver
```

Probar el uso de claves en lugar de una contraseña de inicio de sesión:

```bash
ssh -o PreferredAuthentications=publickey -o PubkeyAuthentication=yes -i ~/.ssh/id_rsa myusername@myserver
Last login: Tue April 12 07:07:09 2016 from 66.215.22.201
$
```

## <a name="detailed-walkthrough"></a>Tutorial detallado

Uso de claves públicas y privadas de SSH es la manera más sencilla de iniciar sesión en los servidores Linux. [Cifrado de clave pública](https://en.wikipedia.org/wiki/Public-key_cryptography) ofrece una manera mucho más segura para iniciar sesión su Linux o BSD VM en Azure que las contraseñas, que pueden ser forzado bruta mucho más fácilmente. La clave pública se puede compartir con nadie; pero solo usted (o su infraestructura de seguridad local) posee su clave privada.  La clave privada de SSH debe tener una [contraseña muy segura](https://www.xkcd.com/936/) (fuente:[xkcd.com](https://xkcd.com)) para protegerla.  Esta contraseña es solo para acceder a la clave privada de SSH y **no es** la contraseña de la cuenta de usuario.  Cuando agregue una contraseña para la clave SSH, cifra la clave privada para que la clave privada es inútil sin la contraseña para desbloquearla.  Si un intruso robaron su clave privada y clave no tiene una contraseña, podrán usar dicha clave privada para iniciar sesión en los servidores que tienen la clave pública correspondiente.  Si una clave privada está protegido por contraseña no puede ser usada por dicho intruso, lo que proporciona un nivel de seguridad adicional para la infraestructura de Azure.

En este artículo, se crea archivos clave *ssh rsa* con formato, que se recomiendan para implementaciones en el Administrador de recursos.  claves *SSH rsa* son necesarios en el [portal](https://portal.azure.com) para implementaciones estándar y el Administrador de recursos.

## <a name="create-the-ssh-keys"></a>Crear las claves SSH

Azure requiere al menos 2048 bits, ssh rsa formato claves públicas y privadas. Para crear el uso de claves `ssh-keygen`, que realiza una serie de preguntas y, a continuación, escribe una clave privada y una clave pública coincidente. Cuando se crea una máquina virtual de Azure, la clave pública se copia en `~/.ssh/authorized_keys`.  Teclas SSH en `~/.ssh/authorized_keys` se usan para determinar el cliente para que coincida con la clave privada correspondiente en una conexión de inicio de sesión SSH.

## <a name="using-ssh-keygen"></a>Uso de ssh keygen

Este comando crea una contraseña segura (cifrados) SSH par con RSA 2048 bits y muchos comentarios para identificar fácilmente.  

Iniciar cambiando directorios, para que todo su ssh se crean claves en el directorio.

```bash
cd ~/.ssh
```

Si no tiene un `~/.ssh` directorio la `ssh-keygen` comando creará automáticamente con los permisos correctos.

```bash
ssh-keygen -t rsa -b 2048 -C "myusername@myserver"
```

_Explicada de comando_

`ssh-keygen`= el programa utilizado para crear las claves

`-t rsa`= tipo de clave para crear que es el [RSA formato](https://en.wikipedia.org/wiki/RSA_(cryptosystem)

`-b 2048`= bits de la clave

`-C "myusername@myserver"`= un comentario al final del archivo de clave público para identificar fácilmente.  Normalmente se utiliza un correo electrónico como el comentario, pero puede usar cualquier cosa funciona mejor para su infraestructura.

### <a name="using-pem-keys"></a>Uso de claves PEM

Si está utilizando el clásico implementa modelo (Portal clásico de Azure o CLI de administración del servicio de Azure `asm`), debe usar PEM con formato de claves SSH para acceder a sus máquinas virtuales de Linux.  Aquí le mostramos cómo crear una clave PEM desde una SSH clave pública existente y un x509 existente certificado.

Para crear un PEM con formato de clave de una clave pública de SSH existente:

```bash
ssh-keygen -f ~/.ssh/id_rsa.pub -e > ~/.ssh/id_ssh2.pem
```

## <a name="example-of-ssh-keygen"></a>Ejemplo de ssh keygen

```bash
ssh-keygen -t rsa -b 2048 -C "myusername@myserver"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/myusername/.ssh/id_rsa): id_rsa
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in id_rsa.
Your public key has been saved in id_rsa.pub.
The key fingerprint is:
14:a3:cb:3e:78:ad:25:cc:55:e9:0c:08:e5:d1:a9:08 myusername@myserver
The key's randomart image is:
+--[ RSA 2048]----+
|        o o. .   |
|      E. = .o    |
|      ..o...     |
|     . o....     |
|      o S =      |
|     . + O       |
|      + = =      |
|       o +       |
|        .        |
+-----------------+
```

Guardar archivos de claves:

`Enter file in which to save the key (/home/myusername/.ssh/id_rsa): id_rsa`

El nombre de par de claves para este artículo.  Tener un par de claves denominado **id_rsa** es el valor predeterminado y algunas herramientas cabría esperar el nombre de archivo de clave privada **id_rsa** por lo que ésta es una buena idea. El directorio `~/.ssh/` es la ubicación predeterminada para los pares de claves SSH y el archivo de configuración SSH.

```bash
ls -al ~/.ssh
-rw------- 1 myusername staff  1675 Aug 25 18:04 id_rsa
-rw-r--r-- 1 myusername staff   410 Aug 25 18:04 rsa.pub
```
Una lista de los `~/.ssh` directorio. `ssh-keygen`crea la `~/.ssh` directorio si aún no está presente y también establece los modos de propiedad y archivo correctos.

Contraseña de la clave:

`Enter passphrase (empty for no passphrase):`

`ssh-keygen`hace referencia a una contraseña, como "una frase de contraseña".  Es *muy* recomendada agregar una contraseña a los pares de claves. Sin una par de claves de proteger con contraseña, cualquier usuario con el archivo de clave privada puede utilizar para iniciar sesión cualquier servidor que tenga la clave pública correspondiente. Agregar una contraseña ofrece más protección en caso de una persona puede tener acceso a su archivo de clave privada, dado tiempo para cambiar las teclas que se utiliza para autenticar.

## <a name="using-ssh-agent-to-store-your-private-key-password"></a>Uso de ssh agente para almacenar su contraseña de clave privada

Para evitar escribir la contraseña del archivo de clave privada con cada inicio de sesión SSH, puede usar `ssh-agent` en caché la contraseña del archivo de clave privada. Si está utilizando un equipo Mac, la cadena de claves OSX almacena de forma segura las contraseñas de clave privadas cuando se invoca `ssh-agent`.

En primer lugar, compruebe que `ssh-agent` se está ejecutando

```bash
eval "$(ssh-agent -s)"
```

Ahora, agregue la clave privada `ssh-agent` mediante el comando `ssh-add`.

```bash
ssh-add ~/.ssh/id_rsa
```

Ahora, la contraseña de clave privada se almacena en `ssh-agent`.

## <a name="create-and-configure-an-ssh-config-file"></a>Crear y configurar un archivo de configuración SSH

Es una práctica recomendada para crear y configurar un `~/.ssh/config` archivo para acelerar inicios de sesión y para optimizar el comportamiento del cliente SSH.

En el ejemplo siguiente se muestra una configuración estándar.

### <a name="create-the-file"></a>Crear el archivo

```bash
touch ~/.ssh/config
```

### <a name="edit-the-file-to-add-the-new-ssh-configuration"></a>Editar el archivo para agregar la nueva configuración de SSH:

```bash
vim ~/.ssh/config
```

### <a name="example-sshconfig-file"></a>Ejemplo `~/.ssh/config` archivo:

```bash
# Azure Keys
Host fedora22
  Hostname 102.160.203.241
  User myusername
# ./Azure Keys
# Default Settings
Host *
  PubkeyAuthentication=yes
  IdentitiesOnly=yes
  ServerAliveInterval=60
  ServerAliveCountMax=30
  ControlMaster auto
  ControlPath ~/.ssh/SSHConnections/ssh-%r@%h:%p
  ControlPersist 4h
  IdentityFile ~/.ssh/id_rsa
```

Esta configuración SSH ofrece secciones para cada servidor habilitar que tengan su propio par de claves dedicado. La configuración predeterminada (`Host *`) son para cualquier host que no cumplan ninguno de los hosts específicos más arriba en el archivo de configuración.


### <a name="config-file-explained"></a>Explicada el archivo de configuración

`Host`= el nombre del host que se llama en el terminal.  `ssh fedora22`indica `SSH` usar los valores en el bloque de configuración denominado `Host fedora22` Nota: puede ser cualquier etiqueta que es lógico para su uso y representa el nombre de host real de un servidor.

`Hostname 102.160.203.241`= la dirección IP o el nombre DNS para el servidor que se tiene acceso.

`User myusername`= la cuenta de usuario remoto para utilizar al iniciar sesión en el servidor.

`PubKeyAuthentication yes`= indica SSH que desea usar una clave SSH para iniciar sesión.

`IdentityFile /home/myusername/.ssh/id_id_rsa`= la clave privada de SSH y la clave pública correspondiente a usar para la autenticación.


## <a name="ssh-into-linux-without-a-password"></a>SSH en Linux sin una contraseña

Ahora que tiene un par de claves SSH y un archivo de configuración SSH configurado, es posible iniciar sesión en su VM Linux rápidamente y de forma segura. La primera vez que inicie sesión en un servidor con una clave SSH el símbolo del sistema para la frase de contraseña para ese archivo de clave.

```bash
ssh fedora22
```

### <a name="command-explained"></a>Explicada de comando

Cuando `ssh fedora22` se ejecuta SSH primero busca y carga cualquier configuración de la `Host fedora22` bloquear y, a continuación, carga todas las opciones restantes desde el último bloque, `Host *`.

## <a name="next-steps"></a>Pasos siguientes

Próximamente es crear máquinas virtuales de Linux Azure con la nueva clave pública SSH.  Máquinas virtuales de Azure que se crean con una clave pública SSH como el inicio de sesión son más seguras que máquinas virtuales creadas con el método de inicio de sesión de forma predeterminada, las contraseñas.  Máquinas virtuales de Azure creadas con claves SSH están configurados con contraseñas deshabilitadas, de forma predeterminada evitar intentos de averiguación fuerza bruta.

- [Crear una VM Linux seguro mediante una plantilla de Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
- [Crear una VM Linux seguro con el portal de Azure](virtual-machines-linux-quick-create-portal.md)
- [Crear una VM Linux seguro mediante la CLI de Azure](virtual-machines-linux-quick-create-cli.md)

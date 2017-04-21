<properties
    pageTitle="Deshabilitar las contraseñas SSH en su VM Linux configurando SSHD | Microsoft Azure"
    description="Proteger su Linux VM en Azure deshabilitando inicios de sesión de contraseña para SSH."
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
    ms.topic="article"
    ms.date="08/26/2016"
    ms.author="v-livech"/>

# <a name="disable-ssh-passwords-on-your-linux-vm-by-configuring-sshd"></a>Deshabilitar las contraseñas SSH en su VM Linux configurando SSHD

En este artículo se centra en cómo bloquear la seguridad de inicio de sesión de su VM Linux.  Tan pronto como se abre el puerto SSH 22 al inicio world robots intenta iniciar sesión adivinar contraseñas.  ¿Qué haremos en este artículo es deshabilitar inicios de sesión de contraseña sobre SSH.  Eliminando completamente la capacidad de usar las contraseñas se proteja la VM Linux este tipo de ataques.  La ventaja es que configurar SSHD Linux para permitir únicamente los inicios de sesión a través de SSH de claves públicas y privadas, con mucho, la forma más segura para iniciar sesión en Linux.  Las combinaciones posibles de la misma se requeriría adivinar la clave privada es inmensa y, por tanto, desalienta robots de molestarle incluso para intentar fuerza bruta SSH teclas.


## <a name="goals"></a>Objetivos

- Configurar SSHD para no permitir:
  - Inicios de sesión de contraseña
  - Inicio de sesión de usuario de raíz
  - Autenticación de desafío y respuesta
- Configurar SSHD para permitir que:
  - sólo SSH claves inicios de sesión
- Reinicie SSHD mientras todavía conectado
- Probar la nueva configuración SSHD

## <a name="introduction"></a>Introducción

[SSH definido](https://en.wikipedia.org/wiki/Secure_Shell)

SSHD es el servidor SSH que se ejecuta en la VM Linux.  SSH es un cliente que se ejecuta desde un shell en su estación de trabajo MacBook o Linux.  SSH también es el protocolo utilizado para proteger y cifrar la comunicación entre su estación de trabajo y la VM Linux.

En este artículo es muy importante mantener un inicio de sesión para su VM Linux abrir para todo recorra el.  Para ello se abrirá dos terminales SSH VM Linux de ambos.  Vamos a utilizar el primer terminal para realizar los cambios en el archivo de configuración de SSHDs y reinicie el servicio SSHD.  Vamos a utilizar la segunda terminal para probar esos cambios una vez que se reinicia el servicio.  Dado que estamos deshabilitar SSH contraseñas y depender estrictamente SSH teclas, si las claves SSH no son correctas y cierra la conexión a la máquina virtual, la máquina virtual se permanentemente bloqueado y nadie podrá iniciar sesión en ella que requieren que se elimina y vuelve a crear.

## <a name="prerequisites"></a>Requisitos previos

- [Cree claves SSH en Linux y Mac máquinas virtuales de Linux en Azure](virtual-machines-linux-mac-create-ssh-keys.md)
- Cuenta de Azure
  - [suscripción de prueba gratuita](https://azure.microsoft.com/pricing/free-trial/)
  - [Portal de Azure](http://portal.azure.com)
- VM Linux ejecuta en azure
- SSH par claves públicas y privadas en`~/.ssh/`
- Clave pública de SSH en `~/.ssh/authorized_keys` en la VM Linux
- Derechos de SUDO en la máquina virtual
- Puerto 22 abierto

## <a name="quick-commands"></a>Comandos rápidos

_Veteranos Linux Admins que la versión TLDR empiece aquí.  Para el resto de los que desea que la explicación detallada y recorrer omitir esta sección._

```
username@macbook$ sudo vim /etc/ssh/sshd_config

# Change PasswordAuthentication to this:
PasswordAuthentication no

# Change PubkeyAuthentication to this:
PubkeyAuthentication yes

# Change PermitRootLogin to this:
PermitRootLogin no

# Change ChallengeResponseAuthentication to this:
ChallengeResponseAuthentication no

# On the Debian Family
username@macbook$ sudo service ssh restart

# On the RedHat Family
username@macbook$ sudo service sshd restart
```

## <a name="detailed-walk-through"></a>Recorrer detallada

Inicie sesión en la VM Linux en terminal 1 (T1).  Inicie sesión en la VM Linux en terminal 2 (T2).

En T2, vamos a editar el archivo de configuración SSHD.  

```
username@macbook$ sudo vim /etc/ssh/sshd_config
```

Desde aquí se modificará solo la configuración para deshabilitar las contraseñas y habilitar SSH claves inicios de sesión.  Hay muchas opciones en el archivo que debe investigar y cambiar proteger SSH y Linux como como necesite.

#### <a name="disable-password-logins"></a>Deshabilitar los inicios de sesión de contraseña

```
username@macbook$ sudo vim /etc/ssh/sshd_config

# Change PasswordAuthentication to this:
PasswordAuthentication no
```

#### <a name="enable-public-key-authentication"></a>Habilitar la autenticación de clave pública

```
username@macbook$ sudo vim /etc/ssh/sshd_config

# Change PubkeyAuthentication to this:
PubkeyAuthentication yes
```

#### <a name="disable-root-login"></a>Deshabilitar el inicio de sesión de raíz

```
username@macbook$ sudo vim /etc/ssh/sshd_config

# Change PermitRootLogin to this:
PermitRootLogin no
```

#### <a name="disable-challenge-response-authentication"></a>Deshabilitar la autenticación de desafío y respuesta

```
# Change ChallengeResponseAuthentication to this:
ChallengeResponseAuthentication no
```

### <a name="restart-sshd"></a>Reinicie SSHD

Desde la consola de T1, compruebe que aún se registran en.  Esto es fundamental para que no se bloquee el VM si las claves SSH no son correctas, ya que ahora se deshabilitan las contraseñas.  Si cualquier valor es incorrecto en el VM Linux puede usar T1 para corregir sshd_config mientras que aún se registrarán en y SSH mantendrá la conexión activo durante el servicio SSHD reinicie.

De T2 ejecutar:

##### <a name="on-the-debian-family"></a>En la familia Debian

```
username@macbook$ sudo service ssh restart
```

##### <a name="on-the-redhat-family"></a>En la familia RedHat

```
username@macbook$ sudo service sshd restart
```

Las contraseñas están deshabilitadas ahora en la máquina virtual protegerse de intentos de inicio de sesión de contraseña de fuerza bruta.  Con solo SSH claves permitido que puede iniciar sesión más rápido y mucho más segura.

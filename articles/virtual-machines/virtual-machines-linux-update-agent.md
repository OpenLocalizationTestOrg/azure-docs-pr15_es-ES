<properties
    pageTitle="Actualizar el agente de Linux Azure desde GitHub | Microsoft Azure"
    description="Obtenga información sobre cómo la actualización Azure Linux agente para su Linux VM en Azure a la versión lateset de Github"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="SuperScottz"
    manager="timlt"
    editor=""
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/14/2015"
    ms.author="mingzhan"/>


# <a name="how-to-update-the-azure-linux-agent-on-a-vm-to-the-latest-version-from-github"></a>Cómo actualizar al agente de Linux de Azure en una máquina virtual a la versión más reciente de GitHub

Para actualizar el [Agente de Azure Linux](https://github.com/Azure/WALinuxAgent) en una VM Linux en Azure, ya debe tener:

1. Una VM en ejecución Linux en Azure.
2. Una conexión a esa VM Linux mediante SSH.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

<br>

> [AZURE.NOTE] Si va a realizar esta tarea desde un equipo con Windows, puede usar PuTTY a SSH en su equipo Linux. Para obtener más información, vea [cómo iniciar sesión en la máquina Virtual ejecuta Linux](virtual-machines-linux-mac-create-ssh-keys.md).

Respaldo de Azure Linux distros han instalado el paquete de Azure Linux agente sus repositorios, así que compruebe e instalar la última versión de repositorio Distro primero si es posible.  

Para Ubuntu, escriba:

    #sudo apt-get install walinuxagent

Y en CentOS, escriba:

    #sudo yum install waagent


Para Oracle Linux, asegúrese de que el `Addons` repositorio está habilitado. Elija Editar el archivo `/etc/yum.repos.d/public-yum-ol6.repo`(Oracle Linux 6) o `/etc/yum.repos.d/public-yum-ol7.repo`(Oracle Linux) y cambie la línea `enabled=0` a `enabled=1` en **[ol6_addons]** o **[ol7_addons]** en este archivo.

A continuación, para instalar la versión más reciente del agente de Linux Azure, escriba:


    #sudo yum install WALinuxAgent

Si no encuentra el repositorio de complemento simplemente agregue estas líneas al final del archivo .repo según la versión de Oracle Linux:

Máquinas virtuales de Oracle Linux 6:

    [ol6_addons]
    name=Add-Ons for Oracle Linux $releasever ($basearch)
    baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL6/addons/x86_64
    gpgkey=http://public-yum.oracle.com/RPM-GPG-KEY-oracle-ol6
    gpgcheck=1
    enabled=1

Máquinas virtuales de Oracle Linux 7:

    [ol7_addons]
    name=Oracle Linux $releasever Add ons ($basearch)
    baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL7/addons/$basearch/
    gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
    gpgcheck=1
    enabled=0

A continuación, escriba:

    #sudo yum update WALinuxAgent

Normalmente esto es todo lo que necesita, pero si por algún motivo debe instalar desde https://github.com directamente, siga los pasos siguientes.


## <a name="install-wget"></a>Instalar wget

Inicie sesión en la máquina virtual mediante SSH.

Instalar wget (hay algunas distros que no se instala como Redhat, CentOS y Oracle Linux versiones 6.4 y 6.5 de forma predeterminada) escribiendo `#sudo yum install wget` en la línea de comandos.


## <a name="download-the-latest-version"></a>Descargar la versión más reciente

Abra [la versión de agente de Linux de Azure en GitHub](https://github.com/Azure/WALinuxAgent/releases) en una página web y averigüe el número de versión más reciente. (Puede encontrar su versión actual escribiendo `#waagent --version`.)

### <a name="for-version-20x-type"></a>Para la versión 2.0, escriba:

    #wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-[version]/waagent  

   La siguiente línea utiliza versión 2.0.14 como ejemplo:

    #wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-2.0.14/waagent  

### <a name="for-version-21x-or-later-type"></a>Versión 2.1. x o posterior, escriba:

    #wget https://github.com/Azure/WALinuxAgent/archive/WALinuxAgent-[version].zip
    #unzip WALinuxAgent-[version].zip
    #cd WALinuxAgent-[version]

   La siguiente línea utiliza versión 2.1.0 como ejemplo:

    #wget https://github.com/Azure/WALinuxAgent/archive/WALinuxAgent-2.1.0.zip
    #unzip WALinuxAgent-2.1.0.zip  
    #cd WALinuxAgent-2.1.0

## <a name="install-the-azure-linux-agent"></a>Instalar al agente de Linux Azure

### <a name="for-version-20x-use"></a>Para la versión 2.0, use:

 Asegúrese de waagent ejecutable:

    #chmod +x waagent

 Copiar el archivo ejecutable de nuevo en/usr/sbin /.

  Para la mayoría de Linux, use:

    #sudo cp waagent /usr/sbin

  Para CoreOS, use:

    #sudo cp waagent /usr/share/oem/bin/

  Si se trata de una nueva instalación del agente de Azure Linux, ejecute:
 
    #sudo /usr/sbin/waagent -install -verbose

### <a name="for-version-21x-use"></a>Versión 2.1. x, use:

Tendrá que instalar el paquete `setuptools` consulte en primer lugar, [aquí](https://pypi.python.org/pypi/setuptools). A continuación, ejecute:

    #sudo python setup.py install

## <a name="restart-the-waagent-service"></a>Reinicie el servicio de waagent

Para la mayoría de linux Distros:

    #sudo service waagent restart

Para Ubuntu, use:

    #sudo service walinuxagent restart

Para CoreOS, use:

    #sudo systemctl restart waagent

## <a name="confirm-the-azure-linux-agent-version"></a>Confirme la versión de agente de Linux de Azure

    #waagent -version

Para CoreOS, puede no funcionar el comando anterior.

Verá que la versión de agente de Azure Linux se ha actualizado a la versión nueva.

Para obtener más información sobre el agente de Linux Azure, vea [Léame de agente de Azure Linux](https://github.com/Azure/WALinuxAgent).

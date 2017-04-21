<properties
    pageTitle="Configurar MySQL en una VM Linux | Microsoft Azure "
    description="Obtenga información sobre cómo instalar la pila de MySQL en una máquina virtual de Linux (Ubuntu o RedHat familia OS) en Azure"
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
    ms.date="02/01/2016"
    ms.author="mingzhan"/>


#<a name="how-to-install-mysql-on-azure"></a>Cómo instalar MySQL en Azure


En este artículo, aprenderá cómo instalar y configurar MySQL en una máquina virtual Azure con Linux.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


##<a name="install-mysql-on-your-virtual-machine"></a>Instalar MySQL en su máquina virtual

> [AZURE.NOTE] Debe haber una máquina virtual de Microsoft Azure ejecutan Linux para poder completar este tutorial. Consulte el [tutorial de Azure Linux VM](virtual-machines-linux-quick-create-cli.md) para crear y configurar una VM Linux con `mysqlnode` como el nombre de la máquina virtual y `azureuser` como usuario antes de continuar.

En este caso, use el puerto 3306 como el puerto MySQL.  

Conectarse a la máquina virtual creado a través de putty Linux. Si es la primera vez que use Azure Linux VM, vea cómo usar putty conectarse a una VM Linux [aquí](virtual-machines-linux-mac-create-ssh-keys.md).

Paquete de repositorio se utilizará para instalar MySQL5.6 como un ejemplo de este artículo. MySQL5.6 tiene en realidad más mejora el rendimiento MySQL5.5.  Más información [aquí](http://www.mysqlperformanceblog.com/2013/02/18/is-mysql-5-6-slower-than-mysql-5-5/).


###<a name="how-to-install-mysql56-on-ubuntu"></a>Cómo instalar MySQL5.6 en Ubuntu
Vamos a usar Linux VM con Ubuntu de Azure aquí.

- Paso 1: Instalar MySQL Server 5.6 cambiar a `root` usuario:

            #[azureuser@mysqlnode:~]sudo su -

    Instalar el servidor mysql 5.6:

            #[root@mysqlnode ~]# apt-get update
            #[root@mysqlnode ~]# apt-get -y install mysql-server-5.6

    Durante la instalación, verá un poping de la ventana de diálogo hasta pida establecer contraseña de raíz de MySQL a continuación, debe establecer la contraseña aquí.

    ![imagen](./media/virtual-machines-linux-mysql-install/virtual-machines-linux-install-mysql-p1.png)


    Escriba la contraseña nuevamente para confirmar.

    ![imagen](./media/virtual-machines-linux-mysql-install/virtual-machines-linux-install-mysql-p2.png)

- Paso 2: Servidor MySQL

    Cuando finalice la instalación del servidor MySQL, servicio MySQL se iniciará automáticamente. Puede iniciar la sesión de servidor MySQL con `root` usuario.
    Usar la debajo de comando para la contraseña de inicio de sesión y la entrada.

             #[root@mysqlnode ~]# mysql -uroot -p

- Paso 3: Administrar el servicio MySQL en ejecución

    (a) obtener el estado del servicio MySQL

             #[root@mysqlnode ~]# service mysql status

    (b) iniciar el servicio MySQL

             #[root@mysqlnode ~]# service mysql start

    (c) detener el servicio MySQL

             #[root@mysqlnode ~]# service mysql stop

    (d) reiniciar el servicio MySQL

             #[root@mysqlnode ~]# service mysql restart


###<a name="how-to-install-mysql-on-red-hat-os-family-like-centos-oracle-linux"></a>Cómo instalar MySQL en familia rojos sombrero OS como CentOS, Linux de Oracle
Vamos a usar Linux VM con CentOS u Oracle Linux aquí.

- Paso 1: Agregar el repositorio de MySQL Yum cambiar a `root` usuario:

            #[azureuser@mysqlnode:~]sudo su -

    Descargar e instalar el paquete de la versión de MySQL:

            #[root@mysqlnode ~]# wget http://repo.mysql.com/mysql-community-release-el6-5.noarch.rpm
            #[root@mysqlnode ~]# yum localinstall -y mysql-community-release-el6-5.noarch.rpm

- Paso 2: Editar debajo de archivo para habilitar el repositorio de MySQL para descargar el paquete MySQL5.6.

            #[root@mysqlnode ~]# vim /etc/yum.repos.d/mysql-community.repo

    Actualizar cada valor de este archivo a continuación:

        \# *Enable to use MySQL 5.6*

        [mysql56-community]
        name=MySQL 5.6 Community Server

        baseurl=http://repo.mysql.com/yum/mysql-5.6-community/el/6/$basearch/

        enabled=1

        gpgcheck=1

        gpgkey=file:/etc/pki/rpm-gpg/RPM-GPG-KEY-mysql

- Paso 3: Instalar MySQL MySQL repositorio MySQL instalar:

           #[root@mysqlnode ~]#yum install mysql-community-server

    Se instalará el paquete RPM MySQL y todos los paquetes relacionados.

- Paso 4: Administrar el servicio MySQL en ejecución

    (a) Compruebe el estado del servicio del servidor MySQL:

           #[root@mysqlnode ~]#service mysqld status

    (b) Compruebe si se está ejecutando el servidor de puerto de MySQL predeterminado:

           #[root@mysqlnode ~]#netstat  –tunlp|grep 3306


    (c) el servidor MySQL de inicio:

           #[root@mysqlnode ~]#service mysqld start

    (d) dejar el servidor MySQL:

           #[root@mysqlnode ~]#service mysqld stop

    (e) establecer MySQL al iniciar la seguridad de inicio de sistema:

           #[root@mysqlnode ~]#chkconfig mysqld on


###<a name="how-to-install-mysql-on-suse-linux"></a>Cómo instalar MySQL en SUSE Linux
Vamos a usar Linux VM con OpenSUSE aquí.

- Paso 1: Descargar e instalar a servidor MySQL

    Cambiar a `root` usuario por debajo de comando:  

           #sudo su -

    Descargar e instalar el paquete de MySQL:

           #[root@mysqlnode ~]# zypper update

           #[root@mysqlnode ~]# zypper install mysql-server mysql-devel mysql

- Paso 2: Administrar el servicio MySQL en ejecución

    (a) comprobar el estado del servidor MySQL:

           #[root@mysqlnode ~]# rcmysql status

    (b) comprobar si el puerto predeterminado del servidor MySQL:

           #[root@mysqlnode ~]# netstat  –tunlp|grep 3306


    (c) el servidor MySQL de inicio:

           #[root@mysqlnode ~]# rcmysql start

    (d) dejar el servidor MySQL:

           #[root@mysqlnode ~]# rcmysql stop

    (e) establecer MySQL al iniciar la seguridad de inicio de sistema:

           #[root@mysqlnode ~]# insserv mysql

###<a name="next-step"></a>Siguiente paso
Encontrará más información acerca de MySQL [aquí](https://www.mysql.com/)y uso.

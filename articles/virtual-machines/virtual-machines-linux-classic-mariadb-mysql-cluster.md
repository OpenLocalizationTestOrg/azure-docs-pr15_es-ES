<properties
    pageTitle="Ejecuta un clúster MariaDB (MySQL) en Azure"
    description="Crear un MariaDB + Galera MySQL clúster en Azure máquinas virtuales de Windows"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="sabbour"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure-services"
    ms.date="04/15/2015"
    ms.author="v-ahsab"/>

# <a name="mariadb-mysql-cluster---azure-tutorial"></a>MariaDB (MySQL) clúster: tutorial de Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

> [AZURE.NOTE]  Clúster de MariaDB Enterprise está ahora disponible en Azure Marketplace.  La nueva oferta implementará automáticamente un clúster de MariaDB Galera en el BRAZO. Debe usar la nueva oferta de https://azure.microsoft.com/en-us/marketplace/partners/mariadb/cluster-maxscale/ 

Estamos creando un clúster de [Galera](http://galeracluster.com/products/) múltiples maestro de [MariaDBs](https://mariadb.org/en/about/), reemplaza sólida, scalable y confiable de orden MySQL para trabajar en un entorno altamente disponible en Azure máquinas virtuales de Windows.

## <a name="architecture-overview"></a>Introducción a la arquitectura

En este tema se realiza los pasos siguientes:

1. Crear un clúster de nodo 3
2. Separar los discos de datos desde el disco de sistema operativo
3. Crear los discos de datos en la configuración de RAID-0/seccionados a aumentar IOPS
4. Usar el equilibrio de carga de Azure para equilibrar la carga para los 3 nodos
5. Para reducir el trabajo repetitivo, crear una imagen de máquina virtual que contenga MariaDB + Galera y usarlo para crear el clúster otro máquinas virtuales.

![Arquitectura](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/Setup.png)

> [AZURE.NOTE]  Este tema usa las herramientas de [Azure CLI](../xplat-cli-install.md) , asegúrese de descargar y conectarlos a su suscripción de Azure según las instrucciones. Si necesita una referencia a los comandos disponibles en CLI Azure, consulte la [referencia de comandos de CLI de Azure](../virtual-machines-command-line-tools.md)en este vínculo. También se necesita para [crear una tecla SSH para la autenticación] y tome nota de la **ubicación del archivo .pem**.


## <a name="creating-the-template"></a>Crear la plantilla

### <a name="infrastructure"></a>Infraestructura de

1. Crear un grupo de afinidad para mantener los recursos unidas

        azure account affinity-group create mariadbcluster --location "North Europe" --label "MariaDB Cluster"

2. Crear una red Virtual

        azure network vnet create --address-space 10.0.0.0 --cidr 8 --subnet-name mariadb --subnet-start-ip 10.0.0.0 --subnet-cidr 24 --affinity-group mariadbcluster mariadbvnet

3. Crear una cuenta de almacenamiento para hospedar todos nuestros discos. Nota que no se pueden colocar más de 40 intensamente usa discos en la misma cuenta de almacenamiento para evitar que se alcance el límite de cuenta de almacenamiento IOPS 20.000. En este caso, estamos lejos de este número para a almacenar todo en la misma cuenta para simplificar

        azure storage account create mariadbstorage --label mariadbstorage --affinity-group mariadbcluster

3. Busque el nombre de la imagen de la máquina Virtual de CentOS 7

        azure vm image list | findstr CentOS
Esto generará algo parecido a `5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20140926`. Use el nombre en el paso siguiente.

4. Crear la plantilla de máquina virtual reemplazando **/path/to/key.pem** con la ruta de acceso que se ha guardado la clave SSH .pem generado

        azure vm create --virtual-network-name mariadbvnet --subnet-names mariadb --blob-url "http://mariadbstorage.blob.core.windows.net/vhds/mariadbhatemplate-os.vhd"  --vm-size Medium --ssh 22 --ssh-cert "/path/to/key.pem" --no-ssh-password mariadbtemplate 5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20140926 azureuser

5. Adjuntar discos de datos de 4 x 500GB a la máquina virtual para su uso en la configuración de RAID

        FOR /L %d IN (1,1,4) DO azure vm disk attach-new mariadbhatemplate 512 http://mariadbstorage.blob.core.windows.net/vhds/mariadbhatemplate-data-%d.vhd

6. SSH en la plantilla de máquina virtual que ha creado en **mariadbhatemplate.cloudapp.net:22** y conecta utilizando su clave privada.

### <a name="software"></a>Software

1. Obtener la raíz

        sudo su

2. Instalar la compatibilidad RAID:

     - Instalar mdadm

                yum install mdadm

     - Crear la configuración RAID0 o bandas con un sistema de archivos EXT4

                mdadm --create --verbose /dev/md0 --level=stripe --raid-devices=4 /dev/sdc /dev/sdd /dev/sde /dev/sdf
                mdadm --detail --scan >> /etc/mdadm.conf
                mkfs -t ext4 /dev/md0

     - Crear el directorio de punto de montaje

                mkdir /mnt/data

     - Recuperar el UUID del dispositivo RAID recién creado

                blkid | grep /dev/md0

     - Editar/etc/fstab

                vi /etc/fstab

     - Agregar el dispositivo en allí para habilitar montaje automático al reiniciar reemplazar el UUID con el valor obtenido el comando **blkid** antes de

                UUID=<UUID FROM PREVIOUS>   /mnt/data ext4   defaults,noatime   1 2

     - Montaje de la nueva partición

                mount /mnt/data

3. Instalar MariaDB:

     - Crear el archivo MariaDB.repo:

                vi /etc/yum.repos.d/MariaDB.repo

     - Rellénela con el debajo de contenido

                [mariadb]
                name = MariaDB
                baseurl = http://yum.mariadb.org/10.0/centos7-amd64
                gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
                gpgcheck=1

     - Quitar sufijo existente y mariadb bibliotecas para evitar conflictos

            yum remove postfix mariadb-libs-*

     - Instalar MariaDB con Galera

            yum install MariaDB-Galera-server MariaDB-client galera

4. Mover el directorio de datos MySQL en el dispositivo de bloque RAID

     - Copie el directorio MySQL actual en su nueva ubicación y quitar el directorio antiguo

            cp -avr /var/lib/mysql /mnt/data  
            rm -rf /var/lib/mysql

     - Establecer permisos en el nuevo directorio según corresponda

            chown -R mysql:mysql /mnt/data && chmod -R 755 /mnt/data/  

     - Crear un vínculo simbólico que señala el directorio antiguo a la nueva ubicación en la partición RAID

            ln -s /mnt/data/mysql /var/lib/mysql

5. Dado que [que SELinux interfieran con las operaciones de clúster](http://galeracluster.com/documentation-webpages/configuration.html#selinux), es necesario deshabilitar para la sesión actual (hasta que aparezca una versión compatible). Editar `/etc/selinux/config` para deshabilitar para reiniciar las siguientes:

            setenforce 0

       then editing `/etc/selinux/config` to set `SELINUX=permissive`

6. Validar ejecuciones MySQL

    - Iniciar MySQL

            service mysql start

    - Proteger la instalación de MySQL, establezca la contraseña de raíz, eliminar usuarios anónimos, deshabilitación de inicio de sesión remota raíz y la eliminación de la base de datos de prueba

            mysql_secure_installation

    - Crear un usuario en la base de datos de las operaciones de clúster y, opcionalmente, las aplicaciones

            mysql -u root -p
            GRANT ALL PRIVILEGES ON *.* TO 'cluster'@'%' IDENTIFIED BY 'p@ssw0rd' WITH GRANT OPTION; FLUSH PRIVILEGES;
            exit

   - Detener MySQL

            service mysql stop

7. Crear el marcador de posición de configuración

    - Editar la configuración de MySQL para crear un marcador de posición para la configuración de clúster. No reemplazar la **`<Vairables>`** o quite ahora. Que sucederá después de haber creado una máquina virtual de esta plantilla.

            vi /etc/my.cnf.d/server.cnf

    - Editar la sección **[galera]** y borrar los

    - Modifique la sección **[mariadb]**

            wsrep_provider=/usr/lib64/galera/libgalera_smm.so
            binlog_format=ROW
            wsrep_sst_method=rsync
            bind-address=0.0.0.0 # When set to 0.0.0.0, the server listens to remote connections
            default_storage_engine=InnoDB
            innodb_autoinc_lock_mode=2

            wsrep_sst_auth=cluster:p@ssw0rd # CHANGE: Username and password you created for the SST cluster MySQL user
            #wsrep_cluster_name='mariadbcluster' # CHANGE: Uncomment and set your desired cluster name
            #wsrep_cluster_address="gcomm://mariadb1,mariadb2,mariadb3" # CHANGE: Uncomment and Add all your servers
            #wsrep_node_address='<ServerIP>' # CHANGE: Uncomment and set IP address of this server
            #wsrep_node_name='<NodeName>' # CHANGE: Uncomment and set the node name of this server

8. Abra los puertos necesarios en el servidor de seguridad (mediante FirewallD CentOS 7)

    - MySQL:`firewall-cmd --zone=public --add-port=3306/tcp --permanent`
    - GALERA:`firewall-cmd --zone=public --add-port=4567/tcp --permanent`
    - TSI GALERA:`firewall-cmd --zone=public --add-port=4568/tcp --permanent`
    - RSYNC:`firewall-cmd --zone=public --add-port=4444/tcp --permanent`
    - Volver a cargar el firewall:`firewall-cmd --reload`

9.  Optimizar el sistema de rendimiento. Consulte este artículo sobre la [estrategia de optimización del rendimiento](virtual-machines-linux-classic-optimize-mysql.md) para obtener más detalles

    - Editar el archivo de configuración de MySQL de nuevo

            vi /etc/my.cnf.d/server.cnf

    - Modifique la sección **[mariadb]** y anexar la debajo

    > [AZURE.NOTE] Se recomienda que **innodb\_búfer\_pool_size** ser 70% de memoria de la máquina virtual. Se ha establecido en 2,45 GB aquí para medio Azure VM con 3,5 GB de RAM.

            innodb_buffer_pool_size = 2508M # The buffer pool contains buffered data and the index. This is usually set to 70% of physical memory.
            innodb_log_file_size = 512M #  Redo logs ensure that write operations are fast, reliable, and recoverable after a crash
            max_connections = 5000 # A larger value will give the server more time to recycle idled connections
            innodb_file_per_table = 1 # Speed up the table space transmission and optimize the debris management performance
            innodb_log_buffer_size = 128M # The log buffer allows transactions to run without having to flush the log to disk before the transactions commit
            innodb_flush_log_at_trx_commit = 2 # The setting of 2 enables the most data integrity and is suitable for Master in MySQL cluster
            query_cache_size = 0

10. Detener MySQL, activar el equipo y deshabilitar el servicio MySQL ejecuten durante el inicio para evitar alterar el clúster al agregar un nuevo nodo.

        service mysql stop
        chkconfig mysql off
        waagent -deprovision

11. Captura de la máquina virtual a través del portal. (Actualmente, herramientas de [problema #1268 en Azure CLI] describe el hecho de que imágenes capturadas por las herramientas de Azure CLI no capturar los discos de datos adjuntos).

    - Cierre el equipo a través del portal
    - Haga clic en captura y especifique el nombre de la imagen como **imagen de galera mariadb** y proporcione una descripción y compruebe "He ejecutado waagent".
    ![Capturar la máquina Virtual](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/Capture.png)
    ![capturar la máquina Virtual](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/Capture2.PNG)

## <a name="creating-the-cluster"></a>Crear el clúster

Crear máquinas 3 virtuales fuera de la plantilla que acaba de crea y, a continuación, configura e iniciar el clúster.

1. Crear la primera VM 7 de CentOS desde la imagen de la **imagen de galera mariadb** que creó, proporcionar el nombre de red virtual **mariadbvnet** y la subred **mariadb**, tamaño **medio**, pasando el servicio de nube de máquina nombre sea **mariadbha** (o el nombre que desee tener acceso a través de mariadbha.cloudapp.net), establecer el nombre de este equipo para que sea el nombre de usuario sean **azureuser**y **mariadb1** y habilitar acceso SSH y pasando el SSH de certificados .pem archivo y reemplazar **/path/to/key.pem** con la ruta de acceso donde se almacena la clave SSH .pem generado.

    > [AZURE.NOTE] Los comandos siguientes se dividen en varias líneas para claridad, pero debe escribir cada uno como una sola línea.

        azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb
        --availability-set clusteravset
        --vm-size Medium
        --ssh-cert "/path/to/key.pem"
        --no-ssh-password
        --ssh 22
        --vm-name mariadb1
        mariadbha mariadb-galera-image azureuser

2. Crear 2 más máquinas virtuales mediante una _conexión_ a creadas actualmente **mariadbha** servicio de nube, cambiar el **nombre de la máquina virtual** , así como el **puerto SSH** a un único puerto no entra en conflicto con otras máquinas virtuales en el mismo servicio de nube.

        azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb
        --availability-set clusteravset
        --vm-size Medium
        --ssh-cert "/path/to/key.pem"
        --no-ssh-password
        --ssh 23
        --vm-name mariadb2
        --connect mariadbha mariadb-galera-image azureuser
y para MariaDB3

        azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb
        --availability-set clusteravset
        --vm-size Medium
        --ssh-cert "/path/to/key.pem"
        --no-ssh-password
        --ssh 24
        --vm-name mariadb3
        --connect mariadbha mariadb-galera-image azureuser

3. Debe obtener la dirección IP interna de cada una de las 3 VM para el siguiente paso:

    ![Obtener la dirección IP](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/IP.png)

4. SSH en las 3 VM y y editar el archivo de configuración en cada uno

        sudo vi /etc/my.cnf.d/server.cnf

    comentario de **`wsrep_cluster_name`** y **`wsrep_cluster_address`** eliminando la **#** al principio y validación son en realidad lo que desea.
    Asimismo, reemplace **`<ServerIP>`** en **`wsrep_node_address`** y **`<NodeName>`** en **`wsrep_node_name`** de la máquina virtual con la dirección y nombre respectivamente y quite los comentarios de las líneas también.

5. Iniciar el clúster en MariaDB1 y deje que se ejecute en el inicio

        sudo service mysql bootstrap
        chkconfig mysql on

6. Iniciar MySQL en MariaDB2 y MariaDB3 y deje que se ejecute en el inicio

        sudo service mysql start
        chkconfig mysql on

## <a name="load-balancing-the-cluster"></a>El clúster de equilibrio de carga
Cuando cree las máquinas virtuales agrupadas, se agrega a una disponibilidad establecido denominado **clusteravset** para asegurarse de que se colocan en diferentes errores y actualizar dominios y que Azure no nunca mantenimiento en todos los equipos a la vez. Esta configuración cumple los requisitos del que sea compatible con por el contrato de nivel de servicio (SLA) de ese Azure.

Ahora use el equilibrador de carga de Azure equilibrar las solicitudes entre nuestros 3 nodos.

Ejecutar la debajo de comandos en el equipo mediante la CLI de Azure.
La estructura de parámetros de comando es:`azure vm endpoint create-multiple <MachineName> <PublicPort>:<VMPort>:<Protocol>:<EnableDirectServerReturn>:<Load Balanced Set Name>:<ProbeProtocol>:<ProbePort>`

    azure vm endpoint create-multiple mariadb1 3306:3306:tcp:false:MySQL:tcp:3306
    azure vm endpoint create-multiple mariadb2 3306:3306:tcp:false:MySQL:tcp:3306
    azure vm endpoint create-multiple mariadb3 3306:3306:tcp:false:MySQL:tcp:3306

Por último, dado que CLI establece el intervalo de sondeo de equilibrador de carga en 15 segundos (que puede ser un poco demasiado largos), lo cambia en el portal de **extremos** por cualquiera de las máquinas virtuales

![Editar extremo](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/Endpoint.PNG)

a continuación, haga clic en establecer Reconfigure The Load-Balanced y vaya siguiente

![Vuelva a configurar cargar conjunto equilibrada](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/Endpoint2.PNG)

a continuación, cambie el intervalo de sondeo a 5 segundos y guardar

![Intervalo de sondeo de cambio](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/Endpoint3.PNG)

## <a name="validating-the-cluster"></a>Validar el clúster

Se realiza el trabajo. Debe ser el clúster ahora accesible en `mariadbha.cloudapp.net:3306` que se visitas equilibrador de carga y enrutar solicitudes entre las 3 VM sin problemas y de forma eficaz.

Usar al cliente de MySQL favorito para conectarse o solo una de las máquinas virtuales para comprobar que este clúster está funcionando.

     mysql -u cluster -h mariadbha.cloudapp.net -p

A continuación, crear una nueva base de datos y rellenar con algunos datos

    CREATE DATABASE TestDB;
    USE TestDB;
    CREATE TABLE TestTable (id INT NOT NULL AUTO_INCREMENT PRIMARY KEY, value VARCHAR(255));
    INSERT INTO TestTable (value)  VALUES ('Value1');
    INSERT INTO TestTable (value)  VALUES ('Value2');
    SELECT * FROM TestTable;

Se traducirán en la tabla siguiente

    +----+--------+
  	| id | value  |
    +----+--------+
  	|  1 | Value1 |
  	|  4 | Value2 |
    +----+--------+
    2 rows in set (0.00 sec)

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Pasos siguientes

En este artículo, se ha creado un 3 nodo MariaDB + clúster altamente disponible de Galera en Azure máquinas virtuales que ejecutan CentOS 7. Las máquinas virtuales son el equilibrio de carga de Azure equilibrada de carga.

Desea Eche un vistazo a [otra forma de clúster MySQL en Linux](virtual-machines-linux-classic-mysql-cluster.md) y formas de [optimizar y probar el rendimiento de MySQL en máquinas virtuales de Azure Linux](virtual-machines-linux-classic-optimize-mysql.md).

<!--Anchors-->
[Architecture overview]: #architecture-overview
[Creating the template]: #creating-the-template
[Creating the cluster]: #creating-the-cluster
[Load balancing the cluster]: #load-balancing-the-cluster
[Validating the cluster]: #validating-the-cluster
[Next steps]: #next-steps

<!--Image references-->

<!--Link references-->
[Galera]: http://galeracluster.com/products/
[MariaDBs]: https://mariadb.org/en/about/
[crear una clave de SSH para la autenticación]:http://www.jeff.wilcox.name/2013/06/secure-linux-vms-with-ssh-certificates/
[problema #1268 en CLI de Azure]:https://github.com/Azure/azure-xplat-cli/issues/1268

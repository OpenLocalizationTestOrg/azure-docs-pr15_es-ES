<properties
    pageTitle="Clusterize MySQL con conjuntos de equilibrio de carga | Microsoft Azure"
    description="Configurar una equilibrio de carga de alta disponibilidad Linux MySQL clúster creada con el modelo de implementación clásica en Azure"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="bureado"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="04/14/2015"
    ms.author="jparrel"/>

# <a name="using-load-balanced-sets-to-clusterize-mysql-on-linux"></a>Uso de conjuntos de equilibrio de carga para clusterize MySQL en Linux

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


El propósito de este artículo es explorar e ilustrar los métodos diferentes disponibles para implementar servicios de basados en Linux altamente disponibles en Microsoft Azure, exploración de alta disponibilidad de servidor MySQL como un manual. Un vídeo que ilustra este enfoque está disponible en el [canal 9](http://channel9.msdn.com/Blogs/Open/Load-balancing-highly-available-Linux-services-on-Windows-Azure-OpenLDAP-and-MySQL).

Mostraremos una solución de alta disponibilidad sin elementos compartidos dos nodos maestro único MySQL basada en DRBD, Corosync y marcapasos. Solo nodo está ejecutando MySQL a la vez. Lectura y escritura desde el recurso DRBD también se limita a un solo nodo a la vez.

No es necesario para una solución VIP como informes LV puesto que usamos conjuntos de equilibrio de carga de Microsoft Azure para proporcionar la funcionalidad de turnos y la detección de extremos, eliminación y recuperación correcta de la dirección VIP. La dirección VIP es una dirección IPv4 globalmente enrutable asignada Microsoft Azure cuando se crea por primera vez el servicio de nube.

Hay otras arquitecturas de MySQL incluidos NBD clúster, Percona y Galera, así como varias soluciones de software intermedio, lo que incluye al menos una posible como una máquina virtual en [Depósito de máquina virtual](http://vmdepot.msopentech.com). Siempre y cuando estas soluciones se pueden replicar en unidifusión y multidifusión o difusión y no dependen de almacenamiento compartido o varias interfaces de red, los escenarios deben ser fáciles de implementar en Microsoft Azure.

Por supuesto estas arquitecturas clústeres pueden ampliarse a otros productos, como PostgreSQL y OpenLDAP, de manera similar. Por ejemplo, se ha probado correctamente este equilibrio de carga procedimiento con sin recursos compartidos con varios maestros OpenLDAP y puede ver en nuestro blog de canal 9.

## <a name="getting-ready"></a>Preparación

Necesita una cuenta de Microsoft Azure con una suscripción válida que se pueden crear máquinas virtuales de al menos dos (2) (en este ejemplo se usó x), configurar una red y una subred, un grupo de afinidad y una disponibilidad, así como la capacidad para crear nuevos VHD en la misma región como el servicio de nube y vincularlos a las máquinas virtuales de Linux.

### <a name="tested-environment"></a>Entorno probado

- Ubuntu 13.10
  - DRBD
  - Servidor MySQL
  - Corosync y marcapasos

### <a name="affinity-group"></a>Grupo afinidad

Se crea un grupo de afinidad para la solución, inicie sesión en el Azure clásica portal desplazamiento hacia abajo hasta la configuración y crear un nuevo grupo de afinidad. Los recursos asignados creados más adelante se asignará a este grupo de afinidad.

### <a name="networks"></a>Redes

Se crea una nueva red y se crea una subred dentro de la red. Elegimos una red de 10.10.10.0/24 con /24 solo una subred dentro.

### <a name="virtual-machines"></a>Máquinas virtuales de Windows

La primera VM 13.10 de Ubuntu es creada con una imagen de la Galería de Ubuntu Endorsed y denominada `hadb01`. Se crea un nuevo servicio de nube en el proceso, denominado hadb. Llamamos a este modo para ilustrar la naturaleza compartida, equilibrio de carga que tendrá el servicio cuando se agrega más recursos. La creación de `hadb01` es sin incidentes y completadas con el portal. Se crea automáticamente un punto final para SSH y nuestra red creada está seleccionada. Elegimos también crear una nueva disponibilidad para las máquinas virtuales.

Una vez creada la primera VM (técnicamente, cuando se crea el servicio de nube) continuar para crear la segunda VM, `hadb02`. Para la segunda VM también se utilizará Ubuntu 13.10 VM desde la galería con el portal pero Elegimos utilizar un servicio de nube existente, `hadb.cloudapp.net`, en lugar de crear uno nuevo. El conjunto de red y disponibilidad debe estar seleccionado automáticamente para nosotros. También se creará el extremo SSH.

Después de han creado ambos VM, daremos Nota el puerto SSH para `hadb01` (TCP 22) y `hadb02` (asignadas automáticamente por Azure)

### <a name="attached-storage"></a>Almacenamiento de conexión

Se conecta un disco nuevo a ambas máquinas virtuales y crear nuevos discos de 5 GB en el proceso. Los discos se alojarán en el contenedor de disco duro virtual de uso de nuestros discos de sistema operativo principal. Una vez que se crean y se adjunta discos no es necesario para que podamos reinicie Linux como el núcleo verán el nuevo dispositivo (normalmente `/dev/sdc`, puede comprobar `dmesg` para la salida)

En cada VM continuar para crear una nueva partición usando `cfdisk` (principal, Linux partición) y escriba la nueva tabla de partición. **No crear un sistema de archivos en esta partición** .

## <a name="setting-up-the-cluster"></a>Configurar el clúster

En ambos VM Ubuntu, debemos usar APT para instalar Corosync, marcapasos y DRBD. Usando `apt-get`:

    sudo apt-get install corosync pacemaker drbd8-utils.

**No instale MySQL en este momento** . Debian y secuencias de comandos de instalación de Ubuntu inicializará un directorio de datos MySQL en `/var/lib/mysql`, pero desde el directorio será reemplazado por un sistema de archivos DRBD, debemos hacerlo más tarde.

En este momento, debemos comprobar (con `/sbin/ifconfig`) que utilizan máquinas virtuales de ambas direcciones de la subred 10.10.10.0/24 y que puede hacer ping entre sí por su nombre. Si lo desea también puede usar `ssh-keygen` y `ssh-copy-id` para asegurarse de que ambos VM pueden comunicarse a través de SSH sin necesidad de tener una contraseña.

### <a name="setting-up-drbd"></a>Configurar DRBD

Vamos a crear un recurso DRBD que usa subyacente `/dev/sdc1` partición para generar un `/dev/drbd1` recursos pueden ser formateada con ext3 y utilizar en los nodos primario y secundarios. Para ello, abra `/etc/drbd.d/r0.res` y copie la siguiente definición de recursos. Haga lo siguiente en ambas máquinas virtuales:

    resource r0 {
      on `hadb01` {
        device  /dev/drbd1;
        disk   /dev/sdc1;
        address  10.10.10.4:7789;
        meta-disk internal;
      }
      on `hadb02` {
        device  /dev/drbd1;
        disk   /dev/sdc1;
        address  10.10.10.5:7789;
        meta-disk internal;
      }
    }

Después de esto, inicializar el uso de recursos `drbdadm` en ambas máquinas virtuales:

    sudo drbdadm -c /etc/drbd.conf role r0
    sudo drbdadm up r0

Y por último, en el equipo principal (`hadb01`) forzar (principal) de la propiedad del recurso DRBD:

    sudo drbdadm primary --force r0

Si examina el contenido de/proceso/drbd (`sudo cat /proc/drbd`) en ambos VM, debería ver `Primary/Secondary` en `hadb01` y `Secondary/Primary` en `hadb02`coherente con la solución en este momento. El disco de 5 GB se sincronizarán en la red 10.10.10.0/24 sin cargo para los clientes.

Una vez que se ha sincronizado el disco puede crear el sistema de archivos en `hadb01`. Con fines de pruebas utilizamos ext2 pero las siguientes instrucciones crearán un sistema de archivos ext3:

    mkfs.ext3 /dev/drbd1

### <a name="mounting-the-drbd-resource"></a>Montaje del recurso DRBD

En `hadb01` ahora podemos montaje los recursos DRBD. Uso de Debian y derivados `/var/lib/mysql` como directorio de datos de MySQL. Dado que nos no hemos instalado MySQL, se creará el directorio y montaje del recurso DRBD. On `hadb01`:

    sudo mkdir /var/lib/mysql
    sudo mount /dev/drbd1 /var/lib/mysql

## <a name="setting-up-mysql"></a>Configuración de MySQL

Ahora está preparado para instalar MySQL en `hadb01`:

    sudo apt-get install mysql-server

Para `hadb02`, tiene dos opciones. Puede instalar a servidor mysql ahora, que creará /var/lib/mysql y rellénela con un nuevo directorio de datos y, a continuación, vaya al quitar el contenido. On `hadb02`:

    sudo apt-get install mysql-server
    sudo service mysql stop
    sudo rm –rf /var/lib/mysql/*

La segunda opción es para conmutar por error a `hadb02` y, a continuación, instalar servidor mysql allí (secuencias de comandos de instalación observará la instalación existente y no toque)

On `hadb01`:

    sudo drbdadm secondary –force r0

On `hadb02`:

    sudo drbdadm primary –force r0
    sudo apt-get install mysql-server

Si no planea para la recuperación ante DRBD ahora, la primera opción es más fácil aunque posiblemente es menos elegante. Después de configurar esto, puede comenzar a trabajar en la base de datos MySQL. En `hadb02` (o independientemente de los servidores está activo, según DRBD):

    mysql –u root –p
    CREATE DATABASE azureha;
    CREATE TABLE things ( id SERIAL, name VARCHAR(255) );
    INSERT INTO things VALUES (1, "Yet another entity");
    GRANT ALL ON things.\* TO root;

**Advertencia**: esta última instrucción desactiva la autenticación para el usuario de raíz de esta tabla. Debería sustituirse por su nivel de producción conceder instrucciones y se incluye solo con fines ilustrativos.

También debe habilitar la red para MySQL si desea realizar consultas desde fuera de la VM, que es la finalidad de esta guía. En ambos VM, abra `/etc/mysql/my.cnf` y vaya a `bind-address`, cambiar de 127.0.0.1 a 0.0.0.0. Después de guardar el archivo, emitir una `sudo service mysql restart` en su principal actual.

### <a name="creating-the-mysql-load-balanced-set"></a>Crear la carga de MySQL equilibrada conjunto

Que se vuelva al portal y vaya a la `hadb01` VM y, a continuación, extremos. Nos se cree un nuevo punto final, elija MySQL (TCP 3306) desde la lista desplegable y graduación en el cuadro de *establecer de equilibrio de carga nuevo de crear* . Llamamos el extremo de equilibrio de carga `lb-mysql`. Deje la mayoría de las opciones solo excepto el tiempo que se reducirá a 5 (segundos, mínimos)

Una vez creado el extremo vamos a `hadb02`, extremos y crear un nuevo punto final pero debemos seleccionar `lb-mysql`, a continuación, seleccione MySQL en el menú desplegable. También puede utilizar la CLI Azure para este paso.

En este momento tenemos todo lo que necesitamos para una operación manual del clúster.

### <a name="testing-the-load-balanced-set"></a>Probar la carga equilibrada conjunto

Se pueden realizar pruebas desde un equipo exterior, usando un cliente de MySQL, así como aplicaciones (por ejemplo, phpMyAdmin ejecuta como un sitio Web de Azure) en este caso, hemos usado herramienta de línea de comandos de MySQL en otro cuadro de Linux:

    mysql azureha –u root –h hadb.cloudapp.net –e "select * from things;"

### <a name="manually-failing-over"></a>Experimentando problemas manualmente

Ahora puede simular migraciones tras error cerrando MySQL, cambiando principal del DRBD y vuelva a iniciar MySQL.

En hadb01:

    service mysql stop && umount /var/lib/mysql ; drbdadm secondary r0

A continuación, en hadb02:

    drbdadm primary r0 ; mount /dev/drbd1 /var/lib/mysql && service mysql start

Una vez que failover manualmente puede repetir la consulta remota y deben estar trabajando perfectamente.

## <a name="setting-up-corosync"></a>Configurar Corosync

Corosync es necesaria para que marcapasos para que funcione la infraestructura de clúster subyacente. Para usuarios de v1 y v2 latido (y otros métodos como Ultramonkey) Corosync es una división de las funcionalidades CRM mientras marcapasos permanecen más similar al latido de funcionalidad.

La restricción principal para Corosync en Azure es que Corosync prefiere multidifusión difusión sobre las comunicaciones de unidifusión, pero las redes de Microsoft Azure solo individual.

Afortunadamente, Corosync tiene un modo de unidifusión de trabajo y la única restricción real es que, dado que todos los nodos no comunican entre sí *automáticamente*, debe definir los nodos en los archivos de configuración, incluidos sus direcciones IP. Podemos utilizar los archivos de ejemplo Corosync para unidifusión y cambie enlazan dirección, listas de nodos y directorio de registro (utiliza Ubuntu `/var/log/corosync` mientras el ejemplo archivos usan `/var/log/cluster`) y permitir quórum herramientas.

**Nota la `transport: udpu` directiva siguiente y las direcciones IP manualmente definidas para los nodos**.

En `/etc/corosync/corosync.conf` para ambos nodos:

    totem {
      version: 2
      crypto_cipher: none
      crypto_hash: none
      interface {
        ringnumber: 0
        bindnetaddr: 10.10.10.0
        mcastport: 5405
        ttl: 1
      }
      transport: udpu
    }

    logging {
      fileline: off
      to_logfile: yes
      to_syslog: yes
      logfile: /var/log/corosync/corosync.log
      debug: off
      timestamp: on
      logger_subsys {
        subsys: QUORUM
        debug: off
        }
      }

    nodelist {
      node {
        ring0_addr: 10.10.10.4
        nodeid: 1
      }

      node {
        ring0_addr: 10.10.10.5
        nodeid: 2
      }
    }

    quorum {
      provider: corosync_votequorum
    }

Nos copiar este archivo de configuración en ambas máquinas virtuales e inicie Corosync en ambos nodos:

    sudo service start corosync

Poco después de iniciar el servicio de clúster debe establecerse en el menú llamar actual y debe constituir quórum. Podemos comprobar esta funcionalidad revisando registros o:

    sudo corosync-quorumtool –l

Debería seguir un resultado similar a la imagen siguiente:

![resultados de ejemplo de corosync quorumtool - l](media/virtual-machines-linux-classic-mysql-cluster/image001.png)

## <a name="setting-up-pacemaker"></a>Configurar marcapasos

Marcapasos usa el clúster para supervisar para los recursos, defina cuando primarios ir hacia abajo y cambiar los recursos a secundarios. Pueden definir recursos de un conjunto de secuencias de comandos disponibles o de LSB (como inicialización) secuencias de comandos, entre otras opciones.

Queremos marcapasos "propietario" del recurso DRBD, el punto de montaje y el servicio MySQL. Si marcapasos pueden activar y desactivar DRBD, lo montaje / umount se e inicio o parada MySQL en el orden correcto cuando algo incorrecto sucede con el principal, el programa de instalación está completa.

Cuando instala marcapasos por primera vez, la configuración debe ser bastante sencilla, algo parecido:

    node $id="1" hadb01
      attributes standby="off"
    node $id="2" hadb02
      attributes standby="off"

Comprobar si ejecuta `sudo crm configure show`. Ahora, cree un archivo (por ejemplo, `/tmp/cluster.conf`) con los siguientes recursos:

    primitive drbd_mysql ocf:linbit:drbd \
          params drbd_resource="r0" \
          op monitor interval="29s" role="Master" \
          op monitor interval="31s" role="Slave"

    ms ms_drbd_mysql drbd_mysql \
          meta master-max="1" master-node-max="1" \
            clone-max="2" clone-node-max="1" \
            notify="true"

    primitive fs_mysql ocf:heartbeat:Filesystem \
          params device="/dev/drbd/by-res/r0" \
          directory="/var/lib/mysql" fstype="ext3"

    primitive mysqld lsb:mysql

    group mysql fs_mysql mysqld

    colocation mysql_on_drbd \
           inf: mysql ms_drbd_mysql:Master

    order mysql_after_drbd \
           inf: ms_drbd_mysql:promote mysql:start

    property stonith-enabled=false

    property no-quorum-policy=ignore

Y ahora cargarla en la configuración (solo debe hacer esto en un nodo):

    sudo crm configure
      load update /tmp/cluster.conf
      commit
      exit

Asegúrese de que se inicia en el inicio en ambos nodos marcapasos:

    sudo update-rc.d pacemaker defaults

Después de unos segundos y el uso de `sudo crm_mon –L`, compruebe que se ha convertido en el patrón para el clúster de uno de los nodos y está ejecutando todos los recursos. Puede usar montaje y ps para comprobar que se están ejecutando los recursos.

Se muestra en la siguiente captura de pantalla `crm_mon` con un nodo detenido (salir con CTRL-C)

![nodo de crm_mon detenido](media/virtual-machines-linux-classic-mysql-cluster/image002.png)

Y esta captura de pantalla muestra ambos nodos, con un patrón y uno subordinado:

![principal-secundario operativas crm_mon](media/virtual-machines-linux-classic-mysql-cluster/image003.png)

## <a name="testing"></a>Realizar pruebas

Estamos listos para una simulación de migración tras error automática. Hay dos formas de hacerlo: suaves y disco duro. La forma suave es mediante la función de cierre del clúster: ``crm_standby -U `uname -n` -v on``. Con esto en el patrón, el secundario tendrá sobre. Recuerde establecer volver a desactivar (crm_mon le indicará un nodo está en suspensión en caso contrario)

El disco duro forma es apagar la máquina virtual principal (hadb01) a través del portal o cambiando el runlevel en la máquina virtual (es decir, detener, apagado), a continuación, ayudamos a Corosync y marcapasos mediante señales ir del patrón hacia abajo. Probemos esto (resulta útil ventanas de mantenimiento), pero también se puede exigir el escenario inmovilizando solo la máquina virtual.

## <a name="stonith"></a>STONITH

Es posible realizar un apagado VM mediante la CLI de Azure en lugar de una secuencia de comandos STONITH que controla un dispositivo físico. Puede usar `/usr/lib/stonith/plugins/external/ssh` como base y habilitar STONITH en configuración del clúster. Debe instalarse globalmente CLI Azure y debe cargarse el perfil y configuración de publicación para usuario del clúster.

Código de ejemplo para el recurso disponible en [GitHub](https://github.com/bureado/aztonith). Debe cambiar la configuración del clúster mediante la adición de las siguientes acciones para `sudo crm configure`:

    primitive st-azure stonith:external/azure \
      params hostlist="hadb01 hadb02" \
      clone fencing st-azure \
      property stonith-enabled=true \
      commit

**Nota:** no realiza la secuencia de comandos ascendentes y descendentes comprobaciones. El recurso SSH original tenía 15 comprobaciones ping pero el tiempo de recuperación para una máquina virtual de Azure podría ser más variables.

## <a name="limitations"></a>Limitaciones

Las siguientes limitaciones:

- La secuencia de comandos de recursos DRBD linbit que administra DRBD como un recurso en usos marcapasos `drbdadm down` al apagar un nodo, aunque el nodo solo va en espera. No es ideal desde el subordinado no se sincroniza el recurso DRBD mientras el patrón obtiene escribe. Si el patrón no funcionan gentilmente, el secundario puede tomar el control un estado de sistema de archivos antiguo. Hay dos maneras de solucionar esto:
  - Aplicar un `drbdadm up r0` en todos los nodos a través de local (no clusterized) a continuación se proporciona, o bien,
  - Editar la linbit DRBD secuencia de comandos para asegurarse de que `down` no se llama, en `/usr/lib/ocf/resource.d/linbit/drbd`.
- Equilibrador de carga necesita al menos 5 segundos responder para que aplicaciones deben ser compatible con clústeres y más tolerancia a errores de tiempo de espera; otras arquitecturas también puede ayudar a, por ejemplo colas de la aplicación, consulta middlewares, etcetera.
- Optimización de MySQL es necesario para asegurarse de escritura se realiza a un cara ritmo y se descargan disco frecuentes para minimizar las pérdidas de memoria caché
- Escribir el rendimiento será dependiente de máquina virtual interconexión en el modificador virtual como éste es el mecanismo utilizado DRBD para replicar el dispositivo

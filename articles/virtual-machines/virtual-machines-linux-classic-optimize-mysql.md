<properties
    pageTitle="Optimizar el rendimiento de MySQL en máquinas virtuales de Linux | Microsoft Azure"
    description="Aprenda a optimizar MySQL que se ejecuta en una máquina virtual (VM) Azure con Linux."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="NingKuang"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/15/2015"
    ms.author="ningk"/>

#<a name="optimizing-mysql-performance-on-azure-linux-vms"></a>Optimizar el rendimiento de MySQL en máquinas virtuales de Linux Azure

Hay muchos factores que afectar al rendimiento de MySQL en Azure, tanto en la configuración de software y selección de hardware virtual. En este artículo se centra en optimizar el rendimiento a través de almacenamiento, sistema y configuraciones de base de datos.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


##<a name="utilizing-raid-on-an-azure-virtual-machine"></a>Uso de RAID en una máquina virtual de Azure
Almacenamiento es el factor clave que afecta al rendimiento de la base de datos en entornos de nube.  En comparación con un disco, RAID puede proporcionar acceso más rápido a través de simultaneidad.  Para obtener más información, consulte [Niveles RAID estándar](http://en.wikipedia.org/wiki/Standard_RAID_levels) .   

Tiempo de respuesta de i/OS en Azure y rendimiento de disco i/OS pueden mejorarse significativamente por RAID. Nuestras pruebas de laboratorio mostrar disco i/OS se puede duplicar el rendimiento y tiempo de respuesta de i/OS se puede reducir en mitad Media cuando se duplica el número de discos RAID (de 2 a 4, 4 a 8 etcetera.). Para obtener información detallada, consulte el [Apéndice A](#AppendixA) .  

Además de i/OS de disco, rendimiento de MySQL mejora al aumentar el nivel RAID.  Para obtener información detallada, consulte el [Apéndice B](#AppendixB) .  

También puede tener en cuenta el tamaño del fragmento. En general cuando tenga un tamaño de campo más grande, obtendrá inferior aérea, especialmente para grandes escribe. Sin embargo, cuando el tamaño del fragmento es demasiado grande, puede agregar sobrecarga adicional y no puede aprovechar el RAID. El actual tamaño predeterminado es 512KB, que está comprobado que sea óptima para los entornos de producción más generales. Para obtener información detallada, consulte [Apéndice C](#AppendixC) .   

Tenga en cuenta que existen límites para cuántos discos puede agregar tipos de máquina virtual diferente. Estos límites se detallan en [Máquina Virtual y los tamaños de servicio de nube para Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx). Necesitará 4 discos de datos adjuntos para seguir el ejemplo RAID en este artículo, aunque puede optar por configurar RAID con menos discos.  

En este artículo se supone ya ha creado una máquina virtual Linux y MYSQL instalado y configurado. Para obtener más información acerca de cómo empezar, consulte cómo instalar MySQL en Azure.  

###<a name="setting-up-raid-on-azure"></a>Configurar RAID en Azure
Los pasos siguientes muestran cómo crear RAID en Azure con el portal de clásico de Azure. Puede también configurar RAID mediante secuencias de comandos de Windows PowerShell.
En este ejemplo se configurará RAID 0 con 4 discos.  

####<a name="step-1-add-a-data-disk-to-your-virtual-machine"></a>Paso 1: Agregar un disco de datos a su equipo Virtual  

En la página de máquinas virtuales de Azure portal clásica, haga clic en la máquina virtual al que desea agregar un disco de datos. En este ejemplo, la máquina virtual es mysqlnode1.  

![][1]

En la página de la máquina virtual, haga clic en **panel**.  

![][2]


En la barra de tareas, haga clic en **adjuntar**.

![][3]

Y, a continuación, haga clic en **adjuntar vacío disco**.  

![][4]

Discos de datos, se debe establecer la **Preferencia de caché de Host** en **ninguna**.  

Se agregará un disco vacío en la máquina virtual. Repita este paso tres veces más para que tengan 4 discos de datos para RAID.  

Puede ver las unidades de agregado de la máquina virtual consultando el registro de mensajes del núcleo. Por ejemplo, para verlo en Ubuntu, use el siguiente comando:  

    sudo grep SCSI /var/log/dmesg

####<a name="step-2-create-raid-with-the-additional-disks"></a>Paso 2: Crear RAID con los discos adicionales
Siga este artículo para obtener los pasos detallados de configuración RAID:  

[Configurar el software RAID en Linux](virtual-machines-linux-configure-raid.md)

>[AZURE.NOTE] Si está utilizando el sistema de archivos XFS, siga los pasos siguientes después de haber creado RAID.

Para instalar XFS en Debian, Ubuntu o Linux Mint, utilice el siguiente comando:  

    apt-get -y install xfsprogs  

Para instalar XFS en Fedora, CentOS o RHEL, utilice el siguiente comando:  

    yum -y install xfsprogs  xfsdump


####<a name="step-3-set-up-a-new-storage-path"></a>Paso 3: Configurar un nuevo trazado de almacenamiento
Utilice el siguiente comando:  

    root@mysqlnode1:~# mkdir -p /RAID0/mysql

####<a name="step-4-copy-the-original-data-to-the-new-storage-path"></a>Paso 4: Copiar los datos originales en la ruta de almacenamiento nuevo
Utilice el siguiente comando:  

    root@mysqlnode1:~# cp -rp /var/lib/mysql/* /RAID0/mysql/

####<a name="step-5-modify-permissions-so-mysql-can-access-read-and-write-the-data-disk"></a>Paso 5: Modificar permisos de modo que puedan acceder MySQL (lectura y escritura) el disco de datos
Utilice el siguiente comando:  

    root@mysqlnode1:~# chown -R mysql.mysql /RAID0/mysql && chmod -R 755 /RAID0/mysql


##<a name="adjust-the-disk-io-scheduling-algorithm"></a>Ajustar el algoritmo de programación de i/OS de disco
Linux implementa cuatro tipos de i/OS programación algoritmos:  

-   Algoritmo NOOP (sin operación)
-   Algoritmo de fecha límite (límite)
-   Completamente algoritmo de cola (CFQ)
-   Algoritmo de período de presupuesto (Anticipatory)  

Puede seleccionar distintos programadores de i/OS en escenarios diferentes para optimizar el rendimiento. En un entorno de acceso completamente aleatorio, no hay una gran diferencia entre los algoritmos CFQ y límite de rendimiento. Se recomienda configurar el entorno de base de datos MySQL a fecha límite de estabilidad. Si hay una gran cantidad de secuenciales i/OS, CFQ puede reducir el rendimiento disco.   

De SSD y otros equipos, usando NOOP o fecha límite puede lograr un mejor rendimiento que el programador predeterminado.   

Desde el núcleo 2,5, el algoritmo de programación de i/OS predeterminado es fecha límite. A partir del núcleo 2.6.18, CFQ se convirtió en el algoritmo de programación de i/OS de forma predeterminada.  Puede especificar esta configuración durante el inicio del núcleo o modificar dinámicamente esta opción cuando el sistema se está ejecutando.  

En el ejemplo siguiente se muestra cómo comprobar y establecer al programador predeterminado para el algoritmo NOOP.  

Para la familia Debian distribución:

###<a name="step-1view-the-current-io-scheduler"></a>Programador de ver las i/OS actual de paso 1.
Utilice el siguiente comando:  

    root@mysqlnode1:~# cat /sys/block/sda/queue/scheduler

Verá la siguiente resultado, lo que indica al programador actual.  

    noop [deadline] cfq


###<a name="step-2-change-the-current-device-devsda-of-io-scheduling-algorithm"></a>Paso 2. Cambiar el dispositivo actual (/ desarrollo/empaquetados) de i/OS algoritmo de programación
Use los comandos siguientes:  

    azureuser@mysqlnode1:~$ sudo su -
    root@mysqlnode1:~# echo "noop" >/sys/block/sda/queue/scheduler
    root@mysqlnode1:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
    root@mysqlnode1:~# update-grub

>[AZURE.NOTE] Establecer esta opción para/dev/sda por sí sola no es útil. Debe establecerse en todos los discos de datos donde reside la base de datos.  

Verá el siguiente resultado, que indica que se ha vuelto a ese grub.cfg correctamente y que el programador predeterminado se ha actualizado a NOOP.  

    Generating grub configuration file ...
    Found linux image: /boot/vmlinuz-3.13.0-34-generic
    Found initrd image: /boot/initrd.img-3.13.0-34-generic
    Found linux image: /boot/vmlinuz-3.13.0-32-generic
    Found initrd image: /boot/initrd.img-3.13.0-32-generic
    Found memtest86+ image: /memtest86+.elf
    Found memtest86+ image: /memtest86+.bin
    done

Para la familia de distribución Redhat, solo se necesita el siguiente comando:   

    echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local

##<a name="configure-system-file-operations-settings"></a>Configurar las opciones de las operaciones de archivos de sistema
Un procedimiento recomendado es deshabilitar la característica de registro atime en el sistema de archivos. Atime es la hora del último acceso de archivo. Siempre que se tiene acceso a un archivo, el sistema de archivos registra la marca de tiempo en el registro. Sin embargo, esta información se utiliza habitualmente. Se puede deshabilitar si ya no lo necesita, que se reduce el tiempo de acceso de disco general.  

Para deshabilitar atime registro, debe modificar el fstab de /etc/ de archivo de configuración de archivo sistema y agregue la opción **noatime** .  

Por ejemplo, modifique el archivo / etc/fstab de vim, agregue el noatime tal como se muestra a continuación.  

    # CLOUD_IMG: This file was created/modified by the Cloud Image build process
    UUID=3cc98c06-d649-432d-81df-6dcd2a584d41       /        ext4   defaults,discard        0 0
    #Add the “noatime” option below to disable atime logging
    UUID="431b1e78-8226-43ec-9460-514a9adf060e"     /RAID0   xfs   defaults,nobootwait, noatime 0 0
    /dev/sdb1       /mnt    auto    defaults,nobootwait,comment=cloudconfig 0       2

A continuación, vuelva a montar el sistema de archivos con el siguiente comando:  

    mount -o remount /RAID0

Probar el resultado modificado. Tenga en cuenta que, cuando se modifica el archivo de prueba, no se actualiza el tiempo de acceso.  

Antes de ejemplo:     

![][5]

Después de ejemplo:

![][6]

##<a name="increase-the-maximum-number-of-system-handles-for-high-concurrency"></a>Aumentar el número máximo de identificadores del sistema para simultaneidad alta
MySQL es la base de datos de gran simultaneidad. El número predeterminado de controladores simultáneas es 1024 para Linux, que no siempre es suficiente. **Realice los siguientes pasos para aumentar los controladores simultáneos máximos del sistema para admitir gran simultaneidad de MySQL**.

###<a name="step-1-modify-the-limitsconf-file"></a>Paso 1: Modificar el archivo de limits.conf
Agregue las cuatro líneas siguientes en el archivo /etc/security/limits.conf para aumentar los controladores de simultáneos máximos permitidos. Tenga en cuenta que 65536 es el número máximo que puede admitir el sistema.   

    * nofile suave 65536
    * disco duro nofile 65536
    * nproc suave 65536
    * disco duro nproc 65536

###<a name="step-2-update-the-system-for-the-new-limits"></a>Paso 2: Actualizar el sistema para los nuevos límites
Ejecute los comandos siguientes:  

    ulimit -SHn 65536
    ulimit -SHu 65536

###<a name="step-3-ensure-that-the-limits-are-updated-at-boot-time"></a>Paso 3: Asegúrese de que los límites están actualizados en el momento de inicio
Escriba los siguientes comandos de inicio en el archivo /etc/rc.local para surtirá efecto durante cada hora de inicio.  

    echo “ulimit -SHn 65536” >>/etc/rc.local
    echo “ulimit -SHu 65536” >>/etc/rc.local

##<a name="mysql-database-optimization"></a>Optimización de base de datos MySQL
Puede usar la misma estrategia de optimización del rendimiento para configurar MySQL en Azure como en un equipo local.  

Las reglas de optimización de i/OS principales son:   

-   Aumentar el tamaño de caché.
-   Reducir el tiempo de respuesta de i/OS.  

Para optimizar la configuración del servidor MySQL, puede actualizar el archivo my.cnf, que es el archivo de configuración predeterminada de los equipos cliente y servidor.  

Los siguientes elementos de configuración son los principales factores que afectan al rendimiento MySQL:  

-   **innodb_buffer_pool_size**: el búfer contiene datos almacenados en búfer y el índice. Normalmente, esto se establece en 70% de memoria física.
-   **innodb_log_file_size**: este es el tamaño del registro de rehacer. Usar registros rehacer para asegurarse de que las operaciones de escritura son rápidos, confiables y recuperables después de un error. Esto se establece en 512MB, que le proporcionará una gran cantidad de espacio para el registro de las operaciones de escritura.
-   **max_connections**: a veces aplicaciones no cierre conexiones correctamente. Un valor mayor el servidor tendrá más tiempo para reciclaje inactivos conexiones. Número máximo de conexiones es 10000, pero el máximo recomendado es 5000.
-   **Innodb_file_per_table**: esta opción Habilitar o deshabilitar la posibilidad de InnoDB almacenar tablas en archivos independientes. Activar la opción se asegurará de que se pueden aplicar varias operaciones de administración avanzada eficazmente. Desde el punto de vista del rendimiento, puede acelerar la transmisión de espacio de tabla y optimizar el rendimiento de la administración de residuos. Por lo tanto, la configuración recomendada para este está activada.</br>
    Desde 5.6 MySQL, la configuración predeterminada está activada. Por lo tanto, se requiere ninguna acción. Para otras versiones, que es anterior a 5.6, configuración predeterminada está desactivada. Es necesario activar esta activado. Y debe aplicarlo antes de cargar datos, porque solo las tablas recién creado se ven afectadas.
-   **innodb_flush_log_at_trx_commit**: valor predeterminado es 1, con el ámbito establecido en 0 ~ 2. El valor predeterminado es la opción más adecuada para la base de datos MySQL de independiente. La configuración de 2 habilita la mayoría integridad de datos y es adecuada para el patrón de clúster MySQL. El valor 0 permite pérdida de datos, lo que puede afectar a la confiabilidad, en algunos casos con mejor rendimiento y es adecuado para subordinado clúster MySQL.
-   **Innodb_log_buffer_size**: el búfer de registro permite que las transacciones para ejecutarse sin tener que vacíe el registro en el disco antes de confirmación las transacciones. Sin embargo, si hay objeto binario grande o campo de texto, se consume muy rápidamente la memoria caché y se activará i/OS de disco frecuentes. Es mejor aumentar el tamaño del búfer si variable de estado de Innodb_log_waits no es 0.
-   **query_cache_size**: es la mejor opción Deshabilitar desde el principio. Establecer query_cache_size a 0 (ahora es el valor predeterminado en MySQL 5.6) y usar otros métodos para acelerar las consultas.  

Consulte el [Apéndice D](#AppendixD) para comparar el rendimiento después de la optimización.


##<a name="turn-on-the-mysql-slow-query-log-for-analyzing-the-performance-bottleneck"></a>Activar el registro de consulta lenta MySQL para analizar la botella en el rendimiento
El registro de consulta lenta MySQL puede ayudarle a identificar las consultas lentas para MySQL. Después de habilitar el registro de consulta lenta MySQL, puede usar herramientas de MySQL como **mysqldumpslow** para identificar la botella en el rendimiento.  

Tenga en cuenta que de forma predeterminada este no está habilitado. Activar el registro de consultas lento puede consumir recursos de la CPU. Por lo tanto, se recomienda que habilite esta temporalmente para solucionar problemas de botella de rendimiento.

###<a name="step-1-modify-mycnf-file-by-adding-the-following-lines-to-the-end"></a>Paso 1: Modificar archivo my.cnf agregando las siguientes líneas al final   

    long_query_time = 2
    slow_query_log = 1
    slow_query_log_file = /RAID0/mysql/mysql-slow.log

###<a name="step-2-restart-mysql-server"></a>Paso 2: Reiniciar el servidor mysql
    service  mysql  restart

###<a name="step-3-check-whether-the-setting-is-taking-effect-using-the-show-command"></a>Paso 3: Comprobar si la configuración está teniendo efecto mediante el comando "Mostrar"

![][7]   

![][8]

En este ejemplo, puede ver que se ha activado la característica de consulta lenta. A continuación, puede usar la herramienta de **mysqldumpslow** para determinar la botella de rendimiento y optimizar el rendimiento, como agregar índices.





##<a name="appendix"></a>Apéndice

A continuación se muestran datos de prueba de rendimiento de ejemplo creados en el entorno de prueba destino, proporcionan información general en la tendencia de datos de rendimiento con enfoques, de ajuste del rendimiento de diferentes a pesar de que los resultados pueden variar en diferentes versiones de entorno o de producto.

<a name="AppendixA"></a>Apéndice A:  
**Rendimiento del disco (IOPS) con RAID diferentes niveles**


![][9]

**Comandos de prueba:**  

    fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=5G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite

>AZURE. Nota: La carga de trabajo de esta prueba utiliza 64 subprocesos, intentando alcanzar el límite de RAID.

<a name="AppendixB"></a>Apéndice B:  
**Comparación de rendimiento (rendimiento) MySQL con RAID diferentes niveles**   
(Sistema de archivos XFS)


![][10]  
![][11]

**Comandos de prueba:**

    mysqlslap -p0ps.123 --concurrency=2 --iterations=1 --number-int-cols=10 --number-char-cols=10 -a --auto-generate-sql-guid-primary --number-of-queries=10000 --auto-generate-sql-load-type=write –engine=innodb

**Comparación de rendimiento (OLTP) MySQL con RAID diferentes niveles**  
![][12]

**Comandos de prueba:**

    time sysbench --test=oltp --db-driver=mysql --mysql-user=root --mysql-password=0ps.123  --mysql-table-engine=innodb --mysql-host=127.0.0.1 --mysql-port=3306 --mysql-socket=/var/run/mysqld/mysqld.sock --mysql-db=test --oltp-table-size=1000000 prepare

<a name="AppendixC"></a>Apéndice C:   
**Comparación de rendimiento (IOPS) de disco fragmento distintos tamaños de**  
(Sistema de archivos XFS)


![][13]

**Comandos de prueba:**  

    fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=30G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite
    fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=1G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite  

Tenga en cuenta el tamaño de archivo utilizado para esta prueba es 30 y 1GB respectivamente, con RAID 0(4 disks) XFS campo sistema.


<a name="AppendixD"></a>Apéndice D:  
**Comparación de rendimiento (rendimiento) MySQL antes y después de la optimización**  
(Sistema de archivos XFS)


![][14]

**Comandos de prueba:**

    mysqlslap -p0ps.123 --concurrency=2 --iterations=1 --number-int-cols=10 --number-char-cols=10 -a --auto-generate-sql-guid-primary --number-of-queries=10000 --auto-generate-sql-load-type=write –engine=innodb,misam

**La configuración predeterminada y optimización del es la siguiente:**

|Parámetros |Predeterminado    |optimización del
|-----------|-----------|-----------
|**innodb_buffer_pool_size**    |Ninguno   |7G
|**innodb_log_file_size**   |5M |512M
|**max_connections**    |100    |5000
|**innodb_file_per_table**  |0  |1
|**innodb_flush_log_at_trx_commit** |1  |2
|**innodb_log_buffer_size** |8M |128M
|**query_cache_size**   |16M    |0


Parámetros de configuración de optimización más detalladas, consulte las instrucciones oficial de mysql.  

[http://dev.MySQL.com/doc/refman/5.6/en/InnoDB-Configuration.HTML](http://dev.mysql.com/doc/refman/5.6/en/innodb-configuration.html)  

[http://dev.MySQL.com/doc/refman/5.6/en/InnoDB-Parameters.HTML#sysvar_innodb_flush_method](http://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html#sysvar_innodb_flush_method)

**Entorno de prueba**  

|Hardware   |Detalles
|-----------|-------
|CPU    |AMD Opteron (TM) procesador 4171 / 4 núcleos
|Memoria |14G
|disco   |10G o disco
|sistema operativo |Ubuntu 14.04.1 LTADOS



[1]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-01.png
[2]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-02.png
[3]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-03.png
[4]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-04.png
[5]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-05.png
[6]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-06.png
[7]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-07.png
[8]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-08.png
[9]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-09.png
[10]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-10.png
[11]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-11.png
[12]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-12.png
[13]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-13.png
[14]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-14.png

<properties
    pageTitle="Configuración de protección de datos de Oracle en máquinas virtuales | Microsoft Azure"
    description="Paso a través de un tutorial para configurar e implementar la protección de datos de Oracle en Azure máquinas virtuales de alta disponibilidad y recuperación de desastres."
    services="virtual-machines-windows"
    authors="rickstercdn"
    manager="timlt"
    documentationCenter=""
    tags="azure-service-management"/>
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows"
    ms.workload="infrastructure-services"
    ms.date="09/06/2016"
    ms.author="rclaus" />

#<a name="configuring-oracle-data-guard-for-azure"></a>Configuración de protección de datos de Oracle para Azure


En este tutorial se muestra cómo configurar e implementar la protección de datos de Oracle en máquinas virtuales de Azure entorno para alta disponibilidad y la recuperación. El tutorial se centra en replicación unidireccional para bases de datos no RAC Oracle.

Protección de datos de Oracle es compatible con protección de datos y de recuperación de base de datos de Oracle. Es un simple, alto rendimiento, solución de orden de recuperación, protección de datos y alta disponibilidad de la base de datos Oracle.

En este tutorial se supone que ya conoce teórica y práctica sobre los conceptos de alta disponibilidad de base de datos de Oracle y recuperación. Para obtener más información, consulte el [sitio web de Oracle](http://www.oracle.com/technetwork/database/features/availability/index.html) y también la [Guía de administración y conceptos de protección de datos de Oracle](https://docs.oracle.com/cd/E11882_01/server.112/e41134/toc.htm).

Además, en el tutorial se supone que ya ha implementado los siguientes requisitos previos:

- Ya ha revisado la sección Consideraciones de recuperación de desastres y alta disponibilidad en el tema de [imágenes de máquina Virtual de Oracle - varias consideraciones](virtual-machines-windows-classic-oracle-considerations.md) . Azure admite instancias de base de datos de Oracle independiente, pero no Oracle Real aplicación clústeres (Oracle RAC) actualmente.


- Dos máquinas virtuales (VM) que haya creado en Azure con la misma plataforma que proporciona la imagen de Oracle Enterprise Edition. Asegúrese de que los equipos virtuales están en el [mismo servicio de nube](virtual-machines-windows-load-balance.md) y en la misma red Virtual para asegurarse de que pueden acceder a ellos a través de la dirección IP privada persistente. Además, se recomienda para colocar las máquinas virtuales en el mismo [conjunto de disponibilidad](virtual-machines-windows-manage-availability.md) para permitir Azure colocarlos en dominios de error independiente y actualizar los dominios. Protección de datos de Oracle sólo está disponible con Oracle Database Enterprise Edition. Cada equipo debe tener al menos 2 GB de memoria y 5 GB de espacio en disco. Para obtener la información más actualizada sobre la plataforma proporcionada tamaños VM, vea [Tamaños de máquina Virtual para Azure](virtual-machines-windows-sizes.md). Si necesita volumen de disco adicional para sus máquinas virtuales, puede adjuntar discos adicionales. Para obtener más información, consulte [cómo adjuntar un disco de datos a una máquina Virtual](virtual-machines-windows-classic-attach-disk.md).



- Que ha definido los nombres de máquina Virtual como "Machine1" para la máquina virtual y "Machine2" de la máquina virtual en espera principal en el portal de clásico de Azure.

- Ha establecido la variable de entorno **ORACLE_HOME** para que apunten a la misma ruta de instalación de raíz de oracle en principal y de reserva máquinas virtuales, como por ejemplo `C:\OracleDatabase\product\11.2.0\dbhome_1\database`.

- Inicie sesión en su servidor de Windows como miembro del grupo **administradores** o como miembro del grupo **ORA_DBA** .

En este tutorial, podrá:

Implementar el entorno de base de datos física en espera

1. Crear una base de datos principal

2. Preparar la base de datos principal para la creación de la base de datos en espera

    1. Habilitar el registro de forzado

    2. Crear un archivo de contraseña

    3. Configurar un registro de rehacer en espera

    4. Habilitar el archivado

    5. Establecer parámetros de inicialización de base de datos principal

Crear una base de datos en espera físico

1. Preparar un archivo de parámetros de inicialización para base de datos en espera

2. Configurar la escucha y tnsnames para admitir la base de datos en los equipos principales y de reserva

    1. Configurar listener.ora en ambos servidores para mantener las entradas para ambas bases de datos

    2. Para mantener las entradas de bases de datos principales y de reserva, configure tnsnames.ora en los equipos virtuales principal y de reserva. 

    3. Iniciar la escucha y Active tnsping en ambas máquinas virtuales tanto a los servicios.

3. Iniciar la instancia de estado de nomount en espera

4. Utilizar RMAN para duplicar la base de datos y crear una base de datos en espera

5. Iniciar la base de datos en espera físico en modo de recuperación administrados

6. Comprobar la base de datos en espera físico

> [AZURE.IMPORTANT] En este tutorial se ha configurado y probado con la siguiente configuración de hardware y software:
>
>|                      | **Base de datos principal**                      | **Base de datos en espera**                      |
>|----------------------|-------------------------------------------|-------------------------------------------|
>| **Versión de Oracle**   | Versión de Enterprise Oracle11g (11.2.0.4.0) | Versión de Enterprise Oracle11g (11.2.0.4.0) |
>| **Nombre del equipo**     | Machine1                                  | Machine2                                  |
>| **Sistema operativo** | Windows 2008 R2                           | Windows 2008 R2                           |
>| **Oracle SID**       | PRUEBA                                      | PRUEBA\_Stby de EMC                                |
>| **Memoria**           | Min 2 GB                                  | Min 2 GB                                  |
>| **Espacio en disco**       | Min 5 GB                                  | Min 5 GB                                  |

En versiones posteriores de base de datos de Oracle y protección de datos de Oracle, puede haber algunos cambios adicionales que necesita para implementar. La información específica de la versión más actualizada, consulte la documentación de [Protección de datos](http://www.oracle.com/technetwork/database/features/availability/data-guard-documentation-152848.html) y la [Base de datos de Oracle](http://www.oracle.com/us/corporate/features/database-12c/index.html) en el sitio web de Oracle.

##<a name="implement-the-physical-standby-database-environment"></a>Implementar el entorno de base de datos física en espera
### <a name="1-create-a-primary-database"></a>1. crear una base de datos principal

- Crear una base de datos principal "Prueba" en la máquina Virtual principal. Para obtener más información, vea Crear y configurar una base de datos de Oracle.
- Para ver el nombre de la base de datos, conectarse a la base de datos como el usuario de sistema con el rol SYSDBA en la instrucción SQL * Plus símbolo del sistema y ejecute la instrucción siguiente:

        SQL> select name from v$database;

        The result will display like the following:

        NAME
        ---------
        TEST
- A continuación, los nombres de los archivos de base de datos de la vista del sistema dba_data_files de consulta:

        SQL> select file_name from dba_data_files;
        FILE_NAME
        -------------------------------------------------------------------------------
        C:\ <YourLocalFolder>\TEST\USERS01.DBF
        C:\ <YourLocalFolder>\TEST\UNDOTBS01.DBF
        C:\ <YourLocalFolder>\TEST\SYSAUX01.DBF
        C:\<YourLocalFolder>\TEST\SYSTEM01.DBF
        C:\<YourLocalFolder>\TEST\EXAMPLE01.DBF

### <a name="2-prepare-the-primary-database-for-standby-database-creation"></a>2. preparar la base de datos principal para la creación de la base de datos en espera

Antes de crear una base de datos en espera, se recomienda que se asegure de que la base de datos principal está configurado correctamente. A continuación se muestra una lista de los pasos que debe llevar a cabo:

1. Habilitar el registro de forzado
2. Crear un archivo de contraseña
3. Configurar un registro de rehacer en espera
4. Habilitar el archivado
5. Establecer parámetros de inicialización de base de datos principal

#### <a name="enable-forced-logging"></a>Habilitar el registro de forzado

Para implementar una base de datos en espera, es necesario habilitar 'Forzado registro' en la base de datos principal. Esta opción garantiza que incluso si se realiza una operación de 'NOREGISTRO', forzar registro tiene prioridad y se registran todas las operaciones en los registros de recuperación. Por lo tanto, nos Asegúrese de que todo el contenido de la base de datos principal está conectado y que espera la replicación incluye todas las operaciones en la base de datos principal. Para habilitar el registro de fuerza, ejecute la instrucción alter database:

    SQL> ALTER DATABASE FORCE LOGGING;

    Database altered.

#### <a name="create-a-password-file"></a>Crear un archivo de contraseña

Para poder enviar y aplicar los registros archivados desde el servidor principal en el servidor en espera, la contraseña de sistema debe ser idéntica en los servidores principales y de reserva. Por eso cree un archivo de contraseña en la base de datos principal y copiarlo en el servidor en espera.

>[AZURE.IMPORTANT] Cuando se usa la base de datos de Oracle 12c, hay un nuevo usuario, **SYSDG**, que puede utilizar para administrar la protección de datos de Oracle. Para obtener más información, vea [cambios en la base de datos de Oracle 12 c lanzamiento](http://docs.oracle.com/database/121/UNXAR/release_changes.htm#UNXAR404).

Además, asegúrese de que la ORACLE\_entorno de inicio ya está definido en Machine1. Si no es así, puede definir como una variable de entorno mediante el cuadro de diálogo Variables de entorno. Para obtener acceso a este cuadro de diálogo, inicie la utilidad de **sistema** haciendo doble clic en el icono del sistema en el **Panel de Control**; a continuación, haga clic en la ficha **Opciones avanzadas** y seleccione **Variables de entorno**. Para establecer las variables de entorno, haga clic en el botón **nuevo** en **Variables del sistema**. Después de configurar las variables de entorno, cierre el símbolo de Windows existente y abrir uno nuevo.

Ejecute la instrucción siguiente para pasar a la Oracle\_directorio de inicio, como C:\\bases de datos Oracle\\producto\\11.2.0\\dbhome\_1\\base de datos.

    cd %ORACLE_HOME%\database

A continuación, cree un archivo de contraseña con la herramienta de creación de archivo de contraseña, [ORAPWD](http://docs.oracle.com/cd/B28359_01/server.111/b28310/dba007.htm). En el mismo Windows símbolo Machine1, ejecute el siguiente comando, establezca el valor de contraseña como la contraseña del **sistema**:

    ORAPWD FILE=PWDTEST.ora PASSWORD=password FORCE=y

Este comando crea un archivo de contraseña, denominado como PWDTEST.ora en ORACLE\_inicio\\directorio de base de datos. Debe copiar este archivo en % ORACLE\_inicio %\\directorio en MÁQUINA2 de base de datos manualmente.

#### <a name="configure-a-standby-redo-log"></a>Configurar un registro de rehacer en espera

A continuación, debe configurar un registro de rehacer en espera para que el principal correctamente puede recibir el rehacer cuando se convierte en una suspensión. Previamente crearlos aquí también permite a los registros de recuperación en espera crear automáticamente en espera. Es importante configurar suspensión Rehacer registros (SRL) con el mismo tamaño como el online Rehacer registros. El tamaño de los archivos de registro de rehacer en espera actual debe coincidir exactamente con el tamaño de los archivos de registro de rehacer en línea de base de datos principal actual.

Ejecute la instrucción siguiente en la instrucción SQL\*PLUS símbolo en Machine1. El archivo de registro de $ v es una vista de sistema que contiene información acerca de los archivos de registro de rehacer.

    SQL> select * from v$logfile;
    GROUP# STATUS  TYPE    MEMBER                                                       IS_
    ---------- ------- ------- ------------------------------------------------------------ ---
    3         ONLINE  C:\<YourLocalFolder>\TEST\REDO03.LOG               NO
    2         ONLINE  C:\<YourLocalFolder>\TEST\REDO02.LOG               NO
    1         ONLINE  C:\<YourLocalFolder>\TEST\REDO01.LOG               NO
    Next, query the v$log system view, displays log file information from the control file.
    SQL> select bytes from v$log;
    BYTES
    ----------
    52428800
    52428800
    52428800


Tenga en cuenta que 52428800 50 megabytes.

A continuación, en la instrucción SQL\*Plus ventana, ejecute las siguientes instrucciones para agregar un nuevo grupo de archivos de registro de rehacer en espera a una base de datos en espera y especifique un número que identifica el grupo mediante la cláusula GROUP. Usar números de grupo puede realizar la administración de grupos de archivos de registro de rehacer en espera sea más fáciles:

    SQL> ALTER DATABASE ADD STANDBY LOGFILE GROUP 4 'C:\<YourLocalFolder>\TEST\REDO04.LOG' SIZE 50M;
    Database altered.
    SQL> ALTER DATABASE ADD STANDBY LOGFILE GROUP 5 'C:\<YourLocalFolder>\TEST\REDO05.LOG' SIZE 50M;
    Database altered.
    SQL> ALTER DATABASE ADD STANDBY LOGFILE GROUP 6 'C:\<YourLocalFolder>\TEST\REDO06.LOG' SIZE 50M;
    Database altered.

A continuación, ejecute la siguiente vista del sistema para mostrar información sobre rehacer los archivos de registro. Esta operación también comprueba que se han creado los grupos de archivos de registro de rehacer en espera:

    SQL> select * from v$logfile;
    GROUP# STATUS  TYPE MEMBER IS_
    ---------- ------- ------- --------------------------------------------- ---
    3         ONLINE C:\<YourLocalFolder>\TEST\REDO03.LOG NO
    2         ONLINE C:\<YourLocalFolder>\TEST\REDO02.LOG NO
    1         ONLINE C:\<YourLocalFolder>\TEST\REDO01.LOG NO
    4         STANDBY C:\<YourLocalFolder>\TEST\REDO04.LOG
    5         STANDBY C:\<YourLocalFolder>\TEST\REDO05.LOG NO
    6         STANDBY C:\<YourLocalFolder>\TEST\REDO06.LOG NO
    6 rows selected.

#### <a name="enable-archiving"></a>Habilitar el archivado

A continuación, habilitar el archivado mediante la ejecución de las siguientes instrucciones para la base de datos principal en modo ARCHIVELOG y habilitar el archivado automático. Puede activar el modo de registro de archivo montaje de la base de datos y, a continuación, ejecute el comando archivelog.

En primer lugar, inicie sesión como sysdba. En el símbolo del sistema de Windows, ejecute:

    sqlplus /nolog

    connect / as sysdba

A continuación, cierre la base de datos en la instrucción SQL\*Plus símbolo del sistema:

    SQL> shutdown immediate;
    Database closed.
    Database dismounted.
    ORACLE instance shut down.

A continuación, ejecute el comando de montaje de inicio a la base de datos de montaje. Esto garantiza que Oracle asocia la instancia a la base de datos especificada.

    SQL> startup mount;
    ORACLE instance started.
    Total System Global Area 1503199232 bytes
    Fixed Size                  2281416 bytes
    Variable Size             922746936 bytes
    Database Buffers          570425344 bytes
    Redo Buffers                7745536 bytes
    Database mounted.

A continuación, ejecute:

    SQL> alter database archivelog;
    Database altered.

A continuación, ejecute al Alter instrucción de base de datos con la cláusula abierta para que la base de datos disponibles para su uso normal:

    SQL> alter database open;

    Database altered.

#### <a name="set-primary-database-initialization-parameters"></a>Establecer parámetros de inicialización de base de datos principal

Para configurar la protección de datos, debe crear y configurar los parámetros de espera en un pfile normal (archivo de texto inicialización parámetro) primera. Cuando la pfile esté listo, debe convertirlo en un archivo de parámetro de servidor (SPFILE).

Puede controlar el entorno de protección de datos usando los parámetros en la inicialización. Archivo de ORA. Cuando se siguen este tutorial, necesita actualizar la base de datos principal inicialización. ORA para que TI puede contener dos funciones: principal o en espera.

    SQL> create pfile from spfile;
    File created.

A continuación, debe modificar la pfile para agregar los parámetros en espera. Para ello, abra la INITTEST. ORA de archivos en la ubicación de % ORACLE\_inicio %\\base de datos. A continuación, anexar las siguientes instrucciones al archivo INITTEST.ora. Convención de nomenclatura para su inicialización. Archivo de ORA es inicialización\<YourDatabaseName\>. ORA.

    db_name='TEST'
    db_unique_name='TEST'
    LOG_ARCHIVE_CONFIG='DG_CONFIG=(TEST,TEST_STBY)'
    LOG_ARCHIVE_DEST_1= 'LOCATION=C:\OracleDatabase\archive   VALID_FOR=(ALL_LOGFILES,ALL_ROLES) DB_UNIQUE_NAME=TEST'
    LOG_ARCHIVE_DEST_2= 'SERVICE=TEST_STBY LGWR ASYNC VALID_FOR=(ONLINE_LOGFILES,PRIMARY_ROLE) DB_UNIQUE_NAME=TEST_STBY'
    LOG_ARCHIVE_DEST_STATE_1=ENABLE
    LOG_ARCHIVE_DEST_STATE_2=ENABLE
    REMOTE_LOGIN_PASSWORDFILE=EXCLUSIVE
    LOG_ARCHIVE_FORMAT=%t_%s_%r.arc
    LOG_ARCHIVE_MAX_PROCESSES=30
    # Standby role parameters --------------------------------------------------------------------
    fal_server=TEST_STBY
    fal_client=TEST
    standby_file_management=auto
    db_file_name_convert='TEST_STBY','TEST'
    log_file_name_convert='TEST_STBY','TEST'
    # ---------------------------------------------------------------------------------------------


El bloque de declaración anterior incluye tres elementos importantes de configuración:
-   **... LOG_ARCHIVE_CONFIG:** Definir los identificadores únicos de la base de datos con esta instrucción.
-   **... LOG_ARCHIVE_DEST_1:** Definir la ubicación de la carpeta de archivo local con esta instrucción. Le recomendamos que cree un nuevo directorio para las necesidades de archivado de la base de datos y especifique la ubicación del archivo local con esta instrucción explícitamente en lugar de usar carpeta predeterminada de Oracle % ORACLE_HOME%\database\archive.
-   **LOG_ARCHIVE_DEST_2... LGWR ASYNC...:** definir un proceso de escritura de registro asincrónica (LGWR) para recopilar datos de transacción rehacer y transmitir a destinos en espera. En este caso, el DB_UNIQUE_NAME especifica un nombre único para la base de datos en el servidor de reserva de destino.

Una vez que el nuevo archivo de parámetro esté listo, debe crear el spfile de ella.

Primero, cierre la base de datos:

    SQL> shutdown immediate;

    Database closed.

    Database dismounted.

    ORACLE instance shut down.

A continuación, ejecute el comando nomount de inicio de como sigue:

    SQL> startup nomount pfile='c:\OracleDatabase\product\11.2.0\dbhome_1\database\initTEST.ora';
    ORACLE instance started.
    Total System Global Area 1503199232 bytes
    Fixed Size                  2281416 bytes
    Variable Size             922746936 bytes
    Database Buffers          570425344 bytes
    Redo Buffers                7745536 bytes

Ahora, cree un spfile:

    SQL>create spfile frompfile='c:\OracleDatabase\product\11.2.0\dbhome\_1\database\initTEST.ora';

    File created.

A continuación, cierre la base de datos:

    SQL> shutdown immediate;

    ORA-01507: database not mounted

A continuación, use el comando de inicio para iniciar una instancia:

    SQL> startup;
    ORACLE instance started.
    Total System Global Area 1503199232 bytes
    Fixed Size                  2281416 bytes
    Variable Size             922746936 bytes
    Database Buffers          570425344 bytes
    Redo Buffers                7745536 bytes
    Database mounted.
    Database opened.

##<a name="create-a-physical-standby-database"></a>Crear una base de datos en espera físico
En esta sección se centra en los pasos que debe realizar en MÁQUINA2 para preparar la base de datos en espera física.

En primer lugar, necesitará escritorio remoto a Machine2 a través del portal clásico Azure.

A continuación, en el servidor de reserva (Machine2), crear todas las carpetas necesarias para la base de datos en espera, como C:\\\<YourLocalFolder\>\\prueba. Mientras sigue este tutorial, asegúrese de que la estructura de carpetas coincide con la estructura de carpetas en Machine1 conservar todos los archivos necesarios, como archivos controlfile, archivos de datos, redologfiles, udump, bdump y cdump. Además, defina el ORACLE\_inicio y ORACLE\_variables de entorno BASE en Máquina2. Si no es así, definirlos como una variable de entorno mediante el cuadro de diálogo Variables de entorno. Para obtener acceso a este cuadro de diálogo, inicie la utilidad de **sistema** haciendo doble clic en el icono del sistema en el **Panel de Control**; a continuación, haga clic en la ficha **Opciones avanzadas** y seleccione **Variables de entorno**. Para establecer las variables de entorno, haga clic en el botón **nuevo** en las **Variables del sistema**. Después de configurar las variables de entorno, debe cerrar el símbolo de Windows existente y abrir uno nuevo para ver los cambios.

A continuación, siga estos pasos:

1. Preparar un archivo de parámetros de inicialización para base de datos en espera

2. Configurar la escucha y tnsnames para admitir la base de datos en los equipos principales y de reserva

    1. Configurar listener.ora en ambos servidores para mantener las entradas para ambas bases de datos

    2. Configurar tnsnames.ora en las máquinas virtuales de principal y de reserva para mantener las entradas de bases de datos principales y de reserva

    3. Iniciar la escucha y Active tnsping en ambas máquinas virtuales tanto a los servicios.

3. Iniciar la instancia de estado de nomount en espera

4. Utilizar RMAN para duplicar la base de datos y crear una base de datos en espera

5. Iniciar la base de datos en espera físico en modo de recuperación administrados

6. Comprobar la base de datos en espera físico

### <a name="1-prepare-an-initialization-parameter-file-for-standby-database"></a>1. preparar un archivo de parámetros de inicialización para base de datos en espera

Esta sección muestra cómo preparar un archivo de parámetros de inicialización para la base de datos en espera. Para ello, primero copie la INITTEST. ORA de archivos del equipo 1 a Machine2 manualmente. Debería poder ver la INITTEST. ORA de archivos en la carpeta % ORACLE\_inicio %\\carpeta de base de datos en dos equipos. A continuación, modifique el archivo INITTEST.ora en MÁQUINA2 para configurarla para el rol en espera como se indica a continuación:

    db_name='TEST'
    db_unique_name='TEST_STBY'
    db_create_file_dest='c:\OracleDatabase\oradata\test_stby’
    db_file_name_convert=’TEST’,’TEST_STBY’
    log_file_name_convert='TEST','TEST_STBY'


    job_queue_processes=10
    LOG_ARCHIVE_CONFIG='DG_CONFIG=(TEST,TEST_STBY)'
    LOG_ARCHIVE_DEST_1='LOCATION=c:\OracleDatabase\TEST_STBY\archives VALID_FOR=(ALL_LOGFILES,ALL_ROLES) DB_UNIQUE_NAME=’TEST'
    LOG_ARCHIVE_DEST_2='SERVICE=TEST LGWR ASYNC VALID_FOR=(ONLINE_LOGFILES,PRIMARY_ROLE)
    LOG_ARCHIVE_DEST_STATE_1='ENABLE'
    LOG_ARCHIVE_DEST_STATE_2='ENABLE'
    LOG_ARCHIVE_FORMAT='%t_%s_%r.arc'
    LOG_ARCHIVE_MAX_PROCESSES=30


El bloque de declaración anterior incluye dos elementos importantes de configuración:

-   **\*. LOG_ARCHIVE_DEST_1:** debe crear manualmente la carpeta c:\OracleDatabase\TEST_STBY\archives en Máquina2.
-   **\*. LOG_ARCHIVE_DEST_2:** se trata de un paso opcional. Se establece como podrían ser necesarias cuando el equipo principal está en mantenimiento y el equipo en espera, se convierte en una base de datos principal.

A continuación, debe iniciar la instancia en espera. En el servidor de base de datos en espera, escriba el comando siguiente en el símbolo del sistema de Windows para crear una instancia de Oracle mediante la creación de un servicio de Windows:

    oradim -NEW -SID TEST\_STBY -STARTMODE MANUAL

El comando **Oradim** crea una instancia de Oracle, pero no inicia. Puede encontrar en la unidad C:\\bases de datos Oracle\\producto\\11.2.0\\dbhome\_1\\directorio BIN.

##<a name="configure-the-listener-and-tnsnames-to-support-the-database-on-primary-and-standby-machines"></a>Configurar la escucha y tnsnames para admitir la base de datos en los equipos principales y de reserva
Antes de crear una base de datos en espera, debe asegurarse de que las bases de datos principales y de reserva en la configuración pueden hablar entre sí. Para ello, debe configurar la escucha y TNSNames manualmente o mediante la herramienta de configuración de red NETCA. Esto es una tarea obligatoria cuando se usa la utilidad de recuperación Manager (RMAN).

### <a name="configure-listenerora-on-both-servers-to-hold-entries-for-both-databases"></a>Configurar listener.ora en ambos servidores para mantener las entradas para ambas bases de datos

Escritorio remoto Machine1 y editar el archivo listener.ora como especificado a continuación. Al editar el archivo listener.ora, siempre asegúrese de que la apertura y cierre de paréntesis alineen en la misma columna. Puede encontrar el archivo listener.ora en la siguiente carpeta: c:\\bases de datos Oracle\\producto\\11.2.0\\dbhome\_1\\red\\administración\\.

    # listener.ora Network Configuration File: C:\OracleDatabase\product\11.2.0\dbhome_1\network\admin\listener.ora

    # Generated by Oracle configuration tools.

    SID_LIST_LISTENER =
      (SID_LIST =
        (SID_DESC =
          (SID_NAME = test)
          (ORACLE_HOME = C:\OracleDatabase\product\11.2.0\dbhome_1)
          (PROGRAM = extproc)
          (ENVS = "EXTPROC_DLLS=ONLY:C:\OracleDatabase\product\11.2.0\dbhome_1\bin\oraclr11.dll")
        )
      )

    LISTENER =
      (DESCRIPTION_LIST =
        (DESCRIPTION =
          (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))
          (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
        )
      )

Escritorio remoto, siguiente Machine2 y editar los listener.ora archivo como sigue: # listener.ora archivo de configuración de red: C:\OracleDatabase\product\11.2.0\dbhome_1\network\admin\listener.ora

    # Generated by Oracle configuration tools.

    SID_LIST_LISTENER =
      (SID_LIST =
        (SID_DESC =
          (SID_NAME = test_stby)
          (ORACLE_HOME = C:\OracleDatabase\product\11.2.0\dbhome_1)
          (PROGRAM = extproc)
          (ENVS = "EXTPROC_DLLS=ONLY:C:\OracleDatabase\product\11.2.0\dbhome_1\bin\oraclr11.dll")
        )
      )

    LISTENER =
      (DESCRIPTION_LIST =
        (DESCRIPTION =
          (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))
          (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
        )
      )


### <a name="configure-tnsnamesora-on-the-primary-and-standby-virtual-machines-to-hold-entries-for-both-primary-and-standby-databases"></a>Configurar tnsnames.ora en las máquinas virtuales de principal y de reserva para mantener las entradas de bases de datos principales y de reserva

Escritorio remoto Machine1 y editar el archivo tnsnames.ora como especificado a continuación. Puede encontrar el archivo tnsnames.ora en la siguiente carpeta: c:\\bases de datos Oracle\\producto\\11.2.0\\dbhome\_1\\red\\administración\\.

    TEST =
      (DESCRIPTION =
        (ADDRESS_LIST =
          (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))
        )
        (CONNECT_DATA =
          (SERVICE_NAME = test)
        )
      )

    TEST_STBY =
      (DESCRIPTION =
        (ADDRESS_LIST =
          (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))
        )
        (CONNECT_DATA =
          (SERVICE_NAME = test_stby)
        )
      )

Escritorio remoto Machine2 y editar el tnsnames.ora archivo como sigue:

    TEST =
      (DESCRIPTION =
        (ADDRESS_LIST =
          (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))
        )
        (CONNECT_DATA =
          (SERVICE_NAME = test)
        )
      )

    TEST_STBY =
      (DESCRIPTION =
        (ADDRESS_LIST =
          (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))
        )
        (CONNECT_DATA =
          (SERVICE_NAME = test_stby)
        )
      )


### <a name="start-the-listener-and-check-tnsping-on-both-virtual-machines-to-both-services"></a>Iniciar la escucha y Active tnsping en ambas máquinas virtuales tanto a los servicios.

Abra un símbolo de Windows en máquinas virtuales de principal y de reserva y ejecute las instrucciones siguientes:

    C:\Users\DBAdmin>tnsping test

    TNS Ping Utility for 64-bit Windows: Version 11.2.0.1.0 - Production on 14-NOV-2013 06:29:08
    Copyright (c) 1997, 2010, Oracle.  All rights reserved.
    Used parameter files:
    C:\OracleDatabase\product\11.2.0\dbhome_1\network\admin\sqlnet.ora
    Used TNSNAMES adapter to resolve the alias
    Attempting to contact (DESCRIPTION = (ADDRESS_LIST = (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))) (CONNECT_DATA = (SER
    VICE_NAME = test)))
    OK (0 msec)


    C:\Users\DBAdmin>tnsping test_stby

    TNS Ping Utility for 64-bit Windows: Version 11.2.0.1.0 - Production on 14-NOV-2013 06:29:16
    Copyright (c) 1997, 2010, Oracle.  All rights reserved.
    Used parameter files:
    C:\OracleDatabase\product\11.2.0\dbhome_1\network\admin\sqlnet.ora
    Used TNSNAMES adapter to resolve the alias
    Attempting to contact (DESCRIPTION = (ADDRESS_LIST = (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))) (CONNECT_DATA = (SER
    VICE_NAME = test_stby)))
    OK (260 msec)


##<a name="start-up-the-standby-instance-in-nomount-state"></a>Iniciar la instancia de estado de nomount en espera
Para configurar el entorno para admitir la base de datos en espera en la máquina Virtual en espera (MACHINE2).

En primer lugar, copie el archivo de contraseña desde el equipo principal (Machine1) en el equipo en espera (Machine2) manualmente. Esto es necesario, como la contraseña de **sistema** debe ser idéntica en ambos equipos.

A continuación, abra el símbolo del sistema de Windows en MÁQUINA2 y configurar las variables de entorno para que apunten a la base de datos en espera como sigue:

    SET ORACLE_HOME=C:\OracleDatabase\product\11.2.0\dbhome_1
    SET ORACLE_SID=TEST_STBY

A continuación, iniciar la base de datos de suspensión en el estado de nomount y, a continuación, generar un spfile.

Iniciar la base de datos:

    SQL>shutdown immediate;

    SQL>startup nomount
    ORACLE instance started.

    Total System Global Area  747417600 bytes
    Fixed Size                  2179496 bytes
    Variable Size             473960024 bytes
    Database Buffers          264241152 bytes
    Redo Buffers                7036928 bytes


##<a name="use-rman-to-clone-the-database-and-to-create-a-standby-database"></a>Utilizar RMAN para duplicar la base de datos y crear una base de datos en espera
Puede usar la utilidad de recuperación Manager (RMAN) para tomar cualquier copia de seguridad de la base de datos principal para crear la base de datos en espera física.

Escritorio remoto para la máquina Virtual en espera (MACHINE2) y ejecutar la herramienta RMAN especificando una conexión completa de cadena para el destino (base de datos principal, Machine1) y auxiliar (database en espera, Machine2) instancias.

>[AZURE.IMPORTANT] No utilice la autenticación del sistema operativo porque no hay ninguna base de datos en el equipo servidor de reserva todavía.

    C:\> RMAN TARGET sys/password@test AUXILIARY sys/password@test_STBY

    RMAN>DUPLICATE TARGET DATABASE
      FOR STANDBY
      FROM ACTIVE DATABASE
      DORECOVER
        NOFILENAMECHECK;

##<a name="start-the-physical-standby-database-in-managed-recovery-mode"></a>Iniciar la base de datos en espera físico en modo de recuperación administrados
Este tutorial muestra cómo crear una base de datos en espera física. Para obtener información sobre la creación de una base de datos en espera lógico, consulte la documentación de Oracle.

Abra SQL\*Plus símbolo y habilitar la protección de datos en el servidor (MACHINE2) o máquina Virtual en espera como sigue:

    SHUTDOWN IMMEDIATE;
    STARTUP MOUNT;
    ALTER DATABASE RECOVER MANAGED STANDBY DATABASE DISCONNECT FROM SESSION;

Cuando se abre la base de datos en espera en el registro de almacenamiento del **montaje** modo continúa de envío y el proceso de recuperación administrados continúa registro de aplicación de la base de datos en espera. Esto garantiza que la base de datos en espera permanezca actualizada con la base de datos principal. Observe que la base de datos en espera no puede ser accesible para generar informes durante este período.

Al abrir la base de datos en espera en modo de **Solo lectura** , el archivo de envío de registro continúa. Pero el proceso de recuperación administrados se detiene. Hace que la base de datos en espera hasta que se reanude el proceso de recuperación administrados, puede convertirse en cada vez más actualizada. Puede tener acceso a la base de datos en espera para los informes durante este periodo pero pueden que los datos no refleje los cambios más recientes.

En general, recomendamos que mantenga la base de datos en espera en modo de **montaje** para mantener los datos de la base de datos en espera actualizada si se produce un error de la base de datos principal. Sin embargo, puede mantener la base de datos en espera en modo de **Solo lectura** para generar informes en función de los requisitos de la aplicación. Los pasos siguientes muestran cómo habilitar la protección de datos en modo de solo lectura con SQL\*Plus:

    SHUTDOWN IMMEDIATE;
    STARTUP MOUNT;
    ALTER DATABASE OPEN READ ONLY;


##<a name="verify-the-physical-standby-database"></a>Comprobar la base de datos en espera físico
Esta sección muestra cómo comprobar la configuración de alta disponibilidad como administrador.

Abra SQL\*Plus ventana de símbolo del sistema y verificación archivados Rehacer registro en la máquina Virtual de suspensión (Machine2):

    SQL> show parameters db_unique_name;

    NAME                                TYPE       VALUE
    ------------------------------------ ----------- ------------------------------
    db_unique_name                      string     TEST_STBY

    SQL> SELECT NAME FROM V$DATABASE

    SQL> SELECT SEQUENCE#, FIRST_TIME, NEXT_TIME, APPLIED FROM V$ARCHIVED_LOG ORDER BY SEQUENCE#;

    SEQUENCE# FIRST_TIM NEXT_TIM APPLIED
    ----------------  ---------------  --------------- ------------
    45                    23-FEB-14   23-FEB-14   YES
    45                    23-FEB-14   23-FEB-14   NO
    46                    23-FEB-14   23-FEB-14   NO
    46                    23-FEB-14   23-FEB-14   YES
    47                    23-FEB-14   23-FEB-14   NO
    47                    23-FEB-14   23-FEB-14   NO

Abra SQL * Plus archivos de registro de símbolo ventana y cambiar en el equipo principal (Machine1):

    SQL> alter system switch logfile;
    System altered.

    SQL> archive log list
    Database log mode              Archive Mode
    Automatic archival             Enabled
    Archive destination            C:\OracleDatabase\archive
    Oldest online log sequence     69
    Next log sequence to archive   71
    Current log sequence           71

Compruebe el registro de rehacer archivado en la máquina Virtual de suspensión (Machine2):

    SQL> SELECT SEQUENCE#, FIRST_TIME, NEXT_TIME, APPLIED FROM V$ARCHIVED_LOG ORDER BY SEQUENCE#;

    SEQUENCE# FIRST_TIM NEXT_TIM APPLIED
    ----------------  ---------------  --------------- ------------
    45                    23-FEB-14   23-FEB-14   YES
    46                    23-FEB-14   23-FEB-14   YES
    47                    23-FEB-14   23-FEB-14   YES
    48                    23-FEB-14   23-FEB-14   YES

    49                    23-FEB-14   23-FEB-14   YES
    50                    23-FEB-14   23-FEB-14   IN-MEMORY

Compruebe cualquier espacio en la máquina Virtual de suspensión (Machine2):

    SQL> SELECT * FROM V$ARCHIVE_GAP;
    no rows selected.

Otro método de verificación podría ser para conmutar por error a la base de datos en espera y probar si es posible recuperación de la base de datos principal. Para activar la base de datos en espera como una base de datos principal, use las instrucciones siguientes:

    SQL> ALTER DATABASE RECOVER MANAGED STANDBY DATABASE FINISH;
    SQL> ALTER DATABASE ACTIVATE STANDBY DATABASE;

Si no ha habilitado flashback en la base de datos principal original, se recomienda que anular la base de datos principal original y volver a crear como una base de datos en espera.

Le recomendamos que habilite la base de datos de flashback en el principal y las bases de datos en espera. Cuando se produce un error, la base de datos principal puede marcada volver a la vez antes de la migración tras error y se convierten rápidamente en una base de datos en espera.

##<a name="additional-resources"></a>Recursos adicionales
[Imágenes de máquina Virtual de Oracle para Azure](virtual-machines-windows-classic-oracle-images.md)

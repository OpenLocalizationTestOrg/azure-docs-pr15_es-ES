<properties
    pageTitle="Configurar GoldenGate de Oracle en máquinas virtuales | Microsoft Azure"
    description="Paso a través de un tutorial para configurar e implementar Oracle GoldenGate en máquinas virtuales de servidor de Windows Azure para alta disponibilidad y recuperación de desastres."
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


#<a name="configuring-oracle-goldengate-for-azure"></a>Configuración de Oracle GoldenGate de Azure


Este tutorial muestra cómo configurar GoldenGate de Oracle para máquinas virtuales de Azure entorno para alta disponibilidad y la recuperación. El tutorial se centra en [la replicación bidireccional](http://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_about_gg.htm) de bases de datos de Oracle que no sean de RAC y requiere que ambos sitios están activos.

Oracle GoldenGate es compatible con la distribución de datos y la integración de datos. Le permite configurar una distribución de datos y solución de sincronización de datos a través de la configuración de la replicación de Oracle Oracle y proporciona una solución flexible de alta disponibilidad. Oracle GoldenGate el complemento de protección de datos de Oracle con sus capacidades de replicación para habilitar las migraciones y actualizaciones de distribución y el tiempo de inactividad de cero de la información de toda la empresa. Para obtener información detallada, consulte [Uso de Oracle GoldenGate con protección de datos de Oracle](http://docs.oracle.com/cd/E11882_01/server.112/e17157/unplanned.htm).

Oracle GoldenGate contiene los siguientes componentes principales: extraer datos bomba, Replicat, pistas o extraer archivos, puntos de control, administrador y Selector. Para que la replicación bidireccional entre dos sitios, debe crear y comenzar a todos los componentes en ambos sitios. Para obtener información detallada sobre la arquitectura de Oracle GoldenGate, consulte la [Guía de GoldenGate de Oracle](http://docs.oracle.com/goldengate/1212/gg-winux/index.html).

En este tutorial se supone que ya conoce teórica y práctica en conceptos de alta disponibilidad de base de datos de Oracle y recuperación, así como [GoldenGate de Oracle](http://docs.oracle.com/goldengate/1212/gg-winux/index.html). Para obtener más información, consulte el [sitio web de Oracle](http://www.oracle.com/technetwork/database/features/availability/index.html).

Además, en el tutorial se supone que ya ha implementado los siguientes requisitos previos:

- Ya ha revisado la sección Consideraciones de recuperación de desastres y alta disponibilidad en el tema de [imágenes de máquina Virtual de Oracle - varias consideraciones](virtual-machines-windows-classic-oracle-considerations.md) . Tenga en cuenta que Azure admite instancias de base de datos de Oracle independiente pero no Oracle Real aplicación clústeres (Oracle RAC) actualmente.

- El software de Oracle GoldenGate que haya descargado desde el sitio web de [Descargas de Oracle](http://www.oracle.com/us/downloads/index.html) . Ha seleccionado el software intermedio la fusión de producto paquete Oracle: integración de datos. A continuación, ha seleccionado Oracle GoldenGate en Oracle v11.2.1 Pack de Windows Media para Microsoft Windows x64 (64 bits) para una base de datos de Oracle 11 g. A continuación, descargue V11.2.1.0.3 de GoldenGate de Oracle para Oracle 11g de 64 bits en Windows 2008 (64 bits).

- Dos máquinas virtuales (VM) que haya creado en Azure con Oracle Enterprise Edition en Windows Server. Asegúrese de que los equipos virtuales están en el [mismo servicio de nube](virtual-machines-linux-load-balance.md) y en la misma [Red Virtual](https://azure.microsoft.com/documentation/services/virtual-network/) para asegurarse de que pueden acceder a ellos a través de la dirección IP privada persistente.

- En el portal de clásico Azure configurado los nombres de máquina Virtual como "MachineGG1" para un sitio y "MachineGG2" para el sitio B.

- Ha creado bases de datos de prueba "TestGG1" en el sitio A y "TestGG2" en el sitio B.

- Inicie sesión en su servidor de Windows como miembro del grupo Administradores o como miembro del grupo **ORA_DBA** .

En este tutorial, podrá:

1. Configuración de la base de datos en el sitio A y b.  

    1. Realizar la carga inicial de datos

2. Preparar el sitio A y B de sitio para la replicación de base de datos

3. Crear todos los objetos necesarios para admitir réplica DDL

4. Configurar el Administrador de GoldenGate en el sitio A y B

5. Crear grupo extraer y datos bomba procesos en el sitio A y b.

    1. Crear procesos extraer y bomba de datos en el sitio A

    2. Crear una tabla de control de GoldenGate en el sitio B

    3. Agregar REPLICAT en sitio B

    4. Crear procesos extraer y bomba de datos en el sitio B

    5. Crear una tabla de control de GoldenGate en el sitio A

    6. Agregar REPLICAT en el sitio A

    7. Agregar trandata en el sitio A y b.

    8. Iniciar procesos extraer y bomba de datos en el sitio A

    9. Iniciar procesos extraer y bomba de datos en el sitio B

    10. Iniciar proceso de REPLICAT en el sitio A

    11. Iniciar proceso de REPLICAT en el sitio B

6. Comprobar el proceso de replicación bidireccional

>[AZURE.IMPORTANT] Este tutorial se ha configurar y probar con la configuración de software siguiente:
>
>|                        | **Una base de datos del sitio**              | **Base de datos del sitio B**              |
>|------------------------|----------------------------------|----------------------------------|
>| **Versión de Oracle**     | Lanzamiento de Oracle11g 2-(11.2.0.1) | Lanzamiento de Oracle11g 2-(11.2.0.1) |
>| **Nombre del equipo**       | MachineGG1                       | MachineGG2                       |
>| **Sistema operativo**   | Windows 2008 R2                  | Windows 2008 R2                  |
>| **Oracle SID**         | TESTGG1                          | TESTGG2                          |
>| **Esquema de replicación** | SCOTT                            | SCOTT                            |

En versiones posteriores de base de datos de Oracle y Oracle GoldenGate, puede haber algunos cambios adicionales que necesita para implementar. La información más actualizada de versión específicos, consulte la documentación de [Oracle GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/index.html) y [Base de datos de Oracle](http://www.oracle.com/us/corporate/features/database-12c/index.html) en el sitio web de Oracle. Por ejemplo, para una base de datos de origen de la versión 11.2.0.4 y posterior, la captura de DDL realiza el servidor de logmining asincrónica y requiere sin desencadenadores especiales, tablas u otros objetos de base de datos esté instalado. Actualizaciones de Oracle GoldenGate pueden realizarse sin detener aplicaciones de usuario. El uso de un desencadenador DDL y objetos auxiliares se requiere cuando extraer está en modo integrado con una base de datos de origen de 11g Oracle anterior a la versión 11.2.0.4. Para obtener instrucciones detalladas, vea [instalar y configurar Oracle GoldenGate para base de datos de Oracle](http://docs.oracle.com/goldengate/1212/gg-winux/GIORA.pdf).

##<a name="1-setup-database-on-site-a-and-site-b"></a>1. configuración de la base de datos en el sitio A y b.
En esta sección se explica cómo llevar a cabo los requisitos previos de la base de datos en el sitio A y el sitio B. Debe realizar todos los pasos de esta sección en ambos sitios: sitio A y el sitio B.

En primer lugar, remoto escritorio al sitio A y B de sitio a través del portal clásico Azure. Abra un símbolo del sistema de Windows y cree un directorio de inicio para los archivos de instalación de Oracle GoldenGate:

    mkdir C:\OracleGG

A continuación, descomprima e instale el software de Oracle GoldenGate en esta carpeta. Después de este paso, puede iniciar el intérprete de comandos de Software de GoldenGate (GGSCI), ejecute el siguiente comando:

    C:\OracleGG\.\ggsci

Puede usar [GGSCI](http://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_gettingstarted.htm) para ejecutar varios comandos que configuran, controlar y supervisar GoldenGate de Oracle.

A continuación, ejecute el comando siguiente para crear las subcarpetas desde el paquete de instalación:

    GGSCI (Hostname) 1> CREATE SUBDIRS

Ejecute el comando siguiente para salir del símbolo del sistema de GGSCI:

    GGSCI (Hostname) 1> EXIT

A continuación, debe crear un usuario de base de datos que se utilizarán en los procesos de Oracle GoldenGate Manager, extraer y replicación. Tenga en cuenta que puede crear usuarios individuales para cada proceso o configurar un único usuario comunes. En este tutorial, creamos un usuario, que se conoce como ggate. A continuación, se concesión a ese usuario los privilegios necesarios. Tenga en cuenta que debe realizar las siguientes operaciones en el sitio A y el sitio B.

Abra SQL\*además de la ventana de comandos en el sitio A y B de sitio con privilegios de administrador de base de datos con **SYSDBA**, como:

    Enter username: / as sysdba

Y ejecutar:

    SQL> create tablespace ggs_data   datafile 'c:\OracleDatabase\oradata\<DBNAME>\<DBNAME>ggs_data01.dbf' size 200m;
    SQL> create user ggate identified by ggate default tablespace ggs_data  temporary tablespace temp;
          grant connect, resource to ggate;
          grant select any dictionary, select any table to ggate;
          grant create table to ggate;
          grant flashback any table to ggate;
          grant execute on dbms_flashback to ggate;
          grant execute on utl_file to ggate;
          grant create any table to ggate;
          grant insert any table to ggate;
          grant update any table to ggate;
          grant delete any table to ggate;
          grant drop any table to ggate;

A continuación, busque la inicialización\<DatabaseSID\>. ORA de archivos en la carpeta % ORACLE\_inicio %\\carpeta en el sitio A y B de sitio de la base de datos y anexar los siguientes parámetros de base de datos a INITTEST.ora:

    UNDO\_MANAGEMENT=AUTO
    UNDO\_RETENTION=86400

Para obtener una lista completa de todos los comandos de Oracle GoldenGate GGSCI, vea [referencia de Oracle GoldenGate para Windows](http://docs.oracle.com/goldengate/1212/gg-winux/GWURF/ggsci_commands.htm).

### <a name="perform-initial-data-load"></a>Realizar la carga inicial de datos

Puede realizar la carga inicial de datos en la base de datos siguiendo varios métodos. Por ejemplo, puede usar la [Carga directa de Oracle GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_initsync.htm) o normales utilidades de importación y exportación para exportar los datos de una tabla desde el sitio al sitio B.

Para mostrar el proceso de replicación de Oracle GoldenGate, este tutorial muestra la creación de una tabla en el sitio y el sitio B, use los comandos siguientes.

En primer lugar, abra el SQL\*además de ventana de comandos y ejecute el comando siguiente para crear una tabla de inventario en bases de datos de sitio A y B de sitio:

    create table scott.inventory
    (prod_id number,
    prod_category varchar2(20),
    qty_in_stock number,
    last_dml timestamp default systimestamp);

A continuación, agregue una restricción a la tabla recién creada en el sitio y las bases de datos del sitio B:

    alter table scott.inventory add constraint pk_inventory primary key (prod_id) ;

A continuación, conceder todos los privilegios de la tabla de inventario de nuevo a la ggate de usuario en el sitio A y el sitio B:

    grant all on scott.inventory to ggate;

A continuación, crear y habilitar un desencadenador de base de datos, INVENTORY_CDR_TRG, en la tabla recién creado para asegurarse de que todas las transacciones de la nueva tabla se registran si el usuario no es ggate. Realizar esta operación en el sitio A y el sitio B.

    CREATE OR REPLACE TRIGGER INVENTORY_CDR_TRG
    BEFORE UPDATE
    ON SCOTT.INVENTORY
    REFERENCING NEW AS New OLD AS Old
    FOR EACH ROW
    BEGIN
    IF SYS_CONTEXT ('USERENV', 'SESSION_USER') != 'GGATE'
    THEN
    :NEW.LAST_DML := SYSTIMESTAMP;
    END IF;
    END;
    /


##<a name="2-prepare-site-a-and-site-b-for-database-replication"></a>2. preparar el sitio A y B de sitio para la replicación de base de datos
En esta sección se explica cómo preparar el sitio A y B de sitio para la replicación de base de datos. Debe realizar todos los pasos de esta sección en ambos sitios: sitio A y el sitio B.

En primer lugar, remoto escritorio al sitio A y B de sitio a través del portal clásico Azure. Cambiar la base de datos a modo de archivelog mediante la instrucción SQL * Plus ventana de comandos:

    sql>shutdown immediate
    sql>startup mount
    sql>alter database archivelog;
    sql>alter database open;


A continuación, habilitar mínimas [registro complementario](http://docs.oracle.com/cd/E11882_01/server.112/e22490/logminer.htm) como sigue:

    SQL> ALTER DATABASE ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;

A continuación, preparar la base de datos admite la replicación de DDL (lenguaje de definición de datos):

    SQL> alter system set recyclebin=off scope=spfile;

Después, cierre y reinicie la base de datos:

    sql>shutdown immediate
    sql>startup


##<a name="3-create-all-necessary-objects-to-support-ddl-replication"></a>3. crear todos los objetos necesarios para admitir réplica DDL
Esta sección muestra las secuencias de comandos que se debe usar para crear todos los objetos necesarios para admitir réplica DDL. Que necesita para ejecutar las secuencias de comandos especificadas en esta sección en el sitio A y el sitio B.

Abra un símbolo del sistema de Windows y desplácese a la carpeta GoldenGate de Oracle, como C:\\OracleGG. Inicie SQL\*Plus símbolo del sistema con privilegios de administrador de la base de datos, como el uso de **SYSDBA** en el sitio A y el sitio B.

A continuación, ejecute los scripts siguientes:

    SQL> @marker_setup.sql  
    Enter GoldenGate schema name: ggate
    SQL> @ddl_setup.sql  
    Enter GoldenGate schema name: ggate
    SQL> @role_setup.sql
    Enter GoldenGate schema name: ggate
    SQL> grant ggs_ggsuser_role to ggate;
     Grant succeeded.
     SQL> @ddl_enable
     Trigger altered.
     SQL> @ddl_pin ggate


Herramienta de GoldenGate de Oracle requiere un inicio de sesión de nivel de tabla para la compatibilidad con DDL (lenguaje de definición de datos). Ese es el motivo, habilitar complementaria de registro en el nivel de tabla mediante el comando Agregar TRANDATA. Abrir una ventana de intérprete de comandos de GoldenGate de Oracle, inicie sesión en la base de datos y luego ejecute el comando TRANDATA agregar:

    GGSCI 5> DBLOGIN USERID ggate, PASSWORD ggate

    GGSCI(Hostname) 6> add trandata scott.inventory

##<a name="4-configure-goldengate-manager-on-site-a-and-site-b"></a>4. configurar GoldenGate Manager en el sitio A y B
El Administrador de GoldenGate de Oracle realiza una serie de funciones como iniciar los demás procesos de GoldenGate, informes y administración de archivos de registro de seguimiento.

Necesita configurar el proceso de Oracle GoldenGate Manager en el sitio A y el sitio B. Para ello, siga estos pasos en el sitio A y el sitio B.

Ventanas abiertas en ventana de comandos e inician el intérprete de comandos de Oracle GoldenGate:

    cd C:\OracleGG\
    c:\OracleGG>ggsci
    Oracle GoldenGate Command Interpreter for Oracle
    Version 11.2.1.0.3 14400833 OGGCORE_11.2.1.0.3_PLATFORMS_120823.1258
    Windows x64 (optimized), Oracle 11g on Aug 23 2012 16:50:36
    Copyright (C) 1995, 2012, Oracle and/or its affiliates. All rights reserved.


Inicie la sesión GGSCI en una base de datos para que puedan ejecutar comandos que afectan a la base de datos:

    GGSCI (HostName) 1> DBLOGIN USERID ggate, PASSWORD ggate
    Successfully logged into database.

Mostrar el estado y de posposición (si procede) para todos los procesos de administrador, extraer y Replicat en un sistema:

    GGSCI (HostName) 2> info all
    Program     Status      Group       Lag           Time Since Chkpt
    MANAGER     STOPPED

Abra el archivo de parámetros con el comando Editar parámetros y, a continuación, agregue la información siguiente:

    GGSCI (HostName) 3> edit params mgr
    PORT 7809
    USERID ggate, PASSWORD ggate
    PURGEOLDEXTRACTS  C:\OracleGG\dirdat\ex, USECHECKPOINTS

Mostrar el estado y de posposición (si procede) para todos los procesos de administrador, extraer y Replicat en un sistema:

    GGSCI (HostName) 46> info all
    Program     Status      Group       Lag           Time Since Chkpt
    MANAGER     STOPPED

Inicie la sesión GGSCI en una base de datos para que puedan ejecutar comandos que afectan a la base de datos:

    GGSCI (HostName) 47> dblogin USERID ggate, PASSWORD ggate

    Successfully logged into database.

Iniciar el proceso de administrador:

    GGSCI (HostName) 48> start manager
    Manager started.

##<a name="5-create-extract-group-and-data-pump-processes-on-site-a-and-site-b"></a>5. crear extraer procesos de grupo y bomba de datos en el sitio A y b.

###<a name="create-extract-and-data-pump-processes-on-site-a"></a>Crear procesos extraer y bomba de datos en el sitio A

Tiene que crear los procesos de extraer y bomba de datos en el sitio A y el sitio B. escritorio remoto sitio A y B de sitio a través del portal clásico Azure. Abrir ventana de intérprete de comandos GGSCI. Ejecute los comandos siguientes en el sitio A:

    GGSCI (MachineGG1) 14> add extract ext1 tranlog begin now
    EXTRACT added.
    GGSCI (MachineGG1) 4> add exttrail C:\OracleGG\dirdat\lt, extract ext1
    EXTTRAIL added.
    GGSCI (MachineGG1) 16> add extract dpump1 exttrailsource C:\OracleGG\dirdat\aa
    EXTRACT added.
    GGSCI (MachineGG1) 17> add rmttrail C:\OracleGG\dirdat\ab extract dpump1
    RMTTRAIL added.

Abra el archivo de parámetro con el comando Editar parámetros y, a continuación, agregue la información siguiente: GGSCI (MachineGG1) 18 > Editar parámetros ext1 extraer ext1 ID ggate, contraseña ggate EXTTRAIL C:\OracleGG\dirdat\aa TRANLOGOPTIONS EXCLUDEUSER ggate tabla scott.inventory, GETBEFORECOLS (ON UPDATE KEYINCLUDING (prod_category, qty_in_stock, last_dml), en Eliminar KEYINCLUDING (prod_category, qty_in_stock, last_dml);)

Abra el archivo de parámetros con el comando Editar parámetros y, a continuación, agregue la información siguiente:

    GGSCI (MachineGG1) 15> edit params dpump1
    EXTRACT dpump1
     USERID ggate, PASSWORD ggate
     RMTHOST ActiveGG2orcldb, MGRPORT 7809, TCPBUFSIZE 100000
     RMTTRAIL C:\OracleGG\dirdat\ab
     PASSTHRU
     TABLE scott.inventory;

###<a name="create-a-goldengate-checkpoint-table-on-site-b"></a>Crear una tabla de control de GoldenGate en el sitio B

A continuación, debe agregar una tabla de control en el sitio B. Para ello, debe abrir una ventana de intérprete de comandos de GoldenGate y ejecutar:

    C:\OracleGG\ggsci
    GGSCI (MachineGG2) 1> DBLOGIN USERID ggate, PASSWORD ggate
    Successfully logged into database.

Y, a continuación, agregue la tabla de control a la base de datos, donde ggate es el propietario:

    GGSCI (MachineGG2) 2> ADD CHECKPOINTTABLE ggate.checkpointtable
    Successfully created checkpoint table ggate.checkpointtable.

Agregue el nombre de la tabla de punto de comprobación para el archivo de globales en el servidor de destino, que es el sitio B en este paso. Editar el archivo de globales en sitio B:

    GGSCI (MachineGG2) 1> EDIT PARAMS ./GLOBALS

A continuación, anexar el parámetro CHECKPOINTTABLE para el archivo de globales existente:

    GGSCHEMA ggate
    CHECKPOINTTABLE ggate.checkpointtable

Como paso final, guarde y cierre el archivo de parámetros globales.


###<a name="add-replicat-on-site-b"></a>Agregar REPLICAT en sitio B
Esta sección describe cómo agregar un proceso REPLICAT "REP2" en el sitio B.

Use el comando de agregar REPLICAT para crear un grupo de Replicat en sitio B:

    GGSCI (MachineGG2) 37> add replicat rep2 exttrail C:\OracleGG\dirdatab, checkpointtable ggate.checkpointtable

Abra el archivo de parámetros con el comando Editar parámetros y, a continuación, agregue la información siguiente:

    GGSCI (MachineGG2) 10> edit params rep2
    REPLICAT rep2
    ASSUMETARGETDEFS
    USERID ggate, PASSWORD ggate
    DISCARDFILE C:\OracleGG\dirdat\discard.txt, append,megabytes 10
    MAP scott.inventory, TARGET scott.inventory;

###<a name="create-extract-and-data-pump-processes-on-site-b"></a>Crear procesos extraer y bomba de datos en el sitio B

Esta sección describe cómo crear un nuevo proceso de extraer "EXT2" y "DPUMP2" a un nuevo proceso de bomba de datos en el sitio B:

    GGSCI (MachineGG2) 3> add extract ext2 tranlog begin now
     EXTRACT added.
    GGSCI (MachineGG2) 4> add exttrail C:\OracleGG\dirdat\ac extract ext2
     EXTTRAIL added.
    GGSCI (MachineGG2) 5> add extract dpump2 exttrailsource C:\OracleGG\dirdat\ac
     EXTRACT added.
    GGSCI (MachineGG2) 6> add rmttrail C:\OracleGG\dirdat\ad extract dpump2
     RMTTRAIL added.

Abra el archivo de parámetros con el comando Editar parámetros y, a continuación, agregue la información siguiente:

    GGSCI (MachineGG2) 31> edit params ext2
    EXTRACT ext2
    USERID ggate, PASSWORD ggate
    EXTTRAIL C:\OracleGG\dirdat\ac
    TRANLOGOPTIONS EXCLUDEUSER ggate
    TABLE scott.inventory,
    GETBEFORECOLS (
    ON UPDATE KEYINCLUDING (prod_category,qty_in_stock, last_dml),
    ON DELETE KEYINCLUDING (prod_category,qty_in_stock, last_dml));

Abra el archivo de parámetros con el comando Editar parámetros y, a continuación, agregue la información siguiente:

    GGSCI (MachineGG2) 32> edit params dpump2
    EXTRACT dpump2
    USERID ggate, PASSWORD ggate
    RMTHOST MachineGG1, MGRPORT 7809, TCPBUFSIZE 100000
    RMTTRAIL C:\OracleGG\dirdat\ad
    PASSTHRU
    TABLE scott.inventory;

###<a name="create-a-goldengate-checkpoint-table-on-site-a"></a>Crear una tabla de control de GoldenGate en el sitio A

Abra una ventana de intérprete de comandos de Oracle GoldenGate y crear una tabla de control:

    GGSCI (MachineGG1) 1> DBLOGIN USERID ggate, PASSWORD ggate
    Successfully logged into database.

    GGSCI (MachineGG1) 2> ADD CHECKPOINTTABLE ggate.checkpointtable

    Successfully created checkpoint table ggate.checkpointtable.

También debe agregar el nombre de la tabla de punto de comprobación para el archivo de globales en el sitio A.

Abrir ventana de intérprete de comandos de Oracle GoldenGate y editar el archivo de globales en sitio A:

    GGSCI (MachineGG1) 1> EDIT PARAMS ./GLOBALS
    Add the CHECKPOINTTABLE parameter to the existing GLOBALS file as follows:
    GGSCHEMA ggate
    CHECKPOINTTABLE ggate.checkpointtable

Guarde y cierre el archivo de parámetros globales.

###<a name="add-replicat-on-site-a"></a>Agregar REPLICAT en el sitio A

Esta sección describe cómo agregar un proceso REPLICAT "REP1" en el sitio A.

El comando siguiente crea una rep1 de grupo de Replicat con el nombre de un seguimiento y la checkpointtable asociada.

    GGSCI (MachineGG1) 21> add replicat rep1 exttrail C:\OracleGG\dirdat\ad,checkpointtable ggate.checkpointtable
     REPLICAT added.

Abra el archivo de parámetros con el comando Editar parámetros y, a continuación, agregue la información siguiente:

    GGSCI (MachineGG1) 10> edit params rep1
    REPLICAT rep1
    ASSUMETARGETDEFS
    USERID ggate, PASSWORD ggate
    DISCARDFILE C:\OracleGG\dirdat\discard.txt, append, megabytes 10
    MAP scott.inventory, TARGET scott.inventory;

### <a name="add-trandata-on-site-a-and-site-b"></a>Agregar trandata en el sitio A y b.

Habilitar el registro adicional en el nivel de tabla mediante el comando Agregar TRANDATA. Abrir una ventana de intérprete de comandos de GoldenGate de Oracle, inicie sesión en la base de datos y, a continuación, ejecute el comando de agregar TRANDATA.

Escritorio remoto para MachineGG1, abra el intérprete de comandos de Oracle GoldenGate y ejecutar:

    GGSCI (MachineGG1) 11> dblogin userid ggate password ggate
     Successfully logged into database.
    GGSCI (MachineGG1) 12> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
    GGSCI (MachineGG1) 13> info trandata scott.inventory
    Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
    Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

Escritorio remoto para MachineGG2, abra el intérprete de comandos de Oracle GoldenGate y ejecutar:

    GGSCI (MachineGG2) 18> dblogin userid ggate password ggate
     Successfully logged into database.
    GGSCI (MachineGG2) 14> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
    Logging of supplemental redo data enabled for table SCOTT.INVENTORY.

Mostrar información sobre el estado de nivel de tabla complementaria de registro:

    GGSCI (MachineGG2) 15> info trandata scott.inventory
    Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
    Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

###<a name="add-trandata-on-site-a-and-site-b"></a>Agregar trandata en el sitio A y b.

Habilitar el registro adicional en el nivel de tabla mediante el comando Agregar TRANDATA. Abrir una ventana de intérprete de comandos de GoldenGate de Oracle, inicie sesión en la base de datos y, a continuación, ejecute el comando de agregar TRANDATA.

Escritorio remoto para MachineGG1, abra el intérprete de comandos de Oracle GoldenGate y ejecutar:

    GGSCI (MachineGG1) 11> dblogin userid ggate password ggate
     Successfully logged into database.
    GGSCI (MachineGG1) 12> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
    GGSCI (MachineGG1) 13> info trandata scott.inventory
    Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
    Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

Escritorio remoto para MachineGG2, abra el intérprete de comandos de Oracle GoldenGate y ejecutar:

    GGSCI (MachineGG2) 18> dblogin userid ggate password ggate
     Successfully logged into database.
    GGSCI (MachineGG2) 14> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
    Logging of supplemental redo data enabled for table SCOTT.INVENTORY.
    Display information about the state of table-level supplemental logging:
    GGSCI (MachineGG2) 15> info trandata scott.inventory
    Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
    Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

###<a name="start-extract-and-data-pump-processes-on-site-a"></a>Iniciar procesos extraer y bomba de datos en el sitio A

Iniciar la ext1 proceso extraer en sitio A:

    GGSCI (MachineGG1) 31> start extract ext1
    Sending START request to MANAGER …
    EXTRACT EXT1 starting

Iniciar el dpump1 de proceso de bomba de datos en el sitio A:

    GGSCI (MachineGG1) 23> start extract dpump1
    Sending START request to MANAGER …
    EXTRACT DPUMP1 starting
Mostrar información sobre la ext1 grupo extraer: GGSCI (MachineGG1) 32 > información extraer ext1 extraer EXT1 última iniciado 2013-11-25 08:03 posposición de punto de comprobación de estado EJECUTANDO 00:00:00 (actualizado 00:00:02 hace) registro lectura control Oracle Rehacer registros 2013-11-25 08:03:18 Seqno no 6, RBA 3230720 Pant 0.1074371 (1074371)

Mostrar el estado y de posposición (si procede) para todos los procesos de administrador, extraer y Replicat en un sistema:

    GGSCI (MachineGG1) 16> info all
    Program     Status      Group       Lag at Chkpt  Time Since Chkpt

    MANAGER     RUNNING
    EXTRACT     RUNNING     DPUMP1      00:00:00      00:46:33
    EXTRACT     RUNNING     EXT1        00:00:00      00:00:04

###<a name="start-extract-and-data-pump-processes-on-site-b"></a>Iniciar procesos extraer y bomba de datos en el sitio B

Iniciar la ext2 proceso extraer en sitio B:

    GGSCI (MachineGG2) 22> start extract ext2
    Sending START request to MANAGER …
    EXTRACT EXT2 starting

Iniciar el dpump2 de proceso de bomba de datos en el sitio B:

    GGSCI (MachineGG2) 23> start extract dpump2
    Sending START request to MANAGER …
    EXTRACT DPUMP2 starting

Mostrar el estado y de posposición (si procede) para todos los procesos de administrador, extraer y Replicat en un sistema:

    GGSCI (ActiveGG2orcldb) 6> info all
    Program     Status      Group       Lag at Chkpt  Time Since Chkpt

    MANAGER     RUNNING
    EXTRACT     RUNNING     DPUMP2      00:00:00      136:13:33
    EXTRACT     RUNNING     EXT2        00:00:00      00:00:04

### <a name="start-replicat-process-on-site-a"></a>Iniciar proceso de REPLICAT en el sitio A

Esta sección describe cómo iniciar el proceso REPLICAT "REP1" en el sitio A.

Iniciar el proceso de Replicat en sitio A:

    GGSCI (MachineGG1) 38> start replicat rep1
    Sending START request to MANAGER …
    REPLICAT REP1 starting

Mostrar el estado de un grupo de Replicat:

    GGSCI (MachineGG1) 39> status replicat rep1
     REPLICAT REP1: RUNNING

###<a name="start-replicat-process-on-site-b"></a>Iniciar proceso de REPLICAT en el sitio B

Esta sección describe cómo iniciar el proceso REPLICAT "REP2" en el sitio B.

Iniciar el proceso de Replicat en sitio B:

    GGSCI (MachineGG2) 26> start replicat rep2
    Sending START request to MANAGER …
    REPLICAT REP2 starting

Mostrar el estado de un grupo de Replicat:

    GGSCI (MachineGG2) 27> status replicat rep2
    REPLICAT REP2: RUNNING

##<a name="6-verify-the-bi-directional-replication-process"></a>6. Compruebe el proceso de replicación bidireccional

Para comprobar la configuración de Oracle GoldenGate, insertar una fila en la base de datos de escritorio remoto de sitio a sitio a abrir el SQL * Plus ventana de comandos y ejecutar: SQL > seleccione nombre de base de datos de $ v;

    NAME
    ———
    TESTGG

    SQL> insert into inventory values  (100,’TV’,100,sysdate);

    1 row created.

    SQL> commit;

    Commit complete.

A continuación, compruebe si esa fila se duplica en el sitio B. Para ello, escritorio remoto sitio B. Abrir arriba ventana SQL Plus y ejecutar:

    SQL> select name from v$database;

    NAME
    ———
    TESTGG

    SQL> select * from inventory;

    PROD_ID PROD_CATEGORY QTY_IN_STOCK LAST_DML
    ———- ——————– ———— ———
    100 TV 100 22-MAR-13

Insertar un nuevo registro al sitio B:

    SQL> insert into inventory  values  (101,’DVD’,10,sysdate);
    1 row created.

    SQL> commit;

    Commit complete.

Escritorio remoto al sitio y compruebe si ha realizado la replicación:

    SQL> select * from inventory;

    PROD_ID PROD_CATEGORY QTY_IN_STOCK LAST_DML
    ———- ——————– ———— ———
    100 TV 100 22-MAR-13
    101 DVD 10 22-MAR-13

##<a name="additional-resources"></a>Recursos adicionales
[Imágenes de máquina Virtual de Oracle para Azure](virtual-machines-linux-classic-oracle-images.md)

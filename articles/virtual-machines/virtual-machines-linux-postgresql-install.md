<properties
    pageTitle="Configurar PostgreSQL en una VM Linux | Microsoft Azure"
    description="Obtenga información sobre cómo instalar y configurar PostgreSQL en una máquina virtual de Linux en Azure"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="SuperScottz"
    manager="timlt"
    editor=""
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure-services"
    ms.date="02/01/2016"
    ms.author="mingzhan"/>


# <a name="install-and-configure-postgresql-on-azure"></a>Instalar y configurar PostgreSQL en Azure

PostgreSQL es una base de datos de código abierto avanzada similar a Oracle y DB2. Incluye características para la empresa como cumplimiento completo ACID, procesamiento de transacciones confiable y control de simultaneidad varias versiones. También es compatible con estándares como ANSI SQL y SQL/MED (incluidos los contenedores de datos externa para Oracle, MySQL, MongoDB y muchas otras). Es muy extensible con compatibilidad con idiomas procedimientos más de 12, índices GINEBRA y general, la compatibilidad con datos espacial y varias características como NoSQL para JSON o aplicaciones basadas en el valor de clave.

En este artículo, aprenderá cómo instalar y configurar PostgreSQL en una máquina virtual Azure con Linux.


[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


## <a name="install-postgresql"></a>Instalar PostgreSQL

> [AZURE.NOTE] Debe haber una máquina virtual Azure ejecutan Linux para poder completar este tutorial. Para crear y configurar una VM Linux antes de continuar, consulte el [tutorial de Azure VM de Linux](virtual-machines-linux-quick-create-cli.md).

En este caso, use el puerto 1999 como el puerto PostgreSQL.  

Conectarse a la máquina virtual creado a través de PuTTY Linux. Si es la primera vez que usa una máquina virtual Linux de Azure, consulte [cómo usar SSH con Linux en Azure](virtual-machines-linux-mac-create-ssh-keys.md) para aprender a usar PuTTY para conectarse a una VM Linux.

1. Ejecute el comando siguiente para pasar a la raíz (Administrador):

        # sudo su -

2. Algunas distribuciones tienen dependencias que debe instalar antes de instalar PostgreSQL. Compruebe si su distro de esta lista y ejecute el comando adecuado:

    - Rojo sombrero Linux base:

            # yum install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  

    - Debian Linux base:

            # apt-get install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam libxslt-devel tcl-devel python-devel -y  

    - SUSE Linux:

            # zypper install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  

3. Descargar PostgreSQL en el directorio raíz y, a continuación, descomprima el paquete:

        # wget https://ftp.postgresql.org/pub/source/v9.3.5/postgresql-9.3.5.tar.bz2 -P /root/

        # tar jxvf  postgresql-9.3.5.tar.bz2

    El ejemplo anterior es un ejemplo. Puede encontrar la dirección de descarga más detallada en el [índice de/pub/origen /](https://ftp.postgresql.org/pub/source/).

4. Para iniciar la compilación, ejecute estos comandos:

        # cd postgresql-9.3.5

        # ./configure --prefix=/opt/postgresql-9.3.5

5. Si desea todo lo que puede generar, incluida la documentación (páginas HTML y hombre) y módulos adicionales (hogar), crear ejecute el comando siguiente en su lugar:

        # gmake install-world

    Debe aparecer el siguiente mensaje de confirmación:

        PostgreSQL, contrib, and documentation successfully made. Ready to install.

## <a name="configure-postgresql"></a>Configurar PostgreSQL

1. (Opcional) Crear un vínculo simbólico para acortar la referencia de PostgreSQL para no incluir el número de versión:

        # ln -s /opt/pgsql9.3.5 /opt/pgsql

2. Cree un directorio para la base de datos:

        # mkdir -p /opt/pgsql_data

3. Crear un usuario no raíz y modificar el perfil del usuario. A continuación, cambie a este nuevo usuario (denominado *postgres* en nuestro ejemplo):

        # useradd postgres

        # chown -R postgres.postgres /opt/pgsql_data

        # su - postgres

   > [AZURE.NOTE] Por motivos de seguridad, PostgreSQL usa un usuario no raíz para inicializar, iniciar o cerrar la base de datos.


4. Editar el archivo *bash_profile* introduciendo los comandos siguientes. Estas líneas se agregará al final del archivo *bash_profile* :

        cat >> ~/.bash_profile <<EOF
        export PGPORT=1999
        export PGDATA=/opt/pgsql_data
        export LANG=en_US.utf8
        export PGHOME=/opt/pgsql
        export PATH=\$PATH:\$PGHOME/bin
        export MANPATH=\$MANPATH:\$PGHOME/share/man
        export DATA=`date +"%Y%m%d%H%M"`
        export PGUSER=postgres
        alias rm='rm -i'
        alias ll='ls -lh'
        EOF

5. Ejecute el archivo *bash_profile* :

        $ source .bash_profile

6. Valide la instalación mediante el siguiente comando:

        $ which psql

    Si la instalación se realiza correctamente, verá la respuesta siguiente:

        /opt/pgsql/bin/psql

7. También puede comprobar la versión de PostgreSQL:

        $ psql -V

8. Inicializar la base de datos:

        $ initdb -D $PGDATA -E UTF8 --locale=C -U postgres -W

    Recibirá el siguiente resultado:

![imagen](./media/virtual-machines-linux-postgresql-install/no1.png)

## <a name="set-up-postgresql"></a>Configurar PostgreSQL

<!--    [postgres@ test ~]$ exit -->

Ejecute los comandos siguientes:

    # cd /root/postgresql-9.3.5/contrib/start-scripts

    # cp linux /etc/init.d/postgresql

Modificar dos variables en el archivo /etc/init.d/postgresql. El prefijo se establece en la ruta de instalación de PostgreSQL: **/opt/pgsql**. PGDATA se establece en la ruta de almacenamiento de datos de PostgreSQL: **/opt/pgsql_data**.

    # sed -i '32s#usr/local#opt#' /etc/init.d/postgresql

    # sed -i '35s#usr/local/pgsql/data#opt/pgsql_data#' /etc/init.d/postgresql

![imagen](./media/virtual-machines-linux-postgresql-install/no2.png)

Cambiar el archivo para que sea ejecutable:

    # chmod +x /etc/init.d/postgresql

Iniciar PostgreSQL:

    # /etc/init.d/postgresql start

Compruebe si el extremo de PostgreSQL es en:

    # netstat -tunlp|grep 1999

Verá el resultado siguiente:

![imagen](./media/virtual-machines-linux-postgresql-install/no3.png)

## <a name="connect-to-the-postgres-database"></a>Conectarse a la base de datos de Postgres

Cambie de nuevo al usuario postgres:

    # su - postgres

Crear una base de datos de Postgres:

    $ createdb events

Conectarse a la base de datos de eventos que acaba de crear:

    $ psql -d events

## <a name="create-and-delete-a-postgres-table"></a>Crear y eliminar una tabla Postgres

Ahora que ha conectado a la base de datos, puede crear tablas en él.

Por ejemplo, crear una nueva tabla Postgres de ejemplo mediante el siguiente comando:

    CREATE TABLE potluck (name VARCHAR(20), food VARCHAR(30),   confirmed CHAR(1), signup_date DATE);

Ahora ha configurado una tabla de cuatro columnas con los siguientes nombres de columna y restricciones:

1. La columna "nombre" está limitada por el comando VARCHAR ser inferior a 20 caracteres de longitud.
2. La columna "alimentos" indica el elemento de comida que cada persona. VARCHAR limita este texto para menos de 30 caracteres.
3. La columna "confirmada" registros si ha RSVP'd la persona a la Fiesta americana. Los valores aceptables son "Y" y "N".
4. La columna "fecha" se muestra cuando se registraron para el evento. Postgres requiere que se escriban fechas como aaaa-mm-dd.

Si la tabla se ha creado correctamente verá lo siguiente:

![imagen](./media/virtual-machines-linux-postgresql-install/no4.png)

También puede comprobar la estructura de tabla mediante el comando siguiente:

![imagen](./media/virtual-machines-linux-postgresql-install/no5.png)

### <a name="add-data-to-a-table"></a>Agregar datos a una tabla

En primer lugar, insertar información en una fila:

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('John', 'Casserole', 'Y', '2012-04-11');

Verá este resultado:

![imagen](./media/virtual-machines-linux-postgresql-install/no6.png)

Puede agregar un par más personas a la tabla. Estas son algunas opciones o puede crear su propio:

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Sandy', 'Key Lime Tarts', 'N', '2012-04-14');

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES ('Tom', 'BBQ','Y', '2012-04-18');

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Tina', 'Salad', 'Y', '2012-04-18');

### <a name="show-tables"></a>Mostrar tablas

Use el comando siguiente para mostrar una tabla:

    select * from potluck;

El resultado es:

![imagen](./media/virtual-machines-linux-postgresql-install/no7.png)

### <a name="delete-data-in-a-table"></a>Eliminar datos de una tabla

Use el comando siguiente para eliminar datos de una tabla:

    delete from potluck where name=’John’;

Esto elimina toda la información de la fila de "Juan". El resultado es:

![imagen](./media/virtual-machines-linux-postgresql-install/no8.png)

### <a name="update-data-in-a-table"></a>Actualizar datos en una tabla

Use el comando siguiente para actualizar datos en una tabla. Para este uno Sandy ha confirmado que asiste, así que cambiarán su PENSADO de "N" para "Y":

    UPDATE potluck set confirmed = 'Y' WHERE name = 'Sandy';


##<a name="get-more-information-about-postgresql"></a>Obtener más información acerca de PostgreSQL
Ahora que ha finalizado la instalación de PostgreSQL en una máquina virtual Linux de Azure, podrá disfrutar de uso en Azure. Para obtener más información acerca de PostgreSQL, visite el [sitio Web de PostgreSQL](http://www.postgresql.org/).

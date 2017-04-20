<properties
   pageTitle="Utilice las teclas SSH con Hadoop basados en Linux de Linux, Unix y OS X | Microsoft Azure"
   description=" Puede tener acceso a HDInsight basados en Linux mediante Secure Shell (SSH). Este documento proporciona información sobre cómo utilizar SSH con HDInsight de los clientes de OS X, Unix o Linux."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/13/2016"
   ms.author="larryfr"/>

#<a name="use-ssh-with-linux-based-hadoop-on-hdinsight-from-linux-unix-or-os-x"></a>Utilizar SSH con Hadoop basados en Linux en HDInsight de Linux, Unix y OS X

> [AZURE.SELECTOR]
- [Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
- [Linux, Unix y OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

[Secure Shell (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) le permite realizar operaciones en los clústeres basados en Linux HDInsight mediante una interfaz de línea de comandos de forma remota. Este documento proporciona información sobre cómo utilizar SSH con HDInsight de los clientes de OS X, Unix o Linux.

> [AZURE.NOTE] Los pasos de este artículo suponen que está utilizando a un cliente Linux, Unix y OS X. Estos pasos se pueden realizar en un cliente basado en Windows si tiene instalado un paquete que proporciona `ssh` y `ssh-keygen`, como [Bash en Ubuntu en Windows](https://msdn.microsoft.com/commandline/wsl/about).
>
> Si no tiene instalada en el cliente basado en Windows SSH, utilice los pasos en [Utilizar SSH con HDInsight basados en Linux (Hadoop) de Windows](hdinsight-hadoop-linux-use-ssh-windows.md) para obtener información sobre la instalación y el uso de PuTTY.

##<a name="prerequisites"></a>Requisitos previos

* **SSH-keygen** y **ssh** para clientes Linux, Unix y OS X. Utilidades de este normalmente se proporcionan con el sistema operativo, o disponibles a través del sistema de gestión de paquete.

* Un explorador de web moderna compatible con HTML5.

OR

* [CLI de azure](../xplat-cli-install.md).

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)] 

##<a name="what-is-ssh"></a>¿Qué es SSH?

SSH es una utilidad para iniciar sesión y ejecutar remotamente, comandos en un servidor remoto. Con HDInsight basados en Linux, SSH establece una conexión cifrada con el headnode de clúster y proporciona una línea de comandos que se utiliza para escribir en los comandos. A continuación, se ejecutan los comandos directamente en el servidor.

###<a name="ssh-user-name"></a>Nombre de usuario SSH

Un nombre de usuario SSH es el nombre que utiliza para autenticarse en el clúster HDInsight. Cuando se especifica un nombre de usuario SSH durante la creación del clúster, este usuario se crea en todos los nodos del clúster. Una vez creado el clúster, puede utilizar este nombre de usuario para conectarse a la headnodes de clúster HDInsight. Desde el headnodes, puede conectarse a los nodos de trabajo individual.

###<a name="ssh-password-or-public-key"></a>Contraseña SSH o clave pública

Un usuario SSH puede utilizar una contraseña o una clave pública para la autenticación. Una contraseña es simplemente una cadena de texto, mientras que una clave pública es parte de un par de claves criptográficas que se genera para identificarle exclusivamente.

Una clave es más segura que una contraseña, pero requiere pasos adicionales para generar la clave y debe mantener los archivos que contienen la clave en una ubicación segura. Si alguien obtiene acceso a los archivos clave, ellos tener acceso a su cuenta. O bien, si pierde los archivos de claves, no podrá iniciar sesión en su cuenta.

Un par de claves consta de una clave pública (que se envía al servidor de HDInsight) y una clave privada (que se mantiene en el equipo cliente.) Cuando se conecta al servidor HDInsight mediante SSH, el cliente SSH utilizará la clave privada en el equipo para autenticar con el servidor.

##<a name="create-an-ssh-key"></a>Crear una clave SSH

Utilice la siguiente información si va a utilizar claves SSH con el clúster. Si desea utilizar una contraseña, puede omitir esta sección.

1. Abra una sesión de terminal y utilice el siguiente comando para ver si tienen las claves SSH existentes:

        ls -al ~/.ssh

    Busque los siguientes archivos en la lista del directorio. Estos son nombres comunes para las claves públicas de SSH.

    * Id. de\_dsa.pub
    * Id. de\_ecdsa.pub
    * Id. de\_ed25519.pub
    * Id. de\_rsa.pub

2. Si no desea usar un archivo existente o no tiene ningún claves SSH existentes, utilice lo siguiente para generar un nuevo archivo:

        ssh-keygen -t rsa

    Se le pedirá la siguiente información:

    * La ubicación predeterminada a la ubicación del archivo - a ~/.ssh/id\_rsa.
    * Una frase de contraseña - le pedirá que vuelva a escribir esto.

        > [AZURE.NOTE] Se recomienda encarecidamente que utilice una contraseña segura para la clave. Sin embargo, si olvida la contraseña, no hay ninguna manera de recuperarla.

    Cuando finalice el comando, tendrá dos nuevos archivos, la clave privada (por ejemplo, **id de\_rsa**) y la clave pública (por ejemplo, **id de\_rsa.pub**).

##<a name="create-a-linux-based-hdinsight-cluster"></a>Crear un clúster basado en Linux HDInsight

Al crear un clúster basado en Linux, HDInsight, debe proporcionar la clave pública que se creó anteriormente. Desde clientes Linux, Unix y OS X, hay dos maneras de crear un clúster de HDInsight:

* **Portal de azure** - utiliza un portal basado en web para crear el clúster.

* **CLI de azure para Mac, Linux y Windows** - utiliza comandos de línea de comandos para crear el clúster.

Cada uno de estos métodos se requerirá una contraseña o una clave pública. Para obtener información sobre cómo crear un clúster basado en Linux, HDInsight, consulte [clústeres basados en Linux de provisión HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

###<a name="azure-portal"></a>Portal de Azure

Al utilizar el [Portal de Azure] [ preview-portal] para crear un clúster basado en Linux, HDInsight, debe escribir un **Nombre de usuario SSH**y seleccione esta opción para escribir una **contraseña** o **Clave pública SSH**.

Si selecciona **Clave pública SSH**, puede pegar la clave pública (contenida en el archivo con la extensión **. pub** ) en el campo de __Clave pública SSH__ o seleccione __Seleccionar un archivo__ para buscar y seleccionar el archivo de clave pública.

![Imagen de formulario que pide la clave pública](./media/hdinsight-hadoop-linux-use-ssh-unix/ssh-key.png)

> [AZURE.NOTE] El archivo de clave es simplemente un archivo de texto. El contenido debería parecerse al siguiente:
> ```
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCelfkjrpYHYiks4TM+r1LVsTYQ4jAXXGeOAF9Vv/KGz90pgMk3VRJk4PEUSELfXKxP3NtsVwLVPN1l09utI/tKHQ6WL3qy89WVVVLiwzL7tfJ2B08Gmcw8mC/YoieT/YG+4I4oAgPEmim+6/F9S0lU2I2CuFBX9JzauX8n1Y9kWzTARST+ERx2hysyA5ObLv97Xe4C2CQvGE01LGAXkw2ffP9vI+emUM+VeYrf0q3w/b1o/COKbFVZ2IpEcJ8G2SLlNsHWXofWhOKQRi64TMxT7LLoohD61q2aWNKdaE4oQdiuo8TGnt4zWLEPjzjIYIEIZGk00HiQD+KCB5pxoVtp user@system
> ```

Esto crea un inicio de sesión para el usuario especificado, utilizando la contraseña o la clave pública que proporcionan.

###<a name="azure-command-line-interface-for-mac-linux-and-windows"></a>Interfaz de línea de comandos de Azure para Mac, Linux y Windows

Puede utilizar la [CLI de Azure para Mac, Linux y Windows](../xplat-cli-install.md) para crear un nuevo clúster mediante el `azure hdinsight cluster create` comando.

Para obtener más información acerca de cómo utilizar este comando, consulte [clústeres de Hadoop Linux de provisión en HDInsight mediante opciones personalizadas](hdinsight-hadoop-provision-linux-clusters.md).

##<a name="connect-to-a-linux-based-hdinsight-cluster"></a>Conectar con un clúster basado en Linux HDInsight

Desde una sesión de terminal, utilice el comando SSH para conectarse a la headnode de clúster proporcionando la dirección y nombre de usuario:

* **Dirección SSH** - hay dos direcciones que pueden utilizarse para conectar con un clúster mediante SSH:

    * **Conectar a la headnode**: el nombre del clúster, seguido de **-ssh.azurehdinsight.net**. Por ejemplo, **ssh.azurehdinsight.net de miClúster**.
    
    * **Conectar con el nodo del borde**: si el clúster es servidor R en HDInsight, el clúster también contendrá un nodo del borde que se puede tener acceso mediante **RServer.CLUSTERNAME.ssh.azurehdinsight.net**, donde __NOMBREDECLÚSTER__ es el nombre del clúster.

* **Nombre de usuario** - SSH el nombre de usuario que proporcionó al crear el clúster.

En el ejemplo siguiente, se conectará con el headnode principal de **miClúster** usuario **me**:

    ssh me@mycluster-ssh.azurehdinsight.net

Si utiliza una contraseña para la cuenta de usuario, se le pedirá que escriba la contraseña.

Si utiliza una clave SSH que está protegida con una contraseña, se le pedirá que lo especifique la frase de contraseña. De lo contrario, SSH intentará autenticar automáticamente mediante una de las claves privadas locales en el cliente.

> [AZURE.NOTE] Si SSH no autenticará automáticamente con la clave privada correcta, utilice el parámetro **-i** y especifique la ruta de acceso a la clave privada. En el ejemplo siguiente, se cargará la clave privada de `~/.ssh/id_rsa`:
>
> `ssh -i ~/.ssh/id_rsa me@mycluster-ssh.azurehdinsight.net`

Si se está conectando a utilizando la dirección de la headnode y no se especifica ningún puerto, se predefinirá SSH al puerto 22, que se conectará a la headnode principal en el clúster HDInsight. Si utiliza el puerto 23, se conectará a la secundaria. Para obtener más información sobre la headnodes, vea la [disponibilidad y confiabilidad de los clústeres de Hadoop en HDInsight](hdinsight-high-availability-linux.md).

###<a name="connect-to-worker-nodes"></a>Conectarse a nodos de trabajador

Los nodos de trabajo no son accesibles directamente desde fuera del centro de datos de Azure, pero se puede acceder desde el headnode del clúster a través de SSH.

Si utiliza una clave SSH para autenticar su cuenta de usuario, debe completar los siguientes pasos en el cliente:

1. Con un editor de texto, abra `~/.ssh/config`. Si este archivo no existe, puede crear escribiendo `touch ~/.ssh/config` en el terminal.

2. Agregue lo siguiente al archivo. Reemplazar *NOMBREDECLÚSTER* con el nombre del clúster HDInsight.

        Host CLUSTERNAME-ssh.azurehdinsight.net
          ForwardAgent yes

    Esto configura el reenvío para el clúster de HDInsight de agente SSH.

3. Agente de SSH reenvío mediante el comando siguiente desde el terminal de prueba:

        echo "$SSH_AUTH_SOCK"

    Esto debería devolver información similar al siguiente:

        /tmp/ssh-rfSUL1ldCldQ/agent.1792

    Si no se devuelve nada, esto indica que el **ssh-agent** no se está ejecutando. Consulte la documentación del sistema operativo para obtener pasos específicos sobre la instalación y configuración de **ssh-agent**o vea [utilizar ssh-agent con ssh](http://mah.everybody.org/docs/ssh).

4. Una vez que haya comprobado que **ssh-agent** se está ejecutando, utilice lo siguiente para agregar la clave privada de SSH al agente:

        ssh-add ~/.ssh/id_rsa

    Si la clave privada se almacena en un archivo diferente, reemplace `~/.ssh/id_rsa` con la ruta de acceso al archivo.

Utilice los pasos siguientes para conectarse a los nodos de trabajador para el clúster.

> [AZURE.IMPORTANT] Si utiliza una clave SSH para autenticar su cuenta, debe completar los pasos anteriores para comprobar que el agente de reenvío está funcionando.

1. Conectarse al clúster HDInsight mediante SSH como se describió anteriormente.

2. Una vez que esté conectado, utilice lo siguiente para recuperar una lista de los nodos del clúster. Reemplazar *ADMINPASSWORD* con la contraseña de su cuenta de administrador de clústeres. Reemplazar *NOMBREDECLÚSTER* con el nombre del clúster.

        curl --user admin:ADMINPASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/hosts

    Esto devolverá información en formato JSON para los nodos del clúster, incluyendo `host_name`, que contiene el nombre de dominio completo (FQDN) para cada nodo. El siguiente es un ejemplo de un `host_name` entrada devuelta por el comando **curl** :

        "host_name" : "workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net"

3. Una vez que tenga una lista de los nodos de trabajo que desea conectarse, utilice el comando siguiente desde la sesión SSH al servidor para abrir una conexión a un nodo de trabajo:

        ssh USERNAME@FQDN

    Reemplazar el *nombre de usuario* con su nombre de usuario SSH y *FQDN* con el FQDN del nodo del trabajador. Por ejemplo, `workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net`.

    > [AZURE.NOTE] Si utiliza una contraseña para la autenticación de la sesión SSH, se le pedirá que escriba la contraseña otra vez. Si utiliza una clave SSH, debe finalizar la conexión sin preguntas.

4. Una vez establecida la sesión, el símbolo del sistema terminal cambiará de `username@hn#-clustername` a `username@wk#-clustername` para indicar que está conectado al nodo de trabajo. Los comandos que se ejecuta en este momento se ejecutarán en el nodo de trabajo.

4. Una vez que haya terminado de realizar acciones en el nodo de trabajo, utilice el `exit` comando para cerrar la sesión en el nodo de trabajo. Esto le devolverá a la `username@hn#-clustername` símbolo del sistema.

## <a name="connect-to-a-domain-joined-hdinsight-cluster"></a>Conectar con un clúster HDInsight Unidos a un dominio

[HDInsight Unidos a un dominio](hdinsight-domain-joined-introduction.md) Kerberos se integra con Hadoop en HDInsight. Porque el usuario SSH no es un usuario de dominio de Active Directory, esta cuenta de usuario no puede ejecutar comandos de Hadoop de shell SSH en un clúster Unidos a un dominio directamente. En primer lugar debe ejecutar *kinit* . 

**Para ejecutar la sección consultas en un clúster de HDInsight Unidos a un dominio mediante SSH**

1. Conectar con un clúster de HDInsight Unidos a un dominio mediante SSH.  Para instrocutions, vea [conectarse a un clúster de HDInsight basados en Linux](#connect-to-a-linux-based-hdinsight-cluster).
2. Ejecutar kinit. Le pedirá un nombre de usuario de dominio y la contraseña de usuario de dominio. Para obtener más información sobre configurar clústeres de HDInsight Unidos a un dominio de los usuarios del dominio, consulte [Configurar dominio-une HDInisight de clústeres](hdinsight-domain-joined-configure.md).

    ![Kinit Unidos a un dominio de Hadoop de HDInsight](./media/hdinsight-hadoop-linux-use-ssh-unix/hdinsight-domain-joined-hadoop-kinit.png)
3. Abra la sección consola, escriba:

        hive

    A continuación, puede ejecutar comandos de subárbol.

##<a name="add-more-accounts"></a>Agregar más cuentas

1. Generar una nueva clave pública y clave privada para la nueva cuenta de usuario, como se describe en la sección [crear una clave SSH](#create-an-ssh-key-optional) .

    > [AZURE.NOTE] La clave privada o bien debe generarse en un cliente que el usuario va a utilizar para conectarse al clúster o segura se transfiere al cliente después de la creación.

1. Desde una sesión SSH al clúster, agregue el nuevo usuario con el siguiente comando:

        sudo adduser --disabled-password <username>

    Esto creará una nueva cuenta de usuario, pero deshabilita la autenticación de contraseña.

2. Cree el directorio y los archivos que contenga la clave mediante los comandos siguientes:

        sudo mkdir -p /home/<username>/.ssh
        sudo touch /home/<username>/.ssh/authorized_keys
        sudo nano /home/<username>/.ssh/authorized_keys

3. Cuando se abre el editor nano, copie y pegue el contenido de la clave pública para la nueva cuenta de usuario. Finalmente, utilice **Ctrl-X** para guardar el archivo y salga del editor.

    ![imagen de editor nano con clave de ejemplo](./media/hdinsight-hadoop-linux-use-ssh-unix/nano.png)

4. Utilice el comando siguiente para cambiar la propiedad de la carpeta .ssh y el contenido a la nueva cuenta de usuario:

        sudo chown -hR <username>:<username> /home/<username>/.ssh

5. Ahora debería ser capaz de autenticarse en el servidor con la nueva cuenta de usuario y la clave privada.

##<a id="tunnel"></a>Túnel de SSH

SSH puede utilizarse para las solicitudes locales, como las solicitudes web, al clúster HDInsight del túnel. A continuación, se enrutarán la solicitud al recurso solicitado como si lo hubiera enviado en el headnode de clúster HDInsight.

> [AZURE.IMPORTANT] Un túnel SSH es un requisito para tener acceso a la interfaz de usuario web para algunos servicios de Hadoop. Por ejemplo, la interfaz de usuario de historial de trabajo o la interfaz de usuario del Administrador de recursos puede sólo tener acceso mediante un túnel SSH.

Para obtener más información sobre cómo crear y utilizar un túnel SSH, consulte [Utilizar SSH túnel para obtener acceso a la interfaz de usuario web de Ambari, ResourceManager, JobHistory, NameNode, Oozie y otra de la interfaz de usuario web](hdinsight-linux-ambari-ssh-tunnel.md).

##<a name="next-steps"></a>Próximos pasos

Ahora que entiende cómo se autentica mediante claves SSH, aprenda a usar MapReduce con Hadoop en HDInsight.

* [Sección de uso con HDInsight](hdinsight-use-hive.md)

* [Utilice cerdo con HDInsight](hdinsight-use-pig.md)

* [Utilizar trabajos MapReduce con HDInsight](hdinsight-use-mapreduce.md)

[preview-portal]: https://portal.azure.com/

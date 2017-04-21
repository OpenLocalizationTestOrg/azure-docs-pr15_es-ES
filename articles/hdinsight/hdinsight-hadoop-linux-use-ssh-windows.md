<properties
   pageTitle="Utilice las teclas SSH con Hadoop en clústeres basados en Linux desde Windows | Microsoft Azure"
   description="Aprenda a crear y usar claves SSH para autenticar clústeres HDInsight basados en Linux. Conectar clústeres desde clientes basados en Windows mediante el cliente PuTTY SSH."
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
   ms.date="08/30/2016"
   ms.author="larryfr"/>

#<a name="use-ssh-with-linux-based-hadoop-on-hdinsight-from-windows"></a>Utilizar SSH con basados en Linux Hadoop en HDInsight de Windows.

> [AZURE.SELECTOR]
- [Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
- [Linux, Unix y OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

[Shell seguro (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) le permite realizar operaciones en los clústeres basados en Linux HDInsight mediante una interfaz de línea de comandos de forma remota. Este documento proporciona información sobre cómo conectarse a HDInsight de clientes de Windows mediante el cliente PuTTY SSH.

> [AZURE.NOTE] Los pasos de este artículo se suponen que está usando a un cliente basado en Windows. Si está utilizando a un cliente Linux, Unix o OS X, vea [Usar SSH con basados en Linux Hadoop en HDInsight de Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md).
>
> Si tiene Windows 10 y usa [Bash en Ubuntu en Windows](https://msdn.microsoft.com/commandline/wsl/about), puede usar los pasos en el documento [Utilizar SSH con basados en Linux Hadoop en HDInsight de Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md) .

##<a name="prerequisites"></a>Requisitos previos

* **PuTTY** y **PuTTYGen** para clientes basados en Windows. Estas utilidades están disponibles en [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

* Un explorador web moderna compatible con HTML5.

OR

* [CLI azure](../xplat-cli-install.md).

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)] 

##<a name="what-is-ssh"></a>¿Qué es SSH?

SSH es una utilidad para iniciar sesión y ejecución de forma remota, comandos en un servidor remoto. Con HDInsight basados en Linux, SSH establece una conexión cifrada con el nodo de clúster principal y proporciona una línea de comandos que se utiliza para escribir en los comandos. A continuación, se ejecutan comandos directamente en el servidor.

###<a name="ssh-user-name"></a>Nombre de usuario SSH

Un nombre de usuario SSH es el nombre que usa para autenticar al clúster HDInsight. Al especificar un nombre de usuario SSH durante la creación de un clúster, este usuario se crea en todos los nodos en el clúster. Una vez creado el clúster, puede usar este nombre de usuario para conectarse a los nodos de cabeza de clúster HDInsight. Desde los nodos del cabezales, puede conectarse a los nodos de trabajo individuales.

###<a name="ssh-password-or-public-key"></a>Contraseña SSH o clave pública

Un usuario SSH puede usar una contraseña o una clave pública para la autenticación. Una contraseña sólo es una cadena de texto que componen, mientras que una clave pública es parte de un par de clave de cifrado generado para identificar.

Una clave es más segura que una contraseña, pero requiere pasos adicionales para generar la clave y debe mantener los archivos que contiene la clave en una ubicación segura. Si alguien obtiene acceso a los archivos clave, obtener acceso a su cuenta. O bien, si pierde la claves archivos, no podrá iniciar sesión en su cuenta.

Un par de claves consta de una clave pública (que se envía al servidor de HDInsight) y una clave privada (que se guarda en el equipo cliente.) Cuando se conecta al servidor HDInsight mediante SSH, el cliente SSH usará la clave privada en su equipo para autenticar con el servidor.

##<a name="create-an-ssh-key"></a>Crear una clave de SSH

Utilice la siguiente información si va a usar claves SSH con el clúster. Si va a usar una contraseña, puede omitir esta sección.

1. Abra PuTTYGen.

2. **Tipo de clave para generar**, seleccione **RSA SSH 2**y, a continuación, haga clic en **Generar**.

    ![Interfaz de PuTTYGen](./media/hdinsight-hadoop-linux-use-ssh-windows/puttygen.png)

3. Desplazarse el mouse en el área situada debajo de la barra de progreso hasta que la barra se rellena. Mover el mouse genera datos aleatorios que se usan para generar la clave.

    ![desplazarse el mouse](./media/hdinsight-hadoop-linux-use-ssh-windows/movingmouse.png)

    Una vez que se ha generado la clave, se mostrará la clave pública.

4. Para una mayor seguridad, puede escribir una frase de contraseña en el campo de **clave frase de contraseña** y, a continuación, escriba el mismo valor en el campo **Confirmar frase de contraseña** .

    ![frase de contraseña](./media/hdinsight-hadoop-linux-use-ssh-windows/key.png)

    > [AZURE.NOTE] Le recomendamos encarecidamente que use una frase de contraseña segura para la clave. Sin embargo, si ha olvidado la contraseña, no hay ninguna forma de recuperarla.

5. Haga clic en **Guardar clave privada** para guardar la clave a un archivo **.ppk** . Esta clave se utilizará para autenticar al clúster HDInsight basados en Linux.

    > [AZURE.NOTE] Debe almacenar esta clave en una ubicación segura, como se puede utilizarse para tener acceso a su clúster HDInsight basados en Linux.

6. Haga clic en **Guardar clave pública** para guardar la clave como un archivo **.txt** . Esto le permite volver a utilizar la clave pública en el futuro al crear más clústeres HDInsight basados en Linux.

    > [AZURE.NOTE] La clave pública también se muestra en la parte superior de PuTTYGen. Puede haga clic en este campo, copie el valor y, a continuación, péguelo en un formulario para crear un clúster con el Portal de Azure.

##<a name="create-a-linux-based-hdinsight-cluster"></a>Crear un clúster de HDInsight basados en Linux

Al crear un clúster de HDInsight basados en Linux, debe proporcionar la clave pública que creó anteriormente. Desde los clientes basados en Windows, hay dos formas de crear un clúster de HDInsight basados en Linux:

* **Portal de azure** - utiliza un portal basado en web para crear el clúster.

* **Azure CLI para Mac, Linux y Windows** - usa comandos de línea de comandos para crear el clúster.

Cada uno de estos métodos requieren la clave pública. Para obtener información completa sobre la creación de un clúster basados en Linux HDInsight, consulte [clústeres de HDInsight basados en Linux de aprovisionamiento](hdinsight-hadoop-provision-linux-clusters.md).

###<a name="azure-portal"></a>Portal de Azure

Al usar el [Portal de Azure] [ preview-portal] para crear un clúster basados en Linux HDInsight, debe escribir un **Nombre de usuario SSH**y seleccione esta opción para introducir una **contraseña** o una **Clave pública SSH**.

Si selecciona **Clave pública SSH**, o bien puede pegar la clave pública (mostrados en la __clave pública para pegar en OpenSSH autorizado\_archivo de claves__ PuttyGen, campo) en el campo __SSH PublicKey__ o seleccione __Seleccionar un archivo__ para examinar y seleccione el archivo que contiene la clave pública.

![Formulario solicitan clave pública](./media/hdinsight-hadoop-linux-use-ssh-windows/ssh-key.png)

Esto crea un inicio de sesión para el usuario especificado y permite la autenticación de contraseña o la autenticación de clave SSH.

###<a name="azure-command-line-interface-for-mac-linux-and-windows"></a>Interfaz de línea de comandos de Azure para Mac, Linux y Windows

Puede usar [Azure CLI para Mac, Linux y Windows](../xplat-cli-install.md) para crear un nuevo clúster mediante la `azure hdinsight cluster create` comando.

Para obtener más información sobre el uso de este comando, consulte [clústeres de aprovisionamiento Hadoop Linux en HDInsight mediante opciones personalizadas](hdinsight-hadoop-provision-linux-clusters.md).

##<a name="connect-to-a-linux-based-hdinsight-cluster"></a>Conectarse a un clúster de HDInsight basados en Linux

1. Abra PuTTY.

    ![interfaz masilla](./media/hdinsight-hadoop-linux-use-ssh-windows/putty.png)

2. Si proporciona una clave SSH cuando creó su cuenta de usuario, debe realizar el paso siguiente para seleccionar la clave privada debe utilizar al autenticar al clúster:

    En **categoría**, expanda la **conexión**, expanda **SSH**y seleccione **Auth**. Por último, haga clic en **Examinar** y seleccione el archivo .ppk que contiene su clave privada.

    ![interfaz de puTTY, seleccione clave privada](./media/hdinsight-hadoop-linux-use-ssh-windows/puttykey.png)

3. En **categoría**, seleccione **sesión**. En la pantalla **Opciones básicas para su sesión masilla** , escriba la dirección SSH de su servidor de HDInsight en el campo **nombre de Host (o dirección IP)** . Existen dos posibles direcciones SSH que puede utilizar al conectarse a un clúster:

    * __Dirección de nodo de cabeza__: para conectar con el nodo principal del clúster, use el nombre de clúster, a continuación, **-ssh.azurehdinsight.net**. Por ejemplo, **miClúster ssh.azurehdinsight.net**.
    
    * __Dirección de nodo de borde__: si se está conectando a un servidor R en clúster HDInsight, puede conectarse al nodo R servidor perimetral usando la dirección __RServer.CLUSTERNAME.ssh.azurehdinsight.net__, donde NOMBREDECLÚSTER es el nombre del clúster. Por ejemplo, __RServer.mycluster.ssh.azurehdinsight.net__.

    ![interfaz masilla con ssh dirección escrita](./media/hdinsight-hadoop-linux-use-ssh-windows/puttyaddress.png)

4. Para guardar la información de conexión para usos futuros, escriba un nombre para esta conexión en **Sesiones guardado**y, a continuación, haga clic en **Guardar**. La conexión se agregará a la lista de sesiones guardadas.

5. Haga clic en **Abrir** para conectar con el clúster.

    > [AZURE.NOTE] Si esta es la primera vez que se ha conectado al clúster, recibirá una alerta de seguridad. Esto es normal. Seleccione **Sí** en la memoria caché de la clave del servidor RSA2 para continuar.

6. Cuando se le pida, escriba el usuario que especificó al crear el clúster. Si proporciona una contraseña para el usuario, se le pedirá que lo especifique también.

> [AZURE.NOTE] Los pasos anteriores se suponen que está utilizando el puerto 22, que se conectará el headnode principal en el clúster HDInsight. Si utiliza el puerto 23, se conectará a la secundaria. Para obtener más información sobre los nodos cabezales, vea [disponibilidad y confiabilidad de clústeres Hadoop HDInsight](hdinsight-high-availability-linux.md).

###<a name="connect-to-worker-nodes"></a>Conectarse a los nodos de trabajo

Los nodos de trabajo no son accesibles directamente desde fuera del centro de datos de Azure, pero se puede obtener acceso desde el nodo principal del clúster a través de SSH.

Si proporciona una clave SSH cuando creó su cuenta de usuario, debe realizar los pasos siguientes para utilizar la clave privada para autenticar al clúster si desea conectarse a los nodos de trabajo.

1. Instale concurso de [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html). Esta herramienta se utiliza en caché claves SSH para deformar.

2. Ejecutar concurso. Minimizará a un icono en la Bandeja de estado. Haga clic en el icono y seleccione **Agregar clave**.

    ![Agregar clave](./media/hdinsight-hadoop-linux-use-ssh-windows/addkey.png)

3. Cuando aparezca el cuadro de diálogo Examinar, seleccione el archivo .ppk que contiene la clave y, a continuación, haga clic en **Abrir**. La clave se agrega a concurso, que le proporcionará a PuTTY al conectar con el clúster.

    > [AZURE.IMPORTANT] Si utiliza una clave SSH para proteger su cuenta, debe completar los pasos anteriores para que pueda conectarse a los nodos de trabajo.

4. Abra PuTTY.

5. Si usa una clave SSH para autenticar, en la sección **categoría** , expanda la **conexión**, expanda **SSH**y, a continuación, seleccione **Auth**.

    En la sección **parámetros de autenticación** , habilitar **agente de permitir reenvío**. Esto permite PuTTY pasar automáticamente la autenticación de certificado a través de la conexión al nodo principal del clúster al conectarse a los nodos de trabajo.

    ![permitir a agente de reenvío](./media/hdinsight-hadoop-linux-use-ssh-windows/allowforwarding.png)

6. Conectar con el clúster tal como se describe anteriormente. Si utiliza una clave SSH para la autenticación, no es necesario seleccionar la clave: la clave SSH agregada a concurso se usará para autenticar al clúster.

7. Después de establecer la conexión, utilice el siguiente para recuperar una lista de los nodos en el clúster. Reemplazar *ADMINPASSWORD* con la contraseña de su cuenta de administrador de clúster. Reemplazar *NOMBREDECLÚSTER* con el nombre del clúster.

        curl --user admin:ADMINPASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/hosts

    Devolverá información en formato JSON para los nodos en el clúster, incluyendo `host_name`, que contiene el nombre de dominio completo (FQDN) para cada nodo. El siguiente es un ejemplo de un `host_name` entrada devuelta por el comando **doblez** :

        "host_name" : "workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net"

8. Una vez que tenga una lista de los nodos de trabajo que desea conectarse, utilice el siguiente comando de la sesión masilla para abrir una conexión a un nodo de trabajo:

        ssh USERNAME@FQDN

    Reemplace el *nombre de usuario* con el nombre de usuario SSH y *FQDN* con el FQDN para el nodo de trabajo. Por ejemplo, `workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net`.

    > [AZURE.NOTE] Si usa una contraseña para la autenticación de su sesión SSH, le pedirá que vuelva a escribir la contraseña. Si usa una clave SSH, debe finalizar la conexión sin preguntas.

9. Una vez que se ha establecido la sesión, el símbolo del sistema para su sesión masilla cambiará `username@hn#-clustername` a `username@wn#-clustername` para indicar que está conectado al nodo de trabajo. Todos los comandos que se ejecuta en ese momento se ejecutarán en el nodo de trabajo.

10. Una vez que haya terminado de realizar acciones en el nodo de trabajo, use la `exit` comando para cerrar la sesión en el nodo de trabajo. Esto le devolverá a la `username@hn#-clustername` símbolo del sistema.

##<a name="add-more-accounts"></a>Agregar más cuentas

Si necesita agregar más cuentas a su clúster, realice los pasos siguientes:

1. Generar una nueva clave pública y clave privada para la nueva cuenta de usuario, como se describió anteriormente.

2. Desde una sesión SSH al clúster, agregar el nuevo usuario con el siguiente comando:

        sudo adduser --disabled-password <username>

    Se creará una nueva cuenta de usuario, pero se deshabilita la autenticación de contraseña.

3. Cree los directorios y archivos a mantenga presionada la tecla, use los comandos siguientes:

        sudo mkdir -p /home/<username>/.ssh
        sudo touch /home/<username>/.ssh/authorized_keys
        sudo nano /home/<username>/.ssh/authorized_keys

4. Cuando se abre el editor de nano, copiar y pegar el contenido de la clave pública para la nueva cuenta de usuario. Por último, use **Ctrl-X** para guardar el archivo y salga del editor.

    ![imagen del editor de nano con clave de ejemplo](./media/hdinsight-hadoop-linux-use-ssh-windows/nano.png)

5. Use el comando siguiente para cambiar la propiedad de la carpeta .ssh y el contenido a la nueva cuenta de usuario:

        sudo chown -hR <username>:<username> /home/<username>/.ssh

6. Ahora debería poder autenticarse en el servidor con la nueva cuenta de usuario y la clave privada.

##<a id="tunnel"></a>Túnel de SSH

SSH puede usarse para peticiones locales, como las de web, en el clúster HDInsight de túnel. La solicitud, a continuación, se enruta al recurso solicitado como si hubiera se ha originado en el nodo principal del clúster de HDInsight.

> [AZURE.IMPORTANT] Un túnel SSH es necesario para obtener acceso a la interfaz de usuario web para algunos servicios Hadoop. Por ejemplo, la interfaz de usuario de historial de trabajo o la interfaz de usuario del Administrador de recursos solo se puede acceder mediante un túnel SSH.

Para obtener más información sobre cómo crear y usar un túnel SSH, consulte [Usar SSH túnel para tener acceso a la interfaz de usuario de web Ambari, ResourceManager, JobHistory, NameNode, Oozie y otra de la interfaz de usuario de web](hdinsight-linux-ambari-ssh-tunnel.md).

##<a name="next-steps"></a>Pasos siguientes

Ahora que sabe cómo autenticar mediante una clave SSH, aprenda a usar MapReduce con Hadoop en HDInsight.

* [Usar subárbol con HDInsight](hdinsight-use-hive.md)

* [Usar cerdo con HDInsight](hdinsight-use-pig.md)

* [Usar MapReduce trabajos con HDInsight](hdinsight-use-mapreduce.md)

[preview-portal]: https://portal.azure.com/

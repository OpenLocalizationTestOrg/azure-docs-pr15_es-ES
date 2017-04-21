<properties
pageTitle="Usar túnel SSH para acceder a Ambari web UI, ResourceManager, JobHistory, NameNode, Oozie y de otro web interfaz de usuario"
description="Aprenda a usar un túnel SSH para examinar recursos web hospedados en los nodos basados en Linux HDInsight de forma segura."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="jhubbard"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="10/17/2016"
ms.author="larryfr"/>

# <a name="use-ssh-tunneling-to-access-ambari-web-ui-jobhistory-namenode-oozie-and-other-web-uis"></a>Usar túnel SSH para tener acceso a la interfaz de usuario de web Ambari, JobHistory, NameNode, Oozie y de otro web interfaz de usuario

Clústeres de HDInsight basados en Linux proporcionan acceso a la interfaz de usuario de web Ambari a través de Internet, pero algunas características de la interfaz de usuario no están. Por ejemplo, la interfaz de usuario web para otros servicios que se expone a través de Ambari. Para todas las funciones de la interfaz de usuario de web Ambari, debe utilizar un túnel SSH a la cabeza de clúster.

## <a name="what-requires-an-ssh-tunnel"></a>¿Qué requiere un túnel SSH?

Algunos de los menús de Ambari no totalmente rellenará sin un túnel SSH, tal como se basan en los sitios web y servicios expuestos por los otros servicios de Hadoop que se ejecutan en el clúster. A menudo, estos sitios web no están protegidos, por lo que no es seguro directamente los exponen en internet. A veces el servicio ejecuta el sitio web en otro nodo de clúster como un nodo Zookeeper.

Los siguientes son servicios que utiliza la interfaz de usuario de web Ambari, que no se puede acceder sin un túnel SSH:

* JobHistory,
* NameNode,
* Pilas de subprocesos
* Interfaz de usuario de web Oozie
* Patrón de HBase y los registros de interfaz de usuario

Si utiliza acciones de secuencia de comandos para personalizar el clúster, todos los servicios o utilidades instalar que exponen una interfaz de usuario web requieren un túnel SSH. Por ejemplo, si instala matiz con una acción de secuencia de comandos, debe usar un túnel SSH para acceder a la interfaz de usuario de web matiz.

## <a name="what-is-an-ssh-tunnel"></a>¿Qué es un túnel SSH?

[Túnel de Shell seguro (SSH)](https://en.wikipedia.org/wiki/Tunneling_protocol#Secure_Shell_tunneling) enruta el tráfico enviado a un puerto en su estación de trabajo, mediante una conexión SSH para el nodo principal del clúster de HDInsight, donde la solicitud se resuelve como si se ha originado en el nodo principal. La respuesta a continuación, se dirige a través del túnel su estación de trabajo.

## <a name="prerequisites"></a>Requisitos previos

Cuando se usa un túnel SSH para el tráfico web, debe tener el siguiente:

* Un cliente SSH. Para las distribuciones de Linux y Unix o Macintosh OS X, el `ssh` comando se suministra con el sistema operativo. Para Windows, se recomienda [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)

    > [AZURE.NOTE] Si desea utilizar un cliente SSH distinto de `ssh` o masilla, consulte la documentación de su cliente acerca de cómo establecer un túnel SSH.

* Un explorador web que se puede configurar para utilizar a un proxy SOCKS

## <a name="usessh"></a>Crear un túnel con el comando SSH

Use el comando siguiente para crear un SSH túnel utilizando la `ssh` comando. Reemplazar el __nombre de usuario__ con un usuario SSH para el clúster HDInsight y reemplace __NOMBREDECLÚSTER__ con el nombre de su clúster HDInsight

    ssh -C2qTnNf -D 9876 USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

Esto crea una conexión que enruta el tráfico al puerto local 9876 al clúster sobre SSH. Las opciones son:

* **D 9876** - el puerto local que se usará para enrutar el tráfico a través del túnel.

* **C** - comprimir todos los datos, porque el tráfico web principalmente texto.

* **2** - forzar SSH para probar la versión 2 del protocolo.

* **preguntas** - modo silencioso.

* **T** - deshabilitar la asignación de ficticio tty, dado que nos estamos reenvío simplemente un puerto.

* **n** - evitar la lectura de STDIN, dado que nos estamos reenvío simplemente un puerto.

* **N** - no ejecutar un comando remoto, ya que nos estamos reenvío simplemente un puerto.

* **f** - ejecutar en segundo plano.

Si ha configurado el clúster con una clave SSH, debe usar la `-i` parámetro y especifique la ruta de acceso a la clave privada de SSH.

Una vez que finalice el comando, el tráfico enviado al puerto 9876 en el equipo local se enrutan sobre Secure Sockets Layer (SSL) en el clúster sede nodo y parecen provenir allí.

## <a name="useputty"></a>Crear un túnel con PuTTY

Use los siguientes pasos para crear un túnel SSH usando PuTTY.

1. Abra PuTTY y escriba la información de conexión. Si no está familiarizado con PuTTY, vea [Usar SSH con basados en Linux Hadoop en HDInsight de Windows](hdinsight-hadoop-linux-use-ssh-windows.md) para obtener información sobre cómo usar con HDInsight.

2. En la sección **categoría** a la izquierda del cuadro de diálogo, expanda la **conexión**, expanda **SSH**y, a continuación, seleccione **túneles**.

3. En el formulario de **Opciones para controlar el reenvío de puerto SSH** , complete la información siguiente:

    * **Puerto de origen** - el puerto en el cliente que desea reenviar. Por ejemplo, **9876**.

    * **Destino** : SSH la dirección para el clúster HDInsight basados en Linux. Por ejemplo, **miClúster ssh.azurehdinsight.net**.

    * **Dinámico** - proxy SOCKS dinámicas permite enrutamiento.

    ![imagen de túnel opciones](./media/hdinsight-linux-ambari-ssh-tunnel/puttytunnel.png)

4. Haga clic en **Agregar** para agregar la configuración y, a continuación, haga clic en **Abrir** para abrir una conexión SSH.

5. Cuando se le solicita, inicie sesión en el servidor. Se establezca una sesión SSH y habilitar el túnel.

## <a name="use-the-tunnel-from-your-browser"></a>Usar el túnel desde el explorador

> [AZURE.NOTE] Los pasos descritos en esta sección utilizan el Explorador de FireFox, como está disponible de forma gratuita para sistemas Linux, Unix, Macintosh OS X y Windows. Otros exploradores modernos que admiten el uso de un proxy SOCKS funcionará también.

1. Configurar el explorador para usar **localhost:9876** como un proxy **SOCKS v5** . Aquí tiene el aspecto de la configuración de Firefox. Si utiliza un puerto diferente 9876, cambie el puerto a la que se ha usado:

    ![imagen de la configuración de Firefox](./media/hdinsight-linux-ambari-ssh-tunnel/socks.png)

    > [AZURE.NOTE] Seleccionar **Remoto DNS** resolverá solicitudes de sistema de nombres de dominio (DNS) mediante el clúster HDInsight. Si no está seleccionada, se resolverá DNS localmente.

2. Compruebe que el tráfico se ha enrutado a través del túnel vising un sitio como [http://www.whatismyip.com/](http://www.whatismyip.com/) con la configuración de proxy activa y desactiva en Firefox. Mientras que la configuración está habilitada, será la dirección IP para un equipo en el centro de datos de Microsoft Azure.

##<a name="verify-with-ambari-web-ui"></a>Compruebe con la interfaz de usuario de web Ambari

Una vez que se ha establecido el clúster, use los siguientes pasos para comprobar que puede tener acceso a web service interfaces de usuario desde la Ambari Web:

1. En el explorador, vaya a http://headnodehost:8080. La `headnodehost` dirección se enviarán a través del túnel al clúster y resolver en el headnode que se está ejecutando Ambari. Cuando se le pida, escriba el nombre de usuario de administrador (admin) y la contraseña para el clúster. Se le pedirá una segunda vez por la interfaz de usuario de web Ambari. Si es así, vuelva a escribir la información.
    
    > [AZURE.NOTE] Al utilizar la dirección de http://headnodehost:8080 para conectar con el clúster, se conecta directamente a través del túnel al nodo principal que se está ejecutando Ambari utilizando HTTP y comunicación está protegida mediante el túnel SSH. al conectarse a través de internet sin el uso de un túnel, comunicación es segura mediante HTTPS. Para conectarse a través de internet usando HTTPS, use https://CLUSTERNAME.azurehdinsight.net, donde __NOMBREDECLÚSTER__ es el nombre del clúster.

2. En la interfaz de usuario de Web Ambari, seleccione HDFS desde la lista de la izquierda de la página.

    ![Imagen con HDFS seleccionado](./media/hdinsight-linux-ambari-ssh-tunnel/hdfsservice.png)

3. Cuando se muestra la información del servicio HDFS, seleccione __Vínculos rápidos__. Aparecerá una lista de los nodos de clúster central. Seleccione uno de los nodos de cabeza y a continuación, seleccione la __Interfaz de usuario de NameNode__.

    ![Imagen con el menú de vínculos rápidos expandido](./media/hdinsight-linux-ambari-ssh-tunnel/namenodedropdown.png)

    > [AZURE.NOTE] Si tiene una conexión lenta a internet o el nodo principal está muy ocupado, pueden obtener un indicador de espera en lugar de un menú cuando se selecciona __Vínculos rápidos__. Si es así, espere un minuto o dos para los datos que se ha recibido desde el servidor y luego vuelva a la lista.
    >
    > Si tiene un monitor con resolución inferior o no está maximizada la ventana del explorador, algunas entradas en el menú __Vínculos rápidos__ pueden cortadas por el lado derecho de la pantalla. Si es así, expandir el menú con el mouse, utilice la tecla de flecha derecha para desplazarse a la pantalla hacia la derecha para ver el resto del menú.

4. Debe aparecer una página similar al siguiente:

    ![Imagen de la interfaz de usuario de NameNode](./media/hdinsight-linux-ambari-ssh-tunnel/namenode.png)

    > [AZURE.NOTE] Observe la dirección URL de esta página; debe ser similar a __http://hn1-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8088/clúster__. Esto es usar el nombre de dominio completo interno (FQDN) del nodo y no es accesible sin usar un túnel SSH.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido a crear y usar un túnel SSH, consulte lo siguiente para obtener información sobre supervisión y administrar el clúster utilizando Ambari:

* [Administrar clústeres HDInsight mediante Ambari](hdinsight-hadoop-manage-ambari.md)

Para obtener más información sobre cómo utilizar SSH con HDInsight, consulte lo siguiente:

* [Utilizar SSH con basados en Linux Hadoop en HDInsight de Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

* [Utilizar SSH con basados en Linux Hadoop en HDInsight de Windows.](hdinsight-hadoop-linux-use-ssh-windows.md)

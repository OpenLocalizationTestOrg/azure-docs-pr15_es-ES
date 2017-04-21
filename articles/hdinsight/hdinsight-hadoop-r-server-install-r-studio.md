<properties
    pageTitle="Instalar RStudio con servidor R HDInsight (vista preliminar) | Microsoft Azure"
    description="Cómo instalar RStudio con el servidor de R en HDInsight (vista preliminar)."
    services="hdinsight"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/16/2016"
   ms.author="jeffstok"/>


# <a name="installing-rstudio-with-r-server-on-hdinsight-preview"></a>Instalación RStudio con servidor R HDInsight (vista previa)

Existen varios entornos de desarrollo integrados (IDE) para R hoy, incluidos los de Microsoft recientemente anuncian [R Tools para Visual Studio](https://www.visualstudio.com/en-us/features/rtvs-vs.aspx) (RTV), una familia de herramientas de servidor y de escritorio de [RStudio](https://www.rstudio.com/products/rstudio-server/)o basada en Eclipse de Walware [StatET](http://www.walware.de/goto/statet). Entre más populares de Linux es el uso de [Servidor de RStudio](https://www.rstudio.com/products/rstudio-server/) que proporciona un IDE basada en explorador para su uso por los clientes remotos.  Instalar RStudio Server en el nodo del borde de un clúster de HDInsight Premium ofrece una experiencia completa de IDE para el desarrollo y la ejecución de scripts de R con el servidor de R en el clúster y puede ser mucho más productivo que uso predeterminado de la consola de R.

En este artículo aprenderá cómo instalar la versión (gratuito) de la Comunidad de RStudio Server en el nodo del borde de un clúster mediante un script personalizado. Si prefiere la versión Pro comercialmente con licencia RStudio Server, debe seguir las instrucciones de instalación del [Servidor de RStudio](https://www.rstudio.com/products/rstudio/download-server/).

> [AZURE.NOTE] Los pasos de este documento requiere un servidor R en clúster HDInsight y no funcionará correctamente si está utilizando un clúster de HDInsight donde R se ha instalado con la [Acción de secuencia de comandos de R instalar](hdinsight-hadoop-r-scripts-linux.md).

## <a name="prerequisites"></a>Requisitos previos

* Un clúster de Azure HDInsight con servidor R instalado. Para obtener instrucciones, consulte [Introducción a servidor R en clústeres HDInsight](hdinsight-hadoop-r-server-get-started.md).
* Un cliente SSH. Para las distribuciones de Linux y Unix o Macintosh OS X, el `ssh` comando se suministra con el sistema operativo. Para Windows, se recomienda [Cygwin](http://www.redhat.com/services/custom/cygwin/) con la [opción OpenSSH](https://www.youtube.com/watch?v=CwYSvvGaiWU)o [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).  


## <a name="install-rstudio-on-the-cluster-using-a-custom-script"></a>Instalar RStudio en el clúster mediante un script personalizado

1. Identificar el nodo del borde del clúster. Para un clúster de HDInsight con servidor R, a continuación se muestra la convención de nomenclatura para nodo principal y nodo del borde.

    * Nodo de cabeza:`CLUSTERNAME-ssh.azurehdinsight.net`
    * Nodo del borde:`R-Server.CLUSTERNAME-ssh.azurehdinsight.net` 

2. SSH en el nodo del borde del clúster mediante el modelo de nomenclatura anterior. 
 
    * Si va a conectar un cliente Linux, vea [conectarse a un clúster de HDInsight basados en Linux](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster).
    * Si se está conectando desde un cliente de Windows, vea [conectarse a un clúster de HDInsight basados en Linux mediante PuTTY](hdinsight-hadoop-linux-use-ssh-windows.md#connect-to-a-linux-based-hdinsight-cluster).

3. Cuando se haya conectado, convertirse en un usuario raíz en el clúster. En la sesión SSH, use el siguiente comando.

        sudo su -

4. Descargar el script personalizado para instalar RStudio. Use el siguiente comando.

        wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/InstallRStudio.sh

5. Cambiar los permisos en el archivo de script personalizado y ejecute la secuencia de comandos. Use los comandos siguientes.

        chmod 755 InstallRStudio.sh
        ./InstallRStudio.sh

6. Si utiliza una contraseña SSH al crear un clúster de HDInsight R Server, puede omitir este paso y pasar a la siguiente. Si utiliza una clave SSH en su lugar para crear el clúster, debe establecer una contraseña para el usuario SSH. Necesitará esta contraseña al conectarse a RStudio. Ejecute los comandos siguientes. Cuando se le pida **contraseña Kerberos actual**, presione **ENTRAR**.  Tenga en cuenta que debe reemplazarse `USERNAME` con un usuario SSH para el clúster de HDInsight.

        passwd USERNAME
        Current Kerberos password:
        New password:
        Retype new password:
        Current Kerberos password:
        
    Si la contraseña está configurada correctamente, verá un mensaje similar a este.

        passwd: password updated successfully


    Salir de la sesión SSH.

7. Crear un túnel SSH al clúster asignando `localhost:8787` en el clúster de HDInsight en el equipo cliente. Debe crear un túnel SSH antes de abrir una nueva sesión de explorador.

    * En un cliente Linux o un cliente de Windows con [Cygwin](http://www.redhat.com/services/custom/cygwin/) , a continuación, abra una sesión de terminal y use el comando siguiente.

            ssh -L localhost:8787:localhost:8787 USERNAME@R-Server.CLUSTERNAME-ssh.azurehdinsight.net
            
        Reemplace el **nombre de usuario** con un usuario SSH para el clúster HDInsight y **NOMBREDECLÚSTER** con el nombre de su clúster HDInsight también puede usar una clave SSH en lugar de una contraseña agregando`-i id_rsa_key`     

    * Si usa a un cliente de Windows con PuTTY después

        1.  Abra PuTTY y escriba la información de conexión. Si no está familiarizado con PuTTY, vea [Usar SSH con basados en Linux Hadoop en HDInsight de Windows](hdinsight-hadoop-linux-use-ssh-windows.md) para obtener información sobre cómo usar con HDInsight.
        2.  En la sección **categoría** a la izquierda del cuadro de diálogo, expanda la **conexión**, expanda **SSH**y, a continuación, seleccione **túneles**.
        3.  En el formulario de **Opciones para controlar el reenvío de puerto SSH** , complete la información siguiente:

            * **Puerto de origen** - el puerto en el cliente que desea reenviar. Por ejemplo, **8787**.
            * **Destino** : el destino que se debe asignar a la máquina cliente local. Por ejemplo, **localhost:8787**.

            ![Crear un túnel SSH] (./media/hdinsight-hadoop-r-server-install-r-studio/createsshtunnel.png "Crear un túnel SSH")

        4. Haga clic en **Agregar** para agregar la configuración y, a continuación, haga clic en **Abrir** para abrir una conexión SSH.
        5. Cuando se le solicita, inicie sesión en el servidor. Se establezca una sesión SSH y habilitar el túnel.

8. Abra un explorador web y escriba la siguiente URL basada en el puerto especificado para el túnel.

        http://localhost:8787/ 

9. Se le pedirá para escribir el nombre de usuario SSH y la contraseña para conectarse al clúster. Si utiliza una clave SSH al crear el clúster, debe escribir la contraseña que creó en el paso 5.

    ![Conectarse a R Studio] (./media/hdinsight-hadoop-r-server-install-r-studio/connecttostudio.png "Crear un túnel SSH")

10. Para comprobar si la instalación de RStudio es correcta, puede ejecutar una prueba de secuencia de comandos que se ejecuta R según trabajos MapReduce y motor en el clúster. Volver a la consola SSH y escriba los siguientes comandos para descargar el script de prueba para que se ejecute en RStudio.

    * Si ha creado un clúster de Hadoop con R, use este comando.
        
            wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/testhdi.r

    * Si ha creado un clúster de motor con R, use este comando.

            wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/testhdi_spark.r

11. En RStudio, verá la secuencia de comandos de prueba que ha descargado. Haga doble clic en el archivo para abrirlo, seleccione el contenido del archivo y, a continuación, haga clic en **Ejecutar**. Debe ver el resultado en el panel de **consola** .
 
    ![Probar la instalación] (./media/hdinsight-hadoop-r-server-install-r-studio/test-r-script.png "Probar la instalación")

Otra opción sería escribir `source(testhdi.r)` o `source(testhdi_spark.r)` para ejecutar la secuencia de comandos.

## <a name="see-also"></a>Vea también

- [Calcular las opciones de contexto de servidor R en clústeres HDInsight](hdinsight-hadoop-r-server-compute-contexts.md)

- [Azure opciones de almacenamiento para servidor R en HDInsight premium](hdinsight-hadoop-r-server-storage.md)


 

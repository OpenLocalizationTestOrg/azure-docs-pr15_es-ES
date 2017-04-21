<properties
    pageTitle="Administrar clústeres basados en Linux Hadoop en HDInsight con Azure portal | Microsoft Azure"
    description="Aprenda a crear y administrar clústeres basados en Linux HDInsight con el portal de Azure."
    services="hdinsight"
    documentationCenter=""
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="jgao"/>

#<a name="manage-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>Administrar clústeres Hadoop en HDInsight con el portal de Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Con el [portal de Azure][azure-portal], puede administrar clústeres basados en Linux en HDInsight de Azure. Utilice el tabulador para obtener información sobre cómo crear clústeres Hadoop en HDInsight con otras herramientas. 

**Requisitos previos**

Antes de comenzar este artículo, debe tener el siguiente:

- **Suscripción de un Azure**. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

##<a name="open-the-portal"></a>Abra el portal.

1. Inicie sesión en [https://portal.azure.com](https://portal.azure.com).
2. Después de abrir el portal, puede:

    - Haga clic en **nuevo** en el menú izquierdo para crear un nuevo clúster:
    
        ![botón nuevo de clúster HDInsight](./media/hdinsight-administer-use-portal-linux/azure-portal-new-button.png)
    - Haga clic en **Clústeres HDInsight** desde el menú de la izquierda para mostrar los clústeres existentes
    
        ![Botón de clúster de HDInsight portal Azure](./media/hdinsight-administer-use-portal-linux/azure-portal-hdinsight-button.png)

        Si **HDInsight** no aparece en el menú de la izquierda, haga clic en **Examinar**y, a continuación, haga clic en **Clústeres HDInsight**.

        ![Azure buttomn de clúster examinar portal](./media/hdinsight-administer-use-portal-linux/azure-portal-browse-button.png)

##<a name="create-clusters"></a>Crear clústeres

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

HDInsight funciona con los componentes de una amplia gama de Hadoop. Para la lista de los componentes que se hayan verificado y compatibles, consulte [¿qué versión de Hadoop es en HDInsight de Azure](hdinsight-component-versioning.md). La información de creación de clúster general, vea [crear Hadoop clústeres en HDInsight](hdinsight-hadoop-provision-linux-clusters.md). 

##<a name="list-and-show-clusters"></a>Lista y mostrar clústeres

1. Inicie sesión en [https://portal.azure.com](https://portal.azure.com).
2. Haga clic en **Clústeres HDInsight** desde el menú de la izquierda para mostrar los clústeres existentes.
3. Haga clic en el nombre del clúster. Si la lista de clúster es larga, puede usar el filtro en la parte superior de la página.
4. Haga doble clic en un clúster de la lista para mostrar los detalles.

    **Menú y essentials**:

    ![Aspectos básicos de clúster de Azure hdinsight portal](./media/hdinsight-administer-use-portal-linux/hdinsight-essentials.png)
    
    - **Configuración** y **Todas las configuraciones**: muestra el módulo de **configuración** para el clúster, que le permite acceder a la información de configuración detallada para el clúster.
    - **Paneles**, el **Panel de clúster** y ** URL: se trata de todas las formas de tener acceso al panel de clúster, que es Ambari Web para clústeres basados en Linux.
    - **Secure Shell**: muestra las instrucciones para conectar con el clúster con conexión de Shell seguro (SSH).
    - **Escala de clúster**: le permite cambiar el número de nodos de trabajo para este clúster.
    - **Eliminar**: elimina el clúster.
    - **Tutorial rápido (![icono de nube y thunderbolt = tutorial](./media/hdinsight-administer-use-portal-linux/quickstart.png))**: muestra la información que le ayudarán a empezar a usar HDInsight.
    - **Los usuarios (![icono usuarios](./media/hdinsight-administer-use-portal-linux/users.png))**: permite establecer permisos para la _administración de portal_ de este clúster para otros usuarios de su suscripción de Azure.
    
        > [AZURE.IMPORTANT] Este _sólo_ afecta al acceso y permisos para este clúster en el portal de Azure y no tiene ningún efecto en la que puede conectar a o enviar trabajos al clúster HDInsight.
    - **Etiquetas (![icono de la etiqueta](./media/hdinsight-administer-use-portal-linux/tags.png))**: etiquetas permite establecer pares de clave/valor para definir una taxonomía personalizada de los servicios de nube. Por ejemplo, puede crear una clave con el nombre de __proyecto__y, a continuación, use un valor común para todos los servicios asociados a un proyecto específico.
    - **Vistas de Ambari**: vínculos a Ambari Web.
    
    > [AZURE.IMPORTANT] Para administrar los servicios proporcionados por el clúster HDInsight, debe utilizar Ambari Web o la API de REST de Ambari. Para obtener más información sobre cómo usar Ambari, vea [clústeres administrar HDInsight con Ambari](hdinsight-hadoop-manage-ambari.md).

    **Uso**:
    
    ![Uso de clúster de Azure hdinsight portal](./media/hdinsight-administer-use-portal-linux/hdinsight-portal-cluster-usage.png)
    
5. Haga clic en **configuración**.

    ![Uso de clúster de Azure hdinsight portal](./media/hdinsight-administer-use-portal-linux/hdinsight.portal.cluster.settings.png)

    - **Registros de auditoría**:
    - **Inicio rápido**: muestra la información que le ayudarán a empezar a usar HDInsight.
    - **Escala de clúster**: aumentar y disminuir el número de nodos del clúster de trabajo.
    - **Secure Shell**: muestra las instrucciones para conectar con el clúster con conexión de Shell seguro (SSH).
    - **HDInsight Partner**: agregar o quitar el HDInsight Partner actual.
    - **Metastores externos**: vea la sección y Oozie metastores. Solo se puede configurar la metastores durante el proceso de creación de clúster.
    - **Acciones de script**: ejecutar Bash secuencias de comandos en el clúster.
    - **Propiedades**: ver las propiedades de clúster.
    - **Teclas de almacenamiento de Azure**: ver la cuenta de almacenamiento predeterminada y su clave. La cuenta de almacenamiento es configuración durante el proceso de creación de clúster.
    - **Identidad AAD de clúster**: 
    - **Los usuarios**: permite establecer permisos para la _administración de portal_ de este clúster para otros usuarios de su suscripción de Azure.
    - **Etiquetas**: etiquetas permite establecer pares de clave/valor para definir una taxonomía personalizada de los servicios de nube. Por ejemplo, puede crear una clave con el nombre de __proyecto__y, a continuación, use un valor común para todos los servicios asociados a un proyecto específico.
    
    > [AZURE.NOTE] Se trata de una lista genérica de las configuraciones disponibles; No todos ellos se presentarán para todos los tipos de clúster.

6. Haga clic en **Propiedades**:

    Las propiedades son:
    
    - **Hostname**: nombre del clúster.
    - **Dirección URL del clúster**.
    - **Estado**: incluir anulado, acepte, ClusterStorageProvisioned, AzureVMConfiguration, HDInsightConfiguration, operativa, ejecutar, Error, eliminar, eliminar, tiempo de espera agotado, DeleteQueued, DeleteTimedout, DeleteError, PatchQueued, CertRolloverQueued, ResizeQueued, ClusterCustomization
    - **Región**: ubicación de Azure. Para obtener una lista de ubicaciones de Azure admitidos, vea el cuadro de lista desplegable de **región** en [HDInsight de precios](https://azure.microsoft.com/pricing/details/hdinsight/).
    - **Crean datos**.
    - **Sistema operativo**: **Windows** o **Linux**.
    - **Tipo**: Hadoop, HBase, tormenta, despierten. 
    - **Versión**. Vea [versiones HDInsight](hdinsight-component-versioning.md)
    - **Suscripción**: nombre de suscripción.
    - **Id. de suscripción**.
    - **Origen de datos predeterminado**: el sistema de archivos de clúster de forma predeterminada.
    - **Nivel de precios de nodos de trabajo**.
    - **Nivel de precios de nodo de cabeza**.

##<a name="delete-clusters"></a>Eliminar clústeres

Eliminar un clúster no elimina la cuenta de almacenamiento predeterminada o cualquier cuenta de almacenamiento vinculado. Puede volver a crear el clúster con las mismas cuentas de almacenamiento y el mismo metastores. Se recomienda usar un nuevo contenedor de blobs de forma predeterminada, cuando vuelva a crear el clúster.

1. Inicie sesión en el [Portal de][azure-portal].
2. Haga clic en **Examinar todos** en el menú de la izquierda, haga clic en **HDInsight clústeres**, haga clic en el nombre del clúster.
3. Haga clic en **Eliminar** en el menú superior y, a continuación, siga las instrucciones.

Vea también [pausa o apagado clústeres](#pauseshut-down-clusters).

##<a name="scale-clusters"></a>Clústeres de escala
La característica de ajuste de escala de clúster le permite cambiar el número de nodos de trabajo utilizados por un clúster que se ejecuta en Azure HDInsight sin tener que volver a crear el clúster.

>[AZURE.NOTE] Solo clústeres con HDInsight versión 3.1.3 o superior son compatibles. Si no está seguro de la versión de su clúster, puede comprobar la página de propiedades.  Consulte [clústeres de lista y mostrar](#list-and-show-clusters).

El impacto de cambiar el número de nodos de datos para cada tipo de clúster compatible con HDInsight:

- Hadoop

    Sin problemas, puede aumentar el número de nodos de trabajo en un clúster de Hadoop que se está ejecutando sin afectar a los trabajos pendientes o se está ejecutando. También se pueden enviar trabajos de nuevos mientras la operación está en curso. Errores en una operación de escalado se administran correctamente para que el clúster siempre se quede en un estado funcional.

    Cuando se cambia el tamaño de un clúster de Hadoop hacia abajo al reducir el número de nodos de datos, algunos de los servicios en el clúster se reinician. Esto hace todas ellas y trabajos pendientes errores al final de la operación de rotación. Sin embargo, puede volver a enviar las tareas una vez completada la operación.

- HBase

    Sin problemas, puede agregar o quitar nodos en el clúster HBase mientras se está ejecutando. Los servidores regionales se equilibran automáticamente unos minutos después de completar la operación de escala. Sin embargo, puede ajustar manualmente los servidores regionales, inicie sesión en la headnode de clúster y ejecute los comandos siguientes desde una ventana de símbolo del sistema:

        >pushd %HBASE_HOME%\bin
        >hbase shell
        >balancer

    Para obtener más información sobre el uso de la consola de HBase, consulte]
- Tormenta

    Sin problemas, puede agregar o quitar nodos de datos en el clúster de tormenta mientras se está ejecutando. Pero después de la finalización correcta de la operación de escalado, debe equilibrar la topología.

    Volver a equilibrar puede realizarse de dos maneras:

    * Interfaz de usuario de web de tormenta
    * Herramienta de línea de comandos de interfaz

    Consulte la [documentación de tormenta Apache](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) para obtener más detalles.

    La interfaz de usuario de web tormenta está disponible en el clúster HDInsight:

    ![equilibrar de escala de tormenta hdinsight](./media/hdinsight-administer-use-portal-linux/hdinsight.portal.scale.cluster.storm.rebalance.png)

    Mostramos un ejemplo de cómo usar el comando CLI a equilibrar la topología de tormenta:

        ## Reconfigure the topology "mytopology" to use 5 worker processes,
        ## the spout "blue-spout" to use 3 executors, and
        ## the bolt "yellow-bolt" to use 10 executors

        $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10


**Escalar clústeres**

1. Inicie sesión en el [Portal de][azure-portal].
2. Haga clic en **Examinar todos** en el menú de la izquierda, haga clic en **HDInsight clústeres**, haga clic en el nombre del clúster.
3. Haga clic en **configuración** en el menú superior y, a continuación, haga clic en **El clúster de escala**.
4. Escriba **los nodos de número de trabajo**. El límite del número de nodo varía entre suscripciones de Azure. Puede ponerse en contacto con el soporte de facturación para aumentar el límite.  La información de costo reflejarán los cambios realizados en el número de nodos.

    ![escala de motor de hdinsight hadoop hbase tormenta](./media/hdinsight-administer-use-portal-linux/hdinsight.portal.scale.cluster.png)

##<a name="pauseshut-down-clusters"></a>Pausar o apagado clústeres

La mayoría de los trabajos de Hadoop son procesos que sólo se ha encontrado en ocasiones. La mayoría de los clústeres Hadoop, hay grandes períodos de tiempo que el clúster no se utiliza para el procesamiento. Con HDInsight, los datos se almacenan en el almacenamiento de Azure, por lo que puede eliminar un clúster cuando no está en uso.
También se cargan un clúster HDInsight, incluso cuando no está en uso. Dado que muchas veces más que los cargos para el almacenamiento de los cargos para el clúster, tiene sentido económico para eliminar clústeres cuando no están en uso.

Hay muchas formas puede programar el proceso:

- Generador de Azure datos de usuario. Para crear servicios de HDInsight vinculado a petición, vea [crear a petición basados en Linux Hadoop clústeres en HDInsight con el generador de datos de Azure](hdinsight-hadoop-create-linux-clusters-adf.md) .
- Usar PowerShell Azure.  Vea [analizar datos de retrasos de vuelo](hdinsight-analyze-flight-delay-data.md).
- Utilizar CLI Azure. Consulte [clústeres administrar HDInsight mediante CLI de Azure](hdinsight-administer-use-command-line.md).
- Use HDInsight .NET SDK. Consulte [Hadoop enviar trabajos](hdinsight-submit-hadoop-jobs-programmatically.md).

Para la información de precios, consulte [HDInsight de precios](https://azure.microsoft.com/pricing/details/hdinsight/). Para eliminar un clúster desde el Portal, vea [Eliminar clústeres](#delete-clusters)

##<a name="change-passwords"></a>Cambiar las contraseñas

Un clúster de HDInsight puede tener dos cuentas de usuario. HDInsight de la cuenta de usuario de clúster (también conocido como Cuenta de usuario HTTP) y la cuenta de usuario SSH se crean durante el proceso de creación. Puede que la interfaz de usuario para cambiar el nombre de cuenta de usuario de clúster y contraseña y a continuación, acciones de secuencia de comandos para cambiar la cuenta de usuario SSH de Ambari web

###<a name="change-the-cluster-user-password"></a>Cambiar la contraseña de usuario de clúster

Puede usar la interfaz de usuario de Web Ambari para cambiar la contraseña de usuario de clúster. Para iniciar sesión en Ambari, debe usar el nombre de usuario de clúster existente y la contraseña.

> [AZURE.NOTE] Si cambia la contraseña de usuario (admin) de clúster, esto puede ocasionar acciones ejecutaban contra este clúster errores de script. Si tiene las acciones de script almacenado ese nodos de trabajo de destino, estos pueden fallar al agregar nodos al clúster a través de cambiar el tamaño de las operaciones. Para obtener más información sobre las acciones de secuencias de comandos, consulte [clústeres de personalizar HDInsight usar acciones de script](hdinsight-hadoop-customize-cluster-linux.md).

1. Inicie sesión en la interfaz de usuario de Ambari Web con las credenciales de usuario de clúster HDInsight. El nombre de usuario predeterminado es **admin**. La dirección URL es **https://&lt;nombre de clúster HDInsight > azurehdinsight.net**.
2. Haga clic en **Administrador** en el menú superior y, a continuación, haga clic en "Administrar Ambari". 
3. En el menú de la izquierda, haga clic en **usuarios**.
4. Haga clic en **Administrador**.
5. Haga clic en **Cambiar contraseña**.

Ambari, a continuación, cambia la contraseña en todos los nodos en el clúster.

###<a name="change-the-ssh-user-password"></a>Cambiar la contraseña de usuario SSH

1. Con un editor de texto, guarde lo siguiente como un archivo denominado __changepassword.sh__.

    > [AZURE.IMPORTANT] Debe utilizar un editor que utiliza LF como el final de la línea. Si el editor utiliza CRLF, la secuencia de comandos no funcionará.
    
        #! /bin/bash
        USER=$1
        PASS=$2

        usermod --password $(echo $PASS | openssl passwd -1 -stdin) $USER

2. Cargue el archivo en una ubicación de almacenamiento que se puede acceder desde HDInsight con una dirección HTTP o HTTPS. Por ejemplo, un archivo público almacenar como el almacenamiento de blobs de Windows Azure o de OneDrive. Guardar el URI (dirección HTTP o HTTPS), en el archivo, como en el siguiente paso es necesario.

3. Desde el portal de Azure, seleccione el clúster HDInsight y, a continuación, seleccione __toda la configuración__. En el módulo de __configuración__ , seleccione __Acciones de Script__.

4. En el módulo de __Secuencia de comandos de acciones__ , seleccione __Enviar de nuevo__. Cuando aparezca el módulo de __acción de secuencia de comandos de envío__ , escriba la información siguiente.

  	| Campo | Valor |
  	| ----- | ----- |
  	| Nombre | Cambiar ssh contraseña |
  	| Secuencia de comandos de fiesta URI | La URI del archivo de changepassword.sh |
  	| Nodos (cabeza, empleado, Nimbus, Supervisor, Zookeeper, etcetera) | ✓ para todos los tipos de nodo enumerados |
  	| Parámetros | Escriba el nombre de usuario SSH y, a continuación, en la nueva contraseña. Debe haber un espacio entre el nombre de usuario y la contraseña.
  	| Conservar esta acción de secuencia de comandos... | Deje este campo en desactivado.

5. Seleccione __crear__ para aplicar la secuencia de comandos. Una vez que finalice la secuencia de comandos, podrá conectar con el clúster mediante SSH con la nueva contraseña.

##<a name="grantrevoke-access"></a>Conceder o denegar el acceso a

HDInsight clústeres tienen los siguientes servicios de web HTTP (todos estos servicios tienen extremos REST):

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton

De forma predeterminada, estos servicios se le ha concedido acceso. Puede revocar o conceder el acceso mediante [CLI de Azure](hdinsight-administer-use-command-line.md#enabledisable-http-access-for-a-cluster) y [Azure PowerShell](hdinsight-administer-use-powershell.md#grantrevoke-access).

##<a name="find-the-subscription-id"></a>Busque el identificador de la suscripción

**Para encontrar la suscripción de Azure identificadores**

1. Inicie sesión en el [Portal de][azure-portal].
2. Haga clic en **Examinar todos** en el menú izquierdo y, a continuación, haga clic en **suscripciones**. Cada suscripción tiene un nombre y un Id.

Cada clúster está vinculado a una suscripción de Azure. El identificador de la suscripción se muestra en el clúster **esenciales** de mosaico. Consulte [clústeres de lista y mostrar](#list-and-show-clusters).

##<a name="find-the-resource-group"></a>Busque el grupo de recursos 

En el modo ARM, cada clúster HDInsight se crea con un grupo de recursos de Azure. El grupo de recursos de Azure que pertenece un clúster aparece en:

- La lista de clúster tiene una columna de **Grupo de recursos** .
- Mosaico de clúster **esenciales** .  

Consulte [clústeres de lista y mostrar](#list-and-show-clusters).


##<a name="find-the-default-storage-account"></a>Buscar la cuenta de almacenamiento predeterminada

Cada clúster HDInsight tiene una cuenta de almacenamiento predeterminada. La cuenta de almacenamiento predeterminada y sus claves para un clúster aparece en **configuración**/**Propiedades**/**Claves de almacenamiento de Azure**. Consulte [clústeres de lista y mostrar](#list-and-show-clusters).


##<a name="run-hive-queries"></a>Ejecutar consultas de la sección

No se puede ejecutar el trabajo de la sección directamente desde el portal de Azure, pero puede usar la vista de sección en la interfaz de usuario de Ambari Web.

**Ejecutar consultas de la sección con la vista de sección Ambari**

1. Inicie sesión en la interfaz de usuario de Ambari Web con las credenciales de usuario de clúster HDInsight. El nombre de usuario predeterminado es **admin**. La dirección URL es **https://&lt;nombre de clúster HDInsight > azurehdinsight.net**.
2. Abrir vista subárbol como se muestra en la siguiente captura de pantalla:  

    ![vista de la sección de hdinsight](./media/hdinsight-administer-use-portal-linux/hdinsight-hive-view.png)
3. En el menú superior, haga clic en **consulta** .
4. Escriba una consulta de sección en el **Editor de consultas**y, a continuación, haga clic en **Ejecutar**.

##<a name="monitor-jobs"></a>Supervisar los trabajos.

Consulte [HDInsight administrar clústeres mediante el uso de la interfaz de usuario de Web Ambari](hdinsight-hadoop-manage-ambari.md#monitoring).

##<a name="browse-files"></a>Examinar los archivos

Con el portal de Azure, puede examinar el contenido del contenedor predeterminado.

1. Inicie sesión en [https://portal.azure.com](https://portal.azure.com).
2. Haga clic en **Clústeres HDInsight** desde el menú de la izquierda para mostrar los clústeres existentes.
3. Haga clic en el nombre del clúster. Si la lista de clúster es larga, puede usar el filtro en la parte superior de la página.
4. Haga clic en **configuración**.
5. Módulo de **configuración** , haga clic en **Claves de almacenamiento de Azure**.
6. Haga clic en el nombre de la cuenta de almacenamiento predeterminada.
7. Haga clic en el mosaico de **BLOB** .
8. Haga clic en el nombre del contenedor predeterminado.


##<a name="monitor-cluster-usage"></a>Supervisar el uso de clúster

La sección __uso__ del módulo de clúster HDInsight muestra información sobre el número de núcleos disponibles para su suscripción para su uso con HDInsight, así como el número de núcleos asignada a este clúster y cómo se asignan para los nodos de este clúster. Consulte [clústeres de lista y mostrar](#list-and-show-clusters).

> [AZURE.IMPORTANT] Para supervisar los servicios proporcionados por el clúster HDInsight, debe utilizar Ambari Web o la API de REST de Ambari. Para obtener más información sobre cómo usar Ambari, vea [clústeres administrar HDInsight con Ambari](hdinsight-hadoop-manage-ambari.md)

##<a name="connect-to-a-cluster"></a>Conectarse a un clúster

Consulte [usar subárbol con Hadoop en HDInsight con SSH](hdinsight-hadoop-use-hive-ssh.md#ssh).
    
##<a name="next-steps"></a>Pasos siguientes
En este artículo, ha visto cómo crear un clúster de HDInsight a través del Portal y cómo abrir la herramienta de línea de comandos de Hadoop. Para obtener más información, consulte los artículos siguientes:

* [Administrar HDInsight con PowerShell Azure](hdinsight-administer-use-powershell.md)
* [Administrar HDInsight mediante CLI Azure](hdinsight-administer-use-command-line.md)
* [Crear clústeres HDInsight](hdinsight-provision-clusters.md)
* [Usar subárbol en HDInsight](hdinsight-use-hive.md)
* [Usar cerdo en HDInsight](hdinsight-use-pig.md)
* [Usar Sqoop en HDInsight](hdinsight-use-sqoop.md)
* [Introducción a HDInsight de Azure](hdinsight-hadoop-linux-tutorial-get-started.md)
* [¿Qué versión de Hadoop es en HDInsight de Azure?](hdinsight-component-versioning.md)

[azure-portal]: https://portal.azure.com
[image-hadoopcommandline]: ./media/hdinsight-administer-use-portal-linux/hdinsight-hadoop-command-line.png "Línea de comandos de Hadoop"

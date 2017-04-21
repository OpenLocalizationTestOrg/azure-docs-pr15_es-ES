<properties
    pageTitle="Crear clústeres Hadoop, HBase, tormenta o motor en Linux en HDInsight con el portal | Microsoft Azure"
    description="Obtenga información sobre cómo crear clústeres Hadoop, HBase, tormenta o motor en Linux para HDInsight utilizando un explorador web y el portal de vista previa de Azure."
    services="hdinsight"
    documentationCenter=""
    authors="nitinme"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="10/05/2016"
    ms.author="nitinme"/>


#<a name="create-linux-based-clusters-in-hdinsight-using-the-azure-portal"></a>Crear clústeres basados en Linux en HDInsight con el portal de Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

El portal de Azure es una herramienta de administración basada en web para los servicios y los recursos alojados en la nube de Microsoft Azure. En este artículo aprenderá a crear clústeres basados en Linux HDInsight con el portal.

## <a name="prerequisites"></a>Requisitos previos

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


- **Suscripción de un Azure**. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- __Un explorador web moderna__. El portal de Azure usa HTML5 y Javascript y podría no funcionar correctamente en exploradores web antiguos.

### <a name="access-control-requirements"></a>Requisitos de control de acceso

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

##<a name="create-clusters"></a>Crear clústeres

El portal de Azure expone la mayoría de las propiedades del clúster. Utilizar el Administrador de recursos de Azure plantilla, puede ocultar una gran cantidad de detalles. Para obtener más información, vea [Hadoop basados en Linux crear clústeres en HDInsight con plantillas de administrador de recursos de Azure](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

1. Inicie sesión en el [portal de Azure](https://portal.azure.com).

2. Haga clic en **nuevo**, haga clic en **Análisis de datos**y, a continuación, haga clic en **HDInsight**.

    ![Crear un nuevo clúster en el portal de Azure] (./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.1.png "Crear un nuevo clúster en el portal de Azure")
3. Escriba **El nombre de clúster**: este nombre debe ser único global.
4. Haga clic en **Seleccionar tipo de clúster**y, a continuación, seleccione:

    - **Tipo de clúster**: si no sabe qué elegir, seleccione **Hadoop**. Es el tipo de clúster más popular.

        > [AZURE.IMPORTANT] HDInsight formar una gran variedad de tipos, que corresponden a la carga de trabajo o la tecnología de clúster está optimizado para clústeres. No hay ningún método para crear un clúster que combina varios tipos, como tormenta y HBase en un clúster. 

    - **Sistema operativo**: seleccione **Linux**.
    - **Versión**: usar la versión predeterminada si no sabe qué elegir. Para obtener más información, vea [versiones de clúster HDInsight](hdinsight-component-versioning.md).
    - **Nivel de clúster**: HDInsight de Azure proporciona las ofertas de nube de datos grande en dos categorías: nivel Premium y estándar. Para obtener más información, vea [niveles de clúster](hdinsight-hadoop-provision-linux-clusters.md#cluster-tiers).
    
    ![Configuración de nivel de HDInsight premium](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-cluster-type-configuration.png)

4. Haga clic en la **suscripción** para seleccionar la suscripción de Azure que se utilizará para el clúster.

5. Haga clic en el **Grupo de recursos** para seleccionar un grupo de recursos existente o haga clic en **nuevo** para crear un nuevo grupo de recursos

    > [AZURE.NOTE] Esta entrada predeterminada uno de sus grupos de recursos existentes, si alguno está disponible.

6. Haga clic en **credenciales** y, a continuación, escriba una contraseña para el usuario administrador. También debe escribir un **Nombre de usuario SSH** y una **contraseña** o **Clave pública**, que se usará para autenticar al usuario SSH. Con una clave pública es el enfoque recomendado. En la parte inferior para guardar la configuración de credenciales, haga clic en **Seleccionar** .

    ![Proporcionar credenciales de clúster] (./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.3.png "Proporcionar credenciales de clúster")

    Para obtener más información sobre cómo utilizar SSH con HDInsight, consulte uno de los siguientes artículos:

    * [Utilizar SSH con basados en Linux Hadoop en HDInsight de Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [Utilizar SSH con basados en Linux Hadoop en HDInsight de Windows.](hdinsight-hadoop-linux-use-ssh-windows.md)


7. Haga clic en **Origen de datos** para elegir un origen de datos existente para el clúster o cree uno nuevo.

    ![Módulo de origen de datos] (./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.4.png "Configuración de origen de datos de proporcionar")

    Actualmente puede seleccionar una cuenta de almacenamiento de Azure como origen de datos para un clúster de HDInsight. Utilice las siguientes acciones para comprender las entradas en el módulo de **Origen de datos** .

    - **Método de selección**: establezca este valor en el cuadro **de todas las suscripciones** para habilitar la exploración de cuentas de almacenamiento de todas las suscripciones. Establezca **Tecla de acceso** si desea escribir el **Nombre de almacenamiento** y una **Tecla de acceso** de una cuenta de almacenamiento existente.

    - **Seleccione cuenta de almacenamiento / nuevo**: haga clic en **Seleccionar cuenta de almacenamiento** para examinar y seleccione una cuenta existente de almacenamiento que desea asociar con el clúster. O bien, haga clic en **nuevo** para crear una nueva cuenta de almacenamiento. Utilice el campo que aparece para especificar el nombre de la cuenta de almacenamiento. Si el nombre está disponible, aparecerá una marca de verificación verde.

    - **Elija predeterminado contenedor**: Use esta opción para escribir el nombre del contenedor predeterminado que se utilizará para el clúster. Aunque puede escribir cualquier nombre aquí, recomendamos con el mismo nombre que el clúster de modo que pueda reconocer fácilmente que se utiliza el contenedor para este clúster específico.

    - **Ubicación**: la región geográfica que está en la cuenta de almacenamiento o se creará en.

        > [AZURE.IMPORTANT] Seleccionar la ubicación de origen de datos predeterminado, también se establece la ubicación del clúster HDInsight. El origen de datos predeterminada y clúster debe estar ubicado en la misma región.
        
    - **Identidad de AAD de clúster**: mediante su configuración, realiza el clúster sea accesible para el stores lago de datos de Azure según la configuración de AAD.

    Haga clic en **Seleccionar** para guardar la configuración de origen de datos.

8. Haga clic en **Niveles de precios de nodo** para mostrar información acerca de los nodos que se crearán para este clúster. Establecer el número de nodos de trabajo que necesita para el clúster. El costo estimado del clúster se mostrarán en el módulo.

    ![Módulo de niveles de precios de nodo] (./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.5.png "Especificar número de nodos de clúster")
    
    > [AZURE.IMPORTANT] Si piensa en más de 32 nodos de trabajo, en la creación de un clúster o ajustando el clúster tras la creación, debe seleccionar un tamaño de nodo principal con al menos 8 núcleos y 14GB de ram.
    >
    > Para obtener más información sobre los tamaños de nodo y los costos asociados, consulte [HDInsight de precios](https://azure.microsoft.com/pricing/details/hdinsight/).

    Haga clic en **Seleccionar** para guardar la configuración de precios de nodo.

9. Haga clic en **Configuración opcional** para seleccionar la versión de clúster, así como configurar otras opciones opcionales como unirse a una **Red Virtual**, configurar un **Metastore externo** para almacenar datos de la sección y Oozie, usar acciones de secuencia de comandos para personalizar un clúster para instalar componentes personalizados o usar cuentas de almacenamiento adicional con el clúster.

    * **Una red virtual**: seleccione una red virtual Azure y la subred si desea colocar el clúster en una red virtual.  

        ![Módulo de red virtual] (./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.6.png "Detalles de la red virtual de especificar")

        Para obtener información sobre cómo usar HDInsight con una red Virtual, incluidos los requisitos de configuración específica de la red Virtual, vea [capacidades de extender HDInsight mediante una red Virtual de Azure](hdinsight-extend-hadoop-virtual-network.md).

    * Haga clic en **Metastores externo** para especificar la base de datos SQL que desea utilizar para guardar la sección y Oozie metadatos asociados con el clúster.
    
        > [AZURE.NOTE] Configuración de Metastore no está disponible para los tipos de clúster HBase.

        ![Módulo de metastores personalizada] (./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.7.png "Especificar externos metastores")

        Para **usar una base de datos de SQL existente para subárbol** metadatos, haga clic en **Sí**, seleccione una base de datos SQL y, a continuación, proporcione el nombre de usuario y la contraseña para la base de datos. Repita estos pasos si desea **usar una base de datos de SQL existente para metadatos Oozie**. Haga clic en **Seleccionar** hasta que se encuentra en el módulo de **Configuración opcional** .

        >[AZURE.NOTE] La base de datos de SQL Azure utilizado para el metastore debe permitir la conectividad con otros servicios de Azure, incluidos HDInsight de Azure. En el panel de la base de datos de SQL Azure, en el lado derecho, haga clic en el nombre del servidor. Éste es el servidor en el que se ejecuta la instancia de base de datos SQL. Una vez que se muestran en la vista de servidor, haga clic en **Configurar**y para los **Servicios de Azure**, haga clic en **Sí**y, a continuación, haga clic en **Guardar**.

        &nbsp;

        > [AZURE.IMPORTANT] Al crear un metastore, no use un nombre de base de datos que contiene guiones o guiones, ya que puede causar el proceso de creación de clúster no se realice correctamente.

    * **Acciones de script** si desea usar un script personalizado para personalizar un clúster, como el clúster se está creando. Para obtener más información acerca de las acciones de secuencias de comandos, consulte [clústeres de personalizar HDInsight con la acción de secuencia de comandos](hdinsight-hadoop-customize-cluster-linux.md). En el módulo de acciones de Script proporcionar los detalles, como se muestra en la captura de pantalla.

        ![Módulo de acción de secuencia de comandos] (./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.8.png "Especificar acción de secuencia de comandos")

    * Haga clic en **Cuentas de almacenamiento vinculadas** para especificar las cuentas de almacenamiento adicional para asociar con el clúster. En el módulo de **Claves de almacenamiento de Azure** , haga clic en **Agregar una clave de almacenamiento**, seleccione una cuenta de almacenamiento existente o crear una nueva cuenta.

        ![Módulo de almacenamiento adicional] (./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.9.png "Especificar cuentas de almacenamiento adicional")

        También puede agregar cuentas de almacenamiento adicional después de haber creado un clúster.  Consulte [clústeres basados en Linux personalizar HDInsight con la acción de secuencia de comandos](hdinsight-hadoop-customize-cluster-linux.md).

        Haga clic en **Seleccionar** hasta que se encuentra en el módulo de **clúster HDInsight de nuevo** .
        
        Además de la cuenta de almacenamiento de blobs, también puede vincular lago de datos de Azure stores. La configuración se puede configurar AAD de origen de datos donde configuró la cuenta de almacenamiento predeterminada y el contenedor predeterminado.

10. En el módulo **Nuevo HDInsight clúster** , asegúrese de que está seleccionada la opción **Anclar al Startboard** y, a continuación, haga clic en **crear**. Esto creará el clúster y agregar un mosaico correspondiente a la Startboard de su portal de Azure. El icono indicará que el clúster es aprovisionamiento y cambiará para mostrar el icono de HDInsight una vez completada la aprovisionamiento.

  	| Durante el aprovisionamiento | Aprovisionamiento completa |
  	| ------------------ | --------------------- |
  	| ![Indicador de aprovisionamiento en startboard](./media/hdinsight-hadoop-create-linux-cluster-portal/provisioning.png) | ![Mosaico de clúster preparación](./media/hdinsight-hadoop-create-linux-cluster-portal/provisioned.png) |

    > [AZURE.NOTE] Esta acción tardará algún tiempo para que el clúster creado, normalmente unos 15 minutos. Use el mosaico de la Startboard o la entrada de **notificaciones** de la izquierda de la página para comprobar el proceso de aprovisionamiento.

11. Una vez finalizado el proceso de creación, haga clic en el mosaico de clúster desde el Startboard para iniciar el módulo de clúster. El módulo de clúster proporciona información esencial sobre el clúster como el nombre, el grupo de recursos que pertenece, la ubicación, el sistema operativo, la dirección URL para el panel de clúster, etcetera.

    ![Módulo de clúster] (./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.Cluster.Blade.png "Propiedades de clúster")

    Comprender los iconos en la parte superior de este módulo y, en la sección **Essentials** , utilice la siguiente:

    * **Configuración** y **Todas las configuraciones**: muestra el módulo de **configuración** para el clúster, que le permite acceder a la información de configuración detallada para el clúster.

    * **Paneles**, el **Panel clúster**y la **dirección URL**: se trata de todas las formas de tener acceso al panel de clúster, que es un portal Web para ejecutar trabajos en el clúster.

    * **Secure Shell**: información necesaria para tener acceso al clúster mediante SSH.

    * **Eliminar**: elimina el clúster HDInsight.

    * **Tutorial rápido** (![icono de nube y thunderbolt = tutorial](./media/hdinsight-hadoop-create-linux-cluster-portal/quickstart.png)): muestra la información que le ayudarán a empezar a usar HDInsight.

    * **Usuarios** (![icono usuarios](./media/hdinsight-hadoop-create-linux-cluster-portal/users.png)): permite establecer permisos para la _administración de portal_ de este clúster para otros usuarios de su suscripción de Azure.

        > [AZURE.IMPORTANT] Este _sólo_ afecta al acceso y permisos para este clúster en el portal de Azure y no tiene ningún efecto en la que puede conectar a o enviar trabajos al clúster HDInsight.

    * **Etiquetas** (![icono de la etiqueta](./media/hdinsight-hadoop-create-linux-cluster-portal/tags.png)): etiquetas permite establecer pares de clave/valor para definir una taxonomía personalizada de los servicios de nube. Por ejemplo, puede crear una clave con el nombre de __proyecto__y, a continuación, use un valor común para todos los servicios asociados a un proyecto específico.

##<a name="customize-clusters"></a>Personalizar clústeres

- Consulte [clústeres personalizar HDInsight con inicio](hdinsight-hadoop-customize-cluster-bootstrap.md).
- Consulte [clústeres basados en Linux personalizar HDInsight con la acción de secuencia de comandos](hdinsight-hadoop-customize-cluster-linux.md).

##<a name="delete-the-cluster"></a>Eliminar el clúster

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

##<a name="next-steps"></a>Pasos siguientes

Ahora que ha creado correctamente un clúster HDInsight, use los siguientes para obtener información sobre cómo trabajar con el clúster:

###<a name="hadoop-clusters"></a>Clústeres Hadoop

* [Usar subárbol con HDInsight](hdinsight-use-hive.md)
* [Usar cerdo con HDInsight](hdinsight-use-pig.md)
* [Usar MapReduce con HDInsight](hdinsight-use-mapreduce.md)

###<a name="hbase-clusters"></a>Clústeres HBase

* [Introducción a HBase en HDInsight](hdinsight-hbase-tutorial-get-started-linux.md)
* [Desarrollar aplicaciones Java para HBase en HDInsight](hdinsight-hbase-build-java-maven-linux.md)

###<a name="storm-clusters"></a>Clústeres de tormenta

* [Desarrollar topologías Java para tormenta en HDInsight](hdinsight-storm-develop-java-topology.md)
* [Utilizar componentes de Python en tormenta en HDInsight](hdinsight-storm-develop-python-topology.md)
* [Implementar y supervisar topologías con tormenta en HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

###<a name="spark-clusters"></a>Motor de clústeres

* [Crear una aplicación independiente de Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Ejecutar trabajos de forma remota en un clúster de motor mediante Livio](hdinsight-apache-spark-livy-rest-interface.md)
* [Motor con BI: realizar análisis de datos interactivas con motor en HDInsight con herramientas de inteligencia empresarial](hdinsight-apache-spark-use-bi-tools.md)
* [Motor con Machine aprendizaje: motor de uso en HDInsight de predecir los resultados de la inspección de comida](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Motor de flujo de datos: Motor de uso en HDInsight para la creación de aplicaciones de transmisión en tiempo real](hdinsight-apache-spark-eventhub-streaming.md)

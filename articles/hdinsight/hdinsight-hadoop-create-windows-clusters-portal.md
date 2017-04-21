<properties
   pageTitle="Crear clústeres de Hadoop en HDInsight | Microsoft Azure"
    description="Obtenga información sobre cómo crear clústeres para HDInsight de Azure a través del Portal de Azure."
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/02/2016"
   ms.author="jgao"/>

# <a name="create-windows-based-hadoop-clusters-in-hdinsight-using-the-azure-portal"></a>Crear clústeres basados en Windows Hadoop en HDInsight con el Portal de Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Obtenga información sobre cómo crear un clúster de Hadoop en HDInsight con el Portal de Azure. Microsoft [Azure portal](../azure-portal-overview.md) es un lugar central donde puede aprovisionar y administrar los recursos de Azure. Portal de Azure es una de las herramientas que puede usar para crear basado en Linux o en Windows clúster de Hadoop en HDInsight. Para otra creación de clúster características y herramientas haga clic en la ficha, seleccione en la parte superior de esta página o vea [métodos de creación de clúster](hdinsight-provision-clusters.md#cluster-creation-methods).

##<a name="prerequisites"></a>Requisitos previos:

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Antes de comenzar las instrucciones de este artículo, debe tener el siguiente:

- Una suscripción de Azure. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

### <a name="access-control-requirements"></a>Requisitos de control de acceso

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-clusters"></a>Crear clústeres


**Para crear un clúster de HDInsight**

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Haga clic en **nuevo**, haga clic en **Análisis de datos**y, a continuación, haga clic en **HDInsight**.

    ![Crear un nuevo clúster en el Portal de Azure] (./media/hdinsight-provision-clusters/HDI.CreateCluster.1.png "Crear un nuevo clúster en el Portal de Azure")

3. Escriba o seleccione los valores siguientes:

    * **Nombre de clúster**: escriba un nombre para el clúster. Aparece una marca de verificación verde junto al nombre de clúster si el nombre está disponible.

    * **Tipo de clúster**: seleccione **Hadoop**. Otras opciones inclue **HBase**, **tormenta**y **motor**.

        > [AZURE.IMPORTANT] HDInsight formar una gran variedad de tipos, que corresponden a la carga de trabajo o la tecnología de clúster está optimizado para clústeres. No hay ningún método para crear un clúster que combina varios tipos, como tormenta y HBase en un clúster.

    * **Sistema operativo del clúster**: seleccione **Windows**. Para crear un clúster de base de Linux, seleccione **Linux**.
    * **Versión**: consulte [HDInsight versiones](hdinsight-component-versioning.md).
    * **Suscripción**: seleccione la suscripción de Azure que se usará para la creación de este clúster.
    * **Grupo de recursos**: seleccione una existente o crear un nuevo grupo de recursos. Esta entrada predeterminada uno de sus grupos de recursos existentes, si alguno está disponible.
    * **Credenciales**: configurar el nombre de usuario y la contraseña para el usuario de Hadoop (HTTP usuario). Si habilita Escritorio remoto para el clúster, debe configurar el nombre de usuario de escritorio remoto de usuario y contraseña y una fecha de expiración de la cuenta. En la parte inferior para guardar los cambios, haga clic en **Seleccionar** .

        ![Proporcionar credenciales de clúster] (./media/hdinsight-provision-clusters/HDI.CreateCluster.3.png "Proporcionar credenciales de clúster")

    * **Origen de datos**: crear una nueva o seleccione una cuenta de Azure almacenamiento existente que se utilizará como el sistema de archivo predeterminado para el clúster.

        ![Módulo de origen de datos] (./media/hdinsight-provision-clusters/HDI.CreateCluster.4.png "Configuración de origen de datos de proporcionar")

        * **Método de selección**: establezca este valor en el cuadro **de todas las suscripciones** para habilitar la exploración de cuentas de almacenamiento de todas las suscripciones. Establezca **Tecla de acceso** si desea escribir el **Nombre de almacenamiento** y una **Tecla de acceso** de una cuenta de almacenamiento existente.
        * **Seleccione cuenta de almacenamiento y crear nuevos**: haga clic en **Seleccionar cuenta de almacenamiento** para examinar y seleccione una cuenta existente de almacenamiento que desea asociar con el clúster. O bien, haga clic en **Crear nuevo** para crear una nueva cuenta de almacenamiento. Utilice el campo que aparece para especificar el nombre de la cuenta de almacenamiento. Si el nombre está disponible, aparecerá una marca de verificación verde.
        * **Elija predeterminado contenedor**: Use esta opción para escribir el nombre del contenedor predeterminado que se utilizará para el clúster. Aunque puede escribir cualquier nombre aquí, recomendamos con el mismo nombre que el clúster de modo que pueda reconocer fácilmente que se utiliza el contenedor para este clúster específico.
        * **Ubicación**: la región geográfica que está en la cuenta de almacenamiento o se creará en. Esta ubicación determinará la ubicación de clúster.  El clúster y su cuenta de almacenamiento predeterminada deben ubicar en el mismo centro de datos de Azure.
    
    * **Niveles de precios de nodo**: establecer el número de nodos de trabajo que necesita para el clúster. El costo estimado del clúster se mostrarán en el módulo.
  

        ![Módulo de niveles de precios de nodo] (./media/hdinsight-provision-clusters/HDI.CreateCluster.5.png "Especificar número de nodos de clúster")


    * **Configuración opcional** para seleccionar la versión de clúster, así como configurar otras opciones opcionales como unirse a una **Red Virtual**, configurar un **Metastore externo** para mantener los datos de la sección y Oozie, utilice acciones de secuencia de comandos para personalizar un clúster para instalar componentes personalizados, o cuentas de almacenamiento adicional con el clúster.

    * **HDInsight versión**: seleccione la versión que desea usar para el clúster. Para obtener más información, vea [versiones de clúster HDInsight](hdinsight-component-versioning.md).
    * **Una red virtual**: seleccione una red virtual Azure y la subred si desea colocar el clúster en una red virtual.  

        ![Módulo de red virtual] (./media/hdinsight-provision-clusters/HDI.CreateCluster.6.png "Detalles de la red virtual de especificar")

        Para obtener información sobre cómo usar HDInsight con una red Virtual, incluidos los requisitos de configuración específica de la red Virtual, vea [ampliar HDInsight capbilities mediante una red Virtual de Azure](hdinsight-extend-hadoop-virtual-network.md).
  

        
    * **Metastores externos**: especifique una base de datos de SQL Azure para almacenar los metadatos de la sección y Oozie asociado con el clúster.
 
        > [AZURE.NOTE] Configuración de Metastore no está disponible para los tipos de clúster HBase.

    ![Módulo de metastores personalizada] (./media/hdinsight-provision-clusters/HDI.CreateCluster.7.png "Especificar externos metastores")

    Para **usar una base de datos de SQL existente para subárbol** metadatos, haga clic en **Sí**, seleccione una base de datos SQL y, a continuación, proporcione el nombre de usuario y la contraseña para la base de datos. Repita estos pasos si desea **usar una base de datos de SQL existente para metadatos Oozie**. Haga clic en **Seleccionar** hasta que se encuentra en el módulo de **Configuración opcional** .

    >[AZURE.NOTE] La base de datos de SQL Azure utilizado para el metastore debe permitir la conectividad con otros servicios de Azure, incluidos HDInsight de Azure. En el panel de la base de datos de SQL Azure, en el lado derecho, haga clic en el nombre del servidor. Éste es el servidor en el que se ejecuta la instancia de base de datos SQL. Una vez que se muestran en la vista de servidor, haga clic en **Configurar**y para los **Servicios de Azure**, haga clic en **Sí**y, a continuación, haga clic en **Guardar**.

            &nbsp;

            > [AZURE.IMPORTANT] Al crear un metastore, no use un nombre de base de datos que contiene guiones o guiones, ya que puede causar el proceso de creación de clúster no se realice correctamente.
        
        * **Script Actions** if you want to use a custom script to customize a cluster, as the cluster is being created. For more information about script actions, see [Customize HDInsight clusters using Script Action](hdinsight-hadoop-customize-cluster.md). On the Script Actions blade provide the details as shown in the screen capture.
    

            ![Script action blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.8.png "Specify script action")


        * **Azure Storage Keys**: Specify additional storage accounts to associate with the cluster. In the **Azure Storage Keys** blade, click **Add a storage key**, and then select an existing storage account or create a new account.
    

            ![Additional storage blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.9.png "Specify additional storage accounts")


4. Haga clic en **crear**. Seleccionar **Anclar al Startboard** agregará un mosaico de clúster a la Startboard del Portal. El icono indicará que se va a crear el clúster y cambiará para mostrar el icono de HDInsight una vez completada la creación.
    
    Esta acción tardará algún tiempo para que el clúster creado, normalmente unos 15 minutos. Use el mosaico de la Startboard o la entrada de **notificaciones** de la izquierda de la página para comprobar el proceso de aprovisionamiento.
    

5. Una vez completada la creación, haga clic en el mosaico de clúster desde el Startboard para iniciar el módulo de clúster. El módulo de clúster proporciona información esencial sobre el clúster como el nombre, el grupo de recursos que pertenece, la ubicación, el sistema operativo, la dirección URL para el panel de clúster, etcetera.


    ![Módulo de clúster] (./media/hdinsight-provision-clusters/HDI.Cluster.Blade.png "Propiedades de clúster")


    Comprender los iconos en la parte superior de este módulo y, en la sección **Essentials** , utilice la siguiente:


    * **Configuración** y **Todas las configuraciones**: muestra el módulo de **configuración** para el clúster, que le permite acceder a la información de configuración detallada para el clúster.
    * **Paneles**, el **Panel clúster**y la **dirección URL**: se trata de todas las formas de tener acceso al panel de clúster, que es un portal Web para ejecutar trabajos en el clúster.
    * **Escritorio remoto**: le permite habilitar o deshabilitar Escritorio remoto en los nodos de clúster.
    * **Escala de clúster**: le permite cambiar el número de nodos de trabajo para este clúster.
    * **Eliminar**: elimina el clúster HDInsight.
    * **Tutorial rápido** (![icono de nube y thunderbolt = tutorial](./media/hdinsight-provision-clusters/quickstart.png)): muestra la información que le ayudarán a empezar a usar HDInsight.
    * **Usuarios** (![icono usuarios](./media/hdinsight-provision-clusters/users.png)): permite establecer permisos para la _administración de portal_ de este clúster para otros usuarios de su suscripción de Azure.
    

        > [AZURE.IMPORTANT] Este _sólo_ afecta al acceso y permisos para este clúster en el Portal y no tiene ningún efecto en la que puede conectar a o enviar trabajos al clúster HDInsight.
        
    * **Etiquetas** (![icono de la etiqueta](./media/hdinsight-provision-clusters/tags.png)): etiquetas permite establecer pares de clave/valor para definir una taxonomía personalizada de los servicios de nube. Por ejemplo, puede crear una clave con el nombre de __proyecto__y, a continuación, use un valor común para todos los servicios asociados a un proyecto específico.

##<a name="customize-clusters"></a>Personalizar clústeres

- Consulte [clústeres personalizar HDInsight con inicio](hdinsight-hadoop-customize-cluster-bootstrap.md).
- Consulte [clústeres basados en Windows personalizar HDInsight con la acción de secuencia de comandos](hdinsight-hadoop-customize-cluster.md).

##<a name="next-steps"></a>Pasos siguientes
En este artículo, ha aprendido diversas maneras para crear un clúster de HDInsight. Para obtener más información, consulte los artículos siguientes:

* [Introducción a HDInsight de Azure](hdinsight-hadoop-linux-tutorial-get-started.md) : Obtenga información sobre cómo empezar a trabajar con el clúster HDInsight
* [Enviar Hadoop mediante programación de trabajos](hdinsight-submit-hadoop-jobs-programmatically.md) - Aprenda mediante programación enviar trabajos a HDInsight
* [Administrar clústeres de Hadoop en HDInsight a través del Portal de Azure](hdinsight-administer-use-management-portal.md)



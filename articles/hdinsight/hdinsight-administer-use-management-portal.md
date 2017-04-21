<properties
    pageTitle="Administrar clústeres Hadoop en HDInsight con el portal de Azure | Microsoft Azure"
    description="Obtenga información sobre cómo administrar HDInsight Service. Crear un clúster de HDInsight, abra la consola de JavaScript interactiva y abrir la consola de comandos de Hadoop."
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/14/2016"
    ms.author="jgao"/>

# <a name="manage-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>Administrar clústeres Hadoop en HDInsight con el portal de Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Con el [portal de Azure][azure-portal], puede crear clústeres Hadoop en HDInsight de Azure, cambiar la contraseña de usuario Hadoop y habilitar el protocolo de escritorio remoto (RDP) para que pueda acceder a la consola de comandos de Hadoop en el clúster.

La información en este artículo se aplica solo a clústeres HDInsight basado en la ventana. Para obtener información acerca de cómo administrar clústeres basados en Linux, haga clic en el selector de tabulaciones anterior.

Haga clic en el selector de tabulaciones para obtener información sobre cómo crear clústeres Hadoop en HDInsight con otras herramientas. 

**Requisitos previos**

Antes de comenzar este artículo, debe tener el siguiente:

- **Suscripción de un Azure**. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Cuenta de almacenamiento de azure** - HDInsight un clúster usa un contenedor de almacenamiento de blobs de Windows Azure como el sistema de archivos de forma predeterminada. Para obtener más información sobre cómo el almacenamiento de blobs de Windows Azure ofrece una experiencia perfecta con clústeres HDInsight, consulte [Usar almacenamiento de blobs de Azure con HDInsight](hdinsight-hadoop-use-blob-storage.md). Para obtener más información sobre la creación de una cuenta de almacenamiento de Azure, consulte [cómo crear una cuenta de almacenamiento](../storage/storage-create-storage-account.md).

##<a name="open-the-portal"></a>Abra el portal.

1. Inicie sesión en [https://portal.azure.com](https://portal.azure.com).
2. Después de abrir el portal, puede:

    - Haga clic en **nuevo** en el menú izquierdo para crear un nuevo clúster:
    
        ![botón nuevo de clúster HDInsight](./media/hdinsight-administer-use-management-portal/azure-portal-new-button.png)
    - Menú de la izquierda, haga clic en **Clústeres HDInsight** .
    
        ![Botón de clúster de HDInsight portal Azure](./media/hdinsight-administer-use-management-portal/azure-portal-hdinsight-button.png)

    Si **HDInsight** no aparece en el menú de la izquierda, haga clic en **Examinar**. 

    ![Botón de clúster de examinar portal Azure](./media/hdinsight-administer-use-management-portal/azure-portal-browse-button.png)

##<a name="create-clusters"></a>Crear clústeres

Para las instrucciones de creación con el Portal, consulte [crear HDInsight clústeres](hdinsight-provision-clusters.md#create-using-the-preview-portal).

HDInsight funciona con los componentes de una amplia gama de Hadoop. Para la lista de los componentes que se hayan verificado y compatibles, consulte [¿qué versión de Hadoop es en HDInsight de Azure](hdinsight-component-versioning.md). Puede personalizar HDInsight mediante una de las siguientes opciones:

- Usar la acción de secuencia de comandos para ejecutar scripts personalizados que pueden personalizar un clúster para cambiar la configuración de clúster o instalar componentes personalizados, como Giraph o Solr. Para obtener más información, consulte [clúster de personalizar HDInsight con la acción de secuencia de comandos](hdinsight-hadoop-customize-cluster.md).
- Usar los parámetros de personalización de clúster en el SDK de .NET HDInsight o Azure PowerShell durante la creación de clúster. Estos cambios de configuración, a continuación, se mantienen a través de la duración del clúster y no se ven afectados por reimages de nodo de clúster plataforma Windows Azure realiza periódicamente para realizar el mantenimiento. Para obtener más información sobre el uso de los parámetros de personalización de clúster, consulte [crear HDInsight clústeres](hdinsight-provision-clusters.md).
- Algunos componentes de Java nativas, como Mahout y en cascada, se pueden ejecutar en el clúster como archivos JAR. Estos archivos JAR pueden distribuidos al almacenamiento de blobs de Windows Azure y se envía a HDInsight clústeres a través de mecanismos de envío de trabajo Hadoop. Para obtener más información, vea [Hadoop enviar trabajos mediante programación](hdinsight-submit-hadoop-jobs-programmatically.md).

    >[AZURE.NOTE] Si tiene problemas para implementar archivos JAR para clústeres HDInsight o llamar a archivos JAR en clústeres HDInsight, póngase en contacto con el [Soporte técnico de Microsoft](https://azure.microsoft.com/support/options/).

    > En cascada no es compatible con HDInsight y no es elegible para Microsoft Support. Para obtener listas de componentes compatibles, vea [Novedades en las versiones de clúster proporcionadas por HDInsight?](hdinsight-component-versioning.md).

No se admite la instalación de software personalizada en el clúster mediante el uso de la conexión a Escritorio remoto. Debe evitar almacenar los archivos en las unidades del nodo principal, como se perderán si necesita volver a crear los clústeres. Se recomienda almacenar archivos en el almacenamiento de blobs de Windows Azure. Almacenamiento de blobs es persistente.

##<a name="list-and-show-clusters"></a>Lista y mostrar clústeres

1. Inicie sesión en [https://portal.azure.com](https://portal.azure.com).
2. Menú de la izquierda, haga clic en **Clústeres HDInsight** .
3. Haga clic en el nombre del clúster. Si la lista de clúster es larga, puede usar el filtro en la parte superior de la página.
4. Haga doble clic en un clúster de la lista para mostrar los detalles.

    **Menú y essentials**:

    ![Azure essentials de clúster HDInsight portal](./media/hdinsight-administer-use-management-portal/hdinsight-essentials.png)
    
    - Para personalizar el menú, haga clic en cualquier lugar en el menú y, a continuación, haga clic en **Personalizar**.
    - **Configuración** y **Todas las configuraciones**: muestra el módulo de **configuración** para el clúster, que le permite acceder a la información de configuración detallada para el clúster.
    - Dirección URL de **paneles**, **Clúster paneles** y **: se trata de todas las formas de tener acceso al panel de clúster, que es Ambari Web para clústeres basados en Linux. - **Shell seguro **: muestra las instrucciones para conectar con el clúster con conexión de Shell seguro (SSH).
    - **Escala de clúster**: le permite cambiar el número de nodos de trabajo para este clúster.
    - **Eliminar**: elimina el clúster.
    - **Tutorial rápido (![icono de nube y thunderbolt = tutorial](./media/hdinsight-administer-use-portal-linux/quickstart.png))**: muestra la información que le ayudarán a empezar a usar HDInsight.
    - **Los usuarios (![icono usuarios](./media/hdinsight-administer-use-portal-linux/users.png))**: permite establecer permisos para la _administración de portal_ de este clúster para otros usuarios de su suscripción de Azure.
    
        > [AZURE.IMPORTANT] Este _sólo_ afecta al acceso y permisos para este clúster en el portal de Azure y no tiene ningún efecto en la que puede conectar a o enviar trabajos al clúster HDInsight.
    - **Etiquetas (![icono de la etiqueta](./media/hdinsight-administer-use-portal-linux/tags.png))**: etiquetas permite establecer pares de clave/valor para definir una taxonomía personalizada de los servicios de nube. Por ejemplo, puede crear una clave con el nombre de __proyecto__y, a continuación, use un valor común para todos los servicios asociados a un proyecto específico.
    - **Vistas de Ambari**: vínculos a Ambari Web.
    
    > [AZURE.IMPORTANT] Para administrar los servicios proporcionados por el clúster HDInsight, debe utilizar Ambari Web o la API de REST de Ambari. Para obtener más información sobre cómo usar Ambari, vea [clústeres administrar HDInsight con Ambari](hdinsight-hadoop-manage-ambari.md).

    **Uso**:
    
    ![Uso de clúster de Azure hdinsight portal](./media/hdinsight-administer-use-management-portal/hdinsight-portal-cluster-usage.png)
    
5. Haga clic en **configuración**.

    ![Uso de clúster de Azure hdinsight portal](./media/hdinsight-administer-use-management-portal/hdinsight.portal.cluster.settings.png)

    - **Propiedades**: ver las propiedades de clúster.
    - **Identidad AAD de clúster**: 
    - **Teclas de almacenamiento de Azure**: ver la cuenta de almacenamiento predeterminada y su clave. La cuenta de almacenamiento es configuración durante el proceso de creación de clúster.
    - **Inicio de sesión de clúster**: cambiar el nombre de usuario de clúster HTTP y la contraseña.
    - **Metastores externos**: vea la sección y Oozie metastores. Solo se puede configurar la metastores durante el proceso de creación de clúster.
    - **Escala de clúster**: aumentar y disminuir el número de nodos del clúster de trabajo.
    - **Escritorio remoto**: habilitar y deshabilitar el acceso de escritorio remoto (RDP) y configurar el nombre de usuario RDP.  El nombre de usuario RDP debe ser diferente del nombre de usuario HTTP.
    - **Partner de registro**:
    
    > [AZURE.NOTE] Se trata de una lista genérica de las configuraciones disponibles; No todos ellos se presentarán para todos los tipos de clúster.

6. Haga clic en **Propiedades**:

    Las listas de propiedades de las siguientes acciones:
    
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
    - **Origen de datos principal**. La cuenta de almacenamiento de blobs de Windows Azure que se utiliza como el sistema de archivos Hadoop de forma predeterminada.
    - **Nivel de precios de nodos de trabajo**.
    - **Nivel de precios de nodo de cabeza**.

##<a name="delete-clusters"></a>Eliminar clústeres

Eliminar un clúster no elimina la cuenta de almacenamiento predeterminada o cualquier cuenta de almacenamiento vinculado. Puede volver a crear el clúster con las mismas cuentas de almacenamiento y el mismo metastores.

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

    ![Equilibrar de escala de tormenta HDInsight](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.storm.rebalance.png)

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

    ![Escala de HDInsight Hadoop HBase tormenta motor](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.png)

##<a name="pauseshut-down-clusters"></a>Pausar o apagado clústeres

La mayoría de los trabajos de Hadoop son procesos que sólo se ha encontrado en ocasiones. La mayoría de los clústeres Hadoop, hay grandes períodos de tiempo que el clúster no se utiliza para el procesamiento. Con HDInsight, los datos se almacenan en el almacenamiento de Azure, por lo que puede eliminar un clúster cuando no está en uso.
También se cargan un clúster HDInsight, incluso cuando no está en uso. Dado que muchas veces más que los cargos para el almacenamiento de los cargos para el clúster, tiene sentido económico para eliminar clústeres cuando no están en uso.

Hay muchas formas puede programar el proceso:

- Generador de Azure datos de usuario. Vea [Servicios de Azure HDInsight vinculados](../data-factory/data-factory-compute-linked-services.md) y [transformar y analizar con el generador de datos de Azure](../data-factory/data-factory-data-transformation-activities.md) para servicios de HDInsight vinculado a petición y automática definidos.
- Usar PowerShell Azure.  Vea [analizar datos de retrasos de vuelo](hdinsight-analyze-flight-delay-data.md).
- Utilizar CLI Azure. Consulte [clústeres administrar HDInsight mediante CLI de Azure](hdinsight-administer-use-command-line.md).
- Use HDInsight .NET SDK. Consulte [Hadoop enviar trabajos](hdinsight-submit-hadoop-jobs-programmatically.md).

Para la información de precios, consulte [HDInsight de precios](https://azure.microsoft.com/pricing/details/hdinsight/). Para eliminar un clúster desde el Portal, vea [Eliminar clústeres](#delete-clusters)

##<a name="change-cluster-username"></a>Nombre de usuario de clúster de cambio

Un clúster de HDInsight puede tener dos cuentas de usuario. La cuenta de usuario de clúster HDInsight se crea durante el proceso de creación. También puede crear una cuenta de usuario RDP para obtener acceso al clúster a través de RDP. Vea [Habilitar Escritorio remoto](#connect-to-hdinsight-clusters-by-using-rdp).

**Para cambiar el nombre de usuario de clúster HDInsight y la contraseña**

1. Inicie sesión en el [Portal de][azure-portal].
2. Haga clic en **Examinar todos** en el menú de la izquierda, haga clic en **HDInsight clústeres**, haga clic en el nombre del clúster.
3. Haga clic en **configuración** en el menú superior y, a continuación, haga clic en **Inicio de sesión de clúster**.
4. Si se ha habilitado el **Inicio de sesión de clúster** , debe haga clic en **Deshabilitar**y, a continuación, haga clic en **Habilitar** antes de poder cambiar el nombre de usuario y la contraseña...
4. Cambiar el **Nombre de inicio de sesión de clúster** o la **Contraseña de inicio de sesión de clúster**y, a continuación, haga clic en **Guardar**.

    ![HDInsight cambiar clúster nombreDeUsuario contraseña http usuario](./media/hdinsight-administer-use-management-portal/hdinsight.portal.change.username.password.png)

##<a name="grantrevoke-access"></a>Conceder o denegar el acceso a

HDInsight clústeres tienen los siguientes servicios de web HTTP (todos estos servicios tienen extremos REST):

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton

De forma predeterminada, estos servicios se le ha concedido acceso. Se puede revocar/conceder el acceso desde el portal de Azure.

>[AZURE.NOTE] Conceder y revocar el acceso, se restablece el clúster nombre de usuario y contraseña.

**Para conceder o revocar el acceso de servicios web HTTP**

1. Inicie sesión en el [Portal de][azure-portal].
2. Haga clic en **Examinar todos** en el menú de la izquierda, haga clic en **HDInsight clústeres**, haga clic en el nombre del clúster.
3. Haga clic en **configuración** en el menú superior y, a continuación, haga clic en **Inicio de sesión de clúster**.
4. Si se ha habilitado el **Inicio de sesión de clúster** , debe haga clic en **Deshabilitar**y, a continuación, haga clic en **Habilitar** antes de poder cambiar el nombre de usuario y la contraseña...
6. **Nombre de usuario de inicio de sesión de clúster** y la **Contraseña de inicio de sesión de clúster**, escriba el nuevo nombre de usuario y la contraseña (respectivamente) para el clúster.
7. Haga clic en **Guardar**.

    ![HDInsight grand quitar acceso del servicio web http](./media/hdinsight-administer-use-management-portal/hdinsight.portal.change.username.password.png)




##<a name="find-the-default-storage-account"></a>Buscar la cuenta de almacenamiento predeterminada

Cada clúster HDInsight tiene una cuenta de almacenamiento predeterminada. La cuenta de almacenamiento predeterminada y sus claves para un clúster aparece en **configuración**/**Propiedades**/**Claves de almacenamiento de Azure**. Consulte [clústeres de lista y mostrar](#list-and-show-clusters).

    
##<a name="find-the-resource-group"></a>Busque el grupo de recursos 

En el modo de administrador de recursos de Azure, se crea cada clúster HDInsight con un grupo de recursos de Azure. El grupo de recursos de Azure que pertenece un clúster aparece en:

- La lista de clúster tiene una columna de **Grupo de recursos** .
- Mosaico de clúster **esenciales** .  

Consulte [clústeres de lista y mostrar](#list-and-show-clusters).
   
##<a name="open-hdinsight-query-console"></a>Abra la consola de consulta HDInsight

La consola de HDInsight consulta incluye las siguientes características:

- **Introducción a galería**: para usar la galería, consulte [Obtener Hadoop mediante la Galería de introducción de introducción de HDInsight de Azure](hdinsight-learn-hadoop-use-sample-gallery.md).
- **Editor de sección**: A web interfaz gráfica de usuario enviar trabajos de sección.  Consulte [Ejecutar subárbol consultas con la consola de consulta](hdinsight-hadoop-use-hive-query-console.md).

    ![Editor de subárbol portal HDInsight](./media/hdinsight-administer-use-management-portal/hdinsight-hive-editor.png)

- **Historial de trabajos**: Monitor Hadoop trabajos.  

    ![Historial de trabajos de portal de HDInsight](./media/hdinsight-administer-use-management-portal/hdinsight-job-history.png)

    Haga clic en el **Nombre de la consulta** para mostrar los detalles incluidas las propiedades de trabajo, **Consulta de la tarea**, y ** salida de trabajo. También puede descargar la consulta y los resultados en su estación de trabajo.

- **Explorador de archivos**: examinar la cuenta de almacenamiento predeterminada y las cuentas de almacenamiento vinculado.

    ![Examinar de explorador de archivos portal HDInsight](./media/hdinsight-administer-use-management-portal/hdinsight-file-browser.png)

    En la captura de pantalla del **<Account>** tipo indica que el elemento es una cuenta de almacenamiento de Azure.  Haga clic en el nombre de cuenta para examinar los archivos.
    
- **Interfaz de usuario de Hadoop**.

    ![HDInsight portal Hadoop UI](./media/hdinsight-administer-use-management-portal/hdinsight-hadoop-ui.png)
    
    Desde **Hadoop interfaz de usuario*, puede examinar los archivos y compruebe los registros. 

- **Interfaz de usuario del hilo**.

    ![HDInsight portal hilo IU](./media/hdinsight-administer-use-management-portal/hdinsight-yarn-ui.png)

##<a name="run-hive-queries"></a>Ejecutar consultas de la sección

A ejecutó subárbol trabajos desde el Portal, haga clic en **Editor de sección** en la consola de consulta HDInsight. Consulte [consola de consulta HDInsight abierta](#open-hdinsight-query-console).

##<a name="monitor-jobs"></a>Supervisar los trabajos.

Para supervisar trabajos desde el Portal, haga clic en **Historial de trabajos** en la consola de consulta HDInsight. Consulte [consola de consulta HDInsight abierta](#open-hdinsight-query-console).

##<a name="browse-files"></a>Examinar los archivos

Para examinar los archivos almacenados en la cuenta de almacenamiento predeterminada y las cuentas de almacenamiento vinculado, haga clic en **Explorador de archivos** en la consola de consulta HDInsight. Consulte [consola de consulta HDInsight abierta](#open-hdinsight-query-console).

También puede usar la utilidad **Examinar el sistema de archivos** de la **Interfaz de usuario de Hadoop** en la consola de HDInsight.  Consulte [consola de consulta HDInsight abierta](#open-hdinsight-query-console).



##<a name="monitor-cluster-usage"></a>Supervisar el uso de clúster

La sección __uso__ del módulo de clúster HDInsight muestra información sobre el número de núcleos disponibles para su suscripción para su uso con HDInsight, así como el número de núcleos asignada a este clúster y cómo se asignan para los nodos de este clúster. Consulte [clústeres de lista y mostrar](#list-and-show-clusters).

> [AZURE.IMPORTANT] Para supervisar los servicios proporcionados por el clúster HDInsight, debe utilizar Ambari Web o la API de REST de Ambari. Para obtener más información sobre cómo usar Ambari, vea [clústeres administrar HDInsight con Ambari](hdinsight-hadoop-manage-ambari.md)


##<a name="open-hadoop-ui"></a>Abra la interfaz de usuario de Hadoop

Para supervisar el clúster, busque el sistema de archivos y compruebe los registros, haga clic en **La interfaz de usuario de Hadoop** en la consola de consulta HDInsight. Consulte [consola de consulta HDInsight abierta](#open-hdinsight-query-console).

##<a name="open-yarn-ui"></a>Abrir la interfaz de usuario del hilo

Para usar la interfaz de usuario del hilo, haga clic en **Interfaz de usuario del hilo** en la consola de consulta HDInsight. Consulte [consola de consulta HDInsight abierta](#open-hdinsight-query-console).

##<a name="connect-to-clusters-using-rdp"></a>Conectarse a clústeres mediante RDP

Las credenciales para el clúster que proporciona en su creación conceder acceso a los servicios en el clúster, pero no para el clúster a través de escritorio remoto. Puede activar el acceso de escritorio remoto cuando aprovisionar un clúster o después de que se le proporciona un clúster. Instrucciones sobre cómo habilitar Escritorio remoto durante la creación, vea [crear HDInsight clúster](hdinsight-provision-clusters.md).

**Para habilitar Escritorio remoto**

1. Inicie sesión en el [Portal de][azure-portal].
2. Haga clic en **Examinar todos** en el menú de la izquierda, haga clic en **HDInsight clústeres**, haga clic en el nombre del clúster.
3. Haga clic en **configuración** en el menú superior y, a continuación, haga clic en **Escritorio remoto**.
4. Escriba **Caduca**, **Nombre de usuario de escritorio remoto** y **Contraseña de escritorio remoto**y, a continuación, haga clic en **Habilitar**.

    ![hdinsight habilitar deshabilitar configurar Escritorio remoto](./media/hdinsight-administer-use-management-portal/hdinsight.portal.remote.desktop.png)

    Los valores predeterminados de caduca es una semana.
> [AZURE.NOTE] También puede usar el SDK de .NET HDInsight para habilitar Escritorio remoto en un clúster. Use el método de **EnableRdp** en el objeto de cliente de HDInsight de la siguiente manera: **cliente. EnableRdp (nombreDeClúster, ubicación, "rdpuser", "rdppassword", DateTime.Now.AddDays(6))**. Del mismo modo, para deshabilitar Escritorio remoto en el clúster, puede usar al cliente de **. DisableRdp (nombreDeClúster, ubicación)**. Para obtener más información sobre estos métodos, vea [Referencia de HDInsight .NET SDK](http://go.microsoft.com/fwlink/?LinkId=529017). Esto es aplicable sólo para clústeres HDInsight que se ejecutan en Windows.

**Para conectarse a un clúster mediante RDP**

1. Inicie sesión en el [Portal de][azure-portal].
2. Haga clic en **Examinar todos** en el menú de la izquierda, haga clic en **HDInsight clústeres**, haga clic en el nombre del clúster.
3. Haga clic en **configuración** en el menú superior y, a continuación, haga clic en **Escritorio remoto**.
4. Haga clic en **Conectar** y siga las instrucciones. Si la conexión está deshabilitado, debe habilitar en primer lugar. Asegúrese de usar el escritorio remoto nombre de usuario y la contraseña.  No puede usar las credenciales de usuario de clúster.


##<a name="open-hadoop-command-line"></a>Abra la línea de comandos de Hadoop

Para conectarse al clúster utilizando Escritorio remoto y utilizar la línea de comandos de Hadoop, debe primero Habilitar acceso de escritorio remoto al clúster como se describe en la sección anterior.

**Para abrir una línea de comandos de Hadoop**

1. Conectarse al clúster mediante Escritorio remoto.
8. En el escritorio, haga doble clic en **línea de comandos de Hadoop**.

    ![HDI. HadoopCommandLine][image-hadoopcommandline]

    Para obtener más información acerca de los comandos de Hadoop, vea [referencia de comandos de Hadoop](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/CommandsManual.html).

En la captura de pantalla anterior, el nombre de la carpeta tiene el número de versión Hadoop incrustado. El número de versión puede cambiar en función de la versión de los componentes de Hadoop instalados en el clúster. Puede utilizar variables de entorno Hadoop para hacer referencia a las carpetas. Por ejemplo:

    cd %hadoop_home%
    cd %hive_home%
    cd %hbase_home%
    cd %pig_home%
    cd %sqoop_home%
    cd %hcatalog_home%
    
##<a name="next-steps"></a>Pasos siguientes
En este artículo, ha visto cómo crear un clúster de HDInsight a través del Portal y cómo abrir la herramienta de línea de comandos de Hadoop. Para obtener más información, consulte los artículos siguientes:

* [Administrar HDInsight con PowerShell Azure](hdinsight-administer-use-powershell.md)
* [Administrar HDInsight mediante CLI Azure](hdinsight-administer-use-command-line.md)
* [Crear clústeres HDInsight](hdinsight-provision-clusters.md)
* [Enviar trabajos de Hadoop mediante programación](hdinsight-submit-hadoop-jobs-programmatically.md)
* [Introducción a HDInsight de Azure](hdinsight-hadoop-linux-tutorial-get-started.md)
* [¿Qué versión de Hadoop es en HDInsight de Azure?](hdinsight-component-versioning.md)

[azure-portal]: https://portal.azure.com
[image-hadoopcommandline]: ./media/hdinsight-administer-use-management-portal/hdinsight-hadoop-command-line.png "Línea de comandos de Hadoop"

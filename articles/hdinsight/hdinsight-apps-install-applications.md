<properties
    pageTitle="Instalar aplicaciones de Hadoop en HDInsight | Microsoft Azure"
    description="Obtenga información sobre cómo instalar aplicaciones de HDInsight en HDInsight aplicaciones."
    services="hdinsight"
    documentationCenter=""
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="09/14/2016"
    ms.author="jgao"/>

# <a name="install-hdinsight-applications"></a>Instalar aplicaciones de HDInsight

Una aplicación de HDInsight es una aplicación que los usuarios pueden instalar en un clúster de HDInsight basados en Linux. Estas aplicaciones se pueden desarrollar por Microsoft, proveedores de software independientes (ISV) o por usted mismo. En este artículo, obtendrá información sobre cómo instalar una aplicación publicada. Para instalar la aplicación, consulte [instalar aplicaciones personalizadas de HDInsight](hdinsight-apps-install-custom-applications.md). 

Actualmente no hay una aplicación publicada:

- **Datameer**: [Datameer](http://www.datameer.com/documentation/display/DAS50/Home?ls=Partners&lsd=Microsoft&c=Partners&cd=Microsoft) ofrece analistas de forma interactiva para descubrir, analizar y visualizar los resultados en Big Data. Extraer en orígenes de datos adicionales con facilidad para descubrir nuevas relaciones y obtener las respuestas que necesita rápidamente.

>[AZURE.NOTE] Datameer está actualmente solo se admiten en clústeres de versión 3.2 HDInsight de Azure.

Las instrucciones de este artículo usar el portal de Azure. También puede exportar la plantilla de administrador de recursos de Azure desde el portal u obtener una copia de la plantilla de administrador de recursos de los proveedores y usar PowerShell de Azure y Azure CLI para implementar la plantilla.  Vea [Hadoop basados en Linux crear clústeres en HDInsight con plantillas de administrador de recursos](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

## <a name="prerequisites"></a>Requisitos previos

Si desea instalar aplicaciones HDInsight en un clúster de HDInsight existente, debe tener un clúster de HDInsight. Para crear uno, consulte [crear clústeres](hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster). También puede instalar aplicaciones de HDInsight cuando se crea un clúster de HDInsight.

## <a name="install-applications-to-existing-clusters"></a>Instalar aplicaciones para clústeres existentes

El procedimiento siguiente muestra cómo instalar aplicaciones de HDInsight a un clúster de HDInsight existente.

**Para instalar una aplicación de HDInsight**

1. Inicie sesión en el [portal de Azure](https://portal.azure.com).
2. En el menú de la izquierda, haga clic en **Clústeres HDInsight** .  Si no lo ves, haga clic en **Examinar**y, a continuación, haga clic en **Clústeres HDInsight**.
3. Haga clic en un clúster de HDInsight.  Si no tiene una, debe crearla primero.  consulte [crear clústeres](hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster).
4. En el módulo de **configuración** , haga clic en **aplicaciones** en la categoría **General** . El módulo de **Aplicaciones instaladas** muestra todas las aplicaciones instaladas. 

    ![menú de portal de aplicaciones hdinsight](./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png)

5. En el menú de la hoja, haga clic en **Agregar** . 

    ![aplicaciones de hdinsight aplicaciones instaladas](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps.png)

    Deberá ver una lista de aplicaciones de HDInsight existentes.

    ![hdinsight aplicaciones disponibles](./media/hdinsight-apps-install-applications/hdinsight-apps-list.png)

6. Haga clic en una de las aplicaciones, acepte las condiciones legales y, a continuación, haga clic en **Seleccionar**.

Puede ver el estado de instalación de las notificaciones de portal (haga clic en el icono de campana en el portal de la parte superior). Después de instala la aplicación, la aplicación aparecerá en el módulo de aplicaciones instaladas.

## <a name="install-applications-during-cluster-creation"></a>Instalar aplicaciones durante la creación de un clúster

Tiene la opción de instalar aplicaciones HDInsight cuando se crea un clúster. Durante el proceso de HDInsight aplicaciones se instalan después de que el clúster se crea y se encuentra en el estado de ejecución. El procedimiento siguiente muestra cómo instalar aplicaciones de HDInsight cuando se crea un clúster.

**Para instalar una aplicación de HDInsight**

1. Inicie sesión en el [portal de Azure](https://portal.azure.com).
2. Haga clic en **nuevo**, haga clic en **datos + análisis**y, a continuación, haga clic en **HDInsight**.
3. Escriba **El nombre de clúster**: este nombre debe ser único global.
4. Haga clic en la **suscripción** para seleccionar la suscripción de Azure que se utilizará para el clúster.
5. Haga clic en **Seleccionar tipo de clúster**y, a continuación, seleccione:

    - **Tipo de clúster**: si no sabe qué elegir, seleccione **Hadoop**. Es el tipo de clúster más popular.
    - **Sistema operativo**: seleccione **Linux**.
    - **Versión**: usar la versión predeterminada si no sabe qué elegir. Para obtener más información, vea [versiones de clúster HDInsight](hdinsight-component-versioning.md).
    - **Nivel de clúster**: HDInsight de Azure proporciona las ofertas de nube de datos grande en dos categorías: nivel Premium y estándar. Para obtener más información, vea [niveles de clúster](hdinsight-hadoop-provision-linux-clusters.md#cluster-tiers).
6. Haga clic en **aplicaciones**, haga clic en una de las aplicaciones publicadas y, a continuación, haga clic en **Seleccionar**.
6. Haga clic en **credenciales** y, a continuación, escriba una contraseña para el usuario administrador. También debe escribir un **Nombre de usuario SSH** y una **contraseña** o **Clave pública**, que se usará para autenticar al usuario SSH. Con una clave pública es el enfoque recomendado. En la parte inferior para guardar la configuración de credenciales, haga clic en **Seleccionar** .
8. Haga clic en **Origen de datos**, seleccione una de la cuenta de almacenamiento existente o crear una nueva cuenta de almacenamiento que se usará como la cuenta de almacenamiento predeterminada para el clúster.
9. Haga clic en el **Grupo de recursos** para seleccionar un grupo de recursos existente o haga clic en **nuevo** para crear un nuevo grupo de recursos

10. En el módulo **Nuevo HDInsight clúster** , asegúrese de que está seleccionada la opción **Anclar al Startboard** y, a continuación, haga clic en **crear**. 

## <a name="list-installed-hdinsight-apps-and-properties"></a>Lista de aplicaciones instaladas de HDInsight y propiedades

El portal muestra una lista de las aplicaciones instaladas de HDInsight para un clúster y las propiedades de cada aplicación instalada.

**HDInsight aplicación de la lista y mostrar las propiedades**

1. Inicie sesión en el [portal de Azure](https://portal.azure.com).
2. En el menú de la izquierda, haga clic en **Clústeres HDInsight** .  Si no lo ves, haga clic en **Examinar**y, a continuación, haga clic en **Clústeres HDInsight**.
3. Haga clic en un clúster de HDInsight.
4. En el módulo de **configuración** , haga clic en **aplicaciones** en la categoría **General** . El módulo de aplicaciones instaladas muestra todas las aplicaciones instaladas. 

    ![aplicaciones de hdinsight aplicaciones instaladas](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png)

5. Haga clic en una de las aplicaciones instaladas para mostrar la propiedad. Listas de hoja de propiedades:

    - Nombre de la aplicación: nombre de la aplicación.
    - Estado: estado de aplicación. 
    - Página Web: La dirección URL de la aplicación web que se ha implementado en el nodo del borde si hay alguna. La credencial es el mismo que las credenciales de usuario HTTP que ha configurado para el clúster.
    - Extremo HTTP: la credencial es el mismo que las credenciales de usuario HTTP que ha configurado para el clúster. 
    - Extremo SSH: puede usar [SSH](hdinsight-hadoop-linux-use-ssh-unix.md) para conectar con el nodo del borde. Las credenciales SSH están la misma que las credenciales de usuario SSH que ha configurado para el clúster.

6. Para eliminar una aplicación, haga clic en la aplicación y, a continuación, haga clic en **Eliminar** en el menú contextual.

## <a name="connect-to-the-edge-node"></a>Conectar con el nodo del borde

Puede conectarse al nodo de borde mediante HTTP y SSH. Encontrará la información de extremo desde el [portal](#list-installed-hdinsight-apps-and-properties). Para obtener más información sobre el uso de SSH, vea [Usar SSH con basados en Linux Hadoop en HDInsight de Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md). 

Las credenciales de extremo HTTP son las credenciales de usuario HTTP que ha configurado para el clúster HDInsight; las credenciales de extremo SSH son las credenciales SSH que ha configurado para el clúster de HDInsight.

## <a name="troubleshoot"></a>Solución de problemas

Consulte [solucionar problemas de la instalación](hdinsight-apps-install-custom-applications.md#troubleshoot-the-installation).

## <a name="next-steps"></a>Pasos siguientes

- [Instalar aplicaciones personalizadas de HDInsight](hdinsight-apps-install-custom-applications.md): Obtenga información sobre cómo implementar una aplicación de HDInsight no publicada en HDInsight.
- [HDInsight publicar aplicaciones](hdinsight-apps-publish-applications.md): Obtenga información sobre cómo publicar las aplicaciones HDInsight Azure Marketplace.
- [MSDN: instalar una aplicación de HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): Obtenga información sobre cómo definir aplicaciones HDInsight.
- [Clústeres basados en Linux personalizar HDInsight con la acción de secuencia de comandos](hdinsight-hadoop-customize-cluster-linux.md): Obtenga información sobre cómo usar la acción de secuencia de comandos para instalar las aplicaciones adicionales.
- [Hadoop basados en Linux crear clústeres en HDInsight con plantillas de administrador de recursos](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Aprenda a plantillas de administrador de recursos para crear clústeres HDInsight de llamada.
- [Usar los nodos de borde vacío en HDInsight](hdinsight-apps-use-edge-node.md): Aprenda a usar un nodo del borde vacío para acceder HDInsight clúster, probar aplicaciones HDInsight y hospedaje de aplicaciones de HDInsight.


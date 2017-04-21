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

# <a name="install-custom-hdinsight-applications"></a>Instalar aplicaciones personalizadas de HDInsight

Una aplicación de HDInsight es una aplicación que los usuarios pueden instalar en un clúster de HDInsight basados en Linux.  Estas aplicaciones se pueden desarrollar por Microsoft, proveedores de software independientes (ISV) o por usted mismo. En este artículo, aprenderá a instalar una aplicación de HDInsight que no se ha publicado en el portal de Azure en HDInsight. La aplicación que se va a instalar es [matiz](http://gethue.com/). 

Otros artículos relacionados:

- [HDInsight de instalar aplicaciones](hdinsight-apps-install-applications.md): Obtenga información sobre cómo instalar una aplicación de HDInsight a los clústeres.
- [HDInsight publicar aplicaciones](hdinsight-apps-publish-applications.md): Obtenga información sobre cómo publicar las aplicaciones HDInsight Azure Marketplace.
- [MSDN: instalar una aplicación de HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): Obtenga información sobre cómo definir aplicaciones HDInsight.

 
## <a name="prerequisites"></a>Requisitos previos

Si desea instalar aplicaciones HDInsight en un clúster de HDInsight existente, debe tener un clúster de HDInsight. Para crear uno, consulte [crear clústeres](hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster). También puede instalar aplicaciones de HDInsight cuando se crea un clúster de HDInsight.


## <a name="install-hdinsight-applications"></a>Instalar aplicaciones de HDInsight

Cuando se crea un clúster o a un clúster de HDInsight existente, se pueden instalar aplicaciones de HDInsight. Para definir las plantillas de administrador de recursos de Azure, consulte [MSDN: instalar una aplicación de HDInsight](https://msdn.microsoft.com/library/mt706515.aspx).

Los archivos necesarios para implementar esta aplicación (matiz):

- [azuredeploy.JSON](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/azuredeploy.json): el Administrador de recursos de plantilla para instalar la aplicación HDInsight. Consulte [MSDN: instalar una aplicación de HDInsight](https://msdn.microsoft.com/library/mt706515.aspx) para desarrollar su propia plantilla de administrador de recursos.
- [tono install_v0.sh](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/scripts/Hue-install_v0.sh): acción de la secuencia de comandos que se llama a la plantilla de administrador de recursos para configurar el nodo del borde. 
- [tono binaries.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz): el archivo binario de tono que se llama desde hui install_v0.sh. 
- [tono-binarios-14-04.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz): el archivo binario de tono que se llama desde hui install_v0.sh. 
- [webwasb tomcat.tar.gz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/webwasb-tomcat.tar.gz): una aplicación web de ejemplo (Tomcat) que se llama desde hui install_v0.sh.

**Para instalar el matiz en un clúster de HDInsight existente**

1. Haga clic en la imagen siguiente para iniciar sesión Azure y abra la plantilla de administrador de recursos en el Portal de Azure. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2FIaas-Applications%2Fmaster%2FHue%2Fazuredeploy.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

    Este botón abre una plantilla de administrador de recursos en el portal de Azure.  La plantilla de administrador de recursos se encuentra en [https://github.com/hdinsight/Iaas-Applications/tree/master/Hue](https://github.com/hdinsight/Iaas-Applications/tree/master/Hue).  Para obtener información sobre cómo escribir esta plantilla de administrador de recursos, consulte [MSDN: instalar una aplicación de HDInsight](https://msdn.microsoft.com/library/mt706515.aspx).
    
2. Desde el módulo de **parámetros** , escriba lo siguiente:

    - **NombreDeClúster**: escriba el nombre del clúster donde desea instalar la aplicación. Este clúster debe ser un clúster existente.
    
3. Haga clic en **Aceptar** para guardar los parámetros.
4. Desde el módulo de **implementación personalizada** , escriba el **grupo de recursos**.  El grupo de recursos es un contenedor que agrupa el clúster, la cuenta de almacenamiento dependientes y otros recursos. Es necesario utilizar el mismo grupo de recursos que el clúster.
5. Haga clic en **condiciones legales**y, a continuación, haga clic en **crear**.
6. Compruebe que está seleccionada la casilla de verificación **Anclar al panel** y, a continuación, haga clic en **crear**. Puede ver el estado de la instalación desde el mosaico anclado en el panel de portal y la notificación de portal (haga clic en el icono de campana en el portal de la parte superior).  Tardará unos 10 minutos para instalar la aplicación.

**Para instalar el tono al crear un clúster**

1. Haga clic en la imagen siguiente para iniciar sesión Azure y abra la plantilla de administrador de recursos en el Portal de Azure. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhdinsightapps%2Fcreate-linux-based-hadoop-cluster-in-hdinsight.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

    Este botón abre una plantilla de administrador de recursos en el portal de Azure.  La plantilla de administrador de recursos se encuentra en [https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json](https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json).  Para obtener información sobre cómo escribir esta plantilla de administrador de recursos, consulte [MSDN: instalar una aplicación de HDInsight](https://msdn.microsoft.com/library/mt706515.aspx).

2. Siga las instrucciones para crear clúster e instalar matiz. Para obtener más información sobre la creación de clústeres HDInsight, consulte [clústeres basados en Linux crear Hadoop en HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

Además de portal de Azure, también puede usar [PowerShell de Azure](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-with-powershell) y [Azure CLI](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-with-azure-cli) para llamar a las plantillas de administrador de recursos.

## <a name="validate-the-installation"></a>Validar la instalación

Puede comprobar el estado de la aplicación en el portal de Azure para validar la instalación de la aplicación. Además, también puede validar todas HTTP extremos resultado como se esperaba y la página Web en su caso:

**Para abrir el portal de tono**

1. Inicie sesión en el [portal de Azure](https://portal.azure.com).
2. En el menú de la izquierda, haga clic en **Clústeres HDInsight** .  Si no lo ves, haga clic en **Examinar**y, a continuación, haga clic en **Clústeres HDInsight**.
3. Haga clic en el clúster donde ha instalado la aplicación.
4. En el módulo de **configuración** , haga clic en **aplicaciones** en la categoría **General** . Deberá ver **matiz** que aparece en el módulo de **Aplicaciones instaladas** .
5. Haga clic en el **tono** de la lista de las propiedades de la lista.  
6. Haga clic en el vínculo de la página Web para validar el sitio Web; Abra el extremo HTTP en un explorador para validar la interfaz de usuario de web matiz, abra el extremo SSH mediante [PuTTY](hdinsight-hadoop-linux-use-ssh-windows.md) u otros [clientes SSH](hdinsight-hadoop-linux-use-ssh-unix.md).
 
## <a name="troubleshoot-the-installation"></a>Solucionar problemas de instalación

Puede comprobar el estado de instalación de la aplicación del portal notificación (haga clic en el icono de campana en el portal de la parte superior). 


Si el error de instalación de una aplicación, puede ver los mensajes de error y depurar información de 3 decimales:

- Aplicaciones de HDInsight: información error general.

    Abra el clúster desde el portal y haga clic en aplicaciones desde el módulo de configuración:

    ![error de instalación de aplicaciones de hdinsight aplicaciones](./media/hdinsight-apps-install-applications/hdinsight-apps-error.png)

- Acción de secuencia de comandos de HDInsight: si el mensaje de error de las aplicaciones HDInsight indica un error de acción de secuencia de comandos, se presentará más detalles sobre los errores de script en el panel de acciones de script.

    Haga clic en acción de secuencia de comandos de la hoja de configuración. Historial de acción de secuencia de comandos muestra los mensajes de error

    ![error de acción de secuencia de comandos de hdinsight aplicaciones](./media/hdinsight-apps-install-applications/hdinsight-apps-script-action-error.png)
    
- Interfaz de usuario de Ambari Web: Si la secuencia de comandos de instalación fue la causa del error, utilice Ambari Web UI para comprobar registros completos acerca de las secuencias de comandos de instalación.

    Para obtener más información, vea [solución de problemas](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting).

## <a name="remove-hdinsight-applications"></a>Quitar aplicaciones HDInsight

Hay varias maneras de eliminar aplicaciones de HDInsight.

### <a name="use-portal"></a>Usar el portal

**Para quitar una aplicación con el portal**

1. Inicie sesión en el [portal de Azure](https://portal.azure.com).
2. En el menú de la izquierda, haga clic en **Clústeres HDInsight** .  Si no lo ves, haga clic en **Examinar**y, a continuación, haga clic en **Clústeres HDInsight**.
3. Haga clic en el clúster donde ha instalado la aplicación.
4. En el módulo de **configuración** , haga clic en **aplicaciones** en la categoría **General** . Deberá ver una lista de aplicaciones instaladas. En este tutorial, **matiz** aparece en el módulo de **Aplicaciones instaladas** .
5. Haga clic en la aplicación que desee quitar y, a continuación, haga clic en **Eliminar**.
6. Haga clic en **Sí** para confirmar.

Desde el portal, también puede eliminar el clúster o eliminar el grupo de recursos que contiene la aplicación.

### <a name="use-azure-powershell"></a>Usar PowerShell Azure

Usando PowerShell de Azure, puede eliminar el clúster o eliminar el grupo de recursos. Consulte [Eliminar clústeres con PowerShell de Azure](hdinsight-administer-use-powershell.md#delete-clusters).

### <a name="use-azure-cli"></a>Utilizar CLI Azure

Azure CLI puede eliminar el clúster o eliminar el grupo de recursos. Consulte [Eliminar clústeres usando CLI de Azure](hdinsight-administer-use-command-line.md#delete-clusters).


## <a name="next-steps"></a>Pasos siguientes

- [MSDN: instalar una aplicación de HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): Obtenga información sobre cómo desarrollar plantillas de administrador de recursos para implementar aplicaciones de HDInsight.
- [HDInsight de instalar aplicaciones](hdinsight-apps-install-applications.md): Obtenga información sobre cómo instalar una aplicación de HDInsight a los clústeres.
- [HDInsight publicar aplicaciones](hdinsight-apps-publish-applications.md): Obtenga información sobre cómo publicar las aplicaciones HDInsight Azure Marketplace.
- [Clústeres basados en Linux personalizar HDInsight con la acción de secuencia de comandos](hdinsight-hadoop-customize-cluster-linux.md): Obtenga información sobre cómo usar la acción de secuencia de comandos para instalar las aplicaciones adicionales.
- [Hadoop basados en Linux crear clústeres en HDInsight con plantillas de administrador de recursos](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Aprenda a plantillas de administrador de recursos para crear clústeres HDInsight de llamada.
- [Usar los nodos de borde vacío en HDInsight](hdinsight-apps-use-edge-node.md): Aprenda a usar un nodo del borde vacío para acceder HDInsight clúster, probar aplicaciones HDInsight y hospedaje de aplicaciones de HDInsight.

<properties
    pageTitle="Publicar aplicaciones HDInsight | Microsoft Azure"
    description="Aprenda a crear y publicar aplicaciones HDInsight."
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
    ms.date="10/18/2016"
    ms.author="jgao"/>

# <a name="publish-hdinsight-applications-into-the-azure-marketplace"></a>Publicar aplicaciones HDInsight en Azure Marketplace

Una aplicación de HDInsight es una aplicación que los usuarios pueden instalar en un clúster de HDInsight basados en Linux. Estas aplicaciones se pueden desarrollar por Microsoft, proveedores de software independientes (ISV) o por usted mismo. En este artículo, aprenderá cómo publicar una aplicación de HDInsight en Azure Marketplace.  Para obtener información general sobre la publicación en el catálogo de soluciones de Azure, consulte [publicar una oferta de Azure Marketplace](../marketplace-publishing/marketplace-publishing-getting-started.md).

Aplicaciones de HDInsight utilizan el modelo de *Traer su propia licencia (BYOL)* , donde el proveedor de aplicación es el responsable de la aplicación a los usuarios finales de licencias y los usuarios finales sólo se cargan por Azure para los recursos que se crean, como el clúster HDInsight y sus nodos de máquinas virtuales. En este momento, la facturación para la aplicación no se realiza a través de Azure.

Artículo relacionado con la otra aplicación HDInsight:

- [HDInsight de instalar aplicaciones](hdinsight-apps-install-applications.md): Obtenga información sobre cómo instalar una aplicación de HDInsight a los clústeres.
- [Instalar aplicaciones personalizadas de HDInsight](hdinsight-apps-install-custom-applications.md): Obtenga información sobre cómo instalar y probar aplicaciones personalizadas de HDInsight.

 
## <a name="prerequisites"></a>Requisitos previos

Para enviar su aplicación personalizada para el catálogo de soluciones, debe tener creado y probado la aplicación personalizada. Consulte los artículos siguientes:

- [Instalar aplicaciones personalizadas de HDInsight](hdinsight-apps-install-custom-applications.md): Obtenga información sobre cómo instalar y probar aplicaciones personalizadas de HDInsight.

También debe registrar su cuenta de desarrollador. Consulte [crear una cuenta de Microsoft Developer](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md)y [publicar una oferta de Azure Marketplace](../marketplace-publishing/marketplace-publishing-getting-started.md) .

## <a name="define-application"></a>Definir la aplicación

Existen dos pasos para publicar aplicaciones de Azure Marketplace.  Definir primero un archivo de **createUiDef.json** para indicar cuáles clústeres su aplicación es compatible con; y, a continuación, publicar la plantilla desde el portal de Azure. El siguiente es un ejemplo de archivo de createUiDef.json.

    {
        "handler": "Microsoft.HDInsight",
        "version": "0.0.1-preview",
        "clusterFilters": {
            "types": ["Hadoop", "HBase", "Storm", "Spark"],
            "tiers": ["Standard", "Premium"],
            "versions": ["3.4"]
        }
    }


|Campo  | Descripción   | Valores posibles|
|-------|---------------|----------------|
|tipos de  | Los tipos de clúster es compatible con la aplicación.    |Hadoop, HBase, tormenta, motor (o cualquier combinación de ellos)|
|niveles  | Los niveles de clúster es compatible con la aplicación.    |Estándar, Premium (o ambas)|
|versiones|  Los tipos de clúster HDInsight que es compatible con la aplicación.    |3.4|

## <a name="package-application"></a>Paquete de aplicaciones

Crear un archivo zip que contiene todos los archivos necesarios para instalar las aplicaciones de HDInsight. Necesitará el archivo zip en [aplicación de la publicación](#publish-application).

- [createUiDefinition.json](#define-application).
- mainTemplate.json. Vea un ejemplo en [instalar aplicaciones personalizadas de HDInsight](hdinsight-apps-install-custom-applications.md).

    >[AZURE.IMPORTANT] El nombre de los nombres de secuencia de comandos de instalación de aplicación debe ser único para un determinado clúster con el formato siguiente. Además cualquiera instalar y desinstalar acciones de script debe ser idempotente, lo que significa que las secuencias de comandos puede llamar repeatly mientras se produce el mismo resultado.
    
    >   nombre de":" [concatenación ('v0 de instalación de matiz ','-', uniquestring('applicationName')] "
        
    >Tenga en cuenta que existen tres partes en el nombre de la secuencia de comandos:
        
    >   1. Prefijo de nombre de secuencia de comandos, que se incluirá el nombre de la aplicación o un nombre de la aplicación.
    >   2. A "-" para mejorar la legibilidad.
    >   3. Una función de cadena única con el nombre de la aplicación como parámetro.

    >   Un ejemplo es el anterior termina convertirse en: matiz-install-v0-4wkahss55hlas en la lista de acciones de script almacenado. Para una carga JSON de ejemplo, vea [https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json](https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json).

- Secuencias de comandos necesarios.

> [AZURE.NOTE] Los archivos de la aplicación (incluidos los archivos de la aplicación web si hay alguna) puede estar ubicado en cualquier extremo accesible públicamente.

## <a name="publish-application"></a>Publicar aplicación

Siga los pasos siguientes para publicar una aplicación HDInsight:

1. Inicie sesión el [portal de publicación de Azure](https://publish.windowsazure.com/).
2. Haga clic en **plantillas de solución** desde la izquierda para crear una nueva plantilla de solución.
3. Escriba un título y, a continuación, haga clic en **crear una nueva plantilla de solución**.
3. Haga clic en **Centro de desarrollo de crear la cuenta y unirse al programa de Azure** para registrar su compañía si todavía no lo ha hecho.  Consulte [crear una cuenta de Microsoft Developer](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md).
4. Haga clic en **definir algunas topologías para empezar**. Una plantilla de solución es "padre" a todos sus topologías. Puede definir varias topologías en una plantilla de oferta o solución. Cuando se inserta una oferta a ensayo, se inserta con todos sus topologías. 
4. Escriba un nombre de topología y, a continuación, haga clic en el signo más.
5. Escriba una nueva versión y, a continuación, haga clic en el signo más.
6. Cargue el archivo zip preparado en el [paquete de aplicaciones](#package-application).  
7. Haga clic en **Solicitar certificados**. El equipo de certificación Microsoft revisará los archivos y certificar la topología.

## <a name="next-steps"></a>Pasos siguientes

- [HDInsight de instalar aplicaciones](hdinsight-apps-install-applications.md): Obtenga información sobre cómo instalar una aplicación de HDInsight a los clústeres.
- [Instalar aplicaciones personalizadas de HDInsight](hdinsight-apps-install-custom-applications.md): Obtenga información sobre cómo implementar una aplicación de HDInsight no publicada en HDInsight.
- [Clústeres basados en Linux personalizar HDInsight con la acción de secuencia de comandos](hdinsight-hadoop-customize-cluster-linux.md): Obtenga información sobre cómo usar la acción de secuencia de comandos para instalar las aplicaciones adicionales.
- [Hadoop basados en Linux crear clústeres en HDInsight con el Administrador de recursos de Azure plantillas](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Aprenda a plantillas de administrador de recursos para crear clústeres HDInsight de llamada.
- [Usar los nodos de borde vacío en HDInsight](hdinsight-apps-use-edge-node.md): Aprenda a usar un nodo del borde vacío para acceder HDInsight clúster, probar aplicaciones HDInsight y hospedaje de aplicaciones de HDInsight.


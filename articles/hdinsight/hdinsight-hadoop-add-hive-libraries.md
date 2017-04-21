<properties
pageTitle="Agregar bibliotecas de subárbol durante la creación de clúster HDInsight | Azure"
description="Aprenda a agregar sección bibliotecas (archivos jar) a un clúster de HDInsight durante la creación de clúster."
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
ms.date="09/20/2016"
ms.author="larryfr"/>

#<a name="add-hive-libraries-during-hdinsight-cluster-creation"></a>Agregar bibliotecas de subárbol durante la creación de clúster HDInsight

Si dispone de bibliotecas que usa frecuentemente con subárbol en HDInsight, este documento contiene información sobre cómo usar una acción de secuencia de comandos para cargar previamente las bibliotecas durante la creación de clúster. De esta forma las bibliotecas disponibles globalmente en subárbol (sin necesidad de utilizar [Agregar JAR](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli) cargarlos.)

##<a name="how-it-works"></a>Cómo funciona

Al crear un clúster, opcionalmente, puede especificar una acción de secuencia de comandos que se ejecuta una secuencia de comandos en los nodos de clúster mientras se crean. La secuencia de comandos de este documento acepta un único parámetro, que es una ubicación de WASB que contiene las bibliotecas (almacenadas como archivos jar,) que se cargarán previamente.

Durante la creación de clúster, la secuencia de comandos enumera los archivos, los copia a la `/usr/lib/customhivelibs/` directorio en los nodos de cabeza y el trabajo, a continuación, agrega a la `hive.aux.jars.path` propiedad en el `core-site.xml` archivo. En clústeres basados en Linux, también actualiza el `hive-env.sh` archivo con la ubicación de los archivos.

> [AZURE.NOTE] Con las acciones de secuencias de comandos de este artículo proporciona las bibliotecas en las situaciones siguientes:
>
> * __HDInsight de Linux__ - cuando se usa la __sección de línea de comandos__, __WebHCat__ (Templeton) y __HiveServer2__.
> * __HDInsight de Windows__ - cuando se usa la __sección de línea de comandos__ y __WebHCat__ (Templeton).

##<a name="the-script"></a>La secuencia de comandos

__Ubicación de la secuencia de comandos__

Para __clústeres basados en Linux__: [https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh)

Para __clústeres basados en Windows__: [https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1](https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1)

__Requisitos__

* Las secuencias de comandos se deben aplicar a los __nodos de cabeza__ y __nodos de trabajo__.

* La jars que desea instalar deben estar almacenados en el almacenamiento de blobs de Windows Azure en un __solo contenedor__. 

* La cuenta de almacenamiento que contiene la biblioteca de archivos de jar __debe__ estar vinculado a clúster HDInsight durante la creación. Esto puede realizarse en una de estas dos maneras:

    * Si está en un contenedor de la cuenta de almacenamiento predeterminada para el clúster.
    
    * Si está en un contenedor en un contenedor de almacenamiento vinculado. Por ejemplo, puede usar __Configuración opcional__, __cuentas de almacenamiento vinculado__ agregar almacenamiento adicional en el portal.

* La ruta de acceso WASB al contenedor debe especificarse como un parámetro a la acción de secuencia de comandos. Por ejemplo, si la jars se almacenan en un contenedor denominado __bibliotecas__ en una cuenta de almacenamiento denominada __mystorage__, el parámetro sería __wasbs://libs@mystorage.blob.core.windows.net/__.

    > [AZURE.NOTE] Este documento se supone que ya dispone de crear una cuenta de almacenamiento, blob contenedor y cargar los archivos en él. 
    >
    > Si no ha creado una cuenta de almacenamiento, puede hacerlo a través del [portal de Azure](https://portal.azure.com). A continuación, puede usar una herramienta como [El Explorador de almacenamiento de Azure](http://storageexplorer.com/) para crear un nuevo contenedor de la cuenta y cargar archivos en ella.

##<a name="create-a-cluster-using-the-script"></a>Crear un clúster mediante la secuencia de comandos

> [AZURE.NOTE] Los pasos siguientes crean un nuevo clúster de HDInsight basados en Linux. Para crear un nuevo clúster basado en Windows, seleccione __Windows__ como el clúster SO al crear el clúster y usar la secuencia de comandos de Windows (PowerShell) en lugar de la secuencia de comandos de fiesta.
> 
> También puede usar PowerShell Azure o el SDK de .NET HDInsight para crear un clúster mediante esta secuencia de comandos. Para obtener más información sobre cómo usar estos métodos, vea [Personalizar HDInsight clústeres con acciones de Script](hdinsight-hadoop-customize-cluster-linux.md).

1. Iniciar el aprovisionamiento de un clúster con los pasos en [clústeres HDInsight de aprovisionamiento en Linux](hdinsight-hadoop-provision-linux-clusters.md#portal), pero no se completan aprovisionamiento.

2. En el módulo **Configuración opcional** , seleccione **Acciones de secuencias de comandos**y como se muestra a continuación, complete la información:

    * __Nombre__: escriba un nombre descriptivo para la acción de secuencia de comandos.
    * __URI de secuencia de comandos__: https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh
    * __Cabeza__: Active esta opción
    * __Trabajo__: seleccione esta opción.
    * __ZOOKEEPER__: deje el campo vacío.
    * __Parámetros__: escriba la dirección WASB a la cuenta de almacenamiento y contenedor que contiene el jars. Por ejemplo, __wasbs://libs@mystorage.blob.core.windows.net/__.

3. En la parte inferior de las **Acciones de secuencias de comandos**, use el botón **Seleccionar** para guardar la configuración.

4. En el módulo **Configuración opcional** , seleccione __Cuentas de almacenamiento vinculado__ y seleccione el vínculo __Agregar una clave de almacenamiento__ . Seleccione la cuenta de almacenamiento que contiene la botes y, a continuación, use los botones __Seleccionar__ para guardar la configuración y devolver el módulo de __Configuración opcional__ .

5. Use el botón **Seleccionar** en la parte inferior del módulo **Opcional de configuración** para guardar la información de configuración opcional.

6. Continúe el clúster de aprovisionamiento, como se describe en [clústeres HDInsight de aprovisionamiento en Linux](hdinsight-hadoop-provision-linux-clusters.md#portal).

Una vez que finalice la creación de un clúster, podrá usar el jars agregados a través de esta secuencia de comandos de la sección sin tener que utilizar el `ADD JAR` instrucción.

##<a name="next-steps"></a>Pasos siguientes

En este documento ha visto cómo agregar bibliotecas de subárbol contenidas en archivos jar a un clúster de HDInsight durante la creación de clúster. Para obtener más información sobre cómo trabajar con la sección, vea [Usar subárbol con HDInsight](hdinsight-use-hive.md)

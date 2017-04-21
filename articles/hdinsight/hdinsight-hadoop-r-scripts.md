<properties
    pageTitle="Usar R en HDInsight personalizar clústeres | Microsoft Azure"
    description="Obtenga información sobre cómo instalar R con la acción de secuencia de comandos y usar R en clústeres HDInsight."
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

# <a name="install-and-use-r-on-hdinsight-hadoop-clusters"></a>Instalar y usar R en clústeres HDInsight Hadoop

Obtenga información sobre cómo personalizar Windows basa clúster HDInsight con R con la acción de secuencia de comandos y cómo usar R en HDInsight clústeres. El [nivel premium](https://azure.microsoft.com/pricing/details/hdinsight/) que se ofrece para HDInsight incluye R Server como parte de su clúster HDInsight. Esto permite que las secuencias de comandos de R usar MapReduce y motor para ejecutar cálculos distribuidos. Para obtener más información, vea [Introducción al uso de servidor de R en HDInsight](hdinsight-hadoop-r-server-get-started.md). Para obtener información sobre el uso de R con un clúster de Linux, consulte [instalar y usar R en clústeres HDinsight Hadoop (Linux)](hdinsight-hadoop-r-scripts-linux.md).
 
Puede instalar R en cualquier tipo de clúster (Hadoop, tormenta, HBase, motor) en HDInsight de Azure mediante *Secuencia de comandos de acción*. Una secuencia de comandos de ejemplo para instalar R en un clúster de HDInsight está disponible desde un blob de solo lectura almacenamiento de Azure en [https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1). 

**Artículos relacionados**

- [Instalar y usar R en clústeres HDinsight Hadoop (Linux)](hdinsight-hadoop-r-scripts-linux.md)
- [Crear Hadoop clústeres en HDInsight](hdinsight-provision-clusters.md): información general sobre la creación de clústeres HDInsight
- [Personalizar clúster HDInsight con la acción de secuencia de comandos][hdinsight-cluster-customize]: información general sobre la personalización de clústeres HDInsight con la acción de secuencia de comandos
- [Desarrollar secuencias de comandos de acción de secuencia de comandos para HDInsight](hdinsight-hadoop-script-actions.md)

## <a name="what-is-r"></a>¿Qué es R?

El <a href="http://www.r-project.org/" target="_blank">Proyecto R sistemas estadísticos de</a> es un idioma de origen abierto y el entorno para las estadísticas. R proporciona cientos de compilación de funciones estadísticas y su propio lenguaje de programación que combina aspectos de la programación funcional y orientado a objetos. También proporciona capacidades gráficas extensas. R es el entorno de programación preferido para profesionales más estadísticos y científicos en una amplia variedad de campos.

R es compatible con el almacenamiento de blobs de Windows Azure (WASB) para que los datos que se almacenan allí pueden procesarse mediante R en HDInsight.  

## <a name="install-r"></a>Instalar R

Un [script de ejemplo](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1) para instalar R en un clúster de HDInsight está disponible desde un blob de solo lectura en el almacenamiento de Azure. Esta sección proporciona instrucciones sobre cómo usar la secuencia de comandos al crear el clúster con el Portal de Azure.

> [AZURE.NOTE] La secuencia de comandos se introdujo con la versión de clúster HDInsight 3.1. Para obtener más información acerca de las versiones de clúster HDInsight, consulte [versiones de clúster HDInsight](hdinsight-component-versioning.md).

1. Cuando se crea un clúster HDInsight desde el Portal, haga clic en **Configuración opcional**y, a continuación, haga clic en **Acciones de Script**.
2. En la página de **Acciones de secuencia de comandos** , escriba los valores siguientes:

    ![Acción de secuencia de comandos de uso para personalizar un clúster] (./media/hdinsight-hadoop-r-scripts/hdi-r-script-action.png "Acción de secuencia de comandos de uso para personalizar un clúster")

    <table border='1'>
        <tr><th>(Propiedad)</th><th>Valor</th></tr>
        <tr><td>Nombre</td>
            <td>Especifique un nombre para la acción de secuencia de comandos, por ejemplo, <b>R instalar</b>.</td></tr>
        <tr><td>URI de secuencia de comandos</td>
            <td>Especificar el URI a la secuencia de comandos que se invoca para personalizar el clúster, por ejemplo, <i>https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1</i></td></tr>
        <tr><td>Tipo de nodo</td>
            <td>Especifique los nodos en que se ejecuta la secuencia de comandos de personalización. Puede elegir <b>Todos los nodos</b>, <b>solo los nodos de cabeza</b>o <b>nodos de trabajo</b> solo.
        <tr><td>Parámetros</td>
            <td>Especificar los parámetros, si es necesario la secuencia de comandos. Sin embargo, la secuencia de comandos para instalar R no requiere parámetros, por lo que puede dejar en blanco.</td></tr>
    </table>

    Puede agregar más de una acción de secuencia de comandos para instalar varios componentes en el clúster. Después de haber agregado las secuencias de comandos, haga clic en la marca de verificación para iniciar embalarlos el clúster.

También puede usar la secuencia de comandos para instalar R en HDInsight con Azure PowerShell o el SDK de .NET HDInsight. Más adelante en este artículo se proporcionan instrucciones para estos procedimientos.

## <a name="run-r-scripts"></a>Ejecutar secuencias de comandos de R
Esta sección describe cómo ejecutar una secuencia de comandos de R en el clúster de Hadoop con HDInsight.

1. **Establecer una conexión a Escritorio remoto para el clúster**: desde el Portal, habilitar Escritorio remoto para el clúster creado con R instalado y, a continuación, conéctese al clúster. Para obtener instrucciones, vea [conectarse a clústeres HDInsight con RDP](hdinsight-administer-use-management-portal.md#rdp).

2. **Abra la consola de R**: instalación de R la coloca un vínculo en la consola de R en el escritorio del nodo principal. Haga clic en ella para abrir la consola de R.

3. **Ejecutar la secuencia de comandos de R**: secuencia de comandos de la R se puede ejecutar directamente desde la consola de R pegándola, seleccionándolo y presionando ENTRAR. Aquí es una secuencia de comandos de ejemplo sencillo que genera los números del 1 al 100 y, a continuación, multiplica por 2.

        library(rmr2)
        library(rhdfs)
        ints = to.dfs(1:100)
        calc = mapreduce(input = ints, map = function(k, v) cbind(v, 2*v))
        from.dfs(calc)

Las dos primeras líneas llamar a las bibliotecas de RHadoop que se instalan con R. La línea final imprime los resultados en la consola. El resultado debería tener este aspecto:

    [1,]  1 2
    [2,]  2 4
    .
    .
    .
    [98,]  98 196
    [99,]  99 198
    [100,] 100 200


## <a name="install-r-using-aure-powershell"></a>Instalar R con Aure PowerShell

Consulte [clústeres personalizar HDInsight con la acción de secuencia de comandos](hdinsight-hadoop-customize-cluster.md#call_scripts_using_powershell).  El ejemplo muestra cómo instalar el motor con PowerShell de Azure. Debe personalizar la secuencia de comandos para usar [https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1).

## <a name="install-r-using-net-sdk"></a>Instalar R mediante .NET SDK

Consulte [clústeres personalizar HDInsight con la acción de secuencia de comandos](hdinsight-hadoop-customize-cluster.md#call_scripts_using_azure_powershell). El ejemplo muestra cómo instalar motor mediante .NET SDK. Debe personalizar la secuencia de comandos para usar [https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps11).


## <a name="see-also"></a>Vea también

- [Instalar y usar R en clústeres HDinsight Hadoop (Linux)](hdinsight-hadoop-r-scripts-linux.md)
- [Crear Hadoop clústeres en HDInsight](hdinsight-provision-clusters.md): información general sobre la creación de clústeres HDInsight
- [Personalizar clúster HDInsight con la acción de secuencia de comandos][hdinsight-cluster-customize]: información general sobre la personalización de clústeres HDInsight con la acción de secuencia de comandos
- [Desarrollar secuencias de comandos de acción de secuencia de comandos para HDInsight](hdinsight-hadoop-script-actions.md)
- [Instalar y usar el motor en clústeres HDInsight][hdinsight-install-spark]: ejemplo de acción de secuencia de comandos sobre la instalación de motor
- [Instalar Giraph en HDInsight clústeres](hdinsight-hadoop-giraph-install.md): ejemplo de acción de secuencia de comandos sobre la instalación de Giraph
- [Instalar Solr en HDInsight clústeres](hdinsight-hadoop-solr-install-linux.md): ejemplo de acción de secuencia de comandos sobre la instalación de Solr.

[powershell-install-configure]: powershell-install-configure.md
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
[hdinsight-install-spark]: hdinsight-apache-spark-jupyter-spark-sql.md

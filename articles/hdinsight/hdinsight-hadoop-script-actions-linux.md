<properties
    pageTitle="Secuencia de comandos de desarrollo de acción con HDInsight de Linux | Microsoft Azure"
    description="Cómo personalizar clústeres basados en Linux HDInsight con la acción de secuencia de comandos. Acciones de script son una forma de personalizar HDInsight de Azure clústeres especificando valores de configuración de clúster o instalar servicios adicionales, herramientas u otro software en el clúster. "
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="larryfr"/>

# <a name="script-action-development-with-hdinsight"></a>Desarrollo de acción de secuencia de comandos con HDInsight

Acciones de script son una forma de personalizar HDInsight de Azure clústeres especificando valores de configuración de clúster o instalar servicios adicionales, herramientas u otro software en el clúster. Puede usar acciones de script durante la creación de clúster o en un clúster de ejecución.

> [AZURE.NOTE] La información de este documento es específica para clústeres HDInsight basados en Linux. Para obtener información sobre cómo utilizar las acciones de script con clústeres basados en Windows, vea [desarrollo de acción de secuencia de comandos con HDInsight (Windows)](hdinsight-hadoop-script-actions.md).

## <a name="what-are-script-actions"></a>¿Qué acciones de secuencia de comandos?

Acciones de script son secuencias de comandos de fiesta Azure se ejecuta en los nodos del clúster para realizar cambios de configuración o instalar el software. Una acción de secuencia de comandos se ejecuta como raíz y proporciona derechos de acceso completo a los nodos de clúster.

Acciones de script se pueden aplicar a través de los métodos siguientes:

| Use esta opción para aplicar una secuencia de comandos... | Durante la creación de clúster... | En un clúster de ejecución... |
| ----- |:-----:|:-----:|
| Portal de Azure | ✓ | ✓ |
| Azure PowerShell | ✓ | ✓ |
| CLI de Azure | &nbsp; | ✓ |
| HDInsight .NET SDK | ✓ | ✓ |
| Azure plantilla de administrador de recursos | ✓ | &nbsp; |

Para obtener más información sobre cómo usar estos métodos para aplicar acciones de secuencia de comandos, consulte [clústeres de personalizar HDInsight usar acciones de script](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="bestPracticeScripting"></a>Prácticas recomendadas para el desarrollo de secuencias de comandos

Al desarrollar una secuencia de comandos personalizada para un clúster de HDInsight, hay varias prácticas recomendadas para tener en cuenta:

- [La versión Hadoop de destino](#bPS1)
- [La versión del SO de destino](#bps10)
- [Proporcionar estable vínculos a recursos de script](#bPS2)
- [Usar recursos compilados previamente](#bPS4)
- [Asegúrese de que la secuencia de comandos de personalización de clúster es idempotente](#bPS3)
- [Alta disponibilidad de la arquitectura de clúster](#bPS5)
- [Configurar los componentes personalizados para utilizar el almacenamiento de blobs de Windows Azure](#bPS6)
- [Escribir información en STDOUT y STDERR](#bPS7)
- [Guardar archivos como ASCII con el fin de línea LF](#bps8)
- [Utilizar lógica de reintento para recuperar de errores transitorios](#bps9)

> [AZURE.IMPORTANT] Acciones de secuencia de comandos deben completarse dentro de 60 minutos o aparecerán tiempo de espera. Durante el aprovisionamiento de nodo, se ejecutó la secuencia de comandos simultáneamente con otros procesos de instalación y configuración. Competencia por recursos como el ancho de banda de red o el tiempo de CPU podría provocar tardar mucho más que tiene en su entorno de desarrollo de la secuencia de comandos.

### <a name="bPS1"></a>La versión Hadoop de destino

Diferentes versiones de HDInsight tienen diferentes versiones de servicios de Hadoop y componentes instalados. Si la secuencia de comandos espera una versión específica de un componente o servicio, solo debe usar la secuencia de comandos con la versión de HDInsight que incluye los componentes necesarios. Puede encontrar información sobre las versiones de componente incluido con HDInsight usando el documento de [control de versiones de componente HDInsight](hdinsight-component-versioning.md) .

###<a name="bps10"></a>La versión del SO de destino

HDInsight de Linux se basa en la distribución de Ubuntu Linux. Diferentes versiones de HDInsight se basan en diferentes versiones de Ubuntu, que puede afectar el comportamiento de la secuencia de comandos. Por ejemplo, HDInsight 3.4 y anterior se basan en las versiones de Ubuntu que utilizan advenedizo. Versión 3.5 se basa en Ubuntu 16.04, que usa Systemd. Systemd y advenedizo dependen de comandos diferentes, por lo que debe escribirse la secuencia de comandos para trabajar con ambos.

Otra diferencia importante entre HDInsight 3.4 y 3.5 es que `JAVA_HOME` Java 8 de destino ahora.

Puede comprobar la versión del SO mediante `lsb_release`. Los siguientes fragmentos de código de la secuencia de comandos de instalación de tono se muestra cómo determinar si se está ejecutando el script en Ubuntu 14 o 16:

    OS_VERSION=$(lsb_release -sr)
    if [[ $OS_VERSION == 14* ]]; then
        echo "OS verion is $OS_VERSION. Using hue-binaries-14-04."
        HUE_TARFILE=hue-binaries-14-04.tgz
    elif [[ $OS_VERSION == 16* ]]; then
        echo "OS verion is $OS_VERSION. Using hue-binaries-16-04."
        HUE_TARFILE=hue-binaries-16-04.tgz
    fi
    ...
    if [[ $OS_VERSION == 16* ]]; then
        echo "Using systemd configuration"
        systemctl daemon-reload
        systemctl stop webwasb.service    
        systemctl start webwasb.service
    else
        echo "Using upstart configuration"
        initctl reload-configuration
        stop webwasb
        start webwasb
    fi
    ...
    if [[ $OS_VERSION == 14* ]]; then
        export JAVA_HOME=/usr/lib/jvm/java-7-openjdk-amd64
    elif [[ $OS_VERSION == 16* ]]; then
        export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
    fi

Puede encontrar la secuencia de comandos completa que contiene estos fragmentos de código en https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh.

Para la versión de Ubuntu que utiliza HDInsight, consulte el documento de la [versión del componente HDInsight](hdinsight-component-versioning.md) .

Para conocer las diferencias entre Systemd y advenedizo, vea [Systemd para usuarios advenedizo](https://wiki.ubuntu.com/SystemdForUpstartUsers).

### <a name="bPS2"></a>Proporcionar estable vínculos a recursos de script

Debe asegurarse de que los scripts y los recursos utilizados por la secuencia de comandos siguen estando disponibles en toda la duración del clúster y que las versiones de estos archivos no cambian para la duración. Estos recursos son necesarios si se agregan nuevos nodos al clúster durante las operaciones de ajuste de escala.

Lo mejor es descargar y archivar todo el contenido de una cuenta de Azure almacenamiento de su suscripción.

> [AZURE.IMPORTANT] La cuenta de almacenamiento utilizada debe ser la cuenta de almacenamiento predeterminada para el clúster o un contenedor público de sólo lectura en cualquier otra cuenta de almacenamiento.

Por ejemplo, los ejemplos proporcionados por Microsoft se almacenan en la cuenta de almacenamiento [https://hdiconfigactions.blob.core.windows.net/](https://hdiconfigactions.blob.core.windows.net/) , que es un contenedor público, sólo lectura mantenido el equipo de HDInsight.

### <a name="bPS4"></a>Usar recursos compilados previamente

Para reducir el tiempo necesario para ejecutar la secuencia de comandos, evite operaciones que compilación recursos de código fuente. En su lugar, la compilación previa de los recursos y almacenar la versión binaria en el almacenamiento de blobs de Windows Azure para que rápidamente se puede descargar en el clúster de la secuencia de comandos.

### <a name="bPS3"></a>Asegúrese de que la secuencia de comandos de personalización de clúster es idempotente

Las secuencias de comandos deben estar diseñados para ser idempotente en el sentido de que si la secuencia de comandos ha encontrado varias veces, debe asegurarse de que el clúster se devuelve el mismo estado cada vez se ha encontrado.

Por ejemplo, si un script personalizado instala una aplicación en /usr/local/bin en su primera ejecución, cada posteriores que se ejecute la secuencia de comandos debe comprobar si la aplicación ya existe en la ubicación de /usr/local/bin antes de continuar con otros pasos de la secuencia de comandos.

### <a name="bPS5"></a>Alta disponibilidad de la arquitectura de clúster

Clústeres de HDInsight basados en Linux proporciona dos nodos de cabeza que están activos en el clúster y, a continuación, ejecutó script son acciones para ambos nodos. Si los componentes que instalar esperan solo un nodo principal, debe diseñar una secuencia de comandos que sólo se instalará el componente en uno de los dos nodos de cabeza en el clúster.

> [AZURE.IMPORTANT] Servicios predeterminados instalados como parte de HDInsight están diseñados para conmutar entre los dos nodos de cabeza según sea necesario, sin embargo, esta funcionalidad no se extiende componentes personalizado instalado mediante cciones de secuencia de comandos. Si necesita los componentes instalados a través de una acción de secuencia de comandos disponible, debe implementar su propio mecanismo de migración tras error que usa los dos nodos de cabeza disponibles.

### <a name="bPS6"></a>Configurar los componentes personalizados para utilizar el almacenamiento de blobs de Windows Azure

Componentes que se instalan en el clúster podrían tener una configuración predeterminada que usa almacenamiento de sistema de archivos distribuido Hadoop (HDFS). HDInsight usa almacenamiento de blobs de Windows Azure (WASB) como el almacenamiento predeterminado. Esto proporciona un sistema de archivos compatibles HDFS que conserva los datos incluso si se elimina el clúster. Debe configurar componentes instalar para utilizar WASB en lugar de HDFS.

Por ejemplo, la siguiente copia el archivo giraph examples.jar desde el sistema de archivos locales en WASB:

    hadoop fs -copyFromLocal /usr/hdp/current/giraph/giraph-examples.jar /example/jars/

### <a name="bPS7"></a>Escribir información en STDOUT y STDERR

Información que se escribe en STDOUT y STDERR durante la ejecución de scripts se registra y se puede ver mediante la interfaz de usuario de web Ambari.

> [AZURE.NOTE] Ambari solo estará disponible si se ha creado correctamente el clúster. Si usa una acción de secuencia de comandos durante la creación de clúster y no se puede crear, consulte la sección de solución de problemas [que hdinsight personalizar clústeres con la acción de secuencia de comandos](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting) para otras maneras de obtener acceso a información registrada.

La mayoría de utilidades y paquetes de instalación ya escribirá información en STDOUT y STDERR, pero desea agregar un registro adicional. Para enviar texto a usar STDOUT `echo`. Por ejemplo:

        echo "Getting ready to install Foo"

De forma predeterminada, `echo` enviará la cadena en STDOUT. Para mostrar directas a STDERR, agregue `>&2` antes de `echo`. Por ejemplo:

        >&2 echo "An error occurred installing Foo"

La información enviada a STDOUT (1, que es la predeterminada, así que no se muestran aquí,) se redirige a STDERR (2). Para obtener más información sobre redirección de IO, consulte [http://www.tldp.org/LDP/abs/html/io-redirection.html](http://www.tldp.org/LDP/abs/html/io-redirection.html).

Para obtener más información sobre la visualización de la información que registra acciones de secuencia de comandos, consulte [clústeres de personalizar HDInsight con la acción de secuencia de comandos](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting)

###<a name="bps8"></a>Guardar archivos como ASCII con el fin de línea LF

Las secuencias de comandos de fiesta deben almacenarse como formato ASCII, con líneas finalizadas LF. Si se almacenan los archivos como UTF-8, que puede incluir una marca de orden de bytes al comienzo del archivo, o con el fin de línea de CRLF, que es común para los editores de Windows, la secuencia de comandos se generan errores de similar al siguiente:

    $'\r': command not found
    line 1: #!/usr/bin/env: No such file or directory

###<a name="bps9"></a>Utilizar lógica de reintento para recuperar de errores transitorios

Descarga de archivos, instalar paquetes mediante apt get u otras acciones que transmiten datos a través de internet, la acción puede fallar debido a errores de red transitorios. Por ejemplo, puede ser el recurso remoto con que se comunique en el proceso de error sobre un nodo de copia de seguridad.

Para hacer la secuencia de comandos y son resistentes a errores transitorios, puede implementar la lógica de reintento. El siguiente es un ejemplo de una función que se ejecutará cualquier comando pasa a él y (si el comando falla,), vuelva a intentar hasta tres veces. Esperará dos segundos entre cada reintento.

    #retry
    MAXATTEMPTS=3

    retry() {
        local -r CMD="$@"
        local -i ATTMEPTNUM=1
        local -i RETRYINTERVAL=2

        until $CMD
        do
            if (( ATTMEPTNUM == MAXATTEMPTS ))
            then
                    echo "Attempt $ATTMEPTNUM failed. no more attempts left."
                    return 1
            else
                    echo "Attempt $ATTMEPTNUM failed! Retrying in $RETRYINTERVAL seconds..."
                    sleep $(( RETRYINTERVAL ))
                    ATTMEPTNUM=$ATTMEPTNUM+1
            fi
        done
    }

Los siguientes son ejemplos de uso de esta función.

    retry ls -ltr foo

    retry wget -O ./tmpfile.sh https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh

## <a name="helpermethods"></a>Métodos de aplicación auxiliar de scripts personalizados

métodos de secuencia de comandos acción auxiliares son utilidades que puede usar al escribir scripts personalizados. Estos se definen en [https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh)y se pueden incluir en las secuencias de comandos con los siguientes:

    # Import the helper method module.
    wget -O /tmp/HDInsightUtilities-v01.sh -q https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh && source /tmp/HDInsightUtilities-v01.sh && rm -f /tmp/HDInsightUtilities-v01.sh

Esto hace que los siguientes servicios de ayuda disponibles para su uso en la secuencia de comandos:

| Uso de auxiliar | Descripción |
| ------------ | ----------- |
| `download_file SOURCEURL DESTFILEPATH [OVERWRITE]` | Descarga un archivo desde la dirección URL de origen en la ruta de acceso del archivo especificado. De forma predeterminada, no sobrescribirá un archivo existente. |
| `untar_file TARFILE DESTDIR` | Extrae un archivo tar (con `-xf`,) para el directorio de destino. |
| `test_is_headnode` | Si ejecutó en un encabezado nodo, devuelve 1; en caso contrario, 0. |
| `test_is_datanode` | Si el nodo actual es un nodo de datos (trabajo), devuelve un 1; en caso contrario, 0. |
| `test_is_first_datanode` | Si el nodo actual es el primer nodo de datos (trabajo) (denominado workernode0), devuelve un 1; en caso contrario, 0. |
| `get_headnodes` | Devuelve el nombre de dominio completo de la headnodes en el clúster. Nombres están delimitados por comas. Error, se devuelve una cadena vacía. |
| `get_primary_headnode` | Obtiene el nombre de dominio completo de la headnode principal. Error, se devuelve una cadena vacía. |
| `get_secondary_headnode` | Obtiene el nombre de dominio completo de la headnode secundario. Error, se devuelve una cadena vacía. |
| `get_primary_headnode_number` | Obtiene el sufijo numérico de la headnode principal. Error, se devuelve una cadena vacía. |
| `get_secondary_headnode_number` | Obtiene el sufijo numérico de la headnode secundario. Error, se devuelve una cadena vacía. |

## <a name="commonusage"></a>Patrones de uso común

Esta sección proporciona instrucciones sobre cómo implementar algunos de los patrones de uso común que pueden surgir al escribir su propio script personalizado.

### <a name="passing-parameters-to-a-script"></a>Pasar parámetros a una secuencia de comandos

En algunos casos, la secuencia de comandos puede requerir parámetros. Por ejemplo, puede que tenga la contraseña de administrador para el clúster para recuperar información de la API de REST de Ambari.

Parámetros que se pasan a la secuencia de comandos se conocen como _parámetros de posición_y se asignan a `$1` para el primer parámetro, `$2` para el segundo y el modo en. `$0`contiene el nombre de la secuencia de comandos.

Pasada a la secuencia de comandos como parámetros de valores deben estar entre comillas simples (') para que el valor pasado se trata como un literal y tratamiento especial no está asignado a los caracteres incluidos como '!'.

### <a name="setting-environment-variables"></a>Las variables de entorno

Establecer una variable de entorno realiza lo siguiente:

    VARIABLENAME=value

Donde VARIABLENAME es el nombre de la variable. Para obtener acceso a la variable después de esto, utilice `$VARIABLENAME`. Por ejemplo, para asignar un valor proporcionado por un parámetro de posición, como una variable de entorno denominada contraseña, utilizar lo siguiente:

    PASSWORD=$1

A continuación, puede utilizar el acceso posterior a la información `$PASSWORD`.

Variables de entorno establecidas dentro de la secuencia de comandos sólo existen en el ámbito de la secuencia de comandos. En algunos casos, debe agregar variables de entorno amplia de sistema que se conservan después de que el script ha terminado. Normalmente, esto es para que los usuarios se conectan al clúster a través de SSH pueden usar los componentes instalados por la secuencia de comandos. Puede conseguirlo agregando la variable de entorno `/etc/environment`. Por ejemplo, el siguiente ejemplo añade __HADOOP\_conferencia\_DIR__:

    echo "HADOOP_CONF_DIR=/etc/hadoop/conf" | sudo tee -a /etc/environment

### <a name="access-to-locations-where-the-custom-scripts-are-stored"></a>Acceso a ubicaciones donde se almacenan los scripts personalizados

A cualquiera secuencias de comandos que se usa para personalizar un clúster deben estar en la cuenta de almacenamiento predeterminada para el clúster o, si en otra cuenta de almacenamiento, en un contenedor de solo lectura público. Si la secuencia de comandos tiene acceso a los recursos que se encuentra en otro punto también estos necesitan estar en accesible públicamente (al menos pública de solo lectura). Por ejemplo desea descargar un archivo en el clúster utilizando `download_file`.

Almacenar el archivo en una cuenta de almacenamiento de Azure accesible por el clúster (por ejemplo, la cuenta de almacenamiento predeterminada), le proporcionará un acceso rápido, como este almacenamiento está dentro de la red de Azure.

### <a name="checking-the-operating-system-version"></a>Comprobar la versión del sistema operativo

Diferentes versiones de HDInsight se basan en versiones específicas de Ubuntu. Puede haber diferencias entre las versiones de sistema operativo que debe buscar en la secuencia de comandos. Por ejemplo, debe instalar un archivo binario que está vinculado a la versión de Ubuntu.

Para comprobar la versión del SO, utilice `lsb_release`. Por ejemplo, la siguiente muestra cómo hacer referencia a un archivo tar diferentes dependiendo de la versión del SO:

    OS_VERSION=$(lsb_release -sr)
    if [[ $OS_VERSION == 14* ]]; then
        echo "OS verion is $OS_VERSION. Using hue-binaries-14-04."
        HUE_TARFILE=hue-binaries-14-04.tgz
    elif [[ $OS_VERSION == 16* ]]; then
        echo "OS verion is $OS_VERSION. Using hue-binaries-16-04."
        HUE_TARFILE=hue-binaries-16-04.tgz
    fi

## <a name="deployScript"></a>Lista de comprobación para implementar una acción de secuencia de comandos

Estos son los pasos que realizó al prepararse para implementar estas secuencias de comandos:

- Coloque los archivos que contienen los scripts personalizados en un lugar accesible por los nodos de clúster durante la implementación. Esto puede representar cualquiera de los predeterminados o cuentas adicionales de almacenamiento especificadas en el momento de la implementación de clúster o cualquier otro contenedor de almacenamiento accesible públicamente.

- Agregar controles a secuencias de comandos para asegurarse de que ejecutar impotently, para que la secuencia de comandos se puede ejecutar varias veces en el mismo nodo.

- Use un directorio de archivo temporal/tmp para conservar los archivos descargados utilizados por las secuencias de comandos y, a continuación, limpiarlos después de que han ejecutado secuencias de comandos.

- En caso de que se han cambiado la configuración de nivel de sistema operativo o archivos de configuración del servicio de Hadoop, desea reiniciar servicios HDInsight para que puede seleccionar cualquier configuración de nivel de sistema operativo, como las variables de entorno establecidas en las secuencias de comandos.

## <a name="runScriptAction"></a>Cómo ejecutar una acción de secuencia de comandos

Puede usar acciones de secuencia de comandos para personalizar HDInsight clústeres mediante el portal de Azure, Azure PowerShell, plantillas de Azure Resource Manager (ARM) o el SDK de .NET HDInsight. Para obtener instrucciones, consulte [cómo usar la acción de secuencia de comandos](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="sampleScripts"></a>Ejemplos de scripts personalizados

Microsoft proporciona secuencias de comandos de ejemplo para instalar componentes en un clúster de HDInsight. Las secuencias de comandos de ejemplo e instrucciones sobre cómo usarlas están disponibles en los siguientes vínculos:

- [Instalar y usar matiz en clústeres HDInsight](hdinsight-hadoop-hue-linux.md)
- [Instalar y usar R en clústeres HDInsight Hadoop](hdinsight-hadoop-r-scripts-linux.md)
- [Instalar y usar Solr en clústeres HDInsight](hdinsight-hadoop-solr-install-linux.md)
- [Instalar y usar Giraph en clústeres HDInsight](hdinsight-hadoop-giraph-install-linux.md)  

> [AZURE.NOTE] Los documentos vinculados encima son específicos para clústeres HDInsight basados en Linux. Secuencias de comandos que funcionan con HDInsight de Windows, vea [desarrollo de acción de secuencia de comandos con HDInsight (Windows)](hdinsight-hadoop-script-actions.md) o use los vínculos disponibles en la parte superior de cada artículo.

##<a name="troubleshooting"></a>Solución de problemas

A continuación se indican los errores que pueden surgir al usar scripts que ha desarrollado:

__Error__: `$'\r': command not found`. A veces seguido `syntax error: unexpected end of file`.

_Causa_: este error se produce si las líneas en una secuencia de comandos terminan con CRLF. Sistemas UNIX esperan solo LF como el final de la línea.

Este problema con mayor frecuencia se produce cuando se creó el script en un entorno de Windows, como CRLF es una línea común Finalizar para muchos editores de texto en Windows.

_Resolución_: si se trata de una opción en el editor de texto, seleccione formato Unix o LF el fin de línea. También puede utilizar los comandos siguientes en un sistema Unix para cambiar la CRLF a un LF:

> [AZURE.NOTE] Los siguientes comandos equivalen aproximadamente en que debe cambiar el fin de línea CRLF a LF. Seleccione una basada en las herramientas disponibles en el sistema.

| Comando | Notas |
| ------- | ----- |
| `unix2dos -b INFILE` | El archivo original se copiarán con una. Extensión BAK |
| `tr -d '\r' < INFILE > OUTFILE` | Archivo externo contendrá una versión con solo finales de LF |
| `perl -pi -e 's/\r\n/\n/g' INFILE` | Esto modificará el archivo directamente sin crear un nuevo archivo |
| ```sed 's/$'"/`echo \\\r`/" INFILE > OUTFILE``` | Archivo externo contendrá una versión con solo finales de LF.

__Error__: `line 1: #!/usr/bin/env: No such file or directory`.

_Causa_: este error se produce cuando se guardó la secuencia de comandos como UTF-8 con una marca de orden de bytes (BOM).

_Resolución_: guardar el archivo como ASCII o como UTF-8 sin una lista de materiales. También puede utilizar el comando siguiente en un sistema Linux o Unix para crear un nuevo archivo sin la lista de materiales:

    awk 'NR==1{sub(/^\xef\xbb\xbf/,"")}{print}' INFILE > OUTFILE

El comando anterior, reemplazar __este argumento__ con el archivo que contiene la lista de materiales. __Archivo externo__ debe ser un nuevo nombre de archivo que contendrá la secuencia de comandos sin la lista de materiales.

## <a name="seeAlso"></a>Pasos siguientes

* Obtenga información sobre cómo [clústeres personalizar HDInsight con la acción de secuencia de comandos](hdinsight-hadoop-customize-cluster-linux.md)

* Usar la [referencia de HDInsight .NET SDK](https://msdn.microsoft.com/library/mt271028.aspx) para obtener más información sobre cómo crear aplicaciones .NET que administración HDInsight

* Utilizar la [API de REST de HDInsight](https://msdn.microsoft.com/library/azure/mt622197.aspx) para aprender a usar el resto de opciones para realizar acciones de administración de clústeres HDInsight.

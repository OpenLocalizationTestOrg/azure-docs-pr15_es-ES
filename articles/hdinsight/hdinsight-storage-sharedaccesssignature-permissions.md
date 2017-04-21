<properties
pageTitle="Restringir el acceso de HDInsight a usar las firmas de acceso compartido de datos"
description="Obtenga información sobre cómo usar firmas de acceso compartido para restringir el acceso de HDInsight a los datos almacenados en BLOB de almacenamiento de Azure."
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
ms.date="10/11/2016"
ms.author="larryfr"/>

#<a name="use-azure-storage-shared-access-signatures-to-restrict-access-to-data-with-hdinsight"></a>Usar firmas de Azure almacenamiento compartido acceso para restringir el acceso a datos con HDInsight

HDInsight usa almacenamiento Azure BLOB para el almacenamiento de datos. Mientras HDInsight debe tener acceso completo a la blob utilizado como almacenamiento predeterminado para el clúster, puede restringir los permisos a los datos almacenados en otros Blob utilizado por el clúster. Por ejemplo, desea realizar algunos datos de solo lectura. Para ello, usar firmas de acceso compartido.

Firmas de acceso compartido (SA) son una característica de cuentas de almacenamiento de Azure que le permite limitar el acceso a los datos. Por ejemplo, proporcionar acceso de solo lectura a los datos. En este documento, aprenderá cómo usar asociaciones de seguridad para habilitar el acceso de lectura y solo lista a un contenedor de blob desde HDInsight.

##<a name="requirements"></a>Requisitos

* Una suscripción de Azure

* C# o Python. Código de ejemplo de C# se proporciona una solución de Visual Studio.

    * Visual Studio debe tener la versión 2013 o 2015
    
    * Python debe ser 2.7 o posterior

* Un HDInsight de Linux clúster o [Azure PowerShell] [ powershell] -si tiene un clúster basados en Linux existente, puede usar Ambari para agregar una firma de acceso compartido al clúster. Si no es así, puede usar PowerShell de Azure para crear un nuevo clúster y agregar una firma de acceso compartido durante la creación de un clúster.

* Los archivos de ejemplo de [https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature). Este repositorio tiene las siguientes:

    * Un proyecto de Visual Studio que puede crear un contenedor de almacenamiento, directiva almacenada y SA para su uso con HDInsight
    
    * Una secuencia de comandos de Python que puede crear un contenedor de almacenamiento, directiva almacenada y SA para su uso con HDInsight
    
    * Un script de PowerShell que puede crear un nuevo clúster de HDInsight y configúrelo para utilizar las asociaciones de seguridad.

##<a name="shared-access-signatures"></a>Firmas de acceso compartido

Hay dos formas de compartir firmas de acceso:

* Ad hoc: la hora de inicio, fecha de caducidad y los permisos para las asociaciones de seguridad están todas especificado en el URI de SAS (o implícita, en el caso de que se omite la hora de inicio).

* Almacena la directiva de acceso: una directiva de acceso almacenadas se define en un contenedor de recursos un contenedor de blob, tabla, cola o recurso compartido de archivos - y puede utilizarse para administrar las restricciones de una o varias firmas de acceso compartido. Cuando se asocia un SA con una directiva de acceso almacenadas, las asociaciones de seguridad hereda las restricciones - la hora de inicio, fecha de caducidad y permisos - definidos para la directiva de acceso almacenadas.

La diferencia entre las dos formas es importante para un escenario clave: revocación. Una SA es una dirección URL, para cualquier persona que obtiene las asociaciones de seguridad puede usarla, independientemente de que solicita comenzar. Si está publicado un SA, puede usar cualquier persona en el mundo. Una SA que se distribuye es válido hasta que se produce uno de los cuatro elementos:

1. Se alcanza la fecha de caducidad especificada en las asociaciones de seguridad.

2. Se ha alcanzado el tiempo de caducidad especificado en la directiva de acceso almacenadas hace referencia a las asociaciones de seguridad (si se hace referencia a una directiva de acceso almacenadas y especifica una fecha de caducidad). O bien, esto puede ocurrir porque transcurre el intervalo o porque se ha modificado la directiva de acceso almacenadas para tener un tiempo de caducidad en el pasado, que es una forma de revocar las asociaciones de seguridad.

3. Se elimina la directiva de acceso almacenadas hace referencia a las asociaciones de seguridad, que es otra forma de revocar las asociaciones de seguridad. Observe que si se vuelva a crear la directiva de acceso almacenados con exactamente el mismo nombre, todos los tokens SA existentes vuelva a ser válidos de acuerdo con los permisos asociados con esa directiva de acceso almacenadas (suponiendo que no ha superado el tiempo de caducidad en las asociaciones de seguridad). Si desea revocar las asociaciones de seguridad, asegúrese de usar un nombre distinto si se vuelve a crear la directiva de acceso con una fecha de caducidad en el futuro.

4. La clave de cuenta que se usó para crear la SA se regenera. Observe que hará que todos los componentes de la aplicación con esa clave de cuenta no se pueda autenticar hasta que se actualicen para usar la otra clave de cuenta válida o la clave de cuenta recién generado.

> [AZURE.IMPORTANT] Una firma de acceso compartido URI está asociada a la clave de cuenta utilizada para crear la firma y asociado almacena la directiva de acceso (si existe). Si no se especifica ninguna directiva de acceso almacenadas, es la única manera de revocar una firma de acceso compartido cambiar la clave de cuenta. 

Se recomienda usar siempre las directivas de acceso almacenadas, por lo que puede revocar firmas o ampliar la fecha de caducidad según sea necesario. Los pasos de este documento se usa almacenan las directivas de acceso para generar la SA.

Para obtener más información sobre firmas de acceso compartido, consulte [Descripción general del modelo de SA](../storage/storage-dotnet-shared-access-signature-part-1.md).

##<a name="create-a-stored-policy-and-generate-a-sas"></a>Crear una directiva almacenada y generar un SA

Actualmente debe crear una directiva almacenada mediante programación. Puede encontrar C# y ejemplo de Python de crear una directiva almacenada y SA en [https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature).

###<a name="create-a-stored-policy-and-sas-using-c"></a>Crear una directiva almacenada y SA mediante C\#

1. Abra la solución en Visual Studio.

2. En el Explorador de soluciones, haga clic en el proyecto __SASToken__ y seleccione __Propiedades__.

3. Seleccione __configuración__ y agregar los valores de las siguientes entradas:

    * StorageConnectionString: La cadena de conexión de la cuenta de almacenamiento que desea crear una directiva de almacenado y SA para. El formato debe ser `DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey` donde `myaccount` es el nombre de su cuenta de almacenamiento y `mykey` es la clave para la cuenta de almacenamiento.
    
    * Nombre del contenedor: El contenedor de la cuenta de almacenamiento que desea restringir el acceso a.
    
    * SASPolicyName: El nombre para la directiva almacenada que se creará.
    
    * FileToUpload: La ruta de acceso a un archivo que se cargarán en el contenedor.
    
4. Ejecute el proyecto. Aparecerá una ventana de consola y una vez que se ha generado las asociaciones de seguridad, se mostrará información similar al siguiente:

        Container SAS token using stored access policy: sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14
        
    Guarde el token de directiva SA, ya que lo necesitará cuando asocie el clúster de HDInsight en la cuenta de almacenamiento. También necesitará el nombre de la cuenta de almacenamiento y el nombre del contenedor.
    
###<a name="create-a-stored-policy-and-sas-using-python"></a>Crear una directiva almacenada y SA con Python

1. Abra el archivo SASToken.py y cambie los siguientes valores:

    * directiva\_nombre: el nombre para la directiva almacenada que se creará.
    
    * almacenamiento\_cuenta\_nombre: el nombre de su cuenta de almacenamiento.
    
    * almacenamiento\_cuenta\_clave: la clave para la cuenta de almacenamiento.
    
    * almacenamiento\_contenedor\_nombre: el contenedor de la cuenta de almacenamiento que desea restringir el acceso a.
    
    * ejemplo\_archivo\_ruta: la ruta de acceso a un archivo que se cargarán en el contenedor

2. Ejecute la secuencia de comandos. Cuando finalice la secuencia de comandos mostrará el token de SA similar al siguiente:

        sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14
    
    Guarde el token de directiva SA, ya que lo necesitará cuando asocie el clúster de HDInsight en la cuenta de almacenamiento. También necesitará el nombre de la cuenta de almacenamiento y el nombre del contenedor.
    
##<a name="use-the-sas-with-hdinsight"></a>Usar las asociaciones de seguridad con HDInsight

Al crear un clúster de HDInsight, debe especificar una cuenta de almacenamiento principal y, opcionalmente, puede especificar cuentas de almacenamiento adicional. Ambos métodos de agregar almacenamiento requieren acceso total a las cuentas de almacenamiento y los contenedores que se usan.

Para usar una firma de acceso compartido para limitar el acceso a un contenedor, debe agregar una entrada personalizada a la configuración del __sitio principal__ para el clúster.

* Para clústeres __basados en Windows__ o HDInsight de __Linux__ , puede hacerlo durante la creación de clúster con PowerShell.

* Para clústeres de HDInsight __basados en Linux__ , cambiar la configuración después de la creación de clúster con Ambari.

###<a name="create-a-new-cluster-that-uses-the-sas"></a>Crear un nuevo clúster que usa las asociaciones de seguridad

Un ejemplo de cómo crear un clúster de HDInsight que usa las asociaciones de seguridad se incluye en el `CreateCluster` directorio del repositorio. Para utilizarla, realice los siguientes pasos:

1. Abrir la `CreateCluster\HDInsightSAS.ps1` en un editor de texto y modifique los valores siguientes al principio del documento.

        # Replace 'mycluster' with the name of the cluster to be created
        $clusterName = 'mycluster'
        # Valid values are 'Linux' and 'Windows'
        $osType = 'Linux'
        # Replace 'myresourcegroup' with the name of the group to be created
        $resourceGroupName = 'myresourcegroup'
        # Replace with the Azure data center you want to the cluster to live in
        $location = 'North Europe'
        # Replace with the name of the default storage account to be created
        $defaultStorageAccountName = 'mystorageaccount'
        # Replace with the name of the SAS container created earlier
        $SASContainerName = 'sascontainer'
        # Replace with the name of the SAS storage account created earlier
        $SASStorageAccountName = 'sasaccount'
        # Replace with the SAS token generated earlier
        $SASToken = 'sastoken'
        # Set the number of worker nodes in the cluster
        $clusterSizeInNodes = 2
        
    Por ejemplo, cambiar `'mycluster'` el nombre del clúster que desea crear. Los valores de SA deben coincidir con los valores de los pasos anteriores al crear una cuenta de almacenamiento y el token de SA.
    
    Una vez que se han cambiado los valores, guarde el archivo.

1. Abra un nuevo símbolo del sistema de PowerShell de Azure. Si está familiarizado con PowerShell de Azure, o si no ha instalado, consulte [instalar y configurar Azure PowerShell][powershell].

2. En el símbolo del sistema, utilice la siguiente para autenticar a su suscripción de Azure:

        Login-AzureRmAccount
    
    Cuando se le solicita, inicie sesión con la cuenta para la suscripción de Azure.
    
    Si el inicio de sesión está asociado con varias suscripciones de Azure, tendrá que usar `Select-AzureRmSubscription` para seleccionar la suscripción que desea usar.

2. En el símbolo del sistema, cambie los directorios a la `CreateCluster` directorio que contiene el archivo HDInsightSAS.ps1. Utilice las siguientes acciones para ejecutar la secuencia de comandos
        
        .\HDInsightSAS.ps1
    
    Mientras se ejecuta la secuencia de comandos, registrará salida en el símbolo del sistema de PowerShell que crea el recurso de cuentas de grupo y almacenamiento. A continuación, le pedirá que introduzca el usuario HTTP para el clúster de HDInsight. Esta es la cuenta de usuario que se utiliza para proteger el acceso HTTP/s en el clúster.
    
    Si está creando un clúster basado en Linux, se le pedirá también SSH nombre de la cuenta de usuario y la contraseña. Se utiliza para iniciar una sesión remota al clúster.
    
    > [AZURE.IMPORTANT] Cuando se le solicite la HTTP/s o SSH nombre de usuario y contraseña, debe proporcionar una contraseña que cumpla los criterios siguientes:
    >
    > - Debe tener al menos 10 caracteres de longitud
    > - Debe contener al menos un dígito
    > - Debe contener al menos un carácter no alfanumérico
    > - Debe contener al menos una superior o letras minúsculas


Tardará un rato para esta secuencia de comandos completar, normalmente unos 15 minutos. Cuando finalice la secuencia de comandos sin errores, se ha creado el clúster.

###<a name="update-an-existing-cluster-to-use-the-sas"></a>Actualizar un clúster existente para usar las asociaciones de seguridad

Si tiene un clúster basados en Linux existente, puede agregar las SA a la configuración del __sitio principal__ con los pasos siguientes:

1. Abra la interfaz de usuario de web Ambari para el clúster. La dirección de esta página es https://YOURCLUSTERNAME.azurehdinsight.net. Cuando se le solicite, autenticar al clúster mediante el nombre de administrador (admin) y contraseña que utilizó al crear el clúster.

2. Desde el lado izquierdo de la interfaz de usuario de Ambari web, seleccione __HDFS__ y, a continuación, seleccione la ficha de __configuraciones__ en el medio de la página.

3. Seleccione la ficha __Opciones avanzadas__ y, a continuación, desplácese hasta que encuentre la sección __Personalizar sitios principales__ .

4. Expandir la sección __Personalizar sitios principales__ , a continuación, desplácese hasta el final y seleccione el vínculo __Agregar propiedad...__ . Utilice los siguientes valores para los campos de __clave__ y __valor__ :

    * __Tecla__: fs.azure.sas.CONTAINERNAME.STORAGEACCOUNTNAME.blob.core.windows.net
    
    * __Valor__: SA el devuelto por la aplicación de C# o Python ejecutó previamente
    
    Reemplazar el __nombre del contenedor__ con el nombre del contenedor que ha usado con la aplicación C# o SA. Reemplace __STORAGEACCOUNTNAME__ con el nombre de la cuenta de almacenamiento utilizado.

5. Haga clic en el botón __Agregar__ para guardar esta clave y el valor y luego haga clic en el botón __Guardar__ para guardar los cambios de configuración. Cuando se le solicite, agregue una descripción del cambio ("Agregar SA almacenamiento acceso" por ejemplo) y, a continuación, haga clic en __Guardar__.

    Cuando se han completado los cambios, haga clic en __Aceptar__ .

    > [AZURE.IMPORTANT] Guarda los cambios de configuración, pero debe reiniciar varios servicios antes de que el cambio surta efecto.

6. En la interfaz de usuario de Ambari web, seleccione __HDFS__ de la lista de la izquierda y, a continuación, seleccione __Reiniciar todos los__ en el desplegable de __Acciones de servicio__ de lista de la derecha. Cuando se le solicite, seleccione __activar el modo de mantenimiento__ y, a continuación, seleccione __Conform reinicie todos".

    Repita este proceso para las entradas MapReduce2 e hilo de la lista de la izquierda de la página.

7. Una vez estos reinician, seleccione cada uno de ellos y deshabilitan el modo de mantenimiento de las __Acciones de servicio__ de la lista desplegable.

##<a name="test-restricted-access"></a>Probar el acceso restringido

Para comprobar que dispone de acceso restringido, utilice los métodos siguientes:

* Para clústeres de HDInsight __basados en Windows__ , use Escritorio remoto para conectarse al clúster. Para obtener más información, vea [Conectar con HDInsight mediante RDP](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp) .

    Una vez conectado, use el icono de __línea de comandos de Hadoop__ en el escritorio para abrir un símbolo del sistema.

* Para clústeres de HDInsight __basados en Linux__ , use SSH para conectarse al clúster. Vea uno de los siguientes para obtener información sobre cómo utilizar SSH con clústeres basados en Linux:

    * [Utilizar SSH con basados en Linux Hadoop en HDInsight de Linux, OS X y Unix](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [Utilizar SSH con basados en Linux Hadoop en HDInsight de Windows.](hdinsight-hadoop-linux-use-ssh-windows.md)
    
Una vez conectado al clúster, realice los siguientes pasos para comprobar que es posible de solo lectura y la lista de elementos en la cuenta de almacenamiento SA:

1. En el símbolo del sistema, escriba lo siguiente. Reemplace __SASCONTAINER__ con el nombre del contenedor creado para la cuenta de almacenamiento SA. Reemplace __SASACCOUNTNAME__ con el nombre de la cuenta de almacenamiento utilizada para las asociaciones de seguridad:

        hdfs dfs -ls wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/
    
    Se mostrará el contenido del contenedor, que debe incluir el archivo que se cargó cuando se creó el contenedor y SA.
    
2. Utilice las siguientes acciones para comprobar que puede leer el contenido del archivo. Reemplazar el __SASCONTAINER__ y __SASACCOUNTNAME__ como en el paso anterior. Reemplazar el __nombre de archivo__ con el nombre del archivo que aparece en el comando anterior:

        hdfs dfs -text wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME
        
    Esto muestre el contenido del archivo.
    
3. Para descargar el archivo en el sistema de archivos local, utilice la siguiente:

        hdfs dfs -get wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME testfile.txt
    
    Esto Descargue el archivo a un archivo local denominado __testfile.txt__.

4. Utilice la siguiente para cargar el archivo local a un nuevo archivo denominado __testupload.txt__ en el almacenamiento de asociaciones de seguridad:

        hdfs dfs -put testfile.txt wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/testupload.txt
    
    Recibirá un mensaje similar al siguiente:
    
        put: java.io.IOException
        
    Este error se produce porque la ubicación de almacenamiento lectura + lista solo. Para poner los datos en el almacenamiento predeterminado para el clúster, que se puede escribir, utilice la siguiente:
    
        hdfs dfs -put testfile.txt wasbs:///testupload.txt
        
    En este momento, la operación debe completarse correctamente.
    
##<a name="troubleshooting"></a>Solución de problemas

###<a name="a-task-was-canceled"></a>Se ha cancelado una tarea

__Síntomas__: al crear un clúster mediante la secuencia de comandos de PowerShell, puede recibir el siguiente mensaje de error:

    New-AzureRmHDInsightCluster : A task was canceled.
    At C:\Users\larryfr\Documents\GitHub\hdinsight-azure-storage-sas\CreateCluster\HDInsightSAS.ps1:62 char:5
    +     New-AzureRmHDInsightCluster `
    +     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : NotSpecified: (:) [New-AzureRmHDInsightCluster], CloudException
        + FullyQualifiedErrorId : Hyak.Common.CloudException,Microsoft.Azure.Commands.HDInsight.NewAzureHDInsightClusterCommand

__Causa__: este error puede ocurrir si usa una contraseña para el usuario de administración/HTTP para el clúster o (si es basados en Linux clústeres,) el usuario SSH.

__Resolución__: usar una contraseña que cumpla los criterios siguientes:

- Debe tener al menos 10 caracteres de longitud
- Debe contener al menos un dígito
- Debe contener al menos un carácter no alfanumérico
- Debe contener al menos una superior o letras minúsculas

##<a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido a agregar almacenamiento de acceso limitado al clúster HDInsight, obtenga información sobre otras formas de trabajar con datos en el clúster:

* [Usar subárbol con HDInsight](hdinsight-use-hive.md)

* [Usar cerdo con HDInsight](hdinsight-use-pig.md)

* [Usar MapReduce con HDInsight](hdinsight-use-mapreduce.md)

[powershell]: ../powershell-install-configure.md

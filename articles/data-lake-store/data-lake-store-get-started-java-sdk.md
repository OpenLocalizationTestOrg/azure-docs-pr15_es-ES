<properties
   pageTitle="Utilizar datos lago almacén Java SDK para desarrollar aplicaciones | Microsoft Azure"
   description="Utilizar datos lago almacén Java SDK de Azure para desarrollar aplicaciones"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/17/2016"
   ms.author="nitinme"/>

# <a name="get-started-with-azure-data-lake-store-using-java"></a>Introducción a Azure datos lago tienda con Java

> [AZURE.SELECTOR]
- [Portal](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [API DE REST](data-lake-store-get-started-rest-api.md)
- [CLI de Azure](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)

Obtenga información sobre cómo usar el SDK de Java Azure datos lago almacén para realizar operaciones básicas, como crear carpetas, cargar y descargar archivos de datos, etcetera. Para obtener más información acerca de lago de datos, vea [Lago almacén de datos de Azure](data-lake-store-overview.md).

Puede obtener acceso a los documentos de Java SDK API para lago almacén de datos de Azure en [documentos de la API de Java del almacén de lago de datos de Azure](https://azure.github.io/azure-data-lake-store-java/javadoc/).

## <a name="prerequisites"></a>Requisitos previos

* Kit de desarrollo de Java (JDK 7 o superior, utilizando Java versión 1.7 o superior)
* Cuenta de la tienda de lago de datos de Azure. Siga las instrucciones en [Introducción a Azure datos lago tienda con el Portal de Azure](data-lake-store-get-started-portal.md).
* [Experto](https://maven.apache.org/install.html). Este tutorial usa a experto en las dependencias de generación y project. Aunque es posible crear sin usar un sistema de compilación como Maven o Gradle, estas hacen sistemas es mucho más fácil de administrar dependencias.
* (Opcional) Y IDE como [IDEA IntelliJ](https://www.jetbrains.com/idea/download/) o [Eclipse](https://www.eclipse.org/downloads/) o similares.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>¿Cómo se puede autenticar mediante Azure Active Directory?

En este tutorial, usamos un secreto de cliente de aplicación de Azure AD para recuperar un símbolo de Azure Active Directory (autenticación de servicio de servicio). Usamos este token para crear un objeto de cliente de almacén de datos lago para realizar operaciones de archivo y directorio de operaciones. Para obtener instrucciones sobre cómo autenticar con el almacén de lago de datos de Azure mediante el secreto de cliente, realizamos los siguientes pasos de alto nivel:

1. Crear una aplicación web de Azure AD
2. Recuperar el identificador de cliente, secreto cliente y token extremo de la aplicación web de Azure AD.
3. Configurar el acceso de la aplicación web de Azure AD en el que desea acceder desde la aplicación de Java que se va a crear almacén de datos lago archivo o carpeta.

Para obtener instrucciones sobre cómo llevar a cabo estos pasos, consulte [crear una aplicación de Active Directory](data-lake-store-authenticate-using-active-directory.md#create-an-active-directory-application).

Azure Active Directory ofrece otras opciones para recuperar un token. Puede seleccionar un número de mecanismos de autenticación que se adapten a su situación, por ejemplo, una aplicación que se ejecute en un explorador, una aplicación distribuida como una aplicación de escritorio o una aplicación de servidor que se ejecuta en local o en una máquina virtual Azure. También puede elegir entre diferentes tipos de credenciales, como contraseñas, certificados, autenticación multifactor de 2, etcetera. Además, Azure Active Directory permite sincronizar los usuarios de Active Directory local con la nube. Para obtener más información, vea [Escenarios de autenticación de Azure Active Directory](../active-directory/active-directory-authentication-scenarios.md). 

## <a name="create-a-java-application"></a>Crear una aplicación de Java

El código de ejemplo disponible [en GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/) recorridos a través del proceso de creación de archivos en el almacén de concatenación de archivos, descargar un archivo y eliminar algunos archivos de la tienda. Esta sección de este artículo le guiará a través de las partes principales del código.

1. Crear un proyecto de experto mediante [Arquetipo mvn](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) desde la línea de comandos o mediante un IDE. Para obtener instrucciones sobre cómo crear un proyecto de Java con IntelliJ, consulte [aquí](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html). Para obtener instrucciones sobre cómo crear un proyecto con Eclipse, vea [aquí](http://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm). 

2. Agregue las siguientes dependencias al archivo **pom.xml** Maven. Agregar el siguiente fragmento de texto entre el ** \</Version >** etiqueta y la ** \</proyecto >** etiqueta:

        <dependencies>
          <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-data-lake-store-sdk</artifactId>
            <version>2.0.4-SNAPSHOT</version>
          </dependency>
          <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-nop</artifactId>
            <version>1.7.21</version>
          </dependency>
        </dependencies>

    La primera dependencia es usar el SDK de almacén de datos lago (`azure-datalake-store`) del repositorio de experto. La segunda dependencia (`slf4j-nop`) es especificar qué framework de registro para esta aplicación. El SDK de almacén de datos lago usa fachada [slf4j](http://www.slf4j.org/) registro, que le permite elegir entre un número de marcos de registro populares, como log4j, Java registro, logback, etc., o ningún registro. En este ejemplo, se deshabilitar el registro, por lo tanto, usamos el enlace **slf4j nop** . Para usar otras opciones de registro de la aplicación, consulte [aquí](http://www.slf4j.org/manual.html#projectDep).

### <a name="add-the-application-code"></a>Agregar el código de la aplicación

Hay tres partes principales al código.

1. Obtener el token de Azure Active Directory

2. Usar el símbolo para crear a un cliente de almacén de datos lago.

3. Utilizar al cliente de almacén de datos lago para realizar operaciones.

#### <a name="step-1-obtain-an-azure-active-directory-token"></a>Paso 1: Obtener un token de Azure Active Directory.

El SDK de almacén de datos lago proporciona métodos útiles que le permiten obtener los símbolos de seguridad es necesarios hablar con la cuenta de almacén de datos lago. Sin embargo, el SDK exige utilizar estos métodos solamente. Puede usar cualquier otro medio de obtención de token físicos, como con el [SDK de Azure Active Directory](https://github.com/AzureAD/azure-activedirectory-library-for-java)o su propio código personalizado.

Para usar el SDK de almacén de datos lago para obtener el token de la aplicación Web de Active Directory que creó anteriormente, utilice los métodos estáticos en `AzureADAuthenticator` clase. Reemplazar **Rellenar en aquí** con los valores reales de la aplicación Web de Azure Active Directory.

    private static String clientId = "FILL-IN-HERE";
    private static String authTokenEndpoint = "FILL-IN-HERE";
    private static String clientKey = "FILL-IN-HERE";

    AzureADToken token = AzureADAuthenticator.getTokenUsingClientCreds(authTokenEndpoint, clientId, clientKey);

#### <a name="step-2-create-an-azure-data-lake-store-client-adlstoreclient-object"></a>Paso 2: Crear un objeto de cliente (ADLStoreClient) del almacén de lago de datos de Azure

Creación de un objeto [ADLStoreClient](https://azure.github.io/azure-data-lake-store-java/javadoc/) requiere que especifique el nombre de cuenta de almacén de datos lago y el token de Azure Active Directory generadas en el último paso. Tenga en cuenta que el nombre de cuenta de almacén de datos lago debe ser un nombre de dominio completo. Por ejemplo, reemplace **Rellenar en aquí** por algo parecido a **mydatalakestore.azuredatalakestore.net**.

    private static String accountFQDN = "FILL-IN-HERE";  // full account FQDN, not just the account name
    ADLStoreClient client = ADLStoreClient.createClient(accountFQDN, token);

### <a name="step-3-use-the-adlstoreclient-to-perform-file-and-directory-operations"></a>Paso 3: Usar el ADLStoreClient para realizar operaciones de directorios y archivos

El código siguiente contiene fragmentos de código de ejemplo de algunas operaciones comunes. Puede mirar el completa [API de SDK de Java del almacén de datos lago documentos](https://azure.github.io/azure-data-lake-store-java/javadoc/) del objeto **ADLStoreClient** para ver otras operaciones.
 
Tenga en cuenta que los archivos están leer y escritos en usando secuencias de Java estándar. Esto significa que puede crear niveles en cualquiera de las secuencias de Java encima de los flujos de datos de la tienda lago para aprovechar la funcionalidad de Java estándar (por ejemplo, imprimir secuencias de salida con formato o cualquiera de las secuencias de compresión o cifrado para funciones adicionales en la parte superior, etcetera.).

    // set file permission
    client.setPermission(filename, "744");

    // append to file
    stream = client.getAppendStream(filename);
    stream.write(getSampleContent());
    stream.close();

    // Read File
    InputStream in = client.getReadStream(filename);
    byte[] b = new byte[64000];
    while (in.read(b) != -1) {
        System.out.write(b);
    }
    in.close();

    // concatenate the two files into one
    List<String> fileList = Arrays.asList("/a/b/c.txt", "/a/b/d.txt");
    client.concatenateFiles("/a/b/f.txt", fileList);

    //rename the file
    client.rename("/a/b/f.txt", "/a/b/g.txt");

    // list directory contents
    List<DirectoryEntry> list = client.enumerateDirectory("/a/b", 2000);
    System.out.println("Directory listing for directory /a/b:");
    for (DirectoryEntry entry : list) {
        printDirectoryInfo(entry);
    }

    // delete directory along with all the subdirectories and files in it
    client.deleteRecursive("/a");

#### <a name="step-4-build-and-run-the-application"></a>Paso 4: Crear y ejecutar la aplicación

1. Para ejecutar desde dentro de un IDE, busque y haga clic en el botón **Ejecutar** . Para ejecutar de experto, use [ejecutivo: ejecutivo](http://www.mojohaus.org/exec-maven-plugin/exec-mojo.html).

2. Para generar un jar independiente que puede ejecutar desde la línea de comandos generar el tarro con todas las dependencias incluidas, mediante el [complemento de ensamblado de experto](http://maven.apache.org/plugins/maven-assembly-plugin/usage.html). El pom.xml en el [código fuente de ejemplo en github](https://github.com/Azure-Samples/data-lake-store-java-upload-download-get-started/blob/master/pom.xml) tiene un ejemplo de cómo hacerlo.


## <a name="next-steps"></a>Pasos siguientes

- [Proteger los datos en el almacén de datos de lago](data-lake-store-secure-data.md)
- [Usar el análisis de datos de Azure lago con el almacén de datos de lago](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Usar Azure HDInsight con el almacén de datos de lago](data-lake-store-hdinsight-hadoop-use-portal.md)

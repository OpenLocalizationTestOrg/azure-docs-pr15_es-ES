<properties
    pageTitle="Cómo utilizar Hudson con el almacenamiento de blobs | Microsoft Azure"
    description="Describe cómo usar a Hudson con el almacenamiento de blobs de Windows Azure como un repositorio para generar artefactos."
    services="storage"
    documentationCenter="java"
    authors="dineshmurthy"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="10/18/2016" 
    ms.author="dinesh"/>

# <a name="using-azure-storage-with-a-hudson-continuous-integration-solution"></a>Usar el almacenamiento de Azure con una solución de integración de Hudson continuo

## <a name="overview"></a>Información general

La información siguiente muestra cómo usar el almacenamiento de blobs como un repositorio de artefactos de compilación creado por una solución Hudson continuo integración (elemento de configuración) o como un origen de los archivos que se pueden descargar para usarlo en un proceso de generación. Uno de los escenarios donde encontrará esto útil es cuando está codificación en un entorno de desarrollo ágil (con Java u otros idiomas), versiones se ejecutan basándose en la integración continua y necesita un repositorio para los efectos de compilación para que podría, por ejemplo, compártalos con otros miembros de la organización, los clientes o mantener un archivo.  Otro escenario es cuando su propio trabajo de compilación requiere otros archivos, por ejemplo, las dependencias para descargar como parte de la entrada de generación.

En este tutorial va a usar el complemento de almacenamiento de Azure para los elementos de configuración de Hudson facilitado por Microsoft.

## <a name="introduction-to-hudson"></a>Introducción a Hudson ##

Hudson permite la integración continua de un proyecto de software al permitir a los desarrolladores integrar fácilmente sus cambios en el código y han versiones generado automáticamente y con frecuencia, lo que aumenta la productividad de los desarrolladores. Versiones tienen versiones y generar artefactos se pueden cargar en distintos repositorios. En este artículo le mostrará cómo usar el almacenamiento de blobs de Windows Azure como repositorio de los artefactos de compilación. También se muestran cómo descargar las dependencias de almacenamiento de blobs de Windows Azure.

Más información sobre Hudson puede encontrarse en [Hudson reunirse](http://wiki.eclipse.org/Hudson-ci/Meet_Hudson).

## <a name="benefits-of-using-the-blob-service"></a>Ventajas de usar el servicio de blobs ##

Ventajas de usar el servicio de blobs para hospedar su artefactos de compilación de desarrollo ágil incluyen:

- Alta disponibilidad de los artefactos de compilación o dependencias descargables.
- Rendimiento de la solución de elemento de configuración de Hudson carga los artefactos de compilación.
- Rendimiento cuando los clientes y socios descargar los artefactos de compilación.
- Controlar las directivas de acceso de usuario, con una selección entre el acceso anónimo, acceso de acceso compartido basadas en la caducidad de la firma, privado acceso, etcetera.

## <a name="prerequisites"></a>Requisitos previos ##

Necesita lo siguiente para usar el servicio de Blob con la solución de elemento de configuración de Hudson:

- Una solución Hudson la integración continua.

    Si actualmente no tiene una solución de elemento de configuración de Hudson, puede ejecutar una solución de elemento de configuración de Hudson mediante el procedimiento siguiente:

    1. En un equipo habilitado para Java, descargue la guerra Hudson desde <http://hudson-ci.org/>.
    2. En el símbolo del sistema que se abre la carpeta que contiene la guerra Hudson, ejecute la guerra Hudson. Por ejemplo, si ha descargado versión 3.1.2:

        `java -jar hudson-3.1.2.war`

    3. En el explorador, abra `http://localhost:8080/`. Se abrirá el panel Hudson.

    4. Al utilizar por primera vez de Hudson, completar la configuración inicial en `http://localhost:8080/`.

    5. Después de completar la configuración inicial, cancelar la instancia de ejecución de la guerra Hudson, vuelva a iniciar la guerra Hudson y vuelva a abrir el panel Hudson, `http://localhost:8080/`, que se usará para instalar y configurar el complemento de almacenamiento de Azure.

        Mientras una solución de elemento de configuración de Hudson típica se debe configurar para que se ejecute como un servicio en el que se ejecuta la guerra Hudson en la línea de comandos será suficiente para este tutorial.

- Una cuenta de Azure. Puede registrarse para una cuenta de Azure en <http://www.azure.com>.

- Una cuenta de almacenamiento de Azure. Si todavía no tiene una cuenta de almacenamiento, puede crear una siguiendo los pasos en [crear una cuenta de almacenamiento](storage-create-storage-account.md#create-a-storage-account).

- Se recomienda estar familiarizado con la solución de elemento de configuración de Hudson, aunque no es necesario, como el siguiente contenido un ejemplo básico para mostrar los pasos necesarios al utilizar el servicio de Blob como un repositorio para el elemento de configuración de Hudson generación artefactos.

## <a name="how-to-use-the-blob-service-with-hudson-ci"></a>Cómo usar el servicio de blobs con el elemento de configuración de Hudson ##

Para usar el servicio de blobs con Hudson, debe instalar el complemento de almacenamiento de Azure, configurar el complemento para usar su cuenta de almacenamiento y, a continuación, cree una acción posterior a la compilación que carga los artefactos de compilación a su cuenta de almacenamiento. En las siguientes secciones, se describen estos pasos.

## <a name="how-to-install-the-azure-storage-plugin"></a>Cómo instalar el complemento de almacenamiento de Azure ##

1. En el panel Hudson, haga clic en **Administrar Hudson**.
2. En la página **Administrar Hudson** , haga clic en **Administrar complementos**.
3. Haga clic en la ficha **disponible** .
4. Haga clic en **otros**.
5. En la sección **Uploaders de muestra** , seleccione **complemento de Microsoft Azure almacenamiento**.
6. Haga clic en **instalar**.
7. Una vez completada la instalación, reinicie a Hudson.

## <a name="how-to-configure-the-azure-storage-plugin-to-use-your-storage-account"></a>Cómo configurar el complemento de almacenamiento de Azure para usar su cuenta de almacenamiento ##

1. En el panel Hudson, haga clic en **Administrar Hudson**.
2. En la página **Administrar Hudson** , haga clic en **Configurar el sistema**.
3. En la sección **Configuración de la cuenta de Microsoft Azure almacenamiento** :

    una. Escriba su nombre de cuenta de almacenamiento, que puede obtener desde el [Portal de Azure](https://portal.azure.com).

    b. Escriba la clave de cuenta de almacenamiento, también puede obtener desde el [Portal de Azure](https://portal.azure.com).

    c. Use el valor predeterminado para la **Dirección URL del extremo de servicio de blobs** si está utilizando la nube pública de Azure. Si está utilizando una nube Azure diferente, use el extremo según lo especificado en el [Portal de Azure](https://portal.azure.com) para su cuenta de almacenamiento.

    d. Haga clic en **validar credenciales de almacenamiento** para validar su cuenta de almacenamiento.

    e. [Opcional] Si tiene cuentas de almacenamiento adicional que desee a disposición de los elementos de configuración de Hudson, haga clic en **Agregar más cuentas de almacenamiento**.

    f. Haga clic en **Guardar** para guardar la configuración.

## <a name="how-to-create-a-post-build-action-that-uploads-your-build-artifacts-to-your-storage-account"></a>Cómo crear una acción posterior a la compilación que carga los artefactos de compilación a su cuenta de almacenamiento ##

Con fines de instrucción, primero se deberá crear un trabajo que creará varios archivos y, a continuación, agregue la acción posterior a la compilación para cargar los archivos a su cuenta de almacenamiento.

1. En el panel Hudson, haga clic en **Nueva tarea**.
2. Nombre del trabajo **MyJob**, haga clic en **crear un trabajo de software de estilo libre**y, a continuación, haga clic en **Aceptar**.
3. En la sección **crear** de la configuración del trabajo, haga clic en **Agregar paso de generación** y elija **ejecutar Windows por lotes comando**.
4. En el **comando**, use los comandos siguientes:

        md text
        cd text
        echo Hello Azure Storage from Hudson > hello.txt
        date /t > date.txt
        time /t >> date.txt

5. En la sección **Acciones posteriores a la compilación** de la configuración del trabajo, haga clic en **cargar artefactos al almacenamiento de blobs de Microsoft Azure**.
6. **Nombre de la cuenta de almacenamiento**, seleccione la cuenta de almacenamiento para usar.
7. **Nombre del contenedor**, especifique el nombre del contenedor. (El contenedor se creará si aún no existe cuando se cargan los artefactos de compilación.) Puede utilizar variables de entorno, en este ejemplo indique **${JOB_NAME}** como el nombre del contenedor.

    **Sugerencia**

    A continuación el **comando** sección donde ha escrito una secuencia de comandos de **Windows ejecutar por lotes comando** es un vínculo a las variables de entorno reconocida Hudson. Haga clic en ese vínculo para obtener información sobre los nombres de variable de entorno y sus descripciones. Tenga en cuenta que no se permiten variables de entorno que contienen caracteres especiales, como la variable de entorno **BUILD_URL** , como un nombre de contenedor o la ruta de acceso virtual común.

8. En este ejemplo, haga clic en **Crear nuevo contenedor público de forma predeterminada** . (Si desea usar un contenedor privado, necesitará crear una firma de acceso compartido para permitir el acceso. Que está fuera del ámbito de este artículo. Puede obtener más información sobre las firmas acceso compartido en [Uso compartido acceso firmas (SA)](storage-dotnet-shared-access-signature-part-1.md).)
9. [Opcional] Haga clic en **Limpiar contenedor antes de cargar** si desea que el contenedor se borra de contenido antes de cargarán los artefactos de compilación (deja desactivada si no desea limpiar el contenido del contenedor).
10. **Lista de artefactos para cargar**, escriba * *texto /*.txt**.
11. **Ruta de acceso virtual común para artefactos cargados**, escriba **${Generar\_ID} / ${generar\_número}**.
12. Haga clic en **Guardar** para guardar la configuración.
13. En el panel Hudson, haga clic en **Crear ahora** para ejecutar **MyJob**. Examine la salida de la consola de estado. Mensajes de estado para el almacenamiento de Azure se incluirán en el resultado de la consola cuando se inicia la acción posterior a la compilación cargar artefactos de compilación.
14. Cuando finalice el trabajo, puede examinar los artefactos de compilación abriendo el blob público.

    una. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

    b. Haga clic en **almacenamiento**.

    c. Haga clic en el nombre de la cuenta de almacenamiento que ha usado para Hudson.

    d. Haga clic en **contenedores**.

    e. Haga clic en el contenedor denominado **myjob**, que es la versión en minúsculas del nombre del trabajo que asignó cuando creó el trabajo Hudson. Nombres de contenedor y blob están en minúsculas (y distingue mayúsculas de minúsculas) en el almacenamiento de Azure. En la lista de blobs para el contenedor denominado **myjob** debería ver **hello.txt** y **date.txt**. Copie la dirección URL para uno de estos elementos y ábralo en el explorador. Verá el archivo de texto que se ha cargado como una muestra de compilación.

Solo una acción posterior a la compilación que carga artefactos al almacenamiento de blobs de Windows Azure puede crearse por trabajo. Observe que la acción posterior a la compilación para cargar artefactos al almacenamiento de blobs de Windows Azure puede especificar diferentes archivos (incluidos los caracteres comodín) y rutas de acceso a archivos de la **Lista de artefactos cargar** con un punto y coma como separador. Por ejemplo, si la compilación Hudson genera txt en la carpeta de **compilación** del área de trabajo y archivos JAR y desea cargar ambos con el almacenamiento de blobs de Windows Azure, use las siguientes acciones para el valor de la **Lista de artefactos cargar** : **Generar /\*.jar; compilación /\*.txt**. También puede usar dos puntos dobles sintaxis para especificar una ruta de acceso para usar en el nombre de blobs de Windows. Por ejemplo, si desea que la JARs para obtener cargado utilizando **binarios** en la ruta de acceso de blobs de Windows y los archivos TXT a obtener cargado con **avisos** en la ruta de acceso de blobs, use los siguientes para el valor de la **Lista de artefactos cargar** : **Generar /\*. jar::binaries; compilación /\*. txt::notices**.

## <a name="how-to-create-a-build-step-that-downloads-from-azure-blob-storage"></a>Cómo crear un paso de compilación descarga desde el almacenamiento de blobs de Windows Azure ##

Los pasos siguientes muestran cómo configurar un paso de compilación para descargar los elementos de almacenamiento de blobs de Windows Azure. Esto sería útil si desea incluir elementos en su compilación, por ejemplo, JARs que mantiene en el almacenamiento de blobs de Windows Azure.

1. En la sección **crear** de la configuración del trabajo, haga clic en **Agregar paso de generación** y elija **descargar desde el almacenamiento de blobs de Windows Azure**.
2. **Nombre de la cuenta de almacenamiento**, seleccione la cuenta de almacenamiento para usar.
3. **Nombre del contenedor**, especifique el nombre del contenedor que tiene el BLOB que desee descargar. Puede utilizar variables de entorno.
4. **Nombre de blobs**, especifique el nombre de blobs de Windows. Puede utilizar variables de entorno. Además, puede usar un asterisco como carácter comodín después de especificar las letras iniciales del nombre del blob. Por ejemplo, **proyecto\* ** especificaría todos los blobs cuyos nombres empiezan por **proyecto**.
5. [Opcional] Para **Descargar la ruta de acceso**, especifique la ruta de acceso en el equipo de Hudson donde desea descargar los archivos de almacenamiento de blobs de Windows Azure. También se pueden utilizar variables de entorno. (Si no se proporciona un valor para **Descargar la ruta de acceso**, los archivos de almacenamiento de blobs de Windows Azure se descargarán al área de trabajo de la tarea).

Si tiene elementos adicionales que desea descargar desde el almacenamiento de blobs de Windows Azure, puede crear pasos de compilación adicionales.

Después de ejecutar una generación, puede comprobar el resultado de la consola de historial de generación o busque la ubicación de descarga para ver si el BLOB esperaba se descargaron correctamente.

## <a name="components-used-by-the-blob-service"></a>Usada el servicio de blobs de componentes ##

El siguiente proporciona una descripción general de los componentes del servicio de blobs de Windows.

- **Cuenta de almacenamiento**: todo el acceso al almacenamiento de Azure se realiza a través de una cuenta de almacenamiento. Esto es el nivel superior del espacio de nombres para tener acceso a BLOB. Una cuenta puede contener un número ilimitado de contenedores, como su tamaño total es en 100 TB.
- **Contenedor**: un contenedor proporciona una agrupación de un conjunto de BLOB. Todos los blobs deben estar en un contenedor. Una cuenta puede contener un número ilimitado de contenedores. Un contenedor puede almacenar una cantidad ilimitada de BLOB.
- **Blobs**: un archivo de cualquier tipo y tamaño. Existen dos tipos de BLOB que puede almacenarse en el almacenamiento de Azure: BLOB bloque y página. La mayoría de archivos es BLOB de bloque. Un blob único bloque puede ser de hasta 200 GB. Este tutorial usa BLOB de bloque. Página blobs, otro tipo de blob, pueden ser hasta 1 TB en tamaño y son más eficaces cuando se modifiquen los intervalos de bytes en un archivo con frecuencia. Para obtener más información acerca de BLOB, vea [Descripción bloque BLOB, anexar Blobs y Blobs de página](http://msdn.microsoft.com/library/azure/ee691964.aspx).
- **Formato de dirección URL**: BLOB es accesible con el formato de dirección URL siguiente:

    `http://storageaccount.blob.core.windows.net/container_name/blob_name`

    (El formato anterior se aplica a la nube pública de Azure. Si está utilizando una nube Azure diferente, use el extremo dentro del [Portal de Azure](https://portal.azure.com) para determinar el punto final de la dirección URL.)

    En el formato anterior, `storageaccount` representa el nombre de su cuenta de almacenamiento, `container_name` representa el nombre de su contenedor, y `blob_name` representa el nombre de su blob, respectivamente. En el nombre del contenedor, puede tener varias rutas, separados por una barra diagonal, **/**. El nombre del contenedor de ejemplo en este tutorial fue **MyJob**, y **${Generar\_ID} / ${generar\_número}** se utilizó para la ruta de acceso virtual comunes, lo que el blob de una dirección URL de la forma siguiente:

    `http://example.blob.core.windows.net/myjob/2014-05-01_11-56-22/1/hello.txt`

## <a name="next-steps"></a>Pasos siguientes

- [Reunirse Hudson](http://wiki.eclipse.org/Hudson-ci/Meet_Hudson)
- [Almacenamiento de Azure SDK de Java](https://github.com/azure/azure-storage-java)
- [Referencia SDK de cliente de almacenamiento de Azure](http://dl.windowsazure.com/storage/javadoc/)
- [API de REST de servicios de almacenamiento de Azure](https://msdn.microsoft.com/library/azure/dd179355.aspx )
- [Blog del equipo de almacenamiento de Azure](http://blogs.msdn.com/b/windowsazurestorage/)

Para obtener más información, vea también el [Centro para desarrolladores de Java](https://azure.microsoft.com/develop/java/).
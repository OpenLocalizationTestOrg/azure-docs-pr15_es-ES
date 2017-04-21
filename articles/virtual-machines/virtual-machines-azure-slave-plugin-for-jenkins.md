<properties
    pageTitle="Cómo usar el complemento de subordinado Azure con la integración continua Jenkins | Microsoft Azure"
    description="Describe cómo usar el complemento de subordinado Azure con Jenkins la integración continua."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="rmcmurray"
    manager="wpickett"
    editor="" />

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-multiple"
    ms.devlang="java"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="robmcm"/>

# <a name="how-to-use-the-azure-slave-plug-in-with-jenkins-continuous-integration"></a>Cómo usar el complemento de subordinado Azure con Jenkins la integración continua

Puede usar el complemento de subordinado Azure para Jenkins a disposición subordinado nodos en Azure cuando ejecuta distribuido genera.

## <a name="install-the-azure-slave-plug-in"></a>Instalar el complemento de subordinado Azure

1. En el panel Jenkins, haga clic en **Administrar Jenkins**.

1. En la página **Administrar Jenkins** , haga clic en **Administrar complementos**.

1. Haga clic en la ficha **disponible** .

1. En el campo de filtro encima de la lista de complementos disponibles, escriba **Azure** para limitar la lista complementos relevantes.

    Si decide para desplazarse por la lista de complementos disponibles, encontrará al subordinado Azure complemento en la sección **administración de clúster y distribuido generar** .

1. Active la casilla de verificación de **Complemento de Azure subordinado** .

1. Haga clic en **instalar sin reiniciar** o **Descargar ahora e instalar después de reiniciar**.

Ahora que está instalado el complemento, los pasos siguientes son para configurar el complemento con el perfil de suscripción de Azure y para crear una plantilla que se utilizará en la creación de la máquina virtual para el nodo secundario.


## <a name="configure-the-azure-slave-plug-in-with-your-subscription-profile"></a>Configurar el complemento de subordinado Azure con su perfil de suscripción

Un perfil de la suscripción, también contemplado como configuración de publicación, es un archivo XML que contiene las credenciales de seguridad y alguna información adicional, que tendrá que trabajar con Azure en su entorno de desarrollo. Para configurar el complemento de subordinado Azure, debe:

* El identificador de la suscripción
* Un certificado de administración de la suscripción

Estos se pueden encontrar en el [perfil de la suscripción]. A continuación se muestra un ejemplo de un perfil de suscripción.

    <?xml version="1.0" encoding="utf-8"?>

        <PublishData>

        <PublishProfile SchemaVersion="2.0" PublishMethod="AzureServiceManagementAPI">

        <Subscription

            ServiceManagementUrl="https://management.core.windows.net"

            Id="<Subscription ID value>"

            Name="Pay-As-You-Go"
            ManagementCertificate="<Management certificate value>" />

        </PublishProfile>

    </PublishData>

Una vez que el perfil de la suscripción, siga estos pasos para configurar el complemento de subordinado Azure:

1. En el panel Jenkins, haga clic en **Administrar Jenkins**.

1. Haga clic en **Configurar el sistema**.

1. Desplácese hasta la página para buscar la sección de la **nube** .

1. Haga clic en **Agregar nuevo nube > Microsoft Azure**.

    ![sección de la nube][cloud section]

    Esto le mostrará los campos dónde debe introducir los detalles de suscripción.

    ![configuración de la suscripción][subscription configuration]

1. Copie los valores de certificado de administración y el identificador de suscripción de su perfil de suscripción y pegarlos en los campos correspondientes.

    Al copiar el certificado de identificador y administración de la suscripción, no incluya las comillas que encierran los valores.

1. Haga clic en **comprobar la configuración**.

1. Verifica la configuración correcta, haga clic en **Guardar**.

## <a name="set-up-a-virtual-machine-template-for-the-azure-slave-plug-in"></a>Configurar una plantilla de máquina virtual para el subordinado Azure complemento

Una plantilla de máquina virtual define los parámetros que se use el complemento para crear un nodo secundario en Azure. En los pasos siguientes, vamos a crear una plantilla para una máquina virtual de Ubuntu.

1. En el panel Jenkins, haga clic en **Administrar Jenkins**.

1. Haga clic en **Configurar el sistema**.

1. Desplácese hasta la página para buscar la sección de la **nube** .

1. En la sección de la **nube** , buscar la **Plantilla de máquina Virtual de Azure agregar**y, a continuación, haga clic en **Agregar**.

    ![agregar la plantilla de máquina virtual][add vm template]

    Esto le mostrará los campos donde especificar detalles acerca de la plantilla que está creando.

    ![configuración general en blanco][blank general configuration]

1. En el cuadro **nombre** , escriba un nombre de servicio de nube de Azure. Si el nombre escrito hace referencia a un servicio de nube existente, la máquina virtual se aprovisionará en dicho servicio. En caso contrario, Azure creará una nueva.

1. En el cuadro **Descripción** , escriba el texto que describa la plantilla que está creando. Esto es solo para los registros y no se usa en una máquina virtual de aprovisionamiento.

1. El cuadro **etiquetas** se usa para identificar la plantilla que se va a crear y posteriormente se usa para hacer referencia a la plantilla al crear un trabajo Jenkins. Para nuestro propósito, escriba **linux** en este cuadro.

1. En la lista de la **región** , haga clic en la región donde se creará la máquina virtual.

1. En la lista de **Tamaños de máquina Virtual** , haga clic en el tamaño adecuado.

1. En el cuadro **Nombre de la cuenta de almacenamiento** , especifique una cuenta de almacenamiento donde se creará la máquina virtual. Asegúrese de que se encuentra en la misma región como el servicio de nube que se va a usar. Si desea que el almacenamiento nueva creación, puede dejar este cuadro en blanco.

1. Tiempo de retención especifica el número de minutos antes de Jenkins elimina a un subordinado inactivo. Deje el valor predeterminado de 60. También puede elegir cerrar el subordinado en lugar de eliminarlos cuando está inactivo. Para ello, seleccione la casilla de verificación **Solo apagado (no elimine) tras el tiempo de retención** .

1. En la lista de **uso** , haga clic en la condición apropiada cuando se usará este nodo secundario. Por ahora, haga clic en **Utilice este nodo tanto como sea posible**.

    En este momento, el formulario debe ser similar a lo siguiente:

    ![configuración de la plantilla general de control][checkpoint general template config]

    El siguiente paso es proporcionar detalles sobre la imagen de sistema operativo que desea que su subordinado que se cree.

1. En el cuadro de **familia de la imagen o el identificador** , tiene que especificar qué imagen del sistema se instalarán en la máquina virtual. Puede seleccionar de una lista de familias de imagen, o bien especificar una imagen personalizada.

    Si desea seleccionar de una lista de familias de imagen, escriba el primer carácter (distingue mayúsculas de minúsculas) del nombre de la familia de imagen. Por ejemplo, escribir **U** mostrará una lista de familias de servidor Ubuntu. Después de seleccionar de la lista, Jenkins usará la versión más reciente de la imagen de esa familia de sistema al hacer el aprovisionamiento de la máquina virtual.

    ![Ejemplo de lista de imagen de sistema operativo][OS Image list sample]

    Si tiene una imagen personalizada que desea usar en su lugar, escriba el nombre de la imagen personalizada. Nombres de imagen personalizada no se muestran en una lista, por lo que deberá asegurarse de que el nombre está escrito correctamente.

    En este tutorial, escriba **U** para que aparezca una lista de imágenes de Ubuntu y, a continuación, haga clic en **Ubuntu Server 14.04 LTADOS**.

1. En la lista de **Métodos de inicio** , haga clic en **SSH**.

1. Copie la siguiente secuencia de comandos y pegarlo en el cuadro de **Secuencia de comandos de inicialización** .

        # Install Java

        sudo apt-get -y update

        sudo apt-get install -y openjdk-7-jdk

        sudo apt-get -y update --fix-missing

        sudo apt-get install -y openjdk-7-jdk

        # Install git

        sudo apt-get install -y git

        #Install ant

        sudo apt-get install -y ant

        sudo apt-get -y update --fix-missing

        sudo apt-get install -y ant

    La secuencia de comandos de inicialización se ejecutará después de crear la máquina virtual. En este ejemplo, la secuencia de comandos instala Java, Git y ant.

1. En los cuadros **nombre de usuario** y **contraseña** , escriba los valores preferidos para la cuenta de administrador que se creará en la máquina virtual.

1. Haga clic en **Comprobar plantilla** para comprobar si los parámetros especificados son válidos.

1. Haga clic en **Guardar**.


## <a name="create-a-jenkins-job-that-runs-on-a-slave-node-on-azure"></a>Crear un trabajo Jenkins que se ejecute en un nodo secundario en Azure

En esta sección, creará una tarea de Jenkins que se ejecutará en un nodo secundario en Azure. Debe tener su propio proyecto hacia arriba en GitHub para seguir a lo largo.

1. En el panel Jenkins, haga clic en **Nuevo elemento**.

1. Escriba un nombre para la tarea que está creando.

1. El tipo de proyecto, haga clic en **proyecto estilo libre**.

1. Haga clic en **Aceptar**.

1. En la página de configuración de la tarea, seleccione **restringir donde se pueden ejecutar este proyecto**.

1. En el cuadro **Expresión de etiqueta** , escriba **linux**. En la sección anterior, creamos una plantilla secundaria que hemos denominado **linux**, que es lo que vamos a especificar aquí.

1. En la sección **crear** , haga clic en **Agregar paso de generación** y seleccione **shell Execute**.

1. Editar la siguiente secuencia de comandos, reemplace **(su nombre de cuenta de GitHub)**, **(su nombre de proyecto)**y **(el directorio de proyecto)** con los valores apropiados y pegue el script modificado en el área de texto que aparece.

        # Clone from git repo

        currentDir="$PWD"

        if [ -e (your project directory) ]; then

            cd (your project directory)

            git pull origin master

        else

            git clone https://github.com/(your GitHub account name)/(your project name).git

        fi

        # change directory to project

        cd $currentDir/(your project directory)

        #Execute build task

        ant

1. Haga clic en **Guardar**.

1. En el panel Jenkins, desplace el puntero sobre la tarea que acaba de crear y haga clic en la flecha de lista desplegable para mostrar opciones de la tarea.

1. Haga clic en **crear ahora**.

Jenkins se cree un nodo secundario con la plantilla que creó en la sección anterior y ejecutar el script especificado en el paso de compilación para esta tarea.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre el uso de Azure con Java, consulte el [Centro para desarrolladores de Java de Azure].

<!-- URL List -->

[Centro para desarrolladores de Java de Azure]: https://azure.microsoft.com/develop/java/
[perfil de la suscripción]: http://go.microsoft.com/fwlink/?LinkID=396395

<!-- IMG List -->

[cloud section]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-cloud-section.png
[subscription configuration]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-account-configuration-fields.png
[add vm template]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-add-vm-template.png
[blank general configuration]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-slave-template-general-configuration-blank.png
[checkpoint general template config]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-slave-template-general-configuration.png
[OS Image list sample]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-os-family-list-sample.png
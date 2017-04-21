<properties
    pageTitle="Cómo usar el complemento de subordinado Azure con la integración continua Hudson | Microsoft Azure"
    description="Describe cómo usar el complemento de subordinado Azure con la integración continua Hudson."
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

# <a name="how-to-use-the-azure-slave-plug-in-with-hudson-continuous-integration"></a>Cómo usar el complemento de subordinado Azure con Hudson la integración continua

El complemento para Hudson de subordinado Azure le permite aprovisionar nodos subordinado en Azure cuando ejecuta distribuido genera.

## <a name="install-the-azure-slave-plug-in"></a>Instalar al complemento secundario de Azure

1. En el panel Hudson, haga clic en **Administrar Hudson**.

1. En la página **Administrar Hudson** , haga clic en **Administrar complementos**.

1. Haga clic en la ficha **disponible** .

1. Haga clic en **Buscar** y escriba **Azure** para limitar la lista complementos relevantes.

    Si decide para desplazarse por la lista de complementos disponibles, encontrará al subordinado Azure complemento en la sección **administración de clúster y distribuido generar** en la pestaña de **otras personas** .

1. Seleccione la casilla de verificación para **Azure subordinado complemento**.

1. Haga clic en **instalar**.

1. Reinicie a Hudson.

Ahora que está instalado el complemento, sería los siguientes pasos para configurar el complemento con el perfil de suscripción de Azure y para crear una plantilla que se utilizará en la creación de la máquina virtual para el nodo secundario.

## <a name="configure-the-azure-slave-plug-in-with-your-subscription-profile"></a>Configurar al complemento secundario de Azure con su perfil de suscripción

Un perfil de la suscripción, también contemplado como configuración de publicación, es un archivo XML que contiene las credenciales de seguridad y alguna información adicional, que tendrá que trabajar con Azure en su entorno de desarrollo. Para configurar el complemento de subordinado Azure, debe:

* El identificador de la suscripción
* Un certificado de administración de la suscripción

Estos se pueden encontrar en el [perfil de la suscripción]. A continuación se muestra un ejemplo de un perfil de suscripción.

    <?xml version="1.0" encoding="utf-8"?>

        <PublishData>

        <PublishProfile SchemaVersion="2.0" PublishMethod="AzureServiceManagementAPI">

        <Subscription

            ServiceManagementUrl="https://management.core.windows.net"

            Id="<Subscription ID>"

            Name="Pay-As-You-Go"
            ManagementCertificate="<Management certificate value>" />

        </PublishProfile>

    </PublishData>

Una vez que el perfil de la suscripción, siga estos pasos para configurar el complemento de subordinado Azure.

1. En el panel Hudson, haga clic en **Administrar Hudson**.

1. Haga clic en **Configurar el sistema**.

1. Desplácese hasta la página para buscar la sección de la **nube** .

1. Haga clic en **Agregar nuevo nube > Microsoft Azure**.

    ![Agregar nuevo nube][add new cloud]

    Esto le mostrará los campos dónde debe introducir los detalles de suscripción.

    ![configurar el perfil][configure profile]

1. Copie el certificado de identificador y administración de la suscripción de su perfil de suscripción y péguelas en los campos correspondientes.

    Al copiar el certificado de identificador y administración de la suscripción, **no** se incluyen las comillas que encierran los valores.

1. Haga clic en **Comprobar configuración**.

1. Cuando la configuración se comprueba correctamente, haga clic en **Guardar**.

## <a name="set-up-a-virtual-machine-template-for-the-azure-slave-plug-in"></a>Configurar una plantilla de máquina virtual para el subordinado Azure complemento

Una plantilla de máquina virtual define los parámetros que se use el complemento para crear un nodo secundario en Azure. En los pasos siguientes se creará plantilla para una VM Ubuntu.

1. En el panel Hudson, haga clic en **Administrar Hudson**.

1. Haga clic en **Configurar el sistema**.

1. Desplácese hasta la página para buscar la sección de la **nube** .

1. En la sección de la **nube** , busque **Agregar plantilla de máquina Virtual de Azure** y haga clic en el botón **Agregar** .

    ![agregar la plantilla de máquina virtual][add vm template]

1. Especifique un nombre de servicio de nube en el campo **nombre** . Si el nombre que especifique hace referencia a un servicio de nube existente, la máquina virtual se aprovisionará en dicho servicio. En caso contrario, Azure creará una nueva.

1. En el campo **Descripción** , escriba el texto que describa la plantilla que está creando. Esta información solo con fines documental y no se usa en una máquina virtual de aprovisionamiento.

1. En el campo de **etiquetas** , escriba **linux**. Esta etiqueta se usa para identificar la plantilla que se va a crear y posteriormente se usa para hacer referencia a la plantilla al crear un trabajo Hudson.

1. Seleccione una región donde se creará la máquina virtual.

1. Seleccione el tamaño de la máquina virtual correspondiente.

1. Especifique una cuenta de almacenamiento donde se creará la máquina virtual. Asegúrese de que se encuentra en la misma región como el servicio de nube que se va a usar. Si desea que el almacenamiento nueva creación, puede dejar este campo en blanco.

1. Tiempo de retención especifica el número de minutos antes de Hudson elimina a un subordinado inactivo. Deje el valor predeterminado de 60.

1. En **uso**, seleccione la condición apropiada cuando se usará este nodo secundario. Por ahora, seleccione **Utilice este nodo tanto como sea posible**.

    En este momento, el formulario tendría un aspecto similar a lo siguiente:

    ![configuración de la plantilla][template config]

1. En la **familia de la imagen o el identificador** tiene que especificar qué imagen del sistema se instalarán en la máquina virtual. Puede seleccionar de una lista de familias de imagen, o bien especificar una imagen personalizada.

    Si desea seleccionar de una lista de familias de imagen, escriba el primer carácter (distingue mayúsculas de minúsculas) del nombre de la familia de imagen. Por ejemplo, escribir **U** mostrará una lista de familias de servidor Ubuntu. Una vez que seleccione de la lista, Jenkins usará la versión más reciente de la imagen de esa familia de sistema al hacer el aprovisionamiento de la máquina virtual.

    ![Lista de familia de sistemas operativos][OS family list]

    Si tiene una imagen personalizada que desea usar en su lugar, escriba el nombre de la imagen personalizada. Nombres de imagen personalizada no se muestran en una lista para que tenga que asegurarse de que el nombre está escrito correctamente.    

    En este tutorial, escriba **U** para abrir una lista de imágenes de Ubuntu y seleccione **Ubuntu Server 14.04 LTADOS**.

1. **Método de inicio**, seleccione **SSH**.

1. La siguiente secuencia de comandos de copiar y pegar en el campo de **secuencia de comandos de inicialización** .

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

    La **secuencia de comandos de inicialización** se ejecutará después de crea la máquina virtual. En este ejemplo, la secuencia de comandos instala Java, git y ant.

1. En los campos **nombre de usuario** y **contraseña** , escriba los valores preferidos para la cuenta de administrador que se creará en la máquina virtual.

1. Haga clic en **Comprobar plantilla** para comprobar si los parámetros especificados son válidos.

1. Haga clic en **Guardar**.

## <a name="create-a-hudson-job-that-runs-on-a-slave-node-on-azure"></a>Crear un trabajo Hudson que se ejecute en un nodo secundario en Azure

En esta sección, creará una tarea de Hudson que se ejecutará en un nodo secundario en Azure.

1. En el panel Hudson, haga clic en **Nueva tarea**.

1. Escriba un nombre para el trabajo que está creando.

1. Para el tipo de trabajo, seleccione **crear una tarea de software de estilo libre**.

1. Haga clic en **Aceptar**.

1. En la página de configuración de trabajo, seleccione **restringir donde se pueden ejecutar este proyecto**.

1. Seleccione el **menú nodo y etiqueta** y **linux** (hemos especificado esta etiqueta al crear la plantilla de máquina virtual en la sección anterior).

1. En la sección **crear** , haga clic en **Agregar paso de generación** y seleccione **shell Execute**.

1. Editar la siguiente secuencia de comandos, reemplace **{su nombre de cuenta de github}**, **{el nombre del proyecto}**y **{el directorio del proyecto}** con los valores apropiados y pegue el script modificado en el área de texto que aparece.

        # Clone from git repo

        currentDir="$PWD"

        if [ -e {your project directory} ]; then

            cd {your project directory}

            git pull origin master

        else

            git clone https://github.com/{your github account name}/{your project name}.git

        fi

        # change directory to project

        cd $currentDir/{your project directory}

        #Execute build task

        ant

1. Haga clic en **Guardar**.

1. En el panel Hudson, encuentre el trabajo que acaba de crea y haga clic en el icono de **programación una compilación** .

Hudson después crear un nodo secundario con la plantilla que creó en la sección anterior y ejecute el script especificado en el paso de compilación para esta tarea.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre el uso de Azure con Java, consulte el [Centro para desarrolladores de Java de Azure].

<!-- URL List -->

[Centro para desarrolladores de Java de Azure]: https://azure.microsoft.com/develop/java/
[perfil de la suscripción]: http://go.microsoft.com/fwlink/?LinkID=396395

<!-- IMG List -->

[add new cloud]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-addcloud.png
[configure profile]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-configureprofile.png
[add vm template]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-addnewvmtemplate.png
[template config]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-templateconfig1-withdata.png
[OS family list]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-oslist.png


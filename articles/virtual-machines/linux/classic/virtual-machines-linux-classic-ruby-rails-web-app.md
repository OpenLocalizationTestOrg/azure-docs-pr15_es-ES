<properties
    pageTitle="Hospedar un Ruby en sitio Web de guías en una VM Linux | Microsoft Azure"
    description="Configurar y hospedar Ruby en sitio Web basada en guías en Azure con un equipo virtual Linux."
    services="virtual-machines-linux"
    documentationCenter="ruby"
    authors="rmcmurray"
    manager="wpickett"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="web"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="ruby"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="ruby-on-rails-web-application-on-an-azure-vm"></a>Ruby en la aplicación Web de guías en una máquina virtual de Azure

Este tutorial muestra cómo hospedar un Ruby en sitio Web de guías en Azure con un equipo virtual Linux.  

En este tutorial se valida utilizando Ubuntu Server 14.04 LTADOS. Si utiliza una distribución Linux diferente, debe modificar los pasos para instalar las guías.

> [AZURE.IMPORTANT] Azure tiene dos diferentes modelos de implementación para crear y trabajar con recursos: [Administrador de recursos y clásica](../../../resource-manager-deployment-model.md).  En este artículo trata sobre utiliza el modelo de implementación clásico. Microsoft recomienda que más nuevas implementaciones de usar el modelo de administrador de recursos.

## <a name="create-an-azure-vm"></a>Crear una máquina virtual de Azure

Empiece por crear una máquina virtual de Azure con una imagen de Linux.

Para crear la máquina virtual, puede usar el portal de clásico Azure o la interfaz de línea de comandos de Azure (CLI).

### <a name="azure-management-portal"></a>Portal de administración de Azure

1. Inicie sesión en el [portal de clásico de Azure](http://manage.windowsazure.com)
2. Haga clic en **nuevo** > **calcular** > **Máquina Virtual** > **crear rápido**. Seleccione una imagen de Linux.
3. Escriba una contraseña.

Después de que se aprovisiona la máquina virtual, haga clic en el nombre de la máquina virtual y haga clic en **panel**. Busque el extremo SSH, que aparece en **SSH detalles**.

### <a name="azure-cli"></a>CLI de Azure

Siga los pasos de [crear Máquina Virtual ejecuta Linux][vm-instructions].

Después de la máquina virtual se aprovisiona, puede obtener el extremo SSH ejecutando el siguiente comando:

    azure vm endpoint list <vm-name>  

## <a name="install-ruby-on-rails"></a>Instalar Ruby en guías

1. Usar SSH para conectarse a la máquina virtual.

2. Desde la sesión SSH, use los comandos siguientes para instalar Ruby en la máquina virtual:

        sudo apt-get update -y
        sudo apt-get upgrade -y
        sudo apt-get install ruby ruby-dev build-essential libsqlite3-dev zlib1g-dev nodejs -y

    La instalación puede tardar unos minutos. Cuando se complete, use el comando siguiente para comprobar que está instalado Ruby:

        ruby -v

    Devuelve la versión de rubí que haya instalado.

3. Use el comando siguiente para instalar las guías:

        sudo gem install rails --no-rdoc --no-ri -V

    Usar los--indicadores rdoc n y--no ri para omitir la instalación de la documentación, que es más rápida.
    Este comando probablemente tendrá mucho tiempo en ejecución, para agregar -V mostrará información sobre el progreso de la instalación.

## <a name="create-and-run-an-app"></a>Crear y ejecutar una aplicación

Mientras sigue conectado a través de SSH, ejecute los comandos siguientes:

    rails new myapp
    cd myapp
    rails server -b 0.0.0.0 -p 3000

El comando [nuevo](http://guides.rubyonrails.org/command_line.html#rails-new) crea una nueva aplicación de guías. El comando de [servidor](http://guides.rubyonrails.org/command_line.html#rails-server) inicia el servidor web WEBrick que viene con guías. (Para el uso de producción, probablemente quiera que usar un servidor distinto, como unicornio o pasajeros.)

Debe ver un resultado similar al siguiente.

    => Booting WEBrick
    => Rails 4.2.1 application starting in development on http://0.0.0.0:3000
    => Run `rails server -h` for more startup options
    => Ctrl-C to shutdown server
    [2015-06-09 23:34:23] INFO  WEBrick 1.3.1
    [2015-06-09 23:34:23] INFO  ruby 1.9.3 (2013-11-22) [x86_64-linux]
    [2015-06-09 23:34:23] INFO  WEBrick::HTTPServer#start: pid=27766 port=3000

## <a name="add-an-endpoint"></a>Agregar un punto final

1. Vaya al [portal clásica Azure] [ management-portal] y seleccione la máquina virtual.

    ![lista de máquina virtual][vmlist]

2. Seleccione **EXTREMOS de** la parte superior de la página y, a continuación, haga clic en **+ Agregar extremo** en la parte inferior de la página.

    ![página extremos][endpoints]

3. En el cuadro de diálogo **Agregar extremo** , haga clic en "Agregar un extremo independiente" y haga clic en la flecha **siguiente** .

    ![cuadro de diálogo nuevo punto final][new-endpoint1]

3. En la siguiente página del cuadro de diálogo, escriba la información siguiente:

    * **Nombre**: HTTP

    * **Protocolo**: TCP

    * **Puerto público**: 80

    * **Puerto privado**: 3000

    Esta opción creará un puerto público 80 que enruta el tráfico al puerto privado del 3000, donde está escuchando el servidor guías.

    ![cuadro de diálogo nuevo punto final][new-endpoint]

4. Haga clic en la marca de verificación para guardar el extremo.

5. Debería aparecer un mensaje que indica la **Actualización en curso**. Una vez que desaparece este mensaje, el extremo está activo. Ahora puede probar la aplicación desplazándose hasta el nombre DNS de la máquina virtual. El sitio Web debería ser similar al siguiente:

    ![página predeterminada de guías][default-rails-cloud]

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha manualmente la mayoría de los pasos. En un entorno de producción, desea escribir su aplicación en un equipo de desarrollo e implementar la máquina virtual de Azure. Además, la mayoría de los entornos de producción hospedar la aplicación de guías junto con otro proceso de servidor como Apache o NginX, qué controladores solicitar enrutamiento a varias instancias de la aplicación de guías y servir recursos estáticos. Para obtener más información, consulte http://rubyonrails.org/deploy/.

Para obtener más información acerca de rubí en guías, visite el [Ruby guías guías][rails-guides].

Para usar los servicios de Azure desde la aplicación ideogramas y transcripción fonética, consulte:

* [Almacenar los datos no estructurados con BLOB][blobs]

* [Pares de clave/valor de la tienda mediante tablas][tables]

* [Servir contenido de gran ancho de banda con la red de entrega de contenido][cdn-howto]

<!-- WA.com links -->
[blobs]: ../../../storage/storage-ruby-how-to-use-blob-storage.md
[cdn-howto]: https://azure.microsoft.com/develop/ruby/app-services/
[management-portal]: https://manage.windowsazure.com/
[tables]: ../../../storage/storage-ruby-how-to-use-table-storage.md
[vm-instructions]: ../../virtual-machines-linux-classic-createportal.md

<!-- External Links -->
[rails-guides]: http://guides.rubyonrails.org/
[sqlite3]: http://www.sqlite.org/

<!-- Images -->

[default-rails-cloud]: ./media/virtual-machines-linux-classic-ruby-rails-web-app/basicrailscloud.png
[vmlist]: ./media/virtual-machines-linux-classic-ruby-rails-web-app/vmlist.png
[endpoints]: ./media/virtual-machines-linux-classic-ruby-rails-web-app/endpoints.png
[new-endpoint]: ./media/virtual-machines-linux-classic-ruby-rails-web-app/newendpoint.png
[new-endpoint1]: ./media/virtual-machines-linux-classic-ruby-rails-web-app/newendpoint1.png

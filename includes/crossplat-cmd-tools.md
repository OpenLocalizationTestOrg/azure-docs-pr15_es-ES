#<a name="how-to-use-the-azure-command-line-tools-for-mac-and-linux"></a>Cómo usar las herramientas de línea de comandos de Azure para Mac y Linux

Esta guía describe cómo usar las herramientas de línea de comandos de Azure para Mac y Linux para crear y administrar servicios en Azure. Los escenarios cubiertos incluyen **instalar las herramientas**, **importar la configuración de publicación**, **crear y administrar sitios Web de Azure**y **crear y administrar máquinas virtuales de Azure**. Documentación de referencia completa, consulte [Azure herramienta de línea de comandos para Mac y documentación Linux][reference-docs]. 

##<a name="table-of-contents"></a>Tabla de contenido
* [¿Cuáles son las herramientas de línea de comandos de Azure para Mac y Linux](#Overview)
* [Cómo instalar las herramientas de línea de comandos de Azure para Mac y Linux](#Download)
* [Cómo crear una cuenta de Azure](#CreateAccount)
* [Cómo descargar y configuración de publicación de importación](#Account)
* [Cómo crear y administrar un sitio Web de Azure](#WebSites)
* [Cómo crear y administrar una máquina Virtual de Azure](#VMs)


<h2><a id="Overview"></a>¿Cuáles son las herramientas de línea de comandos de Azure para Mac y Linux</h2>

Las herramientas de línea de comandos de Azure para Mac y Linux son un conjunto de herramientas de línea de comandos para implementar y administrar servicios de Azure.
 
Las tareas compatibles son las siguientes:

* Importar configuración de publicación.
* Crear y administrar sitios Web de Azure.
* Crear y administrar máquinas virtuales de Azure.

Para obtener una lista completa de los comandos compatibles, escriba `azure -help` en la línea de comandos después de instalar las herramientas, o consulte la [documentación de referencia][reference-docs].

<h2><a id="Download">Cómo instalar las herramientas de línea de comandos de Azure para Mac y Linux</a></h2>

La siguiente lista contiene información para instalar las herramientas de línea de comandos, dependiendo del sistema operativo:

* **Mac**: descargar el [Instalador de Azure SDK][mac-installer]. Abra el archivo descargado .pkg y complete los pasos de instalación cuando se le pida.

* **Linux**: instalar la última versión de [Node.js] [ nodejs-org] (consulte [Node.js instalar a través del Administrador de paquetes][install-node-linux]), a continuación, ejecute el siguiente comando:

        npm install azure-cli -g

    **Nota**: debe ejecutar este comando con privilegios elevados:

        sudo npm install azure-cli -g

* **Windows**: ejecute el instalador de Winows (archivo .msi), que está disponible aquí: [Herramientas de línea de comandos de Azure][windows-installer].


Para probar la instalación, escriba `azure` en el símbolo del sistema. Si la instalación se realizó correctamente, verá una lista de todos los disponibles `azure` comandos.

<h2><a id="CreateAccount"></a>Cómo crear una cuenta de Azure</h2>

Para usar las herramientas de línea de comandos de Azure para Mac y Linux, necesita una cuenta de Azure.

Abra un navegador web y vaya a [http://www.windowsazure.com] [ windowsazuredotcom] y haga clic en la **versión de prueba gratuita** en la esquina superior derecha.

![Sitio Web de Azure][Azure Web Site]

Siga las instrucciones para crear una cuenta.

<h2><a id="Account"></a>Cómo descargar y configuración de publicación de importación</h2>

Para empezar, debe descargar e importar la configuración de publicación. Esto le permitirá utilizar las herramientas para crear y administrar los servicios de Azure. Para descargar la configuración de publicación, use la `account download` comando:

    azure account download

Se abrirá el explorador predeterminado y le pida que inicie sesión en el Portal de administración. Después de iniciar sesión, su `.publishsettings` se descargará el archivo. Tome nota de donde se guarda el archivo.

A continuación, importar el `.publishsettings` archivo, ejecute el comando siguiente, reemplazando `{path to .publishsettings file}` con la ruta de acceso a su `.publishsettings` archivo:

    azure account import {path to .publishsettings file}

Puede quitar toda la información almacenada por el <code>import</code> comando utilizando la <code>account clear</code> comando:

    azure account clear

Para ver una lista de opciones para `account` comandos, use la `-help` opción:

    azure account -help

Después de importar la configuración de publicación, debe eliminar la `.publishsettings` archivo por motivos de seguridad.

> [AZURE.NOTE] Al importar la configuración de publicación, se almacenan las credenciales para acceder a su suscripción de Azure dentro de su `user` carpeta. Su `user` carpeta está protegida por el sistema operativo. Sin embargo, se recomienda que realizar pasos adicionales para cifrar el `user` carpeta. Puede hacerlo de las siguientes maneras:    
> 
> - En Windows, modifique las propiedades de carpeta o usar BitLocker.
> - En Mac, active FileVault para la carpeta.
> - En Ubuntu, use la característica de directorio de inicio de cifrado. Otras distribuciones Linux ofrecen características equivalentes.

Ya está listo para ser crear y administrar sitios Web de Azure y máquinas virtuales de Azure.  

<h2><a id="WebSites"></a>Cómo crear y administrar un sitio Web de Azure</h2>

###<a name="create-a-website"></a>Crear un sitio Web

Para crear un sitio Web de Azure, cree primero un directorio vacío denominado `MySite` y vaya a ese directorio.

A continuación, ejecute el siguiente comando:

    azure site create MySite --git

El resultado de este comando contendrá la dirección URL predeterminada para el sitio Web recién creado. La `--git` opción le permite usar git para publicar en su sitio Web mediante la creación de git repositorios en el directorio de aplicación local y en el centro de datos de su sitio Web. Tenga en cuenta que si su carpeta local ya es un repositorio git, el comando agrega un nuevo remoto al repositorio existente, que señala al repositorio en el centro de datos de su sitio Web.

Tenga en cuenta que puede ejecutar la `azure site create` comando con cualquiera de las siguientes opciones:

* `--location [location name]`. Esta opción permite especificar la ubicación del centro de datos en el que se crea el sitio Web (por ejemplo, "Oeste nos"). Si omite esta opción, estará promted para elegir una ubicación.
* `--hostname [custom host name]`. Esta opción permite especificar un nombre de host personalizado para el sitio Web.

A continuación, puede agregar contenido al directorio de sitio Web. Usar el flujo de git normal (`git add`, `git commit`) para confirmar su contenido. Use el siguiente comando git para insertar el contenido de sitio Web en Azure: 

    git push azure master

###<a name="set-up-publishing-from-github"></a>Configurar la publicación de GitHub

Para establecer la publicación continua desde un repositorio de GitHub, use la `--GitHub` opción al crear un sitio:

    auzre site create MySite --github --githubusername username --githubpassword password --githubrepository githubuser/reponame

Si tiene un clonar local de un repositorio de GitHub, o si tiene un repositorio con una sola referencia remota a un repositorio de GitHub, este comando Publicar automáticamente código en el repositorio de GitHub a su sitio. Luego de los cambios que se inserta en el repositorio de GitHub se publicará automáticamente en su sitio.

Al configurar la publicación de GitHub, la bifurcación predeterminada que se utiliza es la rama principal. Para especificar una rama distinta, ejecute el comando siguiente desde su repositorio local:

    azure site repository <branch name>

###<a name="configure-app-settings"></a>Establecer la configuración de la aplicación

Configuración de la aplicación es pares de valor de clave que están disponibles para su aplicación en tiempo de ejecución. Cuando se establece para un sitio Web de Azure, valores de configuración de aplicación reemplazará la configuración con la misma clave que se definen en el archivo Web.config de su sitio. Para las aplicaciones de Node.js y PHP, la configuración de la aplicación está disponibles como variables de entorno. En el ejemplo siguiente se muestra cómo configurar un par de valor de clave:

    azure site config add <key>=<value> 

Para ver una lista de todos los pares de clave y valor, use las siguientes acciones:

    azure site config list 

O bien, si sabe la clave y busca el valor, puede usar:

    azure site config get <key> 

Si desea cambiar el valor de una clave existente debe desactivar la clave existente y vuelva a agregarla. El comando Borrar es:

    azure site config clear <key> 

###<a name="list-and-show-sites"></a>Lista y mostrar los sitios

Para obtener una lista de los sitios Web, utilice el siguiente comando:

    azure site list

Para obtener información detallada sobre un sitio, use la `site show` comando. En el ejemplo siguiente se muestra detalles para `MySite`:

    azure site show MySite

###<a name="stop-start-or-restart-a-site"></a>Detener, iniciar o reiniciar un sitio

Puede detener, iniciar o reiniciar un sitio con el `site stop`, `site start`, o `site restart` comandos:

    azure site stop MySite
    azure site start MySite
    azure site restart MySite

###<a name="delete-a-site"></a>Eliminar un sitio

Por último, puede eliminar un sitio con el `site delete` comando:

    azure site delete MySite

Tenga en cuenta que, si está ejecutando cualquiera de encima de comandos de dentro de la carpeta donde ejecutó `site create`, no es necesario especificar el nombre del sitio `MySite` como último parámetro.

Para ver una lista completa de `site` comandos, use la `-help` opción:

    azure site -help 

<h2><a id="VMs"></a>Cómo crear y administrar una máquina Virtual de Azure</h2>

se crea una máquina Virtual de Azure desde una imagen de máquina virtual (un archivo .vhd) que proporciona o que está disponible en la Galería de imágenes. Para ver las imágenes que se encuentran disponibles, use la `vm image list` comando:

    azure vm image list

Puede aprovisionar e iniciar una máquina virtual desde una de las imágenes disponibles con la `vm create` comando. En el ejemplo siguiente se muestra cómo crear una máquina virtual Linux (llamado `myVM`) de una imagen en la Galería de imágenes (CentOS 6.2). El nombre de usuario de raíz y la contraseña para la máquina virtual son `myusername` y `Mypassw0rd` respectivamente. (Tenga en cuenta que el `--location` parámetro especifica el centro de datos en la que se creó la máquina virtual. Si omite el `--location` parámetro, se le pedirá que elija una ubicación.)

    azure vm create myVM OpenLogic__OpenLogic-CentOS-62-20120509-en-us-30GB.vhd myusername --location "West US"

Puede pasar el `--ssh` indicador (Linux) o `--rdp` marca (Windows) para `vm create` para habilitar las conexiones remotas a la máquina virtual recién creado.

Si prefiere se aprovisiona una máquina virtual de una imagen personalizada, puede crear una imagen desde un archivo .vhd con la `vm image create` comando y luego use la `vm create` comando proporcionando la máquina virtual. En el ejemplo siguiente se muestra cómo crear una imagen de Linux (llamado `myImage`) de un archivo .vhd local. (El `--location` parámetro especifica los datos en el que está almacenada la imagen.)

    azure vm image create myImage /path/to/myImage.vhd --os linux --location "West US"

En lugar de crear una imagen desde un .vhd local, puede crear una imagen desde un .vhd almacenado en el almacenamiento de blobs de Windows Azure. Puede hacerlo con la `blob-url` parámetro:

    azure vm image create myImage --blob-url <url to .vhd in Blob Storage> --os linux

Después de crear una imagen, puede configurar una máquina virtual de la imagen mediante `vm create`. El comando siguiente crea una máquina virtual denominada `myVM` de la imagen que creó anteriormente (`myImage`).

    azure vm create myVM myImage myusername --location "West US"

Una vez haya proporcionado una máquina virtual, desea crear extremos para permitir el acceso remoto a su equipo virtual (por ejemplo). En el ejemplo siguiente se utiliza la `vm create endpoint` comando para abrir el puerto externo 22 y puerto local 22 en `myVM`:

    azure vm endpoint create myVM 22 22

Puede obtener información detallada sobre una máquina virtual (incluidos dirección IP, el nombre DNS y la información de extremo) con la `vm show` comando:

    azure vm show myVM

Para cerrar, iniciar, o reiniciar la máquina virtual, use uno de los siguientes comandos:

    azure vm shutdown myVM
    azure vm start myVM
    azure vm restart myVM

Y por último, para eliminar la máquina virtual, use la `vm delete` comando:

    azure vm delete myVM

Para obtener una lista completa de comandos para crear y administrar máquinas virtuales de Windows, use la `-h` opción:

    azure vm -h

<!-- IMAGES -->
[Azure Web Site]: ./media/crossplat-cmd-tools/freetrial.png

<!-- LINKS -->
[nodejs-org]: http://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[windows-installer]: http://go.microsoft.com/fwlink/?LinkID=275464
[reference-docs]: http://go.microsoft.com/fwlink/?LinkId=252246
[windowsazuredotcom]: http://www.windowsazure.com


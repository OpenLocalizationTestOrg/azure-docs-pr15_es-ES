<properties
   pageTitle="Implementación de Azure máquina virtual con Chef | Microsoft Azure"
   description="Aprenda a usar a Chef automatizado máquina virtual implementación y configuración de Microsoft Azure"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="diegoviso"
   manager="timlt"
   tags="azure-service-management,azure-resource-manager"
   editor=""/>

<tags ms.service="virtual-machines-windows" ms.workload="infrastructure-services"
ms.tgt_pltfrm="vm-multiple"
ms.devlang="na"
ms.topic="article"
ms.date="05/19/2015"
ms.author="diviso"/>

# <a name="automating-azure-virtual-machine-deployment-with-chef"></a>Automatización de implementación de Azure máquina virtual con Chef

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Chef es una excelente herramienta para proporcionar automatización y desea configuraciones de estado.

Con esta última versión de la api de la nube, Chef proporciona integración perfecta con Azure, que le da la capacidad de aprovisionar e implementar los Estados de configuración a través de un solo comando.

En este artículo, mostraremos cómo configurar su entorno de Chef para aprovisionar máquinas virtuales de Windows Azure y le guían a través de la creación de una directiva o "Libro de cocina" y, a continuación, implementar este libro de cocina para una máquina virtual Azure.

Empecemos

## <a name="chef-basics"></a>Conceptos básicos de Chef

Antes de comenzar, le sugiero que revisar los conceptos básicos de Chef. Hay excelente material <a href="http://www.chef.io/chef" target="_blank">aquí</a> y se recomienda tiene una rápida lectura antes de intentar este procedimiento. Sin embargo, se resumen los conceptos básicos antes de empezar.

El siguiente diagrama muestra la arquitectura de alto nivel Chef.

![][2]

Chef tiene tres componentes principales de la arquitectura: servidor Chef, Chef cliente (nodo) y Chef estación de trabajo.

El servidor de Chef es nuestro punto de administración y existen dos opciones para el servidor de Chef: una solución hospedada o una solución local. Vamos a usar una solución hospedada.

El cliente de Chef (nodo) es el agente que se encuentra en los servidores que está administrando.

Los Chef es nuestra estación de trabajo de administración donde creamos nuestros directivas y ejecutar nuestros comandos de administración. Se ejecute el comando de **cuchillo** desde la estación de trabajo de Chef administrar nuestra infraestructura.

También es el concepto de "Cocina" y "Recetas". Estos son eficazmente las directivas se definen y se aplican a los servidores.

## <a name="preparing-the-workstation"></a>Preparar la estación de trabajo

En primer lugar, le permite preparar la estación de trabajo. Estoy usando una estación de trabajo estándar de Windows. Se necesita crear un directorio para almacenar nuestros archivos de configuración y la cocina.

Primero, cree un directorio denominado C:\chef.

A continuación, cree un segundo directorio denominado c:\chef\cookbooks.

Es necesario descargar el archivo de configuración de Azure modo Chef puedan comunicarse con nuestro suscripción Azure.

Descargar su configuración de [aquí](https://manage.windowsazure.com/publishsettings/)de publicación.

Guarde el archivo de configuración de publicación en C:\chef.

##<a name="creating-a-managed-chef-account"></a>Crear una cuenta de Chef administrada

Registrarse para una cuenta de Chef hospedada [aquí](https://manage.chef.io/signup).

Durante el proceso de suscripción, le pedirá que cree una nueva organización.

![][3]

Una vez creada la organización, descargue el starter kit.

![][4]

> [AZURE.NOTE] Si recibe un mensaje de advertencia que se restablecerán las claves, es Aceptar para continuar como no tenemos ninguna infraestructura configurada todavía.

Archivo zip starter kit contiene los archivos de configuración de la organización y las teclas.

##<a name="configuring-the-chef-workstation"></a>Configurar la estación de trabajo de Chef

Extraer el contenido de la starter.zip chef a C:\chef.

Copiar todos los archivos de chef-starter\chef-repo\.chef al directorio c:\chef.

El directorio ahora debe ser similar en el siguiente ejemplo.

![][5]

Ahora debería tener cuatro archivos, incluyendo el archivo de publicación de Azure en la raíz de c:\chef.

Los archivos PEM contienen su organización y la administración de claves privadas para la comunicación mientras el archivo knife.rb contiene la configuración de Cuchilla. Debemos editar el archivo knife.rb.

Abra el archivo en el editor de su elección y modificar "cookbook_path" eliminando la /... desde la ruta de acceso para que aparezca como se muestra a continuación.

    cookbook_path  ["#{current_dir}/cookbooks"]

Agregue la siguiente línea que refleja el nombre de su Azure publica el archivo de configuración.

    knife[:azure_publish_settings_file] = "yourfilename.publishsettings"

El archivo knife.rb debe ser similar al siguiente ejemplo.

![][6]

Estas líneas se asegurará que hace referencia al directorio de cocina en c:\chef\cookbooks cuchillo y también usa el archivo de configuración de publicación de Azure durante las operaciones de Azure.

## <a name="installing-the-chef-development-kit"></a>Instalar el Kit de desarrollo de Chef

Siguiente [descargar e instalar](http://downloads.getchef.com/chef-dk/windows) la ChefDK (Chef Development Kit) para configurar su estación de trabajo de Chef.

![][7]

Instalar en la ubicación predeterminada de c:\opscode. Esta instalación tardará unos 10 minutos.

Confirmar que la variable PATH contiene entradas para C:\opscode\chefdk\bin; C:\opscode\chefdk\embedded\bin;c:\users\yourusername\.chefdk\gem\ruby\2.0.0\bin

Si no aparecen, asegúrese de que agrega estas rutas!

*¡NOTA EL ORDEN DE LA RUTA DE ACCESO ES IMPORTANTE!* Si las rutas de acceso de opscode no están en el orden correcto, tendrá problemas.

Reinicie su estación de trabajo antes de continuar.

A continuación, se instalará la extensión para Azure. Esto proporciona cuchillo con el complemento de Azure"".

Ejecute el siguiente comando.

    chef gem install knife-azure ––pre

> [AZURE.NOTE] El argumento pre-garantiza que recibe la última versión RC del complemento de Azure para que proporciona acceso al último conjunto de API.

Es probable que un número de dependencias también se instalará al mismo tiempo.

![][8]


Para asegurarse de que todo está configurado correctamente, ejecute el siguiente comando.

    knife azure image list

Si todo está configurado correctamente, verá una lista de desplazamiento de las imágenes de Azure disponibles a través de.

Enhorabuena. ¡La estación de trabajo está configurado!

##<a name="creating-a-cookbook"></a>Crear un libro de cocina

Un libro de cocina se usa por Chef para definir un conjunto de comandos que desea ejecutar en el cliente administrado. Crear un libro de cocina es sencillo y usamos el comando **chef generar libro de cocina** para generar la plantilla de libro de cocina. Se relacionarán servidor web libro de cocina como deseo una directiva que implementa automáticamente IIS.

En el directorio C:\Chef, ejecute el siguiente comando.

    chef generate cookbook webserver

Así generará un conjunto de archivos en el directorio C:\Chef\cookbooks\webserver. Es necesario definir el conjunto de comandos que le gustaría nuestro cliente Chef para ejecutar en nuestro equipo virtual administrado.

Los comandos se almacenan en el archivo default.rb. En este archivo, deberá ser define un conjunto de comandos que se instala IIS, IIS se inicia y copia de un archivo de plantilla en la carpeta wwwroot.

Modificar el archivo C:\chef\cookbooks\webserver\recipes\default.rb y agregue las siguientes líneas.

    powershell_script 'Install IIS' do
        action :run
        code 'add-windowsfeature Web-Server'
    end

    service 'w3svc' do
        action [ :enable, :start ]
    end

    template 'c:\inetpub\wwwroot\Default.htm' do
        source 'Default.htm.erb'
        rights :read, 'Everyone'
    end

Una vez que haya terminado, guarde el archivo.

## <a name="creating-a-template"></a>Creación de una plantilla

Como se mencionó anteriormente, es necesario generar un archivo de plantilla que se utilizará como nuestra página default.html.

Ejecute el comando siguiente para generar la plantilla.

    chef generate template webserver Default.htm

Ahora, vaya al archivo C:\chef\cookbooks\webserver\templates\default\Default.htm.erb. Editar el archivo agregando código "Hola a todos" HTML sencillo y, a continuación, guarde el archivo.



## <a name="upload-the-cookbook-to-the-chef-server"></a>Cargar el libro de cocina en el servidor de Chef

En este paso, estamos realizando una copia del libro de cocina que hemos creado en nuestro equipo local y cargar en el servidor de hospedada Chef. Una vez cargado, el libro de cocina aparecerá en la pestaña de la **Directiva** .

    knife cookbook upload webserver

![][9]

## <a name="deploy-a-virtual-machine-with-knife-azure"></a>Implementar una máquina virtual con para Azure

Ahora implementar una máquina virtual Azure y aplicar el libro de cocina "Servidor Web" que se instalará nuestra página de web IIS web servicio y de forma predeterminada.

Para ello, utilice el comando **crear servidor cuchillo azure** .

Estoy ejemplo del comando aparece siguiente.

    knife azure server create --azure-dns-name 'diegotest01' --azure-vm-name 'testserver01' --azure-vm-size 'Small' --azure-storage-account 'portalvhdsxxxx' --bootstrap-protocol 'cloud-api' --azure-source-image 'a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-Datacenter-201411.01-en.us-127GB.vhd' --azure-service-location 'Southeast Asia' --winrm-user azureuser --winrm-password 'myPassword123' --tcp-endpoints 80,3389 --r 'recipe[webserver]'

Los parámetros no necesitan explicación. Sustituir las variables particulares y ejecutar.

> [AZURE.NOTE] A través de la línea de comandos, también estoy automatizar mis reglas de filtro de red de extremo mediante el parámetro – tcp extremos. He abierto los puertos 80 y 3389 para proporcionar acceso a la página web y sesión RDP.

Una vez que ejecute el comando, vaya al portal de Azure y verá que su equipo empezar a disposición.

![][13]

El símbolo aparece siguiente.

![][10]

Una vez completada la implementación, debemos podremos conectar al servicio web en el puerto 80 como se ha abierto el puerto cuando se aprovisiona la máquina virtual con el comando para Azure. Como esta máquina virtual de la máquina virtual sola en mi servicio de nube, conectará con la dirección url de servicio de nube.

![][11]

Como puede ver, he recibido su creatividad con mi código HTML.

No olvide que también podemos conectar a través de una sesión RDP desde el portal de clásica Azure en el puerto 3389.

Espero que esto ha sido útil! ¡Vaya e inicie la infraestructura como viaje de código con Azure hoy!


<!--Image references-->
[2]: ./media/virtual-machines-windows-chef-automation/2.png
[3]: ./media/virtual-machines-windows-chef-automation/3.png
[4]: ./media/virtual-machines-windows-chef-automation/4.png
[5]: ./media/virtual-machines-windows-chef-automation/5.png
[6]: ./media/virtual-machines-windows-chef-automation/6.png
[7]: ./media/virtual-machines-windows-chef-automation/7.png
[8]: ./media/virtual-machines-windows-chef-automation/8.png
[9]: ./media/virtual-machines-windows-chef-automation/9.png
[10]: ./media/virtual-machines-windows-chef-automation/10.png
[11]: ./media/virtual-machines-windows-chef-automation/11.png
[13]: ./media/virtual-machines-windows-chef-automation/13.png


<!--Link references-->

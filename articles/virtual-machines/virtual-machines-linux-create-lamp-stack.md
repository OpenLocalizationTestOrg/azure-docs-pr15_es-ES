<properties
    pageTitle="Implementar lámpara en una máquina virtual Linux | Microsoft Azure"
    description="Obtenga información sobre cómo instalar la pila de luz en una VM Linux"
    services="virtual-machines-linux"
    documentationCenter="virtual-machines"
    authors="jluk"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="NA"
    ms.topic="article"
    ms.date="06/07/2016"
    ms.author="juluk"/>

# <a name="deploy-lamp-stack-on-azure"></a>Implementar la pila de luz en Azure
En este artículo le guiará durante el proceso de implementar un servidor web Apache, MySQL y PHP (la pila de luz) en Azure. Se necesita una cuenta de Azure ([obtener una prueba gratuita](https://azure.microsoft.com/pricing/free-trial/)) y la [CLI de Azure](../xplat-cli-install.md) que está [conectado a su cuenta de Azure](../xplat-cli-connect.md).

Existen dos métodos para instalar lámpara de este artículo:

## <a name="quick-command-summary"></a>Resumen de comandos rápidos

1) Implementar lámpara de nueva máquina virtual

```
# One command to create a resource group holding a VM with LAMP already on it
$ azure group create -n uniqueResourceGroup -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json
```

2) Implementar lámpara en máquina virtual existente

```
# Two commands: one updates packages, the other installs Apache, MySQL, and PHP
user@ubuntu$ sudo apt-get update
user@ubuntu$ sudo apt-get install apache2 mysql-server php5 php5-mysql
```

## <a name="deploy-lamp-on-new-vm-walkthrough"></a>Implementar lámpara de nuevo tutorial VM

Puede iniciar mediante la creación de un nuevo [grupo de recursos](../azure-resource-manager/resource-group-overview.md) que contendrá la máquina virtual:

    $ azure group create uniqueResourceGroup westus
    info:    Executing command group create
    info:    Getting resource group uniqueResourceGroup
    info:    Creating resource group uniqueResourceGroup
    info:    Created resource group uniqueResourceGroup
    data:    Id:                  /subscriptions/########-####-####-####-############/resourceGroups/uniqueResourceGroup
    data:    Name:                uniqueResourceGroup
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: null
    data:
    info:    group create command OK

Para crear la máquina virtual de sí misma, puede usar una plantilla de administrador de recursos de Azure ya escrito encuentra [aquí en GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/lamp-app).

    $ azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json uniqueResourceGroup uniqueLampName

Verá una respuesta preguntar algunas entradas más:

    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    storageAccountNamePrefix: lampprefix
    location: westus
    adminUsername: someUsername
    adminPassword: somePassword
    mySqlPassword: somePassword
    dnsLabelPrefix: azlamptest
    info:    Initializing template configurations and parameters
    info:    Creating a deployment
    info:    Created template deployment "uniqueLampName"
    info:    Waiting for deployment to complete
    data:    DeploymentName     : uniqueLampName
    data:    ResourceGroupName  : uniqueResourceGroup
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          :
    data:    Mode               : Incremental
    data:    CorrelationId      : d51bbf3c-88f1-4cf3-a8b3-942c6925f381
    data:    TemplateLink       : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json
    data:    ContentVersion     : 1.0.0.0
    data:    DeploymentParameters :
    data:    Name                      Type          Value
    data:    ------------------------  ------------  -----------
    data:    storageAccountNamePrefix  String        lampprefix
    data:    location                  String        westus
    data:    adminUsername             String        someUsername
    data:    adminPassword             SecureString  undefined
    data:    mySqlPassword             SecureString  undefined
    data:    dnsLabelPrefix            String        azlamptest
    data:    ubuntuOSVersion           String        14.04.2-LTS
    info:    group deployment create command OK

Se ha creado una VM Linux con lámpara ya instalado en él. Si lo desea, puede comprobar la instalación saltar hacia abajo hasta [comprobar lámpara instalado correctamente].

## <a name="deploy-lamp-on-existing-vm-walkthrough"></a>Implementar lámpara de tutorial de VM existente

Si necesita ayuda para crear una VM Linux prefiere [aquí para obtener información sobre cómo crear una VM Linux] (. / virtual-machines-linux-quick-create-cli.md). A continuación, necesitará SSH en la VM Linux. Si necesita ayuda con la creación de una clave SSH prefiere [aquí para obtener información sobre cómo crear una clave de SSH en Mac o Linux] (. / virtual-machines-linux-mac-create-ssh-keys.md).
Si ya tiene una clave SSH, vaya anticipada y SSH en su VM Linux con `ssh username@uniqueDNS`.

Ahora que está trabajando en su VM Linux, se le guiará a través de la instalación de la pila de luz en distribuciones de Debian. Los comandos exactos pueden diferir de otros distros Linux.

#### <a name="installing-on-debianubuntu"></a>Instalación de Debian/Ubuntu

Necesitará la siguientes paquetes instalados: `apache2`, `mysql-server`, `php5`, y `php5-mysql`. Puede instalar estos directamente captura estos paquetes o utilizando Tasksel. A continuación se muestran las instrucciones para ambas opciones.
Antes de instalar debe descargar y actualizar las listas de paquete.

    user@ubuntu$ sudo apt-get update
    
##### <a name="individual-packages"></a>Paquetes individuales
Utilizando apt get:

    user@ubuntu$ sudo apt-get install apache2 mysql-server php5 php5-mysql

##### <a name="using-tasksel"></a>Usar Tasksel
También puede descargar Tasksel, una herramienta de Debian/Ubuntu que se instala varios paquetes relacionados como una "tarea" coordinada en el sistema.

    user@ubuntu$ sudo apt-get install tasksel
    user@ubuntu$ sudo tasksel install lamp-server

Después de ejecutar cualquiera de las opciones anteriores, se le pedirá instalar estos paquetes y un número de otras dependencias. Presione 'y' y, a continuación, 'Intro para continuar y a continuación, siga las indicaciones otros para establecer una contraseña administrativa para MySQL. Se va a instalar las extensiones PHP necesarias mínimas necesarias para usar PHP con MySQL. 

![][1]

Ejecute el comando siguiente para ver otras extensiones PHP que están disponibles como paquetes:

    user@ubuntu$ apt-cache search php5


#### <a name="create-infophp-document"></a>Crear documento info.php

Ahora debería poder comprobar qué versión de Apache, MySQL y PHP a través de la línea de comandos escribiendo `apache2 -v`, `mysql -v`, o `php -v`.

Si desea probar aún más, puede crear una página de información PHP rápida para ver en un explorador. Crear un nuevo archivo con Nano editor de texto con este comando:

    user@ubuntu$ sudo nano /var/www/html/info.php

En el editor de texto GNU Nano, agregue las siguientes líneas:

    <?php
    phpinfo();
    ?>

A continuación, guarde y cierre el editor de texto.

Reinicie Apache con este comando para que todas las instalaciones nuevas surta efecto.

    user@ubuntu$ sudo service apache2 restart

## <a name="verify-lamp-successfully-installed"></a>Comprobar lámpara instalado correctamente

Ahora puede comprobar que acaba de crear en el explorador, vaya a http://youruniqueDNS/info.php la página de información de PHP, debe ser similar a este.

![][2]

Puede comprobar la instalación de Apache viendo la página predeterminada de Apache2 Ubuntu yendo a http://youruniqueDNS/. Verá algo parecido a esto.

![][3]

Enhorabuena, tiene una pila de lámpara de instalación simplemente en la máquina virtual de Azure.

## <a name="next-steps"></a>Pasos siguientes

Consulte la documentación de Ubuntu en la pila de luz:

- [https://help.Ubuntu.com/community/ApacheMySQLPHP](https://help.ubuntu.com/community/ApacheMySQLPHP)

[1]: ./media/virtual-machines-linux-deploy-lamp-stack/configmysqlpassword-small.png
[2]: ./media/virtual-machines-linux-deploy-lamp-stack/phpsuccesspage.png
[3]: ./media/virtual-machines-linux-deploy-lamp-stack/apachesuccesspage.png

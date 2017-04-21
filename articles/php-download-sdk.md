<properties
    pageTitle="Descargar el SDK de Azure para PHP"
    description="Aprenda a descargar e instalar el SDK de Azure para PHP."
    documentationCenter="php"
    services="app-service\web"
    authors="allclark"
    manager="douge"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="PHP"
    ms.topic="article"
    ms.date="06/01/2016"
    ms.author="allclark;yaqiyang"/>

#<a name="download-the-azure-sdk-for-php"></a>Descargar el SDK de Azure para PHP

## <a name="overview"></a>Información general

El SDK de Azure para PHP incluye componentes que permiten desarrollar, implementar y administrar aplicaciones de PHP para Azure. Más concretamente, el SDK de Azure para PHP incluye lo siguiente:

* **Bibliotecas de cliente de la PHP de Azure**. Estas bibliotecas de clases ofrecen una interfaz para tener acceso a características de Azure, como servicios de administración de datos y servicios de nube.  
* **La interfaz de línea de comandos de Azure para Mac, Linux y Windows (CLI Azure)**. Se trata de un conjunto de comandos para implementar y administrar servicios de Azure, como sitios Web de Azure y máquinas virtuales de Azure. El trabajo de Azure CLI en cualquier plataforma, incluidos Mac, Linux y Windows.
* **Azure PowerShell (sólo Windows)**. Se trata de un conjunto de cmdlets de PowerShell para implementar y administrar servicios de Azure, como servicios en la nube y máquinas virtuales de Windows.
* **Los emuladores Azure (sólo Windows)**. El proceso y almacenamiento emuladores son emuladores locales de servicios en la nube y los servicios de administración de datos que permiten probar una aplicación localmente. Los emuladores de Azure solo se ejecutan en Windows.

Las secciones siguientes describen cómo descargar e instalar los componentes descritos anteriormente.

Las instrucciones de este tema se suponen que tiene [PHP] [ install-php] instalado.

> [AZURE.NOTE] Debe tener PHP 5,5 o posterior para usar las bibliotecas de cliente PHP para Azure.

##<a name="php-client-libraries-for-azure"></a>Bibliotecas de cliente PHP de Azure

Las bibliotecas de cliente de PHP para Azure ofrecen una interfaz para tener acceso a características de Azure, como servicios de administración de datos y servicios, desde cualquier sistema operativo de la nube. Estas bibliotecas se pueden instalar mediante el compositor.

Para obtener información sobre cómo usar las bibliotecas de cliente de PHP para Azure, consulte [cómo usar el servicio de Blob][blob-service], [cómo usar el servicio de tabla] [ table-service] y [cómo usar el servicio de cola][queue-service].

###<a name="install-via-composer"></a>Instalar a través de composición

1. [Instalar Git][install-git].


    > [AZURE.NOTE] En Windows, también debe agregar la Git ejecutable a la variable de entorno de ruta de acceso.

2. Cree un archivo llamado **composer.json** en la raíz de su proyecto y agregarle el siguiente código:

        {
            "require": {
                "microsoft/windowsazure": "^0.4"
            }
        }

3. Descargar ** [composer.phar] [ composer-phar] ** en la raíz del proyecto.

4. Abra un símbolo del sistema y ejecutar esto en la raíz de proyecto

        php composer.phar install

##<a name="azure-powershell-and-azure-emulators"></a>Emuladores de Azure y Azure PowerShell

PowerShell Azure es un conjunto de cmdlets de PowerShell para implementar y administrar servicios de Azure (como servicios en la nube y máquinas virtuales). Los emuladores de Azure son emuladores de servicios en la nube y los servicios de administración de datos que permiten probar una aplicación localmente. Estos componentes son compatibles con sólo Windows.

La manera recomendada para instalar la emuladores de Azure y Azure PowerShell es usar el [Instalador de plataforma Web de Microsoft][download-wpi]. Tenga en cuenta que también puede instalar otros componentes de desarrollo, como PHP, SQL Server, Drivers de Microsoft para SQL Server para PHP y WebMatrix.

Para obtener información sobre cómo usar PowerShell de Azure, vea [cómo usar PowerShell de Azure][powershell-tools].

##<a name="azure-cli"></a>CLI de Azure

CLI Azure es un conjunto de comandos para implementar y administrar servicios de Azure, como sitios Web de Azure y máquinas virtuales de Azure. Para obtener información sobre la instalación de CLI de Azure, consulte [instalar CLI Azure](xplat-cli-install.md).

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información, consulte el [Centro para desarrolladores de PHP](/develop/php/).


[install-php]: http://www.php.net/manual/en/install.php
[composer-github]: https://github.com/composer/composer
[composer-phar]: http://getcomposer.org/composer.phar
[nodejs-org]: http://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
[download-wpi]: http://go.microsoft.com/fwlink/?LinkId=253447
[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[blob-service]: http://go.microsoft.com/fwlink/?LinkId=252714
[table-service]: http://go.microsoft.com/fwlink/?LinkId=252715
[queue-service]: http://go.microsoft.com/fwlink/?LinkId=252716
[azure cli]: http://go.microsoft.com/fwlink/?LinkId=252717
[powershell-tools]: http://go.microsoft.com/fwlink/?LinkId=252718
[php-sdk-github]: http://go.microsoft.com/fwlink/?LinkId=252719
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git

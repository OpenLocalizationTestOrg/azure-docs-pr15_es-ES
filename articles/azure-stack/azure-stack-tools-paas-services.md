<properties
    pageTitle="Servicios de PaaS y herramientas para la pila de Azure | Microsoft Azure"
    description="Obtenga información sobre cómo empezar a trabajar con los servicios de PaaS de pila de Azure."
    services="azure-stack"
    documentationCenter=""
    authors="ErikjeMS"
    manager="byronr"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="erikje"/>

# <a name="tools-for-azure-stack"></a>Herramientas para la pila de Azure

Puede descargar las herramientas que se describen a continuación. Si desea recibir una notificación de herramientas y nuevos servicios, siga #AzureStack en Twitter.

## <a name="template-tools"></a>Herramientas de plantilla

### <a name="azure-stack-github-templates"></a>Plantillas de Github de pila de Azure
Explorar la creciente colección de [Plantillas de GitHub de pila de Azure](https://github.com/Azure/AzureStack-QuickStart-Templates). Al igual que [Azure](https://github.com/Azure/azure-quickstart-templates), estas plantillas de "Inicio rápido" Administrador de recursos de Azure objetivo para empezar con bloques de creación sencillos y ejemplos, listos implementar en la Microsoft Azure Technical Preview prueba de concepto entorno de pila. Incluye son ejemplos de carga de trabajo de fiesta primera para [ad-no-ha](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/ad-non-ha), [sql-2014-no-ha](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sql-2014-non-ha), [sharepoint 2013-no-alta disponibilidad](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sharepoint-2013-non-ha), así como varias plantillas de 101 simples quiera [101-simple-vm de windows](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-simple-windows-vm).


### <a name="marketplace-item-packaging-tool-and-sample"></a>Muestra y la herramienta de empaquetado de elemento de catálogo de soluciones
[Descargar y usar la herramienta de empaquetado](http://www.aka.ms/azurestackmarketplaceitem) para crear elementos de catálogo de soluciones para sus propias plantillas personalizadas agregar al catálogo de soluciones de pila de Azure. Encontrará instrucciones sobre cómo crear un elemento del catálogo de soluciones y ponerlo a disposición de los inquilinos de [elemento del catálogo de soluciones de crear](azure-stack-create-and-publish-marketplace-item.md).

## <a name="developer-tools"></a>Herramientas de desarrollador


### <a name="use-visual-studio-and-azure-stack-tp2-on-the-mas-con01-virtual-machine"></a>Use Visual Studio y TP2 de pila de Azure en la máquina virtual de MAS CON01
Si desea usar Visual Studio en la consola de máquina virtual para trabajar con plantillas de pila de Azure, debe instalar las versiones correctas de las herramientas necesarias. Use el siguiente procedimiento para instalar las versiones compatibles para TP2.

1. Usar conexión a Escritorio remoto para iniciar sesión en la máquina virtual de CON01 MAS con las credenciales azurestack\azurestackadmin.
2. Instale y abra el instalador de plataforma Web.
3. Busque e instale **Visual Studio de 2015 Comunidad con Microsoft Azure SDK - 2.9.5**.
4. Con **Agregar o quitar programas**, desinstale **Microsoft Azure PowerShell (septiembre)** que se instala como parte del SDK.
5. Abrir al instalador de plataforma Web.
6. Buscar e instalar **Microsoft Azure PowerShell - Azure pila Technical Preview 2**. 
7. Reinicie el equipo virtual CON01 MAS.
8. Usar conexión a Escritorio remoto para iniciar sesión en la máquina virtual de CON01 MAS con las credenciales azurestack\azurestackadmin.
9. Abra Visual Studio y validar que puede conectar con el entorno de pila de Azure, plantillas y así sucesivamente. 

### <a name="azure-powershell-sdk"></a>Azure SDK de PowerShell
PowerShell Azure es un módulo que proporciona cmdlets para administrar Azure y Azure pila con Windows PowerShell. Puede usar los cmdlets para crear, probar, implementar y administrar soluciones y servicios ofrecidos a través de la plataforma de pila de Azure.
[Descargar el SDK de PowerShell de Azure](http://aka.ms/azStackPsh) e [instale PowerShell](azure-stack-connect-powershell.md).

### <a name="azure-cross-platform-command-line-interfaces"></a>Azure cruzada interfaces de línea de comandos de plataforma
Instalar rápidamente la interfaz de línea de comandos de Azure (Azure CLI) para usar un conjunto de código abierto basado en el shell de comandos para crear y administrar recursos en la pila de Microsoft Azure.

[Descargue Windows CLI](http://aka.ms/azstack-windows-cli)

[Descargar el CLI Mac](http://aka.ms/azstack-linux-cli)

[Descargar CLI Linux](http://aka.ms/azstack-mac-cli)

>[AZURE.NOTE]
>
> + Si se encuentra en un equipo Mac o Linux, también puede obtener CLI mediante el comando`npm install -g azure-cli@0.9.11`</br>
> + Si recibe los problemas de validación de certificado, ejecute el comando`set NODE_TLS_REJECT_UNAUTHORIZED=0`

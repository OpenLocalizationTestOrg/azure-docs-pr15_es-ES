<properties
   pageTitle="Habilitar o deshabilitar la supervisión de Azure VM"
   description="Describe cómo habilitar o deshabilitar la supervisión de Azure VM"
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="kmouss"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="02/08/2016"
   ms.author="kmouss"/>
   
# <a name="enable-or-disable-azure-vm-monitoring"></a>Habilitar o deshabilitar la supervisión de Azure VM

Esta sección describe cómo habilitar o deshabilitar la supervisión en máquinas virtuales que ejecutan en Azure. Supervisión está deshabilitada de forma predeterminada en máquinas virtuales de Azure si implementa desde el [portal de Azure](https://portal.azure.com) y gráficos de supervisión se proporcionan de forma predeterminada con un período de 1 minuto. Puede habilitar o deshabilitar la supervisión con el portal o la interfaz de línea de comandos de Azure para Mac, Linux y Windows (CLI de Azure). 

## <a name="enable--disable-monitoring-through-the-azure-portal"></a>Habilitar o deshabilitar la supervisión a través del Portal de Azure
 
Para habilitar la supervisión de la máquina virtual de Azure, que proporciona datos sobre su instancia en periodos de 1 minuto. (se aplican cambios de almacenamiento). Datos de diagnóstico detallada están disponibles para la máquina virtual en los gráficos de portal o a través de la API. De forma predeterminada, el portal de Azure permite la supervisión, pero puede desactivar el tal como se describe a continuación. Puede habilitar la supervisión mientras la máquina virtual se está ejecutando o en estado detenido.

- Abra el portal en **Azure [https://portal.azure.com](https://portal.azure.com)**

- En el panel de navegación izquierdo, haga clic en máquinas virtuales de Windows.

- En las máquinas virtuales de lista, seleccione una instancia de ejecución o detenida. Se abrirá la máquina virtual blad.

- Haga clic en "Todas las configuraciones".

- Haga clic en "Diagnósticos".

- Cambiar el estado a activar o desactivar. También puede seleccionar en este módulo el nivel de detalles que desea habilitar para la máquina virtual de control.

[Azure.Note] El modificador diagnósticos en es el valor predeterminado cuando se crea una nueva máquina virtual

![Habilitar o deshabilitar la supervisión a través del Portal de Azure.][1]


## <a name="enable--disable-monitoring-with-azure-cli"></a>Habilitar o deshabilitar la supervisión de Azure CLI
 
Para habilitar la supervisión de una máquina virtual de Azure.

- Crear un archivo llamado como PrivateConfig.json con el siguiente contenido.
        {"storageAccountName": "la cuenta de almacenamiento para recibir datos", "storageAccountKey": "la clave de la cuenta"}
- Ejecute el siguiente comando CLI de Azure.

        azure vm extension set myvm LinuxDiagnostic Microsoft.OSTCExtensions 2.0 --private-config-path PrivateConfig.json

[Azure.Note] Puede cambiar de la versión 2.0 a una versión posterior cuando esté disponible. 

Para obtener más detalles sobre la configuración de supervisión métricas y ejemplos, visite el documento - **[Usando Linux diagnóstico extensión Monitor Linux VM datos de rendimiento y diagnósticos](virtual-machines-linux-classic-diagnostic-extension.md).

<!--Image references-->
[1]: ./media/virtual-machines-linux-vm-monitoring/portal-enable-disable.png
 


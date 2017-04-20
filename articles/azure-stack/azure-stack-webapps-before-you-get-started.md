<properties
    pageTitle="Pila de Azure servicio App Technical Preview 1 antes de comenzar a | Microsoft Azure"
    description="Pasos para completar antes de implementar aplicaciones Web de pila de Azure"
    services="azure-stack"
    documentationCenter=""
    authors="apwestgarth"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="app-service"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="anwestg"/>
    
# <a name="before-you-get-started-with-azure-stack-web-apps"></a>Antes de empezar con aplicaciones Web de pila de Azure

> [AZURE.NOTE] La siguiente información solo se aplica a implementaciones TP1 de pila de Azure.

Necesitará algunos elementos para instalar aplicaciones Web de Azure pila:

- Una implementación completa de [Azure pila Technical Preview 1](azure-stack-run-powershell-script.md)
- Hay suficiente espacio en el sistema de pila de Azure una pequeña implementación de aplicaciones Web de pila de Azure.  El espacio necesario es aproximadamente 20GB de espacio en disco.
- [Un servidor que ejecuta SQL Server](#SQL-Server).

>[AZURE.NOTE] Los siguientes pasos que todos tenga lugar en la máquina virtual de cliente.

## <a name="before-you-deploy-azure-stack-web-apps"></a>Antes de implementar aplicaciones Web de pila de Azure

Para implementar un proveedor de recursos, debe ejecutar el Environment(ISE) integrado de secuencias de comandos de PowerShell como administrador. Por este motivo, debe permitir las cookies y JavaScript en el perfil de Internet Explorer que utiliza para iniciar sesión en Azure Active Directory.

## <a name="turn-off-internet-explorer-enhanced-security"></a>Desactivar la seguridad mejorada de Internet Explorer

1.  Inicie sesión en el equipo de pila de Azure prueba de concepto (prueba de concepto) como **AzureStack o administrador**y, a continuación, abra el **Administrador del servidor**.
2.  Desactivar **La configuración de seguridad mejorada de Internet Explorer** para administradores y usuarios.
3.  Inicie sesión en la máquina virtual ClientVM.AzureStack.local como administrador y, a continuación, abra el **Administrador del servidor**.
4.  Desactivar **La configuración de seguridad mejorada de Internet Explorer** para administradores y usuarios.

## <a name="enable-cookies"></a>Habilitar las cookies

1.  Seleccione **Inicio**, > **todas las aplicaciones**, > **Accesorios de Windows**. Haga clic en **Internet Explorer** > **más** > **Ejecutar como administrador**.
2.  Si se le solicita, seleccione **usar recomendada seguridad**y, a continuación, seleccione **Aceptar**.
3.  En Internet Explorer, seleccione **Herramientas** (el icono de engranaje) > **Opciones de Internet** > **privacidad** > **Avanzadas**.
4.  Seleccione **Avanzadas**. Asegúrese de que están seleccionadas ambas casillas de verificación de **Aceptar** y, a continuación, seleccione **Aceptar** y en **Aceptar** nuevamente.
5.  Cierre Internet Explorer y reinicie PowerShell ISE como administrador.

## <a name="install-the-latest-version-of-azure-powershell"></a>Instalar la última versión de PowerShell de Azure

1.  Inicie sesión en el equipo de prueba de concepto de pila de Azure como **AzureStack o administrador**.
2.  Usar conexión a Escritorio remoto para iniciar sesión la máquina virtual ClientVM.AzureStack.local como administrador.
3.  Abra **El Panel de Control** y seleccione **desinstalar un programa**. 
4.  Haga clic en **Microsoft Azure PowerShell - noviembre de 2015** y seleccione **desinstalar**.
5.  Después de la desinstalación finalice, reinicie el equipo virtual de ClientVM.AzureStack.local
6.  Descargar e instalar la última [Azure PowerShell](http://aka.ms/azstackpsh).


## <a name="sql-server"></a>SQL Server

De forma predeterminada, el programa de instalación de aplicaciones Web de pila de Azure está configurado para utilizar SQL Server que se instala junto con el proveedor de recursos de SQL de pila de Azure. Cuando se instala el proveedor de recursos de SQL Server (SQL RP) asegúrese de que tenga en cuenta el nombre de usuario de administrador de base de datos y la contraseña. Necesita ambas al instalar aplicaciones Web de pila de Azure.
Nota: También tendrá la opción de implementar y usar otro servidor que ejecuta SQL Server. Puede elegir la instancia de SQL Server a utilizar cuando complete las opciones en el programa de instalación de aplicaciones Web de pila de Azure.

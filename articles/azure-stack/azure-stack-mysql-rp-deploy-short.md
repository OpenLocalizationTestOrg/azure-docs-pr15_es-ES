<properties
    pageTitle="Usar bases de datos MySQL como PaaS en pila de Azure | Microsoft Azure"
    description="Comprender los pasos rápidos para implementar el proveedor de recursos MySQL y proporcionar MySQL como un servicio en la pila de Azure."
    services="azure-stack"
    documentationCenter=""
    authors="Dumagar"
    manager="bradleyb"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="dumagar"/>

# <a name="use-mysql-databases-as-paas-on-azure-stack"></a>Usar bases de datos MySQL como PaaS en pila de Azure

> [AZURE.NOTE] La siguiente información solo se aplica a implementaciones TP1 de pila de Azure.

Puede implementar un proveedor de recursos de MySQL en pila de Azure. Después de implementar el proveedor de recursos, puede crear servidores MySQL y bases de datos a través de plantillas de implementación de administrador de recursos de Azure y proporcionar las bases de datos MySQL como un servicio. Bases de datos MySQL, que son comunes en los sitios web, muchas plataformas de sitio Web de soporte técnico. Después de implementar el proveedor de recursos, puede crear sitios Web WordPress desde la plataforma de aplicaciones Web de Azure como un complemento de servicio (PaaS) para la pila de Azure.

## <a name="quick-steps-to-deploy-the-resource-provider"></a>Pasos rápidos para implementar el proveedor de recursos
Siga estos pasos si ya está familiarizado con la pila de Azure. Si desea obtener más detalles, siga los vínculos de cada sección o ir directamente a [implementar el adaptador de proveedor MySQL recursos de base de datos de prueba de concepto de pila de Azure](azure-stack-mysql-rp-deploy-long.md).

1.  Asegúrese de que [completar los pasos de configuración todas](azure-stack-mysql-rp-deploy-long.md#set-up-steps-before-you-deploy) antes de implementar el proveedor de recursos:

    - 3.5 de .NET framework ya está configurado en la imagen base de Windows Server. (Si ha descargado los bits de Azure pila después 23 de febrero de 2016, puede omitir este paso.)
    - [Está instalada una versión de Azure PowerShell, que es compatible con la pila de Azure](http://aka.ms/azStackPsh).
    - En configuración de seguridad de Internet Explorer en el ClientVM, [Internet Explorer seguridad mejorada está desactivada y cookies están habilitadas](azure-stack-mysql-rp-deploy-long.md#Turn-off-IE-enhanced-security-and-enable-cookies).

2. [Descargar el archivo de los archivos binarios de proveedor de recursos de MySQL](http://aka.ms/masmysqlrp) y extraer en el ClientVM en la pila de Azure prueba de concepto (prueba de concepto).

3. [Ejecute bootstrap.cmd y las secuencias de comandos](azure-stack-mysql-rp-deploy-long.md#Bootstrap-the-resource-provider-deployment-PowerShell-and-Prepare-for-deployment).

    Un conjunto de secuencias de comandos que se agrupa por dos fichas principales se abre en el entorno de Scripting integrado en PowerShell (ISE). Ejecutar todas las secuencias de comandos cargados en secuencia de izquierda a derecha en cada ficha.

    1. Ejecutar secuencias de comandos en la pestaña de **la preparación** de izquierda a derecha para:

        - Crear un certificado comodín para proteger la comunicación entre el proveedor de recursos y el Administrador de recursos de Azure.
        - Acepte los términos de CLUF MySQL y descargar los archivos binarios de MySQL.
        - Cargue los certificados y todos los otros defectos a una cuenta de pila de Azure almacenamiento.
        - Publicar paquetes de la Galería para que se pueden implementar recursos MySQL a través de la galería.

        > [AZURE.IMPORTANT] Si cualquiera de las secuencias de comandos se bloquea sin motivo aparente después de enviar a su inquilino de Azure Active Directory, la configuración de seguridad está bloqueando un DLL necesario para que la implementación ejecutar. Para resolver este problema, busque la Microsoft.AzureStack.Deployment.Telemetry.Dll en la carpeta de proveedor de recursos, derecho haga clic en y, a continuación, haga clic en **Propiedades**y después active **desbloquear** en la pestaña **General** .

    2. Ejecutar secuencias de comandos en la pestaña **implementar** de izquierda a derecha para:

        - [Implementar una máquina virtual (VM)](azure-stack-mysql-rp-deploy-long.md#Deploy-the-MySQLResource-Provider-VM) que hospedará el proveedor de recursos, a servidores MySQL y bases de datos que se creará una instancia. Esta secuencia de comandos hace referencia a un archivo de parámetros JSON, deberá actualizar con algunos valores antes de ejecutar la secuencia de comandos.
        - [Registrar un registro DNS local](azure-stack-mysql-rp-deploy-long.md#Update-the-local-DNS) que se asignará a su proveedor de recursos VM.
        - [Registrar su proveedor de recursos](azure-stack-mysql-rp-deploy-long.md#Register-the-MySQL-RP-Resource-Provider) el Administrador de recursos con Azure local.

        > [AZURE.IMPORTANT] Todas las secuencias de comandos, se suponen que la imagen de sistema operativo cumple los requisitos previos (.NET 3.5 instalado, JavaScript y las cookies habilitadas en el ClientVM y la última versión de Azure PowerShell instalado). Si se producen errores al ejecutar las secuencias de comandos, vuelva a comprobar que cumplen los requisitos previos.

5. Para [probar el nuevo proveedor de recursos de MySQL](/azure-stack-MySql-rp-deploy-long.md#create-your-first-mysql-database-to=test-your-deployment), implementar una base de datos MySQL desde el portal de pila de Azure. Haga clic en **crear** &gt; **personalizado** &gt; **servidor MySQL y base de datos**.

Debe obtener de su proveedor de recursos de MySQL hacia arriba y ejecutando en aproximadamente 25 minutos.

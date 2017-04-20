<properties
    pageTitle="Implementar el proveedor de recursos de MySQL en pila de Azure | Microsoft Azure"
    description="Pasos detallados para implementar el proveedor de recursos de MySQL pila de Azure."
    services="azure-stack"
    documentationCenter=""
    authors="Dumagar"
    manager="byronr"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="dumagar"/>


# <a name="deploy-the-mysql-resource-provider-on-azure-stack-to-use-with-webapps"></a>Implementar el proveedor de recursos de MySQL en pila de Azure para usar con aplicaciones Web

> [AZURE.NOTE] La siguiente información solo se aplica a implementaciones TP1 de pila de Azure.

Use este artículo para seguir los pasos detallados para configurar el proveedor de recursos de MySQL en la prueba de Azure pila de concepto (prueba de concepto) para que puede comenzar a [usar las bases de datos MySQL](azure-stack-mysql-rp-deploy-short.md) en pila de Azure, incluido el uso de MySQL como el back-end de WordPress sitios creado con [Aplicaciones Web de Azure](azure-stack-webapps-deploy.md).

## <a name="set-up-steps-before-you-deploy"></a>Pasos de configuración antes de implementar

Antes de implementar el proveedor de recursos, debe:

- Tiene una imagen predeterminada de Windows Server con .NET 3.5
- Desactivar la seguridad mejorada de Internet Explorer (IE)
- Instalar la última versión de PowerShell de Azure

### <a name="create-an-image-of-windows-server-including-net-35"></a>Crear una imagen de Windows Server, incluidos .NET 3.5

Puede omitir este paso si ha descargado los bits de Azure pila después 23/2/2016 porque la imagen predeterminada de Windows Server 2012 R2 base incluye 3.5 de .NET framework en esta descarga y versiones posteriores.

Si ha descargado antes de 23/2/2016, tiene que crear un disco duro virtual de Windows Server 2012 R2 centro de datos con la imagen de .NET 3.5 y conjunto es como la imagen predeterminada en el repositorio de imágenes de la plataforma.

### <a name="turn-off-ie-enhanced-security-and-enable-cookies"></a>Desactivar IE mejorado de seguridad y habilitar cookies

Para implementar un proveedor de recursos, ejecute el entorno de Scripting integrado (ISE) de PowerShell como administrador, por lo que debe permitir las cookies y JavaScript en el perfil de Internet Explorer que usa para iniciar sesión en Azure Active Directory para inicios de sesión de usuario y de administrador.

**Para desactivar IE seguridad mejorada:**

1. Inicie sesión en el equipo de pila de Azure prueba de concepto (prueba de concepto) como un administrador de AzureStack y, a continuación, abra el administrador del servidor.

2. Desactivar **La configuración de seguridad mejorada de Internet Explorer** para administradores y usuarios.

3. Inicie sesión en la máquina virtual **ClientVM.AzureStack.local** como administrador y, a continuación, abra el administrador del servidor.

4. Desactivar **La configuración de seguridad mejorada de Internet Explorer** para administradores y usuarios.

**Para habilitar las cookies:**

1. En la pantalla de inicio de Windows, haga clic en **todas las aplicaciones**, haga clic en **Accesorios de Windows**, haga clic en **Internet Explorer**, seleccione **más**y, a continuación, seleccione **Ejecutar como administrador**.

2. Si se le solicita, active **usar recomendada seguridad**y, a continuación, haga clic en **Aceptar**.

3. En Internet Explorer, haga clic en el **icono Herramientas (del engranaje)** &gt; **Opciones de Internet** &gt; pestaña **privacidad** .

4. Haga clic en **Avanzadas**, asegúrese de que se seleccionan ambos botones **Aceptar** , haga clic en **Aceptar**y, a continuación, haga clic en **Aceptar** nuevamente.

5. Cierre Internet Explorer y reinicie PowerShell ISE como administrador.

### <a name="install-an-azure-stack-compatible-release-of-azure-powershell"></a>Instalar una versión compatible de Azure pila de PowerShell de Azure

1. Desinstale cualquier PowerShell Azure existente de la máquina virtual de cliente.

2. Inicie sesión como un administrador de AzureStack en el equipo de prueba de concepto de pila de Azure.

3. Uso de escritorio remoto, inicie sesión en la máquina virtual **ClientVM.AzureStack.local** como administrador.

4. Abra el Panel de Control, haga clic en **desinstalar un programa** &gt; haga clic en **Azure PowerShell** &gt; haga clic en **desinstalar**.

5. [Descargar la última Azure PowerShell que admita pila de Azure](http://aka.ms/azstackpsh) e instálelo.

    Después de instalar PowerShell, puede ejecutar esta comprobación de secuencias de comandos de PowerShell para asegurarse de que puede conectarse a su instancia de pila de Azure (debería aparecer una página web de inicio de sesión).

## <a name="bootstrap-the-resource-provider-deployment-powershell"></a>Iniciar la implementación de proveedor de recursos PowerShell

1. Conectar la versión de escritorio remoto de prueba de concepto de pila de Azure con clientVm.AzureStack.Local e inicie sesión como azurestack\\azurestackuser.

2. [Descargar los archivos binarios de MySQL RP](http://aka.ms/masmysqlrp) archivo y extraer a D:\\MySQLRP.

3. Ejecutar la D:\\MySQLRP\\Bootstrap.cmd el archivo como un administrador (azurestack\administrator).

    Se abrirá el archivo Bootstrap.ps1 en PowerShell ISE.

4. Cuando finaliza la carga de windows PowerShell ISE, haga clic en el botón "reproducir" o presione F5.

    Se cargará dos pestañas principales, cada uno con todas las secuencias de comandos y los archivos que necesita para implementar el proveedor de recursos de MySQL.

## <a name="prepare-prerequisites"></a>Preparar los requisitos previos

Haga clic en la pestaña de **Requisitos previos de prepararse** para:

- Crear certificados necesarios
- Descargar archivos binarios MySQL en la pila de Azure
- Cargar artefactos a una cuenta de almacenamiento en la pila de Azure
- Publicar elementos de la Galería

### <a name="create-the-required-certificates"></a>Cree los certificados necesarios
Esta secuencia de comandos de **Nuevo SslCert.ps1** agrega la \_. Certificado SSL de AzureStack.local.pfx D:\\MySQLRP\\requisitos previos\\BlobStorage\\carpeta del contenedor. El certificado protege la comunicación entre el proveedor de recursos y la instancia local del Administrador de recursos de Azure.

1. En la pestaña principal **Requisitos previos preparar** , haga clic en la ficha **Nuevo SslCert.ps1** y ejecútelo.

2. En el mensaje que aparece, escriba una contraseña PFX que protege la clave privada y **tome nota de esta contraseña**. Necesitará más adelante.

### <a name="download-mysql-binaries-to-your-azure-stack"></a>Descargar archivos binarios MySQL en la pila de Azure

1. Seleccione la ficha **MySqlServer.ps1 de descarga** y ejecute.
2. Cuando se le solicite, haga clic en **Sí** en el cuadro de diálogo de confirmación para aceptar al CLUF.

    Este comando agrega dos archivos zip a la carpeta D:\MySql\Prerequisites\BlobStorage\Container.

### <a name="upload-all-artifacts-to-a-storage-account-on-azure-stack"></a>Cargar todos los artefactos a una cuenta de almacenamiento en la pila de Azure

1. Haga clic en la ficha **RP.ps1 de Microsoft.MySql de carga** y ejecutarla.

2. En el cuadro de diálogo solicitud de credenciales de Windows PowerShell, escriba las credenciales de administrador del servicio de pila de Azure.

3. Cuando se le solicite el identificador de inquilinos de Azure Active Directory, escriba su nombre de dominio completo del inquilino de Azure Active Directory: por ejemplo, microsoftazurestack.onmicrosoft.com.

    Una ventana emergente solicita las credenciales.

    > [AZURE.TIP] Si no aparece la ventana emergente, que ya sea todavía no lo ha desactivado IE mayor seguridad para habilitar JavaScript en este equipo y el usuario o no ha aceptado cookies en Internet Explorer. Vea [Configurar pasos antes de implementar](#set-up-steps-before-you-deploy).

4. Escriba sus credenciales de administrador de servicios de pila de Azure y, a continuación, haga clic en **Iniciar sesión**.

### <a name="publish-gallery-items-for-later-resource-creation"></a>Publicar elementos de la Galería para la posterior creación de recursos

Seleccione la pestaña **Publicar GalleryPackages.ps1** y ejecútelo. Esta secuencia de comandos agrega dos elementos de catálogo de soluciones Marketplace del portal de prueba de concepto de pila de Azure que puede utilizar para implementar recursos de base de datos como elementos de catálogo de soluciones.

## <a name="deploy-the-mysql-resource-provider-vm"></a>Implementar el proveedor de recursos de MySQL VM

Ahora que ha preparado la prueba de concepto de pila de Azure con los certificados necesarios y los elementos de catálogo de soluciones, puede implementar un proveedor de recursos de SQL Server. Haga clic en la pestaña **proveedor implementar MySQL** para:

   - Proporcionar los valores en un archivo JSON que hace referencia el proceso de implementación
   - Implementar el proveedor de recursos
   - Actualizar el DNS local
   - Registrar el adaptador de proveedor de recursos SQL Server

### <a name="provide-values-in-the-json-file"></a>Proporcionar los valores en el archivo JSON

Haga clic en **Microsoft.MySqlprovider.Parameters.JSON**. Este archivo tiene parámetros que la plantilla de administrador de recursos de Azure se necesita para implementar correctamente en pila de Azure.

1. Rellene los parámetros del archivo JSON **vacía** :

    - Asegúrese de que proporciona la **adminusername** y **adminpassword** para el proveedor de recursos MySQL máquina virtual.

    - Asegúrese de que proporcione la contraseña para el parámetro de **SetupPfxPassword** anotó en el paso de [requisitos previos de preparar](#prepare-prerequisites) .

    - Asegúrese de que proporciona parámetros **basicAuthUserName** y **basicAuthPassword** . **Tome nota de los valores siguientes.** Necesitará más adelante para registrar el proveedor de recursos.

2. Haga clic en **Guardar**.

### <a name="deploy-the-resource-provider"></a>Implementar el proveedor de recursos

1. Haga clic en la pestaña **implementar-Microsoft.Mysql-provider.PS1** y ejecute la secuencia de comandos.
2. Escriba el nombre del inquilino de Azure Active Directory cuando se le solicite.
3. En la ventana emergente, envíe sus credenciales de administrador del servicio de pila de Azure.

La implementación completa puede tardar entre 15 y 45 minutos en algunos altamente utilizados POCs pila de Azure.

### <a name="update-the-local-dns"></a>Actualizar el DNS local

1. Haga clic en la pestaña **registro-Microsoft.MySQL-fqdn.ps1** y ejecute la secuencia de comandos.
2. Cuando se le solicite para Azure Active Directory inquilino ID, introduzca su nombre de dominio completo del inquilino de Azure Active Directory: por ejemplo, **microsoftazurestack.onmicrosoft.com**.

### <a name="register-the-sql-rp-resource-provider"></a>Registrar el proveedor de recursos RP de SQL

1. Haga clic en la pestaña **registro-Microsoft.My-provider.ps1** y ejecute la secuencia de comandos.

2. Cuando se le solicita las credenciales, utilice anotó como los parámetros **basicAuthUserName** y **basicAuthPassword** .

## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>Comprobar la implementación con el Portal de la pila de Azure

1. Cerrar la sesión de la ClientVM y volver a iniciar sesión como **AzureStack\User**.

2. En el escritorio, haga clic en el **Portal de prueba de concepto de pila de Azure** e inicie sesión en el portal como el Administrador de servicio.

3. Compruebe que la implementación correctamente. Haga clic en **Examinar** &gt; **Grupos de recursos**, haga clic en el grupo de recursos que usa ( **MySQLRP**de forma predeterminada) y asegúrese de que el elemento essentials del módulo (mitad superior) lee **implementación se realizó correctamente**.


4. Compruebe que el registro se realizó correctamente. Haga clic en **Examinar** &gt; **proveedores de recursos**y, a continuación, busque **MySQL Local**.

## <a name="create-your-first-mysql-database-to-test-your-deployment"></a>Crear la primera base de datos MySQL para probar la implementación

1. Inicie sesión en el portal de prueba de concepto de pila de Azure como administrador de servicio.

2. Haga clic en el **+** botón &gt; **personalizado** &gt; **servidor MySQL y bases de datos**.

3. Rellene el formulario con detalles de la base de datos.

    **Anote el "nombre de servidor".** La cadena de conexiones de la base de datos incluye el nombre del servidor"" como parte del nombre de usuario: por ejemplo, ** "user@ <ServerName>"**. Necesitará un nombre de usuario en este formato de entrada cuando se conecte a la base de datos: por ejemplo, cuando se implementa un sitio web de MySQL con el proveedor de recursos de sitio Web de Azure


## <a name="next-steps"></a>Pasos siguientes

Pruebe otros [Servicios de PaaS](azure-stack-tools-paas-services.md) como el [proveedor de recursos de SQL Server](azure-stack-sql-rp-deploy-short.md) y el [proveedor de recursos de aplicaciones Web](azure-stack-webapps-deploy.md).

<properties
    pageTitle="Implementar la prueba de concepto de pila Azure | Microsoft Azure"
    description="Aprenda a preparar la prueba de concepto de pila de Azure y ejecutar la secuencia de comandos de PowerShell para implementar la prueba de concepto de pila de Azure."
    services="azure-stack"
    documentationCenter=""
    authors="ErikjeMS"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/20/2016"
    ms.author="erikje"/>

# <a name="deploy-azure-stack-poc"></a>Implementar la prueba de concepto de pila de Azure
Para implementar la prueba de concepto de pila de Azure, primero debe [Preparar el equipo de implementación](#prepare-the-deployment-machine) y, a continuación, [ejecute el script de implementación de PowerShell](#run-the-powershell-deployment-script).

## <a name="download-and-extract-microsoft-azure-stack-poc-tp2"></a>Descargar y extraer TP2 de prueba de concepto de pila de Microsoft Azure

Antes de empezar, asegúrese de que al menos 85 GB de espacio.

1. La descarga de Azure pila de prueba de concepto TP2 consta de un archivo zip que contiene los siguientes 12 archivos sumatorio ~ 20 GB:
    - 1 MicrosoftAzureStackPOC.EXE
2. Revise la información del Asistente de extracción automática y pantalla Contrato de licencia y, a continuación, haga clic en **siguiente**.
3. Revise la información del Asistente de extracción automática y la pantalla de la declaración de privacidad y, a continuación, haga clic en **siguiente**.
4. Seleccione el destino para los archivos se extraen, haga clic en **siguiente**.
    - El valor predeterminado es: <drive letter>:\<carpeta actual > \Microsoft prueba de concepto de pila de Azure
5. Revise la información del Asistente de extracción automática y la pantalla de la ubicación de destino y, a continuación, haga clic en **extraer** para extraer los archivos de ThirdPartyLicenses.rtf y CloudBuilder.vhdx (~44.5 GB).

> [AZURE.NOTE] Después de extraer los archivos, puede eliminar el archivo zip para recuperar espacio en el equipo. O bien, puede mover el archivo zip a otra ubicación, por lo que si no ha podido implementar no es necesario volver a descargar los archivos zip.

## <a name="prepare-the-deployment-machine"></a>Preparar el equipo de implementación

1. Asegúrese de que puede física conectar con el equipo de implementación, o tener acceso de consola físicos (como KVM). Tendrá acceso después de reiniciar el equipo de implementación en el paso 9.

2. Asegúrese de que el equipo de implementación cumple los [requisitos mínimos](azure-stack-deploy.md). Puede usar el [Comprobador de implementación de Azure pila Technical Preview 2](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) para confirmar los requisitos.

3. Inicie sesión como administrador Local en el equipo de prueba de concepto.

4. Copie el archivo CloudBuilder.vhdx a C:\CloudBuilder.vhdx.

    > [AZURE.NOTE] Si elige no usar el script recomendado para preparar el equipo del host de prueba de concepto (pasos 5-paso 7), no escriba ninguna clave de licencia en la página de activación. Se incluye una versión de prueba de imagen de Windows Server 2016 e introduciendo una clave de licencia provoca mensajes de advertencia de expiración.

5. En el equipo de prueba de concepto, ejecute el siguiente script de PowerShell para descargar los archivos de soporte técnico de Azure pila TP2:

    ```powershell
    # Variables
    $Uri = 'https://raw.githubusercontent.com/Azure/AzureStack-Tools/master/Deployment/'
    $LocalPath = 'c:\AzureStack_TP2_SupportFiles'

    # Create folder
    New-Item $LocalPath -type directory

    # Download files
    ( 'BootMenuNoKVM.ps1', 'PrepareBootFromVHD.ps1', 'Unattend.xml', 'unattend_NoKVM.xml') | foreach { Invoke-WebRequest ($uri + $_) -OutFile ($LocalPath + '\' + $_) } 
    ```

    Esta secuencia de comandos descarga los archivos de soporte técnico de Azure pila TP2 a la carpeta especificada por el parámetro $LocalPath.
    
6. Abrir una consola de PowerShell elevada y cambie el directorio a donde copió los archivos.
    - 11 N.BIN de MicrosoftAzureStackPOC (donde N es 1-11)
7. Haga clic en la MicrosoftAzureStackPOC.EXE > Ejecutar como administrador.

8. Ejecute el script PrepareBootFromVHD.ps1. Esta secuencia de comandos y los archivos de instalación desatendida están disponibles con el otros soporte técnico secuencias de comandos proporcionadas junto con esta versión de compilación.
    Hay cinco parámetros de esta secuencia de comandos de PowerShell:
    - CloudBuilderDiskPath (obligatorio): la ruta de acceso a la CloudBuilder.vhdx en el HOST.
    - DriverPath (opcional): le permite agregar controladores adicionales para el host en el disco duro virtual.
    - ApplyUnattend (opcional): especifique este parámetro de conmutador para automatizar la configuración del sistema operativo. Si se especifica, el usuario debe proporcionar AdminPassword para configurar el sistema operativo durante el inicio (requiere siempre que acompaña archivo unattend_NoKVM.xml).
    Si no utiliza este parámetro, se utiliza el archivo unattend.xml genérico sin una mayor personalización. Necesitará KVM personalización completa después de que se reinicia.
    - AdminPassword (opcional): solo se utiliza cuando se establece el parámetro ApplyUnattend, requiere un mínimo de seis caracteres.
    - VHDLanguage (opcional): especifica el idioma del disco duro virtual, el valor predeterminado de "en-US".
    La secuencia de comandos documentado y contiene ejemplo de uso, aunque es el uso más común:
    
        `.\PrepareBootFromVHD.ps1 -CloudBuilderDiskPath C:\CloudBuilder.vhdx -ApplyUnattend`
    
        Si ejecuta este comando exacto, debe escribir AdminPassword cuando se le solicite.

9. Una vez completada la secuencia de comandos, confirme el reinicio. Si hay otros usuarios conectados, este comando se producirá un error. Si el comando no funciona, ejecute el siguiente comando:`Restart-Computer -force` 

10. Se reinicia el HOST en el sistema operativo de la CloudBuilder.vhdx, donde la implementación continúa.

> [AZURE.IMPORTANT] Pila de Azure requiere acceso a Internet, ya sea directa o a través de un proxy transparente. La implementación de prueba de concepto TP2 admite exactamente una NIC para redes. Si tiene varias tarjetas NIC, asegúrese de que sólo uno está habilitado (y todos los demás están deshabilitadas) antes de ejecutar el script de implementación de la siguiente sección.

## <a name="run-the-powershell-deployment-script"></a>Ejecute el script de implementación de PowerShell

1. Inicie sesión como administrador Local en el equipo de prueba de concepto. Use las credenciales especificadas en los pasos anteriores.

2. Abrir una consola de PowerShell elevada.

3. En PowerShell, ejecute este comando:`cd C:\CloudDeployment\Configuration`

4. Ejecute el comando implementar:`.\InstallAzureStackPOC.ps1`

5. En el símbolo del sistema **Escriba la contraseña** , escriba una contraseña y confírmela. Esta es la contraseña para todas las máquinas virtuales. Asegúrese de que se registra.

6. Escriba las credenciales de su cuenta de Azure Active Directory. Este usuario debe ser el administrador Global en el inquilino de directorio.

7. El proceso de implementación puede tardar un par de horas, durante el que el sistema se reinicia automáticamente una vez.

    > [AZURE.IMPORTANT] Si desea supervisar el progreso de la implementación, inicie sesión como azurestack\AzureStackAdmin. Si iniciar sesión como administrador local después de que el equipo está conectado a del dominio, no verá el progreso de la implementación. No volver a ejecutar la implementación, en su lugar iniciar sesión como azurestack\AzureStackAdmin validar que se está ejecutando.

    Cuando se complete la operación de la implementación, se muestra la consola de PowerShell: **completado: acción 'Implementación'**.

    Si se produce un error en la implementación, puede intentar [ejecutarla](azure-stack-rerun-deploy.md). O bien, puede [volver a](azure-stack-redeploy.md) él desde cero.

### <a name="deployment-script-examples"></a>Ejemplos de script de implementación

Si su identidad de AAD solo está asociada con un directorio de AAD:

    cd C:\CloudDeployment\Configuration
    $adminpass = ConvertTo-SecureString "<LOCAL ADMIN PASSWORD>" -AsPlainText -Force
    $aadpass = ConvertTo-SecureString "<AAD GLOBAL ADMIN ACCOUNT PASSWORD>" -AsPlainText -Force
    $aadcred = New-Object System.Management.Automation.PSCredential ("<AAD GLOBAL ADMIN ACCOUNT>", $aadpass)
    .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass -AADAdminCredential $aadcred

Si su identidad de AAD está asociado con mayor que un AAD directorio:

    cd C:\CloudDeployment\Configuration
    $adminpass = ConvertTo-SecureString "<LOCAL ADMIN PASSWORD>" -AsPlainText -Force
    $aadpass = ConvertTo-SecureString "<AAD GLOBAL ADMIN ACCOUNT PASSWORD>" -AsPlainText -Force
    $aadcred = New-Object System.Management.Automation.PSCredential ("<AAD GLOBAL ADMIN ACCOUNT> example: user@AADDirName.onmicrosoft.com>", $aadpass)
    .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass -AADAdminCredential $aadcred -AADDirectoryTenantName "<SPECIFIC AAD DIRECTORY example: AADDirName.onmicrosoft.com>"

Si su entorno no tiene DHCP habilitado, debe incluir los siguientes parámetros adicionales a una de estas opciones (ejemplo de uso proporcionados):

    .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass -AADAdminCredential $aadcred
    -NatIPv4Subnet 10.10.10.0/24 -NatIPv4Address 10.10.10.3 -NatIPv4DefaultGateway 10.10.10.1


### <a name="installazurestackpocps1-optional-parameters"></a>Parámetros opcionales de InstallAzureStackPOC.ps1

| Parámetro | Obligatorio opcional | Descripción |
| --------- | ----------------- | ----------- |
| AADAdminCredential | Opcional | Establece el nombre de usuario de Azure Active Directory y la contraseña. Estas credenciales Azure pueden ser un identificador de organización o una Account de Microsoft. Para usar las credenciales de Microsoft Account, omita este parámetro en el cmdlet. Si se omite este parámetro, el menú emergente autenticación de Azure le pide durante la implementación. Esto crea los tokens de autenticación y actualización usados durante la implementación. |
| AADDirectoryTenantName | Obligatorio | Establece el directorio del inquilino. Use este parámetro para especificar un directorio específico en la cuenta AAD tiene permisos para administrar varios directorios. Nombre de un inquilino de directorio AAD en el formato de pantalla completa <directoryName>. onmicrosoft.com. |
| AdminPassword | Obligatorio | Establece la cuenta de administrador local y otras cuentas de usuario en todos los equipos virtuales creados como parte de la implementación de prueba de concepto. Esta contraseña debe coincidir con la contraseña de administrador local actual en el host. |
| AzureEnvironment | Opcional | Seleccione el entorno de Azure con la que desea registrar esta implementación de la pila de Azure. Las opciones incluyen *Azure público*, *Azure - China*, *Azure - EE*. |
| EnvironmentDNS | Opcional | Como parte de la implementación de la pila de Azure, se crea un servidor DNS. Para permitir que los equipos dentro de la solución para resolver nombres fuera de la marca, proporcione su servidor DNS de la infraestructura existente. El servidor en DNS reenvía las solicitudes de resolución de nombre desconocido a este servidor. |
| NatIPv4Address | Necesario para la compatibilidad con DHCP NAT | Establece una dirección IP estática para MAS BGPNAT01. Solo use este parámetro si DHCP no puede asignar una dirección IP válida para tener acceso a Internet. |
| NatIPv4DefaultGateway | Necesario para la compatibilidad con DHCP NAT | Establece la puerta de enlace que se utiliza con la dirección IP estática para MAS BGPNAT01. Solo use este parámetro si DHCP no puede asignar una dirección IP válida para tener acceso a Internet.  |
| NatIPv4Subnet | Necesario para la compatibilidad con DHCP NAT | Prefijo de subred IP utilizado para DHCP sobre la compatibilidad con NAT. Solo use este parámetro si DHCP no puede asignar una dirección IP válida para tener acceso a Internet.  |
| PublicVLan | Opcional | Establece el identificador de VLAN. Solo use este parámetro si el host y BGPNAT01 MAS deben configurar VLAN ID para tener acceso a la red física (e Internet). Por ejemplo,`.\InstallAzureStackPOC.ps1 –Verbose –PublicVLan 305` |
| Volver a ejecutar | Opcional | Utilice este indicador para volver a ejecutar la implementación.  Se usan todas las entradas anteriores. Vuelva a introducir datos proporcionados anteriormente no es compatible porque se generan varios valores únicos y se utiliza para su implementación. |
| Servidores horarios | Opcional | Use este parámetro si necesita especificar un servidor de tiempo específico. |

## <a name="next-steps"></a>Pasos siguientes

[Conectarse a la pila de Azure](azure-stack-connect-azure-stack.md)

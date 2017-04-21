<properties 
    pageTitle="Usar PowerShell para crear una máquina virtual con un servidor de informes de modo nativo | Microsoft Azure"
    description="Este tema describe y le guiará a través de la implementación y configuración de un servidor de informes de modo nativo de SQL Server Reporting Services en un equipo Virtual de Azure. "
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="guyinacube"
    manager="erikre"
    editor="monicar" 
    tags="azure-service-management"/>
<tags 
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/04/2016"
    ms.author="asaxton" />

# <a name="use-powershell-to-create-an-azure-vm-with-a-native-mode-report-server"></a>Usar PowerShell para crear una máquina virtual de Azure con un servidor de informes de modo nativo

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]
 

Este tema describe y le guiará a través de la implementación y configuración de un servidor de informes de modo nativo de SQL Server Reporting Services en un equipo Virtual de Azure. Los pasos de este documento use una combinación de pasos manuales para crear la máquina virtual y una secuencia de comandos de Windows PowerShell para configurar Reporting Services en la máquina virtual. La secuencia de comandos de configuración incluye abrir un puerto de firewall para HTTP o HTTPs.

>[AZURE.NOTE] Si no se requieren **HTTPS** en el servidor de informes, **omita el paso 2**.
>
>Después de crear la máquina virtual en el paso 1, vaya a la sección Usar secuencia de comandos para configurar el servidor de informes y HTTP. Después de ejecutar la secuencia de comandos, está listo para usar el servidor de informes.

## <a name="prerequisites-and-assumptions"></a>Requisitos previos y suposiciones

- **Suscripción de Azure**: comprobar el número de núcleos disponibles en su suscripción de Azure. Si crea el tamaño de memoria virtual recomendado de **A3**, debe núcleos disponibles de **4** . Si utiliza un tamaño de memoria virtual de **A2**, debe núcleos disponibles de **2** .
    
    - Para comprobar el límite de núcleo de su suscripción, en el portal de Azure clásico, haga clic en configuración en el panel izquierdo y, a continuación, haga clic en uso en el menú superior.
    
    - Para aumentar la cuota de core, póngase en contacto con [Soporte técnico de Azure](https://azure.microsoft.com/support/options/). Para obtener información de tamaño VM, vea [Tamaños de máquina Virtual para Azure](virtual-machines-linux-sizes.md).

- **Secuencias de comandos de Windows PowerShell**: el tema, se supone que tiene un conocimiento básico del funcionamiento de Windows PowerShell. Para obtener más información sobre el uso de Windows PowerShell, consulte lo siguiente:

    - [Inicio de Windows PowerShell en Windows Server](https://technet.microsoft.com/library/hh847814.aspx)
    
    - [Introducción a Windows PowerShell](https://technet.microsoft.com/library/hh857337.aspx)

## <a name="step-1-provision-an-azure-virtual-machine"></a>Paso 1: Aprovisionar una máquina Virtual de Azure

1. Desplácese hasta el portal clásico Azure.

1. En el panel izquierdo, haga clic en **máquinas virtuales de Windows** .

    ![máquinas virtuales de Microsoft azure](./media/virtual-machines-windows-classic-ps-sql-report/IC660124.gif)

1. Haga clic en **nuevo**.

    ![botón nuevo](./media/virtual-machines-windows-classic-ps-sql-report/IC692019.gif)

1. Haga clic en **desde la Galería**.

    ![nueva máquina virtual de galería](./media/virtual-machines-windows-classic-ps-sql-report/IC692020.gif)

1. Haga clic en **SQL Server 2014 RTM Standard, Windows Server 2012 R2** y, a continuación, haga clic en la flecha para continuar.

    ![siguiente](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)

    Si necesita los datos de Reporting Services controlados por la característica de suscripciones, seleccione **SQL Server 2014 RTM Enterprise – Windows Server 2012 R2**. Para obtener más información sobre las ediciones de SQL Server y compatibilidad de características, consulte [Características compatibles con las ediciones de SQL Server 2012](https://msdn.microsoft.com/library/cc645993.aspx#Reporting).

1. En la página de **configuración de máquina Virtual** , edite los campos siguientes:
                                    
    - Si hay más de una **fecha de lanzamiento de versión**, seleccione la versión más reciente.
    
    - **Nombre de la máquina virtual**: el nombre del equipo también se usa en la siguiente página de configuración como el nombre de servicio de nube DNS predeterminado. El nombre DNS debe ser único en el servicio de Azure. Considere la posibilidad de configurar la máquina virtual con un nombre de equipo que describe qué se utiliza la máquina virtual. Por ejemplo, ssrsnativecloud.
    
    - **Nivel**: estándar
    
    - **Tamaño: A3** es el tamaño de memoria virtual recomendado para cargas de trabajo de SQL Server. Si una máquina virtual solo se utiliza como un servidor de informes, basta con un tamaño de memoria virtual de A2 a menos que el servidor de informes experimenta un gran volumen de trabajo. Para obtener información sobre precios de VM, vea [Máquinas virtuales de precios](https://azure.microsoft.com/pricing/details/virtual-machines/).
    
    - **Nuevo nombre de usuario**: el nombre que proporcione se crea como administrador en la máquina virtual.
    
    - **Nueva contraseña** y **Confirmar**. Esta contraseña se utiliza para la nueva cuenta de administrador y se recomienda que usar una contraseña segura.
    
    - Haga clic en **siguiente**. ![siguiente](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)

1. En la siguiente página, modifique los campos siguientes:

    - **Servicio de nube**: seleccione **crear un nuevo servicio de nube**.
    
    - **Nombre de DNS del servicio de nube**: este es el nombre DNS público del servicio de nube que está asociado a la máquina virtual. El nombre predeterminado es el nombre que escribió en el nombre de la máquina virtual. Si se encuentra en pasos posteriores del tema, crear un certificado SSL de confianza y, a continuación, se utiliza el nombre DNS para el valor de la "**emitido para**" del certificado.
    
    - **Red de grupo y virtuales o de afinidad de región**: seleccione la región más próxima a los usuarios finales.
    
    - **Cuenta de almacenamiento**: usar una cuenta de almacenamiento generada automáticamente.
    
    - **Establece la disponibilidad**: ninguno.
    
    - **EXTREMOS** Mantener los extremos de **Escritorio remoto** y **PowerShell** y, a continuación, agregue extremo HTTP o HTTPS, dependiendo de su entorno.

        - **HTTP**: los puertos públicos y privados de predeterminados son **80**. Tenga en cuenta que si utiliza un puerto privado distinto de 80, modificar **$HTTPport = 80** en la secuencia de comandos de http.

        - **HTTPS**: los puertos públicos y privados de forma predeterminada son **443**. Recomendación de seguridad es cambiar el puerto privado y configurar el firewall y el servidor de informes para usar el puerto privado. Para obtener más información sobre los extremos, vea [cómo establecer comunicación con un equipo Virtual](virtual-machines-windows-classic-setup-endpoints.md). Tenga en cuenta que si usa un puerto 443, cambie el parámetro **$HTTPsport = 443** en la secuencia de comandos HTTPS.
    
    - Haga clic en siguiente. ![siguiente](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)

1. En la última página del asistente, mantener el predeterminado **instalar al agente VM** seleccionado. Los pasos de este tema no utilizan al agente VM pero si piensa mantener esta VM, el agente VM y extensiones le permitirá mejorar CM.  Para obtener más información sobre el agente VM, consulte [agente de VM y extensiones: parte 1](https://azure.microsoft.com/blog/2014/04/11/vm-agent-and-extensions-part-1/). Uno de ad instaladas las extensiones de forma predeterminada ejecuta es la extensión de "BGINFO" que se muestra en el escritorio de la máquina virtual, la información del sistema como IP interna y espacio libre en disco.

1. Haga clic en Finalizar. ![Vale](./media/virtual-machines-windows-classic-ps-sql-report/IC660122.gif)

1. El **estado** de la máquina virtual de muestra como **inicial (aprovisionamiento)** durante el proceso de aprovisionamiento y, a continuación, como **Ejecutar** cuando la máquina virtual está configurado y listo para usar.

## <a name="step-2-create-a-server-certificate"></a>Paso 2: Crear un certificado de servidor

>[AZURE.NOTE] Si no necesita HTTPS en el servidor de informes, puede **Omitir el paso 2** y vaya a la sección **Usar secuencia de comandos para configurar el servidor de informes y HTTP**. Utilice la secuencia de comandos HTTP para configurar rápidamente el servidor de informes y estará listo para usar el servidor de informes.

Para utilizar HTTPS en la máquina virtual, necesita un certificado SSL de confianza. Dependiendo de su situación, puede usar uno de los dos métodos siguientes:

- Un certificado SSL emitido por una entidad emisora de certificados (CA) y de confianza de Microsoft. Los certificados de entidad emisora de certificados raíz se requieren para distribuirse a través del programa de certificados raíz de Microsoft. Para obtener más información acerca de este programa, vea [Windows y Windows Phone 8 SSL raíz programa de certificados (CA a miembro)](http://social.technet.microsoft.com/wiki/contents/articles/14215.windows-and-windows-phone-8-ssl-root-certificate-program-member-cas.aspx) e [Introducción para el programa de certificados raíz de Microsoft](http://social.technet.microsoft.com/wiki/contents/articles/3281.introduction-to-the-microsoft-root-certificate-program.aspx).

- Un certificado autofirmado. Certificados de firma automática no se recomiendan para entornos de producción.

### <a name="to-use-a-certificate-created-by-a-trusted-certificate-authority-ca"></a>Usar un certificado creado por una autoridad de confianza de certificación (CA)

1. **Solicitar un certificado de servidor para el sitio Web de una entidad emisora de certificados**. 

    Puede usar al Asistente para certificados de servidor Web para generar un archivo de solicitud de certificado (Certreq.txt) que se envía a una entidad emisora de certificados o para generar una solicitud de una entidad emisora de certificados en línea. Por ejemplo, certificado de servicios de Microsoft en Windows Server 2012. Según el nivel de garantía de identificación ofrecido por el certificado de servidor, es varios días hasta varios meses de la entidad emisora de certificados aprobar la solicitud y enviar un archivo de certificado. 

    Para obtener más información acerca de cómo pedir certificados de servidor, consulte lo siguiente: 
    
    - Utilice [Certreq](https://technet.microsoft.com/library/cc725793.aspx), [Certreq](https://technet.microsoft.com/library/cc725793.aspx).
    
    - Herramientas de seguridad para administrar Windows Server 2012.

    [Herramientas de seguridad para administrar Windows Server 2012](https://technet.microsoft.com/library/jj730960.aspx)

    >[AZURE.NOTE] El campo **emitido a** de la confianza certificado SSL debe ser el mismo que el **Nombre de DNS del servicio de nube** que se ha usado para la nueva máquina virtual.

1. **Instalar el certificado de servidor en el servidor Web**. En este caso, el servidor Web es la máquina virtual que hospeda el servidor de informes y el sitio Web se crea en pasos posteriores al configurar Reporting Services. Para obtener más información acerca de cómo instalar el certificado de servidor en el servidor Web mediante el complemento de MMC certificados, consulte [instalar un certificado de servidor](https://technet.microsoft.com/library/cc740068).
    
    **Si desea usar los comandos que se incluye en este tema, para configurar el servidor de informes, el valor de los certificados huella digital** es necesario realizar un parámetro de la secuencia de comandos. Vea la siguiente sección para obtener más información sobre cómo obtener la huella digital del certificado.

1. Asignar el certificado de servidor a servidor de informes. La asignación se ha completado en la sección siguiente al configurar el servidor de informes.

### <a name="to-use-the-virtual-machines-self-signed-certificate"></a>Usar el certificado autofirmado máquinas virtuales de Windows

Un certificado autofirmado se creó en la máquina virtual cuando se aprovisiona la máquina virtual. El certificado tiene el mismo nombre que el nombre de VM DNS. Para evitar errores de certificado, es necesario que el certificado es de confianza en la máquina virtual de sí misma y también por todos los usuarios del sitio.

1. Para confiar en la entidad emisora de certificados raíz del certificado en la máquina virtual Local, agregue el certificado a las **Entidades emisoras de certificados raíz de confianza**. A continuación se muestra un resumen de los pasos necesarios. Para conocer los pasos detallados acerca de cómo confiar en la entidad emisora de certificados, consulte [instalar un certificado de servidor](https://technet.microsoft.com/library/cc740068).

    1. Desde el portal de clásico Azure, seleccione la máquina virtual y haga clic en conectar. Según la configuración del explorador, es posible que se le pregunte si para guardar un archivo RDP para conectarse a la máquina virtual.
    
        ![conectarse a azure máquina virtual](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) Use el nombre de la máquina virtual de usuario, el nombre de usuario y la contraseña configurada cuando creó la máquina virtual. 
    
        Por ejemplo, en la siguiente imagen, el nombre de la máquina virtual es **ssrsnativecloud** y el nombre de usuario es **usuario de prueba**.
        
        ![nombre de inicio de sesión recoge vm](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)
    
    1. Ejecute mmc.exe. Para obtener más información, vea [Cómo: ver los certificados con el complemento de MMC](https://msdn.microsoft.com/library/ms788967.aspx).
    
    1. En el menú **archivo** de aplicación de consola, agregue el complemento de **certificados** , seleccione la **Cuenta de equipo** cuando se le solicite y, a continuación, haga clic en **siguiente**.
    
    1. Seleccione el **Equipo Local** para administrar y, a continuación, haga clic en **Finalizar**.
    
    1. Haga clic en **Aceptar** y, a continuación, expanda la **certificados - Personal** nodos y, a continuación, haga clic en **certificados**. El certificado se denomina después del nombre DNS de la máquina virtual y termina con **cloudapp.net**. Haga clic en el nombre del certificado y haga clic en **Copiar**.
    
    1. Expanda el nodo de **Entidades emisoras de certificados raíz de confianza** y, a continuación, haga clic en **certificados** y, a continuación, haga clic en **Pegar**.
    
    1. Para validar, haga doble clic en el nombre del certificado en **Entidades emisoras de certificados raíz de confianza** y compruebe que no hay ningún error y ver el certificado. **Si desea usar la secuencia de comandos HTTPS incluido en este tema, para configurar el servidor de informes, el valor de los certificados huella digital** es necesario realizar un parámetro de la secuencia de comandos. **Para obtener el valor de la huella digital**, complete los pasos siguientes. También es un ejemplo de PowerShell para recuperar la huella digital en la sección [Usar secuencia de comandos para configurar el servidor de informes y HTTPS](#use-script-to-configure-the-report-server-and-HTTPS).
        
        1. Haga doble clic en el nombre del certificado, por ejemplo ssrsnativecloud.cloudapp.net.
        
        1. Haga clic en la ficha **Detalles** .
        
        1. Haga clic en la **huella digital**. El valor de la huella digital se muestra en el campo detalles, por ejemplo a6 08 3c df f9 0b f7 e3 7c 25 ed a4 ed 7 ac 91 c 9 2c fb 2f.
        
        1. Copie la huella digital y guardar el valor más adelante o modificar la secuencia de comandos ahora.
        
        1. (*) Antes de ejecutar la secuencia de comandos, quite los espacios entre los pares de valores. Por ejemplo la huella digital indicada anteriormente ahora sería a6083cdff90bf7e37c25eda4ed7eac919c2cfb2f.
        
        1. Asignar el certificado de servidor a servidor de informes. La asignación se ha completado en la sección siguiente al configurar el servidor de informes.

Si está utilizando un certificado SSL autofirmado, el nombre del certificado ya de coincide con el nombre de host de la máquina virtual. Por lo tanto, el DNS de la máquina ya se ha registrado globalmente y se puede acceder desde cualquier cliente.

## <a name="step-3-configure-the-report-server"></a>Paso 3: Configurar el servidor de informes

En esta sección se explica cómo configurar la máquina virtual como un servidor de informes de modo nativo de Reporting Services. Puede usar uno de los siguientes métodos para configurar el servidor de informes:

- Utilice la secuencia de comandos para configurar el servidor de informes

- Use el Administrador de configuración para configurar el servidor de informes.

Para conocer los pasos más detallados, vea la sección [Conectar con la máquina Virtual e iniciar el Administrador de configuración de Reporting Services](virtual-machines-windows-classic-ps-sql-bi.md#connect-to-the-virtual-machine-and-start-the-reporting-services-configuration-manager).

**Nota de autenticación:** Autenticación de Windows es el método de autenticación recomendado y es la autenticación de Reporting Services de forma predeterminada. Solo los usuarios que están configurados en la máquina virtual pueden tener acceso a Reporting Services y asignan a roles de Reporting Services.

### <a name="use-script-to-configure-the-report-server-and-http"></a>Usar la secuencia de comandos para configurar el servidor de informes y HTTP

Para utilizar la secuencia de comandos de Windows PowerShell para configurar el servidor de informes, realice los pasos siguientes. La configuración incluye HTTP, no HTTPS:

1. Desde el portal de clásico Azure, seleccione la máquina virtual y haga clic en conectar. Según la configuración del explorador, es posible que se le pregunte si para guardar un archivo RDP para conectarse a la máquina virtual.

    ![conectarse a azure máquina virtual](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) Use el nombre de la máquina virtual de usuario, el nombre de usuario y la contraseña configurada cuando creó la máquina virtual. 

    Por ejemplo, en la siguiente imagen, el nombre de la máquina virtual es **ssrsnativecloud** y el nombre de usuario es **usuario de prueba**.
    
    ![nombre de inicio de sesión recoge vm](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)

1. En la máquina virtual, abra **Windows PowerShell ISE** con privilegios de administrador. PowerShell ISE está instalado de forma predeterminada en Windows server 2012. Se recomienda que usar el ISE en lugar de una ventana de Windows PowerShell estándar para que puedan pegue el script en el ISE, modifique la secuencia de comandos y, a continuación, ejecute el script.

1. En Windows PowerShell ISE, haga clic en el menú **Ver** y, a continuación, haga clic en **Mostrar panel de secuencia de comandos**.

1. Copie la siguiente secuencia de comandos y pegue el script en el panel de secuencia de comandos de Windows PowerShell ISE.

        ## This script configures a Native mode report server without HTTPS
        $ErrorActionPreference = "Stop"
        
        $server = $env:COMPUTERNAME
        $HTTPport = 80 # change the value if you used a different port for the private HTTP endpoint when the VM was created.
        
        ## Set PowerShell execution policy to be able to run scripts
        Set-ExecutionPolicy RemoteSigned -Force
        
        ## Utility method for verifying an operation's result
        function CheckResult
        {
            param($wmi_result, $actionname)
            if ($wmi_result.HRESULT -ne 0) {
                write-error "$actionname failed. Error from WMI: $($wmi_result.Error)"
            }
        }
        
        $starttime=Get-Date
        write-host -foregroundcolor DarkGray $starttime StartTime
        
        ## ReportServer Database name - this can be changed if needed
        $dbName='ReportServer'
        
        ## Register for MSReportServer_ConfigurationSetting
        ## Change the version portion of the path to "v11" to use the script for SQL Server 2012
        $RSObject = Get-WmiObject -class "MSReportServer_ConfigurationSetting" -namespace "root\Microsoft\SqlServer\ReportServer\RS_MSSQLSERVER\v12\Admin"
        
        ## Report Server Configuration Steps
        
        ## Setting the web service URL ##
        write-host -foregroundcolor green "Setting the web service URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## SetVirtualDirectory for ReportServer site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportServerWebService','ReportServer',1033)
            CheckResult $r "SetVirtualDirectory for ReportServer"
        
        ## ReserveURL for ReportServerWebService - port $HTTPport (for local usage)
            write-host "Calling ReserveURL port $HTTPport"
            $r = $RSObject.ReserveURL('ReportServerWebService',"http://+:$HTTPport",1033)
            CheckResult $r "ReserveURL for ReportServer port $HTTPport" 
           
        ## Setting the Database ##
        write-host -foregroundcolor green "Setting the Database"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## GenerateDatabaseScript - for creating the database
            write-host "Calling GenerateDatabaseCreationScript for database $dbName"
            $r = $RSObject.GenerateDatabaseCreationScript($dbName,1033,$false)
            CheckResult $r "GenerateDatabaseCreationScript"
            $script = $r.Script
        
        ## Execute sql script to create the database
            write-host 'Executing Database Creation Script'
            $savedcvd = Get-Location
            Import-Module SQLPS              ## this automatically changes to sqlserver provider
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
          
        ## GenerateGrantRightsScript 
            $DBUser = "NT Service\ReportServer"
            write-host "Calling GenerateDatabaseRightsScript with user $DBUser"
            $r = $RSObject.GenerateDatabaseRightsScript($DBUser,$dbName,$false,$true)
            CheckResult $r "GenerateDatabaseRightsScript"
            $script = $r.Script
        
        ## Execute grant rights script
            write-host 'Executing Database Rights Script'
            $savedcvd = Get-Location
            cd sqlserver:\
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
        
        ## SetDBConnection - uses Windows Service (type 2), username is ignored
            write-host "Calling SetDatabaseConnection server $server, DB $dbName"
            $r = $RSObject.SetDatabaseConnection($server,$dbName,2,'','')
            CheckResult $r "SetDatabaseConnection"  
        
        ## Setting the Report Manager URL ##
        
        write-host -foregroundcolor green "Setting the Report Manager URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## SetVirtualDirectory for Reports (Report Manager) site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportManager','Reports',1033)
            CheckResult $r "SetVirtualDirectory"
        
        ## ReserveURL for ReportManager  - port $HTTPport
            write-host "Calling ReserveURL for ReportManager, port $HTTPport"
            $r = $RSObject.ReserveURL('ReportManager',"http://+:$HTTPport",1033)
            CheckResult $r "ReserveURL for ReportManager port $HTTPport"
        
        write-host -foregroundcolor green "Open Firewall port for $HTTPport"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## Open Firewall port for $HTTPport
            New-NetFirewallRule -DisplayName “Report Server (TCP on port $HTTPport)” -Direction Inbound –Protocol TCP –LocalPort $HTTPport
            write-host "Added rule Report Server (TCP on port $HTTPport) in Windows Firewall"
        
        write-host 'Operations completed, Report Server is ready'
        write-host -foregroundcolor DarkGray $starttime StartTime
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time

1. Si ha creado la máquina virtual con un puerto HTTP distinto de 80, modifique el parámetro $HTTPport = 80.

1. La secuencia de comandos está actualmente configurado para Reporting Services. Si desea ejecutar la secuencia de comandos de Reporting Services, modifique la parte de la versión de la ruta de acceso al espacio de nombres "v11", en la instrucción Get-WmiObject.

1. Ejecute la secuencia de comandos.

**Validación**: para comprobar que la funcionalidad del servidor informe básico está funcionando, consulte la sección de [comprobar la configuración](#verify-the-configuration) más adelante en este tema.

### <a name="use-script-to-configure-the-report-server-and-https"></a>Usar la secuencia de comandos para configurar el servidor de informes y HTTPS

Para usar Windows PowerShell para configurar el servidor de informes, realice los pasos siguientes. La configuración incluye HTTPS y no HTTP.

1. Desde el portal de clásico Azure, seleccione la máquina virtual y haga clic en conectar. Según la configuración del explorador, es posible que se le pregunte si para guardar un archivo RDP para conectarse a la máquina virtual.

    ![conectarse a azure máquina virtual](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) Use el nombre de la máquina virtual de usuario, el nombre de usuario y la contraseña configurada cuando creó la máquina virtual. 

    Por ejemplo, en la siguiente imagen, el nombre de la máquina virtual es **ssrsnativecloud** y el nombre de usuario es **usuario de prueba**.

    ![nombre de inicio de sesión recoge vm](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)

1. En la máquina virtual, abra **Windows PowerShell ISE** con privilegios de administrador. PowerShell ISE está instalado de forma predeterminada en Windows server 2012. Se recomienda que usar el ISE en lugar de una ventana de Windows PowerShell estándar para que puedan pegue el script en el ISE, modifique la secuencia de comandos y, a continuación, ejecute el script.

1. Para habilitar la ejecución de scripts, ejecute el siguiente comando de Windows PowerShell:

        Set-ExecutionPolicy RemoteSigned

    A continuación, puede ejecutar el siguiente procedimiento para comprobar la directiva:

        Get-ExecutionPolicy

1. En **Windows PowerShell ISE**, haga clic en el menú **Ver** y, a continuación, haga clic en **Mostrar panel de secuencia de comandos**.

1. Copie la siguiente secuencia de comandos y pegarlo en el panel de secuencia de comandos de Windows PowerShell ISE.

        ## This script configures the report server, including HTTPS
        $ErrorActionPreference = "Stop"
        $httpsport=443 # modify if you used a different port number when the HTTPS endpoint was created.
        
        # You can run the following command to get (.cloudapp.net certificates) so you can copy the thumbprint / certificate hash
        #dir cert:\LocalMachine -rec | Select-Object * | where {$_.issuer -like "*cloudapp*" -and $_.pspath -like "*root*"} | select dnsnamelist, thumbprint, issuer
        #
        # The certifacte hash is a REQUIRED parameter
        $certificatehash="" 
        # the certificate hash should not contain spaces
        
        if ($certificatehash.Length -lt 1) 
        {
            write-error "certificatehash is a required parameter"
        } 
        # Certificates should be all lower case
        $certificatehash=$certificatehash.ToLower()
        $server = $env:COMPUTERNAME
        # If the certificate is not a wildcard certificate, comment out the following line, and enable the full $DNSNAme reference.
        $DNSName="+"
        #$DNSName="$server.cloudapp.net"
        $DNSNameAndPort = $DNSName + ":$httpsport"
        
        ## Utility method for verifying an operation's result
        function CheckResult
        {
            param($wmi_result, $actionname)
            if ($wmi_result.HRESULT -ne 0) {
                write-error "$actionname failed. Error from WMI: $($wmi_result.Error)"
            }
        }
        
        $starttime=Get-Date
        write-host -foregroundcolor DarkGray $starttime StartTime
        
        ## ReportServer Database name - this can be changed if needed
        $dbName='ReportServer'
        
        write-host "The script will use $DNSNameAndPort as the DNS name and port" 
        
        ## Register for MSReportServer_ConfigurationSetting
        ## Change the version portion of the path to "v11" to use the script for SQL Server 2012
        $RSObject = Get-WmiObject -class "MSReportServer_ConfigurationSetting" -namespace "root\Microsoft\SqlServer\ReportServer\RS_MSSQLSERVER\v12\Admin"
        
        ## Reporting Services Report Server Configuration Steps
        
        ## 1. Setting the web service URL ##
        write-host -foregroundcolor green "Setting the web service URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## SetVirtualDirectory for ReportServer site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportServerWebService','ReportServer',1033)
            CheckResult $r "SetVirtualDirectory for ReportServer"
        
        ## ReserveURL for ReportServerWebService - port 80 (for local usage)
            write-host 'Calling ReserveURL port 80'
            $r = $RSObject.ReserveURL('ReportServerWebService','http://+:80',1033)
            CheckResult $r "ReserveURL for ReportServer port 80" 
        
        ## ReserveURL for ReportServerWebService - port $httpsport
            write-host "Calling ReserveURL port $httpsport, for URL: https://$DNSNameAndPort"
            $r = $RSObject.ReserveURL('ReportServerWebService',"https://$DNSNameAndPort",1033)
            CheckResult $r "ReserveURL for ReportServer port $httpsport" 
        
        ## CreateSSLCertificateBinding for ReportServerWebService port $httpsport
            write-host "Calling CreateSSLCertificateBinding port $httpsport, with certificate hash: $certificatehash"
            $r = $RSObject.CreateSSLCertificateBinding('ReportServerWebService',$certificatehash,'0.0.0.0',$httpsport,1033)
            CheckResult $r "CreateSSLCertificateBinding for ReportServer port $httpsport" 
            
        ## 2. Setting the Database ##
        write-host -foregroundcolor green "Setting the Database"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## GenerateDatabaseScript - for creating the database
            write-host "Calling GenerateDatabaseCreationScript for database $dbName"
            $r = $RSObject.GenerateDatabaseCreationScript($dbName,1033,$false)
            CheckResult $r "GenerateDatabaseCreationScript"
            $script = $r.Script
        
        ## Execute sql script to create the database
            write-host 'Executing Database Creation Script'
            $savedcvd = Get-Location
            Import-Module SQLPS                    ## this automatically changes to sqlserver provider
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
          
        ## GenerateGrantRightsScript 
            $DBUser = "NT Service\ReportServer"
            write-host "Calling GenerateDatabaseRightsScript with user $DBUser"
            $r = $RSObject.GenerateDatabaseRightsScript($DBUser,$dbName,$false,$true)
            CheckResult $r "GenerateDatabaseRightsScript"
            $script = $r.Script
        
        ## Execute grant rights script
            write-host 'Executing Database Rights Script'
            $savedcvd = Get-Location
            cd sqlserver:\
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
        
        ## SetDBConnection - uses Windows Service (type 2), username is ignored
            write-host "Calling SetDatabaseConnection server $server, DB $dbName"
            $r = $RSObject.SetDatabaseConnection($server,$dbName,2,'','')
            CheckResult $r "SetDatabaseConnection"  
        
        ## 3. Setting the Report Manager URL ##
        
        write-host -foregroundcolor green "Setting the Report Manager URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## SetVirtualDirectory for Reports (Report Manager) site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportManager','Reports',1033)
            CheckResult $r "SetVirtualDirectory"
        
        ## ReserveURL for ReportManager  - port 80
            write-host 'Calling ReserveURL for ReportManager, port 80'
            $r = $RSObject.ReserveURL('ReportManager','http://+:80',1033)
            CheckResult $r "ReserveURL for ReportManager port 80"
        
        ## ReserveURL for ReportManager - port $httpsport
            write-host "Calling ReserveURL port $httpsport, for URL: https://$DNSNameAndPort"
            $r = $RSObject.ReserveURL('ReportManager',"https://$DNSNameAndPort",1033)
            CheckResult $r "ReserveURL for ReportManager port $httpsport" 
        
        ## CreateSSLCertificateBinding for ReportManager port $httpsport
            write-host "Calling CreateSSLCertificateBinding port $httpsport with certificate hash: $certificatehash"
            $r = $RSObject.CreateSSLCertificateBinding('ReportManager',$certificatehash,'0.0.0.0',$httpsport,1033)
            CheckResult $r "CreateSSLCertificateBinding for ReportManager port $httpsport" 
        
        write-host -foregroundcolor green "Open Firewall port for $httpsport"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## Open Firewall port for $httpsport
            New-NetFirewallRule -DisplayName “Report Server (TCP on port $httpsport)” -Direction Inbound –Protocol TCP –LocalPort $httpsport
            write-host "Added rule Report Server (TCP on port $httpsport) in Windows Firewall"
        
        write-host 'Operations completed, Report Server is ready'
        write-host -foregroundcolor DarkGray $starttime StartTime
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time

1. Modificar el parámetro **$certificatehash** en la secuencia de comandos:

    - Este es un parámetro **necesario** . Si no ha guardado el valor de certificado de los pasos anteriores, use uno de los siguientes métodos para copiar el valor de hash de certificado de la huella digital de certificados.:

        En la máquina virtual, abra Windows PowerShell ISE y ejecute el siguiente comando:

            dir cert:\LocalMachine -rec | Select-Object * | where {$_.issuer -like "*cloudapp*" -and $_.pspath -like "*root*"} | select dnsnamelist, thumbprint, issuer

        El resultado tendrá un aspecto similar al siguiente. Si la secuencia de comandos devuelve una línea en blanco, la máquina virtual no tiene un certificado configurado por ejemplo, vea la sección [usar el certificado autofirmado máquinas virtuales de Windows](#to-use-the-virtual-machines-self-signed-certificate).
    
    OR
    
    - En la máquina virtual ejecute mmc.exe y, a continuación, agregue el complemento de **certificados** .
    
    - En el nodo de **Entidades emisoras de certificados raíz de confianza** , haga doble clic en el nombre del certificado. Si está utilizando el certificado autofirmado de la máquina virtual, el certificado se denomina después del nombre DNS de la máquina virtual y termina con **cloudapp.net**.
    
    - Haga clic en la ficha **Detalles** .
    
    - Haga clic en la **huella digital**. El valor de la huella digital se muestra en el campo detalles, por ejemplo af 11 60 b6 4b 28 8 d 89 0a 82 12 ff 6b a9 c3 66 4f 31 90 48
    
    - **Antes de ejecutar la secuencia de comandos**, quitar los espacios entre los pares de valores. Por ejemplo, af1160b64b288d890a8212ff6ba9c3664f319048

1. Modificar el parámetro **$httpsport** : 

    - Si utiliza el puerto 443 del extremo HTTPS, es necesario actualizar este parámetro en la secuencia de comandos. En caso contrario, use el valor de puerto seleccionado cuando se configura el extremo HTTPS privado en la máquina virtual.

1. Modificar el parámetro **$DNSName** : 

    - La secuencia de comandos está configurado para un certificado comodín $DNSName = "+". Si no desea configurar para un enlace de certificado comodín, comente $DNSName no hace ="+"y habilite la línea siguiente, la referencia de $DNSNAme completa, ## $DNSName="$server.cloudapp.net".

        Cambie el valor de $DNSName si no desea usar el nombre DNS de la máquina virtual de Reporting Services. Si utiliza el parámetro, el certificado también debe utilizar este nombre y registrar el nombre de forma global en un servidor DNS.

1. La secuencia de comandos está actualmente configurado para Reporting Services. Si desea ejecutar la secuencia de comandos de Reporting Services, modifique la parte de la versión de la ruta de acceso al espacio de nombres "v11", en la instrucción Get-WmiObject.

1. Ejecute la secuencia de comandos.

**Validación**: para comprobar que la funcionalidad del servidor informe básico está funcionando, consulte la sección de [comprobar la configuración](#verify-the-connection) más adelante en este tema. Para comprobar el certificado enlace abra un símbolo del sistema con privilegios de administrador y, a continuación, ejecute el siguiente comando:

    netsh http show sslcert

El resultado incluye las siguientes:

    IP:port                      : 0.0.0.0:443

    Certificate Hash             : f98adf786994c1e4a153f53fe20f94210267d0e7

### <a name="use-configuration-manager-to-configure-the-report-server"></a>Usar el Administrador de configuración para configurar el servidor de informes

Si no desea ejecutar el script de PowerShell para configurar el servidor de informes, siga los pasos de esta sección para utilizar el Administrador de configuración de modo nativo de Reporting Services para configurar el servidor de informes.

1. Desde el portal de clásico Azure, seleccione la máquina virtual y haga clic en conectar. Use el nombre de usuario y contraseña configurada cuando creó la máquina virtual.

    ![conectarse a azure máquina virtual](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif)

1. Ejecutar Windows update e instalar actualizaciones para la máquina virtual. Si se requiere un reinicio de la máquina virtual, reinicie la máquina virtual y volver a conectar con la máquina virtual desde el portal de clásico de Azure.

1. En el menú Inicio de la máquina virtual, escriba **Reporting Services** y abra el **Administrador de configuración de Reporting Services**.

1. Deje los valores predeterminados para el **Nombre del servidor** y la **Instancia del servidor de informes**. Haga clic en **Conectar**.

1. En el panel izquierdo, haga clic en **Dirección URL del servicio Web**.

1. De forma predeterminada, RS está configurado para el puerto HTTP 80 con "todos asignado" de IP. Para agregar HTTPS:

    1. En el **Certificado SSL**: seleccione el certificado que desea usar, por ejemplo [nombre de VM]. cloudapp.net. Si no se muestran ningún certificados, consulte la sección **paso 2: crear un certificado de servidor** para obtener información sobre cómo instalar y confiar en el certificado en la máquina virtual.
    
    1. En **Puerto SSL**: elija 443. Si ha configurado el extremo HTTPS privado en la máquina virtual con otro puerto privado, usar ese valor aquí.
    
    1. Haga clic en **Aplicar** y espere a que finalice la operación.

1. En el panel izquierdo, haga clic en **base de datos**.

    1. Haga clic en **cambiar BAS**.
    
    1. Haga clic en **crear una nueva base de datos de servidor de informes** y, a continuación, haga clic en **siguiente**.
    
    1. Deje el **Nombre del servidor**predeterminado: la máquina virtual de nombres y deje el valor predeterminado de **Tipo de autenticación** como **Usuario actual** : **Seguridad integrada**. Haga clic en **siguiente**.
    
    1. Deje el **Nombre de la base de datos** como **servidor de informes** predeterminado y haga clic en **siguiente**.
    
    1. Deje el valor predeterminado del **Tipo de autenticación** como **Credenciales de servicio** y haga clic en **siguiente**.
    
    1. En la página **Resumen** , haga clic en **siguiente** .
    
    1. Una vez completada la configuración, haga clic en **Finalizar**.

1. En el panel izquierdo, haga clic en **Dirección URL del Administrador de informes**. Deje el valor predeterminado del **Directorio Virtual** como **informes** y haga clic en **Aplicar**.

1. Haga clic en **Salir** para cerrar el Administrador de configuración de Reporting Services.

## <a name="step-4-open-windows-firewall-port"></a>Paso 4: Abrir el puerto de Firewall de Windows

>[AZURE.NOTE] Si ha usado una de las secuencias de comandos para configurar el servidor de informes, puede omitir esta sección. La secuencia de comandos incluye un paso para abrir el puerto de firewall. El valor predeterminado era puertos 80 para HTTP y 443 para HTTPS.

Para conectarse al administrador de informes o el servidor de informes en la máquina virtual de forma remota, es necesario un extremo TCP en la máquina virtual. Es necesario abrir el mismo puerto en firewall de la máquina virtual. Se creó el extremo cuando la máquina virtual aprovisionada.

Esta sección proporciona información básica sobre cómo abrir el puerto de firewall. Para obtener más información, consulte [configurar un Firewall para el acceso al servidor de informes](https://technet.microsoft.com/library/bb934283.aspx)

>[AZURE.NOTE] Si utiliza la secuencia de comandos para configurar el servidor de informes, puede omitir esta sección. La secuencia de comandos incluye un paso para abrir el puerto de firewall.

Si ha configurado un puerto privado para HTTPS 443, modifique la siguiente secuencia de comandos en consecuencia. Para abrir el puerto **443** en el Firewall de Windows, haga lo siguiente:

1. Abra una ventana de Windows PowerShell con privilegios de administrador.

1. Si utiliza un puerto 443 cuando configura el extremo HTTPS en la máquina virtual, actualice el puerto en el comando siguiente y, a continuación, ejecute el comando:

        New-NetFirewallRule -DisplayName “Report Server (TCP on port 443)” -Direction Inbound –Protocol TCP –LocalPort 443

1. Cuando finalice el comando, **Aceptar** se muestra en el símbolo del sistema.

Para comprobar que el puerto está abierto, abra una ventana de Windows PowerShell y ejecute el siguiente comando:

    get-netfirewallrule | where {$_.displayname -like "*report*"} | select displayname,enabled,action

## <a name="verify-the-configuration"></a>Comprobar la configuración

Para comprobar que la funcionalidad del servidor informe básico funciona, abra el explorador con privilegios de administrador y, a continuación, busque el Administrador de informes de ad de servidor de informe siguiente URL:

- En la máquina virtual, vaya a la dirección URL de servidor de informes:

        http://localhost/reportserver

- En la máquina virtual, vaya a la dirección URL de administrador de informes:

        http://localhost/Reports

- Desde el equipo local, busque el informe **remoto** administrador en la máquina virtual. Actualizar el nombre DNS en el ejemplo siguiente, según corresponda. Cuando se le solicite una contraseña, use las credenciales de administrador que creó al aprovisionada la máquina virtual. El nombre de usuario está en [dominio]\[nombre de usuario] formato, donde el dominio es el nombre del equipo VM, por ejemplo ssrsnativecloud\testuser. Si no está utilizando HTTP**S**, quite el **s** en la dirección URL. Vea la siguiente sección para obtener información sobre cómo crear usuarios adicionales en máquina virtual.

        https://ssrsnativecloud.cloudapp.net/Reports

- Desde el equipo local, vaya a la URL del servidor de informes remoto. Actualizar el nombre DNS en el ejemplo siguiente, según corresponda. Si no usa HTTPS, quite el s en la dirección URL.

        https://ssrsnativecloud.cloudapp.net/ReportServer

## <a name="create-users-and-assign-roles"></a>Crear usuarios y asignar Roles

Después de configurar y comprobar el servidor de informes, una tarea administrativa común es crear uno o más usuarios y asignar a usuarios a las funciones de Reporting Services. Para obtener más información, consulte lo siguiente:

- [Crear una cuenta de usuario local](https://technet.microsoft.com/library/cc770642.aspx)

- [Usuario conceder acceso a un servidor de informes (Administrador de informes)](https://msdn.microsoft.com/library/ms156034.aspx))

- [Crear y administrar las asignaciones de roles](https://msdn.microsoft.com/library/ms155843.aspx)

## <a name="to-create-and-publish-reports-to-the-azure-virtual-machine"></a>Crear y publicar informes en la máquina Virtual de Azure

En la tabla siguiente resume algunas de las opciones disponibles para publicar informes existentes desde un equipo local al servidor de informes alojada en la máquina Virtual de Microsoft Azure:

- **Secuencia de comandos de RS.exe**: usar RS.exe script para copiar elementos de informe de y el servidor de informes existente a su máquina Virtual de Microsoft Azure. Para obtener más información, vea la sección "Modo nativo al modo nativo: Máquina Virtual de Microsoft Azure" en el [ejemplo Reporting Services rs.exe Script para migrar el contenido entre servidores de informes](https://msdn.microsoft.com/library/dn531017.aspx).

- **Generador de informes**: la máquina virtual incluye el clic-una vez una versión de Microsoft SQL Server Report Builder. Para iniciar el tiempo de generador el primer informe en la máquina virtual:

    1. Inicie el explorador con privilegios de administrador.
    
    1. Vaya a Administrador de informes en la máquina virtual y haga clic en **Generador de informes** en la cinta de opciones.

    Para obtener más información, vea [instalar, desinstalar y compatible con el generador de informes](https://technet.microsoft.com/library/dd207038.aspx).

- **SQL Server Data Tools: VM**: si ha creado la máquina virtual con SQL Server 2012, las herramientas de datos de SQL Server está instalada en la máquina virtual y puede utilizarse para crear **Proyectos de servidor de informes** e informes en la máquina virtual. Herramientas de datos de SQL Server pueden publicar los informes en el servidor de informes en la máquina virtual.
    
    Si ha creado la máquina virtual con SQL server 2014, puede instalar SQL Server datos herramientas de inteligencia empresarial para visual Studio. Para obtener más información, consulte lo siguiente:

    - [Microsoft SQL Server Data Tools - Business Intelligence para Visual Studio de 2013](https://www.microsoft.com/download/details.aspx?id=42313)

    - [Microsoft SQL Server Data Tools - Business Intelligence para Visual Studio 2012](https://www.microsoft.com/download/details.aspx?id=36843)

    - [Herramientas de datos SQL Server y SQL Server Business Intelligence (BI de SSDT)](http://curah.microsoft.com/30004/sql-server-data-tools-ssdt-and-sql-server-business-intelligence)

- **SQL Server Data Tools: remoto**: en el equipo local, crear un proyecto de Reporting Services en herramientas de datos de SQL Server que contiene los informes de Reporting Services. Configurar el proyecto para conectarse a la dirección URL del servicio web.

    ![propiedades del proyecto ssdt para el proyecto SSRS](./media/virtual-machines-windows-classic-ps-sql-report/IC650114.gif)

- **Usar secuencia de comandos**: Usar secuencia de comandos para copiar el contenido del servidor de informes. Para obtener más información, vea [ejemplo Reporting Services rs.exe Script para migrar el contenido entre servidores de informes](https://msdn.microsoft.com/library/dn531017.aspx).

## <a name="minimize-cost-if-you-are-not-using-the-vm"></a>Minimizar el costo si no está utilizando la máquina virtual

>[AZURE.NOTE] Para minimizar los cargos para sus máquinas virtuales de Azure cuando no esté en uso, cierre la máquina virtual desde el portal de clásico de Azure. Si usa las opciones de energía de Windows dentro de una máquina virtual para apagar la máquina virtual, le cobrarán siguen la misma cantidad de la máquina virtual. Para reducir los gastos, debe apagar la máquina virtual en el portal de clásico de Azure. Si ya no necesita la máquina virtual, no olvide eliminar la máquina virtual y los archivos asociados .vhd para evitar gastos de almacenamiento. Para obtener más información, consulte la sección Preguntas más frecuentes sobre los [Detalles de precios de máquinas virtuales](https://azure.microsoft.com/pricing/details/virtual-machines/)de.

## <a name="more-information"></a>Más información

### <a name="resources"></a>Recursos

- Contenido similar relacionados con una implementación de servidor único de inteligencia empresarial de SQL Server y SharePoint 2013, vea [Usar Windows PowerShell para crear una máquina virtual de Azure con SQL Server BI y SharePoint 2013](https://msdn.microsoft.com/library/azure/dn385843.aspx).

- Contenido similar relacionados con una implementación de varios servidor de inteligencia empresarial de SQL Server y SharePoint 2013, vea [Implementar SQL Server Business Intelligence en Azure máquinas virtuales de Windows](https://msdn.microsoft.com/library/dn321998.aspx).

- Para obtener información General relacionado con las implementaciones de SQL Server Business Intelligence en Azure máquinas virtuales de Windows, vea [SQL Server Business Intelligence en Azure máquinas virtuales de Windows](virtual-machines-windows-classic-ps-sql-bi.md).

- Para obtener más información acerca de los costos de Azure calcular cargos, consulte la ficha máquinas virtuales de [Calculadora de precio de Azure](https://azure.microsoft.com/pricing/calculator/?scenario=virtual-machines).

### <a name="community-content"></a>Contenido de la Comunidad

- Para obtener instrucciones paso a paso sobre cómo crear un servidor de informes de modo nativo de Reporting Services sin usar secuencia de comandos, vea [Hospedaje SQL Reporting Services en el equipo de Virtual de Azure](http://adititechnologiesblog.blogspot.in/2012/07/hosting-sql-reporting-service-on-azure.html).

### <a name="links-to-other-resources-for-sql-server-in-azure-vms"></a>Vínculos a otros recursos de SQL Server en máquinas virtuales de Azure

[SQL Server en máquinas virtuales de Windows Azure Introducción](virtual-machines-windows-sql-server-iaas-overview.md)

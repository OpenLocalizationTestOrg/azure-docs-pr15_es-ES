<properties
    pageTitle="Conectar equipos de Windows para el análisis de registro | Microsoft Azure"
    description="Este artículo le muestra los pasos para conectar los equipos de Windows en su infraestructura local directamente a OMS utilizando una versión personalizada de agente de supervisión de Microsoft (MMA)."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="banders"/>


# <a name="connect-windows-computers-to-log-analytics"></a>Conectar equipos de Windows para el análisis de registro

Este artículo le muestra los pasos para conectar los equipos de Windows en su infraestructura local directamente a áreas de trabajo OMS utilizando una versión personalizada de agente de supervisión de Microsoft (MMA). Debe instalar y conectar a agentes para todos los equipos que desee a incorporado a OMS máquina para que enviar datos a OMS así como ver y actuar en esos datos en el portal OMS. Cada agente puede informar a varias áreas de trabajo.

Puede instalar agentes mediante la configuración de la línea de comandos, o con configuración de estado deseado (DSC) en la automatización de Azure.  

>[AZURE.NOTE] Para equipos virtuales ejecuta en Azure puede simplificar instalación mediante la [extensión de máquina virtual](log-analytics-azure-vm-extension.md).

En equipos con conexión a Internet, el agente utilizará la conexión a Internet para enviar datos a OMS. Para equipos que no tiene conexión a Internet, puede usar un servidor proxy o el reenviador de análisis de registro de OMS.

Conectar los equipos de Windows a OMS es sencillo con 3 sencillos pasos:

1. Descargar el archivo de instalación de agente
2. Instalar al agente mediante el método que elija
3. Configurar al agente o agregar áreas de trabajo adicionales, si es necesario

El diagrama siguiente muestra la relación entre equipos de Windows y OMS después de que haya instalado y configurado a agentes.

![OMS-directos-agente-diagrama](./media/log-analytics-windows-agents/oms-direct-agent-diagram.png)


## <a name="system-requirements-and-required-configuration"></a>Requisitos del sistema y la configuración necesaria
Antes de instalar o implementar a agentes, revise los siguientes detalles para asegurarse de que cumple los requisitos necesarios.

- Sólo se puede instalar el MMA OMS en equipos que ejecutan Windows Server 2008 Service Pack 1 o posterior o Windows 7 SP1 o posterior.
- Necesitará una suscripción de OMS.  Para obtener más información, vea [Introducción a análisis de registro](log-analytics-get-started.md).
- Cada equipo de Windows debe poder conectarse a Internet usando HTTPS. Esta conexión puede ser directa a través de un servidor proxy o mediante el reenviador de análisis de registro de OMS.
- Puede instalar el MMA OMS en equipos independientes, servidores y máquinas virtuales de Windows. Si desea conectarse máquinas virtuales de hospedado Azure OMS, vea [Conectar Azure máquinas virtuales análisis de registro](log-analytics-azure-vm-extension.md).
- El agente debe usar el puerto TCP 443 para diversos recursos. Para obtener más información, vea [establecer la configuración del servidor proxy y el firewall de análisis de registro](log-analytics-proxy-firewall.md).

## <a name="download-the-agent-setup-file-from-oms"></a>Descargar el archivo de instalación de agente de OMS
1. En el portal OMS, en la página **Introducción** , haga clic en el icono **configuración** .  Haga clic en la pestaña **Orígenes conectados** en la parte superior.  
    ![Ficha de fuentes conectada](./media/log-analytics-windows-agents/oms-direct-agent-connected-sources.png)
2. **Adjuntar equipos directamente**, haga clic en **Descargar el agente de Windows** aplicables al tipo de procesador de equipo para descargar el archivo de configuración.
3. A la derecha del **ID de área de trabajo**, haga clic en el icono de copiar y pegar el identificador en el Bloc de notas.
4. A la derecha de la **Clave principal**, haga clic en el icono de copiar y pegar la clave en el Bloc de notas.     
    ![Copiar identificador de área de trabajo y la clave principal](./media/log-analytics-windows-agents/oms-direct-agent-primary-key.png)

## <a name="install-the-agent-using-setup"></a>Instalar al agente mediante el programa de instalación
1. Ejecutar el programa de instalación para instalar al agente en un equipo que desee administrar.
2. En la página principal, haga clic en **siguiente**.
3. En la página términos de licencia, lea el contrato de licencia y, a continuación, haga clic en **acepto**.
4. En la página de la carpeta de destino, cambiar o mantener la carpeta predeterminada y, a continuación, haga clic en **siguiente**.
5. En la página de opciones de configuración de agente, puede elegir conectar el agente a Azure registro análisis (OMS), Operations Manager, o puede dejar que las opciones en blanco si desea configurar al agente más tarde. Haga clic en **siguiente**.   
    - Si ha decidido conectarse a Azure registro análisis (OMS), pegue el **Identificador de área de trabajo** y el **Área de trabajo clave (principal)** que ha copiado en el Bloc de notas en el procedimiento anterior y, a continuación, haga clic en **siguiente**.  
        ![Pegue el identificador del área de trabajo y la clave principal](./media/log-analytics-windows-agents/connect-workspace.png)
    - Si elige conectarse a Operations Manager, escriba el **Nombre del grupo de administración**, nombre del **Servidor de administración** y **Puerto de servidor de administración**y, a continuación, haga clic en **siguiente**. En la página de la cuenta de acción de agente, elija la cuenta del sistema Local o una cuenta de dominio local y, a continuación, haga clic en **siguiente**.  
        ![configuración del grupo de administración](./media/log-analytics-windows-agents/oms-mma-om-setup01.png)![cuenta de acción de agente](./media/log-analytics-windows-agents/oms-mma-om-setup02.png)

6. En la página preparado para instalar, revise las opciones y, a continuación, haga clic en **instalar**.
7. En la configuración que se completó correctamente página, haga clic en **Finalizar**.
8. Cuando haya terminado, el **Agente de supervisión de Microsoft** aparece en **El Panel de Control**. Puede revisar la configuración existe y compruebe que el agente está conectado a perspectivas operativas (OMS). Cuando se conecta a OMS, el agente muestra un mensaje que indica: **el agente de supervisión de Microsoft se ha conectado correctamente al servicio de administración de operaciones de Microsoft Suite.**

## <a name="install-the-agent-using-the-command-line"></a>Instalar al agente mediante la línea de comandos
- Modificar y, a continuación, use el ejemplo siguiente para instalar al agente mediante la línea de comandos.

    >[AZURE.NOTE] Si desea actualizar a un agente, debe usar el análisis de registro API de scripting. Vea la siguiente sección para actualizar a un agente.

    ```
    MMASetup-AMD64.exe /Q:A /R:N /C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1"
    ```

## <a name="upgrade-the-agent-and-add-a-workspace-using-a-script"></a>Actualizar al agente y agregar un área de trabajo con una secuencia de comandos
Puede actualizar a un agente y agregar un área de trabajo con la API de secuencias de comandos con el siguiente ejemplo de PowerShell el análisis de registro.

```
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey)
$mma.ReloadConfiguration()
```

>[AZURE.NOTE] Si ha usado la línea de comandos o script previamente para instalar o configurar el agente `EnableAzureOperationalInsights` se reemplazó con `AddCloudWorkspace`.

## <a name="install-the-agent-using-dsc-in-azure-automation"></a>Instalar al agente mediante DSC en automatización de Azure

>[AZURE.NOTE] En este ejemplo de procedimiento y de secuencia de comandos no actualizará a un agente existente.

1. Importar el módulo de DSC xPSDesiredStateConfiguration desde [http://www.powershellgallery.com/packages/xPSDesiredStateConfiguration](http://www.powershellgallery.com/packages/xPSDesiredStateConfiguration) en automatización de Azure.  
2.  Crear activos de variable de automatización de Azure para *OPSINSIGHTS_WS_ID* y *OPSINSIGHTS_WS_KEY*. Establezca *OPSINSIGHTS_WS_ID* a su identificador de área de trabajo de análisis de registro de OMS y *OPSINSIGHTS_WS_KEY* a la clave principal de su área de trabajo.
3.  Usar la siguiente secuencia de comandos y guárdela como MMAgent.ps1
4.  Modificar y, a continuación, utilice el siguiente ejemplo para instalar al agente mediante DSC en automatización de Azure. Importar MMAgent.ps1 en Azure automatización mediante la interfaz de automatización de Azure o cmdlet.
5.  Asignar un nodo de configuración. En 15 minutos, el nodo deberá comprobar su configuración y se insertará el MMA al nodo.

```
Configuration MMAgent
{
    $OIPackageLocalPath = "C:\MMASetup-AMD64.exe"
    $OPSINSIGHTS_WS_ID = Get-AutomationVariable -Name "OPSINSIGHTS_WS_ID"
    $OPSINSIGHTS_WS_KEY = Get-AutomationVariable -Name "OPSINSIGHTS_WS_KEY"


    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    Node OMSnode {
        Service OIService
        {
            Name = "HealthService"
            State = "Running"
            DependsOn = "[Package]OI"
        }

        xRemoteFile OIPackage {
            Uri = "http://download.microsoft.com/download/0/C/0/0C072D6E-F418-4AD4-BCB2-A362624F400A/MMASetup-AMD64.exe"
            DestinationPath = $OIPackageLocalPath
        }

        Package OI {
            Ensure = "Present"
            Path  = $OIPackageLocalPath
            Name = "Microsoft Monitoring Agent"
            ProductId = "8A7F2C51-4C7D-4BFD-9014-91D11F24AAE2"
            Arguments = '/C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=' + $OPSINSIGHTS_WS_ID + ' OPINSIGHTS_WORKSPACE_KEY=' + $OPSINSIGHTS_WS_KEY + ' AcceptEndUserLicenseAgreement=1"'
            DependsOn = "[xRemoteFile]OIPackage"
        }
    }
}  


```


## <a name="configure-an-agent-manually-or-add-additional-workspaces"></a>Configurar a un agente de forma manual o agregar áreas de trabajo adicionales
Si ha instalado a agentes pero no los configuró o si desea que el agente para notificar a varias áreas de trabajo, puede usar la siguiente información para habilitar a un agente o vuelva a configurarlo. Después de haber configurado al agente, se registrará con el servicio de agente y obtener información de configuración necesaria y paquetes de administración que contienen información sobre la solución.

1. Una vez instalado al agente de supervisión de Microsoft, abra **El Panel de Control**.
2. Abra **El agente de supervisión de Microsoft** y, a continuación, haga clic en la pestaña de **Análisis de registro de Azure (OMS)** .   
3. Haga clic en **Agregar** para abrir el cuadro **Agregar un área de trabajo de análisis de registro** .
4. Pegue el **Identificador de área de trabajo** y el **Área de trabajo clave (principal)** que ha copiado en el Bloc de notas en un procedimiento anterior para el área de trabajo que desea agregar y, a continuación, haga clic en **Aceptar**.  
    ![Configurar perspectivas operativas](./media/log-analytics-windows-agents/add-workspace.png)

Después de equipos supervisados por el agente de recopilación de datos, el número de equipos supervisado por OMS aparecerá en el portal OMS en la ficha **Orígenes conectados** **configuración** como **Servidores conectados**.


## <a name="to-disable-an-agent"></a>Para deshabilitar un agente
1. Después de instalar al agente, abra **El Panel de Control**.
2. Abra el agente de supervisión de Microsoft y, a continuación, haga clic en la pestaña de **Análisis de registro de Azure (OMS)** .
3. Seleccione un área de trabajo y, a continuación, haga clic en **Quitar**. Repita este paso para otras áreas de trabajo.


## <a name="optionally-configure-agents-to-report-to-an-operations-manager-management-group"></a>También puede configurar agentes para informar a un grupo de administración de Operations Manager

Si usa Operations Manager en la infraestructura de TI, también puede usar al agente MMA como un agente de Operations Manager.

### <a name="to-configure-mma-agents-to-report-to-an-operations-manager-management-group"></a>Configurar agentes MMA para informar a un grupo de administración de Operations Manager
1.  En el equipo donde está instalado el agente, abra **El Panel de Control**.
2.  Abra **El agente de supervisión de Microsoft** y, a continuación, haga clic en la pestaña de **Operations Manager** .
    ![Ficha agente Operations Manager de supervisión de Microsoft](./media/log-analytics-windows-agents/om-mg01.png)
3.  Si los servidores de Operations Manager tienen integración con Active Directory, haga clic en **actualizar automáticamente las asignaciones de grupo de administración de AD DS**.
4.  Haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar un grupo de administración** .  
    ![Agente de supervisión de Microsoft agregar un grupo de administración](./media/log-analytics-windows-agents/oms-mma-om02.png)
5.  En el cuadro **nombre de grupo de administración** , escriba el nombre de su grupo de administración.
6.  En el cuadro **servidor de administración principal** , escriba el nombre del equipo del servidor de administración principal.
7.  En el cuadro **puerto de servidor de administración** , escriba el número de puerto TCP.
8.  En la **Cuenta de acción de agente**, elija la cuenta del sistema Local o una cuenta de dominio local.
9.  Haga clic en **Aceptar** para cerrar el cuadro de diálogo **Agregar un grupo de administración** y, a continuación, haga clic en **Aceptar** para cerrar el cuadro de diálogo **Propiedades del agente de supervisión de Microsoft** .

## <a name="optionally-configure-agents-to-use-the-oms-log-analytics-forwarder"></a>También puede configurar los agentes para utilizar el reenviador de análisis de registro de OMS

Si tiene servidores o clientes que no tienen una conexión a Internet, puede aún tiene enviar datos a OMS mediante el reenviador de análisis de registro de OMS.  Cuando se utiliza el reenviador, todos los datos de agentes se envía a través de un servidor único que tiene acceso a Internet. El reenviador transfiere datos de los agentes a OMS directamente sin analizarlos datos que se transfieren.

Vea [OMS registro análisis reenviador](https://blogs.technet.microsoft.com/msoms/2016/03/17/oms-log-analytics-forwarder) para obtener más información sobre el reenviador, incluyendo la instalación y configuración.

Para obtener información sobre cómo configurar los agentes para usar un servidor proxy, que en este caso, es el reenviador OMS, vea [establecer la configuración de proxy y el firewall de análisis de registro](log-analytics-proxy-firewall.md).

## <a name="optionally-configure-proxy-and-firewall-settings"></a>Opcionalmente, configuración de proxy y firewall
Si tiene servidores proxy o Firewall en su entorno que restringir el acceso a Internet, vea [establecer la configuración del servidor proxy y el firewall de análisis de registro](log-analytics-proxy-firewall.md) para habilitar los agentes para comunicarse con el servicio OMS.

## <a name="next-steps"></a>Pasos siguientes

- [Soluciones de agregar el registro de análisis de la Galería de soluciones](log-analytics-add-solutions.md) para agregar funcionalidad y recopilación de datos.
- [Establecer la configuración del servidor proxy y el firewall de análisis de registro](log-analytics-proxy-firewall.md) si su organización usa un servidor proxy o firewall para que los agentes puedan comunicarse con el servicio de análisis de registro.

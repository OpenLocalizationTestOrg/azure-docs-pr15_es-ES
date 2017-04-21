<properties
    pageTitle="Configurar opciones de proxy y el firewall de análisis de registro | Microsoft Azure"
    description="Establecer la configuración de proxy y firewall cuando los agentes o los servicios OMS necesitan usar puertos específicos."
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
    ms.topic="get-started-article"
    ms.date="08/23/2016"
    ms.author="banders;magoedte"/>

# <a name="configure-proxy-and-firewall-settings-in-log-analytics"></a>Configurar opciones de proxy y el firewall de análisis de registro

Acciones necesarias para configurar el servidor proxy y configuración de firewall para el análisis de registro en OMS diferir al usar Operations Manager y sus agentes frente a agentes de supervisión de Microsoft que se conectan directamente a los servidores. Revise las secciones siguientes para el tipo de agente que usa.

## <a name="configure-proxy-and-firewall-settings-with-the-microsoft-monitoring-agent"></a>Establecer la configuración de proxy y firewall con el agente de supervisión de Microsoft

Para que el agente de supervisión de Microsoft para conectarse y registrar con el servicio OMS, debe tener acceso al número de puerto de los dominios y las direcciones URL. Si usa un servidor proxy para la comunicación entre el agente y el servicio OMS, debe asegurarse de que los recursos apropiados son accesibles. Si utiliza un firewall para restringir el acceso a Internet, debe configurar el firewall para permitir el acceso a OMS. Las tablas siguientes enumeran los puertos que necesita OMS.

|**Recurso Agente**|**Puertos**|**Inspección de derivación HTTPS**|
|--------------|-----|--------------|
|\*. ods.opinsights.azure.com|443|Sí|
|\*. oms.opinsights.azure.com|443|Sí|
|\*. blob.core.windows.net|443|Sí|
|ODS.systemcenteradvisor.com|443| |

Puede usar el siguiente procedimiento para establecer la configuración de proxy para el agente de supervisión de Microsoft mediante el Panel de Control. Debe utilizar el procedimiento para cada servidor. Si tiene muchos servidores que necesita configurar, puede resultarle más fácil de usar una secuencia de comandos para automatizar este proceso. Si es así, vea el procedimiento siguiente [para configurar el agente de supervisión de Microsoft con una secuencia de comandos en la configuración de proxy](#to-configure-proxy-settings-for-the-microsoft-monitoring-agent-using-a-script).

### <a name="to-configure-proxy-settings-for-the-microsoft-monitoring-agent-using-control-panel"></a>Para establecer la configuración de proxy para el agente de supervisión de Microsoft mediante el Panel de Control

1. Abra **el Panel de Control**.

2. Abra el **agente de supervisión de Microsoft**.

3. Haga clic en la pestaña **Configuración de Proxy** .<br>  
  ![ficha Configuración de proxy](./media/log-analytics-proxy-firewall/proxy-direct-agent-proxy.png)

4. Seleccione **usar un servidor proxy** y escriba la dirección URL y el número de puerto, si lo que sea necesario, de forma similar al ejemplo mostrado. Si su servidor proxy requiere autenticación, escriba el nombre de usuario y contraseña para tener acceso al servidor proxy.

Use el procedimiento siguiente para crear un script de PowerShell que se puede ejecutar para establecer la configuración de proxy para cada agente que se conecta directamente a los servidores.

### <a name="to-configure-proxy-settings-for-the-microsoft-monitoring-agent-using-a-script"></a>Para establecer la configuración de proxy para el agente de supervisión de Microsoft con una secuencia de comandos

Copie en el ejemplo siguiente, actualizar información específica para su entorno, guardar con una extensión de nombre de archivo PS1 y, a continuación, ejecute el script en cada equipo que se conecta directamente al servicio de OMS.

        
    param($ProxyDomainName="http://proxy.contoso.com:80", $cred=(Get-Credential))

    # First we get the Health Service configuration object.  We need to determine if we
    #have the right update rollup with the API we need.  If not, no need to run the rest of the script.
    $healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'

    $proxyMethod = $healthServiceSettings | Get-Member -Name 'SetProxyInfo'

    if (!$proxyMethod)
    {
         Write-Output 'Health Service proxy API not present, will not update settings.'
         return
    }

    Write-Output "Clearing proxy settings."
    $healthServiceSettings.SetProxyInfo('', '', '')

    $ProxyUserName = $cred.username

    Write-Output "Setting proxy to $ProxyDomainName with proxy username $ProxyUserName."
    $healthServiceSettings.SetProxyInfo($ProxyDomainName, $ProxyUserName, $cred.GetNetworkCredential().password)
        

## <a name="configure-proxy-and-firewall-settings-with-operations-manager"></a>Establecer la configuración de proxy y firewall con Operations Manager

Para que un grupo de administración de Operations Manager para conectarse y registrar con el servicio OMS, debe tener acceso a los números de puerto de los dominios y direcciones URL. Si usa un servidor proxy para la comunicación entre el servidor de administración de Operations Manager y el servicio OMS, debe asegurarse de que los recursos apropiados son accesibles. Si utiliza un firewall para restringir el acceso a Internet, debe configurar el firewall para permitir el acceso a OMS. Aunque no es un servidor de administración de Operations Manager detrás de un servidor proxy, podrían ser sus agentes. En este caso, debe configurarse el servidor proxy de la misma manera como agentes con el fin de habilitar y permitir la seguridad y servicio web de datos de soluciones de administración de registros que se envían a la OMS.

Para los agentes de Operations Manager para comunicarse con el servicio OMS, la infraestructura de Operations Manager (incluidos a los agentes) debe tener la configuración de proxy correcta y la versión. La configuración de agentes de proxy se especifica en la consola del Administrador de operaciones. La versión debe ser uno de estos procedimientos:

- Operations Manager 2012 SP1 acumulativo 7 o posterior
- Operations Manager 2012 R2 acumulativo 3 o posterior


Las tablas siguientes enumeran los puertos relacionados con estas tareas.

>[AZURE.NOTE] Algunos de los recursos siguientes mencionan Advisor y perspectivas operativas, ambos estaban en versiones anteriores de OMS. Sin embargo, los recursos enumerados cambiará en el futuro.

Aquí tiene una lista de recursos de agente y puertos:<br>

|**Recurso Agente**|**Puertos**|
|--------------|-----|
|\*. ods.opinsights.azure.com|443|
|\*. oms.opinsights.azure.com|443|
|\*.BLOB.Core.Windows.NET/\*|443|
|ODS.systemcenteradvisor.com|443|
<br>
Aquí tiene una lista de recursos de servidor de administración y puertos:<br>

|**Recursos de servidor de administración**|**Puertos**|**Inspección de derivación HTTPS**|
|--------------|-----|--------------|
|Service.systemcenteradvisor.com|443| |
|\*. service.opinsights.azure.com|443| |
|\*. blob.core.windows.net|443|Sí| 
|Data.systemcenteradvisor.com|443| | 
|ODS.systemcenteradvisor.com|443| | 
|\*. ods.opinsights.azure.com|443|Sí| 
<br>
Aquí tiene una lista de puertos y recursos de la consola OMS y Operations Manager.<br>

|**Recursos de consola OMS y Operations Manager**|**Puertos**|
|----|----|
|Service.systemcenteradvisor.com|443|
|\*. service.opinsights.azure.com|443|
|\*. live.com|Puertos 80 y 443|
|\*. microsoft.com|Puertos 80 y 443|
|\*. microsoftonline.com|Puertos 80 y 443|
|\*. mms.microsoft.com|Puertos 80 y 443|
|Login.Windows.NET|Puertos 80 y 443|
<br>

Use los procedimientos siguientes para registrar su grupo de administración de Operations Manager con el servicio OMS. Si está teniendo problemas de comunicación entre el grupo de administración y el servicio OMS, use los procedimientos de validación para solucionar problemas de transmisión de datos al servicio de OMS.

### <a name="to-request-exceptions-for-the-oms-service-endpoints"></a>Para solicitar excepciones para los extremos de servicio OMS

1. Use la información de la primera tabla presentada anteriormente para garantizar que los recursos necesarios para el servidor de administración de Operations Manager sean accesibles a través de los firewalls, es posible que tenga.
2. Use la información de la segunda tabla presentada anteriormente para garantizar que los recursos necesarios para la consola de operaciones en Operations Manager y OMS sean accesibles a través de los firewalls, es posible que tenga.
3. Si usa un servidor proxy con Internet Explorer, asegúrese de que está configurado y funciona correctamente. Para comprobar, puede abrir una conexión web segura (HTTPS), por ejemplo [https://bing.com](https://bing.com). Si la conexión web segura no funciona en un explorador, probablemente no funcionará en la consola de administración de Operations Manager con servicios web en la nube.

### <a name="to-configure-the-proxy-server-in-the-operations-manager-console"></a>Para configurar el servidor proxy en la consola de Operations Manager

1. Abra la consola de Operations Manager y seleccione el área de trabajo de **administración** .

2. Expanda **Perspectivas operativas**y, a continuación, seleccione la **Conexión de perspectivas operativas**.<br>  
    ![Conexión de Operations Manager OMS](./media/log-analytics-proxy-firewall/proxy-om01.png)
3. En la vista de OMS conexión, haga clic en **Configurar el servidor de Proxy**.<br>  
    ![Conexión de Operations Manager OMS configurar servidor Proxy](./media/log-analytics-proxy-firewall/proxy-om02.png)
4. En Asistente de configuración de perspectivas operativas: Servidor Proxy, seleccione **usar un servidor proxy para tener acceso al servicio Web de perspectivas operativas**y luego escriba la dirección URL con el puerto número, por ejemplo, **http://myproxy:80**.<br>  
    ![Dirección del proxy de Operations Manager OMS](./media/log-analytics-proxy-firewall/proxy-om03.png)


### <a name="to-specify-credentials-if-the-proxy-server-requires-authentication"></a>Para especificar credenciales si el servidor proxy requiere autenticación
 Configuración y las credenciales del servidor proxy necesita propagarse a equipos administrados que notificarán a OMS. Los servidores deberían estar en el *Grupo de Microsoft System Center Advisor Monitoring Server*. Credenciales están cifradas en el registro de cada servidor en el grupo.

1. Abra la consola de Operations Manager y seleccione el área de trabajo de **administración** .
2. En **El configuración**, seleccione **perfiles**.
3. Abra el perfil de **Sistema Centro Advisor ejecutar como Proxy de perfil** .  
    ![imagen de perfil de System Center Advisor ejecutar como Proxy](./media/log-analytics-proxy-firewall/proxy-proxyacct1.png)
4. El ejecutar como asistente de perfiles, haga clic en **Agregar** para usar una cuenta ejecutar como. Puede crear una nueva cuenta ejecutar como o usar una cuenta existente. Esta cuenta debe tener permisos suficientes para pasar por el servidor proxy.  
    ![imagen del ejecutar como asistente de perfil](./media/log-analytics-proxy-firewall/proxy-proxyacct2.png)
5. Para configurar la cuenta para administrar, elija **una clase seleccionada, el grupo o el objeto** para abrir el cuadro de búsqueda de objetos.  
    ![imagen del ejecutar como asistente de perfil](./media/log-analytics-proxy-firewall/proxy-proxyacct2-1.png)
6. Busque y seleccione el **Grupo de Microsoft System Center Advisor Monitoring Server**.  
    ![imagen del cuadro de búsqueda de objetos](./media/log-analytics-proxy-firewall/proxy-proxyacct3.png)
7. Haga clic en **Aceptar** para cerrar el cuadro Agregar un ejecutar como cuenta.  
    ![imagen del ejecutar como asistente de perfil](./media/log-analytics-proxy-firewall/proxy-proxyacct4.png)
8. Complete el asistente y guardar los cambios.  
    ![imagen del ejecutar como asistente de perfil](./media/log-analytics-proxy-firewall/proxy-proxyacct5.png)


### <a name="to-validate-that-oms-management-packs-are-downloaded"></a>Para validar que la administración de OMS se descargan paquetes

Si ha agregado soluciones a OMS, puede verlos en la consola del Administrador de operaciones como paquetes de administración en **administración**. Buscar *Asesor de centro de sistema* encontrarlos rápidamente.  
    ![descargar los paquetes de administración](./media/log-analytics-proxy-firewall/proxy-mpdownloaded.png) o bien, también puede comprobar los paquetes de administración de OMS mediante el siguiente comando de Windows PowerShell en el servidor de administración de Operations Manager:

    ```
    Get-ScomManagementPack | where {$_.DisplayName -match 'Advisor'} | select Name,DisplayName,Version,KeyToken
    ```

### <a name="to-validate-that-operations-manager-is-sending-data-to-the-oms-service"></a>Para validar que el Administrador de operaciones enviar datos al servicio de OMS

1. En el servidor de administración de Operations Manager, abra al Monitor de rendimiento (perfmon.exe) y seleccione **Monitor de rendimiento**.
2. Haga clic en **Agregar**y, a continuación, seleccione **Grupos de administración de estado del servicio**.
3. Agregar todos los contadores que comienzan con **HTTP**.  
    ![Agregar contadores](./media/log-analytics-proxy-firewall/proxy-sendingdata1.png)
4. Si la configuración de Operations Manager es correcta, que verá actividad para contadores de administración de servicios de salud de eventos y otros elementos de datos, en función de los paquetes de administración que agregó en OMS y la directiva de colección de registro configurado.  
    ![Supervisar la actividad que muestra de rendimiento](./media/log-analytics-proxy-firewall/proxy-sendingdata2.png)


## <a name="next-steps"></a>Pasos siguientes

- [Soluciones de agregar el registro de análisis de la Galería de soluciones](log-analytics-add-solutions.md) para agregar funcionalidad y recopilación de datos.
- Familiarizarse con [las búsquedas de registro](log-analytics-log-searches.md) ver información detallada recopilada por soluciones.

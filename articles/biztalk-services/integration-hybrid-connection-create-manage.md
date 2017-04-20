<properties 
    pageTitle="Crear y administrar conexiones híbrido | Microsoft Azure" 
    description="Aprenda a crear una conexión híbrido, administrar la conexión e instalar al administrador de conexión híbrido. MABS, WABS" 
    services="biztalk-services" 
    documentationCenter="" 
    authors="MandiOhlinger" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="biztalk-services" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/18/2016" 
    ms.author="ccompy"/>


# <a name="create-and-manage-hybrid-connections"></a>Crear y administrar conexiones híbrido


## <a name="overview-of-the-steps"></a>Información general sobre los pasos
1. Crear una conexión híbrido escribiendo el **nombre de host** o **FQDN** del recurso local de su red privada.
2. Vincular sus aplicaciones web de Azure o Azure aplicaciones móviles para la conexión de híbrido.
3. Instalar al administrador de conexión híbrido en el recurso local y conectarse a la conexión híbrido específicos. El portal de Azure ofrece una experiencia de un solo clic para instalar y conectar.
4. Administrar conexiones híbrido y sus claves de conexión.

En este tema se enumeran estos pasos. 

> [AZURE.IMPORTANT] Es posible establecer un extremo de conexión híbrido en una dirección IP. Si usa una dirección IP, puede o no se puede llegar al recurso local, dependiendo de su cliente. La conexión híbrido depende del cliente que realiza una búsqueda DNS. En la mayoría de los casos, el __cliente__ es el código de la aplicación. Si el cliente no realiza una búsqueda DNS (no intenta resolver la dirección IP como si fuese un nombre de dominio (x.x.x.x)), y no se envía el tráfico a través de la conexión híbrido.
>
> Por ejemplo (pseudocódigo), defina **10.4.5.6** como host local:
> 
> **Funciona el siguiente escenario:**  
> `Application code -> GetHostByName("10.4.5.6") -> Resolves to 127.0.0.3 -> Connect("127.0.0.3") -> Hybrid Connection -> on-prem host`
> 
> **No funciona el siguiente escenario:**  
> `Application code -> Connect("10.4.5.6") -> ?? -> No route to host`


## <a name="CreateHybridConnection"></a>Crear una conexión híbrido

Una conexión de híbridos pueden crearse en el portal de Azure utilicen aplicaciones Web **o** servicios de BizTalk. 

**Crear conexiones de híbrido con Web Apps**, vea [Conectar aplicaciones Web de Azure a un recurso local](../app-service-web/web-sites-hybrid-connection-get-started.md). También puede instalar el híbrido conexión Manager (gestión RR.) desde la aplicación web, que es el método preferido. 

**Crear conexiones de híbrido en servicios de BizTalk**:

1. Inicie sesión en el [portal de clásico de Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. En el panel de navegación izquierdo, seleccione **Servicios de BizTalk** y, a continuación, seleccione su BizTalk Service. 

    Si no tiene un BizTalk Service existente, puede [crear un servicio de BizTalk](biztalk-provision-services.md).
3. Seleccione la pestaña **Conexiones híbrido** :  
![Pestaña de conexiones híbrido][HybridConnectionTab]

4. Seleccione **crear una conexión híbrido** o seleccione el botón **Agregar** en la barra de tareas. Escriba lo siguiente:

    (Propiedad) | Descripción
--- | ---
Nombre | El nombre de conexión híbrido debe ser único y no puede ser el mismo nombre que BizTalk Service. Puede introducir cualquier nombre pero sea específico con su finalidad. Algunos ejemplos:<br/><br/>Nóminas*SQL Server*<br/>SupplyList*SharepointServer*<br/>Clientes*OracleServer*
Nombre de host | Escriba el nombre de host completo, solo el nombre de host o la dirección IPv4 del recurso local. Algunos ejemplos:<br/><br/>mySQLServer<br/>*mySQLServer*. *Dominio*. .com de*empresa*Corporation.<br/>*myHTTPSharePointServer*<br/>*myHTTPSharePointServer*. .com de *empresa*<br/>10.100.10.10<br/><br/>Si usa la dirección IPv4, tenga en cuenta que el código de aplicación o el cliente no puede resolver la dirección IP. Vea la nota importante en la parte superior de este tema.
Puerto | Escriba el número de puerto del recurso local. Por ejemplo, si está usando aplicaciones de Web, escriba el puerto 80 o 443. Si está usando SQL Server, escriba el puerto 1433.

5. Seleccione la marca de verificación para completar la configuración. 

#### <a name="additional"></a>Adicionales

- Se pueden crear varias conexiones híbrido. Consulte la [servicios BizTalk: gráfico de ediciones](biztalk-editions-feature-chart.md) para el número de conexiones permitidas. 
- Se crea cada conexión híbrido con un par de cadenas de conexión: aplicación claves que claves de envío y local que ESCUCHAN. Cada pareja tiene un principal y clave secundaria. 


## <a name="LinkWebSite"></a>Vínculo a su aplicación de Azure de aplicación de servicio Web o una aplicación móvil

Para vincular una aplicación Web o aplicación móvil en la aplicación de servicio de Azure a una conexión de híbridos existente, seleccione **usar una conexión de híbridos existente** en el módulo de conexiones híbrido. Consulte [acceso a recursos mediante conexiones híbrido en el servicio de aplicación de Azure local](../app-service-web/web-sites-hybrid-connection-get-started.md).

## <a name="InstallHCM"></a>Instalar el Administrador de conexiones de híbrida local

Después de crear una conexión híbrido, instale al administrador de conexión híbrido en el recurso local. Puede descargarse desde sus aplicaciones web de Azure o desde el BizTalk Service. Pasos de servicios de BizTalk: 

1. Inicie sesión en el [portal de clásico de Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. En el panel de navegación izquierdo, seleccione **Servicios de BizTalk** y, a continuación, seleccione su BizTalk Service. 
3. Seleccione la pestaña **Conexiones híbrido** :  
![Pestaña de conexiones híbrido][HybridConnectionTab]
4. En la barra de tareas, seleccione **El programa de instalación local**:  
![Instalación local][HCOnPremSetup]
5. Seleccione **instalar y configurar** para ejecutar o descargar el Administrador de conexión híbrido en el sistema local. 
6. Seleccione la marca de verificación para iniciar la instalación. 

<!--
You can also download the Hybrid Connection Manager MSI file and copy the file to your on-premises resource. Specific steps:

1. Copy the on-premises primary Connection String. See [Manage Hybrid Connections](#ManageHybridConnection) in this topic for the specific steps.
2. Download the Hybrid Connection Manager MSI file. 
3. On the on-premises resource, install the Hybrid Connection Manager from the MSI file. 
4. Using Windows PowerShell, type: 
> Add-HybridConnection -ConnectionString “*Your On-Premises Connection String that you copied*” 
--> 

#### <a name="additional"></a>Adicionales
- Administrador de conexiones de híbrido se puede instalar en los siguientes sistemas operativos:

    - Windows Server 2008 R2 (.NET Framework 4.5 + y Windows Management Framework 4.0 + requerido)
    - Windows Server 2012 (Windows Management Framework 4.0 + requerido)
    - Windows Server 2012 R2


- Después de instalar al administrador de conexión híbrido, ocurre lo siguiente: 

    - La conexión de híbrido hospedado en Azure automáticamente está configurada para usar la cadena de conexión de aplicación principal. 
    - El recurso local se configura automáticamente para usar la cadena de conexión local principal.

- El Administrador de conexión híbrido debe utilizar una cadena de conexión válida en local para la autorización. Las aplicaciones Web de Azure o aplicaciones Mobile debe utilizar una cadena de conexión de la aplicación válido para autorización.
- Puede escalar híbrido conexiones instalando otra instancia del Administrador de conexión de híbrido en otro servidor. Configurar la escucha local para usar la misma dirección como primer escucha local. En esta situación, el tráfico es distribuidos aleatoriamente (turnos) entre los agentes de escucha activa local. 


## <a name="ManageHybridConnection"></a>Administrar conexiones híbrido
Para administrar las conexiones híbrido, puede:

- Usar el portal de Azure y vaya a su BizTalk Service. 
- Use [las API de REST](http://msdn.microsoft.com/library/azure/dn232347.aspx).

#### <a name="copyregenerate-the-hybrid-connection-strings"></a>Copiar o volver a generar las cadenas de conexión híbrido

1. Inicie sesión en el [portal de clásico de Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. En el panel de navegación izquierdo, seleccione **Servicios de BizTalk** y, a continuación, seleccione su BizTalk Service. 
3. Seleccione la pestaña **Conexiones híbrido** :  
![Pestaña de conexiones híbrido][HybridConnectionTab]
4. Seleccione la conexión híbrido. En la barra de tareas, seleccione **Administrar conexión**:  
![Administrar las opciones][HCManageConnection]

    **Administrar conexión** enumera las cadenas de conexión de la aplicación y local. Puede copiar las cadenas de conexión o regenerar la clave de acceso utilizados en la cadena de conexión. 

    **Si selecciona regenerar**, la tecla de acceso compartido que se utilizan dentro de la cadena de conexión cambia. Haga lo siguiente:
    - En el portal de clásico Azure, seleccione **Claves de sincronización** en la aplicación de Azure.
    - Vuelva a ejecutar el **Programa de instalación local**. Cuando vuelva a ejecutar el programa de instalación local, el recurso local se configura automáticamente para usar la cadena de conexión principal actualizada.


#### <a name="use-group-policy-to-control-the-on-premises-resources-used-by-a-hybrid-connection"></a>Usar Directiva de grupo para controlar los recursos locales usados por una conexión híbrido

1. Descargar las [plantillas administrativas del Administrador de conexiones de híbrido](http://www.microsoft.com/download/details.aspx?id=42963).
2. Extraer los archivos.
3. En el equipo que modifica la directiva de grupo, haga lo siguiente:  

    - Copiar la. Archivos ADMX a la carpeta *%WINROOT%\PolicyDefinitions* .
    - Copiar la. Archivos ADML a la carpeta *%WINROOT%\PolicyDefinitions\en-us* .

Una vez copiado, puede usar el Editor de directivas de grupo para cambiar la directiva.




## <a name="next"></a>Siguiente

[Conectar aplicaciones Web Azure a un recurso local](../app-service-web/web-sites-hybrid-connection-get-started.md)  
[Conectarse a la implementación local de SQL Server desde aplicaciones Web de Azure](../app-service-web/web-sites-hybrid-connection-connect-on-premises-sql-server.md)   
[Introducción a las conexiones híbrido](integration-hybrid-connection-overview.md)


## <a name="see-also"></a>Vea también

[API de REST de administración de servicios de BizTalk en Microsoft Azure](http://msdn.microsoft.com/library/azure/dn232347.aspx)  
[Servicios de BizTalk: Gráfico de ediciones](biztalk-editions-feature-chart.md)  
[Crear un BizTalk Service con Azure portal clásica](biztalk-provision-services.md)  
[Servicios de BizTalk: Fichas de paneles, el Monitor y la escala](biztalk-dashboard-monitor-scale-tabs.md)


[HybridConnectionTab]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionTab.png
[HCOnPremSetup]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionOnPremSetup.png
[HCManageConnection]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionManageConn.png 

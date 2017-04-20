<properties
    pageTitle="Instalación de agente de mantenimiento de conexión de AD Azure | Microsoft Azure"
    description="Esta es la página de estado de conexión de Azure AD que describa la instalación del agente de AD FS y sincronización."
    services="active-directory"
    documentationCenter=""
    authors="karavar"
    manager="samueld"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="vakarand"/>


# <a name="azure-ad-connect-health-agent-installation"></a>Azure AD Connect instalación de agente de estado

Este documento le guiará a través de instalar y configurar a agentes de mantenimiento de AD conectar Azure. Puede descargar a los agentes desde [aquí](active-directory-aadconnect-health.md#download-and-install-azure-ad-connect-health-agent).

##  <a name="requirements"></a>Requisitos
En la tabla siguiente es una lista de los requisitos para usar el estado de conexión de Azure AD.

| Requisito | Descripción|
| ----------- | ---------- |
|Premium de Azure AD| Estado de conexión de Azure AD es una característica de Azure AD Premium y requiere Azure AD Premium. </br></br>Para obtener más información, consulte [Introducción a Azure AD Premium](active-directory-get-started-premium.md) </br>Para iniciar una prueba gratuita de 30 días, consulte [iniciar una prueba.](https://azure.microsoft.com/trial/get-started-active-directory/)|
|Debe ser administrador global de su Azure AD empezar a trabajar con su estado de conexión de Azure AD|De forma predeterminada, solo los administradores globales pueden instalar y configurar los agentes de estado para empezar, acceder al portal y realizar operaciones de estado de conexión de Azure AD. Para obtener más información, vea [administrar el directorio de Azure AD](active-directory-administer.md). <br><br> Con Control de acceso en función de funciones puede permitir el acceso a Azure AD conectar estado a otros usuarios de su organización. Para obtener más información, vea [rol de Control de acceso para el estado de conexión de Azure AD.](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control) </br></br>**Importante:** La cuenta utilizada al instalar a los agentes debe ser una cuenta profesional o educativa. No puede ser una cuenta de Microsoft. Para obtener más información, consulte [registrarse para Azure como una organización](sign-up-organization.md)
|Está instalado el agente de mantenimiento conectar de AD Azure en cada servidor de destino| Estado de conexión de Azure AD requiere la instalación de un agente en servidores de destino para proporcionar los datos que se visualiza en el portal. </br></br>Por ejemplo, para obtener datos de la infraestructura de AD FS en local, el agente debe instalarse en los servidores de AD FS, Proxy AD FS y Proxy de la aplicación Web. Asimismo, para obtener los datos en sus instalaciones infraestructura de AD DS, el agente debe estar instalado en los controladores de dominio. </br></br>**Importante:** La cuenta utilizada al instalar a los agentes debe ser una cuenta profesional o educativa. No puede ser una cuenta de Microsoft. Para obtener más información, consulte [registrarse para Azure como una organización](sign-up-organization.md)|
|Conectividad saliente de los extremos del servicio de Azure|Durante la instalación y tiempo de ejecución, el agente requiere conectividad a los extremos de servicio de estado de conexión de Azure AD. Si está bloqueado conectividad saliente, asegúrese de que los extremos siguientes se agregan a la lista permitida: </br></br><li>& #42;. BLOB.Core.Windows.NET </li><li>& #42;. Queue.Core.Windows.NET</li><li>adhsprodwus.ServiceBus.Windows.NET - puerto: 5671 </li><li>https://Management.Azure.com </li><li>https://s1.adhybridhealth.Azure.com/</li><li>https://policykeyservice.DC.AD.msft.NET/</li><li>https://Login.Windows.NET</li><li>https://Login.microsoftonline.com</li><li>https://Secure.aadcdn.microsoftonline-p.com</li> |
|Puertos del firewall en el servidor que ejecuta al agente.| El agente requiere los siguientes puertos del firewall que se abra en orden para el agente para comunicarse con los extremos de servicio de estado de Azure AD.</br></br><li>TCP/UDP puerto 443</li><li>Puerto TCP/UDP 5671</li>
|Permitir que los siguientes sitios Web si está habilitada la seguridad mejorada de Internet Explorer| Si está habilitada la seguridad mejorada de Internet Explorer, deben poder los siguientes sitios Web en el servidor que se va a tiene instalado el agente.</br></br><li>https://Login.microsoftonline.com</li><li>https://Secure.aadcdn.microsoftonline-p.com</li><li>https://Login.Windows.NET</li><li>El servidor de federación para su organización Azure Active Directory de confianza. Por ejemplo: https://sts.contoso.com</li>




## <a name="installing-the-azure-ad-connect-health-agent-for-ad-fs"></a>Instalación de Azure AD conectar el agente de mantenimiento de AD FS
Para iniciar la instalación del agente, haga doble clic en el archivo .exe que ha descargado. En la primera pantalla, haga clic en instalar.

![Comprobar Azure AD Connect estado](./media/active-directory-aadconnect-health-requirements/install1.png)

Una vez finalizada la instalación, haga clic en configurar ahora.

![Comprobar Azure AD Connect estado](./media/active-directory-aadconnect-health-requirements/install2.png)

Se inicia un símbolo del sistema, seguido por algunos PowerShell que ejecuta registrar AzureADConnectHealthADFSAgent. Cuando se le solicite para iniciar sesión en Azure, continuar e inicie sesión.

![Comprobar Azure AD Connect estado](./media/active-directory-aadconnect-health-requirements/install3.png)


Después de iniciar sesión, seguirá PowerShell. Cuando haya terminado, puede cerrar PowerShell y la configuración se completa.

En este momento, se deben iniciar los servicios permitir automáticamente el agente de supervisión y recopilación de datos. Si no se cumplen todos los requisitos previos descritos en las secciones anteriores, advertencias aparecen en la ventana de PowerShell. Asegúrese de completar los [requisitos](active-directory-aadconnect-health-agent-install.md#requirements) antes de instalar al agente. La siguiente captura de pantalla es un ejemplo de estos errores.

![Comprobar Azure AD Connect estado](./media/active-directory-aadconnect-health-requirements/install4.png)

Para comprobar que se ha instalado el agente, busque los siguientes servicios en el servidor. Si ha finalizado la configuración, ya deben ejecutarse. En caso contrario, se detienen hasta que se complete la configuración.

- Azure AD Connect estado AD FS diagnósticos servicio
- Azure AD Connect estado AD FS perspectivas servicio
- Azure AD Connect FS salud AD servicio de supervisión

![Comprobar Azure AD Connect estado](./media/active-directory-aadconnect-health-requirements/install5.png)


### <a name="agent-installation-on-windows-server-2008-r2-servers"></a>Instalación del agente en servidores de Windows Server 2008 R2

Pasos para servidores de Windows Server 2008 R2:

1. Asegúrese de que el servidor está ejecutando a Service Pack 1 o superior.
1. Desactivar ESC de Internet Explorer para la instalación de agente:
1. Instalar Windows PowerShell 4.0 en cada uno de los servidores antes de instalar al agente de mantenimiento de AD. Para instalar Windows PowerShell 4.0:
 - Instale [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=40779) mediante el siguiente vínculo para descargar al instalador de sin conexión.
 - Instalar PowerShell ISE (de características de Windows)
 - Instalar el [Windows Management Framework 4.0.](https://www.microsoft.com/download/details.aspx?id=40855)
 - Instalar Internet Explorer 10 o posterior en el servidor. (Requiere el servicio de estado para autenticar, usando sus credenciales de administrador de Azure).
1. Para obtener más información sobre cómo instalar Windows PowerShell 4.0 en Windows Server 2008 R2, vea el artículo wiki [aquí](http://social.technet.microsoft.com/wiki/contents/articles/20623.step-by-step-upgrading-the-powershell-version-4-on-2008-r2.aspx).

### <a name="enable-auditing-for-ad-fs"></a>Habilitar la auditoría de AD FS

> [AZURE.NOTE] Esta sección solo se aplica a los servidores de federación de AD FS.

Fin de la característica de análisis de uso recopilar y analizar datos, el agente de mantenimiento de conexión de Azure AD necesita la información en los registros de auditoría de AD FS. Estos registros no están habilitados de forma predeterminada. Use los procedimientos siguientes para habilitar la auditoría de AD FS y buscar los registros de auditoría de AD FS en los servidores de AD FS.

#### <a name="to-enable-auditing-for-ad-fs-20"></a>Para habilitar la auditoría de AD FS 2.0

1. Haga clic en **Inicio**, seleccione **programas**, seleccione **Herramientas administrativas**y, a continuación, haga clic en **Directiva de seguridad Local**.
2. Vaya a la carpeta de **Administración de derechos de directivas de seguridad seguridad\Directivas** y, a continuación, haga doble clic en generar auditorías de seguridad.
3. En la pestaña **Configuración de seguridad Local** , compruebe que aparece la cuenta de AD FS 2.0 service. Si no está presente, haga clic en **Agregar usuario o grupo** y agregar a la lista y, a continuación, haga clic en **Aceptar**.
4. Para habilitar la auditoría, abra un símbolo del sistema con privilegios elevados y ejecute el siguiente comando:<code>auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable</code>
5. Cierre Directiva de seguridad Local y, a continuación, abra el complemento de administración. Para abrir el complemento de administración, haga clic en **Inicio**, seleccione **programas**, **Herramientas administrativas**y, a continuación, haga clic en AD FS 2.0 administración.
6. En el panel de acciones, haga clic en Editar propiedades de servicio de federación.
7. En el cuadro de diálogo **Propiedades del servicio de federación** , haga clic en la ficha **eventos** .
8. Seleccione las casillas de verificación **auditorías de éxito** y **auditorías de error** .
9. Haga clic en **Aceptar**.

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2012-r2"></a>Para habilitar la auditoría de AD FS en Windows Server 2012 R2

1. Abra **La directiva de seguridad Local** abriendo el **Administrador del servidor** en la pantalla de inicio o el administrador del servidor en la barra de tareas en el escritorio, haga clic en **Herramientas de locales, directiva de seguridad**.
2. Vaya a la carpeta de **La asignación de derechos de directivas de seguridad seguridad\Directivas** y, a continuación, haga doble clic en **Generar auditorías de seguridad**.
3. En la pestaña **Configuración de seguridad Local** , compruebe que aparece la cuenta de servicio de AD FS. Si no está presente, haga clic en **Agregar usuario o grupo** y agregar a la lista y, a continuación, haga clic en **Aceptar**.
4. Para habilitar la auditoría, abra un símbolo del sistema con privilegios elevados y ejecute el siguiente comando:<code>auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable.</code>
5. Cierre la **Directiva de seguridad Local**y, a continuación, abra el complemento de **Administración de AD FS** (en el administrador del servidor, haga clic en herramientas y, a continuación, seleccione Administración de AD FS).
6. En el panel de acciones, haga clic en **Editar propiedades de servicio de federación**.
7. En el cuadro de diálogo Propiedades del servicio de federación, haga clic en la ficha **eventos** .
8. Seleccione las casillas de verificación **auditorías de éxito y auditorías de error** y, a continuación, haga clic en **Aceptar**.






#### <a name="to-locate-the-ad-fs-audit-logs"></a>Para localizar la auditoría de AD FS registros


1. Abra el **Visor de eventos**.
2. Vaya a registros de Windows y seleccione **seguridad**.
3. A la derecha, haga clic en **Registros de filtro actual**.
4. En el origen de evento, seleccione **Auditoría de AD FS**.

![Registros de auditoría de AD FS](./media/active-directory-aadconnect-health-requirements/adfsaudit.png)

> [AZURE.WARNING] Si existe una directiva de grupo que se deshabilitar auditoría de AD FS, agente de mantenimiento de AD conectar Azure es no se puede recopilar información. Asegúrese de que no tiene una directiva de grupo que se puede deshabilitar la auditoría.

[//]: # (Start of Agent Proxy Configuration Section)

## <a name="installing-the-azure-ad-connect-health-agent-for-sync"></a>Instalar al agente de mantenimiento de conexión de Azure AD para sincronizar
El agente de mantenimiento de conexión de Azure AD para sincronizar se instala automáticamente en la última versión de Azure AD Connect. Para usar Azure AD Connect para sincronizar, debe descargar la versión más reciente de Azure AD Connect e instalarlo. Puede descargar la versión más reciente [aquí](http://www.microsoft.com/download/details.aspx?id=47594).

Para comprobar que se ha instalado el agente, busque los siguientes servicios en el servidor. Si ha finalizado la configuración, ya deben ejecutarse. En caso contrario, se detienen hasta que se complete la configuración.

- Azure AD Connect servicio de perspectivas de sincronización de estado
- Azure AD Connect supervisar el servicio de sincronización de estado

![Comprobar Azure AD Connect estado de sincronización](./media/active-directory-aadconnect-health-sync/services.png)

> [AZURE.NOTE] Recuerde que el uso de estado de conexión de Azure AD requiere Azure AD Premium. Si no tiene Azure AD Premium, no es posible completar la configuración en el portal de Azure. Para obtener más información, consulte la [página de requisitos](active-directory-aadconnect-health-agent-install.md#requirements).


## <a name="manual-azure-ad-connect-health-for-sync-registration"></a>Manual de Azure AD conectar estado para el registro de sincronización
Si el estado de conexión AD Azure para el registro de agente de sincronización no funciona después de instalar correctamente Azure AD Connect, puede usar el siguiente comando PowerShell para registrar al agente de forma manual.

>[AZURE.IMPORTANT] Mediante el siguiente comando PowerShell sólo es obligatorio si se produce un error en el registro de agente después de instalar Azure AD Connect.

El siguiente PowerShell comando es necesaria solo cuando el registro de agente de salud falla incluso después de una correcta instalación y configuración de Azure AD Connect. Los servicios de estado de conexión de Azure AD se iniciarán el agente se ha registrado correctamente.

Puede registrar al agente de mantenimiento de conexión de Azure AD para sincronizar con el siguiente comando PowerShell:

`Register-AzureADConnectHealthSyncAgent -AttributeFiltering $false -StagingMode $false`

El comando toma siguientes parámetros:

- AttributeFiltering: $true (predeterminado): si Azure AD Connect no está sincronizando el atributo predeterminado establecido y se ha personalizado para usar un conjunto de atributos filtrados. $false en caso contrario.
- StagingMode: $false (predeterminado): si el servidor de Azure AD Connect no está en modo de $true de ensayo si el servidor está configurado para que esté en modo de ensayo.

Cuando se le solicite para la autenticación debe usar la misma cuenta de administrador global (como admin@domain.onmicrosoft.com) que se usó para configurar Azure AD Connect.

## <a name="installing-the-azure-ad-connect-health-agent-for-ad-ds"></a>Instalación de Azure AD conectar el agente de mantenimiento de AD DS
Para iniciar la instalación del agente, haga doble clic en el archivo .exe que ha descargado. En la primera pantalla, haga clic en instalar.

![Comprobar Azure AD Connect estado](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install1.png)

Una vez finalizada la instalación, haga clic en configurar ahora.

![Comprobar Azure AD Connect estado](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install2.png)

Se inicia un símbolo del sistema, seguido por algunos PowerShell que ejecuta registrar AzureADConnectHealthADDSAgent. Cuando se le solicite para iniciar sesión en Azure, continuar e inicie sesión.

![Comprobar Azure AD Connect estado](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install3.png)

Después de iniciar sesión, seguirá PowerShell. Cuando haya terminado, puede cerrar PowerShell y la configuración se completa.

En este momento, se deben iniciar los servicios permitir automáticamente el agente de supervisión y recopilación de datos. Si no se cumplen todos los requisitos previos descritos en las secciones anteriores, advertencias aparecen en la ventana de PowerShell. Asegúrese de completar los [requisitos](active-directory-aadconnect-health-agent-install.md#requirements) antes de instalar al agente. La siguiente captura de pantalla es un ejemplo de estos errores.

![Comprobar Azure AD Connect estado para AD DS](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install4.png)

Para comprobar que se ha instalado el agente, busque los servicios siguientes en el controlador de dominio.

- Azure AD Connect estado AD DS perspectivas servicio
- Azure AD Connect DS salud AD servicio de supervisión

Si ha finalizado la configuración, estos servicios deben ya se está ejecutando. En caso contrario, se detienen hasta que se complete la configuración.

![Comprobar Azure AD Connect estado](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install5.png)

## <a name="installing-the-azure-ad-connect-health-agent-for-ad-ds-on-server-core"></a>Instalación de Azure AD conectar el agente de mantenimiento de AD DS en Server Core.
Después de instalar el archivo .exe, puede completar el proceso de registro mediante el siguiente comando PowerShell:

`Register-AzureADConnectHealthADDSAgent -Credential $cred`

## <a name="configure-azure-ad-connect-health-agents-to-use-http-proxy"></a>Configurar Azure AD agentes conectar de estado para usar a HTTP Proxy
Puede configurar Azure AD agentes conectar de estado para que funcione con un servidor HTTP Proxy.

>[AZURE.NOTE]
- Usar "Netsh WinHttp establecer ProxyServerAddress" no se admite como el agente utiliza System.Net para hacer solicitudes web en lugar de los servicios HTTP de Microsoft Windows.
- La dirección del Http Proxy configurada se usa para los mensajes cifrados de Https paso a través.
- No se admiten servidores proxy autenticados (con HTTPBasic).

### <a name="change-health-agent-proxy-configuration"></a>Cambiar configuración de Proxy de agente de estado
Tiene las siguientes opciones para configurar Azure AD agente conectar de estado para usar a un servidor HTTP Proxy.

>[AZURE.NOTE]Deben reiniciar todos los servicios de Azure AD conectar agente de mantenimiento, en orden actualizar la configuración de proxy. Ejecute el siguiente comando:<br>
Restart-Service AdHealth *

#### <a name="import-existing-proxy-settings"></a>Importar configuración de proxy existente

##### <a name="import-from-internet-explorer"></a>Importar desde Internet Explorer
Puede importar la configuración de proxy HTTP de Internet Explorer, para ser usada por agentes de mantenimiento de AD conectar Azure. En cada uno de los servidores que ejecutan al agente de mantenimiento, ejecute el siguiente comando PowerShell:

    Set-AzureAdConnectHealthProxySettings -ImportFromInternetSettings

##### <a name="import-from-winhttp"></a>Importar desde WinHTTP
Puede importar la configuración de proxy WinHTTP, para ser usada por agentes de mantenimiento de AD conectar Azure. En cada uno de los servidores que ejecutan al agente de mantenimiento, ejecute el siguiente comando PowerShell:

    Set-AzureAdConnectHealthProxySettings -ImportFromWinHttp

#### <a name="specify-proxy-addresses-manually"></a>Especificar las direcciones del Proxy manualmente
Puede especificar manualmente un servidor proxy en cada uno de los servidores que ejecutan al agente de mantenimiento, ejecute el siguiente comando PowerShell:

    Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress address:port

Ejemplo: *myproxyserver AzureAdConnectHealthProxySettings Set - HttpsProxyAddress: 443*

- "dirección" puede ser un nombre de servidor puede resolver de DNS o una dirección de IPv4
- puede omitir "puerto". Si se omite, a continuación, se elige 443 como puerto predeterminado.

#### <a name="clear-existing-proxy-configuration"></a>Borrar configuración de proxy existente
Puede borrar la configuración de proxy existente, ejecute el siguiente comando:

    Set-AzureAdConnectHealthProxySettings -NoProxy


### <a name="read-current-proxy-settings"></a>Leer la configuración de proxy actual
Puede leer la configuración de proxy actualmente configurado, ejecute el siguiente comando:

    Get-AzureAdConnectHealthProxySettings


## <a name="test-connectivity-to-azure-ad-connect-health-service"></a>Probar la conectividad a Azure AD conectar el servicio de estado
Es posible que pueden surgir problemas que provocan el agente de mantenimiento de conexión de Azure AD pierda la conectividad con el servicio de estado de conexión de Azure AD. Estos son problemas de red, problemas de permisos o varias otras razones.

Si el agente no se ha podido enviar datos al servicio de estado de conexión de Azure AD durante más de dos horas, se indica con el siguiente mensaje en el portal: "datos de servicio de salud no están actualizados." Puede confirmar si el afectado Azure AD Connect agente de mantenimiento es poder cargar los datos en el servicio de estado de conexión de Azure AD ejecutando el siguiente comando PowerShell:

    Test-AzureADConnectHealthConnectivity -Role ADFS

El parámetro de la función actualmente tiene los siguientes valores:

- ADFS
- Sincronización
- AGREGA

Puede utilizar el indicador - ShowResults en el comando para ver detalles de los registros. Use el siguiente ejemplo:

    Test-AzureADConnectHealthConnectivity -Role Sync -ShowResult

>[AZURE.NOTE]Para usar la herramienta de conectividad, primero debe completar el registro de agente. Si no se puede completar el registro de agente, asegúrese de que cumple los [requisitos de](active-directory-aadconnect-health-agent-install.md#requirements) estado de conexión de Azure AD. Esta prueba de conectividad se realiza de forma predeterminada durante el registro de agente.



## <a name="related-links"></a>Vínculos relacionados

* [Azure AD Connect estado](active-directory-aadconnect-health.md)
* [Azure AD Connect operaciones de mantenimiento](active-directory-aadconnect-health-operations.md)
* [Conecte mediante Azure AD estado con AD FS](active-directory-aadconnect-health-adfs.md)
* [Usando el estado de conexión de Azure AD para sincronizar](active-directory-aadconnect-health-sync.md)
* [Conecte mediante Azure AD estado con AD DS](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect salud preguntas más frecuentes](active-directory-aadconnect-health-faq.md)
* [Azure AD Connect historial de versiones de estado](active-directory-aadconnect-health-version-history.md)

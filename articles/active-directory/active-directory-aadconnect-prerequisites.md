<properties
   pageTitle="Azure AD Connect: Hardware y los requisitos previos | Microsoft Azure"
   description="Este tema describe los requisitos previos y los requisitos de hardware para Azure AD Connect"
   services="active-directory"
   documentationCenter=""
   authors="andkjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="10/12/2016"
   ms.author="billmath"/>

# <a name="prerequisites-for-azure-ad-connect"></a>Conectan los requisitos previos para Azure AD
Este tema describe los requisitos previos y los requisitos de hardware de Azure AD Connect.

## <a name="before-you-install-azure-ad-connect"></a>Antes de instalar Azure AD Connect
Antes de instalar Azure AD Connect, hay algunas cosas que necesita.

### <a name="azure-ad"></a>Azure AD
- Una suscripción de Azure o una [suscripción de prueba de Azure](https://azure.microsoft.com/pricing/free-trial/). Esto solo es necesario para acceder al portal de Azure y no para utilizar Azure AD Connect. Si está utilizando PowerShell u Office 365 no es necesario una suscripción de Azure usar Azure AD Connect. Si tiene una licencia de Office 365, también puede usar el portal de Office 365. Con una licencia de Office 365 pagada también puede acceder al portal de Azure desde el portal de Office 365.
    - También puede usar la funcionalidad de vista previa de Azure AD en el [portal de Azure](https://portal.azure.com). Este portal no requiere una licencia de Azure.
- [Agregar y verificar el dominio](active-directory-add-domain.md) que planea usar en Azure AD. Por ejemplo, si tiene previsto usar contoso.com para sus usuarios, a continuación, asegúrese de que se ha comprobado este dominio y no solo usa el dominio predeterminado de contoso.onmicrosoft.com.
- Permite a un inquilino de Azure AD objetos predeterminado 50k. Al comprobar el dominio, el límite aumenta a objetos de 300 k. Si necesita incluso más objetos en Azure AD necesita para abrir un caso de soporte técnico para el límite aumentar aún más. Si necesita más de 500 objetos k necesita una licencia, como Office 365, Azure AD básica, Azure AD Premium o serie de movilidad para empresas.

### <a name="prepare-your-on-premises-data"></a>Preparar los datos locales
- Revise [las características de sincronización opcional que puede habilitar en Azure AD](active-directory-aadconnectsyncservice-features.md) y evaluar qué características se debe habilitar.

### <a name="on-premises-servers-and-environment"></a>Entorno y servidores locales
- El AD versión y bosques funcional nivel de esquema debe ser Windows Server 2003 o posterior. Los controladores de dominio pueden ejecutar cualquier versión siempre y cuando se cumplen los requisitos de nivel de esquema y bosque.
- Si tiene previsto usar la característica **reescritura de contraseña** debe ser los controladores de dominio en Windows Server 2008 (con el Service Pack más reciente) o posterior. Si los controladores de dominio están en 2008 (pre-R2), a continuación, también debe aplicar la [revisión KB2386717](http://support.microsoft.com/kb/2386717).
- El controlador de dominio utilizado por Azure AD debe ser grabable. No es compatible para usar un RODC (controlador de dominio de solo lectura) y Azure AD Connect no sigue los redireccionamientos de escritura.
- Azure AD Connect no se puede instalar en Small Business Server o Windows Server Essentials. El servidor debe estar usando Windows Server estándar o mejor.
- El servidor de Azure AD Connect debe tener un gráfico completo instalado. No se admite para instalar en server core.
- Azure AD Connect debe estar instalado en Windows Server 2008 o posterior. Este servidor puede ser un controlador de dominio o un servidor miembro si usa la configuración de express. Si usa una configuración personalizada, el servidor también puede ser independiente y no tiene unirse a un dominio.
- Si instala Azure AD Connect en Windows Server 2008, asegúrese de aplicar las revisiones más recientes de Windows Update. La instalación no es posible iniciar con un servidor sin revisión.
- Si tiene previsto usar la característica **sincronización de contraseñas**, debe ser el servidor de Azure AD Connect en Windows Server 2008 R2 SP1 o posterior.
- El servidor de Azure AD Connect debe tener [.NET Framework 4.5.1](#component-prerequisites) o posterior y [Microsoft PowerShell 3.0](#component-prerequisites) o posterior instalado.
- Si se está implementando servicios de federación de Active Directory, los servidores donde está instalado AD FS o Proxy de aplicación Web deben ser Windows Server 2012 R2 o posterior. [Administración remota de Windows](#windows-remote-management) debe estar habilitado en estos servidores para la instalación remota.
- Si se va a implementar los servicios de federación de Active Directory, debe [Certificados SSL](#ssl-certificate-requirements).
- Si se está implementando servicios de federación de Active Directory, debe configurar [la resolución de nombres](#name-resolution-for-federation-servers).
- Azure AD Connect requiere una base de datos de SQL Server para almacenar los datos de identidad. De forma predeterminada se instala un SQL Server 2012 Express LocalDB (una versión ligera de SQL Server Express) y se crea la cuenta de servicio para el servicio en el equipo local. SQL Server Express tiene un límite de tamaño de 10GB que le permita administrar aproximadamente 100000 objetos. Si necesita administrar un mayor número de objetos de directorio, debe apunte al Asistente para instalación a otra instalación de SQL Server.
- Si usa un servidor SQL independiente, aplicarán estos requisitos:
    - Azure AD Connect es compatible con todas las versiones de Microsoft SQL Server de SQL Server 2008 (con Service Pack 4) para SQL Server 2014. Base de datos de SQL de Microsoft Azure está **incompatibles** como una base de datos.
    - Debe usar una intercalación SQL entre mayúsculas y minúsculas. Se identifican con un \_CI_ en su nombre. Es **no compatible** para utilizar una intercalación distingue mayúsculas de minúsculas, identificada por \_CS_ en su nombre.
    - Sólo puede tener un motor de sincronización por cada instancia de base de datos. Es **no admite** compartir la instancia de base de datos con sincronización FIM/MIM, DirSync o Azure AD Sync.

### <a name="accounts"></a>Cuentas
- Una cuenta de administrador Global de Azure AD para el directorio de Azure AD que desea integrar con. Debe ser una **cuenta de escuela u organización** y no puede ser una **cuenta de Microsoft**.
- Si utiliza configuración express o actualizar de sincronización de directorios, debe tener una cuenta de administrador de la empresa para su Active Directory local.
- [Cuentas de Active Directory](./connect/active-directory-aadconnect-accounts-permissions.md) si utiliza la ruta de instalación de una configuración personalizada.

### <a name="azure-ad-connect-server-configuration"></a>Configuración del servidor de Azure AD Connect
- Si los administradores globales tienen AMF habilitado, la dirección URL **https://secure.aadcdn.microsoftonline-p.com** debe estar en la lista de sitios de confianza. Deberá agregar este a la lista de sitios de confianza si no se agrega antes de que se le pide un desafío AMF. Puede usar Internet Explorer para agregar a sus sitios de confianza.

### <a name="connectivity"></a>Conectividad
- El servidor de Azure AD Connect necesita resolución DNS para intranet e internet. El servidor DNS debe poder resolver nombres tanto a su Active Directory local, así como los extremos de Azure AD.
- Si tiene los firewalls en la Intranet y necesita abrir puertos entre los servidores de Azure AD Connect y los controladores de dominio, consulte [Puertos de Azure AD Connect](active-directory-aadconnect-ports.md) para obtener más información.
- Si se debe abrir el servidor proxy o firewall límite que pueden tener acceso a las direcciones URL, a continuación, las direcciones URL documentados en [intervalos de direcciones IP y URL de Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) .
    - Si está utilizando la Cloud Microsoft en Alemania o la administración pública de Microsoft Azure en la nube, consulte [Consideraciones de instancias de servicio de sincronización de Azure AD Connect](active-directory-aadconnect-instances.md) para las direcciones URL.
- Azure AD Connect está utilizando TLS 1.0 para comunicarse con Azure AD de forma predeterminada. Puede cambiar a TLS 1.2 siguiendo los pasos de [Habilitar TLS 1.2 para Azure AD Connect](#enable-tls-12-for-azure-ad-connect).
- Si está utilizando a un proxy saliente para conectarse a Internet, debe agregar la siguiente configuración en el archivo **C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config** del Asistente para la instalación y la sincronización de Azure AD Connect para que pueda conectarse a Internet y Azure AD. Este texto debe introducirse en la parte inferior del archivo. En este código, &lt;PROXYADRESS&gt; representa el nombre de host o la dirección IP de proxy real.

```
    <system.net>
        <defaultProxy>
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

- Si su servidor proxy requiere autenticación, la [cuenta de servicio](./connect/active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-accounts) debe estar ubicado en el dominio y debe utilizar la ruta de instalación de configuración personalizada para especificar una [cuenta de servicio personalizada](./connect/active-directory-aadconnect-get-started-custom.md#install-required-components). También es necesario un cambio diferente en machine.config. Con este cambio en el Asistente para instalación de machine.config y motor de sincronización responden a solicitudes de autenticación del servidor proxy. En el Asistente para instalación de todas las páginas, excepto la página **Configurar** , firmada en las credenciales de usuario se usan. En la página **Configurar** al final del Asistente para la instalación, se cambia el contexto a la [cuenta de servicio](./connect/active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-accounts) creado por el usuario. La sección de machine.config debería tener este aspecto.

```
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

Consulte MSDN para obtener más información sobre el [proxy predeterminado del elemento](https://msdn.microsoft.com/library/kd3cf2ex.aspx).

Si tiene problemas con la conectividad, vea [solucionar problemas de conectividad](active-directory-aadconnect-troubleshoot-connectivity.md).

### <a name="other"></a>Otros
- Opcional: Una cuenta de usuario prueba para comprobar la sincronización.

## <a name="component-prerequisites"></a>Requisitos previos de componentes

### <a name="powershell-and-net-framework"></a>PowerShell y .net Framework
Azure AD Connect depende de Microsoft PowerShell y .NET Framework 4.5.1. Necesitará esta versión o una versión posterior instalada en su servidor. Según la versión de Windows Server, haga lo siguiente:

- Windows Server 2012R2
  - Microsoft PowerShell está instalado de forma predeterminada, se requiere ninguna acción.
  - .NET framework 4.5.1 y versiones posteriores se ofrecen a través de Windows Update. Asegúrese de que ha instalado las últimas actualizaciones de Windows Server en el Panel de Control.
- Windows Server 2008R2 y Windows Server 2012
  - La última versión de Microsoft PowerShell está disponible en **Windows Management Framework 4.0**, disponible en el [Centro de descarga de Microsoft](http://www.microsoft.com/downloads).
  - .NET framework 4.5.1 y versiones posteriores están disponibles en [El centro de descarga de Microsoft](http://www.microsoft.com/downloads).
- Windows Server 2008
  - La última versión admitida de PowerShell está disponible en **Windows Management Framework 3.0**, disponible en el [Centro de descarga de Microsoft](http://www.microsoft.com/downloads).
 - .NET framework 4.5.1 y versiones posteriores están disponibles en [El centro de descarga de Microsoft](http://www.microsoft.com/downloads).

### <a name="enable-tls-12-for-azure-ad-connect"></a>Habilitar TLS 1.2 para Azure AD Connect
Azure AD Connect usa TLS 1.0 predeterminada para cifrar la comunicación entre el servidor de motor de sincronización y Azure AD. Se puede cambiar esta configuración de aplicaciones .net usar TLS 1.2 de forma predeterminada en el servidor. Encontrará más información sobre TLS 1.2 en [2960358 aviso de seguridad de Microsoft](https://technet.microsoft.com/security/advisory/2960358).

1. No se puede habilitar TLS 1.2 en Windows Server 2008. Necesita Windows Server 2008R2 o posterior. Asegúrese de tiene la revisión de .net 4.5.1 instalada para su sistema operativo, consulte [2960358 aviso de seguridad de Microsoft](https://technet.microsoft.com/security/advisory/2960358). Es posible que tenga este o una versión posterior instalada en su servidor ya.
2. Si usa Windows Server 2008R2, asegúrese de que está habilitada 1.2 TLS. En el servidor de Windows Server 2012 y versiones posteriores, TLS 1.2 ya debe estar habilitado.
```
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
```
3. Para todos los sistemas operativos, establezca esta clave del registro y reinicie el servidor.
```
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319
"SchUseStrongCrypto"=dword:00000001
```
4. Si también desea habilitar 1.2 TLS entre el servidor de motor de sincronización y un SQL Server remoto, asegúrese de que tiene las versiones necesarias instaladas para [TLS 1.2 de soporte técnico para Microsoft SQL Server](https://support.microsoft.com/kb/3135244).

## <a name="prerequisites-for-federation-installation-and-configuration"></a>Requisitos previos de instalación de federación y configuración

### <a name="windows-remote-management"></a>Administración remota de Windows
Al utilizar Azure AD Connect para implementar los servicios de federación de Active Directory o el Proxy de la aplicación Web, compruebe los siguientes requisitos para garantizar la conectividad y configuración se realizará correctamente:

- Si el servidor de destino es la pertenencia a un dominio, asegúrese de que está habilitada la administradas remoto de Windows
    - En una ventana de comandos Psh: elevada, use el comando`Enable-PSRemoting –force`
- Si el servidor de destino es una máquina de dominio no combinada WAP, hay un par de requisitos adicionales
    - En el equipo de destino (WAP equipo):
         - Garantizar la winrm (administración remota de Windows o WS-Management) se está ejecutando el servicio a través de los servicios
         - En una ventana de comandos Psh: elevada, use el comando`Enable-PSRemoting –force`
    - En el equipo en el que se está ejecutando el Asistente (si el equipo de destino es combinado o confianza dominio):
        - En una ventana de comandos Psh: elevada, use el comando`Set-Item WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate`
        - En el administrador del servidor:
             - Agregar host DMZ WAP a grupo de máquinas (Administrador del servidor -> Administrar -> Agregar servidores... usar la ficha DNS)
             - Ficha de todos los servidores de administrador de servidores: haga clic con el botón secundario del mouse en el servidor WAP y elija administrar como..., escribir credenciales local (no en el dominio) para el equipo WAP
             - Para validar la conectividad Psh: remota, en la pestaña todos los servidores de administrador de servidores: haga clic con el botón secundario del mouse en el servidor WAP y elija Windows PowerShell.  Debe abrir una sesión remota de Psh: para garantizar que se pueden establecer sesiones de PowerShell remoto.

### <a name="ssl-certificate-requirements"></a>Requisitos de los certificados SSL
**Importante:** se recomienda usar el mismo certificado SSL a través de todos los nodos de la granja de AD FS, así como todos los servidores proxy de aplicación Web.

- El certificado debe ser un X509 certificado.
- Puede usar un certificado autofirmado en servidores de federación en un entorno de prueba. Sin embargo, para un entorno de producción, se recomienda obtener el certificado de una CA pública.
    - Si usa un certificado que no es de confianza públicamente, asegúrese de que el certificado instalado en cada servidor Proxy de la aplicación Web es de confianza en el servidor local y en todos los servidores de federación
- La identidad del certificado debe coincidir con el nombre de servicio de federación (por ejemplo, sts.contoso.com).
    - La identidad es una extensión de nombre alternativo (SAN) de asunto de tipo dNSName o, si no hay ninguna entrada de SAN, el nombre del asunto especificado como un nombre común.  
    - Múltiples entradas de SAN pueden estar presentes en el certificado, siempre una de ellas coincide con el nombre de servicio de federación.
    - Si tiene previsto usar unirse al lugar de trabajo, un SAN adicional se requiere con el valor **enterpriseregistration.** seguido por el sufijo de nombre Principal de usuario (UPN) de su organización, por ejemplo, **enterpriseregistration.contoso.com**.
- No se admiten certificados basados en claves de CryptoAPI siguientes generación (CNG) y proveedores de almacenamiento de claves. Esto significa que debe utilizar un certificado basado en un CSP (proveedor de servicios de cifrado) y no un KSP (proveedor de servicios de almacenamiento de claves).
- Los certificados comodín son compatibles.

### <a name="name-resolution-for-federation-servers"></a>Resolución de nombres de los servidores de federación
- Configurar los registros DNS para el nombre de servicio de federación de AD FS (por ejemplo, sts.contoso.com) para la intranet (servidor DNS interno) y la extranet (DNS público mediante el registrador de dominios). Para la intranet DNS asegurarse de que utilice un registro registros y no los registros CNAME. Esto es necesario para la autenticación de windows para que funcione correctamente desde su equipo combinados de dominio.
- Si va a implementar más de un servidor de AD FS o servidor Proxy de la aplicación Web, asegúrese de que ha configurado el equilibrador de carga y que los registros DNS para el nombre de servicio de federación de AD FS (por ejemplo sts.contoso.com) apunten al equilibrador de carga.
- Autenticación de windows integrada para que trabaje para las aplicaciones del explorador utilizando Internet Explorer en la intranet, asegúrese de que el nombre de servicio de federación de AD FS (por ejemplo, sts.contoso.com) se agrega a la zona de intranet en Internet Explorer. Esto puede controla mediante la directiva de grupo e implementado en todos los equipos de dominio unido.

## <a name="azure-ad-connect-supporting-components"></a>Compatibilidad con componentes de Azure AD Connect
A continuación se muestra una lista de componentes de Azure AD Connect se instala en el servidor donde está instalado Azure AD Connect. Esta lista es para una instalación Express básica.  Si elige usar un servidor SQL diferente en la página de servicios de sincronización de instalación, SQL Express LocalDB no está instalado localmente.

- Azure AD Connect estado
- Asistente Microsoft Online Services inicio de sesión para profesionales de TI (instalado pero no dependencia)
- Utilidades de línea de comandos de Microsoft SQL Server 2012
- Microsoft SQL Server 2012 Express LocalDB
- Microsoft SQL Server 2012 Native Client
- Paquete de Microsoft Visual C++ 2013 redistribución

## <a name="hardware-requirements-for-azure-ad-connect"></a>Requisitos de hardware de Azure AD Connect
La siguiente tabla muestra los requisitos mínimos para el equipo de sincronización de Azure AD Connect.

| Número de objetos de Active Directory | CPU | Memoria | Tamaño de la unidad de disco duro |
| ------------------------------------- | --- | ------ | --------------- |
| Menos de 10.000 | 1,6 GHz | 4 GB | 70 GB |
| 10.000 – 50.000 | 1,6 GHz | 4 GB | 70 GB |
| 50.000-100,000 | 1,6 GHz | 16 GB | 100 GB |
| Para 100000 o más objetos se requiere la versión completa de SQL Server|  |  |  |
| 100,000 – 300.000 | 1,6 GHz | 32 GB | 300 GB |
| 300.000 – 600.000 | 1,6 GHz | 32 GB | 450 GB |
| Más de 600.000 | 1,6 GHz | 32 GB | 500 GB |

Los requisitos mínimos para equipos con AD FS o servidores de aplicaciones Web es la siguiente:

- CPU: Doble núcleo 1,6 GHz o superior
- MEMORIA: 2GB o superior
- Azure VM: Configuración A2 o superior

## <a name="next-steps"></a>Pasos siguientes
Más información acerca de cómo [integrar las identidades locales con Azure Active Directory](active-directory-aadconnect.md).

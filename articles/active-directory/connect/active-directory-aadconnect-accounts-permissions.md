<properties
   pageTitle="Azure AD Connect: Cuentas y permisos | Microsoft Azure"
   description="Este tema describe las cuentas utiliza y crea y los permisos necesarios."
   services="active-directory"
   documentationCenter=""
   authors="billmath"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"  
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="10/04/2016"
   ms.author="billmath"/>


# <a name="azure-ad-connect-accounts-and-permissions"></a>Azure AD Connect: Cuentas y permisos
El Asistente para instalación de Azure AD Connect ofrece dos formas diferentes:

- En configuración rápida, el asistente requiere más privilegios para que la configuración puede configurar con facilidad, sin necesidad de crear o configurar permisos por separado.

- En la configuración personalizada, el asistente le ofrece más opciones y opciones, pero hay algunas situaciones en las que tenga que asegurarse de que tiene los permisos correctos.

## <a name="related-documentation"></a>Documentación relacionada
Si no leído la documentación sobre la [integración de las identidades locales con Azure Active Directory](../active-directory-aadconnect.md), la tabla siguiente proporciona vínculos a temas relacionados.

Tema |  
--------- | ---------
Instalar usando la configuración de Express | [Instalación Express de Azure AD Connect](active-directory-aadconnect-get-started-express.md)
Instalar con la configuración personalizada | [Instalación personalizada de Azure AD Connect](active-directory-aadconnect-get-started-custom.md)
Actualización de sincronización de directorios | [Actualizar desde la herramienta de sincronización de Azure AD (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md)


## <a name="express-settings-installation"></a>Instalación de configuración rápida
En la configuración de Express, el Asistente para instalación solicita las credenciales de administrador de empresa de AD DS para que se pueden configurar su Active Directory local con los permisos necesarios para Azure AD Connect. Si está actualizando desde DirSync, se usan las credenciales de administradores de empresa de AD DS para restablecer la contraseña para la cuenta de la sincronización de directorios. También necesitará credenciales de administrador Global de Azure AD.

Página del Asistente  | Credenciales recopiladas | Permisos necesarios| Utilizado para
------------- | ------------- |------------- |-------------
N/A.|Usuario que ejecuta al Asistente para instalación| Administrador del servidor local| <li>Crea la cuenta local que se usa como la [cuenta de servicio del motor de sincronización](#azure-ad-connect-sync-service-account).
Conectarse a Azure AD| Credenciales de directorio de Azure AD | Función de administrador global en Azure AD | <li>Habilitar la sincronización en el directorio de Azure AD.</li>  <li>Creación de la [cuenta de Azure AD](#azure-ad-service-account) que se utiliza para operaciones de sincronización en curso en Azure AD.</li>
Conectar con AD DS | Credenciales de Active Directory local | Miembro del grupo de administradores de Enterprise (EA) en Active Directory| <li>Crea una [cuenta](#active-directory-account) de Active Directory y concede permisos a él. Esto crea la cuenta se utiliza para leer y escribir información de directorio durante la sincronización.</li>

### <a name="enterprise-admin-credentials"></a>Credenciales de administrador de empresa
Estas credenciales solo se utilizan durante la instalación y se usan una vez completada la instalación. Es administrador de empresa y no el Administrador de dominio, para asegurarse de que se pueden configurar los permisos de Active Directory en todos los dominios.

### <a name="global-admin-credentials"></a>Credenciales de administrador globales
Estas credenciales solo se utilizan durante la instalación y no se usan una vez completada la instalación. Se usa para crear la [cuenta de Azure AD](#azure-ad-service-account) utilizado para sincronizar cambios Azure AD. La cuenta también permite sincronización como característica de Azure AD.

### <a name="permissions-for-the-created-ad-ds-account-for-express-settings"></a>Permisos para creado AD DS de la cuenta para la configuración de express
La [cuenta](#active-directory-account) que se creó para lectura y escritura a AD DS tiene los permisos siguientes cuando crea express configuración:

Permisos | Utilizado para
---- | ----
<li>Replicar cambios de directorio</li><li>Directorio replicar cambios: todos | Sincronización de la contraseña
Lectura y escritura todas las propiedades de usuario | Importar y Exchange híbrido
Lectura y escritura iNetOrgPerson de todas las propiedades | Importar y Exchange híbrido
Grupo de todas las propiedades de lectura y escritura | Importar y Exchange híbrido
Póngase en contacto con todas las propiedades de lectura y escritura | Importar y Exchange híbrido
Restablecer la contraseña | Preparación para habilitar la reescritura de contraseña

## <a name="custom-settings-installation"></a>Instalación de una configuración personalizada
Cuando se usa una configuración personalizada, la cuenta utilizada para conectar con Active Directory debe crearse antes de la instalación. Los permisos que debe conceder a esta cuenta se pueden encontrar en [crear la cuenta de AD DS](#create-the-ad-ds-account).

Página del Asistente  | Credenciales recopiladas | Permisos necesarios| Utilizado para
------------- | ------------- |------------- |-------------
N/A. | Usuario que ejecuta al Asistente para instalación|<li>Administrador del servidor local</li><li>Si usa un servidor SQL completo, el usuario debe ser administrador del sistema (SA) en SQL</li>| De forma predeterminada, se crea la cuenta local que se usa como la [cuenta de servicio del motor de sincronización](#azure-ad-connect-sync-service-account). Cuando el administrador no especifica una cuenta, en particular, solo se crea la cuenta.
Instale los servicios de sincronización, opción de la cuenta de servicio | AD o las credenciales de cuenta de usuario local | Permisos de usuario, se conceden por el Asistente para instalación | Si el administrador especifica una cuenta, esta cuenta se usa como la cuenta de servicio del servicio de sincronización.
Conectarse a Azure AD | Credenciales de directorio de Azure AD| Función de administrador global en Azure AD| <li>Habilitar la sincronización en el directorio de Azure AD.</li>  <li>Creación de la [cuenta de Azure AD](#azure-ad-service-account) que se utiliza para operaciones de sincronización en curso en Azure AD.</li>
Conectar los directorios | Credenciales de Active Directory local para cada bosque que está conectado a Azure AD | Los permisos dependen de las características que habilita y se encuentra en la [cuenta de crear AD DS](#create-the-ad-ds-account) |Esta cuenta se usa para leer y escribir información de directorio durante la sincronización.
Servidores de AD FS | Para cada servidor en la lista, el asistente recopila credenciales cuando las credenciales de inicio de sesión del usuario que ejecuta al asistente no son suficientes para conectar | Administrador de dominio | Instalación y configuración del rol de servidor AD FS.
Servidores proxy de aplicación Web |Para cada servidor en la lista, el asistente recopila credenciales cuando las credenciales de inicio de sesión del usuario que ejecuta al asistente no son suficientes para conectar | Administrador local en el equipo de destino | Instalación y configuración de rol de servidor WAP.
Credenciales de confianza de proxy |Credenciales de confianza de servicios de federación (las credenciales del proxy usa inscribirse en un certificado de confianza de la FS |Cuenta de dominio que sea un administrador local del servidor AD FS | Inscripción inicial de certificado de confianza FS WAP.
Página de AD FS cuenta de servicio, "Usar una opción de cuenta de usuario de dominio" | Credenciales de cuenta de usuario de AD | Usuario del dominio | La cuenta de usuario de AD se proporcionan cuyas credenciales se usa como la cuenta de inicio de sesión del servicio de AD FS.

### <a name="create-the-ad-ds-account"></a>Crear la cuenta de AD DS
Cuando se instala Azure AD Connect, la cuenta que especifique en la página **conectarse los directorios** deben existir en Active Directory y tener los permisos que le ha concedido necesarios. El Asistente para instalación no comprueba los problemas y los permisos que se encuentran en solo durante la sincronización.

¿Qué permisos necesitan depende de las características opcionales habilitar. Si tiene varios dominios, deben conceder los permisos para todos los dominios del bosque. Si no habilita estas características, los permisos de **Usuario de dominio** predeterminados son suficientes.

Característica | Permisos
------ | ------
Sincronización de la contraseña | <li>Replicar cambios de directorio</li>  <li>Directorio replicar cambios: todos
Implementación híbrida de Exchange | Permisos de escritura a los atributos documentados en [reescritura híbrida de Exchange](../active-directory-aadconnectsync-attributes-synchronized.md#exchange-hybrid-writeback) para los usuarios, grupos y contactos.
Reescritura de contraseña | Permisos de escritura a los atributos documentados en [Introducción a la administración de contraseñas](../active-directory-passwords-getting-started.md#step-4-set-up-the-appropriate-active-directory-permissions) para los usuarios.
Reescritura de dispositivo | Permisos concedidos con un script de PowerShell como se describe en el [dispositivo reescritura](../active-directory-aadconnect-feature-device-writeback.md).
Reescritura de grupo | Leer, crear, actualizar y eliminar los objetos de grupo en la unidad organizativa donde se deben ubicados los grupos de distribución.

## <a name="upgrade"></a>Actualizar
Si actualiza desde una versión de Azure AD Connect a una nueva versión, necesita los permisos siguientes:

Principal | Permisos necesarios | Utilizado para
---- | ---- | ----
Usuario que ejecuta al Asistente para instalación | Administrador del servidor local | Actualizar los archivos binarios.
Usuario que ejecuta al Asistente para instalación | Miembro de ADSyncAdmins | Realizar cambios en las reglas de sincronización y otra configuración.
Usuario que ejecuta al Asistente para instalación | Si usa un servidor SQL completo: DBO (o similares) de la base de datos de motor de sincronización | Realice los cambios de nivel de base de datos, como actualizar tablas con las nuevas columnas.

## <a name="more-about-the-created-accounts"></a>Más información sobre las cuentas creadas

### <a name="active-directory-account"></a>Cuenta de Active Directory
Si usa la configuración de express, se crea una cuenta de Active Directory que se utiliza para la sincronización. La cuenta creada se encuentra en el dominio raíz en el contenedor usuarios y ha su nombre con el prefijo **MSOL_**. Se crea la cuenta con una contraseña larga compleja que no caduque. Si tiene una directiva de contraseñas en su dominio, realice sabe larga y podrán contraseñas complejas para esta cuenta.

![Cuenta de AD](./media/active-directory-aadconnect-accounts-permissions/adsyncserviceaccount.png)

### <a name="azure-ad-connect-sync-service-accounts"></a>Cuentas de servicio de sincronización de Azure AD Connect
Se crea una cuenta de servicio local mediante el Asistente para instalación (a menos que especifique la cuenta que desee usar en una configuración personalizada). La cuenta se encuentra el prefijo **AAD_** y se usa para el servicio de sincronización real para ejecutar como. Si instala Azure AD Connect en un controlador de dominio, se crea la cuenta del dominio. Si usa un servidor remoto que ejecuta SQL server o si usa un servidor proxy que requiere autenticación, la cuenta de servicio **AAD_** debe estar ubicada en el dominio.

![Cuenta de servicio de sincronización](./media/active-directory-aadconnect-accounts-permissions/syncserviceaccount.png)

Se crea la cuenta con una contraseña larga compleja que no caduque.

Esta cuenta se usa para almacenar las contraseñas de otras cuentas de forma segura. Estas otras contraseñas de cuentas se almacenan cifrados en la base de datos. Las claves privadas para las claves de cifrado están protegidas con el cifrado de clave secreta de servicios de cifrado con la API de protección de datos (DPAPI) de Windows. No debe restablecer la contraseña de la cuenta de servicio desde Windows destroy, a continuación, las claves de cifrado por motivos de seguridad.

Si usa un servidor SQL completa, la cuenta del servicio es DBO de la base de datos creado para el motor de sincronización. El servicio no funcionará correctamente con los permisos. También se crea un inicio de sesión SQL.

La cuenta también tiene permisos para archivos, claves de registro y otros objetos relacionados con el motor de sincronización.

### <a name="azure-ad-service-account"></a>Cuenta de servicio de Azure AD
Se crea una cuenta de Azure AD para su uso del servicio de sincronización. Esta cuenta puede identificarse por su nombre para mostrar.

![Cuenta de AD](./media/active-directory-aadconnect-accounts-permissions/aadsyncserviceaccount.png)

El nombre del servidor que se utiliza la cuenta en puede identificarse en la segunda parte del nombre de usuario. En la imagen, el nombre del servidor es FABRIKAMCON. Si tiene servidores de ensayo, cada servidor tiene su propia cuenta. Hay un límite de 10 cuentas de servicio de sincronización de Azure AD.

La cuenta de servicio se creó con una contraseña larga compleja que no caduca. Se le ha concedido una función especial **Cuentas de sincronización de directorios** que sólo tiene permisos para realizar tareas de sincronización de directorios. No se puede conceder esta función integrada especial fuera del Asistente de Azure AD Connect y el portal de Azure muestra esta cuenta con el rol de **usuario**.

![Cuenta de rol de AD](./media/active-directory-aadconnect-accounts-permissions/aadsyncserviceaccountrole.png)

## <a name="next-steps"></a>Pasos siguientes

Más información acerca de cómo [integrar las identidades locales con Azure Active Directory](../active-directory-aadconnect.md).

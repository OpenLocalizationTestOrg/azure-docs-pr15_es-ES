<properties
    pageTitle="Servicios de dominio de Azure Active Directory: Características | Microsoft Azure"
    description="Características de servicios de dominio de Azure Active Directory"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/07/2016"
    ms.author="maheshu"/>

# <a name="azure-ad-domain-services"></a>Servicios de dominio de Active Directory de Azure

## <a name="features"></a>Características
Las siguientes características están disponibles en los servicios de dominio de Active Directory de Azure administrados dominios.

- **Experiencia de implementación simple:** Puede habilitar servicios de dominio de Active Directory de Azure para su inquilino de Azure AD con unos pocos clics. Independientemente del inquilino de Azure AD es un inquilino de nube o sincronizado con el directorio local, el dominio administrado pueden aprovisionar rápidamente.

- **Soporte para combinación de dominio:** Puede fácilmente equipos de combinación de dominio en la red virtual Azure que está disponible en su dominio administrado. La experiencia de combinación de dominio en el cliente de Windows y Server sistemas operativos funciona sin problemas con dominios atendidas por los servicios de dominio de Active Directory de Azure. También puede usar la combinación de dominio automatizado herramientas contra esos dominios.

- **Instancia de un dominio por directorio de Azure AD:** Puede crear un dominio de Active Directory único para cada directorio de Azure AD.

- **Crear dominios con nombres personalizados:** Puede crear dominios con nombres personalizados (por ejemplo, ' contoso100.com') mediante servicios de dominio de Active Directory de Azure. Puede usar en nombres de dominio comprobado o no comprobados. Si lo desea, también puede crear un dominio con el sufijo del dominio integrado (es decir, ' *. onmicrosoft.com') ofrecidas por el directorio de Azure AD.

- **Integrado con Azure AD:** No necesita configurar o administrar la replicación de los servicios de dominio de Active Directory de Azure. Cuentas de usuario, pertenencias a grupos y las credenciales de usuario (contraseñas) desde el directorio de Azure AD están disponibles automáticamente en servicios de dominio de Active Directory de Azure. Nuevos usuarios, grupos o cambios en los atributos de su inquilino de Azure AD o su directorio local se sincronizan automáticamente a los servicios de dominio de Active Directory de Azure.

- **Autenticación Kerberos y NTLM:** Compatibilidad con autenticación Kerberos y NTLM, puede implementar aplicaciones que se basan en autenticación de Windows integrada.

- **Utilice sus credenciales y contraseñas corporativas:** Las contraseñas de los usuarios de su inquilino de Azure AD funcionan con los servicios de dominio de Active Directory de Azure. Usuarios pueden usar sus credenciales corporativas a los equipos de la combinación de dominio, inicie sesión en forma interactiva o a través de escritorio remoto y autenticarse en el dominio administrado.

- **Enlace LDAP & LDAP Obtenga soporte técnico:** Puede usar las aplicaciones que se basan en los enlaces LDAP para autenticar usuarios en dominios atendidas por los servicios de dominio de Active Directory de Azure. Además, las aplicaciones que usan las operaciones de lectura LDAP para consultar los atributos de equipo y de usuario desde el directorio también pueden trabajar con los servicios de dominio de Active Directory de Azure.

- **Secure LDAP (LDAPS):** Puede habilitar el acceso al directorio sobre LDAP seguro (LDAPS). Proteger el acceso LDAP está disponible en la red virtual de forma predeterminada. Sin embargo, opcionalmente, también puede habilitar acceso LDAP seguro por internet.

- **Directiva de grupo:** Puede utilizar cada de un solo GPO integrado para los usuarios y equipos de contenedores para exigir el cumplimiento obligatorio directivas de seguridad para cuentas de usuario y equipos de dominio.

- **Administrar DNS:** Los miembros del grupo 'AAD DC administradores' pueden administrar el DNS de su dominio administrado mediante herramientas familiares de administración de DNS, como el complemento MMC de administración de DNS.

- **Crear personalizado unidades organizativas (OU):** Los miembros del grupo 'AAD DC administradores' pueden crear unidades organizativas personalizadas en el dominio administrado. Estos usuarios se conceden privilegios administrativos completos sobre unidades organizativas personalizadas, por lo que pueden agregar o quitar cuentas de servicio, equipos, grupos, etc. dentro de estas OU personalizadas.

- **Disponible en varias áreas de Azure:** Consulte la página de [Servicios de Azure por región](https://azure.microsoft.com/regions/#services/) saber las regiones de Azure en la que los servicios de dominio de Azure AD está disponible.

- **Alta disponibilidad:** Servicios de dominio de Azure AD ofrece alta disponibilidad para su dominio. Esta característica le ofrece la garantía de mayor tiempo de actividad de servicio y resistencia a errores. Supervisión ofertas de estado integrado automatiza la corrección de errores de poner en marcha nuevas instancias para reemplazar instancias error y para proporcionar servicio continuo de su dominio.

- **Usar herramientas de administración familiares:** Puede usar las herramientas de administración de Active Directory de Windows Server habituales como el centro de administración de Active Directory o Active Directory PowerShell para administrar dominios administrados.

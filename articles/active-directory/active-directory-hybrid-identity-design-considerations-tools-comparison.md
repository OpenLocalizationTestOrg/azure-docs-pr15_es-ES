<properties
    pageTitle="Identidad híbrido: Comparación de las herramientas de integración de directorios | Microsoft Azure"
    description="Esta es la página proporciona una tabla completa que compara las diversas herramientas de integración de directorio que se pueden usar para la integración de directorios."
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/08/2016"
    ms.author="billmath"/>

# <a name="hybrid-identity-directory-integration-tools-comparison"></a>Comparación de herramientas de integración de directorios de identidad híbrido

En los años las herramientas de integración de directorios han crecido y desarrollado a partir.  Este documento es ayudar a proporcionar una vista consolidada de estas herramientas y una comparación de las características que están disponibles en cada uno.

<!-- The hardcoded link is a workaround for campaign ids not working in acom links-->

>[AZURE.NOTE] Azure AD Connect incorpora los componentes y la funcionalidad publicado previamente como Dirsync y sincronización de AAD. Estas herramientas ya no son que se lanzaron individualmente y, a continuación, se incluirán todas las futuras mejoras en actualizaciones de Azure AD Connect, para que siempre sepa dónde puede obtener la funcionalidad más reciente.
>
>Sincronización de directorios y sincronización de Azure AD están obsoletos. Puede encontrar más información en [aquí](active-directory-aadconnect-dirsync-deprecated.md).


Utilice la siguiente clave para cada una de las tablas.

● = Disponible ahora  
FN = versión futura  
PP = Public Preview  

## <a name="on-premises-to-cloud-synchronization"></a>En la nube de sincronización local

| Característica  | Conectar de Azure Active Directory  | Servicios de sincronización de Azure Active Directory (AAD Sync) | Herramienta de sincronización de Azure Active Directory (DirSync)| Administrador de identidad de Forefront 2010 R2 (FIM) |Administrador de identidad de Microsoft 2016 (MIM)|
| :-------- |:--------:|:--------:|:--------:|:--------:|:--------:
| Conectarse a único bosque de AD local | ● | ● | ● | ● |● |
| Conectarse a varios bosques de AD local |●  | ● |  | ● |● |
| Conectarse a varios Orgs de Exchange local | ● |  |  |  | |
| Conectar con el directorio LDAP local único | NG |  |  | ● |● |
| Conectarse a varios directorios LDAP local |NG  |  |  | ● |● |
| Conectarse a directorios LDAP local y AD local |NG  |  |  | ● |● |
| Conectarse a sistemas personalizados (es decir, SQL, Oracle, MySQL, etcetera) | NG |  |  | ● |● |
| Sincronizar atributos definido por el cliente (extensiones de directorio) | ● |  |  |  |  |
|Conectarse a local HR (es decir, SAP, Oracle e-Business, PeopleSoft)| NG |  |  | ● |● |
|Es compatible con reglas de sincronización de FIM y conectores para el aprovisionamiento de sistemas locales.|  |  |  | ● |● |

## <a name="cloud-to-on-premises-synchronization"></a>Nube de sincronización local

| Característica  | Conectar de Azure Active Directory  | Servicios de sincronización de Azure Active Directory | Herramienta de sincronización de Azure Active Directory (DirSync) | Administrador de identidad de Forefront 2010 R2 (FIM) |Administrador de identidad de Microsoft 2016 (MIM)|
| :-------- |:--------:|:--------:|:--------:|:--------:|:--------:
| Reescritura de dispositivos | ● |  | ● |  ||
| Reescritura de atributo (para la implementación híbrida de Exchange) | ● | ● | ● | ● |● |
| Reescritura de objetos de usuarios y grupos |  ●|  | |  ||
| Reescritura de contraseñas (de autoservicio restablecer la contraseña (SSPR) y cambiar la contraseña) |  ● | ● |  |  ||



## <a name="authentication-feature-support"></a>Compatibilidad de características de autenticación

| Característica  | Conectar de Azure Active Directory | Servicios de sincronización de Azure Active Directory | Herramienta de sincronización de Azure Active Directory (DirSync) | Administrador de identidad de Forefront 2010 R2 (FIM) |Administrador de identidad de Microsoft 2016 (MIM)|
| :-------- |:--------:|:--------:|:--------:|:--------:|:--------:
| Sincronización de la contraseña de un solo bosque de AD local | ● | ● | ● |  ||
| Sincronización de la contraseña para varios bosques de AD local |  ●| ● |  |  ||
| Inicio de sesión único con la federación | ● | ● | ● | ● |● |
| Reescritura de contraseñas (de cambio SSPR y la contraseña) |●  | ● |  |  ||



## <a name="set-up-and-installation"></a>Configuración e instalación

| Característica  | Conectar de Azure Active Directory  | Servicios de sincronización de Azure Active Directory | Herramienta de sincronización de Azure Active Directory (DirSync) | Administrador de identidad de Microsoft 2016 (MIM) |
| :-------- |:--------:|:--------:|:--------:|:--------:
| Admite la instalación en un controlador de dominio | ● | ● | ● |  |
| Es compatible con la instalación con SQL Express | ● | ● | ● |  |
| Fácil actualización de sincronización de directorios |● |  |  |  |
| Localización de administración UX para idiomas de Windows Server | ● | ● | ● |  |
|Localización de usuario final UX idiomas de Windows Server| |  |  |● |
| Soporte técnico para Windows Server 2008 y Windows Server 2008 R2 | ● para sincronizar, No para la federación| ● | ●  | ● |
| Soporte técnico para Windows Server 2012 y Windows Server 2012 R2 | ● | ● | ● | ● |

## <a name="filtering-and-configuration"></a>Filtrado y configuración

Característica  | Conectar de Azure Active Directory | Servicios de sincronización de Azure Active Directory | Herramienta de sincronización de Azure Active Directory (DirSync) | Administrador de identidad de Forefront 2010 R2 (FIM)| Administrador de identidad de Microsoft 2016 (MIM)
:-------- |:--------:|:--------:|:--------:|:--------:|:--------:|
Filtrar los datos de los dominios y unidades organizativas | ● | ● | ● | ●  | ●
Filtrar por valores de atributo de los objetos | ● | ● | ● | ●| ●
Permitir el conjunto mínimo de atributos sincroniza (MinSync) | ● | ● |  ||
Permitir que las plantillas de servicio diferente que se aplique para los flujos de atributo |●  | ● |  ||
Permitir quitar atributos de flujo de AD a Azure AD | ● | ● |  |  |
Permitir la personalización avanzada de flujos de atributos | ● | ● |  | ●  | ●

## <a name="next-steps"></a>Pasos siguientes
Más información acerca de cómo [integrar las identidades locales con Azure Active Directory](active-directory-aadconnect.md).

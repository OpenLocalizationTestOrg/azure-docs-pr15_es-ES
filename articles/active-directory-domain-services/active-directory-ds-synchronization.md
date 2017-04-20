<properties
    pageTitle="Azure Active Directory Domain Services: Sincronización en dominios administrados | Microsoft Azure"
    description="Comprender la sincronización en un dominio administrado de Azure Active Directory Domain Services"
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
    ms.date="10/03/2016"
    ms.author="maheshu"/>

# <a name="synchronization-in-an-azure-ad-domain-services-managed-domain"></a>Sincronización en un dominio administrado de servicios de dominio de Active Directory de Azure
El siguiente diagrama muestra cómo funciona la sincronización en servicios de dominio de Active Directory de Azure dominios administrados.

![Topología de sincronización en servicios de dominio de Active Directory de Azure](./media/active-directory-domain-services-design-guide/sync-topology.png)

## <a name="synchronization-from-your-on-premises-directory-to-your-azure-ad-tenant"></a>Sincronización desde el directorio local en el inquilino de Azure AD
Sincronización de Azure AD Connect se usa para sincronizar las cuentas de usuario, la pertenencia a grupos y credenciales hash a su inquilino de Azure AD. Atributos de usuario de cuentas como el UPN y local están sincronizados SID (identificador de seguridad). Si usa servicios de dominio de Active Directory de Azure, hash credencial heredados necesarios para la autenticación Kerberos y NTLM también se sincronizan con el inquilino de Azure AD.

Si configura reescritura, se sincronizan los cambios que se producen en su directorio de Azure AD volver a su Active Directory local. Por ejemplo, si cambia la contraseña con características de cambio de contraseña de autoservicio de Azure AD, la contraseña modificada se actualiza en sus instalaciones de dominio de Active Directory.

> [AZURE.NOTE] Usar siempre la versión más reciente de Azure AD Connect para asegurarse de que tiene las soluciones para todos los errores conocidos.


## <a name="synchronization-from-your-azure-ad-tenant-to-your-managed-domain"></a>Sincronización de su inquilino de Azure AD a su dominio administrado
Cuentas de usuario, pertenencias a grupos y credenciales hash se sincronizan desde el inquilino de Azure AD a su dominio de servicios de dominio de Active Directory de Azure administrado. Este proceso de sincronización es automático. No es necesario configurar, supervisar o administrar este proceso de sincronización. El proceso de sincronización es también una-vías/unidireccional de naturaleza. Su dominio administrado es en gran medida de solo lectura excepto las unidades organizativas personalizadas que cree. Por lo tanto, no puede realizar cambios en los atributos de usuario, las contraseñas de usuario o pertenencias a grupos dentro del dominio administrado. Por tanto, no hay ninguna sincronización inversa de los cambios de su dominio administrado volver a su inquilino de Azure AD.


## <a name="synchronization-from-a-multi-forest-on-premises-environment"></a>Sincronización de un entorno de varios bosques local
Muchas organizaciones tienen una infraestructura de identidad local bastante complejo que consta de varios bosques de cuenta. Azure AD Connect es compatible con la sincronización de los usuarios, grupos y credenciales hash desde entornos de varios bosques en el inquilino de Azure AD.

En cambio, el inquilino de Azure AD está mucho más simples y plano. Para permitir a los usuarios acceso sujeto a las aplicaciones protegidas por Azure AD, resolver conflictos UPN en todas las cuentas de usuario en diferentes bosques. Su osos dominio administrado de servicios de dominio de Active Directory de Azure cierre semejanza a su inquilino de Azure AD. Por tanto, verá una estructura OU plana de su dominio administrado. Todos los usuarios y grupos se almacenan en el contenedor 'AADDC usuarios', independientemente del bosque o dominio local desde el que se han sincronizado en. Puede haber configurado una unidad organizativa jerárquica estructura local. Sin embargo, el dominio administrado aún tiene una estructura OU simple sin formato.


## <a name="exclusions---what-isnt-synchronized-to-your-managed-domain"></a>Exclusiones - ¿qué no está sincronizado con su dominio administrado
Los siguientes objetos o atributos no están sincronizados en el inquilino de Azure AD o a su dominio administrado:

- **Excluir atributos:** Puede elegir excluir determinados atributos de sincronización en el inquilino de Azure AD desde su dominio locales con Azure AD Connect. Estos atributos excluidos no están disponibles en su dominio administrado.

- **Directivas de grupo:** Las directivas de grupo configuradas en el dominio local no se sincronizan con su dominio administrado.

- **Compartido SYSVOL:** Del mismo modo, el contenido del recurso compartido SYSVOL en su dominio local no se sincroniza con su dominio administrado.

- **Objetos de equipo:** Objetos de equipo para los equipos conectados a su dominio local no se sincronizan con su dominio administrado. Estos equipos no tienen una relación de confianza con su dominio administrado y pertenecen a su dominio local solo. En su dominio administrado, encontrará los objetos de equipo solo para equipos que ha explícitamente dominio-unido al dominio administrado.

- **Atributos SidHistory para usuarios y grupos:** El usuario principal y el grupo principal SID desde su dominio local se sincronizan con su dominio administrado. Sin embargo, los atributos SidHistory existentes para usuarios y grupos no se sincronizan desde su dominio local a su dominio administrado.

- **Estructuras de unidades de organización (OU):** Unidades organizativas definidas en su dominio local no se sincronizan con su dominio administrado. Hay dos OU integrada en su dominio administrado. De forma predeterminada, su dominio administrado tiene una estructura OU plana. Sin embargo, puede [crear una OU personalizada en su dominio administrado](./active-directory-ds-admin-guide-create-ou.md).


## <a name="how-specific-attributes-are-synchronized-to-your-managed-domain"></a>Cómo se sincronizan los atributos específicos a su dominio administrado
La siguiente tabla enumera algunas atributos comunes y describe cómo se sincronizarán con su dominio administrado.

| El atributo en su dominio administrado | Origen | Notas |
|:---|:---|:---|
|UPN|Atributo UPN del usuario en su inquilino de Azure AD|El atributo UPN de su inquilino de Azure AD se sincroniza mientras está en su dominio administrado. Por lo tanto, la manera más confiable para iniciar sesión en su dominio administrado usa el UPN.|
|SAMAccountName|MailNickname del usuario, el atributo en su inquilino de Azure AD o generadas automáticamente|El atributo SAMAccountName proceden del atributo mailNickname en su inquilino de Azure AD. Si varias cuentas de usuario tienen el mismo atributo mailNickname, SAMAccountName es generado automáticamente. Si el usuario mailNickname o prefijo UPN es más de 20 caracteres, el atributo SAMAccountName es generado automáticamente para cumplir con el límite de 20 caracteres en atributos SAMAccountName.|
|Contraseñas|Contraseña de usuario de su inquilino de Azure AD|Hash credencial necesarios para la autenticación de NTLM o Kerberos (también denominada credenciales complementarios) se sincronizan desde el inquilino de Azure AD. Si su inquilino de Azure AD es un inquilino sincronizado, estas credenciales proceden de su dominio local.|
|Usuario o grupo SID principal|Generadas automáticamente|El SID principal para las cuentas de usuario o grupo está generado automáticamente en su dominio administrado. Este atributo no coincide con el usuario o grupo principal SID del objeto en sus instalaciones de dominio de Active Directory. Este error de coincidencia es porque el dominio administrado tiene un espacio de nombres de SID diferente de su dominio local.|
|Historial de SID para usuarios y grupos|Usuario principal de local y SID de grupo|El atributo SidHistory para usuarios y grupos en su dominio administrado se establece para que coincida con el usuario principal correspondiente o grupo SID en su dominio local. Esta característica le ayuda a facilitar la elevación y MAYÚS aplicaciones locales en el dominio administrado, ya que no es necesario a recursos de ACL re.|

> [AZURE.NOTE] **Iniciar sesión en el dominio administrado usando el formato UPN:** El atributo SAMAccountName puede ser generado automáticamente algunas cuentas de usuario en su dominio administrado. Si varios usuarios que tienen el mismo atributo mailNickname o los usuarios tienen prefijos UPN demasiado largos, SAMAccountName para estos usuarios pueden generado automáticamente. Por lo tanto, el formato de SAMAccountName (por ejemplo, ' CONTOSO100\joeuser') no siempre es una manera confiable para iniciar sesión en el dominio. SAMAccountName generado automáticamente de los usuarios puede diferir de su prefijo UPN. Usar el formato UPN (por ejemplo, 'joeuser@contoso100.com') iniciar sesión en el dominio administrado confiable.


## <a name="objects-that-are-not-synchronized-to-your-azure-ad-tenant-from-your-managed-domain"></a>Objetos que no están sincronizados en el inquilino de Azure AD desde su dominio administrado
Como se describe en una sección anterior de este artículo, no hay ninguna sincronización desde su dominio administrado volver a su inquilino de Azure AD. Puede [crear una unidad organizativa (OU) personalizado](./active-directory-ds-admin-guide-create-ou.md) en el dominio administrado. Además, puede crear otras unidades organizativas, usuarios, grupos o cuentas de servicio en estas unidades organizativas personalizadas. Ninguno de los objetos creados en unidades organizativas personalizadas se vuelven a sincronizar el inquilino de Azure AD. Estos objetos están disponibles para su uso solo dentro de su dominio administrado. Por lo tanto, estos objetos no son visibles mediante cmdlets de PowerShell de Azure AD, API de Azure AD Graph o mediante la interfaz de usuario de administración de Azure AD.


## <a name="related-content"></a>Contenido relacionado
- [Servicios de dominio de Active Directory Azure características-](active-directory-ds-features.md)

- [Escenarios de implementación - Servicios de dominio de Active Directory de Azure](active-directory-ds-scenarios.md)

- [Consideraciones de red para los servicios de dominio de Active Directory de Azure](active-directory-ds-networking.md)

- [Introducción a servicios de dominio de Active Directory de Azure](active-directory-ds-getting-started.md)

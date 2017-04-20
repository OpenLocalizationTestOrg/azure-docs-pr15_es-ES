<properties
    pageTitle="Servicios de dominio de Azure AD: Comparar un dominio de Active Directory de Azure servicios a los controladores de dominio DIY | Microsoft Azure"
    description="Comparación de servicios de dominio de Azure Active Directory en controladores de dominio BRICOLAJE"
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
    ms.date="10/01/2016"
    ms.author="maheshu"/>

# <a name="how-to-decide-if-azure-ad-domain-services-is-right-for-your-use-case"></a>Cómo decidir si Servicios de dominio de Active Directory de Azure es la adecuada para el caso de uso
Servicios de dominio de Active Directory Azure le permite implementar las cargas de trabajo en servicios de infraestructura de Azure, sin tener que preocuparse de mantener su infraestructura de identidad. Este servicio administrado es diferente de una implementación de Active Directory de Windows Server típica que implementar y administrar sus propias. El servicio está diseñado para facilidad de implementación, supervisión de estado automatizado y corrección y una infraestructura de identidad simple para la nube. Nos estamos constantemente evolución para agregar compatibilidad para escenarios comunes de implementación.

Para decidir si desea usar los servicios de dominio de Active Directory de Azure o giran y administrar su propia infraestructura de AD (personal) en Azure:

- Vea la lista de [características que ofrece servicios de dominio de Active Directory de Azure](active-directory-ds-features.md).

- Revise comunes [escenarios de implementación de servicios de dominio de Active Directory de Azure](active-directory-ds-scenarios.md).

- Por último, [comparar los servicios de dominio de Active Directory de Azure a una opción de AD por](active-directory-ds-comparison.md#compare-azure-ad-domain-services-to-diy-ad-domain-in-azure).


## <a name="compare-azure-ad-domain-services-to-diy-ad-domain-in-azure"></a>Comparación de servicios de dominio de Active Directory de Azure dominio AD BRICOLAJE en Azure
La siguiente tabla le ayuda a decidir entre el uso de servicios de dominio de Active Directory de Azure y administrar su propia infraestructura de AD en Azure.

|**Característica**|**Servicios de dominio de Active Directory de Azure**|**'Por' AD en máquinas virtuales de Azure**|
|---|:---:|:---:|
|[**Administración de servicios**](active-directory-ds-comparison.md#managed-service)|**& #x 2713;**|**& #x 2715;**|
|[**Implementaciones seguras**](active-directory-ds-comparison.md#secure-deployments)|**& #x 2713;**|Administrador debe proteger la implementación.|
|[**Servidor DNS**](active-directory-ds-comparison.md#dns-server)|**& #x 2713;** (servicio administrado)|**& #x 2713;**|
|[**Privilegios de administrador de dominio o de organización**](active-directory-ds-comparison.md#domain-or-enterprise-administrator-privileges)|**& #x 2715;**|**& #x 2713;**|
|[**Unirse a un dominio**](active-directory-ds-comparison.md#domain-join)|**& #x 2713;**|**& #x 2713;**|
|[**Autenticación de dominio mediante NTLM y Kerberos**](active-directory-ds-comparison.md#domain-authentication-using-ntlm-and-kerberos)|**& #x 2713;**|**& #x 2713;**|
|[**Estructura OU personalizada**](active-directory-ds-comparison.md#custom-ou-structure)|**& #x 2713;**|**& #x 2713;**|
|[**Extensiones de esquema**](active-directory-ds-comparison.md#schema-extensions)|**& #x 2715;**|**& #x 2713;**|
|[**Confía en el dominio o bosque de AD**](active-directory-ds-comparison.md#ad-domain-or-forest-trusts)|**& #x 2715;**|**& #x 2713;**|
|[**Lectura LDAP**](active-directory-ds-comparison.md#ldap-read)|**& #x 2713;**|**& #x 2713;**|
|[**LDAP seguro (LDAPS)**](active-directory-ds-comparison.md#secure-ldap)|**& #x 2713;**|**& #x 2713;**|
|[**Escritura LDAP**](active-directory-ds-comparison.md#ldap-write)|**& #x 2715;**|**& #x 2713;**|
|[**Directiva de grupo**](active-directory-ds-comparison.md#group-policy)|Simple|Pantalla completa|
|[**Implementaciones distribuidas geo**](active-directory-ds-comparison.md#geo-dispersed-deployments)|**& #x 2715;**|**& #x 2713;**|


#### <a name="managed-service"></a>Administración de servicios
Microsoft administra Azure dominios de servicios de dominio de Active Directory. No tiene que preocuparse de correcciones, actualizaciones, supervisión, copias de seguridad y garantizar la disponibilidad de su dominio. Estas tareas de administración están disponibles como un servicio por Microsoft Azure para sus dominios administrados.

#### <a name="secure-deployments"></a>Implementaciones seguras
El dominio administrado es bloquear de forma segura según las prácticas recomendadas de seguridad de Microsoft para implementaciones de AD. Estas recomendaciones provienen de décadas del equipo del producto de AD de experiencia de ingeniería y soporte de implementaciones de AD. Para implementaciones personal, debe tomar medidas de implementación específico para bloquear abajo o proteger la implementación.

#### <a name="dns-server"></a>Servidor DNS
Un dominio de servicios de dominio de Active Directory de Azure administrado incluye servicios DNS administrados. Los miembros del grupo 'AAD DC administradores' pueden administrar DNS en el dominio administrado. Miembros de este grupo se conceden privilegios de administración de DNS del dominio administrado. Administración de DNS se puede realizar utilizando la 'consola de administración de DNS' incluida en el paquete de herramientas de administración de servidor remoto (RSAT).

#### <a name="domain-or-enterprise-administrator-privileges"></a>Privilegios de administrador de empresa o dominio
No se ofrecen estas privilegios elevados en un dominio de AAD DS administrado. No se puede ejecutar aplicaciones que requieren estos privilegios elevados estar instalado o ejecutar contra dominios administrados. Un subconjunto más pequeño de privilegios está disponible para los miembros del grupo de administración delegada denominado 'AAD DC administradores'. Estos privilegios incluyen privilegios para configurar DNS, configurar Directiva de grupo, obtener privilegios de administrador en equipos unidos a un dominio etcetera.

#### <a name="domain-join"></a>Unirse a un dominio
Puede unir máquinas virtuales al dominio administrado similar a cómo unir equipos a un dominio de Active Directory.

#### <a name="domain-authentication-using-ntlm-and-kerberos"></a>Autenticación de dominio mediante NTLM y Kerberos
Con los servicios de dominio de Active Directory de Azure, puede usar sus credenciales corporativas para autenticar con el dominio administrado. Las credenciales se guardan en sincronización con el inquilino de Azure AD. Para los inquilinos sincronizados, Azure AD Connect garantiza que los cambios realizadas de credenciales locales están sincronizados con Azure AD. Con una configuración de dominio DIY, debe configurar una relación de confianza del dominio con un bosque de cuentas locales a los usuarios para autenticar con sus credenciales corporativas. Como alternativa, tendrá que configurar la replicación de AD para asegurarse de que se sincronizan las contraseñas de usuario a los equipos virtuales de controlador de dominio de Azure.

#### <a name="custom-ou-structure"></a>Estructura OU personalizada
Los miembros del grupo 'AAD DC administradores' pueden crear unidades organizativas personalizadas dentro del dominio administrado.

#### <a name="schema-extensions"></a>Extensiones de esquema
No puede extender el esquema de la base de un dominio de servicios de dominio de Active Directory de Azure administrado. Por lo tanto, las aplicaciones que dependen de las extensiones de esquema de Active Directory (por ejemplo, nuevos atributos en el objeto de usuario) no se eleva y se desplaza a dominios AAD DS.

#### <a name="ad-domain-or-forest-trusts"></a>Dominio de Active Directory o de bosque
No se puede configurar dominios administrados para configurar las relaciones de confianza (entrada/salida) con otros dominios. Por lo tanto, escenarios como implementaciones de bosque de recursos o casos donde no desea sincronizar contraseñas para Azure AD no pueden usar servicios de dominio de Active Directory de Azure.

#### <a name="ldap-read"></a>Lectura LDAP
El dominio administrado es compatible con las cargas de trabajo de lectura LDAP. Por lo tanto, puede implementar aplicaciones que lleve a cabo operaciones de lectura de LDAP con el dominio administrado.

#### <a name="secure-ldap"></a>LDAP seguro
Puede configurar los servicios de dominio de Active Directory de Azure para proporcionar acceso seguro de LDAP a su dominio administrado, incluso a través de internet.

#### <a name="ldap-write"></a>Escritura LDAP
El dominio administrado es de solo lectura para objetos de usuario. Por lo tanto, las aplicaciones que realizan operaciones de escritura LDAP en atributos del objeto de usuario no funcionan en un dominio administrado. Además, las contraseñas de usuario no se puede cambiar desde dentro del dominio administrado. Otro ejemplo sería modificación de pertenencia a grupos o atributos de grupo en el dominio administrado, que no está permitido. Sin embargo, los cambios a los atributos de usuario o contraseñas en Azure AD (a través del portal de PowerShell/Azure) o AD se sincronizan con el dominio administrado AAD DS en local.

#### <a name="group-policy"></a>Directiva de grupo
No se admiten construcciones de directiva de grupo sofisticada en el dominio administrado AAD DS. Por ejemplo, no puede crear e implementar GPO independientes para cada OU en el dominio personalizado o usar filtros para dirigir GP WMI. Hay un GPO integrado cada para los contenedores 'AADDC equipos' y 'AADDC usuarios', que se pueden personalizar para configurar la directiva de grupo.

#### <a name="geo-dispersed-deployments"></a>Implementaciones disperso geo
Azure AD administrados dominios están disponibles en una única red virtual en Azure. Escenarios que requieren controladores de dominio esté disponible en varias áreas de Azure en todo el mundo, la configuración de los controladores de dominio en máquinas virtuales de IaaS de Azure posible la alternativa mejor.


## <a name="do-it-yourself-diy-ad-deployment-options"></a>Opciones de implementación (y MANUALIDADES) AD 'Por'
Puede que deba casos de uso de implementación donde se necesitan algunas de las capacidades de una instalación de Windows Server AD. En estos casos, tenga en cuenta las siguientes opciones (y MANUALIDADES) por:

- **Dominio de nube independiente:** Puede configurar un programa independiente 'dominio de nube' usando Azure máquinas virtuales que se han configurado como controladores de dominio. Esta infraestructura no se integra con su local en el entorno de AD. Esta opción, se requeriría un conjunto diferente de 'nube credenciales' inicio de sesión y administrar máquinas virtuales en la nube.

- **Implementación de bosque de recursos:** Puede configurar un dominio en la topología de bosque de recursos, uso de Azure máquinas virtuales que está configurados como controladores de dominio. A continuación, puede configurar una relación de confianza de AD con su local en el entorno de AD. Puede unirse a un dominio equipos (Azure VM) a este bosque de recursos en la nube. Autenticación de usuario pasa sobre cualquiera una conexión VPN/ExpressRoute al directorio local.

- **Ampliar su dominio local a Azure:** Una red virtual Azure puede conectarse a su red local mediante una conexión VPN/ExpressRoute, para que pueden unirse a su local en máquinas virtuales de Azure AD. Otra alternativa es promover controladores de dominio de réplica del dominio local en Azure como una máquina virtual. A continuación, puede configurar el replicar a través de una conexión VPN/ExpressRoute al directorio local. El modo de implementación eficaz extiende su dominio local a Azure.

> [AZURE.NOTE] Puede determinar que una opción de BRICOLAJE es más apropiada para su implementación de casos de uso. Piense en [Compartir comentarios](active-directory-ds-contact-us.md) para ayudarnos a comprender qué características que permita decide usar servicios de dominio de Active Directory de Azure en el futuro. Sus comentarios nos ayudan a evolucionar el servicio para que se ajuste mejor a sus necesidades de implementación y casos de uso.

Que hemos publicado [directrices para implementar Active Directory de Windows Server en Azure máquinas virtuales de Windows](https://msdn.microsoft.com/library/azure/jj156090.aspx) para facilitar la DIY instalaciones.


## <a name="related-content"></a>Contenido relacionado
- [Servicios de dominio de Active Directory Azure características-](active-directory-ds-features.md)

- [Escenarios de implementación - Servicios de dominio de Active Directory de Azure](active-directory-ds-scenarios.md)

- [Directrices para la implementación de Active Directory Windows Server en máquinas virtuales de Windows Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx)

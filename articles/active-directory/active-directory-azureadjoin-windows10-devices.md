<properties
    pageTitle="Con dispositivos de Windows 10 en su lugar de trabajo | Microsoft Azure"
    description="Proporciona una instantánea de las capacidades de los usuarios y TI, contraste las distintas maneras en un dispositivo puede aprovisionar y utilizar en una empresa con Windows 10."
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""
    tags="azure-classic-portal"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/17/2016"
    ms.author="femila"/>

# <a name="using-windows-10-devices-in-your-workplace"></a>Uso de dispositivos de Windows 10 en su lugar de trabajo

Se aplica a: PC de Windows 10

Windows 10 ofrece tres modelos para las organizaciones que permiten a los usuarios acceso a los recursos de trabajo de forma segura y cómoda.

- **Unirse Azure Active Directory** (Combinación de azure AD), para los trabajadores que tener acceso a los recursos, como Office 365 principalmente en la nube. Combinación de Azure AD es trabajo autoservicio aprovisionamiento experiencia de nuevo en Windows 10.
- **Unirse a un dominio**, para las organizaciones que tienen las inversiones en aplicaciones locales y recursos. Unirse a un dominio ofrece una mejor experiencia en Windows 10 cuando está conectado a Azure AD.
- **Una nueva experiencia BYOD simplificada**, los usuarios que desea agregar una cuenta de trabajo o escuela Windows y fácilmente acceso a los recursos en sus dispositivos personales.

En la tabla siguiente se presenta una instantánea de las capacidades de los usuarios y administradores de TI de las distintas maneras en un dispositivo puede aprovisionar y utilizar en una empresa con Windows 10 de contraste:

|                                                                                                                                                                 | Unirse a un dominio     | Combinación de Azure AD | Dispositivo personal |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------|---------------|-----------------|
| Dispositivo de inicio de sesión de Windows para las cuentas de trabajo o escuela.                                                                                                                      | Sí             | Sí           | No              |
| Usuario de inicio de sesión único (SSO) para las aplicaciones de Office 365 y Azure AD. SSO es la capacidad para iniciar sesión en una sola vez tener acceso a recursos de la organización. | Sí             | Sí           | Sí             |
| Usuario SSO para aplicaciones de Kerberos/NTLM.                                                                                                                                  | Sí             | Limitado       | Sí, mediante VPN         |
| Autorización fuerte y un inicio de sesión adecuado para las cuentas de trabajo o escuela con Microsoft Passport y Hola de Windows.                                                                   | Sí             | Sí           | Sí             |
| Acceso a la empresa de la tienda Windows con una cuenta profesional o educativa (no una cuenta de Microsoft).                                                                                    | Sí             | Sí           | Sí             |
| Compatible con Enterprise móviles de configuración de usuario en dispositivos con cuentas de trabajo o escuela.                                                                                 | Sí             | Sí           | Sí             |
| La capacidad para restringir el acceso a aplicaciones organizativas a dispositivos que son compatibles con las directivas de dispositivo organizativa.                                                      | Sí             | Sí           | Sí             |
| Usuario autoservicio de aprovisionamiento de dispositivos para "trabajo desde cualquier lugar".                                                                                                | No              | Sí           | Sí             |
| Capacidad para administrar dispositivos.                                                                                                                                       | Sí, mediante GP/SCCM | Sí           | Sí             |

## <a name="use-work-owned-devices-with-azure-ad-join-and-domain-join-in-windows-10"></a>Unirse dispositivos que posee el trabajo de uso con la combinación de Azure AD y el dominio en Windows 10

Windows 10 ofrece dos maneras de dispositivos que posee el trabajo tener acceso a los recursos de trabajo:

- Combinación de Azure AD
- Unirse a un dominio

 Ambos pueden ser válidas opciones según las necesidades y los requisitos de la organización. En algunos casos, las organizaciones pueden beneficiarse de habilitar ambos métodos de implementación.

## <a name="when-to-use-azure-active-directory-join"></a>Cuándo usar la combinación de Azure Active Directory

Combinación de Azure AD es un nuevo trabajo de autoservicio de aprovisionamiento experiencia en Windows 10.  Está dirigido a los trabajadores acceso a los recursos de trabajo, como Office 365 principalmente en la nube. Es una forma sencilla de configurar los equipos, tabletas y teléfonos para la empresa. Administran dispositivos a través de la administración de dispositivos móviles, mediante controles coherentes entre plataformas de Windows.

**Usar Azure AD unirse a cualquiera de los siguientes motivos**:

- Que desea habilitar el aprovisionamiento de autoservicio de dispositivos para los trabajadores sobre la marcha.
- Proporcionar a los usuarios con dispositivos móviles que posee el trabajo como tabletas y teléfonos.
- Desea administrar un conjunto de usuarios de Azure AD en lugar de en Active Directory, como trabajadores de temporada, los contratista o estudiantes.
- Desea proporcionar capacidades de unión para los trabajadores de sucursales remotas con infraestructura locales limitada.
- No tiene un Active Directory local.

Algunas organizaciones utiliza Azure AD unirse como la forma principal para implementar los dispositivos que posee el trabajo, sobre todo si se migran la mayoría o todos los recursos en la nube. También pueden elegir las organizaciones híbrido con Active Directory y Azure AD implementar un método u otra dependiendo del usuario o departamento.

Zonas de la escuela y universidades, por ejemplo, pueden administrar personal en Active Directory y los estudiantes de Azure AD. Algunas compañías que desee administrar sucursales o departamentos de ventas en Azure AD. Combinación de Azure AD y métodos de combinación de dominio pueden usarse dentro de una organización híbrido. Combinación de Azure AD puede ser un buen complemento para unirse a un dominio para la implementación de dispositivos en un entorno de trabajo.

**Si el acceso a recursos de empresa más habitual es a través de la nube, su organización puede disfrutar de las ventajas adicionales si**:

- Puede quitar las dependencias en la infraestructura de identidades locales.
- Puede simplificar su modelo de implementación de dispositivos recibe lejos de soluciones de imágenes al permitir la configuración de autoservicio.
- Puede usar la administración de dispositivos móviles para administrar todos los dispositivos entre distintas plataformas.

Para obtener más información acerca de Azure AD unirse, consulte [capacidades de nube de ampliar a Windows 10 dispositivos a través de Azure Active Directory unirse](active-directory-azureadjoin-overview.md).

## <a name="when-to-use-domain-join-or-keep-using-it"></a>Cuándo usar la combinación de dominio (o mantener usarlo)

Durante los últimos 15 años, muchas organizaciones han utilizado unirse a un dominio para conectar dispositivos de trabajo. Permite a los usuarios iniciar sesión en sus dispositivos con su trabajo de Active Directory cuentas profesionales o educativas. Unirse a un dominio también permite TI estos dispositivos de administrar de forma centralizada y totalmente. Organizaciones generalmente se basan en imágenes métodos para aprovisionar dispositivos y a menudo con el Administrador de configuración de System Center (SCCM) o grupo Directiva de administrarlos.

**Su empresa debe usar la combinación de dominio (o mantener usarlo) por cualquiera de estas razones**:

- Tiene aplicaciones Win32 implementadas en estos dispositivos que utilizan NTLM o Kerberos.
- Requerir GP o SCCM/DCM administrar dispositivos.
- Desea seguir utilizando soluciones de imágenes para configurar los dispositivos para los empleados.

**Unirse a un dominio en Windows 10 también proporciona las siguientes ventajas cuando se conecta a Azure AD**:

- Autenticación de dispositivo enlazadas segura y un inicio de sesión adecuado para las cuentas de trabajo o escuela con Microsoft Passport y Hola de Windows.
- Acceso a la tienda Windows de empresa para dispositivos que utilizan el trabajo o escuela cuentas (ninguna cuenta Microsoft requerida).
- Compatible con Enterprise móviles de configuración de usuario en todos los dispositivos que utilizan cuentas de trabajo o escuela (ninguna cuenta Microsoft requerida).
- La capacidad para restringir el acceso a aplicaciones organizativas a dispositivos que son compatibles con las directivas de dispositivo organizativa.

Para obtener más información acerca de Azure AD unirse, consulte [capacidades de nube de ampliar a Windows 10 dispositivos a través de Azure Active Directory unirse](active-directory-azureadjoin-overview.md).

## <a name="enable-joining-of-personally-owned-devices-for-work-or-school"></a>Habilitar la unión de dispositivos de propiedad de los usuarios para el trabajo o escuela

Para admitir BYOD de la empresa, Windows 10 permite al usuario a agregar una cuenta profesional o educativa en su equipo, una tableta o un teléfono. Después de que el usuario agrega una cuenta profesional o educativa, el dispositivo está registrado con Azure AD y, opcionalmente, inscrito en el sistema de administración de dispositivos móviles que la organización ha configurado. El directorio mostrará estos dispositivos como 'Registrado' frente a 'Azure AD unido'. Administraors de TI puede aplicar diferentes directivas en función de esta información, lo que proporciona un toque más claro en dispositivos de propiedad de los usuarios que en dispositivos de trabajo si lo desea.

Los usuarios pueden agregar un trabajo o escuela cuenta con su dispositivo personal muy cómodamente. Puede hacerlo al acceder a una aplicación de trabajo por primera vez o puede hacer manualmente a través del menú de configuración. Esta cuenta le proporcionará SSO a recursos de la organización.

Para obtener más información acerca de Azure AD unirse, consulte [conectar dispositivos de dominio a Azure Active Directory para Windows 10 experiencias](active-directory-azureadjoin-devices-group-policy.md).

## <a name="enable-domain-join-or-azure-ad-join"></a>Habilitar la combinación de dominio o Azure AD

Todos los métodos descritos anteriormente (unirse a un dominio, Azure AD combinación y agregar cuenta escolar o trabajar) tiene puntos de entrada en la experiencia del usuario de Windows 10. No obstante, todos requieren un administrador de TI habilitar la funcionalidad de la infraestructura para que funcione la experiencia.

## <a name="requirements-for-deploying-azure-ad-join"></a>Requisitos para implementar Azure AD unirse

Para implementar Azure AD unirse para cualquier grupo de usuarios necesita lo siguiente:

- Una suscripción de Azure AD.
- Una suscripción de Azure AD Premium, como el dispositivo móvil administración inscripción automática, si necesita más funciones.
- Administración de dispositivos móviles, por ejemplo, una suscripción a Microsoft Intune, administración de dispositivos móviles para Office 365 o cualquiera de los proveedores de administración de dispositivos móviles asociado que se integran en Azure AD. (Consulte la [sección Preguntas más frecuentes](#frequently-asked-questions) cerca del final de este artículo para obtener más información).

Si sus instalaciones son híbrido, se recomienda que distribuya Azure AD Connect para ampliar el directorio local a Azure AD.

## <a name="requirements-for-using-domain-join-with-azure-ad"></a>Requisitos para usar el dominio unirse con Azure AD

Unirse a un dominio continúa funcionando como siempre tiene. Sin embargo, para obtener las ventajas de Azure AD se necesita lo siguiente:

- Una suscripción de Azure AD.
- Una implementación de Azure AD Connect para extender el directorio local a Azure AD.
- Directiva que permite que los dispositivos tengan acceso condicional a Azure AD unido al dominio.
- Directiva que permite el acceso a dispositivos "dominio une" Si desea restringir el acceso para algunos dispositivos.
- Administrador de configuración de System Center versión 1509 para Technical Preview, habilitar reglas para requerir dispositivos compatibles con. (Consulte la documentación de TechNet y la entrada de blog).

Para obtener más información sobre cómo unirse a un dominio en Windows 10, vea [experiencias de dispositivos de dominio conectarse a Azure Active Directory para Windows 10](active-directory-azureadjoin-devices-group-policy.md)

## <a name="requirements-for-using-byod-and-add-a-work-or-school-account"></a>Requisitos para usar BYOD y "Agregar una cuenta profesional o educativa"

Para habilitar "Traer su propio dispositivo" (BYOD) con cuentas de trabajo o escuela, necesita lo siguiente:

- Una suscripción de Azure AD.
- Una suscripción de Azure AD Premium, como el dispositivo móvil administración inscripción automática, si necesita más funciones.

## <a name="requirements-for-using-microsoft-passport"></a>Requisitos para usar Microsoft Passport

Para habilitar Microsoft Passport, necesita lo siguiente:

- Infraestructura de claves públicas (PKI) para la compatibilidad con la autenticación basada en certificados que usa Microsoft Passport.
- Una suscripción Intune para compatibilidad con la autenticación basada en certificados que usa Microsoft Passport para Azure AD unirse y cuentas de trabajo o escuela.
- Administrador de configuración de System Center versión 1509 para Technical Preview (consulte la documentación de TechNet y la entrada de blog) para la compatibilidad con la autenticación basada en certificados que utiliza Microsoft Passport para unirse a un dominio.
- Una directiva para habilitar Microsoft Passport de la organización.

Como alternativa a una PKI, puede habilitar basada en claves Microsoft Passport haciendo lo siguiente:

- Implementar controladores de dominio de Windows Server 2016 "Vista previa de producción 1" (sin necesidad de niveles funcionales de dominio o bosque; varios controladores de dominio para servir redundancia basta con cada sitio de Active Directory).
- Establecer directivas para habilitar Microsoft Passport de la organización.

Para obtener más información sobre Microsoft Passport y Windows Hola en Windows 10, consulte < link-to-MS-Passport-and-Windows-Hello-document >.

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes
### <a name="which-partner-mobile-device-management-products-integrate-with-azure-ad"></a>¿Qué productos de administración del dispositivo móvil asociado se integran con Azure AD?

Los siguientes productos de proveedor que se integran con Azure AD de inscripción unificada y acceso condicional en Windows 10:

- AirWatch VMware
- Citrix Xenmobile
- Lightspeed Mobile Manager
- Administración de dispositivos móviles SOTI local
- MobileIron

### <a name="what-about-workplace-join-in-windows-10"></a>¿Qué hay de lugar de trabajo unirse en Windows 10?
Combinación de área de trabajo se usó en Windows 8.1 para habilitar BYOD. En Windows 10, BYOD está habilitada a través de "Agregar un trabajo o escuela cuenta" como se explica más adelante en este documento. Para las organizaciones que no integran la administración de su dispositivo móvil con Azure AD, los usuarios pueden inscribir el dispositivo manualmente a través de la **configuración**de administración > **cuentas** > **acceso de trabajo**.


### <a name="can-users-connect-their-microsoft-account-to-their-domain-account-in-windows-10"></a>¿Los usuarios pueden conectar su cuenta de Microsoft a su cuenta de dominio en Windows 10?
No en Windows 10. En Windows 8.1, los usuarios de dispositivos de dominio podrían "conectarse" su cuenta de Microsoft (por ejemplo, Hotmail, Live, Outlook, Xbox, etc.) a su cuenta de dominio para habilitar determinados experiencias como SSO para servicios directo, uso de la tienda Windows y movilidad de configuración de usuario en todos los dispositivos. En Windows 10, la cuenta de Microsoft "conectar" funcionalidad se ha retirado. El usuario puede agregar una o varias cuentas de Microsoft como cuentas adicionales para habilitar SSO a servicios de consumidor como la tienda Windows. Esto se realiza en la **configuración de** > **cuentas** > **su cuenta**.

Los usuarios que está actualizando desde dispositivos de dominio de Windows 8.1 y quién ha tenido su cuenta de Microsoft conectado, tendrá automáticamente su cuenta de Microsoft conectada agregado a la lista de cuentas adicionales que utilizan.


## <a name="additional-information"></a>Información adicional
* [Windows 10 para la empresa: formas de usar dispositivos para el trabajo](active-directory-azureadjoin-windows10-devices-overview.md)
* [Amplía las capacidades de nube para Windows 10 dispositivos a través de Azure Active Directory unirse](active-directory-azureadjoin-user-upgrade.md)
* [Obtenga información sobre escenarios de uso para unirse a AD de Azure](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Conectar dispositivos unido al dominio con Azure AD para experiencias de Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configurar combinación de Azure AD](active-directory-azureadjoin-setup.md)

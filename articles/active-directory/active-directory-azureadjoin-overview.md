<properties
    pageTitle="Amplía las capacidades de nube para Windows 10 dispositivos a través de Azure Active Directory unirse | Microsoft Azure"
    description="Proporciona una descripción detallada de modo que pueden utilizar los dispositivos de Windows 10 Azure AD unirse a registrarse en Azure Active Directory."
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
    ms.date="09/27/2016"
    ms.author="femila"/>

# <a name="extending-cloud-capabilities-to-windows-10-devices-through-azure-active-directory-join"></a>Amplía las capacidades de nube para Windows 10 dispositivos a través de Azure Active Directory unirse

## <a name="what-is-azure-active-directory-join"></a>¿Qué es la combinación de Azure Active Directory?
Azure Active Directory unirse (Azure AD unirse a) es la funcionalidad que se registra un dispositivo propiedad de la empresa en Azure Active Directory para habilitar la administración centralizada del dispositivo. Es posible para los usuarios como empleados y estudiantes para conectarse a la nube de empresa a través de Azure Active Directory. Esto permite simplificadas implementaciones de Windows y el acceso a aplicaciones de la organización y recursos desde cualquier dispositivo de Windows, ambos su propiedad corporativa y propiedad de los usuarios (BYOD).

Combinación de Azure AD está diseñada para empresas que se nube-primero o nube sólo--normalmente pequeñas y medianas empresas que no tienen una infraestructura de Active Directory de Windows Server local. Que Azure, dicha combinación de AD puede y también se usarán en grandes organizaciones en dispositivos que no se pueden realizar una combinación de dominio tradicionales (dispositivos móviles, por ejemplo), o para usuarios que necesitan tener acceso a Office 365 u otras aplicaciones de Azure AD SaaS principalmente.

Aunque la combinación de dominio tradicional ofrece aún que el local mejor experiencia en los dispositivos que son capaces de unirse a dominio, combinación de Azure AD es adecuado para los dispositivos que no pueden unirse a un dominio. Combinación de Azure AD también es adecuado para administrar usuarios en la nube. Para ello, usar las capacidades de administración de dispositivos móviles en lugar de mediante herramientas de administración de dominio tradicional como directiva de grupo y el Administrador de configuración de System Center (SCCM).

![Información general sobre dispositivos corporativos y personal con local de Active Directory y Azure AD](./media/active-directory-azureadjoin/active-directory-azureadjoin-overview.png)


## <a name="why-should-enterprises-adopt-azure-ad-join"></a>¿Por qué deben adoptar las empresas de Azure AD unirse?

* **Empresas que se encuentran en gran medida en la nube**: si se han movido o están cambiando a un modelo en el que se reduce su huella local y desea trabajar más en la nube, Azure AD unirse pueden beneficiarse. Es posible que haya creado cuentas de Azure AD manualmente o a través de sincronización de Active Directory local. En ambos casos, dispone de una cuenta de Azure AD y puede usarlo para iniciar sesión en Windows 10. Los usuarios pueden unirse a sus equipos a Azure AD a través de la experiencia de predefinidas (configuración rápida) o el menú Configuración. Después de unirse, los usuarios disfrutarán de inicio de sesión único (SSO) acceso único a recursos de la nube como Office 365, en los exploradores o aplicaciones de Office.

* **Instituciones educativas**: uno de los escenarios recibimos sobre a menudo es que instituciones educativas tienen dos tipos de usuario: los profesores y alumnos. Los miembros del personal docente se consideran a miembros a largo plazo de la organización para crear cuentas de local para sea conveniente. Pero estudiantes shorter-term miembros de la organización y, por tanto, pueden administrarse en Azure AD. Esto significa que se puede insertar escala de directorio en la nube en lugar de almacenan localmente. También significa que los alumnos pueden iniciar sesión en Windows con sus cuentas de Azure AD y obtener acceso a los recursos de Office 365, en los exploradores o aplicaciones de Office.

* **Empresas de comercio por menor**: otro escenario nuestros usuarios acerca de los clientes es su deseo de administrar más fácilmente los trabajadores de temporada.  De nuevo, cuentas para empleados a tiempo completo, a largo plazo normalmente los crean como las cuentas en equipos de dominio local. Pero trabajadores de temporada shorter-term miembros de la organización, por lo que es aconsejable administrarlos donde licencias de usuario se pueden mover con más facilidad en. Creación de estas cuentas de usuario en la nube con licencias de Office 365 permite a los usuarios aprovechar las ventajas de iniciar sesión en aplicaciones de Windows y Office con una cuenta de Azure AD. Mientras tanto, mantener una mayor flexibilidad con sus licencias después de abandonar.
* **Otras empresas**: aunque mantener los usuarios de su Active Directory local, todavía puede beneficiarse que los usuarios pueden Azure AD unido. Eso es porque ofrece una experiencia simplificada de unión, administración de dispositivos eficaz, inscripción de administración automática de dispositivos móviles y capacidad de inicio de sesión único de Azure AD de Azure AD y recursos locales.  

## <a name="what-capabilities-does-azure-ad-join-offer"></a>¿Qué capacidades de Azure AD unirse ofrece?
Con Azure AD unirse, podrá hacer lo siguiente:

* **Automática de aprovisionamiento de dispositivos de su propiedad corporativa**: con Windows 10, los usuarios pueden configurar un dispositivo cubetas de nuevo en la experiencia de predefinidas, sin participación de TI.


* **Compatibilidad con formatos moderna**: combinación de Azure AD funciona en dispositivos que no tienen el dominio tradicional unirse a las capacidades.  


* **Compatibilidad con cuentas de organización existentes**: ya no se necesitan los usuarios para crear y mantener un una cuenta personal de Microsoft para obtener la mejor experiencia en dispositivos emitido por la empresa, al igual que con Windows 8. Pueden usar sus cuentas de trabajo existente de Azure AD en su lugar. Para muchas organizaciones, esto significa básicamente que los usuarios pueden configurar e inicie sesión en Windows con las mismas credenciales que se utilizan para acceder a Office 365.


* **Inscripción de administración automática de dispositivos móviles**: dispositivos se pueden inscribir automáticamente en administración de dispositivos móviles cuando se conecta a Azure AD. Este proceso funciona con Microsoft Intune y asociado de soluciones de administración de dispositivos móviles. Cuando haya terminado la administración de dispositivos con Intune, los administradores de TI pueden supervisar o administrar Azure unido AD dispositivos junto con dispositivos de dominio en la consola de administración de SCCM.


* **Inicio de sesión único a recursos de empresa**: los usuarios disfrutar de inicio de sesión único desde el escritorio de Windows a aplicaciones y recursos en la nube, como Office 365 y miles de aplicaciones empresariales que se basan en Azure AD autenticación a través de [Azure AD Connect](active-directory-azureadjoin-deployment-aadjoindirect.md). Dispositivos que pertenecen a la organización que se unen a Azure AD disfrutan también SSO a los recursos locales cuando el dispositivo está en una red corporativa y desde cualquier lugar cuando estos recursos se exponen a través del [Proxy de aplicación de Azure AD](https://msdn.microsoft.com/library/azure/Dn768219.aspx).


* **Estado de sistema operativo móvil**: configuración de accesibilidad, sitios Web, las contraseñas de Wi-Fi y otras opciones de configuración están sincronizados entre dispositivos su propiedad corporativo sin necesidad de tener una cuenta personal de Microsoft.


* **Tienda de Windows para la empresa**: el almacén de Windows admite adquisición de aplicación y licencias con cuentas de Azure AD. Las organizaciones pueden aplicaciones licencia por volumen y que estén disponibles para los usuarios de su organización.

## <a name="how-do-different-devices-work-with-azure-ad-join"></a>¿Cómo funcionan diferentes dispositivos con Azure AD Join?

| Dispositivo de la empresa (unido al dominio local)                                                                                                                                                                                         | Dispositivo de la empresa (unido a la nube)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     | Dispositivo personal                                                                                                         |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------|
| Los usuarios pueden iniciar sesión en Windows con credenciales de trabajo (como hoy lo hacen).                                                                                                                                                                        | Usuarios pueden iniciar sesión en Windows con las credenciales de trabajo que se administran en Azure AD. Esto es relevante para dispositivos corporativos en tres casos: 1) la organización no tiene Active Directory local (por ejemplo, una pequeña empresa). 2) la organización no crea todas las cuentas de usuario en Active Directory (por ejemplo, cuentas de estudiantes, asesores o trabajadores de temporada no se crean en Active Directory). 3) la organización tiene corporativos dispositivos que no puedan unirse a un dominio (local), como teléfonos o tabletas ejecuta un SKU móvil (por ejemplo, un dispositivo secundario en una planta de fábrica o comercial). Combinación de Azure AD admite unión de dispositivos corporativos para las organizaciones federadas y no administrado. | Usuarios iniciar sesión en Windows con sus credenciales de cuenta personales de Microsoft (sin cambios).                                                |
| Los usuarios tienen acceso a configuración móvil y la tienda Windows de empresa. Estos servicios funcionan con cuentas de trabajo y no requieren una cuenta personal de Microsoft. Esto requiere que las organizaciones conectar su Active Directory local a Azure AD.                                        | Los usuarios pueden hacer que el programa de instalación de autoservicio. Pueden ir a través de la experiencia de primera ejecución (FRX) a través de su cuenta de trabajo como alternativa a disposición de TI de que los dispositivos, aunque se admiten ambos métodos.                                                                                                                                                                                                                                                                                                                                                                             | Los usuarios pueden agregar fácilmente una cuenta de trabajo que se administra en Active Directory o de Azure AD.                                                      |
| Los usuarios tienen la capacidad SSO desde el escritorio para trabajar aplicaciones, sitios y recursos, incluidos los recursos locales y aplicaciones de nube que utilicen Azure AD para la autenticación.                                                                                                            | Dispositivos se registra automáticamente en el directorio corporativo (Azure AD) e inscrito automáticamente en administración de dispositivos móviles. (Función Premium de azure AD).                                                                                                                                                                                                                                                                                                                                                                                                                                                  | Los usuarios tienen la capacidad SSO entre aplicaciones y sitios Web o recursos con esta cuenta de trabajo.                                              |
| Los usuarios pueden agregar sus cuentas personales de Microsoft para obtener acceso a sus imágenes personales y archivos sin afectar a los datos empresariales. (Configuración movilidad seguir trabajando con sus cuentas de trabajo). La cuenta de Microsoft permite SSO y unidades ya no la movilidad de configuración.  | Los usuarios pueden hacer un restablecimiento de contraseña de autoservicio (SSPR) en inglés, lo que significa que pueden restablecer una contraseña olvidada. (Función Premium de azure AD).                                                                                                                                                                                                                                                                                                                                                                                                                                   | Los usuarios tienen acceso a la empresa de la tienda Windows para que puede adquirir y usar aplicaciones de línea de negocio en sus dispositivos personales. |                                                               |


## <a name="additional-information"></a>Información adicional
* [Windows 10 para la empresa: formas de usar dispositivos para el trabajo](active-directory-azureadjoin-windows10-devices-overview.md)
* [Amplía las capacidades de nube para Windows 10 dispositivos a través de Azure Active Directory unirse](active-directory-azureadjoin-user-upgrade.md)
* [Autenticación de identidades sin contraseñas a través de Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Obtenga información sobre escenarios de uso para unirse a AD de Azure](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Conectar dispositivos unido al dominio con Azure AD para experiencias de Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configurar combinación de Azure AD](active-directory-azureadjoin-setup.md)

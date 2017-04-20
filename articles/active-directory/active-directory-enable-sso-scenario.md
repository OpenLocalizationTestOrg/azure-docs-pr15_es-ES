<properties
    pageTitle="Administrar aplicaciones de Azure Active Directory | Microsoft Azure"
    description="En este artículo se las ventajas de la integración de Azure Active Directory con local, nube y aplicaciones SaaS."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

   <tags
      ms.service="active-directory"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="identity"
      ms.date="10/10/2016"
      ms.author="markvi"/>

# <a name="managing-applications-with-azure-active-directory"></a>Administrar aplicaciones de Azure Active Directory

Más allá del flujo de trabajo real o el contenido, las empresas tienen dos requisitos básicos para todas las aplicaciones:

1. Para aumentar la productividad, deben ser fáciles descubrir y tener acceso a las aplicaciones

2. Para habilitar la seguridad y gobierno, la organización tiene control y supervisión en quién puede y realmente tiene acceso a cada aplicación

En el mundo de aplicaciones de nube mejor para ello con identidad para controlar "*quién puede hacer cada cosa*".

En la informática terminología:

- *Que* se conoce como *identidad* - la administración de usuarios y grupos

- *¿Qué* se conoce como la *administración de acceso* : la administración de acceso a los recursos protegidos

Ambos componentes juntos se conocen como *identidad y administración de acceso (IAM)*, que está definida por el grupo [Gartner](http://www.gartner.com/it-glossary/identity-and-access-management-iam) como "*la disciplina de seguridad que permite a las personas adecuadas tener acceso a los recursos adecuados en el lado derecho veces por los motivos derecha*".

No se preocupe, ¿cuál es el problema? Si IAM es *no administrado* en un solo lugar con una solución integrada:

- Los administradores de identidad tienen que crear y actualizar las cuentas de usuario en todas las aplicaciones por separado, de forma individual una actividad redundante y lleva mucho tiempo.

- Los usuarios tienen que memorizar varias credenciales para tener acceso a las aplicaciones que necesitan para trabajar con. Como resultado, los usuarios suelen anotar sus contraseñas o use otras soluciones de administración de la contraseña que presenta otros riesgos de seguridad de datos.

- Redundantes actividades mucho tiempo reducen la cantidad de tiempo que los usuarios y administradores trabajan en actividades que aumentan rentabilidad de su empresa.

Por lo tanto, ¿qué generalmente impide que las organizaciones de adopción de soluciones integradas de IAM?

- Soluciones más técnicas se basan en las plataformas de software que necesita para implementar y adaptadas por cada organización para sus propias aplicaciones.

- Aplicaciones de nube con qué frecuencia se adopten a una velocidad mayor organización de TI puede integrar con las soluciones existentes de IAM.

- Seguridad y herramientas de supervisión requieren personalización adicional e integración para lograr escenarios de E2E completos.

## <a name="azure-active-directory-integrated-with-applications"></a>Integrar las aplicaciones de Azure Active Directory

Azure Active Directory es integral de identidades de Microsoft como una solución de servicio (IDaaS) que:

- Permite IAM como un servicio de nube 

- Proporciona administración central de access, el inicio de sesión único (SSO) y generación de informes 

- Es compatible con la administración de acceso integrado para [miles de aplicaciones](https://azure.microsoft.com/marketplace/active-directory/) en la Galería de aplicación, incluyendo Salesforce, Google Apps, cuadro, Concur y mucho más. 


Con Azure Active Directory, todas las aplicaciones publica para los socios y clientes (empresa o consumidor) tienen la misma identidad y tener acceso a las capacidades de administración.<br> Esto le permite reducir considerablemente los costos de operaciones.

¿Qué sucede si necesita implementar una aplicación que todavía no aparece en la Galería de la aplicación? Aunque es un poco más tiempo que la configuración de SSO para aplicaciones de la Galería de aplicación, Azure AD proporciona un asistente que le ayuda con la configuración.

El valor de Azure AD va más allá de aplicaciones de nube "solo". También puede usar con aplicaciones locales proporcionando acceso remoto seguro. Acceso remoto seguro, puede eliminar la necesidad de VPN u otras implementaciones de administración de acceso remoto tradicionales.

Al proporcionar administración central de acceso e inicio de sesión único (SSO) de todas las aplicaciones, Azure AD proporciona la solución a los problemas de seguridad y productividad de datos principal.

- Los usuarios pueden acceder a varias aplicaciones con un inicio de sesión que le da más tiempo a ingresos profesionales o generar actividades de operaciones terminadas.

- Los administradores de identidad pueden administrar el acceso a las aplicaciones en un único lugar.

La ventaja para el usuario y para la empresa es obvia. Echemos un vistazo las ventajas para un administrador de identidad y de la organización.

## <a name="integrated-application-benefits"></a>Ventajas de la aplicación integrada

El proceso SSO tiene dos pasos:

- Autenticación, el proceso de validación de la identidad del usuario.

- Autorización, la decisión de permitir o bloquear el acceso a un recurso con una directiva de acceso.

Al usar Azure AD para administrar las aplicaciones y habilitar SSO de:

- La autenticación se realiza en el usuario local (por ejemplo, AD) o cuenta de Azure AD.

- Autorización se ejecuta en la directiva de asignación y la protección de Azure AD garantizar la experiencia de usuario final coherente y lo que le permite agregar asignación, ubicaciones y condiciones AMF en cualquier aplicación, independientemente de sus capacidades internas.

Es importante comprender que haya sido aprobada la manera en que la autorización en la aplicación de destino varía en función de cómo se integra la aplicación con Azure AD.

- **Las aplicaciones integradas previamente por su proveedor de servicios** Como Office 365 y Azure, son aplicaciones integrada directamente en Azure AD y confiar en él para sus capacidades de administración de identidades y acceso completas. Acceso a estas aplicaciones se habilita mediante la información de directorio y emisión de token.

- **Las aplicaciones integradas previamente por Microsoft y aplicaciones personalizadas** Estos son aplicaciones de nube independiente que se basan en un directorio interno de la aplicación y pueden funcionar con independencia de Azure AD. Acceso a estas aplicaciones se habilita emitir una credencial de aplicación específico asignada a una cuenta de la aplicación. Dependiendo de las funciones de la aplicación, la credencial puede ser un token de federación o el nombre de usuario y la contraseña de una cuenta que se ha suministrado anteriormente en la aplicación.

- **Aplicaciones locales** Publican aplicaciones a través del proxy de aplicación de Azure AD principalmente permitir el acceso a aplicaciones locales. Estas aplicaciones se basan en una central en el directorio local como Active Directory de Windows Server. Acceso a estas aplicaciones está habilitado por activar al servidor proxy para reproducir el contenido de la aplicación para el usuario final respetando el requisito de inicio de sesión local.

Por ejemplo, si un usuario une a la organización, debe crear una cuenta para el usuario en Azure AD para las operaciones de inicio de sesión principales. Si este usuario requiere acceso a una aplicación administrada como Salesforce, también debe crear una cuenta para este usuario de Salesforce y vincularlo a la cuenta de Azure para que SSO funcione. Cuando el usuario deja la organización, es aconsejable eliminar la cuenta de Azure AD y todas las cuentas de equivalente en IAM almacena las aplicaciones que el usuario tiene acceso a.

## <a name="access-detection"></a>Detección de Access

En las empresas modernas, los departamentos de TI a menudo no son conscientes de todas las aplicaciones de nube que se utilizan. Junto con la detección de la aplicación de nube, Azure AD proporciona una solución para detectar estas aplicaciones.

## <a name="account-management"></a>Administración de cuentas

Tradicionalmente, administración de cuentas en las distintas aplicaciones es un proceso manual realizado por TI o soporte técnico personal de la organización. Azure AD totalmente automatizado administración de cuenta en todas las aplicaciones integrada de proveedor de servicio y las aplicaciones integradas previamente auxiliares aprovisionamiento automatizado usuario de Microsoft o de SAML JIT.

## <a name="automated-user-provisioning"></a>Aprovisionamiento automatizado usuario

Algunas aplicaciones proporcionan interfaces de automatización para la creación y eliminación o desactivación de cuentas. Si un proveedor ofrece una interfaz, se aprovecha por Azure AD. Esto reduce los costos de operaciones porque las tareas administrativas se realizarán de forma automática y mejora la seguridad de su entorno porque reduce la probabilidad de acceso no autorizado.

## <a name="access-management"></a>Administración de acceso

Con Azure AD puede administrar el acceso a aplicaciones mediante individuales o regla controlados por las asignaciones. También puede delegar el acceso a la administración de los usuarios adecuados de la organización garantizar la mejor supervisión y reducir la carga en el departamento de soporte técnico.

## <a name="on-premises-applications"></a>Aplicaciones locales

Integrada en la aplicación proxy permite publicar las aplicaciones locales a los usuarios de ambas coherentes acceder experiencia con la aplicación de nube moderna y las ventajas de las capacidades de supervisión, informes y seguridad de Azure AD.

## <a name="reporting-and-monitoring"></a>Supervisión y generación de informes

Azure AD proporciona funciones que le permite saber de supervisión y generación de informes integradas previamente quién tiene acceso a las aplicaciones que utilicen realmente.

## <a name="related-capabilities"></a>Funciones relacionadas

Azure AD para proteger sus aplicaciones con las directivas de acceso granulares y AMF previamente integrada. Para obtener más información acerca de Azure AMF consulte [AMF de Azure](https://azure.microsoft.com/services/multi-factor-authentication/).

## <a name="getting-started"></a>Introducción

Para empezar a integrar aplicaciones con Azure AD, eche un vistazo a la [Guía de introducción a la integración Azure Active Directory con aplicaciones de introducción](active-directory-integrating-applications-getting-started.md).

## <a name="see-also"></a>Vea también

[Índice de artículos de administración de aplicaciones de Azure Active Directory](active-directory-apps-index.md)

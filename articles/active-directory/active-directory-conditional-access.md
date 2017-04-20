<Properties
    pageTitle="Acceso condicional de Azure Active Directory | Microsoft Azure"  
    description="Usar el control de acceso condicional de Azure Active Directory para comprobar condiciones específicas cuando autenticar para tener acceso a las aplicaciones."  
    services="active-directory"
    keywords="acceso condicional a aplicaciones, acceso condicional con Azure AD, acceso seguro a los recursos de empresa, las directivas de acceso condicional"
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
    ms.date="09/21/2016"
    ms.author="markvi"/>


# <a name="conditional-access-in-azure-active-directory"></a>Acceso condicional de Azure Active Directory   

Las capacidades de control de acceso condicional de Azure Active Directory (AD Azure) ofrecen formas muy sencillas para ayudar a proteger recursos en la nube y locales. Las directivas de acceso condicional como autenticación multifactor puede ayudar a evitar el riesgo de robo y phished credenciales. Otras directivas de acceso condicional pueden ayudar a proteger los datos de su organización. Por ejemplo, además de credenciales, es posible que tenga una directiva que solo los dispositivos que se ha inscrito en un sistema de administración de dispositivos móviles como Microsoft Intune puede acceder a los servicios confidencial de su organización.


## <a name="prerequisites"></a>Requisitos previos

Acceso condicional de Azure AD es una característica de [Azure Active Directory Premium](http://www.microsoft.com/identity). Cada usuario que tiene acceso a una aplicación que tiene las directivas de acceso condicional aplicadas debe tener una licencia de Azure AD Premium. Puede obtener más información sobre los requisitos de licencia de [informe de usuario no tiene licencia](https://aka.ms/utc5ix).


## <a name="how-is-conditional-access-control-enforced"></a>¿Cómo se aplica el control de acceso condicional?  

Con control de acceso condicional en su lugar, Azure AD comprueba si las condiciones específicas de la configuración para que un usuario tener acceso a una aplicación. Después de que se cumplen los requisitos de acceso, el usuario se autentica y puede tener acceso a la aplicación.  

![Información general de acceso condicional](./media/active-directory-conditional-access/conditionalaccess-overview.png)

## <a name="conditions"></a>Condiciones

Estas son las condiciones que puede incluir en una directiva de acceso condicional:

- **Pertenencia a grupos**. Controlar el acceso de un usuario según la pertenencia a un grupo.

- **Ubicación**. Utilice la ubicación del usuario para la autenticación multifactor de desencadenador y controles de bloque cuando un usuario no está en una red de confianza.

- **Plataforma de dispositivos**. Usar la plataforma de dispositivo, como iOS, Android, Windows Mobile o Windows, como una condición para aplicar la directiva.

- **Dispositivo habilitado**. Estado del dispositivo, se valida si habilitado o deshabilitado, durante la evaluación de la directiva de dispositivo. Si deshabilita un dispositivo de pérdida o robo en el directorio, ya no pueden satisfacer requisitos de la directiva.

- **Riesgo de inicio de sesión y usuario**. Puede usar la [protección de identidad de Azure AD](active-directory-identityprotection.md) para las directivas de acceso condicional riesgo. Directivas de riesgo de acceso condicional le ayudarán a la protección de avance de la organización basándose en eventos de riesgos y actividades de inicio de sesión inusuales.


## <a name="controls"></a>Controles

Estos son los controles que puede usar para aplicar una directiva de acceso condicional:

- **Autenticación multifactor**. Puede requerir autenticación segura a través de la autenticación multifactor. Puede usar la autenticación multifactor con autenticación multifactor de Azure o mediante un proveedor de servicios de autenticación multifactor local, junto con los servicios de federación de Active Directory (AD FS). Usar autenticación multifactor le ayuda a proteger recursos de accediendo a un usuario no autorizado que podría ha obtenido acceso a las credenciales de un usuario válido.

- **Bloque**. Puede aplicar condiciones como ubicación de usuario para bloquear el acceso de usuario. Por ejemplo, puede bloquear el acceso cuando un usuario no está en una red de confianza.

- **Dispositivos compatibles con**. Puede establecer directivas de acceso condicional en el nivel de dispositivo. Puede configurar una directiva para que solo los equipos unidos a un dominio o dispositivos móviles que se ha inscrito en una aplicación de administración de dispositivos móviles, pueden tener acceso a los recursos de su organización. Por ejemplo, puede usar Intune para comprobar la compatibilidad del dispositivo y, a continuación, informar a Azure AD para la aplicación cuando el usuario intenta obtener acceso a una aplicación. Para obtener instrucciones detalladas acerca de cómo usar Intune para proteger los datos y aplicaciones, vea [proteger aplicaciones y datos con Microsoft Intune](https://docs.microsoft.com/intune/deploy-use/protect-apps-and-data-with-microsoft-intune). También puede usar Intune para aplicar la protección de datos para los dispositivos de pérdida o robados. Para obtener más información, vea [ayudar a proteger sus datos con borrado selectivo o completa mediante Intune de Microsoft](https://docs.microsoft.com/intune/deploy-use/use-remote-wipe-to-help-protect-data-using-microsoft-intune).

## <a name="applications"></a>Aplicaciones

Puede aplicar una directiva de acceso condicional en el nivel de aplicación. Establecer niveles de acceso para aplicaciones y servicios en la nube o local. La directiva se aplica directamente al sitio Web o servicio. Se aplica la directiva de acceso al explorador y a las aplicaciones que tienen acceso al servicio.


## <a name="device-based-conditional-access"></a>Acceso condicional basado en el dispositivo

Puede restringir el acceso a las aplicaciones desde dispositivos que están registrados con Azure AD y que cumplan condiciones específicas. Acceso condicional basado en dispositivos protege los recursos de la organización de los usuarios que intenten tener acceso a los recursos de:

- Dispositivos desconocidos o no administrados.
- Dispositivos que no cumplan las directivas de seguridad de su organización.

Puede establecer directivas basadas en estos requisitos:

- **Dispositivos de dominio**. Establecer una directiva para restringir el acceso a los dispositivos que se unen a un dominio de Active Directory local y que están registrados con Azure AD. Esta directiva se aplica a sobremesa, portátiles y las tabletas de empresa de Windows.
Para obtener más información acerca de cómo configurar el registro automático del dominio dispositivos con Azure AD, vea [Configurar el registro automático de Windows dominio dispositivos con Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md).

- **Dispositivos compatibles con**. Establecer una directiva para restringir el acceso a los dispositivos que se marcan **compatible** en el directorio del sistema de administración. Esta directiva garantiza que sólo los dispositivos que cumplen las directivas de seguridad como Forzar cifrado de archivos en un dispositivo tienen permiso de acceso. Puede usar esta directiva para restringir el acceso de los siguientes dispositivos:

    - **Dispositivos de dominio de Windows**. Administrado por centro de configuración de administrador del sistema (en la rama actual) implementado en una configuración híbrida.
    - **Trabajo de Windows 10 Mobile o dispositivos personales**. Administrar mediante Intune o un sistema de administración de dispositivos móviles compatibles de terceros.
    - **dispositivos iOS y Android**. Administrar Intune.


Los usuarios que tengan acceso a aplicaciones que están protegidas por basadas en dispositivos, directiva de entidad emisora de certificados debe tener acceso a la aplicación desde un dispositivo que cumpla los requisitos de la directiva. Acceso denegado si intenta en un dispositivo que no cumple los requisitos de la directiva.

Para obtener información sobre cómo configurar una directiva de autoridad de certificación basada en el dispositivo, en Azure AD, vea [establecer la directiva de acceso condicional basado en el dispositivo para aplicaciones conectadas de Azure Active Directory](active-directory-conditional-access-policy-connected-applications.md).

## <a name="resources"></a>Recursos

Vea los siguientes artículos para obtener más información sobre la configuración de acceso condicional para su organización y categorías de recursos.


### <a name="multi-factor-authentication-and-location-policies"></a>Directivas de ubicación y de autenticación multifactor

- [Introducción a access condicional a aplicaciones Azure AD conectado basado en grupo, la ubicación y las directivas de autenticación multifactor](active-directory-conditional-access-azuread-connected-apps.md)
- [Aplicaciones que son compatibles](active-directory-conditional-access-supported-apps.md)


### <a name="device-based-conditional-access"></a>Acceso condicional basado en el dispositivo

- [Establecer la directiva de acceso condicional basado en el dispositivo para controlar el acceso a las aplicaciones de Azure Active Directory conectado](active-directory-conditional-access-policy-connected-applications.md)
- [Configurar el registro automático de Windows dominio dispositivos con Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md)
- [Solución de problemas de acceso de Azure Active Directory](active-directory-conditional-access-device-remediation.md)
- [Ayudar a proteger los datos en los dispositivos de pérdida o robados mediante Intune de Microsoft](https://docs.microsoft.com/intune/deploy-use/use-remote-wipe-to-help-protect-data-using-microsoft-intune)


### <a name="protect-resources-based-on-sign-in-risk"></a>Proteger recursos basándose en el riesgo de inicio de sesión

-   [Protección de identidad de Azure AD](active-directory-identityprotection.md)

### <a name="next-steps"></a>Pasos siguientes

- [Preguntas más frecuentes de acceso condicional](active-directory-conditional-faqs.md)
- [Referencia técnica](active-directory-conditional-access-technical-reference.md)

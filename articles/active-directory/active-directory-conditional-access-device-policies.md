<properties
    pageTitle="Directivas de dispositivo de acceso condicional para servicios de Office 365 | Microsoft Azure"
    description="Obtener más información sobre cómo basadas en dispositivos condiciones controlar el acceso a servicios de Office 365. Mientras los trabajadores de la información (trabajadores) desea servicios de acceso a Office 365 como Exchange y SharePoint Online en el trabajo o escuela desde sus dispositivos personales, su administrador de TI quiere el acceso a ser secure.IT administradores pueden aprovisionar directivas de dispositivo de acceso condicional para proteger recursos corporativos, mientras que al mismo tiempo que permite que trabajadores en dispositivos compatibles con acceso a los servicios."
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="femila"/>
# <a name="conditional-access-device-policies-for-office-365-services"></a>Directivas de dispositivo de acceso condicional para servicios de Office 365

El término "acceso condicional" tiene muchas condiciones asociadas como usuario autenticado multifactor, autenticado dispositivo, dispositivo compatible con etcetera. En este tema principalmente focusses en condiciones basadas en el dispositivo para controlar el acceso a servicios de Office 365. Mientras los trabajadores de la información (trabajadores) desea servicios de acceso a Office 365 como Exchange y SharePoint Online en el trabajo o escuela desde sus dispositivos personales, su administrador de TI quiere el acceso seguro. Los administradores de TI pueden aprovisionar directivas de dispositivo de acceso condicional para proteger recursos corporativos, mientras que al mismo tiempo que permite que trabajadores en dispositivos compatibles con acceso a los servicios. Pueden configurar directivas de acceso condicional a Office 365 desde el portal de Microsoft Intune acceso condicional.

Azure Active Directory aplica las directivas de acceso condicional para proteger el acceso a servicios de Office 365. Un administrador de inquilinos puede crear una directiva de acceso condicional que bloquea un usuario en un dispositivo no compatible de acceso a un servicio de O365. El usuario debe cumplir las directivas de dispositivo de la empresa antes de que se puede conceder acceso al servicio. Como alternativa, el administrador también puede crear una directiva que requiere que los usuarios solo inscriban sus dispositivos para tener acceso a un servicio de O365. Directivas de podrán aplicadas a todos los usuarios de una organización, o limitadas a algunos grupos de destino y mejoras para incluir los grupos de destino adicionales.

Es un requisito previo para aplicar las directivas de dispositivo a los usuarios para registrar sus dispositivos con el servicio de registro de dispositivo de Azure Active Directory. Puede elegir para habilitar la autenticación multifactor (AMF) para registrar los dispositivos con el servicio de registro de dispositivo de Azure Active Directory. Se recomienda AMF para el servicio de registro de dispositivo de Azure Active Directory. Cuando se habilita AMF, los usuarios con el registro de sus dispositivos con el servicio de registro de dispositivo de Azure Active Directory enfrenta segunda autenticación multifactor.

##<a name="how-does-conditional-access-policy-work"></a>¿Cómo funciona la directiva de acceso condicional?

Cuando un usuario solicita acceso al servicio de O365 desde una plataforma de dispositivo admitido, Azure Active Directory autentica el usuario y el dispositivo desde el que el usuario inicia la solicitud; y concede acceso al servicio solo cuando el usuario se ajusta a la directiva establecida para el servicio. Los usuarios que no tienen su dispositivo inscrito reciben correctoras instrucciones sobre cómo inscribirse y cumplir para tener acceso a los servicios de O365 corporativos. Los usuarios de dispositivos iOS y Android le pedirá que inscriban sus dispositivos con la aplicación de Portal de empresa. Cuando un usuario inscribe su dispositivo, el dispositivo está registrado con Azure Active Directory e inscrito para cumplimiento y administración de dispositivos. Los clientes deberán usar el servicio de registro de dispositivo de Azure Active Directory junto con Microsoft Intune para habilitar la administración de dispositivos móviles para el servicio de Office 365. Inscripción del dispositivo es un requisito previo para que los usuarios de los servicios de acceso a Office 365 cuando se aplican las directivas de dispositivo.

Cuando un usuario inscribe correctamente su dispositivo, el dispositivo se convierte en confiable. Azure Active Directory proporciona solo-inicio de sesión en aplicaciones de access para la empresa y se aplica la directiva de acceso condicional para conceder acceso a una hora de servicio no solo la primera que el usuario solicite acceso, pero cada vez que el usuario solicite acceso de renovar. El usuario se ha denegado acceso a servicios cuando se cambian las credenciales de inicio de sesión, perdido o robo o la directiva no se cumple en el momento de la solicitud de renovación.

## <a name="deployment-considerations"></a>Consideraciones de implementación:
Debe usar el servicio de registro de dispositivo de Azure Active Directory para el registro del dispositivo.

Cuando los usuarios se pueden autenticar en local, es necesario Active Directory Federation Services (AD FS) (1,0 y anteriores). Se producirá un error en la autenticación multifactor para unirse al área de trabajo cuando el proveedor de identidades no es capaz de autenticación multifactor. Por ejemplo, AD FS 2.0 no es multifactor capaz de autenticación. Administrador de inquilinos debe asegurarse de que las instalaciones AD FS es capaz de AMF y está habilitado un método AMF válido, antes de habilitar AMF en servicio de registro de dispositivo de Azure Active Directory. Por ejemplo, AD FS en Windows Server 2012 R2 tiene capacidades MFA. También debe habilitar un método de autenticación válida adicionales (AMF) en el servidor de AD FS antes de habilitar AMF en servicio de registro de dispositivo de Azure Active Directory. Para obtener más información sobre los métodos de AMF admitidos en AD FS, vea Configurar otros métodos de autenticación de AD FS.

## <a name="frequently-asked-questions-faq"></a>Preguntas más frecuentes (P+F)

P: ¿cuál es la directiva de exclusión predeterminada para las plataformas de dispositivo no compatible?

R: en este momento, las directivas de acceso condicional selectivamente se aplican a los usuarios en dispositivos iOS y Android. Las aplicaciones en otras plataformas de dispositivos son, de forma predeterminada, afectados por la directiva de acceso condicional para dispositivos iOS y Android. Administrador de inquilinos, sin embargo, puede reemplazar la directiva global para no permitir el acceso a los usuarios de plataformas no compatibles.
Está en la guía básica para extender la directiva de acceso condicional a los usuarios en otras plataformas, incluidas las ventanas.

P: ¿cuándo se ampliarán directiva de acceso condicional a los servicios de Office 365 a aplicaciones de basado en el explorador (por ejemplo, OWA, SharePoint basados en explorador).

R: en este momento, acceso condicional a los servicios de Office 365 se limita a aplicaciones en el dispositivo. Está en la guía básica para extender la directiva de acceso condicional a los usuarios acceso a los servicios de los exploradores.

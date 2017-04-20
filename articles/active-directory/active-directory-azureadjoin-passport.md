<properties
    pageTitle="Autenticación de identidades sin contraseñas a través de Microsoft Passport | Microsoft Azure"
    description="Proporciona una descripción general de Microsoft Passport e información adicional sobre la implementación de Microsoft Passport."
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

# <a name="authenticating-identities-without-passwords-through-microsoft-passport"></a>Autenticación de identidades sin contraseñas a través de Microsoft Passport

Los métodos de autenticación con contraseñas solo actuales no son suficientes para mantener a los usuarios. Los usuarios reutilización y olvidarán contraseñas. Las contraseñas distinguen breachable, phishable, susceptible a grietas y adivinar. También obtienen difícil recordar y susceptible a ataques como "[pasar el hash](https://technet.microsoft.com/dn785092.aspx)".

## <a name="about-microsoft-passport"></a>Acerca de MSN
Microsoft Passport es una clave privada y pública o el método de autenticación basada en certificados para las organizaciones y consumidores que vaya más allá de contraseñas. Esta forma de autenticación se basa en las credenciales de par de claves que pueden reemplazar las contraseñas y son resistentes a violaciones, robos y suplantación de identidad.

 Microsoft Passport permite a los usuarios autenticar a una cuenta de Microsoft, una cuenta de Active Directory de Windows Server, una cuenta de Microsoft Azure Active Directory (AD Azure) o un servicio de Microsoft que admite la autenticación de Fast identidad en línea (BOBI). Después de una comprobación de dos pasos inicial durante la inscripción de Microsoft Passport, Microsoft Passport está configurado en el dispositivo del usuario y el usuario establece un gesto, que puede ser Windows Hola o un PIN. El usuario proporciona el gesto para verificar su identidad. Windows utiliza Microsoft Passport para autenticar al usuario y ayudarle a tener acceso a los recursos protegidos y servicios.

La clave privada estará disponible únicamente a través de un gesto de usuario de"" como un PIN, biometría o un dispositivo remoto como una tarjeta inteligente que el usuario usa para iniciar sesión en el dispositivo. Esta información está vinculada a un certificado o un par de claves asimétrico. La clave privada es hardware acreditada si el dispositivo tiene un chip de módulo de plataforma segura (TPM). La clave privada nunca abandona el dispositivo.

La clave pública está registrada con Azure Active Directory y Active Directory de Windows Server (para local). Proveedores de identidades (IDPs) validar al usuario mediante la asignación de la clave pública del usuario a la clave privada y proporcionan información de inicio de sesión mediante contraseña de una vez (OTP), PhoneFactor o un mecanismo de notificación diferente.

## <a name="why-enterprises-should-adopt-microsoft-passport"></a>¿Por qué las empresas deben adoptar Microsoft Passport

Habilitando Microsoft Passport empresas pueden realizar sus recursos aún más seguro por:

* La configuración de Microsoft Passport con la opción preferida de hardware. Esto significa que se generan claves en TPM 1.2 o TPM 2.0 cuando esté disponible. Al TPM no está disponible, software genera la clave.

* Definir la complejidad y la longitud del PIN, y si se habilita el uso de Hola a todos en su organización.

* Configuración de Microsoft Passport para admitir escenarios de tarjeta inteligente como usando confianza basada en certificados.

## <a name="how-microsoft-passport-works"></a>¿Cómo funciona Microsoft Passport
1. Se generan claves en el hardware TPM o software. Muchos dispositivos tienen un chip TPM integrado que protege el hardware mediante la integración de las claves de cifrado en dispositivos. TPM 1.2 o TPM 2.0 genera claves o certificados que se crean a partir de las teclas generadas.

2. TPM certifica estas teclas hardware enlazadas.

3. Un gesto solo Desbloquear desbloquea el dispositivo. Este gesto permite el acceso a varios recursos si el dispositivo está unido al dominio o Azure AD unido.

## <a name="how-the-microsoft-passport-lifecycle-works"></a>Cómo funciona el ciclo de vida de Microsoft Passport

![Ciclo de vida de Microsoft Passport](./media/active-directory-azureadjoin/active-directory-azureadjoin-microsoft-passport.png)

El diagrama anterior muestra el par de claves públicas y privadas y la validación por el proveedor de identidades. Cada uno de estos pasos se explica detalladamente aquí:

1. El usuario demuestra su identidad a través de varios métodos de corrección integradas (gestos, las tarjetas inteligentes físicas, autenticación multifactor) y envía esta información a un proveedor de identidades (IDP) como Azure Active Directory o Active Directory local.

2. El dispositivo, a continuación, crea la clave, certifica la clave, le lleva a la parte de esta clave pública, adjunta con las instrucciones de estación, inicia sesión y envía a la IDP para registrar la clave.

4. Tan pronto como el IDP registra la parte de la clave pública, el IDP desafíos del dispositivo para iniciar sesión con la parte de la clave privada.

5. A continuación, el IDP valida y emite el símbolo de autenticación que le permite el acceso de usuarios y el dispositivo los recursos protegidos. IDPs puede escribir aplicaciones entre plataformas o usar la compatibilidad del explorador (a través de la API de JavaScript/Webcrypto) para crear y usar credenciales de Microsoft Passport para sus usuarios.

## <a name="the-deployment-requirements-for-microsoft-passport"></a>Los requisitos de implementación de Microsoft Passport
### <a name="at-the-enterprise-level"></a>En el nivel de empresa

* La empresa tiene una suscripción de Azure.

### <a name="at-the-user-level"></a>En el nivel de usuario

* El equipo del usuario ejecuta Windows 10 Professional o Enterprise.

Para obtener instrucciones detalladas de implementación, vea [Habilitar MSN para el trabajo de la organización](active-directory-azureadjoin-passport-deployment.md).


## <a name="additional-information"></a>Información adicional

* [Windows 10 para la empresa: formas de usar dispositivos para el trabajo](active-directory-azureadjoin-windows10-devices-overview.md)
* [Amplía las capacidades de nube para Windows 10 dispositivos a través de Azure Active Directory unirse](active-directory-azureadjoin-user-upgrade.md)
* [Obtenga información sobre escenarios de uso para unirse a AD de Azure](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Conectar dispositivos unido al dominio con Azure AD para experiencias de Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configurar combinación de Azure AD](active-directory-azureadjoin-setup.md)

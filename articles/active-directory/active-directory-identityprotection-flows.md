<properties
    pageTitle="Experiencias de inicio de sesión con protección de la identidad de Azure AD | Microsoft Azure"
    description="Proporciona una descripción general de la experiencia del usuario cuando protección de identidad tiene mitigar o corrigen un usuario o cuando se requiere la autenticación multifactor por una directiva."
    services="active-directory"
    keywords="protección de la identidad de Azure directorio activo, detección de aplicación de nube, administración de aplicaciones, seguridad, riesgo, nivel de riesgo, vulnerabilidad, directiva de seguridad"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/16/2016"
    ms.author="markvi"/>

# <a name="sign-in-experiences-with-azure-ad-identity-protection"></a>Experiencias de inicio de sesión con protección de la identidad de Azure AD

Azure Active Directory identidad protección, puede:

- requerir que los usuarios registrar con autenticación multifactor

- controlar los inicios de sesión con riesgos y usuarios comprometidos

La respuesta del sistema para estos problemas tiene un gran impacto en la experiencia de inicio de sesión de un usuario porque solo directamente la firma al proporcionar un nombre de usuario y una contraseña no se podrá ya. Se requieren pasos adicionales para obtener un usuario de forma segura en la empresa.

Este tema ofrece una descripción general de la experiencia de inicio de sesión de un usuario para todos los casos que pueden ocurrir.

**Autenticación multifactor**

- Registro de autenticación multifactor



**Iniciar sesión en riesgo**

- Recuperación de inicio de sesión con riesgos

- Bloquear el inicio de sesión con riesgos

- Registro de autenticación multifactor durante un inicio de sesión con riesgos
 

**Usuario en riesgo**

- Recuperación de cuenta comprometida

- Cuenta comprometida bloqueado




## <a name="multi-factor-authentication-registration"></a>Registro de autenticación multifactor

La mejor experiencia de usuario en ambos casos, el flujo de recuperación de cuenta comprometida y el riesgo flujo de inicio de sesión, es cuando el usuario puede recuperar automáticamente. Si los usuarios están registrados para la autenticación multifactor, ya tiene un número de teléfono asociado con su cuenta que puede utilizarse para pasar los desafíos de seguridad. Ninguna participación de escritorio o el Administrador de la Ayuda es necesaria para recuperar de compromiso de cuenta. Por lo tanto, es muy recomendable a los usuarios registrados para la autenticación multifactor. 

Los administradores pueden:

- establecer una directiva que exige a los usuarios a configurar sus cuentas para comprobar la seguridad adicional. 
- Permitir omitiendo el registro de autenticación multifactor hasta 30 días, en caso de desean conceder a los usuarios un período de gracia antes de registrar.

**El registro de autenticación multifactor tiene tres pasos:**

1. En el primer paso, el usuario obtiene una notificación sobre la necesidad de configurar la cuenta para la autenticación multifactor. 

    ![Corrección] (./media/active-directory-identityprotection-flows/140.png "Corrección")


2. Para configurar la autenticación multifactor, necesitará que el sistema sepa cómo desea ponerse en contacto con.

    ![Corrección] (./media/active-directory-identityprotection-flows/141.png "Corrección")
 
3. El sistema envía un desafío a usted y debe responder.

    ![Corrección] (./media/active-directory-identityprotection-flows/142.png "Corrección")

 



## <a name="risky-sign-in-recovery"></a>Recuperación de inicio de sesión con riesgos

Cuando un administrador ha configurado una directiva para los riesgos de inicio de sesión, los usuarios afectados le notificará cuando intenta iniciar sesión. 

**El flujo de inicio de sesión arriesgado tiene dos pasos:** 

1. El usuario se le informará de que se ha detectado la algo inusual sobre su sesión, por ejemplo, iniciar sesión desde una nueva ubicación, dispositivo o aplicación. 

    ![Corrección] (./media/active-directory-identityprotection-flows/120.png "Corrección")

2. El usuario es necesario para demostrar su identidad y resolver un desafío de seguridad. Si el usuario está registrado con autenticación multifactor necesitan ida y vuelta un código de seguridad a su número de teléfono. Dado que ésta es solo un inicio de sesión con riesgos y no una cuenta comprometida, el usuario no tenga que cambiar la contraseña en este flujo. 

    ![Corrección] (./media/active-directory-identityprotection-flows/121.png "Corrección")



 
## <a name="risky-sign-in-blocked"></a>Bloquear el inicio de sesión con riesgos
Los administradores también puede establecer una directiva de inicio de sesión de riesgo para bloquear a los usuarios al iniciar sesión en función del nivel de riesgo. Para obtener desbloqueada, los usuarios finales deben ponerse en contacto con un administrador o servicio de asistencia, o puede intentar iniciar sesión desde un dispositivo o ubicación familiar. No es una opción en este caso las recuperación automática por autenticación multifactor de resolución.

![Corrección] (./media/active-directory-identityprotection-flows/200.png "Corrección")




## <a name="compromised-account-recovery"></a>Recuperación de cuenta comprometida

Cuando se ha configurado una directiva de seguridad de usuario riesgo, el riesgo de usuarios que cumplen el usuario especificado en la directiva de nivel (y, por tanto, se supone comprometido) debe pasar por el flujo de recuperación de compromiso de usuario antes de que puedan iniciar sesión. 

**El flujo de recuperación de compromiso de usuario tiene tres pasos:**

1. El usuario se le informará de que su seguridad de la cuenta está en riesgo debido a la actividad sospechosa o pérdidas credenciales.

    ![Corrección] (./media/active-directory-identityprotection-flows/101.png "Corrección")

2.  El usuario es necesario para demostrar su identidad y resolver un desafío de seguridad. Si el usuario está registrado con autenticación multifactor automática pueden recuperar se vean comprometidos. Tendrá que acudir un código de seguridad a su número de teléfono. 

    ![Corrección] (./media/active-directory-identityprotection-flows/110.png "Corrección")


3.  Por último, el usuario se obliga a cambiar su contraseña, ya que otra persona puede haber tenido acceso a su cuenta. Capturas de pantalla de esta experiencia están por debajo.
 
    ![Corrección] (./media/active-directory-identityprotection-flows/111.png "Corrección")



## <a name="compromised-account-blocked"></a>Cuenta comprometida bloqueado 

Para obtener un usuario que se ha bloqueado por una directiva de seguridad de riesgo de usuario desbloqueada, el usuario debe ponerse en contacto con un administrador o de asistencia. No es una opción en este caso las recuperación automática por autenticación multifactor de resolución.


![Corrección] (./media/active-directory-identityprotection-flows/104.png "Corrección")



 
## <a name="reset-password"></a>Restablecer la contraseña

Si se bloquean en peligro de los usuarios de inicio de sesión, un administrador puede generar una contraseña temporal para ellos. Los usuarios deben cambiar su contraseña durante un inicio de sesión siguiente.

![Corrección] (./media/active-directory-identityprotection-flows/160.png "Corrección")


 




 

## <a name="see-also"></a>Vea también

- [Protección de la identidad de Azure Active Directory](active-directory-identityprotection.md) 
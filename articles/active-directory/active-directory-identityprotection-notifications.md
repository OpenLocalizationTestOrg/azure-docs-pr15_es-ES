<properties
    pageTitle="Notificaciones de protección de identidad de Active Directory Azure | Microsoft Azure"
    description="Obtenga información sobre cómo notificaciones admiten las actividades de investigación."
    services="active-directory"
    keywords="protección de la identidad de Azure directorio activo, detección de aplicación de nube, administración de aplicaciones, seguridad, riesgo, nivel de riesgo, vulnerabilidad, directiva de seguridad"
    documentationCenter=""
    authors="MarkusVi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="markvi"/>

#<a name="azure-active-directory-identity-protection-notifications"></a>Notificaciones de protección de identidad de Active Directory de Azure 


Protección de identidades de AD Azure envía dos tipos de mensajes de correo electrónico de notificación automatizada para ayudarle a administrar el riesgo de usuario y eventos de riesgo:

- Usuario comprometido alerta por correo electrónico

- Correo electrónico del resumen semanal

## <a name="user-compromised-alert-email"></a>Usuario comprometido alerta por correo electrónico

Una alerta de correo electrónico en peligro de usuario se genera cuando protección de Azure AD identidad identifica una cuenta como comprometido. El correo electrónico incluye un vínculo a los usuarios de marcado para el informe de riesgo en el panel de protección de la identidad. Le recomendamos que investigue inmediatamente notificaciones de comprometido.


## <a name="weekly-digest-email"></a>Correo electrónico del resumen semanal

El correo electrónico del resumen semanal contiene un resumen de los eventos de riesgo.<br>
Incluye:

- Usuarios de riesgo
- Actividades sospechosas
- Vulnerabilidad detectado
- Vínculos a los informes relacionados en protección de identidad


<br>
![Corrección](./media/active-directory-identityprotection-notifications/400.png "Remediation")
<br> 

Puede elegir enviar un correo electrónico con resumen semanal.
<br><br>
![Riesgos de usuario](./media/active-directory-identityprotection-notifications/62.png "User risks")
<br>
 

**Para abrir el cuadro de diálogo de configuración**:

1. En el módulo de **identidad de Azure AD protección** , haga clic en **configuración**.
<br><br>
![Directiva de riesgo de usuario](./media/active-directory-identityprotection-notifications/401.png "User risk policy")
<br>

2. En la sección **General** , haga clic en **notificaciones**.
<br><br>
![Directiva de riesgo de usuario](./media/active-directory-identityprotection-notifications/405.png "User risk policy")
<br>




## <a name="see-also"></a>Vea también

- [Protección de la identidad de Azure Active Directory](active-directory-identityprotection.md) 


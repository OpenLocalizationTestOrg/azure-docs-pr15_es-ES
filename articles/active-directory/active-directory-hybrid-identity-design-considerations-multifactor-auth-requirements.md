<properties
    pageTitle="Azure Active Directory híbrido identidad consideraciones de diseño - determinar los requisitos de autenticación multifactor"
    description="Con control de acceso condicional Azure Active Directory comprueba las condiciones específicas que elegir para autenticar al usuario y antes de permitir el acceso a la aplicación. Una vez que se cumplen las condiciones, el usuario autenticado y permite el acceso a la aplicación."
    documentationCenter=""
    services="active-directory"
    authors="femila"
    manager="billmath"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
    ms.date="08/08/2016"
    ms.author="billmath"/>

# <a name="determine-multi-factor-authentication-requirements-for-your-hybrid-identity-solution"></a>Determinar los requisitos de autenticación multifactor para la solución de identidad híbrido

En este mundo de la movilidad con usuarios que tienen acceso a datos y aplicaciones en la nube y desde cualquier dispositivo, proteger esta información se ha convertido en fundamental.  Todos los días hay un nuevo título sobre una infracción de seguridad.  Aunque no hay ninguna garantía frente a causa de las mismas, la autenticación multifactor, proporciona una capa adicional de seguridad para ayudar a evitar estos problemas.
Comience por evaluar los requisitos de las organizaciones con autenticación multifactor. Es decir, lo que la organización desea proteger.  Esta evaluación es importante para definir los requisitos técnicos de configuración y activación de los usuarios de las organizaciones con autenticación multifactor.

>[AZURE.NOTE]
Si no está familiarizado con AMF y qué hace, se recomienda que lea el artículo [¿Qué es la autenticación multifactor de Azure?](../multi-factor-authentication/multi-factor-authentication.md) antes de continuar leyendo esta sección.

Asegúrese de que responda a la siguiente:

- ¿Su empresa está intentando proteger aplicaciones de Microsoft? 
- ¿Cómo se publican estas aplicaciones?
- ¿Su empresa ofrece acceso remoto para permitir que los empleados tener acceso a aplicaciones local?

Si es así, ¿qué tipo de acceso remoto? También debe evaluar donde se encuentran los usuarios que tienen acceso a estas aplicaciones. Esta evaluación es otro paso importante para definir la estrategia de autenticación multifactor apropiada. Asegúrese de que responda a las preguntas siguientes:

- ¿Dónde van los usuarios ubicar?
- ¿Se pueden encontrar en cualquier lugar?
- ¿Su empresa desea establecer restricciones de acuerdo con la ubicación del usuario?

Una vez que comprenda estos requisitos, es importante también evaluar los requisitos del usuario para la autenticación multifactor. Esta evaluación es importante porque se define los requisitos para implementar la autenticación multifactor. Asegúrese de que responda a las preguntas siguientes:

- ¿Está familiarizado con autenticación multifactor los usuarios?
- ¿Será necesarios para permitir la autenticación adicional algunos usos?  
 - ¿En caso afirmativo, todo el tiempo, cuando procedentes de redes externas, o acceso a aplicaciones específicas, o en otras condiciones?
- ¿Los usuarios requieren formación sobre cómo configurar e implementar la autenticación multifactor?
- ¿Cuáles son los escenarios clave que su compañía desea habilitar la autenticación multifactor para sus usuarios?

Después de responder a las preguntas anteriores, podrá comprender si hay autenticación multifactor que ya se ha implementado en local. Esta evaluación es importante para definir los requisitos técnicos de configuración y activación de los usuarios de las organizaciones con autenticación multifactor. Asegúrese de que responda a las preguntas siguientes:

- ¿Su empresa necesita proteger cuentas con privilegios con AMF?
- ¿Su empresa necesita habilitar AMF para algunas aplicaciones por motivos de cumplimiento?
- ¿Qué necesita habilitar AMF para todos los usuarios de estas aplicaciones o solo los administradores de su empresa?
- ¿Necesita tienen AMF siempre habilitado o solo cuando los usuarios han iniciado fuera de su red corporativa?


## <a name="next-steps"></a>Pasos siguientes
[Definir una estrategia de adopción de identidad híbrido](active-directory-hybrid-identity-design-considerations-identity-adoption-strategy.md)


## <a name="see-also"></a>Vea también
[Información general de las consideraciones de diseño](active-directory-hybrid-identity-design-considerations-overview.md)

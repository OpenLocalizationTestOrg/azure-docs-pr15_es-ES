<properties
    pageTitle="Azure Active Directory híbrido identidad consideraciones de diseño - determinar los requisitos de administración de contenido | Microsoft Azure"
    description="Proporciona información sobre cómo determinar los requisitos de administración de contenido de su empresa. Normalmente cuando un usuario tiene su propio dispositivo podía tener también varias credenciales que se alternos según la aplicación que usa. Es importante diferenciar qué contenido se creó con las credenciales personales frente a los creados con credenciales corporativas. La solución de identidad podrá interactuar con cloud services para proporcionar una experiencia perfecta para el usuario final mientras garantizar su privacidad y aumentar la protección contra la pérdida de datos."
    documentationCenter=""
    services="active-directory"
    authors="billmath"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
    ms.date="08/08/2016"
    ms.author="billmath"/>

# <a name="determine-content-management-requirements-for-your-hybrid-identity-solution"></a>Determinar los requisitos de administración de contenido para la solución de identidad híbrido

Descripción de los requisitos de administración de contenido para su empresa directa puede afectar a su decisión sobre la solución de identidad híbrido para usar. Con la proliferación de varios dispositivos y la capacidad de los usuarios para que aparezca sus propio dispositivos ([BYOD](http://aka.ms/byodcg)), la empresa debe proteger sus propios datos pero también debe intacto privacidad del usuario. Normalmente cuando un usuario tiene su propio dispositivo podía tener también varias credenciales que se alternos según la aplicación que usa. Es importante diferenciar qué contenido se creó con las credenciales personales frente a los creados con credenciales corporativas. La solución de identidad podrá interactuar con cloud services para proporcionar una experiencia perfecta para el usuario final mientras garantizar su privacidad y aumentar la protección contra la pérdida de datos. 

Distintos controles técnicos utilizarán su solución de identidad para proporcionar administración de contenido, como se muestra en la figura siguiente:
 
![](./media/hybrid-id-design-considerations/securitycontrols.png)

**Controles de seguridad que se pueden sacar provecho de su sistema de administración de identidades**

En general, los requisitos de administración de contenido aprovechen su sistema de administración de identidades en las siguientes áreas:

- Declaración de privacidad: identificar el usuario que es propietario de un recurso y los controles adecuados para mantener la integridad de la aplicación.
- Clasificación de datos: identificar el usuario o grupo y nivel de acceso a un objeto según su clasificación. 
- Protección contra la pérdida de datos: responsable de protección de datos para evitar la pérdida de controles de seguridad necesitará interactuar con el sistema de identidad para validar la identidad del usuario. Esto también es importante para el propósito de rastro de auditoría.

>[AZURE.NOTE]
Lea la [clasificación de datos para la disponibilidad de la nube](http://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) para obtener más información acerca de prácticas recomendadas y directrices para la clasificación de datos.

Al planear la solución de identidad híbrido asegurarse de que se responden las preguntas siguientes según las necesidades de su organización:

- ¿Su empresa tiene controles de seguridad en el lugar para exigir la privacidad de los datos?
 - ¿En caso afirmativo, los controles de seguridad podrán integrar con la solución de identidad híbrida que va a adoptar?
- ¿Su empresa utiliza la clasificación de datos?
 - ¿Si es así, la solución actual puede integrar con la solución de identidad híbrida que va a adoptar?
- ¿Su compañía tiene actualmente cualquier solución de filtrado de datos? 
 - ¿Si es así, la solución actual puede integrar con la solución de identidad híbrida que va a adoptar?
- ¿Su empresa necesita auditar el acceso a los recursos?
 - Si es así, ¿qué tipo de recursos?
 - ¿En caso afirmativo, qué nivel de información es necesario?
 - ¿Si es así, debe residen el registro de auditoría? ¿Local o en la nube?
- ¿Su empresa necesita cifrar los mensajes de correo electrónico que contengan datos confidenciales (números de seguridad social, números de tarjeta de crédito, etcetera)?
- ¿Su empresa necesita cifrar todos los documentos o contenido compartido con socios externos?
- ¿Su empresa necesita aplicar directivas corporativas en determinados tipos de mensajes de correo electrónico (sin responder a todos, no reenviar)?
 
>[AZURE.NOTE]
Asegúrese de tomar notas de cada respuesta y entender la razón para la respuesta. [Definir la estrategia de protección de datos](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) irán sobre las opciones disponibles y ventajas y desventajas de cada opción.  Al tener respondidas estas preguntas que seleccione la opción que mejor se adapte a su empresa necesita.


## <a name="next-steps"></a>Pasos siguientes
[Determinar los requisitos de control de acceso](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)

## <a name="see-also"></a>Vea también
[Información general de las consideraciones de diseño](active-directory-hybrid-identity-design-considerations-overview.md)

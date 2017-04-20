
<properties
    pageTitle="Azure Active Directory híbrido identidad consideraciones de diseño - determinar los requisitos de incidencias rResponse | Requisitos de Microsoft Azure "
    description="Determinar las capacidades de supervisión y generación de informes para la solución de identidad híbrido que puede aprovechar mediante TI para realizar acciones para identificar y mitigar un amenazas"
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

# <a name="determine-incident-response-requirements-for-your-hybrid-identity-solution"></a>Determinar los requisitos de respuesta a incidencias para la solución de identidad híbrido

Organizaciones grandes o medianas probablemente tendrá una [respuesta de incidente de seguridad](https://technet.microsoft.com/library/cc700825.aspx) para ayudar a TI realizar acciones como corresponda al nivel de incidente. El sistema de administración de identidades es un componente importante en el proceso de respuesta a incidencias porque se puede usar para ayudar a identificar quién realiza una acción específica en el destino. La solución de identidad híbrida puedan proporcionar las capacidades de supervisión y generación de informes que pueden aprovechar mediante TI para realizar acciones para identificar y mitigar una amenaza. En un plan de respuesta a incidencias típica tendrá las siguientes fases como parte del plan:

1.  Evaluación inicial.
2.  Comunicación de incidencias.
3.  Control de daños y reducción de los riesgos.
4.  Identificación de lo que estaba compromiso y gravedad.
5.  Conservación de pruebas.
6.  Notificación a las partes apropiadas.
7.  Recuperación del sistema.
8.  Documentación.
9.  Evaluación de daños y costo.
10. Revisión de proceso y plan.

Durante la identificación de lo que estaba compromiso y fase de gravedad, será necesario identificar los sistemas que se han comprometido, archivos que se ha tenido acceso y determinan la sensibilidad de los archivos. El sistema de identidad híbrido debería poder cumplir estos requisitos para ayudarle a identificar el usuario que realiza dichos cambios. 

## <a name="monitoring-and-reporting"></a>Supervisión e informes
El sistema de identidad muchas veces también puede ayudar a la fase de evaluación inicial principalmente si ha creado el sistema de auditoría y capacidades de reporting. Durante la evaluación inicial, Administrador de TI debe poder identificar una actividad sospechosa o el sistema debería poder desencadenador automáticamente basándose en una tarea preconfigurada. Muchas actividades podrían indicar un posible ataque, pero en otros casos, un sistema mal configurado puede llevar a un número falsos en un sistema de detección de intrusiones. 

El sistema de administración de identidades debe ayudar a los administradores de TI para identificar y notificar las actividades sospechosas. Normalmente se pueden cumplir estos requisitos técnicos por todos los sistemas de supervisión y tener la capacidad de reporting que puede resaltar las posibles amenazas. Use las siguientes preguntas para ayudarle a diseñar su solución de identidad híbrido teniendo en requisitos de respuesta a incidencias de cuenta:

- ¿Su compañía tiene una respuesta de incidencias de seguridad en su lugar?
 - ¿En caso afirmativo, se usa el sistema de administración de identidades actual como parte del proceso?
- ¿Su empresa necesita identificar sospechosos intentos de inicio de sesión de los usuarios a través de los distintos dispositivos?
- ¿Su empresa necesita detectar posibles comprometido las credenciales de usuario?
- ¿Su empresa necesita auditar acceso y la acción del usuario?
- ¿Su empresa necesita saber cuando un usuario restablece su contraseña?

## <a name="policy-enforcement"></a>Aplicación de directivas

Durante el control de daños y fase de reducción de riesgos, es importante reducir rápidamente los efectos reales y posibles de un ataque. Acción que va a realizar en este momento puede hacer que la diferencia entre un menor y mayor importancia. La respuesta exacta dependerá de su organización y la naturaleza del ataque que se enfrenta. Si la evaluación inicial concluye que se ha comprometido una cuenta, debe aplicar directivas para bloquear esta cuenta. Que es un ejemplo donde se puede aprovechar el sistema de administración de identidades. Use las siguientes preguntas para ayudarle a diseñar su solución de identidad híbrida teniendo en cuenta cómo se aplicarán las directivas para ante un incidente en curso:

- ¿Su empresa tiene directivas en lugar para bloquear usuarios desde access la red si es necesario?
 - Si es así, ¿la solución actual se integra con el sistema de administración de identidades híbrido que va a adoptar?
- ¿Su empresa necesita exigir acceso condicional para los usuarios que están en cuarentena? 
 
>[AZURE.NOTE]
Asegúrese de tomar notas de cada respuesta y entender la razón para la respuesta. [Definir estrategia de protección de datos](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) se enviarán a través de las opciones disponibles y las ventajas y desventajas de cada opción.  Al tener respondidas estas preguntas que seleccione la opción que mejor se adapte a su empresa necesita.

## <a name="next-steps"></a>Pasos siguientes
[Definir la estrategia de protección de datos](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md)

## <a name="see-also"></a>Vea también
[Información general de las consideraciones de diseño](active-directory-hybrid-identity-design-considerations-overview.md)

<properties
    pageTitle="Azure Active Directory híbrido identidad consideraciones de diseño - determinar los requisitos de protección de datos | Microsoft Azure"
    description="Al planear la solución de identidad híbrido, identificar los requisitos de protección de datos para su empresa y qué opciones están disponibles para satisfacer mejor estos requisitos."
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

#<a name="plan-for-enhancing-data-security-through-strong-identity-solution"></a>Plan para mejorar la seguridad de datos a través de solución de identidad sólida

El primer paso para proteger los datos es identificar quién puede tener acceso a datos y como parte de este proceso que debe tener una identidad solución que puede se integra con el sistema para proporcionar funciones de autenticación y la autorización. Autenticación y la autorización se suelen confundir entre sí y sus funciones interpretarse incorrectamente. En realidad, son bastante diferentes, como se muestra en la figura siguiente:

![](./media/hybrid-id-design-considerations/mobile-devicemgt-lifecycle.png)
 
**Etapas de administración de dispositivos móviles**

Al planear la solución de identidad de híbrido debe entender los requisitos de protección de datos de su empresa y qué opciones están disponibles para satisfacer mejor estos requisitos.
 
>[AZURE.NOTE]
Una vez que termine de planificación de la seguridad de datos, revise [determinar los requisitos de autenticación multifactor](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md) para asegurarse de que las selecciones acerca de los requisitos de autenticación multifactor no afectadas por las decisiones que se realicen en esta sección.

## <a name="determine-data-protection-requirements"></a>Determinar los requisitos de protección de datos
En la edad de la movilidad, la mayoría de las empresas tienen un objetivo común: habilitar los usuarios a ser productivo en sus dispositivos móviles mientras local o remota desde cualquier lugar para aumentar la productividad. Aunque podría ser un objetivo común, las empresas que tienen como requisito también será preocupa en relación a la cantidad de amenazas que debe eliminarse para proteger los datos de la compañía y mantener la privacidad del usuario. Cada empresa puede tener requisitos distintos respecto; reglas de cumplimiento diferentes varían según qué sector actúa la empresa le llevará a decisiones de diseño diferente. 

Sin embargo, hay algunos aspectos de seguridad que deberían explorados y validadas, independientemente de la industria, que se explican en la siguiente sección.

## <a name="data-protection-paths"></a>Rutas de protección de datos

![](./media/hybrid-id-design-considerations/data-protection-paths.png)
 
**Rutas de protección de datos**

En el diagrama anterior, el componente de identidad será la primera que comprobar antes de que se tiene acceso a datos. Sin embargo, pueden ser estos datos en los distintos Estados durante el tiempo que buscaba. Cada número de este diagrama representa una ruta en la que pueden estar ubicados en algún momento datos en tiempo. Estos números se explican a continuación:

1. Protección de datos en el nivel de dispositivo.
2. Protección de datos mientras está en tránsito.
3. Protección de datos en el resto de local.
4. Protección de datos en el resto de la nube.

Aunque técnicos controles que le permiten a TI para proteger los datos en cada una de estas fases no directamente ofrece la solución de identidad híbrido, es necesario que la solución de identidad híbrida es capaz de aprovechamiento local y nube de recursos de administración de identidades para identificar al usuario antes de concesión acceso a los datos. Al planear la solución de identidad híbrido asegurarse de que se responden las preguntas siguientes según las necesidades de su organización:

## <a name="data-protection-at-rest"></a>Protección de datos en rest
Independientemente de dónde están los datos al resto (dispositivo, nube o local), es importante realizar una evaluación para comprender las necesidades de la organización en este aspecto. Para esta área, asegúrese de que se le solicita las preguntas siguientes:

- ¿Su empresa necesita proteger los datos almacenados?
 - ¿Si es así, la solución de identidad híbrida capaz de integrarse con su infraestructura local actual?
 - ¿Si es así, la solución de identidad híbrida puede integrar con las cargas de trabajo que se encuentra en la nube?
- ¿Es la administración de identidades de nube puede proteger las credenciales del usuario y otros datos almacenados en la nube?

## <a name="data-protection-in-transit"></a>Protección de datos al transmitirlos
Deben protegerse los datos al transmitirlos entre el dispositivo y el centro de datos o entre el dispositivo y la nube. Sin embargo, está en tránsito necesariamente un proceso de comunicaciones con un componente fuera de su servicio de nube; se mueve internamente, además, por ejemplo, entre dos redes virtuales. Para esta área, asegúrese de que se le solicita las preguntas siguientes:

- ¿Su empresa necesita proteger los datos al transmitirlos?
 - ¿Si es así, la solución de identidad híbrida puede integrar con controles seguros como SSL/TLS?
- ¿La administración de identidades de nube mantener el tráfico y dentro de la tienda de directorio (dentro y entre los centros de datos) que se ha iniciado sesión?


## <a name="compliance"></a>Cumplimiento
Regulaciones, las leyes y los requisitos de cumplimiento de reglamentaciones varían según el sector que pertenece de su empresa. Las empresas de altos sectores regulados deben solucionar problemas de administración de identidades relacionados con problemas de cumplimiento. Normativas como Sarbanes-Oxley (SOX), la portabilidad de seguros de salud y responsabilidad (HIPAA), la Ley Gramm-Leach-Bliley (GLBA) y el pago tarjeta sector seguridad estándar de datos (PCI DSS) son muy estrictas de identidad y acceso. La solución de identidad de híbrida adoptará su compañía debe tener las funciones principales que se cumplen los requisitos de una o varias de estas reglamentaciones. Para esta área, asegúrese de que se le solicita las preguntas siguientes:

- ¿Es la solución de identidad híbrida compatible con los requisitos normativos para su empresa?
- ¿La solución de identidad híbrida incorpora capacidades que permiten a su empresa sean compatibles con requisitos normativos? 
 
>[AZURE.NOTE]
Asegúrese de tomar notas de cada respuesta y entender la razón para la respuesta. [Definir la estrategia de protección de datos](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) irán sobre las opciones disponibles y ventajas y desventajas de cada opción.  Al tener respondidas estas preguntas que seleccione la opción que mejor se adapte a su empresa necesita.

## <a name="next-steps"></a>Pasos siguientes
 [Determinar los requisitos de administración de contenido](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)


## <a name="see-also"></a>Vea también
[Información general de las consideraciones de diseño](active-directory-hybrid-identity-design-considerations-overview.md)

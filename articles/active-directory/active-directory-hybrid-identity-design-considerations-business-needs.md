<properties
    pageTitle="Azure Active Directory híbrido identidad consideraciones de diseño - determinar los requisitos de identidad | Microsoft Azure"
    description="Identificar las necesidades empresariales de la compañía que llevará a definir los requisitos para el diseño de identidad híbrido."
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

# <a name="determine-identity-requirements-for-your-hybrid-identity-solution"></a>Determinar los requisitos de identidad para la solución de identidad híbrido
Es el primer paso para diseñar una solución de identidad híbrido determinar los requisitos de la organización de empresa que se puede aprovechar esta solución.  Híbrido inicia como apoyo (admite todas las otras soluciones de nube mediante la autenticación) e identidad se va a proporcionar funciones nuevas e interesantes que desbloquear nuevas cargas de trabajo para los usuarios.  Estas cargas de trabajo o los servicios que desea que adopten para sus usuarios determinará los requisitos para el diseño de identidad híbrido.  Estos servicios y cargas de trabajo necesitan aprovechar identidad híbrido ambos local y en la nube.  

Debe ir sobre estos aspectos clave de la empresa para comprender qué es ahora un requisito y ¿qué planes de la compañía en el futuro. Si no tiene la visibilidad de la estrategia de largo plazo para el diseño de identidad híbridos, lo más probable es que la solución no estará scalable las necesidades de la empresa crecen y cambian.   T diagrama siguiente se muestra un ejemplo de una arquitectura de identidad híbrido y las cargas de trabajo que se están desbloqueados para los usuarios. Esto es simplemente un ejemplo de todas las posibilidades nuevos puede desbloqueados y suministrados con una estrategia de identidad sólida híbrido. 
 
Algunos componentes que forman parte de la arquitectura de identidad híbrido![](./media/hybrid-id-design-considerations/hybrid-identity-architechture.png)

## <a name="determine-business-needs"></a>Determinar la necesidad de negocio
Cada empresa tiene requisitos distintos, incluso si estas empresas son parte de la misma industria, la empresa real requisitos pueden variar. Aún podrá aprovechar las mejores prácticas de la industria, pero finalmente es necesidades empresariales de la compañía que llevará a definir los requisitos para el diseño de identidad híbrido. 

Asegúrese de que responda a las preguntas siguientes para identificar las necesidades de su empresa:

- ¿Está buscando su empresa para cortar el costo de operaciones de TI?
- ¿Está buscando su empresa para proteger los activos de nube (SaaS aplicaciones, infraestructura)?
- ¿Su empresa busca Modernice su TI?
  - ¿Son los usuarios más móvil y exigentes para crear excepciones en su DMZ para permitir que otro tipo de tráfico para tener acceso a diferentes recursos?
  - ¿Su empresa tiene aplicaciones heredadas que es necesario que se publiquen a estos usuarios moderna pero no son fáciles de reescritura?
  - ¿Su empresa necesita realizar todas estas tareas y poner bajo control al mismo tiempo?
- ¿Está buscando su empresa para proteger las identidades de los usuarios y reducir el riesgo aportando nuevas herramientas que aprovechen la experiencia de Azure seguridad experiencia local Microsoft?
- ¿Su empresa intenta deshacerse de las cuentas "externas" temidas local y los mueve a la nube donde ya no son una amenaza inactiva dentro de su entorno local?

## <a name="analyze-on-premises-identity-infrastructure"></a>Analizar la infraestructura de identidades locales
Ahora que ya tiene una idea sobre los requisitos de negocio de su compañía, debe evaluar su infraestructura de identidad local. Esta evaluación es importante para definir los requisitos técnicos integrar su solución de identidad actual al sistema de administración de identidades de nube. Asegúrese de que responda a las preguntas siguientes:

- ¿Qué solución de autenticación y la autorización lo hace su empresa usar local? 
- ¿Su compañía tiene actualmente los servicios de sincronización local?
- ¿Su empresa utiliza los proveedores de identidades (IdP) de terceros?

También debe tener en cuenta los servicios de nube que podría tener su empresa. Realizar una evaluación para comprender la integración con los modelos de SaaS, IaaS o PaaS en su entorno actual es muy importante. Asegúrese de que responda a las preguntas siguientes durante esta evaluación:
- ¿Su empresa tiene integración con un proveedor de servicios de nube?
- ¿En caso afirmativo, qué servicios están usando?
- ¿Esta integración actualmente en producción o es un programa piloto?


>[AZURE.NOTE]
Si no tiene una asignación precisa de todas las aplicaciones y servicios de nube, puede usar la herramienta de detección de la aplicación de nube. Esta herramienta puede proporcionar el departamento de TI visibilidad empresas de todas las de su organización y aplicaciones de nube de consumidores. Que es más fácil que nunca a descubrir sombra TI de su organización, incluidos los detalles sobre patrones de uso y a los usuarios acceso a sus aplicaciones de nube. Para obtener acceso a esta herramienta, vaya a [https://appdiscovery.azure.com](https://appdiscovery.azure.com/)

## <a name="evaluate-identity-integration-requirements"></a>Evaluar los requisitos de integración de identidad
A continuación, debe evaluar los requisitos de integración de identidad. Esta evaluación es importante para definir los requisitos técnicos de cómo los usuarios se autentican, el aspecto que tendrá la presencia de la organización en la nube, cómo la organización permitirá autorización y lo que va a ser la experiencia del usuario. Asegúrese de que responda a las preguntas siguientes:

- ¿Su organización utilizarán federación, autenticación estándar o ambas?
- ¿Es necesario la federación?  Debido a la siguiente:
 - SSO basado en Kerberos
 - Su compañía tiene un aplicaciones local (ya sea integrado fiesta interna o 3 º) que utiliza SAML o similar capacidades de federación.
 - AMF mediante tarjetas inteligentes. RSA SecurID, etcetera.
 - Reglas de acceso de cliente que respondan a las preguntas siguientes:
     1. ¿Bloquear todo el acceso externo en función de la dirección IP del cliente de Office 365?
     1. ¿Bloquear todo el acceso externo a Office 365, excepto Exchange ActiveSync?
     1. ¿Bloquear todo el acceso externo a Office 365, excepto para aplicaciones basadas en navegador (OWA, SPO)
     1. ¿Bloquear todo el acceso externo a Office 365 para los miembros de grupos de AD designados
- Problemas de seguridad y auditoría
- Inversión ya existente en autenticación federados
- ¿Qué nombre usará nuestra organización para nuestro dominio en la nube?
- ¿La organización tiene un dominio personalizado?
    1. ¿Es ese dominio público y fácilmente puede comprobar a través de DNS?
    1. ¿Si no es así, a continuación, tiene un dominio público que puede usarse para registrar un UPN alternativo en AD?
- ¿Son coherentes para la representación de nube los identificadores de usuario? 
- ¿La organización tiene aplicaciones que requieren la integración con servicios de nube?
- ¿La organización tiene varios dominios y utilizarán todos ellos autenticación estándar o federada?

## <a name="evaluate-applications-that-run-in-your-environment"></a>Evaluar las aplicaciones que se ejecutan en su entorno
Ahora que tiene una idea sobre su local y la infraestructura de nube, debe evaluar las aplicaciones que se ejecutan en estos entornos. Esta evaluación es importante para definir los requisitos técnicos integrar estas aplicaciones en el sistema de administración de identidades de nube. Asegúrese de que responda a las preguntas siguientes:

- ¿Dónde se live nuestras aplicaciones?
- ¿Los usuarios tengan acceso a aplicaciones local?  ¿En la nube? ¿O ambas?
- ¿Existen planes para realizar las cargas de trabajo de aplicación existente y los mueve a la nube?
- ¿Existen planes para desarrollar nuevas aplicaciones que residen en modo local o en la nube que se usan en la nube autenticación?

## <a name="evaluate-user-requirements"></a>Evaluar los requisitos de usuario
También debe evaluar los requisitos de usuario. Esta evaluación es importante para definir los pasos necesarios para incorporación y ayudan a los usuarios mientras realizan la transición a la nube. Asegúrese de que responda a las preguntas siguientes:

- ¿Los usuarios tengan acceso a aplicaciones local?
- ¿Los usuarios tengan acceso a aplicaciones en la nube?
- ¿Cómo normalmente los usuarios iniciar sesión en su entorno local?
- ¿Cómo se usuarios inicie sesión en la nube?

>[AZURE.NOTE]
Asegúrese de tomar notas de cada respuesta y entender la razón para la respuesta. [Requisitos de respuesta a incidencias de determinar](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md) irán sobre las opciones disponibles y ventajas e inconvenientes de cada opción.  Al tener respondidas estas preguntas que seleccione la opción que mejor se adapte a su empresa necesita.

## <a name="next-steps"></a>Pasos siguientes
[Determinar los requisitos de sincronización de directorios](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)

## <a name="see-also"></a>Vea también
[Información general de las consideraciones de diseño](active-directory-hybrid-identity-design-considerations-overview.md)

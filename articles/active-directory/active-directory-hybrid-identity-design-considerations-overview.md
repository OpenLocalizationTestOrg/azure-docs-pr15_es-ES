<properties
    pageTitle="Azure Active Directory híbrido identidad consideraciones de diseño - descripción general | Microsoft Azure"
    description="Información general y mapa de contenido de guía de consideraciones de diseño de identidad híbrido"
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

# <a name="azure-active-directory-hybrid-identity-design-considerations"></a>Consideraciones de diseño de identidad de Azure Active Directory híbrido

Dispositivos basados en consumidor proliferan el mundo corporativo y resultan fáciles de adoptar basada en nube aplicaciones de software como servicio (SaaS). Por tanto, es difícil mantener el control de acceso de los usuarios aplicación centros de datos y nube plataformas interno.  Soluciones de identidad de Microsoft abarcan local y capacidades basadas en la nube, la creación de una identidad de usuario único para la autenticación y autorización para todos los recursos, independientemente de la ubicación. Llamamos a esta identidad híbrido. Hay diferentes diseño y opciones de configuración de identidad híbrido con soluciones de Microsoft y, en algunos casos, puede resultar difícil determinar qué combinación mejor las necesidades de su organización. Esta guía de consideraciones de diseño de identidad híbrido le ayudará a comprender cómo diseñar una solución de identidad híbrido mejor se adapte a su empresa y tecnología necesita para su organización.  Esta guía detallan una serie de pasos y las tareas que puede seguir para ayudarle a diseñar una solución de identidad híbrido que cumpla los requisitos únicos de su organización. A lo largo de los pasos y las tareas, la guía presentará la tecnologías relevantes y opciones de características disponibles para las organizaciones a reunirse funcional y calidad de servicio (por ejemplo, disponibilidad, escalabilidad, rendimiento, administración y seguridad) requisitos de nivel. Específicamente, los objetivos de guía híbrido consideraciones de diseño de identidad son responder a las preguntas siguientes: 

- ¿Qué preguntas debo a formular y responder a un diseño de identidad específica híbrido para un dominio de tecnología o el problema que mejor adapte Mis requisitos a la unidad?
- ¿Qué secuencia de actividades debo completar para diseñar una solución de identidad híbrido para el dominio de tecnología o problema? 
- ¿Qué opciones de configuración y tecnología de identidad híbrido están disponibles para ayudarme a mis necesidades? ¿Cuáles son las ventajas y desventajas de las opciones para que puedo seleccionar la mejor opción para mi negocio?


## <a name="who-is-this-guide-intended-for"></a>¿Quién está destinada esta guía?
 Director, CITO, director identidad arquitectos, arquitectos y arquitectos de TI responsable de diseñar una solución de identidad híbrido para las organizaciones de mediano o grandes.

## <a name="how-can-this-guide-help-you"></a>¿Cómo puede ayudarle esta guía? 
Puede usar a esta guía para comprender cómo diseñar una solución de identidad híbrido que puede integrar un sistema de administración de identidades basada en la nube con la solución de identidad local actual. Gráfico muestra en el siguiente ejemplo una solución de identidad híbrida que permite a los administradores de TI administrar integrar su solución actual de Active Directory de Windows Server ubicado en local con Microsoft Azure Active Directory para permitir a los usuarios usar el inicio de sesión único (SSO) entre las aplicaciones que se encuentra en la nube y locales.

![](./media/hybrid-id-design-considerations/hybridID-example.png)


La ilustración anterior se muestra un ejemplo de una solución de identidad híbrido que sacar provecho de servicios en la nube para integrar con capacidades locales para proporcionar un único para el proceso de autenticación de usuario final y facilitar la administración de los recursos de TI. Aunque puede ser un escenario muy común, es probable que ser diferentes de ejemplo ilustrado en la figura 1 debido a los diferentes requisitos cada híbrido identidad diseño de la organización. Esta guía proporciona una serie de pasos y las tareas que puede seguir para diseñar una solución de identidad híbrido que cumpla los requisitos únicos de su organización. En los siguientes pasos y tareas, la guía presenta las tecnologías relevantes y opciones de características disponibles con funcional y los requisitos de nivel de calidad de servicio para su organización.

**Suposiciones**: tienen experiencia con Windows Server, servicios de dominio de Active Directory y Azure Active Directory. En este documento, se supone que está buscando cómo estas soluciones pueden satisfacer sus necesidades de la empresa en su propio o en una solución integrada.

## <a name="design-considerations-overview"></a>Información general de las consideraciones de diseño
Este documento proporciona un conjunto de pasos y las tareas que puede seguir para diseñar una solución de identidad híbrido que mejor se adapte a sus necesidades. Los pasos se presentan en una secuencia ordenada. Consideraciones de diseño que más adelante pueden requerir que cambie las decisiones que haya hecho en los pasos anteriores, sin embargo, debido a las opciones de diseño en conflicto. Cada intento para alertarle posibles conflictos de diseño en todo el documento. 

Llegarán en el diseño que mejor adapte los requisitos solo después de recorrer los pasos tantas veces como sea necesario para incorporar todas las consideraciones dentro del documento. 

| Fase de identidad híbrido                                             | Lista de temas                                                                                                                                                                                       |
|-------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Determinar los requisitos de identidad                                   | [Determinar la necesidad de negocio](active-directory-hybrid-identity-design-considerations-business-needs.md)<br> [Determinar los requisitos de sincronización de directorios](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)<br> [Determinar los requisitos de autenticación multifactor](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)<br> [Definir una estrategia de adopción de identidad híbrido](active-directory-hybrid-identity-design-considerations-identity-adoption-strategy.md)                       |
| Plan para mejorar la seguridad de datos a través de solución de identidad sólida | [Determinar los requisitos de protección de datos](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md) <br> [Determinar los requisitos de administración de contenido](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)<br> [Determinar los requisitos de control de acceso](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)<br> [Determinar los requisitos de respuesta a incidencias](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md) <br> [Definir la estrategia de protección de datos](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md)  |
| Planificación de ciclo de vida de identidad híbrido                                | [Determinar las tareas de administración de identidades híbrido](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md) <br> [Administración de sincronización](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)<br> [Determinar la estrategia de adopción de administración de identidades híbrido](active-directory-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md) |     


##<a name="download-this-guide"></a>Descargue esta guía
Puede descargar una versión pdf de la Guía de híbrido consideraciones de diseño de identidad de la [Galería de Technet](https://gallery.technet.microsoft.com/Azure-Hybrid-Identity-b06c8288). 

                                                             

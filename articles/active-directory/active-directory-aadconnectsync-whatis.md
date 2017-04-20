<properties
    pageTitle="Sincronización de Azure AD Connect: entender y personalizar la sincronización | Microsoft Azure"
    description="Se explica cómo Azure AD Connect sincronizar works y cómo personalizar."
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/29/2016"
    ms.author="markusvi;andkjell"/>


# <a name="azure-ad-connect-sync-understand-and-customize-synchronization"></a>Sincronización de Azure AD Connect: entender y personalizar la sincronización
Los servicios de sincronización de Azure Active Directory Connect (sincronización de Azure AD Connect) es un componente principal de Azure AD Connect. Se encarga de todas las operaciones que están relacionadas para sincronizar los datos de identidad entre su entorno local y Azure AD. Sincronización de Azure AD Connect es sucesora de la sincronización de directorios, sincronización de Azure AD y el Administrador de identidad de Forefront con el conector de Azure Active Directory configurado.

Este tema es la página principal para la **sincronización de Azure AD Connect** (también denominado **motor de sincronización**) y vínculos a todos los otros temas relacionados con ella. Para obtener vínculos a Azure AD Connect, consulte [integrar las identidades locales con Azure Active Directory](active-directory-aadconnect.md).

El servicio de sincronización consta de dos componentes: el componente de **sincronización de Azure AD Connect** local y servicio de Azure AD denominado **servicio de sincronización de Azure AD Connect**. El servicio está comunes de sincronización de directorios, sincronización AD de Azure y Azure AD Connect.

## <a name="azure-ad-connect-sync-topics"></a>Temas de sincronización de Azure AD Connect

Tema | ¿Qué cubre y cuándo leer
----- | -----
**Aspectos básicos de la sincronización de Azure AD Connect** |
[Descripción de la arquitectura](active-directory-aadconnectsync-understanding-architecture.md) | Para aquellos que está familiarizado con el motor de sincronización y desea obtener información sobre la arquitectura y los términos usados.
[Conceptos técnicos](active-directory-aadconnectsync-technical-concepts.md) | Una versión abreviada del tema arquitectura y brevemente explica los términos usados.
[Conectan topologías para Azure AD](active-directory-aadconnect-topologies.md) | Describe los diferentes topologías y escenarios que admite el motor de sincronización.
**Configuración personalizada** |
[Volver a ejecutar al Asistente para instalación](active-directory-aadconnectsync-installation-wizard.md) | Explica las opciones tiene disponible cuando vuelva a ejecutar el Asistente para instalación de Azure AD Connect.
[Descripción de aprovisionamiento descriptiva](active-directory-aadconnectsync-understanding-declarative-provisioning.md)| Describe el modelo de configuración que se denomina aprovisionamiento descriptiva.
[Descripción de las expresiones de aprovisionamiento descriptiva](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) | Describe la sintaxis para el lenguaje de expresiones utilizado en aprovisionamiento descriptiva.
[Descripción de la configuración predeterminada](active-directory-aadconnectsync-understanding-default-configuration.md)| Describe las reglas de fuera de la caja y la configuración predeterminada. También se describe cómo las reglas trabajar de manera conjunta para que los escenarios de listos trabajar.
[Contactos y usuarios de descripción](active-directory-aadconnectsync-understanding-users-and-contacts.md) | Continúa en el tema anterior y se describe cómo funciona la configuración de los usuarios y contactos juntas, en particular en un entorno de varios bosque.
[Cómo hacer un cambio en la configuración predeterminada](active-directory-aadconnectsync-change-the-configuration.md) | Describe cómo hacer que una configuración común cambiar a flujos de atributos.
[Prácticas recomendadas para cambiar la configuración predeterminada](active-directory-aadconnectsync-best-practices-changing-default-configuration.md) | Limitaciones de compatibilidad y para realizar cambios en la configuración de fábrica.
[Configurar el filtrado](active-directory-aadconnectsync-configure-filtering.md) | Describe las diferentes opciones para cómo limitar los objetos que están sincronizados con Azure AD y paso a paso cómo configurar estas opciones.
**Características y escenarios** |
[Evitar eliminaciones accidentales](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) | Describe la característica de *evitar eliminaciones accidentales* y cómo configurarlo.
[Programador](active-directory-aadconnectsync-feature-scheduler.md) | Describe al programador integrado, que está importando, sincronizar y exportación de datos.
[Implementar la sincronización de contraseña](active-directory-aadconnectsync-implement-password-synchronization.md) | Describe cómo funciona la sincronización de contraseña, cómo implementar y cómo funcionan y solucionar problemas.
[Reescritura de dispositivo](active-directory-aadconnect-feature-device-writeback.md) | Describe cómo funciona la reescritura de dispositivo en Azure AD Connect.
[Extensiones de directorio](active-directory-aadconnectsync-feature-directory-extensions.md) | Describe cómo extender el esquema de Azure AD con sus propios atributos personalizados.
**Servicio de sincronización** |
[Características del servicio de sincronización de Azure AD Connect](active-directory-aadconnectsyncservice-features.md) | Describe el lado de servicio de sincronización y cómo cambiar la configuración de sincronización de Azure AD.
[Resistencia de atributo duplicado](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md) | Describe cómo habilitar y usar la resistencia de valores duplicados atributo **userPrincipalName** y **proxyAddresses** .
**Interfaz de usuario y las operaciones** |
[Administrador de servicios de sincronización](active-directory-aadconnectsync-service-manager-ui.md) | Describe la UI Administrador de servicios de sincronización, incluidos [operaciones](active-directory-aadconnectsync-service-manager-ui-operations.md), [conectores](active-directory-aadconnectsync-service-manager-ui-connectors.md), [Diseñador de Metaverse](active-directory-aadconnectsync-service-manager-ui-mvdesigner.md)y fichas de [Búsqueda en Metaverse](active-directory-aadconnectsync-service-manager-ui-mvsearch.md) .
[Consideraciones y tareas operativas](active-directory-aadconnectsync-operations.md) | Describe cuestiones operativas, como la recuperación.
**Cómo...** |
[Restablecer la cuenta de Azure AD](active-directory-aadconnectsync-howto-azureadaccount.md) | Cómo restablecer las credenciales de la cuenta de servicio utilizada para conectar de sincronización de Azure AD Connect a Azure AD.
**Obtener más información y referencias** |
[Puertos](active-directory-aadconnect-ports.md) | Enumera los puertos que necesita abrir entre el motor de sincronización y los directorios local y Azure AD.
[Atributos sincronizados con Azure Active Directory](active-directory-aadconnectsync-attributes-synchronized.md) | Enumera todos los atributos que se sincronizan entre local AD y Azure AD.
[Referencia de funciones](active-directory-aadconnectsync-functions-reference.md) | Enumera todas las funciones disponibles en aprovisionamiento descriptiva.

## <a name="additional-resources"></a>Recursos adicionales

* [Integrar las identidades locales con Azure Active Directory](active-directory-aadconnect.md)

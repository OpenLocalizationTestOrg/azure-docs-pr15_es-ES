<properties
    pageTitle="Azure AD Connect historial de versiones de estado"
    description="Este documento describe las versiones de estado de conexión de Azure AD y lo que se ha incluido en esas versiones."
    services="active-directory"
    documentationCenter=""
    authors="karavar"
    manager="samueld"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="vakarand"/>

# <a name="azure-ad-connect-health-version-release-history"></a>Azure AD Connect estado: Historial de versiones de versión

El equipo de Azure Active Directory actualiza periódicamente estado de conexión de Azure AD con nuevas características y funcionalidades. En este artículo se enumeran las versiones y las características que se han publicado.

## <a name="october-2016"></a>Octubre de 2016
**Agente de actualización:**
- Agente de Azure AD conectar salud de AD FS \(versión 2.6.408.0\)
    1. Mejoras para detectar direcciones IP de cliente en las solicitudes de autenticación
    2. Correcciones de errores relacionados con las alertas
- Agente de estado de conexión de AD Azure AD DS (versión 2.6.408.0)
    1. Correcciones de errores relacionados con las alertas.
- Agente de Azure AD conectar estado para sincronizar (versión 2.6.353.0) publicado con Azure AD Connect versión 1.1.281.0
    1. Proporcionar los datos necesarios para los informes de errores de sincronización
    2. Correcciones de errores relacionados con las alertas

**Nuevas características de vista previa:**
- Informes de errores de sincronización de Azure AD conectar

**Nuevas características:**
- Azure AD conectar salud de AD FS - el campo dirección IP está disponible en el informe sobre los principales 50 usuarios con el nombre de usuario y la contraseña incorrecta.

## <a name="july-2016"></a>Julio de 2016

**Nuevas características de vista previa:**

- [Azure AD Connect estado para AD DS](active-directory-aadconnect-health-adds.md).


## <a name="january-2016"></a>Enero de 2016


**Agente de actualización:**

- Agente de Azure AD conectar salud de AD FS (versión 2.6.91.1512)


**Nuevas características:**

- [Herramienta de conectividad de prueba de Azure AD conectarse agentes de mantenimiento](active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)


## <a name="november-2015"></a>Noviembre de 2015


**Nuevas características:**

- Compatibilidad con [Control de acceso basado en roles](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control)


**Nuevas características de vista previa:**

- [Azure AD conectar estado de sincronización](active-directory-aadconnect-health-sync.md).

**Problemas fijos:**

- Correcciones de errores para errores vistos durante los registros de agente.

## <a name="september-2015"></a>Septiembre de 2015

**Nuevas características:**

- Incorrecto informe de contraseña de nombre de usuario de AD FS
- Soporte técnico para configurar a no autenticados Proxy HTTP
- Soporte técnico para configurar a agente en Server core
- Mejoras en las alertas de AD FS
- Mejoras en Azure AD conectar agente de mantenimiento de AD FS de conectividad y los datos de carga.


**Problemas fijos:**

- Correcciones de errores en la información de uso para los tipos de Error de AD FS.


## <a name="june-2015"></a>Junio de 2015

**Lanzamiento de estado de conexión de Azure AD para AD FS y Proxy AD FS.**

**Nuevas características:**

- Alertas de supervisión de servidores de AD FS y Proxy AD FS con notificaciones de correo electrónico.
- Facilitar el acceso a la topología de AD FS y patrones en contadores de rendimiento de AD FS.
- Tendencia en solicitudes de tokens correctas en servidores de AD FS agrupados por aplicaciones, métodos de autenticación, solicitar etcetera de ubicación de red.
- Tendencias de solicitud errónea en servidores de AD FS agrupados por aplicaciones, etcetera de tipos de Error.
- Implementación de agente más sencilla con credenciales de administrador Global de Azure AD.  




## <a name="next-steps"></a>Pasos siguientes
Más información sobre [los servicios de sincronización y la infraestructura de identidad Monitor su local en la nube](active-directory-aadconnect-health.md).

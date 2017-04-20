<properties
    pageTitle="Grupo Configuración de directiva y MDM | Microsoft Azure"
    description="Proporciona información sobre la directiva de grupo y un dispositivo móvil configuración de administración (MDM) que debe usarse en su propiedad corporativa dispositivos. Estas directivas se aplican al dispositivo del usuario completo."
    services="active-directory"
    keywords="¿Qué directiva de grupo y configuración de MDM para móviles de estado de empresa, Enterprise movilidad de estado, nube de windows"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor="curtand"/>

<tags
    ms.service="active-directory"  
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="femila"/>

# <a name="group-policy-and-mdm-settings"></a>Configuración de directiva de grupo y MDM

Use estas directivas de grupo y configuración de dispositivo móvil de administración (MDM) solo en su propiedad corporativa dispositivos porque estas directivas se aplican al dispositivo del usuario completo. Aplicar una directiva de MDM para deshabilitar la sincronización de la configuración para un personal, dispositivo de usuario impacto negativo en el uso de ese dispositivo. Además, otras cuentas de usuario en el dispositivo también se verá afectados por la directiva.

Empresas que desea administrar movilidad para personales dispositivos (no administrados) pueden usar el portal de Azure para habilitar o deshabilitar la movilidad, en lugar de usar la directiva de grupo o MDM.
Las tablas siguientes describen las opciones de directiva disponibles.

## <a name="mdm-settings"></a>Configuración de MDM
Aplica la configuración de la directiva MDM para Windows 10 y Windows 10 Mobile.  Soporte técnico de Windows 10 Mobile existe solo para la cuenta de Microsoft según móvil a través de la cuenta de OneDrive de usuario.  Consulte "Dispositivos y extremos" sección para obtener detalles sobre qué dispositivos son compatibles para la sincronización de Azure AD según.

| Nombre                               | Descripción                                                          |
|------------------------------------|----------------------------------------------------------------------|
| Permitir la conexión de cuenta de Microsoft | Permite a los usuarios autenticar con una cuenta de Microsoft en el dispositivo |
| Permitir a mi configuración de sincronización             | Permite a los usuarios cambiar la configuración de Windows y datos de la aplicación; Deshabilitar esta directiva se deshabilita sincronización, así como las copias de seguridad en dispositivos móviles                  |

## <a name="group-policy-settings"></a>Configuración de la directiva de grupo
La configuración de directiva de grupo se aplica a los dispositivos de Windows 10 que se unen a un dominio de Active Directory. La tabla también incluye configuración heredados que aparecerá administrar la configuración de sincronización, pero que no funciona para Enterprise estado movilidad para Windows 10, que se indica con 'Do not use' en la descripción.

| Nombre                                | Descripción |
|-------------------------------------|-------------|
| Cuentas: Cuentas de Microsoft de bloque  |Esta configuración de directiva impide que los usuarios agregar nuevas cuentas de Microsoft en este equipo|
| No sincronizar                         |Impide que los usuarios para trasladar datos de aplicación y la configuración de Windows|
| No se sincronizan personalizar             |Deshabilita la sincronización del grupo temas|
| No sincronizar la configuración del explorador        |Deshabilita la sincronización del grupo de Internet Explorer|
| No se sincronizan las contraseñas               |Deshabilita la sincronización de grupo de contraseñas|
| No se sincronizan otras opciones de configuración de Windows  |Deshabilita la sincronización de grupo de configuración de otras ventanas|
| Personalización del escritorio no se sincronizan |No usar; no tiene ningún efecto|
| No se sincronizarán en conexiones de uso medido  |Deshabilita la movilidad de uso medido conexiones, como celular 3 G|
| No se sincronizan aplicaciones                    |No usar; no tiene ningún efecto|
|Configuración de la aplicación no se sincronizan             |Deshabilita la movilidad de datos de aplicación|
|No sincronizar la configuración de inicio           |No usar; no tiene ningún efecto|


## <a name="related-topics"></a>Temas relacionados
- [Información general de movilidad de estado de Enterprise](active-directory-windows-enterprise-state-roaming-overview.md)
- [Habilitar el estado de enterprise movilidad de Azure Active Directory](active-directory-windows-enterprise-state-roaming-enable.md)
- [Preguntas más frecuentes de movilidad de datos y la configuración](active-directory-windows-enterprise-state-roaming-faqs.md)
- [Referencia de configuración de servicio de roaming de Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)

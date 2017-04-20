<properties
    pageTitle="Actualización de sincronización de directorios y sincronización de Azure AD | Microsoft Azure"
    description="Describe cómo actualizar de sincronización de directorios y sincronización de Azure AD a Azure AD Connect."
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
    ms.date="06/27/2016"
    ms.author="billmath"/>


# <a name="upgrade-windows-azure-active-directory-sync-dirsync-and-azure-active-directory-sync-azure-ad-sync"></a>Actualización de Windows Azure Active Directory Sync ("DirSync") y sincronización de Azure Active Directory ("Azure AD Sync")
Azure AD Connect es la mejor manera de conectarse a su directorio local con Azure AD y Office 365. Este es un buen momento para actualizar a Azure AD Connect desde Windows Azure Active Directory Sync (DirSync) o de sincronización de Azure AD como estas herramientas se han quedado obsoletas y llegarán a fin de soporte de 13 de abril de 2017.

Las herramientas de sincronización de dos identidad que han quedado obsoletas se ofrece para los clientes de bosque único (DirSync) y para varios bosques y otros avanzadas a clientes (Azure AD Sync). Estas herramientas anteriores se han reemplazado con una única solución que está disponible para todos los escenarios: Azure AD Connect. Ofrece nueva funcionalidad, características mejoradas y soporte técnico para escenarios de nuevos. Para poder seguir sincronizar los datos de identidad local a Azure AD y Office 365, se recomienda que actualice a Azure AD Connect.

La última versión de sincronización de directorios se publicó en julio de 2014 y la última versión de sincronización de Azure AD se publicó en mayo de 2015.

## <a name="what-is-azure-ad-connect"></a>¿Qué es Azure AD Connect
Azure AD Connect es la sucesora de sincronización de directorios y sincronización de Azure AD. Combina todos los escenarios de estas dos compatibles. Puede obtener más información sobre esto en [integrar las identidades locales con Azure Active Directory](active-directory-aadconnect.md).

## <a name="deprecation-schedule"></a>Programación de amortización

Fecha | Comentario
 --- | ---
13 de abril de 2016 | Windows Azure Active Directory Sync ("DirSync") and ("Azure AD Sync") de Microsoft Azure Active Directory Sync se presentó como obsoletas.
13 de abril de 2017 | Finaliza la compatibilidad. Los clientes ya no podrán abrir un caso de soporte técnico sin actualizar a Azure AD Connect en primer lugar.

## <a name="how-to-transition-to-azure-ad-connect"></a>Cómo realizar la transición a Azure AD Connect
Si está ejecutando la sincronización de directorios que puede actualizar de dos formas: implementación paralela y actualización en contexto. Se recomienda una actualización en contexto para la mayoría de los clientes y, si tiene un reciente sistema operativo y objetos de menos de 50.000. En los demás casos, se recomienda realizar una implementación paralela donde la configuración de sincronización de directorios se mueve a un nuevo servidor que ejecuta Azure AD Connect.

Si utiliza la sincronización de Azure AD se recomienda una actualización en contexto. Si lo desea, es posible instalar a un servidor de Azure AD Connect nuevo en paralelo y realizar una migración de recorrido desde el servidor de sincronización de Azure AD a Azure AD Connect.

Solución | Escenario
----- | -----
[Actualización de sincronización de directorios](./connect/active-directory-aadconnect-dirsync-upgrade-get-started.md) | <li>Si tiene un servidor de sincronización de directorios existente ya en ejecución.</li>
[Actualización de sincronización de Azure AD](active-directory-aadconnect-upgrade-previous-version.md)| <li>Si está moviendo de Azure AD Sync.</li>

Si desea ver cómo hacer una actualización en contexto de sincronización de directorios para Azure AD Connect, consulte este vídeo 9 de canal:

> [AZURE.VIDEO azure-active-directory-connect-in-place-upgrade-from-legacy-tools]

## <a name="faq"></a>Preguntas más frecuentes
**P: he recibido una notificación de correo electrónico desde el equipo de Azure o un mensaje desde el centro de mensajes de Office 365, pero estoy usando conectar.**  
La notificación también se envió a los clientes que usan Azure AD Connect con un número de versión 1.0. \*.0 (usando una versión 1.1 anterior). Microsoft recomienda a los clientes para estar al día con las versiones de Azure AD Connect. Con la característica de [actualización automática](active-directory-aadconnect-feature-automatic-upgrade.md) le será de 1.1 instalado realmente muy fácil siempre dispone de una versión reciente de Azure AD Connect.

**P: ¿le DirSync/Azure AD Sync dejan de funcionar en 13 de abril de 2017?**  
No. La fecha de cuando estos ya no podrán comunicarse con Azure AD se anunciará en una fecha posterior. Podrá encontrar información en este tema cuando esté disponible.

**P: ¿qué versiones de sincronización de directorios ¿actualizar desde?**  
Es compatible para actualizar desde cualquier versión de sincronización de directorios está aplicando.

**P: ¿qué hay de Azure AD conector para FIM/MIM?**  
El conector de Azure AD **para FIM/MIM ha sido** anunciada como obsoleto. Está en la **característica Inmovilizar**; no se agrega ninguna nueva funcionalidad y no recibe correcciones de errores. Microsoft recomienda a los clientes que usan planear mover desde ella a Azure AD Connect. Se recomienda no iniciar las nuevas implementaciones usarlo. Este conector se anunciará obsoleto en el futuro.

## <a name="additional-resources"></a>Recursos adicionales

* [Integrar las identidades locales con Azure Active Directory](active-directory-aadconnect.md)

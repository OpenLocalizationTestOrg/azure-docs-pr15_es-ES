<properties
    pageTitle="Sincronización de Azure AD Connect: descripción de usuarios y contactos | Microsoft Azure"
    description="Explica que los usuarios y los contactos en la sincronización de Azure AD Connect."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="markusvi;andkjell"/>


# <a name="azure-ad-connect-sync-understanding-users-and-contacts"></a>Sincronización de Azure AD Connect: descripción de usuarios y contactos

Existen varias razones diferentes por tendría varios bosques de Active Directory y hay varias topologías de implementación diferentes. Modelos comunes incluyen una implementación de recursos de la cuenta y GAL sync'ed bosques después de una fusión y adquisición. Pero incluso si hay modelos puros, modelos híbridos son también comunes. La configuración predeterminada de la sincronización de Azure AD Connect no supone ningún modelo concreto pero dependiendo de cómo se ha seleccionado la coincidencia de usuarios en la Guía de instalación, pueden observar comportamientos diferentes.

En este tema, vamos a través de comportamiento de la configuración predeterminada en determinadas topologías. Vamos a través de la configuración y puede utilizarse el Editor de reglas de sincronización para ver la configuración.

Hay algunas reglas generales que supone la configuración:

- Independientemente de qué orden se importa desde el origen de Active Directory, el resultado final debería ser siempre el mismo.
- Una cuenta activa siempre contribuyan información de inicio de sesión, incluidos **userPrincipalName** y **sourceAnchor**.
- Una cuenta deshabilitada contribuyan userPrincipalName y sourceAnchor, a menos que sea un buzón vinculado, si no hay ninguna cuenta activa se encuentra.
- Una cuenta con un buzón vinculado nunca se usará para userPrincipalName y sourceAnchor. Se supone que una cuenta activa se encuentra más adelante.
- Puede proporcionar un objeto de contacto a Azure AD como un contacto o como un usuario. Realmente no sabe hasta que se procesan todos los bosques de Active Directory de origen.

## <a name="contacts"></a>Contactos

Contactos que representa un usuario en un bosque diferente es comunes después de una fusión y adquisición donde una solución GALSync rompe dos o más bosques de Exchange. El objeto de contacto siempre se une desde el espacio de conector a metaverse utilizando el atributo de correo. Si ya existe un objeto de contacto o un objeto de usuario con la misma dirección de correo, se unen los objetos. Esto se configura en la regla **en de AD: unirse a contacto**. También es una regla denominada **en de AD: contacto comunes** con un flujo de atributo a metaverse atributo **tipoobjetoorigen** con la constante **contacto**. Esta regla tiene prioridad muy baja por lo que si cualquier objeto de usuario se ha unido al mismo objeto de metaverso y, después, en la regla **en de AD: usuario comunes** contribuyan el valor para este atributo. Con esta regla, este atributo tendrá el valor si no se ha unido ningún usuario de contacto y el usuario si se ha encontrado al menos un usuario.

Para el aprovisionamiento de un objeto a Azure AD **Salí a AAD: póngase en contacto con unirse a** la regla de salida creará un objeto de contacto, si se establece el atributo de metaverse **tipoobjetoorigen** ponerse en **contacto**. Si está establecido para el **usuario**, a continuación, la regla **Salí a AAD: unirse a usuario** creará un objeto de usuario en su lugar.
Es posible que un objeto se promueve de contacto al usuario cuando se importan y sincronizar más directorios activo de origen.

Por ejemplo, en una topología de GALSync se encontrará los objetos de contacto para todos los usuarios del segundo bosque cuando se importa del primer bosque. Esto fase nuevos objetos de contacto en el conector de AAD. Cuando más adelante importar y sincronizar del segundo bosque, se encontrará los usuarios reales y unirse a ellas a los objetos de metaverso existente. A continuación, se eliminará el objeto de contacto en AAD y crear un nuevo objeto de usuario en su lugar.

Si tiene una topología donde los usuarios y representados como contactos, asegúrese de que selecciona para que coincida con los usuarios en el atributo de correo en la Guía de instalación. Si selecciona otra opción, tendrá una configuración de orden dependientes. Objetos de contacto se unirán siempre en el atributo de correo, pero solo se unirá objetos de usuario en el atributo mail si se selecciona esta opción en la Guía de instalación. Podrían terminar con dos objetos diferentes en el metaverso con el mismo atributo de correo si el objeto de contacto se importó antes del objeto de usuario. Durante la exportación a Azure AD, se producirá un error. Este comportamiento es por diseño e indica datos no válidos o que no se ha identificado correctamente la topología durante la instalación.

## <a name="disabled-accounts"></a>Cuentas deshabilitadas

Cuentas deshabilitadas se sincronizan también a Azure AD. Cuentas deshabilitadas son comunes para representar los recursos de Exchange, por ejemplo las salas de conferencias. La excepción es que los usuarios con un buzón vinculado; como se mencionó anteriormente, estos aprovisionar nunca una cuenta de Azure AD.

La suposición es si se encuentra una cuenta de usuario deshabilitada, no veremos otra cuenta activa más tarde y aprovisionado a Azure AD con el userPrincipalName y sourceAnchor que se encuentra el objeto. En caso de que se unirá a otra cuenta activa al mismo objeto de metaverso, a continuación, su userPrincipalName y sourceAnchor se usará.

## <a name="changing-sourceanchor"></a>Cambiar sourceAnchor

Cuando un objeto se ha exportado a Azure AD, a continuación, no se permite cambiar la sourceAnchor ya. Cuando el objeto se ha exportado el atributo de metaverse **cloudSourceAnchor** se ha configurado con el valor de **sourceAnchor** admitido por Azure AD. Si **sourceAnchor** se cambia y no coinciden con **cloudSourceAnchor**, la regla **Salí a AAD: unirse a usuario** producirá el error **sourceAnchor atributo ha cambiado**. En este caso, la configuración o los datos deben corregirse para que el mismo sourceAnchor está presente en el metaverso nuevo antes de que el objeto se puede sincronizar de nuevo.

## <a name="additional-resources"></a>Recursos adicionales

* [Sincronización de conectar Azure AD: Personalizar las opciones de sincronización](active-directory-aadconnectsync-whatis.md)
* [Integrar las identidades locales con Azure Active Directory](active-directory-aadconnect.md)

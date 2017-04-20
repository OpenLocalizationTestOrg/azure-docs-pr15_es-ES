<properties
    pageTitle="Sincronización de Azure AD Connect: descripción de la configuración predeterminada | Microsoft Azure"
    description="En este artículo se describe la configuración predeterminada de la sincronización de Azure AD Connect."
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
    ms.date="09/01/2016"
    ms.author="billmath"/>

# <a name="azure-ad-connect-sync-understanding-the-default-configuration"></a>Sincronización de Azure AD Connect: descripción de la configuración predeterminada
Este artículo explica las reglas de configuración. Documenta las reglas y cómo estas reglas afectan la configuración. También le guía a través de la configuración predeterminada de sincronización de Azure AD Connect. El objetivo es que el lector de comprende cómo funciona el modelo de configuración, denominado aprovisionamiento descriptiva, en un ejemplo real. En este artículo se supone que ya ha instalado y configurar la sincronización de Azure AD Connect utilizando al Asistente para instalación.

Para conocer los detalles del modelo de configuración, lea la [Descripción de aprovisionamiento descriptiva](active-directory-aadconnectsync-understanding-declarative-provisioning.md).

## <a name="out-of-box-rules-from-on-premises-to-azure-ad"></a>Cuadro de reglas locales a Azure AD
Las siguientes expresiones pueden encontrarse en la configuración de fábrica.

### <a name="user-out-of-box-rules"></a>Reglas de fuera de la caja de usuario
Estas reglas también se aplican al tipo de objeto iNetOrgPerson.

Un objeto de usuario debe cumplir las siguientes acciones para sincronizar:

- Debe tener un sourceAnchor.
- Después de que se ha creado el objeto en Azure AD, no se puede cambiar sourceAnchor. Si el valor es modificado en local, el objeto deja de sincronización hasta que se cambie la sourceAnchor a su valor anterior.
- Debe se el atributo accountEnabled (userAccountControl) rellenado. Con un Active Directory local, este atributo siempre está presente y rellenada.

Los siguientes objetos de usuario son **no** sincronizado con Azure AD:

- `IsPresent([isCriticalSystemObject])`. Garantizar muchos objetos fuera de la caja en Active Directory, como la cuenta de administrador integrada, no están sincronizados.
- `IsPresent([sAMAccountName]) = False`. Asegúrese de que no se sincronizan los objetos de usuario sin el atributo sAMAccountName. En este caso solo prácticamente sucede en un dominio actualizado desde NT4.
- `Left([sAMAccountName], 4) = "AAD_"`, `Left([sAMAccountName], 5) = "MSOL_"`. No sincronizar la cuenta de servicio utilizada por sincronización de Azure AD Connect y sus versiones anteriores.
- No se sincronizan las cuentas de Exchange que no funciona en Exchange Online.
    - `[sAMAccountName] = "SUPPORT_388945a0"`
    - `Left([mailNickname], 14) = "SystemMailbox{"`
    - `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`
    - `(Left([sAMAccountName], 4) = "CAS_" && (InStr([sAMAccountName], "}")> 0))`
- No se sincronizan los objetos que no funciona en Exchange Online.
`CBool(IIF(IsPresent([msExchRecipientTypeDetails]),BitAnd([msExchRecipientTypeDetails],&H21C07000) > 0,NULL))`  
Esta máscara de bits (& H21C07000) ¿filtrar los siguientes objetos:
    - Carpetas públicas habilitadas para correo
    - Buzón del operador de sistema
    - Buzón de base de datos de buzón de correo (buzón del sistema)
    - Grupo de seguridad universal (no se aplican para un usuario, pero está presente por razones de herencia)
    - Grupo no Universal (no se aplican para un usuario, pero está presente por razones de herencia)
    - Plan de buzón
    - Buzón de detección
- `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. No se sincronizan los objetos de víctima de replicación.

Se aplican las siguientes reglas de atributo:

- `sourceAnchor <- IIF([msExchRecipientTypeDetails]=2,NULL,..)`. El atributo sourceAnchor no se ha contribuido desde un buzón vinculado. Se supone que si se ha encontrado un buzón vinculado, la cuenta se ha unido más adelante.
- Solo se sincronizan los atributos si el atributo **mailNickName** tiene un valor, relacionados con Exchange.
- Si hay varios bosques, se consumen atributos en el siguiente orden:
    1. Atributos relacionados con el inicio de sesión (por ejemplo userPrincipalName) reciben contribuciones del bosque con una cuenta habilitada.
    2. Atributos que se encuentra en una GAL de Exchange (lista Global de direcciones) se ha contribuido del bosque con un buzón de Exchange.
    3. Si no se encuentra ningún buzón, estos atributos pueden provenir de cualquier bosque.
    4. Relacionados con Exchange se ha contribuido atributos (atributos técnicos no están visibles en la GAL) del bosque donde `mailNickname ISNOTNULL`.
    5. Si hay varios bosques que se cumplan una de estas reglas, el orden de creación (fecha y hora) de los conectores (bosques) se usa para determinar qué bosque contribuye los atributos.

### <a name="contact-out-of-box-rules"></a>Póngase en contacto con el cuadro de reglas
Un objeto de contacto debe cumplir las siguientes acciones para sincronizar:

- El contacto debe ser habilitados para correo. Verificar con las siguientes reglas:
    - `IsPresent([proxyAddresses]) = True)`. Debe rellenar el atributo proxyAddresses.
    - Una dirección de correo electrónico puede encontrarse en el atributo proxyAddresses o el atributo de correo. La presencia de un @ se usa para comprobar que el contenido es una dirección de correo electrónico. Una de estas dos reglas debe dar como resultada True.
        - `(Contains([proxyAddresses], "SMTP:") > 0) && (InStr(Item([proxyAddresses], Contains([proxyAddresses], "SMTP:")), "@") > 0))`. ¿Existe una entrada con "SMTP:" y si hay, puede un @ se encuentra en la cadena?
        - `(IsPresent([mail]) = True && (InStr([mail], "@") > 0)`. ¿Es el atributo mail rellena y si es así, puede un @ se encuentra en la cadena?

Los siguientes objetos de contacto son **no** sincronizado con Azure AD:

- `IsPresent([isCriticalSystemObject])`. Asegúrese de que no se sincronizan objetos de contacto marcadas como críticas. No debería ser cualquiera con una configuración predeterminada.
- `((InStr([displayName], "(MSOL)") > 0) && (CBool([msExchHideFromAddressLists])))`.
- `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`. Estos objetos no funcionan en Exchange Online.
- `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. No se sincronizan los objetos de víctima de replicación.

### <a name="group-out-of-box-rules"></a>Reglas de cuadro de grupo
Un objeto de grupo debe cumplir las siguientes acciones para sincronizar:

- Debe tener menos de 50.000 miembros. Este número es el número de miembros en el grupo local.
    - Si tiene más miembros antes de la primera vez que inicia la sincronización, el grupo no está sincronizado.
    - Aumenta el número de miembros de cuándo se creó, a continuación, cuando llegue 50.000 miembros deja sincronizar hasta que el recuento de pertenencia es menor que 50.000 de nuevo.
    - Nota: El recuento de 50.000 pertenencia también se aplica mediante Azure AD. No es posible sincronizar los grupos con más miembros aunque modificar o eliminar esta regla.
- Si el grupo es un **Grupo de distribución**, a continuación, también debe ser habilitado para correo. Vea [reglas de cuadro de contacto](#contact-out-of-box-rules) para esta regla se aplica.

Los siguientes objetos de grupo son **no** sincronizado con Azure AD:

- `IsPresent([isCriticalSystemObject])`. Asegúrese de muchos objetos fuera de la caja en Active Directory, como el grupo de administradores integrados, no están sincronizados.
- `[sAMAccountName] = "MSOL_AD_Sync_RichCoexistence"`. Grupo heredado usado por la sincronización de directorios.
- `BitAnd([msExchRecipientTypeDetails],&amp;H40000000)`. Grupo de roles.
- `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. No se sincronizan los objetos de víctima de replicación.

### <a name="foreignsecurityprincipal-out-of-box-rules"></a>Reglas de fuera de la caja de ForeignSecurityPrincipal
FSP se une a "cualquiera" (\*) objeto en el metaverso. En realidad, esta combinación sólo se produce para los usuarios y grupos de seguridad. Esta configuración garantiza que pertenencias entre bosques se resuelve y representan correctamente en Azure AD.

### <a name="computer-out-of-box-rules"></a>Reglas de fuera de la caja de equipo
Un objeto de equipo debe cumplir las siguientes acciones para sincronizar:

- `userCertificate ISNOTNULL`. Sólo los equipos de Windows 10 rellenan este atributo. Todos los objetos de equipo con un valor de este atributo están sincronizados.

## <a name="understanding-the-out-of-box-rules-scenario"></a>Descripción del escenario de cuadro de reglas
En este ejemplo, estamos usando una distribución con una cuenta bosque (A), un bosque de recursos (R) y un directorio de Azure AD.

![Imagen con descripción del escenario](./media/active-directory-aadconnectsync-understanding-default-configuration/scenario.png)

En esta configuración, se supone que hay una cuenta habilitada del bosque de cuenta y una cuenta deshabilitada en el bosque de recursos con un buzón vinculado.

Nuestro objetivo con la configuración predeterminada es:

- Atributos relacionados con el inicio de sesión se sincronizan desde el bosque con la cuenta habilitada.
- Se sincronizan los atributos que se encuentra en la lista global de direcciones (lista Global de direcciones) del bosque con el buzón. Si no se encuentra ningún buzón, se usa cualquier otro bosque.
- Si se encuentra un buzón vinculado, debe encontrarse la cuenta vinculada habilitada para que el objeto que se pueden exportar a Azure AD.

### <a name="synchronization-rule-editor"></a>Editor de reglas de sincronización
Puede ver la configuración y ha cambiado con la herramienta de sincronización de reglas de Editor (SRE) y un acceso directo a ella puede encontrarse en el menú Inicio.

![Icono de Editor de reglas de sincronización](./media/active-directory-aadconnectsync-understanding-default-configuration/sre.png)

El SRE es una herramienta del kit de recursos y se instala con la sincronización de Azure AD Connect. Para poder iniciarlo, debe ser miembro del grupo ADSyncAdmins. Cuando se inicia, verá algo parecido a esto:

![Reglas de sincronización entrante](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulesinbound.png)

En este panel, puede ver todas las reglas de sincronización creadas para su configuración. Cada línea de la tabla es una regla de sincronización. A la izquierda en tipos de regla, se muestran los dos tipos diferentes: entrante y saliente. Entrante y saliente es desde la vista del metaverso. Va principalmente para centrarse en las reglas de entrada en esta introducción. La lista de reglas de sincronización real depende del esquema detectado en AD. En la imagen anterior, el bosque de cuenta (fabrikamonline.com) carece de los servicios, como Exchange y Lync, y no se han creado reglas de sincronización para estos servicios. Sin embargo, en el bosque de recursos (res.fabrikamonline.com) Buscar reglas de sincronización para estos servicios. El contenido de las reglas es diferente según la versión detectada. Por ejemplo, en una implementación de Exchange 2013 hay más flujos de atributo configurados que en Exchange 2010 y 2007.

### <a name="synchronization-rule"></a>Regla de sincronización
Una regla de sincronización es un objeto de configuración con un conjunto de atributos que fluye cuando se cumple una condición. También se utiliza para describir cómo un objeto en un espacio de conector relacionado con un objeto en el metaverso, conocido como **unirse** o **coincide con**. Las reglas de sincronización tienen un valor de prioridad que se indica cómo se relacionan entre sí. Una regla de sincronización con un valor numérico menor tiene una prioridad más alta y un conflicto de flujo de atributo, mayor prioridad gana la resolución de conflictos.

Por ejemplo, mire la regla de sincronización **en de AD: usuario AccountEnabled**. Marcar esta línea en el SRE y seleccione **Editar**.

Puesto que esta regla es una regla de salida del cuadro, recibirá una advertencia al abrir la regla. No debe hacer [cambios en el cuadro de reglas](active-directory-aadconnectsync-best-practices-changing-default-configuration.md), por lo que se le pregunte ¿cuáles son sus intenciones. En este caso, sólo desea ver la regla. Seleccione **No**.

![Sincronización de reglas de advertencia](./media/active-directory-aadconnectsync-understanding-default-configuration/warningeditrule.png)

Una regla de sincronización tiene cuatro secciones de configuración: descripción, especificar el ámbito de filtro, reglas de combinación y transformaciones.

#### <a name="description"></a>Descripción
La primera sección proporciona información básica, como un nombre y una descripción.

![Descripción de la pestaña editor de reglas en sincronización ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncruledescription.png)

También encontrará información sobre qué sistema conectado se relaciona esta regla, el tipo de objeto en el sistema conectado a que se aplica y el tipo de objeto de metaverse. El tipo de objeto de metaverse siempre es independientemente de la persona, cuando el tipo de objeto de origen es un usuario, iNetOrgPerson o contacto. Nunca debe cambiar el tipo de objeto de metaverse para que se crea como un tipo genérico. El tipo de vínculo se puede configurar para unirse, StickyJoin o aprovisionar. Esta configuración funciona junto con la sección reglas de combinación y se describe más adelante.

También puede ver que esta regla de sincronización se usa para la sincronización de la contraseña. Si es un usuario en el ámbito de esta regla de sincronización, la contraseña se ha sincronizado desde local en la nube (suponiendo que ha habilitado la característica de sincronización de contraseña).

#### <a name="scoping-filter"></a>Especificar el ámbito de filtro
La sección especificar el ámbito de filtro se utiliza para configurar cuándo debe aplicarse una regla de sincronización. Dado que el nombre de la regla de sincronización que examina indica sólo se debe aplicar para usuarios habilitados, el ámbito está configurado para el atributo de AD **userAccountControl** no debe tener el bit 2 establecer. Cuando el motor de sincronización encuentra un usuario en Active Directory, se aplica esta regla de sincronización cuando **userAccountControl** es el valor decimal 512 (usuario normal habilitado). No se aplica la regla cuando el usuario tiene **userAccountControl** establecer 514 (usuario normal deshabilitado).

![Especificar el ámbito de pestaña en el editor de reglas de sincronización ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulescopingfilter.png)

El filtro de ámbito tiene cláusulas que se pueden anidar y grupos. Deben satisfacer todas las cláusulas dentro de un grupo aplicar una regla de sincronización. Cuando se definen varios grupos, se debe cumplir al menos un grupo aplicar la regla. Es decir, un operador lógico OR se evalúe entre los grupos y lógico y se evalúa dentro de un grupo. Un ejemplo de esta configuración se puede encontrar en la regla de sincronización saliente **Salí a AAD: unirse a grupo**. Existen varios grupos de filtro de sincronización, por ejemplo, uno para grupos de seguridad (`securityEnabled EQUAL True`) y otro para grupos de distribución (`securityEnabled EQUAL False`).

![Especificar el ámbito de pestaña en el editor de reglas de sincronización ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulescopingfilterout.png)

Esta regla se usa para definir qué grupos deben proporcionar a Azure AD. Grupos de distribución deben tener el correo habilitado para sincronizarse con Azure AD, pero para grupos de seguridad no es necesario un correo electrónico.

#### <a name="join-rules"></a>Unirse a las reglas
En la tercera sección se utiliza para configurar cómo se relacionan los objetos en el espacio del conector con objetos en el metaverso. La regla que miró anteriormente no tiene ninguna configuración para unirse a las reglas, en su lugar se va a analizar **en de AD: unirse a usuario**.

![Unirse a la ficha reglas en el editor de reglas de sincronización ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulejoinrules.png)

El contenido de la regla de combinación depende de la opción correspondiente seleccionada en el Asistente para instalación. Para una regla de entrada, la evaluación se inicia con un objeto en el espacio de conector de origen y cada grupo en las reglas de combinación se evalúa en secuencia. Si se evalúa un objeto de origen para que coincida exactamente un objeto en el metaverso mediante una de las reglas de combinación, se unen los objetos. Si todas las reglas que se han evaluado y no hay ninguna coincidencia, se utiliza el tipo de vínculo en la página Descripción. Si esta configuración se establece en **abastecerse**, se crea un nuevo objeto en el destino, el metaverso. Para crear un nuevo objeto en el metaverso es también conocida como **proyecto** un objeto a metaverse.

Las reglas de combinación sólo se evalúan una vez. Cuando se unen un objeto de espacio de conector y un objeto de metaverso, permanecen combinados como el ámbito de la regla de sincronización es aún satisfecho.

Al evaluar reglas de sincronización, debe ser solo una regla de sincronización con las reglas de combinación definidas en el ámbito. Si se encuentran varias reglas de sincronización con reglas de unión para un objeto, se produce un error. Por este motivo, el procedimiento recomendado es tener solo una regla de sincronización con la combinación definida cuando varias reglas de sincronización en el ámbito de un objeto. En la configuración de cuadro de sincronización de Azure AD Connect, estas reglas pueden encontrarse consultando el nombre y encuentre aquellas con la palabra **unirse** al final del nombre. Una regla de sincronización sin reglas combinación definidas aplica los flujos de atributo cuando otra regla de sincronización unen los objetos o se aprovisione un nuevo objeto en el destino.

Si observa la imagen de arriba, puede ver que la regla está intentando unirse a **objectSID** con **msExchMasterAccountSid** (Exchange) y **msRTCSIP-OriginatorSid** (Lync), que es lo que esperamos en una topología de bosque de recursos de la cuenta. Busque la misma regla en todos los bosques. Se supone que cada bosque podría ser bosque una cuenta o un recurso. Esta configuración también funciona si tiene cuentas que están en un único bosque y no tienen que ser unido.

#### <a name="transformations"></a>Transformaciones
La sección transformación define todos los flujos de atributo que se aplican al objeto de destino cuando se unen los objetos y se cumple el filtro del ámbito. Volver a la **en de AD: usuario AccountEnabled** regla de sincronización, encontrará las siguientes transformaciones:

![Transformaciones ficha editor de reglas en sincronización ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncruletransformations.png)

Para colocar esta configuración en contexto, en una implementación de bosque de recursos de la cuenta, se espera para buscar una cuenta habilitada en el bosque de cuenta y una cuenta deshabilitada en el bosque de recursos con la configuración de Exchange y Lync. La regla de sincronización que está viendo contiene los atributos necesarios para el inicio de sesión y estos atributos deberán flujo del bosque donde hay una cuenta habilitada. Estos flujos de atributo se colocan juntos en una regla de sincronización.

Una transformación puede tener tipos diferentes: constante, directa y Expression.

- Un flujo constante siempre fluye un valor codificado. En el caso anterior, siempre establece el valor **True** en el metaverso atributo denominado **accountEnabled**.
- Un flujo directo siempre fluye el valor del atributo en el origen de para el atributo de destino como-es.
- El tercer tipo de flujo es expresión y permite configuraciones más avanzadas.

El lenguaje de expresiones es VBA (Visual Basic para aplicaciones), así que las personas con experiencia de Microsoft Office o VBScript reconocerá el formato. Atributos están entre corchetes, [attributeName]. Nombres de atributos y nombres de función distinguen mayúsculas de minúsculas, pero el Editor de reglas de sincronización evalúa las expresiones y proporcione una advertencia si la expresión no es válida. Todas las expresiones se expresan en una sola línea con funciones anidadas. Para mostrar la potencia del lenguaje de configuración, esto es el flujo de pwdLastSet, pero con comentarios adicionales insertados:

```
// If-then-else
IIF(
// (The evaluation for IIF) Is the attribute pwdLastSet present in AD?
IsPresent([pwdLastSet]),
// (The True part of IIF) If it is, then from right to left, convert the AD time format to a .Net datetime, change it to the time format used by Azure AD, and finally convert it to a string.
CStr(FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")),
// (The False part of IIF) Nothing to contribute
NULL
)
```

Vea [Expresiones de aprovisionamiento de descripción descriptiva](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) para obtener más información sobre el lenguaje de expresiones para los flujos de atributo.

### <a name="precedence"></a>Prioridad
Ahora veamos algunas reglas de sincronización individuales, pero las reglas de trabajaron en conjunto en la configuración. En algunos casos, un valor de atributo es contribuido de varias reglas de sincronización para el mismo atributo de destino. En este caso, la prioridad de los atributos se usa para determinar qué atributo gana. Por ejemplo, mire la sourceAnchor de atributo. Este es un atributo importante para poder iniciar sesión en Azure AD. Puede encontrar un flujo de atributo para este atributo en dos reglas de sincronización diferentes **en de AD: usuario AccountEnabled** y **en de AD: comunes de usuario**. Debido a la prioridad de la regla de sincronización, el atributo sourceAnchor se ha contribuido del bosque con una cuenta habilitada en primer lugar si hay varios objetos Unidos al objeto de metaverse. Si no hay ningún cuentas habilitadas, el motor de sincronización utiliza la regla de sincronización comodín **en de AD: comunes de usuario**. Esta configuración garantiza que incluso para las cuentas que están deshabilitadas, aún hay un sourceAnchor.

![Reglas de sincronización entrante](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulesinbound.png)

El Asistente para instalación establece la prioridad para las reglas de sincronización en grupos. Todas las reglas de un grupo tienen el mismo nombre, pero están conectados a diferentes directorios conectados. El Asistente de instalación le ofrece la regla **en de AD: unirse a usuario** máxima prioridad y se itera sobre todos los directorios de AD de conectado. A continuación, continúa con el siguientes grupos de reglas en un orden predefinido. Dentro de un grupo, se agregan las reglas en el orden en que se agregaron los conectores en el asistente. Si se agrega otro conector a través del asistente, se vuelven a ordenar las reglas de sincronización y reglas de conector nuevo se insertan última en cada grupo.

### <a name="putting-it-all-together"></a>Todo en conjunto
Ahora sabemos suficiente acerca de las reglas de sincronización para poder comprender cómo funciona la configuración con las reglas de sincronización diferente. Si observa un usuario y los atributos que se han aumentado metaverse, se aplican las reglas en el siguiente orden:

Nombre | Comentario
:------------- | :-------------
En de AD: combinación de usuario | Regla para unirse a objetos de espacio de conector con metaverse.
En de AD: cuenta habilitada | Atributos necesarios para el inicio de sesión en Azure AD y Office 365. Queremos estos atributos de la cuenta habilitada.
En de AD: común de usuario de Exchange | Atributos que se encuentra en la lista Global de direcciones. Se supone que es la mejor calidad de los datos del bosque donde hemos encontrado el buzón del usuario.
En de AD: común de usuario | Atributos que se encuentra en la lista Global de direcciones. En caso de que no se encuentra un buzón de correo, cualquier otro objeto combinado puede contribuir el valor del atributo.
En de AD: Exchange de usuario | Sólo existe si se ha detectado Exchange. Pase todos los atributos de Exchange de infraestructura.
En de AD: Lync del usuario | Sólo existe si se ha detectado Lync. Pase todos los atributos de Lync de infraestructura.

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre el modelo de configuración de [Aprovisionamiento descriptiva de descripción](active-directory-aadconnectsync-understanding-declarative-provisioning.md).
- Más información sobre el lenguaje de expresiones en [Expresiones de aprovisionamiento descriptiva en Descripción](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md).
- Continúe leyendo cómo funciona la configuración del cuadro de [Descripción de usuarios](active-directory-aadconnectsync-understanding-users-and-contacts.md) y contactos
- Vea cómo hacer un cambio de práctica con aprovisionamiento descriptiva cómo [hacer un cambio en la configuración predeterminada](active-directory-aadconnectsync-change-the-configuration.md).

**Temas de información general**

- [Sincronización de Azure AD Connect: entender y personalizar la sincronización](active-directory-aadconnectsync-whatis.md)
- [Integrar las identidades locales con Azure Active Directory](active-directory-aadconnect.md)

<properties
   pageTitle="Azure AD Connect: Conceptos de diseño | Microsoft Azure"
   description="Este tema describe algunas áreas de diseño de implementación"
   services="active-directory"
   documentationCenter=""
   authors="billmath"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.custom = "azure-ad-connect"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="Identity"
   ms.date="09/13/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-design-concepts"></a>Azure AD Connect: Conceptos de diseño
El propósito de este tema es describir las áreas que deben considerar a través de durante el diseño de implementación de Azure AD Connect. Este tema es un análisis detallado en algunas áreas y estos conceptos se describen brevemente en otros temas también.

## <a name="sourceanchor"></a>sourceAnchor
El atributo sourceAnchor está definido como *un atributo inmutable durante la duración de un objeto*. Identifica inequívocamente un objeto, como el objeto mismo local y en Azure AD. El atributo también se llama **immutableId** y los dos nombres se usan intercambiables.

La palabra inmutable, que es "no se puede cambiar", es importante que este tema. Puesto que no se puede cambiar el valor de este atributo después de que se ha establecido, es importante elegir un diseño que admita su situación.

El atributo se usa para las siguientes situaciones:

- Cuando un nuevo servidor de motor de sincronización es integrado o volver a generar después de un escenario de recuperación ante, este atributo vincula objetos existentes en Azure AD con objetos locales.
- Si mueve un modelo de identidad sincronizados de una identidad de nube, este atributo permite objetos objetos existentes de "coincidencia importante" en Azure AD con objetos local.
- Si utiliza federación, este atributo junto con el **userPrincipalName** se usa en la notificación para identificar un usuario.

En este tema habla sourceAnchor solo lo que se refiere a los usuarios. Las mismas reglas que se aplican a todos los tipos de objeto, pero es únicamente para usuarios de que este problema suele ser un problema.

### <a name="selecting-a-good-sourceanchor-attribute"></a>Si selecciona un atributo buena sourceAnchor
El valor del atributo debe seguir las siguientes reglas:

- Tener menos de 60 caracteres de longitud
    - Caracteres no está a la z, A-z o 0-9 y codificación cuentan como 3 caracteres
- No contiene un carácter especial: & #92;! # $ % & * + / = ? ^ & #96; { } | ¿~ (de < >) '; : , [ ] " @ _
- Debe ser único global
- Debe ser una cadena, entero o binario.
- No debe estar basado en el nombre de usuario, estos cambios
- Deben no distingue mayúsculas de minúsculas y evitar los valores que pueden variar de mayúsculas y minúsculas
- Debe asignarse cuando se crea el objeto

Si el sourceAnchor seleccionado no es de tipo cadena, a continuación, Base64Encode conectar de Azure AD el valor del atributo para no garantizar caracteres especiales aparecen. Si utiliza otro servidor de federación de ADFS, asegúrese de que el servidor también puede Base64Encode el atributo.

El atributo sourceAnchor distingue mayúsculas de minúsculas. Un valor de "JohnDoe" no es igual a "johndoe". Pero no debería tener dos objetos diferentes con solo una diferencia de mayúsculas y minúsculas.

Si tiene un único bosque local, a continuación, el atributo debe usar es **GUID de objeto**. También es el atributo que se usa con configuración express en Azure AD Connect y también el atributo de la sincronización de directorios.

Si tiene varios bosques y no mover los usuarios entre dominios y bosques, **GUID de objeto** es un atributo bueno usar incluso en este caso.

Si mueve usuarios entre dominios y bosques, debe buscar un atributo que no cambia o se puede mover con los usuarios durante el desplazamiento. Es recomendable introducir un atributo síntesis. Atributo que podría contener algo que parece un GUID sería adecuado. Durante la creación de objeto, se crea un nuevo GUID y se marca en el usuario. Puede crear una regla personalizada de sincronización en el servidor de motor de sincronización para crear este valor basado en el **GUID de objeto** y actualice el atributo seleccionado en agrega. Al mover el objeto, asegúrese de copiar el contenido de este valor.

Otra solución consiste en seleccionar un atributo existente que se sabe que no cambia. Los atributos utilizados con frecuencia incluyen **IdEmpleado**. Si se considera un atributo que contiene las letras, asegúrese de que no hay que ninguna oportunidad el caso (mayúsculas y minúsculas) puede cambiar el valor del atributo. Incorrectas atributos que no se recomienda incluyen los atributos con el nombre del usuario. En un matrimonio o divorcio, el nombre debe cambiar, que no está permitido para este atributo. También es una razón por qué atributos como **userPrincipalName**, **correo**y **targetAddress** no están incluso puede seleccionar en el Asistente para instalación de Azure AD Connect. Los atributos también contienen la @-character, que no está permitido en la sourceAnchor.

### <a name="changing-the-sourceanchor-attribute"></a>Cambiar el atributo sourceAnchor
No se puede cambiar el valor del atributo sourceAnchor después de que se ha creado el objeto en Azure AD y se sincroniza la identidad.

Por este motivo, las siguientes restricciones se aplican a Azure AD Connect:

- El atributo sourceAnchor sólo se puede establecer durante la instalación inicial. Si vuelve a ejecutar al Asistente para instalación, esta opción es de solo lectura. Si necesita cambiar esta configuración, debe desinstalar y volver a instalar.
- Si instala a otro servidor de Azure AD Connect, debe seleccionar el mismo atributo sourceAnchor usado anteriormente. Si ha estado utilizando una versión anterior de sincronización de directorios y mover a Azure AD Connect, debe utilizar **GUID de objeto** ya que es el atributo de la sincronización de directorios.
- Si se cambia el valor de sourceAnchor después el objeto se ha exportado a Azure AD, a continuación, Azure AD Connect produce un error de sincronización y no admite más cambios en que se modifica el objeto antes de que el problema se ha corregido y la sourceAnchor en el directorio de origen.

## <a name="azure-ad-sign-in"></a>Inicio de sesión de Azure AD
Mientras se integra su directorio local con Azure AD, es importante comprender cómo la configuración de sincronización puede afectar al usuario de manera autentica. Azure AD utiliza userPrincipalName (UPN) para autenticar al usuario. Sin embargo, al sincronizar los usuarios, debe elegir el atributo que se utilizará para el valor de userPrincipalName con cuidado.

### <a name="choosing-the-attribute-for-userprincipalname"></a>Elegir el atributo userPrincipalName
Cuando se selecciona el atributo para proporcionar el valor de UPN para utilizarse en Azure uno debe asegurarse

- Los valores de atributo se ajustan a la sintaxis UPN (RFC 822), que se deben tener el formatousername@domain
- El sufijo de los valores coincide con uno de los dominios personalizados comprobados en Azure AD

En configuración de express, la opción supuesta para el atributo es userPrincipalName. Si el atributo userPrincipalName no contiene el valor desea que los usuarios para iniciar sesión en Azure, debe elegir la **Instalación personalizada**.

### <a name="custom-domain-state-and-upn"></a>Estado del dominio personalizado y UPN
Es importante para asegurarse de que hay un dominio comprobado para el sufijo UPN.

John es un usuario en contoso.com. Desea que John usar el UPN local john@contoso.com para iniciar sesión en Azure después de que ha sincronizado los usuarios a su contoso.onmicrosoft.com del directorio de Azure AD. Para ello, debe agregar y verificar contoso.com como un dominio personalizado en Azure AD antes de iniciar la sincronización de los usuarios. Si el sufijo UPN de John, por ejemplo contoso.com, no coincide con un dominio comprobado en Azure AD, Azure AD reemplaza el sufijo UPN con contoso.onmicrosoft.com.

### <a name="non-routable-on-premises-domains-and-upn-for-azure-ad"></a>No se pueden enrutar local dominios y UPN Azure AD
Algunas organizaciones tienen dominios no enrutables, como contoso.local o dominios simple solo una etiqueta como contoso. No es posible comprobar un dominio no enrutables en Azure AD. Azure AD Connect puede sincronizar a solo un dominio comprobado en Azure AD. Cuando se crea un directorio de Azure AD, crea un dominio enrutable que se convierte en dominio predeterminado para su Azure AD, por ejemplo, contoso.onmicrosoft.com. Por lo tanto, es necesario comprobar cualquier otro dominio enrutable en este caso, en caso de que no desea sincronizar con el dominio onmicrosoft.com de forma predeterminada.

Para obtener más información sobre cómo agregar y verificar dominios, lea [Agregar el nombre de dominio personalizado para Azure Active Directory](active-directory-add-domain.md) .

Azure AD Connect detecta si se están ejecutando en un entorno de dominio no enrutables y haría advertir adecuadamente vayan anticipada con configuración de express. Si está trabajando en un dominio no enrutables, es probable que el UPN de los usuarios no enrutables sufijos demasiado. Por ejemplo, si se están ejecutando en contoso.local, Azure AD Connect sugiere para usar una configuración personalizada en lugar de utilizar la configuración de express. Con una configuración personalizada, es posible especificar el atributo que se debe utilizar como UPN para iniciar sesión en Azure después de que los usuarios se sincronizan con Azure AD.

## <a name="next-steps"></a>Pasos siguientes
Más información acerca de cómo [integrar las identidades locales con Azure Active Directory](active-directory-aadconnect.md).

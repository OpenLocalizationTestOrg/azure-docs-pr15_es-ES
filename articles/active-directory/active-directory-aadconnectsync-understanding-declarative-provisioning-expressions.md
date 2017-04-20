<properties
    pageTitle="Sincronización de Azure AD Connect: descripción expresiones de aprovisionamiento descriptiva | Microsoft Azure"
    description="Explica las expresiones de aprovisionamiento descriptiva."
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
    ms.date="08/31/2016"
    ms.author="markusvi;andkjell"/>


# <a name="azure-ad-connect-sync-understanding-declarative-provisioning-expressions"></a>Sincronización de Azure AD Connect: descripción expresiones de aprovisionamiento descriptiva
Sincronización de Azure AD Connect se basa en aprovisionamiento descriptiva por primera vez en Forefront identidad Manager 2010. Permite implementar la lógica de negocios de integración de identidad completa sin necesidad de escribir código compilado.

Una parte esencial de aprovisionamiento descriptiva es el lenguaje de expresiones utilizado en flujos de atributo. El idioma que utiliza es un subconjunto de Microsoft® de Visual Basic para aplicaciones (VBA). Este idioma se usa en Microsoft Office y los usuarios con experiencia de VBScript también reconocen. El lenguaje de expresiones de aprovisionamiento descriptiva solo está usando funciones y no es un lenguaje estructurado. No existen métodos ni instrucciones. En su lugar se anidan funciones para el flujo del programa express.

Para obtener más detalles, consulte [Bienvenido a la referencia Visual Basic para aplicaciones idioma para Office 2013](https://msdn.microsoft.com/library/gg264383.aspx).

Los atributos están inflexible. Una función sólo acepta atributos del tipo correcto. También distingue mayúsculas de minúsculas. Nombres de funciones y nombres de atributo deben tener NOMPROPIO mayúsculas y minúsculas o se produce un error.

## <a name="language-definitions-and-identifiers"></a>Definiciones de idioma e identificadores

- Las funciones tiene un nombre seguido de los argumentos entre corchetes: nombrefunción (el argumento 1, el argumento N).
- Atributos identificados corchetes: [attributeName]
- Parámetros se identifican por signos de porcentaje: ParameterName %
- Las constantes de cadena están entre comillas: por ejemplo, "Contoso" (Nota: debe usar comillas rectas "" y no comillas tipográficas "")
- Valores numéricos se expresa sin comillas y debe ser decimal. Prefijan hexadecimal valores & H. Por ejemplo, 98052 & HFF
- Valores booleanos se expresan con constantes: verdadero, falso.
- Literales y constantes integradas se expresan sólo con su nombre: NULL, CRLF, IgnoreThisFlow

### <a name="functions"></a>Funciones
Aprovisionamiento descriptiva usa muchas de las funciones para habilitar la posibilidad de transformar los valores de atributo. Estas funciones se pueden anidar para que el resultado de una función se pasa a otra función.

`Function1(Function2(Function3()))`

La lista completa de funciones puede encontrarse en la [referencia de funciones](active-directory-aadconnectsync-functions-reference.md).

### <a name="parameters"></a>Parámetros
Un parámetro está definido por un conector o un administrador con PowerShell. Parámetros normalmente contienen valores que son diferentes de sistema, por ejemplo el nombre del dominio del usuario se encuentra en. Estos parámetros pueden utilizarse en flujos de atributos.

El conector de Active Directory proporciona los siguientes parámetros para las reglas de sincronización entrante:

| Nombre de parámetro | Comentario |
| --- | --- |
| Domain.Netbios | Formato de NetBIOS del dominio que se importa actualmente, por ejemplo FABRIKAMSALES |
| Domain.FQDN | Formato de nombre de dominio completo del dominio que se importa actualmente, por ejemplo sales.fabrikam.com |
| Domain.LDAP | Formato LDAP del dominio que se importa actualmente, por ejemplo DC = ventas, DC = fabrikam, DC = com |
| Forest.Netbios | Formato de NetBIOS del bosque nombre actualmente que se importa, por ejemplo FABRIKAMCORP |
| Forest.FQDN | Formato FQDN del bosque nombre actualmente que se importa, por ejemplo fabrikam.com |
| Forest.LDAP | Formato LDAP del bosque nombre actualmente que se importa, por ejemplo, DC = fabrikam, DC = com |

El sistema proporciona el parámetro siguiente, que se usa para obtener el identificador del conector que se está ejecutando:  
`Connector.ID`

Aquí tiene un ejemplo que rellena el dominio de atributo metaverso con el nombre de netbios del dominio donde se encuentra el usuario:  
`domain` <- `%Domain.Netbios%`

### <a name="operators"></a>Operadores
Pueden utilizar los operadores siguientes:

- **Comparación**: <, < =, <>, =, >, > =
- **Matemáticas**: +, -, \*, -
- **Cadena**: & (concatenación)
- **Lógica**: & & (y), || (o)
- **Orden de evaluación**:)

Operadores se evalúan de izquierda a derecha y tienen la misma prioridad de evaluación. Es decir, el \* (multiplicación) no se evalúa antes - (resta). 2\*(5 + 3) no es igual a 2\*5 + 3. Los corchetes () se utilizan para cambiar el orden de evaluación cuando izquierda para el orden de evaluación derecha no sea adecuada.

## <a name="multi-valued-attributes"></a>Atributos de valores múltiples
Las funciones pueden funcionar en atributos de valor único y con varios valores. Atributos de varios valores, la función funciona a través de cada valor y aplica la misma función a todos los valores.

Por ejemplo:  
`Trim([proxyAddresses])`Hacer un recorte de cada valor en el atributo proxyAddress.  
`Word([proxyAddresses],1,"@") & "@contoso.com"`Para cada valor con un @-sign, reemplazar el dominio con @contoso.com.  
`IIF(InStr([proxyAddresses],"SIP:")=1,NULL,[proxyAddresses])`Busque la dirección SIP y quitar de los valores.

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre el modelo de configuración de [Aprovisionamiento descriptiva de descripción](active-directory-aadconnectsync-understanding-declarative-provisioning.md).
- Vea cómo descriptiva aprovisionamiento es usados de predefinidas en la [Descripción de la configuración predeterminada](active-directory-aadconnectsync-understanding-default-configuration.md).
- Vea cómo hacer un cambio de práctica con aprovisionamiento descriptiva cómo [hacer un cambio en la configuración predeterminada](active-directory-aadconnectsync-change-the-configuration.md).

**Temas de información general**

- [Sincronización de Azure AD Connect: entender y personalizar la sincronización](active-directory-aadconnectsync-whatis.md)
- [Integrar las identidades locales con Azure Active Directory](active-directory-aadconnect.md)

**Temas de referencia**

- [Sincronización de Azure AD Connect: referencia de funciones](active-directory-aadconnectsync-functions-reference.md)

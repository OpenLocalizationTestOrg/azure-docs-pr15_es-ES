
<properties
    pageTitle="Usar atributos para crear reglas avanzadas | Microsoft Azure"
    description="¿Cómo-de crear reglas avanzadas para un grupo incluidos operadores de regla de expresiones y parámetros."
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="curtand"/>


# <a name="using-attributes-to-create-advanced-rules"></a>Usar atributos para crear reglas avanzadas

El portal clásico Azure le ofrece la capacidad de crear reglas avanzadas para habilitar a más complejos basada en atributos dinámicos pertenencias a grupos de Azure Active Directory (AD Azure).  

Cuando se evalúa el sistema de los atributos de un cambio de usuario, todas las reglas de grupo dinámico en un directorio para ver si el cambio de atributo del usuario activaría cualquier grupo agrega o quita. Si un usuario cumple una regla en un grupo, que se hayan agregado como miembro al grupo. Si ya no cumplen la regla de un grupo que están miembro, se eliminan como miembros de ese grupo.

## <a name="to-create-the-advanced-rule"></a>Para crear la regla avanzada

1. En el [portal de clásica Azure](https://manage.windowsazure.com), seleccione **Active Directory**y, a continuación, abra el directorio de su organización.

2. Seleccione la ficha **grupos** y, a continuación, abra el grupo que desea editar.

3. Seleccione la ficha **Configurar** , seleccione la opción **regla avanzada** y, a continuación, escriba la regla avanzada en el cuadro de texto.

## <a name="constructing-the-body-of-an-advanced-rule"></a>Crear el cuerpo de una regla avanzada

La regla avanzada que se puede crear para las pertenencias dinámicas para los grupos es, esencialmente, una expresión binaria que consta de tres partes y da un resultado true o false. Las tres partes son:

- Parámetro izquierda
- Operador binario
- Constante derecha

Una regla avanzada completa este aspecto: (leftParameter binaryOperator "RightConstant"), donde la apertura y cierre de paréntesis son necesarios para toda la expresión binaria, comillas dobles son necesarias para la constante derecha y la sintaxis de la izquierda parámetro es user.property. Una regla avanzada puede constar de más de uno expresiones binarias separadas por- y, - o y - operadores lógicos no.
Los siguientes son ejemplos de una regla avanzada construida correctamente:

- (user.department - EC "Ventas")- o (user.department - EC "Marketing")
- (user.department - EC "Ventas")- y - no (user.jobTitle-contiene "SDE")

Para obtener una lista completa de parámetros admitidos y operadores de regla de expresión, vea las secciones siguientes.

La longitud total del cuerpo de la regla avanzada no puede superar los 2048 caracteres.

> [AZURE.NOTE]
>Operaciones de cadena y regex distinguen mayúsculas de minúsculas. También puede realizar comprobaciones Null, con $null como una constante, por ejemplo, user.department - EC $null.
Cadenas que contengan comillas "escape con ' carácter, por ejemplo, user.department - EC \`"Ventas".

## <a name="supported-expression-rule-operators"></a>Operadores de regla de expresión admitidos
La siguiente tabla enumeran los operadores de regla de expresión compatibles y su sintaxis para utilizarse en el cuerpo de la regla avanzada:

| Operador        | Sintaxis         |
|-----------------|----------------|
| No es igual a      | -ne            |
| Es igual a          | -EC            |
| No empieza por | -notStartsWith |
| Empieza por     | -startsWith    |
| No contiene    | -notContains   |
| Contiene        | -contiene      |
| No coinciden       | -notMatch      |
| Coincidir           | -coincide con         |


## <a name="query-error-remediation"></a>Corrección de errores de consulta
La siguiente tabla enumera los posibles errores y cómo corregirlos si se producen

| Error de análisis de consulta     | Uso de error       | Uso de corregido             |
|-----------------------|-------------------|-----------------------------|
| Error: El atributo no admitido.                                      | (user.invalidProperty - EC "Valor")       | (user.department - EC "valor")<br/>Propiedad debe coincidir con uno de la [lista de propiedades compatibles](#supported-properties).                          |
| Error: El operador no se admite en el atributo.                       | (user.accountEnabled-contiene verdadero)                                                                               | (user.accountEnabled - EC verdadero)<br/>La propiedad es de tipo boolean. Utilizar los operadores compatibles (-EC o - ne) en tipo boolean desde la lista anterior.                                                                                                                                   |
| Error: Error de compilación de consulta.                                      | (user.department - EC "Ventas")- y (user.department - EC "Marketing")(user.userPrincipalName-match"*@domain.ext") | (user.department - EC "Ventas")- y (user.department - EC "Marketing")<br/>Operador lógico debe coincidir con uno de la lista de propiedades admitidas anterior. (user.userPrincipalName-coincide con ".*@domain.ext")or(user.userPrincipalName -coincide con "@domain.ext$")Error en expresión regular. |
| Error: Expresión binaria no está en el formato correcto.                     | (user.department-EC "Ventas") (user.department - EC "Ventas") (user.department-EC "Ventas")                             | (user.accountEnabled - EC true)- y (user.userPrincipalName-contiene"alias@domain")<br/>Consulta tiene varios errores. Paréntesis no en el lugar correcto.                                                                                                                            |
| Error: Error desconocido al configurar dinámicos pertenencias a grupos. | (user.accountEnabled - EC "True" y user.userPrincipalName-contiene"alias@domain")                               | (user.accountEnabled - EC true)- y (user.userPrincipalName-contiene"alias@domain")<br/>Consulta tiene varios errores. Paréntesis no en el lugar correcto.                                                                                                                            |

## <a name="supported-properties"></a>Propiedades compatibles
Éstas son todas las propiedades de usuario que puede usar en la regla avanzada:

### <a name="properties-of-type-boolean"></a>Propiedades de tipo boolean

Operadores permitidos

* -EC


* -ne


| Propiedades     | Valores permitidos  | Uso                          |
|----------------|-----------------|--------------------------------|
| accountEnabled | Verdadero Falso      | user.accountEnabled - EC verdadero)  |
| dirSyncEnabled | True null false | (user.dirSyncEnabled - EC verdadero) |

### <a name="properties-of-type-string"></a>Propiedades de tipo cadena

Operadores permitidos

* -EC


* -ne


* -notStartsWith


* -StartsWith


* -contiene


* -notContains


* -coincide con


* -notMatch

| Propiedades                 | Valores permitidos                                                                                        | Uso                                                     |
|----------------------------|-------------------------------------------------------------------------------------------------------|-----------------------------------------------------------|
| Ciudad                       | Cualquier valor de cadena o $null                                                                           | (user.city - EC "valor")                                   |
| país                    | Cualquier valor de cadena o $null                                                                            | (user.country - EC "valor")                                |
| departamento                 | Cualquier valor de cadena o $null                                                                          | (user.department - EC "valor")                             |
| displayName                | Cualquier valor de cadena                                                                                 | (user.displayName - EC "valor")                            |
| facsimileTelephoneNumber   | Cualquier valor de cadena o $null                                                                           | (user.facsimileTelephoneNumber - EC "valor")               |
| givenName                  | Cualquier valor de cadena o $null                                                                           | (user.givenName - EC "valor")                              |
| Puesto temporal                   | Cualquier valor de cadena o $null                                                                           | (user.jobTitle - EC "valor")                               |
| correo                       | Cualquier valor de cadena o $null (dirección SMTP del usuario)                                                  | (user.mail - EC "valor")                                   |
| mailNickName               | Cualquier valor de cadena (alias de correo del usuario)                                                            | (user.mailNickName - EC "valor")                           |
| móvil                     | Cualquier valor de cadena o $null                                                                           | (user.mobile - EC "valor")                                 |
| Id. de objeto                   | GUID del objeto de usuario                                                                            | (user.objectId - EC "1111111-1111-1111-1111-111111111111") |
| passwordPolicies           | Ninguno DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration, DisableStrongPassword |   (user.passwordPolicies - EC "DisableStrongPassword")                                                      |
| physicalDeliveryOfficeName | Cualquier valor de cadena o $null                                                                            | (user.physicalDeliveryOfficeName - EC "valor")             |
| código postal                 | Cualquier valor de cadena o $null                                                                            | (user.postalCode - EC "valor")                             |
| preferredLanguage          | Código ISO 639-1                                                                                        | (user.preferredLanguage - EC "en-US")                      |
| sipProxyAddress            | Cualquier valor de cadena o $null                                                                            | (user.sipProxyAddress - EC "valor")                        |
| estado                      | Cualquier valor de cadena o $null                                                                            | (user.state - EC "valor")                                  |
| Dirección              | Cualquier valor de cadena o $null                                                                            | (user.streetAddress - EC "valor")                          |
| apellido                    | Cualquier valor de cadena o $null                                                                            | (user.surname - EC "valor")                                |
| telephoneNumber            | Cualquier valor de cadena o $null                                                                            | (user.telephoneNumber - EC "valor")                        |
| usageLocation              | Código de país con letras dos                                                                           | (user.usageLocation - EC "Estados Unidos")                             |
| userPrincipalName          | Cualquier valor de cadena                                                                                     | (user.userPrincipalName - EC"alias@domain")               |
| userType                   | invitado miembro $null                                                                                    | (user.userType - EC "Miembro")                              |

### <a name="properties-of-type-string-collection"></a>Propiedades de la colección de tipo cadena

Operadores permitidos

* -contiene


* -notContains

| Poperties      | Valores permitidos                        | Uso                                                |
|----------------|---------------------------------------|------------------------------------------------------|
| otherMails     | Cualquier valor de cadena                      | (user.otherMails-contiene"alias@domain")           |
| proxyAddresses | SMTP: alias@domain smtp:alias@domain | (user.proxyAddresses-contiene "SMTP:alias@domain") |

## <a name="extension-attributes-and-custom-attributes"></a>Atributos de extensión y atributos personalizados
Atributos de extensión y atributos personalizados son compatibles con las reglas de pertenencia dinámica.

Atributos de extensión se sincronizan desde local ventana servidor AD y llevar el formato de "ExtensionAttributeX", donde X es igual a 1-15.
Un ejemplo de una regla que utiliza un atributo de extensión sería

(user.extensionAttribute15 - EC "Marketing")

Atributos personalizados se sincronizan desde local AD de Windows Server o desde una aplicación de SaaS conectada y el formato de "user.extension_[GUID]\__ [atributo]", donde [GUID] es el identificador único para la aplicación que creó el atributo de AAD AAD y [atributo] es el nombre del atributo tal como se ha creado.
Es un ejemplo de una regla que utiliza un atributo personalizado

User.extension_c272a57b722d4eb29bfe327874ae79cb__OfficeNumber  

El nombre de atributo personalizado se puede encontrar en el directorio mediante una consulta de un usuario del atributo con el Explorador de gráfico y buscar el nombre del atributo.

## <a name="direct-reports-rule"></a>Regla de subordinados directos
Ahora puede rellenar a los miembros de un grupo basado en el atributo de administrador de un usuario.

**Para configurar un grupo como un grupo de "Administrador"**

1. En el portal de Azure clásico, haga clic en **Active Directory**y, a continuación, haga clic en el nombre del directorio de su organización.

2. Seleccione la ficha **grupos** y, a continuación, abra el grupo que desea editar.

3. Seleccione la ficha **Configurar** y, a continuación, seleccione **Regla avanzada**.

4. Escriba la regla con la siguiente sintaxis:

    Informes directos para *informes directos para {obectID_of_manager}*. Es un ejemplo de una regla válida para informes directos

                    Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863”

    donde "62e19b97-8b3d-4d4a-a106-4ce66896a863" es el Id. de objeto del administrador. El identificador de objeto puede encontrarse en Azure AD en la **pestaña de perfil** de la página de usuario para el usuario que es el administrador.

3. Al guardar esta regla, todos los usuarios que cumplan la regla se unirán como miembros del grupo. Puede llevar algunos minutos para el grupo de rellenar inicialmente.


## <a name="using-attributes-to-create-rules-for-device-objects"></a>Usar atributos para crear reglas para objetos de dispositivo

También puede crear una regla que selecciona objetos de dispositivo de pertenencia de un grupo. Pueden utilizar los siguientes atributos de dispositivo:

| Propiedades              | Valores permitidos                  | Uso                                                       |
|-------------------------|---------------------------------|-------------------------------------------------------------|
| displayName             | cualquier valor de cadena                | (device.displayName - EC "Roberto Iphone")                       |
| deviceOSType            | cualquier valor de cadena                | (device.deviceOSType - EC "IOS")                             |
| deviceOSVersion         | cualquier valor de cadena                | (el dispositivo. OSVersion - EC "9.1")                                |
| isDirSynced             | True null false                 | (device.isDirSynced - EC "true")                             |
| isManaged               | True null false                 | (device.isManaged - EC "false")                              |
| isCompliant             | True null false                 | (device.isCompliant - EC "true")                             |
| deviceCategory          | cualquier valor de cadena                | (device.deviceCategory - EC "")                              |
| entradas deviceManufacturer      | cualquier valor de cadena                | (device.deviceManufacturer - EC "Microsoft")                 |
| deviceModel             | cualquier valor de cadena                | (device.deviceModel - EC "IPhone 7 +")                        |
| deviceOwnership         | cualquier valor de cadena                | (device.deviceOwnership - EC "")                             |
| nombre de dominio              | cualquier valor de cadena                | (device.domainName - EC "contoso.com")                       |
| enrollmentProfileName   | cualquier valor de cadena                | (device.enrollmentProfileName - EC "")                       |
| isRooted                | True null false                 | (device.deviceOSType - EC "true")                            |
| managementType          | cualquier valor de cadena                | (device.managementType - EC "")                              |
| organizationalUnit      | cualquier valor de cadena                | (device.organizationalUnit - EC "")                          |
| deviceId                | deviceId válido                | (device.deviceId - EC "d4fe7726-5966-431c-b3b8-cddc8fdb717d" |

> [AZURE.NOTE]
> Estas reglas de dispositivo no pueden crearse con la lista desplegable "simple regla" en el portal de clásico de Azure.


## <a name="additional-information"></a>Información adicional
Estos artículos proporcionan información adicional sobre Azure Active Directory.

* [Solución de problemas dinámicos pertenencias a grupos](active-directory-accessmanagement-troubleshooting.md)

* [Administrar el acceso a los recursos con grupos de Azure Active Directory](active-directory-manage-groups.md)

* [Cmdlets de Azure Active Directory para configurar las opciones de grupo](active-directory-accessmanagement-groups-settings-cmdlets.md)

* [Índice de artículos de administración de aplicaciones de Azure Active Directory](active-directory-apps-index.md)

* [Integrar las identidades locales con Azure Active Directory](active-directory-aadconnect.md)

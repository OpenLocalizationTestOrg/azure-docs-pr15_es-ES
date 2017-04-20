
<properties
    pageTitle="Usar atributos para crear reglas avanzadas para la pertenencia a grupos en vista previa de Azure Active Directory | Microsoft Azure"
    description="Cómo crear reglas avanzadas en el grupo dinámico pertenencia incluidos compatibles con parámetros y operadores de regla de expresiones."
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
    ms.date="09/12/2016"
    ms.author="curtand"/>


# <a name="using-attributes-to-create-advanced-rules-for-group-membership-in-azure-active-directory-preview"></a>Usar atributos para crear reglas avanzadas para la pertenencia a grupos en vista previa de Azure Active Directory

El portal de Azure le ofrece la capacidad de crear reglas avanzadas para habilitar a más complejos basada en atributos dinámicos pertenencias a grupos de vista previa de Azure Active Directory (AD Azure). [¿Qué es la vista previa?](active-directory-preview-explainer.md) Este artículo describe la sintaxis para crear estas reglas avanzadas y atributos de la regla.

## <a name="to-create-the-advanced-rule"></a>Para crear la regla avanzada

1.  Inicie sesión en el [portal de Azure](https://portal.azure.com) con una cuenta que sea un administrador global para el directorio.

2.  Seleccione **más servicios**, escriba **los usuarios y grupos** en el cuadro de texto y, a continuación, presione **ENTRAR**.

  ![Administración de usuarios de apertura](./media/active-directory-groups-dynamic-membership-azure-portal/search-user-management.png)

3.  En el módulo de **usuarios y grupos** , seleccione **todos los grupos**.

  ![Abrir el módulo de grupos](./media/active-directory-groups-dynamic-membership-azure-portal/view-groups-blade.png)

4. En el módulo de **usuarios y grupos: todos los grupos** , seleccione el comando **Agregar** .

  ![Agregar nuevo grupo](./media/active-directory-groups-dynamic-membership-azure-portal/add-group-type.png)

5. En el módulo de **grupo** , escriba un nombre y una descripción para el nuevo grupo. Seleccione un **tipo de suscripción** de **Usuario dinámicos** o **Dispositivo dinámico**, dependiendo de si desea crear una regla para los usuarios o dispositivos y, a continuación, seleccione **Agregar dinámico consulta**. Para los atributos que se utiliza para las reglas de dispositivo, consulte [uso de atributos para crear reglas para los objetos del dispositivo](#using-attributes-to-create-rules-for-device-objects).

  ![Agregar regla de pertenencia dinámica](./media/active-directory-groups-dynamic-membership-azure-portal/add-dynamic-group-rule.png)

6. En el módulo de **reglas de pertenencia dinámica** , escriba la regla en el cuadro **Agregar regla de pertenencia dinámica avanzada** , presione ENTRAR y, a continuación, seleccione **crear** en la parte inferior de la hoja.

7. Seleccione **crear** en el módulo de **grupo** para crear el grupo.

## <a name="constructing-the-body-of-an-advanced-rule"></a>Crear el cuerpo de una regla avanzada

La regla avanzada que se puede crear para las pertenencias dinámicas para los grupos es, esencialmente, una expresión binaria que consta de tres partes y da un resultado true o false. Las tres partes son:

- Parámetro izquierda
- Operador binario
- Constante derecha

Una regla avanzada completa este aspecto: (leftParameter binaryOperator "RightConstant"), donde la apertura y cierre de paréntesis son necesarios para toda la expresión binaria, comillas dobles son necesarias para la constante derecha y la sintaxis de la izquierda parámetro es user.property. Una regla avanzada puede constar de más de uno expresiones binarias separadas por- y, - o y - operadores lógicos no.

Los siguientes son ejemplos de una regla avanzada construida correctamente:

- (user.department - EC "Ventas")- o (user.department - EC "Marketing")
- (user.department - EC "Ventas")- y - no (user.jobTitle-contiene "SDE")

Para obtener una lista completa de parámetros admitidos y operadores de regla de expresión, vea las secciones siguientes. Para los atributos que se utiliza para las reglas de dispositivo, consulte [uso de atributos para crear reglas para los objetos del dispositivo](#using-attributes-to-create-rules-for-device-objects).

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

1. Siga los pasos 1 a 5 para [crear la regla avanzada](#to-create-the-advanced-rule)y seleccione un **tipo de suscripción** de **Usuario dinámica**.

2. En el módulo de **reglas de pertenencia dinámica** , especifique la regla con la siguiente sintaxis:

    Informes directos para *informes directos para {obectID_of_manager}*. Es un ejemplo de una regla válida para informes directos

                    Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863”

    donde "62e19b97-8b3d-4d4a-a106-4ce66896a863" es el Id. de objeto del administrador. El identificador de objeto puede encontrarse en Azure AD en la **pestaña de perfil** de la página de usuario para el usuario que es el administrador.

3. Al guardar esta regla, todos los usuarios que cumplan la regla se unirán como miembros del grupo. Puede llevar algunos minutos para el grupo de rellenar inicialmente.


## <a name="using-attributes-to-create-rules-for-device-objects"></a>Usar atributos para crear reglas para objetos de dispositivo

También puede crear una regla que selecciona objetos de dispositivo de pertenencia de un grupo. Pueden utilizar los siguientes atributos de dispositivo:

| Propiedades           | Valores permitidos                  | Uso                                                |
|----------------------|---------------------------------|------------------------------------------------------|
| displayName          | cualquier valor de cadena                | (device.displayName - EC "Roberto Iphone")                 |
| deviceOSType         | cualquier valor de cadena                | (device.deviceOSType - EC "IOS")                      |
| deviceOSVersion      | cualquier valor de cadena                | (el dispositivo. OSVersion - EC "9.1")                         |
| isDirSynced          | True null false                 | (device.isDirSynced - EC "true")                      |
| isManaged            | True null false                 | (device.isManaged - EC "false")                       |
| isCompliant          | True null false                 | (device.isCompliant - EC "true")                      |


## <a name="additional-information"></a>Información adicional
Estos artículos proporcionan información adicional sobre grupos en Azure Active Directory.

* [Ver grupos existentes](active-directory-groups-view-azure-portal.md)
* [Crear un nuevo grupo y agregar miembros](active-directory-groups-create-azure-portal.md)
* [Administrar la configuración de un grupo](active-directory-groups-settings-azure-portal.md)
* [Administrar la pertenencia a un grupo](active-directory-groups-membership-azure-portal.md)
* [Administrar reglas dinámicas para usuarios de un grupo](active-directory-groups-dynamic-membership-azure-portal.md)

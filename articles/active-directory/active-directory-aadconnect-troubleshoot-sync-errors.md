<properties
    pageTitle="Azure AD Connect: Solución de errores durante la sincronización | Microsoft Azure"
    description="Se explica cómo solucionar problemas que puedan surgir durante la sincronización con Azure AD Connect."
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

# <a name="troubleshooting-errors-during-synchronization"></a>Solución de errores durante la sincronización
Pueden producirse errores cuando se sincronizan los datos de identidad de Active Directory (AD DS) de Windows Server a Azure Active Directory (AD Azure). Este artículo proporciona una descripción general de los diferentes tipos de errores de sincronización, algunas de las posibles situaciones que provocan estos errores y posibles formas de corregir los errores. Este artículo incluye los tipos de error comunes y no podrá referirse a los posibles errores.

 Este artículo se presupone que el lector está familiarizado con subyacente [diseñar los conceptos de Azure AD y Azure AD Connect](active-directory-aadconnect-design-concepts.md).

Con la última versión de Azure AD Connect \(agosto 2016 o superior\), un informe de errores de sincronización está disponible en el [Portal de Azure](https://aka.ms/aadconnecthealth) como parte de Azure AD conectar estado de sincronización.


Iniciar el 1 de septiembre de 2016 se habilitarán la característica de [Azure Active Directory Duplicar atributo resistencia](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md) predeterminada para todos los *nuevos* inquilinos de Azure Active Directory. Esta característica se activa de manera automática para los inquilinos existentes en los próximos meses.

Azure AD Connect realiza 3 tipos de operaciones de los directorios que mantiene en sincronización: sincronización, importar y exportar. Errores pueden realizar en todas las operaciones. En este artículo se centra principalmente en errores durante la exportación a Azure AD.

## <a name="errors-during-export-to-azure-ad"></a>Errores durante la exportación a Azure AD
Pasos de la sección describen los diferentes tipos de errores de sincronización que pueden surgir durante la operación de exportación a Azure AD mediante el conector de Azure AD. Este conector puede identificarse por el formato de nombre está "contoso. *onmicrosoft.com*".
Errores durante la exportación a Azure AD indican que la operación \(agregar, actualizar y eliminar etc.\) intentar realizar de Azure AD Connect \(motor de sincronización\) en Azure Active Directory falló.

![Exportar información general de errores](.\media\active-directory-aadconnect-troubleshoot-sync-errors\Export_Errors_Overview_01.png)

## <a name="data-mismatch-errors"></a>Errores de coincidencia de datos
### <a name="invalidsoftmatch"></a>InvalidSoftMatch
#### <a name="description"></a>Descripción
- Cuando Azure AD Connect \(motor de sincronización\) indica que Azure Active Directory para agregar o actualizar objetos, Azure AD coincide con el objeto de entrada con el atributo **sourceAnchor** para el atributo **immutableId** de objetos en Azure AD. Esta coincidencia se denomina una **Coincide con disco duro**.
- Cuando Azure AD **no encuentra** ningún objeto que coincida con el **immutableId** atributo con el atributo **sourceAnchor** del objeto entrante, antes de hacer el aprovisionamiento de un objeto nuevo, volver correspondiente para usar los atributos ProxyAddresses y UserPrincipalName para encontrar a una coincidencia. Esta coincidencia se denomina una **Coincide con suaves**. El software que coincida con está diseñada para que coincidan con objetos ya están en Azure AD (que proceden de Azure AD) con los nuevos ser agregado o actualizado durante la sincronización de objetos que representan la misma entidad local (usuarios, grupos).
- **InvalidSoftMatch** error se produce cuando el disco duro coincidir no encuentra cualquier objeto coincidente **y** suaves coincide con encuentra un objeto coincidente pero ese objeto tiene un valor diferente de *immutableId* que del objeto entrante *SourceAnchor*, sugiriendo que se sincronizó el objeto que coincida con otro objeto de Active Directory local.

En otras palabras, en orden para la coincidencia suave para que funcione, suaves coincide con el objeto no debe tener cualquier valor para el *immutableId*. Si establece cualquier objeto con *immutableId* con un valor se producen errores en la coincidencia importante pero que cumplen los criterios de coincidencia de suaves, la operación daría como resultado un error de sincronización InvalidSoftMatch.

Esquema de Azure Active Directory permite dos o más objetos tienen el mismo valor de los siguientes atributos. \(No es una lista exhaustiva.\)

- ProxyAddresses
- UserPrincipalName
- onPremisesSecurityIdentifier
- Id. de objeto

>[AZURE.NOTE] [Atributo de Azure Active Directory Duplicar atributo resistencia](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md) característica también se está ejecutando como el comportamiento predeterminado de Azure Active Directory.  Esto reducirá el número de errores de sincronización ver por Azure AD Connect (así como otros clientes de sincronización) realizando más flexibles Azure AD en el modo en que trata duplicados atributos ProxyAddresses y UserPrincipalName presentes en entornos locales AD. Esta característica no corregir los errores de duplicación. Por tanto, los datos aún deben corregirse. Pero permite hacer el aprovisionamiento de nuevos objetos que están bloqueados en caso contrario, se aprovisiona debido a valores duplicados en Azure AD. Esto también reducirá el número de errores de sincronización devuelve al cliente de sincronización.
Si esta característica está habilitada para el inquilino, no verá los errores de sincronización de InvalidSoftMatch vistos durante el aprovisionamiento de nuevos objetos.


#### <a name="example-scenarios-for-invalidsoftmatch"></a>Escenarios de ejemplo para InvalidSoftMatch
1. Dos o más objetos con el mismo valor de atributo ProxyAddresses existe en local de Active Directory. Sólo una introducción está configurada en Azure AD.
2. Dos o más objetos con el mismo valor de userPrincipalName existe en local de Active Directory. Sólo una introducción está configurada en Azure AD.
3. Se agregó un objeto en el local de Active Directory con el mismo valor de atributo ProxyAddresses que de un objeto existente en Azure Active Directory. El objeto agregado local no recibe está configurado en Azure Active Directory.
4. Se agregó un objeto en local de Active Directory con el mismo valor de atributo userPrincipalName que el de una cuenta de Azure Active Directory. El objeto no recibe está configurado en Azure Active Directory.
5. Mover una cuenta de sincronizada de bosque A bosque B. Azure AD Connect (motor de sincronización) estaba utilizando el atributo GUID de objeto para calcular la SourceAnchor. Después de mover el bosque, el valor de la SourceAnchor es diferente. El nuevo objeto (del bosque B) no puede sincronizar con el objeto existente en Azure AD.
6. Un objeto sincronizado se eliminó accidentalmente de local de Active Directory y un nuevo objeto creado en Active Directory para la misma entidad (como usuario) sin eliminar la cuenta de Azure Active Directory. La nueva cuenta no se puede sincronizar con el objeto de Azure AD existente.
7. Azure AD Connect desinstalado y volver a instalar. Durante la instalación de nuevo, se ha elegido un atributo diferente como la SourceAnchor. Todos los objetos que habían sincronizado previamente detener la sincronización con el error InvalidSoftMatch.

#### <a name="example-case"></a>Ejemplo de caso:
1. **Juan Saavedra** es un usuario sincronizado de Azure Active Directory local *contoso.com* de Active Directory
2. Bob Smith **UserPrincipalName** se establece como **bobs@contoso.com**.
3. **"abcdefghijklmnopqrstuv =="** es la **SourceAnchor** calculada Azure AD Connect con Bob Smith **GUID de objeto** de activado locales de Active Directory, que es el **immutableId** de Bob Smith en Azure Active Directory.
4. Bob también tiene los siguientes valores para el atributo **proxyAddresses** :
    - smtp:bobs@contoso.com
    - smtp:bob.smith@contoso.com
    - **smtp:bob@contoso.com**
5.  Un nuevo usuario, **Bob Taylor**, se agrega a la local de Active Directory.
6. De Bob Taylor **UserPrincipalName** se establece como **bobt@contoso.com**.
7. **"abcdefghijkl0123456789 ==" "** es la **sourceAnchor** calculada Azure AD Connect utilizando de Bob Taylor **GUID de objeto** de activado locales de Active Directory. Objeto de Bob Taylor no ha sincronizado con Azure Active Directory todavía.
8. Bob Taylor tiene los siguientes valores para el atributo proxyAddresses
    - smtp:bobt@contoso.com
    - smtp:bob.taylor@contoso.com
    - **smtp:bob@contoso.com**
9. Durante la sincronización, Azure AD Connect reconocerá la adición de Bob Taylor en local de Active Directory y pregunte Azure AD para realizar el cambio de la misma.
10. Azure AD realizará primero coincidencia de disco duro. Es decir, buscará si hay cualquier objeto con la immutableId igual a "abcdefghijkl0123456789 ==". Se producirá un error de coincidencia importante como ningún otro objeto en Azure AD tendrán que immutableId.
11. Azure AD intentará suaves-coincidir a Bob Taylor. Es decir, buscará si hay cualquier objeto con proxyAddresses igual a los tres valores, incluidossmtp:bob@contoso.com
12. Azure AD encontrará el objeto de Bob Smith para que coincida con los criterios de coincidencia suaves. Pero este objeto no tiene el valor de immutableId = "abcdefghijklmnopqrstuv ==". lo que indica que este objeto se ha sincronizado desde otro objeto de Active Directory local. Por lo tanto, Azure AD coincidir en no suaves estos objetos y se producirá un error de sincronización **InvalidSoftMatch** .

#### <a name="how-to-fix-invalidsoftmatch-error"></a>Cómo corregir el error de InvalidSoftMatch
La razón más común para el error InvalidSoftMatch es dos objetos con diferentes SourceAnchor \(immutableId\) tienen el mismo valor para los atributos ProxyAddresses o UserPrincipalName, que se utiliza durante el proceso de coincidencia suaves en Azure AD. Para corregir a la coincidencia suaves no válidos

1.  Identifique proxyAddresses duplicado, userPrincipalName u otro valor de atributo que causa el error. También identificar qué dos \(o más\) objetos implicados en el conflicto. El informe generado por [Azure AD conectar estado de sincronización](https://aka.ms/aadchsyncerrors) puede ayudarle a identificar los dos objetos.
2. Identificar el objeto que debe seguir tienen el valor duplicado y el objeto que no debería.
3. Quitar el valor duplicado del objeto que no debería tener ese valor. Tenga en cuenta que debe hacer el cambio en el directorio de dónde proceden el objeto. En algunos casos, debe eliminar uno de los objetos en conflicto.
4. Si el cambio realizado en las instalaciones de AD, le permiten Azure AD Connect sincronizar el cambio.

Tenga en cuenta que informe de errores de sincronización de Azure AD conectar estado de sincronización se actualiza cada 30 minutos e incluye los errores desde el último intento de sincronización.

>[AZURE.NOTE] ImmutableId, por definición, no debe cambiar en la duración del objeto. Si no se ha configurado Azure AD Connect con algunos de los casos en mente desde la lista anterior, podría acabar en una situación donde Azure AD Connect calcula un valor diferente de la SourceAnchor para el objeto de AD que representa la misma entidad (mismo usuario o grupo y contacto etcetera) que tiene un objeto de AD Azure existente que desea seguir utilizando.

#### <a name="related-articles"></a>Artículos relacionados
- [Atributos duplicados o no válidos impiden la sincronización de directorios en Office 365](https://support.microsoft.com/en-us/kb/2647098)

### <a name="objecttypemismatch"></a>ObjectTypeMismatch
#### <a name="description"></a>Descripción
Cuando se trata de Azure AD suave que coincida con dos objetos, es posible que dos objetos de diferentes "tipo de objeto" (como usuario, grupo, etc. de contacto) tiene los mismos valores para los atributos que se utilizan para realizar la coincidencia suave. Tal como duplicación de estos atributos no está permitido en Azure AD, la operación puede provocar errores de sincronización "ObjectTypeMismatch".

#### <a name="example-scenarios-for-objecttypemismatch-error"></a>Escenarios de ejemplo para ObjectTypeMismatch error
- Se crea un grupo de seguridad habilitados para correo en Office 365. Administrador agrega un usuario nuevo o contacto en local AD (que no se sincroniza a Azure AD todavía) con el mismo valor para el atributo ProxyAddresses como del grupo de Office 365.

#### <a name="example-case"></a>Ejemplo de caso

1. Administrador crea un nuevo grupo de seguridad habilitados para correo en Office 365 para el departamento de impuestos y proporciona una dirección de correo electrónico como tax@contoso.com. Se asigna el atributo ProxyAddresses para este grupo con el valor de**smtp:tax@contoso.com**
2. Agrega un nuevo usuario Contoso.com y se crea una cuenta para el usuario local con la proxyAddress como**smtp:tax@contoso.com**
3. Cuando Azure AD Connect sincronizará la nueva cuenta de usuario, obtendrá el error "ObjectTypeMismatch".

#### <a name="how-to-fix-objecttypemismatch-error"></a>Cómo corregir el error de ObjectTypeMismatch
La razón más común para el error ObjectTypeMismatch es dos objetos de tipo diferente (usuario, grupo, contacto etc.) tienen el mismo valor para el atributo ProxyAddresses. Para corregir el ObjectTypeMismatch:

1.  Identificar el duplicado proxyAddresses (u otro atributo) valor que está causando el error. También identificar qué dos \(o más\) objetos implicados en el conflicto. El informe generado por [Azure AD conectar estado de sincronización](https://aka.ms/aadchsyncerrors) puede ayudarle a identificar los dos objetos.
2. Identificar el objeto que debe seguir tienen el valor duplicado y el objeto que no debería.
3. Quitar el valor duplicado del objeto que no debería tener ese valor. Tenga en cuenta que debe hacer el cambio en el directorio de dónde proceden el objeto. En algunos casos, debe eliminar uno de los objetos en conflicto.
4. Si el cambio realizado en las instalaciones de AD, le permiten Azure AD Connect sincronizar el cambio. Informe de errores de sincronización de Azure AD conectar estado de sincronización se actualiza cada 30 minutos e incluye los errores desde el último intento de sincronización.


## <a name="duplicate-attributes"></a>Atributos duplicados
### <a name="attributevaluemustbeunique"></a>AttributeValueMustBeUnique
#### <a name="description"></a>Descripción
Esquema de Azure Active Directory permite dos o más objetos tienen el mismo valor de los siguientes atributos. Que es que cada objeto de Azure AD se obliga a tener un valor único de estos atributos en una instancia dada.

- ProxyAddresses
- UserPrincipalName

Si Azure AD Connect intenta agregar un nuevo objeto o actualizar un objeto existente con un valor para los atributos anteriores que ya se ha asignado a otro objeto de Azure Active Directory, la operación da error de sincronización de "AttributeValueMustBeUnique".
#### <a name="possible-scenarios"></a>Posibles situaciones:
1. Valor duplicado se asigna a un objeto ya sincronizado, que está en conflicto con otro objeto sincronizada.

#### <a name="example-case"></a>Ejemplo de caso:
1. **Juan Saavedra** es un usuario sincronizado de Azure Active Directory local contoso.com de Active Directory
2. Bob Smith **UserPrincipalName** local se establece como **bobs@contoso.com**.
3. Bob también tiene los siguientes valores para el atributo **proxyAddresses** :
    - smtp:bobs@contoso.com
    - smtp:bob.smith@contoso.com
    - **smtp:bob@contoso.com**
4. Un nuevo usuario, **Bob Taylor**, se agrega a la local de Active Directory.
5. De Bob Taylor **UserPrincipalName** se establece como **bobt@contoso.com**.
6. **Bob Taylor** tiene los siguientes valores para el atributo **ProxyAddresses** i. smtp:bobt@contoso.comII. smtp:bob.taylor@contoso.com
7. Objeto de Bob Taylor se ha sincronizado con Azure AD correctamente.
8. Administración decidió actualizar el atributo **ProxyAddresses** de Bob Taylor con el siguiente valor:. **smtp:bob@contoso.com**
9. Azure AD intentará actualizar objeto de Bob Taylor en Azure AD con el valor anterior, pero que no funcionará como que ProxyAddresses valor ya está asignado a Bob Smith de error de "AttributeValueMustBeUnique".

#### <a name="how-to-fix-attributevaluemustbeunique-error"></a>Cómo corregir el error de AttributeValueMustBeUnique
La razón más común para el error AttributeValueMustBeUnique es dos objetos con diferentes SourceAnchor \(immutableId\) tienen el mismo valor para los atributos ProxyAddresses o UserPrincipalName. Para corregir el error de AttributeValueMustBeUnique

1.  Identifique proxyAddresses duplicado, userPrincipalName u otro valor de atributo que causa el error. También identificar qué dos \(o más\) objetos implicados en el conflicto. El informe generado por [Azure AD conectar estado de sincronización](https://aka.ms/aadchsyncerrors) puede ayudarle a identificar los dos objetos.
2. Identificar el objeto que debe seguir tienen el valor duplicado y el objeto que no debería.
3. Quitar el valor duplicado del objeto que no debería tener ese valor. Tenga en cuenta que debe hacer el cambio en el directorio de dónde proceden el objeto. En algunos casos, debe eliminar uno de los objetos en conflicto.
4. Si el cambio realizado en las instalaciones de AD, le permiten Azure AD Connect sincronizar el cambio para corregir el error.

#### <a name="related-articles"></a>Artículos relacionados
-[Atributos duplicados o no válidos impiden la sincronización de directorios en Office 365](https://support.microsoft.com/en-us/kb/2647098)


## <a name="data-validation-failures"></a>Errores de validación de datos
### <a name="identitydatavalidationfailed"></a>IdentityDataValidationFailed
#### <a name="description"></a>Descripción
Azure Active Directory aplica varias restricciones en los datos antes de permitir que los datos se escriban en el directorio. Esto es para asegurarse de que los usuarios finales obtener la mejores experiencias posibles al utilizar las aplicaciones que dependen de estos datos.
#### <a name="scenarios"></a>Escenarios
una. El valor del atributo UserPrincipalName tiene caracteres no válida o incompatible.
b. El atributo UserPrincipalName no sigue el formato requerido.
#### <a name="how-to-fix-identitydatavalidationfailed-error"></a>Cómo corregir el error de IdentityDataValidationFailed

una. Asegúrese de que admite el atributo userPrincipalName caracteres y formato requerido.

#### <a name="related-articles"></a>Artículos relacionados
- [Preparar el aprovisionamiento de usuarios mediante sincronización de directorios para Office 365]( https://support.office.com/en-us/article/Prepare-to-provision-users-through-directory-synchronization-to-Office-365-01920974-9e6f-4331-a370-13aea4e82b3e)


### <a name="datavalidationfailed"></a>DataValidationFailed
#### <a name="description"></a>Descripción
Este es un caso muy específico que da un error de sincronización de **"DataValidationFailed"** cuando se cambia el sufijo del UserPrincipalName de un usuario de un dominio federado a otro dominio federado.

#### <a name="scenarios"></a>Escenarios
Para un usuario sincronizado, el sufijo UserPrincipalName se cambió de un dominio federado a otro dominio federado local. Por ejemplo, *UserPrincipalName = bob@contoso.com * se ha cambiado a *UserPrincipalName = bob@fabrikam.com *.

#### <a name="example"></a>Ejemplo
1. Bob Smith, una cuenta de Contoso.com, se agrega como un nuevo usuario en Active Directory con UserPrincipalNamebob@contoso.com
2. Bob se mueve a una división diferente de Contoso.com denominado Fabrikam.com y su UserPrincipalName cambia abob@fabrikam.com
3. Los dominios contoso.com y fabrikam.com son dominios federados con Azure Active Directory.
4. UserPrincipalName Bob no se actualiza y se produce un error de sincronización de "DataValidationFailed".

#### <a name="how-to-fix"></a>Procedimientos para la corrección
Si se actualizó sufijo de UserPrincipalName de un usuario de bob@ **contoso.com** a bob@ **fabrikam.com**, donde **contoso.com** y **fabrikam.com** son **dominios federados**, a continuación, siga los pasos siguientes para corregir el error de sincronización

1. Actualización UserPrincipalName del usuario en Azure AD de bob@contoso.com a bob@contoso.onmicrosoft.com. Puede usar el siguiente comando PowerShell con el módulo de PowerShell de Azure AD:`Set-MsolUserPrincipalName -UserPrincipalName bob@contoso.com -NewUserPrincipalName bob@contoso.onmicrosoft.com`

2. Permitir el siguiente ciclo de sincronización intentar la sincronización. La sincronización de hora se realizará correctamente y se actualizará UserPrincipalName de Bob a bob@fabrikam.com según lo esperado.


## <a name="largeobject"></a>LargeObject
### <a name="description"></a>Descripción
Cuando un atributo supera el límite de tamaño permitido, el límite de longitud o el límite de recuento esquema de Azure Active Directory, la operación de sincronización provocará el error de sincronización **LargeObject** o **ExceededAllowedLength** . Normalmente, este error se produce para los siguientes atributos

- userCertificate
- thumbnailPhoto
- proxyAddresses

### <a name="possible-scenarios"></a>Posibles situaciones
1. Atributo de Bob userCertificate es almacenar certificados demasiados asignados a Bob. Estos pueden incluir certificados anteriores y vencidos.
2. ThmubnailPhoto de Bob establecido en Active Directory es demasiado grande para ser sincronizado en Azure AD.
3. Durante la población automática del atributo ProxyAddresses de Active Directory, ¿tiene asignado un objeto > 500 ProxyAddresses.

### <a name="how-to-fix"></a>Procedimientos para la corrección

1. Asegúrese de que el atributo la causa del error es dentro de la limitación de permitidos.

## <a name="related-links"></a>Vínculos relacionados
- [Localizar los objetos de Active Directory en el centro de administración de Active Directory] (https://technet.microsoft.com/library/dd560661.aspx)
- [Cómo consultar Azure Active Directory para un objeto con Azure Active Directory PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx)

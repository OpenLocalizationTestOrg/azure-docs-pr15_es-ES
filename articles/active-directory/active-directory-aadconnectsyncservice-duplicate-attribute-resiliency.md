<properties
    pageTitle="Resistencia de atributo duplicados y sincronización de identidad | Microsoft Azure"
    description="Nuevo comportamiento de cómo administrar objetos con el UPN o ProxyAddress conflictos durante la sincronización de directorios con Azure AD Connect."
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
    ms.date="09/29/2016"
    ms.author="markusvi"/>



# <a name="identity-synchronization-and-duplicate-attribute-resiliency"></a>Resistencia de atributo duplicados y sincronización de identidad
Duplicado de la resistencia de atributo es una característica de Azure Active Directory eliminará fricción causado por **UserPrincipalName** y **ProxyAddress** conflictos cuando se ejecuta una de las herramientas de sincronización de Microsoft.

Estos dos atributos son generalmente se requiere ser único en el **usuario**, **grupo**o **contacto** todos los objetos en un inquilino de Azure Active Directory determinado.

> [AZURE.NOTE] Solo los usuarios pueden tener UPN.

El nuevo comportamiento que permite esta característica se encuentra en la parte de la nube de la canalización de sincronización, por lo que cliente independiente y pertinente para cualquier producto de sincronización de Microsoft incluidos Azure AD Connect, DirSync y MIM + conector. El término genérico "cliente de sincronización" se usa en este documento para representar cualquiera de estos productos.

## <a name="current-behavior"></a>Comportamiento actual
Si hay un intento de aprovisionamiento de un objeto nuevo con un valor UPN o ProxyAddress que infringe esta restricción de exclusividad, Azure Active Directory bloquea ese objeto se creen. Del mismo modo, si se actualiza un objeto con un UPN o ProxyAddress no son únicos, se produce un error en la actualización. El intento de aprovisionamiento o actualización se vuelve a intentar por el cliente de sincronización en cada ciclo de exportación y sigue sin hasta que se resuelva el conflicto. Un correo electrónico de informe de error se genera en cada intento y se registra un error por el cliente de sincronización.

## <a name="behavior-with-duplicate-attribute-resiliency"></a>Comportamiento de la resistencia de atributo duplicado
En lugar de conmutar completamente aprovisionar o actualizar un objeto con un atributo duplicado, Azure Active Directory "pone en cuarentena" el atributo duplicado que podría infringir la restricción de exclusividad. Si este atributo es necesario para aprovisionar, como UserPrincipalName, el servicio asigna un valor de marcador de posición. Es el formato de los valores siguientes temporales  
"***<OriginalPrefix>+<4DigitNumber>@<InitialTenantDomain>. onmicrosoft.com***".  
Si el atributo no es necesario, como un **ProxyAddress**, Azure Active Directory simplemente pone en cuarentena el atributo de conflicto y continúa con la creación de objetos o la actualización.

Al poner en cuarentena el atributo, se envía información sobre el conflicto en el correo electrónico el informe de error mismo usado en el comportamiento anterior. Sin embargo, esta información solo aparece en el informe de errores de una sola vez, cuando se produce la cuarentena, no siga haber iniciado sesión en futuros mensajes de correo electrónico. Además, puesto que la exportación de este objeto se ha realizado correctamente, el cliente de sincronización no registra un error y no se vuelva a intentar la crear / actualizar la operación de ciclos de sincronización subsiguientes.

Compatibilidad con este comportamiento se ha agregado un nuevo atributo a las clases de objeto de usuario, grupo y contacto:  
**DirSyncProvisioningErrors**

Se trata de un atributo con varios valores que se utiliza para almacenar los atributos en conflicto que podría infringir la restricción de exclusividad se deben agregar normalmente. Una tarea de temporizador de fondo se ha habilitado en Azure Active Directory que se ejecute cada hora para buscar conflictos en los atributos duplicados que se han resuelto y elimina automáticamente los atributos en cuestión de cuarentena.

### <a name="enabling-duplicate-attribute-resiliency"></a>Habilitar la resistencia de atributo duplicado
Resistencia de atributo duplicados será el nuevo comportamiento predeterminado entre todos los inquilinos de Azure Active Directory. Aparecerá en predeterminada para todos los inquilinos que habilitó la sincronización por primera vez en 22 de agosto de 2016 o posterior. Los inquilinos que habilita la sincronización antes de esa fecha tendrá la característica habilitada en lotes. Esta implementación comenzará en septiembre de 2016 y se enviará una notificación de correo electrónico al contacto de notificación técnica de cada inquilino con la fecha específica cuando se habilitarán la característica.

Una vez que se ha activado duplicar resistencia de atributo no puede deshabilitarse.

Para comprobar si la característica está habilitada para el inquilino, puede hacerlo por descargar la versión más reciente del módulo Azure Active Directory PowerShell y ejecutar:

`Get-MsolDirSyncFeatures -Feature DuplicateUPNResiliency`

`Get-MsolDirSyncFeatures -Feature DuplicateProxyAddressResiliency`

Si le gustaría proactiva habilitar la característica antes de que está activada para el inquilino, puede hacerlo descargar la versión más reciente del módulo Azure Active Directory PowerShell y ejecutando:

`Set-MsolDirSyncFeature -Feature DuplicateUPNResiliency -Enable $true`

`Set-MsolDirSyncFeature -Feature DuplicateProxyAddressResiliency -Enable $true`

## <a name="identifying-objects-with-dirsyncprovisioningerrors"></a>Identificar los objetos con DirSyncProvisioningErrors
Existen actualmente dos métodos para identificar los objetos que tienen estos errores debido a los conflictos de propiedad duplicada, Azure Active Directory PowerShell y el Portal de administración de Office 365. Existen planes para ampliar adicionales portal basado en informes en el futuro.

### <a name="azure-active-directory-powershell"></a>Azure Active Directory PowerShell
Para los cmdlets de PowerShell en este tema, es verdadero lo siguiente:

- Todos los cmdlets siguientes distinguen mayúsculas de minúsculas.
- Siempre se deben incluir la **-ErrorCategory PropertyConflict** . Actualmente no hay ningún otro tipo de **ErrorCategory**, pero puede ampliarse en el futuro.

En primer lugar, introducción ejecutando **MsolService conectar** y especificar credenciales de administrador de inquilinos.

A continuación, use los cmdlets y los operadores siguientes para ver los errores de diferentes maneras:

1. [Ver todos](#see-all)

2. [Por tipo de propiedad](#by-property-type)

3. [Valor en conflicto](#by-conflicting-value)

4. [Uso de una cadena de búsqueda](#using-a-string-search)

5. [Ordenar](#sorted)

6. [En una cantidad limitada o todos](#in-a-limited-quantity-or-all)


#### <a name="see-all"></a>Ver todos
Una vez conectado, para ver una lista general de aprovisionamiento de atributo errores en el inquilino ejecutan:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict`

Esto produce un resultado similar al siguiente:  
 ![Get-MsolDirSyncProvisioningError](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/1.png "Get-MsolDirSyncProvisioningError")  


#### <a name="by-property-type"></a>Por tipo de propiedad
Para ver errores de tipo de propiedad, agregue el indicador **- PropertyName** con el argumento **UserPrincipalName** o **ProxyAddresses** :

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName UserPrincipalName`

O

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName ProxyAddresses`

#### <a name="by-conflicting-value"></a>Valor en conflicto
Para ver errores relacionados con una propiedad específica agregar la marca de **-PropertyValue** (**- PropertyName** deben usarse también al agregar este marcador):

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyValue User@domain.com -PropertyName UserPrincipalName`


#### <a name="using-a-string-search"></a>Usar una búsqueda de cadena
Para realizar una búsqueda de cadena amplia use el indicador **- SearchString** . Se puede utilizar independientemente de todos los indicadores anteriores, con la excepción de **ErrorCategory - PropertyConflict**, que siempre se requiere:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -SearchString User`

#### <a name="in-a-limited-quantity-or-all"></a>En una cantidad limitada o todos
1. **MaxResults <Int> ** puede utilizarse para limitar la consulta a un número específico de valores.

2. **Todos los** puede usarse para asegurarse de que se recuperen todos los resultados en el caso de que existe una gran cantidad de errores.

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -MaxResults 5`

## <a name="office-365-admin-portal"></a>Portal de administración de Office 365

Puede ver los errores de sincronización de directorios en el centro de administración de Office 365. El informe en el portal de Office 365 solo muestra los objetos de **usuario** que tienen estos errores. No se muestra información acerca de los conflictos entre los **grupos** y **contactos**.


![Usuarios activos] (./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/1234.png "Usuarios activos")

Para obtener instrucciones sobre cómo ver errores de sincronización de directorios en el centro de administración de Office 365, consulte [identificar problemas de sincronización de directorios en Office 365](https://support.office.com/en-us/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067).


### <a name="identity-synchronization-error-report"></a>Informe de errores de sincronización de identidad
Cuando se controla un objeto con un conflicto de atributo duplicado con este nuevo comportamiento se incluye una notificación en el estándar informe de errores de sincronización de identidad de correo electrónico que se envía a la notificación técnica de contacto del inquilino. Sin embargo, hay un cambio importante en este comportamiento. En el pasado, se incluirá información sobre un conflicto de atributo duplicado en cada informe de errores de las siguientes hasta que se ha resuelto el conflicto. Con este nuevo comportamiento, la notificación de error de un conflicto determinado solo aparecerá una vez en el momento en que se ha puesto en cuarentena el atributo en conflicto.

Aquí tiene un ejemplo del aspecto de la notificación de correo electrónico para un conflicto de ProxyAddress:  
    ![Usuarios activos](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/6.png "Active Users")  

## <a name="resolving-conflicts"></a>Resolver conflictos
Solución de problemas de estrategia y resolución tácticas estos errores no debe ser diferentes de la manera en que se controlan los errores de atributo duplicado en el pasado. La única diferencia es que la tarea de temporizador limpia a través del inquilino en el lado de servicio para agregar automáticamente el atributo en cuestión al propio objeto una vez que se resuelva el conflicto.

El siguiente artículo describe varias estrategias de solución de problemas y resolución: [Duplicar o atributos no válidos impiden la sincronización de directorios en Office 365](https://support.microsoft.com/kb/2647098).

## <a name="known-issues"></a>Problemas conocidos
Ninguno de estos problemas conocidos hace degradación pérdida o servicio de datos. Algunos de ellos son estéticas, otras producen estándar "*resistencia anterior*" atributo duplicado errores se inicia en lugar de poner en cuarentena el atributo de conflicto y otro hace que requiere revisión de seguridad manual adicional determinados errores.

**Comportamiento de principales:**

1. Objetos con las configuraciones de atributo específico continuarán recibiendo errores de exportación en lugar de los atributos duplicados se pongan en cuarentena.  
Por ejemplo:

    una. Se crea el nuevo usuario en Active Directory con un UPN de **Joe@contoso.com** y ProxyAddress**smtp:Joe@contoso.com**

    b. Las propiedades de este objeto en conflicto con un grupo existente, donde es ProxyAddress **SMTP:Joe@contoso.com**.

    c. Tras la exportación, se produce un error de **conflicto de ProxyAddress** en lugar de tener los atributos de conflicto puesto en cuarentena. Se vuelve a intentar la operación en cada ciclo de sincronización subsiguientes, como lo hubiera sido antes de habilita la característica de la resistencia.

2. Si se crean dos grupos locales con la misma dirección SMTP, uno falla a disposición en el primer intento con un error **ProxyAddress** duplicado estándar. Sin embargo, el valor duplicado correctamente está en cuarentena en el siguiente ciclo de sincronización.

**Informe de Portal de office**:

1. Mensaje de error detallado para dos objetos de un conjunto de conflicto UPN es el mismo. Esto indica que ambos tenían su UPN modificada o puesto en cuarentena, cuando en realidad solo uno de ellos había ningún dato cambiado.

2. Mensaje de error detallado para un conflicto UPN muestra displayName incorrecto para un usuario que ha tenido su UPN cambiado o puesto en cuarentena. Por ejemplo:

    una. **El usuario A** sincroniza primero con **UPN = User@contoso.com **.

    b. **Usuario B** se intenta sincronizarse siguiente con **UPN = User@contoso.com **.

    c. **Usuario B** UPN cambia a **User1234@contoso.onmicrosoft.com** y **User@contoso.com** se agrega a **DirSyncProvisioningErrors**.

    d. El mensaje de error para el **Usuario B** debe indicar que ya tiene **un usuario** **User@contoso.com** como un UPN, pero muestra **Usuario B** propio displayName.



**Informe de errores de sincronización de identidad**:

El vínculo para *conocer los pasos sobre cómo resolver este problema* es incorrecto:  
    ![Usuarios activos](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/6.png "Active Users")  

Debe apuntar a [https://aka.ms/duplicateattributeresiliency](https://aka.ms/duplicateattributeresiliency).


## <a name="see-also"></a>Vea también

- [Sincronización de Azure AD Connect](active-directory-aadconnectsync-whatis.md)

- [Integrar las identidades locales con Azure Active Directory](active-directory-aadconnect.md)

- [Identificar problemas de sincronización de directorios en Office 365](https://support.office.com/en-us/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067)

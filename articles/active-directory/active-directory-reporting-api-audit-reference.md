<properties
    pageTitle="Auditoría de Azure Active Directory referencia de la API | Microsoft Azure"
    description="Cómo empezar a trabajar con la API de auditoría de Azure Active Directory"
    services="active-directory"
    documentationCenter=""
    authors="dhanyahk"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="10/24/2016"
    ms.author="dhanyahk;markvi"/>

# <a name="azure-active-directory-audit-api-reference"></a>Auditoría de Azure Active Directory referencia de la API

Este tema es parte de una colección de temas de Azure Active Directory con la API de informes.  
Informes de Azure AD proporcionan una API que permite obtener acceso a los datos de auditoría usando código o herramientas relacionadas.
El ámbito de este tema es proporcionar información de referencia sobre la **API de auditoría**.

Vea:

- [Registros de auditoría](active-directory-reporting-azure-portal.md#audit-logs) para obtener más información
- [Introducción a Azure Active Directory Reporting API](active-directory-reporting-api-getting-started.md) para obtener más información acerca de la API de generación de informes.

Si tiene preguntas, problemas o comentarios, póngase en contacto con [Informes le ayudan a AAD](mailto:aadreportinghelp@microsoft.com).


## <a name="who-can-access-the-data"></a>¿Quién puede tener acceso a los datos?

- Usuarios de la función de administrador de seguridad o lector de seguridad

- Administradores globales

- Cualquier aplicación que tiene autorización para acceder a la API (autorización de aplicación puede ser el programa de instalación solamente basándose en los permisos de administrador Global)



## <a name="prerequisites"></a>Requisitos previos

Para obtener acceso a este informe a través de la API de informes, debe tener:

- Un [Azure Active Directory gratuita o mejor edition](active-directory-editions.md)

- Completar los [requisitos previos para obtener acceso a la Azure AD API de informes](active-directory-reporting-api-prerequisites.md). 
 

##<a name="accessing-the-api"></a>Acceso a la API

Puede obtener acceso a esta API mediante el [Explorador de gráfico](https://graphexplorer2.cloudapp.net) o mediante programación, por ejemplo, mediante PowerShell. Fin de PowerShell para interpretar correctamente la sintaxis de filtro de OData usada en llamadas del resto del gráfico de AAD, debe usar la marca inversa (también conocido como: acento grave) carácter "escape" el carácter $. El carácter de marca inversa sirve como [carácter de escape de PowerShell](https://technet.microsoft.com/library/hh847755.aspx), que permite PowerShell para realizar una interpretación literal del carácter $ y evitar confusión como un nombre de variable de PowerShell (ie: $filter).

El objetivo de este tema se encuentra en el Explorador de gráfico. Para obtener un ejemplo de PowerShell, vea este [script de PowerShell](active-directory-reporting-api-audit-samples.md#powershell-script).

 
 

## <a name="api-endpoint"></a>Punto final de la API


Puede tener acceso a esta API utilizando el URI siguiente:  

    https://graph.windows.net/contoso.com/activities/audit?api-version=beta

No hay ningún límite del número de registros devueltos por la API de auditoría de Azure AD (con OData paginación).
Límites de retención de datos de informes, consulte [Informes directivas de retención](active-directory-reporting-retention.md).

Esta llamada devuelve los datos en lotes. Cada lote tiene un máximo de 1000 registros.  
Para obtener el siguiente lote de registros, use el siguiente vínculo. Obtener la información de skiptoken desde el primer conjunto de registros devueltos. El token de saltar al final del resultado establecerá.  

    https://graph.windows.net/contoso.com/activities/audit?api-version=beta&%24skiptoken=-1339686058




## <a name="supported-filters"></a>Filtros compatibles

Puede limitar el número de registros devueltos por una API llamar en forma de un filtro.  
Para iniciar sesión API relacionadas con datos, se admiten los siguientes filtros:

- **$top =\<número de registros que se devolverá\> ** - para limitar el número de registros devueltos. Se trata de una operación cara. No debe utilizar este filtro si desea volver a miles de objetos.     
- **$filter =\<la instrucción de filtro\> ** - especificar basándose en los campos de filtro compatibles, el tipo de registros que le interesan



## <a name="supported-filter-fields-and-operators"></a>Operadores y campos de filtro admitidos

Para especificar el tipo de registros que le interesa, puede crear un informe de filtro que puede contener uno o una combinación de los siguientes campos de filtro:

- [activityDate](#activitydate) - define una fecha o un intervalo de fechas
- [activityType](#activitytype) - define el tipo de una actividad
- [actividad](#activity) - define la actividad como cadena  
- [nombre del actor](#actorname) - define el actor en forma de nombre de actor
- [actor o Id. de objeto](#actorobjectid) - define el actor en forma de ID de actor   
- [actor o upn](#actorupn) - define el actor en forma principio de actor nombre de usuario (UPN) 
- [nombre del destino](#targetname) - define el destino en forma de nombre de actor
- [destino o Id. de objeto](#targetobjectid) - define el destino en forma de Id. de destino  
- [destino o upn](#targetupn) - define el actor en forma principio de actor nombre de usuario (UPN)   




----------

### <a name="activitydate"></a>activityDate

**Operadores compatibles**: EC, ge, le, gt, lt

**Ejemplo**:

    $filter=tdomain + 'activities/audit?api-version=beta&`$filter=eventTime gt ' + $7daysago    

**Notas**:

debe ser la fecha y hora en formato UTC

----------

### <a name="activitytype"></a>activityType

**Operadores compatibles**: EC

**Ejemplo**:

    $filter=activityType eq 'User'  

**Notas**:

distingue mayúsculas de minúsculas

----------

### <a name="activity"></a>actividad

**Operadores compatibles**: EC, contiene, startsWith

**Ejemplo**:

    $filter=activity eq 'Add application' or contains(activity, 'Application') or startsWith(activity, 'Add')   

**Notas**:

distingue mayúsculas de minúsculas

----------

### <a name="actorname"></a>nombre del actor

**Operadores compatibles**: EC, contiene, startsWith

**Ejemplo**:

    $filter=actor/name eq 'test' or contains(actor/name, 'test') or startswith(actor/name, 'test')  

**Notas**:

entre mayúsculas y minúsculas

    

----------
### <a name="actorobjectid"></a>actor o Id. de objeto

**Operadores compatibles**: EC

**Ejemplo**:

    $filter=actor/objectId eq 'e8096343-86a2-4384-b43a-ebfdb17600ba'    

----------
### <a name="targetname"></a>nombre del destino

**Operadores compatibles**: EC, contiene, startsWith

**Ejemplo**:

    $filter=targets/any(t: t/name eq 'some name')   

**Notas**:

Entre mayúsculas y minúsculas

----------

### <a name="targetupn"></a>destino o upn

**Operadores compatibles**: EC, startsWith

**Ejemplo**:

    $filter=targets/any(t: startswith(t/Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.TargetResourceUserEntity/userPrincipalName,'abc')) 

**Notas**:

- Entre mayúsculas y minúsculas
- Debe agregar el espacio de nombres completo al consultar Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.TargetResourceUserEntity

----------

### <a name="targetobjectid"></a>destino o Id. de objeto

**Operadores compatibles**: EC

**Ejemplo**:

    $filter=targets/any(t: t/objectId eq 'e8096343-86a2-4384-b43a-ebfdb17600ba')    

----------

### <a name="actorupn"></a>actor o upn

**Operadores compatibles**: EC, startsWith

**Ejemplo**:

    $filter=startswith(actor/Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.ActorUserEntity/userPrincipalName,'abc')  

**Notas**:

- Entre mayúsculas y minúsculas 
- Debe agregar el espacio de nombres completo al consultar Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.ActorUserEntity

----------




## <a name="next-steps"></a>Pasos siguientes

- ¿Desea ver ejemplos de actividades de sistema filtrada? Consulte los [ejemplos de API de auditoría de Azure Active Directory](active-directory-reporting-api-audit-samples.md).

- ¿Desea obtener más información sobre la Azure AD reporting API? Consulte [Introducción a Azure Active Directory Reporting API](active-directory-reporting-api-getting-started.md).
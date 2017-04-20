<properties
    pageTitle="Informe de actividad de inicio de sesión de Azure Active Directory referencia de la API | Microsoft Azure"
    description="Referencia de la API de informe de actividad de inicio de sesión de Azure Active Directory"
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
    ms.date="09/25/2016"
    ms.author="dhanyahk;markvi"/>

# <a name="azure-active-directory-sign-in-activity-report-api-reference"></a>Informe de actividad de inicio de sesión de Azure Active Directory referencia de la API


Este tema es parte de una colección de temas de Azure Active Directory con la API de informes.  
Informes de Azure AD proporcionan una API que le permite acceder a los datos del informe de actividad de inicio de sesión utilizando código o herramientas relacionadas.
El ámbito de este tema es proporcionar información de referencia sobre la **API de informe de actividad de inicio de sesión**.

Vea:

- [Las actividades de inicio de sesión](active-directory-reporting-azure-portal.md#sign-in-activities) para obtener más información
- [Introducción a Azure Active Directory Reporting API](active-directory-reporting-api-getting-started.md) para obtener más información acerca de la API de generación de informes.

Si tiene preguntas, problemas o comentarios, póngase en contacto con [Informes le ayudan a AAD](mailto:aadreportinghelp@microsoft.com).



## <a name="who-can-access-the-api-data"></a>¿Quién puede tener acceso a los datos de la API?

- Usuarios de la función de administrador de seguridad o lector de seguridad

- Administradores globales

- Cualquier aplicación que tiene autorización para acceder a la API (autorización de aplicación puede ser el programa de instalación solamente basándose en los permisos de administrador Global)



## <a name="prerequisites"></a>Requisitos previos

Para obtener acceso a este informe a través de la API de generación de informes, debe tener:

- Un [Azure Active Directory Premium P1 o P2 edition](active-directory-editions.md)

- Completar los [requisitos previos para obtener acceso a la Azure AD API de informes](active-directory-reporting-api-prerequisites.md). 


##<a name="accessing-the-api"></a>Acceso a la API

Puede obtener acceso a esta API mediante el [Explorador de gráfico](https://graphexplorer2.cloudapp.net) o mediante programación, por ejemplo, mediante PowerShell. Fin de PowerShell para interpretar correctamente la sintaxis de filtro de OData usada en llamadas del resto del gráfico de AAD, debe usar la marca inversa (también conocido como: acento grave) carácter "escape" el carácter $. El carácter de marca inversa sirve como [carácter de escape de PowerShell](https://technet.microsoft.com/library/hh847755.aspx), que permite PowerShell para realizar una interpretación literal del carácter $ y evitar confusión como un nombre de variable de PowerShell (ie: $filter).

El objetivo de este tema se encuentra en el Explorador de gráfico. Para obtener un ejemplo de PowerShell, vea este [script de PowerShell](active-directory-reporting-api-sign-in-activity-samples.md#powershell-script).


## <a name="api-endpoint"></a>Punto final de la API

Puede tener acceso a esta API mediante el siguiente URI base:  
    
    https://graph.windows.net/contoso.com/activities/signinEvents?api-version=beta  



Debido a la cantidad de datos, esta API tiene un límite de un millón de registros devueltos. 

Esta llamada devuelve los datos en lotes. Cada lote tiene un máximo de 1000 registros.  
Para obtener el siguiente lote de registros, use el siguiente vínculo. Obtener la información de [skiptoken](https://msdn.microsoft.com/library/dd942121.aspx) desde el primer conjunto de registros devueltos. El token de saltar al final del resultado establecerá.  

    https://graph.windows.net/$tenantdomain/activities/signinEvents?api-version=beta&%24skiptoken=-1339686058


## <a name="supported-filters"></a>Filtros compatibles

Puede limitar el número de registros devueltos por una API llamar en forma de un filtro.  
Para iniciar sesión API relacionadas con datos, se admiten los siguientes filtros:

- **$top =\<número de registros que se devolverá\> ** - para limitar el número de registros devueltos. Se trata de una operación cara. No debe utilizar este filtro si desea volver a miles de objetos.  
- **$filter =\<la instrucción de filtro\> ** - especificar basándose en los campos de filtro compatibles, el tipo de registros que le interesan



## <a name="supported-filter-fields-and-operators"></a>Operadores y campos de filtro admitidos

Para especificar el tipo de registros que le interesa, puede crear un informe de filtro que puede contener uno o una combinación de los siguientes campos de filtro:

- [signinDateTime](#signindatetime) - define una fecha o un intervalo de fechas

- [ID](#userid) - define específicos de una usuario según el identificador. de usuario

- [userPrincipalName](#userprincipalname) - define específicos de una usuario según el nombre principal de usuario (UPN) del usuario

- [ID](#appid) - define una determinada aplicación según el identificador de la aplicación

- [appDisplayName](#appdisplayname) - define una determinada aplicación según el nombre para mostrar de la aplicación

- [loginStatus](#loginStatus) - define el estado de los inicios de sesión (éxito o error)


> [AZURE.NOTE] Cuando se utiliza el Explorador de gráfico, que la necesidad de usar mayúsculas y minúsculas letras es los campos de filtro.


Para limitar el ámbito de los datos devueltos, que puede crear combinaciones de los filtros disponibles y los campos de filtro. Por ejemplo, la siguiente instrucción devuelve los registros de 10 principales entre de 2016 1 de julio y 6a de julio de 2016:

    https://graph.windows.net/contoso.com/activities/signinEvents?api-version=beta&$top=10&$filter=signinDateTime+ge+2016-07-01T17:05:21Z+and+signinDateTime+le+2016-07-07T00:00:00Z


----------

### <a name="signindatetime"></a>signinDateTime

**Operadores compatibles**: EC, ge, le, gt, lt

**Ejemplo**:

Uso de una fecha específica

    $filter=signinDateTime+eq+2016-04-25T23:59:00Z  



Usar un intervalo de fechas    

    $filter=signinDateTime+ge+2016-07-01T17:05:21Z+and+signinDateTime+le+2016-07-07T17:05:21Z


**Notas**:

Debe ser el parámetro de fecha y hora en formato UTC 


----------

### <a name="userid"></a>identificador de usuario

**Operadores compatibles**: EC

**Ejemplo**:

    $filter=userId+eq+’00000000-0000-0000-0000-000000000000’

**Notas**:

El valor del identificador de usuario es un valor de cadena



----------

### <a name="userprincipalname"></a>userPrincipalName

**Operadores compatibles**: EC

**Ejemplo**:

    $filter=userPrincipalName+eq+'audrey.oliver@wingtiptoysonline.com' 


**Notas**:

El valor de userPrincipalName es un valor de cadena

----------

### <a name="appid"></a>Id

**Operadores compatibles**: EC

**Ejemplo**:

    $filter=appId+eq+’00000000-0000-0000-0000-000000000000’



**Notas**:

El valor de ID es un valor de cadena

----------


### <a name="appdisplayname"></a>appDisplayName

**Operadores compatibles**: EC

**Ejemplo**:

    $filter=appDisplayName+eq+'Azure+Portal' 


**Notas**:

El valor de appDisplayName es un valor de cadena

----------

### <a name="loginstatus"></a>loginStatus

**Operadores compatibles**: EC

**Ejemplo**:

    $filter=loginStatus+eq+'1'  


**Notas**:

Existen dos opciones para loginStatus: 0 - correcto, 1 - error

----------



## <a name="next-steps"></a>Pasos siguientes

- ¿Desea ver ejemplos de actividades de inicio de sesión filtradas? Consulte los [ejemplos de informe API de actividad de inicio de sesión de Azure Active Directory](active-directory-reporting-api-sign-in-activity-samples.md).

- ¿Desea obtener más información sobre la Azure AD reporting API? Consulte [Introducción a Azure Active Directory Reporting API](active-directory-reporting-api-getting-started.md).
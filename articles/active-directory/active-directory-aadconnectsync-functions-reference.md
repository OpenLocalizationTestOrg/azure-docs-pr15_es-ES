<properties
    pageTitle="Sincronización de Azure AD Connect: referencia de funciones | Microsoft Azure"
    description="Referencia de expresiones de aprovisionamiento descriptiva de sincronización de Azure AD Connect."
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
    ms.date="08/23/2016"
    ms.author="andkjell;markvi"/>


# <a name="azure-ad-connect-sync-functions-reference"></a>Sincronización de Azure AD Connect: referencia de funciones

En Azure AD Connect, las funciones se utilizan para manipular un valor de atributo durante la sincronización.  
La sintaxis de las funciones se expresa con el siguiente formato:  
`<output type> FunctionName(<input type> <position name>, ..)`

Si una función se sobrecarga y acepta sintaxis múltiple, se muestran todas las sintaxis válidas.  
Inflexible de las funciones y comprueben que el tipo pasa coincide con el tipo documentado.  
Si el tipo no coincide, se produce un error.

Los tipos se expresan con la siguiente sintaxis:

- **Papelera** – binario.
- **bool** : booleano
- **dt** -UTC Fecha y hora
- **enumeración** – enumeración de constantes conocidas
- **exp** : expresión que se espera que se evalúa como un valor Boolean
- **mvbin** – binario multivalor
- **mvstr** : cadena de varios valores
- **mvref** : referencia de varios valores
- **núm** -numérico
- **ref** : referencia
- **str** : cadena
- **var** : una variante de (casi) cualquier otro tipo
- **Anular** : no devuelve un valor

Las funciones con los tipos **mvbin**, **mvstr**y **mvref** sólo pueden trabajar en atributos con varios valores. Funciones con **ref** de **ubicación**, **str**y trabajar en atributos de valor único y con varios valores.

## <a name="functions-reference"></a>Referencia de funciones

Lista de funciones | | | | |  
--------- | --------- | --------- | --------- | --------- | ---------
**Conversión** |  
[CBool](#cbool) | [CDate](#cdate) | [CGuid](#cguid) | [ConvertFromBase64](#convertfrombase64)
[ConvertToBase64](#converttobase64) | [ConvertFromUTF8Hex](#convertfromutf8hex) | [ConvertToUTF8Hex](#converttoutf8hex) | [CNum](#cnum)
[CRef](#cref) | [CStr](#cstr) | [StringFromGuid](#StringFromGuid) | [StringFromSid](#stringfromsid)
**Fecha / hora** |  
[DateAdd](#dateadd) | [DateFromNum](#datefromnum) | [FormatDateTime](#formatdatetime) | [Ahora](#now)
[NumFromDate](#numfromdate) |  
**Directorio** |  
[DNComponent](#dncomponent) | [DNComponentRev](#dncomponentrev) | [EscapeDNComponent](#escapedncomponent)
**Evaluación** |  
[IsBitSet](#isbitset) | [IsDate](#isdate) | [IsEmpty](#isempty) | [IsGuid](#isguid)
[IsNull](#isnull) | [IsNullOrEmpty](#isnullorempty) | [IsNumeric](#isnumeric) | [IsPresent](#ispresent) |
[IsString](#isstring) |  
**Matemáticas** |  
[Bit.y](#bitand) | [BitOr](#bitor) | [RandomNum](#randomnum)
**Valor múltiple** |  
[Contiene](#contains) | [Recuento](#count) | [Elemento](#item) | [ItemOrNull](#itemornull)
[Unirse a](#join) | [RemoveDuplicates](#removeduplicates) | [División](#split) |
**Flujo del programa** |  
[Error](#error) | [IIF](#iif)  | [Cambiar](#switch)
**Texto** |  
[GUID](#guid) | [InStr](#instr) | [InStrRev](#instrrev) | [Minús](#lcase)
[Izquierda](#left) | [Largo](#len) | [LTrim](#ltrim) | [Extrae](#mid)
[PadLeft](#padleft) | [PadRight](#padright) | [PCase](#pcase) | [Reemplazar](#replace)
[ReplaceChars](#replacechars) | [Correcto](#right) | [RTrim](#rtrim) | [Recortar](#trim)
[UCase](#ucase) | [Word](#word)

----------
### <a name="bitand"></a>Bit.y

**Descripción:**  
La función bit.y establece bits especificados en un valor.

**Sintaxis:**  
`num BitAnd(num value1, num value2)`

- valor1, valor2: valores numéricos que deberían estar unidas por and juntos

**Notas:**  
Esta función convierte ambos parámetros en la representación binaria y establece un poco en:

- 0 - si uno o ambos de los bits correspondientes de *máscara* y *marca* son 0
- 1 - si ambos de los bits correspondientes son 1.

En otras palabras, devuelve 0 en todos los casos excepto cuando los bits correspondientes de dos parámetros son 1.

**Ejemplo:**  
`BitAnd(&HF, &HF7)`  
Devuelve 7 porque hexadecimal "F" y "F7" se evalúan como este valor.

----------
### <a name="bitor"></a>BitOr

**Descripción:**  
La función BitOr establece bits especificados en un valor.

**Sintaxis:**  
`num BitOr(num value1, num value2)`

- valor1, valor2: valores numéricos que deberían estar or juntos

**Notas:**  
Esta función convierte ambos parámetros en la representación binaria y establece un bit 1 si uno o ambos de los bits correspondientes de máscara y marca son 1 y 0 si ambos de los bits correspondientes son 0. En otras palabras, devuelve 1 en todos los casos excepto cuando los bits correspondientes de dos parámetros son 0.

----------
### <a name="cbool"></a>CBool

**Descripción:**  
La función CBool devuelve un valor Boolean en función de la expresión evaluada

**Sintaxis:**  
`bool CBool(exp Expression)`

**Notas:**  
Si la expresión se evalúa como un valor distinto de cero, CBool devuelve True, else devuelve False.

**Ejemplo:**  
`CBool([attrib1] = [attrib2])`  

Devuelve True si ambos atributos tienen el mismo valor.

----------
### <a name="cdate"></a>CDate

**Descripción:**  
La función CDate devuelve un valor de DateTime de UTC de una cadena. DateTime no es un tipo de atributo nativa sincronizado, pero se usa algunas de las funciones.

**Sintaxis:**  
`dt CDate(str value)`

- Valor: Una cadena con una fecha, hora y, opcionalmente, zona horaria

**Notas:**  
La cadena devuelta esté siempre en UTC.

**Ejemplo:**  
`CDate([employeeStartTime])`  
Devuelve que un valor DateTime basado en el empleado la hora de inicio

`CDate("2013-01-10 4:00 PM -8")`  
Devuelve un DateTime que representa "2013-01-11 12:00 A.M."

----------
### <a name="cguid"></a>CGuid

**Descripción:**  
La función CGuid convierte la representación de cadena de un GUID en la representación binaria.

**Sintaxis:**  
`bin CGuid(str GUID)`

- Una cadena con formato en este modelo: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx o {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

----------
### <a name="contains"></a>Contiene

**Descripción:**  
La función Contains busca una cadena dentro de un atributo multivalor

**Sintaxis:**  
`num Contains (mvstring attribute, str search)`-distingue mayúsculas de minúsculas  
`num Contains (mvstring attribute, str search, enum Casetype)`  
`num Contains (mvref attribute, str search)`-distingue mayúsculas de minúsculas

- atributo: el atributo de búsqueda multivalor.
- Buscar: cadena para buscar en el atributo.
- Casetype: CaseInsensitive o CaseSensitive.

Devuelve el índice en el atributo de valor múltiple donde se encuentra la cadena. Si no se encuentra la cadena, se devuelve 0.

**Notas:**  
Para los atributos de cadena multivalor, la búsqueda busca subcadenas en los valores.  
Para los atributos de referencia, la cadena de búsqueda debe coincidir exactamente con el valor para que se considera a una coincidencia.

**Ejemplo:**  
`IIF(Contains([proxyAddresses],"SMTP:")>0,[proxyAddresses],Error("No primary SMTP address found."))`  
Si el atributo proxyAddresses tiene una dirección de correo electrónico principal (indicada por mayúsculas "SMTP:"), a continuación, devuelve el atributo proxyAddress, else devuelve un error.

----------
### <a name="convertfrombase64"></a>ConvertFromBase64

**Descripción:**  
La función ConvertFromBase64 convierte el valor especificado en base 64 codificado en una cadena normal.

**Sintaxis:**  
`str ConvertFromBase64(str source)`-supone Unicode para la codificación  
`str ConvertFromBase64(str source, enum Encoding)`

- fuente: cadena codificado de base 64  
- Codificación: Unicode, ASCII, UTF8

**Ejemplo**  
`ConvertFromBase64("SABlAGwAbABvACAAdwBvAHIAbABkACEA")`  
`ConvertFromBase64("SGVsbG8gd29ybGQh", UTF8)`

Dos ejemplos devuelven "*Hola a todos!*"

----------
### <a name="convertfromutf8hex"></a>ConvertFromUTF8Hex

**Descripción:**  
La función ConvertFromUTF8Hex convierte el valor de UTF8 codificada en hexadecimal especificado en una cadena.

**Sintaxis:**  
`str ConvertFromUTF8Hex(str source)`

- fuente: cadena de codificación de 2 bytes UTF8

**Notas:**  
La diferencia entre esta función y ConvertFromBase64([],UTF8) que el resultado es descriptivo para el atributo DN.  
Este formato se usa Azure Active Directory como DN.

**Ejemplo:**  
`ConvertFromUTF8Hex("48656C6C6F20776F726C6421")`  
Devuelve "*Hola a todos!*"

----------
### <a name="converttobase64"></a>ConvertToBase64

**Descripción:**  
La función ConvertToBase64 convierte una cadena en una cadena de base 64 Unicode.  
Convierte el valor de una matriz de enteros en su representación de cadena equivalente codificada con dígitos de base 64.

**Sintaxis:**  
`str ConvertToBase64(str source)`

**Ejemplo:**  
`ConvertToBase64("Hello world!")`  
Devuelve "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

----------
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex

**Descripción:**  
La función ConvertToUTF8Hex convierte una cadena en un valor hexadecimal UTF8 codificada.

**Sintaxis:**  
`str ConvertToUTF8Hex(str source)`

**Notas:**  
El formato de salida de esta función se usa Azure Active Directory como formato del atributo DN.

**Ejemplo:**  
`ConvertToUTF8Hex("Hello world!")`  
Devuelve 48656C6C6F20776F726C6421

----------
### <a name="count"></a>Recuento

**Descripción:**  
La función Count devuelve el número de elementos de un atributo multivalor

**Sintaxis:**  
`num Count(mvstr attribute)`

----------
### <a name="cnum"></a>CNum

**Descripción:**  
La función CNum toma una cadena y devuelve un tipo de datos numéricos.

**Sintaxis:**  
`num CNum(str value)`

----------
### <a name="cref"></a>CRef

**Descripción:**  
Convierte una cadena en un atributo de referencia

**Sintaxis:**  
`ref CRef(str value)`

**Ejemplo:**  
`CRef("CN=LC Services,CN=Microsoft,CN=lcspool01,CN=Pools,CN=RTC Service," & %Forest.LDAP%)`

----------
### <a name="cstr"></a>CStr

**Descripción:**  
La función CStr se convierte en un tipo de datos string.

**Sintaxis:**  
`str CStr(num value)`  
`str CStr(ref value)`  
`str CStr(bool value)`  

- valor: puede ser un valor numérico, el atributo de referencia o Boolean.

**Ejemplo:**  
`CStr([dn])`  
Podría devolver "cn = Juan, dc = contoso, dc = com"

----------
### <a name="dateadd"></a>DateAdd

**Descripción:**  
Devuelve una fecha que contiene una fecha a la que se ha agregado un intervalo de tiempo especificado.

**Sintaxis:**  
`dt DateAdd(str interval, num value, dt date)`

- intervalo: expresión de cadena que es el intervalo de tiempo que desea agregar. La cadena debe tener uno de los siguientes valores:
 - yyyy año
 - preguntas trimestre
 - m mes
 - Día y año
 - Día de d.
 - w Weekday
 - ss semana
 - h hora
 - n minutos
 - s segundo
- valor: el número de unidades que desea agregar. Puede ser positivo (para obtener fechas futuras) o negativo (para obtener fechas en el pasado).
- fecha: fecha y hora que representa la fecha a la que se agrega el intervalo.

**Ejemplo:**  
`DateAdd("m", 3, CDate("2001-01-01"))`  
Agrega 3 meses y devuelve un valor de DateTime que representa "2001-04-01".

----------
### <a name="datefromnum"></a>DateFromNum

**Descripción:**  
La DateFromNum función se convierte un valor de fecha de anuncio dar formato a un tipo de fecha y hora.

**Sintaxis:**  
`dt DateFromNum(num value)`

**Ejemplo:**  
`DateFromNum([lastLogonTimestamp])`  
`DateFromNum(129699324000000000)`  
Devuelve un valor de DateTime que representa 2012-01-01 23:00:00

----------
### <a name="dncomponent"></a>DNComponent

**Descripción:**  
La función DNComponent devuelve el valor de un componente de DN especificado desde la izquierda.

**Sintaxis:**  
`str DNComponent(ref dn, num ComponentNumber)`

- DN: el atributo de referencia para interpretar
- ComponentNumber: El componente en el nombre completo para devolver

**Ejemplo:**  
`DNComponent([dn],1)`  
Si dn es "cn = Juan, ou =..." devuelve Joe

----------
### <a name="dncomponentrev"></a>DNComponentRev

**Descripción:**  
La función DNComponentRev devuelve el valor de un componente de DN especificado que va desde la derecha (final).

**Sintaxis:**  
`str DNComponentRev(ref dn, num ComponentNumber)`  
`str DNComponentRev(ref dn, num ComponentNumber, enum Options)`

- DN: el atributo de referencia para interpretar
- ComponentNumber - el componente en el nombre completo para devolver
- Opciones: DC-ignorar todos los componentes con "dc ="

**Ejemplo:**  
Si dn es "cn = Juan, ou = Atlanta, ou = GA, ou = US, dc = contoso, dc = com", a continuación,  
`DNComponentRev([dn],3)`  
`DNComponentRev([dn],1,"DC")`  
Ambas devuelven nosotros.

----------
### <a name="error"></a>Error

**Descripción:**  
La función de Error se utiliza para devolver un error personalizado.

**Sintaxis:**  
`void Error(str ErrorMessage)`

**Ejemplo:**  
`IIF(IsPresent([accountName]),[accountName],Error("AccountName is required"))`  
Si el atributo nombre de la cuenta no está presente, se produce un error en el objeto.

----------
### <a name="escapedncomponent"></a>EscapeDNComponent

**Descripción:**  
La función EscapeDNComponent toma un componente de un DN y secuencias de escape, por lo que se pueden representar en LDAP.

**Sintaxis:**  
`str EscapeDNComponent(str value)`

**Ejemplo:**  
`EscapeDNComponent("cn=" & [displayName]) & "," & %ForestLDAP%)`  
Se asegura de que el objeto que puede crearse en un directorio LDAP incluso si el atributo displayName tiene caracteres que deben ser de escape en LDAP.

----------
### <a name="formatdatetime"></a>FormatDateTime

**Descripción:**  
Se usa la función FormatDateTime para dar formato a una fecha y hora en una cadena con un formato específico

**Sintaxis:**  
`str FormatDateTime(dt value, str format)`

- valor: un valor en el formato de fecha y hora
- formato: una cadena que representa el formato para convertir a.

**Notas:**  
Los valores posibles para el formato pueden encontrarse aquí: [Formatos de fecha y hora definidos por el usuario (función Format)](http://msdn2.microsoft.com/library/73ctwf33\(VS.90\).aspx)

**Ejemplo:**  

`FormatDateTime(CDate("12/25/2007"),"yyyy-mm-dd")`  
Resultados en "2007-12-25".

`FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")`  
Puede dar lugar a "20140905081453.0Z"

----------
### <a name="guid"></a>GUID

**Descripción:**  
La función GUID genera un nuevo GUID aleatorio

**Sintaxis:**  
`str GUID()`

----------
### <a name="iif"></a>IIF

**Descripción:**  
La función IIF devuelve un conjunto de valores posibles basándose en una condición especificada.

**Sintaxis:**  
`var IIF(exp condition, var valueIfTrue, var valueIfFalse)`

- condición: cualquier valor o expresión que puede evaluarse como true o false.
- ValorSiVerdadero: si la condición se evalúa como verdadero, el valor devuelto.
- valorSiFalse: si la condición se evalúa como falso, el valor devuelto.

**Ejemplo:**  
`IIF([employeeType]="Intern","t-" & [alias],[alias])`  
 Si el usuario es una interna, devuelve el alias de un usuario con "t-" se agrega al principio de la misma, más el alias del usuario como está.

----------
### <a name="instr"></a>InStr

**Descripción:**  
La función EnCad encuentra la primera aparición de una subcadena en una cadena

**Sintaxis:**  

`num InStr(str stringcheck, str stringmatch)`  
`num InStr(str stringcheck, str stringmatch, num start)`  
`num InStr(str stringcheck, str stringmatch, num start , enum compare)`

- comprobarcadena: cadena que se busca
- coincidircadena: cadena que se encuentra
- inicio: posición para buscar la subcadena inicial
- comparar: vbTextCompare o vbBinaryCompare

**Notas:**  
Devuelve la posición donde se encuentra la subcadena o 0 si no se encuentra.

**Ejemplo:**  
`InStr("The quick brown fox","quick")`  
Evalues a 5

`InStr("repEated","e",3,vbBinaryCompare)`  
Se evalúa como 7

----------
### <a name="instrrev"></a>InStrRev

**Descripción:**  
La función InStrRev encuentra la última aparición de una subcadena en una cadena

**Sintaxis:**  
`num InstrRev(str stringcheck, str stringmatch)`  
`num InstrRev(str stringcheck, str stringmatch, num start)`  
`num InstrRev(str stringcheck, str stringmatch, num start, enum compare)`

- comprobarcadena: cadena que se busca
- coincidircadena: cadena que se encuentra
- inicio: posición para buscar la subcadena inicial
- comparar: vbTextCompare o vbBinaryCompare

**Notas:**  
Devuelve la posición donde se encuentra la subcadena o 0 si no se encuentra.

**Ejemplo:**  
`InStrRev("abbcdbbbef","bb")`  
Devuelve 7

----------
### <a name="isbitset"></a>IsBitSet

**Descripción:**  
La función IsBitSet comprueba si un poco se establece o no

**Sintaxis:**  
`bool IsBitSet(num value, num flag)`

- valor: un valor numérico que es evaluated.flag: un valor numérico que tiene el bit que se debe evaluar

**Ejemplo:**  
`IsBitSet(&HF,4)`  
Devuelve verdadero porque bit "4" se establece en el valor hexadecimal "F"

----------
### <a name="isdate"></a>IsDate

**Descripción:**  
Si la expresión puede ser se evalúa como un tipo de fecha y hora, la función IsDate se evalúa como verdadero.

**Sintaxis:**  
`bool IsDate(var Expression)`

**Notas:**  
Se utiliza para determinar si CDate() puede ser correcta.

----------
### <a name="isempty"></a>IsEmpty

**Descripción:**  
Si el atributo existe en el CS o MV pero se evalúa como una cadena vacía, la función IsEmpty se evalúa como verdadero.

**Sintaxis:**  
`bool IsEmpty(var Expression)`

----------
### <a name="isguid"></a>IsGuid

**Descripción:**  
Si la cadena se puede convertir en un GUID, la función IsGuid se evalúa como true.

**Sintaxis:**  
`bool IsGuid(str GUID)`

**Notas:**  
Un GUID se define como una cadena siguiendo uno de estos modelos: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx o {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

Se utiliza para determinar si CGuid() puede ser correcta.

**Ejemplo:**  
`IIF(IsGuid([strAttribute]),CGuid([strAttribute]),NULL)`  
Si el StrAttribute tiene un formato GUID, devolver una representación binaria, en caso contrario, devuelve un valor nulo.

----------
### <a name="isnull"></a>IsNull

**Descripción:**  
Si la expresión se evalúa como null, la función EsNulo devuelve true.

**Sintaxis:**  
`bool IsNull(var Expression)`

**Notas:**  
Para un atributo, un valor nulo se expresa por la ausencia del atributo.

**Ejemplo:**  
`IsNull([displayName])`  
Devuelve verdadero si el atributo no está presente en el CS o MV.

----------
### <a name="isnullorempty"></a>IsNullOrEmpty

**Descripción:**  
Si la expresión es null o una cadena vacía, la función IsNullOrEmpty devuelve true.

**Sintaxis:**  
`bool IsNullOrEmpty(var Expression)`

**Notas:**  
Para un atributo, esto se evalúa como verdadero si el atributo existe o está presente pero es una cadena vacía.  
La función inversa de esta función se denomina IsPresent.

**Ejemplo:**  
`IsNullOrEmpty([displayName])`  
Devuelve verdadero si el atributo no está presente o es una cadena vacía en el CS o MV.

----------
### <a name="isnumeric"></a>IsNumeric

**Descripción:**  
La función IsNumeric devuelve un valor Boolean que indica si una expresión puede evaluarse como un tipo de número.

**Sintaxis:**  
`bool IsNumeric(var Expression)`

**Notas:**  
Se utiliza para determinar si puede realizarse CNum() analizar la expresión.

----------
### <a name="isstring"></a>IsString

**Descripción:**  
Si la expresión puede evaluarse como un tipo de cadena, la función IsString se evalúa como verdadero.

**Sintaxis:**  
`bool IsString(var expression)`

**Notas:**  
Se utiliza para determinar si CStr() pueda realizarse analizar la expresión.

----------
### <a name="ispresent"></a>IsPresent

**Descripción:**  
Si la expresión se evalúa como una cadena que no es nulo y no está vacío, la función IsPresent devuelve true.

**Sintaxis:**  
`bool IsPresent(var expression)`

**Notas:**  
La función inversa de esta función se denomina IsNullOrEmpty.

**Ejemplo:**  
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

----------
### <a name="item"></a>Elemento

**Descripción:**  
La función Item devuelve un elemento de un cadena o atributo multivalor.

**Sintaxis:**  
`var Item(mvstr attribute, num index)`

- atributo: atributo multivalor
- índice: índice de un elemento de la cadena de valor múltiple.

**Notas:**  
La función elemento es útil junto con la función Contains debido a que la función segunda devuelve el índice a un elemento en el atributo multivalor.

Se produce un error si el índice está fuera de los límites.

**Ejemplo:**  
`Mid(Item([proxyAddress],Contains([proxyAddress], "SMTP:")),6)`  
Devuelve la dirección de correo electrónico principal.

----------
### <a name="itemornull"></a>ItemOrNull

**Descripción:**  
La función ItemOrNull devuelve un elemento de un cadena o atributo multivalor.

**Sintaxis:**  
`var ItemOrNull(mvstr attribute, num index)`

- atributo: atributo multivalor
- índice: índice de un elemento de la cadena de valor múltiple.

**Notas:**  
La función ItemOrNull es útil junto con la función Contains debido a que la función segunda devuelve el índice a un elemento en el atributo multivalor.

Si el índice está fuera de los límites, a continuación, devuelve un valor nulo.

----------
### <a name="join"></a>Unirse a

**Descripción:**  
La función de combinación toma una cadena con varios valores y devuelve una cadena de valor único con especificado separador insertada entre cada elemento.

**Sintaxis:**  
`str Join(mvstr attribute)`  
`str Join(mvstr attribute, str Delimiter)`

- atributo: atributo con varios valores que contiene que se unirán.
- delimitador: cualquier cadena utilizada para separar las subcadenas en la cadena devuelta. Si se omite, el carácter de espacio ("") se usa. Si delimitador es una cadena de longitud cero ("") o nada, se concatenarán todos los elementos de la lista sin delimitadores.

**Notas**  
Hay paridad entre las funciones de combinación y dividir. La función de combinación toma una matriz de cadenas y une con una cadena de delimitación para devolver una única cadena. La función Split toma una cadena y separa en el delimitador para devolver una matriz de cadenas. Sin embargo, una diferencia clave es que unirse puede concatenar cadenas con cualquier cadena de delimitador, dividir puede separar sólo cadenas mediante un delimitador de carácter.

**Ejemplo:**  
`Join([proxyAddresses],",")`  
Podría devolver:"SMTP:john.doe@contoso.com,smtp:jd@contoso.com"

----------
### <a name="lcase"></a>Minús

**Descripción:**  
La función minús convierte todos los caracteres de una cadena a minúsculas.

**Sintaxis:**  
`str LCase(str value)`

**Ejemplo:**  
`LCase("TeSt")`  
Devuelve "prueba".

----------
### <a name="left"></a>Izquierda

**Descripción:**  
La función Left devuelve un número especificado de caracteres desde la izquierda de una cadena.

**Sintaxis:**  
`str Left(str string, num NumChars)`

- cadena: la cadena para devolver los caracteres de
- NumChars: un número que identifica el número de caracteres para devolver desde el principio (izquierdo) de cadena

**Notas:**  
Una cadena que contiene los primeros caracteres de numChars en cadena:

- Si numChars = 0, devolver una cadena vacía.
- Si numChars < 0, devuelve la cadena de entrada.
- Si la cadena es null, devolver una cadena vacía.

Si cadena contiene menos caracteres que el numChars especificado en número, se devuelve una cadena idéntica a la cadena (que es, que contiene todos los caracteres en el parámetro 1).

**Ejemplo:**  
`Left("John Doe", 3)`  
Devuelve "Joh".

----------
### <a name="len"></a>Largo

**Descripción:**  
La función Len devuelve el número de caracteres de una cadena.

**Sintaxis:**  
`num Len(str value)`

**Ejemplo:**  
`Len("John Doe")`  
Devuelve 8

----------
### <a name="ltrim"></a>LTrim

**Descripción:**  
La función LTrim quita los espacios en blanco de una cadena.

**Sintaxis:**  
`str LTrim(str value)`

**Ejemplo:**  
`LTrim(" Test ")`  
Devuelve "prueba"

----------
### <a name="mid"></a>Extrae

**Descripción:**  
La función Mid devuelve un número especificado de caracteres desde la posición especificada de una cadena.

**Sintaxis:**  
`str Mid(str string, num start, num NumChars)`

- cadena: la cadena para devolver los caracteres de
- inicio: colocar un número que identifica el inicio de cadena para devolver los caracteres de
- NumChars: un número que identifica el número de caracteres para devolver desde la posición de cadena.

**Notas:**  
Devolver caracteres numChars empezando desde la posición inicial de cadena.  
Una cadena que contiene caracteres de numChars desde la posición start de cadena:

- Si numChars = 0, devolver una cadena vacía.
- Si numChars < 0, devuelve la cadena de entrada.
- Si Inicio > la longitud de cadena, devolver cadena de entrada.
- Si iniciar < = 0, devolver la cadena de entrada.
- Si la cadena es null, devolver una cadena vacía.

Si no hay caracteres numChar restante de cadena desde la posición start, tal como se devuelve el número de caracteres como sea posible.

**Ejemplo:**  
`Mid("John Doe", 3, 5)`  
Devuelve "hn hacer".

`Mid("John Doe", 6, 999)`  
Devuelve "López"

----------
### <a name="now"></a>Ahora

**Descripción:**  
La función Now devuelve un valor de DateTime que especifica la fecha y hora actuales, según la fecha del sistema y la hora de su equipo.

**Sintaxis:**  
`dt Now()`

----------
### <a name="numfromdate"></a>NumFromDate

**Descripción:**  
La función NumFromDate devuelve una fecha en formato de fecha de AD.

**Sintaxis:**  
`num NumFromDate(dt value)`

**Ejemplo:**  
`NumFromDate(CDate("2012-01-01 23:00:00"))`  
Devuelve 129699324000000000

----------
### <a name="padleft"></a>PadLeft

**Descripción:**  
La PadLeft función izquierda-rellena una cadena de una longitud especificada utilizando un carácter de relleno proporcionado.

**Sintaxis:**  
`str PadLeft(str string, num length, str padCharacter)`

- cadena: la cadena de relleno.
- longitud: un número entero que representa la longitud de cadena deseada.
- padCharacter: una cadena formada por un solo carácter para utilizarlo como carácter de relleno

**Notas:**

- Si la longitud de cadena es menor que la longitud, padCharacter varias veces se anexa al principio (izquierda) de la cadena hasta que tenga una longitud igual a longitud.
- PadCharacter puede ser un carácter de espacio, pero no puede ser un valor nulo.
- Si la longitud de cadena es igual o mayor que la longitud, cadena se devuelve sin cambios.
- Si la cadena tiene una longitud mayor o igual que la longitud, se devuelve una cadena idéntica a la cadena.
- Si la longitud de cadena es menor que la longitud, se devuelve una cadena nueva de la longitud deseada rellenado con una padCharacter de cadena que contiene.
- Si la cadena es null, la función devuelve una cadena vacía.

**Ejemplo:**  
`PadLeft("User", 10, "0")`  
Devuelve "000000User".

----------
### <a name="padright"></a>PadRight

**Descripción:**  
La PadRight función derecha-rellena una cadena de una longitud especificada utilizando un carácter de relleno proporcionado.

**Sintaxis:**  
`str PadRight(str string, num length, str padCharacter)`

- cadena: la cadena de relleno.
- longitud: un número entero que representa la longitud de cadena deseada.
- padCharacter: una cadena formada por un solo carácter para utilizarlo como carácter de relleno

**Notas:**

- Si la longitud de cadena es menor que la longitud, a continuación, padCharacter es repetidamente al final (derecho) de cadena hasta que tenga una longitud igual a longitud.
- padCharacter puede ser un carácter de espacio, pero no puede ser un valor nulo.
- Si la longitud de cadena es igual o mayor que la longitud, cadena se devuelve sin cambios.
- Si la cadena tiene una longitud mayor o igual que la longitud, se devuelve una cadena idéntica a la cadena.
- Si la longitud de cadena es menor que la longitud, se devuelve una cadena nueva de la longitud deseada rellenado con una padCharacter de cadena que contiene.
- Si la cadena es null, la función devuelve una cadena vacía.

**Ejemplo:**  
`PadRight("User", 10, "0")`  
Devuelve "User000000".

----------
### <a name="pcase"></a>PCase

**Descripción:**  
La función PCase convierte el primer carácter de cada palabra delimitado por espacios de una cadena a mayúsculas, y todos los demás caracteres se convierten en minúsculas.

**Sintaxis:**  
`String PCase(string)`

**Notas:**

- Esta función no ofrece actualmente de mayúsculas y minúsculas correcto para convertir una palabra que esté completamente en mayúscula, como un acrónimo.

**Ejemplo:**  
`PCase("TEsT")`  
Devuelve "Prueba".

`PCase(LCase("TEST"))`  
Devuelve "prueba"

----------
### <a name="randomnum"></a>RandomNum

**Descripción:**  
La función RandomNum devuelve un número aleatorio entre un intervalo especificado.

**Sintaxis:**  
`num RandomNum(num start, num end)`

- iniciar: un número que identifica el límite inferior del valor aleatorio para generar
- final: un número que identifica el límite superior del valor aleatorio para generar

**Ejemplo:**  
`Random(100,999)`  
Puede devolver 734.

----------
### <a name="removeduplicates"></a>RemoveDuplicates

**Descripción:**  
La función RemoveDuplicates toma una cadena con varios valores y asegúrese de que cada valor sea único.

**Sintaxis:**  
`mvstr RemoveDuplicates(mvstr attribute)`

**Ejemplo:**  
`RemoveDuplicates([proxyAddresses])`  
Devuelve un atributo proxyAddress limpio donde se han quitado todos los valores duplicados.

----------
### <a name="replace"></a>Reemplazar

**Descripción:**  
La función Replace reemplaza todas las repeticiones de una cadena de otra cadena.

**Sintaxis:**  
`str Replace(str string, str OldValue, str NewValue)`

- cadena: reemplazar valores en una cadena.
- OldValue: La cadena para buscar y reemplazar.
- NuevoValor: Cadena para reemplazar a.

**Notas:**  
La función reconoce los monikers especiales siguientes:

- \n – línea nueva
- \r: retorno de carro
- \t – ficha

**Ejemplo:**  
`Replace([address],"\r\n",", ")`  
Reemplaza CRLF con una coma y un espacio y a continuación, podría producir "One Microsoft Way, Redmond, Washington, EE"

----------
### <a name="replacechars"></a>ReplaceChars

**Descripción:**  
La función ReplaceChars reemplaza todas las apariciones de caracteres que se encuentra en la cadena tanto.

**Sintaxis:**  
`str ReplaceChars(str string, str ReplacePattern)`

- cadena: reemplazar los caracteres de una cadena.
- Tanto: una cadena que contiene un diccionario con caracteres para reemplazar.

El formato es {source1}: {Rectangle target1}, {source2}: {target2}, {origenN}, {targetN} donde origen es el carácter para buscar y reemplazar con la cadena de destino.

**Notas:**

- La función toma cada aparición de orígenes definidos y reemplaza con los objetivos.
- El origen debe ser exactamente un carácter (unicode).
- El origen no puede ser vacío o más de un carácter (error de análisis).
- El destino puede tener varios caracteres, por ejemplo ö:oe, β:ss.
- El destino puede estar vacío que indica que se debe quitar el carácter.
- El origen distingue mayúsculas de minúsculas y debe ser una coincidencia exacta.
- (Coma) y: (dos puntos) son caracteres reservados y no se puede reemplazar con esta función.
- Espacios y otros caracteres en blancos en la cadena tanto se pasan por alto.

**Ejemplo:**  
`%ReplaceString% = ’:,Å:A,Ä:A,Ö:O,å:a,ä:a,ö,o`

`ReplaceChars("Räksmörgås",%ReplaceString%)`  
Devuelve Raksmorgas

`ReplaceChars("O’Neil",%ReplaceString%)`  
Devuelve "ONeil", que es el único paso se define para eliminarse.

----------
### <a name="right"></a>Correcto

**Descripción:**  
La función derecha devuelve un número especificado de caracteres desde la derecha (Finalizar) de una cadena.

**Sintaxis:**  
`str Right(str string, num NumChars)`

- cadena: la cadena para devolver los caracteres de
- NumChars: un número que identifica el número de caracteres para devolver desde el final (derecho) de cadena

**Notas:**  
Caracteres de NumChars se devuelven desde la última posición de la cadena.

Una cadena que contiene los últimos caracteres numChars en cadena:

- Si numChars = 0, devolver una cadena vacía.
- Si numChars < 0, devuelve la cadena de entrada.
- Si la cadena es null, devolver una cadena vacía.

Si cadena contiene menos caracteres que el NumChars especificado en número, se devuelve una cadena idéntica a la cadena.

**Ejemplo:**  
`Right("John Doe", 3)`  
Devuelve "López".

----------
### <a name="rtrim"></a>RTrim

**Descripción:**  
La función RTrim quita los espacios en blanco al final de una cadena.

**Sintaxis:**  
`str RTrim(str value)`

**Ejemplo:**  
`RTrim(" Test ")`  
Devuelve "Prueba".

----------
### <a name="split"></a>División

**Descripción:**  
La función Split toma una cadena que se separan con un delimitador y hace que una cadena con varios valores.

**Sintaxis:**  
`mvstr Split(str value, str delimiter)`  
`mvstr Split(str value, str delimiter, num limit)`

- valor: la cadena con un carácter delimitador para separar.
- delimitador: carácter que se utilizará como delimitador.
- límite: número máximo de valores que puede devolver.

**Ejemplo:**  
`Split("SMTP:john.doe@contoso.com,smtp:jd@contoso.com",",")`  
Devuelve una cadena con varios valores con 2 elementos útiles para el atributo proxyAddress.

----------
### <a name="stringfromguid"></a>StringFromGuid

**Descripción:**  
La función StringFromGuid tiene un GUID binario y lo convierte en una cadena

**Sintaxis:**  
`str StringFromGuid(bin GUID)`

----------
### <a name="stringfromsid"></a>StringFromSid

**Descripción:**  
La función StringFromSid convierte una matriz de bytes que contiene un identificador de seguridad en una cadena.

**Sintaxis:**  
`str StringFromSid(bin ObjectSID)`  

----------
### <a name="switch"></a>Cambiar

**Descripción:**  
Se usa la función Switch para devolver un valor único basándose en condiciones evaluadas.

**Sintaxis:**  
`var Switch(exp expr1, var value1[, exp expr2, var value … [, exp expr, var valueN]])`

- expr: expresión de tipo Variant que desea evaluar.
- valor: valor que se devolverá si la expresión correspondiente es True.

**Notas:**  
La lista de argumentos de función Conmutador consta de pares de expresiones y valores. Las expresiones se evalúan de izquierda a derecha y, a continuación, se devuelve el valor asociado a la primera expresión se evalúa como verdadero. Si los elementos no se ha sincronizado correctamente, se produce un error de tiempo de ejecución.

Por ejemplo, si expr1 es verdadero, conmutador devuelve valor1. Si expr-1 es falso, pero expr-2 es verdadero, conmutador devuelve valor2 y así sucesivamente.

Cambiar devuelve un nada si:

- Ninguna de las expresiones son True.
- La primera expresión True tiene un valor correspondiente que es nulo.

Función Conmutador evalúa todas las expresiones, aunque sólo devuelve una de ellas. Por este motivo, debería ver efectos no deseados. Por ejemplo, si la evaluación de cualquier expresión que da como resultado una división por error cero, se produce un error.

Valor también puede ser la función de Error, que devuelve una cadena personalizada.

**Ejemplo:**  
`Switch([city] = "London", "English", [city] = "Rome", "Italian", [city] = "Paris", "French", True, Error("Unknown city"))`  
Devuelve el idioma que se habla en algunos principales ciudades, en caso contrario, devuelve un Error.

----------
### <a name="trim"></a>Recortar

**Descripción:**  
La función espacios quita iniciales y finales de una cadena de espacios en blanco.

**Sintaxis:**  
`str Trim(str value)`  

**Ejemplo:**  
`Trim(" Test ")`  
Devuelve "Prueba".

`Trim([proxyAddresses])`  
Quita los espacios para cada valor en el atributo proxyAddress iniciales y finales.

----------
### <a name="ucase"></a>UCase

**Descripción:**  
La función UCase convierte todos los caracteres de una cadena en mayúsculas.

**Sintaxis:**  
`str UCase(str string)`

**Ejemplo:**  
`UCase("TeSt")`  
Devuelve "Prueba".

----------
### <a name="word"></a>Word

**Descripción:**  
La función de Word devuelve una palabra dentro de una cadena, basándose en los parámetros que describe los delimitadores de uso y para devolver el número de word.

**Sintaxis:**  
`str Word(str string, num WordNumber, str delimiters)`

- cadena: la cadena para devolver una palabra desde.
- WordNumber: debe devolver un número que identifica el número de word.
- delimitadores: una cadena que representa la delimiter(s) que debe usarse para identificar palabras

**Notas:**  
Cada cadena de caracteres de cadena separados por uno de los caracteres de delimitadores se consideran como palabras:

- Si número < 1, y devuelve una cadena vacía.
- Si la cadena es null, devuelve una cadena vacía.

Si cadena contiene menor que el número de palabras o cadena no contiene cualquiera de las palabras identificada con delimitadores, se devuelve una cadena vacía.

**Ejemplo:**  
`Word("The quick brown fox",3," ")`  
Devuelve "marrón"

`Word("This,string!has&many separators",3,",!&#")`  
Devuelve "tiene"

## <a name="additional-resources"></a>Recursos adicionales

* [Descripción de las expresiones de aprovisionamiento descriptiva](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)
* [Sincronización de conectar Azure AD: Personalizar las opciones de sincronización](active-directory-aadconnectsync-whatis.md)
* [Integrar las identidades locales con Azure Active Directory](active-directory-aadconnect.md)

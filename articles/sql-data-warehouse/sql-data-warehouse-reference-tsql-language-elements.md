<properties
   pageTitle="Elementos de lenguaje de almacén de datos de SQL Transact-SQL | Microsoft Azure"
   description="Lista de vínculos a contenido de referencia para los elementos del lenguaje Transact-SQL utilizado para el almacén de datos de SQL."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/08/2016"
   ms.author="barbkess;sonyama;kevin"/>

# <a name="language-elements"></a>Elementos de idioma

## <a name="core-elements"></a>Elementos básicos

- [convenciones de sintaxis](https://msdn.microsoft.com/library/ms177563.aspx)
- [reglas de nomenclatura de objetos](https://msdn.microsoft.com/library/ms175874.aspx)
- [palabras clave reservadas](https://msdn.microsoft.com/library/ms189822.aspx)
- [intercalación](https://msdn.microsoft.com/library/ff848763.aspx)
- [comentarios](https://msdn.microsoft.com/library/ms181627.aspx)
- [constantes](https://msdn.microsoft.com/library/ms179899.aspx)
- [tipos de datos](https://msdn.microsoft.com/library/ms187752.aspx)
- [EJECUTAR](https://msdn.microsoft.com/library/ms188332.aspx)
- [expresiones](https://msdn.microsoft.com/library/ms190286.aspx)
- [ELIMINAR](https://msdn.microsoft.com/library/ms173730.aspx)
- [Solución de identidad (propiedad)](https://msdn.microsoft.com/library/ms186775.aspx)
- [IMPRIMIR](https://msdn.microsoft.com/library/ms176047.aspx)
- [USAR](https://msdn.microsoft.com/library/ms188366.aspx)

## <a name="batches-control-of-flow-and-variables"></a>Lotes, control de flujo y variables

- [COMENZAR... FINAL](https://msdn.microsoft.com/library/ms190487.aspx)
- [SALTO](https://msdn.microsoft.com/library/ms181271.aspx)
- [DECLARAR@local_variable](https://msdn.microsoft.com/library/ms188927.aspx)
- [IF... MÁS](https://msdn.microsoft.com/library/ms182717.aspx)
- [RAISERROR](https://msdn.microsoft.com/library/ms178592.aspx)
- [SET@local_variable](https://msdn.microsoft.com/library/ms189484.aspx)
- [INICIAR](https://msdn.microsoft.com/library/ee677615.aspx)
- [INTENTE... PONERSE AL DÍA](https://msdn.microsoft.com/library/ms175976.aspx)
- [MIENTRAS](https://msdn.microsoft.com/library/ms178642.aspx)

## <a name="operators"></a>Operadores
- [+ (Agregar)](https://msdn.microsoft.com/library/ms178565.aspx)
- [+ (Concatenación)](https://msdn.microsoft.com/library/ms177561.aspx)
- [-(Negativo)](https://msdn.microsoft.com/library/ms189480.aspx)
- [-(Restar)](https://msdn.microsoft.com/library/ms189518.aspx)
- [* (Multiplicar)](https://msdn.microsoft.com/library/ms176019.aspx)
- [/ (Dividir)](https://msdn.microsoft.com/library/ms175009.aspx)
- [Módulo](https://msdn.microsoft.com/library/ms190279.aspx)

## <a name="wildcard-characters-to-match"></a>Caracteres comodín para que coincida con
- [= (Igual a)](https://msdn.microsoft.com/library/ms175118.aspx)
- [> (Mayor que)](https://msdn.microsoft.com/library/ms178590.aspx)
- [< (Menor que)](https://msdn.microsoft.com/library/ms179873.aspx)
- [> = (genial o igual que)](https://msdn.microsoft.com/library/ms181567.aspx)
- [< = (menor o igual que)](https://msdn.microsoft.com/library/ms174978.aspx)
- [<> (No es igual a)](https://msdn.microsoft.com/library/ms176020.aspx)
- [! = (No es igual a)](https://msdn.microsoft.com/library/ms190296.aspx)
- [Y](https://msdn.microsoft.com/library/ms188372.aspx)
- [ENTRE](https://msdn.microsoft.com/library/ms187922.aspx)
- [EXISTE](https://msdn.microsoft.com/library/ms188336.aspx)
- [EN](https://msdn.microsoft.com/library/ms177682.aspx)
- [[NO] ES NULO](https://msdn.microsoft.com/library/ms188795.aspx)
- [AL IGUAL QUE](https://msdn.microsoft.com/library/ms179859.aspx)
- [NO](https://msdn.microsoft.com/library/ms189455.aspx)
- [OR](https://msdn.microsoft.com/library/ms188361.aspx)

### <a name="bitwise-operators"></a>Operadores bit a bit

- [& (AND bit a bit)](https://msdn.microsoft.com/library/ms174965.aspx)
- [| (O bit a bit)](https://msdn.microsoft.com/library/ms186714.aspx)
- [^ (Bit a bit o exclusivo)](https://msdn.microsoft.com/library/ms190277.aspx)
- [¿~ (NOT bit a bit)](https://msdn.microsoft.com/library/ms173468.aspx)
- [^ = (Exclusivo bit a bit o igual a)](https://msdn.microsoft.com/library/cc627413.aspx)
- [| = (O bit a bit es igual a)](https://msdn.microsoft.com/library/cc627409.aspx)
- [& = (signo más y bit a bit)](https://msdn.microsoft.com/library/cc627427.aspx)

## <a name="functions"></a>Funciones

- [@@DATEFIRST](https://msdn.microsoft.com/library/ms187766.aspx)
- [@@ERROR](https://msdn.microsoft.com/library/ms188790.aspx)
- [@@LANGUAGE](https://msdn.microsoft.com/library/ms177557.aspx)
- [@@SPID](https://msdn.microsoft.com/library/ms189535.aspx)
- [@@TRANCOUNT](https://msdn.microsoft.com/library/ms187967.aspx)
- [@@VERSION](https://msdn.microsoft.com/library/ms177512.aspx)
- [ABS](https://msdn.microsoft.com/library/ms189800.aspx)
- [ACOS](https://msdn.microsoft.com/library/ms178627.aspx)
- [ASCII](https://msdn.microsoft.com/library/ms177545.aspx)
- [ASIN](https://msdn.microsoft.com/library/ms181581.aspx)
- [ATAN](https://msdn.microsoft.com/library/ms181746.aspx)
- [ATN2](https://msdn.microsoft.com/library/ms173854.aspx)
- [BINARY_CHECKSUM](https://msdn.microsoft.com/library/ms173784.aspx)
- [MAYÚSCULAS Y MINÚSCULAS](https://msdn.microsoft.com/library/ms181765.aspx)
- [CONVERSIÓN y convertir](https://msdn.microsoft.com/library/ms187928.aspx)
- [LÍMITE MÁXIMO](https://msdn.microsoft.com/library/ms189818.aspx)
- [CHAR](https://msdn.microsoft.com/library/ms187323.aspx)
- [CHARINDEX](https://msdn.microsoft.com/library/ms186323.aspx)
- [SUMA DE COMPROBACIÓN](https://msdn.microsoft.com/library/ms189788.aspx)
- [UNIÓN](https://msdn.microsoft.com/library/ms190349.aspx)
- [NOMBRECOLUMNA](https://msdn.microsoft.com/library/ms174974.aspx)
- [COLLATIONPROPERTY](https://msdn.microsoft.com/library/ms190305.aspx)
- [CONCATENACIÓN](https://msdn.microsoft.com/library/hh231515.aspx)
- [COS](https://msdn.microsoft.com/library/ms188919.aspx)
- [COT](https://msdn.microsoft.com/library/ms188921.aspx)
- [RECUENTO](https://msdn.microsoft.com/library/ms175997.aspx)
- [COUNT_BIG](https://msdn.microsoft.com/library/ms190317.aspx)
- [CUME_DIST](https://msdn.microsoft.com/library/hh231078.aspx)
- [CURRENT_TIMESTAMP](https://msdn.microsoft.com/library/ms188751.aspx)
- [CURRENT_USER](https://msdn.microsoft.com/library/ms176050.aspx)
- [DATABASEPROPERTYEX](https://msdn.microsoft.com/library/ms186823.aspx)
- [DATALENGTH](https://msdn.microsoft.com/library/ms173486.aspx)
- [DATEADD](https://msdn.microsoft.com/library/ms186819.aspx)
- [DATEDIFF](https://msdn.microsoft.com/library/ms189794.aspx)
- [DATEFROMPARTS](https://msdn.microsoft.com/library/hh213228.aspx)
- [DATENAME](https://msdn.microsoft.com/library/ms174395.aspx)
- [DATEPART](https://msdn.microsoft.com/library/ms174420.aspx)
- [DATETIME2FROMPARTS](https://msdn.microsoft.com/library/hh213312.aspx)
- [DATETIMEFROMPARTS](https://msdn.microsoft.com/library/hh213233.aspx)
- [DATETIMEOFFSETFROMPARTS](https://msdn.microsoft.com/library/hh231077.aspx)
- [DÍA](https://msdn.microsoft.com/library/ms176052.aspx)
- [DB_ID](https://msdn.microsoft.com/library/ms186274.aspx)
- [DB_NAME](https://msdn.microsoft.com/library/ms189753.aspx)
- [GRADOS](https://msdn.microsoft.com/library/ms178566.aspx)
- [DENSE_RANK](https://msdn.microsoft.com/library/ms173825.aspx)
- [DIFERENCIA](https://msdn.microsoft.com/library/ms188753.aspx)
- [EOMONTH](https://msdn.microsoft.com/library/hh213020.aspx)
- [ERROR_MESSAGE](https://msdn.microsoft.com/library/ms190358.aspx)
- [NÚMERODEERROR](https://msdn.microsoft.com/library/ms175069.aspx)
- [ERROR_PROCEDURE](https://msdn.microsoft.com/library/ms188398.aspx)
- [ERROR_SEVERITY](https://msdn.microsoft.com/library/ms178567.aspx)
- [ERROR_STATE](https://msdn.microsoft.com/library/ms180031.aspx)
- [EXP](https://msdn.microsoft.com/library/ms179857.aspx)
- [FIRST_VALUE](https://msdn.microsoft.com/library/hh213018.aspx)
- [PISO](https://msdn.microsoft.com/library/ms178531.aspx)
- [GETDATE](https://msdn.microsoft.com/library/ms188383.aspx)
- [GETUTCDATE](https://msdn.microsoft.com/library/ms178635.aspx)
- [HAS_DBACCESS](https://msdn.microsoft.com/library/ms187718.aspx)
- [HASHBYTES](https://msdn.microsoft.com/library/ms174415.aspx)
- [INDEXPROPERTY](https://msdn.microsoft.com/library/ms187729.aspx)
- [ISDATE](https://msdn.microsoft.com/library/ms187347.aspx)
- [ISNULL](https://msdn.microsoft.com/library/ms184325.aspx)
- [ISNUMERIC](https://msdn.microsoft.com/library/ms186272.aspx)
- [POSPOSICIÓN](https://msdn.microsoft.com/library/hh231256.aspx)
- [LAST_VALUE](https://msdn.microsoft.com/library/hh231517.aspx)
- [CLIENTE POTENCIAL](https://msdn.microsoft.com/library/hh213125.aspx)
- [IZQUIERDA](https://msdn.microsoft.com/library/ms177601.aspx)
- [LARGO](https://msdn.microsoft.com/library/ms190329.aspx)
- [REGISTRO](https://msdn.microsoft.com/library/ms190319.aspx)
- [LOG10](https://msdn.microsoft.com/library/ms175121.aspx)
- [INFERIOR](https://msdn.microsoft.com/library/ms174400.aspx)
- [LTRIM](https://msdn.microsoft.com/library/ms177827.aspx)
- [MAX](https://msdn.microsoft.com/library/ms187751.aspx)
- [MIN](https://msdn.microsoft.com/library/ms179916.aspx)
- [MES](https://msdn.microsoft.com/library/ms187813.aspx)
- [NCHAR](https://msdn.microsoft.com/library/ms182673.aspx)
- [NTILE](https://msdn.microsoft.com/library/ms175126.aspx)
- [NULLIF](https://msdn.microsoft.com/library/ms177562.aspx)
- [OBJECT_ID](https://msdn.microsoft.com/library/ms190328.aspx)
- [NOMBRE DE OBJETO](https://msdn.microsoft.com/library/ms186301.aspx)
- [OBJECTPROPERTY](https://msdn.microsoft.com/library/ms176105.aspx)
- [OIBJECTPROPERTYEX](https://msdn.microsoft.com/library/ms188390.aspx)
- [Funciones escalares de ODBCS](https://msdn.microsoft.com/library/bb630290.aspx)
- [EN la cláusula](https://msdn.microsoft.com/library/ms189461.aspx)
- [PARSENAME](https://msdn.microsoft.com/library/ms188006.aspx)
- [PATINDEX](https://msdn.microsoft.com/library/ms188395.aspx)
- [PERCENTILE_CONT](https://msdn.microsoft.com/library/hh231473.aspx)
- [PERCENTILE_DISC](https://msdn.microsoft.com/library/hh231327.aspx)
- [PERCENT_RANK](https://msdn.microsoft.com/library/hh213573.aspx)
- [PI](https://msdn.microsoft.com/library/ms189512.aspx)
- [POWER](https://msdn.microsoft.com/library/ms174276.aspx)
- [QUOTENAME](https://msdn.microsoft.com/library/ms176114.aspx)
- [RADIANES](https://msdn.microsoft.com/library/ms189742.aspx)
- [ALEATORIO](https://msdn.microsoft.com/library/ms177610.aspx)
- [JERARQUÍA](https://msdn.microsoft.com/library/ms176102.aspx)
- [REEMPLAZAR](https://msdn.microsoft.com/library/ms186862.aspx)
- [REPLICAR](https://msdn.microsoft.com/library/ms174383.aspx)
- [INVERTIR](https://msdn.microsoft.com/library/ms180040.aspx)
- [Correcto](https://msdn.microsoft.com/library/ms177532.aspx)
- [REDONDEAR](https://msdn.microsoft.com/library/ms175003.aspx)
- [NÚM_FILA](https://msdn.microsoft.com/library/ms186734.aspx)
- [RTRIM](https://msdn.microsoft.com/library/ms178660.aspx)
- [SCHEMA_ID](https://msdn.microsoft.com/library/ms188797.aspx)
- [SCHEMA_NAME](https://msdn.microsoft.com/library/ms175068.aspx)
- [SERVERPROPERTY](https://msdn.microsoft.com/library/ms174396.aspx)
- [SESSION_USER](https://msdn.microsoft.com/library/ms177587.aspx)
- [INICIO DE SESIÓN](https://msdn.microsoft.com/library/ms188420.aspx)
- [SENO](https://msdn.microsoft.com/library/ms188377.aspx)
- [SMALLDATETIMEFROMPARTS](https://msdn.microsoft.com/library/hh213396.aspx)
- [SOUNDEX](https://msdn.microsoft.com/library/ms187384.aspx)
- [ESPACIO](https://msdn.microsoft.com/library/ms187950.aspx)
- [SQL_VARIANT_PROPERTY](https://msdn.microsoft.com/library/ms178550.aspx)
- [SQRT](https://msdn.microsoft.com/library/ms176108.aspx)
- [CUADRADO](https://msdn.microsoft.com/library/ms173569.aspx)
- [STATS_DATE](https://msdn.microsoft.com/library/ms190330.aspx)
- [DESVEST](https://msdn.microsoft.com/library/ms190474.aspx)
- [DESVESTP (STDEVP)](https://msdn.microsoft.com/library/ms176080.aspx)
- [STR](https://msdn.microsoft.com/library/ms189527.aspx)
- [COSAS](https://msdn.microsoft.com/library/ms188043.aspx)
- [SUBCADENA](https://msdn.microsoft.com/library/ms187748.aspx)
- [SUMA](https://msdn.microsoft.com/library/ms187810.aspx)
- [SUSER_SNAME](https://msdn.microsoft.com/library/ms174427.aspx)
- [SWITCHOFFSET](https://msdn.microsoft.com/library/bb677244.aspx)
- [SYSDATETIME](https://msdn.microsoft.com/library/bb630353.aspx)
- [SYSDATETIMEOFFSET](https://msdn.microsoft.com/library/bb677334.aspx)
- [SYSTEM_USER](https://msdn.microsoft.com/library/ms179930.aspx)
- [SYSUTCDATETIME](https://msdn.microsoft.com/library/bb630387.aspx)
- [TG](https://msdn.microsoft.com/library/ms190338.aspx)
- [TERTIARY_WEIGHTS](https://msdn.microsoft.com/library/ms186881.aspx)
- [TIMEFROMPARTS](https://msdn.microsoft.com/library/hh213398.aspx)
- [TODATETIMEOFFSET](https://msdn.microsoft.com/library/bb630335.aspx)
- [TYPE_ID](https://msdn.microsoft.com/library/ms181628.aspx)
- [TYPE_NAME](https://msdn.microsoft.com/library/ms189750.aspx)
- [TYPEPROPERTY](https://msdn.microsoft.com/library/ms188419.aspx)
- [UNICODE](https://msdn.microsoft.com/library/ms180059.aspx)
- [SUPERIOR](https://msdn.microsoft.com/library/ms180055.aspx)
- [USUARIO](https://msdn.microsoft.com/library/ms186738.aspx)
- [NOMBREDEUSUARIO](https://msdn.microsoft.com/library/ms188014.aspx)
- [VAR](https://msdn.microsoft.com/library/ms186290.aspx)
- [VARP](https://msdn.microsoft.com/library/ms188735.aspx)
- [AÑO](https://msdn.microsoft.com/library/ms186313.aspx)
- [XACT_STATE](https://msdn.microsoft.com/library/ms189797.aspx)

## <a name="transactions"></a>Transacciones

- [transacciones](https://msdn.microsoft.com/library/mt204031.aspx)

## <a name="diagnostic-sessions"></a>Sesiones de diagnóstico

- [CREAR UNA SESIÓN DE DIAGNÓSTICO](https://msdn.microsoft.com/library/mt204029.aspx)

## <a name="procedures"></a>Procedimientos

- [sp_addrolemember](https://msdn.microsoft.com/library/ms187750.aspx)
- [sp_columns](https://msdn.microsoft.com/library/ms176077.aspx)
- [sp_configure](https://msdn.microsoft.com/library/ms188787.aspx)
- [sp_datatype_info_90](https://msdn.microsoft.com/library/mt204014.aspx)
- [sp_droprolemember](https://msdn.microsoft.com/library/ms188369.aspx)
- [sp_execute](https://msdn.microsoft.com/library/ff848746.aspx)
- [sp_executesql](https://msdn.microsoft.com/library/ms188001.aspx)
- [sp_fkeys](https://msdn.microsoft.com/library/ms175090.aspx)
- [sp_pdw_add_network_credentials](https://msdn.microsoft.com/library/mt204011.aspx)
- [sp_pdw_database_encryption](https://msdn.microsoft.com/library/mt219360.aspx)
- [sp_pdw_database_encryption_regenerate_system_keys](https://msdn.microsoft.com/library/mt204033.aspx)
- [sp_pdw_log_user_data_masking](https://msdn.microsoft.com/library/mt204023.aspx)
- [sp_pdw_remove_network_credentials](https://msdn.microsoft.com/library/mt204038.aspx)
- [sp_pkeys](https://msdn.microsoft.com/library/ms189813.aspx)
- [sp_prepare](https://msdn.microsoft.com/library/ff848808.aspx)
- [sp_spaceused](https://msdn.microsoft.com/library/ms188776.aspx)
- [sp_special_columns_100](https://msdn.microsoft.com/library/mt204025.aspx)
- [sp_sproc_columns](https://msdn.microsoft.com/library/ms182705.aspx)
- [sp_statistics](https://msdn.microsoft.com/library/ms173842.aspx)
- [sp_tables](https://msdn.microsoft.com/library/ms186250.aspx)
- [sp_unprepare](https://msdn.microsoft.com/library/ff848735.aspx)



## <a name="set-statements"></a>CONJUNTO de instrucciones

- [SET ANSI_DEFAULTS](https://msdn.microsoft.com/library/ms188340.aspx)
- [ESTABLECER ANSI_NULL_DFLT_OFF](https://msdn.microsoft.com/library/ms187356.aspx)
- [SET ANSI_NULL_DFLT_ON](https://msdn.microsoft.com/library/ms187375.aspx)
- [SET ANSI_NULLS](https://msdn.microsoft.com/library/ms188048.aspx)
- [ESTABLECER ANSI_PADDING](https://msdn.microsoft.com/library/ms187403.aspx)
- [ESTABLECER ANSI_WARNINGS](https://msdn.microsoft.com/library/ms190368.aspx)
- [ESTABLECER ARITHABORT](https://msdn.microsoft.com/library/ms190306.aspx)
- [ESTABLECER ARITHIGNORE](https://msdn.microsoft.com/library/ms184341.aspx)
- [ESTABLECER CONCAT_NULL_YIELDS_NULL](https://msdn.microsoft.com/library/ms176056.aspx)
- [SET DATEFIRST](https://msdn.microsoft.com/library/ms181598.aspx)
- [ESTABLECER FORMATO_FECHA](https://msdn.microsoft.com/library/ms189491.aspx)
- [ESTABLECER FMTONLY](https://msdn.microsoft.com/library/ms173839.aspx)
- [ESTABLECER IMPLICIT_TRANSACITONS](https://msdn.microsoft.com/library/ms187807.aspx)
- [ESTABLECER LOCK_TIMEOUT](https://msdn.microsoft.com/library/ms189470.aspx)
- [ESTABLECER NUMBERIC_ROUNDABORT](https://msdn.microsoft.com/library/ms188791.aspx)
- [ESTABLECER QUOTED_IDENTIFIER](https://msdn.microsoft.com/library/ms174393.aspx)
- [ESTABLECER NÚMERO DE FILAS](https://msdn.microsoft.com/library/ms188774.aspx)
- [ESTABLECER TEXTSIZE](https://msdn.microsoft.com/library/ms186238.aspx)
- [ESTABLECER EL NIVEL DE AISLAMIENTO](https://msdn.microsoft.com/library/ms173763.aspx)
- [ESTABLECER XACT_ABORT](https://msdn.microsoft.com/library/ms188792.aspx)


## <a name="next-steps"></a>Pasos siguientes
Para obtener más información de referencia, vea [información general de referencia de almacén de datos de SQL][].

<!--Image references-->

<!--Article references-->
[Información general de referencia de almacén de datos SQL]: sql-data-warehouse-overview-reference.md

<!--MSDN references-->

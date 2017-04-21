<properties
   pageTitle="Construcción de cadenas de filtro para el Diseñador de tablas | Microsoft Azure"
   description="Construcción de cadenas de filtro para el Diseñador de tablas"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="storage"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="constructing-filter-strings-for-the-table-designer"></a>Construcción de cadenas de filtro para el Diseñador de tablas

## <a name="overview"></a>Información general

Para filtrar datos en una tabla de Azure que se muestra en el **Diseñador de tablas**de Visual Studio, cree una cadena de filtro y escriba en el campo de filtro. La sintaxis de la cadena de filtro definida por WCF Data Services y es similar a una cláusula WHERE de SQL, pero se envía al servicio de tabla mediante una solicitud HTTP. El **Diseñador de tablas** controla la codificación adecuada para que usted, para filtrar por un valor de propiedad deseada, solo necesita escribir el nombre de propiedad, operador de comparación, valor de criterio y de manera opcional, booleano operador en el campo de filtro. No debe incluir la opción de consulta $filter como lo haría si estaba construir una dirección URL para la tabla a través de la [Referencia de API de REST de servicios de almacenamiento](http://go.microsoft.com/fwlink/p/?LinkId=400447)de la consulta.

WCF Data Services se basan en el [Protocolo Open Data](http://go.microsoft.com/fwlink/p/?LinkId=214805) (OData). Para obtener detalles sobre la opción de consulta de sistema de filtro (**$filter**), vea la [especificación de convenciones de URI de OData](http://go.microsoft.com/fwlink/p/?LinkId=214806).

## <a name="comparison-operators"></a>Operadores de comparación

Se admiten los siguientes operadores lógicos para todos los tipos de propiedad:

|Operador lógico|Descripción|Ejemplo de cadena de filtro|
|---|---|---|
|EC|Igual|Ciudad EC 'Redmond'|
|gt|Mayor que|Precio gt 20|
|página|Mayor o igual a|Página de precio 10|
|lt|Menor que|Precio lt 20|
|le|Menor o igual|Le precio 100|
|ne|No es igual a|Ciudad ne 'Londres'|
|y|Y|Le precio 200 y precio gt 3.5|
|o|O|Le precio 3.5 o precio gt 200|
|no|No|no isAvailable|

Al crear una cadena de filtro, son importantes las siguientes reglas:

- Utilizar los operadores lógicos para comparar una propiedad a un valor. Tenga en cuenta que no es posible comparar una propiedad a un valor dinámico; un lado de la expresión debe ser una constante.

- Todas las partes de la cadena de filtro distinguen mayúsculas de minúsculas.

- El valor de la constante debe ser del mismo tipo de datos que la propiedad en orden para el filtro para devolver resultados válidos. Para obtener más información sobre tipos de propiedad admitidos, vea la [Descripción del modelo de datos de servicio de tabla](http://go.microsoft.com/fwlink/p/?LinkId=400448).

## <a name="filtering-on-string-properties"></a>Filtrado de las propiedades de cadena

Cuando se filtran las propiedades de cadena, escriba la constante de cadena entre comillas simples.

El ejemplo siguiente se filtra en las propiedades **PartitionKey** y **RowKey** ; propiedades adicionales de clave no también pueden agregarse a la cadena de filtro:

    PartitionKey eq 'Partition1' and RowKey eq '00001'

Puede incluir las expresiones de filtro entre paréntesis, aunque no es necesario:

    (PartitionKey eq 'Partition1') and (RowKey eq '00001')

Observe que el servicio de la tabla no admite consultas con caracteres comodín y no son compatibles o bien en el Diseñador de tablas. Sin embargo, puede realizar prefijo coincidentes mediante el uso de operadores de comparación en el prefijo que desee. En el ejemplo siguiente se devuelve entidades con un apellido propiedad que comienza con la letra 'A':

    LastName ge 'A' and LastName lt 'B'

## <a name="filtering-on-numeric-properties"></a>Filtrado en propiedades numéricas

Para filtrar por un número de punto flotante o un entero, especifique el número sin comillas.

Este ejemplo devuelve todas las entidades con una propiedad de edad cuyo valor es mayor que 30:

    Age gt 30

Este ejemplo devuelve todas las entidades con una propiedad CantidadDebida cuyo valor es menor o igual a 100.25:

    AmountDue le 100.25

## <a name="filtering-on-boolean-properties"></a>Filtrado de propiedades booleanas

Para filtrar por un valor de tipo Boolean, especifique **true** o **false** sin comillas.

El ejemplo siguiente devuelve todas las entidades donde la propiedad IsActive se establece en **true**:

    IsActive eq true

También puede escribir esta expresión de filtro sin el operador lógico. En el ejemplo siguiente, el servicio de la tabla también devolverá todas las entidades donde IsActive sea **verdadera**:

    IsActive

Para volver a todas las entidades donde IsActive es false, puede usar not operador:

    not IsActive

## <a name="filtering-on-datetime-properties"></a>Filtrado de las propiedades de fecha y hora

Para filtrar por un valor DateTime, especifique la palabra clave de **fecha y hora** , seguida de la constante de fecha y hora entre comillas simples. Debe ser la constante de fecha y hora en formato UTC combinado, como se describe en [Formato de valores de propiedad de fecha y hora](http://go.microsoft.com/fwlink/p/?LinkId=400449).

En el ejemplo siguiente se devuelve entidades donde la propiedad CustomerSince es igual a 10 de julio de 2008:

    CustomerSince eq datetime'2008-07-10T00:00:00Z'

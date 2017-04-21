<properties
   pageTitle="Interactuar con informes con la API de JavaScript | Microsoft Azure"
   description="Power BI incrustado, interactuar con informes de uso de la API de JavaScript"
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>

# <a name="interact-with-power-bi-reports-using-the-javascript-api"></a>Interactuar con informes de Power BI con la API de JavaScript

La API de JavaScript de Power BI le permite incrustar fácilmente informes de Power BI en las aplicaciones. Con la API, las aplicaciones mediante programación pueden interactuar con elementos de informe diferente como páginas y filtros. Este interactividad hace que los informes de Power BI más integrada parte de la aplicación.

Insertar un informe de Power BI en la aplicación mediante un iframe alojado como parte de la aplicación. El iframe actúa como límite entre la aplicación y el informe, como puede ver en la siguiente imagen. 

![Power BI incrustado iframe sin API de Javascript](media\powerbi-embedded-interact-with-reports\powerbi-embedded-interact-report-1.png)

El iframe facilita el proceso de incrustación mucho, pero sin la API de JavaScript el informe y la aplicación no pueden interactuar con ellos. Esta falta de interacción puede hacer que cree que el informe no está realmente parte de la aplicación. El informe y la aplicación se necesitan realmente para comunicarse y hacia atrás, como se muestra en la siguiente imagen.

![Power BI incrustado iframe con la API de Javascript](media\powerbi-embedded-interact-with-reports\powerbi-embedded-interact-report-2.png)

La API de JavaScript de Power BI le permite escribir código que puede pasar a través del límite de iframe de forma segura. Esto permite a su aplicación para realizar una acción de programación de un informe y para escuchar los eventos de acciones que los usuarios realizan en el informe.

## <a name="what-can-you-do-with-the-power-bi-javascript-api"></a>¿Qué puede hacer con la API de JavaScript de Power BI?
Con la API de JavaScript administrar informes, vaya a las páginas de un informe, filtrar un informe y controlar eventos la incrustación. En el diagrama siguiente muestra la estructura de la API.

![Diagrama de Power BI API de JavaScript](media\powerbi-embedded-interact-with-reports\powerbi-embedded-interact-report-3.png)


### <a name="manage-reports"></a>Administrar informes
La API de Javascript permite administrar el comportamiento en el nivel de página y el informe:

- Incrustar un informe de Power BI específicas de forma segura en la aplicación, pruebe la [aplicación de demostración para insertar](http://azure-samples.github.io/powerbi-angular-client/#/scenario1)
  - Conjunto de token de acceso
- Configurar el informe
  - Habilitar y deshabilitar el panel de filtro y el panel de navegación de página, pruebe la [aplicación de demostración de la configuración de actualización](http://azure-samples.github.io/powerbi-angular-client/#/scenario6)
  - Establecer valores predeterminados para los filtros y páginas: pruebe la [demostración de valores predeterminados de establecer](http://azure-samples.github.io/powerbi-angular-client/#/scenario5)
- Escriba y salir del modo de pantalla completa

[Obtener más información sobre cómo incrustar un informe](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embedding-Basics)


### <a name="navigate-to-pages-in-a-report"></a>Vaya a las páginas de un informe
Enbales de la API de JavaScript para detectar todas las páginas de un informe y establecer la página actual. Pruebe la [aplicación de demostración de navegación](http://azure-samples.github.io/powerbi-angular-client/#/scenario3).

[Obtenga más información sobre la navegación de páginas](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Page-Navigation)

### <a name="filter-a-report"></a>Filtrar un informe
La API de JavaScript proporciona básicas y avanzadas de filtrado de las capacidades de informes incrustados y las páginas de informe. Pruebe la [aplicación de demostración de filtrado](http://azure-samples.github.io/powerbi-angular-client/#/scenario4)y revise aquí algunos código introductorio.  


#### <a name="basic-filters"></a>Filtros básicos
Un filtro básico se coloca en un columna o nivel de jerarquía y contiene una lista de valores para incluir o excluir.

```
const basicFilter: pbi.models.IBasicFilter = {
  $schema: "http://powerbi.com/product/schema#basic",
  target: {
    table: "Store",
    column: "Count"
  },
  operator: "In",
  values: [1,2,3,4]
}
```


#### <a name="advanced-filters"></a>Filtros avanzados
Filtros avanzados usar el operador lógico y u o y aceptar uno o dos condiciones, cada una con su propio operador y un valor. Condiciones compatibles son:

- Ninguno
- LessThan
- LessThanOrEqual
- Mayor que
- GreaterThanOrEqual
- Contiene
- DoesNotContain
- StartsWith
- DoesNotStartWith
- Es
- IsNot
- ESBLANCO
- IsNotBlank

```
const advancedFilter: pbi.models.IAdvancedFilter = {
  $schema: "http://powerbi.com/product/schema#advanced",
  target: {
    table: "Store",
    column: "Name"
  },
  logicalOperator: "Or",
  conditions: [
    {
      operator: "Contains",
      value: "Wash"
    },
    {
      operator: "Contains",
      value: "Park"
    }
  ]
}
```
[Obtenga más información sobre el filtrado](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Filters)


### <a name="handling-events"></a>Control de eventos
Además de enviar información en iframe, la aplicación también puede recibir información sobre los siguientes eventos procedentes de iframe:

- Insertar
  - cargar
  - Error
- Informes
  - pageChanged
  - datosSeleccionada (próximamente)

[Más información sobre cómo controlar eventos](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Handling-Events)


## <a name="next-steps"></a>Pasos siguientes
Para obtener más información acerca de la API de JavaScript de Power BI, consulte los siguientes vínculos:

- [Wiki de la API de JavaScript](https://github.com/Microsoft/PowerBI-JavaScript/wiki)
- [Referencia del modelo de objeto](https://microsoft.github.io/powerbi-models/modules/_models_.html)
- Ejemplos
  - [Angular](http://azure-samples.github.io/powerbi-angular-client)
  - [Ember](https://github.com/Microsoft/powerbi-ember)
- [Demostración](https://microsoft.github.io/PowerBI-JavaScript/demo/)

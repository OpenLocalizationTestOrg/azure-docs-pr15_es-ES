<properties 
    pageTitle="Cómo utilizar las propiedades de las directivas de administración de la API de Azure" 
    description="Obtenga información sobre cómo usar las propiedades de las directivas de administración de la API de Azure." 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>


# <a name="how-to-use-properties-in-azure-api-management-policies"></a>Cómo utilizar las propiedades de las directivas de administración de la API de Azure

Las directivas de administración de la API son una capacidad eficaz del sistema que permiten la publisher cambiar el comportamiento de la API a través de la configuración. Las directivas son un conjunto de instrucciones que se ejecutan secuencialmente en la solicitud o respuesta de una API. Instrucciones de directiva se pueden construir con valores de texto literal, las expresiones de directiva y propiedades. 

Cada instancia del servicio de administración de la API tiene una colección de propiedades de pares de clave/valor son globales a la instancia de servicio. Estas propiedades pueden utilizarse para administrar los valores de cadena constante en todas las directivas y configuración de la API. Cada propiedad tiene los siguientes atributos.


| Atributo | Tipo            | Descripción                                                                                             |
|-----------|-----------------|---------------------------------------------------------------------------------------------------------|
| Nombre      | cadena          | El nombre de la propiedad. Puede contener sólo letras, dígitos, período, guiones y caracteres de subrayado. |
| Valor     | cadena          | El valor de la propiedad. No puede estar vacía ni constar únicamente de espacios en blanco.                           |
| Secreto    | valor booleano         | Determina si el valor es un secreto y se debe cifrar o no.                                |
| Etiquetas      | matriz de cadena | Etiquetas opcional que cuando proporciona puede usarse para filtrar la lista de propiedades.                               |

Propiedades se configuran en el portal de publisher en la pestaña **Propiedades** . En el ejemplo siguiente, se configuran tres propiedades.

![Propiedades][api-management-properties]

Valores de propiedad pueden contener cadenas literales y [expresiones de directiva](https://msdn.microsoft.com/library/azure/dn910913.aspx). La siguiente tabla muestra las propiedades de tres de ejemplo anterior y sus atributos. El valor de `ExpressionProperty` es una expresión de directiva que devuelve una cadena que contiene la fecha y hora actuales. La propiedad `ContosoHeaderValue` está marcado como un secreto, por lo que no se muestra el valor.

| Nombre               | Valor                      | Secreto | Etiquetas    |
|--------------------|----------------------------|--------|---------|
| ContosoHeader      | TrackingId                 | Falso  | Contoso |
| ContosoHeaderValue | ••••••••••••••••••••••     | True   | Contoso |
| ExpressionProperty | @(DateTime.Now.ToString()) | Falso  |         |

## <a name="to-use-a-property"></a>Usar una propiedad

Para usar una propiedad en una directiva, coloque el nombre de propiedad dentro de un par de llaves como doble `{{ContosoHeader}}`, como se muestra en el ejemplo siguiente.

    <set-header name="{{ContosoHeader}}" exists-action="override">
      <value>{{ContosoHeaderValue}}</value>
    </set-header>

En este ejemplo, `ContosoHeader` se usa como el nombre de un encabezado en un `set-header` directiva, y `ContosoHeaderValue` se usa como el valor de dicho encabezado. Cuando esta directiva se evalúa durante una solicitud o una respuesta a la puerta de enlace API administración, `{{ContosoHeader}}` y `{{ContosoHeaderValue}}` se reemplazan por sus valores de propiedad respectivos.

Propiedades de pueden usarse como atributo completado o valores de elemento, como se muestra en el ejemplo anterior, pero también pueden insertar en o combinados con un elemento de una expresión de texto literal, tal como se muestra en el ejemplo siguiente:`<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

Propiedades también pueden contener expresiones de directiva. En el ejemplo siguiente, la `ExpressionProperty` se utiliza.

    <set-header name="CustomHeader" exists-action="override">
        <value>{{ExpressionProperty}}</value>
    </set-header>

Cuando se evalúa esta directiva, `{{ExpressionProperty}}` se reemplaza por su valor: `@(DateTime.Now.ToString())`. Dado que el valor es una expresión de directiva, la expresión se evalúa y la directiva continúa con la ejecución.

Puede probar este en el portal de programadores mediante una llamada a una operación que tiene una directiva con las propiedades en el ámbito. En el ejemplo siguiente, se denomina una operación con el ejemplo anterior dos `set-header` directivas de propiedades. Observe que la respuesta contiene dos encabezados personalizados que se han configurado con las directivas de propiedades.

![Portal de programadores][api-management-send-results]

Si mira el [Inspector de la API de seguimiento](api-management-howto-api-inspector.md) para una llamada que incluye las dos directivas de ejemplo anterior con las propiedades, puede ver los dos `set-header` directivas con los valores de propiedad insertados, así como la evaluación de la expresión de directiva de la propiedad que contiene la expresión de la directiva.

![Traza de API Inspector][api-management-api-inspector-trace]

Tenga en cuenta que, mientras que los valores de propiedad pueden contener expresiones de directiva, valores de propiedad no pueden contener otras propiedades. Si el texto que contiene una referencia de propiedad se usa como un valor de propiedad, `Property value text {{MyProperty}}`, esa referencia de propiedad no pueden sustituirse y se incluye como parte del valor de propiedad.

## <a name="to-create-a-property"></a>Para crear una propiedad

Para crear una propiedad, haga clic en **Agregar propiedad** en la pestaña **Propiedades** .

![Agregar (propiedad)][api-management-properties-add-property-menu]

**Nombre** y **valor** son valores requeridos. Si el valor de esta propiedad es un secreto, marque la casilla **este es un secreto** . Escriba una o más etiquetas opcionales para ayudar a organizar las propiedades y haga clic en **Guardar**.

![Agregar (propiedad)][api-management-properties-add-property]

Cuando se guarda una nueva propiedad, el cuadro de texto de **la propiedad de búsqueda** se rellena con el nombre de la nueva propiedad y se muestra la nueva propiedad. Para mostrar todas las propiedades, desactive el cuadro de texto de **la propiedad de búsqueda** y presione ENTRAR.

![Propiedades][api-management-properties-property-saved]

Para obtener información sobre cómo crear una propiedad con la API de REST, consulte [crear una propiedad con la API de REST](https://msdn.microsoft.com/library/azure/mt651775.aspx#Put).

## <a name="to-edit-a-property"></a>Para editar una propiedad

Para editar una propiedad, haga clic en **Editar** junto a la propiedad que desea editar.

![Editar propiedad][api-management-properties-edit]

Realice los cambios que desee y haga clic en **Guardar**. Si cambia el nombre de propiedad, las directivas que hacen referencia a esa propiedad se actualizan automáticamente para usar el nuevo nombre.

![Editar propiedad][api-management-properties-edit-property]

Para obtener información sobre cómo editar una propiedad con la API de REST, consulte [Editar una propiedad con la API de REST](https://msdn.microsoft.com/library/azure/mt651775.aspx#Patch).

## <a name="to-delete-a-property"></a>Para eliminar una propiedad

Para eliminar una propiedad, haga clic en **Eliminar** junto a la propiedad que desea eliminar.

![Eliminar (propiedad)][api-management-properties-delete]

Haga clic en **Sí, elimínelo** para confirmar.

![Confirmar la eliminación][api-management-delete-confirm]

>[AZURE.IMPORTANT] Si la propiedad se hace referencia a las directivas, podrá eliminarlo correctamente hasta que se quita la propiedad de todas las directivas que usa.

Para obtener información acerca de cómo eliminar una propiedad utilizando la API de REST, consulte [Eliminar una propiedad con la API de REST](https://msdn.microsoft.com/library/azure/mt651775.aspx#Delete).

## <a name="to-search-and-filter-properties"></a>Para buscar y filtrar propiedades

La ficha **Propiedades** incluye búsquedas y funciones para ayudarle a administrar las propiedades de filtrado. Para filtrar la lista de propiedades por nombre de propiedad, escriba un término de búsqueda en el cuadro de texto de **la propiedad de búsqueda** . Para mostrar todas las propiedades, desactive el cuadro de texto de **la propiedad de búsqueda** y presione ENTRAR.

![Búsqueda][api-management-properties-search]

Para filtrar la lista de propiedades por valores de etiqueta, escriba una o varias etiquetas en el cuadro de texto de **Filtro por etiquetas** . Para mostrar todas las propiedades, desactive el cuadro de texto **filtrar por etiquetas** y presione ENTRAR.

![Filtro][api-management-properties-filter]

## <a name="next-steps"></a>Pasos siguientes

-   Más información sobre cómo trabajar con directivas
    -   [Directivas de administración de la API de](api-management-howto-policies.md)
    -   [Referencia de directiva](https://msdn.microsoft.com/library/azure/dn894081.aspx)
    -   [Expresiones de directiva](https://msdn.microsoft.com/library/azure/dn910913.aspx)

## <a name="watch-a-video-overview"></a>Ver un vídeo de introducción

> [AZURE.VIDEO use-properties-in-policies]

[api-management-properties]: ./media/api-management-howto-properties/api-management-properties.png
[api-management-properties-add-property]: ./media/api-management-howto-properties/api-management-properties-add-property.png
[api-management-properties-edit-property]: ./media/api-management-howto-properties/api-management-properties-edit-property.png
[api-management-properties-add-property-menu]: ./media/api-management-howto-properties/api-management-properties-add-property-menu.png
[api-management-properties-property-saved]: ./media/api-management-howto-properties/api-management-properties-property-saved.png
[api-management-properties-delete]: ./media/api-management-howto-properties/api-management-properties-delete.png
[api-management-properties-edit]: ./media/api-management-howto-properties/api-management-properties-edit.png
[api-management-delete-confirm]: ./media/api-management-howto-properties/api-management-delete-confirm.png
[api-management-properties-search]: ./media/api-management-howto-properties/api-management-properties-search.png
[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png


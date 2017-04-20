<properties 
    pageTitle="Nueva versión del esquema 2016-06-01 | Microsoft Azure" 
    description="Obtenga información sobre cómo escribir la definición de JSON para la versión más reciente de aplicaciones de lógica" 
    authors="jeffhollan" 
    manager="dwrede" 
    editor="" 
    services="logic-apps" 
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/25/2016"
    ms.author="jehollan"/>
    
# <a name="new-schema-version-2016-06-01"></a>Nueva versión del esquema 2016-06-01

El nuevo esquema y API versión para las aplicaciones de lógica tiene una serie de mejoras que mejorar la confiabilidad y facilidad de uso de aplicaciones de lógica. Hay diferencias clave 3:

1. Adición de ámbitos, que son acciones que contienen un conjunto de acciones.
1. Condiciones y bucles son acciones de primera clase
1. Orden de ejecución más detallado a través de `runAfter` propiedad (que reemplaza `dependsOn`)

Para obtener información sobre cómo actualizar sus aplicaciones de lógica desde el esquema de la vista previa de 2015-08-01 en el esquema de 2016-06-01, [consulte la sección actualización.](#upgrading-to-2016-06-01-schema)


## <a name="1-scopes"></a>1. ámbitos de

Uno de los cambios más importantes de este esquema es la adición de ámbitos y la capacidad de anidar acciones unas dentro de otras.  Esto es útil cuando la agrupación de un conjunto de acciones, o cuando es necesario anidar acciones dentro de otro (por ejemplo una condición puede contener otra condición).  Pueden obtener más detalles sobre la sintaxis de ámbito se encuentra [aquí](app-service-logic-loops-and-scopes.md), pero un ejemplo sencillo ámbito se encuentra debajo de:


```
{
    "actions": {
        "My_Scope": {
            "type": "scope",
            "actions": {                
                "Http": {
                    "inputs": {
                        "method": "GET",
                        "uri": "http://www.bing.com"
                    },
                    "runAfter": {},
                    "type": "Http"
                }
            }
        }
    }
}
```

## <a name="2-conditions-and-loops-changes"></a>2. condiciones y bucles cambios

En las versiones anteriores del esquema, condiciones y bucles eran parámetros asociados a una sola acción.  Esta limitación se eliminó en este esquema y ahora condiciones y bucles mostrarse como un tipo de acción.  Puede encontrar más información [en este artículo](app-service-logic-loops-and-scopes.md)y, a continuación se muestra un ejemplo sencillo de una condición de acción:

```
{
    "If_trigger_is_foo": {
        "type": "If",
        "expression": "@equals(triggerBody(), 'foo')",
        "runAfter": { },
        "actions": {
            "Http_2": {
                "inputs": {
                    "method": "GET",
                    "uri": "http://www.bing.com"
                },
                "runAfter": {},
                "type": "Http"
            }
        },
        "else": 
        {
            "if_trigger_is_bar": "..."
        }      
    }
}
```

## <a name="3-runafter-property"></a>3. propiedad RunAfter

El nuevo `runAfter` reemplazo de la propiedad `dependsOn` para ayudar a permitir más precisión en orden de ejecución.  `dependsOn`era sinónimo de "la acción ejecutó y se realizó correctamente," sin embargo, muchas veces necesita ejecutar una acción si la acción anterior se realiza correctamente, no se pudo u omitidos.  `runAfter`permite dicha flexibilidad.  Es un objeto que especifica todos los nombres de acción que se ejecutará después y se define una matriz de estado que son aceptables para activar desde.  Por ejemplo, si desea ejecutar después de paso correcta A y B se realizó correctamente o no, se crea la siguiente `runAfter` propiedad:

```
{
    "...",
    "runAfter": {
        "A": ["Succeeded"],
        "B": ["Succeeded", "Failed"]
    }
}
```

## <a name="upgrading-to-2016-06-01-schema"></a>Actualizar esquema 2016-06-01

Actualizar el nuevo esquema 2016-06-01 basta con unos pocos pasos.  Obtener más información sobre los cambios del esquema se puede encontrar [en este artículo](app-service-logic-schema-2016-04-01.md).  El proceso de actualización incluye ejecutando el script de actualización, guardar como una nueva aplicación de lógica y podría sobrescribir antigua aplicación lógica si es necesario.

1. Abra la aplicación de lógica actual.
1. Haga clic en el botón de **Actualización de esquema** en la barra de herramientas
   
    ![][1]
   
    Se devolverá la definición actualizada.  Puede copiar y pegar esta en una definición de recursos si es necesario, pero se **recomienda** usar el botón **Guardar como** garantizar que todas las conexiones las referencias son válidas en la aplicación lógica actualizado.
1. Haga clic en el botón **Guardar como** en la barra de herramientas del módulo de actualización.
1. Rellene el nombre y lógica el estado de aplicación y haga clic en **crear** para implementar la aplicación de la lógica de actualización.
1. Compruebe que su aplicación lógica actualizado funciona según lo esperado.

    >[AZURE.NOTE] Si está utilizando un desencadenador manual o solicitud, habrá cambiado la dirección URL de devolución de llamada en la nueva aplicación lógica.  Utilice la nueva dirección URL para comprobar funciona to-end y, a continuación, se puede clonar sobre la aplicación de lógica existente para conservar las direcciones URL anteriores.

1. *Opcional* Utilizar el botón **Duplicar** en la barra de herramientas (adyacente al icono de **Esquema de actualización** de la imagen de arriba) para sobrescribir su aplicación lógica anterior con la nueva versión del esquema.  Esto es necesario sólo si desea conservar el mismo identificador de recursos o desencadenador URL de la aplicación de la lógica de solicitud.

### <a name="upgrade-tool-notes"></a>Notas de la herramienta de actualización

#### <a name="condition-mapping"></a>Asignación de condición

La herramienta le será un mejor esfuerzo para agrupar las acciones de true y false rama juntos en un ámbito de la definición actualizado.  Concreto, el patrón de diseñador `@equals(actions('a').status, 'Skipped')` debe mostrarse como una `else` acción.  Sin embargo si la herramienta detecta tramas no reconoce potencialmente creará condiciones independientes para el true y la rama false.  Acciones pueden asignadas volver a publicar la actualización si es necesario.

#### <a name="foreach-with-condition"></a>ForEach con condición
  
El modelo anterior de un bucle con una condición por elemento se puede replicar en el nuevo esquema con la acción de filtro.  Debe aparecer automáticamente en la actualización.  La condición se convierte en una acción de filtro antes el bucle (para devolver solo una matriz de elementos que cumplan la condición) y matriz se pasa a la acción foreach.  Puede ver un ejemplo de este [en este artículo](app-service-logic-loops-and-scopes.md)

#### <a name="resource-tags"></a>Etiquetas de recursos

Se quitarán las etiquetas de recursos en la actualización y debe establecer de nuevo para el flujo de trabajo actualizado.

## <a name="other-changes"></a>Otros cambios

### <a name="manual-trigger-renamed-to-request-trigger"></a>Cambiar el nombre de desencadenador de solicitud de desencadenador manual

El tipo de `manual` ha sido descartado y cambia a `request` con el tipo de `http`.  Esto es más coherente con el tipo de trama que del desencadenador se usa para generar.

### <a name="new-filter-action"></a>Nueva acción 'filtro'

Si está trabajando con una matriz de gran tamaño y la necesidad de filtro a un conjunto más pequeño de elementos, puede usar el nuevo tipo de 'filtro'.  Acepta una matriz y una condición y se evalúa la condición para cada elemento y devolver una matriz de elementos que cumplan la condición.

### <a name="foreach-and-until-action-restrictions"></a>ForEach y hasta restricciones de acción

Foreach y hasta que el bucle se limitan a una única acción.

### <a name="trackedproperties-on-actions"></a>TrackedProperties de acciones

Acciones ahora pueden tener una propiedad adicional (elemento relacionado con `runAfter` y `type`) denominado `trackedProperties`.  Es un objeto que especifica determinadas entradas de acción o las salidas que se incluirán en la telemetría diagnóstico de Azure que emite como parte de un flujo de trabajo.  Por ejemplo:

```
{                
    "Http": {
        "inputs": {
            "method": "GET",
            "uri": "http://www.bing.com"
        },
        "runAfter": {},
        "type": "Http",
        "trackedProperties": {
            "responseCode": "@action().outputs.statusCode",
            "uri": "@action().inputs.uri"
        }
    }
}
```

## <a name="next-steps"></a>Pasos siguientes
- [Usar la definición de flujo de trabajo de aplicación de lógica](app-service-logic-author-definitions.md)
- [Crear una plantilla de implementación de la aplicación de lógica](app-service-logic-create-deploy-template.md)


<!-- Image references -->
[1]: ./media/app-service-logic-schema-2016-04-01/upgradeButton.png

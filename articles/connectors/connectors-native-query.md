<properties
    pageTitle="Agregar la acción de consulta en las aplicaciones de lógica | Microsoft Azure"
    description="Descripción general de la acción de consulta para realizar acciones como matriz de filtro."
    services=""
    documentationCenter=""
    authors="jeffhollan"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/20/2016"
   ms.author="jehollan"/>

# <a name="get-started-with-the-query-action"></a>Introducción a la acción de consulta

Mediante la acción de consulta, puede trabajar con lotes y las matrices para llevar a cabo los flujos de trabajo:

- Crear una tarea para todos los registros de alta prioridad desde una base de datos.
- Guardar todos los datos adjuntos de PDF para correos electrónicos en un blobs de Windows Azure.

Para empezar a usar la acción de consulta en una aplicación de lógica, consulte [crear una aplicación de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="use-the-query-action"></a>Usar la acción de consulta

Una acción es una operación que se lleva a cabo por el flujo de trabajo que se define en una aplicación de lógica. [Más información acerca de las acciones](connectors-overview.md).  

La acción de consulta actualmente tiene una operación, denominada la matriz de filtro, que se expone en el diseñador. Esto le permite consultar una matriz y devuelve un conjunto de resultados filtrados.

Le mostramos cómo agregar en una aplicación de lógica:

1. Seleccione el botón **Nuevo paso** .
2. Elija **Agregar una acción**.
3. En el cuadro de búsqueda de la acción, escriba **filtro** para la acción de la **matriz de filtro** de la lista.

    ![Seleccione la acción de consulta](./media/connectors-native-query/using-action-1.png)

4. Seleccionar una matriz a filtrar. (La siguiente captura de pantalla muestra la matriz de resultados de una búsqueda de Twitter).
5. Crear una condición se evalúa en cada elemento. (La siguiente captura de pantalla filtros tweets de los usuarios que tienen más de 100 seguidores).

    ![Completar la acción de consulta](./media/connectors-native-query/using-action-2.png)

    La acción dará como resultado una nueva matriz que contiene solo los resultados que cumplen los requisitos de filtro.
6. Haga clic en la esquina superior izquierda de la barra de herramientas para guardar, y su aplicación lógica guardar y publicar (activar).

## <a name="query-action"></a>Acción de consulta

Estos son los detalles de la acción que admita este conector. El conector tiene una posible acción.

|Acción|Descripción|
|---|---|
|Matriz de filtro|Evalúa una condición para cada elemento de una matriz y devuelve los resultados|

## <a name="action-details"></a>Detalles de acción

La acción de la consulta incluye una posible acción. Las tablas siguientes describen los campos de entrada obligatorios y opcionales para la acción y los detalles de salida correspondientes que están asociados con el uso de la acción.

### <a name="filter-array"></a>Matriz de filtro
Los siguientes son campos de entrada de la acción que realiza una solicitud HTTP saliente.
A * significa que es un campo obligatorio.

|Nombre para mostrar|Nombre de propiedad|Descripción|
|---|---|---|
|Desde *|De|La matriz para filtrar|
|Condición *|donde|La condición se evalúa para cada elemento|
<br>

### <a name="output-details"></a>Detalles de salida

Los siguientes son los detalles de resultados de la respuesta HTTP.

|Nombre de propiedad|Tipo de datos|Descripción|
|---|---|---|
|Matriz filtrada|matriz|Matriz que contiene un objeto por cada resultado filtrado|

## <a name="next-steps"></a>Pasos siguientes

Ahora, pruebe la plataforma y [crear una aplicación de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md). Puede explorar los otros conectores disponibles en las aplicaciones de lógica consultando nuestra [lista de API](apis-list.md).

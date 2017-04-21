<properties
    pageTitle="Iniciar el Diseñador de vistas de análisis | Microsoft Azure"
    description="Diseñador de vista de análisis de registro le permite crear vistas personalizadas en la consola OMS que contienen diferentes visualizaciones de datos en el repositorio OMS. Este artículo contiene información general sobre el Diseñador de vistas y procedimientos para crear y editar vistas personalizadas."
    services="log-analytics"
    documentationCenter=""
    authors="bwren"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="bwren"/>

# <a name="log-analytics-view-designer"></a>Diseñador de vistas de análisis de registro
El Diseñador de vista de análisis de registro le permite crear vistas personalizadas en la consola OMS que contienen diferentes visualizaciones de datos en el repositorio OMS. Este artículo contiene información general sobre el Diseñador de vistas y procedimientos para crear y editar vistas personalizadas.

Otros artículos disponibles para el Diseñador de vistas son:

- [Referencia de mosaico](log-analytics-view-designer-tiles.md) : referencia de la configuración para cada uno de los mosaicos disponibles para su uso en sus vistas personalizadas. 
- [Referencia de la parte de visualización](log-analytics-view-designer-parts.md) : referencia de la configuración para cada uno de los mosaicos disponibles para su uso en sus vistas personalizadas. 


## <a name="concepts"></a>Conceptos
Vistas creadas con el Diseñador de vistas contienen los elementos de la tabla siguiente.

| Elemento | Descripción |
|:--|:--|
| Mosaico | Se muestra en el panel de información general de análisis de registro principal.  Incluye un resumen visual de la información contenida en la vista personalizada.  Los distintos tipos de mosaico proporcionan diferentes visualizaciones de registros en el repositorio OMS.  Haga clic en el icono para abrir la vista personalizada. |
| Vista personalizada | Se muestra cuando el usuario hace clic en el mosaico.  Contiene uno o más elementos de visualización. |
| Elementos de visualización | Visualización de datos en el repositorio OMS basada en una o varias [búsquedas de registro](log-analytics-log-searches.md).  La mayoría de elementos incluirá un encabezado que proporciona una visualización de alto nivel y una lista de los mejores resultados.  Tipos de otra parte proporcionan diferentes visualizaciones de registros en el repositorio OMS.  Haga clic en elementos en el elemento para realizar una búsqueda de registro proporcionar registros detallados. |

![Información general del Diseñador de vista](media/log-analytics-view-designer/overview.png)

## <a name="add-view-designer-to-your-workspace"></a>Agregar Generador de vista al área de trabajo
Mientras está en el Diseñador de vistas en vista previa, debe agregar al área de trabajo, seleccione **Características de vista previa** en la sección **configuración** del portal OMS.

![Habilitar vista previa](media/log-analytics-view-designer/preview.png)

## <a name="creating-and-editing-views"></a>Crear y editar vistas

### <a name="create-a-new-view"></a>Crear una nueva vista
Abrir una nueva vista en el **Diseñador de vista** haciendo clic en el mosaico del Diseñador de vistas en el panel OMS principal.

![Mosaico de diseñador de vista](media/log-analytics-view-designer/view-designer-tile.png)

### <a name="edit-an-existing-view"></a>Editar una vista existente
Para editar una vista existente en el Diseñador de vistas, abra la vista haciendo clic en su mosaico en el panel OMS principal.  A continuación, haga clic en el botón **Editar** para abrir la vista en el Diseñador de vistas.

![Editar una vista](media/log-analytics-view-designer/menu-edit.png)

### <a name="clone-an-existing-view"></a>Clonar una vista existente
Al clonar una vista, crea una nueva vista y se abre en el Diseñador de vistas.  La nueva vista tendrá el mismo nombre del original con "Copiar" anexado al final de la misma.  Para duplicar una vista, abra la vista existente haciendo clic en su mosaico en el panel OMS principal.  A continuación, haga clic en el botón **Duplicar** para abrir la vista en el Diseñador de vistas.

![Clonar una vista](media/log-analytics-view-designer/edit-menu-clone.png)

### <a name="delete-an-existing-view"></a>Eliminar una vista existente
Para eliminar una vista existente, abra la vista haciendo clic en su mosaico en el panel OMS principal.  A continuación, haga clic en el botón **Editar** para abrir la vista en el Diseñador de vistas y haga clic en **Eliminar vista**.

![Eliminar una vista](media/log-analytics-view-designer/edit-menu-delete.png)

### <a name="export-an-existing-view"></a>Exportar una vista existente
Puede exportar una vista a un archivo JSON que puede importar a otra área de trabajo o utilizar en una [plantilla de administrador de recursos de Azure](../resource-group-authoring-templates.md).  Para exportar una vista existente, abra la vista haciendo clic en su mosaico en el panel OMS principal.  A continuación, haga clic en el botón **Exportar** para crear un archivo en la carpeta de descarga del explorador.  El nombre del archivo será el nombre de la vista con la extensión *omsview*.

![Exportar una vista](media/log-analytics-view-designer/edit-menu-export.png)

### <a name="import-an-existing-view"></a>Importar una vista existente
Puede importar un archivo *omsview* que ha exportado desde otro grupo de administración.  Para importar una vista existente, crear una nueva vista.  A continuación, haga clic en el botón **Importar** y seleccione el archivo *omsview* .  La configuración en el archivo se copiarán en la vista existente.

![Exportar una vista](media/log-analytics-view-designer/edit-menu-import.png)

## <a name="working-with-view-designer"></a>Trabajar con el Diseñador de vistas
El Diseñador de vistas tiene tres paneles.  El panel de **Diseño** representa la vista personalizada.  Al agregar mosaicos y elementos desde el panel de **Control** en el panel de **Diseño** que se hayan agregado a la vista.  El panel **Propiedades** mostrará las propiedades para el mosaico o el elemento seleccionado.

![Diseñador de vistas](media/log-analytics-view-designer/view-designer-screenshot.png)

### <a name="configure-view-tile"></a>Configurar el mosaico de la vista
Una vista personalizada puede tener solo un mosaico simple.  Seleccione la pestaña **de mosaico** en el panel de **Control** para ver el mosaico actual o seleccione una alternativa.  El panel **Propiedades** mostrará las propiedades para el mosaico actual.  Configurar las propiedades de mosaico según la información detallada en la [Referencia de mosaico](log-analytics-view-designer-tiles.md) y haga clic en **Aplicar** para guardar los cambios.

### <a name="configure-visualization-parts"></a>Configurar partes de visualización
Una vista puede incluir cualquier número de elementos de visualización.  Seleccione la ficha **Ver** y, a continuación, en un elemento de visualización para agregar a la vista.  El panel **Propiedades** mostrará las propiedades del elemento seleccionado.  Configurar las propiedades de vista según la información detallada en la [referencia de elemento de visualización](log-analytics-view-designer-parts.md) y haga clic en **Aplicar** para guardar los cambios.

### <a name="delete-a-visualization-part"></a>Eliminar un elemento de visualización
Puede quitar un elemento de visualización de la vista haciendo clic en el botón **X** de la esquina superior derecha del elemento.

### <a name="rearrange-visualization-parts"></a>Reorganizar elementos de visualización
Vistas solo tienen una fila de elementos de visualización.  Reorganizar los elementos existentes en una vista haciendo clic y arrastrándolos a una nueva ubicación.


## <a name="next-steps"></a>Pasos siguientes

- Agregar [mosaicos](log-analytics-view-designer-tiles.md) para la vista personalizada.
- Agregar [Elementos de visualización](log-analytics-view-designer-parts.md) a la vista personalizada.

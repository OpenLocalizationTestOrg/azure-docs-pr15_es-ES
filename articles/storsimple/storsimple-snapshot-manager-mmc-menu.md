<properties 
   pageTitle="Menú de acciones de MMC Administrador de instantáneas StorSimple | Microsoft Azure"
   description="Describe cómo utilizar las acciones de menú estándar de consola de administración de Microsoft (MMC) en el Administrador de instantáneas StorSimple."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="04/25/2016"
   ms.author="v-sharos" />

# <a name="use-the-mmc-menu-actions-in-storsimple-snapshot-manager"></a>Usar el menú de acciones de MMC en el Administrador de instantáneas StorSimple

## <a name="overview"></a>Información general

En el Administrador de StorSimple instantánea, verá las siguientes acciones aparece en todos los menús de acción y todas las variaciones del panel de **acciones** . 

- Vista
- Nueva ventana desde aquí 
- Actualizar 
- Lista de exportación 
- Ayuda 

Estas acciones forman parte de la consola de administración de Microsoft (MMC) y no son específicas para el Administrador de instantáneas StorSimple. Este tutorial describe estas acciones y explica cómo usar cada uno de ellos en el Administrador de instantáneas StorSimple.

## <a name="view"></a>Vista

Puede usar la opción de **vista** para cambiar la vista del panel de **resultados** y cambiar la vista de la ventana de consola. 

#### <a name="to-change-the-results-pane-view"></a>Para cambiar la vista del panel de resultados

1. Haga clic en el icono del escritorio para iniciar el Administrador de instantánea StorSimple.

2. En el panel de **ámbito** , haga clic en cualquier nodo o expanda el nodo secundario de un elemento en el panel de **resultados** y, a continuación, haga clic en la opción de **vista** . 

3. Para agregar o quitar las columnas que aparecen en el panel de **resultados** , haga clic en **Agregar o quitar columnas**. Aparece el cuadro de diálogo **Agregar o quitar columnas** .

    ![Agregar o quitar columnas del panel de resultados](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Add_remove_columns.png) 

4. Complete el formulario de la siguiente manera:

    - Seleccione los elementos de la lista de columnas **disponibles** y haga clic en **Agregar** para agregarlos a la lista **columnas mostradas** . 

    - Haga clic en elementos de la lista de **columnas mostradas** y haga clic en **Quitar** para eliminarlo de la lista. 

    - Seleccione un elemento en la lista de columnas **mostradas** y haga clic en **Subir** o **Bajar** para mover el elemento hacia arriba o hacia abajo en la lista. 

    - Haga clic en **Restaurar valores predeterminados** para volver a la configuración predeterminada del panel de **resultados** . 

5. Cuando haya terminado con las selecciones, haga clic en **Aceptar**. 

#### <a name="to-change-the-console-window-view"></a>Para cambiar la vista de la ventana de consola

1. Haga clic en el icono del escritorio para iniciar el Administrador de instantánea StorSimple.

2. En el panel de **ámbito** , haga clic en cualquier nodo, haga clic en **vista**y, a continuación, haga clic en **Personalizar**. Aparece el cuadro de diálogo **Personalizar** .

    ![Personalizar la ventana de la consola](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Customize.png) 

3. Active o desactive las casillas de verificación para mostrar u ocultar elementos en la ventana de la consola. Cuando haya terminado con las selecciones, haga clic en **Aceptar**.

## <a name="new-window-from-here"></a>Nueva ventana desde aquí

Puede usar la opción de **Nueva ventana desde aquí** para abrir una nueva ventana de consola.

#### <a name="to-open-a-new-console-window"></a>Para abrir una nueva ventana de consola

1. Haga clic en el icono del escritorio para iniciar el Administrador de instantánea StorSimple.

2. En el panel de **ámbito** , haga clic en cualquier nodo y, a continuación, haga clic en **Nueva ventana desde aquí**. 

    Aparece una nueva ventana, que muestra solo el ámbito que seleccionó. Por ejemplo, si haga el nodo de **Directivas de copia de seguridad** , la nueva ventana Mostrar solo el nodo de **Directivas de copia de seguridad** en el panel de **ámbito** y una lista de directivas de copia de seguridad definidas en el panel de **resultados** . Vea el siguiente ejemplo.

    ![Nueva ventana desde aquí](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_NewWindow.png) 
 
## <a name="refresh"></a>Actualizar

Puede usar la acción **Actualizar** para actualizar la ventana de consola.

#### <a name="to-update-the-console-window"></a>Para actualizar la ventana de consola

1. Haga clic en el icono del escritorio para iniciar el Administrador de instantánea StorSimple.

2. En el panel de **ámbito** , haga clic en cualquier nodo o expanda el nodo secundario de un elemento en el panel de **resultados** y, a continuación, haga clic en **Actualizar**. 

## <a name="export-list"></a>Lista de exportación

Puede usar la acción **Exportar lista** para guardar una lista en un archivo de valores separados por comas (CSV). Por ejemplo, puede exportar la lista de directivas de copia de seguridad o el catálogo de copia de seguridad. A continuación, puede importar el archivo CSV en una aplicación de hoja de cálculo para el análisis.

#### <a name="to-save-a-list-in-a-comma-separated-value-csv-file"></a>Para guardar una lista en un archivo de valores separados por comas (CSV)

1. Haga clic en el icono del escritorio para iniciar el Administrador de instantánea StorSimple. 

2. En el panel de **ámbito** , haga clic en cualquier nodo o expanda el nodo secundario de un elemento en el panel de **resultados** y, a continuación, haga clic en **Exportar lista**. 

3. Aparece el cuadro de diálogo **Exportar lista** . Complete el formulario de la siguiente manera: 

    1. En el cuadro **nombre de archivo** , escriba un nombre para el archivo CSV o haga clic en la flecha para seleccionar en la lista desplegable.

    2. En el cuadro **Guardar como tipo** , haga clic en la flecha y seleccione un tipo de archivo de la lista desplegable.

    3. Para guardar sólo los elementos seleccionados, seleccione las filas y, a continuación, haga clic en la casilla de verificación **Guardar solo las filas seleccionadas** . Para guardar exportadas todas las listas, desactive la casilla de verificación **Guardar solo las filas seleccionadas** .

    4. Haga clic en **Guardar**.

    ![Exportar lista como un archivo de valores separados por comas](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Export_List.png) 
 
## <a name="help"></a>Ayuda

Puede usar el menú **Ayuda** para ver la Ayuda en línea disponible para el Administrador de instantáneas StorSimple y MMC.

#### <a name="to-view-available-online-help"></a>Para ver la Ayuda en línea disponible

1. Haga clic en el icono del escritorio para iniciar el Administrador de instantánea StorSimple.

2. En el panel de **ámbito** , haga clic en cualquier nodo o expanda el nodo secundario de un elemento en el panel de **resultados** y, a continuación, haga clic en **Ayuda**. 

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre la [interfaz de usuario de administrador de instantáneas StorSimple](storsimple-use-snapshot-manager.md).
- Más información sobre el [uso de administrador de instantáneas StorSimple para administrar la solución StorSimple](storsimple-snapshot-manager-admin.md).

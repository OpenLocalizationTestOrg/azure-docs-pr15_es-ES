<properties
    pageTitle="Importar datos en el equipo aprendizaje Studio desde otro ensayo | Microsoft Azure"
    description="Cómo guardar datos de formación en Azure máquina aprendizaje Studio y usarla en otro ensayo."
    keywords="importar datos, datos, orígenes de datos, datos de entrenamiento"
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="garye;bradsev" />


# <a name="import-your-data-into-azure-machine-learning-studio-from-another-experiment"></a>Importar los datos en Azure máquina aprendizaje Studio desde otro ensayo

[AZURE.INCLUDE [import-data-into-aml-studio-selector](../../includes/machine-learning-import-data-into-aml-studio.md)]


Habrá ocasiones al que desee llevar un resultado intermedio de un ensayo y usarlo como parte de otro ensayo.  Para ello, guarde el módulo como un conjunto de datos:

1. Haga clic en el resultado del módulo que desee guardar como un conjunto de datos.

2. Haga clic en **Guardar como conjunto de datos**.

3. Cuando se le pida, escriba un nombre y una descripción que le permitirá identificar el conjunto de datos con facilidad.

4. Haga clic en la marca de verificación **Aceptar** .

Cuando finalice la operación de guardar, el conjunto de datos estará disponible para su uso dentro de cualquier ensayo en el área de trabajo. Puede encontrarlo en la lista de **Conjuntos de datos guardado** en la paleta de módulo.


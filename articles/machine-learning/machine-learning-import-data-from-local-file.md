<properties
    pageTitle="Importar datos en el equipo aprendizaje Studio desde un archivo local | Microsoft Azure"
    description="Cómo importar los datos de formación Azure máquina aprendizaje Studio desde un archivo local."
    keywords="importar datos, formato de datos, tipos de datos, orígenes de datos, datos de entrenamiento"
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


# <a name="import-your-training-data-into-azure-machine-learning-studio-from-a-local-file"></a>Importar los datos de formación en Azure máquina aprendizaje Studio desde un archivo local

[AZURE.INCLUDE [import-data-into-aml-studio-selector](../../includes/machine-learning-import-data-into-aml-studio.md)]


Para usar sus propios datos en Studio de aprendizaje de equipo, puede cargar un archivo de datos con anterioridad desde el disco duro local para crear un módulo de conjunto de datos en el área de trabajo. 


## <a name="import-data-from-a-local-file"></a>Importar datos desde un archivo local

Puede importar datos desde un disco duro local haciendo lo siguiente:

1. Haga clic en **+ nuevo** en la parte inferior de la ventana de máquina aprendizaje Studio.
2. Seleccione el **conjunto de datos** y **archivo LOCAL de desde**.
3. En el cuadro de diálogo **cargar un nuevo conjunto de datos** , busque el archivo que desea cargar
4. Escriba un nombre, identificar el tipo de datos y, opcionalmente, escriba una descripción. Se recomienda una descripción: le permite grabar cualquier características acerca de los datos que desea recordar al utilizar los datos en el futuro.
5. La casilla de verificación **Esta es la nueva versión de un conjunto de datos existente** permite actualizar un conjunto de datos existente con los nuevos datos. Haga clic en esta casilla de verificación y, a continuación, escriba el nombre de un conjunto de datos existente.

Durante la carga, verá un mensaje que se está cargando el archivo. Cargar tiempo dependerá del tamaño de los datos y la velocidad de la conexión al servicio.
Si que tarda mucho tiempo en el archivo, puede hacer otras cosas dentro de máquina aprendizaje Studio mientras se espera. Sin embargo, cierre el explorador hará que la carga de datos no se realice correctamente.

Una vez que se carguen los datos, se almacena en un módulo de conjunto de datos y está disponible para cualquier ensayo en el área de trabajo.
Mientras edita un ensayo, puede encontrar los conjuntos de datos que ha creado en la lista **Mis conjuntos de datos** en la lista de **Conjuntos de datos guardado** en la paleta de módulo. Puede arrastrar y colocar el conjunto de datos en el lienzo de ensayo cuando desee utilizar el conjunto de datos para el análisis adicional y aprendizaje del equipo.




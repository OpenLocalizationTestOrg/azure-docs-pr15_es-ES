<properties
    pageTitle="Mover datos a y desde el almacenamiento de blobs de Windows Azure | Microsoft Azure"
    description="Mover datos a y desde el almacenamiento de blobs de Windows Azure"
    services="machine-learning,storage"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/14/2016"
    ms.author="bradsev;sachouks" />

# <a name="move-data-to-and-from-azure-blob-storage"></a>Mover datos a y desde el almacenamiento de blobs de Windows Azure

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]

Consejos sobre tecnologías utilizadas para mover datos a o desde el almacenamiento de blobs de Windows Azure están vinculadas a continuación:

[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]
 
Qué método es mejor para usted depende del escenario. El artículo [escenarios para análisis avanzado en Azure el aprendizaje](machine-learning-data-science-plan-sample-scenarios.md) ayude a determinar los recursos que necesita para una variedad de flujos de trabajo de ciencias de datos utilizados en el proceso de análisis avanzadas.

> [AZURE.NOTE] Para obtener una introducción completa con el almacenamiento de blobs de Windows Azure, consulte [Conceptos básicos de blobs de Windows Azure](../storage/storage-dotnet-how-to-use-blobs.md) y [Servicio de blobs de Windows Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).

Como alternativa, puede usar el [Generador de datos de Azure](https://azure.microsoft.com/services/data-factory/) para: 

- crear y programar una canalización que descarga datos desde el almacenamiento de blobs de Windows Azure, 
- pasar a un servicio web de aprendizaje del equipo de Azure publicado, 
- recibir los resultados del análisis predictiva, y 
- cargue los resultados al almacenamiento. 

Para obtener más información, vea [crear canalizaciones predictivas con el generador de datos de Azure y Azure el aprendizaje](../data-factory/data-factory-azure-ml-batch-execution-activity.md).

## <a name="prerequisites"></a>Requisitos previos

Este documento se supone que tiene una suscripción de Azure, una cuenta de almacenamiento y la clave de almacenamiento correspondiente para la cuenta. Antes de cargar o descargar datos, debe conocer la clave de nombre y la cuenta de la cuenta de almacenamiento de Azure.

- Para configurar una suscripción de Azure, vea la [versión de prueba gratuita de un mes](https://azure.microsoft.com/pricing/free-trial/).
- Para obtener instrucciones sobre cómo crear una cuenta de almacenamiento y para obtener información de cuenta y clave, vea [acerca de Azure cuentas de almacenamiento](../storage/storage-create-storage-account.md).

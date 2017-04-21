<properties 
    pageTitle="Operaciones comunes en la API de recomendaciones de aprendizaje de equipo | Microsoft Azure" 
    description="Aplicación de ejemplo de recomendación de Azure m" 
    services="machine-learning" 
    documentationCenter="" 
    authors="LuisCabrer" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/08/2016" 
    ms.author="luisca"/> 


# <a name="recommendations-api-sample-application-walkthrough"></a>Tutorial de aplicación de ejemplo de la API de recomendaciones

>[AZURE.NOTE]Debe comenzar a usar el servicio cognitivas API de recomendaciones en lugar de esta versión. El servicio de recomendaciones cognitivas se van a reemplazar este servicio y las nuevas características se desarrollará allí. Tiene nuevas capacidades como lotes de soporte técnico, una mejor API Explorer, una experiencia de suscripción y facturación de superficie, más coherente API más limpio, etcetera.
> Más información acerca de la [migración al nuevo servicio cognitivas](http://aka.ms/recomigrate)

##<a name="purpose"></a>Propósito

Este documento muestra el uso de la API de recomendaciones de aprendizaje de Azure máquina a través de una [aplicación de ejemplo](https://code.msdn.microsoft.com/Recommendations-144df403).

Esta aplicación no está pensada para incluir la funcionalidad completa ni utiliza todas las API. Muestran algunas operaciones comunes a realizar cuando desee reproducir con el servicio de recomendación de aprendizaje de equipo en primer lugar. 

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##<a name="introduction-to-machine-learning-recommendation-service"></a>Introducción al servicio de recomendación de aprendizaje de equipo

Recomendaciones a través del servicio de recomendación de aprendizaje del equipo están habilitadas si genera un modelo de recomendación basado en los datos siguientes:

* Un repositorio de los elementos que desea recomendar, también conocido como un catálogo
* Datos que representa el uso de elementos por el usuario o sesión (se adquirida con el tiempo a través de adquisición de datos, no como parte de la aplicación de ejemplo)

Una vez creado un modelo de recomendación, puede usarlo para predecir elementos que un usuario puede estar interesen en, según un conjunto de elementos (o un solo elemento) el usuario selecciona.

Para habilitar el escenario anterior, haga lo siguiente en el servicio de recomendación de aprendizaje:

* Crear un modelo: este es un contenedor lógico que contiene los datos (catálogo y uso) y los modelos de predicción. Cada contenedor de modelo se identifica mediante un identificador único, que se asigna cuando se crea. Este ID se denomina el identificador de modelo, y se utiliza por la mayoría de las API. 
* Cargar al catálogo: cuando se crea un contenedor de modelo, puede asociar a ella un catálogo.

**Nota**: crear un modelo y cargar a un catálogo normalmente se realizan una vez para el ciclo de vida del modelo.

* Cargar uso: añade datos de uso al contenedor de modelo.
* Cree un modelo de recomendación: después de que tiene suficiente datos, puede generar el modelo de recomendación. Esta operación utiliza los algoritmos de aprendizaje de máquina superiores para crear un modelo de recomendación. Cada compilación está asociada con un identificador único. Debe mantener un registro de este ID porque es necesario para la funcionalidad de algunas API.
* Supervisar el proceso de creación: una compilación de modelo de recomendación es una operación asincrónica y puede tardar desde varios minutos a varias horas, dependiendo de la cantidad de datos (catálogo y uso) y los parámetros de compilación. Por lo tanto, debe supervisar la compilación. Se crea un modelo de recomendación solo si su versión de compilación asociado se completa correctamente.
* (Opcional) Elija una versión de modelo de recomendación activa: este paso solo es necesario si tiene más de un modelo de recomendación que se basa en el contenedor de modelo. Cualquier solicitud para obtener recomendaciones sin que indica que el modelo de recomendación activo se redirige automáticamente por el sistema a la compilación activa de forma predeterminada. 

**Nota**: un modelo de recomendación active es producción listo y está diseñado para la carga de trabajo. Esto difiere de un modelo de recomendación no está activo, permanece en un entorno de prueba (a veces denominado ensayo).

* Obtener recomendaciones: después de que tiene un modelo de recomendación, se pueden activar recomendaciones para un solo elemento o una lista de elementos que seleccione. 

Normalmente se llamará obtener recomendaciones para un período de tiempo determinado. Durante ese período de tiempo, puede redirigir los datos de uso para el sistema de recomendación aprendizaje del equipo, que se agrega los datos al contenedor de modelo especificado. Cuando tenga suficientes datos de uso, puede crear un nuevo modelo de recomendación que incorpora los datos de uso adicionales. 

##<a name="prerequisites"></a>Requisitos previos

* 2013 de Visual Studio
* Acceso a Internet 
* Suscripción a las recomendaciones API (https://datamarket.azure.com/dataset/amla/recommendations).

##<a name="azure-machine-learning-sample-app-solution"></a>Solución de aplicación de ejemplo de aprendizaje de máquina Azure

Esta solución contiene el código fuente, el uso de muestra, el archivo de catálogo y el directivas para descargar los paquetes necesarios para la compilación.

##<a name="the-apis-used"></a>Las API utilizadas

La aplicación utiliza la funcionalidad de recomendación de aprendizaje de equipo a través de un subconjunto de API disponibles. Las siguientes API se muestran en la aplicación:

* Crear modelo: crear un contenedor lógico para mantener los modelos de datos y recomendación. Un modelo se identifica mediante un nombre y no puede crear más de un modelo con el mismo nombre.
* Cargar archivo del catálogo: usar para cargar los datos del catálogo.
* Cargar archivo de uso: usar para cargar los datos de uso.
* Desencadenar generar: usar para crear un modelo de recomendación.
* Supervisar la ejecución de la compilación: usar para supervisar el estado de compilación de modelo de recomendación.
* Elija un modelo integrado de recomendación: se utiliza para indicar qué modelo de recomendación para usar de manera predeterminada para una determinada contenedor del modelo. Este paso es necesario únicamente si tiene más de un modelo de recomendación y desea activar una versión no activo como el modelo de recomendación activa.
* Obtener recomendación: usar para recuperar elementos recomendados según un solo elemento determinado o un conjunto de elementos. 

Para obtener una descripción completa de la API, consulte la documentación de Microsoft Azure Marketplace. 

**Nota**: un modelo puede tener varias versiones en el tiempo (no simultáneamente). Cada compilación se crea con el mismo o un catálogo actualizado y los datos de uso adicionales.

## <a name="common-pitfalls"></a>Problemas comunes

* Debe proporcionar el nombre de usuario y la clave de cuenta principal de Microsoft Azure Marketplace para ejecutar la aplicación de ejemplo.
* Ejecute la aplicación de ejemplo de forma consecutiva producirá un error. El flujo de la aplicación incluye crear, cargar, crear al monitor y obtener recomendaciones de un modelo predefinido; por lo tanto, se producirá en ejecución consecutiva si no cambia el nombre del modelo entre las llamadas.
* Recomendaciones pueden devolver sin datos. La aplicación de ejemplo usa un archivo muy pequeño de catálogo y el uso. Por lo tanto, algunos elementos del catálogo no tendrá elementos recomendados.

## <a name="disclaimer"></a>Declinación de responsabilidades
La aplicación de ejemplo no está pensada para ejecutarse en un entorno de producción. Los datos proporcionados en el catálogo están muy pequeños y no proporcionará un modelo de recomendación significativo. Los datos se proporcionan como una demostración. 
 

<properties 
    pageTitle="Crear flujos de trabajo de codificación avanzadas con el Diseñador de flujo de trabajo | Microsoft Azure" 
    description="Obtenga información sobre cómo crear flujos de trabajo de codificación avanzadas con el Diseñador de flujo de trabajo." 
    services="media-services" 
    documentationCenter="" 
    authors="anilmur" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/15/2016"
    ms.author="juliako;johndeu;anilmur"/>


#<a name="create-advanced-encoding-workflows-with-workflow-designer"></a>Crear flujos de trabajo de codificación avanzadas con el Diseñador de flujo de trabajo

##<a name="overview"></a>Información general

El **Diseñador de flujo de trabajo** es una herramienta de escritorio de Windows que se utiliza para diseñar y crear flujos de trabajo personalizados para codificar con **El flujo de trabajo de Media Encoder Premium**.
Mediante el uso de la potencia de la herramienta de diseñador de flujo de trabajo, puede diseñar y crear flujos de trabajo complejos que se ejecutarán en **Media Encoder Premium**.  

Flujos de trabajo pueden incluir lógica de decisión de clientes y bifurcación según las propiedades del archivo de origen de entrada. Puede crear flujos de trabajo con propiedades puede reemplazables y valores dinámicos para facilitar la repita y personalizar en la nube incluso las tareas de codificación más complejas.

Flujos de trabajo de ejemplo que se pueden crear incluyen:

- Decisión en función de los flujos de trabajo que inspeccionar el contenido de origen para la resolución y codifican solamente las pistas de salida deseado.  Esto es helfpul eliminando las pistas desperdiciadas que podría generar ampliación de escala involuntariamente de contenido de origen.
- Varios archivos de entrada pueden usarse para admitir títulos, superposiciones y creación de contenido juntas. 

Esta herramienta también puede usarse para modificar cualquiera de nuestros [publicado flujos de trabajo](media-services-workflow-designer.md#existing_workflows). 

>[AZURE.NOTE]Para obtener una copia de la herramienta de diseñador de flujo de trabajo, póngase en contacto con mepd@microsoft.com.


Una vez que se crea un archivo de flujo de trabajo, se pueden cargar como un activo y, a continuación, se pueden usar para la codificación de archivos multimedia. Para obtener información sobre cómo codificar con **El flujo de trabajo de Media Encoder Premium** con **.NET**, vea [avanzada de codificación con el flujo de trabajo de Media Encoder Premium](media-services-encode-with-premium-workflow.md).

##<a id="existing_workflows"></a>Modificar los flujos de trabajo existentes

El predeterminado [publicado flujos de trabajo](media-services-workflow-designer.md#existing_workflows) pueden modificarse mediante la herramienta de diseñador. Puede obtener el valor predeterminado de archivos de flujo de trabajo [aquí](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows). La carpeta también contiene la descripción de estos archivos.

Los vídeos siguientes muestran cómo usar el diseñador.

###<a name="day-1--getting-started"></a>Día 1: Introducción

Vídeo de 1 día abarca:

- Información general de diseñador
- Flujos de trabajo básicas: "Hola a todos"
- Crear varias archivos MP4 para su uso con Azure Media Services transmisión de salida

> [AZURE.VIDEO azure-premium-encoder-workflow-designer-training-videos-day-1]

###<a name="day-2"></a>Día 2

Día 2 vídeo abarca:

- Diferentes escenarios de archivo de origen: controlar el audio
- Flujos de trabajo con lógica avanzada
- Etapas del gráfico

> [AZURE.VIDEO azure-premium-encoder-workflow-designer-training-videos-day-2]

###<a name="day-3"></a>Día 3

Vídeo de día 3 abarca:

- Dentro de los flujos de trabajo y planos de secuencias de comandos
- Restricciones con el codificador actual
- Preguntas y respuestas
 
> [AZURE.VIDEO azure-premium-encoder-workflow-designer-training-videos-day-3]


## <a name="next-step"></a>Siguiente paso

Revise las rutas de aprendizaje de Media Services.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


Si necesita soporte técnico o tiene preguntas sobre cómo crear flujos de trabajo personalizados en la herramienta de diseñador de flujo de trabajo, envíe un correo electrónico a mepd@microsoft.com.

##<a name="see-also"></a>Vea también

[Vídeos de aprendizaje de diseñador de flujo de trabajo de codificador de Azure Premium](http://johndeutscher.com/2015/07/06/azure-premium-encoder-workflow-designer-training-videos/)

<properties 
    pageTitle="Cómo usar Blitline para imagen procesamiento - Azure Guía de funciones" 
    description="Obtenga información sobre cómo usar el servicio Blitline para procesar imágenes dentro de una aplicación de Azure." 
    services="" 
    documentationCenter=".net" 
    authors="blitline-dev" 
    manager="jason@blitline.com" 
    editor="jason@blitline.com"/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/09/2014" 
    ms.author="support@blitline.com"/>
# <a name="how-to-use-blitline-with-azure-and-azure-storage"></a>Cómo usar Blitline con Azure y el almacenamiento de Azure

Esta guía explica cómo acceder a los servicios de Blitline y cómo enviar trabajos a Blitline.

## <a name="what-is-blitline"></a>¿Qué es Blitline?

Blitline es una imagen en la nube procesamiento de servicio que proporciona el procesamiento de imágenes de nivel de empresa a una fracción del precio que costaría para generar por sí mismo.

La realidad es que el procesamiento de imágenes se ha realizado una y otra vez, normalmente reconstruir desde el sitio Web de cada pensando. Sabemos esto porque hemos creado una millones de veces demasiado. Un día, que hemos decidido que quizás es el momento que simplemente hacerlo para todos los usuarios. Sabemos cómo hacerlo, para hacerlo de manera rápida y eficaz y guardar todos los usuarios trabajo mientras tanto.

Para obtener más información, consulte [http://www.blitline.com](http://www.blitline.com).

## <a name="what-blitline-is-not"></a>¿Qué Blitline no es...

Para aclarar lo que es útil para Blitline, a menudo es más fácil de identificar qué Blitline no hace antes de continuar.

- Blitline no tiene widgets HTML para cargar imágenes. Debe tener imágenes disponibles públicamente o con permisos restringidos para Blitline alcance.

- Blitline no admite live procesamiento de imagen, como Aviary.com

- Blitline no acepta las cargas de imagen, no puede insertar las imágenes a Blitline directamente. Debe insertarlos para el almacenamiento de Azure u otros lugares Blitline es compatible con y, a continuación, indique a Blitline dónde conseguirlos.

- Blitline es masivos en paralelo y realice cualquier procesamiento sincrónico. Lo que significa que debe enviar una postback_url y podemos informarle cuando se han terminado procesamiento.

## <a name="create-a-blitline-account"></a>Crear una cuenta de Blitline

[AZURE.INCLUDE [blitline-signup](../includes/blitline-signup.md)]

## <a name="how-to-create-a-blitline-job"></a>Cómo crear un trabajo Blitline

Blitline usa JSON para definir las acciones que desea tomar una imagen. Este JSON está compuesto por algunos campos simples.

El ejemplo más sencillo es la siguiente:

        json : '{
       "application_id": "MY_APP_ID",
       "src" : "http://cdn.blitline.com/filters/boys.jpeg",
       "functions" : [ {
           "name": "resize_to_fit",
           "params" : { "width": 240, "height": 140 },
           "save" : { "image_identifier" : "external_sample_1" }
       } ]
    }'

Aquí tenemos JSON que lo llevarán a una imagen de "origen" "... boys.jpeg" y, a continuación, cambiar el tamaño de imagen a 240 x 140.

El identificador de aplicación es algo que puede encontrar en la pestaña **Información de conexión** o **Administrar** en Azure. Es el identificador de secreto que le permite ejecutar trabajos en Blitline.

El parámetro de "Guardar" identifica información sobre dónde desea colocar la imagen, una vez que lo hemos procesado. En este caso trivial, no hemos definido uno. Si no se ha definido ninguna ubicación Blitline almacenará localmente (y temporalmente) en una ubicación de la nube únicos. Podrá obtener esa ubicación de la JSON devuelto por Blitline cuando realiza la Blitline. El identificador de "imagen" es necesario y se le devuelve al identificar este en particular guardar la imagen.

Puede encontrar más información sobre las *funciones* se admiten aquí: <http://www.blitline.com/docs/functions>

También puede encontrar documentación acerca de las opciones de trabajo aquí: <http://www.blitline.com/docs/api>

Una vez que tenga su JSON todo lo que debe hacer es **publicación** para`http://api.blitline.com/job`

Obtendrá JSON volver que tiene un aspecto similar a este:

    {
     "results":
         {"images":
            [{
              "image_identifier":"external_sample_1",
              "s3_url":"https://s3.amazonaws.com/dev.blitline/2011110722/YOUR_APP_ID/CK3f0xBF_2bV6wf7gEZE8w.jpg"
            }],
          "job_id":"4eb8c9f72a50ee2a9900002f"
         }
    }


Esto le indica que Blitline ha recibido su solicitud ha guardado en una cola de procesamiento y cuando haya completado la imagen estará disponible en: **https://s3.amazonaws.com/dev.blitline/2011110722/YOUR\_aplicación\_ID/CK3f0xBF_2bV6wf7gEZE8w.jpg**

## <a name="how-to-save-an-image-to-your-azure-storage-account"></a>Cómo guardar una imagen a su cuenta de almacenamiento de Azure

Si tiene una cuenta de almacenamiento de Azure, puede tener fácilmente Blitline push las imágenes procesadas en el contenedor de Azure. Agregando una "azure_destination" se definen la ubicación y permisos para Blitline vincular a.

Aquí tenemos un ejemplo:

    job : '{
      "application_id": "YOUR_APP_ID",
      "src" : "http://www.google.com/logos/2011/houdini11-hp.jpg",
         "functions" : [{
         "name": "blur",
         "save" : {
             "image_identifier" : "YOUR_IMAGE_IDENTIFIER",
             "azure_destination" : {
                 "account_name" : "YOUR_AZURE_CONTAINER_NAME",
                 "shared_access_signature" : "SAS_THAT_GIVES_BLITLINE_PERMISSION_TO_WRITE_THIS_OBJECT_TO_CONTAINER",
               }
           }
         }]
       }'


Al rellenar los valores CAPITALIZED con su propio, puede enviar esta JSON a http://api.blitline.com/job y se procesan con un filtro de desenfoque la imagen de "origen" y, a continuación, se inserta al destino de Azure.

###<a name="please-note"></a>Tenga en cuenta:

Las asociaciones de seguridad deben contener la dirección url SA completa, incluido el nombre de archivo del archivo de destino.

Ejemplo:

    http://blitline.blob.core.windows.net/sample/image.jpg?sr=b&sv=2012-02-12&st=2013-04-12T03%3A18%3A30Z&se=2013-04-12T04%3A18%3A30Z&sp=w&sig=Bte2hkkbwTT2sqlkkKLop2asByrE0sIfeesOwj7jNA5o%3D


También puede leer la última edición de documentos de Azure almacenamiento de Blitline [aquí](http://www.blitline.com/docs/azure_storage).


## <a name="next-steps"></a>Pasos siguientes

Visite blitline.com para obtener información acerca de todas las nuestras características:

* Extremo de la API de Blitline documentos <http://www.blitline.com/docs/api>
* Funciones de Blitline API <http://www.blitline.com/docs/functions>
* Ejemplos de Blitline API <http://www.blitline.com/docs/examples>
* Tercera parte de la biblioteca de Nuget <http://nuget.org/packages/Blitline.Net>

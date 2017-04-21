<properties
    pageTitle="Extraer datos públicos en Azure evento Hubs | Microsoft Azure"
    description="Importar información general de los centros de evento de ejemplo de web"
    services="event-hubs"
    documentationCenter="na"
    authors="spyrossak"
    manager="timlt"
    editor=""/>

<tags 
    ms.service="event-hubs"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="08/25/2016"
    ms.author="spyros;sethm" />

# <a name="pulling-public-data-into-azure-event-hubs"></a>Extraer datos públicos en Azure evento Hubs

En escenarios típicos de Internet de cosas (IoT), tiene dispositivos que se pueden programar para introducir datos en Azure, bien a un concentrador de evento de Azure o un concentrador IoT. Ambas esos hubs son puntos de entrada en Azure para almacenar, analizar y visualizar con una gran variedad de herramientas que están disponibles en Microsoft Azure. Sin embargo, ambas requieren inserta los datos en ellos, con formato JSON y protegido de forma específica. Esto incorpora la pregunta siguiente. ¿Qué hace si desea incorporar datos de orígenes de públicos o privados, donde los datos se exponen como un servicio web o la fuente de algún tipo, pero no tiene la posibilidad de cambiar cómo se publican los datos? Tenga en cuenta el tiempo o el tráfico o cotizaciones - no indica NOAA, WSDOT o NASDAQ a configurar una instalación remota en su centro de evento. Para solucionar este problema, hemos escrito y un ejemplo de nube pequeña que se puede modificar e implementar que extraer los datos de algunas de estas fuentes y enviarlo a su centro de evento de Abrir origen. Desde allí, puede hacer lo que quiera con él, asunto, por supuesto, con los términos de licencia del productor. Puede encontrar la aplicación [aquí](https://azure.microsoft.com/documentation/samples/event-hubs-dotnet-importfromweb/).

Tenga en cuenta que el código de este ejemplo solo muestra cómo extraer datos de web típica fuentes y cómo escribir a un concentrador de evento de Azure. Esto no está pensado para ser una aplicación de producción y se han realizado ningún intento para que sea adecuado para su uso en un entorno: strictfly una y MANUALIDADES, centrados programador ejemplo solo. Además, la existencia de este ejemplo no es equivale a una recomendación que debería **extraer** datos en Azure en lugar de **inserción** . Debería revisar la seguridad, rendimiento, funcionalidad y costo factores antes de establecerse en una arquitectura de llevar a cabo.

## <a name="application-structure"></a>Estructura de la aplicación

La aplicación está escrita en C# y la [Descripción de ejemplo](https://azure.microsoft.com/documentation/samples/event-hubs-dotnet-importfromweb/) contiene toda la información que necesita modificar, crear y publicar la aplicación. Las siguientes secciones proporcionan una descripción general de las funciones de la aplicación.

Empezaremos con la suposición de que tiene acceso a una fuente de datos. Por ejemplo, que desea extraer los datos de tráfico desde el departamento de estado de Washington de transporte o los datos del tiempo de NOAA, para mostrar los informes personalizados o para combinar con los otros datos en la aplicación. También necesitará ha configurado un concentrador de evento de Azure y sabe la conexión de cadena que es necesario tener acceso a ella.

Cuando se inicia la solución GenericWebToEH, lee un archivo de configuración (App.config) para obtener una serie de cosas:

1. La dirección URL o una lista de direcciones URL, para el sitio de publicación de los datos. Lo ideal es un sitio que publica datos JSON, como los que se hace referencia a WSDOT [aquí](http://www.wsdot.wa.gov/Traffic/api/). 
2. Credenciales para la dirección URL, si es necesario. Muchos orígenes públicos no necesitan credenciales, o puede incluir las credenciales en la cadena de URL. Otras requieren proporcionados por separado. (Tenga en cuenta que solo puede especificar un conjunto de credenciales en esta aplicación, por lo que solo funcionará si especifica solo una dirección URL, no una lista de direcciones URL).
3. La cadena de conexión y el nombre del evento concentrador en ese espacio Hubs de evento, al que va a instalar los datos. Puede encontrar esta información en el portal de Azure.
4. Un intervalo de espera, en milisegundos, para el intervalo entre el sitio de datos públicos de sondeo. Esta configuración requiere algunas consideraciones. Si sondeos poca, puede que pierda datos; por otro lado, si sondeos con demasiada frecuencia, puede obtener una gran cantidad de datos repetitivos o peor aún, puede estar bloqueado como un componente de malintencionadas. Tenga en cuenta la frecuencia con la que se actualiza el origen de datos - datos meteorológica o tráfico pueden actualizarse cada 15 minutos, pero cotizaciones quizás cada pocos segundos, dependiendo de dónde conseguirlos. 
5. Una marca para indicar a la aplicación si los datos proceden JSON o XML. Puesto que necesita insertar los datos a un concentrador de evento, la aplicación tiene un módulo de convertir XML en JSON antes de enviar.

Después de leer el archivo de configuración, la aplicación entra en un bucle - acceso al sitio web público, convertir los datos si es necesario, escritura a su centro de evento y a continuación, esperando el intervalo de suspensión antes de hacerlo nuevamente. Específicamente:

  * Leer el sitio Web público. Para recibir listo para enviar datos se utiliza la instancia de clase de RawXMLWithHeaderToJsonReader desde Azure/GenericWebToEH/ApiReaders/RawXMLWithHeaderToJsonReader.cs. Lee secuencia de origen en el método GetData() y, a continuación, divide en partes más pequeñas (es decir, registros) mediante GetXmlFromOriginalText. 
  Este método leerá XML, así como correcto JSON o JSON de matriz. Procesar después se inicia con la configuración de MergeToXML de App.config (predeterminado = vacío).
  * Los datos envío y recepción se implementan como un bucle en el método Process() Program.cs. 
  Después de recibir resultados de GetData(), el método pone en cola valores separados por el concentrador de eventos.

## <a name="next-steps"></a>Pasos siguientes

Para implementar la solución, clonar o descargar la aplicación [GenericWebToEH](https://azure.microsoft.com/documentation/samples/event-hubs-dotnet-importfromweb/) , modifique el archivo App.config, generar y finalmente publicarla. Una vez que haya publicado la aplicación, puede ver que se ejecuta en el portal de Azure clásico en servicios de nube y puede cambiar algunas de las opciones de configuración (por ejemplo, el destino del concentrador de evento y el intervalo de suspensión) en la ficha **Configurar** .

Ver más muestras de evento Hubs en [MSDN](https://code.msdn.microsoft.com/site/search?query=event%20hubs&f%5B0%5D.Value=event%20hubs&f%5B0%5D.Type=SearchText&ac=5)y en la [Galería de ejemplos de Azure](https://azure.microsoft.com/documentation/samples/?service=event-hubs) .

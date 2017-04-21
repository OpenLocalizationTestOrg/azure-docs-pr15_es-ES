<properties 
    pageTitle="Crear filtros con los servicios multimedia de Azure .NET SDK" 
    description="Este tema describe cómo crear filtros para que el cliente puede usarlos para determinadas secciones de un flujo de secuencia. Media Services crea manifiestos dinámicos para lograr esta transmisión selectivo." 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="ne" 
    ms.topic="article" 
    ms.date="07/18/2016"
    ms.author="juliako;cenkdin"/>


#<a name="creating-filters-with-azure-media-services-net-sdk"></a>Crear filtros con los servicios multimedia de Azure .NET SDK

> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-dynamic-manifest.md)
- [REST](media-services-rest-dynamic-manifest.md)

A partir de la versión 2.11, Media Services le permite definir filtros para los activos. Éstos son reglas del lado servidor que permiten a los clientes elegir hacer cosas como: reproducción solo una sección de un vídeo (en lugar de reproducir el vídeo completo), o especifique únicamente un subconjunto de representaciones de audio y vídeo dispositivo del cliente puede controlar (en lugar de todas las copias que están asociadas al activo). Este filtrado de los activos se logra mediante s **Dinámico manifiestos**que se crean tras la solicitud del cliente para transmitir un vídeo en función de los filtros especificados.

Para obtener más información relacionada con manifiestos dinámico y filtros, consulte [información general de manifiestos dinámico](media-services-dynamic-manifest-overview.md).

Este tema muestra cómo usar .NET SDK de Media Services para crear, actualizar y eliminar filtros. 


Nota que si actualiza un filtro, puede tardar hasta 2 minutos para la transmisión de extremo para actualizar las reglas. Si el contenido se ha servido con este filtro (y caché en servidores proxy y CDN en caché), actualizar este filtro puede provocar errores del Reproductor. Es recomendable borrar la caché después de actualizar el filtro. Si esta opción no es posible, considere la posibilidad de usar un filtro diferente. 

##<a name="types-used-to-create-filters"></a>Tipos usados para crear filtros

Al crear filtros, se usan los siguientes tipos: 

- **IStreamingFilter**.  Este tipo se basa en el siguiente de la API de REST [filtro](http://msdn.microsoft.com/library/azure/mt149056.aspx)
- **IStreamingAssetFilter**. Este tipo se basa en la API de REST siguiente [AssetFilter](http://msdn.microsoft.com/library/azure/mt149053.aspx)
- **PresentationTimeRange**. Este tipo se basa en la API de REST siguiente [PresentationTimeRange](http://msdn.microsoft.com/library/azure/mt149052.aspx)
- **FilterTrackSelectStatement** y **IFilterTrackPropertyCondition**. Estos tipos se basan en las siguientes API de REST [FilterTrackSelect y FilterTrackPropertyCondition](http://msdn.microsoft.com/library/azure/mt149055.aspx)


##<a name="createupdatereaddelete-global-filters"></a>Crear, actualizar o lectura/eliminar los filtros globales

El código siguiente muestra cómo usar .NET para crear, actualizar, lea y eliminar filtros de activos.
    
    string filterName = "GlobalFilter_" + Guid.NewGuid().ToString();
                
    List<FilterTrackSelectStatement> filterTrackSelectStatements = new List<FilterTrackSelectStatement>();
    
    FilterTrackSelectStatement filterTrackSelectStatement = new FilterTrackSelectStatement();
    filterTrackSelectStatement.PropertyConditions = new List<IFilterTrackPropertyCondition>();
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackNameCondition("Track Name", FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackBitrateRangeCondition(new FilterTrackBitrateRange(0, 1), FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackTypeCondition(FilterTrackType.Audio, FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatements.Add(filterTrackSelectStatement);
    
    // Create
    IStreamingFilter filter = _context.Filters.Create(filterName, new PresentationTimeRange(), filterTrackSelectStatements);
    
    // Update
    filter.PresentationTimeRange = new PresentationTimeRange(timescale: 500);
    filter.Update();
    
    // Read
    var filterUpdated = _context.Filters.FirstOrDefault();
    Console.WriteLine(filterUpdated.Name);

    // Delete
    filter.Delete();


##<a name="createupdatereaddelete-asset-filters"></a>Filtros de crear, actualizar o lectura/eliminar activos

El código siguiente muestra cómo usar .NET para crear, actualizar, lea y eliminar filtros de activos.

    
    string assetName = "AssetFilter_" + Guid.NewGuid().ToString();
    var asset = _context.Assets.Create(assetName, AssetCreationOptions.None);
    
    string filterName = "AssetFilter_" + Guid.NewGuid().ToString();
    
        
    // Create
    IStreamingAssetFilter filter = asset.AssetFilters.Create(filterName,
                                        new PresentationTimeRange(), 
                                        new List<FilterTrackSelectStatement>());
    
    // Update
    filter.PresentationTimeRange = 
            new PresentationTimeRange(start: 6000000000, end: 72000000000);
    
    filter.Update();
    
    // Read
    asset = _context.Assets.Where(c => c.Id == asset.Id).FirstOrDefault();
    var filterUpdated = asset.AssetFilters.FirstOrDefault();
    Console.WriteLine(filterUpdated.Name);
    
    // Delete
    filterUpdated.Delete();
    



##<a name="build-streaming-urls-that-use-filters"></a>Generar direcciones URL que use filtros de transmisión

Para obtener información sobre cómo publicar y entregar a los activos, vea [Ofrecer contenido a información general de los clientes](media-services-deliver-content-overview.md).


Los siguientes ejemplos muestra cómo agregar filtros a las URL streaming.

**MPEG GUIÓN** 

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf, filter=MyFilter)

**Apple HTTP Live Streaming V4 (HLS)**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl, filter=MyFilter)

**Apple HTTP Live Streaming V3 (HLS)**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3, filter=MyFilter)

**Smooth Streaming**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyFilter)


**HDS**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=f4m-f4f, filter=MyFilter)


##<a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="see-also"></a>Vea también 

[Información general de manifiestos dinámico](media-services-dynamic-manifest-overview.md)
 


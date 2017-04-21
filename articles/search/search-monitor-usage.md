<properties 
   pageTitle="Supervisar el uso y estadísticas en un servicio de búsqueda de Azure | Microsoft Azure | Servicio de nube hospedado de búsqueda" 
   description="Realizar un seguimiento de tamaño de índice y consumo de recursos para la búsqueda de Azure, un servicio de búsqueda de nube hospedado en Microsoft Azure." 
   services="search" 
   documentationCenter="" 
   authors="HeidiSteen" 
   manager="jhubbard" 
   editor=""
   tags="azure-portal"/>

<tags
   ms.service="search"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required" 
   ms.date="05/17/2016"
   ms.author="heidist"/>

# <a name="monitor-usage-and-statistics-in-an-azure-search-service"></a>Supervisar el uso y estadísticas en un servicio de búsqueda de Azure

Seguimiento el crecimiento de índices y el tamaño de documento puede ayudarle a ajustar la capacidad antes de alcanzar el límite superior que ha establecido para su servicio de manera proactiva. 

Para supervisar el uso de recursos, los recuentos y las estadísticas de su servicio se ven fácilmente en el [Portal de Azure](https://portal.azure.com), pero también puede obtener la información mediante programación si está creando una herramienta de administración de servicio personalizado. En este artículo se trata los pasos para ambos métodos.

También puede usar la nueva característica de análisis de tráfico de búsqueda para investigar actividad en el nivel de índice. Visite el [Análisis de tráfico de búsqueda de búsqueda de Azure](search-traffic-analytics.md) para empezar.

##<a name="view-counts-and-metrics-in-the-portal"></a>Ver recuentos y mediciones en el portal 

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com). 

2. Abra el panel de servicio del servicio de búsqueda de Azure. Mosaicos para el servicio se pueden encontrar en la página de inicio o puede ir al servicio de examinar, en la JumpBar. Para obtener instrucciones detalladas, vea [crear un servicio](search-create-service-portal.md) .

La sección uso incluye un indicador que indica qué parte de los recursos disponibles que hay actualmente en uso.

  ![][1]

Recuerde que el servicio compartido tiene un máximo de una réplica y dividir cada uno. Además, sólo puede admitir 10.000 documentos en total o 50 MB de datos, lo que suceda primero.

##<a name="get-index-statistics-using-the-rest-api"></a>Obtener las estadísticas de índice con la API de REST

La API de REST de búsqueda de Azure y .NET SDK proporcionan acceso mediante programación a las mediciones de servicio.  Si está utilizando [indizadores](https://msdn.microsoft.com/library/azure/dn946891.aspx) para cargar un índice de base de datos de SQL Azure o DocumentDB, una API adicional está disponible para obtener los números que requiere. 

  + [Obtener las estadísticas de índice](https://msdn.microsoft.com/library/azure/dn798942.aspx)
  + [Recuento de documentos](https://msdn.microsoft.com/library/azure/dn798924.aspx)
  + [Obtener estado del indizador](https://msdn.microsoft.com/library/azure/dn946884.aspx)

## <a name="next-steps"></a>Pasos siguientes

Revise [los límites y la capacidad](search-limits-quotas-capacity.md) para determinar la combinación de particiones y réplicas que deberá si no es suficiente capacidad existente. 

Para obtener más información sobre la administración del servicio, visite [administrar el servicio de búsqueda en Microsoft Azure](search-manage.md) .

<!--Image references-->
[1]: ./media/search-monitor-usage/AzureSearch-Monitor1.PNG




 

<properties
    pageTitle="Prácticas recomendadas para el servicio de aplicación de Azure"
    description="Obtenga información sobre las prácticas recomendadas y solución de problemas de servicio de aplicaciones de Azure."
    services="app-service"
    documentationCenter=""
    authors="dariagrigoriu"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/30/2016"
    ms.author="dariagrigoriu"/>
    
# <a name="best-practices-for-azure-app-service"></a>Prácticas recomendadas para el servicio de aplicación de Azure

En este artículo se resume los procedimientos recomendados para utilizar el [Servicio de aplicación de Azure](http://go.microsoft.com/fwlink/?LinkId=529714). 

## <a name="colocation"></a>Ubicación cooperativa
Cuando los recursos Azure redactar una solución como una aplicación web y una base de datos están ubicados en diferentes regiones los efectos pueden incluir lo siguiente:

*  Mayor latencia de comunicación entre recursos
*  Cargos monetarios para datos salientes transferir entre región tal como se indica en la [página de precios de Azure](https://azure.microsoft.com/pricing/details/data-transfers).

Ubicación cooperativa en la misma región es mejor para recursos de Azure redactar una solución como una aplicación web y una cuenta de base de datos o almacenamiento utilizado para mantener el contenido o los datos. Al crear recursos que debe asegurarse de que están en la misma región Azure a menos que tiene una empresa específica o razón para que no sean de diseño. Puede mover una aplicación de servicio de aplicación a la misma región como la base de datos al aprovechar la [aplicación de servicio clonar característica](app-service-web-app-cloning-portal.md) están disponibles para aplicaciones de Plan de servicio de aplicación Premium.   

## <a name="memoryresources"></a>Cuando aplicaciones consuman más memoria de lo esperado
Cuando se observa una aplicación consume más memoria de lo esperado como se indica a través de supervisión o un servicio de recomendaciones, tenga en cuenta la [característica de aplicación de servicio de resolución de problemas](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites). Una de las opciones de la característica de resolución de problemas dura acciones personalizadas en función de un umbral de memoria. Acciones que abarque el espectro de notificaciones de correo electrónico de la investigación a través de descarga de memoria a mitigación en lugar por el proceso de trabajo de reciclaje. Resolución de problemas se puede configurar a través de web.config y una interfaz de usuario descriptivo, como se describe en esta entrada de blog para la [Aplicación de servicio de soporte técnico sitio extensión](https://azure.microsoft.com/blog/additional-updates-to-support-site-extension-for-azure-app-service-web-apps).   

## <a name="CPUresources"></a>Cuando aplicaciones consumen más CPU de lo esperado
Cuando se observe que una aplicación consume más CPU de lo esperado o experiencias repite picos de CPU como se indica a través de supervisión o un servicio de recomendaciones considere vertical u horizontal del plan de servicio de aplicación. Si su aplicación es estado, escalado es la única opción, mientras que si la aplicación está fuera de la escala, sin estado dará más flexibilidad y mayor potencial de escala. 

Para obtener más información acerca de las aplicaciones "sin estado" de "estado" vs puede ver este vídeo: [Planear una aplicación de varios niveles Scalable llevar a cabo en la aplicación Web de Microsoft Azure](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2014/DEV-B414#fbid=?hashlink=fbid). Para obtener más información acerca de las opciones de ajuste de escala y ajuste automático de servicio de aplicaciones: [escala una aplicación Web de servicio de aplicaciones de Azure](web-sites-scale.md).  

## <a name="socketresources"></a>Cuando se agoten los recursos de socket
Una razón habitual para agote las conexiones salientes de TCP es el uso de bibliotecas de cliente que no se ha implementado reutilizar las conexiones TCP o en el caso de un protocolo de nivel superior como HTTP - de mantenimiento que se usan en no. Revise la documentación de cada una de las bibliotecas que se hace referencia a las aplicaciones de su Plan de servicio de la aplicación para asegurarse de que se configura o acceder a ellas en el código para su reutilización eficaz de conexiones salientes. También, siga las instrucciones de la documentación de biblioteca para NOMPROPIO creación y lanzamiento o Liberador de espacio para evitar la pérdida de conexiones. Mientras tales investigaciones de bibliotecas de cliente de impacto de progreso puede eliminarse mediante el escalado a varias instancias.  

## <a name="appbackup"></a>Cuando la aplicación de copia de seguridad que no se inicia
Las dos razones más comunes ¿por qué correctamente una copia de seguridad de aplicación: configuración de almacenamiento no válidos y la configuración de la base de datos no válidos. Estos errores suele ocurrir cuando hay cambios a los recursos de almacenamiento o base de datos, o para el acceso a estos recursos (por ejemplo, credenciales de actualización para la base de datos seleccionada en la configuración de copia de seguridad). Normalmente, las copias de seguridad ejecuten en una programación y requieren acceso a almacenamiento (para generar la copia de seguridad de los archivos) y las bases de datos (para copiar y leer el contenido que desea incluir en la copia de seguridad). El resultado de no tener acceso a cualquiera de estos recursos sería coherente error de copia de seguridad. 

Cuando se producen errores de copia de seguridad, revise los resultados más recientes para entender qué tipo de error sucede. En el caso de errores de acceso de almacenamiento, revise y actualice la configuración de almacenamiento utilizada en la configuración de copia de seguridad. En el caso de errores de acceso de la base de datos, revise y actualice las cadenas de conexiones como parte de la configuración de la aplicación; a continuación, vaya al actualizar la configuración de copia de seguridad para incluir correctamente las bases de datos necesarios. Para obtener más información sobre la copia de seguridad de aplicación, consulte la documentación de [realizar copias de seguridad de una aplicación web de servicio de aplicaciones de Azure](web-sites-backup.md) .

## <a name="nodejs"></a>Cuando se implementan nuevas aplicaciones Node.js al servicio de la aplicación de Azure
Azure configuración predeterminada de servicio de aplicación para las aplicaciones de Node.js está diseñada para satisfacer mejor las necesidades de las aplicaciones más comunes. Si la configuración de la aplicación Node.js ¿beneficiarse de ajuste personalizada para mejorar el rendimiento u optimizar el uso de recursos para los recursos de CPU o memoria de red, puede revisar nuestras prácticas recomendadas y pasos para solucionar problemas. En este artículo de la documentación describe la configuración de iisnode que tenga que configurar la aplicación de Node.js, se describen los escenarios distintos o problemas que la aplicación que se enfrenta y muestra cómo resolver estos problemas: [mejores prácticas y la Guía de solución de problemas de aplicaciones de nodo de servicio de aplicaciones de Azure](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md).   



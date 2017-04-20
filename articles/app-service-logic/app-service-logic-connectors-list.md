<properties
    pageTitle="Lista de disponibles conectores y aplicaciones de la API | Servicio de aplicaciones de Microsoft Azure"
    description="Obtenga información sobre los conectores y las API de aplicaciones de servicio de aplicaciones de Azure"
    services="logic-apps"
    documentationCenter=""
    authors="MandiOhlinger"
    manager="erikre"
    editor="cgronlun"/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/01/2016"
    ms.author="mandia"/>


# <a name="list-of-connectors-and-api-apps-to-use-in-your-logic-apps"></a>Lista de conectores y aplicaciones de la API para usar en las aplicaciones de lógica
>[AZURE.NOTE] Esta versión de este artículo se aplica a la versión del esquema de 2014 12-01-vista previa de aplicaciones de lógica. En la versión de lógica aplicaciones General disponibilidad (GA), vea la [Nueva lista de conectores](../connectors/apis-list.md).

Obtenga información sobre todas las aplicaciones de la API creado por Microsoft para usar en las aplicaciones de lógica y conectores disponibles.

Para información de precios y una lista de lo que se incluye con cada nivel de servicio, vea [Tarifas de servicio de la aplicación de Azure](https://azure.microsoft.com/pricing/details/app-service/).

> [AZURE.NOTE] Para empezar con aplicaciones de lógica antes de suscribirse para una cuenta de Azure, vaya a [Probar la aplicación de lógica](https://tryappservice.azure.com/?appservice=logic). Inmediatamente, puede crear una aplicación de la lógica de corta duración starter en la aplicación de servicio. Sin tarjetas de crédito obligatoria; sin compromisos.

## <a name="core-connectors"></a>Conectores principales
La siguiente tabla enumeran todos los conectores disponibles y aplicaciones de API creado por Microsoft que están disponibles como conectores principales:

Nombre | Descripción
--- | ---
[Bing Translator](https://azure.microsoft.com/marketplace/partners/bing/microsofttranslator/) | Usar a Bing para traducir texto en otro idioma.
[HTTP](app-service-logic-connector-http.md) | Escucha HTTP abre un extremo que actúa como un servidor HTTP y escucha a las solicitudes entrantes de HTTP o HTTPS. La acción HTTP no requiere un App API y es compatible de forma nativa dentro de la lógica de aplicaciones.
[Margen de demora](app-service-logic-connector-slack.md) | Conectar con el margen de demora y enviar mensajes a canales de margen de demora.


## <a name="enterprise-integration-connectors"></a>Conectores de integración de Enterprise
La siguiente tabla enumeran todos los conectores y las aplicaciones de la API creado por Microsoft disponible como conectores de integración de Enterprise disponibles:

Nombre  | Descripción
------------- | -------------
[Reglas de BizTalk](app-service-logic-use-biztalk-rules.md) | Usar reglas de BizTalk para definir y controlar la lógica empresarial de una organización. Directivas de empresa pueden actualizarse sin volver a compilar o sin volver a implementar las aplicaciones asociadas.
[Extracción de BizTalk XPath](app-service-logic-xpath-extract.md) | Busca y extrae los datos del contenido XML basado en una expresión XPath que elija.
[Conector de DB2](app-service-logic-connector-db2.md) | Se conecta a un IBM DB2 de base de datos local y en una máquina virtual Azure ejecuta un sistema operativo Windows. Puede asignar las operaciones de Web API y API de OData a los comandos de lenguaje de consulta estructurado de Informix. <br/><br/>Ningún desencadenador. Acciones incluyen tabla seleccionar, insertar, actualizar, eliminar e instrucción personalizada<br/><br/>Este conector también incluye el cliente de Microsoft para DRDA conectarse a un servidor de Informix a través de una red TCP/IP.
[Archivo](app-service-logic-connector-file.md) | Con este conector, puede conectarse al sistema de archivos local o red y tareas de completa del archivo diferentes, como cargar, eliminar, incluye los archivos y mucho más.
[Informix](app-service-logic-connector-informix.md) | Se conecta a una base de datos de IBM Informix, local y en una máquina virtual Azure ejecuta un sistema operativo Windows. Puede asignar las operaciones de Web API y API de OData a los comandos de lenguaje de consulta estructurado de Informix.<br/><br/>Ningún desencadenador. Acciones incluyen tabla seleccionar, insertar, actualizar, eliminar e instrucción personalizada.<br/><br/>Cuando se usa en local, puede usarse VPN o Azure ExpressRoute. Este conector también incluye un cliente de Microsoft para DRDA conectarse a un servidor de Informix a través de una red TCP/IP.
[Microsoft SQL Server](app-service-logic-connector-sql.md) | Se conecta a la implementación local de SQL Server o una base de datos SQL Azure. Puede crear, actualizar, obtener y eliminar las entradas en una tabla de base de datos SQL.
MQ | Se conecta a IBM WebSphere MQ Server versión 8, local y en una máquina virtual Azure ejecuta un sistema operativo Windows. Cuando se usa en local, puede usarse VPN o Azure ExpressRoute. El conector también incluye el cliente de Microsoft para MQ.<br/><br/>Ningún desencadenador. Ninguna acción.<br/><br/>**Nota** Actualmente no se pueden usar con aplicaciones de lógica.

## <a name="connectors-as-triggers"></a>Conectores como desencadenadores
Varios conectores proporcionan desencadenadores para aplicaciones de lógica. Estos desencadenadores son de dos tipos:

1. Desencadenadores de sondeo: Estos desencadenadores sondean su servicio en una frecuencia especificada para comprobar si los datos nuevos. Cuando hay nuevos datos, una nueva instancia de la aplicación de la lógica se ejecuta con los datos de entrada. Para evitar que los mismos datos consumida varias veces, el desencadenador puede limpieza datos que leer y pasados a la aplicación de la lógica. Ejemplos de estos conectores son archivos, SQL y el almacenamiento de Azure.
2. Desencadenadores de inserción: Estos desencadenadores escuchan para los datos en un punto final o que se produzca un evento. A continuación, desencadenar una nueva instancia de una aplicación de lógica. Ejemplos de estos conectores son escucha HTTP y Twitter.

## <a name="connectors-as-actions"></a>Conectores como acciones
Conectores pueden utilizarse también como acciones dentro de la aplicación de la lógica. Acciones son útiles para buscar datos dentro de la aplicación de lógica puede, a continuación, se utiliza en la ejecución. Por ejemplo, necesitará buscar datos desde una base de datos SQL para obtener información adicional acerca de un cliente al procesar un pedido. O bien, tendrá que escribir, actualizar o eliminar datos en un destino. Para hacer esto mediante las acciones de los conectores. Acciones asignan a las operaciones en las aplicaciones de la API (como defina sus metadatos Swagger).

## <a name="create-your-own-connectors-and-api-apps"></a>Crear sus propios conectores y aplicaciones de la API
[Referencia de aplicaciones de la API y conectores](http://aka.ms/appservicesconnectorreference)  
[Desencadenadores de aplicación de Azure API del servicio de aplicación](../app-service-api/app-service-api-dotnet-triggers.md)  
[Referencia de la aplicación de lógica](https://msdn.microsoft.com/library/azure/dn948510.aspx)

## <a name="more-on-connectors-and-api-apps"></a>Más información sobre los conectores y aplicaciones de la API
[¿Qué son los conectores y aplicaciones de la API de BizTalk](app-service-logic-what-are-biztalk-api-apps.md)  
[Uso del Administrador de conexión de híbrido en Azure de aplicación de servicio](app-service-logic-hybrid-connection-manager.md)  
[Administrar y supervisar la API de aplicaciones y los conectores integrados](app-service-logic-monitor-your-connectors.md)

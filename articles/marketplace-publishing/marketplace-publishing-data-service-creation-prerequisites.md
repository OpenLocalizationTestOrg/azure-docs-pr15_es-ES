<properties
   pageTitle="Requisitos previos técnicas para crear un servicio de datos para el catálogo de soluciones | Microsoft Azure"
   description="Comprender los requisitos para crear un servicio de datos para implementar y vender en Azure Marketplace"
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/26/2016"
   ms.author="hascipio; avikova" />

# <a name="technical-pre-requisites-for-creating-a-data-service-offer-for-the-azure-marketplace"></a>Requisitos previos técnicas para crear un servicio de datos de la oferta para Azure Marketplace

>[AZURE.IMPORTANT] **En este momento estamos ya no integrado cualquier nuevas publicaciones de servicio de datos. No se obtener aprobado dataservices nueva lista.** Si tiene una aplicación de empresa SaaS que desea publicar en AppSource puede encontrar más información [aquí](https://appsource.microsoft.com/partners). Si tiene un aplicaciones IaaS o servicio de programador que desea publicar en Azure Marketplace puede encontrar más información [aquí](https://azure.microsoft.com/marketplace/programs/certified/).

Lea detenidamente antes de comenzar el proceso de y comprender dónde y por qué se lleva a cabo cada paso. Tanto como sea posible, se debe preparar la información de su organización y otros datos, descargue las herramientas necesarias o crear componentes técnicos antes de comenzar el proceso de creación de la oferta.

Que debería tener a mano antes de comenzar el proceso de lo siguiente:

## <a name="make-a-decision-on-what-technology-will-be-used-to-publish-your-data-service-offer"></a>Tomar la decisión de qué tecnología se usará para publicar su oferta de servicio de datos

Un editor puede decidir entre varias tecnologías al servicio de datos de publicación en Azure Marketplace. Las principales tecnologías que son compatibles se describe a continuación. Sin tener en cuenta qué tecnología se utiliza para publicar el servicio de datos, el usuario final consume los datos a través de la **fuente de OData** expuestas por el servicio de Azure Marketplace. Información completa sobre el servicio de OData puede encontrar en [http://www.odata.org/](http://www.odata.org/)

## <a name="sql-azure-database"></a>SQL Azure base de datos

Conjunto de datos listo en SQL Azure es responsabilidad de Publisher. Debe suscribirse a Azure, aprovisionar tamaño apropiado de la base de datos y cargar los datos en la base de datos de SQL Azure. Publisher también es responsable de mantener sus datos siempre actualizados. Para obtener más información sobre la suscripción a servicios de Azure puede encontrar en [https://azure.microsoft.com/services/sql-database/](https://azure.microsoft.com/services/sql-database/)


Al mover los datos en SQL Azure, puede exponer Azure Marketplace tablas y vistas. El editor puede especificar qué tablas o vistas y columnas se exponen para el usuario final. Aún más el proveedor de contenido también puede especificar qué columnas se pueden consultar por el usuario final y cuáles se devuelven sólo en la carga. Esto le da un alto nivel de flexibilidad sobre el que se deben exponer en la base de datos. Columnas que se pueden consultar necesitan uno o varios de los índices de base de datos de seguridad.

## <a name="rest-based-web-service"></a>Servicio web REST según

Admite el protocolo: **solo HTTPS**

Pueden exponer servicios resto según existentes a través de Azure Marketplace. Dado que el conjunto de datos siempre se expone para el usuario final como una fuente de OData, las necesidades de servicio de Azure Marketplace para poder asignar el servicio a una OData servicio basado en. Para ello, el resto basa extremos necesite exponer todos los parámetros como parámetros HTTP.

La carga debe estar en un formulario que se puede asignar a una respuesta ATOM. Por lo tanto, la respuesta de las necesidades de los servicios en formato XML y sólo puede contener un elemento de repetición que contiene los valores de carga (por ejemplo, conjunto de registros). El servicio de Azure Marketplace asignará el nodo de repetición en el nodo entrada ATOM y los valores de carga en los nodos de propiedad en el nodo de entrada.

Información de autorización (por ejemplo, API, clave, etc. token, autenticación) debe proporcionarse como un parámetro HTTP o en el encabezado HTTP (par de valor de clave): autenticación básica también es compatible. Debe proporcionarse una clave válida y se realizan todas las solicitudes a través de Azure Marketplace a través de dicha clave. Usuario supervisión y facturación sucede en el nivel de Azure Marketplace.

Los errores devueltos por el servicio que necesite asignarse a códigos de estado HTTP. En caso de que el servicio devuelve un XML que contiene el error que estos va a asignar por el servicio de Azure Marketplace códigos de estado HTTP.

## <a name="soap-based-web-services"></a>Servicios web SOAP según

Protocolo: **solo HTTPS**

Los requisitos son que los mismos que el resto según la sección servicio. La única diferencia es que también se pueden proporcionar parámetros en un cuerpo XML que se publican en el servicio de Publisher con cada solicitud realizada a través de Azure Marketplace. Esto significa que el usuario proporciona en el front-end de parámetros HTTP se está traduciendo en los elementos XML de un documento XML que se está enviando a la solicitud de servicio web de su proveedor de contenido.

## <a name="odata-based-web-services"></a>Servicios web OData según

Protocolo: **solo HTTPS**

Datos se pueden exponer como un servicio de OData a Azure Marketplace. El sistema se va a pasar el servicio mediante y reemplaza la raíz del servicio con la raíz del servicio de Azure Marketplace: para asegurarse de que todas las llamadas siguientes vayan a través de Azure Marketplace.

Servicios de OData solo no es necesario ir a una base de datos en el servidor. OData es compatible con cualquier tipo de almacenamiento o la lógica empresarial a conducir el servicio.


## <a name="next-steps"></a>Pasos siguientes
Revisar los requisitos previos y completar las tareas necesarias, puede avanzar a la creación de la oferta de servicio de datos como se describe en la [Guía de publicación de servicio de datos](marketplace-publishing-data-service-creation.md).

O bien, si desea revisar el proceso general y los artículos respectivos para cada una de las fases de publicación, visite el artículo [Introducción: cómo publicar una oferta en Azure Marketplace](marketplace-publishing-getting-started.md).

[link-acct]:marketplace-publishing-accounts-creation-registration.md

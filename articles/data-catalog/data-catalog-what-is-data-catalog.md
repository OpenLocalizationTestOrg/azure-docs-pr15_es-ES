<properties
   pageTitle="¿Qué es el catálogo de datos de Azure? | Microsoft Azure"
   description="Este artículo proporciona una descripción general de catálogo de datos de Microsoft Azure, incluidas sus características y los problemas que se ha diseñado para la dirección. Catálogo de datos ofrece capacidades que permiten a cualquier usuario – desde los analistas científicos de datos a los desarrolladores: registrar, descubrir, comprenden y usar orígenes de datos."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="09/21/2016"
   ms.author="maroche"/>

# <a name="what-is-azure-data-catalog"></a>¿Qué es el catálogo de datos de Azure?

Catálogo de datos de Azure es un servicio de nube totalmente administrado que permite a los usuarios para ver los orígenes de datos que necesitan y comprender los orígenes de datos que se encuentre, al ayudar a las organizaciones a obtener más valor de sus inversiones existentes. Catálogo de datos ofrece capacidades que permiten a cualquier usuario – desde los analistas científicos de datos a los desarrolladores – para detectar, comprender y usar orígenes de datos. Catálogo de datos incluye un modelo de crowdsourcing de metadatos y anotaciones y permite a los usuarios poder colaborar sus conocimientos para crear una Comunidad y la referencia cultural de datos.

## <a name="discovery-challenges-for-data-consumers"></a>Retos de detección para los consumidores de datos

Tradicionalmente, descubrir orígenes de datos de la empresa ha sido un proceso ecológico basado en conocimiento tribal. Esto presenta varios desafíos para las compañías que desean obtener el mayor valor de sus activos de información.

-   Los usuarios desconocen que existen orígenes de datos a menos que entren en contacto con él como parte de ningún otro proceso; No hay ninguna ubicación central donde se registran los orígenes de datos.
-   A menos que un usuario conoce la ubicación de un origen de datos, no puede conectarse a los datos mediante una aplicación de cliente; experiencias de consumo de datos requieran que los usuarios saber la cadena de conexión o la ruta de acceso.
-   A menos que un usuario conoce la ubicación de la documentación del origen de datos, no entiende que el uso de los datos; documentación y orígenes de datos están en distintos lugares y se consumen a través de experiencias diferentes.
-   Si un usuario tiene preguntas sobre un activo de información, debe buscar el experto o grupo responsable de los datos y contar con esos expertos sin conexión; No hay ninguna conexión explícita entre los datos y los con perspectivas expertos en su uso.
-  A menos que un usuario comprende el proceso para solicitar el acceso al origen de datos, descubrir el origen de datos y su documentación todavía no lo habilita para tener acceso a los datos que requiere.

## <a name="discovery-challenges-for-data-producers"></a>Retos de detección para los fabricantes de datos

Mientras los consumidores de datos enfrentan estos desafíos, los usuarios responsables de producir y mantener activos de información enfrentan desafíos de sus propios.

-   Anotar orígenes de datos con metadatos descriptivos a menudo es fruto de una pérdida; aplicaciones cliente normalmente omiten las descripciones que se almacena en el origen de datos.
-   Crear documentación de orígenes de datos a menudo es fruto de una pérdida; Mantener documentación en sincronización con el origen de datos es una responsabilidad en curso y los usuarios no tienen confianza en la documentación como que a menudo se considera que se está actualizada.
- Restringir el acceso al origen de datos y garantizar que los consumidores de datos saben cómo solicitar acceso están un desafío constante.

Crear y mantener la documentación de un origen de datos es compleja y lenta. A menudo es aún más el desafío de realizar dicha documentación están disponibles para todos aquellos que usa el origen de datos.

Cuando se combinan, estos desafíos presentan una barrera significativa para empresas que desean motivar a y promover el uso y la comprensión de datos empresariales.

## <a name="azure-data-catalog-can-help"></a>Puede ayudar catálogo de datos de Azure

Catálogo de datos está diseñada para abordar estos problemas y permitir a las empresas obtener el valor de la mayoría de los activos de información existentes. Ayuda de catálogo de datos creando orígenes de datos fácilmente detectable y comprensible por los usuarios que necesitan los datos que administran.

Catálogo de datos ofrece un servicio basado en la nube en los datos que se puede registrar el origen. Los datos permanecen en su ubicación existente, pero una copia de los metadatos que se agrega al catálogo de datos, junto con una referencia a la ubicación de origen de datos. También se indizan de metadatos para hacer fácilmente detectable mediante búsqueda, cada origen de datos y comprensible a los usuarios que lo detecte.

Una vez que se ha registrado un origen de datos, sus metadatos, a continuación, se pueden mejorar, por el usuario que ha realizado el registro, o por otros usuarios de la empresa. Cualquier usuario puede realizar anotaciones en un origen de datos proporcionando descripciones, etiquetas u otros metadatos, por ejemplo, documentación y procesos de solicitud de acceso al origen de datos. El complemento de metadatos descriptivo los metadatos estructurales (por ejemplo, nombres de columna y tipos de datos) registrados desde el origen de datos.

Descubrir y descripción de los orígenes de datos y su uso es la finalidad principal de registrar los orígenes. Cuando los usuarios empresariales necesitan los datos de sus esfuerzos (que podrían ser inteligencia empresarial, desarrollo de aplicaciones, ciencia de datos o cualquier otra tarea donde se requiere los datos adecuados) pueden usar la experiencia de descubrimiento de catálogo de datos rápidamente para buscar datos que coincidan con sus necesidades, comprender los datos para evaluar su idoneidad para fin y consumir datos abriendo el origen de datos en la herramienta de elección. Al mismo tiempo, catálogo de datos permite a los usuarios colaborar en el catálogo de etiquetado, documentar y anotar orígenes de datos que ya se ha registrado y registrar nuevos orígenes de datos que, a continuación, se pueden detectar, comprender y consumidos por la Comunidad de usuarios de catálogo.

![Capacidades del catálogo de datos](./media/data-catalog-what-is-data-catalog/data-catalog-capabilities.png)

## <a name="get-started-with-data-catalog"></a>Empezar a trabajar con el catálogo de datos

Para empezar a usar el catálogo de datos en la actualidad, visite [www.azuredatacatalog.com](https://www.azuredatacatalog.com).

Una guía de introducción está disponible [aquí](data-catalog-get-started.md).

## <a name="learn-more-about-data-catalog"></a>Obtenga más información sobre el catálogo de datos

Para obtener más información sobre las capacidades del catálogo de datos, consulte:

* [Cómo registrar los orígenes de datos](data-catalog-how-to-register.md)
* [Cómo ver los orígenes de datos](data-catalog-how-to-discover.md)
* [Cómo hacer anotaciones en orígenes de datos](data-catalog-how-to-annotate.md)
* [Cómo a orígenes de datos de documento](data-catalog-how-to-documentation.md)
* [Cómo conectarse a orígenes de datos](data-catalog-how-to-connect.md)
* [Cómo trabajar con datos grandes](data-catalog-how-to-big-data.md)
* [Cómo administrar activos de datos](data-catalog-how-to-manage.md)
* [Cómo configurar el glosario de empresa](data-catalog-how-to-business-glossary.md)
* [Preguntas más frecuentes](data-catalog-frequently-asked-questions.md)

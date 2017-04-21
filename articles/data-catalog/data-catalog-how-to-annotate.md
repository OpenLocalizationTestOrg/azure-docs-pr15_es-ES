<properties
   pageTitle="Cómo hacer anotaciones en orígenes de datos | Microsoft Azure"
   description="Artículo de procedimiento para resaltar cómo anotar activos de datos en el catálogo de datos de Azure, incluidos los expertos, etiquetas, descripciones y nombres descriptivos."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="09/21/2016"
   ms.author="maroche"/>


# <a name="how-to-annotate-data-sources"></a>Cómo hacer anotaciones en orígenes de datos

## <a name="introduction"></a>Introducción
**Catálogo de datos de Microsoft Azure** es un servicio de nube totalmente administrado que sirve como un sistema de registro y sistema de detección de orígenes de datos de la empresa. En otras palabras, el catálogo de datos consiste en ayudar a las personas descubrir, comprender y usar orígenes de datos y ayudar a las organizaciones a obtener más valor de sus datos. Cuando se registra un origen de datos con el catálogo de datos, sus metadatos se copia e indizado por el servicio, pero la historia no termina aquí. Catálogo de datos permite a los usuarios proporcionar sus propios metadatos descriptivo, como descripciones y etiquetas: para completar los metadatos extraídos del origen de datos y para facilitar el origen de datos a más personas.

## <a name="annotation-and-crowdsourcing"></a>Anotación y crowdsourcing
Todos tienen una opinión. Y esto es bueno.
Catálogo de datos reconoce que diferentes usuarios tienen diferentes perspectivas en orígenes de datos de la empresa y, a continuación, cada una de estas perspectivas puede ser valiosa. Tenga en cuenta lo siguiente:

* El administrador del sistema sabe el contrato de nivel de servicio para los servidores o servicios que hospeden el origen de datos.
* El Administrador de la base de datos sabe la programación de copia de seguridad para cada base de datos y las ventanas de procesamiento de ETL permitidos.
* El propietario del sistema sabe el proceso para los usuarios que soliciten acceso al origen de datos.
* Los administradores de datos sabe cómo se asignan los activos y atributos del origen de datos al modelo de datos empresariales.
* Los analistas sabe cómo los datos se utilizan en el contexto de los procesos empresariales que admite.

Cada una de estas perspectivas es valiosa y catálogo de datos utiliza un enfoque crowdsourcing a los metadatos que permite a cada uno de ellos se capturan y utilizarlo para proporcionar un panorama completo de orígenes de datos registrados. Con el portal de catálogo de datos, cada usuario puede agregar y editar sus propias anotaciones, mientras se pueden ver las anotaciones proporcionadas por otros usuarios.

## <a name="different-types-of-annotations"></a>Diferentes tipos de anotaciones
Catálogo de datos es compatible con los siguientes tipos de anotaciones:

| Anotación     | Notas                                                                                                                                                                                                                                                                                                                                                           |
|----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nombre descriptivo  | Pueden proporcionar nombres descriptivos en el nivel de activos de datos para asegurarse de los activos de datos más fáciles de entender. Nombres descriptivos son más útiles cuando el nombre del objeto subyacente es críptica, abreviada o en caso contrario, no significativo a los usuarios.                                                                                                                            |
| Descripción    | Descripciones pueden proporcionar a los datos activos y atributo y niveles de columna. Descripciones son anotaciones de texto de forma libre breve que describen el usuario perspectiva en datos activos o su uso.                                                                                                                                                              |
| Usuario (etiquetas)          | Las etiquetas se pueden proporcionar en los activos de datos y el atributo y niveles de columna. Etiquetas de usuario son etiquetas definidas por el usuario que se pueden usar para clasificar los activos de datos o atributos.                                                                                                                                                                                                    |
| Glosario (etiquetas)          | Las etiquetas se pueden proporcionar en los activos de datos y el atributo y niveles de columna. Etiquetas de glosario son términos de glosario de forma centralizada definidas por el que se pueden usar para clasificar los activos de datos o atributos mediante una taxonomía empresarial común. Para obtener más información, consulte [cómo configurar el glosario de empresa para regulados etiquetado](data-catalog-how-to-business-glossary.md)                                                                                                                                                                                                    |
| Expertos        | Pueden proporcionar expertos en el nivel de activos de datos. Expertos identificar los usuarios o grupos con perspectivas expertos en los datos y pueden servir como puntos de contacto para los usuarios que descubra los orígenes de datos registrados y preguntas que no responde por las anotaciones existentes.  |
| Solicitar acceso | Puede proporcionar información de solicitud de acceso en el nivel de activos de datos. Esta información es para usuarios que descubra un origen de datos que todavía no tiene permisos de acceso. Los usuarios pueden especificar la dirección de correo electrónico del usuario o grupo que conceda acceso, la dirección URL del proceso o herramienta que los usuarios necesitan tener acceso, o pueden especificar el proceso de texto. |
| Documentación | Puede proporcionar la documentación en el nivel de activos de datos. Documentación de activos es la información de texto enriquecido que pueden incluir vínculos e imágenes y que pueden proporcionar información que no se transmite a través de descripciones y etiquetas. |


## <a name="annotating-multiple-assets"></a>Anotar múltiples activos
Al seleccionar varios activos de datos en el portal de catálogo de datos, los usuarios pueden anotar todos los activos seleccionados en una única operación. Las anotaciones se aplicarán a todos los activos seleccionados, lo que posibilita seleccione y proporcione una descripción coherente y conjuntos de etiquetas y expertos de activos de datos relacionados.

> [AZURE.NOTE] Etiquetas y expertos también pueden proporcionar cuando registrar activos de datos con los datos del catálogo de datos de origen de herramienta de registro.

Al seleccionar varias tablas y vistas, solo las columnas que todos los datos activos tienen en común seleccionados se mostrará en el portal de catálogo de datos. Esto permite a los usuarios etiquetas y descripciones de todas las columnas con el mismo nombre para todos los activos seleccionados.

## <a name="annotations-and-discovery"></a>Las anotaciones y detección
Como los metadatos que se extraen del origen de datos durante el registro se agregan al índice de búsqueda de catálogo de datos, también se indizan de metadatos proporcionados por el usuario. Esto significa que no sólo las anotaciones que sea más fácil para los usuarios a comprender los datos descubren, anotaciones también que sea más fácil para los usuarios detectar los activos de datos anotados de búsqueda con los términos que tenga sentido para ellos.

## <a name="summary"></a>Resumen
Cómo registrar un origen de datos con el catálogo de datos asegura de que los datos reconocible copiando descriptivo y estructural metadatos del origen de datos en el servicio de catálogo. Una vez que se ha registrado un origen de datos, los usuarios pueden proporcionar las anotaciones para facilitar a descubrir y comprender desde dentro del portal de catálogo de datos.

## <a name="see-also"></a>Vea también
- [Empezar a trabajar con el catálogo de datos de Azure](data-catalog-get-started.md) tutorial de instrucciones paso a paso acerca de cómo hacer anotaciones en orígenes de datos.

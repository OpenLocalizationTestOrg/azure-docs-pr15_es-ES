<properties
    pageTitle="Cómo configurar el glosario de la empresa para regulados etiquetado | Microsoft Azure"
    description="Artículo de procedimiento para resaltar el glosario de empresa en el catálogo de datos de Azure para definir y usar un vocabulario común de empresa a etiqueta registrado activos de datos."
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

# <a name="how-to-set-up-the-business-glossary-for-governed-tagging"></a>Cómo configurar el glosario de la empresa para regulados etiquetado

## <a name="introduction"></a>Introducción

Catálogo de datos de Azure ofrece capacidades de descubrimiento de origen de datos, permitir a los usuarios descubrir y comprender los orígenes de datos que necesitan para realizar análisis y tomar decisiones fácilmente. Estas capacidades de descubrimiento obtener el mayor impacto cuando los usuarios pueden buscar y comprender la más amplia variedad de orígenes de datos disponibles.

Una característica de catálogo de datos que promueve una mejor comprensión de los datos de activos de etiquetado. Etiquetado permite a los usuarios asociar palabras clave con un activo o una columna, que a su vez facilita descubrir activo a través de buscar o examinar, y permite a los usuarios a comprender más fácilmente el contexto y la finalidad del activo.

Sin embargo, el etiquetado a veces puede causar problemas de su propia. Algunos ejemplos de problemas que pueden aparecer al etiquetado son:

1.  Usuarios con abreviaturas en algunos activos y texto expandido en otras personas al etiquetado. Esta incoherencia dificulta la detección de activos, aunque la intención era etiquetar los activos con la misma etiqueta.
2.  Etiquetas que tienen significados diferentes en contextos diferentes. Por ejemplo, una etiqueta denominada "Ingresos" en un conjunto de datos de cliente, quizá los ingresos por cliente, pero la misma etiqueta en un conjunto de datos de venta trimestral puede significar ingresos trimestrales para la empresa.  

Para ayudar a solucionar éstos y otros desafíos similares, catálogo de datos incluye un glosario de empresa.

El glosario de empresa de catálogo de datos permite a las organizaciones términos clave para el negocio del documento y sus definiciones para crear un vocabulario común de la empresa. Este control permite la coherencia en el uso de datos de toda la organización. Una vez términos se definen en el glosario de empresa, se pueden asignar a los activos de datos en el catálogo, utilizando el mismo enfoque como el etiquetado temático, permitiendo _regulados etiquetado_.

> [AZURE.NOTE] La funcionalidad que se describe en este artículo sólo están disponibles en la edición estándar del catálogo de datos de Azure. La edición gratuita no ofrece capacidades de etiquetado controlados o un glosario de empresa.

## <a name="glossary-availability-and-privileges"></a>Disponibilidad de glosario y privilegios

*Glosario de empresa está disponible en la edición estándar del catálogo de datos de Azure. Datos catálogo gratuito no incluye un glosario.*

Glosario de empresa puede tener acceso a través de la opción "Glosario" en el menú de navegación del portal de catálogo de datos.  

![Obtener acceso a glosario de empresa](./media/data-catalog-how-to-business-glossary/01-portal-menu.png)


Los administradores del catálogo de datos y los miembros de la función administradores glosario pueden crear, editar y eliminar Glosario de términos en el glosario de empresa. Todos los usuarios del catálogo de datos pueden ver las definiciones de términos y pueden etiquetar activos con los términos del glosario.

![Agregar un nuevo término de glosario](./media/data-catalog-how-to-business-glossary/02-new-term.png)


## <a name="creating-glossary-terms"></a>Creación de términos de glosario

Los administradores del catálogo de datos y glosario puede crear nuevos términos de glosario haciendo clic en el nuevo término ' botón Crear glosario con los siguientes campos:

* Una definición de la empresa para el término
* Una descripción que captura el uso de reglas de negocios de la columna de activos
* Una lista de los participantes que saber más sobre el término
* El término primario, que define la jerarquía en la que se organiza el término


## <a name="glossary-term-hierarchies"></a>Jerarquías de términos de glosario

Glosario de catálogo de datos empresariales proporciona la capacidad para describir el vocabulario como una jerarquía de términos. Esto permite a las organizaciones crear una clasificación de términos que representa mejor su taxonomía empresarial.

El nombre de un término debe ser único en un determinado nivel de jerarquía: no se permiten nombres duplicados. No hay ningún límite para el número de niveles en una jerarquía, pero una jerarquía es a menudo más fáciles de entender cuando hay tres niveles o menos.

El uso de jerarquías en el glosario de empresa es opcional. Salir del elemento primario términos campo en blanco para los términos de glosario creará una lista de planos (no jerárquica) de términos en el glosario.  

## <a name="tagging-assets-with-glossary-terms"></a>Etiquetado de activos con los términos de glosario

Una vez que se han definido glosario dentro del catálogo, la experiencia de etiquetado de activos está optimizada para buscar el glosario mientras el usuario escribe su etiqueta. El portal de catálogo de datos muestra una lista de coincidencia Glosario de términos para el usuario que puede elegir. Si el usuario selecciona un término del glosario de la lista se agrega al activo como una etiqueta (también conocido como etiqueta de glosario). El usuario también puede crear una nueva etiqueta escribiendo un término que no está en el glosario (también conocido como etiqueta de usuario).

![Datos activos etiquetan con dos etiquetas de glosario y etiqueta de un usuario](./media/data-catalog-how-to-business-glossary/03-tagged-asset.png)

> [AZURE.NOTE] Etiquetas de usuario son el único tipo de etiqueta de datos catálogo gratuito es compatible.

### <a name="hover-behavior-on-tags"></a>Comportamiento de desplazamiento de las etiquetas
En el portal de catálogo de datos los dos tipos de etiquetas son visualmente distinct, con comportamientos de desplazamiento diferente. Cuando el usuario mantiene el mouse sobre una etiqueta de usuario pueden ver el texto de etiqueta y el usuario o los usuarios que han agregado la etiqueta. Cuando el usuario mantiene el mouse sobre una etiqueta de glosario, verán la definición de los términos de glosario y un vínculo para abrir el glosario de empresa para ver la definición completa del término.

### <a name="search-filters-for-tags"></a>Filtros de búsqueda de etiquetas
Etiquetas de glosario y etiquetas de usuario son búsquedas y se pueden aplicar como filtros en una búsqueda.

## <a name="summary"></a>Resumen
Glosario de negocio en catálogo de datos de Azure y etiquetado controlados le permite, permitir activos de datos para identificarse, administrada y descubre de manera coherente. Glosario de empresa puede aumentar el nivel de aprendizaje del vocabulario empresarial entre los usuarios de una organización y admite metadatos significativo para capturar, realizar detección de activos y comprender sea sencillo.

## <a name="see-also"></a>Vea también

- [Documentación de la API de REST para operaciones de glosario de negocio](https://msdn.microsoft.com/library/mt708855.aspx)

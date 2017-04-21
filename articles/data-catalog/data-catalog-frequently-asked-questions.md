<properties
   pageTitle="Preguntas más frecuentes del catálogo de datos de Azure | Microsoft Azure"
   description="Preguntas más frecuentes sobre el catálogo de datos de Azure, incluidas las capacidades de descubrimiento de origen de datos, las anotaciones y administración."
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
   ms.date="10/04/2016"
   ms.author="maroche"/>

# <a name="azure-data-catalog-frequently-asked-questions"></a>Preguntas más frecuentes del catálogo de datos de Azure

En este artículo se proporciona respuestas para las preguntas más frecuentes relacionados con el servicio de Microsoft **Azure catálogo de datos** .

## <a name="q-what-is-azure-data-catalog"></a>P: ¿qué es el catálogo de datos de Azure?

R: Microsoft catálogo de datos de Azure es un servicio de totalmente administrado hospedado en la nube de Microsoft Azure que sirve como un sistema de registro y sistema de detección de orígenes de datos de la empresa. Catálogo de datos de Azure ofrece capacidades que permiten a cualquier usuario – desde los analistas científicos de datos a los desarrolladores: registrar, descubrir, comprenden y usar orígenes de datos.

## <a name="q-what-customer-challenges-does-azure-data-catalog-solve"></a>P: ¿qué cliente desafíos hace el catálogo de datos de Azure resolver?

Catálogo de datos de Azure resuelve el desafío de detección de origen de datos y "datos oscuros" al permitir a los usuarios descubrir y comprender los orígenes de datos de la empresa.

## <a name="q-who-are-the-target-audiences-for-azure-data-catalog"></a>P: ¿Cuáles son las audiencias de destino para el catálogo de datos de Azure?

Catálogo de datos de Azure ofrece capacidades para usuarios técnicos y no técnicos, incluidos:

- Los desarrolladores de datos, BI y análisis profesionales: quién es responsable de producir datos y análisis de contenido para que otras personas consumir
-   Los administradores de datos: Los usuarios que tengan los conocimientos sobre los datos, lo que significa y cómo está pensado para ser usado y qué finalidad
- Los consumidores de datos: Aquellos que deben poder detectar, comprender y conéctese fácilmente a datos necesarios para realizar su trabajo con la herramienta de su elección
- Central TI: aquellos que necesitan realizar cientos de orígenes de datos reconocible para los usuarios de la empresa y que necesitan mantener una visión general sobre cómo se utiliza datos y por quién

## <a name="q-what-is-the-azure-data-catalog-region-availability"></a>P: ¿cuál es la disponibilidad de la región de catálogo de datos de Azure?

Servicios de catálogo de datos de Azure ya están disponibles en los centros de datos siguientes:

- Estados Unidos occidental
- Estados Unidos oriental
- Europa occidental
- Europa del Norte
- Australia Oriental
- Sudeste asiático

## <a name="q-what-are-the-limits-on-the-number-of-data-assets-in-azure-data-catalog"></a>P: ¿Cuáles son los límites del número de activos de datos en el catálogo de datos de Azure?

La edición gratuita de catálogo de datos de Azure se limita a los activos de datos registrados 5.000.

La edición estándar del catálogo de datos de Azure admite hasta 100000 activos de datos registrados.

## <a name="q-what-are-the-supported-data-source-and-asset-types"></a>P: ¿qué son los tipos de origen y de activos de datos compatibles?

Consulte [DLV del catálogo de datos](data-catalog-dsr.md) para la lista de orígenes de datos compatibles actualmente.

## <a name="q-how-do-i-request-support-for-another-data-source"></a>P: ¿Cómo puedo solicitar soporte técnico para otro origen de datos?

Pueden enviar solicitudes de características y otros comentarios en el [foro de catálogo de datos de Azure](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).

## <a name="q-how-do-i-get-started-with-azure-data-catalog"></a>P: ¿Cómo puedo empezar con el catálogo de datos de Azure?

Es el mejor lugar para empezar, siga las instrucciones en la [Introducción con el catálogo de datos](data-catalog-get-started.md). En este artículo es una descripción general de llevar a cabo de las funciones en el servicio.

## <a name="q-how-do-i-register-my-data"></a>P: ¿cómo registrar Mis datos?

Para registrar los datos en el catálogo de datos de Azure, inicie la herramienta de registro de catálogo de datos de Azure desde el área de "Publicación" del portal del catálogo de datos de Azure. En la aplicación de publicación del catálogo de datos de Azure, inicie sesión con las mismas credenciales que se usa para acceder al portal de catálogo de datos de Azure y, a continuación, seleccione el origen de datos y los activos específicos que desea registrar.

## <a name="q-what-properties-are-extracted-for-data-assets-that-are-registered"></a>P: ¿qué propiedades se extraen para los activos de datos que están registrados?

Las propiedades específicas difieren de origen de datos al origen de datos, pero en general, el servicio de publicación del catálogo de datos de Azure extraerá la información siguiente:

- Nombre de activos
- Tipo de activos
- Descripción de activos
- Nombres de atributos y columnas
- Tipos de datos de atributo y columnas
- Descripción de atributo y columnas

> [AZURE.IMPORTANT] Registro de activos de datos con el catálogo de datos de Azure no mover o copiar los datos en la nube. Registrar activos desde un origen de datos se copiarán los metadatos de los activos en Azure, pero los datos se mantienen en la ubicación de origen de datos existente. La única excepción a esta regla es un usuario que decide cargar registros de vista previa o un perfil de datos al registro de activos. Cuando incluida una vista previa, hasta 20 registros se copiarán de cada activo y se almacenan como una instantánea en el catálogo de datos de Azure. Cuando se incluye un perfil de datos, agregar información (como el tamaño de las tablas, los valores nulos de porcentaje por columna y los valores mínimos, máximos y medios de columnas) se calculará y se incluye en los metadatos que se almacenan en el catálogo.

<br/>

> [AZURE.NOTE] Para orígenes de datos como SQL Server Analysis Services que tienen una propiedad de **Descripción** de primera clase, el catálogo de datos de Azure publicar aplicación extraerá ese valor de propiedad. Bases de datos relacionales SQL Server, que no tienen una propiedad de **Descripción** de primera clase, la aplicación de publicación del catálogo de datos de Azure extraerá el valor de la propiedad para objetos y columnas extendida de ms_description. Para obtener más información, vea TechNet [Utilizando propiedades extendidas en objetos de base de datos](https://technet.microsoft.com/library/ms190243%28v=sql.105%29.aspx).

## <a name="q-how-long-should-it-take-for-newly-registered-assets-to-appear-in-azure-data-catalog"></a>P: ¿cuánto tiempo debe tomar para que activos recién registrados que aparezca en el catálogo de datos de Azure?

Después de registrar activos con el catálogo de datos de Azure puede ser un período de 5 y 10 segundos antes de que aparezcan en el portal de catálogo de datos de Azure.

## <a name="q-how-do-i-annotate-and-enrich-the-metadata-for-my-registered-data-assets"></a>P: ¿cómo realizar anotaciones y enriquecer los metadatos de los activos de datos registrados?

Es el modo más sencillo para proporcionar metadatos para activos registrados seleccionar el activo en el portal de catálogo de datos de Azure y, a continuación, escriba los valores de metadatos en el panel de propiedades o el panel de esquema para el objeto seleccionado.

También puede proporcionar algunos metadatos, como etiquetas y expertos durante el proceso de registro. Los valores proporcionados en el servicio de publicación del catálogo de datos de Azure se aplicarán a todos los activos que se registra en ese momento. Para ver los objetos registrados recientemente en el portal de anotación adicional, seleccione el botón de **Portal de la vista** en la pantalla final de la aplicación de publicación del catálogo de datos de Azure.

## <a name="q-how-do-i-delete-my-registered-data-objects"></a>P: ¿Cómo puedo eliminar los objetos de datos registrados?

Puede eliminar un objeto del catálogo de datos de Azure seleccionar el objeto en el portal y, a continuación, haciendo clic en el botón **Eliminar** . Se quitan los metadatos del objeto del catálogo de datos de Azure, pero no afectará el origen de datos subyacente real.

## <a name="q-what-is-an-expert"></a>P: ¿qué es un experto?

Un experto es una persona que tiene una perspectiva informada sobre un objeto de datos. Un objeto puede tener varios expertos. Un experto no es necesario que sea el "propietario" para un objeto; el experto es simplemente a alguien que sabe cómo pueden los datos y se debe usar.

## <a name="q-how-do-i-share-information-with-the-azure-data-catalog-team-if-i-encounter-problems"></a>P: ¿cómo compartir información con el equipo del catálogo de datos de Azure si producen problemas?

Por favor, use el foro de catálogo de datos de Azure para informar de problemas, compartir información y formular preguntas. El foro se puede encontrar en http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409

##<a name="q-does-azure-data-catalog-work-with-this-other-data-source-im-interested-in"></a>P: ¿catálogo de datos de Azure funciona con este otro origen de datos que me interesan?
Estamos trabajando actualmente sobre cómo agregar más orígenes de datos al catálogo de datos de Azure. Si hay un origen de datos que desea que vea admitidos, por favor sugerir (o el equipo de soporte de voz, si ya se han sugerido) en el [foro de catálogo de datos de Azure](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).

## <a name="q-how-is-azure-data-catalog-related-to-the-data-catalog-in-power-bi-for-office-365"></a>P: ¿cómo está relacionado con el catálogo de datos de Azure en el catálogo de datos de Power BI para Office 365?

Puede considerar del catálogo de datos de Azure como una evolución del catálogo de datos. Catálogo de datos de Azure ofrece capacidades similares de publicación de origen de datos y detección, pero es que tiene el foco en escenarios más amplios y no depende de Office 365. Poco después de que el catálogo de datos de Azure esté disponible, en general, los dos catálogos combinará en un único servicio.

## <a name="q-what-permissions-does-a-user-need-to-register-assets-with-azure-data-catalog"></a>P: ¿qué permisos necesita un usuario registrar activos con el catálogo de datos de Azure?

El usuario que ejecuta la herramienta de registro de catálogo de datos de Azure necesita permisos en el origen de datos que le permitirá leer los metadatos desde el origen. Si el usuario selecciona también para incluir una vista previa, el usuario también debe tener permisos para leer en los datos de los objetos que se registra.

## <a name="q-will-azure-data-catalog-be-made-available-for-on-premises-deployment-as-well"></a>¿P: catálogo de datos de Azure estará disponible para su implementación local también?

Catálogo de datos de Azure es un servicio de nube que puede trabajar con orígenes de datos de nube y locales, ofrecer una solución de detección de origen de datos híbrido. Actualmente no existen planes para una versión del servicio de catálogo de datos de Azure que se ejecutará en local.

##<a name="q-can-we-extract-more--richer-metadata-from-the-data-sources-we-register"></a>P: ¿podemos extraer metadatos más / más completo de los orígenes de datos que se registrar?

Estamos trabajando actualmente para expandir las capacidades del catálogo de datos de Azure. Si hay metadatos adicionales que desea que vean extraída del origen de datos durante el registro, recomendamos póngase en el [foro de catálogo de datos de Azure](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409)(o a voto para él si ya se han sugerido). En el futuro le permitirá terceros agregar nuevos tipos de origen de datos a través de una capacidad de ampliación de API.

## <a name="q-how-do-i-restrict-the-visibility-of-registered-data-assets-so-that-only-certain-people-can-discover-them"></a>P: ¿cómo restringir la visibilidad de los activos de datos registrados, para que solo determinadas personas puedan detectar ellos?

R: seleccionar los activos de datos en el catálogo de datos de Azure y haga clic en el botón "Tomar propiedad". Los propietarios de activos de datos del catálogo de datos de Azure pueden cambiar la configuración de visibilidad, para permitir que todos los usuarios de catálogo para descubrir los activos de propiedad, o para restringir la visibilidad a usuarios específicos.

## <a name="q-how-do-i-update-the-registration-for-a-data-asset-to-that-changes-in-the-data-source-are-reflected-in-the-catalog"></a>P: ¿cómo se puede actualizar el registro de un activo de datos para que los cambios en el origen de datos se reflejan en el catálogo?

R: para actualizar los metadatos de los activos de datos que ya están registradas en el catálogo, simplemente volver a registrar el origen de datos que contiene los activos. Los cambios en el origen de datos como columnas que se agrega o quita tablas o vistas, se actualizarán en el catálogo, pero se mantendrán las anotaciones proporcionadas por los usuarios.

## <a name="q-my-question-isnt-answered-here--what-should-i-do"></a>P: mi pregunta no se responde aquí: ¿qué debo hacer?

Encabezado de sobre en el [foro de catálogo de datos de Azure](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409). Preguntas más frecuentes hay integrarán aquí.

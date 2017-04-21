<properties
   pageTitle="Escenarios comunes de catálogo de datos Azure | Microsoft Azure"
   description="Información general sobre escenarios comunes para Azure catálogo de datos, incluido el registro de detección de orígenes de datos de gran valor, habilitación de inteligencia empresarial de autoservicio y capturar conocimientos tribal acerca de orígenes de datos y procesos."
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
   ms.date="10/03/2016"
   ms.author="maroche"/>


# <a name="azure-data-catalog-common-scenarios"></a>Escenarios comunes de Azure catálogo de datos

Este artículo presenta escenarios comunes donde catálogo de datos de Azure puede ayudar a las organizaciones a obtener más valor de sus orígenes de datos existentes.

## <a name="scenario-1---registration-of-central-data-sources"></a>Escenario #1: registro de orígenes de datos central

A menudo, las organizaciones tiene un número de orígenes de datos de gran valor. Estos orígenes de datos incluyen línea de sistemas de empresa OLTP, datawarehouses y business intelligence y bases de datos de análisis. A menudo se aumenta el número de sistemas y la superposición entre los sistemas, con el tiempo como las necesidades de evolucionar de empresa y, a continuación, como el negocio propio evoluciona a través de fusión y adquisiciones.

A menudo es difícil para los usuarios saber dónde buscar los datos en estos orígenes de datos. Están muy frecuentes preguntas como estas:

- ¿De los sistemas de recursos humanos tres utilizados dentro de la empresa, que debo usar para crear este tipo de informe?
- ¿Dónde puedo para obtener las cifras de ventas certificadas para el año fiscal que acaba de finalizar?
- ¿Quién debo solicitar o proceso que debo usar para obtener acceso al almacén de datos?
- ¿No sé si son adecuados, estos números a quién puedo preguntar para información detallada acerca de cómo estos datos debe usarse antes de compartir este panel con mi equipo?

En este escenario, puede ayudar catálogo de datos de Azure. Los orígenes de datos administrado por TI, central, alto valor que se usan en toda la organización suelen ser el punto de partida lógico para rellenar el catálogo. Aunque cualquier usuario puede registrar un origen de datos, con el catálogo de kick-started con los orígenes de datos que están más probable que proporciona el valor para el mayor número de usuarios ayudará a impulsar la adopción y el uso del sistema. Introducción a Azure catálogo de datos de los clientes, identificar y registrar los orígenes de datos clave utilizados muchos equipos diferentes de los consumidores de datos pueden ser el primer paso para tener éxito.

Este escenario también presenta una oportunidad para realizar anotaciones en los orígenes de datos de gran valor para que resulten fáciles de entender y tener acceso. Un aspecto clave de este esfuerzo es incluir información sobre cómo los usuarios pueden solicitar acceso al origen de datos. Catálogo de datos de Azure permite a los usuarios a la dirección de correo electrónico del usuario o grupo responsable de controlar el acceso a datos de origen, vínculos a herramientas existentes o documentación o texto sin formato que describe el proceso de solicitud de acceso. Con esta información en el catálogo, los usuarios que ver los orígenes de datos registrados, pero que todavía no tiene permisos para tener acceso a los datos pueden solicitar fácilmente mediante los procesos define y controla los propietarios de origen de datos de access.

## <a name="scenario-2---self-service-business-intelligence"></a>Escenario #2: inteligencia empresarial

Aunque se continuarán soluciones de inteligencia empresarial corporativa tradicional a ser una parte valiosa de los entornos de datos de muchas de las organizaciones, el cambio ritmo de empresa realizó BI de autoservicio más importantes. BI de autoservicio permite trabajadores de la información y los analistas crear sus propios informes, libros y paneles sin confiar en un equipo de TI central – o está restringido por la programación y la disponibilidad de ese equipo de TI.

En escenarios de BI de autoservicio, es comunes a los usuarios para combinar datos de varios orígenes, muchos de los cuales pueden no anteriormente se han utilizado para BI y análisis. Aunque pueden que ya se conoce algunos de estos orígenes de datos, con frecuencia es un proceso para descubrir qué debe realizar para buscar y evaluar posibles orígenes de datos para una tarea determinada.

Tradicionalmente, este proceso de detección es un manual: analistas utilizará sus conexiones de red del mismo nivel para identificar otras personas que trabajan con los datos que se buscan. Una vez que se encuentra un origen de datos se puede usar, pero el proceso se repetirá nuevamente para cada subsiguientes autoservicio BI esfuerzo, con varios usuarios que realizan el mismo proceso manual redundante de detección.

Con el catálogo de datos de Azure, los usuarios pueden interrumpir este ciclo de esfuerzo redundante. Una vez que se ha detectado un origen de datos a través de los medios tradicionales, un analista puede registrar el origen de datos para que sea más fácilmente detectable en el futuro. Aunque el usuario puede agregar más valor anotando los activos de datos registrados, no es necesario realizar al mismo tiempo que el registro. Los usuarios pueden contribuir con el tiempo, como su permiso de programaciones gradualmente agregar valor a los orígenes de datos registrados en el catálogo.

Este crecimiento ecológica del contenido del catálogo es un complemento natural al registro inicial de orígenes de datos central. Llenar el catálogo de datos que necesitarán muchos usuarios puede ser un factor de motivación para uso inicial y detección. Permitir a los usuarios registrar y realizar anotaciones en fuentes adicionales puede ser una manera de mantener y sus colegas, atención.

También es importante recordar que aunque este escenario se centra específicamente en BI de autoservicio, el mismo tramas y desafíos se aplican a gran escala proyectos corporativos de BI también. Cualquier esfuerzo que llevará a cabo un proceso manual de detección de origen de datos es un esfuerzo que puede agregar valor a la organización mediante el uso de catálogo de datos de Azure.

## <a name="scenario-3---capturing-tribal-knowledge"></a>Escenario #3 - conocimiento tribal captura

¿Cómo sé qué datos debe realizar su trabajo y dónde encontrar esos datos?

Si ya ha su trabajo durante un tiempo, probablemente sabe. Ha experimentado gradual proceso de aprendizaje y el tiempo que ha aprendido acerca de los orígenes de datos que son clave para el trabajo del día a día.

Cuando su equipo une a un nuevo empleado, ¿cómo sabe qué datos que necesita para hacer su trabajo y dónde se encuentran?

Lo más probable es que le pide que.

Transferencia de este curso de conocimiento tribal forma parte del proceso de descubrimiento de origen de datos en empresas grandes y pequeñas. Miembros del equipo más altos y experimentados han creado una conocimientos de los años y, a continuación, han aprendido nuevos miembros del equipo y pregúntele cuando tengan preguntas. La información esencial más a menudo sólo existe en los jefes de unas pocas personas claves y, a continuación, cuando las personas están de vacaciones o dejar el equipo, la organización se ve afectado.

A veces estos expertos de datos hará que el esfuerzo para documentar sus conocimientos, compartirlo mediante correo electrónico o en documentos de Word en un sitio de SharePoint del grupo. Aunque puede resultar útil, que presenta un problema de detección nuevo: ¿cómo personas saber qué documentación existe y dónde se encuentran...

Catálogo de datos de Azure proporciona una ubicación para compartir este conocimiento tribal y para realizar fácilmente detectable. Expertos de datos pueden anotar directamente los activos de datos y también pueden incluir vínculos a la documentación existente. No sólo se captura el conocimiento propia, pero también coloca el conocimiento de la misma experiencia que se usa para la detección de origen de datos. Cuando alguien utiliza el catálogo de encontrar un origen de datos no solo tendrá buscar el propio origen, también encontrará conocimientos del experto que ya existía previamente solo en la cuenta del experto sí mismo.

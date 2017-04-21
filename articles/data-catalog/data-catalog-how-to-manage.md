<properties
   pageTitle="Cómo administrar activos de datos | Microsoft Azure"
   description="Artículo de procedimiento para resaltar cómo controlar la visibilidad y la propiedad de activos de datos registrados en el catálogo de datos de Azure."
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


# <a name="how-to-manage-data-assets"></a>Cómo administrar activos de datos

## <a name="introduction"></a>Introducción

**Catálogo de datos de Azure** ofrece capacidades de descubrimiento de origen de datos, permitir a los usuarios descubrir y comprender los orígenes de datos que necesitan para realizar análisis y tomar decisiones fácilmente. Estas capacidades de descubrimiento obtener el mayor impacto cuando todos los usuarios puedan encontrar y entender la más amplia variedad de orígenes de datos disponibles. Con esto en mente, el comportamiento predeterminado del catálogo de datos es para que todos los orígenes de datos registrados sean visibles para: y reconocible por: todos los usuarios del catálogo.

Catálogo de datos no dar a los usuarios acceso a los datos. Acceso a datos está controlado por el propietario del origen de datos. Catálogo de datos permite a los usuarios para ver los orígenes de datos y ver los metadatos de los orígenes registrados en el catálogo.

Puede haber situaciones, sin embargo, donde los orígenes de datos solo deben ser visibles para usuarios específicos, o a los miembros de grupos específicos. Para estos escenarios, catálogo de datos permite a los usuarios asumir la propiedad de activos de datos registrados en el catálogo y al control, a continuación, la visibilidad de los activos que poseen.

> [AZURE.NOTE] La funcionalidad que se describe en este artículo sólo están disponibles en la edición estándar del catálogo de datos de Azure. La edición gratuita no ofrece capacidades de propiedad y restringir visibilidad de activos de datos.

## <a name="managing-ownership-of-data-assets"></a>Administrar la propiedad de activos de datos
De forma predeterminada, los activos de datos registrados en el catálogo de datos están sin propietario; cualquier usuario con permisos para tener acceso al catálogo puede detectar y realizar anotaciones en dichos activos. Los usuarios pueden asumir la propiedad de activos de datos sin propietario y, a continuación, pueden limitar la visibilidad de los activos poseen.

Cuando la propiedad de un activo de datos en el catálogo de datos, solo los usuarios autorizados por los propietarios pueden detectar el activo y ver sus metadatos y solo los propietarios pueden eliminar los activos del catálogo.

> [AZURE.NOTE] La propiedad del catálogo de datos solo afecta a los metadatos que se almacenan en el catálogo. No otorgar permisos en el origen de datos subyacente.

### <a name="taking-ownership"></a>Asumir la propiedad
Los usuarios pueden asumir la propiedad de activos de datos, seleccione la opción "Tomar propiedad" en el portal de catálogo de datos. Se necesita ningún permiso especial para asumir la propiedad de un activo de datos sin propietario; cualquier usuario puede asumir la propiedad de un activo de datos sin propietario.

### <a name="adding-owners-and-co-owners"></a>Agregar los propietarios y los propietarios de compañeros
Si ya posee un activo de datos, los usuarios simplemente no pueden apropiarse: debe agregarse como propietarios con un propietario existente. Cualquier propietario puede agregar usuarios o grupos de seguridad adicionales como propietarios compañeros.

> [AZURE.NOTE] Es recomendable tener al menos dos personas como propietarios de los activos de datos de propietario.

### <a name="removing-owners"></a>Quitar los propietarios
Igual que cualquier propietario de activos puede agregar compañeros propietarios, cualquier propietario puede quitar cualquier copropietario.

Si un propietario de activos se quita a sí mismo como propietario, ya no puede administrar el activo. Si quita un propietario activo sí mismo como propietario y no hay ningún otros compañeros propietarios, activo volverá a un estado sin propietario.

## <a name="visibility"></a>Visibilidad
Los propietarios de activos de datos pueden controlar la visibilidad de los activos de datos que poseen. Para restringir la visibilidad del valor predeterminado: donde todos los usuarios del catálogo de datos pueden detectar y ver datos activos: el propietario de activos puede alternar la configuración de visibilidad de "Todos" a "Propietarios & estos usuarios" en las propiedades del activo. Propietarios, a continuación, pueden agregar usuarios y grupos de seguridad específicos.

> [AZURE.NOTE] Siempre que sea posible, los permisos de la propiedad y visibilidad de activos deben asignarse a grupos de seguridad y no a los usuarios individuales.

## <a name="catalog-administrators"></a>Administradores de catálogo
Los administradores del catálogo de datos son implícitamente con los propietarios de todos los activos en el catálogo. Los propietarios de activos no pueden eliminar la visibilidad de los administradores de catálogo y los administradores pueden administrar la propiedad y visibilidad para todos los activos de datos en el catálogo.

## <a name="summary"></a>Resumen
Modelo de crowdsourcing del catálogo de datos para la detección de activos de datos y metadatos permite a los usuarios catálogo todos de colaboración y descubrir. La edición estándar del catálogo de datos ofrece capacidades de administración y la propiedad limitar la visibilidad y el uso de los activos de datos específicos.

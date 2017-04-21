<properties
   pageTitle="Terminología de catálogo de datos de Azure | Microsoft Azure"
   description="Este artículo proporciona una introducción a los conceptos y términos usados en la documentación del catálogo de datos de Azure."
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

# <a name="azure-data-catalog-terminology"></a>Terminología de catálogo de datos de Azure

## <a name="catalog"></a>Catálogo

El catálogo de datos de Azure es un repositorio de metadatos basado en la nube en los datos que se pueden registrar orígenes y datos activos. El catálogo sirve como una ubicación de almacenamiento central de metadatos estructural extraídos de orígenes de datos y metadatos descriptivos agregados por los usuarios.

## <a name="data-source"></a>Origen de datos

Un origen de datos es un sistema o un contenedor que administra activos de datos. Por ejemplo, las bases de datos de SQL Server, bases de datos Oracle, bases de datos de SQL Server Analysis Services (tabulares o multidimensionales) y los servidores de SQL Server Reporting Services.

## <a name="data-asset"></a>Datos activos

Activos de datos son objetos dentro de los orígenes de datos que se pueden registrar con el catálogo. Informes de SQL Server Reporting Services y tablas de SQL Server y vistas, Oracle tablas y vistas, SQL Server Analysis Services medidas, dimensiones y KPI algunos ejemplos.

## <a name="data-asset-location"></a>Ubicación de activos de datos

El catálogo almacena la ubicación de un origen de datos o datos activos, que se pueden utilizar para conectarse al origen con una aplicación de cliente. El formato y los detalles de la ubicación varían según el tipo de origen de datos. Por ejemplo, una tabla de SQL Server puede identificarse por su nombre de cuatro partes: nombre del servidor, base de datos, nombre de esquema, nombre de objeto: mientras un informe de SQL Server Reporting Services pueden identificarse mediante su dirección URL.

## <a name="structural-metadata"></a>Metadatos estructurales

Metadatos estructurales son los metadatos extraídos de un origen de datos que describa la estructura de un activo de datos. Esto incluye la ubicación de activos, su nombre de objeto y el tipo y las características específicas de tipo. Por ejemplo, los metadatos de tablas y vistas estructurales incluyen los nombres y tipos de datos para las columnas del objeto.

## <a name="descriptive-metadata"></a>Metadatos descriptivos

Metadatos descriptivos son los metadatos que describa la finalidad o el propósito de un activo de datos. Normalmente se agregan metadatos descriptivos por usuarios de catálogo con el portal de catálogo de datos de Azure, pero también se puede extraer del origen de datos durante el registro. Por ejemplo, la herramienta de registro de catálogo de datos de Azure extraerá descripciones de la propiedad descripción en SQL Server Analysis Services y SQL Server Reporting Services y de la [propiedad extendida de ms_description](https://technet.microsoft.com/library/ms190243.aspx) en bases de datos de SQL Server, si estas propiedades se han rellenado con valores.

## <a name="request-access"></a>Solicitar acceso

Metadatos descriptivo del activo de datos pueden incluir información sobre cómo solicitar acceso a los activos de datos o el origen de datos. Esta información se presenta con la ubicación de activos de datos y puede incluir una o varias de las siguientes opciones:

- La dirección de correo electrónico del usuario o grupo responsable de conceder acceso al origen de datos.
- La dirección URL del proceso documentado que los usuarios deben seguir para obtener acceso al origen de datos.
- La dirección URL de una identidad y acceso herramienta de administración (como Microsoft identidad Manager) que se pueden usar para tener acceso al origen de datos.
- Entrada de texto libre que describe cómo los usuarios pueden tener acceso al origen de datos.

## <a name="preview"></a>Vista previa

Una vista previa en el catálogo de datos de Azure es una instantánea de hasta 20 registros que puede extraer desde el origen de datos durante el registro y almacenados en el catálogo con los metadatos de activos de datos. La vista previa puede ayudar a los usuarios que descubra un datos activos entiendan mejor su función y propósito. En otras palabras, ver los datos de ejemplo puede ser más valioso que ver solo los nombres de columna y los tipos de datos.
Las vistas previas sólo son compatibles con tablas y vistas y deben estar activadas explícitamente por el usuario durante el registro.

## <a name="data-profile"></a>Perfil de datos

Un perfil de datos en el catálogo de datos de Azure es una instantánea de la tabla y columna nivel metadatos acerca de un activo de datos registrados que puede extraer desde el origen de datos durante el registro y almacenado en el catálogo con los metadatos de activos de datos. El perfil de datos puede ayudar a los usuarios que descubra un datos activos entiendan mejor su función y propósito. De forma similar a las vistas previas, perfiles de datos deben seleccionarse explícitamente por el usuario durante el registro.

> [AZURE.NOTE] Extraer un perfil de datos puede ser una operación costosa para las vistas y tablas de gran tamaño y puede aumentar considerablemente el tiempo necesario para registrar un origen de datos.

## <a name="user-perspective"></a>Perspectiva del usuario

En el catálogo de datos de Azure, cualquier usuario puede proporcionar metadatos descriptivos para un activo de datos registrados. Cada usuario tiene una perspectiva distinta en los datos y su uso. Por ejemplo, el Administrador de un servidor puede proporcionar los detalles de su contrato de nivel de servicio (SLA) o una copia de seguridad windows; un administrador de datos puede proporcionar vínculos a la documentación para el negocio procesa datos admite; y un analista puede proporcionar una descripción de los términos que sean más relevantes para otros analistas y que puede ser más valioso para aquellos usuarios que necesitan para descubrir y comprender los datos.

Cada una de estas perspectivas son inherente valiosos y con el catálogo de datos de Azure cada usuario puede proporcionar la información que es significativa en ellos, mientras que todos los usuarios pueden utilizar esa información para comprender los datos y su finalidad.

## <a name="expert"></a>Experto

Un experto es un usuario que se ha identificado como tener una perspectiva "experta" informada de un activo de datos. Cualquier usuario puede agregar a sí mismos o a otro usuario como un experto de un activo. Se muestra como un experto no transmitir privilegios adicionales en el catálogo de datos de Azure; permite a los usuarios encontrar fácilmente las perspectivas que suelen ser útil cuando se revisan los metadatos descriptivo de un activo.

## <a name="owner"></a>Propietario

Un propietario es un usuario que tiene los privilegios adicionales de administración de un activo de datos en el catálogo de datos de Azure. Los usuarios pueden asumir la propiedad de activos de datos registrados y propietarios pueden agregar otros usuarios como propietarios de compañeros. Para obtener más información, consulte [cómo administrar activos de datos](data-catalog-how-to-manage.md)  
> [AZURE.NOTE] La propiedad y administración sólo están disponibles en la edición estándar del catálogo de datos de Azure.

## <a name="registration"></a>Registro

Registro es el acto de extracción de metadatos de activos de datos desde un origen de datos y copiarla en el servicio de catálogo de datos de Azure. Pueden anotar los activos de datos que se han registrado y descubren.

## <a name="see-also"></a>Vea también

- [¿Qué es el catálogo de datos de Azure?](data-catalog-what-is-data-catalog.md) -Este artículo proporciona información general sobre el servicio de catálogo de datos de Azure, el valor que se proporciona y los escenarios que admite.

- [Empezar a trabajar con el catálogo de datos de Azure](data-catalog-get-started.md) : en este artículo se proporciona un tutorial to-end que muestra cómo usar el catálogo de datos de Azure para detección de origen de datos.  

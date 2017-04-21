<properties
   pageTitle="Cómo conectarse a orígenes de datos | Microsoft Azure"
   description="Artículo de procedimiento para resaltar cómo conectarse a orígenes de datos detectados con el catálogo de datos de Azure."
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
   ms.date="09/15/2016"
   ms.author="maroche"/>


# <a name="how-to-connect-to-data-sources"></a>Cómo conectarse a orígenes de datos

## <a name="introduction"></a>Introducción
**Catálogo de datos de Microsoft Azure** es un servicio de nube totalmente administrado que sirve como un sistema de registro y sistema de detección de orígenes de datos de la empresa. En otras palabras, **El catálogo de datos de Azure** consiste en ayudar a las personas descubrir, comprender y usar orígenes de datos y ayudar a las organizaciones a obtener más valor de sus datos. Un aspecto clave de este escenario está usando los datos: cuando un usuario descubre un origen de datos y comprende su finalidad, el siguiente paso es para conectarse al origen de datos para poner sus datos que quiera usar.

## <a name="data-source-locations"></a>Ubicaciones de origen de datos
Durante el registro del origen de datos, el **Catálogo de datos de Azure** recibe metadatos sobre el origen de datos. Estos metadatos incluyen los detalles de la ubicación del origen de datos. Varían los detalles de la ubicación de origen de datos al origen de datos, pero siempre contendrá la información necesaria para conectarse. Por ejemplo, la ubicación de una tabla de SQL Server incluye el nombre del servidor, nombre de la base de datos, nombre de esquema y nombre de tabla, mientras que la ubicación de un informe de SQL Server Reporting Services incluye el nombre del servidor y la ruta de acceso al informe. Otros tipos de orígenes de datos tendrá ubicaciones que reflejan la estructura y las capacidades del sistema de origen.

## <a name="integrated-client-tools"></a>Herramientas de cliente integrado
La manera más sencilla de conectarse a un origen de datos es usar el "abrir en..." menú en el portal de **Catálogo de datos de Azure** . Este menú muestra una lista de opciones para conectarse a los activos de datos seleccionados.
Cuando se usa la vista en mosaico de forma predeterminada, este menú está disponible en el mosaico cada.

 ![Abrir una tabla de SQL Server en Excel desde el mosaico de activos de datos](./media/data-catalog-how-to-connect/data-catalog-how-to-connect1.png)

Al utilizar la vista de lista, el menú está disponible en la barra de búsqueda en la parte superior de la ventana del portal.

 ![Abrir un informe de SQL Server Reporting Services en el Administrador de informes desde la barra de búsqueda](./media/data-catalog-how-to-connect/data-catalog-how-to-connect2.png)

## <a name="supported-client-applications"></a>Aplicaciones cliente compatibles
Cuando se usa el "abrir en..." menú de orígenes de datos en el portal de catálogo de datos de Azure, la aplicación cliente correcto debe estar instalado en el equipo cliente.

| Abrir en la aplicación | Extensión de archivo y protocolo | Versiones de aplicación compatibles |
| --- | --- | --- |
| Excel | .odc | Excel 2010 o posterior |
| Excel (superior 1000) | .odc | Excel 2010 o posterior |
| Power Query | .xlsx | Excel 2016 o Excel 2010 o Excel 2013 con Power Query para el complemento de Excel instalada
| Power BI Desktop | .pbix | Power BI Desktop julio 2016 o posterior |
| Herramientas de datos SQL Server | vsweb: / / | Actualización de Visual Studio 2013 4 o posterior con herramientas de SQL Server instalado |
| Administrador de informes | http:// | Consulte [requisitos de explorador para SQL Server Reporting Services](https://technet.microsoft.com/en-us/library/ms156511.aspx) |

## <a name="your-data-your-tools"></a>Los datos, las herramientas
Las opciones disponibles en el menú dependerá del tipo de datos activos seleccionado actualmente. Por supuesto, no todas las herramientas posibles se incluirán en la "abrir en..." menú, pero es fácil conectarse al origen de datos con una herramienta de cliente. Cuando se selecciona un activo de datos en el portal de **Catálogo de datos de Azure** , se muestra la ubicación completa en el panel Propiedades.

 ![Información de conexión para una tabla de SQL Server](./media/data-catalog-how-to-connect/data-catalog-how-to-connect3.png)

Detalles de la información de conexión difieren de tipo de origen de datos al tipo de origen de datos, pero la información incluida en el portal le proporcionará todo lo que necesita para conectarse al origen de datos en una herramienta de cliente. Los usuarios pueden copiar los detalles de la conexión de los orígenes de datos que se han descubierto con el **Catálogo de datos de Azure**, lo que les permite trabajar con los datos en su herramienta de elección.

## <a name="connecting-and-data-source-permissions"></a>Permisos de origen de datos y conectar
Aunque **El catálogo de datos de Azure** hace orígenes de datos reconocible, acceso a los datos en sí permanece en el control del propietario de origen de datos o el administrador. Descubrir un origen de datos en el **Catálogo de datos de Azure** no conceder a un usuario permisos para tener acceso al origen de datos propio.

Para que sea más fácil para los usuarios que descubra un origen de datos, pero no tiene permiso para tener acceso a sus datos, los usuarios pueden proporcionar información de la propiedad de solicitud de acceso al anotar un origen de datos. La información proporcionada, incluidos los vínculos al proceso o punto de contacto para obtener acceso al origen de datos: se presenta junto con la información de ubicación de origen de datos en el portal.

 ![Información de conexión con las instrucciones de acceso de solicitud proporcionados](./media/data-catalog-how-to-connect/data-catalog-how-to-connect4.png)

##<a name="summary"></a>Resumen
Cómo registrar un origen de datos con el **Catálogo de datos de Azure** asegura de que los datos reconocible copiando estructurales y descriptivo metadatos del origen de datos en el servicio de catálogo. Una vez un origen de datos ha registrado y descubren, los usuarios pueden conectarse al origen de datos desde el portal de **Catálogo de datos de Azure** "abrir en..." " menú o utilizando las herramientas de datos de elección.

## <a name="see-also"></a>Vea también
- [Empezar a trabajar con el catálogo de datos de Azure](data-catalog-get-started.md) tutorial de instrucciones paso a paso acerca de cómo conectarse a orígenes de datos.

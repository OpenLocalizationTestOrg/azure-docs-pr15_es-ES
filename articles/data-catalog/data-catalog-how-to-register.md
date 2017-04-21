<properties
   pageTitle="Cómo registrar los orígenes de datos | Microsoft Azure"
   description="Artículo de procedimiento para resaltar cómo registrar los orígenes de datos con el catálogo de datos de Azure, incluidos los campos de metadatos que se extraen durante el registro."
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


# <a name="how-to-register-data-sources"></a>Cómo registrar los orígenes de datos

## <a name="introduction"></a>Introducción
**Catálogo de datos de Microsoft Azure** es un servicio de nube totalmente administrado que sirve como un sistema de registro y sistema de detección de orígenes de datos de la empresa. En otras palabras, **El catálogo de datos de Azure** consiste en ayudar a las personas descubrir, comprender y usar orígenes de datos y ayudar a las organizaciones a obtener más valor de sus datos. Y es el primer paso para hacer que un origen de datos reconocible a través del **Catálogo de datos de Azure** registrar ese origen de datos.
## <a name="registering-data-sources"></a>Registrar los orígenes de datos
Registro es el proceso de extracción de metadatos del origen de datos y copiar datos en el servicio de **Catálogo de datos de Azure** . Los datos permanecen en el que actualmente se encuentra y permanece en el control de los administradores y directivas del sistema actual.

Para registrar un origen de datos, simplemente inicie la herramienta de registro del origen de datos de **Catálogo de datos de Azure** desde el portal de **Catálogo de datos de Azure** . Inicie sesión con su trabajo o cuenta de escuela (las mismas credenciales de Azure Active Directory que utiliza para iniciar sesión en el portal) y, a continuación, seleccione el origen de datos que desea registrar.
Consulte el tutorial de [Introducción con el catálogo de datos de Azure](data-catalog-get-started.md) para obtener más detalles paso a paso.

Una vez que se haya registrado el origen de datos, el catálogo de seguimiento de su ubicación e índices sus metadatos, para que los usuarios puedan buscar, examinar y descubrir el origen de datos y, a continuación, usar su ubicación para conectarse a ella mediante la aplicación o la herramienta de su elección.

## <a name="sources-supported"></a>Fuentes compatibles
Consulte [DLV del catálogo de datos](data-catalog-dsr.md) para la lista de orígenes de datos compatibles actualmente.
<br/>


## <a name="structural-metadata"></a>Metadatos estructurales
Cuando se va a registrar un origen de datos, la herramienta de registro extraerá información sobre la estructura de los objetos seleccionados: se trata de metadatos que se hace referencia a como estructurales.

Para todos los objetos, estos metadatos estructurales incluye la ubicación del objeto, para que los usuarios que descubra los datos pueden utilizar esa información para conectar con el objeto de las herramientas de cliente de su elección. Otros metadatos estructural incluyen tipo y nombre de objeto y, a continuación, escriba el nombre de atributo o columna y los datos.

## <a name="descriptive-metadata"></a>Metadatos descriptivos
Además de los metadatos de core estructurales extraídos del origen de datos, la herramienta de registro de origen de datos también extrae metadatos descriptivos también. Para SQL Server Analysis Services y SQL Server Reporting Services, esto se toma de las propiedades de descripción expuestas por estos servicios. Para SQL Server, se extraerán valores proporcionados con la ms_description propiedad extendida. Para la base de datos de Oracle, la herramienta de registro de origen de datos extrae la columna comentarios desde la vista ALL_TAB_COMMENTS.

Además de los metadatos descriptivo extraídos del origen de datos, los usuarios también pueden especificar los metadatos descriptivos mediante la herramienta de registro de origen de datos. Los usuarios pueden agregar etiquetas y pueden identificar a expertos de los objetos que se registra. Todos estos metadatos descriptivo se copia en el servicio de **Catálogo de datos de Azure** junto con los metadatos estructural.

## <a name="including-previews"></a>Incluidas las vistas previas

De forma predeterminada, solo los metadatos extraídos de orígenes de datos y ha copiado en el servicio de **Catálogo de datos de Azure** , pero descripción de que un origen de datos a menudo es más fácil si ve una muestra de los datos que contiene.

La herramienta de registro de origen de datos de **Catálogo de datos de Azure** permite a los usuarios incluir una vista previa de instantánea de los datos en cada tabla y la vista que está registrada. Si el usuario opta en para incluir las vistas previas durante el registro, la herramienta de registro incluirá hasta 20 registros de cada tabla y la vista. Esta instantánea se copia en el catálogo junto con los metadatos estructurales y descriptivo.


> [AZURE.NOTE]  Amplia tablas con un gran número de columnas podrán que tenga menos de 20 registros incluidos en la vista previa.


## <a name="including-data-profiles"></a>Incluidos los perfiles de datos

Como vistas previas incluidas pueden proporcionar contexto valioso para los usuarios buscar orígenes de datos en el **Catálogo de datos de Azure**, incluidos un perfil de datos pueden también que sea más fácil de comprender los orígenes de datos detectados.

La herramienta de registro de origen de datos de **Catálogo de datos de Azure** permite a los usuarios incluir un perfil de datos para cada tabla y la vista que está registrada. Si el usuario decide incluir un perfil de datos durante el registro, la herramienta de registro incluirá estadísticas agregadas acerca de los datos de cada tabla y la vista, incluidos:

* El número de filas y el tamaño de los datos en el objeto
* La fecha de la actualización más reciente de los datos y el esquema de objeto
* El número de registros null y valores distintos para las columnas
* Los valores mínimo, máximo, Media y desviación estándar de columnas

Estas estadísticas se copian en el catálogo junto con los metadatos estructurales y descriptivo.

> [AZURE.NOTE]  Columnas de texto y de fecha no incluirá estadísticas promedio o desviación estándar en su perfil de datos.

## <a name="updating-registrations"></a>Actualizar registros

Cómo registrar un origen de datos hará que sea reconocible en el **Catálogo de datos de Azure** con los metadatos y opcional preview extraídos durante el registro. Si el origen de datos debe actualizarse en el catálogo (por ejemplo, si ha cambiado el esquema de un objeto, se deben incluidos tablas que originalmente se excluyen o un usuario que desea actualizar los datos incluidos en las vistas previas) se puede ejecutar de nuevo la herramienta de registro de origen de datos.

Volver a registrar un origen de datos ya haya registrado realiza una operación de combinación "upsert": objetos existentes se actualizarán mientras se crearán objetos nuevos. Se mantendrán los metadatos proporcionados por los usuarios a través del portal de **Catálogo de datos de Azure** .

## <a name="summary"></a>Resumen
Cómo registrar un origen de datos con el **Catálogo de datos de Azure** facilita ese origen de datos descubrir y comprender copiando estructurales y descriptivo metadatos del origen de datos en el servicio de catálogo. Una vez que se ha registrado un origen de datos, se pueden después anotar, administrada y detectados con el portal de **Catálogo de datos de Azure** .

## <a name="see-also"></a>Vea también
- [Empezar a trabajar con el catálogo de datos de Azure](data-catalog-get-started.md) tutorial de instrucciones paso a paso sobre cómo registrar los orígenes de datos.

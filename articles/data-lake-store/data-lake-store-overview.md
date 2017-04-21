<properties
   pageTitle="Introducción a Azure lago de almacén de datos | Microsoft Azure"
   description="Comprender qué es el almacén de lago de datos de Azure y el valor que se proporciona a través de otra almacena datos"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/28/2016"
   ms.author="nitinme"/>

# <a name="overview-of-azure-data-lake-store"></a>Introducción a Azure lago de almacén de datos

Almacén de lago de datos de Azure es un repositorio de gran escala en toda la empresa para cargas de trabajo de análisis de datos grande. Lago de datos de Azure le permiten capturar los datos de cualquier tamaño, tipo y recopilación de velocidad en un lugar único para el análisis de exploración y operativo.

> [AZURE.TIP] Usar el [almacén de datos de lago ruta de aprendizaje](https://azure.microsoft.com/documentation/learning-paths/data-lake-store-self-guided-training/) para empezar a explorar el servicio de almacenamiento de lago de datos de Azure.

Almacén de lago de datos de Azure puede tener acceso desde Hadoop (disponible con HDInsight clúster) mediante las API de REST compatible con WebHDFS. Está diseñada específicamente para habilitar el análisis de los datos almacenados y está optimizado para escenarios de análisis de datos de rendimiento. Fuera del cuadro, incluye todas las capacidades de nivel empresarial: seguridad, administración, escalabilidad, confiabilidad y disponibilidad: esenciales de casos de uso de la empresa del mundo real.


![Lago de datos de Azure](./media/data-lake-store-overview/data-lake-store-concept.png)

Algunas de las capacidades clave de lago de datos de Azure incluyen las siguientes.

### <a name="built-for-hadoop"></a>Diseñado para Hadoop

El almacén de lago de datos de Azure es un sistema de archivos Hadoop Apache compatible con el sistema de archivos distribuido Hadoop (HDFS) y trabaja con el ecosistema Hadoop.  Los servicios que utilizan la API WebHDFS y aplicaciones de HDInsight existentes pueden integrar fácilmente con el almacén de datos de lago. Almacén de datos de lago también expone una interfaz compatible con WebHDFS resto para aplicaciones

Los datos almacenados en el almacén de datos de lago se pueden analizar fácilmente con Hadoop marcos analíticos como MapReduce o subárbol. Clústeres de Microsoft Azure HDInsight pueden aprovisionar y configurar para tener acceso directo a los datos almacenados en el almacén de datos de lago.

### <a name="unlimited-storage-petabyte-files"></a>Almacenamiento ilimitado, archivos de petabyte

Almacén de lago de datos de Azure proporciona almacenamiento ilimitado y es adecuado para almacenar una amplia variedad de datos para el análisis. No impone los límites de tamaño de la cuenta, tamaños de archivo o la cantidad de datos que se pueden almacenar en un lago de datos. Archivos individuales pueden variar de kilobytes a petabytes en tamaño, lo que es una excelente opción para almacenar cualquier tipo de datos. Los datos se almacenan duradera realizando varias copias y no hay ningún límite en la duración de tiempo para que los datos se pueden almacenar en lago de datos.

### <a name="performance-tuned-for-big-data-analytics"></a>Rendimiento optimizado para análisis de datos grande

Almacén de lago de datos de Azure integrado para ejecutar sistemas analíticos que requieren gran rendimiento para consultar y analizar grandes cantidades de datos de gran escala. Lago datos reparte las partes de un archivo en un número de servidores de almacenamiento individuales. Esto mejora el rendimiento de lectura al leer el archivo en paralelo para realizar análisis de datos.


### <a name="enterprise-ready-highly-available-and-secure"></a>Para la empresa: Altamente disponible y segura

Almacén de lago de datos de Azure proporciona confiabilidad y disponibilidad de estándar de la industria. Los activos de datos se almacenan duradera haciendo copias redundantes para evitar los errores inesperados. Las empresas pueden utilizar lago de datos de Azure en sus soluciones como una parte importante de su plataforma de datos existente.

Almacén de datos de lago también proporciona seguridad de nivel empresarial para los datos almacenados. Para obtener más información, vea [proteger datos Azure almacén de datos lago](#DataLakeStoreSecurity).


### <a name="all-data"></a>Todos los datos

Almacén de lago de datos de Azure puede almacenar los datos en su formato nativo, tal cual, sin necesidad de las transformaciones anteriores. Almacén de datos de lago no requiere un esquema definirse antes de que los datos se cargan, salir hasta el marco analítico individual para interpretar los datos y definir un esquema en el momento del análisis. Para poder almacenar archivos de formatos y tamaños arbitrarios hace almacén de datos de lago controlen datos estructurados, semiestructurados y no estructurados.

Contenedores de Azure datos lago almacén de datos son, esencialmente, carpetas y archivos. Trabajar en los datos almacenados mediante SDK, Portal de Azure y Azure Powershell. Como poner los datos en el almacén mediante estas interfaces y los contenedores adecuados, puede almacenar cualquier tipo de datos. Almacén de datos de lago no realiza cualquier tratamiento especial de datos basándose en el tipo de datos que almacena.


## <a name="DataLakeStoreSecurity"></a>Proteger los datos en el almacén de lago de datos de Azure

Almacén de lago de datos de Azure usa Azure Active Directory para la autenticación y listas de control de acceso (ACL) para administrar el acceso a los datos.

| Característica                                 | Descripción                              |
|-----------------------------------------|------------------------------------------|
| Autenticación | Almacén de lago de datos de Azure se integra con Azure Active Directory (AAD) para la administración de identidades y acceso para todos los datos almacenados en el almacén de lago de datos de Azure. Como resultado de la integración de las ventajas de Azure datos lago de todas las características de AAD incluido autenticación multifactor, acceso condicional, control de acceso basado en roles, supervisión de uso de aplicaciones, seguridad, supervisión y alertas, etcetera. Almacén de lago de datos de Azure es compatible con el protocolo OAuth 2.0 para la autenticación con en la interfaz REST. |
| Control de acceso                          | Almacén de lago de datos de Azure proporciona control de acceso por compatibilidad con los permisos de estilo POSIX expuestos por el protocolo WebHDFS. En la versión actual, se pueden habilitar ACL en la carpeta raíz, las subcarpetas, así como archivos individuales. Las ACL que se aplica a la carpeta raíz mostrará también se aplica a todos los secundarios carpetas o archivos también.|

¿Desea obtener más información sobre cómo proteger los datos en el almacén de datos de lago. Siga los siguientes vínculos.

* Para obtener instrucciones sobre cómo proteger los datos en el almacén de datos de lago, vea [proteger datos Azure almacén de datos lago](data-lake-store-secure-data.md).
* ¿Prefiere vídeos? [Vea este vídeo](https://mix.office.com/watch/1q2mgzh9nn5lx) sobre cómo proteger los datos almacenados en el almacén de datos de lago.

## <a name="applications-compatible-with-azure-data-lake-store"></a>Aplicaciones compatibles con el almacén de lago de datos de Azure

Almacén de lago de datos de Azure es compatible con los componentes de origen más abierta en el ecosistema Hadoop. También integra satisfactoriamente con otros servicios de Azure. De esta forma al almacén de datos de lago una opción ideal para sus necesidades de almacenamiento de datos. Siga los siguientes vínculos para obtener más información acerca de cómo puede utilizarse el almacén de datos de lago tanto con componentes de código abierto, así como otros servicios de Azure.

* Para obtener una lista de aplicaciones de Abrir origen puede interactuar con el almacén de datos de lago, consulte [aplicaciones y servicios compatibles con el almacén de lago de datos de Azure](data-lake-store-compatible-oss-other-applications.md) .
* Vea la [integración con otros servicios de Azure](data-lake-store-integrate-with-other-services.md) a comprender cómo puede utilizarse almacén de datos de lago con otros servicios de Azure para habilitar una amplia gama de escenarios.
* Consulte [escenarios para utilizar el almacén de datos de lago](data-lake-store-data-scenarios.md) para aprender a usar el almacén de datos de lago en escenarios como recopila datos, procesamiento de datos, descargar los datos y visualizar datos.

## <a name="what-is-azure-data-lake-store-file-system-adl"></a>¿Qué es el sistema de archivos de almacenamiento de Azure datos lago (adl: / /)?

Almacén de datos de lago se puede acceder a través del nuevo sistema de archivos, el AzureDataLakeFilesystem (adl: / /), en entornos de Hadoop (disponibles con HDInsight clúster). Aplicaciones y servicios que utilizan adl: / / pueden sacar partido de aún más la optimización del rendimiento que no ya están disponibles en WebHDFS. Como resultado, almacén de datos de lago ofrece la flexibilidad a bien disponible el mejor rendimiento con la opción recomendada de usar adl: / / o mantener código existente al continuar usando la API de WebHDFS directamente. HDInsight de Azure aprovecha completamente el AzureDataLakeFilesystem para proporcionar el mejor rendimiento en el almacén de datos de lago.

Puede acceder a los datos en el almacén de datos lago mediante `adl://<data_lake_store_name>.azuredatalakestore.net`. Para obtener más información sobre cómo obtener acceso a los datos del almacén de datos lago, consulte [Ver propiedades de los datos almacenados](data-lake-store-get-started-portal.md#properties)

## <a name="how-do-i-start-using-azure-data-lake-store"></a>¿Cómo puedo comenzar a usar el almacén de lago de datos de Azure?

Vea [Introducción a la tienda de lago de datos con el Portal de Azure](data-lake-store-get-started-portal.md), sobre cómo aprovisionar un almacén de lago de datos con el Portal de Azure. Una vez que se ha suministrado lago de datos de Azure, aprenda cómo usar ofertas de datos grande como análisis de lago de datos de Azure o HDInsight de Azure con lago almacén de datos. También puede crear una aplicación .NET para crear una cuenta de la tienda de lago de datos de Azure y realizar operaciones como cargar datos, descargar datos, etcetera.

- [Introducción a análisis de datos de Azure lago](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Usar Azure HDInsight con el almacén de datos de lago](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Introducción a Azure datos lago tienda con .NET SDK](data-lake-store-get-started-net-sdk.md)


## <a name="data-lake-store-videos"></a>Vídeos de almacén de datos lago

Si prefiere ver vídeos para aprender, almacén de datos de lago proporciona vídeos en un rango de características.

* [Crear una cuenta de la tienda de lago de datos de Azure](https://mix.office.com/watch/1k1cycy4l4gen)
* [Use el Explorador de datos para administrar datos en el almacén de lago de datos de Azure](https://mix.office.com/watch/icletrxrh6pc)
* [Conectar el análisis de datos de Azure lago al almacén de lago de datos de Azure](https://mix.office.com/watch/qwji0dc9rx9k)
* [Almacén de lago de datos de Access Azure a través de análisis de datos lago](https://mix.office.com/watch/1n0s45up381a8)
* [Conectarse a HDInsight de Azure de lago almacén de datos de Azure](https://mix.office.com/watch/l93xri2yhtp2)
* [Almacén de lago de datos de Access Azure a través de la sección y cerdo](https://mix.office.com/watch/1n9g5w0fiqv1q)
* [Use DistCp (copia distribuido Hadoop) para copiar los datos del almacén de lago de datos de Azure](https://mix.office.com/watch/1liuojvdx6sie)
* [Usar Sqoop Apache para mover datos entre orígenes relacionales y el almacenamiento de lago de datos de Azure](https://mix.office.com/watch/1butcdjxmu114)
* [Orquestación de datos con el generador de datos de Azure para lago almacén de datos de Azure](https://mix.office.com/watch/1oa7le7t2u4ka)
* [Proteger los datos en el almacén de lago de datos de Azure](https://mix.office.com/watch/1q2mgzh9nn5lx)




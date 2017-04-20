<properties
   pageTitle="Cómo trabajar con orígenes de datos 'datos grande' | Microsoft Azure"
   description="Artículo de procedimiento para resaltar los patrones para usar el catálogo de datos de Azure con orígenes de datos 'datos grande', incluidos el almacenamiento de blobs de Windows Azure, lago de datos de Azure y HDFS Hadoop."
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


# <a name="how-to-work-with-big-data-sources-in-azure-data-catalog"></a>Cómo trabajar con orígenes de datos grande en el catálogo de datos de Azure

## <a name="introduction"></a>Introducción
**Catálogo de datos de Microsoft Azure** es un servicio de nube totalmente administrado que sirve como un sistema de registro y sistema de detección de orígenes de datos de la empresa. En otras palabras, **El catálogo de datos de Azure** es todo acerca de las personas de ayudar a descubrir, comprender y usar orígenes de datos y ayudar a las organizaciones a obtener más valor de sus orígenes de datos existentes, incluidos los datos grandes.

**Catálogo de datos de Azure** es compatible con el registro de almacenamiento de Blog de Azure BLOB y directorios, así como directorios y archivos de Hadoop HDFS. La naturaleza de estos orígenes de datos semiestructurada proporciona gran flexibilidad, pero también significa que los usuarios deben considerar cómo se organizan los orígenes de datos con el fin de obtener el mayor valor de registro de catálogo de **datos**de Azure.

## <a name="directories-as-logical-data-sets"></a>Directorios como lógica de conjuntos de datos.

Un modelo común para organizar los orígenes de datos grande es tratar directorios como conjuntos de datos lógicos. Directorios de nivel superior se usan para definir un conjunto de datos mientras subcarpetas definen particiones y, a continuación, los archivos que contienen almacenan los datos en Sí.

Un ejemplo de este modelo podría ser:

    \vehicle_maintenance_events
        \2013
        \2014
        \2015
            \01
                \2015-01-trailer01.csv
                \2015-01-trailer92.csv
                \2015-01-canister9635.csv
                ...
    \location_tracking_events
        \2013
        ...

En este ejemplo, vehicle_maintenance_events y location_tracking_events representan lógicos conjuntos de datos. Cada una de estas carpetas contiene los archivos de datos que se organizan por año y mes en subcarpetas. Cada una de estas carpetas podría contener cientos o miles de archivos.

En este modelo, registrar archivos individuales con el **Catálogo de datos de Azure** probablemente no tenga sentido. En su lugar, registrar los directorios que representan los conjuntos de datos que será significativos a los usuarios trabajar con los datos.

## <a name="reference-data-files"></a>Archivos de datos de referencia

Es un patrón complementario almacenar conjuntos de datos de referencia como archivos individuales. Estos conjuntos de datos puede considerarse como el lado 'pequeño' de datos grandes y con qué frecuencia son similares a las dimensiones en un modelo de datos analíticos. Archivos de datos de referencia contienen los registros que se utilizan para proporcionar contexto para la mayoría de los archivos de datos almacenados en otro lugar del almacén de datos grande.

Un ejemplo de este modelo podría ser:

    \vehicles.csv
    \maintenance_facilities.csv
    \maintenance_types.csv

Cuando un científico analista o los datos que está trabajando con los datos contenidos en las estructuras de directorio más grandes, los datos de estos archivos de referencia pueden utilizarse para proporcionar información más detallada sobre las entidades que se hace referencia a solo por nombre o el identificador del conjunto de datos más grande.

En este modelo, sentido registrar los archivos de datos de referencia individuales con el **Catálogo de datos de Azure**. Cada archivo representa un conjunto de datos, y pueden anotado y descubre de forma individual cada uno de ellos.

## <a name="alternate-patterns"></a>Tramas alternativos

Los modelos descritos anteriormente son sólo dos formas posibles que puede organizarse un almacén de datos grande, pero cada implementación será diferente. Independientemente de cómo están estructurados los orígenes de datos, al registrar los orígenes de datos grande en el **Catálogo de datos de Azure**, centrarse en el registro de los archivos y directorios que representan los conjuntos de datos que será de valor a otras personas de su organización. Registrar todos los archivos y directorios puede provocar confusión en el catálogo, lo que dificulta a los usuarios para encontrar lo que necesitan.

## <a name="summary"></a>Resumen
Registrar los orígenes de datos en el **Catálogo de datos de Azure** es más fácil descubrir y comprender. Mediante el registro y anotar los archivos de datos grande y directorios que representan lógicos conjuntos de datos, puede ayudar a los usuarios encontrar y utilizar los orígenes de datos grande que necesitan.

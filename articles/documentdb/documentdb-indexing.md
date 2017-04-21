<properties 
    pageTitle="Indización automática en DocumentDB | Microsoft Azure" 
    description="Obtenga información sobre los trabajos de indexación automática en DocumentDB de Azure." 
    services="documentdb" 
    authors="arramac" 
    manager="jhubbard" 
    editor="mimig" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/27/2016" 
    ms.author="arramac"/>
    
# <a name="automatic-indexing-in-azure-documentdb"></a>Automático indización en DocumentDB de Azure

En este artículo se ha seleccionado de la nota ["independiente del esquema de indización con DocumentDB Azure"](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) , que se presentará en la [Conferencia interna 41st en bases de datos muy grandes](http://www.vldb.org/2015/) entre 31 de agosto - 4 de septiembre de 2015, y es una introducción a cómo funciona en Azure DocumentDB la indexación. 

Después de leer esto, se responder las preguntas siguientes:

- ¿Cómo DocumentDB deducir el esquema de un documento JSON?
- ¿Cómo crear DocumentDB un índice en diferentes documentos?
- ¿Cómo DocumentDB realiza la indización automática a escala?

##<a id="HowDocumentDBIndexingWorks"></a>Cómo funciona la indización DocumentDB

[Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) es una base de datos libres de esquema true diseñado específicamente para JSON. No espera ni requerir cualquier esquema o definiciones de índice secundario a los datos de índice a escala. Esto le permite definir y trabajemos en modelos de datos de aplicación mediante DocumentDB rápidamente. Al agregar documentos a una colección, DocumentDB índices automáticamente todas las propiedades del documento para que estén disponibles para la consulta. La indización automática le permite almacenar los documentos que pertenecen a esquemas completamente arbitrarios sin preocuparse de esquemas o índices secundarios.

Con el objetivo para eliminar la diferencia de impedancia entre la base de datos y los modelos de programación de aplicaciones, DocumentDB aprovecha la simple de JSON y su falta de una especificación de esquema. No realiza suposiciones acerca de los documentos y permite que los documentos dentro de una colección de DocumentDB variar en esquema, además de los valores específicos de la instancia. A diferencia de otras bases de datos de documento, el motor de base de datos de DocumentDB funciona directamente en el nivel de gramática JSON, restante independiente para el concepto de un esquema de documento y desenfoque el borde entre los valores de la instancia y la estructura de documentos. Esto, a su vez, habilita automáticamente INDICE documenta sin necesidad de esquema o índices secundarios.

La indización en DocumentDB aprovecha el hecho de que la gramática JSON permite documentos que se **representan como árboles**. Para que un documento JSON para representarse como un árbol, es necesario crear que actúa como elemento principal el resto de los nodos reales en el documento debajo de un nodo raíz ficticias. Cada etiqueta incluidos los índices de matriz en un documento JSON se convierte en un nodo del árbol. La siguiente ilustración muestra un documento JSON de ejemplo y su correspondiente representación de árbol.

>[AZURE.NOTE] Dado que JSON es por sí mismo, es decir, cada documento incluye esquema (metadatos) y los datos, por ejemplo, `{"locationId": 5, "city": "Moscow"}` revela que hay dos propiedades `locationId` y `city`, y que tengan un valores de propiedad de cadena y numéricos. DocumentDB es capaz de deducir el esquema de documentos y crea un índice cuando se inserta o reemplaza, sin necesidad de nunca definir esquemas o índices secundarios.


**Documentos JSON como árboles:**

![Documentos como árboles](media/documentdb-indexing/DocumentsAsTrees.png)

Por ejemplo, en el ejemplo anterior:

- La propiedad JSON `{"headquarters": "Belgium"}` propiedad en el ejemplo anterior se corresponde con la ruta de acceso, sede/Bélgica.
- La matriz JSON `{"exports": [{"city": “Moscow"}`, `{"city": Athens"}]}` corresponde a las rutas de acceso `/exports/[]/city/Moscow` y `/exports/[]/city/Athens`.

Con automático indización (1) se indizan de todas las rutas en un árbol de documento (a menos que el programador ha configurado explícitamente la directiva de indexación para excluir determinados patrones de ruta de acceso). (2) cada actualización de un documento a una colección de DocumentDB de clientes potenciales para la actualización de la estructura del índice (es decir, la adición de causas o la eliminación de nodos). Uno de los requisitos principales de la indización automática de documentos es para asegurarse de que el costo para el índice y un documento con varios niveles de anidamiento estructura de la consulta, por ejemplo 10 niveles, igual que el de un documento JSON plana formada por pares de valor de clave de un solo nivel. Por lo tanto, una representación de la ruta de acceso normalizado es la base en el que se crean dos subsistemas de indexación y consulta automáticas.

Una de las implicaciones importante de ambas tratar los valores de esquema e instancia uniformemente en términos de rutas de acceso es que lógicamente, igual que los documentos individuales, un índice de los dos documentos que se muestra que mantiene un mapa entre rutas de acceso y los identificadores de documento que contiene la ruta de acceso también se pueden representar como un árbol. DocumentDB utiliza este hecho para crear un árbol de índice que se crea fuera de la unión de todos los árboles de documentos individuales dentro de la colección. El árbol de índice en colecciones de DocumentDB aumenta con el tiempo, como documentos nuevos obtengan agregados o actualizados a la colección.


**Índice de DocumentDB como un árbol:**

![Índice como un árbol](media/documentdb-indexing/IndexAsTree.png)

A pesar de ser libre de esquema, de DocumentDB SQL y JavaScript lenguajes de consulta proporcionan proyecciones relacionales y filtros, exploración jerárquica en documentos, operaciones espaciales e invocación de UDF escritos completamente en JavaScript. Tiempo de ejecución de consulta de DocumentDB es compatible con estas consultas, ya que funciona directamente con esta representación de árbol de índice de los datos.

La directiva predeterminada de indización automáticamente indiza todas las propiedades de todos los documentos y proporciona consultas coherentes (es decir, el índice se actualiza sincrónicamente con la escritura de documento). ¿DocumentDB es compatible con actualizaciones coherentes para el árbol de índice a escala? DocumentDB usa escritura optimizado, bloquear gratuita y registro estructurado técnicas de mantenimiento de índice. Esto significa que DocumentDB pueden admitir un volumen prolongado de escritura rápida mientras sigue atendiendo consultas coherentes. 

Indización de DocumentDB está diseñada para la eficacia de almacenamiento y controlar varios usuarios. Rentabilidad, la sobrecarga de almacenamiento en disco del índice es baja y predecible. También se realizan las actualizaciones del índice dentro del presupuesto de recursos del sistema asignados por colección de DocumentDB.

##<a name="NextSteps"></a>Pasos siguientes
- Descargar ["independiente del esquema de indización con DocumentDB Azure"](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf), se presente en la conferencia interna 41st muy grandes bases de datos, el 31 de agosto - 4 de septiembre de 2015.
- [Consulta con DocumentDB SQL](documentdb-sql-query.md)
- Obtenga más información sobre cómo personalizar el índice DocumentDB [aquí](documentdb-indexing-policies.md)
 

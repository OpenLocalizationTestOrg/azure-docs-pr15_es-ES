<properties 
    pageTitle="Generador de datos - reglas de nomenclatura | Microsoft Azure" 
    description="Describe las reglas de nomenclatura para entidades de generador de datos." 
    services="data-factory" 
    documentationCenter="" 
    authors="sharonlo101" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/12/2016" 
    ms.author="shlo"/>

# <a name="azure-data-factory---naming-rules"></a>Generador de datos de Azure - reglas de nomenclatura 
En la tabla siguiente proporciona las reglas de nomenclatura para artefactos del generador de datos.



Nombre | Nombres únicos | Comprobaciones de validación
:--- | :-------------- | :----------------
Generador de datos | Únicos a través de Microsoft Azure. Nombres distinguen mayúsculas de minúsculas, es decir, consulte la misma fábrica datos MyDF y mydf. |<ul><li>Cada generador de datos está ligada a exactamente una suscripción de Azure.</li><li>Nombres de objeto deben comenzar con una letra o un número y pueden contener sólo letras, números y el carácter de guión (-).</li><li>Cada carácter de guión (-) debe ser inmediatamente precedido y seguido por una letra o un número. Guiones consecutivos no se permiten en los nombres de contenedor.</li><li>Nombre puede tener 3-63 caracteres.</li></ul>
Vinculado servicios o tablas y canalizaciones | Único con en un generador de datos. Nombres distinguen mayúsculas de minúsculas. | <ul><li>Número máximo de caracteres en un nombre de tabla: 260.</li><li>Nombres de objeto deben comenzar con un número de letra o un carácter de subrayado (_).</li><li>Tras los caracteres no permitidos: ".", "+", "?", "/", "<", ">","*", "%", "&", ":","\\"</li></ul>
Grupo de recursos | Únicos a través de Microsoft Azure. Nombres distinguen mayúsculas de minúsculas. | <ul><li>Número máximo de caracteres: 1000.</li><li>Nombre puede contener letras, dígitos y los siguientes caracteres: "-", "_",","y".".</li></ul>
<properties
   pageTitle="Servicio de Azure tela en Linux | Microsoft Azure"
   description="Clústeres tela de servicio de soporte técnico Linux y Java, lo que significa que podrá implementar y aplicaciones de servicio tela host escritas en Java y C# en Linux."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="Java"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/26/2016"
   ms.author="SubramaR"/>

# <a name="service-fabric-on-linux"></a>Servicio tela en Linux

La vista previa de tela de servicio en Linux le permite crear, implementar y administrar aplicaciones de alta disponibilidad, alta escalabilidad en Linux como lo haría en Windows. Los marcos de servicio tela (servicios confiables y actores confiable) están disponibles en Java en Linux además de C# (.NET núcleo).  También puede crear [Servicios ejecutable de invitado](service-fabric-deploy-existing-app.md) con cualquier idioma o un marco. Además, la vista previa también es compatible con contenedores de organización Docker. Contenedores docker pueden ejecutar ejecutables de invitado o nativos servicios de tela de servicio, que usan los marcos tela de servicio.

Servicio tela en Linux es conceptualmente equivalente a tela de servicio de Windows (excepto para características específicas del sistema operativo y la compatibilidad con idiomas de programación). Por lo tanto, la mayor parte de nuestra [documentación existente](http://aka.ms/servicefabricdocs) se aplica ayudarle a familiarizarse con la tecnología.

> [AZURE.VIDEO service-fabric-linux-preview]

## <a name="supported-operating-systems-and-programming-languages"></a>Sistemas operativos compatibles y lenguajes de programación

La vista previa limitada admite la creación de clústeres de desarrollo de un solo cuadro además clústeres varios equipos en Azure ejecutan Ubuntu Server 16.04. La vista previa es compatible con los actores confiable y los marcos de servicios sin estado confiable en Java y C# además de invitado ejecutables y coordinar contenedores Docker.  

>[AZURE.NOTE] Colecciones confiables todavía no se admiten en Linux. No se admiten clústeres independientes de soporte-solo un cuadro y clústeres varios equipos de Azure Linux son compatibles en la vista previa.

## <a name="supported-tooling"></a>Herramientas compatibles

La vista previa admite la interacción con el clúster mediante CLI de Azure. Para desarrolladores de Java, integración con Eclipse y Yeoman se suministra con Eclipse Linux y OSX compatible. La integración de OSX utiliza una VM Linux Mostrar opciones avanzadas a través de vagrant. Para los desarrolladores de C# integración con Yeoman está disponible para generar las plantillas de aplicación.

## <a name="next-steps"></a>Pasos siguientes


1. Familiarizarse con los entornos de programación [Actores confiable](service-fabric-reliable-actors-introduction.md) y [Servicios confiables](service-fabric-reliable-services-introduction.md) .

2. [Preparar el entorno de desarrollo en Linux](service-fabric-get-started-linux.md)

3. [Preparar el entorno de desarrollo de OSX](service-fabric-get-started-mac.md)

4. [Crear su primera aplicación de servicio tela Java en Linux](service-fabric-create-your-first-linux-application-with-java.md)

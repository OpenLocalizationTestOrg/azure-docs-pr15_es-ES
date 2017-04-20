<properties 
    pageTitle="Información general sobre la integración de empresa | Servicio de aplicaciones de Microsoft Azure | Microsoft Azure" 
    description="Usar las características de integración de Enterprise para habilitar escenarios empresariales proceso y la integración con aplicaciones de lógica" 
    services="logic-apps" 
    documentationCenter=".net,nodejs,java"
    authors="msftman" 
    manager="erikre" 
    editor="cgronlun"/>

<tags 
    ms.service="logic-apps" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/08/2016" 
    ms.author="deonhe"/>

# <a name="overview-of-the-enterprise-integration-pack"></a>Información general sobre el paquete de integración de Enterprise

## <a name="what-is-the-enterprise-integration-pack"></a>¿Qué es el paquete de integración de Enterprise?
El paquete de integración de Enterprise es la solución en la nube de Microsoft para habilitar sin problemas de comunicaciones de empresa a empresa (B2B). El paquete usa protocolos estándar incluidos [AS2](./app-service-logic-enterprise-integration-as2.md), [X12](./app-service-logic-enterprise-integration-x12.md)y [EDIFACT](./app-service-logic-enterprise-integration-edifact.md) para intercambiar mensajes entre socios empresariales. Mensajes pueden opcionalmente protegerse mediante el cifrado y las firmas digitales. 

El paquete permite a las organizaciones que usan distintos protocolos y formatos de mensajes de Exchange electrónicamente por transformar los diferentes formatos en un formato que pueden interpretar y tomar medidas en los sistemas de ambas organizaciones. 

Si está familiarizado con BizTalk Server o servicios de Microsoft Azure BizTalk, le resultará fácil de usar que características de la integración de Enterprise porque la mayoría de los conceptos es similar. Una diferencia importante es que la integración de Enterprise utiliza cuentas de integración para simplificar el almacenamiento y la administración de artefactos que se usan en las comunicaciones B2B. 

Arquitectura, el módulo de integración de empresa se basa en **cuentas de integración** que almacenan todos los artefactos que se pueden usar para diseñar, implementar y mantener sus aplicaciones B2B. Una cuenta de integración es básicamente un contenedor basada en nube donde almacenar artefactos como esquemas, socios, certificados, mapas y contratos. Estos artefactos pueden usarse en las aplicaciones de lógica para crear flujos de trabajo de B2B. Antes de poder usar los artefactos en una aplicación de lógica, debe establecer el vínculo de su cuenta de integración a su aplicación lógica. Después de vincular ellos, la aplicación de lógica tendrán acceso a artefactos de la cuenta de integración.  

## <a name="why-should-you-use-enterprise-integration"></a>¿Por qué debería usar integración empresarial?
- Con la integración de empresa, es posible almacenar todos los artefactos en un solo lugar, que es su cuenta de integración. 
- Puede aprovechar el motor de aplicaciones de lógica y todos sus conectores para crear flujos de trabajo de B2B e intégrelo con aplicaciones de SaaS parte 3ª, aplicaciones locales, así como aplicaciones personalizadas
- También puede aprovechar las funciones de Azure

## <a name="how-to-get-started-with-enterprise-integration"></a>¿Cómo puedo empezar a trabajar con integración de empresa?
Puede crear y administrar aplicaciones B2B usando el módulo de integración de Enterprise mediante el Diseñador de aplicaciones de lógica en el **portal de Azure**.  

También puede usar [PowerShell](https://msdn.microsoft.com/library/azure/mt652195.aspx "lógica aplicaciones temas de PowerShell") para administrar las aplicaciones de lógica. 

Información general de los pasos que debe tener para poder crear aplicaciones en el portal de Azure: ![imagen de introducción](./media/app-service-logic-enterprise-integration-overview/overview-0.png)  

## <a name="what-are-some-common-scenarios"></a>¿Cuáles son algunas situaciones comunes?

Integración de Enterprise es compatible con estos estándares:   

- EDI - intercambio de datos electrónicos  
- EAI - integración de aplicaciones empresariales  

## <a name="heres-what-you-need-to-get-started"></a>Esto es lo que necesita para empezar
- Una suscripción con una cuenta de integración de Azure
- 2015 de Visual Studio para crear mapas y esquemas
- [Integración de aplicaciones empresariales de lógica de Microsoft Azure Tools para Visual Studio 2015 2.0](https://aka.ms/vsmapsandschemas)  

## <a name="try-it"></a>Pruébelo:
[Pruébelo ahora:](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-as2-send-receive) implementar un ejemplo totalmente operativo AS2 enviar y recibir la aplicación de la lógica que usa las características de B2B de lógica de aplicaciones.

## <a name="learn-more-about"></a>Más información acerca de:
- [Agreements] (./app-service-logic-enterprise-integration-agreements.md "Obtenga más información acerca de los contratos de integración de enterprise")
- [Escenarios de empresa a empresa (B2B)] (./app-service-logic-enterprise-integration-b2b.md "Aprenda a crear aplicaciones de lógica con las características de B2B")  
- [Certificados] (./app-service-logic-enterprise-integration-certificates.md "Obtenga más información acerca de los certificados de integración de enterprise")
- [Codificar o descodificar de archivo plano] (./app-service-logic-enterprise-integration-flatfile.md "Obtenga información sobre cómo codificar y descodificar el contenido del archivo plano")  
- [Cuentas de integración] (./app-service-logic-enterprise-integration-accounts.md "Obtenga más información acerca de las cuentas de integración")
- [Mapas] (./app-service-logic-enterprise-integration-maps.md "Obtenga más información acerca de la integración de enterprise de mapas")
- [Socios] (./app-service-logic-enterprise-integration-partners.md "Obtenga información sobre los socios de integración de enterprise")
- [Esquemas] (./app-service-logic-enterprise-integration-schemas.md "Obtenga información sobre los esquemas de integración de enterprise")
- [Validación de mensajes XML] (./app-service-logic-enterprise-integration-xml.md "Obtenga información sobre cómo validar mensajes XML con aplicaciones de lógica")
- [Transformar XML] (./app-service-logic-enterprise-integration-transform.md "Obtenga más información acerca de la integración de enterprise de mapas")
- [Conectores de integración de Enterprise] (../connectors/apis-list.md "Obtenga información sobre los conectores de paquete de integración de enterprise")




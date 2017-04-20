<properties
    pageTitle="Información general de aplicaciones Web de pila de Azure | Microsoft Azure"
    description="Información general sobre aplicaciones Web de pila de Azure"
    services="azure-stack"
    documentationCenter=""
    authors="apwestgarth"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="app-service"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="anwestg"/>
    
# <a name="azure-stack-web-apps-overview"></a>Introducción a aplicaciones Web pila de Azure
    
> [AZURE.NOTE] La siguiente información solo se aplica a implementaciones TP1 de pila de Azure.

Azure aplicaciones Web de pila es el primer elemento de la oferta de servicio de la aplicación de Azure poner en pila de Azure. El programa de instalación de aplicaciones Web de pila de Azure creará una instancia de cada uno de los cinco tipos de función necesaria y también creará un servidor de archivos. Aunque puede añadir más instancias para cada uno de los tipos de rol, recuerde que no hay espacio para máquinas virtuales de Technical Preview 1. Las capacidades actuales de Azure pila Web Apps son principalmente capacidades de foundation que son necesarios para administrar las aplicaciones web de sistema y de host.

![Pila de Azure servicio de aplicación Web Apps en el Azure apilan Portal][1]

## <a name="limitations-of-the-technical-preview"></a>Limitaciones de Technical Preview

No hay ninguna compatibilidad para las versiones de vista previa del servicio de aplicación de pila de Azure. No coloque las cargas de trabajo de producción en esta versión preliminar. No hay ninguna actualización entre versiones de vista previa de servicio de aplicación de pila de Azure. El objetivo principal de las versiones de vista previa es para mostrar lo que proporcionamos y obtener comentarios. 

## <a name="what-is-an-app-service-plan"></a>¿Qué es un Plan de servicio de aplicación?

El proveedor de recursos de aplicaciones Web de pila de Azure utiliza el mismo código que usa la característica de aplicaciones Web de Azure en el servicio de aplicación de Azure. Como resultado, algunos conceptos comunes valen que describe. En aplicaciones Web, el contenedor de precio para las aplicaciones web se denomina el plan de servicios de aplicación. Representa el conjunto de dedicado máquinas virtuales que se utiliza para mantener sus aplicaciones. Dentro de una suscripción determinada, puede tener varios planes de servicio de aplicación. Esto también es verdadero en aplicaciones Web de pila de Azure. 

En Azure, hay trabajadores compartidos y dedicados. Trabajador compartido es compatible con el host de la aplicación en la web multiempresa alta densidad y hay solo un conjunto de los trabajadores compartidos. Servidores dedicados solo se usa un inquilino y vienen en tres tamaños: pequeño, mediano y grande. Las necesidades de los clientes locales siempre no pueden describirse mediante el uso de estos términos. En aplicaciones Web de Azure pila, los administradores de proveedor de recursos pueden definir los niveles de trabajo que desean que estén disponibles que tienen varios conjuntos de trabajadores compartidos o diferentes conjuntos de trabajadores dedicados en función de su necesidades de hospedaje único. Utilizando las definiciones de nivel de trabajo, pueden definir sus propios precios SKU.

## <a name="portal-features"></a>Características de portal

A medida que también es true con el back-end, aplicaciones Web de pila de Azure utiliza la interfaz de usuario mismo que usa aplicaciones Web de Azure. Algunas características están deshabilitadas y todavía no funcionales en pila de Azure. Esto es porque las expectativas de Azure específica o servicios que requieren estas características no aún están disponibles en la pila de Azure. 

## <a name="next-steps"></a>Pasos siguientes

- [Antes de empezar con aplicaciones Web de pila de Azure](azure-stack-webapps-before-you-get-started.md)
- [Instalar al proveedor de recursos de aplicaciones Web](azure-stack-webapps-deploy.md)

También puede probar otros [plataforma como servicio (PaaS)](azure-stack-tools-paas-services.md), como el [proveedor de recursos de SQL Server](azure-stack-sql-rp-deploy-short.md) y el [proveedor de recursos de MySQL](azure-stack-mysql-rp-deploy-short.md).

<!--Image references-->
[1]: ./media/azure-stack-webapps-overview/AppService_Portal.png

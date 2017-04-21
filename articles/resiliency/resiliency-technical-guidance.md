<properties
   pageTitle="Índice de la resistencia orientación técnica | Microsoft Azure"
   description="Índice de artículos técnicos en Descripción y diseñar flexible, altamente disponible, tolerancia aplicaciones, así como planificación de continuidad empresarial y recuperación de desastres"
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="saladki"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="aglick"/>

#<a name="azure-resiliency-technical-guidance"></a>Orientación técnica de la resistencia de Azure

##<a name="introduction"></a>Introducción

La reunión de alta disponibilidad y requisitos de recuperación de desastres requiere dos tipos de conocimiento:

- Descripción técnica detallada de capacidades de la plataforma de nube
- Saber cómo diseñar correctamente un servicio distribuido

Esta serie de artículos cubre los primeros: las capacidades y limitaciones de la plataforma Windows Azure con respecto a la resistencia (a veces denominada continuidad empresarial). Si está interesado en el último, vea la serie de artículo centrada en la [recuperación y alta disponibilidad para las aplicaciones de Azure](https://aka.ms/drtechguide). Aunque esta serie de artículos que se mencionan en patrones de diseño y arquitectura, no es el enfoque de la serie. Para obtener instrucciones de diseño, puede consultar el material en la sección [recursos adicionales](#additional-resources) .

La información se organiza en los siguientes artículos:

- [Recuperación de errores locales](resiliency-technical-guidance-recovery-local-failures.md).
Puede fallar hardware físico (por ejemplo, unidades, servidores y dispositivos de red). Pueden agotado los recursos cuando picos de carga. En este artículo se describe las capacidades que proporciona Azure para mantener la alta disponibilidad en las siguientes circunstancias.

- [Recuperación de una interrupción del servicio de toda la región de Azure](resiliency-technical-guidance-recovery-loss-azure-region.md).
Generalizado errores son poco frecuentes, pero son teóricamente posibles. Regiones enteras pueden convertirse en aislado debido a errores de red, o puede estar dañadas física catástrofes. En este artículo se explica cómo usar Azure para crear aplicaciones que abarcan regiones ubicaciones geográficas.

- [Recuperación locales con Azure](resiliency-technical-guidance-recovery-on-premises-azure.md).
La nube modifica considerablemente el ahorro de recuperación, permitiendo que las organizaciones utilizar Azure para establecer un segundo sitio de recuperación. Puede hacerlo en una fracción del costo de crear y mantener un centro de datos secundario. Este artículo explica las capacidades que proporciona Azure para extender un centro de datos local en la nube.

- [Recuperación de daños en los datos o el borrado accidental](resiliency-technical-guidance-recovery-data-corruption.md).
Aplicaciones pueden tener errores de datos dañadas. Operadores incorrectamente pueden eliminar datos importantes. En este artículo se explica qué Azure proporciona para copias de seguridad y restaurar a un punto anterior tiempo.

##<a name="additional-resources"></a>Recursos adicionales

- [Recuperación y alta disponibilidad de las aplicaciones integradas en Microsoft Azure](resiliency-disaster-recovery-high-availability-azure-applications.md).
En este artículo es una descripción detallada de la disponibilidad y la recuperación. Cubra el desafío de replicación manual de referencia y datos de transacciones. Las secciones finales proporcionan resúmenes de los diferentes tipos de topologías de recuperación de desastres que abarcan regiones Azure el más alto nivel de disponibilidad.

- [Lista de comprobación de alta disponibilidad](resiliency-high-availability-checklist.md).
En este artículo es una lista de características, servicios, y diseños que pueden ayudar a aumentan la resistencia y la disponibilidad de la aplicación.

- [Orientación de la resistencia de servicio de Microsoft Azure](resiliency-service-guidance-index.md).
Este artículo es un índice de servicios de Azure y proporciona vínculos a instrucciones para recuperación y Guía de diseño.

- [Información general: empresa continuidad y base de datos de recuperación de base de datos SQL de nube](../sql-database/sql-database-business-continuity.md).
Este artículo proporciona técnicas de base de datos de SQL Azure disponibilidad. Principalmente se centra en estrategias de copia de seguridad y restauración. Si utiliza la base de datos de SQL Azure en el servicio de nube, debe revisar este documento y sus recursos relacionados.

- [Alta disponibilidad y recuperación para SQL Server en Azure máquinas virtuales de Windows](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).
En este artículo se describe las opciones de disponibilidad que puede explorar cuando usa la infraestructura como servicio (IaaS) para hospedar los servicios de base de datos. Describe los grupos de disponibilidad AlwaysOn, reflejo de base de datos, envío de registros y copia de seguridad y restauración. Varios tutoriales muestran cómo utilizar estas técnicas.

- [Procedimientos recomendados para diseñar servicios a gran escala en servicios de nube de Azure](https://azure.microsoft.com//blog/best-practices-for-designing-large-scale-services-on-windows-azure/).
En este artículo se centra en desarrollar arquitecturas de nube altamente scalable. Muchas de las técnicas que se usan para mejorar la escalabilidad también mejoran la disponibilidad. Además, si la aplicación no se puede escalar en aumento de la carga, escalabilidad se convierte en un problema de disponibilidad.

- [Copia de seguridad y restauración de SQL Server en Azure máquinas virtuales de Windows](../virtual-machines/virtual-machines-windows-sql-backup-recovery.md).
En este artículo se proporciona orientación técnica acerca de cómo hacer copia de seguridad y restauración de Microsoft SQL Server que se ejecuta en Azure máquinas virtuales de Windows.

- [a prueba de errores: Guía para arquitecturas de nube flexibles](https://channel9.msdn.com/Series/FailSafe).
Este artículo proporciona una guía para crear arquitecturas de nube flexibles, instrucciones para la implementación de esas arquitecturas de recetas para implementar estas arquitecturas para escenarios específicos y tecnologías de Microsoft.

- [Caso práctico técnico: uso de las tecnologías nube para mejorar la recuperación](https://www.microsoft.com/itshowcase/Article/Content/737/Using-cloud-technologies-to-improve-disaster-recovery).
En este caso práctico muestra cómo utiliza Microsoft IT Azure para mejorar la recuperación.

##<a name="next-steps"></a>Pasos siguientes

En este artículo forma parte de una serie centrada en orientación técnica de la resistencia de Azure. Si está interesado en otros artículos de esta serie de lectura, puede empezar con la [recuperación de errores locales](resiliency-technical-guidance-recovery-local-failures.md).

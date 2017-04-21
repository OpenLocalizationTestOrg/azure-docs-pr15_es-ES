<properties
   pageTitle="Orientación de la resistencia de servicio | Microsoft Azure"
   description="Vínculos a recuperación y proactiva sobre resistencia y disponibilidad de los servicios de Microsoft Azure."
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

# <a name="microsoft-azure-service-resiliency-guidance"></a>Orientación de la resistencia de servicio de Microsoft Azure
Microsoft Azure está diseñada para proporcionarle los recursos que necesita, cuando los necesite. Como parte de un buen diseño y prácticas operativas, debe conocer tanto cómo diseñar el uso de servicios de Azure para lograr alta disponibilidad como qué hacer si la aplicación se ve afectada por una interrupción del servicio. Para ayudarle en este proceso, este documento contiene vínculos a instrucciones para recuperación, así como la orientación de diseño para diversos servicios de Azure.

##<a name="disaster-recovery-guidance"></a>Instrucciones para recuperación
Instrucciones para la recuperación son vínculos siguientes pueden proporcionarle la información que necesita para obtener rápidamente la aplicación en línea si se ven afectados por una interrupción del servicio de Azure. Estos vínculos se crearon para ayudarle a responder a la pregunta, "Estoy que se ve afectada por una interrupción del servicio de Azure, ¿qué puedo hacer?"

##<a name="design-guidance"></a>Guía de diseño
Los vínculos de la Guía de diseño siguientes son diseño y orientación arquitectónica que se ha creado para ayudarle a comprender mejor cómo usar cada servicio de Azure en la forma que aumente el tiempo de actividad de la aplicación. Estos vínculos se crearon para ayudarle a responder a la pregunta "¿Cómo puedo asegurarme de que un error, error de hardware, interrupción del servicio o cualquier otro no afectar a la disponibilidad general de mi aplicación?" Si no hay ningún instrucciones específicas para el servicio que actualmente está buscando, es posible que el artículo de [alta disponibilidad de las aplicaciones integradas en Microsoft Azure](./resiliency-high-availability-azure-applications.md) tenga información adicional que puede ayudarle en el diseño. 

##<a name="service-guidance"></a>Guía de servicio
| Servicio  | Instrucciones para recuperación | Guía de diseño |
|:---------|:--------------------------:|:------------------:|
| [Servicios de nube] (https://azure.microsoft.com/services/cloud-services/ "Servicios de nube de Azure")       | [vínculo] (../cloud-services/cloud-services-disaster-recovery-guidance.md "Instrucciones para recuperación de servicios de nube de Azure")   | No disponible |
| [Depósito clave] (https://azure.microsoft.com/services/key-vault/ "Depósito de clave de Azure")                      | [vínculo] (../key-vault/key-vault-disaster-recovery-guidance.md "Instrucciones para recuperación de depósito de clave de Azure")        | No disponible |
| [Almacenamiento de información] (https://azure.microsoft.com/services/storage/ "Almacenamiento de Azure")                            | [vínculo] (../storage/storage-disaster-recovery-guidance.md "Instrucciones para recuperación de almacenamiento de Azure")          | No disponible |
| [Bases de datos SQL] (https://azure.microsoft.com/services/sql-database/ "Bases de datos SQL Azure")           | [vínculo] (../sql-database/sql-database-disaster-recovery.md  "Instrucciones para recuperación de base de datos de SQL Azure")    | [vínculo] (../sql-database/sql-database-business-continuity.md "Información general sobre continuidad empresarial con la base de datos de SQL Azure") |
| [Máquinas virtuales de Windows] (https://azure.microsoft.com/services/virtual-machines/ "Máquinas virtuales de Windows Azure") | [vínculo] (../virtual-machines/virtual-machines-disaster-recovery-guidance.md "Instrucciones para recuperación de máquinas virtuales de Azure") | No disponible |
| [Red virtual] (https://azure.microsoft.com/services/virtual-network/ "Red Virtual Azure")    | [vínculo] (../virtual-network/virtual-network-disaster-recovery-guidance.md "Instrucciones para recuperación de red Virtual de Azure")  | No disponible |

##<a name="next-steps"></a>Pasos siguientes
Si está buscando la orientación que se centra con más amplitud en sistemas y soluciones, lea [de recuperación y alta disponibilidad de las aplicaciones integradas en Microsoft Azure](https://aka.ms/drtechguide).

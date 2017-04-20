 (depósitos copia de seguridad<properties
   pageTitle = "Azure copia de seguridadp limits table"
   Descripción = "describe los límites del sistema para Azure Backup."
   services="backup"
   documentationCenter="NA"
   authors="Jim-Parker"
   manager="jwhit"
   editor="" />
<tags
   ms.service="backup"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="10/05/2015"
   ms.author="trinadhk";"jimpark"; "aashishr" />


Los siguientes límites se aplican a la copia de seguridad de Azure.

| Identificador de límite | Límite predeterminado |
|---|---|
|Número de servidores o equipos que se pueden registrar con cada depósito|50 para Windows Server/cliente/SCDPM <br/> 200 para máquinas virtuales de IaaS|
|Tamaño de un origen de datos para los datos almacenados en el almacenamiento de Azure depósito|Número máximo de 54400 GB<sup>1</sup>|
|Número de depósitos de copia de seguridad que pueden crearse en cada suscripción de Azure|25 (depósitos de copia de seguridad) <br/> Servicios de recuperación de 25 depósito por región|
|Número de veces que se copia de seguridad se puede programar por día|3 por día cliente-servidor para Windows <br/> 2 por día para SCDPM <br/> Una vez al día para máquinas virtuales de IaaS|
|Discos de datos adjuntados a una máquina virtual Azure para copia de seguridad|16|

- <sup>1</sup> El límite de 54400 GB no se aplica a la copia de seguridad de VM IaaS.


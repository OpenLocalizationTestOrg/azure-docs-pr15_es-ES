<properties 
   pageTitle="Depurar servicios de nube de Azure | Microsoft Azure"
   description="Depurar servicios de nube de Azure"
   services="visual-studio-online"
   documentationCenter="n/a"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="visual-studio-online"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="debugging-cloud-services"></a>Servicios de nube de depuración

Puede usar diferentes métodos para depurar una aplicación de Azure con las herramientas de Azure para Microsoft Visual Studio y el SDK de Azure:

- Puede depurar una aplicación Azure de Visual Studio al desarrollar, tal como lo haría con cualquier aplicación de Visual C# o Visual Basic. Para obtener más información, vea [depurar su servicio de nube en el equipo local](vs-azure-tools-debug-cloud-services-virtual-machines.md#debug-your-cloud-service-on-your-local-computer).

- Puede utilizar Diagnósticos de Azure para registrar información detallada de código que se ejecuta dentro de funciones, si se están ejecutando los roles en el entorno de desarrollo o en Azure. Para obtener más información, vea [registro de recopilación de datos mediante el uso de diagnósticos de Azure](http://go.microsoft.com/fwlink/p/?LinkId=400450).

- Si está utilizando Visual Studio Enterprise para escribir funciones destinadas a .NET Framework 4 o .NET Framework 4.5, puede habilitar IntelliTrace al tiempo que implementa un servicio de nube Azure desde Visual Studio. IntelliTrace proporciona un registro que puede usar con Visual Studio para depurar su aplicación como si se ejecuta en Azure. Para obtener más información, vea [Depurar un servicio de nube publicados con IntelliTrace y Visual Studio]( http://go.microsoft.com/fwlink/p/?LinkId=623016).

- Puede habilitar la depuración remota en los servicios de nube al tiempo cuando se implementa el servicio de nube de Visual Studio. Si decide activar depuración remota para una implementación, servicios de depuración remotos están instalados en los equipos virtuales que se ejecute cada instancia del rol. Estos servicios, como msvsmon.exe, no afectar al rendimiento o resultado costes adicionales. Para obtener más información, vea [Depurar un servicio de nube en Azure](vs-azure-tools-debug-cloud-services-virtual-machines.md#debug-a-cloud-service-in-azure).




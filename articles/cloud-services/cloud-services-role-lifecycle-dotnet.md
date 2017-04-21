<properties 
pageTitle="Controlar los eventos de ciclo de vida de servicio de nube | Microsoft Azure" 
description="Obtenga información sobre cómo se pueden utilizar los métodos de ciclo de vida de un rol de servicio de nube en .NET" 
services="cloud-services" 
documentationCenter=".net" 
authors="Thraka" 
manager="timlt" 
editor=""/>
<tags 
ms.service="cloud-services" 
ms.workload="tbd" 
ms.tgt_pltfrm="na" 
ms.devlang="na" 
ms.topic="article" 
ms.date="09/06/2016" 
ms.author="adegeo"/>

# <a name="customize-the-lifecycle-of-a-web-or-worker-role-in-net"></a>Personalizar el ciclo de vida de un rol Web o trabajo en .NET

Cuando se crea una función de trabajo, se extiende la clase [RoleEntryPoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx) que proporciona métodos para reemplazar que le permiten responden a eventos de ciclo de vida. Para las funciones web esta clase es opcional, lo que debe usar para responder a eventos de ciclo de vida.

## <a name="extend-the-roleentrypoint-class"></a>Extender la clase RoleEntryPoint

La clase [RoleEntryPoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx) incluye métodos que llaman Azure cuando se **inicia**, **se ejecuta**o **se detiene** una función web o de trabajo. Opcionalmente, puede reemplazar estos métodos para administrar función inicialización, secuencias de cierre de funciones o al subproceso de ejecución de la función. 

Extender **RoleEntryPoint**, debería tener en cuenta los comportamientos de los métodos siguientes:

-   Los métodos [OnStart](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx) y [OnStop](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx) devuelven un valor boolean, por lo que es posible devolver **false** desde estos métodos.

     Si el código devuelve **false**, el proceso de rol se cerrará el servicio, sin ejecutar cualquier secuencia de apagado, es posible que tenga en su lugar. En general, debe evitar devuelve **false** desde el método **OnStart** .
     
-   Cualquier excepción no detectada dentro de una sobrecarga de un método de **RoleEntryPoint** se considera una excepción no controlada.

     Si se produce una excepción dentro de uno de los métodos de ciclo de vida, Azure, producirá el evento [UnhandledException](https://msdn.microsoft.com/library/system.appdomain.unhandledexception.aspx) y, a continuación, se finaliza el proceso. Después de la función se ha desconectado, se reinicie Azure. Cuando se produce una excepción no controlada, no se produce el evento [Detener](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.stopping.aspx) y no se llama el método **OnStop** .

Si su rol no se inicia, o reciclaje entre la inicialización, ocupado y los Estados de detención, puede que el código se producir una excepción no controlada en uno de los eventos de ciclo de vida cada vez que se reinicia el rol. En este caso, use el evento [UnhandledException](https://msdn.microsoft.com/library/system.appdomain.unhandledexception.aspx) para determinar la causa de la excepción y controlar de forma adecuada. También puede devolver su rol desde el método [Ejecutar](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) , que hace que la función reiniciar. Para obtener más información acerca de los Estados de implementación, vea [Problemas que producen funciones comunes a la Papelera](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md).

> [AZURE.NOTE] Si está utilizando las **Herramientas de Azure para Microsoft Visual Studio** para desarrollar la aplicación, las plantillas de proyecto de la función amplían la clase **RoleEntryPoint** , en los archivos *WebRole.cs* y *WorkerRole.cs* .

## <a name="onstart-method"></a>Método OnStart

El método **OnStart** se llama cuando se conecta la instancia de rol por Azure. Mientras se ejecuta el código de OnStart, la instancia de rol está marcada como **ocupado** y no tráfico externo se dirigirá a ella por el equilibrador de carga. Puede reemplazar este método para realizar el trabajo de inicialización, como implementar controladores de eventos y el inicio de [Diagnósticos de Azure](cloud-services-how-to-monitor.md).

Si **OnStart** devuelve **true**, la instancia se ha inicializado correctamente y el método **RoleEntryPoint.Run** llama a Azure. Si **OnStart** devuelve **false**, la función termina inmediatamente, sin ejecutar cualquier secuencia de apagado planeado.

En el ejemplo siguiente se muestra cómo reemplazar el método **OnStart** . Este método configura e inicia a un monitor de diagnóstico cuando se inicia y configura una transferencia de datos de registro a una cuenta de almacenamiento de la instancia de rol:

```csharp
public override bool OnStart()
{
    var config = DiagnosticMonitor.GetDefaultInitialConfiguration();

    config.DiagnosticInfrastructureLogs.ScheduledTransferLogLevelFilter = LogLevel.Error;
    config.DiagnosticInfrastructureLogs.ScheduledTransferPeriod = TimeSpan.FromMinutes(5);

    DiagnosticMonitor.Start("DiagnosticsConnectionString", config);

    return true;
}
```

## <a name="onstop-method"></a>Método OnStop

El método **OnStop** se denomina después una instancia de la función se ha desconectado por Azure y antes de salir del proceso. Puede reemplazar este método para llamar a código necesario de la instancia de rol cerrar sin errores.

> [AZURE.IMPORTANT] Código que se ejecuta en el método **OnStop** tiene un tiempo limitado para finalizar cuando se llama por razones que no sea un apagado iniciado por el usuario. Después de que transcurre este momento, el proceso está terminado, por lo que debe asegurarse de que el código en el método **OnStop** puede ejecutar rápidamente o admite no se ejecuta hasta su finalización. El método **OnStop** se denomina una vez el evento **Detener** .


## <a name="run-method"></a>Ejecutar método

Puede reemplazar el método **Run** para implementar un subproceso larga duración de la instancia de rol.

Reemplazar el método **Run** no es necesario; la implementación predeterminada inicia un subproceso que siempre se suspende. Si reemplaza el método **Ejecutar** , el código debería bloquear indefinidamente. Si el método **Run** devuelve, la función es automáticamente correctamente recicla; en otras palabras, Azure provoca el evento **Detener** y llama al método de **OnStop** para que se pueden ejecutar las secuencias de cierre antes de la función se desconecta.


### <a name="implementing-the-aspnet-lifecycle-methods-for-a-web-role"></a>Implementar los métodos de ciclo de vida ASP.NET para una función web

Puede usar los métodos de ciclo de vida ASP.NET, además de los proporcionados por la clase **RoleEntryPoint** , administrar inicialización y secuencias de cierre para una función de web. Esto puede ser útil para la compatibilidad si son trasladar una aplicación de ASP.NET existente en Azure. Los métodos de ciclo de vida ASP.NET se llaman desde dentro de los métodos de **RoleEntryPoint** . La **aplicación\_iniciar** se invoca cuando finalice el método **RoleEntryPoint.OnStart** . La **aplicación\_final** se invoca antes de que se llama al método **RoleEntryPoint.OnStop** .

## <a name="next-steps"></a>Pasos siguientes
Obtenga información sobre cómo [crear un paquete de servicio de nube](cloud-services-model-and-package.md).
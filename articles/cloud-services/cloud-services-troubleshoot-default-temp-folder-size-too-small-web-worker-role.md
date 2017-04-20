<properties
   pageTitle="Tamaño de la carpeta TEMP predeterminada es demasiado pequeño para una función | Microsoft Azure"
   description="Una función de servicio de nube tiene una cantidad limitada de espacio para la carpeta TEMP. Este artículo proporciona algunas sugerencias sobre cómo evitar quedarse sin espacio."
   services="cloud-services"
   documentationCenter=""
   authors="simonxjx"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="10/12/2016"
   ms.author="v-six" />

# <a name="default-temp-folder-size-is-too-small-on-a-cloud-service-webworker-role"></a>Tamaño de la carpeta TEMP predeterminada es demasiado pequeño en una función de web y trabajador de servicio de nube

El directorio temporal predeterminado de una función de trabajo o web del servicio de nube tiene un tamaño máximo de 100 MB, que puede quedar completa en algún momento. En este artículo se describe cómo evitar quedarse sin espacio para el directorio temporal.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-do-i-run-out-of-space"></a>¿Por qué quede sin espacio?

Las variables de entorno estándares de Windows TEMP y TMP están disponibles para el código que se ejecuta en la aplicación. TEMP y TMP señale a un único directorio que tiene un tamaño máximo de 100 MB. Todos los datos que se almacenan en este directorio no se conservan en el ciclo de vida del servicio de nube; Si las instancias de la función de un servicio de nube recicladas, es limpiar el directorio.

## <a name="suggestion-to-fix-the-problem"></a>Sugerencias para solucionar el problema

Implementar una de las siguientes alternativas:

- Configurar un recurso de almacenamiento local y acceder directamente en lugar de usar TEMP o TMP. Para obtener acceso a un recurso de almacenamiento local desde el código que se está ejecutando dentro de la aplicación, llame al método [RoleEnvironment.GetLocalResource](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.getlocalresource.aspx) . 

- Configurar un recurso de almacenamiento local y elija los directorios TEMP y TMP para que apunten a la ruta de acceso del recurso de almacenamiento local. Esta modificación debe realizarse en el método [RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx) .

En el ejemplo siguiente se muestra cómo modificar los directorios de destino para TEMP y TMP desde dentro del método OnStart:


```csharp
using System;
using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRole1
{
    public class WorkerRole : RoleEntryPoint
    {
        public override bool OnStart()
        {
            // The local resource declaration must have been added to the
            // service definition file for the role named WorkerRole1:
            //
            // <LocalResources>
            //    <LocalStorage name="CustomTempLocalStore"
            //                  cleanOnRoleRecycle="false"
            //                  sizeInMB="1024" />
            // </LocalResources>

            string customTempLocalResourcePath =
            RoleEnvironment.GetLocalResource("CustomTempLocalStore").RootPath;
            Environment.SetEnvironmentVariable("TMP", customTempLocalResourcePath);
            Environment.SetEnvironmentVariable("TEMP", customTempLocalResourcePath);

            // The rest of your startup code goes here…

            return base.OnStart();
        }
    }
}
```

## <a name="next-steps"></a>Pasos siguientes

Leer un blog que describe [cómo aumentar el tamaño de la carpeta temporal de ASP.NET de Azure Web rol](http://blogs.msdn.com/b/kwill/archive/2011/07/18/how-to-increase-the-size-of-the-windows-azure-web-role-asp-net-temporary-folder.aspx).

Ver más [artículos de solución de problemas](/?tag=top-support-issue&product=cloud-services) de servicios en la nube.

Para obtener información sobre cómo solucionar problemas de rol del servicio de nube con los datos de Azure PaaS equipo diagnósticos, ver [serie de blogs de Kevin Williamson](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

<properties
   pageTitle="Causas comunes de los roles de servicio de nube de reciclaje | Microsoft Azure"
   description="Una función de servicio de nube repente recicla puede provocar tiempo de inactividad considerable. Estos son algunos problemas comunes que producen las funciones ser reciclados, que pueden ayudar a reducir el tiempo de inactividad."
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
   ms.date="09/02/2016"
   ms.author="v-six" />

# <a name="common-issues-that-cause-roles-to-recycle"></a>Problemas comunes que producen roles de reciclaje

En este artículo se describe algunas de las causas comunes de problemas de implementación y proporciona sugerencias de solución de problemas para ayudar a resolver estos problemas. Indica que hay un problema con una aplicación es cuando no se puede iniciar la instancia de función o ciclos de entre los Estados al iniciar, detener y ocupados.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-runtime-dependencies"></a>Faltan las dependencias de tiempo de ejecución

Si una función en la aplicación se basa en cualquier ensamblado que no forme parte de .NET Framework o la Azure administrado biblioteca, debe incluir explícitamente ese ensamblado en el paquete de aplicación. Tenga en cuenta que otros marcos de Microsoft no están disponibles en Azure de forma predeterminada. Si su rol se basa en un marco de trabajo, debe agregar esos ensamblados al paquete de aplicación.

Antes de generar y empaquetar la aplicación, compruebe lo siguiente:

- Si utiliza Visual studio, asegúrese de que la propiedad **Copia Local** se establece en **True** para cada ensamblado en el proyecto que no forma parte del SDK de Azure o .NET Framework.

- Asegúrese de que el archivo web.config no hace referencia a los ensamblados sin usar en el elemento de compilación.

- La **Acción de compilación** de cada archivo .cshtml se establece en **contenido**. Esto garantiza que los archivos se muestren correctamente en el paquete y permite a otros archivos que se hace referencia que aparezca en el paquete.

## <a name="assembly-targets-wrong-platform"></a>Plataforma de ensamblado destinos incorrecta

Azure es un entorno de 64 bits. Por lo tanto, ensamblados de .NET compilados para un destino de 32 bits no funcionan en Azure.

## <a name="role-throws-unhandled-exceptions-while-initializing-or-stopping"></a>Función genera una excepción no controlada al inicialización o detener

Las excepciones que se inician los métodos de la clase [RoleEntryPoint] , que incluye la [OnStart], [OnStop]y [Ejecutar] métodos, son las excepciones no controladas. Si se produce una excepción no controlada en uno de estos métodos, la función se reciclaje. Si la función es reciclaje varias veces, puede iniciar una excepción no controlada cada vez que intenta iniciar.

## <a name="role-returns-from-run-method"></a>Función devuelve desde el método de ejecución

El método [Run] pretende ejecutar indefinidamente. Si el código reemplaza el método [Ejecutar] , debe suspender indefinidamente. Si el método [Run] devuelve, recicla el rol.

## <a name="incorrect-diagnosticsconnectionstring-setting"></a>Configuración de DiagnosticsConnectionString incorrecta

Si la aplicación utiliza diagnósticos de Azure, debe especificar el archivo de configuración de servicio del `DiagnosticsConnectionString` de configuración. Este valor debe especificar una conexión de HTTPS a su cuenta de almacenamiento en Azure.

Para asegurarse de que su `DiagnosticsConnectionString` configuración es correcta antes de implementar el paquete de aplicación en Azure, compruebe lo siguiente:  

- La `DiagnosticsConnectionString` establecer puntos en una cuenta de almacenamiento válida en Azure.  
  De forma predeterminada, esta configuración apunta a la cuenta de almacenamiento emulada, por lo que debe cambiar explícitamente esta configuración antes de implementar el paquete de aplicación. Si no cambiar esta configuración, se produce una excepción cuando la instancia de rol intenta iniciar al monitor de diagnóstico. Esto puede provocar la instancia del rol de reciclaje indefinidamente.

- La cadena de conexión especificada en el siguiente [formato](../storage/storage-configure-connection-string.md). (El protocolo debe especificarse como HTTPS). Reemplace *MyAccountName* con el nombre de su cuenta de almacenamiento y *MyAccountKey* con la clave de acceso:    

        DefaultEndpointsProtocol=https;AccountName=MyAccountName;AccountKey=MyAccountKey

  Si va a desarrollar su aplicación mediante herramientas de Azure de Microsoft Visual Studio, puede usar las [páginas de propiedades](https://msdn.microsoft.com/library/ee405486) para establecer este valor.

## <a name="exported-certificate-does-not-include-private-key"></a>Certificado exportado no incluye clave privada

Para ejecutar una función web bajo SSL, debe asegurarse de que el certificado de administración exportado incluye la clave privada. Si utiliza el *Administrador de certificados de Windows* para exportar el certificado, asegúrese de seleccionar **Sí** para la opción de **exportar la clave privada** . Debe exportar el certificado en el formato PFX, que es el único formato compatible actualmente.

## <a name="next-steps"></a>Pasos siguientes

Ver más [artículos de solución de problemas](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) de servicios en la nube.

Ver más funciones escenarios en [serie de blogs de Kevin Williamson](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx)de reciclaje.

[RoleEntryPoint]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx
[OnStart]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx
[OnStop]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx
[Ejecutar]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx

<properties
   pageTitle="Supervisar localmente y diagnosticar servicios escritos con tela de servicio de Azure | Microsoft Azure"
   description="Obtenga información sobre cómo supervisar y diagnosticar los servicios escritos con Microsoft Azure servicio tela en un equipo de desarrollo local."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/24/2016"
   ms.author="subramar"/>


# <a name="monitor-and-diagnose-services-in-a-local-machine-development-setup"></a>Supervisar y diagnosticar servicios en una configuración de desarrollo de equipo local


> [AZURE.SELECTOR]
- [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
- [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)

Supervisión, detectar, diagnosticar y solucionar problemas de permiten para que servicios continuar con interrupción mínima de la experiencia del usuario. Supervisión y diagnóstico es crítica en un entorno de producción implementada real. La adopción de un modelo similar durante el desarrollo de servicios garantiza que la canalización de diagnóstico funciona cuando se mueve a un entorno de producción. Tela de servicio facilita a los desarrolladores de servicios implementar diagnósticos que pueden trabajar sin problemas a través de configuraciones de desarrollo de único equipo local y configuraciones de clúster de producción real.


## <a name="debugging-service-fabric-java-applications"></a>Depuración de aplicaciones de servicio tela Java

Para las aplicaciones de Java, [varios marcos de registro](http://en.wikipedia.org/wiki/Java_logging_framework) están disponibles. Puesto que `java.util.logging` es la opción predeterminada con JRE, también se usa para los [ejemplos de código de github](http://github.com/Azure-Samples/service-fabric-java-getting-started).  Siguiente se explica cómo configurar la `java.util.logging` framework. 
 
Con java.util.logging puede redirigir los registros de la aplicación a memoria, las secuencias de salida, archivos de consola o sockets. Para cada una de estas opciones, hay controladores predeterminados proporcionados en el marco de trabajo. Puede crear un `app.properties` archivo para configurar el controlador de archivo para la aplicación redirigir todos los registros a un archivo local. 

Fragmento de código siguiente contiene un ejemplo de configuración: 

```java 
handlers = java.util.logging.FileHandler
 
java.util.logging.FileHandler.level = ALL
java.util.logging.FileHandler.formatter = java.util.logging.SimpleFormatter
java.util.logging.FileHandler.limit = 1024000
java.util.logging.FileHandler.count = 10
java.util.logging.FileHandler.pattern = /tmp/servicefabric/logs/mysfapp%u.%g.log             
```

La carpeta señalada por el `app.properties` archivo debe existir. Después de la `app.properties` archivo se crea, debe modificar también la secuencia de comandos de punto de entrada, `entrypoint.sh` en la `<applicationfolder>/<servicePkg>/Code/` carpeta para establecer la propiedad `java.util.logging.config.file` a `app.propertes` archivo. La entrada debe ser similar el fragmento de código siguiente:

```sh 
java -Djava.library.path=$LD_LIBRARY_PATH -Djava.util.logging.config.file=<path to app.properties> -jar <service name>.jar
```
 
 
Esta configuración de resultados en los registros que se recopilan de forma rotación en `/tmp/servicefabric/logs/`. El **%u** y **%g** permiten crear más archivos, con nombres de archivo mysfapp0.log, mysfapp1.log y así sucesivamente. De forma predeterminada si ningún controlador está configurado explícitamente, se registra el controlador de consola. Se pueden ver los registros en el registro del sistema en /var/log/syslog.
 
Para obtener más información, vea los [ejemplos de código de github](http://github.com/Azure-Samples/service-fabric-java-getting-started).  



## <a name="next-steps"></a>Pasos siguientes
El mismo código de seguimiento que se agrega a la aplicación también funciona con los diagnósticos de la aplicación en un clúster de Azure. Consulte estos artículos que explique las diferentes opciones para las herramientas y describen cómo puede configurar el.
* [Cómo recopilar los registros de diagnósticos de Azure](service-fabric-diagnostics-how-to-setup-lad.md)

<properties 
   pageTitle="Guían de compatibilidad y la directiva de retirada de Azure invitado OS | Microsoft Azure" 
   description="Proporciona información sobre lo que Microsoft será compatible en relación con el sistema operativo invitado de Azure usados por los servicios de nube." 
   services="cloud-services" 
   documentationCenter="na" 
   authors="raiye" 
   manager="timlt" 
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd" 
   ms.date="10/24/2016"
   ms.author="raiye"/>

# <a name="azure-guest-os-supportability-and-retirement-policy"></a>Azure directiva de compatibilidad y retirada del SO invitado
La información de esta página está relacionado con el sistema operativo de invitado Azure ([SO invitado](cloud-services-guestos-update-matrix.md)) de trabajo de servicios en la nube y funciones web (PaaS). No se aplica a máquinas virtuales (IaaS). 

Microsoft tiene un publicados [admiten la directiva para el sistema operativo de invitado](http://support.microsoft.com/gp/azure-cloud-lifecycle-faq). Ahora, la página que está leyendo describe cómo se implementa la directiva.

La directiva es 

1. Microsoft admitirá **al menos las últimas dos familias del sistema operativo invitado**. Cuando se retira una familia, los clientes tienen 12 meses a partir de la fecha de retirada oficial para actualizar a una familia de SO invitado compatible más reciente.
2. Microsoft admitirá la **al menos las últimas versiones de dos de las familias de sistema operativo invitado admitidas**. 
3. Microsoft admitirá al **menos las últimas dos versiones de SDK de Azure**. Cuando se retira una versión de SDK, los clientes tendrán 12 meses a partir de la fecha de retirada oficial que actualice a una versión más reciente. 

A veces más de dos versiones o familias pueden ser compatible. Información de soporte técnico oficial de sistema operativo de invitado aparecerá en [Azure invitado OS versiones y matriz de compatibilidad de SDK](cloud-services-guestos-update-matrix.md).


## <a name="when-a-guest-os-family-or-version-is-retired"></a>Cuando se retira una familia de SO invitado o una versión 


Se introduce una nueva SO invitado **familia** algún tiempo después del lanzamiento de una nueva versión oficial del sistema operativo Windows Server. Cuando se introduce una nueva familia de sistema operativo de invitado, Microsoft retira la familia SO invitado más antigua. 

Sistema operativo invitado nuevas **versiones** se introducen sobre cada mes para incorporar las últimas actualizaciones MSRC. Debido a las actualizaciones mensuales normales, una versión de SO invitado está deshabilitados normalmente 60 días después de su publicación. Así mantiene al menos dos versiones del sistema operativo de invitado para cada familia disponible para su uso. 

### <a name="process-during-a-guest-os-family-retirement"></a>Proceso durante una retirada de familia SO invitado 


Una vez que se anuncia la jubilación, los clientes tienen un período de 12 meses "transición" antes de la familia anterior oficialmente se elimina del servicio. Este tiempo de transición puede ampliarse a discreción de Microsoft. Las actualizaciones se registrará en las [versiones de sistema operativo de invitado de Azure y matriz de compatibilidad de SDK](cloud-services-guestos-update-matrix.md).

Un proceso de retirada gradual comenzará 6 meses en el período de transición. Durante este periodo:

1. Microsoft le notificará a los clientes de la jubilación. 
2. La versión más reciente de Azure SDK no admite la familia de SO invitado retirada.
3. Nuevas implementaciones y reimplementaciones de servicios de nube no se permitirá en la familia retirada

Microsoft seguirá introducir la nueva versión del sistema operativo de invitado incorporar las últimas actualizaciones MSRC hasta el último día del período de transición, conocido como "fecha de vencimiento". En ese momento, los servicios de nube cualquier ejecución se no ser compatibles en el SLA de Azure. Microsoft tiene discreción Forzar actualización, eliminar o detener estos servicios después de esa fecha.



### <a name="process-during-a-guest-os-version-retirement"></a>Proceso durante una retirada de versión del SO invitado 
Si los clientes establece su sistema operativo de invitado para actualizar automáticamente, nunca tendrá que preocuparse de tratar con versiones del sistema operativo de invitado. Siempre utilizará la última versión de sistema operativo de invitado.

Versiones de sistemas operativos de invitado se publican cada mes. Debido a la velocidad de versiones regulares, cada versión tiene una duración fija.

En 60 días en el ciclo de vida está una versión "*deshabilitada*". "Deshabilitado" significa que se quita la versión desde el portal de clásico de Azure. También puede ya no establecerse desde el archivo de configuración de CSCFG. Implementaciones existentes permanecen en ejecución, pero no se permitirá nuevas implementaciones y las actualizaciones de código y configuración de implementaciones existentes. 

Más adelante, el sistema operativo invitado versión "*expira*" y las instalaciones de que la versión en ejecución es forzar actualizado y establecer para actualizar automáticamente el sistema operativo de invitado en el futuro. Expiración se realiza en lotes para que el período de tiempo de deshabilitación de caducidad puede variar. 

Estos períodos pueden realizarse más discreción de Microsoft para facilitar la transiciones de cliente. Se comunicará los cambios en las [versiones de sistema operativo de invitado de Azure y matriz de compatibilidad de SDK](cloud-services-guestos-update-matrix.md).



### <a name="notifications-during-retirement"></a>Notificaciones durante la jubilación 

* **Familia jubilación** <br>Utiliza Microsoft Azure notificación de portal clásica y entradas de blog. Los clientes que sigue usando una familia de SO invitado retirada le notificará a través de comunicación directa (correo electrónico, mensajes de portal, llamada de teléfono) para los administradores de servicio asignado. Todos los cambios se registrarán en esta página y la fuente RSS enumerados al principio de esta página. 


* **Retirada de versión** <br>Todos los cambios se registrarán en esta página y la fuente RSS enumerados al principio de esta página, incluida la versión, deshabilitado y fechas de vencimiento. Los administradores de servicios recibirán mensajes de correo electrónico si tienen implementaciones que se ejecuta en una versión del SO de invitado deshabilitado o familiares. Los intervalos de estos correos electrónicos pueden variar. Generalmente son al menos un mes antes de deshabilitación, aunque este intervalo no es un SLA oficial. 


## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

**¿Cómo puedo mitigar el impacto de la migración?**

Última familia de SO invitado debe usar para diseñar los servicios de nube. 

1. Empiece a planear la migración a una familia más reciente temprano. 
2. Configurar implementaciones de prueba temporal para probar su servicio de nube que se ejecuta en la nueva familia. 
3. Establecer la versión del sistema operativo de invitado en **automático** (osVersion = * en el archivo [.cscfg](cloud-services-model-and-package.md#cscfg) ) para que la migración a nuevas versiones de sistema operativo invitado se produce automáticamente.

**¿Qué ocurre si mi aplicación web requiere una mayor integración con el sistema operativo?**

Si la arquitectura de la aplicación web requiere más profunda dependencia en el sistema operativo subyacente, use plataforma admite capacidades como [tareas de inicio](cloud-services-startup-tasks.md) u otros mecanismos de capacidad de ampliación que existan en el futuro. Como alternativa, también puede usar [máquinas virtuales de Azure](https://azure.microsoft.com/documentation/scenarios/virtual-machines/) (IaaS – infraestructura como servicio), donde es responsable del mantenimiento del sistema operativo subyacente.
 
## <a name="next-steps"></a>Pasos siguientes
Revise la última [libera SO invitado](cloud-services-guestos-update-matrix.md).

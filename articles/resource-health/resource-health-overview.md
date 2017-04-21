<properties
   pageTitle="Información general de estado de recursos Azure | Microsoft Azure"
   description="Información general sobre el estado de los recursos de Azure"
   services="Resource health"
   documentationCenter="dev-center-name"
   authors="BernardoAMunoz"
   manager=""
   editor=""/>

<tags
   ms.service="resource-health"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="Supportability"
   ms.date="06/01/2016"
   ms.author="BernardoAMunoz"/>

# <a name="azure-resource-health-overview"></a>Información general de estado de recursos Azure

Estado de los recursos Azure es un servicio que expone el mantenimiento de recursos individuales de Azure y proporciona directrices que puede utilizar para solucionar problemas. En un entorno de nube donde no se puede obtener acceso directo a los servidores o elementos de la infraestructura, el objetivo de estado del recurso es para reducir el tiempo que dedican a los clientes en solución de problemas, en particular reducir el tiempo dedicado a determinar si establece la raíz del problema dentro de la aplicación o si está causado por un evento dentro de la plataforma Windows Azure.

## <a name="what-is-considered-a-resource-and-how-does-resource-health-decides-if-the-resource-is-healthy-or-not"></a>¿Qué se considera un recurso y cómo realiza el estado de los recursos decide si el recurso es correcto o no? 
Un recurso es una instancia de creados por el usuario de un tipo de recurso proporcionada por un servicio, por ejemplo: una máquina virtual, una aplicación Web o una base de datos SQL. 

Estado de los recursos se basa en señales emitidas por el recurso o el servicio para determinar si un recurso es correcto o no. Es importante tener en cuenta que actualmente sola cuentas de estado de recursos para el estado de un recurso específico escriba y no tiene en cuenta los otros elementos que pueden contribuir en el estado general. Por ejemplo, al notificar que se considera que el estado de una máquina virtual, solo la parte del cálculo de la infraestructura, es decir, problemas de la red no se mostrará en el estado de los recursos, a menos que haya una interrupción del servicio declarados, en cuyo caso, se se expone a través de la pancarta en la parte superior de la hoja. Para obtener más información acerca de interrupción del servicio se ofrece más adelante en este artículo. 

## <a name="how-is-resource-health-different-from-service-health-dashboard"></a>¿Cómo es diferente de panel de estado del servicio de estado de los recursos?

La información de estado de los recursos es más granular que proporcionan el panel de estado del servicio. Mientras SHD se comunica los eventos que afectan a la disponibilidad de un servicio en una región, estado de los recursos expone información relevante para un recurso específico, por ejemplo, vaya a exponer los eventos que afectan a la disponibilidad de una máquina virtual, una aplicación web o una base de datos SQL. Por ejemplo, si un nodo se reinicia inesperadamente, clientes cuyos máquinas virtuales de Windows se ejecuta en ese nodo podrán obtener el motivo por qué su VM estaba disponible durante un período de tiempo.   

## <a name="how-to-access-resource-health"></a>Cómo obtener acceso a estado de los recursos
Para los servicios disponibles a través de estado de los recursos, existen 2 formas para tener acceso a estado de los recursos.

### <a name="azure-portal"></a>Portal de Azure
El módulo de estado de recursos en el Portal de Azure, se proporciona información detallada sobre el estado del recurso así como recomienda acciones que varían según el estado actual del recurso. Este módulo proporciona la mejor experiencia al consultar el estado de los recursos, ya que facilita el acceso a otros recursos dentro del portal. Como se mencionó anteriormente, el conjunto de acciones recomendadas en el módulo de estado de recursos varían según el estado actual:

* Recursos correcto: puesto que no se ha detectado ningún problema que puede afectar el estado del recurso, las acciones se centran en ayudar al proceso de solución de problemas. Por ejemplo, proporciona acceso directo a módulo de solución de problemas, que ofrece instrucciones resolver la cara de los clientes de problemas más comunes.
* Recursos mal estado: problemas producidos por Azure, el módulo mostrará acciones Microsoft dura (o ha tenido) para recuperar el recurso. Para problemas producidos por el usuario acciones iniciadas, usará módulo puede tomar una lista de clientes de acciones para solucionan el problema y recuperación el recurso.  

Una vez que haya iniciado sesión en el Portal de Azure, hay dos maneras de obtener acceso a los módulos de estado de recursos: 

###<a name="open-the-resource-blade"></a>Abra la hoja de recursos
Abra la hoja de recursos para un recurso determinado. En el módulo de configuración que aparece junto a la hoja de recursos, haga clic en estado de los recursos para abrir el módulo de estado de recursos. 

![Módulo de estado de recursos](./media/resource-health-overview/resourceBladeAndResourceHealth.png)

### <a name="help-and-support-blade"></a>Ayuda y el módulo de compatibilidad
Abra el módulo de ayuda y soporte técnico, al hacer clic en el signo de interrogación en la esquina superior derecha y seleccione Ayuda + soporte técnico. 

**Desde la barra de navegación**

![Ayuda + soporte técnico](./media/resource-health-overview/HelpAndSupport.png)

Haciendo clic en el icono se abre el módulo de suscripción de estado de recursos que se mostrará una lista de todos los recursos en su suscripción. Junto a cada recurso, hay un icono que indica su estado. Al hacer clic en cada recurso se abrirá el módulo de estado de recursos.

**Icono de estado de recursos**

![Icono de estado de recursos](./media/resource-health-overview/resourceHealthTile.png)

## <a name="what-does-my-resource-health-status-mean"></a>¿Qué significa el estado de mantenimiento de recursos
Hay 4 estados de salud diferente que es posible que vea para el recurso.

### <a name="available"></a>Disponible
El servicio no ha detectado algún problema en la plataforma que podría que afectan a la disponibilidad del recurso. Esto se indica mediante un icono de marca de verificación verde. 

![Recurso está disponible](./media/resource-health-overview/Available.png)

### <a name="unavailable"></a>No disponible

En este caso el servicio ha detectado un problema en curso en la plataforma que afecta la disponibilidad de este recurso, por ejemplo, el nodo donde se ejecuta la máquina virtual reiniciado inesperadamente. Esto se indica mediante un icono de advertencia de color rojo. Se proporciona información adicional sobre el problema en la sección intermedia del módulo, incluidos: 

1.  ¿Qué medidas está adoptando Microsoft para recuperar el recurso 
2.  Una escala de tiempo detallada del problema, incluyendo el tiempo de resolución esperado
3.  Una lista de acciones para los usuarios recomendada 

![Recurso no está disponible](./media/resource-health-overview/Unavailable.png)

### <a name="unavailable--customer-initiated"></a>No disponible-cliente iniciado
El recurso no está disponible debido a una solicitud de cliente como detener un recurso o pedir que lo reinicie. Esto se indica mediante un icono informativo azul. 

![Recurso no está disponible debido a una acción iniciada del usuario](./media/resource-health-overview/userInitiated.png)

### <a name="unknown"></a>Desconocido
El servicio no ha recibido información sobre este recurso para más de cinco minutos. Esto se indica mediante un icono de signo de interrogación gris. 

Es importante que tenga en cuenta que no es una indicación definitiva que hay algún error con un recurso, por lo que los clientes deben seguir estas recomendaciones:

* Si se está ejecutando el recurso según lo esperado, pero su estado se establece en desconocido en el estado de los recursos, no existen problemas y puede esperar el estado del recurso para actualizar a buen estado después de unos minutos.
* Si hay problemas de acceso a los recursos y su estado es desconocido en el estado de los recursos, este podría ser una indicación temprana podría haber un problema y las investigaciones adicionales deben realizarse hasta que el estado se actualiza a correcto o mal estado

![Se desconoce el estado de los recursos](./media/resource-health-overview/unknown.png)

## <a name="service-impacting-events"></a>Servicio que afectan a eventos
Si el recurso puede verse afectado por un evento que afectan a servicio en curso, se mostrará una pancarta en la parte superior del módulo de estado de recursos. Al hacer clic en el encabezado se abrirá el módulo de eventos de auditoría, que se mostrará información adicional sobre la interrupción.

![Estado de los recursos puede verse afectado por un SIE](./media/resource-health-overview/serviceImpactingEvent.png)

## <a name="what-else-do-i-need-to-know-about-resource-health"></a>¿Qué más debo saber sobre el estado de los recursos?

### <a name="signal-latency"></a>Latencia de señal
Indica que fuente estado de los recursos, puede ser un máximo de 15 minutos retrasa, que puede provocar discrepancias entre el estado actual del recurso y su disponibilidad real. Es importante tener esto en cuenta lo ayudará a eliminar tiempo innecesario en instructora posibles problemas. 

### <a name="special-case-for-sql"></a>Caso especial para SQL 
Estado de los recursos informa del estado de la base de datos SQL, no el servidor de SQL server. Aunque esta ruta de inicio proporciona una imagen de mantenimiento más realista, requiere que varios componentes y servicios de tenerse en cuenta para determinar el estado de la base de datos. La señal actual se basa en inicios de sesión de la base de datos, lo que significa que las bases de datos que reciben los inicios de sesión normales (que incluye entre otras cosas, recibe solicitudes de ejecución de consulta) el mantenimiento estado regularmente aparecerá. Si no se tiene acceso a la base de datos durante un período de 10 minutos o más, se moverá el estado desconocido. Esto significa que la base de datos está disponible, simplemente que no hay señal emitida porque no se han realizado ningún inicio de sesión. Conectarse a la base de datos y ejecutar una consulta emite las señales necesarias para determinar y actualizar el estado de la base de datos.

## <a name="feedback"></a>Comentarios
¡Siempre estamos abiertos a comentarios y sugerencias! Envíenos sus [sugerencias](https://feedback.azure.com/forums/266794-support-feedback). Además, puede integrarse con nosotros a través de los [foros de MSDN](https://social.msdn.microsoft.com/Forums/azure)o [Twitter](https://twitter.com/azuresupport) .

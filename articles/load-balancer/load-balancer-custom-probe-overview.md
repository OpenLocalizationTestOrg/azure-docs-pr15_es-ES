<properties
  pageTitle="Sondeos personalizados de equilibrador de carga y supervisar estado | Microsoft Azure"
  description="Aprenda a usar sondeos personalizados para equilibrador de carga de Azure para supervisar instancias detrás del equilibrador de carga"
  services="load-balancer"
  documentationCenter="na"
  authors="sdwheeler"
  manager="carmonm"
  editor=""
  tags="azure-resource-manager"
/>
<tags
  ms.service="load-balancer"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="na"
  ms.workload="infrastructure-services"
  ms.date="10/24/2016"
  ms.author="sewhee" />

# <a name="understand-load-balancer-probes"></a>Conocer sondeos del equilibrador de carga

Equilibrador de carga de Azure ofrece la capacidad para supervisar el mantenimiento de instancias de servidor mediante sondeos. Cuando un sondeo deja de responder, equilibrador de carga deja de enviar nuevas conexiones a la instancia de mal estada. No se ven afectadas las conexiones existentes y nuevas conexiones se envían a instancias correcto.

Funciones de servicio de nube (funciones de trabajo y las funciones web) utilizan a un agente de invitado para la supervisión de sondeo. Deben estar configurados TCP o HTTP sondeos personalizados al usar máquinas virtuales detrás del equilibrador de carga.

## <a name="understand-probe-count-and-timeout"></a>Comprender el recuento de sondeo y tiempo de espera

Comportamiento de sondeo depende de:

- El número de sondeos correcta que permiten una instancia de la etiqueta como activo.
- El número de error sondeos que producen una instancia de la etiqueta como inactivo.

El valor de tiempo de espera y frecuencia establecido en SuccessFailCount determinar si una instancia se confirma que se ejecuta o no en ejecución. En el portal de Azure, el tiempo de espera se establece en dos veces el valor de la frecuencia.

La configuración de sondeo de todas las instancias de equilibrio de carga de un extremo (es decir, un conjunto equilibrio de carga) debe ser el mismo. Esto significa que no puede tener una configuración de sondeo diferente para cada instancia del rol o máquina virtual en el mismo servicio hospedado para una combinación de extremo concreto. Por ejemplo, cada instancia debe tener tiempos de espera y puertos locales idénticos.

>[AZURE.IMPORTANT] Un sondeo equilibrador de carga, usa la dirección IP 168.63.129.16. Esta dirección IP pública facilita la comunicación a recursos de la plataforma interno para la Traer-su-propietario-IP escenario de Azure red Virtual. La dirección IP pública virtual 168.63.129.16 se utiliza en todas las regiones y no cambiará. Le recomendamos que permita a esta dirección IP en las directivas de firewall local. Se debe no considerar un riesgo para la seguridad porque solo la plataforma de Windows Azure interna puede de origen de un mensaje de dicha dirección. Si no lo hace, habrá inesperados en una variedad de situaciones como configurar el mismo intervalo de direcciones IP de 168.63.129.16 y tener duplicado direcciones IP.

## <a name="learn-about-the-types-of-probes"></a>Obtenga más información sobre los tipos de sondeos

### <a name="guest-agent-probe"></a>Sondeo de agente de invitado

Este sondeo sólo está disponible para los servicios de nube de Azure. Equilibrador de carga utiliza el agente de invitado dentro de la máquina virtual y escucha y responde con una respuesta HTTP 200 OK solo cuando la instancia está en el estado de lista (es decir, no en otro estado como ocupado, reciclaje o detener).

Para obtener más información, vea [Configurar el archivo de definición de servicio (csdef) de salud sondeos](https://msdn.microsoft.com/library/azure/ee758710.aspx) o [empezar a crear un equilibrador de carga a través de Internet para servicios en la nube](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services).

### <a name="what-makes-a-guest-agent-probe-mark-an-instance-as-unhealthy"></a>¿Qué hace un sondeo de agente invitado marcar una instancia como en mal estado?

Si el agente de invitado deja de responder con HTTP 200 OK, el equilibrador de carga marca la instancia como no responde y deja de enviar tráfico a esa instancia. El equilibrador de carga continúa hacer ping a la instancia. Si el agente de invitado responde con un 200 HTTP, el equilibrador de carga envía tráfico a esa instancia de nuevo.

Cuando usa una función de web, el código del sitio Web normalmente se ejecuta en w3wp.exe, que no está supervisado por la Azure agente tela o invitado. Esto significa que no se mostrarán los errores en w3wp.exe (por ejemplo, las respuestas de HTTP 500) para el agente de invitado y el equilibrador de carga no tendrán esa instancia de rotación.

### <a name="http-custom-probe"></a>Sondeo HTTP personalizada

El sondeo de equilibrador de carga de HTTP personalizada reemplaza el sondeo de agente de invitado de forma predeterminada, lo que significa que puede crear su propia lógica personalizada para determinar el estado de la instancia del rol. El equilibrador de carga sondeos su punto final de cada 15 segundos, de forma predeterminada. La instancia se considera en el giro de equilibrador de carga si responde con un 200 HTTP dentro del período de tiempo de espera (segundos 31 de forma predeterminada).

Esto puede ser útil si desea implementar su propia lógica para quitar instancias de giro de equilibrador de carga. Por ejemplo, podría decidir quitar una instancia si está por encima de 90% CPU y devuelve un estado no es 200. Si tiene los roles de web que use w3wp.exe, también significa que recibe automática supervisión de su sitio Web, debido a errores en el código del sitio Web devolverá un estado no 200 al sondeo de equilibrador de carga.

>[AZURE.NOTE] El sondeo HTTP personalizado admite rutas de acceso relativas y sólo el protocolo HTTP. No se admite HTTPS.

### <a name="what-makes-an-http-custom-probe-mark-an-instance-as-unhealthy"></a>¿Qué hace un sondeo personalizada de HTTP marcar una instancia como en mal estado?

- La aplicación HTTP, devuelve un código de respuesta HTTP distinto 200 (por ejemplo, 403, 404 o 500). Esta es una confirmación positiva que la instancia de la aplicación debería tomarse fuera de servicio inmediatamente.
- El servidor HTTP no responde en absoluto después del período de tiempo de espera. Según el valor de tiempo de espera establecido, esto significa que sondeo varias solicitudes ir sin responder antes de que el sondeo está marcado como no ejecuta (es decir, antes de SuccessFailCount sondeos se envían).
- El servidor cierra la conexión a través de un restablecimiento TCP.

### <a name="tcp-custom-probe"></a>Sondeo TCP personalizada

TCP sondeos inician una conexión a través de un enlace de tres vías con el puerto definido.

### <a name="what-makes-a-tcp-custom-probe-mark-an-instance-as-unhealthy"></a>¿Qué hace un sondeo personalizada de TCP marcar una instancia como en mal estado?

- El servidor TCP no responde en absoluto después del período de tiempo de espera. Cuando el sondeo se marca como no ejecuta depende del número de solicitudes de sondeo error configurados para ir no respondidas antes de marcar el sondeo como no se ejecutan.
- El sondeo recibe un TCP restablecer desde la instancia de rol.

Para obtener más información acerca de cómo configurar un sondeo de estado HTTP o un sondeo TCP, vea [empezar a crear un equilibrador de carga a través de Internet en el Administrador de recursos con PowerShell](load-balancer-get-started-internet-arm-ps.md#create-lb-rules-nat-rules-a-probe-and-a-load-balancer).

## <a name="add-healthy-instances-back-into-load-balancer-rotation"></a>Agregar instancias correcto en giro del equilibrador de carga

TCP y HTTP sondeos se consideran en buen Estados y marcar como buen estado cuando la instancia de función:

- El equilibrador de carga, obtiene un sondeo positivo la primera vez que se inicia la máquina virtual.
- El número SuccessFailCount (descrito anteriormente) define el valor de éxito sondeos que son necesarios para marcar la instancia del rol como correcto. Si se ha eliminado una instancia de la función, el número de sondeos correcto y sucesivos igual o superior al valor de SuccessFailCount para marcar la instancia del rol como en ejecución.

>[AZURE.NOTE] Si el estado de una instancia de rol fluctúa, el equilibrador de carga ya espera antes de poner la instancia del rol de nuevo en buen estado. Esto se realiza mediante directivas para proteger la infraestructura y el usuario.

## <a name="use-log-analytics-for-load-balancer"></a>Usar el análisis de registro para equilibrador de carga

Puede usar [el análisis de registro de equilibrador de carga](load-balancer-monitor-log.md) para comprobar el estado de sondeo y el recuento de sondeo. Registro de puede usarse con Power BI o perspectivas operativas de Azure para proporcionar estadísticas sobre el estado de salud de equilibrador de carga.

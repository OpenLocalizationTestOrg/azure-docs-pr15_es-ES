<properties
   pageTitle="Introducción a la puerta de enlace de aplicación | Microsoft Azure"
   description="Esta página proporciona información general sobre el servicio de puerta de enlace de aplicación de equilibrio de carga de nivel 7, incluidos los tamaños de la puerta de enlace, afinidad de la sesión basada en cookies, equilibrio de carga de HTTP y SSL de descarga."
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="gwallace"/>

# <a name="application-gateway-overview"></a>Información general sobre la puerta de enlace de aplicaciones

## <a name="what-is-application-gateway"></a>¿Qué es la puerta de enlace de aplicación

Puerta de enlace de aplicaciones de Microsoft Azure proporciona controlador de entrega de la aplicación (ADC) como un servicio, que ofrece varias funciones para su aplicación de equilibrio de carga de nivel 7. Permite a los clientes optimizar la productividad de granja de servidores web mediante la descarga de terminación de CPU intensiva SSL a la puerta de enlace de aplicación. También proporciona otras funciones de enrutamiento de nivel 7 incluida turnos distribución del tráfico de entrada, en función de cookie afinidad de la sesión, enrutamiento de direcciones URL en función de la ruta de acceso y capacidad de alojar varios sitios Web detrás de una sola puerta de enlace de aplicación. Puerta de enlace de aplicación también tiene un firewall de aplicación web (WAFS) que protege la aplicación de la mayoría OWASP superior 10 comunes web vulnerabilidad. Puerta de enlace de aplicación puede configurarse como puerta de enlace hacia internet, interna única puerta de enlace o una combinación de ambos. Puerta de enlace de aplicación está totalmente Azure administrado, scalable y altamente disponibles. Proporciona amplia gama de capacidades de registro y diagnóstico para facilitar la tarea. Puerta de enlace de aplicación funciona con máquinas virtuales, servicios en la nube y las aplicaciones web hacia interna o externa.

Puerta de enlace de aplicación es un dispositivo dedicado virtual de la aplicación y se compone de varias instancias de trabajo para alta disponibilidad y escalabilidad. Cuando se crea una puerta de enlace de aplicación, un punto final (público VIP o IP ILB interna) se asociada y se usa para el tráfico de red de entrada. Equilibrador de carga de Azure proporciona esta dirección VIP o IP ILB funciona en el nivel de transporte (TCP/UDP) y tiene todos los tráfico de red está carga equilibrada a las instancias de trabajo de la puerta de enlace de aplicación. La puerta de enlace de aplicación, a continuación, redirige el tráfico HTTP/HTTPS basándose en su configuración, ya sea una máquina virtual, servicio, interno o una dirección IP externa de la nube. Para lo SLA y precios, consulte las páginas de [SLA](https://azure.microsoft.com/support/legal/sla/) y [precios](https://azure.microsoft.com/pricing/details/application-gateway/) .

## <a name="features"></a>Características

Puerta de enlace de aplicación actualmente admite la entrega de aplicación 7 de capa con las características siguientes:

- **[Servidor de aplicaciones web (Preview)](application-gateway-webapplicationfirewall-overview.md)** : el servidor de aplicaciones web (WAFS) en la puerta de enlace de aplicaciones de Azure protege aplicaciones web de ataques basados en web comunes como la inserción de SQL, ataques de scripting entre sitios y apropiaciones de sesión.
- **Equilibrio de carga HTTP** - puerta de enlace de aplicación proporciona equilibrio de carga de turnos. Equilibrio de carga se realiza en el nivel 7 y se usa para el tráfico de HTTP (S) solo.
- **Afinidad de la sesión basada en cookies** : esta característica es útil cuando desea mantener una sesión de usuario en el mismo back-end. Mediante la puerta de enlace administrada cookies, es posible dirigir el tráfico posteriores de una sesión de usuario para el mismo back-end para el procesamiento de la puerta de enlace de aplicación. Esta característica es importante en casos donde el estado de sesión se guarda localmente en el servidor back-end para una sesión de usuario.
- **[Descarga de capa de Sockets seguros (SSL)](application-gateway-ssl-arm.md)** - esta característica le lleva a la tarea costosa de descifrar el tráfico de HTTPS desactivar los servidores web. Al terminar la conexión SSL en la puerta de enlace de la aplicación y reenviar la solicitud al servidor sin cifrado, el servidor web se unburdened por el descifrado.  Puerta de enlace de aplicación cifra volver a la respuesta antes de enviarla al cliente. Esta característica es útil en escenarios donde se encuentra el back-end en la misma red virtual protegida como la puerta de enlace de aplicación en Azure.
- **[Llevar a cabo SSL](application-gateway-backend-ssl.md)** - puerta de enlace de aplicación admite el cifrado de llevar a cabo de tráfico. Para ello, puerta de enlace de aplicación finaliza la conexión SSL en la puerta de enlace de aplicación. La puerta de enlace aplica las reglas de enrutamiento para el tráfico, vuelve a cifra el paquete y reenvía el paquete a la back-end adecuados en función de las reglas de enrutamiento definidas. Cualquier respuesta desde el servidor web pasa por el mismo proceso al usuario final.
- **[Enrutamiento de contenido basado en la dirección URL](application-gateway-url-route-overview.md)** : esta característica proporciona la capacidad de usar diferentes servidores de back-end para el tráfico diferentes. Podría enrutar el tráfico de una carpeta en el servidor web o para un CDN un back-end diferente, reducir la carga innecesaria en back-ends que no sirven contenido específico.
- **[Enrutamiento de múltiples sitios](application-gateway-multi-site-overview.md)** - aplicación permite consolidar hasta 20 sitios Web de una puerta de enlace de aplicación única puerta de enlace.
- **[Soporte técnico Websocket](application-gateway-websocket.md)** - otra gran característica de puerta de enlace de aplicación es la compatibilidad nativa para Websocket.
- La **[supervisión de estado](application-gateway-probe-overview.md)** - puerta de enlace de aplicación proporciona sondeos de salud de predeterminada personalizada y la supervisión de recursos de back-end para supervisar la escenarios más específicos.

## <a name="benefits"></a>Ventajas

Puerta de enlace de aplicación es útil para:

- Aplicaciones que requieren solicitudes desde la misma sesión de usuario o cliente para llegar a la misma máquina virtual de back-end. Ejemplos de estas aplicaciones podrían compras carro aplicaciones y los servidores de correo web.
- Aplicaciones que desee liberar granjas de servidores web aérea de terminación SSL.
- Aplicaciones, como una red de entrega de contenido que requiere varias solicitudes HTTP en la misma conexión TCP de ejecución larga enrutar o cargar equilibrada a diferentes servidores de back-end.
- Aplicaciones que admiten tráfico websocket
- Proteger aplicaciones web de ataques basados en web comunes como inyección de SQL, ataques de scripting entre sitios y apropiaciones de sesión.

Puerta de enlace de aplicación equilibrio de carga como un servicio administrado Azure permite el aprovisionamiento de un equilibrador de carga de nivel 7 detrás del equilibrador de carga de software de Azure. Administrador de tráfico puede utilizarse para completar el escenario, tal como se muestra en la siguiente imagen. En el Administrador de tráfico ofrece redireccionamiento y disponibilidad, equilibrador de carga proporciona escalabilidad de región y disponibilidad de y puerta de enlace de aplicación proporciona equilibrio de carga de región cruzada nivel 7.

![asdasd](./media/application-gateway-introduction/tm-lb-ag-scenario.png)

[AZURE.INCLUDE [load-balancer-compare-tm-ag-lb-include.md](../../includes/load-balancer-compare-tm-ag-lb-include.md)]

## <a name="gateway-sizes-and-instances"></a>Instancias y tamaños de puerta de enlace

Actualmente se ofrece la puerta de enlace de aplicación en tres tamaños: pequeño, mediano y grande. Tamaños de instancia pequeña están pensados para desarrollo y escenarios de pruebas.

Hay dos SKU de puerta de enlace de aplicación: WAFS y estándar.

Puede crear hasta 50 puertas de enlace de aplicación por suscripción y la puerta de enlace de cada aplicación puede tener hasta 10 instancias. Cada puerta de enlace de aplicación puede constar de 20 escucha http. Para obtener una lista completa de los límites de la puerta de enlace de aplicación visite la página de [Los límites del servicio](../azure-subscription-service-limits.md#application-gateway) .

La siguiente tabla muestra un promedio de rendimiento para cada instancia de puerta de enlace de aplicación:

| Respuesta de la página de fondo | Pequeña | Medio | Grande|
|---|---|---|---|
| 6K | 7.5 Mbps | 13 Mbps | 50 Mbps |
|100K | 35 Mbps | 100 Mbps| 200 Mbps |

>[AZURE.NOTE] Estos valores son aproximada para un rendimiento de puerta de enlace de aplicación. El rendimiento real depende de diversos detalles del entorno, como el tamaño de página promedio, ubicación de instancias de back-end y la hora de procesamiento para servir una página. Para los números de rendimiento exacto, debe ejecutar sus propias pruebas, estos valores sólo se proporcionan para la Guía de planificación de capacidad.

## <a name="health-monitoring"></a>Seguimiento de estado

La puerta de enlace de aplicación Azure automáticamente supervisa el estado de las instancias de back-end mediante basic o sondeos estado personalizado. Mediante el uso de sondeos de estado, esto asegura que solo correcto hosts responden a tráfico. Para obtener más información, vea [información general de supervisión de estado de puerta de enlace de aplicación](application-gateway-probe-overview.md).

## <a name="configuring-and-managing"></a>Configurar y administrar

Su extremo de la puerta de enlace de aplicación puede tener una dirección IP pública o IP privado cuando está configurado. Puerta de enlace de aplicación está configurado dentro de una red virtual en su propia subred. La subred creado o utilizado de puerta de enlace de aplicación no puede contener todos los demás tipos de recursos, los únicos recursos que se permiten en la subred son otras puertas de enlace de aplicación. Para proteger los recursos de back-end del back-end servidores pueden estar dentro de una subred diferente en la misma red virtual como la puerta de enlace de aplicación. Esta subred adicional que no se requiere para las aplicaciones de back-end, como la puerta de enlace de aplicación puede llegar a la dirección ip, puerta de enlace de aplicación es capaz de proporcionar las capacidades de ADC para los servidores de back-end.

Puede crear y administrar una puerta de enlace de la aplicación con la API de REST, cmdlets de PowerShell, Azure CLI o [portal de Azure](https://portal.azure.com/).

## <a name="next-steps"></a>Pasos siguientes

Después de obtener información acerca de la puerta de enlace de aplicación, puede [crear una puerta de enlace de aplicación](application-gateway-create-gateway-portal.md) o puede [crear una puerta de enlace de aplicación descarga SSL](application-gateway-ssl-arm.md) a las conexiones HTTPS de equilibrio de carga.

Para obtener información sobre cómo crear una puerta de enlace de aplicación utilizando el enrutamiento de contenido basado en la dirección URL, vaya a [crear una puerta de enlace de aplicación utilizando el enrutamiento basado en la dirección URL](application-gateway-create-url-route-arm-ps.md) para obtener más información.


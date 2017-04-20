

<properties
   pageTitle="Salud información general sobre supervisión de puerta de enlace de aplicación de Azure | Microsoft Azure"
   description="Obtenga más información sobre las capacidades de supervisión de puerta de enlace de aplicaciones de Azure"
   services="application-gateway"
   documentationCenter="na"
   authors="georgewallace"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="gwallace" />

# <a name="application-gateway-health-monitoring-overview"></a>Introducción supervisión de estado de puerta de enlace de aplicaciones

Azure puerta de enlace de aplicación predeterminada supervisa el estado de todos los recursos en su grupo de back-end y quita automáticamente cualquier considera mal estado del grupo de recursos. Puerta de enlace de aplicación continúa supervisar las instancias negativos y agrega al grupo de back-end correcto una vez que estén disponibles y responden a sondeos de estado. Puerta de enlace de aplicación envía que el mantenimiento sondeos con el mismo puerto que se haya definido en la configuración de HTTP back-end. Así se garantiza que el sondeo está probando el mismo puerto que se va a utilizar los clientes para conectar con el servidor.

![ejemplo de sondeo de puerta de enlace de aplicación][1]

Además de usar la supervisión de sondeo de estado de forma predeterminada, también puede personalizar el sondeo de estado para adaptarlos a los requisitos de la aplicación. En este artículo se tratan predeterminada y sondeos de estado personalizado.

## <a name="default-health-probe"></a>Sondeo de estado de forma predeterminada

Una puerta de enlace de aplicación configurará automáticamente un sondeo de mantenimiento de forma predeterminada al no configurar cualquier configuración de sondeo personalizada. El comportamiento de supervisión funciona realizando una solicitud HTTP a las direcciones IP configuradas para el grupo de back-end. Para los sondeos de forma predeterminada si la configuración del servidor http está configurada para HTTPS, el sondeo usará https también para comprobar el estado de la back-ends.

Por ejemplo: configurar la puerta de enlace de aplicaciones para usar los servidores back-end A, B y C para recibir el tráfico de red HTTP en el puerto 80. La supervisión de estado de forma predeterminada, los tres servidores pruebas cada 30 segundos para una respuesta HTTP correcta. Una respuesta HTTP correcta tiene un [código de estado](https://msdn.microsoft.com/library/aa287675.aspx) entre 200 y 399.

Si se produce un error en la comprobación de sondeo predeterminada para que el servidor, la puerta de enlace de la aplicación quita de su grupo de back-end y deja de tráfico de red que fluye en este servidor. Continúa el sondeo de forma predeterminada comprobar servidor un cada 30 segundos. Cuando el servidor A correctamente responda a una solicitud de un sondeo de mantenimiento de forma predeterminada, se agrega nuevo estado correcto al grupo de back-end y tráfico inicia que fluye en el servidor nuevo.

### <a name="default-health-probe-settings"></a>Configuración predeterminada de sondeo de estado

|Sondeo (propiedad) | Valor | Descripción|
|---|---|---|
| Dirección URL de sondeo| http://127.0.0.1:\<puerto\>/ | Dirección URL |
| Intervalo | 30 | Intervalo de sondeo en segundos |
| Tiempo de espera  | 30 | Tiempo de espera de sondeo en segundos |
| Umbral de mal estado | 3 | Número de reintentos de sondeo. El servidor back-end se marca hacia abajo después de que el recuento de errores consecutivos sondeo alcanza el umbral de mal estado. |

> [AZURE.NOTE] El puerto siempre será el mismo puerto que la configuración de HTTP back-end.

El sondeo de forma predeterminada examina solo http://127.0.0.1:\<puerto\> para determinar el estado de salud. Si necesita configurar el sondeo de estado para ir a una dirección URL personalizada o modifique cualquier otra configuración, deberá usar sondeos personalizados como se describe en los siguientes pasos.

## <a name="custom-health-probe"></a>Sondeo de estado personalizados

Sondeos personalizados le permiten tener un control más detallado la supervisión de estado. Al usar sondeos personalizados, puede configurar el intervalo de sondeo, la dirección URL y ruta de acceso para probar y cuántas respuestas erróneas para aceptar antes de marcar la instancia de grupo de back-end como en mal estado.

### <a name="custom-health-probe-settings"></a>Configuración de sondeo de estado personalizados

En la tabla siguiente proporciona definiciones de las propiedades de un sondeo de estado personalizado.

|Sondeo (propiedad)| Descripción|
|---|---|
| Nombre | Nombre del sondeo. Este nombre se usa para hacer referencia al sondeo en la configuración de HTTP back-end. |
| Protocolo | Protocolo utilizado para enviar el sondeo. El sondeo usará el protocolo definido en la configuración de HTTP back-end |
| Host |  Nombre de host para enviar el sondeo. Aplicable únicamente cuando varios sitios está configurado en puerta de enlace de aplicación, en caso contrario, use '127.0.0.1'. Esto es diferente del nombre de host de máquina virtual. |
| Ruta de acceso | Ruta de acceso relativa del sondeo. La ruta de acceso válida comienza desde '/'. |
| Intervalo | Sondeo intervalo en segundos. Este es el intervalo de tiempo entre dos sondeos consecutivos.|
| Tiempo de espera | Sondeo de tiempo de espera en segundos. El sondeo se marcará como error si no se recibe una respuesta válida dentro de un período de tiempo de espera. |
| Umbral de mal estado | Número de reintentos de sondeo. El servidor back-end se marca hacia abajo después de que el recuento de errores consecutivos sondeo alcanza el umbral de mal estado. |

> [AZURE.IMPORTANT] Si la puerta de enlace de aplicación está configurada para un solo sitio, de forma predeterminada, el Host de nombre debe especificarse como '127.0.0.1', a menos que lo contrario configurado en sondeo personalizada.
Para referencia se envía un sondeo personalizada a \<protocolo\>://\<host\>:\<puerto\>\<ruta\>. El puerto utilizado será el mismo puerto según se define en la configuración de HTTP back-end.

## <a name="next-steps"></a>Pasos siguientes

Después de aprendizaje sobre supervisión de estado de puerta de enlace de aplicación, puede configurar un [sondeo de estado personalizados](application-gateway-create-probe-portal.md) en el portal de Azure o un [sondeo de estado personalizados](application-gateway-create-probe-ps.md) usando PowerShell y el modelo de implementación de administrador de recursos de Azure.

[1]: ./media/application-gateway-probe-overview/appgatewayprobe.png
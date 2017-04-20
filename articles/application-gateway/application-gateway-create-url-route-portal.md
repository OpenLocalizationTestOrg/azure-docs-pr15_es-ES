<properties
   pageTitle="Crear una regla basada en la ruta de acceso de una puerta de enlace de aplicación a través del portal | Microsoft Azure"
   description="Obtenga información sobre cómo crear una regla basada en la ruta de acceso de una puerta de enlace de aplicación a través del portal"
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

# <a name="create-a-path-based-rule-for-an-application-gateway-by-using-the-portal"></a>Crear una regla basada en la ruta de acceso de una puerta de enlace de aplicación a través del portal

> [AZURE.SELECTOR]
- [Portal de Azure](application-gateway-create-url-route-portal.md)
- [Azure PowerShell de administrador de recursos](application-gateway-create-url-route-arm-ps.md)

Enrutamiento basado en la ruta de acceso de direcciones URL permite asociar rutas basándose en la dirección URL de la solicitud Http. Comprueba si hay una ruta a un grupo de back-end configurado para las listas de dirección URL de la puerta de enlace de aplicación y enviar el tráfico de red al grupo definido de back-end. Un uso común de enrutamiento basado en la dirección URL es la carga de solicitudes de saldo para distintos tipos de contenido a los grupos de otro servidor back-end.

Enrutamiento basado en la dirección URL introduce un nuevo tipo de regla a la puerta de enlace de aplicación. Puerta de enlace de aplicación tiene dos tipos de reglas: reglas basadas en ruta de acceso y básicas. Tipo de regla básica proporciona turnos de servicio para los grupos de back-end mientras reglas basadas en ruta de acceso además de distribución de turnos, también tiene trama de ruta de acceso de la dirección URL de la solicitud en cuenta al elegir la agrupación de back-end.

## <a name="scenario"></a>Escenario

El siguiente escenario se va a crear una regla basada en la ruta de acceso de una puerta de enlace de aplicación existente.
El escenario, se supone que ya ha seguido los pasos para [crear una puerta de enlace de aplicación](application-gateway-create-gateway-portal.md).

![ruta de dirección URL][scenario]

## <a name="createrule"></a>Crear una regla basada en la ruta de acceso

Una regla basada en la ruta de acceso requiere su propia escucha, antes de crear la regla Asegúrese de comprobar tiene un agente de escucha disponible para usar.

### <a name="step-1"></a>Paso 1

Vaya a http://portal.azure.com y seleccione una puerta de enlace de aplicación existente. Haga clic en **reglas**

![Información general sobre la puerta de enlace de aplicaciones][1]

### <a name="step-2"></a>Paso 2

Haga clic en el botón **basado en la ruta de acceso** para agregar una nueva regla de ruta de acceso.

### <a name="step-3"></a>Paso 3

El módulo de **Agregar regla de ruta de acceso** tiene dos secciones. La primera sección es donde se definen la escucha, el nombre de la regla y la configuración de la ruta de acceso predeterminada. La configuración predeterminada de la ruta de acceso es para rutas que no entran en la ruta personalizada basada en la ruta de acceso. La segunda sección del módulo **Agregar regla de ruta de acceso** es donde definir las reglas de ruta de acceso a sí mismos.

**Configuración básica**

- **Nombre** : se trata de un nombre descriptivo para la regla que es accesible en el portal.
- **Escucha** : esta es la escucha que se usa para la regla.
- **Grupo de back-end predeterminado** : esta opción es la configuración que define el back-end que se usará para la regla predeterminada
- **Configuración predeterminada HTTP** : esta opción es la configuración que define la configuración de HTTP que se usará para la regla predeterminada.

**Reglas basadas en ruta de acceso**

- **Nombre** : se trata de un nombre descriptivo para la regla de ruta de acceso.
- **Rutas de acceso** - esta configuración define la regla buscará al reenviar el tráfico de la ruta de acceso
- **Grupo de back-end** : esta opción es la configuración que define el back-end que se usará para la regla
- **Configuración de HTTP** : esta opción es la configuración que define la configuración de HTTP que se usará para la regla.

>[AZURE.IMPORTANT] Rutas de acceso: La lista de los patrones de ruta para que coincida con. Cada uno debe empezar por / y el único lugar un "\*" se permite al final. Ejemplos válidos son /xyz /xyz* o /xyz/*.  

![Agregar módulo de regla basada en la ruta de acceso con información rellenado][2]

Agregar una regla basada en la ruta de acceso a una puerta de enlace de aplicación existente es un proceso fácil a través del portal. Una vez que se ha creado una regla basada en la ruta de acceso, se puede editar para agregar más reglas fácilmente. 

![agregar reglas de ruta adicionales][3]

## <a name="next-steps"></a>Pasos siguientes

Para obtener información sobre cómo configurar descarga SSL con la puerta de enlace de aplicaciones de Azure vea [Configurar la descarga de SSL](application-gateway-ssl-portal.md)

[1]: ./media/application-gateway-create-url-route-portal/figure1.png
[2]: ./media/application-gateway-create-url-route-portal/figure2.png
[3]: ./media/application-gateway-create-url-route-portal/figure3.png
[scenario]: ./media/application-gateway-create-url-route-portal/scenario.png
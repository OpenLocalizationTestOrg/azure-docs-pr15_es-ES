<properties
   pageTitle="Requisitos de QoS para ExpressRoute | Microsoft Azure"
   description="Esta página proporciona requisitos detallados para configurar y administrar QoS para circuitos ExpressRoute."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="cherylmc"/>

# <a name="expressroute-qos-requirements"></a>Requisitos de ExpressRoute QoS

Skype empresarial tiene varias cargas de trabajo que requieren tratamiento de QoS diferenciado. Si piensa utilizar servicios de voz a través de ExpressRoute, debe cumplir los requisitos que se describen a continuación.

![](./media/expressroute-qos/expressroute-qos.png)

>[AZURE.NOTE] Requisitos de QoS se aplican a la interconexión solo de Microsoft. Los valores DSCP en el tráfico de red que se ha recibido en interconexión pública de Azure e interconexión privada Azure se restablecerán a 0. 

En la tabla siguiente proporciona una lista de marcado de DSCP utilizado Skype para la empresa. Para obtener más información, consulte [Administrar QoS para Skype empresarial](https://technet.microsoft.com/library/gg405409.aspx) .

| **Clase de tráfico** | **Tratamiento (marcado DSCP)** | **Skype para cargas de trabajo de empresa** |
|---|---|---|
| **Voz** | EF (46) | Skype / voz de Lync |
| **Interactivo** | AF41 (34) | Vídeo |
|   | AF21 (18) | Uso compartido de aplicaciones | 
| **Predeterminado** | AF11 (10) | Transferencia de archivos|
|   | CS0 (0) | Algo más| 


- Debe clasificar las cargas de trabajo y marque los valores DSCP correctos. Siga las instrucciones proporcionadas [aquí](https://technet.microsoft.com/library/gg405409.aspx) sobre cómo establecer marcas DSCP en su red.

- Debe configurar y varias colas QoS de soporte técnico de su red. Voz debe ser una clase independiente y recibir el tratamiento de EF especificado en RFC 3246. 

- Puede decidir el mecanismo de cola, la directiva de detección de congestión y la asignación de ancho de banda por clase de tráfico. Sin embargo, se debe mantener el DSCP marcar para Skype para las cargas de trabajo de la empresa. Si está utilizando marcas DSCP no enumerados anteriormente, por ejemplo, AF31 (26), debe escribir este valor DSCP 0 antes de enviarlo a Microsoft. Microsoft sólo envía paquetes marcados con el valor DSCP que se muestra en la tabla anterior. 

## <a name="next-steps"></a>Pasos siguientes

- Consulte los requisitos de [enrutamiento](expressroute-routing.md) y [NAT](expressroute-nat.md).
- Vea los siguientes vínculos para configurar la conexión de ExpressRoute.

    - [Crear un circuito ExpressRoute](expressroute-howto-circuit-classic.md)
    - [Configurar el enrutamiento](expressroute-howto-routing-classic.md)
    - [Vincular un VNet a un circuito ExpressRoute](expressroute-howto-linkvnet-classic.md)

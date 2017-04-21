<properties
   pageTitle="Agregar un firewall de generación siguiente en el centro de seguridad de Azure | Microsoft Azure"
   description="Este documento muestra cómo implementar las recomendaciones de centro de seguridad de Azure **Agregar un Firewall de generación siguiente** y **tráfico de ruta a través de NGFW solo**."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/26/2016"
   ms.author="terrylan"/>

# <a name="add-a-next-generation-firewall-in-azure-security-center"></a>Agregar un Firewall de generación siguiente en el centro de seguridad de Azure

Centro de seguridad de Azure puede recomendar agregar un firewall de generación siguiente (NGFW) de un asociado de Microsoft para aumentar la protección de la seguridad. Este documento le guiará a través de un ejemplo de cómo hacerlo.

> [AZURE.NOTE] Este documento presenta el servicio mediante el uso de una implementación de ejemplo.  No es una guía paso a paso.

## <a name="implement-the-recommendation"></a>Implementar la recomendación

1. En el módulo de **recomendaciones** , seleccione **Agregar un Firewall de generación siguiente**.
![Agregue un Firewall de generación siguiente][1]

2. En el módulo de **Agregar un Firewall de generación siguiente** , seleccione un punto final.
![Seleccione un punto final][2]

3. Se abre un segundo módulo de **Agregar un Firewall de generación siguiente** . Puede usar una solución existente si está disponible, o puede crear uno nuevo. En este ejemplo no existen soluciones existentes para que vamos a crear un nuevo NGFW.
![Crear nuevo Firewall generación siguiente][3]

4. Para crear un nuevo NGFW, seleccione una solución en la lista de socios integrados. En este ejemplo seleccionamos **Punto de verificación**.
![Seleccione siguiente Firewall de generación solución][4]

5. El módulo de **Punto de comprobación** se abre le proporciona información sobre la solución de partner. Seleccione **crear** en el módulo de información.
![Módulo de información del Firewall][5]

6. Se abre el módulo de **máquina virtual de crear** . Aquí puede especificar la información necesaria para el control de una máquina virtual (VM) que se ejecutará la NGFW. Siga los pasos y proporcione la información de NGFW requerida. Seleccione Aceptar para aplicar.
![Crear la máquina virtual para ejecutar NGFW][6]

## <a name="route-traffic-through-ngfw-only"></a>Enrutar el tráfico a través de NGFW solo

Volver a la hoja de **recomendaciones** . Después de agregar un NGFW a través del centro de seguridad, llamado **enrutar el tráfico a través de NGFW solo**, se genera una nueva entrada. Esta recomendación sólo se crea si ha instalado la NGFW a través del centro de seguridad. Si tiene extremos a través de Internet, centro de seguridad se recomienda configurar reglas de grupo de seguridad de red que forzar el tráfico a su máquina virtual a través de su NGFW.

1. En el **módulo de recomendaciones**, seleccione **enrutar el tráfico a través de NGFW solo**.
![Enrutar el tráfico a través de NGFW solo][7]

2. Se abrirá el módulo **enrutar el tráfico a través de NGFW solo** que muestra máquinas virtuales que puede enrutar el tráfico a. Seleccione una máquina virtual de la lista.
![Seleccione una máquina virtual][8]

3. Un módulo de la máquina virtual seleccionada se abrirá con las reglas de entrada relacionadas. Una descripción proporciona más información sobre posibles pasos siguientes. Seleccione **Editar las reglas de entrada** para continuar con la edición de una regla de entrada. Las expectativas es que **origen** no está configurada para **cualquier** para los extremos a través de Internet vinculados con la NGFW. Para obtener más información sobre las propiedades de la regla de entrada, vea [reglas de GSN](../virtual-network/virtual-networks-nsg.md#nsg-rules).
![Configurar reglas para limitar el acceso][9]
![Editar regla de entrada][10]

## <a name="see-also"></a>Vea también

Este documento le mostramos cómo implementar la recomendación del centro de seguridad "Agregar un Firewall de generación siguiente". Para obtener más información sobre NGFWs y la solución de partner de punto de verificación, vea lo siguiente:

- [Firewall de última generación](https://en.wikipedia.org/wiki/Next-Generation_Firewall)
- [Punto de verificación vSEC](https://azure.microsoft.com/marketplace/partners/checkpoint/check-point-r77-10/)

Para obtener más información sobre el centro de seguridad, consulte lo siguiente:

- [Configurar directivas de seguridad en el centro de seguridad de Azure](security-center-policies.md) : Obtenga información sobre cómo configurar directivas de seguridad.
- [Recomendaciones de seguridad de la administración de centro de seguridad de Azure](security-center-recommendations.md) : Descubra cómo recomendaciones ayudar a proteger los recursos de Azure.
- [Supervisión del estado de seguridad del centro de seguridad de Azure](security-center-monitoring.md) : Obtenga información sobre cómo supervisar el estado de los recursos de Azure.
- [Administrar y responder a las alertas de seguridad en el centro de seguridad de Azure](security-center-managing-and-responding-alerts.md) : Obtenga información sobre cómo administrar y responder a las alertas de seguridad.
- [Las soluciones de asociados de supervisión con el centro de seguridad de Azure](security-center-partner-solutions.md) : Obtenga información sobre cómo supervisar el estado de las soluciones de socios.
- [Preguntas más frecuentes sobre centro de seguridad de azure](security-center-faq.md) --buscar con frecuencia preguntas más frecuentes sobre el uso del servicio.
- Entradas de [blog de seguridad de azure](http://blogs.msdn.com/b/azuresecurity/) --buscar blog sobre cumplimiento y la seguridad de Azure.

<!--Image references-->
[1]: ./media/security-center-add-next-gen-firewall/add-next-gen-firewall.png
[2]: ./media/security-center-add-next-gen-firewall/select-an-endpoint.png
[3]: ./media/security-center-add-next-gen-firewall/create-new-next-gen-firewall.png
[4]: ./media/security-center-add-next-gen-firewall/select-next-gen-firewall.png
[5]: ./media/security-center-add-next-gen-firewall/firewall-solution-info-blade.png
[6]: ./media/security-center-add-next-gen-firewall/create-virtual-machine.png
[7]: ./media/security-center-add-next-gen-firewall/route-traffic-through-ngfw.png
[8]: ./media/security-center-add-next-gen-firewall/select-vm.png
[9]: ./media/security-center-add-next-gen-firewall/configure-rules-to-limit-access.png
[10]: ./media/security-center-add-next-gen-firewall/edit-inbound-rule.png

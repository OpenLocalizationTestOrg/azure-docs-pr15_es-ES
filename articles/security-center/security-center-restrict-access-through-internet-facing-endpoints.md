<properties
   pageTitle="Restringir el acceso a través de los extremos a través de Internet en el centro de seguridad de Azure | Microsoft Azure"
   description="Este documento muestra cómo implementar la recomendación del centro de seguridad de Azure **restringir el acceso a través de extremo de hacia Internet**."
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

# <a name="restrict-access-through-internet-facing-endpoints-in-azure-security-center"></a>Restringir el acceso a través de los extremos a través de Internet en el centro de seguridad de Azure

Centro de seguridad de Azure se recomienda restringir el acceso a través de los extremos a través de Internet si alguno de los grupos de seguridad de la red (NSGs) tiene una o más reglas de entrada para permitir el acceso desde "cualquier" dirección IP de origen. Abrir el acceso a "cualquier" puede habilitar atacantes tener acceso a los recursos. Centro de seguridad se recomienda editar estas reglas de entrada para restringir el acceso a las direcciones IP de origen que realmente necesitan acceso.

Esta recomendación se genera por cualquier puerto no web que tiene "cualquiera" como origen.

> [AZURE.NOTE] Este documento presenta el servicio mediante el uso de una implementación de ejemplo. No es una guía paso a paso.

## <a name="implement-the-recommendation"></a>Implementar la recomendación

1. En el **módulo de recomendaciones**, seleccione **restringir el acceso a través de extremo de hacia Internet**.
![Restringir el acceso a través de Internet opuestas extremo][1]

2. Se abrirá el módulo de **restringir el acceso a través de extremo de hacia Internet**. Este módulo enumera las máquinas virtuales (VM) con las reglas de entrada que crear un problema de seguridad. Seleccione una máquina virtual.
![Seleccione una máquina virtual][2]

3. El módulo **GSN** muestra información de grupo de seguridad de red, relacionada con las reglas de entrada y la máquina virtual asociada. Seleccione **Editar las reglas de entrada** para continuar con la edición de una regla de entrada.
![Módulo de grupo de seguridad de red][3]

4. En el módulo de **reglas de seguridad entrante** , seleccione la regla de entrada para editar. En este ejemplo, seleccionaremos **AllowWeb**.
![Reglas de seguridad de entrada][4]

  Tenga en cuenta que también puede seleccionar **reglas predeterminadas** para ver el conjunto de reglas predeterminado contiene todos los NSGs. No se puede eliminar las reglas de forma predeterminada pero, porque se asignan una prioridad inferior, puede reemplazar las reglas que cree. Más información acerca de [las reglas de forma predeterminada](../virtual-network/virtual-networks-nsg.md#default-rules).
![Reglas predeterminadas][5]

5. En el módulo **AllowWeb** , editar las propiedades de la regla de entrada para que el **origen** es una dirección IP o un bloque de direcciones IP. Para obtener más información sobre las propiedades de la regla de entrada, vea [reglas de GSN](../virtual-network/virtual-networks-nsg.md#nsg-rules).

  ![Editar regla de entrada][6]

## <a name="see-also"></a>Vea también

En este artículo le mostramos cómo implementar la recomendación del centro de seguridad "Restringir el acceso a través de extremo de hacia Internet". Para obtener más información acerca de cómo habilitar NSGs y reglas, consulte lo siguiente:

- [¿Qué es un grupo de seguridad de la red (GSN)?](../virtual-network/virtual-networks-nsg.md)
- [Cómo administrar NSGs con el portal de Azure](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)

Para obtener más información sobre el centro de seguridad, consulte lo siguiente:

- [Configurar directivas de seguridad en el centro de seguridad de Azure](security-center-policies.md): Obtenga información sobre cómo configurar directivas de seguridad para suscripciones de Azure y grupos de recursos.
- [Administrar las recomendaciones de seguridad en el centro de seguridad de Azure](security-center-recommendations.md): Descubra cómo recomendaciones ayudar a proteger los recursos de Azure.
- [Supervisión en el centro de seguridad de Azure de estado de seguridad](security-center-monitoring.md): Obtenga información sobre cómo supervisar el estado de los recursos de Azure.
- [Administrar y responder a las alertas de seguridad en el centro de seguridad de Azure](security-center-managing-and-responding-alerts.md): Obtenga información sobre cómo administrar y responder a las alertas de seguridad.
- [Las soluciones de asociados de supervisión con el centro de seguridad de Azure](security-center-partner-solutions.md) : Obtenga información sobre cómo supervisar el estado de las soluciones de socios.
- [Preguntas más frecuentes sobre centro de seguridad de azure](security-center-faq.md)--buscar con frecuencia preguntas más frecuentes sobre el uso del servicio.
- [Blog de seguridad de azure](http://blogs.msdn.com/b/azuresecurity/): obtener las últimas noticias de seguridad de Azure y la información.

<!--Image references-->
[1]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/restrict-access-thru-internet-facing-endpoint.png
[2]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/select-a-vm.png
[3]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/network-security-group-blade.png
[4]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/inbound-security-rules.png
[5]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/default-rules.png
[6]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/edit-inbound-rule.png

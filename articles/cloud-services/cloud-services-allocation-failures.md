<properties
    pageTitle="Solución de problemas de asignación de servicio de nube | Microsoft Azure"
    description="Solución de problemas de asignación cuando se implementación Servicios de nube de Azure"
    services="azure-service-management, cloud-services"
    documentationCenter=""
    authors="simonxjx"
    manager="felixwu"
    editor=""
    tags="top-support-issue"/>

<tags
    ms.service="cloud-services"
    ms.workload="na"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/12/2016"
    ms.author="v-six"/>



# <a name="troubleshooting-allocation-failure-when-you-deploy-cloud-services-in-azure"></a>Solución de problemas de asignación cuando se implementación Servicios de nube de Azure

## <a name="summary"></a>Resumen
Al implementar instancias a un servicio de nube o agregar nuevo sitio web o instancias de la función de trabajo, Microsoft Azure asigna recursos de cálculo. En ocasiones puede recibir errores al realizar estas operaciones antes de llegar a los límites de suscripción de Azure. En este artículo se explica las causas de algunos de los errores comunes de asignación y sugiere posibles soluciones. La información también puede ser útil cuando planea la implementación de los servicios.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

### <a name="background--how-allocation-works"></a>Fondo: cómo funciona la asignación
Los servidores de centros de datos de Azure se dividen en clústeres. Una nueva solicitud de asignación de servicio de nube se intenta en varios clústeres. Cuando se implementa la primera instancia a un servicio de nube (en ensayo o producción), servicio de nube obtiene anclada a un clúster. Cualquier implementaciones adicionales para el servicio de nube sucederá en el mismo clúster. En este artículo, deberá Esto se conoce como "anclar a un clúster". Diagrama 1 a continuación muestra el caso de una asignación normal que intentó en varios clústeres; Diagrama 2 muestra el caso de una asignación que ha anclado a clúster 2 porque es donde se hospeda el CS_1 de servicio de nube existente.

![Diagrama de asignación](./media/cloud-services-allocation-failure/Allocation1.png)

### <a name="why-allocation-failure-happens"></a>¿Por qué sucede errores de asignación
Cuando se ancla una solicitud de asignación a un clúster, hay más posibilidades de errores buscar recursos gratuitos desde el grupo de recursos disponibles se limita a un clúster. Además, si la solicitud de asignación está anclado a un clúster pero el tipo de recurso solicitado no es compatible con ese clúster, se producirá un error en la solicitud incluso si el clúster tiene recursos gratuito. Diagrama 3 siguiente muestra el caso donde una asignación anclada se produce un error porque el clúster de candidatos sólo tiene liberar los recursos. Diagrama 4 muestra el caso donde una asignación anclada se produce un error porque el clúster de candidatos solo no admite el tamaño de memoria virtual solicitado, aunque el clúster tiene liberar los recursos.

![Error de asignación anclada](./media/cloud-services-allocation-failure/Allocation2.png)

## <a name="troubleshooting-allocation-failure-for-cloud-services"></a>Solución de problemas de asignación para los servicios de nube
### <a name="error-message"></a>Mensaje de error
Es posible que vea el siguiente mensaje de error:

    "Azure operation '{operation id}' failed with code Compute.ConstrainedAllocationFailed. Details: Allocation failed; unable to satisfy constraints in request. The requested new service deployment is bound to an Affinity Group, or it targets a Virtual Network, or there is an existing deployment under this hosted service. Any of these conditions constrains the new deployment to specific Azure resources. Please retry later or try reducing the VM size or number of role instances. Alternatively, if possible, remove the aforementioned constraints or try deploying to a different region."

### <a name="common-issues"></a>Problemas comunes
Estos son los escenarios comunes de asignación que producen una solicitud de asignación a estar anclada a un único clúster.

- Implementar para franja de ensayo - si un servicio de nube tiene una implementación en cualquier franja, a continuación, el servicio de nube todo está anclado a un clúster específico.  Esto significa que si una implementación ya existe en la franja de producción, a continuación, una nueva implementación de ensayo solo se puede asignar en el mismo clúster de la franja de producción. Si el clúster se está acercando a capacidad, puede fallar la solicitud.

- Debe asignar escala: agregar nuevas instancias a un servicio de nube existente en el mismo clúster.  Normalmente se debe asignar pequeña escala solicitudes, pero no siempre. Si el clúster se está acercando a capacidad, puede fallar la solicitud.

- Grupo afinidad - implementación nueva en un servicio de nube vacía puede asignarse por la estructura de cualquier clúster de esa región, a menos que el servicio de nube está anclado a un grupo de afinidad. Implementaciones al mismo grupo afinidad intentará en el mismo clúster. Si el clúster se está acercando a capacidad, puede fallar la solicitud.

- Grupo afinidad vNet - redes virtuales anteriores estaban vinculados a grupos de afinidad en lugar de regiones y servicios en la nube en estas redes Virtual podrían estar anclados al grupo afinidad clúster. En el clúster anclado se intentará implementaciones a este tipo de red virtual. Si el clúster se está acercando a capacidad, puede fallar la solicitud.

## <a name="solutions"></a>Soluciones

1. Implementar un nuevo servicio de nube: esta solución es probable que sea más éxito ya que permite la plataforma elegir entre todos los clústeres en esa región.

    - Implementar la carga de trabajo en un nuevo servicio de nube  

    - Actualizar el registro CNAME o un registro que apunte el tráfico al nuevo servicio de nube

    - Una vez que se va cero tráfico al sitio antiguo, puede eliminar el servicio de nube antiguo. Esta solución debe incurre en cero el tiempo de inactividad.

2. Eliminar de producción y ensayo ranuras: esta solución conservará su nombre DNS existente, pero hará que el tiempo de inactividad de la aplicación.

    - Eliminar la producción y ensayo casillas de un servicio de nube existente para que el servicio de nube está vacío y, a continuación

    - Crear una nueva implementación en el servicio de nube existente. Esto intentará volver a asignación en todos los clústeres en la región. Asegúrese de que el servicio de nube no está vinculado a un grupo de afinidad.

3. IP reservada - esta solución conservará su dirección IP existente de direcciones, pero hará que el tiempo de inactividad de la aplicación.  

    - Crear un IP reservada para su implementación existente con Powershell

    ```
    New-AzureReservedIP -ReservedIPName {new reserved IP name} -Location {location} -ServiceName {existing service name}
    ```

    - Siga #2 desde encima, y asegúrese de que especifique la IP reservada nueva en CSCFG del servicio.

4. Quitar grupo afinidad para implementaciones nuevas - ya no se recomienda la afinidad grupos. Siga los pasos para #1 anteriores para implementar un nuevo servicio de nube. Asegúrese de que el servicio de nube no está en un grupo de afinidad.

5. Convertir a una red Virtual Regional: vea [cómo migrar de grupos de afinidad a una red Virtual Regional (VNet)](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).

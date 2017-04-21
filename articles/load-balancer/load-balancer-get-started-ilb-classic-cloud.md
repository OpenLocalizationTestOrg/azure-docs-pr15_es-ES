<properties
   pageTitle="Crear un equilibrador de carga interno para los servicios de nube en el modelo de implementación clásica | Microsoft Azure"
   description="Aprenda a crear un equilibrador de carga interno con PowerShell en el modelo de implementación clásica"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/09/2016"
   ms.author="sewhee" />

# <a name="get-started-creating-an-internal-load-balancer-classic-for-cloud-services"></a>Empezar a crear un equilibrador de carga interno (clásico) para los servicios de nube

[AZURE.INCLUDE [load-balancer-get-started-ilb-classic-selectors-include.md](../../includes/load-balancer-get-started-ilb-classic-selectors-include.md)]

<BR>

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]Obtenga información sobre cómo llevar a [cabo estos pasos con el modelo de administrador de recursos](load-balancer-get-started-ilb-arm-ps.md).


## <a name="configure-internal-load-balancer-for-cloud-services"></a>Configurar equilibrador de carga interno para los servicios de nube

Equilibrador de carga interno es compatible con equipos virtuales y servicios en la nube. Un extremo del equilibrador de carga interno creado en un servicio de nube que se encuentre fuera de una red virtual regional será accesible únicamente dentro del servicio de nube.

La configuración del equilibrador de carga interno se ha establecido durante la creación de la primera implementación en el servicio de nube, como se muestra en el ejemplo siguiente.

>[AZURE.IMPORTANT] Requisito previo para ejecutar los siguientes pasos es tener una red virtual que ya ha creado para la implementación de la nube. Necesitará el nombre de red virtual y el nombre de subred para crear el equilibrio de carga interno.

### <a name="step-1"></a>Paso 1

Abra el archivo de configuración de servicio (.cscfg) para la implementación de la nube en Visual Studio y agregue la siguiente sección para crear el equilibrio de carga interno en la última "`</Role>`" elemento para que la configuración de red.




    <NetworkConfiguration>
      <LoadBalancers>
        <LoadBalancer name="name of the load balancer">
          <FrontendIPConfiguration type="private" subnet="subnet-name" staticVirtualNetworkIPAddress="static-IP-address"/>
        </LoadBalancer>
      </LoadBalancers>
    </NetworkConfiguration>


Vamos a agregar los valores para mostrar el aspecto que tendrá el archivo de configuración de red. En el ejemplo, suponga que crea una subred denominada "test_vnet" con una subred 10.0.0.0/24 denominado test_subnet y una dirección IP estática 10.0.0.4. El equilibrador de carga se llamará PruebaCL.

    <NetworkConfiguration>
      <LoadBalancers>
        <LoadBalancer name="testLB">
          <FrontendIPConfiguration type="private" subnet="test_subnet" staticVirtualNetworkIPAddress="10.0.0.4"/>
        </LoadBalancer>
      </LoadBalancers>
    </NetworkConfiguration>

Para obtener más información sobre el esquema de equilibrador de carga, vea [Agregar equilibrador de carga](https://msdn.microsoft.com/library/azure/dn722411.aspx).

### <a name="step-2"></a>Paso 2


Cambiar el archivo de definición (.csdef) de servicio para agregar extremos para el equilibrio de carga interno. El momento en que se crea una instancia de rol, el archivo de definición de servicio agregará las instancias de la función para el equilibrio de carga interno.


    <WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
      <Endpoints>
        <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancer="load-balancer-name" />
      </Endpoints>
    </WorkerRole>

Siguiendo los mismos valores en el ejemplo anterior, vamos a agregar los valores del archivo de definición de servicio.

    <WorkerRole name=WorkerRole1" vmsize="A7" enableNativeCodeExecution="[true|false]">
      <Endpoints>
        <InputEndpoint name="endpoint1" protocol="http" localPort="80" port="80" loadBalancer="testLB" />
      </Endpoints>
    </WorkerRole>

El tráfico de red estará usando el equilibrador de carga PruebaCL utilizando el puerto 80 para las solicitudes entrantes, enviar a instancias de la función de trabajo también en el puerto 80 de equilibrio de carga.


## <a name="next-steps"></a>Pasos siguientes

[Configurar un modo de distribución del equilibrador de carga mediante la afinidad IP de origen](load-balancer-distribution-mode.md)

[Configurar los valores de tiempo de espera TCP inactivas para el equilibrador de carga](load-balancer-tcp-idle-timeout.md)
<properties
   pageTitle="Empezar a crear un opuestas equilibrador de carga en la implementación clásico modelo de servicios en la nube de Internet | Microsoft Azure"
   description="Aprenda a crear un opuestas equilibrador de carga en el modelo de implementación clásico de servicios en la nube de Internet"
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
   ms.date="03/17/2016"
   ms.author="sewhee" />

# <a name="get-started-creating-an-internet-facing-load-balancer-for-cloud-services"></a>Empezar a crear un opuestas equilibrador de carga para los servicios de nube de Internet

[AZURE.INCLUDE [load-balancer-get-started-internet-classic-selectors-include.md](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]En este artículo trata sobre el modelo de implementación clásico. También puede [obtener información sobre cómo crear un Internet opuestas equilibrador de carga con el Administrador de recursos de Azure](load-balancer-get-started-internet-arm-cli.md).

Servicios de nube se configuran automáticamente con un equilibrador de carga y se pueden personalizar a través del modelo de servicio.

## <a name="create-a-load-balancer-using-the-service-definition-file"></a>Crear un equilibrador de carga con el archivo de definición de servicio

Puede aprovechar el SDK de Azure para .NET 2.5 actualizar el servicio de nube. Configuración de extremo de los servicios de nube se realiza en el archivo de [definición del servicio](https://msdn.microsoft.com/library/azure/gg557553.aspx).csdef.

En el ejemplo siguiente se muestra cómo se configura un archivo servicedefinition.csdef para una implementación de nube:

Comprobación de la snipet para el archivo .csdef generado por una implementación de la nube, puede ver el extremo externo configurado para utilizar puertos HTTP en el puerto 10000, 10001 y 10002.


    <ServiceDefinition name=“Tenant“>
    <WorkerRole name=“FERole” vmsize=“Small“>
    <Endpoints>
        <InputEndpoint name=“FE_External_Http” protocol=“http” port=“10000“ />
        <InputEndpoint name=“FE_External_Tcp“  protocol=“tcp“  port=“10001“ />
        <InputEndpoint name=“FE_External_Udp“  protocol=“udp“  port=“10002“ />

        <InputEndpointname=“HTTP_Probe” protocol=“http” port=“80” loadBalancerProbe=“MyProbe“ />

        <InstanceInputEndpoint name=“InstanceEP” protocol=“tcp” localPort=“80“>
           <AllocatePublicPortFrom>
              <FixedPortRange min=“10110” max=“10120“  />
           </AllocatePublicPortFrom>
        </InstanceInputEndpoint>
        <InternalEndpoint name=“FE_InternalEP_Tcp” protocol=“tcp“ />
    </Endpoints>
    </WorkerRole>
    </ServiceDefinition>




## <a name="check-load-balancer-health-status-for-cloud-services"></a>Comprobar el estado de salud equilibrador de carga para los servicios de nube


El siguiente es un ejemplo de un sondeo de estado:

        <LoadBalancerProbes>
        <LoadBalancerProbe name=“MyProbe” protocol=“http” path=“Probe.aspx” intervalInSeconds=“5” timeoutInSeconds=“100“ />
        </LoadBalancerProbes>

El equilibrador de carga combina la información del extremo y la información del sondeo para crear una dirección URL en el formulario de http://{DIP de VM}:80/Probe.aspx que se pueden usar para consultar el estado del servicio.

El servicio detecta sondeos periódicos desde la misma dirección IP. Esta es la solicitud de sondeo de estado procedentes del host del nodo donde se ejecuta la máquina virtual.
El servicio tiene para responder con un código de estado HTTP 200 para el equilibrador de carga se supone que el servicio es correcto. Cualquier otro código de estado HTTP (por ejemplo 503) toma directamente de la máquina virtual de rotación.

La definición de sondeo también controla la frecuencia de sondeo. En nuestro caso anterior, el equilibrador de carga está analizando el punto final de cada 5 segundos. Si no hay respuesta positivo se recibe 10 segundos (dos intervalos de sondeo), el sondeo se supone hacia abajo y la máquina virtual se toma fuera de la rotación. Del mismo modo, si el servicio está fuera de la rotación y se recibe una respuesta positiva, el servicio se vuelve a colocar giro inmediatamente. Si el servicio fluctúa entre positivos y negativos, puede decidir retrasar la introducción del servicio a giro volver a hasta que se haya correcto para un número de sondeos equilibrador de carga.

Compruebe el esquema de definición de servicio para el [sondeo de estado](https://msdn.microsoft.com/library/azure/jj151530.aspx) para obtener más información.

## <a name="next-steps"></a>Pasos siguientes

[Empezar a configurar un equilibrador de carga interno](load-balancer-get-started-ilb-arm-ps.md)

[Configurar un modo de distribución del equilibrador de carga](load-balancer-distribution-mode.md)

[Configurar los valores de tiempo de espera TCP inactivas para el equilibrador de carga](load-balancer-tcp-idle-timeout.md)


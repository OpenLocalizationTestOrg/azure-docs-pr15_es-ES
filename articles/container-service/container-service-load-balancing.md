<properties
   pageTitle="Cargar saldo contenedores en un clúster de servicio del contenedor de Azure | Microsoft Azure"
   description="Equilibrar la carga entre varios contenedores en un clúster de servicio del contenedor de Azure."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Contenedores, servicios de Micro, DC/OS, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/11/2016"
   ms.author="rogardle"/>

# <a name="load-balance-containers-in-an-azure-container-service-cluster"></a>Contenedores de equilibrio de carga en un clúster de servicio del contenedor de Azure

En este artículo, veremos cómo crear un equilibrador de carga interno en un un controlador de dominio o el sistema operativo administra servicio de contenedor de Azure con maratón Libras. Esto le permitirá escalar sus aplicaciones horizontalmente. También permitirá sacar partido del agente público y privado clústeres colocando los equilibradores de carga en el clúster público y contenedores de aplicación en el clúster privado.

## <a name="prerequisites"></a>Requisitos previos

[Implementar una instancia de servicio del contenedor de Azure](container-service-deployment.md) con tipo orchestrator DC/OS y [Asegúrese de que el cliente puede conectarse a su clúster](container-service-connect.md). 

## <a name="load-balancing"></a>Equilibrio de carga

Hay dos capas de equilibrio de carga en el clúster de servicio de contenedor, que vamos a crear: 

  1. Azure equilibrador de carga proporciona puntos de entrada públicos (los que los usuarios finales se alcanzó). Este servicio del contenedor de Azure proporciona automáticamente y de forma predeterminada, configurado para exponer el puerto 80, 443 y 8080.
  2. El equilibrador de carga de maratón (maratón libras) enruta las solicitudes entrantes a instancias de contenedor que las solicitudes de servicio. Como se escalar los contenedores de proporcionar el servicio web, se adaptan dinámicamente maratón libras. No se proporciona este equilibrador de carga en el servicio de contenedor de forma predeterminada, pero es muy fácil de instalar.

## <a name="marathon-load-balancer"></a>Equilibrador de carga de maratón

Equilibrador de carga de maratón dinámicamente vuelve a configurar basándose en los contenedores que ha implementado. También es y son resistente a la pérdida de un contenedor o un agente - si esto ocurre, Apache Mesos simplemente se reinicie el contenedor en otro lugar y libras maratón se adaptan.

Para instalar el equilibrador de carga de maratón puede utilizar cualquier DC/sistema operativo web interfaz de usuario o la línea de comandos.

### <a name="install-marathon-lb-using-dcos-web-ui"></a>Instalar maratón Libras mediante la interfaz de usuario de Web DC/OS

  1. Haga clic en 'Universo'
  2. Buscar 'Maratón Libras'
  3. Haga clic en "Instalar"

![Instalar maratón kg a través de la interfaz de Web DC/OS](./media/dcos/marathon-lb-install.png)

### <a name="install-marathon-lb-using-the-dcos-cli"></a>Instalar maratón Libras con CLI DC/OS

Después de instalar CLI DC/OS y asegurarse de que puede conectarse a su clúster, ejecute el siguiente comando desde el equipo cliente:

```bash
dcos package install marathon-lb
```

Este comando instala automáticamente el equilibrador de carga en el clúster de agentes público.

## <a name="deploy-a-load-balanced-web-application"></a>Implementar una carga equilibrada aplicación Web

Ahora que tenemos el paquete maratón libras, podemos implementar un contenedor de aplicación que se desea equilibrar la carga. En este ejemplo se describe cómo implementar un servidor web simple mediante la siguiente configuración:

```json
{
  "id": "web",
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "yeasy/simple-web",
      "network": "BRIDGE",
      "portMappings": [
        { "hostPort": 0, "containerPort": 80, "servicePort": 10000 }
      ],
      "forcePullImage":true
    }
  },
  "instances": 3,
  "cpus": 0.1,
  "mem": 65,
  "healthChecks": [{
      "protocol": "HTTP",
      "path": "/",
      "portIndex": 0,
      "timeoutSeconds": 10,
      "gracePeriodSeconds": 10,
      "intervalSeconds": 2,
      "maxConsecutiveFailures": 10
  }],
  "labels":{
    "HAPROXY_GROUP":"external",
    "HAPROXY_0_VHOST":"YOUR FQDN",
    "HAPROXY_0_MODE":"http"
  }
}

```

  * Establecer el valor de `HAProxy_0_VHOST` al FQDN del equilibrador de carga para los agentes. Se trata en el formulario `<acsName>agents.<region>.cloudapp.azure.com`. Por ejemplo, si crea un clúster de servicio de contenedor con nombre `myacs` en región `West US`, sería FQDN `myacsagents.westus.cloudapp.azure.com`. También puede encontrar buscando el equilibrador de carga con "agente" en el nombre cuando está viendo a través de los recursos en el grupo de recursos que ha creado para el servicio de contenedor en el [portal de Azure](https://portal.azure.com).
  * Establecer el servicePort a un puerto > = 10.000. Identifica el servicio que se ejecuta en este contenedor--maratón kg utiliza para identificar los servicios que deben equilibrar entre.
  * Establecer el `HAPROXY_GROUP` etiqueta "externo".
  * Establecer `hostPort` en 0. Esto significa que maratón arbitrario asigna un puerto disponible.
  * Establecer `instances` en el número de instancias que desea crear. Puede siempre escalar estos arriba y hacia abajo más adelante.

Conviene noing que maratón se implementará en el clúster privado de forma predeterminada, esto significa que la implementación anterior sólo será accesible a través de un equilibrador de carga, que suele ser el comportamiento que se desean.

### <a name="deploy-using-the-dcos-web-ui"></a>Implementar mediante la interfaz de usuario de Web DC/OS

  1. Visite la página de maratón en http://localhost/marathon (después de configurar el [SSH túnel](container-service-connect.md) y haga clic en`Create Appliction`
  2. En la `New Application` diálogo haga clic en `JSON Mode` en la esquina superior derecha
  3. Pegue el anterior JSON en el editor
  4. Haga clic en`Create Appliction`

### <a name="deploy-using-the-dcos-cli"></a>Implementar mediante CLI DC/OS

Para implementar esta aplicación con la CLI DC/OS simplemente copie el anterior JSON en un archivo llamado `hello-web.json`y ejecutar:

```bash
dcos marathon app add hello-web.json
```

## <a name="azure-load-balancer"></a>Equilibrador de carga de Azure

De forma predeterminada, equilibrador de carga de Azure expone puertos 80, 8080 y 443. Si está usando uno de estos tres puertos (como se hace en el ejemplo anterior), no hay nada que tiene que hacer. Debería poder presionar FQDN del equilibrador de carga agente--y cada vez que se actualiza, deberá presionar uno de los servidores tres web en una forma circular. Sin embargo, si utiliza un puerto diferente, debe agregar una regla de turnos y un sondeo en el equilibrador de carga para el puerto que ha usado. Puede hacerlo desde la [CLI de Azure](../xplat-cli-azure-resource-manager.md), con los comandos `azure network lb rule create` y `azure network lb probe create`. También puede hacerlo con el Portal de Azure.


## <a name="additional-scenarios"></a>Escenarios adicionales

Puede tener un escenario que utilizar dominios diferentes para exponer servicios diferentes. Por ejemplo:

mydomain1.com -> LB:80 de Azure -> maratón-lb:10001 -> mycontainer1:33292  
mydomain2.com -> LB:80 de Azure -> maratón-lb:10002 -> mycontainer2:22321

Para ello, consulte [hosts virtuales](https://mesosphere.com/blog/2015/12/04/dcos-marathon-lb/), que proporciona una manera de asociar dominios a rutas de libras de maratón específicos.

Como alternativa, podría exponer diferentes puertos y reasignación de al servicio correcto detrás maratón libras. Por ejemplo:

Azure lb:80 -> maratón-lb:10001 -> mycontainer:233423  
Azure lb:8080 -> maratón-lb:1002 -> mycontainer2:33432


## <a name="next-steps"></a>Pasos siguientes

Consulte la documentación de DC/OS para activado más [maratón libras](https://dcos.io/docs/1.7/usage/service-discovery/marathon-lb/).

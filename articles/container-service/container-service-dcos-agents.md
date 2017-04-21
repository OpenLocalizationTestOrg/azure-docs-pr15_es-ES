<properties
   pageTitle="Públicos y privados DC/OS agente grupos ACS | Microsoft Azure"
   description="Funcionan de los grupos públicos y privados agente con un clúster de servicio del contenedor de Azure."
   services="container-service"
   documentationCenter=""
   authors="Thraka"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, contenedores, Micro-services, Mesos, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/16/2016"
   ms.author="timlt"/>

# <a name="dcos-agent-pools-for-azure-container-service"></a>Grupos de DC/OS agente de servicio del contenedor de Azure

Servicio de contenedor de Azure DC/OS divide a agentes en grupos de públicos o privados. Una implementación pueden realizarse en cualquier grupo, que afectan a la accesibilidad entre equipos en el servicio de contenedor. Los equipos pueden exponer a internet (público) o se mantiene a interna (privada). En este artículo se ofrece una breve introducción de por qué hay un grupo público y privado.

### <a name="private-agents"></a>Agentes privados

Nodos de agente privado ejecutar a través de una red no enrutables. Esta red solo es accesible desde la zona de administración o a través del enrutador de borde de la zona pública. De forma predeterminada, DC/OS inicia aplicaciones en los nodos de agente privado. Consulte la [documentación de DC/OS](https://dcos.io/docs/1.7/administration/securing-your-cluster/) para obtener más información sobre la seguridad de la red.

### <a name="public-agents"></a>Agentes públicos

Nodos de agente público ejecutar DC/OS aplicaciones y servicios a través de una red accesible públicamente. Consulte la [documentación de DC/OS](https://dcos.io/docs/1.7/administration/securing-your-cluster/) para obtener más información sobre la seguridad de la red.

## <a name="using-agent-pools"></a>Uso de grupos de agente

De forma predeterminada, **maratón** implementa cualquier nueva aplicación a los nodos de agente *privado* . Tiene que implementar explícitamente la aplicación en el nodo *público* durante la creación de la aplicación. Seleccione la pestaña **opcional** y escriba **slave_public** para el valor **Aceptado Roles de recursos** . Este proceso se documenta [aquí](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) y en la documentación de [DC\OS](https://dcos.io/docs/1.7/administration/installing/custom/create-public-agent/) .

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre la [administración de los contenedores de DC/OS](container-service-mesos-marathon-ui.md).

Obtenga información sobre cómo [Abrir el firewall](container-service-enable-public-access.md) proporcionadas por Azure para permitir el acceso a su contenedor DC/OS público.
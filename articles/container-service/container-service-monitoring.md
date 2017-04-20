<properties
   pageTitle="Supervisar un clúster de servicio del contenedor de Azure con Datadog | Microsoft Azure"
   description="Supervisar un clúster de servicio del contenedor de Azure con Datadog. Usar la interfaz de usuario de web DC/OS para implementar a los agentes de Datadog en el clúster."
   services="container-service"
   documentationCenter=""
   authors="rbitia"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Contenedores, DC/OS, conjunto de Docker, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure"   
   ms.date="07/28/2016"
   ms.author="t-ribhat"/>

# <a name="monitor-an-azure-container-service-cluster-with-datadog"></a>Monitor de un clúster de servicio del contenedor de Azure con Datadog

En este artículo se implementará a agentes Datadog en todos los nodos de agente en el clúster de servicio del contenedor de Azure. Se necesita una cuenta con Datadog para esta configuración. 

## <a name="prerequisites"></a>Requisitos previos 

[Implementar](container-service-deployment.md) y [Conectar](container-service-connect.md) un clúster configurado el servicio de contenedor de Azure. Explorar la [maratón IU](container-service-mesos-marathon-ui.md). Vaya a [http://datadoghq.com](http://datadoghq.com) para configurar una cuenta de Datadog. 

## <a name="datadog"></a>Datadog 

Datadog es un servicio de supervisión que reúne los datos de seguimiento de los contenedores en el clúster de servicio del contenedor de Azure. Datadog tiene un panel de integración Docker, donde puede ver las medidas específicas dentro de los contenedores. Métricas reunidas de los contenedores están organizados por CPU, memoria, red e i/OS. Datadog divide métricas en contenedores e imágenes. Un ejemplo del aspecto de la interfaz de usuario para el uso de CPU está por debajo.

![Interfaz de usuario de Datadog](./media/container-service-monitoring/datadog4.png)

## <a name="configure-a-datadog-deployment-with-marathon"></a>Configurar una implementación de Datadog con maratón

Estos pasos le muestran cómo configurar e implementar aplicaciones Datadog a su clúster con maratón. 

Obtener acceso a la interfaz de usuario de DC/OS a través de [http://localhost: 80 /](http://localhost:80/). Una vez en la interfaz de usuario de DC/OS vaya a "Universo" que se encuentra en la parte inferior izquierda y, a continuación, busque "Datadog" y haga clic en "Instalar".

![Paquete Datadog dentro del universo de DC/OS](./media/container-service-monitoring/datadog1.png)

Ahora para completar la configuración necesitará una cuenta de Datadog o una cuenta de prueba gratuita. Una vez que ha iniciado sesión el aspecto del sitio Web de Datadog a la izquierda y vaya a integraciones -> después de la API. 

![Clave API Datadog](./media/container-service-monitoring/datadog2.png)

A continuación, escriba la clave de la API en la configuración de Datadog dentro del universo de DC/OS. 

![Configuración de Datadog en el universo DC/OS](./media/container-service-monitoring/datadog3.png) 

En la configuración anterior instancias están establecidas 10000000 lo siempre que se agrega un nuevo nodo al clúster Datadog implementará automáticamente un agente a ese nodo. Esta es una solución provisional. Una vez que haya instalado el paquete debe ir al sitio Web de Datadog y busque "Paneles". Desde allí, verá personalizadas y paneles de integración. El panel de la integración de Docker tendrá todas las estadísticas de contenedor que necesita para supervisar el clúster. 

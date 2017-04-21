<properties
   pageTitle="Administración de contenedor de servicio de contenedor Azure con enjambre Docker | Microsoft Azure"
   description="Implementar contenedores en un enjambre Docker en el servicio de contenedor de Azure"
   services="container-service"
   documentationCenter=""
   authors="neilpeterson"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, contenedores, Micro-services, Mesos, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/13/2016"
   ms.author="timlt"/>

# <a name="container-management-with-docker-swarm"></a>Administración de contenedor con enjambre Docker

Conjunto de docker proporciona un entorno para implementar contenedores de cargas de trabajo a través de un conjunto de hosts Docker agrupado. Conjunto de docker usa la API Docker nativa. El flujo de trabajo para administrar contenedores en un enjambre Docker es prácticamente igual a lo que sería en un host único contenedor. Este documento proporciona ejemplos sencillos de implementar contenedores cargas de trabajo en una instancia de servicio del contenedor de Azure de Docker enjambre. Para obtener más en profundidad en Docker enjambre, consulte [Docker enjambre en Docker.com](https://docs.docker.com/swarm/).

Requisitos previos para los ejercicios de este documento:

[Crear un clúster de conjunto de servicio del contenedor de Azure](container-service-deployment.md)

[Conectar con el clúster de conjunto de servicio del contenedor de Azure](container-service-connect.md)

## <a name="deploy-a-new-container"></a>Implementar un nuevo contenedor

Para crear un nuevo contenedor de la Docker enjambre, use la `docker run` comando (lo que garantiza que ha abierto un túnel SSH a los patrones de acuerdo con los requisitos previos anteriores). Este ejemplo crea un contenedor desde la `yeasy/simple-web` imagen:


```bash
user@ubuntu:~$ docker run -d -p 80:80 yeasy/simple-web

4298d397b9ab6f37e2d1978ef3c8c1537c938e98a8bf096ff00def2eab04bf72
```

Una vez creado el contenedor, utilice `docker ps` para devolver información sobre el contenedor. A continuación, observe que aparece en el agente de conjunto que aloja el contenedor:


```bash
user@ubuntu:~$ docker ps

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                 NAMES
4298d397b9ab        yeasy/simple-web    "/bin/sh -c 'python i"   31 seconds ago      Up 9 seconds        10.0.0.5:80->80/tcp   swarm-agent-34A73819-1/happy_allen
```  

Ahora puede tener acceso a la aplicación que se está ejecutando en este contenedor a través del nombre DNS público del equilibrador de carga de agente de conjunto. Puede encontrar esta información en el portal de Azure:  


![Resultados de la visita real](media/real-visit.jpg)  

De forma predeterminada el equilibrador de carga tiene puertos 80, 443 y 8080 abierto. Si desea conectar en otro puerto que se debe abrir ese puerto en el equilibrador de carga de Azure para el grupo de agente.

## <a name="deploy-multiple-containers"></a>Implementar varios contenedores

Como se inician varios contenedores, ejecutando 'docker ejecutar' varias veces, puede utilizar el `docker ps` comando para ver que hospeda los contenedores se están ejecutando. En el ejemplo siguiente, tres contenedores se distribuyan de forma pareja en los tres agentes de conjunto:  


```bash
user@ubuntu:~$ docker ps

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                 NAMES
11be062ff602        yeasy/simple-web    "/bin/sh -c 'python i"   11 seconds ago      Up 10 seconds       10.0.0.6:83->80/tcp   swarm-agent-34A73819-2/clever_banach
1ff421554c50        yeasy/simple-web    "/bin/sh -c 'python i"   49 seconds ago      Up 48 seconds       10.0.0.4:82->80/tcp   swarm-agent-34A73819-0/stupefied_ride
4298d397b9ab        yeasy/simple-web    "/bin/sh -c 'python i"   2 minutes ago       Up 2 minutes        10.0.0.5:80->80/tcp   swarm-agent-34A73819-1/happy_allen
```  

## <a name="deploy-containers-by-using-docker-compose"></a>Implementar contenedores mediante redacte Docker

Puede usar Docker redactar para automatizar la implementación y configuración de varios contenedores. Para ello, asegúrese de que se ha creado un túnel de Shell seguro (SSH) y que se ha establecido la variable DOCKER_HOST (consulte los requisitos previos anteriores).

Crear un archivo de docker compose.yml en el sistema local. Para ello, use este [ejemplo](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/docker-compose.yml).

```bash
web:
  image: adtd/web:0.1
  ports:
    - "80:80"
  links:
    - rest:rest-demo-azure.marathon.mesos
rest:
  image: adtd/rest:0.1
  ports:
    - "8080:8080"

```

Ejecutar `docker-compose up -d` para iniciar las implementaciones de contenedor:


```bash
user@ubuntu:~/compose$ docker-compose up -d
Pulling rest (adtd/rest:0.1)...
swarm-agent-3B7093B8-0: Pulling adtd/rest:0.1... : downloaded
swarm-agent-3B7093B8-2: Pulling adtd/rest:0.1... : downloaded
swarm-agent-3B7093B8-3: Pulling adtd/rest:0.1... : downloaded
Creating compose_rest_1
Pulling web (adtd/web:0.1)...
swarm-agent-3B7093B8-3: Pulling adtd/web:0.1... : downloaded
swarm-agent-3B7093B8-0: Pulling adtd/web:0.1... : downloaded
swarm-agent-3B7093B8-2: Pulling adtd/web:0.1... : downloaded
Creating compose_web_1
```

Por último, se devolverá la lista de contenedores en ejecución. Esta lista refleja los contenedores que se implementaron utilizando redacte Docker:


```bash
user@ubuntu:~/compose$ docker ps
CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS                     NAMES
caf185d221b7        adtd/web:0.1        "apache2-foreground"   2 minutes ago       Up About a minute   10.0.0.4:80->80/tcp       swarm-agent-3B7093B8-0/compose_web_1
040efc0ea937        adtd/rest:0.1       "catalina.sh run"      3 minutes ago       Up 2 minutes        10.0.0.4:8080->8080/tcp   swarm-agent-3B7093B8-0/compose_rest_1
```

Por supuesto, puede usar `docker-compose ps` para examinar solo los contenedores definidos en su `compose.yml` archivo.

## <a name="next-steps"></a>Pasos siguientes

[Más información sobre Docker enjambre](https://docs.docker.com/swarm/)

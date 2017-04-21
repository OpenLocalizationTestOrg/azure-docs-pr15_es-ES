<properties
   pageTitle="Administración de contenedores de servicio de contenedor Azure a través de la API de REST | Microsoft Azure"
   description="Implementar contenedores a un clúster de Azure contenedor servicio Mesos mediante la API de REST de maratón."
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

# <a name="container-management-through-the-rest-api"></a>Administración de contenedor a través de la API de REST

DC/OS proporciona un entorno para implementar y escalar agrupadas cargas de trabajo, mientras condensar el hardware subyacente. En la parte superior DC/OS, hay un marco que administra la programación y ejecución de cargas de trabajo de cálculo.

Aunque marcos están disponibles para muchas cargas de trabajo populares, este documento describe cómo puede crear y escalar implementaciones de contenedor mediante maratón. Antes de trabajar con estos ejemplos, necesita un clúster de DC/OS está configurado en el servicio de contenedor de Azure. También debe tener conectividad remota de este clúster. Para obtener más información sobre estos elementos, consulte los siguientes artículos:

- [Implementar un clúster de servicio del contenedor de Azure](container-service-deployment.md)
- [Conectarse a un clúster de servicio del contenedor de Azure](container-service-connect.md)

Cuando se haya conectado al clúster de servicio del contenedor de Azure, puede tener acceso a través de puerto http://localhost:local DC/sistema operativo y las API de REST relacionadas. Los ejemplos de este documento se suponen que son túnel en el puerto 80. Por ejemplo, puede encontrar el extremo de maratón `http://localhost/marathon/v2/`. Para obtener más información sobre las distintas API, consulte la documentación de Mesosphere de la [API de maratón](https://mesosphere.github.io/marathon/docs/rest-api.html) y la [API Chronos](https://mesos.github.io/chronos/docs/api.html)y la documentación de Apache para la [API del programador de Mesos](http://mesos.apache.org/documentation/latest/scheduler-http-api/).

## <a name="gather-information-from-dcos-and-marathon"></a>Recopile información de DC/OS y maratón

Antes de implementar contenedores en el clúster DC/OS, recopile información sobre el clúster DC/OS, como los nombres y el estado actual de los agentes de DC/OS. Para ello, consulta la `master/slaves` punto final de la API de REST DC/OS. Si todo va bien, verá una lista de agentes de DC/OS y varias propiedades para cada uno.

```bash
curl http://localhost/mesos/master/slaves
```

Ahora, use el maratón `/apps` extremo para comprobar si las implementaciones actuales de aplicación al clúster DC/OS. Si se trata de un nuevo clúster, verá una matriz vacía para las aplicaciones.

```
curl localhost/marathon/v2/apps

{"apps":[]}
```

## <a name="deploy-a-docker-formatted-container"></a>Implementar un contenedor con formato Docker

Implementar contenedores con formato Docker a través de maratón mediante un archivo JSON que describa la implementación prevista. En el ejemplo siguiente se implementará el contenedor Nginx, el puerto 80 de enlace del agente DC/OS al puerto 80 del contenedor. Tenga en cuenta que se establece la propiedad 'acceptedResourceRoles' a 'slave_public'. Se implementará el contenedor a un agente en el conjunto de escala de agente de público.

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 16.0,
  "instances": 1,
    "acceptedResourceRoles": [
    "slave_public"
  ],
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "hostPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Para implementar un contenedor con formato Docker, crear su propio archivo JSON o usar el ejemplo proporcionado en la [demostración de servicio del contenedor de Azure](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/marathon/marathon.json). Guardarla en una ubicación accesible. A continuación, para implementar el contenedor, ejecute el siguiente comando. Especifique el nombre del archivo JSON.

```
curl -X POST http://localhost/marathon/v2/apps -d @marathon.json -H "Content-type: application/json"
```

El resultado será similar al siguiente:

```json
{"version":"2015-11-20T18:59:00.494Z","deploymentId":"b12f8a73-f56a-4eb1-9375-4ac026d6cdec"}
```

Ahora, si consulta maratón aplicaciones, esta nueva aplicación se mostrará en el resultado.

```
curl localhost/marathon/v2/apps
```

## <a name="scale-your-containers"></a>Ajustar el tamaño de los contenedores

También puede usar la API de maratón para escalar o cambiar la escala en implementaciones de aplicación. En el ejemplo anterior, implementar una instancia de una aplicación. Vamos a escalar esta salida de tres instancias de una aplicación. Para ello, crear un archivo JSON mediante el siguiente texto JSON y guardarlo en una ubicación accesible.

```json
{ "instances": 3 }
```

Ejecute el comando siguiente para cambiar la escala de la aplicación.

>[AZURE.NOTE] La URI será http://localhost/marathon/v2/apps/ y, a continuación, el identificador de la aplicación para escalar. Si está utilizando el ejemplo Nginx que se ofrece aquí, el URI sería http://localhost/marathon/v2/apps/nginx.

```json
curl http://localhost/marathon/v2/apps/nginx -H "Content-type: application/json" -X PUT -d @scale.json
```

Por último, la consulta del extremo de maratón para aplicaciones. Verá que ahora hay tres de los contenedores Nginx.

```
curl localhost/marathon/v2/apps
```

## <a name="use-powershell-for-this-exercise-marathon-rest-api-interaction-with-powershell"></a>Usar PowerShell para este ejercicio: interacción API de REST de maratón con PowerShell

Puede realizar las mismas acciones mediante los comandos de PowerShell en un sistema de Windows.

Para obtener información sobre el clúster DC/OS, como los nombres de agente y estado de un agente, ejecute el siguiente comando.

```powershell
Invoke-WebRequest -Uri http://localhost/mesos/master/slaves
```

Implementar contenedores con formato Docker a través de maratón mediante un archivo JSON que describa la implementación prevista. En el ejemplo siguiente se implementará el contenedor Nginx, el puerto 80 de enlace del agente DC/OS al puerto 80 del contenedor.

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 16.0,
  "instances": 1,
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "hostPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Crear su propio archivo JSON, o use el ejemplo proporcionado en la [demostración de servicio del contenedor de Azure](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/marathon/marathon.json). Guardarla en una ubicación accesible. A continuación, para implementar el contenedor, ejecute el siguiente comando. Especifique el nombre del archivo JSON.

```powershell
Invoke-WebRequest -Method Post -Uri http://localhost/marathon/v2/apps -ContentType application/json -InFile 'c:\marathon.json'
```

También puede usar la API de maratón para escalar o cambiar la escala en implementaciones de aplicación. En el ejemplo anterior, implementar una instancia de una aplicación. Vamos a escalar esta salida de tres instancias de una aplicación. Para ello, crear un archivo JSON mediante el siguiente texto JSON y guardarlo en una ubicación accesible.

```json
{ "instances": 3 }
```

Ejecute el comando siguiente para cambiar la escala de la aplicación.

> [AZURE.NOTE] La URI será http://localhost/marathon/v2/apps/ y, a continuación, el identificador de la aplicación para escalar. Si está utilizando el ejemplo Nginx proporcionado aquí, el URI sería http://localhost/marathon/v2/apps/nginx.

```powershell
Invoke-WebRequest -Method Put -Uri http://localhost/marathon/v2/apps/nginx -ContentType application/json -InFile 'c:\scale.json'
```

## <a name="next-steps"></a>Pasos siguientes

- [Obtenga más información sobre los extremos HTTP Mesos]( http://mesos.apache.org/documentation/latest/endpoints/).
- [Obtenga más información acerca de la API de REST de maratón]( https://mesosphere.github.io/marathon/docs/rest-api.html).

<properties 
  pageTitle="Implementar su propio registro privado Docker en Azure | Microsoft Azure"
  description="Describe cómo puede usar el registro de Docker para hospedar las imágenes del contenedor en el servicio de almacenamiento de blobs de Windows Azure."
  services="virtual-machines-linux"
  documentationCenter="virtual-machines"
  authors="ahmetalpbalkan"
  editor="squillace"
  manager="timlt"
  tags="azure-service-management,azure-resource-manager" />

<tags
  ms.service="virtual-machines-linux"
  ms.devlang="multiple"
  ms.topic="article"
  ms.tgt_pltfrm="vm-linux"
  ms.workload="infrastructure-services"
  ms.date="09/27/2016" 
  ms.author="ahmetb" />

# <a name="deploying-your-own-private-docker-registry-on-azure"></a>Implementar su propio registro privado Docker en Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]



Este documento describe qué Docker es registro privado y muestra cómo puede implementar una imagen de contenedor Docker registro 2.0 a un registro privado Docker en Microsoft Azure con el almacenamiento de blobs de Windows Azure.

Este documento se supone:

1. Saber cómo utilizar Docker y Docker imágenes para almacenar. ¿(No es necesario? [Obtenga más información sobre Docker](https://www.docker.com))
2. Tiene un servidor que tiene instalado el motor de Docker. ¿(No es necesario? [Hacerlo rápidamente en Azure.](https://azure.microsoft.com/documentation/templates/docker-simple-on-ubuntu/))


## <a name="what-is-a-private-docker-registry"></a>¿Qué es un registro de Docker privada?

Para enviar sus aplicaciones de contenido en la nube, construir una imagen de contenedor Docker y almacenar en algún lugar para que se puede usar por usted y otros usuarios. 

Aunque es fácil crear una imagen de contenedor y envío en la nube, es un reto para almacenar la imagen generada confiable. Por este motivo, Docker ofrece un servicio centralizado llamado [Concentrador Docker] [ docker-hub] almacenar contenedor imágenes en la nube y le permite crear contenedores en cualquier momento mediante estas imágenes.

Aunque el [Concentrador de Docker] [ docker-hub] es un servicio pagado para almacenar el contenedor de aplicación privada imágenes, necesidades de los desarrolladores de aspectos de Docker y proporciona un conjunto de herramientas de código abierto para almacenar las imágenes en su propio registro Docker privado detrás de un firewall o local sin alcance Internet pública.
Dado que es fácil de proteger el almacenamiento de blobs de Windows Azure, puede usar rápidamente a crear y usar un registro de Docker privada en Azure que controlan usted mismo.

## <a name="why-should-you-host-a-docker-registry-on-azure"></a>¿Por qué debería hospeda un registro Docker en Azure?

Hospeda la instancia de registro de Docker en Microsoft Azure y almacenar las imágenes en el almacenamiento de blobs de Windows Azure, puede tener varias ventajas:

**Seguridad:** Las imágenes de Docker no dejan centros de datos de Azure, por lo que no pueden pasar Internet pública como si estuviera utilizando Docker concentrador.
  
**Rendimiento:** Las imágenes de Docker se almacenan en el mismo centro de datos o región como las aplicaciones. Esto significa que las imágenes se realizará con mayor rapidez y confiabilidad en comparación con Docker concentrador.

**Confiabilidad:** Mediante el uso de almacenamiento de blobs de Microsoft Azure, puede hacer usar de muchas propiedades de almacenamiento como almacenamiento de alta disponibilidad, redundancia, premium (SSD) y así sucesivamente.

## <a name="configuring-docker-registry-to-use-azure-blob-storage"></a>Configuración de registro de Docker para utilizar el almacenamiento de blobs de Windows Azure

(Se recomienda leer la [documentación Docker registro 2.0][documentos de registro] antes de continuar).

Puede [Configurar] [ registry-config] su registro Docker de dos formas diferentes.
Puedes:

1. Usar un `config.yml` archivo. En este caso debe crear una imagen de Docker independiente en la parte superior de `registry` imagen.
2. Reemplazar el archivo de configuración predeterminada mediante variables de entorno: Obtiene cosas sin crear y mantener una imagen de Docker independiente.

Para simplificar, este tema sigue opción 2, con las variables de entorno.

Para ejecutar un registro de Docker instancia que:

* usa su cuenta de almacenamiento de Azure para almacenar las imágenes
* escucha en el puerto de la máquina Virtual 5000
* no tiene ninguna autenticación configurada (no recomendado, vea la nota siguiente)

debe ejecutar el siguiente comando Docker en su terminal fiesta (reemplazar `<storage-account>` y `<storage-key>` con sus credenciales):

```sh
$ docker run -d -p 5000:5000 \
     -e REGISTRY_STORAGE=azure \
     -e REGISTRY_STORAGE_AZURE_ACCOUNTNAME="<storage-account>" \
     -e REGISTRY_STORAGE_AZURE_ACCOUNTKEY="<storage-key>" \
     -e REGISTRY_STORAGE_AZURE_CONTAINER="registry" \
     --name=registry \
     registry:2
```

Una vez que el comando sale de ella, puede ver el contenedor que hospeda la instancia de Docker registro privada ejecutando el `docker ps` comando en el host:

```sh
$ docker ps
CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS                    NAMES
3698ddfebc6f        registry:2          "registry cmd/regist   2 seconds ago       Up 1 seconds        0.0.0.0:5000->5000/tcp   registry
```

> [AZURE.IMPORTANT] Configuración de seguridad para el registro de Docker no se incluye en este documento y su registro serán accesible para todos los usuarios sin autenticación de forma predeterminada si abre el puerto para el puerto del registro en el extremo de la máquina Virtual o equilibrador de carga si usa el comando de implementación anterior.
>
> Lea la [Configuración de registro de Docker] [ registry-config] documentación para obtener información sobre cómo proteger la instancia de registro y las imágenes.

## <a name="next-steps"></a>Pasos siguientes

Una vez que el registro configurar, es hora de usarlo algo más. Comience con el docker [documentos de registro]. 

[docker-hub]: https://hub.docker.com/
[registry]: https://github.com/docker/distribution
[documentos de registro]: http://docs.docker.com/registry/
[registry-config]: http://docs.docker.com/registry/configuration/
 

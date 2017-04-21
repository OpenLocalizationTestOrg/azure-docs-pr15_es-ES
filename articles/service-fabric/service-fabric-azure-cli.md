<properties
   pageTitle="Interacción con clústeres de servicio tela mediante CLI | Microsoft Azure"
   description="Cómo usar Azure CLI para interactuar con un clúster de tela de servicio"
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/24/2016"
   ms.author="subramar"/>


# <a name="using-the-azure-cli-to-interact-with-a-service-fabric-cluster"></a>Con la CLI de Azure para interactuar con un clúster de tela de servicio

Puede interactuar con clúster de tela de servicio de equipos Linux con Azure CLI en Linux.

El primer paso es obtener la última versión de CLI desde el representante git y configurar la ruta de acceso con los comandos siguientes:

```sh
 git clone https://github.com/Azure/azure-xplat-cli.git
 cd azure-xplat-cli
 npm install
 sudo ln -s \$(pwd)/bin/azure /usr/bin/azure
 azure servicefabric
```

Para cada comando lo admite, puede escribir el nombre del comando para obtener la Ayuda de ese comando. Se admite la finalización automática para los comandos. Por ejemplo, el siguiente comando proporciona ayuda para todos los comandos de la aplicación. 

```sh
 azure servicefabric application 
```

Puede filtrar más la ayuda para un comando específico, como se muestra en el ejemplo siguiente:

```sh
 azure servicefabric application  create
```

Para habilitar Autocompletar en CLI, ejecute los comandos siguientes:

```sh
azure --completion >> ~/azure.completion.sh
echo 'source ~/azure.completion.sh' >> ~/.sh\_profile
source ~/azure.completion.sh
```

Los comandos siguientes para conectar con el clúster y mostrar los nodos en el clúster:

```sh
 azure servicefabric cluster connect http://localhost:19080
 azure servicefabric node show
```

Para usar parámetros con nombre y encontrar lo que son, puede escribir--ayuda después de un comando. Por ejemplo:

```sh
 azure servicefabric node show --help
 azure servicefabric application create --help
```

Cuando se conecta a un clúster de varios equipo desde un equipo **que no forma parte del clúster**, utilice el siguiente comando:

```sh
 azure servicefabric cluster connect http://PublicIPorFQDN:19080
```

Reemplace la etiqueta PublicIPorFQDN con la IP real o FQDN según corresponda. Cuando se conecta a un clúster de varios equipo desde un equipo **que forma parte del clúster**, utilice el siguiente comando:

```sh
 azure servicefabric cluster connect --connection-endpoint http://localhost:19080 --client-connection-endpoint PublicIPorFQDN:19000
```

Puede usar PowerShell o CLI para interactuar con Linux servicio tela clúster creado a través del portal de Azure. 

**Precaución:** Estos clústeres no están seguros, por lo tanto, puede abrir el cuadro de uno agregando la dirección IP pública del manifiesto clúster de.



## <a name="using-the-azure-cli-to-connect-to-a-service-fabric-cluster"></a>Con la CLI de Azure para conectarse a un clúster de tela de servicio

Los siguientes comandos de Azure CLI describen cómo conectarse a un clúster seguro. Los detalles del certificado deben coincidir con un certificado en los nodos de clúster.

```
azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert
```
 
Si el certificado tiene entidades emisoras de certificados (CA), debe agregar el parámetro de ruta de certificado de entidad emisora de certificados, como el siguiente ejemplo: 

```
 azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --ca-cert-path /tmp/ca1,/tmp/ca2 
```
Si tiene varias entidades emisoras de certificados, use una coma como delimitador.
 
Si su nombre común en el certificado no coincide con el extremo de la conexión, puede usar el parámetro `--strict-ssl` para omitir la comprobación, como se muestra en el siguiente comando: 

```
azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --strict-ssl false 
```
 
Si desea omitir la comprobación de la entidad emisora de certificados, puede agregar el--parámetro no autorizado rechazar tal como se muestra en el siguiente comando: 

```
azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --reject-unauthorized false 
```
 
Después de conectar, debería poder ejecutar otros comandos CLI para interactuar con el clúster. 

## <a name="deploying-your-service-fabric-application"></a>Implementación de la aplicación de servicio tela

Ejecute los comandos Copiar, registrar e iniciar la aplicación de servicio de tela siguientes:

```
azure servicefabric application package copy [applicationPackagePath] [imageStoreConnectionString] [applicationPathInImageStore]
azure servicefabric application type register [applicationPathinImageStore]
azure servicefabric application create [applicationName] [applicationTypeName] [applicationTypeVersion]
```


## <a name="upgrading-your-application"></a>Actualizar la aplicación

El proceso es similar al [proceso en Windows](service-fabric-application-upgrade-tutorial-powershell.md)).

Crear, copiar, registrar y crear su aplicación desde el directorio raíz del proyecto. Si la instancia de la aplicación se denomina tela: / MySFApp y el tipo es MySFApp, los comandos serán los siguientes:

```
 azure servicefabric cluster connect http://localhost:19080
 azure servicefabric application package copy MySFApp fabric:ImageStore
 azure servicefabric application type register MySFApp
 azure servicefabric application create fabric:/MySFApp MySFApp 1.0
```

Realice el cambio a la aplicación y volver a crear el servicio modificado.  Actualizar archivo manifiestos de modificación del servicio (ServiceManifest.xml) con las versiones actualizadas del servicio (y código o configuración o datos según corresponda). También puede modificar el manifiesto de la aplicación (ApplicationManifest.xml) con el número de versión actualizada de la aplicación y el servicio de modificación.  Ahora, copiar y registrar su aplicación actualizada con los comandos siguientes:

```
 azure servicefabric cluster connect http://localhost:19080>
 azure servicefabric application package copy MySFApp fabric:ImageStore
 azure servicefabric application type register MySFApp
```

Ahora, puede iniciar la actualización de la aplicación con el siguiente comando:

```
 azure servicefabric application upgrade start -–application-name fabric:/MySFApp -–target-application-type-version 2.0  --rolling-upgrade-mode UnmonitoredAuto
```

Ahora puede supervisar la actualización de la aplicación con SFX. En unos minutos, habría ha actualizado la aplicación.  También puede probar una aplicación actualizada con un error y comprobar la funcionalidad de deshacer automática en tela de servicio.

## <a name="troubleshooting"></a>Solución de problemas

### <a name="copying-of-the-application-package-does-not-succeed"></a>Copiar el paquete de aplicación no funciona correctamente

Compruebe si `openssh` está instalado. De forma predeterminada, escritorio Ubuntu no la tiene instalada. Instalar mediante el comando siguiente:

```
 sudo apt-get install openssh-server openssh-client**
```

Si el problema continúa, intente deshabilitar PAM para ssh cambiando el archivo **sshd_config** mediante los siguientes comandos:

```sh
 sudo vi /etc/ssh/sshd_config
#Change the line with UsePAM to the following: UsePAM no
 sudo service sshd reload
```

Si el problema continúa, intente aumentar el número de ssh sesiones ejecutando los siguientes comandos:

```sh
 sudo vi /etc/ssh/sshd\_config
# Add the following to lines:
# MaxSessions 500
# MaxStartups 300:30:500
 sudo service sshd reload
```
Uso de claves para ssh autenticación (en lugar de contraseñas) aún no se admite (como la plataforma utiliza ssh para copiar los paquetes), así que use autenticación de contraseña en su lugar.


## <a name="next-steps"></a>Pasos siguientes

Configurar el entorno de desarrollo e implementar una aplicación de servicio tela a un clúster de Linux.

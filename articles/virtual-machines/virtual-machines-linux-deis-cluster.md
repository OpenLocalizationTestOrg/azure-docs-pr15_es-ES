<properties
   pageTitle="Implementar un nodo 3 Deis clúster | Microsoft Azure"
   description="En este artículo se describe cómo crear un nodo de 3 Deis clúster en Azure mediante una plantilla de administrador de recursos de Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="HaishiBai"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="06/24/2015"
   ms.author="hbai"/>

# <a name="deploy-a-3-node-deis-cluster"></a>Implementar un nodo 3 Deis clúster

En este artículo le guiará por el aprovisionamiento de un [Deis](http://deis.io/) clúster en Azure. Cubra todos los pasos de crear los certificados necesarios para implementar y escalar una aplicación de ejemplo **Ir** en el clúster recientemente creado.

El siguiente diagrama muestra la arquitectura del sistema implementada. Un administrador del sistema administra el clúster utilizando Deis herramientas como **deis** y **deisctl**. Las conexiones se establecen a través de un equilibrador de carga de Azure, que reenvía las conexiones a uno de los miembros de nodos en el clúster. El acceso de clientes implementado aplicaciones a través del equilibrador de carga. En este caso, el equilibrador de carga reenvía el tráfico a un Deis malla enrutador, que routs aún más el tráfico a los contenedores Docker correspondientes alojados en el clúster.

  ![Diagrama de arquitectura de clúster Desis implementado](media/virtual-machines-linux-deis-cluster/architecture-overview.png)

Para poder ejecutar los pasos siguientes, deberá:

 * Una suscripción activa de Azure. Si no tiene una, puede obtener una prueba gratuita de [azure.com](https://azure.microsoft.com/).
 * Identificador de trabajo o escuela usar los grupos de recursos de Azure. Si tiene una cuenta personal e inicie sesión con un identificador de Microsoft, debe [crear un identificador de trabajo desde su personal](virtual-machines-windows-create-aad-work-id.md).
 * Bien, según el sistema operativo de cliente: el [Azure PowerShell](../powershell-install-configure.md) o la [CLI de Azure para Mac, Linux y Windows](../xplat-cli-install.md).
 * [OpenSSL](https://www.openssl.org/). OpenSSL se usa para generar los certificados necesarios.
 * Un cliente de Git como [Git intensiva](https://git-scm.com/).
 * Para probar la aplicación de ejemplo, también deberá un servidor DNS. Puede usar los servidores DNS o servicios que admiten comodín registros.
 * Un equipo para ejecutar Deis las herramientas cliente. Puede usar un equipo local o en una máquina virtual. Puede ejecutar estas herramientas en casi cualquier distribución de Linux, pero las siguientes instrucciones usar Ubuntu.

## <a name="provision-the-cluster"></a>Aprovisionar el clúster

En esta sección, deberá usar una plantilla de [Administrador de recursos de Azure](../azure-resource-manager/resource-group-overview.md) desde el repositorio de Abrir origen [plantillas de tutorial rápido de azure](https://github.com/Azure/azure-quickstart-templates). En primer lugar, deberá copiar hacia abajo de la plantilla. A continuación, debe crear un nuevo par de claves SSH para la autenticación. Y, a continuación, deberá configurar un nuevo identificador para clústeres. Y, por último, deberá usar la secuencia de comandos de Shell o la secuencia de comandos de PowerShell para aprovisionar el clúster.

1. Clonar repositorio: [https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates).

        git clone https://github.com/Azure/azure-quickstart-templates

2. Vaya a la carpeta de plantillas:

        cd azure-quickstart-templates\deis-cluster-coreos

3. Crear un nuevo par de claves SSH mediante ssh keygen:

        ssh-keygen -t rsa -b 4096 -c "[your_email@domain.com]"

4. Generar un certificado mediante la clave privada anterior:

        openssl req -x509 -days 365 -new -key [your private key file] -out [cert file to be generated]

5. Vaya a [https://discovery.etcd.io/new](https://discovery.etcd.io/new) para generar un nuevo token de clúster, que es algo como:

        https://discovery.etcd.io/6a28e078895c5ec737174db2419bb2f3
<br />
Cada clúster CoreOS debe tener un único token de este servicio gratuito. Consulte la [documentación de CoreOS](https://coreos.com/docs/cluster-management/setup/cluster-discovery/) para obtener más detalles.

6. Modificar el archivo **config.yaml en la nube** para reemplazar el token de **descubrimiento** existente con el token de nuevo:

        #cloud-config
        ---
        coreos:
          etcd:
            # generate a new token for each unique cluster from https://discovery.etcd.io/new
            # uncomment the following line and replace it with your discovery URL
            discovery: https://discovery.etcd.io/3973057f670770a7628f917d58c2208a
        ...

7. Modificar el archivo **azuredeploy parameters.json** : abrir el certificado que creó en el paso 4 en un editor de texto. Copiar todo el texto entre `----BEGIN CERTIFICATE-----` y `-----END CERTIFICATE-----` en el parámetro **sshKeyData** (tendrá que quitar todos los caracteres de nueva línea).

8. Modificar el parámetro **newStorageAccountName** . Esta es la cuenta de almacenamiento para discos de VM OS. Este nombre de cuenta tiene que ser único global.

9. Modificar el parámetro **publicDomainName** . Esto se convertirá en parte del nombre DNS asociado con la IP pública del equilibrador de carga. FQDN final tendrá el formato de _[valor de este parámetro]_. _[Región]_. cloudapp.azure.com. Por ejemplo, si especifique el nombre como deishbai32 y el grupo de recursos se implementa en la región Oeste US, el FQDN final a su equilibrador de carga será deishbai32.westus.cloudapp.azure.com.

10. Guarde el archivo de parámetros. Y, a continuación, puede aprovisionar el clúster con PowerShell Azure:

        .\deploy-deis.ps1 -ResourceGroupName [resource group name] -ResourceGroupLocation "West US" -TemplateFile
        .\azuredeploy.json -ParametersFile .\azuredeploy-parameters.json -CloudInitFile .\cloud-config.yaml

  o CLI Azure:

        ./deploy-deis.sh -n "[resource group name]" -l "West US" -f ./azuredeploy.json -e ./azuredeploy-parameters.json
        -c ./cloud-config.yaml  

11. Una vez que se aprovisiona el grupo de recursos, puede ver todos los recursos en el grupo en Azure portal clásico. Como se muestra en la siguiente captura de pantalla, el grupo de recursos contiene una red virtual con tres máquinas virtuales, que se unen en el mismo conjunto de disponibilidad. El grupo también contiene un equilibrador de carga, que tiene una IP pública asociada.

  ![El grupo de recursos de preparación de Azure portal clásica](media/virtual-machines-linux-deis-cluster/resource-group.png)

## <a name="install-the-client"></a>Instalar el cliente

Necesita **deisctl** para controlar su Deis clúster. Aunque deisctl se instala automáticamente en todos los nodos, es aconsejable usar deisctl en otro equipo administrativo. Además, ya que todos los nodos están configurados con solo las direcciones IP privadas, debe usar SSH túnel a través del equilibrador de carga, que tiene una dirección IP pública, para conectarse a los equipos de nodo. Los siguientes son los pasos de configuración de deisctl en un Ubuntu independiente físico o máquina virtual.

1. Instalar deisctl:mkdir deis

        cd deis
        curl -sSL http://deis.io/deisctl/install.sh | sh -s 1.6.1
        sudo ln -fs $PWD/deisctl /usr/local/bin/deisctl

2. Agregar la clave privada ssh agente:

        eval `ssh-agent -s`
        ssh-add [path to the private key file, see step 1 in the previous section]

3. Configurar deisctl:

        export DEISCTL_TUNNEL=[public ip of the load balancer]:2223

La plantilla define reglas NAT entrantes que se asignan 2223 a 1, 2224 a la instancia 2 y 2225 a 3 de la instancia de la instancia. Proporciona redundancia para el uso de la herramienta deisctl. Puede examinar estas reglas en Azure portal clásica:

![Reglas NAT en el equilibrador de carga](media/virtual-machines-linux-deis-cluster/nat-rules.png)

> [AZURE.NOTE] Actualmente la plantilla solo admite clústeres de nodo 3. Esto es debido a una limitación en la plantilla de administrador de recursos de Azure definición de regla NAT que no admite la sintaxis de bucle.

## <a name="install-and-start-the-deis-platform"></a>Instale e inicie la plataforma de Deis

Ahora puede usar deisctl para instalar e iniciar la plataforma de Deis:

    deisctl config platform set domain=[some domain]
    deisctl config platform set sshPrivateKey=[path to the private key file]
    deisctl install platform
    deisctl start platform

> [AZURE.NOTE] Iniciar la plataforma tarda un poco (tanto como 10 minutos). Especialmente, iniciar el servicio de generador puede resultar bastante larga. Y a veces tarda varios intentos para tener éxito: si la operación parece bloquearse, pruebe a escribir `ctrl+c` para interrumpir la ejecución del comando y vuelva a intentarlo.

Puede usar `deisctl list` para verificar si se están ejecutando todos los servicios:

    deisctl list
    UNIT                            MACHINE                 LOAD    ACTIVE          SUB
    deis-builder.service            ebe3005e.../10.0.0.6    loaded  active          running
    deis-controller.service         ebe3005e.../10.0.0.6    loaded  active          running
    deis-database.service           9c79bbdd.../10.0.0.5    loaded  active          running
    deis-logger.service             9c79bbdd.../10.0.0.5    loaded  active          running
    deis-logspout.service           8d658d5a.../10.0.0.4    loaded  active          running
    deis-logspout.service           9c79bbdd.../10.0.0.5    loaded  active          running
    deis-logspout.service           ebe3005e.../10.0.0.6    loaded  active          running
    deis-publisher.service          8d658d5a.../10.0.0.4    loaded  active          running
    deis-publisher.service          9c79bbdd.../10.0.0.5    loaded  active          running
    deis-publisher.service          ebe3005e.../10.0.0.6    loaded  active          running
    deis-registry@1.service         ebe3005e.../10.0.0.6    loaded  active          running
    deis-router@1.service           ebe3005e.../10.0.0.6    loaded  active          running
    deis-router@2.service           8d658d5a.../10.0.0.4    loaded  active          running
    deis-router@3.service           9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-daemon.service       8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-daemon.service       9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-daemon.service       ebe3005e.../10.0.0.6    loaded  active          running
    deis-store-gateway@1.service    9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-metadata.service     8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-metadata.service     9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-metadata.service     ebe3005e.../10.0.0.6    loaded  active          running
    deis-store-monitor.service      8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-monitor.service      9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-monitor.service      ebe3005e.../10.0.0.6    loaded  active          running
    deis-store-volume.service       8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-volume.service       9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-volume.service       ebe3005e.../10.0.0.6    loaded  active          running

¡Felicidades! Ahora tiene un ejecuta Deis clsuter en Azure! A continuación, vamos a implementar una aplicación de ir para ver el clúster en acción de muestra.

## <a name="deploy-and-scale-a-hello-world-application"></a>Implementar y ajustar el tamaño de una aplicación Hola a todos

Los pasos siguientes muestran cómo implementar un "Hola a todos" Ir a la aplicación en el clúster. Los pasos se basan en [Deis documentación](http://docs.deis.io/en/latest/using_deis/using-dockerfiles/#using-dockerfiles).

1. Para que obtener la malla enrutamiento funcione correctamente, debe tener un registro de comodín A su dominio apuntando a la dirección IP pública del equilibrador de carga. La siguiente captura de pantalla muestra el registro para un registro de dominio de ejemplo en GoDaddy:

    ![Registro de GoDaddy A](media/virtual-machines-linux-deis-cluster/go-daddy.png)
<p />
2. Instalar deis:

        mkdir deis
        cd deis
        curl -sSL http://deis.io/deis-cli/install.sh | sh
        ln -fs $PWD/deis /usr/local/bin/deis
        
3. Crear una nueva clave SSH y, a continuación, agregue la clave pública para GitHub (por supuesto, también puede reutilizar las claves existentes). Para crear un nuevo par de claves SSH, use:

        cd ~/.ssh
        ssh-keygen (press [Enter]s to use default file names and empty passcode)

4. Agregar id_rsa.pub o la clave pública de su elección a GitHub. Puede hacerlo mediante el botón clave agregar SSH en la pantalla de configuración de claves SSH:

  ![Github clave](media/virtual-machines-linux-deis-cluster/github-key.png)
<p />
5. Registrar un nuevo usuario:

        deis register http://deis.[your domain]
<p />
6. Agregar la clave SSH:

        deis keys:add [path to your SSH public key]
  <p />      
7. Crear una aplicación.

        git clone https://github.com/deis/helloworld.git
        cd helloworld
        deis create
        git push deis master
<p />
8.La inserción git activará imágenes Docker para compilar e implementar, tardará unos minutos. Desde mi experiencia, en ocasiones, puede bloquear paso 10 (Pushing imagen al repositorio privada). Cuando esto ocurre, puede detener el proceso, quitar la aplicación que utiliza ' deis aplicaciones: destroy: <application name> ` to remove the application and try again. You can use `deis apps:list' para averiguar el nombre de la aplicación. Si todo funciona, verá algo parecido al siguiente al final del comando:

        -----> Launching...
               done, lambda-underdog:v2 deployed to Deis
               http://lambda-underdog.artitrack.com
               To learn more, use `deis help` or visit http://deis.io
        To ssh://git@deis.artitrack.com:2222/lambda-underdog.git
         * [new branch]      master -> master
<p />
9. Comprobar si la aplicación está funcionando:

        curl -S http://[your application name].[your domain]
  Debe ver:

        Welcome to Deis!
        See the documentation at http://docs.deis.io/ for more information.
        (you can use geis apps:list to get the name of your application).
<p />
10. Ajustar el tamaño de la aplicación a las instancias de 3:

        deis scale cmd=3
<p />
11. Si lo desea, puede usar deis información para examinar los detalles de la aplicación. Los resultados siguientes están en la implementación de aplicaciones:

        deis info
        === lambda-underdog Application
        {
          "updated": "2015-05-22T06:14:10UTC",
          "uuid": "10c74ee7-b7ff-4786-967a-7e65af7eabc3",
          "created": "2015-05-22T06:07:55UTC",
          "url": "lambda-underdog.artitrack.com",
          "owner": "haishi",
          "id": "lambda-underdog",
          "structure": {
            "cmd": 3
          }
        }

        === lambda-underdog Processes
        --- cmd:
        cmd.1 up (v2)
        cmd.2 up (v2)
        cmd.3 up (v2)

        === lambda-underdog Domains
        No domains

## <a name="next-steps"></a>Pasos siguientes

En este artículo guiado por todos los pasos para aprovisionar un nuevo Deis clúster en Azure mediante una plantilla de administrador de recursos de Azure. La plantilla admite redundancia de conexiones, así como para aplicaciones distribuidas de equilibrio de carga de herramientas. La plantilla también evita el uso de direcciones IP pública en los nodos de miembro, lo que ahorra valiosos recursos IP públicos y proporciona un entorno más seguro para las aplicaciones de host. Para obtener más información, consulte los artículos siguientes:

[Información general de administrador de recursos de Azure] [resource-group-overview]  
[Cómo utilizar la CLI de Azure] [azure-command-line-tools]  
[Usar PowerShell Azure con el Administrador de recursos Azure] [powershell-azure-resource-manager]  

[azure-command-line-tools]: ../xplat-cli-install.md
[resource-group-overview]: ../azure-resource-manager/resource-group-overview.md
[powershell-azure-resource-manager]: ../powershell-azure-resource-manager.md

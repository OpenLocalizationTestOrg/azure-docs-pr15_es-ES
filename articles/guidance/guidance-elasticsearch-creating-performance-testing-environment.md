<properties
   pageTitle="Crear un entorno de prueba para Elasticsearch de rendimiento | Microsoft Azure"
   description="Cómo configurar un entorno para probar el rendimiento de un clúster de Elasticsearch."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="bennage"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/22/2016"
   ms.author="masashin"/>
   
# <a name="creating-a-performance-testing-environment-for-elasticsearch-on-azure"></a>Crear un entorno de prueba para Elasticsearch en Azure de rendimiento

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

En este artículo forma [parte de una serie](guidance-elasticsearch.md). 

Este documento describe cómo configurar un entorno para probar el rendimiento de un clúster de Elasticsearch. Esta configuración se utilizó para probar el rendimiento de cargas de recopilación y consulta de datos, como se describe en el [ajuste del rendimiento de recopilación de datos para Elasticsearch en Azure][].

El proceso de pruebas de rendimiento utiliza [Apache JMeter](http://jmeter.apache.org/), con el [conjunto estándar](http://jmeter-plugins.org/wiki/StandardSet/) de complementos instalados en una configuración de maestro y subordinado con un conjunto de máquinas virtuales dedicados (que no forma parte del clúster Elasticsearch) configurado específicamente para el propósito. 

El [Agente de servidor de rendimiento](http://jmeter-plugins.org/wiki/PerfMonAgent/) se instaló en cada nodo Elasticsearch. Las siguientes secciones proporcionan instrucciones para volver a crear el entorno de prueba para que pueda realizar su propio pruebas de performance con JMeter. Estas instrucciones se suponen que ya ha creado un clúster de Elasticsearch con nodos conectados mediante una red virtual Azure. 

Tenga en cuenta que el entorno de prueba también se ejecuta como un conjunto de máquinas virtuales de Azure administrado mediante el uso de un grupo de recursos de Azure único.

[Marvel](https://www.elastic.co/products/marvel) también se ha instalado y configurado para habilitar los aspectos internos del clúster Elasticsearch para supervisar y analizar más fácilmente. Si las estadísticas de JMeter mostraron un máximo o Valle de rendimiento, información disponible a través de Marvel puede ser sumamente útil para ayudar a determinar la causa de la variaciones.

La imagen siguiente muestra la estructura de todo el sistema. 

![arco elasticsearch](./media/guidance-elasticsearch/performance-structure.png)

Tenga en cuenta los siguientes puntos:

- El patrón de JMeter VM ejecuta Windows Server para proporcionar el entorno de gráfico para la consola de JMeter. La VM patrón JMeter proporciona la interfaz de usuario (la aplicación de *jmeter* ) para habilitar una herramienta de comprobación crear pruebas, ejecutar pruebas y visualizar los resultados. Este VM coordenadas con el servidor de JMeter máquinas virtuales que realmente enviar las solicitudes que constituyen las pruebas.

- Las máquinas virtuales de JMeter subordinadas ejecutan Ubuntu Server (Linux), no es necesario gráfico para estas máquinas virtuales. El servidor JMeter máquinas virtuales ejecutar el software del servidor (la aplicación de *servidor jmeter* ) para enviar las solicitudes al clúster Elasticsearch JMeter.

- No se utilizaron nodos de cliente dedicado, aunque dedicado nodos de patrón.

- El número de nodos de datos en el clúster puede variar, dependiendo del escenario que se está probando.

- Todos los nodos en el clúster Elasticsearch ejecutan Marvel a observar el rendimiento en tiempo de ejecución y el agente de servidor de JMeter para recopilar datos de seguimiento para su análisis posterior.

- Cuando se prueba Elasticsearch 2.0.0 y versiones posteriores, uno de los nodos de datos también ejecuta Kibana. Esto se requiere la versión de Marvel que se ejecuta en Elasticsearch 2.0.0 o posterior.

## <a name="creating-an-azure-resource-group-for-the-virtual-machines"></a>Crear un grupo de recursos de Azure en los equipos virtuales

El patrón de JMeter debe podrá conectar directamente a cada uno de los nodos en el clúster de Elasticsearch para recopilar datos de rendimiento. Si es distinto del clúster Elasticsearch VNet la JMeter VNet, esto implica la configuración de cada nodo Elasticsearch con una dirección IP pública. Si esto es un problema con la configuración de Elasticsearch, considere la posibilidad de implementar las máquinas virtuales de JMeter en el mismo VNet como el clúster Elasticsearch utilizando el mismo grupo de recursos, en cuyo caso puede omitir este procedimiento primera.

Primero, [Cree un grupo de recursos](../resource-group-template-deploy-portal.md). Este documento asume que el grupo de recursos se denomina *JMeterPerformanceTest*. Si desea ejecutar las máquinas virtuales de JMeter en el mismo VNet como el clúster Elasticsearch, use el mismo grupo de recursos como ese clúster en lugar de crear uno nuevo.

## <a name="creating-the-jmeter-master-virtual-machine"></a>Creación de la máquina virtual de JMeter principal

Siguiente [crea una máquina virtual de Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md) con la imagen de *Windows Server 2008 R2 SP1* .  Recomendamos que seleccione un tamaño VM con suficiente núcleos y memoria para ejecutar las pruebas de rendimiento. Lo ideal es que se trata de un equipo con al menos 2 núcleos y 3,5 GB de memoria RAM (A2 estándar o más grande).

<!-- TODO add info on why disabling diagnostics is positive --> 

Le recomendamos que desactive los diagnósticos. Al crear la máquina virtual en el portal, esto se realiza en el módulo de *configuración* en la sección de *supervisión* en *Diagnósticos*. Deje el resto de opciones con sus valores predeterminados.

Compruebe que la máquina virtual y todos los recursos asociados se han creado correctamente, [Examine el grupo de recursos](../azure-portal/resource-group-portal.md#manage-resource-groups) en el portal. Los recursos enumerados deben constar de una máquina virtual, un grupo de seguridad de red y una dirección IP pública todos con el mismo nombre y la cuenta de almacenamiento y la interfaz de red con nombres basados en de la máquina virtual.

## <a name="creating-the-jmeter-subordinate-virtual-machines"></a>Crear las máquinas virtuales de JMeter subordinadas

Ahora [Cree una máquina virtual de Linux](../virtual-machines/virtual-machines-linux-quick-create-portal.md) con la imagen de *Ubuntu Server 14.04 LTADOS* .  Como ocurre con la VM JMeter principal, seleccione un tamaño VM con suficiente núcleos y memoria para ejecutar las pruebas de rendimiento. Lo ideal es que se trata de un equipo con al menos 2 núcleos y al menos 3,5 GB de memoria RAM (A2 estándar o más grande).

De nuevo, le recomendamos que desactive los diagnósticos.

Puede crear tantas máquinas virtuales subordinadas como desee. 

## <a name="installing-jmeter-server-on-the-jmeter-subordinate-vms"></a>Instalar el servidor de JMeter en las máquinas virtuales de JMeter subordinadas

Las máquinas virtuales de JMeter subordinadas ejecutan Linux y predeterminada no puede conectarse a ellas abriendo una conexión a Escritorio remoto (RDP). En su lugar, puede [usar PuTTY para abrir una ventana de línea de comandos](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md) en cada máquina virtual.

Una vez que se ha conectado a una de las VM subordinadas, usaremos fiesta configurar JMeter.

En primer lugar, instale el entorno de tiempo de ejecución de Java necesario para ejecutar JMeter.

```bash
sudo add-apt-repository ppa:webupd8team/java
sudo apt-get update
sudo apt-get install oracle-java8-installer
```

Ahora, descargue el software de JMeter empaquetado como un archivo zip.

```bash
wget http://apache.mirror.anlx.net/jmeter/binaries/apache-jmeter-2.13.zip
```

Instale el comando descomprimir y usarlo para expandir el software JMeter. El software se copia en una carpeta denominada **apache-jmeter-2.13**.

```bash
sudo apt-get install unzip
unzip apache-jmeter-2.13.zip
```

Cambiar al directorio *bin* manteniendo los ejecutables JMeter y realizar los programas de *servidor de jmeter* y *jmeter* ejecutable.

```bash
cd apache-jmeter-2.13/bin
chmod u+x jmeter-server
chmod u+x jmeter
```

Ahora, es necesario editar el archivo `jmeter.properties` ubicado en la carpeta actual (use el editor de texto que está más familiarizado, como *vi* o *vim*). Busque las siguientes líneas:

```yaml
...
client.rmi.localport=0
...
server.rmi.localport=4000
...
```

Quite los comentarios (quitar el interlineado \#caracteres #) y modificar estas líneas tal como se muestra a continuación, a continuación, guarde el archivo y cierre el editor:

```yaml
...
client.rmi.localport=4441
...
server.rmi.localport=4440
```

Ahora, ejecute los siguientes comandos para abrir el puerto 4441 para el tráfico TCP entrante (este es el puerto que se ha configurado *jmeter servidor* para escuchar):

```bash
sudo iptables -A INPUT -m state --state NEW -m tcp -p tcp --dport 4441 -j ACCEPT
```

Descargue el archivo zip que contiene el conjunto estándar de complementos para JMeter (estos complementos proporcionan contadores de supervisión de rendimiento) y, a continuación, descomprima el archivo en la carpeta **apache-jmeter-2.13** . Descomprimir el archivo en esta ubicación, los complementos coloca en la carpeta correcta.

Si se le pide que reemplazar el archivo de licencia, escriba respuestas (para todos):

```bash
wget http://jmeter-plugins.org/downloads/file/JMeterPlugins-Standard-1.3.0.zip
unzip JMeterPlugins-Standard-1.3.0.zip
```

Usar `nohup` iniciar el servidor JMeter en segundo plano. Debe responder mostrando un ID de proceso y un mensaje que indica que ha creado un objeto remoto y está listo para empezar a recibir comandos.  Ejecute el comando siguiente en el directorio ~/apache-jmeter-2.13/bin. 

```bash
nohup jmeter-server &
```

> [AZURE.NOTE]Si la máquina virtual se cierra finaliza el programa de servidor JMeter. Debe conectarse a la máquina virtual y reinícielo manualmente. Como alternativa, puede configurar el sistema para ejecutar el comando *jmeter server* automáticamente al iniciar mediante la adición de los siguientes comandos para la `/etc/rc.local` archivo (antes del comando *Salir 0* ):

```bash
sudo -u <username> bash << eoc
cd /home/<username>/apache-jmeter-2.13/bin
nohup ./jmeter-server &
eoc
```

Reemplazar `<username>` con su nombre de inicio de sesión.

Es posible que le resulte útil para mantener la ventana de terminal abierta para que pueda supervisar el progreso del servidor JMeter mientras pruebas está en curso.

Debe Repita estos pasos para cada VM subordinada de JMeter.

## <a name="installing-the-jmeter-server-agent-on-the-elasticsearch-nodes"></a>Instalar al agente de servidor de JMeter en los nodos de Elasticsearch

Este procedimiento supone que tiene acceso a los nodos Elasticsearch. Si ha creado el clúster con la plantilla de administrador de recursos, puede conectarse a cada nodo mediante el cuadro saltar VM, como se muestra en la sección topología de Elasticsearch de [Elasticsearch ejecuta en Azure](guidance-elasticsearch-running-on-azure.md). Puede conectarse al cuadro salto utilizando PuTTY también. 

Desde allí, puede usar el comando *ssh* para iniciar sesión en cada uno de los nodos en el clúster Elasticsearch.

Inicie sesión en uno de los nodos de Elasticsearch como administrador.  En el símbolo del sistema de fiesta, escriba los siguientes comandos para crear una carpeta para almacenar al agente de servidor de JMeter y mover a la carpeta:

```bash
mkdir server-agent
cd server-agent
```

Ejecute los comandos siguientes para instalar el comando *descomprima* (si no está ya instalado), descargue el software de agente de servidor de JMeter y descomprima:

```bash
sudo apt-get install unzip
wget http://jmeter-plugins.org/downloads/file/ServerAgent-2.2.1.zip
unzip ServerAgent-2.2.1.zip
```
 
Ejecute el comando siguiente para configurar el firewall y habilitar el tráfico TCP pase a través de puerto 4444 (es decir, el puerto usado por el agente de servidor de JMeter):

```bash
sudo iptables -A INPUT -m state --state NEW -m tcp -p tcp --dport 4444 -j ACCEPT
```

Ejecute el comando siguiente para iniciar al agente de servidor de JMeter en segundo plano:

```bash
nohup ./startAgent.sh &
```

El agente de servidor de JMeter debe responder con mensajes que indica que ha iniciado y está escuchando en el puerto 4444.  Presione ENTRAR para obtener un símbolo del sistema y, a continuación, ejecute el siguiente comando.

```bash
telnet <nodename> 4444
```

Reemplazar `<nodename>` con el nombre de su nodo. (Puede encontrar el nombre de su nodo ejecutando el `hostname` comando.) Este comando abre una conexión telnet al puerto 4444 en su equipo local. Puede utilizar esta conexión para comprobar que el agente de servidor de JMeter se está ejecutando correctamente.

Si no se está ejecutando el agente de servidor de JMeter, recibirá la respuesta 

`*telnet: Unable to connect to remote host: Connection refused*.`

Si está ejecutando el agente de servidor de JMeter y puerto 4444 se ha configurado correctamente, verá la respuesta siguiente:

![](./media/guidance-elasticsearch/performance-telnet-server.png)

> [AZURE.NOTE] La sesión de telnet no proporciona a algún mensaje una vez que ha conectado.

En la sesión de telnet, escriba el siguiente comando:

``` 
test
```

Si está configurado el agente de servidor de JMeter y escuchando correctamente, debe indicar que ha recibido el comando y responder con el mensaje *Yep*.

> [AZURE.NOTE]Puede escribir en otros comandos para obtener datos de supervisión de rendimiento. Por ejemplo, el comando `metric-single:cpu:idle` dará la proporción actual del tiempo que la CPU esté inactiva (es decir, una instantánea). Para obtener una lista completa de los comandos de, visite la página de [Agente de servidor de rendimiento](http://jmeter-plugins.org/wiki/PerfMonAgent/) . : Volver a llamarlo agente de servidor de rendimiento. >>

En la sesión de telnet, escriba el comando siguiente para salir de la sesión y volver a la línea de comandos de fiesta:

``` 
exit
```

> [AZURE.NOTE]Como con la JMeter subordinada VM, si cierra la sesión, o si este equipo se cierre y reinicie el agente de servidor de JMeter tendrán que reiniciar manualmente mediante el uso de la `startAgent.sh` comando. Si desea que el agente de servidor de JMeter que se inicie automáticamente, agregue el comando siguiente al final de la `/etc/rc.local` archivo antes del comando *Salir 0* . 
> Reemplazar `<username>` con su nombre de inicio de sesión:

```bash
sudo -u <username> bash << eoc
cd /home/<username>/server-agent
nohup ./startAgent.sh &
eoc
```

Bien ahora puede repetir este proceso para todos los nodos en el clúster Elasticsearch, o bien puede usar el `scp` comando para copiar la carpeta del agente de servidor y el contenido a todos de los nodos y usar el `ssh` comando iniciar el agente de servidor de JMeter tal como se muestra a continuación. e reemplazar `<username>` con el nombre de usuario y `<nodename>` con el nombre del nodo donde desea copiar y ejecutar el software (se le pedirá que proporcione su contraseña que se ejecute cada comando):

```bash
scp -r \~/server-agent <username>@<nodename>:\~
ssh <nodename> sudo iptables -A INPUT -m state --state NEW -m tcp -p tcp --dport 4444 -j ACCEPT
ssh <nodename> -n -f 'nohup \~/server-agent/startAgent.sh'
```

## <a name="installing-and-configuring-jmeter-on-the-jmeter-master-vm"></a>Instalar y configurar JMeter en la máquina virtual de JMeter principal

En el portal de Azure, haga clic en **grupos de recursos**. En el módulo de **grupos de recursos** , haga clic en el grupo de recursos que contiene el patrón de JMeter y máquinas virtuales subordinadas.  En el módulo de **grupo de recursos** , haga clic en la **máquina virtual principal de JMeter**. En el módulo de máquina virtual, en la barra de herramientas, haga clic en **Conectar**. Abra el archivo RDP cuando se le solicite el explorador web. Windows crea una conexión a Escritorio remoto para su máquina virtual.  Escriba el nombre de usuario y la contraseña de la máquina virtual cuando se le solicite.

En la máquina virtual, utilizando Internet Explorer, vaya a la página de [Descarga Java para Windows](http://www.java.com/en/download/ie_manual.jsp) . Siga las instrucciones para descargar y ejecutar el programa de instalación de Java.

En el explorador web, vaya a la página de [Descarga Apache JMeter](http://jmeter.apache.org/download_jmeter.cgi) y descargar el zip que contiene el binario más reciente. Guarde el zip en una ubicación adecuada en la máquina virtual.

Vaya al sitio [Personalizada JMeter complementos](http://jmeter-plugins.org/) y descargar el conjunto estándar de complementos. Guardar la postal en la misma carpeta que la descarga de JMeter desde el paso anterior.

En el Explorador de Windows, vaya a la carpeta que contiene la apache-jmeter -*largo y LARGOB* zip archivo donde *largo y LARGOB* es la versión actual de JMeter. Extraer los archivos en la carpeta actual.

Extraer los archivos en el JMeterPlugins-estándar -*yyy*.zip archivo donde *yyy* es la versión actual de los complementos en la apache-jmeter -*largo y LARGOB* carpeta. Esto agregará los complementos de la carpeta correcta para JMeter. Forma segura, puede combinar las carpetas biblioteca y sobrescribir los archivos de licencia y archivo Léame si se le solicita.

Vaya a la apache-jmeter*largo y*LARGOB/bin carpeta y editar los jmeter.properties archivo mediante el Bloc de notas.  En la `jmeter.properties` de archivos, busque la sección etiquetada *hosts remotos y configuración de RMI*.  En esta sección del archivo, busque la línea siguiente:

```yaml
remote_hosts=127.0.0.1
```

Cambie esta línea y reemplazar con la lista de direcciones IP o nombres de host para cada uno de los servidores subordinados de JMeter IP dirección 127.0.0.1 con una coma separado. Por ejemplo:

```yaml
remote_hosts=JMeterSub1,JMeterSub2
```

Buscar la siguiente línea y después quite el `#` carácter al comienzo de esta línea y modifique el valor de la configuración de client.rmi.localport de:

```yaml
#client.rmi.localport=0
```

Para:

```yaml
client.rmi.localport=4440
```

Guarde el archivo y cierre el Bloc de notas. 

En la barra de herramientas de Windows, haga clic en **Inicio**, haga clic en **Herramientas administrativas**y, a continuación, haga clic en **Firewall de Windows con seguridad avanzada**.  En el Firewall de Windows con la ventana seguridad avanzada, en el panel izquierdo, haga clic en **Reglas de entrada**y, a continuación, haga clic en **Nueva regla**.

En el **Asistente para reglas de entrada nuevo**, en la página **Tipo de regla** , seleccione el **puerto**y, a continuación, haga clic en **siguiente**.  En la página de puertos y protocolos, seleccione **TCP**, seleccione **puertos locales específicos**, en el cuadro de texto escriba `4440-4444`y, a continuación, haga clic en **siguiente**.  En la página acción, seleccione **Permitir la conexión**y, a continuación, haga clic en **siguiente**. En la página de perfil, deje activadas de opciones y, a continuación, haga clic en **siguiente**.  En la página Nombre, en el **nombre** del cuadro de texto escriba *JMeter*y, a continuación, haga clic en **Finalizar**.  Cierre el Firewall de Windows con seguridad avanzada.

En el Explorador de Windows, en la apache-jmeter**xx/bin carpeta, haga doble clic en el archivo de proceso por lotes de Windows *jmeter* para iniciar la interfaz gráfica. Debe aparecer la interfaz de usuario:

![](./media/guidance-elasticsearch/performance-image17.png)

En la barra de menús, haga clic en **Ejecutar**, haga clic en **Inicio remoto**y compruebe que aparecen los dos equipos subordinados JMeter:

![](./media/guidance-elasticsearch/performance-image18.png)

Ya está listo para comenzar la prueba de rendimiento.

## <a name="installing-and-configuring-marvel"></a>Instalar y configurar Marvel

La plantilla de tutorial rápido Elasticsearch para Azure instalará y configurará automáticamente la versión adecuada de Marvel si establece los parámetros MARVEL y KIBANA True ("Sí") al crear el clúster:

![](./media/guidance-elasticsearch/performance-image19.png)

Si está agregando Marvel a un clúster existente que se debe realizar la instalación de forma manual y el proceso es diferente dependiendo de si está utilizando la versión de Elasticsearch 1.7 o 2.x, como se describe en los siguientes procedimientos.

### <a name="installing-marvel-with-elasticsearch-173-or-earlier"></a>Instalar Marvel con 1,73 Elasticsearch o una versión anterior

Si está utilizando Elasticsearch 1.7.3 o versiones anteriores, realice los siguientes pasos *en todos los nodos* en el clúster:

- Inicie sesión en el nodo y mover al directorio de inicio de Elasticsearch.  En Linux, es el directorio de inicio típico `/usr/share/elasticsearch`.

-  Ejecute el comando siguiente para descargar e instalar el complemento Marvel para Elasticsearch:

```bash
sudo bin/plugin -i elasticsearch/marvel/latest
```

- Detener y reiniciar Elasticsearch en el nodo:

```bash
sudo service elasticsearch restart
```

- Para comprobar que Marvel se ha instalado correctamente, abra un navegador web y vaya a la dirección URL `http://<server>:9200/_plugin/marvel`. Reemplazar `<server>` con el nombre o la dirección IP de un servidor de Elasticsearch en el clúster.  Compruebe que aparece una página similar a la se muestra a continuación:

![](./media/guidance-elasticsearch/performance-image20.png)


### <a name="installing-marvel-with-elasticsearch-200-or-later"></a>Instalación Marvel con Elasticsearch 2.0.0 o posterior

Si está utilizando Elasticsearch 2.0.0 o posterior, realice las siguientes tareas *en todos los nodos* en el clúster:

Inicie sesión en el nodo y mover al directorio de inicio de Elasticsearch (normalmente `/usr/share/elasticsearch`) ejecute los comandos siguientes para descargar e instalar el complemento Marvel para Elasticsearch:

```bash
sudo bin/plugin install license
sudo bin/plugin install marvel-agent
```

Detener y reiniciar Elasticsearch en el nodo:

```bash
sudo service elasticsearch restart
```

En el siguiente procedimiento, reemplace `<kibana-version>` con 4.2.2 si está utilizando Elasticsearch 2.0.0 o Elasticsearch 2.0.1 o con 4.3.1 si está utilizando Elasticsearch 2.1.0 o posterior.  Reemplazar `<marvel-version>` con 2.0.0 si está utilizando Elasticsearch 2.0.0 o Elasticsearch 2.0.1 o con 2.1.0 si está utilizando Elasticsearch 2.1.0 o posterior.  Realice las siguientes tareas *en un nodo* de clúster:

Inicie sesión en el nodo y descargue la versión de Kibana adecuada para su versión de Elasticsearch desde el [Elasticsearch descargar el sitio web](https://www.elastic.co/downloads/past-releases), extraer el paquete:

```bash
wget https://download.elastic.co/kibana/kibana/kibana-<kibana-version>-linux-x64.tar.gz
tar xvzf kibana-<kibana-version>-linux-x64.tar.gz
```

Abrir el puerto 5601 para aceptar las solicitudes entrantes:

```bash
sudo iptables -A INPUT -m state --state NEW -m tcp -p tcp --dport 5601 -j ACCEPT
```

Mover a la carpeta de configuración de Kibana (`kibana-<kibana-version>-linux-x64/config`), editar el `kibana.yml` de archivo y a continuación, agregue la línea siguiente. Reemplazar `<server>` con el nombre o la dirección IP de un servidor de Elasticsearch en el clúster:

```yaml
elasticsearch.url: "http://<server>:9200"
```

Mover a la carpeta de la Papelera de Kibana (`kibana-<kibana-version>-linux-x64/bin`), y ejecute el comando siguiente para integrar el complemento Marvel Kibana:

```bash
sudo ./kibana plugin --install elasticsearch/marvel/<marvel-version>
```

Iniciar Kibana:

```bash
sudo nohup ./kibana &
```

Para comprobar la instalación Marvel, abra un navegador web y vaya a la dirección URL `http://<server>:5601/app/marvel`. Reemplazar `<server>` con el nombre o la dirección IP del servidor que ejecuta Kibana.

Compruebe que aparece una página similar a la que se muestra a continuación (el nombre del clúster es probable que varían desde la que se muestra en la imagen).

![](./media/guidance-elasticsearch/performance-image21.png)

Haga clic en el vínculo que corresponda al clúster (elasticsearch210 en la imagen anterior). Debe aparecer una página similar a la se muestra a continuación:

![](./media/guidance-elasticsearch/performance-image22.png)


[Ajuste del rendimiento de recopilación de datos para Elasticsearch en Azure]: guidance-elasticsearch-tuning-data-ingestion-performance.md  
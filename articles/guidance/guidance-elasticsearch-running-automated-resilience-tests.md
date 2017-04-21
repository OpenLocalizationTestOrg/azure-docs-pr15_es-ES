<properties
   pageTitle="Ejecutar las pruebas automatizadas de la resistencia Elasticsearch | Microsoft Azure"
   description="Descripción de cómo puede ejecutar las pruebas de la resistencia en su entorno."
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

# <a name="running-the-automated-elasticsearch-resiliency-tests"></a>Ejecutar las pruebas automatizadas de la resistencia de Elasticsearch

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

En este artículo forma [parte de una serie](guidance-elasticsearch.md).

En [configuración resistencia y recuperación de Elasticsearch en Azure][elasticsearch-resilience-recovery], se ha descrito una serie de pruebas que se realizaron en un clúster de Elasticsearch de ejemplo para determinar cómo el sistema ha respondido a algunas formas comunes de error y cómo recuperan. Las pruebas se secuencias de comandos para permitir que se ejecuten de manera automática. Este documento describe cómo puede repetir las pruebas en su entorno. 

Se han probado las situaciones siguientes:

- **Error de nodo y reiniciar sin pérdida de datos**. Un nodo de datos se detenga y reinicie después de 5 minutos.
Se ha configurado para Elasticsearch no para reasignar shards que faltan en este intervalo, por lo que no se incurre i/OS adicionales en desplazarse shards. Cuando se reinicia el nodo, el proceso de recuperación incluye la shards en ese nodo volver actualizado.

- **Error de nodo con pérdida de datos grave**. Un nodo de datos se detiene y se eliminan de los datos que contiene para simular un error de error de disco. Reiniciar el nodo después (después de 5 minutos), un modo eficaz que actúe como un reemplazo para el nodo original. El proceso de recuperación requiere reconstruir los datos que faltan para este nodo y puede implicar reubicar shards llevan a cabo en otros nodos.

- **Error de nodo y reiniciar sin pérdida de datos, pero con reasignación fragmentar**. Un nodo de datos se detiene y la shards que contiene se reasignen a otros nodos. A continuación, se reinicia el nodo y reasignación más se produce para equilibrar el clúster.

- **Distribuir actualizaciones**. Cada nodo del clúster se detenga y reinicie después de un intervalo breve para simular máquinas que se reinicia después de una actualización de software. Solo nodo se detiene en cualquier momento.
No se reasignen shards mientras el nodo está desactivado.

## <a name="prerequisites"></a>Requisitos previos

Las pruebas automatizadas requieren los elementos siguientes:

- Un clúster de Elasticsearch.

- Configuración de entorno de JMeter como se describe en las [instrucciones de pruebas de rendimiento]. 

- Las siguientes adiciones instaladas en la VM de patrón JMeter solo.

    - Tiempo de ejecución de Java 7.

    - Nodejs 4.x.x o posterior.

    - Las herramientas de línea de comandos Git.

## <a name="how-the-scripts-work"></a>¿Cómo funcionan las secuencias de comandos

Los scripts de prueba se van a ejecutar en la VM JMeter patrón. Cuando se selecciona una prueba para ejecutar, las secuencias de comandos realizan la secuencia de las operaciones siguiente:

1.  Iniciar un plan de pruebas JMeter pasa los parámetros que ha especificado.

2.  Copiar un script que realiza las operaciones necesarios para que la prueba a una máquina virtual especificada en el clúster. Esto puede ser cualquier máquina virtual que tiene una dirección IP pública o la máquina virtual de *Jumpbox* si ha creado el clúster con la [plantilla de Azure Elasticsearch tutorial rápido](https://github.com/Azure/azure-quickstart-templates/tree/master/elasticsearch).

3.  Ejecute la secuencia de comandos en la máquina virtual (o Jumpbox).

La imagen siguiente muestra la estructura del entorno de prueba y clúster de Elasticsearch. Tenga en cuenta que las secuencias de comandos de prueba usar shell seguro (SSH) para conectarse a cada nodo del clúster para realizar diversas operaciones Elasticsearch como detener o reiniciar un nodo.

![](./media/guidance-elasticsearch/resilience-testing1.png)

## <a name="setting-up-the-jmeter-tests"></a>Comprueba la configuración de la JMeter

Antes de ejecutar la resistencia a prueba debe compilar e implementar las pruebas JUnit ubicadas en la carpeta de la resistencia/jmeter/pruebas. Estas pruebas se hace referencia en el plan de pruebas JMeter. Para obtener más información, vea el procedimiento "Importación de un proyecto existente JUnit en Eclipse" en la [implementación de una muestra de JMeter JUnit para probar el rendimiento de Elasticsearch][].

Hay dos versiones de las pruebas de JUnit mantenidas en las siguientes carpetas:

- **Elasticsearch17.** El proyecto en esta carpeta genera el archivo Elasticsearch17.jar. Use este JAR para realizar pruebas Elasticsearch versiones 1.7

- **Elasticsearch20**. El proyecto en esta carpeta genera el archivo Elasticsearch20.jar. Use este JAR para realizar pruebas Elasticsearch versión 2.0.0 y versiones posteriores

Copie el archivo JAR adecuado junto con el resto de las dependencias en los equipos de JMeter. El proceso se describe en el procedimiento "Implementar una prueba JUnit JMeter" en la [implementación de una muestra de JMeter JUnit pruebas de Elasticsearch de rendimiento].

## <a name="configuring-vm-security-for-each-node"></a>Configuración de seguridad de la máquina virtual para cada nodo

Las secuencias de comandos de prueba requieren un certificado de autenticación esté instalado en cada nodo Elasticsearch en el clúster. Esto permite que las secuencias de comandos que se ejecute automáticamente sin preguntar un nombre de usuario o la contraseña que se conectan a las VM distintos.

Inicie sesión en uno de los nodos en el clúster Elasticsearch (o la VM Jumpbox) y, a continuación, ejecute el comando siguiente para generar una clave de autenticación:

```Shell
ssh-keygen -t rsa
```

Mientras está conectado al Elasticsearch nodo (o Jumpbox), ejecute los comandos siguientes para todos los nodos en el clúster de Elasticsearch. Reemplazar `<username>` con el nombre de un usuario válido en cada VM y reemplazar `<nodename>` con el nombre DNS o la dirección IP de la máquina virtual aloja el nodo Elasticsearch.
Tenga en cuenta que se le pedirá la contraseña del usuario cuando se ejecuta estos comandos.
Para obtener más información, vea [Inicio de sesión SSH sin contraseña](http://www.linuxproblem.org/art_9.html):

```Shell
ssh <username>@<nodename> mkdir -p .ssh (
cat .ssh/id\_rsa.pub | ssh <username>*@<nodename> 'cat &gt;&gt; .ssh/authorized\_keys'
```

## <a name="downloading-and-configuring-the-test-scripts"></a>Descargar y configurar las secuencias de comandos de prueba

Los scripts de prueba se proporcionan en un repositorio Git. Use el procedimiento siguiente para descargar y configurar las secuencias de comandos.

En el equipo de patrón de JMeter donde se ejecutará las pruebas, abra una ventana de escritorio de Git (Git fiesta) y clonar del repositorio que contiene las secuencias de comandos, como sigue:

```Shell
git clone https://github.com/mspnp/azure-guidance.git
```

Mover a la carpeta de pruebas de resistencia y ejecute el comando siguiente para instalar las dependencias necesarias para ejecutar las pruebas:

```Shell
npm install
```

Si el patrón de JMeter se ejecuta en Windows, descargue [Plink](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html), que es una interfaz de línea de comandos al cliente PuTTY Telnet. Copie el ejecutable Plink a la carpeta de la resistencia-pruebas/biblioteca.

Si el patrón de JMeter se ejecuta en Linux, no es necesario descargar Plink, pero deberá configurar contraseña menos SSH entre el patrón de JMeter y el nodo Elasticsearch o Jumpbox utiliza siguiendo los pasos descritos en el procedimiento "Configurar VM seguridad para cada nodo." 

Editar los siguientes parámetros de configuración de la `config.js` archivo para que coincida con su entorno de prueba y clúster de Elasticsearch. Estos parámetros son comunes a todas las pruebas:

| Nombre | Descripción | Valor predeterminado |
| ---- | ----------- | ------------- |
| `jmeterPath` | Ruta de acceso local donde se encuentra JMeter. | `C:/apache-jmeter-2.13` |
| `resultsPath` | Directorio relativo donde la secuencia de comandos vuelca el resultado. | `results` |
| `verbose` | Indica si se envía la secuencia de comandos en modo detallado o no. | `true` |
| `remote` | Indica si las pruebas JMeter ejecutarán localmente o en los servidores remotos. | `true` |
| `cluster.clusterName` | El nombre del clúster Elasticsearch. | `elasticsearch` |
| `cluster.jumpboxIp`         | La dirección IP del equipo Jumpbox.                 |-|
| `cluster.username`          | El usuario de administrador que creó al implementar el clúster. |-|
| `cluster.password`          | La contraseña para el usuario administrador.                        |-|
| `cluster.loadBalancer.ip`   | La dirección IP del equilibrador de carga Elasticsearch.    |-|
| `cluster.loadBalancer.url`  | Dirección URL base del equilibrador de carga.                          |-|

## <a name="running-the-tests"></a>Ejecutar las pruebas

Mover a la carpeta de pruebas de resistencia y ejecute el siguiente comando:

```Shell
node app.js
```

Debería aparecer el siguiente menú:

![](./media/guidance-elasticsearch/resilience-testing2.png)

Escriba el número del escenario que desee ejecutar: `11`, `12`, `13` o `21`. 

Una vez que seleccione un escenario, la prueba se ejecutará automáticamente. Los resultados se almacenan como un conjunto de archivos de valores separados por comas (CSV) en una carpeta que creó en el directorio de resultados. Cada ejecución tiene su propia carpeta de resultados.
Puede usar Excel para analizar y representar gráficamente estos datos.

[Running Elasticsearch on Azure]: guidance-elasticsearch-running-on-azure.md
[Tuning Data Ingestion Performance for Elasticsearch on Azure]: guidance-elasticsearch-tuning-data-ingestion-performance.md
[Guía de pruebas de rendimiento]: guidance-elasticsearch-creating-performance-testing-environment.md
[JMeter guidance]: guidance-elasticsearch-implementing-jmeter.md
[Considerations for JMeter]: guidance-elasticsearch-deploying-jmeter-junit-sampler.md
[Query aggregation and performance]: guidance-elasticsearch-query-aggregation-performance.md
[elasticsearch-resilience-recovery]: guidance-elasticsearch-configuring-resilience-and-recovery.md
[Resilience and Recovery Testing]: guidance-elasticsearch-running-automated-resilience-tests.md
[Implementar un muestrario JMeter JUnit para realizar pruebas de rendimiento Elasticsearch]: guidance-elasticsearch-deploying-jmeter-junit-sampler.md

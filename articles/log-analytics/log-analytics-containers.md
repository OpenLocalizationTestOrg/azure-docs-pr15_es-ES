<properties
    pageTitle="Solución de contenedores en análisis de registro | Microsoft Azure"
    description="La solución de contenedores en análisis de registro le ayuda a ver y administrar sus hosts de contenedor Docker en una única ubicación."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="banders"/>



# <a name="containers-preview-solution-log-analytics"></a>Solución de contenedores (vista preliminar) análisis de registro

En este artículo se describe cómo configurar y utilizar la solución de contenedores en análisis de registro, que le ayudará a ver y administrar sus hosts de contenedor Docker en una única ubicación. Docker es un sistema de virtualización de software utilizado para crear contenedores que automatizan la implementación de software para su infraestructura de TI.

Con la solución, puede ver qué contenedores se están ejecutando en su host contenedor y las imágenes que se ejecuten en los contenedores. Puede ver información detallada de auditoría que muestra comandos usados con contenedores. Y puede solucionar contenedores viendo y buscar registros centralizados sin tener que ver de forma remota hosts Docker. Puede encontrar contenedores que pueden estar ruido y consumir exceso de recursos en un host. Y puede ver centralizada CPU, memoria, almacenamiento e información de uso y el rendimiento de red de los contenedores.

## <a name="installing-and-configuring-the-solution"></a>Instalar y configurar la solución

Use la información siguiente para instalar y configurar la solución.

Agregar la solución de contenedores al área de trabajo OMS con el proceso descrito en [soluciones de agregar el registro de análisis de la Galería de soluciones](log-analytics-add-solutions.md).

Hay dos formas de instalar y usar Docker con OMS:

- En sistemas operativos de Linux compatibles, instalar y ejecutar Docker y, a continuación, instalar y configurar al agente de OMS para Linux
- En CoreOS, instalar y ejecutar Docker y, a continuación, configurar el OMSAgent para ejecutar dentro de un contenedor

Revise las versiones de sistema operativo Docker y Linux compatibles para su host contenedor en [GitHub](https://github.com/Microsoft/OMS-docker).

>[AZURE.IMPORTANT] Docker debe estar ejecutándose **antes de** instalar el [Agente de OMS para Linux](log-analytics-linux-agents.md) en su host contenedor. Si ya ha instalado al agente antes de instalar Docker, deberá volver a instalar al agente de OMS para Linux. Para obtener más información sobre Docker, consulte el [sitio Web de Docker](https://www.docker.com).

Necesita la siguiente configuración configurada en sus hosts de contenedor para poder supervisar los contenedores.

## <a name="configure-settings-for-the-linux-container-host"></a>Configurar las opciones para el host de contenedor Linux

Después de instalar a Docker, use la siguiente configuración para su host de contenedor para configurar al agente para su uso con Docker. CoreOS no admite este método de configuración.

### <a name="to-configure-settings-for-the-container-host---systemd-suse-opensuse-centos-7x-rhel-7x-and-ubuntu-15x-and-higher"></a>Para configurar el host de contenedor - systemd (SUSE, openSUSE, CentOS 7.x, RHEL 7.x y Ubuntu 15.x y posteriores)

1. Editar docker.service para agregar el siguiente:

    ```
    [Service]
    ...
    Environment="DOCKER_OPTS=--log-driver=fluentd --log-opt fluentd-address=localhost:25225"
    ...
    ```

2. Agregar $DOCKER\_opte por &quot;daemon de =, usr, Papelera/docker ExecStart&quot; en el archivo docker.service. Usando el ejemplo siguiente.

    ```
    [Service]
    Environment="DOCKER_OPTS=--log-driver=fluentd --log-opt fluentd-address=localhost:25225"
    ExecStart=/usr/bin/docker daemon -H fd:// $DOCKER_OPTS
    ```

3. Reinicie el servicio de Docker. Por ejemplo:

    ```
    sudo systemctl restart docker.service
    ```

### <a name="to-configure-settings-for-the-container-host---upstart-ubuntu-14x"></a>Para configurar el host de contenedor - Upstart (Ubuntu 14.x)

1. Editar /etc/default/docker y agregue lo siguiente:

    ```
    DOCKER_OPTS="--log-driver=fluentd --log-opt fluentd-address=localhost:25225"
    ```

2. Guarde el archivo y, a continuación, reinicie los servicios Docker y OMS.

    ```
    sudo service docker restart
    ```

### <a name="to-configure-settings-for-the-container-host---amazon-linux"></a>Para configurar el host de contenedor - Linux de Amazon

1. Editar /etc/sysconfig/docker y agregue lo siguiente:

    ```
    OPTIONS="--log-driver=fluentd --log-opt fluentd-address=localhost:25225"
    ```

2. Guarde el archivo y, a continuación, reinicie el servicio de Docker.

    ```
    sudo service docker restart
    ```

## <a name="configure-settings-for-coreos-containers"></a>Establecer la configuración de los contenedores CoreOS

Después de instalar a Docker, use los siguientes ajustes para CoreOS ejecutar Docker y crear un contenedor. Puede usar cualquier versión compatible de Linux, incluidos CoreOS, con este método de configuración. Necesitará su [Id. de área de trabajo OMS y clave](log-analytics-linux-agents.md).

### <a name="to-use-oms-for-all-containers-with-coreos"></a>Para usar OMS para todos los contenedores con CoreOS

- Inicie el contenedor OMS que desea supervisar. Modificar y utilizar el ejemplo siguiente.

  ```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -e WSID="your workspace id" -e KEY="your key" -h=`hostname` -p 127.0.0.1:25224:25224/udp -p 127.0.0.1:25225:25225 --name="omsagent" --log-driver=none --restart=always microsoft/oms
```

### <a name="switching-from-using-an-installed-agent-to-one-in-a-container"></a>Dejar de usar a un agente instalado uno en un contenedor

Si previamente se utiliza al agente de instalado directamente y desea usar en su lugar un agente que se ejecuta en un contenedor, primero debe quitar OMSAgent. Consulte los [pasos para instalar el agente de OMS para Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md).

## <a name="containers-data-collection-details"></a>Detalles del conjunto de datos de contenedores

La solución de contenedores recopila varios datos métricas y registro de rendimiento de hosts de contenedor y contenedores con los agentes de OMS para Linux ha habilitado y de OMSAgent que se ejecuta en contenedores.

La siguiente tabla muestra los métodos de conjunto de datos y otros detalles acerca de cómo se recopilan los datos de los contenedores.

| plataforma | Agente OMS para Linux | Agente SCOM | Almacenamiento de Azure | ¿SCOM obligatorio? | Datos de agente SCOM enviadas por grupo de administración | frecuencia de la colección |
|---|---|---|---|---|---|---|
|Linux|![Sí](./media/log-analytics-containers/oms-bullet-green.png)|![No](./media/log-analytics-containers/oms-bullet-red.png)|![No](./media/log-analytics-containers/oms-bullet-red.png)|            ![No](./media/log-analytics-containers/oms-bullet-red.png)|![No](./media/log-analytics-containers/oms-bullet-red.png)| cada 3 minutos|


La siguiente tabla se muestran ejemplos de los tipos de datos recopilados por la solución de contenedores:

| Tipo de datos | Campos |
| --- | --- |
| Rendimiento para hosts y contenedores | Equipo, ObjectName, CounterName y Nº 40; % tiempo de procesador, lecturas de disco MB, disco escribe MB, MB de uso de memoria, red Bytes, red enviar Bytes, procesador sec de uso, red & #41; CounterValue, TimeGenerated, rutaDeAccesoDeContador, SourceSystem |
| Inventario de contenedor | TimeGenerated, el equipo, el nombre del contenedor, ContainerHostname, imagen, ImageTag, ContinerState, código, EnvironmentVar, comando, CreatedTime, StartedTime, FinishedTime, SourceSystem, IdContenedor, ImageID |
| Inventario de imagen de contenedor | TimeGenerated, equipo, imagen, ImageTag, ImageSize, VirtualSize, en ejecución, en pausa, detenido, error, SourceSystem, ImageID, TotalContainer |
| Registro de contenedor | TimeGenerated, el equipo, el Id. de imagen, el nombre del contenedor, LogEntrySource, entrada de registro, SourceSystem, IdContenedor |
| Registro de servicio de contenedor | TimeGenerated, equipo, TimeOfCommand, imagen, comando, SourceSystem, IdContenedor |

## <a name="monitor-containers"></a>Monitor contenedores

Una vez que la solución habilitada en el portal OMS, verá el mosaico de **contenedores** que muestra información de resumen sobre los hosts contenedor y los contenedores que se ejecuta en hosts.

![Mosaico de contenedores](./media/log-analytics-containers/containers-title.png)

El mosaico muestra una descripción general de los contenedores cuántos tiene en el entorno y si está error, ejecución o detenido.

### <a name="using-the-containers-dashboard"></a>Con el panel de contenedores

Haga clic en el mosaico de **contenedores** . Desde allí verá vistas organizadas por:

- Eventos de contenedor
- Errores
- Estado de contenedores
- Inventario de imagen de contenedor
- Rendimiento de la CPU y la memoria

Cada panel en el panel es una representación visual de una búsqueda que se ejecute en los datos recopilados.

![Panel de contenedores](./media/log-analytics-containers/containers-dash01.png)

![Panel de contenedores](./media/log-analytics-containers/containers-dash02.png)

En el módulo de **Estado de contenedor** , haga clic en el área superior, tal como se muestra a continuación.

![Estado de contenedores](./media/log-analytics-containers/containers-status.png)

Búsqueda de registros se abre, que muestra información sobre los hosts y contenedores que se ejecuta en ellos.

![Búsqueda de registros de los contenedores](./media/log-analytics-containers/containers-log-search.png)

Desde aquí, puede editar la consulta de búsqueda para modificarla para encontrar la información específica que le interesa. Para obtener más información acerca de las búsquedas de registro, vea [búsquedas de registro de análisis de registro](log-analytics-log-searches.md).

Por ejemplo, puede modificar la consulta de búsqueda para que se muestran todos los contenedores detenidos en lugar de los contenedores de ejecución cambiando **ejecutando** en **detenido** en la consulta de búsqueda.

## <a name="troubleshoot-by-finding-a-failed-container"></a>Buscar un contenedor de error para solucionar

OMS marca un contenedor como **error** si ha terminado con un código de salida distinto de cero. Puede ver un resumen de los errores y errores en el entorno en el módulo de **Contenedores de error** .

### <a name="to-find-failed-containers"></a>Buscar errores contenedores

1. Haga clic en el módulo de **Eventos de contenedor** .  
  ![eventos de contenedores](./media/log-analytics-containers/containers-events.png)
2. Búsqueda de registros se abre, que muestra el estado de contenedores, similares al siguiente.  
  ![estado de contenedores](./media/log-analytics-containers/containers-container-state.png)
3. A continuación, haga clic en el valor de error para ver información adicional, como el tamaño de la imagen y el número de error y ha dejado de imágenes. Expanda **Mostrar más** para ver el Id. de imagen  
  ![Error contenedores](./media/log-analytics-containers/containers-state-failed.png)
4. A continuación, busque el contenedor que se está ejecutando esta imagen. Escriba lo siguiente en la consulta de búsqueda.
  `Type=ContainerInventory <ImageID>`Se mostrarán los registros. Puede desplazarse para ver el error al contenedor.  
  ![Error contenedores](./media/log-analytics-containers/containers-failed04.png)


## <a name="search-logs-for-container-data"></a>Registros de búsqueda de datos del contenedor

Cuando está solucionando problemas de un error específico, puede ayudar a ver dónde se está produciendo en su entorno. Los siguientes tipos de registro le ayudará a crear consultas para devolver la información que desee.

- **ContainerInventory** : Utilice este tipo si desea obtener información sobre la ubicación del contenedor, ¿cuáles son sus nombres y qué imágenes está ejecutando.
- **ContainerImageInventory** : Utilice este tipo si está tratando de encontrar información organizados por la imagen y ver información de la imagen como identificadores o tamaños de imagen.
- **ContainerLog** : Utilice este tipo si desea buscar las entradas y la información de registro de error específico.
- **ContainerServiceLog** : Utilice este tipo si está tratando de encontrar la información de auditoría para el daemon Docker, como iniciar, detener, eliminar o comandos de extracción.

### <a name="to-search-logs-for-container-data"></a>Para buscar los registros de datos del contenedor

- Elija una imagen que sabe que no se pudo realizar recientemente y busque los registros de errores. Empiece por buscar un nombre de contenedor que se está ejecutando la imagen con una búsqueda **ContainerInventory** . Por ejemplo, buscar`Type=ContainerInventory ubuntu Failed`  
    ![Búsqueda de los contenedores de Ubuntu](./media/log-analytics-containers/search-ubuntu.png)

  Anote el nombre del contenedor junto al **nombre**y buscar los registros. En este ejemplo, es `Type=ContainerLog adoring_meitner`.

**Ver la información de rendimiento**

Cuando está comenzando a crear consultas, puede ayudar a ver lo que puede hacer en primer lugar. Por ejemplo, para ver todos los datos de rendimiento, pruebe una amplia consulta escribiendo la siguiente consulta de búsqueda.

```
Type=Perf
```

![rendimiento de contenedores](./media/log-analytics-containers/containers-perf01.png)



Puede ver en una forma más gráfica al hacer clic en la palabra **medidas** en los resultados.

![rendimiento de contenedores](./media/log-analytics-containers/containers-perf02.png)



Puede definir el ámbito de los datos de rendimiento que está viendo en un contenedor específico escribiendo el nombre de la misma a la derecha de la consulta.

```
Type=Perf <containerName>
```

Que muestra la lista de los indicadores de rendimiento que se recopilan para un contenedor individual.

![rendimiento de contenedores](./media/log-analytics-containers/containers-perf03.png)

## <a name="example-log-search-queries"></a>Consultas de búsqueda de registro de ejemplo

A menudo es útil crear consultas, comenzando por un ejemplo o dos y modificarlos para adaptarse a su entorno. Como punto de partida, puede experimentar con el módulo **Notables consultas** para generar consultas más avanzadas.

![Consultas de contenedores](./media/log-analytics-containers/containers-queries.png)

## <a name="saving-log-search-queries"></a>Guardar registro de consultas de búsqueda

Guardar consultas es una característica estándar de análisis de registro. Guardándolos, tendrá los que haya encontrado útil útiles para usos futuros.

Después de crear una consulta que resulte útil, guárdelo haciendo clic en **Favoritos** en la parte superior de la página de búsqueda de registros. A continuación, puede puede fácilmente acceso más adelante desde la página de **Mi panel** .

## <a name="next-steps"></a>Pasos siguientes

- [Registros de búsqueda](log-analytics-log-searches.md) para ver los registros de datos de contenedor detallada.

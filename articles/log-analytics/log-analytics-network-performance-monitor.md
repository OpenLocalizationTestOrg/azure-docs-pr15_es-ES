<properties
    pageTitle="Solución de Monitor de rendimiento en OMS red | Microsoft Azure"
    description="Ayuda a supervisar el rendimiento de sus redes de cerca real-tiempo a detecta el Monitor de rendimiento de red y busque botella de rendimiento de red."
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
    ms.date="07/28/2016"
    ms.author="banders"/>

# <a name="network-performance-monitor-preview-solution-in-oms"></a>Solución de Monitor de rendimiento (Preview) en OMS de red

>[AZURE.NOTE] Esta es una [solución de vista previa](log-analytics-add-solutions.md#log-analytics-preview-solutions-and-features).

Este documento describe cómo configurar y utilizar la solución de Monitor de rendimiento de red en OMS, que ayuda a supervisar el rendimiento de sus redes de cerca real-tiempo a detecta y busque red botella de rendimiento. Con la solución de Monitor de rendimiento de red, puede supervisar la pérdida y la latencia entre dos redes, subredes o servidores. Monitor de rendimiento de red detecta problemas de red como blackholing de tráfico, enrutamiento errores y los problemas que no se puede detectar los métodos de supervisión de red convencional. Monitor de rendimiento de red genera alertas y notifica como y cuando se infringe un umbral de un vínculo de red. Pueden aprender estos umbrales automáticamente por el sistema o puede configurarlos para usar las reglas de alertas personalizadas. Monitor de rendimiento de red garantiza la detección oportuna de problemas de rendimiento de red y localiza el origen del problema a un segmento de red determinado o un dispositivo.

Puede detectar problemas de red con el panel de la solución que muestra información resumida sobre su red incluidos recientes eventos de estado de red, vínculos de red mal estado y vínculos de subred que se opuestas latencia y alta pérdida de paquetes. Puede profundizar en un vínculo de red para ver el estado actual de vínculos de subred, así como vínculos de nodo a otro. También puede ver la tendencia histórica de pérdida y la latencia en el nivel de nodo a otro, la subred y la red. Puede detectar problemas de red transitorias mediante la visualización de gráficos de tendencia histórica de pérdida de paquetes y latencia y busque botella en la red en un mapa de topología. El gráfico de topología interactivos le permite visualizar las rutas de red salto a salto y determinar el origen del problema. Como otras soluciones, puede usar la búsqueda de registro para diferentes requisitos de análisis para crear informes personalizados basados en los datos recopilados por el Monitor de rendimiento de red.

La solución utiliza síntesis transacciones como mecanismo principal para detectar errores en la red. Por lo tanto, puede usar sin tener en cuenta para el proveedor del dispositivo de red específica o el modelo. Funciona en entornos híbridos, nube (IaaS) y local. La solución descubre automáticamente la topología de red y diversas rutas en su red.

Productos de supervisión de red típica centran en supervisar el estado del dispositivo (enrutadores, conmutadores, etc.) de red, pero no proporcionan recomendaciones sobre la calidad real de conectividad de red entre dos puntos, que es el Monitor de rendimiento de red.

### <a name="using-the-solution-standalone"></a>Utiliza el independiente de la solución

Si desea controlar la calidad de conexiones de red entre sus cargas de trabajo críticas, redes, centros de datos o sitios de office y después puede usar la solución del Monitor de rendimiento de red por sí mismo para supervisar el mantenimiento de conectividad entre:

- varios sitios de centros de datos o de office que están conectados mediante una red pública o privada
- críticas cargas de trabajo que se están ejecutando aplicaciones empresariales
- Servicios de nube pública como Microsoft Azure o servicios Web de Amazon (AWS) y redes local, si tiene IaaS (VM) disponible y tiene puertas de enlace configurados para permitir la comunicación entre local y redes de nube
- Redes de Azure y local al usar ruta de Express

### <a name="using-the-solution-with-other-networking-tools"></a>Con la solución en otras herramientas de red

Si desea supervisar una línea de aplicación empresarial, puede usar la solución Network Performance Monitor como una solución companion a otras herramientas de red. Una red lenta puede dar lugar a aplicaciones lentas y Monitor de rendimiento de red puede ayudarle a investigar problemas de rendimiento de la aplicación que son causados por problemas de red subyacentes. Dado que la solución no requiere acceso a los dispositivos de red, Administrador de la aplicación no necesita confía en un equipo de red para proporcionar información acerca de cómo afecta la red de aplicaciones.

Además, si ya invertir en otra herramientas de supervisión de red, la solución puede complemento estas herramientas porque las soluciones de supervisión de red más tradicionales no proporcionan recomendaciones sobre los indicadores de rendimiento de red de llevar a cabo como pérdida y la latencia.  La solución de Monitor de rendimiento de red puede ayudar a diferencia de relleno.


## <a name="installing-and-configuring-agents-for-the-solution"></a>Instalar y configurar agentes para la solución

Use los procesos básicos para instalar a agentes en [equipos con Windows conectarse al análisis de registro](log-analytics-windows-agents.md) y [Conectar Operations Manager para el análisis de registro](log-analytics-om-agents.md).

>[AZURE.NOTE]
Debe instalar a al menos 2 agentes para tener suficientes datos para ver y supervisar los recursos de red. En caso contrario, la solución permanecerá en un estado de configuración hasta que instale y configure agentes adicionales.

### <a name="where-to-install-the-agents"></a>Dónde instalar los agentes

Antes de instalar los agentes, tenga en cuenta la topología de su red y qué partes de la red que desea supervisar. Le recomendamos que instale a más de un agente de cada subred que desee supervisar. En otras palabras, para cada subred que desea supervisar, seleccione dos o más servidores o máquinas virtuales e instalar al agente en ellos.

Si no está seguro sobre la topología de la red, instale a los agentes en servidores con cargas de trabajo críticas donde desea supervisar el rendimiento de red. Por ejemplo, que desea realizar un seguimiento de una conexión de red entre un servidor Web y un servidor que ejecuta SQL Server. En este ejemplo, un agente se instalan en ambos servidores.

Agentes supervisan la conectividad de red (vínculos) entre hosts--no los hosts a sí mismos. Por lo tanto, para supervisar un vínculo de red, debe instalar a agentes de ambos extremos de dicho vínculo.

### <a name="configure-agents"></a>Configurar agentes

Después de instalar a agentes, debe abrir los puertos de firewall de esos equipos para asegurarse de que pueden comunicarse agentes. Debe descargar y, a continuación, ejecute el [script de PowerShell EnableRules.ps1](https://gallery.technet.microsoft.com/OMS-Network-Performance-04a66634) sin parámetros en una ventana de PowerShell con privilegios de administrador

La secuencia de comandos crea claves de registro necesarios para que el Monitor de rendimiento de red y crea reglas de firewall de Windows para permitir que los agentes crear conexiones TCP entre sí. Las claves de registro creadas por la secuencia de comandos también especifican si desea registrar los registros de depuración y la ruta de acceso del archivo de registros. También se define el puerto TCP de agente utilizado para la comunicación. Los valores de estas teclas se establecen automáticamente la secuencia de comandos, por lo que no debe cambiar manualmente estas teclas.

El puerto que se abre de manera predeterminada es 8084. Puede usar un puerto personalizado mediante el parámetro `portNumber` a la secuencia de comandos. Sin embargo, se debe utilizar el mismo puerto en todos los equipos donde se ejecuta la secuencia de comandos.

>[AZURE.NOTE] La secuencia de comandos de EnableRules.ps1 configura las reglas de firewall de Windows solo en el equipo donde se ejecuta la secuencia de comandos. Si tiene un firewall de red, asegúrese de que permite tráfico destinado al puerto TCP que utiliza Monitor de rendimiento de red.


## <a name="configuring-the-solution"></a>Configuración de la solución

Use la información siguiente para instalar y configurar la solución.

1. La solución Network Performance Monitor adquiere datos de equipos que ejecutan Windows Server 2008 Service Pack 1 o posterior o Windows 7 SP1 o posterior, que son los requisitos del mismos como agente de supervisión de Microsoft (MMA).
2. Agregar la solución de Monitor de rendimiento de red al área de trabajo OMS con el proceso descrito en [soluciones de agregar el registro de análisis de la Galería de soluciones](log-analytics-add-solutions.md).  
  ![Símbolo de Monitor de rendimiento de red](./media/log-analytics-network-performance-monitor/npm-symbol.png)
3. En el portal OMS, verá un nuevo mosaico titulado **Monitor de rendimiento de red** con el mensaje de *solución requiere configuración adicional*. Deberá configurar la solución para agregar redes basan en subredes y nodos que se detectan agentes. Haga clic en el **Monitor de rendimiento de red** para iniciar la configuración de la red de forma predeterminada.  
  ![solución requiere configuración adicional](./media/log-analytics-network-performance-monitor/npm-config.png)


### <a name="configure-the-solution-with-a-default-network"></a>Configurar la solución con una red de forma predeterminada

En la página Configuración, verá una única red con el nombre **predeterminado**. Si todavía no lo ha definido las redes, las subredes detecta automáticamente se colocan en la red de forma predeterminada.

Cuando se crea una red, agregue una subred a él y subred se quita de la red de forma predeterminada. Si elimina una red, todos sus subredes se devuelven automáticamente a la red de forma predeterminada.

En otras palabras, la red de forma predeterminada es el contenedor de todas las subredes que no están en una red definidas por el usuario. No puede editar o eliminar la red de forma predeterminada. Siempre permanece en el sistema. Sin embargo, puede crear tantas redes como sea necesario.

En la mayoría de los casos, las subredes de su organización se ordenarán en más de una red y debe crear una o varias redes para agrupar lógicamente las subredes.

### <a name="create-new-networks"></a>Crear nuevas redes

Una red de rendimiento en Monitor de red es un contenedor de subredes. Puede crear una red con cualquier nombre que desee y agregar subredes a la red. Por ejemplo, puede crear una red con nombre *Building1* y, a continuación, agregar subredes, o puede crear una red con nombre *DMZ* y, a continuación, agregar todas las subredes que pertenecen a DMZ a esta red.

#### <a name="to-create-a-new-network"></a>Para crear una nueva red

1. Haga clic en **Agregar red** y, a continuación, escriba el nombre de red y la descripción.
2.  Seleccione una o varias subredes y, a continuación, haga clic en **Agregar**.
3. Haga clic en **Guardar** para guardar la configuración.  
  ![Agregar red](./media/log-analytics-network-performance-monitor/npm-add-network.png)



### <a name="wait-for-data-aggregation"></a>Espere para la agregación de datos

Una vez guardada la configuración por primera vez, inicia la solución recopilar información de latencia y pérdida de paquetes de red entre los nodos donde están instalados los agentes. Este proceso puede demorarse unos instantes, a veces 30 minutos. Durante este estado, el icono de Monitor de rendimiento de red en la página Descripción general muestra un mensaje que indica la *agregación de datos en el proceso*.

![agregación de datos en curso](./media/log-analytics-network-performance-monitor/npm-aggregation.png)


Cuando se ha cargado los datos, verá los datos que muestra actualizarlo el icono de Monitor de rendimiento de red.

![Icono de Monitor de rendimiento de red](./media/log-analytics-network-performance-monitor/npm-tile.png)

Haga clic en el icono para ver el escritorio del Monitor de rendimiento de red.

![Panel del Monitor de rendimiento de red](./media/log-analytics-network-performance-monitor/npm-dash01.png)

### <a name="edit-monitoring-settings-for-subnets"></a>Editar la configuración de supervisión de subredes

Todas las subredes donde se instaló al menos un agente aparecen en la ficha **subredes** en la página de configuración.

#### <a name="to-enable-or-disable-monitoring-for-particular-subnetworks"></a>Para habilitar o deshabilitar la supervisión de subredes determinados

1. Seleccione o desactive la casilla al lado del **ID de subred** y, a continuación, asegúrese de que **se usa para supervisión** está seleccionado o desactivada, según corresponda. Puede seleccionar o borrar varias subredes. Cuando está deshabilitada, no se supervisan subredes como los agentes se actualizará para dejar de hacer ping a otros agentes.
2. Elija los nodos que desea supervisar para una determinada subred seleccionando la subred de la lista y mover los nodos necesarios entre las listas que contienen nodos no supervisados y supervisados.
Puede agregar una **Descripción** de personalizado a la subred, si lo desea.
3. Haga clic en **Guardar** para guardar la configuración.  
  ![Editar subred](./media/log-analytics-network-performance-monitor/npm-edit-subnet.png)

### <a name="choose-nodes-to-monitor"></a>Elija los nodos para supervisar

Todos los nodos que tienen un agente instalado en ellos aparecen en la pestaña de **nodos** .

#### <a name="to-enable-or-disable-monitoring-for-nodes"></a>Para habilitar o deshabilitar la supervisión de nodos

1. Active o desactive los nodos que desea supervisar o detener la supervisión.
2. Haga clic en **utilizar para supervisión**o desactivarla, según corresponda.
3. Haga clic en **Guardar**.  
  ![habilitar la supervisión de nodo](./media/log-analytics-network-performance-monitor/npm-enable-node-monitor.png)


### <a name="set-monitoring-rules"></a>Conjunto de reglas de supervisión

Monitor de rendimiento de red genera eventos de estado sobre la conectividad entre un par de nodos o vínculos de red o de subred cuando se infringe un umbral. Pueden aprender estos umbrales automáticamente por el sistema o puede configurar reglas de alertas personalizadas.

La *regla predeterminada* es creado por el sistema y crea un evento de estado cada vez que la pérdida o la latencia entre cualquier par de redes o subred vínculos violaciones el umbral ha aprendido el sistema. Puede deshabilitar la regla predeterminada y crear reglas personalizadas de supervisión

#### <a name="to-create-custom-monitoring-rules"></a>Para crear reglas personalizadas de supervisión

1. Haga clic en **Agregar regla** , en la pestaña **Monitor** y escriba el nombre de la regla y la descripción.
2. Seleccione el par de los vínculos de red o subred para supervisar en las listas.
3. Primero seleccione la red que contiene la primera subred/s de interés en la lista desplegable de red y, a continuación, seleccione la subred/s en la lista desplegable de subred correspondiente.
Seleccione **todas las subredes** si desea supervisar todas las subredes en un vínculo de red. Del mismo modo, seleccione los otros subred/s de interés. Y puede hacer clic en **Agregar excepción** para excluir la supervisión de vínculos de subred determinado de la selección que ha realizado.
4. Si no desea crear eventos de estado de los elementos que ha seleccionado, a continuación, desactive **Habilitar los vínculos de esta regla de supervisión de estado**.
5. Elegir condiciones de supervisión.
Puede establecer umbrales personalizados para la generación de eventos de estado escribiendo valores de umbral. Cuando el valor de la condición pasa por encima de su umbral seleccionado para el par de red/subred seleccionada, se genera un evento de estado.
6. Haga clic en **Guardar** para guardar la configuración.  
  ![crear una regla de supervisión personalizada](./media/log-analytics-network-performance-monitor/npm-monitor-rule.png)


## <a name="data-collection-details"></a>Detalles del conjunto de datos

Monitor de rendimiento de red usa paquetes de protocolo de enlace SYNACK-TCP SYN-ACK para recopilar pérdida y traceroute e información de latencia también se usa para obtener información sobre la topología.

La siguiente tabla muestra los métodos de conjunto de datos y otros detalles acerca de cómo se recopilan los datos para el Monitor de rendimiento de red.

| plataforma | Agente directo | Agente SCOM | Almacenamiento de Azure | ¿SCOM obligatorio? | Datos de agente SCOM enviadas por grupo de administración | frecuencia de la colección |
|---|---|---|---|---|---|---|
| Windows |![Sí](./media/log-analytics-network-performance-monitor/oms-bullet-green.png)|![Sí](./media/log-analytics-network-performance-monitor/oms-bullet-green.png)|![No](./media/log-analytics-network-performance-monitor/oms-bullet-red.png)|            ![No](./media/log-analytics-network-performance-monitor/oms-bullet-red.png)|![No](./media/log-analytics-network-performance-monitor/oms-bullet-red.png)| Protocolos de enlace TCP cada 5 segundos, datos envían cada 3 minutos |

La solución utiliza transacciones síntesis para evaluar el estado de la red. Agentes OMS instalados en varias secciones en los paquetes de TCP de exchange de red entre sí y, en el proceso, obtenga información sobre la pérdida de paquetes y de tiempo de ida y vuelta, si hay alguna. Periódicamente, cada agente también realiza un seguimiento de la ruta a otros agentes para buscar todas las diversas rutas en la red que se debe probar. Con estos datos, los agentes pueden deducir la latencia de red y las cifras de pérdida de paquetes. Las pruebas se repiten cada cinco segundos y los agentes agregados datos durante un período de tres minutos antes de cargarlo a OMS.

>[AZURE.NOTE] Aunque los agentes comuniquen con frecuencia, no genera una gran cantidad de tráfico de red mientras lleva a cabo las pruebas. Agentes confían únicamente en los paquetes de protocolo de enlace SYNACK-TCP SYN-ACK para determinar la pérdida y la latencia--sin datos están intercambiados paquetes. Durante este proceso, agentes comuniquen solo cuando sea necesario y la topología de comunicación de agente está optimizada para reducir el tráfico de red.

## <a name="using-the-solution"></a>Con la solución

Esta sección explica el panel de todas las funciones y cómo usarlas.

### <a name="solution-overview-tile"></a>Mosaico de información general sobre la solución

Después de que se ha habilitado la solución del Monitor de rendimiento de red, el mosaico de la solución en la página de información general de OMS proporciona una breve introducción sobre el estado de la red. Muestra un gráfico de anillos que muestra el número de vínculos de subred positivos y negativos. Al hacer clic en el mosaico, se abre el panel de la solución.

![Icono de Monitor de rendimiento de red](./media/log-analytics-network-performance-monitor/npm-tile.png)


### <a name="network-performance-monitor-solution-dashboard"></a>Panel de solución del Monitor de rendimiento de red

El módulo de **Resumen de red** muestra un resumen de las redes, junto con su tamaño relativo. Está seguido de los mosaicos que muestra el número total de vínculos de red, vínculos de subred y rutas de acceso en el sistema (una ruta de acceso consta de las direcciones IP de dos hosts con agentes y todos los saltos entre ellas).

El módulo de **Eventos de estado de red de principio** proporciona una lista de eventos de mantenimiento más recientes y alertas en el sistema y la hora desde que se activa el evento. Se genera una alerta o un evento de estado cada vez que la pérdida de paquetes o la latencia de un vínculo de red o subred supera un umbral.

El módulo de **Vínculos de red mal estado superior** muestra una lista de vínculos de red mal estado. Estos son los vínculos de red que tienen uno o más eventos de salud negativos en ese momento.

Los módulos de **Vínculos de subred principio con más pérdida** y **subred con más latencia** mostrar los vínculos de subred superior por la pérdida de paquetes y vínculos de subred superior latencia respectivamente. Latencia alta o una cantidad de pérdida de paquetes cabría esperar en algunos vínculos de red. Estos vínculos aparecen en las listas de diez superiores, pero no se marcan mal Estados.

El módulo de **Consultas comunes** contiene un conjunto de consultas de búsqueda que obtener directamente los datos de supervisión de red sin formato. Puede usar estas consultas como punto de partida para crear sus propias consultas para los informes personalizados.

![Panel del Monitor de rendimiento de red](./media/log-analytics-network-performance-monitor/npm-dash01.png)


### <a name="drill-down-for-depth"></a>Obtención de profundidad

Puede hacer clic en los distintos vínculos en el panel de solución para profundizar más en cualquier área de interés. Por ejemplo, cuando vea una alerta o un vínculo de red mal estado aparecerán en el escritorio, puede hacer clic en ella para investigar más. Se le dirigirá a una página que enumera todos los vínculos de subred para el vínculo de red determinado. Podrá ver el estado de pérdida, la latencia y la salud de cada vínculo subred y rápidamente averigüe qué vínculos de subred causan el problema. A continuación, puede hacer clic en **vínculos de nodo de la vista** para ver todos los vínculos de nodo del vínculo de subred mal estado. A continuación, puede ver los vínculos de nodo a nodo individuales y buscar los vínculos de nodo mal estado.

Puede hacer clic en **Ver topología** para ver la topología de salto a salto de las rutas entre los nodos de origen y de destino. Las rutas negativos o saltos se muestran en rojo para que pueda identificar rápidamente el problema a una parte determinada de la red.

![datos de detalle](./media/log-analytics-network-performance-monitor/npm-drill.png)


#### <a name="trend-charts"></a>Gráficos de tendencia.

En cada nivel que detalle, puede ver la tendencia de pérdida y la latencia de un vínculo de red. Gráficos de tendencia también están disponibles para vínculos de subred y nodo. Puede cambiar el intervalo de tiempo para el gráfico trazar mediante el control de tiempo en la parte superior del gráfico.

Los gráficos de tendencia muestran una perspectiva histórica del rendimiento de un vínculo de red. Algunos problemas de red están transitorias de naturaleza y serían difíciles detectar solo consultando el estado actual de la red. Esto es porque problemas pueden exponer rápidamente y desaparezca antes de que nadie solo para volver a aparecer más adelante en el tiempo. También pueden resultar difíciles para los administradores de la aplicación de dichos problemas temporales porque los problemas a menudo superficie como inesperados incrementos en el tiempo de respuesta de aplicación, incluso cuando aparecen todos los componentes de la aplicación funciona sin problemas.

Fácilmente puede detectar esos tipos de problemas buscando en un gráfico de tendencia donde el problema aparecerá como una punta rápida de latencia de red o la pérdida de paquetes.

![gráfico de tendencia.](./media/log-analytics-network-performance-monitor/npm-trend.png)

#### <a name="hop-by-hop-topology-map"></a>Mapa de topología de salto a salto

Monitor de rendimiento de red se muestra la topología de salto a salto de rutas entre dos nodos en un mapa de topología interactivos. Puede ver el mapa de topología seleccionando un vínculo de nodo y, a continuación, haga clic en **Ver topología**. Además, puede ver el mapa de topología haciendo clic en el mosaico de **rutas** en el panel. Al hacer clic en **rutas de acceso** en el panel, tendrá que seleccionar los nodos de origen y destino en el panel izquierdo y, a continuación, haga clic en el **trazado** para trazar las rutas entre los dos nodos.

El mapa de topología muestra cuántos rutas están entre los dos nodos y qué rutas tomar los paquetes de datos. Botella de rendimiento de red se marca en rojo en el mapa de topología. Puede encontrar una conexión de red defectuoso o un dispositivo defectuoso consultando elementos de color rojo en el mapa de topología.

Al hacer clic un nodo o desplace el puntero sobre él en el mapa de topología, verá las propiedades del nodo como FQDN y la dirección IP. Haga clic en un salto que es la dirección IP. Puede resaltar determinadas rutas desactivando y, a continuación, seleccionar sólo las rutas que desea resaltar en el mapa. Puede acercar o alejar el mapa de topología usando la rueda del mouse.

Observe que la topología que se muestra en el mapa de topología de nivel 3 y no contiene conexiones y dispositivos de nivel 2.

![mapa de topología de salto a salto](./media/log-analytics-network-performance-monitor/npm-topology.png)

#### <a name="fault-localization"></a>Localización de errores

Monitor de rendimiento de red es capaz de encontrar la botella en la red sin conexión a los dispositivos de red. En función de los datos que se recopila desde la red y aplicando los algoritmos avanzados en el gráfico de red, Monitor de rendimiento de red hace que una estimación de probabilidad de los componentes de red que es más probable que el origen del problema.

Este enfoque es útil para determinar la botella en la red cuando el acceso a saltos no está disponible porque no requiere todos los datos se recopilan de los dispositivos de red, como enrutadores o conmutadores. También es útil cuando los saltos entre dos nodos no están en el control administrativo. Por ejemplo, los saltos pueden ser enrutadores de ISP.

### <a name="log-analytics-search"></a>Búsqueda de análisis de registro

Todos los datos gráficamente expuestas a través del panel Monitor de rendimiento de red y explorar en profundidad páginas también está disponible originalmente en búsqueda de análisis de registro. Puede consultar los datos mediante el lenguaje de consulta de búsqueda y crear informes personalizados mediante la exportación de los datos a Excel o PowerBI. El módulo de **Consultas comunes** en el panel tiene algunas consultas útiles que puede usar como punto de partida para crear sus propias consultas e informes.

![consultas de búsqueda](./media/log-analytics-network-performance-monitor/npm-queries.png)


## <a name="investigate-the-root-cause-of-a-health-alert"></a>Investigar la causa de una alerta de estado

Ahora que ha leído acerca del Monitor de rendimiento de red, echemos un vistazo a una sencilla investigación de la causa de un evento de estado.

1. En la página información general, obtendrá una instantánea rápida del estado de su red observando el **Monitor de rendimiento de red** de mosaico. Observe que fuera de los vínculos de 80 subredes supervisados, 43 mal estado. Esto garantiza la investigación. Haga clic en el icono para ver el panel de la solución.  
  ![Icono de Monitor de rendimiento de red](./media/log-analytics-network-performance-monitor/npm-investigation01.png)

2. En la imagen de ejemplo siguiente, habrá observado que hay actualmente 4 eventos de estado y 4 vínculos de red que no funcionan correctamente. Decida investigar el problema y haga clic en el vínculo de red **Web de Sharepoint** para averiguar la raíz del problema.  
  ![ejemplo de vínculo de red mal estado](./media/log-analytics-network-performance-monitor/npm-investigation02.png)

3. La página de detalle muestra todos los vínculos de subred en el vínculo de red **Web de Sharepoint** . Habrá observado que para ambos los vínculos de subred, la latencia ha cruzado el umbral haciendo que el vínculo de red mal estado. También puede ver las tendencias de latencia de los vínculos de subred. Puede usar la selección de tiempo control del gráfico para centrarse en el intervalo de tiempo necesario. Puede ver la hora del día cuando latencia alcanza su máximo. Más adelante, puede buscar los registros durante este período de tiempo investigar el problema. Haga clic en **los vínculos de nodo de vista** para profundizar más.  
  ![ejemplo de vínculos de subred negativa](./media/log-analytics-network-performance-monitor/npm-investigation03.png)

4.  Similar a la página anterior, la página de detalle para el vínculo de subred determinado enumera los vínculos de su nodo constituyentes. Puede realizar acciones similares aquí como hizo en el paso anterior. Haga clic en **Ver topología** para ver la topología entre los nodos de 2.  
  ![ejemplo de vínculos de nodos negativa](./media/log-analytics-network-performance-monitor/npm-investigation04.png)

5. Todas las rutas entre los nodos seleccionados 2 se trazan en el mapa de topología. Puede visualizar la topología de salto a salto de rutas entre dos nodos en el mapa de topología. Le proporciona una imagen clara de existen rutas de cuántos entre los dos nodos y qué rutas están realizando los paquetes de datos. Botella de rendimiento de red se marca en color rojo. Puede encontrar una conexión de red defectuoso o un dispositivo defectuoso consultando elementos de color rojo en el mapa de topología.  
  ![ejemplo de la vista de topología negativa](./media/log-analytics-network-performance-monitor/npm-investigation05.png)

6. La pérdida, latencia y el número de saltos de cada path pueden visualizarse en el panel de **Detalles de la ruta de acceso** . En este ejemplo, puede ver que hay rutas mal Estados 3 como se mencionó en el panel. Use la barra de desplazamiento para ver los detalles de las rutas de acceso en mal Estados.  Use las casillas de verificación para seleccionar una de las rutas de acceso para que se representa la topología de un solo path. Puede usar la rueda para acercar o alejar el mapa de topología.

  En la imagen siguiente puede ver claramente la causa de las áreas de problema a la sección específica de la red consultando las rutas y saltos de color rojo. Al hacer clic en un nodo en el mapa de topología muestra las propiedades del nodo, incluido el FQDN y la dirección IP. Al hacer clic en un salto muestra la dirección IP del salto.  
  ![topología de mal estada: ejemplo de ruta de detalles](./media/log-analytics-network-performance-monitor/npm-investigation06.png)

## <a name="next-steps"></a>Pasos siguientes

- [Registros de búsqueda](log-analytics-log-searches.md) para ver los registros de datos de rendimiento de red detallado.

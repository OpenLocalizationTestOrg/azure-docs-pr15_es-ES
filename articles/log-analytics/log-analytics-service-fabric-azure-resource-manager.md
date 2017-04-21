<properties
    pageTitle="Optimizar su entorno con la solución de tela de servicio de análisis de registro | Microsoft Azure"
    description="Puede usar la solución de servicio tela para evaluar los riesgos y la salud de su servicio tela aplicaciones, servicios de micro, nodos y clústeres."
    services="log-analytics"
    documentationCenter=""
    authors="niniikhena"
    manager="jochan"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/21/2016"
    ms.author="nini"/>



# <a name="service-fabric-solution-in-log-analytics"></a>Solución de tela de servicios de análisis de registro

> [AZURE.SELECTOR]
- [Administrador de recursos](log-analytics-service-fabric-azure-resource-manager.md)
- [PowerShell](log-analytics-service-fabric.md)

En este artículo se describe cómo utilizar la solución de servicio tela en análisis de registro para ayudarle a identificar y solucionar problemas en el clúster de servicio tela.

La solución de servicio tela usa datos de Azure diagnósticos de sus máquinas virtuales tela de servicio, mediante la recopilación de datos de las tablas de Azure TORUNDA. Análisis de registro, a continuación, lee eventos de marco de tela de servicio, incluyendo **Confiable eventos de servicio**, **Eventos de Actor**, **Eventos operativas**y **eventos ETW personalizado**. Con el panel de la solución, es posible ver los eventos relevantes y problemas importantes en su entorno de tela de servicio.

Para empezar con la solución, debe conectar el clúster tela de servicio a un área de trabajo de análisis de registro. Hay tres escenarios para tener en cuenta:

1. Si no ha implementado el clúster tela de servicio, realice los pasos de ***implementar un clúster de tela servicio conectado a un área de trabajo de análisis de registro*** para implementar un nuevo clúster y haya configurado al informe de análisis de registro.

2. Si necesita recopilar contadores de rendimiento de sus hosts usar otras soluciones OMS como la seguridad en el clúster de tela de servicio, siga los pasos de ***implementar un clúster de tela servicio conectado a un área de trabajo OMS con extensión VM instalado.***

3. Si ya ha implementado el clúster de servicio tela y desea conectarse a análisis de registro, siga los pasos de ***Agregar una cuenta de almacenamiento existente para el análisis de registro.***


##<a name="deploy-a-service-fabric-cluster-connected-to-a-log-analytics-workspace"></a>Implementar un clúster de tela de servicio conectado a un área de trabajo de análisis de registro.
Esta plantilla hace lo siguiente:


1. Implementa un clúster de Azure servicio tela ya está conectado a un área de trabajo de análisis de registro. Tiene la opción de crear un área de trabajo durante la implementación de la plantilla o el nombre de un área de trabajo existente de análisis de registro de entrada.
2. Agrega la cuenta de almacenamiento de información de diagnóstico para el área de trabajo de análisis de registro.
3. Permite la solución de tela de servicio en el área de trabajo de análisis de registro.

[![Implementar en Azure](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2Fservice-fabric-oms%2F%2Fazuredeploy.json)


Una vez que se seleccione el botón de implementar anterior, llegarán en el portal de Azure con parámetros para modificarlo. Asegúrese de crear un nuevo grupo de recursos, si escribe un nuevo nombre de área de trabajo de análisis de registro: ![tela de servicio](./media/log-analytics-service-fabric/2.png)

![Tela de servicio](./media/log-analytics-service-fabric/3.png)

Acepte las condiciones legales y visitas "Crear" para empezar la implementación. Una vez completada la implementación, debería ver el área de trabajo y clúster creado y la WADServiceFabric * evento, WADWindowsEventLogs y WADETWEvent tablas agregadas:

![Tela de servicio](./media/log-analytics-service-fabric/4.png)

##<a name="deploy-a-service-fabric-cluster-connected-to-an-oms-workspace-with-vm-extension-installed"></a>Implementar un clúster de tela servicio conectada a un área de trabajo OMS con extensión VM instalado.
Esta plantilla hace lo siguiente:

1. Implementa un clúster de Azure servicio tela ya está conectado a un área de trabajo de análisis de registro. Puede crear un área de trabajo o usar una existente.
2. Agrega las cuentas de almacenamiento de información de diagnóstico para el área de trabajo de análisis de registro.
3. Permite la solución de tela de servicio en el área de trabajo de análisis de registro.
4. Instala la extensión del agente MMA en cada escala VM establecida en el clúster de servicio tela. Con el agente MMA instalado, es posible ver los indicadores de rendimiento acerca de los nodos.


[![Implementar en Azure](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2Fservice-fabric-vmss-oms%2F%2Fazuredeploy.json)


Siguiendo los mismos pasos, introduzca los parámetros necesarios y empezar una implementación. Una vez más debe ver el área de trabajo, clúster y las tablas de TORUNDA todo creados:

![Tela de servicio](./media/log-analytics-service-fabric/5.png)

###<a name="viewing-performance-data"></a>Ver datos de rendimiento

Para ver los datos de rendimiento de los nodos:
</br>
- Inicie el área de trabajo de análisis de registro desde el portal de Azure.

![Tela de servicio](./media/log-analytics-service-fabric/6.png)

- Vaya a configuración en el panel izquierdo y a continuación, seleccione datos >> contadores de rendimiento de Windows >> "Agregar contadores de rendimiento seleccionados": ![tela de servicio](./media/log-analytics-service-fabric/7.png)

- En búsqueda de registros, use las siguientes consultas para profundizar en métricas claves sobre los nodos:
</br>

    una. Comparar el uso de CPU promedio en todos los nodos en la última hora para ver los nodos que están teniendo problemas y en el intervalo de tiempo que un nodo tenía un pico:

    ``` Type=Perf ObjectName=Processor CounterName="% Processor Time"|measure avg(CounterValue) by Computer Interval 1HOUR. ```

    ![Tela de servicio](./media/log-analytics-service-fabric/10.png)


    b. Ver los gráficos de líneas similares para la memoria disponible en cada nodo con esta consulta:

    ```Type=Perf ObjectName=Memory CounterName="Available MBytes Memory" | measure avg(CounterValue) by Computer Interval 1HOUR.```

    Para ver una lista de los nodos, que muestra el valor promedio exacto de MB disponibles para cada nodo, utilice esta consulta:

    ```Type=Perf (ObjectName=Memory) (CounterName="Available MBytes") | measure avg(CounterValue) by Computer ```

    ![Tela de servicio](./media/log-analytics-service-fabric/11.png)


    c. En el caso que desea explorar en profundidad en un nodo específico examinando el promedio por hora, el uso de CPU mínimo, máximo y percentil 75, esté puede hacerlo con esta consulta (reemplazar campo equipo):

    ```Type=Perf CounterName="% Processor Time" InstanceName=_Total Computer="BaconDC01.BaconLand.com"| measure min(CounterValue), avg(CounterValue), percentile75(CounterValue), max(CounterValue) by Computer Interval 1HOUR```

    ![Tela de servicio](./media/log-analytics-service-fabric/12.png)

    Obtenga más información sobre los indicadores de rendimiento en el registro de análisis [aquí]. (https://blogs.technet.microsoft.com/msoms/tag/metrics/)


##<a name="adding-an-existing-storage-account-to-log-analytics"></a>Agregar una cuenta de almacenamiento existente para el análisis de registro

Esta plantilla simplemente agrega sus cuentas de almacenamiento existente a un área de trabajo de análisis de registro nuevo o existente.
</br>

[![Implementar en Azure](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Foms-existing-storage-account%2Fazuredeploy.json)

>[AZURE.NOTE] En seleccionar un grupo de recursos, si está trabajando con un área de trabajo de análisis de registro ya existente, seleccione "Usar existente" y busque el grupo de recursos que contiene el área de trabajo OMS. Cree una nueva uno if en caso contrario.
![Tela de servicio](./media/log-analytics-service-fabric/8.png)

Después de que se ha implementado esta plantilla, podrá ver la cuenta de almacenamiento conectada a su área de trabajo de análisis de registro. En este caso, he agregado una cuenta de almacenamiento más al área de trabajo de Exchange que creó anteriormente.
![Tela de servicio](./media/log-analytics-service-fabric/9.png)

## <a name="view-service-fabric-events"></a>Ver eventos de tela de servicio

Una vez completadas las implementaciones y la solución de tela de servicio se ha habilitado en el área de trabajo, seleccione el icono de **Tela de servicio** en el portal de análisis de registro para iniciar el panel de tela de servicio. El panel incluye las columnas de la tabla siguiente. Cada columna enumera los eventos de diez superiores por el número que coincida con los criterios de la columna del intervalo de tiempo especificado. Puede ejecutar una búsqueda de registro que proporciona toda la lista, haga clic en **Ver todo** en la parte inferior derecha de cada columna, o haciendo clic en el encabezado de columna.

| **Evento tela de servicio** | **Descripción** |
| --- | --- |
| Problemas importantes | Una pantalla de problemas como RunAsyncFailures RunAsynCancellations y listas desplegables de nodo. |
| Eventos operativos | Eventos operativos importantes como implementaciones y actualización de la aplicación. |
| Eventos de servicio confiable | Eventos importantes servicio confiable como un Runasyncinvocations. |
| Eventos de actor | Eventos de actor notables generados por los servicios de micro, como las excepciones iniciadas por un método de actor, activaciones actor y desactivaciones y así sucesivamente. |
| Eventos de aplicación | Todos los personalizado ETW eventos generados por las aplicaciones. |

![Panel de tela de servicio](./media/log-analytics-service-fabric/sf3.png)

![Panel de tela de servicio](./media/log-analytics-service-fabric/sf4.png)


La siguiente tabla muestra los métodos de conjunto de datos y otros detalles acerca de cómo se recopilan los datos para servicio tela.

| plataforma | Agente directo | Agente SCOM | Almacenamiento de Azure | ¿SCOM obligatorio? | Datos de agente SCOM enviadas por grupo de administración | frecuencia de la colección |
|---|---|---|---|---|---|---|
|Windows|![No](./media/log-analytics-malware/oms-bullet-red.png)|![No](./media/log-analytics-malware/oms-bullet-red.png)| ![Sí](./media/log-analytics-malware/oms-bullet-green.png)|            ![No](./media/log-analytics-malware/oms-bullet-red.png)|![No](./media/log-analytics-malware/oms-bullet-red.png)|10 minutos |


>[AZURE.NOTE] Puede cambiar el ámbito de estos eventos en la solución de servicio tela haciendo clic en **datos basándose en los últimos 7 días** en la parte superior del panel. También puede mostrar los eventos generados dentro de los últimos 7 días, 1 día o seis horas. O bien, puede seleccionar **personalizada** para especificar un intervalo de fechas personalizado.


## <a name="next-steps"></a>Pasos siguientes

- Usar [Búsquedas de registro de análisis de registro](log-analytics-log-searches.md) para ver datos detallados de evento tela de servicio.

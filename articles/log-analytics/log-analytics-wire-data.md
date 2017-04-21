<properties
    pageTitle="Conectar la solución de datos de análisis de registro | Microsoft Azure"
    description="Datos de alambre están datos consolidados de red y el rendimiento de equipos con agentes OMS, incluidos Operations Manager y agentes conectado de Windows. Datos de la red se combinan con los datos de registro para ayudarle a relacionar datos."
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
    ms.date="08/11/2016"
    ms.author="banders"/>

# <a name="wire-data-solution-in-log-analytics"></a>Conectar la solución de datos de análisis de registro

Datos de alambre están datos consolidados de red y el rendimiento de equipos con agentes OMS, incluidos Operations Manager y agentes conectado de Windows. Datos de la red se combinan con los datos de registro para ayudarle a relacionar datos. Agentes OMS instalados en equipos en los datos de red monitor de infraestructura de TI enviados y de esos equipos para los niveles de red 2-3 en el [modelo OSI](https://en.wikipedia.org/wiki/OSI_model) , incluidos los diversos protocolos y puertos utilizados.

>[AZURE.NOTE] La solución de datos de conexión no está disponible actualmente se agregue a áreas de trabajo. Los clientes que ya dispone de la solución de datos de alambre habilitada pueden seguir usando la solución de datos de alambre.

De forma predeterminada, OMS recopila datos registrados de CPU, memoria, disco y datos de rendimiento de red de contadores integrados en Windows. Red y otro recopilación de datos se realiza en tiempo real para cada agente, incluidos subredes y protocolos de nivel de aplicación que está usando el equipo. Puede agregar otros contadores de rendimiento en la página de configuración en la pestaña de registros.

Si ha usado [sFlow](http://www.sflow.org/) u otro software con el [Protocolo de NetFlow de Cisco](http://www.cisco.com/c/en/us/products/collateral/ios-nx-os-software/ios-netflow/prod_white_paper0900aecd80406232.html), a continuación, las estadísticas y verá los datos de conexión de datos será familiares.

Algunos de los tipos de consultas de búsqueda integradas de registro son:

- Agentes que proporcionan datos de alambre
- Dirección IP de agentes de proporcionar datos de alambre
- Comunicaciones salientes por direcciones IP
- Número de bytes enviados por protocolos de aplicación
- Número de bytes enviados por un servicio de aplicación
- Bytes recibidos por diferentes protocolos
- Número total de bytes enviados y recibidos por IP
- Direcciones IP que se han comunicado con agentes en la subred 10.0.0.0/8
- Latencia promedio de conexiones que se han medido confiable
- Procesos del equipo que iniciaron o recibe el tráfico de red
- Cantidad de tráfico de red de un proceso

Cuando busque usando datos de cable, puede filtrar y agrupar datos para ver información sobre los agentes superiores y los protocolos superiores. O puede buscar en cuando algunos equipos (direcciones IP de las direcciones y MAC) comunicadas con otros, para el tiempo y la cantidad de datos se envió--básicamente, ver metadatos acerca de tráfico de red, que está basado en la búsqueda.

Sin embargo, dado que está viendo metadatos, no es necesariamente útil para la solución de problemas en profundidad. Datos de alambre en OMS no están una captura completa de los datos de la red. Así que no está pensado para la solución de problemas de nivel de paquete oscuro.
La ventaja de usar al agente, en comparación con otros métodos de conjunto, es que no tendrá que instalar electrodomésticos, vuelva a configurar los modificadores de la red o realizar configuraciones complicadas. Datos de alambre están simplemente basada en agente--instalar el agente en un equipo y realizará la supervisión de su propio tráfico de red. Otra ventaja es cuando desee controlar las cargas de trabajo que se ejecuta en proveedores de nube o proveedor de servicios de hospedaje o Microsoft Azure, donde el usuario no posee la capa tela.

Por el contrario, no tiene visibilidad completa de lo que sucede en la red si no instala a agentes en todos los equipos en la infraestructura de red.

## <a name="installing-and-configuring-the-solution"></a>Instalar y configurar la solución
Use la información siguiente para instalar y configurar la solución.

- La solución de datos de alambre adquiere datos de equipos que ejecutan Windows Server 2012 R2, Windows 8.1 y sistemas operativos posteriores.
- Se requiere Microsoft .NET Framework 4.0 o posterior en equipos donde desea obtener datos de alambre de.
- Agregar la solución de conexión de datos al área de trabajo OMS mediante el proceso descrito en [soluciones de agregar el registro de análisis de la Galería de soluciones](log-analytics-add-solutions.md).  No es necesaria realizar ninguna configuración adicional.
- Si desea ver los datos de conexión para una solución específica, debe tener la solución ya agregada al área de trabajo OMS.

## <a name="wire-data-data-collection-details"></a>Detalles del conjunto de datos de datos de cables

Datos de alambre recopila metadatos acerca de tráfico de red mediante los agentes que ha habilitado.

La siguiente tabla muestra los métodos de conjunto de datos y otros detalles acerca de cómo se recopilan los datos para los datos de alambre.


| plataforma | Agente directo | Agente SCOM | Almacenamiento de Azure | ¿SCOM obligatorio? | Datos de agente SCOM enviadas por grupo de administración | frecuencia de la colección |
|---|---|---|---|---|---|---|
|Windows (2012 R2 / 8.1 o posterior)|![Sí](./media/log-analytics-wire-data/oms-bullet-green.png)|![Sí](./media/log-analytics-wire-data/oms-bullet-green.png)|![No](./media/log-analytics-wire-data/oms-bullet-red.png)|            ![No](./media/log-analytics-wire-data/oms-bullet-red.png)|![No](./media/log-analytics-wire-data/oms-bullet-red.png)| cada minuto|


## <a name="combining-wire-data-with-other-solution-data"></a>Combinar datos de alambre con otros datos de la solución

Datos obtenidos mediante las consultas integradas mostradas arriba posible interesantes por sí mismo. Sin embargo, la utilidad de los datos de la conexión se lleva a cabo cuando se combinan con la información de otras soluciones OMS. Por ejemplo, puede usar los datos de eventos de seguridad recopilados por la solución de seguridad y auditoría y combinar con datos de alambre para buscar intentos de inicio de sesión de red inusuales para los procesos con nombre.  En este ejemplo, usaría los operadores IN y DISTINCT para unirse a puntos de datos en la consulta de búsqueda.

Requisitos: Para utilizar el ejemplo siguiente, debe tener instalada la solución de seguridad y auditoría. Sin embargo, puede utilizar datos de otras soluciones para combinar con datos de alambre para conseguir resultados similares.

### <a name="to-combine-wire-data-with-security-events"></a>Para combinar datos de alambre con los eventos de seguridad

1. En la página Introducción, haga clic en el mosaico de **WireData** .
2. En la lista de **Consultas WireData comunes**, haga clic en la **Cantidad de tráfico de red (en Bytes) por el proceso** para ver la lista de procesos devueltos.
    ![consultas de wiredata](./media/log-analytics-wire-data/oms-wiredata-01.png)
3. Si la lista de procesos es demasiado larga para ver fácilmente, puede modificar la consulta de búsqueda similar a la de:

    ```
    Type WireData | measure count() by ProcessName | where AggregatedValue <40
    ```
    Se muestra en el ejemplo siguiente es un proceso denominado DancingPigs.exe, que puede aparecer sospechoso.
    ![resultados de la búsqueda de wiredata](./media/log-analytics-wire-data/oms-wiredata-02.png)

4. Con los datos devueltos en la lista, haga clic en un proceso con nombre. En este ejemplo, DancingPigs.exe se ha hecho clic. Los resultados que se muestra a continuación, describen el tipo de tráfico de red, como comunicaciones salientes sobre diversos protocolos.
    ![resultados de wiredata que muestra un proceso con nombre](./media/log-analytics-wire-data/oms-wiredata-03.png)

5. Porque hay instalada la solución de seguridad y auditoría, puede sondeo a los eventos de seguridad que tengan el mismo valor del campo nombre de proceso modificando la consulta de búsqueda con los operadores de consulta de búsqueda en y DISTINCT. A continuación, puede hacer cuando los datos de alambre y otros registros de solución tienen valores en el mismo formato. Modificar la consulta de búsqueda similar a la de:

    ```
    Type=SecurityEvent ProcessName IN {Type:WireData "DancingPigs.exe" | distinct ProcessName}
    ```    

    ![wiredata resultados que muestra combinar datos](./media/log-analytics-wire-data/oms-wiredata-04.png)
6. En los resultados anteriores, verá que se muestra la información de cuenta. Ahora puede refinar la consulta de búsqueda para averiguar la frecuencia con la cuenta, que muestra datos de auditoría y de seguridad, se utiliza el proceso con una consulta que parezca:        

    ```
    Type=SecurityEvent ProcessName IN {Type:WireData "DancingPigs.exe" | distinct ProcessName} | measure count() by Account
    ```

    ![resultados de wiredata que muestra datos de la cuenta](./media/log-analytics-wire-data/oms-wiredata-05.png)



## <a name="next-steps"></a>Pasos siguientes

- [Registros de búsqueda](log-analytics-log-searches.md) para ver los registros de búsqueda de datos de alambre detallada.
- Ver [Publicar usando datos de alambre en el blog de búsqueda de registros de las operaciones de administración de conjunto de Dan](http://blogs.msdn.com/b/dmuscett/archive/2015/09/09/using-wire-data-in-operations-management-suite.aspx) tiene información adicional sobre la frecuencia con datos que se recopilan y cómo se pueden modificar propiedades de la colección de agentes de Operations Manager.

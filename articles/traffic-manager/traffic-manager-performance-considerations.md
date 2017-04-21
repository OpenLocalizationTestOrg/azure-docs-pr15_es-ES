<properties
    pageTitle="Consideraciones de rendimiento para el administrador del tráfico de Azure | Microsoft Azure"
    description="Comprender el rendimiento en Administrador de tráfico y cómo probar el rendimiento de su sitio Web al usar el Administrador de tráfico"
    services="traffic-manager"
    documentationCenter=""
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="10/11/2016"
    ms.author="sewhee"
/>

# <a name="performance-considerations-for-traffic-manager"></a>Consideraciones de rendimiento para el Administrador de tráfico

Esta página explica las consideraciones de rendimiento con el Administrador de tráfico. Tenga en cuenta lo siguiente:

Hay instancias de su sitio Web en las regiones WestUS y Asia oriental. Una de las instancias falla la comprobación del estado para el sondeo de administrador de tráfico. Tráfico de la aplicación se dirige a la región correcta. Esta migración tras error se espera pero el rendimiento puede ser un problema en función de la latencia del tráfico de viaje ahora a una distancia región.

## <a name="how-traffic-manager-works"></a>Cómo funciona el tráfico de administrador

Solo impacto en el rendimiento que puede tener el Administrador de tráfico en su sitio Web es la búsqueda DNS inicial. El servidor de raíz de DNS de Microsoft que hospeda la zona trafficmanager.net controla una solicitud DNS para el nombre de su perfil de administrador de tráfico. Administrador de tráfico rellena y actualiza periódicamente, servidores de raíz DNS de Microsoft basados en la directiva del Administrador de tráfico y los resultados de sondeo. Aunque durante la búsqueda DNS inicial, ninguna consulta de DNS se envía al administrador de tráfico.

Administrador de tráfico consta de varios componentes: servidores, un servicio de la API, la capa de almacenamiento y un extremo de servicio de supervisión de nombres DNS. Si se produce un error en un componente de servicio del Administrador de tráfico, no hay ningún efecto en el nombre DNS asociado con el perfil de administrador de tráfico. Se modifican los registros de los servidores DNS de Microsoft. Sin embargo, no ocurrir extremo supervisión y actualizaciones de DNS. Por lo tanto, el Administrador de tráfico no es capaz de actualización de DNS para que apunten a su sitio de migración tras error cuando su sitio principal deja de funcionar.

Resolución de nombres DNS es rápida y se almacenan en caché de resultados. La velocidad de la búsqueda DNS inicial depende de los servidores DNS que utiliza el cliente de resolución de nombres. Normalmente, un cliente puede realizar una búsqueda DNS dentro de ms ~ 50. Los resultados de la búsqueda se almacenan en caché para la duración de la DNS tiempo de vida (TTL). El TTL para el Administrador de tráfico predeterminado es 300 segundos.

El tráfico no fluye a través del Administrador de tráfico. Una vez finalizada la búsqueda de DNS, el cliente tiene una dirección IP de una instancia de su sitio web. El cliente se conecta a la dirección y no pasa a través del Administrador de tráfico. La directiva del Administrador de tráfico que elija no tiene influencia en el rendimiento de DNS. Sin embargo, un método enrutamiento de rendimiento puede afectar la experiencia de aplicación. Por ejemplo, si la directiva redirige el tráfico de Norteamérica a una instancia hospedada en Asia, la latencia de red para las sesiones puede ser un problema de rendimiento.

## <a name="measuring-traffic-manager-performance"></a>Medir el rendimiento de administrador de tráfico

Existen varios sitios Web que puede usar para entender el rendimiento y comportamiento de un perfil de administrador de tráfico. Muchos de estos sitios son gratuitas pero pueden tener limitaciones. Algunos sitios ofrecen mejorado el seguimiento y la notificación de pago.

Las herramientas de estos sitios medida DNS latencia y mostrar la resuelve direcciones IP de ubicaciones de cliente todo el mundo. La mayoría de estas herramientas no almacenar en caché los resultados DNS. Por lo tanto, las herramientas de mostrar la búsqueda DNS completa cada vez que se ejecuta una prueba. Cuando se prueba desde su cliente, solo experimenta el rendimiento de búsqueda DNS completo una vez durante la duración TTL.

## <a name="sample-tools-to-measure-dns-performance"></a>Herramientas de ejemplo para medir el rendimiento de DNS

- [SolveDNS](http://www.solvedns.com/dns-comparison/)

    SolveDNS ofrece muchas herramientas de rendimiento. La herramienta de comparación de DNS puede mostrar cuánto tiempo tarda para resolver el nombre DNS y cómo que se compara con otros proveedores de servicios DNS.

- [WebSitePulse](http://www.websitepulse.com/help/tools.php)

    Una de las herramientas más sencillas es WebSitePulse. Escriba la dirección URL para ver el tiempo de resolución DNS, el primer Byte, el último Byte y otras estadísticas de rendimiento. Puede elegir entre tres ubicaciones diferentes de prueba. En este ejemplo, verá que la primera ejecución muestra que búsqueda DNS toma sec 0.204.

    ![pulse1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse.png)

    Porque se almacenan en caché los resultados, la segunda prueba para el mismo extremo de tráfico administrador la búsqueda DNS tiene sec 0.002.

    ![pulse2](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse2.png)

- [Monitor de aplicación sintético de entidad emisora de certificados](https://asm.ca.com/en/checkit.php)

    Anteriormente conocida como la herramienta de sitio Web de verificación Watchmouse, este sitio muestran el tiempo de resolución DNS de varias regiones geográficas simultáneamente. Escriba la dirección URL para ver el tiempo de resolución DNS, la hora de conexión y la velocidad de varias ubicaciones geográficas. Use esta prueba para ver qué servicio hospedado se devuelve para diferentes ubicaciones del mundo.

    ![pulse1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-watchmouse.png)

- [Pingdom](http://tools.pingdom.com/)

    Esta herramienta proporciona estadísticas de rendimiento para cada elemento de una página web. La pestaña página análisis muestra el porcentaje de tiempo invertido en búsqueda DNS.

- [¿Qué es mi DNS?](http://www.whatsmydns.net/)

    Este sitio realiza una búsqueda DNS desde 20 ubicaciones diferentes y muestra los resultados en un mapa.

- [Explore la interfaz Web](http://www.digwebinterface.com)

    Este sitio muestra que información de DNS incluidos registros a y CNAME más detallada. Asegúrese de que comprueba la 'Colorear resultados' y 'Estadísticas' en Opciones y seleccionar 'Todo' en servidores de nombres.

## <a name="next-steps"></a>Pasos siguientes

[Acerca de los métodos de enrutamiento de tráfico de tráfico administrador](traffic-manager-routing-methods.md)

[Pruebe su configuración de administrador de tráfico](traffic-manager-testing-settings.md)

[Operaciones en el Administrador de tráfico (referencia de la API de REST)](http://go.microsoft.com/fwlink/?LinkId=313584)

[Cmdlets de administrador de tráfico de Azure](http://go.microsoft.com/fwlink/p/?LinkId=400769)

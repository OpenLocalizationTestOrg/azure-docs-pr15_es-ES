<properties
    pageTitle="Anidar perfiles de tráfico Manager | Microsoft Azure"
    description="En este artículo se explica la función de 'Anidados perfiles' del Administrador de tráfico de Azure"
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

# <a name="nested-traffic-manager-profiles"></a>Perfiles de administrador de tráfico anidadas

Administrador de tráfico incluye una gama de métodos de enrutamiento de tráfico que le permiten controlar cómo tráfico administrador elige extremo que debe recibir el tráfico de cada usuario final. Para obtener más información, consulte [métodos de enrutamiento de tráfico de administrador de tráfico](traffic-manager-routing-methods.md).

Cada perfil de administrador de tráfico especifica un solo método de enrutamiento de tráfico. Sin embargo, hay situaciones que requieren enrutamiento más complejas del tráfico de la ruta proporcionada por un solo perfil de administrador de tráfico. Puede anidar perfiles de administrador de tráfico para combinar las ventajas de más de un método de enrutamiento de tráfico. Perfiles anidados permiten invalidar el comportamiento de tráfico administrador predeterminado para soporte más grande y más complejas implementaciones de aplicación.

Los siguientes ejemplos muestran cómo utilizar los perfiles de administrador de tráfico anidada en diversas situaciones.

## <a name="example-1-combining-performance-and-weighted-traffic-routing"></a>Ejemplo 1: Combinar enrutamiento de tráfico 'Rendimiento' y 'Weighted'

Suponga que implementar una aplicación en las siguientes regiones Azure: oeste de Estados Unidos, Europa occidental y Asia oriental. Use el método de enrutamiento de tráfico de 'Rendimiento' del jefe de tráfico para distribuir el tráfico a la región más cerca del usuario.

![Perfil de administrador de tráfico único][1]

Ahora, supongamos que desea probar una actualización en el servicio antes de implementar más ampliamente. Desea usar el método de enrutamiento de tráfico 'ponderado' para dirigir un pequeño porcentaje de tráfico a la implementación de prueba. Configurar la implementación de prueba junto con la implementación de producción existente en Europa occidental.

No se puede combinar dos 'Weighted' y ' tráfico enrutamiento de rendimiento en un único perfil. Para admitir este escenario, cree un perfil de administrador de tráfico con los dos extremos de Europa occidental y el método de enrutamiento de tráfico 'Weighted'. A continuación, agregue este perfil 'child' como un extremo al perfil 'principal'. El perfil primario aún utiliza el método de enrutamiento de tráfico de rendimiento y contiene las otras implementaciones globales como extremos.

El siguiente diagrama muestra en este ejemplo:

![Perfiles de administrador de tráfico anidadas][2]

En esta configuración, el tráfico dirigido a través del perfil primario distribuye el tráfico a través de regiones normalmente. En Europa occidental, el perfil anidado distribuye el tráfico a los extremos de prueba y producción según los pesos asignados.

Cuando el perfil primario utiliza el método de enrutamiento de tráfico 'Rendimiento', debe tener asignado a cada extremo de una ubicación. La ubicación se asigna al configurar el extremo. Elija la región de Azure más cercana a su implementación. Las regiones Azure son los valores de ubicación compatibles con la tabla de latencia de Internet. Para obtener más información, vea [tráfico administrador 'Rendimiento' método de enrutamiento de tráfico](traffic-manager-routing-methods.md#performance-traffic-routing-method).

## <a name="example-2-endpoint-monitoring-in-nested-profiles"></a>Ejemplo 2: Supervisión de extremo de perfiles anidados

Administrador de tráfico conocidas supervisa el estado de cada extremo de servicio. Si un punto final está en mal estado, Administrador de tráfico dirige a los usuarios a los extremos alternativos para conservar la disponibilidad de su servicio. Este comportamiento del extremo de supervisión y migración tras error se aplica a todos los métodos de enrutamiento de tráfico. Para obtener más información, vea [Supervisión de extremo de administrador de tráfico](traffic-manager-monitoring.md). Extremo supervisión funciona de manera diferente para perfiles anidados. Con perfiles anidados, el perfil primario no llevar a cabo comprobaciones de estado en el elemento secundario directamente. En su lugar, el estado de los extremos del perfil secundarios se usa para calcular el estado general del perfil secundarios. La información de estado se propaga la jerarquía de perfil anidada. El perfil de primario esto agregado estado para determinar si para dirigir el tráfico al perfil secundarios. Vea la sección [preguntas más frecuentes](#faq) de este artículo para obtener más detalles sobre la supervisión de mantenimiento de perfiles anidados.

Devolver al ejemplo anterior, supongamos que se produce un error en la implementación de producción de Europa occidental. De manera predeterminada, el perfil 'child' dirige todo el tráfico a la implementación de prueba. Si la distribución de prueba también se produce un error, el perfil de primario determina que el perfil secundarios no debe recibir el tráfico debido a que todos los extremos secundarios son mal Estados. A continuación, el perfil principal distribuye el tráfico a las demás regiones.

![Anidadas perfil migración tras error (comportamiento predeterminado)][3]

Es posible que satisfecho con este acuerdo. O puede ser que se trate de que todo el tráfico de Europa occidental ahora va a la implementación de prueba en lugar de tráfico de un subconjunto limitado. Independientemente del estado de la distribución de prueba, desea conmutar a las demás regiones cuando se produce un error en la implementación de producción de Europa occidental. Para habilitar esta migración tras error, puede especificar el parámetro 'MinChildEndpoints' al configurar el perfil secundarios como un extremo del perfil primario. El parámetro determina el número mínimo de extremos disponibles en el perfil de secundarios. El valor predeterminado es '1'. En este escenario, Establece el valor de MinChildEndpoints a 2. Debajo de este umbral, el perfil primario considera el perfil de todo secundario que no esté disponible y dirige el tráfico a los otros extremos.

La figura siguiente muestra esta configuración:

![Anidar migración tras error de perfil con 'MinChildEndpoints' = 2][4]

>[AZURE.NOTE]
>El método de enrutamiento de tráfico de 'Priority' distribuye todo el tráfico a un único extremo. Así hay propósito pequeño en un valor de MinChildEndpoints distinto de '1' para un perfil secundarios.

## <a name="example-3-prioritized-failover-regions-in-performance-traffic-routing"></a>Ejemplo 3: Prioridades regiones de migración tras error en el enrutamiento de tráfico de 'Rendimiento'

El comportamiento predeterminado para el método de enrutamiento de tráfico 'Rendimiento' está diseñado para evitar exceso cargar el extremo más próximo y causando una serie de errores en cascada. Cuando se produce un error en un punto final, todo el tráfico que hubiera ha dirigido a ese extremo se distribuye uniformemente entre los otros extremos en todas las regiones.

![Tráfico 'Rendimiento' enrutamiento de migración tras error de forma predeterminada][5]

Sin embargo, suponga que prefiere la migración tras error de Europa occidental tráfico oeste de Estados Unidos y solo dirigir el tráfico a otras áreas cuando ambos extremos no están disponibles. Puede crear esta solución mediante un perfil secundarios con el método de enrutamiento de tráfico 'Priority'.

![Enrutamiento con preferencia migración tras error del tráfico 'Rendimiento'][6]

Dado que el extremo de Europa occidental tiene mayor prioridad que el extremo de Estados Unidos oeste, todo el tráfico se envía al extremo de Europa occidental cuando ambos extremos están en línea. Si se produce un error en Europa occidental, el tráfico se dirige a oeste de Estados Unidos. Con el perfil anidado, el tráfico se dirige a asiático solo cuando Europa occidental y Estados Unidos oeste dan un error.

Puede repetir este patrón para todas las regiones. Reemplace todos los extremos de tres en el perfil de primario con tres perfiles secundarios, cada uno con una secuencia con prioridad de migración tras error.

## <a name="example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region"></a>Ejemplo 4: Control 'Rendimiento' enrutamiento de tráfico entre varios extremos de la misma región

Supongamos que el enrutamiento de tráfico método se utiliza en un perfil que tenga más de un extremo de una región determinada 'rendimiento'. De forma predeterminada, el tráfico dirigido a esa región se distribuye uniformemente entre todos los extremos disponibles en dicha región.

![Tráfico 'Rendimiento' enrutamiento de distribución en la región de tráfico (comportamiento predeterminado)][7]

En lugar de agregar varios extremos de Europa occidental, incluidos los extremos en un perfil secundario independiente. El perfil de secundarios se agregará al elemento primario como extremo solo en Europa occidental. La configuración de perfil de secundarios puede controlar la distribución de tráfico con Europa occidental habilitando el enrutamiento de tráfico basada en prioridad o ponderado dentro de esa región.

![Enrutamiento con distribución de tráfico personalizado en la región del tráfico 'Rendimiento'][8]

## <a name="example-5-per-endpoint-monitoring-settings"></a>Ejemplo 5: Configuración de supervisión de cada extremo

Supongamos que va a usar el Administrador de tráfico para migrar sin problemas el tráfico de un heredado locales sitio web a una nueva versión basada en nube hospedada en Azure. Para el sitio antiguo, que desea usar URI de la página de inicio para supervisar el mantenimiento del sitio. Pero para la nueva versión basada en la nube, se implementa una página de supervisión personalizada (ruta de acceso ' / monitor.aspx') que incluya comprobaciones adicionales.

![Punto final del Administrador de tráfico supervisión (comportamiento predeterminado)][9]

La configuración de supervisión en un perfil de administrador de tráfico se aplica a todos los extremos dentro de un único perfil. Con perfiles anidados, utiliza un perfil secundarios diferentes por sitio para definir la configuración de supervisión diferente.

![Punto final del Administrador de tráfico con la configuración de cada extremo de supervisión][10]

## <a name="faq"></a>Preguntas más frecuentes

### <a name="how-do-i-configure-nested-profiles"></a>¿Cómo se puede configurar perfiles anidados?

Puede configurar un perfil de administrador de tráfico anidadas con el Administrador de recursos de Azure y las API de REST de Azure clásica, cmdlets de PowerShell de Azure y comandos de Azure CLI entre plataformas. También son compatibles con el nuevo portal de Azure. No se admiten en el portal de clásico.

### <a name="how-many-layers-of-nesting-does-traffic-manger-support"></a>¿Soporte técnico cuántos niveles de anidamiento sirve tráfico Manager?

Puede anidar perfiles de hasta 10 niveles. No se permiten 'Bucles'.

### <a name="can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile"></a>¿Puedo combinar otros tipos de extremo con perfiles secundarios anidados, en el mismo perfil de administrador de tráfico?

Sí. No hay ninguna restricción sobre cómo combinar extremos de los diferentes tipos dentro de un perfil.

### <a name="how-does-the-billing-model-apply-for-nested-profiles"></a>¿Cómo se aplica el modelo de facturación para perfiles anidadas?

No hay no negativo precios impacto del uso de perfiles anidados.

Administrador de tráfico facturación tiene dos componentes: comprobaciones de estado de punto final y millones de consultas DNS

- Comprobaciones de estado de extremo: es gratuito para un perfil secundario cuando está configurado como un extremo de un perfil primario. Supervisión de los extremos del perfil secundarios de facturación de la forma habitual.
- Consultas DNS: cada consulta se cuenta como una sola. Una consulta con un perfil primario que devuelve un extremo de un perfil secundarios se cuenta en el perfil de primario solo.

Para obtener más detalles, consulte la [página del Administrador de tráfico de precios](https://azure.microsoft.com/pricing/details/traffic-manager/).

### <a name="is-there-a-performance-impact-for-nested-profiles"></a>¿Hay un impacto en el rendimiento para perfiles anidados?

No. No hay ningún impacto en el rendimiento contraído cuando se utilizan perfiles anidados.

Los servidores de nombres del Administrador de tráfico recorrerán la jerarquía de perfil internamente al procesar cada consulta DNS. Una consulta DNS a un perfil primario puede recibir una respuesta DNS con un punto final de un perfil secundarios. Si está utilizando un único perfil o perfiles anidados, se utiliza un único registro CNAME. No es necesario para crear un registro CNAME para cada perfil en la jerarquía.

### <a name="how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile"></a>¿Cómo calcular tráfico administrador el estado de un extremo anidado en un perfil primario?

El perfil primario no realiza comprobaciones de estado en el elemento secundario directamente. En su lugar, el estado de los extremos del perfil secundarios se usan para calcular el estado general del perfil secundarios. Esta información se propaga la jerarquía de perfil anidadas para determinar el estado del extremo anidado. El perfil primario usa este estado agregado para determinar si el tráfico puede dirigirá al elemento secundario.

La siguiente tabla describe el comportamiento del Administrador de tráfico comprobaciones de estado para un extremo anidado.

|Estado del Monitor de perfiles secundarios|Estado del Monitor del extremo primario|Notas|
|---|---|---|
|Deshabilitado. Se ha deshabilitado el perfil secundarios.|Detener|El estado del extremo primario se detiene, no se ha deshabilitado. El estado de deshabilitado está reservado para que indica que ha deshabilitado el extremo del perfil primario.|
|Degradado. Extremo de perfil al menos un elemento secundario se encuentra en un estado degradado.| En línea: el número de extremos en línea en el perfil de secundarios mínimo es el valor de MinChildEndpoints.<BR>CheckingEndpoint: el número de extremos Online plus CheckingEndpoint en el perfil de secundarios mínimo es el valor de MinChildEndpoints.<BR>Degradado: en caso contrario.|Se enruta el tráfico a un extremo de estado CheckingEndpoint. Si MinChildEndpoints está configurado demasiado alto, el punto final siempre está degradado.|
|En línea. Extremo de perfil al menos un elemento secundario es un estado en línea. Ningún extremo está en el estado de degradado.|Consulte más arriba.||
|CheckingEndpoints. Extremo de perfil al menos un elemento secundario es 'CheckingEndpoint'. Extremos no son 'Online' o 'Degradado'|Igual que el anterior.||
|Inactivo. Todos los extremos de perfil secundario están deshabilitado o detenido o este perfil no tiene ningún extremo.|Detener||


## <a name="next-steps"></a>Pasos siguientes

Más información sobre el [funcionamiento del Administrador de tráfico](traffic-manager-how-traffic-manager-works.md)

Obtenga información sobre cómo [crear un perfil de administrador de tráfico](traffic-manager-manage-profiles.md)

<!--Image references-->
[1]: ./media/traffic-manager-nested-profiles/figure-1.png
[2]: ./media/traffic-manager-nested-profiles/figure-2.png
[3]: ./media/traffic-manager-nested-profiles/figure-3.png
[4]: ./media/traffic-manager-nested-profiles/figure-4.png
[5]: ./media/traffic-manager-nested-profiles/figure-5.png
[6]: ./media/traffic-manager-nested-profiles/figure-6.png
[7]: ./media/traffic-manager-nested-profiles/figure-7.png
[8]: ./media/traffic-manager-nested-profiles/figure-8.png
[9]: ./media/traffic-manager-nested-profiles/figure-9.png
[10]: ./media/traffic-manager-nested-profiles/figure-10.png


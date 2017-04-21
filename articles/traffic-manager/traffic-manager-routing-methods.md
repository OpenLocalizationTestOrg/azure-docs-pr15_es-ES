<properties
    pageTitle="Administrador de tráfico - métodos de enrutamiento de tráfico | Microsoft Azure"
    description="En este artículo le ayudará a comprender los métodos de enrutamiento de tráfico diferentes utilizados por el Administrador de tráfico"
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

# <a name="traffic-manager-traffic-routing-methods"></a>Métodos de enrutamiento de tráfico de tráfico administrador

Administrador de tráfico Azure admite tres métodos de enrutamiento de tráfico para determinar cómo enrutar el tráfico de red a los distintos extremos de servicio. Administrador de tráfico se aplica el método de enrutamiento de tráfico a cada consulta DNS que recibe. El método de enrutamiento de tráfico determina qué extremo devuelto en la respuesta DNS.

La compatibilidad del Administrador de recursos de Azure para el Administrador de tráfico utiliza terminología diferente a la del modelo de implementación clásico. La siguiente tabla muestra las diferencias entre los términos de administrador de recursos y estándar:

| Términos de administrador de recursos | Términos clásica |
|-----------------------|--------------|
| Método de enrutamiento de tráfico | Método de equilibrio de carga |
| Método de prioridad | Método de migración tras error |
| Método ponderada | Método de turnos |
| Método de rendimiento | Método de rendimiento |

En función de los comentarios de los clientes, hemos cambiado la terminología para mejorar la claridad y reducir inconvenientes comunes. No hay ninguna diferencia de funcionalidad.

Existen tres métodos de enrutamiento de tráfico disponibles en el Administrador de tráfico:

- **Prioridad:** Seleccione 'Priority' cuando desee utilizar un extremo de servicio principal para todo el tráfico y proporcionar las copias de seguridad en caso de que el sitio primario o los extremos de una copia de seguridad no están disponibles.
- **Ponderado:** SELECT '' cuando desea distribuir el tráfico a través de un conjunto de puntos finales, ponderada uniformemente o según grosores, que defina.
- **Rendimiento:** Seleccione 'Rendimiento' cuando tiene extremos en diferentes ubicaciones geográficas y quiere que los usuarios finales utilizar el extremo "más próximo" en términos de la latencia de red más bajo.

Todos los perfiles de administrador de tráfico incluyen supervisión de migración tras error de extremos automática y la salud de extremo. Para obtener más información, vea [Supervisión de extremo de administrador de tráfico](traffic-manager-monitoring.md). Un perfil de administrador de tráfico solo puede usar un solo método de enrutamiento de tráfico. Puede seleccionar un método de enrutamiento de tráfico diferente para su perfil en cualquier momento. Los cambios se aplican dentro de un minuto y, a continuación, no se produce ningún tiempo de inactividad. Métodos de enrutamiento de tráfico pueden combinarse mediante perfiles de administrador de tráfico anidada. El anidamiento permite sofisticadas y flexibles configuraciones de enrutamiento de tráfico que cumplen las necesidades de aplicaciones grandes y complejas. Para obtener más información, vea [anidadas perfiles de administrador de tráfico](traffic-manager-nested-profiles.md).

## <a name="priority-traffic-routing-method"></a>Método de enrutamiento de tráfico de prioridad

A menudo una organización desea proporcionar confiabilidad para sus servicios de implementación de uno o más servicios de copia de seguridad en caso de que su servicio principal deja de funcionar. El método de enrutamiento de tráfico 'Priority' permite a los clientes Azure fácilmente implementar este patrón de migración tras error.

![Administrador de tráfico Azure 'Priority' método de enrutamiento de tráfico][1]

El perfil de administrador de tráfico contiene una lista de prioridades de extremos de servicio. De forma predeterminada, el tráfico administrador envía todo el tráfico al extremo (prioridad más alta) principal. Si el extremo principal no está disponible, Administrador de tráfico enruta el tráfico al segundo extremo. Si los extremos primario y secundarios no están disponibles, el tráfico pasa a la tercera y así sucesivamente. Disponibilidad del extremo se basa en el estado configurado (habilitado o deshabilitado) y la supervisión de extremo en curso.

### <a name="configuring-endpoints"></a>Configuración de extremos

Con el Administrador de recursos de Azure, configure la prioridad de extremo explícitamente con la propiedad 'priority' para cada extremo. Esta propiedad es un valor entre 1 y 1000. Los valores inferiores representan una prioridad más alta. Extremos no pueden compartir los valores de prioridad. Establecer la propiedad es opcional. Cuando se omite, se utiliza una prioridad predeterminada basada en el orden de extremo.

Con la interfaz clásica, la prioridad de extremo se configura implícitamente. La prioridad se basa en el orden en que se enumeran los extremos de la definición de perfil.

## <a name="weighted-traffic-routing-method"></a>Método de enrutamiento de tráfico ponderada

El método de enrutamiento de tráfico 'Ponderado' le permite distribuir uniformemente el tráfico o usar una ponderación predefinida.

![Método de enrutamiento de tráfico 'Ponderada' de Azure tráfico administrador][2]

En el método de enrutamiento de tráfico ponderado, asignar un peso a cada extremo en la configuración de perfil de administrador de tráfico. El grosor es un entero de 1 a 1000. Este parámetro es opcional. Si se omite, los jefes de tráfico utiliza un peso predeterminado de '1'.

Para cada consulta DNS recibido, Administrador de tráfico elige aleatoriamente un extremo. La probabilidad de seleccionar un extremo se basa en los pesos asignados a todos los extremos disponibles. Usar el mismo peso a través de todos los resultados de extremos de una distribución de tráfico par. Uso pesos mayores o menores en los extremos específicos hace esos extremos que se devolverán más o menos frecuencia en las respuestas DNS.

El método ponderado permite algunos escenarios útiles:

- Actualización de la aplicación gradual: asignar un porcentaje del tráfico para redirigir a un nuevo punto final y a continuación, aumentar gradualmente el tráfico con el tiempo en 100%.
- Migración de aplicaciones para Azure: crear un perfil con extremos de Azure y externos. Ajustar el grosor de los extremos de prefiere los extremos nuevos.
- Separar a la nube para capacidad adicional: expandir rápidamente una implementación local en la nube colocando detrás de un perfil de administrador de tráfico. Cuando necesite capacidad adicional en la nube, puede agregar o habilitar más extremos y especificar qué parte del tráfico que se va a cada extremo.

El nuevo portal de Azure es compatible con la configuración de enrutamiento de tráfico ponderado. No se puede configurar grosores en el portal de clásico. También puede configurar grosores con el Administrador de recursos y las versiones estándar de PowerShell de Azure, CLI y las API de REST.

Es importante comprender que las respuestas de DNS se almacenan en caché por los clientes y los servidores DNS recursiva que utilizan los clientes para resolver nombres DNS. Este almacenamiento en caché puede tener un impacto en distribuciones de tráfico ponderada. Cuando el número de clientes y los servidores DNS recursiva es grande, distribución de tráfico funciona según lo esperado. Sin embargo, cuando el número de clientes o los servidores DNS recursiva es pequeño, el almacenamiento en caché puede inclinar significativamente la distribución de tráfico.

Casos de uso comunes incluyen:

- Entornos de pruebas y desarrollo
- Comunicaciones de aplicación a otra
- Aplicaciones destinadas a una base de usuarios estrecha que comparten una infraestructura DNS recursiva común (por ejemplo, empleados de empresa que se conecta a través de un servidor proxy)

Estos efectos de almacenamiento en caché de DNS son comunes a todos los sistemas de enrutamiento de tráfico de DNS, no solo el Administrador de tráfico de Azure. En algunos casos, explícitamente borrar la caché DNS puede proporcionar una solución. En otros casos, un método de enrutamiento de tráfico alternativo puede resultar más apropiado.

## <a name="performance-traffic-routing-method"></a>Método de enrutamiento de tráfico de rendimiento

Implementar los extremos de dos o más ubicaciones en todo el mundo puede mejorar la capacidad de respuesta de muchas aplicaciones enrutar el tráfico a la ubicación que se cercana a usted. El método de enrutamiento de tráfico 'Rendimiento' proporciona esta capacidad.

![Administrador de tráfico Azure 'Rendimiento' método de enrutamiento de tráfico][3]

El extremo 'más cercano' no es necesariamente más cercano medida distancia geográfica. En su lugar, el método de enrutamiento de tráfico 'Rendimiento' determina el extremo más cercano medir la latencia de red. Administrador de tráfico mantiene una tabla de latencia de Internet para un seguimiento del tiempo de ida y vuelta entre intervalos de direcciones IP y cada centro de datos de Azure.

Administrador de tráfico es la dirección IP de origen de la solicitud entrante de DNS de la tabla de latencia de Internet. Administrador de tráfico elige un extremo en el centro de datos de Azure que tiene la menor latencia para ese intervalo de direcciones IP, a continuación, devuelve el extremo de la respuesta DNS.

Como se explica en [Funcionamiento del Administrador de tráfico](traffic-manager-how-traffic-manager-works.md), Administrador de tráfico no recibe consultas DNS directamente desde clientes. En su lugar, las consultas DNS proceden del servicio DNS recursiva que los clientes están configurados para usar. Por lo tanto, la dirección IP se utiliza para determinar el extremo 'más cercano' no tiene la dirección IP del cliente, pero es la dirección IP del servicio DNS recursiva. En la práctica, esta dirección IP es un bueno proxy para el cliente.

Administrador de tráfico actualiza periódicamente la tabla de latencia de Internet para tener en cuenta los cambios en Internet global y nuevas áreas de Azure. Sin embargo, rendimiento de la aplicación varía según las variaciones en tiempo real de carga a través de Internet. Enrutamiento de tráfico de rendimiento no supervisar la carga de un extremo de servicio determinado. Sin embargo, si un punto final está disponible, el Administrador de tráfico no lo hace en respuestas a consultas DNS.

Puntos que tenga en cuenta:

- Si su perfil contiene varios extremos de la misma región de Azure, a continuación, Administrador de tráfico distribuye el tráfico uniformemente entre los extremos disponibles en dicha región. Si prefiere una distribución de tráfico diferentes dentro de una región, puede utilizar [anidadas de tráfico de administrador de perfiles](traffic-manager-nested-profiles.md).

- Si todos los extremos habilitados en una región determinada Azure están degradados, Administrador de tráfico distribuye el tráfico a través de todos los otros extremos disponibles en lugar de en el extremo más cercano siguiente. Esta lógica impide que se produzcan no sobrecarga el extremo más cercano siguiente un error en cascada. Si desea definir una secuencia preferido migración tras error, use [anidadas perfiles de administrador de tráfico](traffic-manager-nested-profiles.md).

- Al usar el método de enrutamiento de tráfico de rendimiento con extremos externos o extremos anidados, debe especificar la ubicación de los extremos. Elija la región de Azure más cercana a su implementación. Esas ubicaciones son los valores compatibles con la tabla de latencia de Internet.

- El algoritmo que elige el extremo es determinante. Consultas DNS repetidas desde el mismo cliente se dirigen al mismo extremo. Normalmente, los clientes utilizan recursiva diferentes los servidores DNS cuando se desplazan. El cliente se puede dirigir a un extremo diferente. Enrutamiento también puede verse afectado por las actualizaciones de la tabla de latencia de Internet. Por lo tanto, el método de enrutamiento de tráfico de rendimiento no garantiza que un cliente siempre se dirige al mismo extremo.

- Cuando se cambia la tabla de latencia de Internet, quizás observe que algunos clientes se dirigen a un extremo diferente. Este cambio de enrutamiento es más preciso basándose en los datos de latencia actual. Estas actualizaciones son esenciales para mantener la precisión del enrutamiento de tráfico de rendimiento como Internet continuamente evoluciona.

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre cómo desarrollar aplicaciones de alta disponibilidad mediante la [supervisión de extremo de tráfico de administrador](traffic-manager-monitoring.md)

Obtenga información sobre cómo [crear un perfil de administrador de tráfico](traffic-manager-manage-profiles.md)

<!--Image references-->
[1]: ./media/traffic-manager-routing-methods/priority.png
[2]: ./media/traffic-manager-routing-methods/weighted.png
[3]: ./media/traffic-manager-routing-methods/performance.png

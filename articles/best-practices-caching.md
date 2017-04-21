<properties
   pageTitle="Almacenamiento en caché instrucciones | Microsoft Azure"
   description="Guía de almacenamiento en caché para mejorar el rendimiento y escalabilidad."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/14/2016"
   ms.author="masashin"/>


# <a name="caching-guidance"></a>Guía de almacenamiento en caché

[AZURE.INCLUDE [pnp-header](../includes/guidance-pnp-header-include.md)]

Almacenamiento en caché es una técnica común que pretende mejorar el rendimiento y escalabilidad de un sistema. Para ello, copiando temporalmente datos frecuente al almacenamiento rápido que se encuentra Cerrar para la aplicación. Si este almacenamiento de datos rápido está más cerca a la aplicación que el origen original, a continuación, almacenamiento en caché puede mejorar significativamente los tiempos de respuesta para las aplicaciones cliente que sirven para los datos más rápidamente.

Almacenamiento en caché es más eficaz cuando una instancia de cliente lee repetidamente los mismos datos, especialmente si todas las condiciones siguientes se aplican al almacén de datos original:
- Permanece relativamente estática.
- Es lenta en comparación con la velocidad de la memoria caché.
- Está sujeta a un alto nivel de conflicto.
- Está lejos de latencia de red puede provocar que access es lenta.

## <a name="caching-in-distributed-applications"></a>Almacenamiento en caché en aplicaciones distribuidas

Aplicaciones distribuidas normalmente implementan una o ambas de las siguientes estrategias al almacenamiento en caché de datos:

- Uso de una caché privada, donde los datos están guardados localmente en el equipo que ejecute una instancia de una aplicación o servicio.
- Usar una caché compartida, actuando como una fuente común que puede tener acceso a varios procesos o equipos.

En ambos casos, almacenamiento en caché puede realizarse de cliente y del servidor. Almacenamiento en caché del cliente se realiza el proceso que proporciona la interfaz de usuario de un sistema, como un explorador web o una aplicación de escritorio.
Almacenamiento en caché del servidor se realiza el proceso que proporciona los servicios de empresa que se estén ejecutando de forma remota.

### <a name="private-caching"></a>El almacenamiento en caché privada

El tipo más básico de caché es un almacén en memoria. Tiene mantenidos en el espacio de direcciones de un único proceso y acceso directamente por el código que se ejecuta en ese proceso. Es muy rápido para acceder a este tipo de caché. También puede proporcionar un medio muy eficaces para almacenar pequeña cantidades de datos estáticos, ya que el tamaño de una caché está restringido por el volumen de memoria que está disponible en el equipo que aloja el proceso.

Si necesita más información de que es posible física en la memoria en caché, puede escribir datos en caché para el sistema de archivos local. Será más lento tener acceso a los datos que se almacenan en memoria, pero deben seguir siendo más rápido y confiable que la recuperación de datos a través de una red.

Si tiene varias instancias de una aplicación que usa este modelo ejecutándose a la vez, cada instancia de aplicación tiene su propia caché independiente mantiene su propia copia de los datos.

Piense en una caché como una instantánea de los datos originales en algún momento en el pasado. Si estos datos no están estáticos, es probable que instancias de aplicación diferente contienen diferentes versiones de los datos en su caché. Por lo tanto, la misma consulta realizada por estas instancias puede devolver resultados diferentes, como se muestra en la figura 1.

![Usar una caché en memoria en distintas instancias de una aplicación](media/best-practices-caching/Figure1.png)

_Figura 1: Usar una caché en memoria en distintas instancias de una aplicación_

### <a name="shared-caching"></a>Compartir el almacenamiento en caché

Una caché compartida puede ayudar a solucionar problemas que pueden diferir datos en caché, que puede ocurrir con en la memoria caché. Caché compartida garantiza que instancias de aplicación diferente ven la misma vista de datos en caché. Para ello, localizar la caché en una ubicación diferente, normalmente hospedado como parte de un servicio independiente, tal como se muestra en la figura 2.

![Usar una caché compartida](media/best-practices-caching/Figure2.png)

_Figura 2: Uso de una caché compartida_

Una ventaja importante del almacenamiento en caché enfoque compartido es la escalabilidad proporciona. Muchos de los servicios de caché compartida se implementan mediante un clúster de servidores y utilizan software que distribuye los datos en el clúster de manera transparente. Una instancia de aplicación simplemente envía una solicitud al servicio de caché.
La infraestructura subyacente es responsable de determinar la ubicación de los datos almacenados en caché en el clúster. Puede escalar fácilmente la caché agregando más servidores.

Hay dos principales desventajas del enfoque de almacenamiento en caché compartida:
- La caché es más lenta acceso a porque ya no se mantiene localmente a cada instancia de la aplicación.
- El requisito de implementar un servicio de caché independiente puede agregar complejidad a la solución.

## <a name="considerations-for-using-caching"></a>Consideraciones sobre el uso de almacenamiento en caché

Las secciones siguientes describen con más detalle las consideraciones para diseñar y utilizar una caché.

### <a name="decide-when-to-cache-data"></a>Decidir cuándo datos en caché

Almacenamiento en caché puede mejorar considerablemente disponibilidad, rendimiento y escalabilidad. Los datos más que tiene y cuanto mayor sea el número de usuarios que necesitan tener acceso a estos datos, la mayores de las ventajas de almacenamiento en caché se convierten en. Eso es porque almacenamiento en caché reduce la latencia y conflictos que está asociada con el manejo de grandes volúmenes de solicitudes simultáneas en el almacén de datos original.

Por ejemplo, una base de datos puede admitir un número limitado de conexiones simultáneas. Recuperar datos de una caché compartida, sin embargo, en lugar de la base de datos, es posible para una aplicación de cliente tener acceso a estos datos, incluso si actualmente se agota el número de conexiones disponibles. Además, si la base de datos no está disponible, las aplicaciones cliente podrían que pueda seguir usando los datos que se almacenan en la memoria caché.

Considere la posibilidad de almacenamiento en caché de datos que se leerá frecuentemente pero modificados con poca frecuencia (por ejemplo, los datos que tiene una proporción de las operaciones de lectura de las operaciones de escritura). Sin embargo, no se recomienda que usa la memoria caché como el almacén de información crítica relevantes. En su lugar, asegúrese de que todos los cambios que no desea perder la aplicación siempre se guardan en un almacén de datos persistente. Esto significa que si la caché no está disponible, la aplicación puede seguir funcionando con el almacén de datos y no perder información importante.

### <a name="determine-how-to-cache-data-effectively"></a>Determinar el modo caché de datos eficaz

La clave para usar una caché de forma eficaz se encuentra en la determinación de los datos más adecuados para la memoria caché y caché en el momento adecuado. Los datos se pueden agregar a la caché a petición la primera vez que se recuperen por una aplicación. Esto significa que la aplicación necesita tener acceso a los datos de una sola vez desde el almacén de datos y que el acceso posterior se puede satisfacer mediante el uso de la memoria caché.

Como alternativa, una caché puede o parcialmente rellenarse con datos por adelantado, normalmente al iniciar la aplicación (es decir, un método conocido como inicialización). Sin embargo, no puede ser conveniente para implementar el inicio para una caché grande porque este enfoque puede imponer una carga imprevista y alto en el almacén de datos original cuando la aplicación se inicia.

A menudo un análisis de patrones de uso puede ayudarle a decidir si completa o parcial rellenar una caché con y elegir los datos en caché. Por ejemplo, puede ser útil para iniciar la memoria caché con los datos de perfil de usuario estático para los clientes que utilizan la aplicación con regularidad (tal vez todos los días), pero no para los clientes que utilizan la aplicación solo una vez por semana.

Almacenamiento en caché normalmente funciona bien con datos inmutable o que cambian con frecuencia. Ejemplos incluyen información de referencia como producto e información de precio en una aplicación de comercio electrónico o recursos compartidos de estáticos que son costos de construcción. Todos o algunos de estos datos se pueden cargar en la caché de inicio de la aplicación para minimizar la demanda de recursos y para mejorar el rendimiento. También puede resultar apropiado tener un proceso de fondo que actualiza periódicamente la referencia de datos en caché para garantizar que están actualizados o que actualiza la caché cuando hacen referencia a datos cambios.

Almacenamiento en caché es tan útil para datos dinámicos, aunque existen algunas excepciones a esta cuenta (consulte la sección datos muy dinámicos de caché más adelante en este artículo para obtener más información). Cuando los datos originales cambian con frecuencia, la información almacenada en caché esté obsoleta muy rápidamente tanto la sobrecarga de sincronización de la caché con el almacén de datos original reduce la eficacia de almacenamiento en caché.

Tenga en cuenta que no tiene una caché incluir los datos de una entidad completados. Por ejemplo, si un elemento de datos representa un objeto multivalor como un cliente de banco con un nombre, dirección y saldo de la cuenta, algunos de estos elementos pueden permanecer estática (como el nombre y dirección), mientras que otras personas (por ejemplo, el saldo de la cuenta) podrían ser más dinámicos. En estos casos, puede ser útil para las partes estáticas de los datos en caché y recuperar (o calcular) solo la información restante cuando sea necesario.

Le recomendamos que llevar a cabo análisis de uso y pruebas de rendimiento para determinar si carga población preliminar o a petición de la memoria caché o una combinación de ambos, es adecuada. La decisión debe basarse en la volatilidad y el modelo de uso de los datos. Análisis de uso y rendimiento de caché resulta especialmente importante en las aplicaciones que encuentran cargas y deben ser muy scalable. Por ejemplo, en escenarios de gran escalabilidad podría sentido inicialización de la caché para reducir la carga en el almacén de datos en momentos.

Almacenamiento en caché también puede usarse para evitar la repetición de cálculos mientras se ejecuta la aplicación. Si una operación de las transformaciones de datos o realiza un cálculo complicado, pueden guardar los resultados de la operación en la memoria caché. Si el mismo cálculo es necesario más adelante, la aplicación simplemente puede recuperar los resultados de la memoria caché.

Una aplicación puede modificar los datos que se almacenan en la memoria caché. Sin embargo, se recomienda pensar en la memoria caché como un almacén de datos transitorias que podría desaparecer en cualquier momento. No almacenar datos importantes en la memoria caché. Asegúrese de mantener la información en el almacén de datos original también. Esto significa que si la caché no está disponible, minimizar la posibilidad de perder los datos.

### <a name="cache-highly-dynamic-data"></a>Caché de datos muy dinámicos

Cuando almacena información cambian rápidamente en un almacén de datos persistente, puede imponer una carga en el sistema. Por ejemplo, considere la posibilidad de un dispositivo que continuamente informes de estado o alguna otra medida. Si elige una aplicación no en caché estos datos en la base de la información almacenada en caché casi siempre será obsoleta, la misma consideración podría ser verdadera al almacenar y recuperar esta información desde el almacén de datos. En el tiempo que se tarda en Guardar y recuperar estos datos, puede haber cambiado.

En una situación como esta, tenga en cuenta las ventajas de almacenar la información dinámica directamente en la caché en lugar de en el almacén de datos persistente. Si los datos no crítica y no requiere la auditoría, no importa si se pierde el cambio ocasional.

### <a name="manage-data-expiration-in-a-cache"></a>Administrar la expiración de los datos en caché

En la mayoría de los casos, datos que se almacenan en la memoria caché están una copia de los datos que se encuentran en el almacén de datos original. Pueden cambiar los datos en el almacén de datos original después de que se caché, causando los datos almacenados en caché queden obsoletos. Muchos sistemas de almacenamiento en caché le permiten configurar la caché para caducar datos y reducir el período que se pueden datos obsoletos.

Cuando los datos almacenados en caché expira, se quita de la memoria caché y la aplicación debe recuperar los datos del almacén de datos original (puede colocar la información recopilada recién atrás en caché). Puede establecer una directiva de expiración predeterminada al configurar la memoria caché. En muchos de los servicios de caché, también puede establecer el período de expiración de los objetos individuales cuando almacenó mediante programación en la caché.
Algunas caché le permiten especificar el período de expiración como un valor absoluto o como un valor de variable que hace que el elemento se quita de la caché si no se accede dentro del tiempo especificado. Esta opción reemplaza cualquier directiva de expiración de toda la caché, pero sólo de los objetos especificados.

> [AZURE.NOTE] Tenga en cuenta el período de expiración de la memoria caché y los objetos que contiene con cuidado. Si lo hace demasiado corto, objetos caducará demasiado rápido y reducirá las ventajas del uso de la memoria caché. Si realiza el período demasiado largo, corre el riesgo los datos de convertirse en obsoletos.

También es posible que la memoria caché podría rellenar hacia arriba, si se permiten que los datos siguen estando residentes por mucho tiempo. En este caso, las solicitudes para agregar nuevos elementos a la caché pueden provocar que algunos elementos forzar quitarse de un proceso conocido como expulsión. Servicios de caché normalmente expulsar datos usados menos recientemente (LRU), pero normalmente puede anular esta directiva y evitar que los elementos que va a expulsar. Sin embargo, si adopta este enfoque, corre el riesgo sobrepasan la memoria disponible en la memoria caché. Se producirá un error en una aplicación que intenta agregar un elemento a la caché con una excepción.

Algunas implementaciones de almacenamiento en caché pueden proporcionar directivas de expulsión adicionales. Existen varios tipos de directivas de expulsión. Se incluyen:
- Directiva usados recientemente (en las expectativas que los datos no será necesarios volver a).
- Una directiva de first in first out (primero se extrae los datos más antiguos).
- Una directiva de eliminación explícitas basada en un evento desencadenado (por ejemplo, los datos que se modifican).

### <a name="invalidate-data-in-a-client-side-cache"></a>Invalidar los datos en una caché de cliente

Datos que se almacenan en una caché de cliente generalmente se consideran fuera los auspicios del servicio que proporciona los datos del cliente. Un servicio no puede forzar directamente un cliente para agregar o quitar la información de la memoria caché de cliente.

Esto significa que es posible para un cliente que utiliza una caché mal configurada para continuar con información obsoleta. Por ejemplo, si no se ha implementado correctamente las directivas de expiración de la memoria caché, un cliente puede utilizar información obsoleta que se almacena localmente cuando ha cambiado la información de origen de datos.

Si está creando una aplicación web que sirve de datos en una conexión HTTP, puede forzar implícitamente un cliente de web (por ejemplo, un explorador o un proxy web) para obtener la información más reciente. Puede hacerlo si se actualiza un recurso por un cambio en el URI de ese recurso. Clientes Web suele usar el URI de un recurso como clave en la caché del cliente, para si cambia el URI, pasa por alto el cliente web cualquiera anteriormente caché versiones de un recurso y recupera la nueva versión en su lugar.

## <a name="managing-concurrency-in-a-cache"></a>Administrar la simultaneidad en caché

Caché a menudo están diseñadas para compartirse entre varias instancias de una aplicación. Cada instancia de la aplicación puede leer y modificar los datos de la memoria caché. Por lo tanto, los mismos problemas de simultaneidad que surgen con cualquier almacén de datos compartido también se aplicarán a una caché. En una situación donde se necesita una aplicación para modificar los datos que se almacenan en la caché, debe asegurarse de que las actualizaciones realizadas por una instancia de la aplicación no sobrescribirán los cambios realizados por otra instancia.

Dependiendo de la naturaleza de los datos y la probabilidad de conflictos, puede adoptar una de dos maneras de simultaneidad:

- __Optimista.__ Inmediatamente antes de actualizar los datos, la aplicación comprueba si los datos de la caché ha cambiado desde que se ha recuperado. Si los datos sean siendo el mismo, se puede realizar el cambio. En caso contrario, la aplicación tiene que decidir si desea actualizar. (La lógica empresarial que unidades esta decisión será específica de la aplicación). Este enfoque es adecuado para las situaciones donde son frecuentes actualizaciones o donde están probable que se produzcan conflictos.
- __Pesimista.__ Cuando recupera los datos, en la aplicación se bloquea en la caché para impedir que lo cambie otra instancia. Este proceso garantiza que no se produzcan conflictos, pero también pueden bloquear otras instancias que necesitan para procesar los mismos datos. Simultaneidad pesimista se recomienda únicamente para las operaciones de corta duración y puede afectar a la escalabilidad de una solución. Este enfoque podría ser apropiado para situaciones donde conflictos están más probable que, sobre todo si actualiza varios elementos en la caché de una aplicación y debe asegurarse de que estos cambios se aplican de forma coherente.

### <a name="implement-high-availability-and-scalability-and-improve-performance"></a>Implementar alta disponibilidad y escalabilidad y mejorar el rendimiento

Evitar el uso de una caché como repositorio principal de datos. Esta es la función del almacén de datos original desde el que se rellena la memoria caché. El almacén de datos original es responsable de garantizar la conservación de los datos.

Tenga cuidado de no introducir dependencias críticas en la disponibilidad de un servicio de caché compartida en sus soluciones. Una aplicación debería poder siga funcionando si el servicio que proporciona la caché compartida no está disponible. La aplicación no debe bloquearse o un error de tiempo de espera para que reanudar el servicio de caché.

Por lo tanto, la aplicación debe estar preparada para detectar la disponibilidad del servicio de caché y retroceder al almacén de datos original si la caché está inaccesible. La [trama disyuntor](http://msdn.microsoft.com/library/dn589784.aspx) es útil para controlar este escenario. Se puede recuperar el servicio que proporciona la memoria caché y, a continuación, una vez que esté disponible, puede volver a llenar la memoria caché cuando se leen datos el almacén de datos original, siguiendo una estrategia como la [trama de caché separada](http://msdn.microsoft.com/library/dn589799.aspx)del formulario.

Sin embargo, puede haber un impacto escalabilidad en el sistema si la aplicación pasa al almacén de datos original cuando la caché no está disponible temporalmente.
Mientras se está recuperando el almacén de datos, el almacén de datos original podría ser inundado de solicitudes de datos, por lo que los tiempos de espera y no pudo conexiones.

Considere la posibilidad de implementar una caché local privada en cada instancia de una aplicación, junto con la caché compartida que tienen acceso todas las instancias de aplicación. Cuando la aplicación recupera un elemento, puede comprobar primero en su memoria caché local y luego en compartido en caché y, por último en los datos originales almacenar. La memoria caché local se puede rellenar con los datos de la caché de compartido o en la base de datos si la memoria caché compartida no está disponible.

Este enfoque requiere configuración cuidadosa para evitar que la memoria caché local obsoletos con respecto a la memoria caché compartida. Sin embargo, la memoria caché local actúa como búfer si la memoria caché compartida no es accesible. Figura 3 muestra la estructura.

![Usar una caché local privada con una caché compartida](media/best-practices-caching/Caching3.png)
_figura 3: usar una caché local privada con una caché compartida_

Para admitir grandes en caché que contienen datos duración relativamente larga, algunos servicios de caché ofrecen una opción de alta disponibilidad que implementa migración tras error automática si no está disponible la memoria caché. Este enfoque normalmente implica la replicación de los datos almacenados en caché que se almacenan en un servidor de caché principal en un servidor de caché secundaria y, a continuación, se perderá el cambio en el servidor secundario si se produce un error en el servidor principal o conectividad.

Para reducir la latencia asociada con la escritura a varios destinatarios, puede producirse de forma asincrónica la replicación al servidor secundario cuando se escriben datos en la memoria caché en el servidor principal. Este método lleva a la posibilidad de que algunos información almacenada en caché pueden perder si se produce un error, pero la proporción de estos datos debería pequeña en comparación con el tamaño total de la memoria caché.

Si una caché compartida es grande, puede ser útil dividir los datos en caché en nodos para reducir las probabilidades de conflictos y mejorar la escalabilidad. Muchos caché compartidas admiten la posibilidad de agregar dinámicamente (y quitar) nodos y equilibrar los datos en todas las particiones. Este enfoque puede implicar clústeres, en el que se presenta la colección de nodos a aplicaciones cliente como caché transparente, solo. Internamente, sin embargo, los datos se reparte entre los nodos siguiendo una estrategia de distribución predefinido que equilibra la carga uniformemente. El [documento de orientación de particiones de datos](http://msdn.microsoft.com/library/dn589795.aspx) en el sitio Web de Microsoft proporciona más información sobre posibles estrategias de partición.

Clústeres, también pueden aumentar la disponibilidad de la memoria caché. Si se produce un error en un nodo, el resto de la memoria caché está todavía accesible.
Clústeres con frecuencia se usan junto con la replicación y migración tras error. Se pueden replicar cada nodo y la réplica rápidamente colocarse en línea si se produce un error en el nodo.

Muchas leer y las operaciones de escritura están probable que implican objetos o valores de datos únicos. Sin embargo, a veces puede ser necesario almacenar o recuperar grandes volúmenes de datos rápidamente.
Por ejemplo, inicialización una caché puede implicar escribir cientos o miles de elementos en la memoria caché. Una aplicación también podría necesitar recuperar una gran cantidad de elementos relacionados de la memoria caché como parte de la misma solicitud.

Caché a gran escala muchos proporcionan operaciones por lotes para estos fines. Esto permite una aplicación de cliente en un paquete un gran volumen de elementos en una única solicitud y reduce la sobrecarga asociada con la realización de una gran cantidad de solicitudes de pequeñas.

## <a name="caching-and-eventual-consistency"></a>Coherencia de almacenamiento en caché y final

Para que el patrón de caché separada para que funcione, la instancia de la aplicación que rellena la caché debe tener acceso a la versión más reciente y coherente de los datos. En un sistema que implementa la coherencia (por ejemplo, un almacén de datos duplicados) no puede suceder.

Una instancia de una aplicación puede modificar un elemento de datos e invalidar la versión en caché de ese elemento. Otra instancia de la aplicación puede intentar leer este elemento de la memoria caché, lo que provoca a un error de caché, por lo que lee los datos del almacén de datos y agrega a la caché. Sin embargo, si el almacén de datos no se sincronizó totalmente con las demás réplicas, la instancia de la aplicación puede leer y llenar la caché con el valor antiguo.

Para obtener más información sobre cómo controlar la coherencia de los datos, consulte la página de [información básica de coherencia de datos](http://msdn.microsoft.com/library/dn589800.aspx) en el sitio Web de Microsoft.

### <a name="protect-cached-data"></a>Proteger los datos almacenados en caché

Con independencia de servicio de caché usar, considere la posibilidad de cómo proteger los datos que se almacena en la caché de acceso no autorizado. Hay dos aspectos principales:

- La declaración de privacidad de los datos en la caché.
- La declaración de privacidad de los datos tal y como fluye entre la caché y la aplicación que está utilizando la caché

Para proteger los datos en caché, el servicio de caché podría implementar un mecanismo de autenticación que requiere que aplicaciones especifican lo siguiente:
- Las identidades pueden tener acceso a datos en la memoria caché.
- Qué operaciones (lectura y escritura) que pueden realizar estas identidades.

Para reducir la sobrecarga está asociada a leer y escribir datos, después una identidad ha concedido permisos de escritura o el acceso de lectura a la memoria caché, que identidad puede utilizar los datos en la memoria caché.

Si necesita restringir el acceso a los subconjuntos de los datos almacenados en caché, puede realizar uno de estos procedimientos:

- Dividir la memoria caché en particiones (mediante servidores de caché diferente) y sólo conceder acceso a las identidades para las particiones que puedan usar.
- Cifrar los datos de cada subconjunto mediante las teclas diferentes y proporcionar las claves de cifrado solo a las identidades que deben tener acceso a cada subconjunto. Una aplicación de cliente aún podrá recuperar todos los datos en la memoria caché, pero solo podrá descifrar los datos para el que tiene las teclas.

También debe proteger los datos como pase dentro y fuera de la memoria caché. Para ello, dependen de las características de seguridad proporcionadas por la infraestructura de red que usan las aplicaciones cliente para conectarse a la memoria caché. Si la caché se implementa mediante un servidor en el sitio de la misma organización que hospeda las aplicaciones cliente, a continuación, el aislamiento de la red podría no requieren que realizar pasos adicionales. Si la caché se encuentra de forma remota y requiere una conexión TCP o HTTP en una red pública (como Internet), considere la posibilidad de implementar SSL.

## <a name="considerations-for-implementing-caching-with-microsoft-azure"></a>Consideraciones para la implementación de almacenamiento en caché con Microsoft Azure

Azure proporciona la memoria caché Redis de Azure. Se trata de una implementación de código abierto caché Redis que se ejecuta como un servicio en un centro de datos de Azure. Proporciona un servicio de caché que puede tener acceso desde cualquier aplicación de Azure, si la aplicación se implementa como un servicio de nube, un sitio Web, o dentro de una máquina virtual Azure. En caché se pueden compartir aplicaciones cliente que tienen la clave de acceso adecuado.

Caché de Azure Redis es una solución de almacenamiento en caché de alto rendimiento que proporciona disponibilidad, escalabilidad y seguridad. Normalmente se ejecuta como servicio extendido por uno o varios equipos dedicados. Intenta almacenar toda la información como sea posible en la memoria para asegurar un acceso rápido. Esta arquitectura está pensada para proporcionar baja latencia y alto rendimiento reduciendo la necesidad de realizar operaciones de i/OS lentas.

 Caché de Azure Redis es compatible con muchas de las distintas API que se usan las aplicaciones cliente. Si tiene aplicaciones existentes que ya utilizan Azure Redis caché local, la caché de Azure Redis proporciona una ruta de migración rápida al almacenamiento en caché en la nube.

> [AZURE.NOTE] Azure también proporciona el servicio de caché administrada. Este servicio se basa en el motor de caché de tela de servicio de Azure. Le permite crear una caché distribuida que se puede compartir con las aplicaciones de acoplamiento flexible. La caché se hospeda en servidores de alto rendimiento en un centro de datos de Azure.
Sin embargo, esta opción ya no se recomienda y solo se proporciona compatibilidad con aplicaciones existentes que se han creado para usarlo. Programar, utilice Azure Redis caché.
>
> Además, Azure admite en función de almacenamiento en caché. Esta característica le permite crear una caché específica de un servicio de nube.
La caché hospedada en instancias de una función web o de trabajo y funciones que funcionan como parte de la misma unidad de implementación de servicio de nube solo puede tener acceso. (Una unidad de implementación es el conjunto de instancias de la función que se implementan como un servicio de nube a un área específica). La caché está agrupada y, a continuación, todas las instancias de la función dentro de la misma unidad de implementación que hospeda la caché formarán parte del mismo clúster de caché. Sin embargo, esta opción ya no se recomienda y solo se proporciona compatibilidad con aplicaciones existentes que se han creado para usarlo. Programar, utilice Azure Redis caché.
>
> Servicio de caché administrada de Azure y Azure en la función de caché actualmente están preparada para jubilación en 16 de noviembre de 2016.
Se recomienda migrar a Azure Redis caché en preparación para esta retirada. Para obtener más información, visite la página   [¿Qué es la oferta de Azure Redis caché y qué tamaño debo usar?](redis-cache/cache-faq.md#what-redis-cache-offering-and-size-should-i-use) en el sitio Web de Microsoft.


### <a name="features-of-redis"></a>Características de Redis

 Redis tiene más de un servidor de caché simple. Proporciona una base de datos en memoria distribuida con un conjunto de comandos amplio compatible con muchos escenarios comunes. Se describen más adelante en este documento, en la sección uso Redis almacenamiento en caché. Esta sección resume algunas de las características clave que Redis proporciona.

### <a name="redis-as-an-in-memory-database"></a>Redis como una base de datos en memoria

Redis admite ambas operaciones lectura y escritura. En Redis, escribe puede protegerse de error del sistema, ya sea por no pueden almacenarse periódicamente en un archivo de instantánea local o en un archivo de registro solo anexar. No es el caso en muchas caché (que debe considerar almacena datos transitoria).

 Escribe todos está asincrónica y no bloquea a los clientes de leer y escribir datos. Redis cuando comienza a ejecutar, lee los datos desde el archivo de registro o instantánea y se usa para crear la memoria caché. Para obtener más información, vea [Redis persistencia](http://redis.io/topics/persistence) en el sitio Web Redis.

> [AZURE.NOTE] Redis no garantiza que escribe todos se guardarán en el caso de un error grave, pero en el peor podrían perder solo unos pocos segundos de datos. Recuerde que una caché no está pensada para que actúe como un origen de datos relevantes, y es responsabilidad de las aplicaciones de uso de la caché para asegurarse de que los datos críticos se guardan correctamente en un almacén de datos correspondiente. Para obtener más información, vea el [patrón de caché separada](http://msdn.microsoft.com/library/dn589799.aspx).

#### <a name="redis-data-types"></a>Redis tipos de datos

Redis es un almacén de clave y valor, donde pueden contener valores tipos simples o estructuras de datos complejos como hash, listas y se establece. Es compatible con un conjunto de operaciones atómicas en estos tipos de datos. Claves pueden ser permanente o etiquetados con un número limitado tiempo de vida, momento en que la clave y su valor correspondiente se eliminan automáticamente de la memoria caché. Para obtener más información sobre claves Redis y valores, visite la página [Introducción a Redis abstracciones y tipos de datos](http://redis.io/topics/data-types-intro) en el sitio Web Redis.

#### <a name="redis-replication-and-clustering"></a>Redis duplicación y clústeres

Redis admite la replicación de maestro y subordinado para ayudar a asegurar la disponibilidad y mantener el rendimiento. Escribir las operaciones a un nodo maestro Redis se duplican a uno o varios de los nodos subordinados. Operaciones de lectura pueden representarse mediante el patrón o cualquiera de las formas subordinadas.

En caso de una partición de red, pueden continuar subordinadas sirva a datos y, a continuación, transparente volver a sincronizar con el patrón cuando se restablece la conexión. Para obtener más detalles, visite la página de [replicación](http://redis.io/topics/replication) en el sitio Web Redis.

Redis también proporciona clústeres, que le permite dividir datos en shards entre servidores transparente y distribuir la carga. Esta característica mejora la escalabilidad, ya que se pueden agregar nuevos servidores Redis y aumentan los datos dividir como el tamaño de la caché.

Además, se puede replicar cada servidor en el clúster mediante la replicación de maestro y subordinado. Esto asegura disponibilidad en cada nodo del clúster. Para obtener más información acerca del clúster y sharding, visite el [Redis página tutorial de clúster](http://redis.io/topics/cluster-tutorial) en el sitio Web Redis.

### <a name="redis-memory-use"></a>Redis uso de la memoria

Una caché Redis tiene un tamaño finito que depende de los recursos disponibles en el equipo host. Cuando configura un servidor Redis, puede especificar la cantidad máxima de memoria que puede utilizar. También puede configurar una clave en una caché Redis tener una fecha de caducidad después de que se elimina automáticamente de la memoria caché. Esta característica puede ayudar a impedir que se rellena la tabla con los datos antiguos o desfasados la memoria caché.

Como memoria llena, Redis puede automáticamente expulsar claves y sus valores siguiendo un número de directivas de. El valor predeterminado es LRU (usado menos recientemente), pero también puede seleccionar otras directivas como expulsar claves al azar o desactivar totalmente expulsión (en qué, intentos de mayúsculas y minúsculas para agregar elementos a la caché de éxito si está lleno). La página [Usando Redis como una caché LRU](http://redis.io/topics/lru-cache) proporciona más información.

### <a name="redis-transactions-and-batches"></a>Redis transacciones y lotes

Redis permite a una aplicación de cliente enviar una serie de operaciones de leer y escribir datos en la memoria caché como una transacción atómica. Se garantiza que todos los comandos de la transacción ejecutar secuencialmente y no hay comandos de otros clientes simultáneas se le integrados entre ellas.

Sin embargo, no son las transacciones true como una base de datos relacional podría realizarlas. Procesamiento de transacciones consta de dos fases: el primero es cuando se ponen en cola los comandos y el segundo es cuando se ejecutan los comandos. Durante la fase de cola de comando, se envían los comandos que forman parte de la transacción por el cliente. Si se produce algún tipo de error en este momento (por ejemplo, un error de sintaxis o un número incorrecto de parámetros) Redis luego rechaza procesar toda la transacción y lo descarta.

Durante la fase de ejecución Redis realiza cada comando en cola en secuencia. Si se produce un error en un comando durante esta fase, Redis continúa con el siguiente comando en cola y deshacer los efectos de los comandos que ya se ha ejecutado. Este tipo de transacción simplificado ayuda a mantener el rendimiento y evitar problemas de rendimiento causados por conflictos.

Redis implementar un formulario de bloqueo optimista para ayudar a mantener la coherencia. Para obtener información detallada sobre las transacciones y el bloqueo con Redis, visite la [página de transacciones](http://redis.io/topics/transactions) en el sitio Web Redis.

Redis también es compatible con no transacciones por lotes de solicitudes. El protocolo Redis que utilizan los clientes para enviar comandos a un servidor Redis permite a un cliente enviar una serie de operaciones como parte de la misma solicitud. Esto puede ayudar a reducir la fragmentación de paquetes en la red. Cuando se procesa el lote, se realiza cada comando. Si cualquiera de estos comandos son incorrecto, será rechazados (que no ocurre con una transacción), pero se realizará el resto de los comandos. No hay ninguna garantía sobre el orden en que se procesará los comandos del lote.

### <a name="redis-security"></a>Redis seguridad

Redis centrados exclusivamente en proporcionar acceso rápido a los datos y a continuación, está diseñado para ejecutarse dentro de un entorno de confianza que se puede acceder sólo por los clientes de confianza. Redis es compatible con un modelo de seguridad limitado basado en autenticación de contraseña. (Es posible quitar autenticación por completo, aunque no se recomienda esto.)

Todos los clientes autenticados compartan la misma contraseña global y tengan acceso a los mismos recursos. Si necesita más completa de inicio de sesión de seguridad, debe implementar su propio nivel de seguridad delante del servidor Redis y todas las solicitudes de cliente deben pasar a través de este nivel adicional. Redis debe no se expone directamente a los clientes de confianza o no autenticados.

Puede restringir el acceso a los comandos deshabilitando ellos o cambiar de nombre (y para mostrar solo los clientes con privilegios con los nuevos nombres).

Redis no admite directamente cualquier forma de cifrado de datos, por lo que toda la codificación debe realizarse por aplicaciones cliente. Además, Redis no proporciona ninguna forma de seguridad de transporte. Si necesita proteger los datos pase a través de la red, se recomienda implementar a un servidor proxy de SSL.

Para obtener más información, visite la página [Redis seguridad](http://redis.io/topics/security) en el sitio Web Redis.

> [AZURE.NOTE] Caché de Redis Azure proporciona su propia capa de seguridad a través del cual se conectan los clientes. Los servidores Redis subyacentes no se exponen a la red pública.

### <a name="using-the-azure-redis-cache"></a>Uso de la caché de Azure Redis

La caché de Azure Redis proporciona acceso a los servidores Redis que se ejecutan en servidores alojados en un centro de datos de Azure; actúa como un frontal que proporciona control de acceso y seguridad. Puede proporcionar una caché a través del portal de administración de Azure. El portal proporciona una serie de configuraciones predefinidas, comprendido entre un 53GB de memoria caché que ejecuta como un servicio dedicado que es compatible con SSL comunicaciones (privacidad) y la replicación de maestro y subordinado con un SLA de disponibilidad del 99,9%, hasta un 250 MB en memoria caché sin replicación (ninguna garantía de disponibilidad) que se ejecuta en hardware compartido.

Con el portal de administración de Azure, también puede configurar la directiva de expulsión de la caché y controlar el acceso a la memoria caché al agregar usuarios a los roles proporcionados; Propietario, Colaborador, lector. Estas funciones definen las operaciones que los miembros pueden realizar. Por ejemplo, los miembros del rol del propietario tienen un control completo sobre la memoria caché (incluida la seguridad) y su contenido, miembros de la función Colaborador pueden leer y escribir información en la memoria caché y los miembros de la función Lector sólo pueden recuperar datos de la memoria caché.

La mayoría de las tareas administrativas se realizan a través del portal de administración de Azure y por este motivo muchos de los comandos administrativos disponibles en la versión estándar de Redis no están disponibles, incluida la capacidad para modificar la configuración de programación, cierre el servidor Redis, configurar esclavos adicionales o forzar guardar datos en el disco.

El portal de administración de Azure incluye una cómoda visualización gráfica que le permite supervisar el rendimiento de la memoria caché. Por ejemplo, puede ver el número de conexiones que se realizan, el número de solicitudes realizadas, el volumen de lectura y escritura, y el número de caché llega frente a errores de la caché. Utilizando esta información, puede determinar la eficacia de la memoria caché y si es necesario cambia a otra configuración o cambia la directiva de expulsión. Además, puede crear alertas que envía mensajes de correo electrónico a un administrador si uno o más métricas críticas superen un intervalo esperado. Por ejemplo, si el número de errores de caché supera un valor especificado en la última hora, un administrador podría recibir alertas como la memoria caché puede ser demasiado pequeña o datos pueden ser que va a expulsar demasiado rápido.

También puede supervisar la CPU, la memoria y el uso de la red de la memoria caché.

Para obtener más información y ejemplos que muestran cómo crear y configurar una caché de Azure Redis, visite la página [Lap alrededor de Azure Redis caché](https://azure.microsoft.com/blog/2014/06/04/lap-around-azure-redis-cache-preview/) en el blog de Azure.

## <a name="caching-session-state-and-html-output"></a>Estado de la sesión de almacenamiento en caché y salida HTML

Si está creando ASP.NET aplicaciones que funcione con funciones de Azure web, puede guardar información de estado de sesión y salida HTML en una caché de Azure Redis web. El proveedor de estado de sesión para Azure Redis caché le permite compartir información de la sesión entre diferentes instancias de una aplicación web ASP.NET y es muy útil en situaciones de granja de servidores web donde afinidad de cliente y el servidor no está disponible y el almacenamiento en caché de datos de sesión en memoria no sería adecuado.

Usar el proveedor de estado de sesión con Azure Redis caché ofrece varias ventajas, incluidos:

- Puede compartir entre un gran número de instancias de una aplicación web ASP.NET, que proporciona escalabilidad mejorada, el estado de sesión
- Admite controlado, simultáneo acceso a los mismos datos de estado de sesión de varios lectores y un único escritor, y
- Puede utilizar la compresión para ahorrar memoria y mejorar el rendimiento de red.

Para obtener más información visite la página de [Proveedor de estado de sesión de ASP.NET para Azure Redis caché](redis-cache/cache-aspnet-session-state-provider.md) en el sitio Web de Microsoft.

> [AZURE.NOTE] No utilice el proveedor de estado de sesión para Azure Redis caché para las aplicaciones de ASP.NET que se ejecutan fuera del entorno de Azure. La latencia de acceso a la memoria caché desde fuera de Azure puede eliminar las ventajas de rendimiento de caché de datos.

Asimismo, el proveedor de caché de resultados para Azure Redis caché le permite guardar las respuestas HTTP generadas por una aplicación web ASP.NET. Utilizar el proveedor de caché de resultados con Azure Redis caché puede mejorar los tiempos de respuesta de las aplicaciones que representan los resultados HTML complejo; instancias de aplicación generar respuestas similar pueden hacer el uso de los fragmentos de salida compartido en la caché en lugar de generar este resultado nuevo en HTML.  Para obtener más información, visite la página del [Proveedor de caché de resultados de ASP.NET para Azure Redis caché](redis-cache/cache-aspnet-output-cache-provider.md) en el sitio Web de Microsoft.

### <a name="azure-redis-cache"></a>Caché de Azure Redis

Caché de Redis Azure proporciona acceso a los servidores Redis que se hospedan en un centro de datos de Azure. Actúa como un frontal que proporciona control de acceso y seguridad. Puede proporcionar una caché a través del portal de Azure.

El portal ofrece una amplia gama de configuraciones predefinidas. Desde un 53 GB de memoria caché que se ejecuta como un servicio dedicado que admita comunicaciones SSL (para privacidad) y la replicación de maestro y subordinado con un SLA de disponibilidad del 99,9%, hacia abajo hasta una caché de 0 MB 25 sin replicación (ninguna garantía de disponibilidad) que se ejecuta en hardware compartido.

Con el portal de Azure, puede también configurar la directiva de expulsión de la caché y controlar el acceso a la memoria caché al agregar usuarios a los roles proporcionados.  Estas funciones, que definen las operaciones que los miembros pueden realizar, incluyen propietario, Colaborador y lector. Por ejemplo, los miembros del rol del propietario tienen un control completo sobre la memoria caché (incluida la seguridad) y su contenido, miembros de la función Colaborador pueden leer y escribir información en la memoria caché y los miembros de la función Lector sólo pueden recuperar datos de la memoria caché.

La mayoría de las tareas administrativas se realizan a través del portal de Azure. Por este motivo, muchos de los comandos administrativos que están disponibles en la versión estándar de Redis no están disponibles, incluida la capacidad para modificar la configuración mediante programación, cierre el servidor Redis, configurar a subordinadas adicionales o forzar guardar datos en el disco.

El portal de Azure incluye una cómoda visualización gráfica que le permite supervisar el rendimiento de la memoria caché. Por ejemplo, puede ver el número de conexiones que se realizan, el número de solicitudes de llevando a cabo, el volumen de lectura y escritura y el número de visitas de caché frente a errores de la caché. Con esta información, puede determinar la eficacia de la memoria caché y si es necesario, cambie a otra configuración o cambiar la directiva de expulsión.

Además, puede crear alertas que envía mensajes de correo electrónico a un administrador si uno o más métricas críticas superen un intervalo esperado. Por ejemplo, es recomendable alertar a un administrador si el número de errores de caché supera un valor especificado en la última hora, ya que significa que la memoria caché podría ser demasiado pequeña o datos podrían ser que va a expulsar demasiado rápido.

También puede supervisar la CPU, la memoria y el uso de la red de la memoria caché.

Para obtener más información y ejemplos que muestran cómo crear y configurar una caché de Azure Redis, visite la página [Lap alrededor de Azure Redis caché](https://azure.microsoft.com/blog/2014/06/04/lap-around-azure-redis-cache-preview/) en el blog de Azure.

## <a name="caching-session-state-and-html-output"></a>Estado de la sesión de almacenamiento en caché y salida HTML

Si desea crear aplicaciones web ASP.NET que funcione con funciones de Azure web, puede guardar sesión estado información y el formato HTML en una caché Redis de Azure. El proveedor de estado de sesión para Azure Redis caché le permite compartir información de la sesión entre diferentes instancias de una aplicación web ASP.NET y es muy útil en situaciones de granja de servidores web donde afinidad de cliente y el servidor no está disponible y el almacenamiento en caché de datos de sesión en memoria no sería adecuado.

Usar el proveedor de estado de sesión con Azure Redis caché ofrece varias ventajas, incluidos:

- Estado de la sesión de uso compartido con un gran número de instancias de aplicaciones web de ASP.NET.
- Proporcionar escalabilidad mejorada.
- Compatibilidad con acceso controlado y simultáneo a los mismos datos de estado de sesión para varios lectores y un único escritor.
- Utilizar la compresión para ahorrar memoria y mejorar el rendimiento de red.

Para obtener más información, visite la página de [proveedor de estado de sesión ASP.NET para Azure Redis caché](redis-cache/cache-aspnet-session-state-provider.md) en el sitio Web de Microsoft.

> [AZURE.NOTE] No utilice el proveedor de estado de sesión de caché de Azure Redis con aplicaciones de ASP.NET que se ejecutan fuera del entorno de Azure. La latencia de acceso a la memoria caché desde fuera de Azure puede eliminar las ventajas de rendimiento de caché de datos.

Asimismo, el proveedor de caché de resultados para Azure Redis caché le permite guardar las respuestas HTTP generadas por una aplicación web ASP.NET. Utilizar el proveedor de caché de resultados con Azure Redis caché puede mejorar los tiempos de respuesta de las aplicaciones que representan los resultados HTML complejo. Instancias de aplicación que generan respuestas similar pueden hacer uso de los fragmentos de salida compartido en la caché en lugar de generar este resultado nuevo en HTML. Para obtener más información, visite la página de [proveedor de caché de resultados ASP.NET para Azure Redis caché](redis-cache/cache-aspnet-output-cache-provider.md) en el sitio Web de Microsoft.

## <a name="building-a-custom-redis-cache"></a>Creación de una caché Redis personalizada

Caché de Azure Redis actúa como un frontal a los servidores Redis subyacentes. Actualmente es compatible con un conjunto fijo de configuraciones, pero no proporciona Redis clústeres. Si necesita una configuración avanzada que no está cubierta por la caché de Azure Redis (por ejemplo, mayor que 53 GB de memoria caché) puede generar y hospedar sus propios servidores Redis mediante máquinas virtuales de Windows Azure.

Esto es un proceso potencialmente complejo, ya que podría ser necesario crear varias máquinas virtuales para que actúe como nodos subordinados y maestras para implementar la replicación. Además, si desea crear un clúster, a continuación, necesita varios patrones y servidores subordinados. Una topología de replicación agrupada mínimas que proporciona un alto grado de disponibilidad y escalabilidad comprende al menos seis máquinas virtuales organizadas en tres pares de servidores maestro y subordinado (un clúster debe contener al menos tres nodos maestros).

Cada par maestro y subordinado debe estar juntos para minimizar la latencia. Sin embargo, cada conjunto de pares de se puede ejecutar en centros de datos distintos Azure ubicados en diferentes regiones, si desea ubicar los datos almacenados en caché cerca de las aplicaciones que están más probable que usa. Un ejemplo que muestra cómo crear y configurar un nodo Redis ejecuta como una máquina virtual de Azure se recorre de la página que se [Ejecuta Redis en una máquina virtual Linux de CentOS en Azure](http://blogs.msdn.com/b/tconte/archive/2012/06/08/running-redis-on-a-centos-linux-vm-in-windows-azure.aspx) en el sitio Web de Microsoft.

[AZURE.NOTE] Tenga en cuenta que si implementa su propia caché Redis de esta manera, usted es responsable de la supervisión, administrar y proteger el servicio.

## <a name="partitioning-a-redis-cache"></a>Partición una caché Redis

Partición de la memoria caché implica la división de la memoria caché en varios equipos. Esta estructura ofrece varias ventajas mediante un servidor de caché, incluidos:

- Crear una caché que es mucho más grande de pueden almacenarse en un solo servidor.
- Distribución de datos entre servidores, mejorar la disponibilidad. Si un servidor falla o deja de estar accesible, los datos que contiene no está disponible, pero los datos en los demás servidores accesibles. Para una caché, esto no es crucial porque los datos en caché están solo una copia temporal de los datos que se almacenan en una base de datos. Los datos almacenados en caché en un servidor que se convierte en inaccesible pueden almacenarse en caché en un servidor distinto en su lugar.
- Distribuir la carga entre servidores, lo que mejora el rendimiento y escalabilidad.
- Geolocating datos cerca de los usuarios que tienen acceso a él, lo que reduce la latencia.

Para una caché, la forma más común de las particiones es sharding. En esta estrategia, cada partición (o fragmentar) es una caché Redis en su propia derecha. Datos se dirigen a una partición específica mediante el uso de la lógica de sharding, que puede utilizar una variedad de métodos para distribuir los datos. El [patrón de Sharding](http://msdn.microsoft.com/library/dn589797.aspx) proporciona más información sobre cómo implementar sharding.

Para implementar una partición en una caché Redis, realice uno de los siguientes enfoques:

- _Enrutamiento de consulta del servidor._ En esta técnica, una aplicación cliente envía una solicitud a cualquiera de los servidores Redis que forman parte de la memoria caché (probablemente, el servidor más cercano). Cada servidor Redis almacena los metadatos que describa la partición que contiene y también contiene información sobre el que se encuentran las particiones en otros servidores. El servidor Redis examina la solicitud del cliente. Si se puede resolver localmente, realizará la operación. En caso contrario, reenvía la solicitud en el servidor correspondiente. Este modelo se implementa mediante Redis clústeres y se describe en más detalle en la página [Redis tutorial de clúster](http://redis.io/topics/cluster-tutorial) en el sitio Web Redis. Redis clústeres es transparente a las aplicaciones cliente y servidores adicionales Redis pueden agregarse a clúster (y los datos que se vuelva a particiones) sin necesidad de volver a configurar a los clientes.

- _Partición del cliente._ En este modelo, la aplicación cliente contiene lógica (posiblemente en el formulario de una biblioteca) que enruta las solicitudes al servidor Redis correspondiente. Este enfoque puede usarse con Azure Redis caché. Crear varias Azure Redis en caché (uno para cada partición de datos) e implementar la lógica de cliente que enruta las solicitudes a la caché correcta. Si el esquema de particiones cambia (si se crean en caché de Redis de Azure adicionales, por ejemplo), aplicaciones cliente posible que tenga que volver a configurar.

- _Proxy asistido particiones._ En este esquema, cliente aplicaciones enviar solicitudes a un servicio de proxy intermediario que comprende cómo los datos se dividen y enruta la solicitud a los correspondientes Redis server. Este método también puede usarse con Azure Redis caché; el servicio proxy se puede implementar como un servicio de nube de Azure. Este enfoque requiere un nivel adicional de complejidad para implementar el servicio y solicitudes pueden tardar más que utilizar las particiones del cliente.

La página [particiones: cómo dividir datos entre varias instancias Redis](http://redis.io/topics/partitioning) en la Redis sitio Web proporciona más información sobre cómo implementar las particiones con Redis.

### <a name="implement-redis-cache-client-applications"></a>Implementar aplicaciones cliente de caché Redis

Redis es compatible con las aplicaciones cliente escritas en lenguajes de programación. Si va a crear nuevas aplicaciones mediante .NET Framework, el enfoque recomendado es usar la biblioteca de cliente StackExchange.Redis. Esta biblioteca proporciona un modelo de objetos de .NET Framework que resume los detalles para conectarse a un servidor Redis, comandos de enviar y recibir respuestas. Está disponible en Visual Studio como un paquete de NuGet. Puede utilizar esta misma biblioteca para conectarse a una caché de Redis Azure o una caché de Redis personalizada hospedado en una máquina virtual.

Para conectarse a un servidor Redis usar estático `Connect` método de la `ConnectionMultiplexer` clase. La conexión que crea este método está diseñada para usarse durante toda la duración de la aplicación cliente y la misma conexión puede ser usada por varios subprocesos simultáneos. No volver a conectar y desconectar cada vez que realice una operación Redis porque puede disminuir el rendimiento.

Puede especificar los parámetros de conexión, como la dirección de host Redis y la contraseña. Si está utilizando la caché de Azure Redis, la contraseña es la clave principal o secundaria que se genera para Azure Redis caché a través del portal de administración de Azure.

Después de que ha conectado al servidor Redis, puede obtener un identificador en la base de datos Redis que actúa como la memoria caché. La conexión Redis proporciona la `GetDatabase` método para hacer esto. A continuación, puede recuperar los elementos de la memoria caché y almacenar datos en la caché mediante la `StringGet` y `StringSet` métodos. Estos métodos espera una clave como parámetro y devolver el elemento en la caché que tenga un valor coincidente (`StringGet`) o agregar el elemento a la caché con esta clave (`StringSet`).

Según la ubicación del servidor Redis, muchas operaciones podrían provocar cierta latencia mientras se transmite una solicitud al servidor y se devuelve una respuesta al cliente. La biblioteca de StackExchange proporciona versiones asincrónicas de muchos de los métodos que expone para ayudar a aplicaciones cliente permanezca alerta. Estos métodos admiten el [Modelo asincrónico basado en tareas](http://msdn.microsoft.com/library/hh873175.aspx) en .NET Framework.

Fragmento de código siguiente muestra un método denominado `RetrieveItem`. Se muestra una implementación del patrón de margen de caché basada en la biblioteca de StackExchange y Redis. El método toma un valor de clave de cadena e intenta recuperar el elemento correspondiente de la caché Redis llamando el `StringGetAsync` método (la versión asincrónica de `StringGet`).

Si no se encuentra el elemento, se obtiene el origen de datos subyacente con el `GetItemFromDataSourceAsync` método (que es un método local y no forma parte de la biblioteca de StackExchange). A continuación, se agrega a la caché mediante la `StringSetAsync` método de modo que se puede recuperar rápidamente más próxima vez.

```csharp
// Connect to the Azure Redis cache
ConfigurationOptions config = new ConfigurationOptions();
config.EndPoints.Add("<your DNS name>.redis.cache.windows.net");
config.Password = "<Redis cache key from management portal>";
ConnectionMultiplexer redisHostConnection = ConnectionMultiplexer.Connect(config);
IDatabase cache = redisHostConnection.GetDatabase();
...
private async Task<string> RetrieveItem(string itemKey)
{
    // Attempt to retrieve the item from the Redis cache
    string itemValue = await cache.StringGetAsync(itemKey);

    // If the value returned is null, the item was not found in the cache
    // So retrieve the item from the data source and add it to the cache
    if (itemValue == null)
    {
        itemValue = await GetItemFromDataSourceAsync(itemKey);
        await cache.StringSetAsync(itemKey, itemValue);
    }

    // Return the item
    return itemValue;
}
```

La `StringGet` y `StringSet` métodos no están limitados a recuperar o almacenar los valores de cadena. Pueden tomar cualquier elemento de la serie como una matriz de bytes. Si necesita guardar un objeto de .NET, puede serializar como una secuencia de bytes y usar la `StringSet` método para escribir en la memoria caché.

Asimismo, puede leer un objeto de la memoria caché utilizando la `StringGet` método y deserializar como un objeto de .NET. El código siguiente muestra un conjunto de métodos de extensión para la interfaz de IDatabase (el `GetDatabase` método de una conexión Redis devuelve un `IDatabase` objeto) y el código de ejemplo que usa estos métodos para leer y escribir un `BlogPost` objeto a la caché:

```csharp
public static class RedisCacheExtensions
{
    public static async Task<T> GetAsync<T>(this IDatabase cache, string key)
    {
        return Deserialize<T>(await cache.StringGetAsync(key));
    }

    public static async Task<object> GetAsync(this IDatabase cache, string key)
    {
        return Deserialize<object>(await cache.StringGetAsync(key));
    }

    public static async Task SetAsync(this IDatabase cache, string key, object value)
    {
        await cache.StringSetAsync(key, Serialize(value));
    }

    static byte[] Serialize(object o)
    {
        byte[] objectDataAsStream = null;

        if (o != null)
        {
            BinaryFormatter binaryFormatter = new BinaryFormatter();
            using (MemoryStream memoryStream = new MemoryStream())
            {
                binaryFormatter.Serialize(memoryStream, o);
                objectDataAsStream = memoryStream.ToArray();
            }
        }

        return objectDataAsStream;
    }

    static T Deserialize<T>(byte[] stream)
    {
        T result = default(T);

        if (stream != null)
        {
            BinaryFormatter binaryFormatter = new BinaryFormatter();
            using (MemoryStream memoryStream = new MemoryStream(stream))
            {
                result = (T)binaryFormatter.Deserialize(memoryStream);
            }
        }

        return result;
    }
}
```

El código siguiente muestra un método denominado `RetrieveBlogPost` que usa estos métodos de extensión para leer y escribir un serializable `BlogPost` objeto a la caché siguiendo el modelo de margen de caché:

```csharp
// The BlogPost type
[Serializable]
private class BlogPost
{
    private HashSet<string> tags = new HashSet<string>();

    public BlogPost(int id, string title, int score, IEnumerable<string> tags)
    {
        this.Id = id;
        this.Title = title;
        this.Score = score;
        this.tags = new HashSet<string>(tags);
    }

    public int Id { get; set; }
    public string Title { get; set; }
    public int Score { get; set; }
    public ICollection<string> Tags { get { return this.tags; } }
}
...
private async Task<BlogPost> RetrieveBlogPost(string blogPostKey)
{
    BlogPost blogPost = await cache.GetAsync<BlogPost>(blogPostKey);
    if (blogPost == null)
    {
        blogPost = await GetBlogPostFromDataSourceAsync(blogPostKey);
        await cache.SetAsync(blogPostKey, blogPost);
    }

    return blogPost;
}
```

Redis es compatible con el comando canalización si una aplicación cliente envía varias solicitudes asincrónicas. Redis puede multiplexar las solicitudes con la misma conexión en lugar de recibir y responder a los comandos en una secuencia estricta.

Este enfoque ayuda a reducir la latencia haciendo uso más eficaz de la red. Fragmento de código siguiente muestra un ejemplo que recupera los detalles de los dos clientes simultáneamente. El código envía dos solicitudes y, a continuación, realiza otro proceso (no se muestra) antes de esperar para recibir los resultados. La `Wait` método del objeto de caché es similar a .NET Framework `Task.Wait` método:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
var task1 = cache.StringGetAsync("customer:1");
var task2 = cache.StringGetAsync("customer:2");
...
var customer1 = cache.Wait(task1);
var customer2 = cache.Wait(task2);
```

La [documentación de Azure Redis caché](https://azure.microsoft.com/documentation/services/cache/) de página en el sitio Web de Microsoft proporciona más información sobre cómo escribir aplicaciones cliente que pueden usar la caché de Azure Redis. Información adicional está disponible en la [página de uso básico](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Basics.md) en el sitio Web de StackExchange.Redis.

La página [canalizaciones y multiplexores](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/PipelinesMultiplexers.md) en el mismo sitio Web proporciona más información sobre las operaciones asincrónica y canalización con Redis y la biblioteca de StackExchange.  La siguiente sección de este artículo, uso Redis almacenamiento en caché, proporciona ejemplos de algunas de las técnicas más avanzadas que puede aplicar a los datos que se almacenan en una caché Redis.

## <a name="using-redis-caching"></a>Usar Redis almacenamiento en caché

El uso más sencillo de Redis en caché preocupaciones es pares de valor de clave cuando el valor es una cadena de longitud arbitraria que puede contener datos binarios no interpretada. (Es, esencialmente, una matriz de bytes que se pueden tratar como una cadena). Este escenario se muestra en las aplicaciones de cliente de caché de implementación Redis sección más adelante en este artículo.

Tenga en cuenta que las claves también contengan datos no interpretados, por lo que puede usar cualquier información binaria como clave. Sin embargo, más tiempo la tecla está más espacio tardará para almacenar, y más tiempo tardará para realizar operaciones de búsqueda. Facilidad de uso y facilidad de mantenimiento, diseñar su espacio de claves con cuidado y use teclas significativas (pero no detallado).

Por ejemplo, use las teclas estructuradas como "cliente: 100" para representar la clave para el cliente con identificador de 100 en lugar de simplemente "100". Esta combinación le permite fácilmente distinguir entre los valores que almacenan diferentes tipos de datos. Por ejemplo, también puede usar la tecla "pedidos: 100" para representar la clave para el pedido con ID 100.

Aparte de cadenas binarias unidimensionales, un valor en un par de valor de clave Redis también puede contener más estructurado información, incluidas las listas, Establece (ordenadas y sin ordenar) y realiza el hash. Redis proporciona un conjunto de comandos completa que puede manipular estos tipos y muchos de estos comandos están disponibles para las aplicaciones de .NET Framework a través de una biblioteca de cliente como StackExchange. La página [Introducción a Redis abstracciones y tipos de datos](http://redis.io/topics/data-types-intro) en el sitio Web Redis ofrece una descripción más detallada de estos tipos y los comandos que puede usar para manipularlos.

Esta sección resume algunos casos de uso común para estos tipos de datos y comandos.

### <a name="perform-atomic-and-batch-operations"></a>Realizar atómica y operaciones por lotes

Redis es compatible con una serie de operaciones atómicas de obtener y establecer valores de cadena. Estas operaciones quitar los riesgos de carreras posibles que pueden producirse al usar independiente `GET` y `SET` comandos. Las operaciones que se encuentran disponibles incluyen:

- `INCR`, `INCRBY`, `DECR`, y `DECRBY`, que realizar operaciones de incrementar y disminuir atómicas en valores de datos numéricos enteros. La biblioteca de StackExchange proporciona versiones de sobrecargadas de la `IDatabase.StringIncrementAsync` y `IDatabase.StringDecrementAsync` métodos para realizar estas operaciones y devolver el valor resultante almacenado en la memoria caché. Fragmento de código siguiente muestra cómo usar estos métodos:

  ```csharp
  ConnectionMultiplexer redisHostConnection = ...;
  IDatabase cache = redisHostConnection.GetDatabase();
  ...
  await cache.StringSetAsync("data:counter", 99);
  ...
  long oldValue = await cache.StringIncrementAsync("data:counter");
  // Increment by 1 (the default)
  // oldValue should be 100

  long newValue = await cache.StringDecrementAsync("data:counter", 50);
  // Decrement by 50
  // newValue should be 50
  ```

- `GETSET`, que recupera el valor que está asociada a una clave y cambia a un nuevo valor. La biblioteca de StackExchange hace que esta operación que esté disponible a través de la `IDatabase.StringGetSetAsync` método. El fragmento de código siguiente muestra un ejemplo de este método. Este código devuelve el valor actual que está asociada con la clave "datos: contador" del ejemplo anterior. A continuación, restablece el valor de esta clave en cero, todo como parte de la misma operación:

  ```csharp
  ConnectionMultiplexer redisHostConnection = ...;
  IDatabase cache = redisHostConnection.GetDatabase();
  ...
  string oldValue = await cache.StringGetSetAsync("data:counter", 0);
  ```

- `MGET`y `MSET`, que puede devolver o cambiar un conjunto de valores de cadena como una única operación. La `IDatabase.StringGetAsync` y `IDatabase.StringSetAsync` se sobrecargan métodos para admitir esta funcionalidad, tal como se muestra en el ejemplo siguiente:

  ```csharp
  ConnectionMultiplexer redisHostConnection = ...;
  IDatabase cache = redisHostConnection.GetDatabase();
  ...
  // Create a list of key-value pairs
  var keysAndValues =
      new List<KeyValuePair<RedisKey, RedisValue>>()
      {
          new KeyValuePair<RedisKey, RedisValue>("data:key1", "value1"),
          new KeyValuePair<RedisKey, RedisValue>("data:key99", "value2"),
          new KeyValuePair<RedisKey, RedisValue>("data:key322", "value3")
      };

  // Store the list of key-value pairs in the cache
  cache.StringSet(keysAndValues.ToArray());
  ...
  // Find all values that match a list of keys
  RedisKey[] keys = { "data:key1", "data:key99", "data:key322"};
  RedisValue[] values = null;
  values = cache.StringGet(keys);
  // values should contain { "value1", "value2", "value3" }
  ```

También puede combinar varias operaciones en una sola transacción Redis como se describe en anteriormente en este artículo la sección de transacciones y lotes Redis. La biblioteca de StackExchange proporciona soporte técnico para las transacciones a través de la `ITransaction` interfaz.

Crear un `ITransaction` objeto utilizando la `IDatabase.CreateTransaction` método. Invocar comandos a la transacción mediante los métodos proporcionados por la `ITransaction` objeto.

La `ITransaction` interfaz proporciona acceso a un conjunto de métodos que es similar a los que tiene acceso a la `IDatabase` interfaz, excepto en que todos los métodos son asincrónicos. Esto significa que son sólo realiza cuando la `ITransaction.Execute` método se invoca. El valor devuelto por la `ITransaction.Execute` método indica si la transacción se ha creado correctamente (true) o si se produce un error (falso).

Fragmento de código siguiente muestra un ejemplo que aumenta y disminuye dos contadores como parte de la misma transacción:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
ITransaction transaction = cache.CreateTransaction();
var tx1 = transaction.StringIncrementAsync("data:counter1");
var tx2 = transaction.StringDecrementAsync("data:counter2");
bool result = transaction.Execute();
Console.WriteLine("Transaction {0}", result ? "succeeded" : "failed");
Console.WriteLine("Result of increment: {0}", tx1.Result);
Console.WriteLine("Result of decrement: {0}", tx2.Result);
```

Recuerde que Redis transacciones a diferencia de transacciones en bases de datos relacionales. La `Execute` método simplemente colas todos los comandos que forman parte de la transacción se ejecute y, a continuación, si cualquiera de ellos está mal formado se detiene la transacción. Si ha formado correctamente todos los comandos, cada comando se ejecuta de forma asincrónica.

Si se produce un error en cualquier comando, los demás continuar procesamiento. Si necesita comprobar que un comando se ha completado correctamente, debe recuperar los resultados del comando mediante la propiedad de **resultado** de la tarea correspondiente, tal como se muestra en el ejemplo anterior. Leer la propiedad del **resultado** se bloqueará el subproceso de llamada hasta que haya completado la tarea.

Para obtener más información, consulte la página de [transacciones en Redis](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Transactions.md) en el sitio Web de StackExchange.Redis.

Al realizar operaciones por lotes, puede usar el `IBatch` interfaz de la biblioteca StackExchange. Esta interfaz proporciona acceso a un conjunto de métodos similares a los que tiene acceso a la `IDatabase` interfaz, excepto en que todos los métodos son asincrónicos.

Crear un `IBatch` objeto utilizando la `IDatabase.CreateBatch` método y a continuación, ejecute el lote utilizando el `IBatch.Execute` método, tal como se muestra en el ejemplo siguiente. Este código simplemente establece un valor de cadena, aumenta y disminuye los mismos contadores utilizados en el ejemplo anterior y muestra los resultados:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
IBatch batch = cache.CreateBatch();
batch.StringSetAsync("data:key1", 11);
var t1 = batch.StringIncrementAsync("data:counter1");
var t2 = batch.StringDecrementAsync("data:counter2");
batch.Execute();
Console.WriteLine("{0}", t1.Result);
Console.WriteLine("{0}", t2.Result);
```

Es importante comprender que a diferencia de una transacción, si se produce un error en un comando en un lote porque está mal formado, los comandos podrían seguir ejecutando. La `IBatch.Execute` método no devuelve ningún indicación de éxito o error.

### <a name="perform-fire-and-forget-cache-operations"></a>Realizar fire y olvide operaciones de caché

Redis admite fire y olvide operaciones usando los indicadores de comando. En este caso, el cliente simplemente inicia una operación pero no tiene interés en el resultado y no espera que llevar a cabo el comando. El ejemplo siguiente muestra cómo realizar el comando INCR como un fire y olvide operación:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
await cache.StringSetAsync("data:key1", 99);
...
cache.StringIncrement("data:key1", flags: CommandFlags.FireAndForget);
```

### <a name="specify-automatically-expiring-keys"></a>Especificar claves caducadas automáticamente

Al almacenar un elemento en una caché Redis, puede especificar un tiempo de espera después de que el elemento se eliminarán automáticamente de la memoria caché. También puede consultar una clave de cuánto tiempo tiene antes de que caduque utilizando la `TTL` comando. Este comando está disponible para las aplicaciones de StackExchange mediante la `IDatabase.KeyTimeToLive` método.

Fragmento de código siguiente muestra cómo establecer una fecha de caducidad de 20 segundos en una clave y la duración restante de la clave de la consulta:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
// Add a key with an expiration time of 20 seconds
await cache.StringSetAsync("data:key1", 99, TimeSpan.FromSeconds(20));
...
// Query how much time a key has left to live
// If the key has already expired, the KeyTimeToLive function returns a null
TimeSpan? expiry = cache.KeyTimeToLive("data:key1");
```

También puede establecer la fecha de expiración para una fecha y hora determinadas mediante el comando caducar, que está disponible en la biblioteca de StackExchange como la `KeyExpireAsync` método:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
// Add a key with an expiration date of midnight on 1st January 2015
await cache.StringSetAsync("data:key1", 99);
await cache.KeyExpireAsync("data:key1",
    new DateTime(2015, 1, 1, 0, 0, 0, DateTimeKind.Utc));
...
```

> _Sugerencia:_ Puede quitar manualmente un elemento de la caché usando el comando DEL, que está disponible a través de la biblioteca de StackExchange como la `IDatabase.KeyDeleteAsync` método.

### <a name="use-tags-to-cross-correlate-cached-items"></a>Usar etiquetas para cruzadas elementos almacenados en caché

Un conjunto Redis es una colección de varios elementos que comparten una sola clave. Puede crear un conjunto con el comando SADD. Puede recuperar los elementos de un conjunto mediante el comando SMEMBERS. La biblioteca de StackExchange implementa el comando SADD con la `IDatabase.SetAddAsync` comando de método y la SMEMBERS con la `IDatabase.SetMembersAsync` método.

También puede combinar conjuntos existentes para crear nuevos conjuntos con la SDIFF (conjunto diferencia), SINTERIZACIÓN (conjunto intersección) y comandos SUNION (configurar unión). La biblioteca de StackExchange unifica estas operaciones en el `IDatabase.SetCombineAsync` método. El primer parámetro a este método especifica la operación de conjunto de llevar a cabo.

Fragmentos de código siguientes muestran cómo conjuntos pueden ser útiles para almacenar rápidamente y recuperar colecciones de elementos relacionados. Este código utiliza la `BlogPost` tipo que se ha descrito en la sección aplicaciones de cliente de caché Redis implementar más adelante en este artículo.

A `BlogPost` objeto contiene cuatro campos: un ID, un título, una calificación de clasificación y un conjunto de etiquetas. El primer fragmento de código siguiente muestra los datos de ejemplo que se utilizan para rellenar una lista de C# de `BlogPost` objetos:

```csharp
List<string[]> tags = new List<string[]>()
{
    new string[] { "iot","csharp" },
    new string[] { "iot","azure","csharp" },
    new string[] { "csharp","git","big data" },
    new string[] { "iot","git","database" },
    new string[] { "database","git" },
    new string[] { "csharp","database" },
    new string[] { "iot" },
    new string[] { "iot","database","git" },
    new string[] { "azure","database","big data","git","csharp" },
    new string[] { "azure" }
};

List<BlogPost> posts = new List<BlogPost>();
int blogKey = 0;
int blogPostId = 0;
int numberOfPosts = 20;
Random random = new Random();
for (int i = 0; i < numberOfPosts; i++)
{
    blogPostId = blogKey++;
    posts.Add(new BlogPost(
        blogPostId,               // Blog post ID
        string.Format(CultureInfo.InvariantCulture, "Blog Post #{0}",
            blogPostId),          // Blog post title
        random.Next(100, 10000),  // Ranking score
        tags[i % tags.Count]));   // Tags--assigned from a collection
                                  // in the tags list
}
```

Puede almacenar las etiquetas para cada `BlogPost` objeto como un conjunto en una caché Redis y asociar cada conjunto con el identificador de la `BlogPost`. Esto permite una aplicación encontrar rápidamente todas las etiquetas que pertenecen a una entrada de blog específica. Para habilitar la búsqueda en la dirección opuesta y encontrar todas las entradas de blog que comparten una etiqueta específica, puede crear otro conjunto que contiene el blog de publicaciones que hace referencia el identificador de etiqueta en la clave:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
// Tags are easily represented as Redis Sets
foreach (BlogPost post in posts)
{
    string redisKey = string.Format(CultureInfo.InvariantCulture,
        "blog:posts:{0}:tags", post.Id);
    // Add tags to the blog post in Redis
    await cache.SetAddAsync(
        redisKey, post.Tags.Select(s => (RedisValue)s).ToArray());

    // Now do the inverse so we can figure how which blog posts have a given tag
    foreach (var tag in post.Tags)
    {
        await cache.SetAddAsync(string.Format(CultureInfo.InvariantCulture,
            "tag:{0}:blog:posts", tag), post.Id);
    }
}
```

Estas estructuras le permiten realizar muchas consultas comunes con gran eficacia. Por ejemplo, puede buscar y mostrar todas las etiquetas para la entrada de blog 1 similar a esta:

```csharp
// Show the tags for blog post #1
foreach (var value in await cache.SetMembersAsync("blog:posts:1:tags"))
{
    Console.WriteLine(value);
}
```

Puede buscar todas las etiquetas que sean comunes en blog publican la entrada de blog y 1 2 al realizar una operación de intersección de conjunto, como sigue:

```csharp
// Show the tags in common for blog posts #1 and #2
foreach (var value in await cache.SetCombineAsync(SetOperation.Intersect, new RedisKey[]
    { "blog:posts:1:tags", "blog:posts:2:tags" }))
{
    Console.WriteLine(value);
}
```

Y puede encontrar todas las entradas de blog que contienen una etiqueta específica:

```csharp
// Show the ids of the blog posts that have the tag "iot".
foreach (var value in await cache.SetMembersAsync("tag:iot:blog:posts"))
{
    Console.WriteLine(value);
}
```

### <a name="find-recently-accessed-items"></a>Elementos de buscar abierto recientemente

Es una tarea común de muchas aplicaciones buscar más recientemente tiene acceso a los elementos. Por ejemplo, un sitio de blog desee mostrar información sobre las entradas de blog leídas más recientemente.

Puede implementar esta funcionalidad mediante una lista Redis. Una lista Redis contiene varios elementos que comparten la misma clave. La lista actúa como una cola de dos puntas. Puede insertar elementos en cualquiera de los extremos de la lista mediante la LPUSH (push izquierda) y los comandos RPUSH (push derecha). Puede recuperar elementos desde cualquiera de los extremos de la lista mediante los comandos LPOP y RPOP. También puede devolver un conjunto de elementos mediante los comandos LRANGE y RGANIZAR.

Los fragmentos de código siguientes muestran cómo puede realizar estas operaciones mediante el uso de la biblioteca StackExchange. Este código utiliza la `BlogPost` tipo de los ejemplos anteriores. Cuando una entrada de blog se leen por un usuario, el `IDatabase.ListLeftPushAsync` método inserta el título de la entrada de blog en una lista que está asociada con la clave "blog:recent_posts" en la caché Redis.

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
string redisKey = "blog:recent_posts";
BlogPost blogPost = ...; // Reference to the blog post that has just been read
await cache.ListLeftPushAsync(
    redisKey, blogPost.Title); // Push the blog post onto the list
```

Mientras se leen más entradas de blog, sus títulos se insertan en la misma lista. La lista está ordenada por la secuencia en la que se han agregado los títulos. Las entradas de blog leídas más recientemente son hacia el extremo izquierdo de la lista. (Si la misma entrada de blog se lee varias veces, tendrá varias entradas en la lista.)

Puede mostrar los títulos de las publicaciones leídas más recientemente mediante la `IDatabase.ListRange` método. Este método toma la clave que contiene la lista, un punto de partida y un punto final. El código siguiente recupera los títulos de los 10 entradas de blog (elementos de 0 a 9) al final de más a la izquierda de la lista:

```csharp
// Show latest ten posts
foreach (string postTitle in await cache.ListRangeAsync(redisKey, 0, 9))
{
    Console.WriteLine(postTitle);
}
```

Tenga en cuenta que el `ListRangeAsync` método no quitar elementos de la lista. Para ello, puede usar el `IDatabase.ListLeftPopAsync` y `IDatabase.ListRightPopAsync` métodos.

Para impedir que la lista creciendo indefinidamente, puede seleccionar periódicamente elementos por la lista de recorte. El fragmento de código siguiente muestra cómo quitar todo pero los cinco más a la izquierda elementos de la lista:

```csharp
await cache.ListTrimAsync(redisKey, 0, 5);
```

### <a name="implement-a-leader-board"></a>Implementar un panel de relleno

De forma predeterminada, los elementos de un conjunto no se encuentran en ningún orden específico. Puede crear un conjunto ordenado mediante el comando ZADD (la `IDatabase.SortedSetAdd` método en la biblioteca de StackExchange). Se ordenan los elementos mediante el uso de un valor numérico denominado una calificación, que se incluye como un parámetro al comando.

Fragmento de código siguiente agrega el título de una entrada de blog en una lista ordenada. En este ejemplo, cada entrada de blog también tiene un campo de puntuación que contiene la clasificación de la entrada de blog.

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
string redisKey = "blog:post_rankings";
BlogPost blogPost = ...; // Reference to a blog post that has just been rated
await cache.SortedSetAddAsync(redisKey, blogPost.Title, blogpost.Score);
```

Puede recuperar los títulos de la entrada de blog y puntuaciones en sentido ascendente de puntuación utilizando la `IDatabase.SortedSetRangeByRankWithScores` método:

```csharp
foreach (var post in await cache.SortedSetRangeByRankWithScoresAsync(redisKey))
{
    Console.WriteLine(post);
}
```

> [AZURE.NOTE] La biblioteca de StackExchange también proporciona la `IDatabase.SortedSetRangeByRankAsync` método, que devuelve los datos en orden de puntuación, pero no devuelve los resultados.

También puede recuperar los elementos en orden descendente de puntuación y limitar el número de elementos que se devuelven al proporcionar parámetros adicionales a la `IDatabase.SortedSetRangeByRankWithScoresAsync` método. En el ejemplo siguiente muestra los títulos y puntuaciones de las entradas de blog de clasificación 10 principales:

```csharp
foreach (var post in await cache.SortedSetRangeByRankWithScoresAsync(
                               redisKey, 0, 9, Order.Descending))
{
    Console.WriteLine(post);
}
```

En el ejemplo siguiente se utiliza la `IDatabase.SortedSetRangeByScoreWithScoresAsync` método, que puede usar para limitar los elementos que se devuelven a los que se encuentran dentro de una determinada puntuación rango:

```csharp
// Blog posts with scores between 5000 and 100000
foreach (var post in await cache.SortedSetRangeByScoreWithScoresAsync(
                               redisKey, 5000, 100000))
{
    Console.WriteLine(post);
}
```

### <a name="message-by-using-channels"></a>Mensaje mediante canales

Además de actuar como una caché de datos, un servidor Redis proporciona mensajería a través de un mecanismo de alto rendimiento publisher de suscriptor. Aplicaciones cliente pueden suscribirse a un canal y otras aplicaciones o servicios pueden publicar mensajes en el canal. Aplicaciones de suscripción, a continuación, recibirá estos mensajes y pueden procesar.

Redis proporciona el comando SUSCRIBIR para las aplicaciones cliente utilizar para suscribirse a los canales. Este comando espera el nombre de uno o más canales en la que la aplicación aceptará mensajes. La biblioteca StackExchange incluye la `ISubscription` interfaz, lo que permite una aplicación de .NET Framework para suscribirse y publicar en los canales.

Crear un `ISubscription` objeto utilizando la `GetSubscriber` método de la conexión al servidor Redis. Luego escuchar mensajes en un canal mediante el `SubscribeAsync` método de este objeto. En el ejemplo siguiente se muestra cómo suscribirse a un canal denominado "mensajes: blogPosts":

```csharp
ConnectionMultiplexer redisHostConnection = ...;
ISubscriber subscriber = redisHostConnection.GetSubscriber();
...
await subscriber.SubscribeAsync("messages:blogPosts", (channel, message) =>
{
    Console.WriteLine("Title is: {0}", message);
});
```

El primer parámetro de la `Subscribe` método es el nombre del canal. Este nombre sigue las mismas convenciones que se usan las claves de la memoria caché. El nombre puede contener los datos binarios, aunque es aconsejable usar cadenas relativamente cortas y significativas para ayudar a garantizar un buen rendimiento y mantenimiento.

Observe también que el espacio de nombres utilizado canales es independiente del que se utiliza por claves. Esto significa que puede tener canales y las teclas que tienen el mismo nombre, aunque esto podría ser más difícil de mantener el código de la aplicación.

El segundo parámetro es un delegado de acción. Este delegado se ejecuta de forma asincrónica cada vez que un nuevo mensaje aparece en el canal. Este ejemplo muestra simplemente el mensaje en la consola (el mensaje contendrá el título de una entrada de blog).

Para publicar en un canal, una aplicación puede utilizar el comando Redis publicar. La biblioteca de StackExchange proporciona la `IServer.PublishAsync` método para realizar esta operación. El fragmento de código siguiente muestra cómo publicar un mensaje en el canal "mensajes: blogPosts":

```csharp
ConnectionMultiplexer redisHostConnection = ...;
ISubscriber subscriber = redisHostConnection.GetSubscriber();
...
BlogPost blogpost = ...;
subscriber.PublishAsync("messages:blogPosts", blogPost.Title);
```

Existen varios aspectos que debe saber sobre el mecanismo de publicación o suscripción:

- Varios suscriptores pueden suscribirse al mismo canal, y todos recibirán los mensajes que se publican en dicho canal.
- Los suscriptores sólo reciban mensajes que se hayan publicado una vez que se ha suscrito. No tienen búfer canales y una vez que se publica un mensaje, la infraestructura Redis inserta el mensaje a cada suscriptor y, a continuación, quita.
- De forma predeterminada, los mensajes se reciben en el orden en que se envían los suscriptores. En un sistema muy activo con un gran número de mensajes y muchos suscriptores y editores, garantía entrega secuencial de mensajes puede disminuir el rendimiento del sistema. Si no está vinculado a cada mensaje y el orden es importante, puede habilitar el procesamiento simultáneo por el sistema Redis, que puede ayudar a mejorar la capacidad de respuesta. Se puede conseguir en un cliente StackExchange estableciendo la PreserveAsyncOrder de la conexión de suscriptor falso:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
redisHostConnection.PreserveAsyncOrder = false;
ISubscriber subscriber = redisHostConnection.GetSubscriber();
```

## <a name="related-patterns-and-guidance"></a>Orientación y patrones relacionados

El siguiente patrón también podría ser relevante para su situación cuando se implementa el almacenamiento en caché en las aplicaciones:

- [Patrón de caché separada](http://msdn.microsoft.com/library/dn589799.aspx): este patrón describe cómo cargar datos a petición en una caché de un almacén de datos. Este modelo también ayuda a mantener la coherencia entre los datos que se almacenan en la memoria caché y los datos en el almacén de datos original.
- El [modelo de Sharding](http://msdn.microsoft.com/library/dn589797.aspx) proporciona información sobre cómo implementar una partición horizontal para ayudar a mejorar la escalabilidad al almacenamiento y acceso a grandes volúmenes de datos.

## <a name="more-information"></a>Más información

- La página de la [clase MemoryCache](http://msdn.microsoft.com/library/system.runtime.caching.memorycache.aspx) en el sitio Web de Microsoft
- La página de la [documentación de Azure Redis caché](https://azure.microsoft.com/documentation/services/cache/) en el sitio Web de Microsoft
- La página de [Preguntas más frecuentes de caché de Redis Azure](redis-cache/cache-faq.md) en el sitio Web de Microsoft
- La página de [modelo de configuración](http://msdn.microsoft.com/library/windowsazure/hh914149.aspx) en el sitio Web de Microsoft
- La página de [Modelo asincrónico basado en tareas](http://msdn.microsoft.com/library/hh873175.aspx) en el sitio Web de Microsoft
- La página de [canalizaciones y multiplexores](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/PipelinesMultiplexers.md) en repo de StackExchange.Redis GitHub
- La página [Redis persistencia](http://redis.io/topics/persistence) en el sitio Web Redis
- La [página de replicación](http://redis.io/topics/replication) en el sitio Web Redis
- La página [Redis tutorial de clúster](http://redis.io/topics/cluster-tutorial) en el sitio Web Redis
- La [particiones: cómo dividir datos entre varias instancias Redis](http://redis.io/topics/partitioning) página del sitio Web Redis
- La página [Usando Redis como una caché LRU](http://redis.io/topics/lru-cache) en el sitio Web Redis
- La página de [transacciones](http://redis.io/topics/transactions) en el sitio Web Redis
- La página [Redis seguridad](http://redis.io/topics/security) en el sitio Web Redis
- La página de [aspectos básicos de Azure Redis caché](https://azure.microsoft.com/blog/2014/06/04/lap-around-azure-redis-cache-preview/) en el blog de Azure
- La página [Ejecuta Redis en una máquina virtual Linux de CentOS en Azure](http://blogs.msdn.com/b/tconte/archive/2012/06/08/running-redis-on-a-centos-linux-vm-in-windows-azure.aspx) en el sitio Web de Microsoft
- La página de [proveedor de estado de sesión ASP.NET para Azure Redis caché](redis-cache/cache-aspnet-session-state-provider.md) en el sitio Web de Microsoft
- La página del [proveedor de caché de resultados ASP.NET para Azure Redis caché](redis-cache/cache-aspnet-output-cache-provider.md) en el sitio Web de Microsoft
- La página de [Introducción a Redis abstracciones y tipos de datos](http://redis.io/topics/data-types-intro) en el sitio Web Redis
- La página de [uso básico](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Basics.md) en el sitio Web de StackExchange.Redis
- La página de [transacciones en Redis](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Transactions.md) en la repo StackExchange.Redis
- La [Guía de particiones de datos](http://msdn.microsoft.com/library/dn589795.aspx) en el sitio Web de Microsoft

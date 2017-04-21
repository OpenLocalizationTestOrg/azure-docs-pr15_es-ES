<properties
   pageTitle="Diseño de aplicaciones de servicio tela capacidad | Microsoft Azure"
   description="Describe cómo identificar el número de nodos de cálculo necesarios para una aplicación de servicio tela"
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="markfuss"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/14/2016"
   ms.author="subramar"/>


# <a name="capacity-planning-for-service-fabric-applications"></a>Diseño de aplicaciones de servicio tela capacidad


Este documento le enseña a calcular la cantidad de recursos (CPU, RAM, almacenamiento en disco) que necesita para ejecutar las aplicaciones de Azure servicio tela. Es común para los requisitos de recursos cambiar a lo largo del tiempo. Normalmente, se requieren algunos recursos como desarrollar o probar el servicio y, a continuación, requieren más recursos como la aplicación crece en popularidad y vaya al entorno de producción. Al diseñar la aplicación, cree a través de los requisitos a largo plazo y tomar decisiones que permite que el servicio para satisfacer la demanda de los clientes alto.

 Cuando se crea un clúster de tela de servicio, puede decidir qué tipos de máquinas virtuales (VM) conforman el clúster. Cada VM viene con una cantidad limitada de recursos en el formulario de CPU (núcleos y velocidad), el ancho de banda de red, RAM y almacenamiento en disco. A medida que crece el servicio con el tiempo, puede actualizar a máquinas virtuales que ofrecen más recursos o agregar más VM a su clúster. Para ello, debe diseñar inicialmente su servicio de modo que puede aprovechar las ventajas de nuevas máquinas virtuales que se agregan dinámicamente al clúster.

Algunos servicios no administran poco o ningún datos en las máquinas virtuales a sí mismos. Por lo tanto, planificación capacidad para estos servicios debe centrarse principalmente en el rendimiento, lo que significa seleccionar las CPU adecuadas (núcleos y velocidad) de las máquinas virtuales. Además, debe tener en cuenta de ancho de banda de red, incluyendo la frecuencia con la que se producen transferencias de red y se transfiere la cantidad de datos. Si su servicio debe realizar así como aumenta el uso del servicio, puede agregar más VM del saldo clúster y carga de las solicitudes de red a través de todas las máquinas virtuales.

Para los servicios que administración grandes cantidades de datos en las máquinas virtuales, planificación de capacidad debe centrarse principalmente tamaño. Por lo tanto, debe considerar la capacidad de memoria RAM de la máquina virtual y almacenamiento en disco. El sistema de administración de memoria virtual de Windows asegura de espacio en disco aspecto RAM al código de aplicación. Además, la ejecución del servicio tela proporciona inteligente paginación mantener datos solo caliente en memoria y mover los datos y en el disco. Aplicaciones, por tanto, pueden usar más memoria de física disponible en la máquina virtual. Simplemente tener más memoria RAM aumenta el rendimiento, ya que la máquina virtual puede tener más espacio de almacenamiento de disco en RAM. La máquina virtual que seleccione debe tener un disco lo suficientemente grande como para almacenar los datos que desee en la máquina virtual. Del mismo modo, la máquina virtual debe tener suficiente memoria RAM para proporcionar el rendimiento que desee. Si los datos de su servicio crecen con el tiempo, puede agregar más VM al clúster y partición de los datos en todas las máquinas virtuales.

## <a name="determine-how-many-nodes-you-need"></a>Determinar el número de nodos que necesita

Su servicio de partición le permite escalar datos del servicio. Para obtener más información acerca de las particiones, vea [Partición tela de servicio](service-fabric-concepts-partitioning.md). Cada partición debe ajustarse dentro de una única VM, pero se pueden colocar varias particiones (pequeñas) en una única VM. Por lo tanto, tener más pequeñas particiones le ofrece mayor flexibilidad que tiene unas particiones más grandes. El equilibrio es que tiene una gran cantidad de particiones aumenta la sobrecarga de tela de servicio y no puede realizar operaciones de transacciones en todas las particiones. También es más tráfico de red posibles si el código de servicio con frecuencia necesita tener acceso a los datos que se encuentran en distintas particiones. Al diseñar el servicio, debe considerar estas ventajas y desventajas para llegar a una estrategia eficaz de particiones.

Supongamos que su aplicación tiene un único servicio con estado que tenga un tamaño de almacenamiento que espera aumentar para DB_Size GB de un año. Está dispuesto a agregar más aplicaciones (y particiones) como experiencia crecimiento más allá de ese año.  El factor de replicación (RN), que determina el número de réplicas para su servicio afecta la DB_Size total. El total DB_Size por todas las réplicas es el Factor de replicación multiplicado por DB_Size.  Node_Size representa el disco espacio/RAM por nodo que desea usar para el servicio. Para mejorar el rendimiento, la DB_Size debe caber en la memoria en el clúster y se deben elegir un Node_Size que hay alrededor de la memoria RAM de la máquina virtual. Al asignar un Node_Size que es mayor que la capacidad de memoria RAM, se basa en la paginación proporcionada por el tiempo de ejecución de tela de servicio. Por lo tanto, el rendimiento puede no ser óptimo si se consideran que los datos todo caliente (desde entonces los datos se paginan dentro/fuera). Sin embargo, para muchos servicios donde está caliente solo una parte de los datos, es más rentable.

El número de nodos necesario para obtener el máximo rendimiento puede se calcula como sigue:

```
Number of Nodes = (DB_Size * RF)/Node_Size

```


## <a name="account-for-growth"></a>Cuenta para el crecimiento

Desea calcular el número de nodos en función de la DB_Size que esperan su servicio para ganar, además de la DB_Size que comenzó con. A continuación, aumentar el número de nodos a medida que crece de su servicio de modo que son no exceso aprovisionamiento el número de nodos. Pero el número de particiones debe ser en función del número de nodos que son necesarios cuando se está ejecutando el servicio en crecimiento máximo.

Es conveniente tener algunos equipos adicionales disponibles en cualquier momento para que se puede controlar los picos inesperados o error (por ejemplo, si unas VM ir hacia abajo).  Mientras la capacidad adicional se debe determinar mediante los picos esperados, es un punto de partida reservar algunas máquinas virtuales adicionales (5-10 por ciento adicional).

Anterior, supone un único servicio con estado. Si tiene más de un servicio de estado, primero tiene que agregar el DB_Size asociado con los otros servicios en la ecuación. Como alternativa, puede calcular el número de nodos por separado para cada servicio de estado.  Su servicio puede tener réplicas o particiones que no están equilibradas. Tenga en cuenta que las particiones también pueden tener más datos de otros usuarios. Para obtener más información acerca de las particiones, vea [partición artículo acerca de prácticas recomendadas](service-fabric-concepts-partitioning.md). Sin embargo, la ecuación anterior es partición y réplica independiente, porque tela servicio asegura que las réplicas se extienden entre los nodos de manera optimizada.


## <a name="use-a-spreadsheet-for-cost-calculation"></a>Usar una hoja de cálculo para el cálculo del costo

Ahora vamos a poner algunos números reales en la fórmula. Una [hoja de cálculo de ejemplo](https://servicefabricsdkstorage.blob.core.windows.net/publicrelease/SF%20VM%20Cost%20calculator-NEW.xlsx) muestra cómo planear la capacidad para una aplicación que contiene tres tipos de objetos de datos. Para cada objeto, nos aproximar su tamaño y cuántos objetos esperamos que. Seleccionamos también cuántas réplicas queremos de cada tipo de objeto. La hoja de cálculo calcula la cantidad total de memoria almacenarse en el clúster.

Introduzca un tamaño de memoria virtual y el coste mensual. En función del tamaño de la máquina virtual, la hoja de cálculo le indica el número mínimo de particiones que debe utilizar para dividir los datos y ajustar física de los nodos. Necesidades de tráfico de red y desea un mayor número de particiones para acomodar el cálculo específicas de la aplicación. La hoja de cálculo muestra el número de particiones que administra los objetos de perfil de usuario ha aumentado de uno a seis.

Ahora, en función de toda esta información, la hoja de cálculo muestra que podría obtener física todos los datos con la que quiera particiones y réplicas en un clúster de nodo de 26. Sin embargo, este clúster podría se muy empaquetar, así que es aconsejable algunos nodos adicionales para adaptarse a las actualizaciones y los errores de nodo. La hoja de cálculo también muestra que tiene más de 57 nodos no proporciona ningún valor adicional porque tendría nodos vacíos. De nuevo, desea ir encima 57 nodos de todos modos para acomodar actualizaciones y errores en los nodos. Puede ajustar la hoja de cálculo para que coincida con las necesidades específicas de la aplicación.   

![Hoja de cálculo para el cálculo del costo][Image1]



## <a name="next-steps"></a>Pasos siguientes

Desproteger [partición tela de servicio servicios] [ 10] para obtener más información sobre el servicio de partición.



<!--Image references-->
[Image1]: ./media/SF-Cost.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-concepts-partitioning.md

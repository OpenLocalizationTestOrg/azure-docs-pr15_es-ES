<properties
   pageTitle="Arquitectura de servicio tela | Microsoft Azure"
   description="Estructura de servicio es una plataforma de sistemas distribuidos que se usa para generar scalable, confiables y fácil de administrar aplicaciones para la nube. En este artículo se muestra la arquitectura de tela de servicio."
   services="service-fabric"
   documentationCenter=".net"
   authors="rishirsinha"
   manager="timlt"
   editor="rishirsinha"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="06/09/2016"
   ms.author="rsinha"/>

# <a name="service-fabric-architecture"></a>Arquitectura de tela de servicio

Tela de servicio integrado con subsistemas superpuestas. Estos subsistemas permiten escribir aplicaciones que son:

* Altamente disponible
* Scalable
* Manejables
* Puede probar

El siguiente diagrama muestra los principales subsistemas de tela de servicio.

![Diagrama de arquitectura de tela de servicio](media/service-fabric-architecture/service-fabric-architecture.png)

En un sistema distribuido, la capacidad de comunicarse de forma segura entre los nodos en un clúster es crucial. En la base de la pila es el subsistema de transporte, que ofrece una comunicación segura entre los nodos. Encima del transporte subsistema encuentra en el subsistema de federación, que clústeres los distintos nodos en una única entidad (denominada clústeres) para que puedan detectar errores, realizar elecciones de relleno y proporcionar enrutamiento coherente tela de servicio. El subsistema de confiabilidad superpuesto el subsistema de federación, es responsable de la confiabilidad de los servicios de tela de servicio a través de mecanismos como replicación, administración de recursos y migración tras error. El subsistema de federación subyacente también el subsistema de hospedaje y la activación, que administra el ciclo de vida de una aplicación en un único nodo. El subsistema de administración administra el ciclo de vida de aplicaciones y servicios. El subsistema de capacidad de prueba ayuda a los desarrolladores de aplicaciones de prueba sus servicios por errores simuladas antes y después de implementar aplicaciones y servicios en entornos de producción. Servicio tela proporciona la capacidad para resolver las ubicaciones de servicio a través de su subsistema de comunicaciones. Los modelos de programación de aplicación expuestos a los desarrolladores se colocan en la parte superior estos subsistemas junto con el modelo de aplicación para habilitar las herramientas.

## <a name="transport-subsystem"></a>Subsistema de transporte
El subsistema de transporte implementa un canal de comunicación de punto a punto datagrama. Este canal se usa para la comunicación dentro de clústeres tela de servicio y la comunicación entre el clúster de tela de servicio y clientes. Admite unidireccional y patrones de comunicación de respuesta de la solicitud, que proporciona la base para implementar ambas en la capa de federación. El subsistema de transporte protege comunicación mediante X509 certificados o la seguridad de Windows. Este subsistema utiliza internamente tela de servicio y no es accesible directamente a los desarrolladores de programación de aplicaciones.

## <a name="federation-subsystem"></a>Subsistema de federación
Con el fin de razón sobre un conjunto de nodos en un sistema distribuido, debe tener una vista coherente del sistema. El subsistema de federación usa a la los fundamentos de comunicación por el subsistema de transporte y une varios nodos en un único clúster unificado que puede razón de sobre. Proporciona a los fundamentos de sistemas distribuidos necesarios otros subsistema - detección de errores, elección de relleno y enrutamiento coherentes. El subsistema de federación se basa en tablas de hash distribuida con un espacio de token de 128 bits. El subsistema crea una topología de anillo por los nodos con cada nodo en el menú llamar está asignado un subconjunto del token espacio para la propiedad. Para la detección de error, la capa usa un mecanismo financiero basado en latido del corazón y arbitraje. El subsistema de federación también garantiza a través de combinación compleja y protocolos de salida que solo un único propietario de un token existe en cualquier momento. Esto proporcionan elecciones de relleno y las garantías de enrutamiento coherentes.

## <a name="reliability-subsystem"></a>Subsistema de confiabilidad
El subsistema de confiabilidad proporciona el mecanismo para hacer que el estado de un servicio de servicio tela altamente disponibles mediante el uso de la _Replicator_, _Administrador de migración tras error_y _Equilibrador de recursos_.

* El replicador garantiza que los cambios de estado de la réplica de servicio principal se reflejarán automáticamente a réplicas secundarias, mantener la coherencia entre las réplicas primaria y secundarias en un conjunto de réplica de servicio. El Replicador es responsable de la administración de quórum entre las réplicas del conjunto de réplicas. Interactúa con la unidad de migración tras error para obtener la lista de operaciones para replicar y, a continuación, el agente de la nueva configuración proporciona con la configuración del conjunto de réplica. Esta configuración indica qué réplicas las operaciones necesitan replicarse. Servicio tela proporciona un replicador predeterminado denominado tela Replicator, que puede ser usada por la API de modelo de programación para hacer que el estado del servicio altamente disponible y confiable.
* El Administrador de migración tras error asegura que cuando se agregan nodos o se quita del clúster, la carga se redistribuye automáticamente a través de los nodos disponibles. Si se produce un error en un nodo del clúster, el clúster automáticamente a configurar las réplicas de servicio para mantener la disponibilidad.
* El Administrador de recursos coloca réplicas de servicio a través de dominio de error en el clúster y garantiza que todas las unidades de migración tras error están en funcionamiento. El Administrador de recursos también equilibra recursos de servicio en el grupo compartido subyacente de nodos de clúster para lograr la distribución de carga uniforme óptima.

## <a name="management-subsystem"></a>Subsistema de administración
El subsistema de administración proporciona servicio de llevar a cabo y administración de ciclo de vida de aplicaciones. Cmdlets de PowerShell y las API administrativas permiten aprovisionar, implementar, revisiones, actualizar y aprovisionar anule la aplicaciones sin pérdida de disponibilidad. El subsistema de administración realiza a través de los siguientes servicios.

* **Administrador de clúster**: este es el servicio principal que interactúa con la migración tras error Manager de confiabilidad para colocar las aplicaciones en los nodos en función de las restricciones de posición de servicio. El Administrador de recursos en el subsistema de migración tras error garantiza que las restricciones nunca se interrumpen. El Administrador de clúster administra el ciclo de vida de las aplicaciones a disposición proporcionando anule la. Se integra con el Administrador de estado para asegurar que disponibilidad de la aplicación no se pierden desde una perspectiva de salud semántico durante las actualizaciones.
* **Administrador de estado**: este servicio permite la supervisión de estado de las entidades de clúster, servicios y aplicaciones. Entidades de clúster (como nodos, las particiones del servicio y réplicas) pueden notificar la información de estado, que se agrega a continuación, en la tienda de salud centralizada. La información de estado proporciona una instantánea de mantenimiento de total en el momento de los servicios y los nodos distribuidos entre varios nodos en el clúster, lo que le permite tomar las medidas correctivas necesarias. API permiten consultar los eventos de estado de consulta de estado informa al subsistema de estado. La consulta de mantenimiento API devuelven los datos de estado sin formato almacenados en el almacén de estado o agregado, interpreta los datos de estado de una entidad de clúster específico.
* **Imagen de la tienda**: este servicio proporciona almacenamiento y distribución de los archivos binarios. Este servicio proporciona un almacén de archivos distribuido simple donde son cargadas y descargadas desde las aplicaciones.


## <a name="hosting-subsystem"></a>Subsistema de hospedaje
El Administrador de clúster informa el subsistema de hospedaje (que se ejecuta en cada nodo), los servicios que necesita para administrar para un nodo determinado. El subsistema de hospedaje administra el ciclo de vida de la aplicación en ese nodo. Interactúa con los componentes de la confiabilidad y la salud para asegurarse de que las réplicas estén colocadas correctamente y que están en buen estadas.

## <a name="communication-subsystem"></a>Subsistema de comunicación
Este subsistema proporciona mensajería confiable en la detección de clúster y servicio a través del servicio de nombres. El servicio de nombres resuelve los nombres de servicio en una ubicación en el clúster y permite a los usuarios administrar propiedades y nombres de servicio. Usando el servicio de nombres, clientes de forma segura pueden comunicarse con cualquier nodo del clúster para resolver un nombre de servicio y recuperar metadatos del servicio. Con un cliente de nomenclatura simple API, los usuarios del servicio tela pueden desarrollar servicios y clientes capaces de resolver la ubicación de red actual a pesar de dinamismo de nodo o el cambio de tamaño del clúster.

## <a name="testability-subsystem"></a>Subsistema de capacidad de prueba
Capacidad de prueba es un conjunto de herramientas diseñadas específicamente para probar servicios basados en tela de servicio. Las herramientas permiten un desarrollador fácilmente provocar errores significativos y ejecutar escenarios de prueba para ejercicio y validar los numerosos Estados y transiciones que experimentará un servicio a lo largo de su duración, todo en forma controlada y segura. Capacidad de prueba también proporciona un mecanismo para ejecutar las pruebas más larga que pueden establecer una iteración a través de varias posibles errores sin perder la disponibilidad. Esto le proporciona un entorno de prueba de producción.

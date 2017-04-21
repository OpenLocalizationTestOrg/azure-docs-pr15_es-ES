<properties
pageTitle="Cómo actualizar un servicio de nube | Microsoft Azure"
description="Obtenga información sobre cómo actualizar servicios en la nube en Azure. Obtenga información sobre cómo se realiza una actualización en un servicio de nube para garantizar la disponibilidad."
services="cloud-services"
documentationCenter=""
authors="Thraka"
manager="timlt"
editor=""/>
<tags
ms.service="cloud-services"
ms.workload="tbd"
ms.tgt_pltfrm="na"
ms.devlang="na"
ms.topic="article"
ms.date="08/10/2016"
ms.author="adegeo"/>

# <a name="how-to-update-a-cloud-service"></a>Cómo actualizar un servicio de nube

## <a name="overview"></a>Información general
A 10.000 pies, la actualización de un servicio de nube, incluidas sus funciones y el invitado OS, es un proceso de tres pasos. En primer lugar, se deben cargar los archivos binarios y configuración para el nuevo servicio de nube o la versión del SO. A continuación, Azure reserva de recursos de cálculo y de red para el servicio de nube basándose en los requisitos de la nueva versión de servicio de nube. Por último, Azure realiza una actualización sucesiva para actualizar incrementalmente el inquilino a la nueva versión o sistema operativo, invitado conservando su disponibilidad. En este artículo se describe los detalles de este último paso: la actualización sucesiva.

## <a name="update-an-azure-service"></a>Actualizar un servicio de Azure

Azure organiza las instancias de la función en grupos lógicos denominados dominios de actualización (Ud.). Dominios de actualización (Ud.) son conjuntos lógicos de instancias de la función que se actualizan como un grupo.  Azure actualiza una nube servicio uno Ud. a la vez, lo que permite instancias en otros UDs para continuar servir el tráfico.

El número predeterminado de dominios actualización es 5. Puede especificar un número diferente de actualización dominios incluyendo el atributo upgradeDomainCount en el archivo de definición del servicio (.csdef). Para obtener más información sobre el atributo upgradeDomainCount, vea [WebRole esquema](https://msdn.microsoft.com/library/azure/gg557553.aspx) o [WorkerRole esquema](https://msdn.microsoft.com/library/azure/gg557552.aspx).

Al realizar una actualización en lugar de una o varias funciones en el servicio, Azure actualiza conjuntos de instancias de la función según actualizar el dominio al que pertenecen. Actualizaciones de Azure todas las instancias de un dominio de actualización dado: detener, actualizándolos reunirlos realizar copias en línea se mueve en el dominio siguiente. Si detiene únicamente las instancias que se ejecuta en el dominio de actualización actual, Azure se asegura de que se produce una actualización con el menor impacto posible el servicio en ejecución. Para obtener más información, vea [¿cómo se realiza la actualización](#howanupgradeproceeds) más adelante en este artículo.

> [AZURE.NOTE] Aunque los términos **Actualizar** y **Actualizar** tienen significado ligeramente diferente en el contexto de Azure, pueden utilizarse indistintamente para los procesos y las descripciones de las características de este documento.

Su servicio debe definir al menos dos instancias de una función para ese rol actualizarse en el lugar sin tiempo de inactividad. Si el servicio consta de una única instancia de una función, su servicio estará disponible hasta que haya terminado la actualización en contexto.

Este tema trata sobre la siguiente información sobre las actualizaciones de Azure:

-   [Permite cambios de servicio durante una actualización](#AllowedChanges)
-   [¿Cómo se lleva a cabo en una actualización](#howanupgradeproceeds)
-   [Deshacer una actualización](#RollbackofanUpdate)
-   [Iniciar varias operaciones mutantes en una implementación en curso](#multiplemutatingoperations)
-   [Distribución de roles a través de dominios de actualización](#distributiondfroles)

<a name="AllowedChanges"></a>
## <a name="allowed-service-changes-during-an-update"></a>Permite cambios de servicio durante una actualización
La siguiente tabla muestra los cambios permitidos a un servicio durante una actualización:

|Cambios permitidos de hospedaje, servicios y funciones|Actualización en contexto|Preconfigurada (intercambiar VIP)|Eliminar y volver a implementar|
|---|---|---|---|
|Versión del sistema operativo|Sí|Sí|Sí
|Nivel de confianza de .NET|Sí|Sí|Sí|
|Tamaño de máquina virtual<sup>1</sup>|Sí<sup>2</sup>|Sí|Sí|
|Configuración de almacenamiento local|Aumentar solo<sup>2</sup>|Sí|Sí|
|Agregar o quitar roles de un servicio|Sí|Sí|Sí|
|Número de instancias de una función en particular|Sí|Sí|Sí|
|Número o tipo de extremos de un servicio|Sí<sup>2</sup>|No|Sí|
|Nombres y valores de configuración|Sí|Sí|Sí|
|Valores (pero no nombres) de la configuración|Sí|Sí|Sí|
|Agregar nuevos certificados|Sí|Sí|Sí|
|Cambiar los certificados existentes|Sí|Sí|Sí|
|Implementar código nuevo|Sí|Sí|Sí|
<sup>1</sup> Cambio de tamaño limitado al subconjunto de tamaños disponibles para el servicio de nube.

<sup>2</sup> Requiere Azure SDK 1.5 o versiones posteriores.

> [AZURE.WARNING] Cambiar el tamaño de la máquina virtual se destruyen datos locales.


Los siguientes elementos no se admiten durante una actualización:

-   Cambiar el nombre de una función. Quitar y, a continuación, agregue la función con el nuevo nombre.
-   Cambiar del recuento de actualización del dominio.
-   Disminuir el tamaño de los recursos locales.

Para realizar otras actualizaciones a la definición del servicio, como reducir el tamaño del recurso local, debe realizar una actualización de intercambio VIP en su lugar. Para obtener más información, vea [Implementación intercambiar](https://msdn.microsoft.com/library/azure/ee460814.aspx).

<a name="howanupgradeproceeds"></a>
## <a name="how-an-upgrade-proceeds"></a>¿Cómo se lleva a cabo en una actualización
Puede decidir si desea actualizar todas las funciones en el servicio o un único rol en el servicio. En cualquier caso, todas las instancias de cada función que se está actualizando y que pertenecen a la primera actualización del dominio se detenido, actualizadas y vuelva a estar en línea. Una vez volver a conectarse, las instancias en el segundo dominio de actualización son detenidas, actualizadas y vuelva a estar en línea. Un servicio de nube puede tener como máximo una actualización de activa en un momento. La actualización se realiza siempre con la versión más reciente del servicio de nube.

El siguiente diagrama muestra cómo se realiza la actualización si está actualizando todas las funciones en el servicio de:

![Actualizar el servicio] (media/cloud-services-update-azure-service/IC345879.png "Actualizar el servicio")

Este diagrama siguiente muestra cómo se realiza la actualización si está actualizando una sola función:

![Función de actualización] (media/cloud-services-update-azure-service/IC345880.png "Función de actualización")  

> [AZURE.NOTE] Al actualizar un servicio de una sola instancia a varias instancias del servicio se interrumpa mientras se realiza la actualización debido a los servicios de Azure actualizaciones de forma. La disponibilidad de servicio garante de acuerdo de nivel de servicio solo se aplica a los servicios que se implementan con más de una instancia. La siguiente lista describe cómo afecta los datos de cada unidad por cada escenario de actualización de servicio de Azure:
>
>Reinicio de la máquina virtual:
>
-   C: conservan
-   D: conservan
-   E: conservan
>
>Reinicie portal:
>
-   C: conservan
-   D: conservan
-   E: destruye
>
>Portal crear una nueva imagen:
>
-   C: conservan
-   D: destruido
-   E: destruye

>Actualización en contexto:
>
-   C: conservan
-   D: conservan
-   E: destruye
>
>Migración de nodo:
>
-   C: destruido
-   D: destruido
-   E: destruye

>Tenga en cuenta que, en la lista anterior, la unidad E: representa la unidad de raíz de la función y no debe ser modificable. En su lugar, use la variable de entorno % RoleRoot % para representar la unidad.

>Para reducir el tiempo de inactividad al actualizar un servicio de instancia única, implementar un nuevo servicio de instancias múltiples en el servidor de ensayo y realizar un intercambio VIP.

Durante una actualización automática, el controlador de tela Azure evalúa periódicamente el estado del servicio de nube para determinar cuándo es seguro recorrer la Ud. siguiente. Esta evaluación de mantenimiento se realiza por rol y considera sólo instancias en la última versión (es decir, instancias de UDs que ya han sido recorrer). Comprueba que un número mínimo de instancias de la función, para cada función, ha alcanzado un buen estado terminal.

### <a name="role-instance-start-timeout"></a>Tiempo de espera de inicio de instancia de funciones
El controlador de tela esperará 30 minutos para cada instancia de rol llegar a un estado de iniciado. Si transcurre la duración del tiempo de espera, el controlador de tela seguirá algunos hasta la siguiente instancia de rol.

<a name="RollbackofanUpdate"></a>
## <a name="rollback-of-an-update"></a>Deshacer una actualización
Azure ofrece mayor flexibilidad en la administración de servicios durante una actualización que le permite iniciar operaciones adicionales en un servicio después de que se acepte la solicitud de actualización inicial por el controlador de tela de Azure. Sólo puede realizar una operación de deshacer cuando una actualización (cambio de configuración) o actualización está en el estado **en curso** en la implementación. Una actualización o la actualización se considera en curso siempre que hay al menos una instancia del servicio que aún no se ha actualizado a la versión nueva. Para comprobar si se permite una operación de deshacer, compruebe el valor de la marca de RollbackAllowed devuelta por las operaciones de [Obtener implementación](https://msdn.microsoft.com/library/azure/ee460804.aspx) y [Obtener propiedades de servicio de nube](https://msdn.microsoft.com/library/azure/ee460806.aspx) , se establece en true.

> [AZURE.NOTE] Solo tiene sentido llamar a deshacer a una actualización **en contexto** o actualizar debido a las actualizaciones de intercambio de VIP implican reemplazar una instancia de ejecución completa de su servicio por otra.

Deshacer una actualización en curso tiene los siguientes efectos en la implementación:

-   Las instancias de cualquier función que habían no se ha actualizado o actualizado a la nueva versión no se actualizan o actualizadas, porque esos casos en los que ya están ejecutando una versión de destino del servicio.
-   Las instancias de cualquier función que tenían ya se han actualizado o actualizado a la nueva versión del paquete de servicio (\*.cspkg) archivo o la configuración del servicio (\*.cscfg) archivo (o ambos archivos) se ha vuelto a la versión anterior a la actualización de estos archivos.

Esta funcionalidad proporciona las siguientes características:

-   La operación [Deshacer actualizar o actualizar](https://msdn.microsoft.com/library/azure/hh403977.aspx) , que se puede llamar en una actualización de la configuración (activado a llamar al [Cambiar la configuración de implementación](https://msdn.microsoft.com/library/azure/ee460809.aspx)) o una actualización (activado llamada [Implementación actualizar](https://msdn.microsoft.com/library/azure/ee460793.aspx)) siempre que hay al menos una instancia en el servicio que aún no se ha actualizado a la versión nueva.
-   El elemento bloqueada y el elemento RollbackAllowed, que se devuelven como parte del cuerpo de la respuesta de las operaciones [Obtener implementación](https://msdn.microsoft.com/library/azure/ee460804.aspx) y [Obtener propiedades de servicio de nube](https://msdn.microsoft.com/library/azure/ee460806.aspx) :
    1.  El elemento bloqueada permite detectar cuando una operación de mutantes se puede invocar en una implementación dada.
    2.  El elemento RollbackAllowed le permite detectar cuando la operación [Deshacer actualización o actualización](https://msdn.microsoft.com/library/azure/hh403977.aspx) puede llamar en una implementación dada.

    Para realizar una operación de deshacer, no tiene que comprobar la bloqueada y los elementos de RollbackAllowed. Es suficiente con para confirmar que RollbackAllowed está establecida en true. Estos elementos se devuelven sólo si invoca estos métodos usando el encabezado de solicitud establecido en "versión de ms x: 2011-10-01" o una versión posterior. Para obtener más información acerca de los encabezados de control de versiones, vea [Versiones de administración de servicios](https://msdn.microsoft.com/library/azure/gg592580.aspx).

Hay algunas situaciones en las que una operación de deshacer de una actualización o no se admite la actualización, estos son los siguientes:

-   Reducción de los recursos locales - si la actualización aumenta los recursos locales para una función de la plataforma Windows Azure no permite deshacer. 
-   Limitaciones de cuota - si la actualización se ha una escala hacia abajo de la operación, puede que no tiene suficiente cuota de cálculo para completar la operación de deshacer. Cada suscripción Azure tiene una cuota asociada que especifica el número máximo de núcleos que puede ser utilizado por todos los servicios alojados que pertenecen a la suscripción. Si realizar una operación de deshacer de una actualización determinada pone la suscripción a través de cuota, a continuación, no se habilitarán deshacer.
-   Condición de anticipación - si ha finalizado la actualización inicial, no es posible deshacer.

Un ejemplo de cuando la restauración de una actualización puede resultar útil es si está utilizando la operación de [Implementación de actualización](https://msdn.microsoft.com/library/azure/ee460793.aspx) en el modo manual para controlar la velocidad a la que una actualización en contexto importante su Azure había hospedado servicio se despliegan las en.

Durante la ejecución de la actualización de llamar a la [Implementación de actualización](https://msdn.microsoft.com/library/azure/ee460793.aspx) en modo manual y comenzar a recorrer la actualización de dominios. Si en algún momento, como supervisar la actualización, observe han responda algunas instancias de la función en la primera dominios de actualización que examinar, puede llamar a la operación de [Deshacer actualizar o actualizar](https://msdn.microsoft.com/library/azure/hh403977.aspx) en la implementación, que deja intacto el instancias que habían no se han actualizado y deshacer que había ha actualizado a la anterior paquete de servicio y la configuración.

<a name="multiplemutatingoperations"></a>
## <a name="initiating-multiple-mutating-operations-on-an-ongoing-deployment"></a>Iniciar varias operaciones mutantes en una implementación en curso
En algunos casos desee iniciar múltiples operaciones mutantes simultáneas en una implementación en curso. Por ejemplo, puede realizar una actualización de servicio y, mientras se está ejecutando dicha actualización a través de su servicio, que desea realizar algún cambio, por ejemplo, para dar a la actualización atrás, aplicar una actualización diferente o incluso eliminar la implementación. Un caso en el que esto podría ser necesario es si una actualización de servicio contiene código con errores que hace que una instancia de rol actualizado que varias veces se bloquea. En este caso, el controlador de tela Azure no pueda progresar en la aplicación que actualizar porque existen un número insuficiente de instancias del dominio actualizado. Este estado se conoce como una *detiene la implementación*. Puede desbloquear la implementación deshaciendo la actualización o aplicando una actualización actualizada sobre la parte superior de la falta de uno.

Una vez que la solicitud inicial para actualizar o actualizar el servicio ha recibido por el controlador de tela de Azure, puede iniciar las operaciones siguientes mutantes. Es decir, no tiene que esperar para que la operación inicial completar antes de iniciar otra operación mutantes.

Iniciar una segunda operación de actualización mientras se está realizando la primera actualización realizará similar a la operación de deshacer. Si la segunda actualización está en modo automático, la actualización del primer dominio se actualizarán inmediatamente, posiblemente lo que lleva a instancias de varios dominios de actualización que se está sin conexión en el mismo punto en el tiempo.

Las operaciones mutantes son las siguientes: [Cambiar la configuración de implementación](https://msdn.microsoft.com/library/azure/ee460809.aspx), [Implementación de actualización](https://msdn.microsoft.com/library/azure/ee460793.aspx), [Estado de implementación de actualización](https://msdn.microsoft.com/library/azure/ee460808.aspx), [Implementación de eliminar](https://msdn.microsoft.com/library/azure/ee460815.aspx)y [Deshacer actualizar o actualizar](https://msdn.microsoft.com/library/azure/hh403977.aspx).

Dos operaciones, [Obtener implementación](https://msdn.microsoft.com/library/azure/ee460804.aspx) y [Obtener propiedades de servicio de nube](https://msdn.microsoft.com/library/azure/ee460806.aspx), vuelva a la marca bloqueada que se puede examinar para determinar si se puede llamar a una operación de mutantes en una implementación dada.

Para llamar a la versión de estos métodos que devuelve el indicador bloqueada, debe establecer el encabezado de la solicitud "versión de ms x: 2011-10-01" o una posterior. Para obtener más información acerca de los encabezados de control de versiones, vea [Versiones de administración de servicios](https://msdn.microsoft.com/library/azure/gg592580.aspx).

<a name="distributiondfroles"></a>
## <a name="distribution-of-roles-across-upgrade-domains"></a>Distribución de roles a través de dominios de actualización
Azure distribuye instancias de una función de manera uniforme en un determinado número de dominios de actualización, que pueden configurarse como parte del archivo de definición (.csdef) de servicio. El número máximo de dominios de actualización es 20 y el valor predeterminado es 5. Para obtener más información sobre cómo modificar el archivo de definición de servicio, vea [El esquema de definición del servicio de Azure (.csdef archivo)](cloud-services-model-and-package.md#csdef).

Por ejemplo, si su rol tiene diez instancias, de forma predeterminada cada dominio de actualización contiene dos instancias. Si su rol tiene 14 instancias, a continuación, cuatro de los dominios de actualización contienen tres instancias y un dominio quinto contiene dos.

Actualización dominios identificados con un índice basado en cero: actualizar el primer dominio tiene un identificador de 0, y actualizar el segundo dominio tiene un identificador de 1 y así sucesivamente.

El siguiente diagrama muestra cómo un servicio de dos funciones que contiene se distribuyen cuando el servicio define dos dominios de actualización. El servicio está ejecutando ocho instancias de la función web y nueve de la función de trabajo.

![Distribución de dominios de actualización] (media/cloud-services-update-azure-service/IC345533.png "Distribución de dominios de actualización")

> [AZURE.NOTE] Tenga en cuenta que Azure controla cómo se asignan los casos en los dominios de actualización. No es posible especificar qué casos en los que están asignados a los dominios.

## <a name="next-steps"></a>Pasos siguientes
[Cómo administrar servicios en la nube](cloud-services-how-to-manage.md)  
[Cómo supervisar los servicios de nube](cloud-services-how-to-monitor.md)  
[Cómo configurar los servicios de nube](cloud-services-how-to-configure.md)  

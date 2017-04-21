<properties
    pageTitle="Replicación de almacenamiento de Azure | Microsoft Azure"
    description="Datos en su cuenta de Microsoft Azure almacenamiento es replicar para alta disponibilidad y la duración. Opciones de replicación incluyen almacenamiento redundante local (LRS), almacenamiento zona redundantes (ZRS), almacenamiento geo redundantes (GRS) y almacenamiento de acceso de lectura geo redundantes (RA GRS)."
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="tamram"/>

# <a name="azure-storage-replication"></a>Replicación de almacenamiento de Azure

Los datos de su cuenta de Microsoft Azure almacenamiento siempre se duplica para garantizar alta disponibilidad y la duración. Duplicación de copia de los datos, dentro del mismo centro de datos o a un segundo centro de datos, dependiendo de la opción de replicación que elija. Replicación protege los datos y conserva el tiempo de la aplicación en caso de errores de hardware transitorias. Si los datos se repliquen en un segundo centro de datos, también protege los datos frente a un error grave en la ubicación principal.

La replicación garantiza que su cuenta de almacenamiento cumple con el [Contrato de nivel de servicio (SLA) para el almacenamiento](https://azure.microsoft.com/support/legal/sla/storage/) incluso en el caso de errores. Vea que el SLA para obtener información sobre el almacenamiento de Azure garantías de disponibilidad y la duración. 

Cuando se crea una cuenta de almacenamiento, puede seleccionar una de las siguientes opciones de replicación:  

- [Almacenamiento redundante local (LRS)](#locally-redundant-storage)
- [Almacenamiento de zona redundantes (ZRS)](#zone-redundant-storage)
- [Almacenamiento geo redundantes (GRS)](#geo-redundant-storage)
- [Almacenamiento de acceso de lectura geo redundantes (RA GRS)](#read-access-geo-redundant-storage)

Almacenamiento de acceso de lectura geo redundantes (RA GRS) es la opción predeterminada cuando se crea una nueva cuenta de almacenamiento.

La tabla siguiente proporciona una descripción general de las diferencias entre LRS, ZRS, GRS y RA-GRS, mientras que las secciones siguientes tratan cada tipo de replicación con más detalle.

| Estrategia de replicación                                                               | LRS | ZRS | GRS | GRS RA |
|:----------------------------------------------------------------------------------|:---|:---|:---|:------|
| Datos es replicar en varios centros de datos.                                     | No  | Sí | Sí | Sí    |
| Pueden leer datos desde la ubicación secundaria y desde la ubicación principal. | No  | No  | No  | Sí    |
| Número de copias de los datos que se mantienen en nodos independientes.                             | 3   | 3   | 6   | 6      |

Consulte [Precios de Azure almacenamiento](https://azure.microsoft.com/pricing/details/storage/) de información de las opciones de redundancia diferentes precios.

>[AZURE.NOTE] Almacenamiento de Premium admite el almacenamiento redundante solo local (LRS). Para obtener información sobre el almacenamiento de Premium, vea [almacenamiento Premium: almacenamiento de alto rendimiento para cargas de trabajo de máquina Virtual de Azure](storage-premium-storage.md).

## <a name="locally-redundant-storage"></a>Almacenamiento redundante local

Almacenamiento redundante local (LRS) aplica los datos tres veces dentro de una unidad de escala de almacenamiento que está alojado en un centro de datos en la región en la que se creó su cuenta de almacenamiento. Una solicitud de escritura correctamente devuelve solo una vez que se ha escrito en las tres réplicas. Estas tres réplicas residen en dominios de errores independiente y actualización dentro de la unidad de almacenamiento de una escala. 

Una unidad de escala de almacenamiento es una colección de bastidores de nodos de almacenamiento. Un dominio de error (FD) es un grupo de nodos que representan una unidad física de error y se puede considerar como nodos que pertenecen a la misma bastidor físico. Un dominio de actualización (Ud.) es un grupo de nodos que se han actualizado juntos durante el proceso de una actualización de servicio (lanzamiento). Las tres réplicas se extienden a través de UDs y FDs dentro de una unidad de escala de almacenamiento para asegurarse de que los datos están disponibles, incluso si el error de hardware afecta a un único bastidor o cuando se actualizan los nodos durante un despliegue. 

LRS es la opción de costo más baja y ofrece menos duración en comparación con las otras opciones. En caso de un desastre nivel de centro de datos (fire, saturación etc.) las tres réplicas posible perdidos o no recuperable. Para reducir el riesgo Geo redundantes almacenamiento (GRS) se recomienda para la mayoría de las aplicaciones.

Almacenamiento redundante local aún puede ser conveniente en determinadas situaciones: 

- Proporciona mayor ancho de banda máximo de opciones de replicación de almacenamiento de Azure.

- Si su aplicación almacena los datos que se pueden reconstruir fácilmente, puede optar por LRS.

- Algunas aplicaciones están limitadas a replicar datos solo dentro de un país debido a los requisitos de control de datos. Puede ser una región pareja en otro país; Para obtener información sobre los pares de región, consulte [regiones Azure](https://azure.microsoft.com/regions/) .

## <a name="zone-redundant-storage"></a>Almacenamiento de zona redundantes

Almacenamiento de zona redundantes (ZRS) aplica los datos de forma asincrónica en centros de datos dentro de una o dos regiones, además de almacenar tres réplicas similares a LRS, lo que proporciona mayor duración que LRS. Datos almacenados en ZRS son resistentes, incluso si el centro de datos principal no está disponible o no recuperable.
Los clientes que plan usar ZRS deben tener en cuenta que: 

- ZRS solo está disponible para blobs de bloque de cuentas de almacenamiento de propósito general y se admite únicamente en las versiones de servicio de almacenamiento de 2014-02-14 y versiones posteriores. 

- Como la replicación asincrónica implica un retraso, en caso de desastre local es posible que los cambios que aún no se ha duplicado a la secundaria se perderán si no se puede recuperar los datos de la principal.

- La réplica no esté disponible hasta que Microsoft inicia la migración tras error a la secundaria.

- No se puede convertir de cuentas ZRS LRS o GRS más adelante. Del mismo modo, no se puede convertir una cuenta LRS o GRS existente a una cuenta ZRS.

- Cuentas ZRS no tiene métricas o capacidad de registro. 

## <a name="geo-redundant-storage"></a>Almacenamiento geo redundantes

Almacenamiento geo redundantes (GRS) aplica los datos a una zona secundaria cientos de millas fuera de la región principal. Si su cuenta de almacenamiento tiene GRS habilitada, los datos son resistentes incluso en el caso de una interrupción regional completa o un desastre en el que el área principal no es recuperable.

Para una cuenta de almacenamiento con GRS habilitado, una actualización en primer lugar se compromete a la región principal, donde crear la réplica tres veces. A continuación, la actualización se duplica asincrónica en la región secundaria, donde también se replicar tres veces. 

Con GRS las regiones primaria y secundarias administración réplicas a través de dominios de error independiente y actualización los dominios dentro de una unidad de escala de almacenamiento como se describe con LRS.

Consideraciones:

- Como la replicación asincrónica implica un retraso, en caso de desastre regional es posible que los cambios que aún no se ha duplicado a la región secundaria se perderán si no se puede recuperar los datos de la región principal.

- La réplica no está disponible a menos que Microsoft inicia la migración tras error a la región secundaria.

- Si una aplicación desea leer de la región secundaria el usuario debe habilitar RA GRS. 

Cuando se crea una cuenta de almacenamiento, seleccione la región principal de la cuenta. La región secundaria se determina en función de la región principal y no se puede cambiar. La siguiente tabla muestra los pares de región primario y secundario.

| Principal             | Secundario           |
|---------------------|---------------------|
| Norte Central de EE.    | Sur Central de EE.    |
| Sur Central de EE.    | Norte Central de EE.    |
| Estados Unidos oriental             | Estados Unidos occidental             |
| Estados Unidos occidental             | Estados Unidos oriental             |
| Estados Unidos oriental 2           | Central de EE.          |
| Central de EE.          | Estados Unidos oriental 2           |
| Europa del Norte        | Europa occidental         |
| Europa occidental         | Europa del Norte        |
| Sudeste asiático     | Asia oriental           |
| Asia oriental           | Sudeste asiático     |
| China oriental          | Norte de China         |
| Norte de China         | China oriental          |
| Japón oriental          | Japón oeste          |
| Japón oeste          | Japón oriental          |
| Sur de Brasil        | Sur Central de EE.    |
| Australia Oriental      | Australia sureste |
| Australia sureste | Australia Oriental      |
| India sur         | India Central       |
| India Central       | India sur         |
| Estados Unidos Gov Iowa         | Estados Unidos Gov Virginia     |
| Estados Unidos Gov Virginia     | Estados Unidos Gov Iowa         |
| Canadá Central      | Canadá oriental          |
| Canadá oriental         | Canadá Central      |
| Reino Unido oeste             | Sur Reino Unido            |
| Sur Reino Unido            | Reino Unido oeste             |
| Alemania Central     | Alemania nordeste   |
| Alemania nordeste   | Alemania Central     |
| Estados Unidos oeste 2           | Central de Estados Unidos occidental     |
| Central de Estados Unidos occidental     | Estados Unidos oeste 2           |

Para obtener información actualizada sobre las regiones que admite Azure, vea [Áreas de Azure](https://azure.microsoft.com/regions/).
 
## <a name="read-access-geo-redundant-storage"></a>Almacenamiento de acceso de lectura geo redundantes

Almacenamiento de acceso de lectura geo redundantes (RA GRS) aumenta al máximo la disponibilidad de su cuenta de almacenamiento, proporcionando acceso de solo lectura a los datos en la ubicación secundaria, además de la replicación entre dos regiones proporcionados por GRS. 

Al habilitar el acceso de solo lectura a los datos en la región secundario, los datos están disponibles en un extremo secundario, además del extremo principal para su cuenta de almacenamiento. El extremo secundario es similar al extremo principal, pero se anexa el sufijo `–secondary` al nombre de cuenta. Por ejemplo, si es el extremo principal para el servicio de Blob `myaccount.blob.core.windows.net`, entonces el extremo secundario es `myaccount-secondary.blob.core.windows.net`. Las teclas de acceso para su cuenta de almacenamiento son los mismos para los extremos primario y secundarios.

Consideraciones:

- Tiene la aplicación administrar extremo que interactúa con cuando se usen RA GRS. 

- RA GRS está pensado para fines de alta disponibilidad. Para obtener instrucciones escalabilidad, revise la [lista de comprobación de rendimiento](storage-performance-checklist.md).

## <a name="next-steps"></a>Pasos siguientes

- [Precios de almacenamiento de Azure](https://azure.microsoft.com/pricing/details/storage/)
- [Acerca de las cuentas de almacenamiento de Azure](storage-create-storage-account.md)
- [Objetivos de rendimiento y escalabilidad de almacenamiento de Azure](storage-scalability-targets.md)
- [Geo acceso de lectura y opciones de redundancia de almacenamiento de Microsoft Azure almacenamiento redundante](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)  
- [Papel SOSP - almacenamiento de Azure: Un servicio de nube altamente disponible almacenamiento con coherencia fuerte](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)  

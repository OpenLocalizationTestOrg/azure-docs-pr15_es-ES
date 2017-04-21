<properties
   pageTitle="Datos de DocumentDB con el tiempo de vida de punto de expirar | Microsoft Azure"
   description="Con TTL, Microsoft Azure DocumentDB proporciona la capacidad de documentos de purga del sistema automáticamente después de un período de tiempo."
   services="documentdb"
   documentationCenter=""
   keywords="tiempo de vida"
   authors="kiratp"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="documentdb"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/28/2016"
   ms.author="kipandya"/>

# <a name="expire-data-in-documentdb-collections-automatically-with-time-to-live"></a>Expire datos en colecciones DocumentDB automáticamente con el tiempo de vida

Las aplicaciones pueden generar y almacenar grandes cantidades de datos. Algunos de estos datos, como la sesión de usuario, los registros y los datos de la evento de equipo genera información solo es útil durante un período de tiempo limitado. Una vez que los datos se convierte en innecesaria según las necesidades de la aplicación es seguro purgar estos datos y reducir las necesidades de almacenamiento de una aplicación.

Con "período de vida" o TTL, Microsoft Azure DocumentDB proporciona la capacidad de documentos purgados automáticamente de la base de datos tras un periodo de tiempo. El tiempo que se puede establecer en el nivel de colección y de forma individual cada documento. Cuando se establece el TTL, como un valor predeterminado de la colección o en un documento, DocumentDB quitará automáticamente los documentos que existen después de ese período de tiempo, en segundos, ya que se modificaron por última vez.

Tiempo de vida en DocumentDB utiliza un desplazamiento contra cuando se modificó por última vez el documento. Para ello utiliza el campo _ts que existe en cada documento. El campo _ts es una marca de tiempo de tiempo de estilo unix que representa la fecha y hora. El campo _ts se actualiza cada vez que se modifique un documento. 

## <a name="ttl-behavior"></a>Comportamiento TTL

Propiedades TTL en dos niveles: el nivel de colección y el nivel de documento controla la característica TTL. Los valores se configuran en segundos y se tratan como delta desde el _ts que se modificó por última vez el documento en.

 1.  DefaultTTL para la colección
  * Si falta (o nulo), los documentos no se eliminan automáticamente.
  
  * Si actualmente y el valor es "-1" = infinito: documentos no expiren de forma predeterminada
  
  * Si presentar y el valor es un número ("n"): los documentos vencen "n" segundos después de la última modificación

 2.  TTL para los documentos: 
  * Propiedad solo es aplicable si está presente para la colección primaria DefaultTTL.
  
  * Anula el valor de DefaultTTL para la colección primaria.

Tan pronto como se ha expirado el documento (ttl + _ts > = hora actual del servidor), el documento se marca como "caducado". No se le permitirá ninguna operación en estos documentos después de este momento y se excluyen de los resultados de las consultas que se realizan. Los documentos física se eliminan del sistema y se eliminan en segundo plano según la ocasión en un momento posterior. Esto no consume cualquier [Solicitud de unidades (RUs)](documentdb-request-units.md) desde el presupuesto de la colección.

La lógica anterior se puede mostrar en la matriz de la siguiente:

|       | DefaultTTL falta o no se establece en la colección | DefaultTTL = -1 en la colección | DefaultTTL = "n" en la colección|
| ------------- |:-------------|:-------------|:-------------|
| TTL que faltan en el documento| No hay nada reemplazar en el nivel de documento, ya que el documento y la colección ningún concepto de TTL. | No hay documentos en esta colección caducará. | Los documentos de esta colección caducará cuando transcurre n de intervalo. |
| TTL = -1 en el documento | Nada reemplazar en el nivel de documento desde la colección no define la propiedad DefaultTTL que puede reemplazar un documento. Período de vida de un documento es sin interpretar el sistema. | No hay documentos en esta colección caducará. | El documento con TTL =-1 de esta colección nunca caducará. Todos los demás documentos caduca después de intervalo "n". |
|  TTL = n en documento | No hay nada reemplazar en el nivel del documento. TTL en un documento en sin interpretar el sistema. | El documento con TTL = n caduca después de n de intervalo, en segundos. Otros documentos heredarán el intervalo de -1 y nunca expire. | El documento con TTL = n caduca después de n de intervalo, en segundos. Otros documentos heredarán intervalo "n" de la colección. |


## <a name="configuring-ttl"></a>Configurar TTL

De forma predeterminada, tiempo de vida está deshabilitado de forma predeterminada en todos los documentos y en todas las colecciones de DocumentDB.

## <a name="enabling-ttl"></a>Habilitar TTL

Para habilitar TTL en una colección o los documentos dentro de una colección, debe establecer la propiedad DefaultTTL de una colección de -1 o número positivo distinto de cero. Establecer la DefaultTTL a-1, significa que todos los documentos de la colección se vida indefinidamente predeterminado pero la DocumentDB servicio debe supervisar esta colección de documentos que ha reemplazado este valor predeterminado.

## <a name="configuring-default-ttl-on-a-collection"></a>Configurar TTL predeterminado en una colección

Es posible configurar un tiempo de forma predeterminada para live en un nivel de la colección. 

Para establecer el valor de TTL en una colección, debe proporcionar un número positivo distinto de cero que indica el período en segundos, para que expire todos los documentos de la colección después de la marca de hora última modificación del documento (_ts).

O bien, puede establecer el valor predeterminado para -1, lo que implica que todos los documentos que se inserta en la colección durará indefinidamente de forma predeterminada.

## <a name="setting-ttl-on-a-document"></a>Valor TTL en un documento

Además de establecer un período de vida predeterminado en una colección puede establecer TTL específico de un nivel de documento. Esto reemplazará el valor predeterminado de la colección.

Para establecer el valor de TTL en un documento, debe proporcionar un número positivo distinto de cero que indica el período, en segundos, para que expire el documento después de la marca de hora última modificación del documento (_ts).

Para establecer este desplazamiento de expiración, establezca el campo TTL en el documento.

Si un documento no tiene ningún campo TTL, se aplicará el valor predeterminado de la colección.

Si TTL está deshabilitada en el nivel de colección, se omitirá el campo TTL en el documento hasta que se vuelve a habilitar TTL en la colección.


## <a name="extending-ttl-on-an-existing-document"></a>Extender TTL en un documento existente

Puede restablecer el período de vida de un documento haciendo cualquier operación de escritura en el documento. Esto establecerá la _ts a la hora actual y la cuenta atrás hasta la expiración de documento, según esté definido por el valor de ttl comenzará nuevamente.

Si desea cambiar el período de vida de un documento, puede actualizar el campo como lo hace con cualquier otro campo configurable.


## <a name="removing-ttl-from-a-document"></a>Quitar el período de vida de un documento

Si se ha establecido un valor de TTL en un documento y ya no desea que este documento para que expire, a continuación, se puede recuperar el documento, quite el campo TTL y reemplazar el documento en el servidor.

Cuando se quita el campo TTL del documento, se aplicará el valor predeterminado de la colección.

Para dejar de un documento desde la expiración y no se heredan de la colección, a continuación, debe establecer el valor TTL en -1.


## <a name="disabling-ttl"></a>Deshabilitar TTL

Para deshabilitar TTL por completo en una colección y detener el proceso de fondo de buscar documentos vencidos que se debe eliminar la propiedad DefaultTTL en la colección.

Al eliminar esta propiedad es diferente de si se establece en -1. Configuración de-1 significa nuevos documentos agregados a la colección se live indefinidamente pero se puede reemplazar en los documentos específicos de la colección.

Quitar esta propiedad por completo de la colección significa que no hay documentos caducará, incluso si hay documentos que se han reemplazado explícitamente predeterminado anterior.


## <a name="faq"></a>Preguntas más frecuentes

**¿Qué TTL costará mí?**

No hay ningún costo adicional a establecer un valor de TTL en un documento.

**¿Cuánto tiempo tardará para eliminar mi documento una vez que el valor TTL es el?**

Los documentos se marcan como no disponibles cuando el documento se ha caducado (ttl + _ts > = hora actual del servidor). No se le permitirá ninguna operación en estos documentos después de este momento y se excluyen de los resultados de las consultas que se realizan. Los documentos se eliminan física por el sistema en segundo plano. No utilizará cualquier RUs del presupuesto de la colección.

**¿Si tarda un período de tiempo para eliminar los documentos, contará hacia la cuota (y factura) hasta que se eliminan?**

No, una vez finalizado el documento no se cargará para el almacenamiento de estos documentos y el tamaño de los documentos no contará para la cuota de almacenamiento para una colección.

**¿Período de vida de un documento tengan cualquier impacto en cargos RU?**

No, habrá ninguna repercusión en cargos RU para las operaciones realizadas en cualquier documento en DocumentDB.

**¿Cambiará la eliminación del impacto de documentos en el rendimiento que he configurado en Mi colección?**

No, servir solicitudes de la colección recibirá prioridad sobre ejecuta el proceso de fondo para eliminar los documentos. Agregar TTL a cualquier documento no afectará esto.

**Cuando expira un documento, ¿cuánto tiempo permanecerá en Mi colección hasta que se elimine?**

Cuando expira el documento ya no será accesible. El tiempo exacto un documento permanecerá en la colección antes de que se elimine realmente no es determinante y se basa cuando el proceso de fondo puede eliminar el documento.

**Se eliminan documentos vencidos en todos los nodos o es "finalmente consistent?"**

El documento estará disponible al mismo tiempo en todos los nodos y en todas las regiones.

**¿Hay un coste RU supervisión TTL tareas en segundo plano?**

No, no hay ningún costo RU para este.

**¿Con qué frecuencia se comprueba caducidad TTL?**

Comprobación de caducidad de TTL no sucede en segundo plano. Al responder a una solicitud del servicio back-end se realice comprobaciones en línea y excluir los documentos que han expirado. La eliminación del documento físico es el único proceso que asincrónica se ejecuta en segundo plano. La frecuencia de este proceso depende de RUs disponible en la colección.

**¿La característica TTL solo se aplican a documentos completos o ¿expire la valores de propiedad de documento individual?**

TTL se aplica a todo el documento. Si le gustaría expire solo una parte de un documento, se recomienda que extraer la parte del documento principal en a un documento independiente "vinculado" y, a continuación, utilizar TTL en ese documento extraído.

**¿La función TTL tiene los requisitos de indización específicos?**

Sí. La colección debe tener [indización Directiva establece](documentdb-indexing-policies.md) Lazy o coherentes. Intentar establecer DefaultTTL en una colección de indización definido en ninguno producirá un error, e intenta desactivar la indización en una colección que tiene un DefaultTTL ha configurado.


## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre DocumentDB de Azure, consulte la página de la [*documentación*](https://azure.microsoft.com/documentation/services/documentdb/) de servicio.





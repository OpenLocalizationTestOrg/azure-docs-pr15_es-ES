<properties
    pageTitle="Niveles de coherencia en DocumentDB | Microsoft Azure"
    description="DocumentDB tiene cuatro niveles de coherencia para saldo la coherencia y disponibilidad y latencia compensaciones."
    keywords="la coherencia, documentdb, azure, Microsoft azure"
    services="documentdb"
    authors="syamkmsft"
    manager="jhubbard"
    editor="cgronlun"
    documentationCenter=""/>

<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/24/2016"
    ms.author="syamk"/>

# <a name="consistency-levels-in-documentdb"></a>Niveles de coherencia en DocumentDB

DocumentDB Azure está diseñado desde el principio hacia arriba con distribución global en cuenta. Está diseñado para ofrecer garantías de latencia baja predecible, un SLA de disponibilidad del 99,99% y varios modelos de coherencia Media bien definido. Actualmente, DocumentDB ofrece cuatro niveles de coherencia: sesión seguro, antigüedad limitada y final. Además de la **fuerte** y la **coherencia final** modelos disponibles comúnmente otras bases de datos NoSQL, DocumentDB también ofrece dos modelos de coherencia cuidadosamente codificada y operationalized – **limitada antigüedad** y **sesión**y validada su utilidad frente a casos de uso del mundo real. Colectivamente estos niveles de cuatro coherencia permiten realizar bien justificadas compensaciones entre coherencia, la disponibilidad y la latencia. 

## <a name="scope-of-consistency"></a>Ámbito de coherencia

El nivel de detalle de la coherencia de ámbito es una solicitud de usuario único. Una solicitud de escritura puede corresponden a un Insertar, reemplazar, upsert o eliminar transacción (con o sin la ejecución de un desencadenador pre o post asociado). O una solicitud de escritura puede corresponden a la ejecución de un procedimiento almacenado de JavaScript trabajan a través de varios documentos dentro de una partición de transacciones. Como ocurre con la escritura, una transacción de lectura o consulta también ámbito es una solicitud de usuario único. El usuario puede que deba paginar a través de un gran conjunto de resultados, que abarcan varias particiones, pero cada lectura transacción es el ámbito de una sola página y atendida desde dentro de una sola partición.

## <a name="consistency-levels"></a>Niveles de coherencia

Puede configurar un nivel de coherencia predeterminado de su cuenta de base de datos que se aplica a todas las colecciones (en todas las bases de datos) en su cuenta de base de datos. De forma predeterminada, todas las lecturas y consultas emitidas para los recursos definidos por el usuario utilizará el nivel de coherencia predeterminada especificado en la cuenta de base de datos. Sin embargo, puede aumentar el nivel de coherencia de una solicitud de lectura o consulta específica especificando el encabezado de la solicitud de [[x-ms-nivel de coherencia]](https://msdn.microsoft.com/library/azure/mt632096.aspx) . Existen cuatro tipos de niveles de coherencia compatibles con el protocolo de replicación DocumentDB que proporcionan un equilibrio borrar entre garantías específicas coherencia y rendimiento, tal como se describe a continuación.

![DocumentDB ofrece varias, bien definidos modelos de coherencia (flexible) para elegir][1]

**Seguro**: 

- Fuerte coherencia ofrece una garantía [linearizability](https://aphyr.com/posts/313-strong-consistency-models) con las lecturas garantizadas que volver a la versión más reciente de un documento. 
- Fuerte coherencia garantiza que una escritura solo está visible después comprometido duradera mediante el quórum mayoría de réplicas. Escritura está bien sincrónicamente comprometido duradera principal y el quórum de secundarios o se anula. La mayoría de leer quórum siempre reconoce una lectura, un cliente nunca puede ver una escritura sin confirmar o parcial y siempre se garantiza que leer la última escritura reconocida. 
- Cuentas de DocumentDB que se haya configurado para usar fuerte coherencia no pueden asociar más de una región de Azure con su cuenta de DocumentDB. 
- El costo de una operación de lectura (en términos de [unidades de solicitud](documentdb-request-units.md) consumidas) con coherencia fuerte es superior a la sesión y final, pero la misma que la antigüedad limitada.
 

**Antigüedad delimitada**: 

- Delimitado garantías de coherencia de antigüedad lecturas pueden retrasarse escribe máximo *K* versiones o prefijos de un documento o *t* intervalo de tiempo. 
- Por consiguiente, al elegir limitada antigüedad, "antigüedad" puede configurarse de dos maneras: 
    - Número de versiones *K* del documento por el que se retrase el lee la escritura
    - Intervalo de tiempo *t* 
- Limitada antigüedad ofertas global pedido total excepto dentro de la ventana de antigüedad de"". Tenga en cuenta que garantiza la lectura monotónica existe dentro de una región dentro y fuera del "antigüedad ventana". 
- Antigüedad limitada proporciona una garantía de coherencia más segura de sesión o la coherencia. Para las aplicaciones distribuidas globalmente, le recomendamos que use antigüedad limitada para escenarios que desea que tenga coherencia fuerte pero desea también disponibilidad del 99,99% y baja latencia. 
- Cuentas DocumentDB que estén configuradas con coherencia de antigüedad limitada pueden asociar cualquier número de áreas de Azure con su cuenta de DocumentDB. 
- El costo de una operación de lectura (en términos de RUs consumida) con antigüedad limitada es superior de la sesión y la coherencia, pero lo mismo que fuerte coherencia.

**Sesión**: 

- A diferencia de los modelos de coherencia global ofrecidos por niveles de coherencia de antigüedad sólida y limitada, coherencia sesión ámbito es una sesión de cliente. 
- Es ideal para todos los escenarios cuando se trate de una dispositivo o sesión de usuario dado que garantiza monotónico lee, escribe monotónico y leer sus propios escribe (RYW) garantiza coherencia de sesión. 
- Coherencia sesión proporciona coherencia predecible para una sesión y rendimiento de lectura de máximo y ofrecer la latencia escribe y lee más bajos. 
- Cuentas de DocumentDB que estén configuradas con coherencia de sesión pueden asociar cualquier número de áreas de Azure con su cuenta de DocumentDB. 
- El costo de una operación de lectura (en términos de RUs consumida) con el nivel de coherencia de sesión es menos seguro y limitada antigüedad, pero más de la coherencia
 

**Final**: 

- La coherencia garantiza que falta de cualquier escritura adicional, las réplicas dentro del grupo finalmente convergerá. 
- La coherencia es la forma más deficiente de coherencia donde un cliente puede obtener los valores que sean anteriores a las que se había visto antes.
- La coherencia proporciona la coherencia de lectura más deficiente pero ofrece la menor latencia de leer y escribir.
- Cuentas de DocumentDB que estén configuradas con la coherencia pueden asociar cualquier número de áreas de Azure con su cuenta de DocumentDB. 
- El costo de una operación de lectura (en términos de RUs consumida) con la coherencia es la menor de todos los niveles de coherencia de DocumentDB nivel.


## <a name="consistency-guarantees"></a>Garantías de coherencia

La siguiente tabla captura diversos garantías de coherencia correspondiente a los niveles de cuatro coherencia.

| Garantía                                                         |    Texto en negrita                                       |    Antigüedad limitada                                                                           |    Sesión                                       |    Final                                 |
|----------------------------------------------------------|-------------------------------------------------|------------------------------------------------------------------------------------------------|--------------------------------------------------|--------------------------------------------------|
|    **Total del pedido global**                                |    Sí                                          |    Sí, fuera de la ventana de antigüedad de""                                                      |    No, orden parcial "sesión"                   |    No                                            |
|    **Garantía de prefijo coherente**                       |    Sí                                          |    Sí                                                                                         |    Sí                                           |    Sí                                           |
|    **Lecturas monotónica**                                   |    Sí                                          |    Sí, en todas las regiones fuera de la ventana de antigüedad y dentro de una región todo el tiempo.     |    Sí, para la sesión determinada                    |    No                                            |
|    **Escribe monotónico**                                  |    Sí                                          |    Sí                                                                                         |    Sí                                           |    Sí                                           |
|    **Leer la escritura**                                  |    Sí                                          |    Sí                                                                                         |    Sí (en la región de escritura)                      |    No                                            |


## <a name="configuring-the-default-consistency-level"></a>Configurar el nivel de coherencia predeterminado

1.  En el [portal de Azure](https://portal.azure.com/), en la Jumpbar, haga clic en **DocumentDB (NoSQL)**.

2. En el módulo **DocumentDB (NoSQL)** , seleccione la cuenta de base de datos para modificar.

3. En el módulo de cuenta, haga clic en **predeterminado coherencia**.


4. En el módulo de **Coherencia predeterminada** , seleccione el nuevo nivel de coherencia y haga clic en **Guardar**.

    ![Captura de pantalla resaltado el icono Configuración y la entrada de coherencia predeterminada](./media/documentdb-consistency-levels/database-consistency-level-1.png)

## <a name="consistency-levels-for-queries"></a>Niveles de coherencia de consultas

De forma predeterminada, para los recursos definidos por el usuario, el nivel de coherencia de consultas es el mismo que el nivel de coherencia para lecturas. De forma predeterminada, el índice se actualiza sincrónicamente en cada Insertar, reemplazar o eliminar de un documento a la colección. Esto permite que las consultas respetar el mismo nivel de coherencia que de lecturas de documento. Mientras DocumentDB está optimizado de escritura y admite volúmenes prolongados de escritura de documento, atendiendo consultas coherentes y mantenimiento de índice sincrónico, puede configurar ciertas colecciones para actualizar su índice diferida. Indización diferida aún más aumenta el rendimiento de escritura y es ideal para escenarios de recopilación de forma masiva cuando una carga de trabajo está visible de lectura principalmente.  

Modo de indización|  Lee|  Consultas  
-------------|-------|---------
Coherente (predeterminado)|   Seleccionar desde la antigüedad fuerte, delimitada, sesión, o posterior|    Seleccionar desde la antigüedad fuerte, delimitada, sesión, o posterior|
Diferida|   Seleccionar desde la antigüedad fuerte, delimitada, sesión, o posterior|    Final  

Como con las solicitudes de lectura, puede reducir el nivel de coherencia de una solicitud de consulta concreta especificando el encabezado de la solicitud de [x ms coherencia nivel](https://msdn.microsoft.com/library/azure/mt632096.aspx) .

## <a name="next-steps"></a>Pasos siguientes

Si desea hacer más lectura sobre los niveles de coherencia y compensaciones, le recomendamos los siguientes recursos:

-   Doug Tomás. Explicación replicada coherencia de los datos a través de béisbol (vídeo).   
[https://www.YouTube.com/watch?v=gluIh8zd26I](https://www.youtube.com/watch?v=gluIh8zd26I)
-   Doug Tomás. Explicación replicada coherencia de los datos a través de béisbol.   
[http://Research.Microsoft.com/pubs/157411/ConsistencyAndBaseballReport.PDF](http://research.microsoft.com/pubs/157411/ConsistencyAndBaseballReport.pdf)
-   Doug Tomás. Garantías de sesión para flexible datos duplicados coherentes.   
[http://DL.ACM.org/Citation.cfm?id=383631](http://dl.acm.org/citation.cfm?id=383631)
-   Gerardo Abadi. Compensaciones de coherencia en el diseño de sistemas de base de datos de distribuido moderna: capital es solo una parte de la historia ".   
[http://Computer.org/CSDL/mags/CO/2012/02/mco2012020037-ABS.HTML](http://computer.org/csdl/mags/co/2012/02/mco2012020037-abs.html)
-   Peter Bailis, Shivaram Venkataraman, Michael J. Franklin, Joseph M. Hellerstein, iones Stoica. Probabilidad limitada antigüedad (PBS) para prácticos quórums parciales.   
[http://VLDB.org/pvldb/vol5/p776_peterbailis_vldb2012.PDF](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
-   Werner Vogels. Final coherente - renegociados.    
[http://allthingsdistributed.com/2008/12/eventually_consistent.HTML](http://allthingsdistributed.com/2008/12/eventually_consistent.html)


[1]: ./media/documentdb-consistency-levels/consistency-tradeoffs.png

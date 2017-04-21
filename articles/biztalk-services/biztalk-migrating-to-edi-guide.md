<properties   
    pageTitle="Migración de soluciones EDI de BizTalk Server a la guía técnica de servicios de BizTalk | Microsoft Azure"
    description="Migrar EDI a MABS; Servicios de Microsoft Azure BizTalk"
    services="biztalk-services"
    documentationCenter="na"
    authors="MandiOhlinger"
    manager="erikre"
    editor=""/>

<tags 
    ms.service="biztalk-services"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="mandia"/>


# <a name="migrating-biztalk-server-edi-solutions-to-biztalk-services-technical-guide"></a>Migrar soluciones EDI de BizTalk Server a BizTalk Services: Guía técnica

Autor: Tim Wieman y Nitin Mehrotra

Revisores: Karthik Bharthy

Escribe utilizando: versión de Microsoft Azure BizTalk Services: febrero de 2014.

## <a name="introduction"></a>Introducción

Intercambio de datos electrónicos (EDI) es uno de los medios más frecuentes por los datos de exchange de empresas electrónicamente, también se conoce como transacciones entre empresas o B2B. BizTalk Server ha tenido EDI compatibilidad con sobre un diez años, desde la versión de servidor BizTalk inicial. Con servicios de BizTalk, Microsoft continúa la compatibilidad para soluciones de EDI en la plataforma de Microsoft Azure. Transacciones B2B principalmente son externas a una organización y, por tanto, es más fácil de implementar si se ha implementado en una plataforma de nube. Microsoft Azure proporciona esta capacidad a través de los servicios de BizTalk.

Mientras algunos clientes ver los servicios de BizTalk como una plataforma "greenfield" nuevas soluciones de EDI, muchos clientes tienen actuales soluciones de EDI de BizTalk Server que desean migrar a Azure. Porque BizTalk servicios EDI está diseñado en función de las mismas entidades claves como arquitectura de EDI de BizTalk Server (cotización socios, entidades, agreements), es posible migrar artefactos EDI de BizTalk Server a BizTalk Services.

Este documento trata algunas de las diferencias que implica la migración artefactos EDI de BizTalk Server a BizTalk Services. Este documento supone un conocimiento de trabajo de procesamiento de EDI de BizTalk Server y contratos de Partner de cotización. Para obtener más información sobre EDI de BizTalk Server, consulte [Cotización Partner administración utilizando BizTalk Server](https://msdn.microsoft.com/library/bb259970.aspx).

## <a name="which-version-of-biztalk-server-edi-artifacts-can-be-migrated-to-biztalk-services"></a>¿Qué versión de artefactos EDI de BizTalk Server se puede migrar a los servicios de BizTalk?

El módulo de EDI de BizTalk Server se ha mejorado significativamente para BizTalk Server 2010, cuando se remodelen para incluir los contratos, perfiles y socios. Servicios de BizTalk utiliza el mismo modelo para organizar los socios comerciales y las divisiones de la empresa dentro de esos socios. Como resultado, migrar artefactos EDI de BizTalk Server 2010 y versiones posteriores para servicios de BizTalk, es un proceso mucho más sencillo. Para migrar artefactos EDI asociados con las versiones anteriores de BizTalk Server 2010, debe actualizar primero BizTalk Server 2010 y, a continuación, migrar los artefactos EDI a servicios de BizTalk.

## <a name="scenariosmessage-flow"></a>Flujo de mensajes y escenarios

Como BizTalk Server, EDI procesamiento en servicios de BizTalk se genera alrededor de una solución de administración de socios comerciales (TPM). La solución TPM tiene los siguientes componentes clave:

- Socios, que representan la organización en una transacción B2B.
- Perfiles, que representan divisiones dentro de un socio comercial.
- Cotización agreements partner (o agreements), que representan el acuerdo entre dos socios/perfiles.

La ilustración siguiente muestra las similitudes, así como las diferencias entre una solución de BizTalk EDI de servicios y soluciones de EDI de BizTalk Server:

![][EDImessageflow]

La claves diferencias y similitudes entre un flujo de solución EDI en BizTalk Server y los servicios de BizTalk son:

- Como BizTalk Server usa una canalización EDIReceive para recibir un mensaje de EDI y un proceso EdiSend para enviar un mensaje EDI, servicios de BizTalk utiliza un puente de recepción EDI para recibir y puente EDI enviar para enviar los mensajes EDI. En BizTalk Server, las canalizaciones están asociadas a un contrato mediante enviar o reciban puertos. En servicios de BizTalk, el acuerdo indica el enviar o recibir puente.
- En BizTalk Server, después de la canalización de EDIReceive procesa el mensaje EDI, el mensaje se guardan a una base de datos de SQL Server. El proceso EdiSend luego toma el mensaje desde la base de datos de SQL Server, lo procesa y lo envía al socio comercial.

    En servicios de BizTalk después de recibir EDI puente procesa el mensaje EDI, enruta el mensaje a un proceso externo. El proceso externo se podría ejecutar en Microsoft Azure o local. El proceso externo debe enrutar el mensaje al puente de envío EDI; puente de envío desarmar inherente el mensaje. Después de procesar el mensaje, el puente de envío EDI enruta el mensaje al socio comercial.

Servicios de BizTalk ofrece una experiencia de configuración fácil de usar para crear e implementar un contrato B2B entre socios comerciales sin configurar cualquier calcular de Microsoft Azure instancias (funciones Web o de trabajo), las bases de datos de SQL de Microsoft Azure o las cuentas de Microsoft Azure almacenamiento rápidamente. Escenarios más complejos requieren vinculación en flujos de trabajo o de otro procesamiento de servicio "alrededor de los bordes" de un acuerdo de Partner de cotización, es decir, antes o después del procesamiento de puente de cotización EDI de contrato asociado. En detalles, la siguiente secuencia de eventos se produce durante un mensaje EDI procesamiento en servicios de BizTalk.

1. Se recibe un mensaje EDI de cotización asociado, Fabrikam.  Para recibir mensajes EDI de socios, servicios de BizTalk admite protocolos de transporte como FTP, SFTP, AS2 y HTTP/S.

2. El procesamiento del lado de recepción de contrato asociado comercial separa el mensaje EDI en formato XML.  Puede enrutar el mensaje EDI desensamblado (en formato XML) a extremos de Bus de servicio como un extremo de retransmisión de Bus de servicio, tema de Bus de servicio, cola de Bus de servicio o un puente de servicios de BizTalk.

3. Los mensajes XML desensamblados podrían recibidos desde el extremo más procesamiento personalizado.  Estos extremos pueden procesar un componente local o una instancia de Microsoft Azure calcular para procesar el mensaje en un servicio de flujo de trabajo de Windows (WF) o Windows Communication Foundation (WCF), por ejemplo.

4. "Procesamiento de lado de envío" de socio comercial contrato luego reúne el mensaje XML en formato EDI y lo envía al socio comercial, Contoso.  Para enviar mensajes EDI a socios, servicios de BizTalk es compatible con los mismos protocolos que se utilizan para recibir mensajes EDI.

Aún más este documento proporciona instrucciones conceptuales en migrar algunos de los distintos artefactos EDI de BizTalk Server a BizTalk Services.

## <a name="sendreceive-ports-to-trading-partners"></a>Enviar y recibir puertos para socios

En BizTalk Server configurar ubicaciones de recepción y puertos de recepción para recibir mensajes EDI/XML de socios y configurar puertos de envío para enviar mensajes EDI/XML a socio comercial. A continuación, ocupar estos puertos a un acuerdo de partner comerciales mediante la consola de administración de BizTalk Server. En servicios de BizTalk, las ubicaciones donde recibe mensajes de socios comerciales y dónde enviar los mensajes a los socios comerciales se configuran como parte del Acuerdo socio comercial (como parte de la configuración de transporte) en el Portal de servicios de BizTalk.  Así que no realmente tiene el concepto de "enviar puertos" y "ubicaciones de recepción," Sí, en servicios de BizTalk. Para obtener más información, consulte [Creación de contratos](https://msdn.microsoft.com/library/windowsazure/hh689908.aspx).

## <a name="pipelines-bridges"></a>Canalizaciones (puentes)

En BizTalk Server EDI, canalizaciones son entidades de procesamiento de mensaje que también se pueden incluir lógica personalizada para las capacidades de procesamiento específico, según sea necesario por la aplicación. Para los servicios de BizTalk, el equivalente sería un puente EDI. Sin embargo en servicios de BizTalk, por ahora, puentes EDI se "cerrados".  Es decir, no puede agregar sus propias actividades personalizadas a un puente EDI. Cualquier procesamiento personalizado debe realizarse fuera del puente EDI en la aplicación, ya sea antes o después de que el mensaje entra en el puente está configurado como parte del contrato de cotización asociado. Puentes EAI tienen la opción de realizar el procesamiento personalizado. Si desea que el procesamiento personalizado, puede usar puentes EAI antes o después de que el mensaje es procesado por el puente EDI. Para obtener más información, vea [cómo incluir código personalizado en puentes](https://msdn.microsoft.com/library/azure/dn232389.aspx).

Puede insertar un flujo de publicación o suscripción con código personalizado o utilizar Bus de servicios de mensajería colas y temas antes de que el acuerdo de socio comercial recibe el mensaje, o después de que el contrato procesa el mensaje y redirige a un extremo de Bus de servicio.

En este tema para el modelo de flujo de mensaje, vea **Flujo de escenarios o mensaje** .

## <a name="agreements"></a>Agreements

Si está familiarizado con BizTalk Server 2010 deportivas Partner Agreements utilizado para el procesamiento de EDI, servicios de BizTalk cotización agreements partner apariencia muy familiares. La mayoría de las configuraciones de contrato es los mismos y use la misma terminología. En algunos casos, la configuración del contrato es mucho más simple en comparación con la misma configuración en BizTalk Server. Servicios de Microsoft Azure BizTalk admite X12, EDIFACT y AS2 transportan.

Servicios de Microsoft Azure BizTalk también proporciona una herramienta de **Migración de datos de TPM** para migrar comerciales asociados y contratos de módulo BizTalk Server cotización asociado al Portal de servicios de BizTalk. La herramienta de migración de datos de TPM está disponible como parte de un paquete de herramientas, que puede descargarse desde el [SDK de MABS](http://go.microsoft.com/fwlink/p/?LinkId=235057). El paquete también incluye un archivo Léame que proporciona instrucciones sobre cómo usar la herramienta y la información básica de solución de problemas de la herramienta.

## <a name="schemas"></a>Esquemas

Servicios de BizTalk proporciona esquemas EDI que se pueden utilizar en soluciones de los servicios de BizTalk.  Además, esquemas BizTalk Server EDI también sirve con servicios de BizTalk porque el nodo raíz del esquema EDI es igual a través de BizTalk Server, así como servicios de BizTalk. Por lo tanto, podrá tomar los esquemas EDI de BizTalk Server y usarlos en las soluciones EDI desarrollar con servicios de BizTalk directamente. También puede descargar los esquemas desde el [SDK de MABS](http://go.microsoft.com/fwlink/p/?LinkId=235057).

## <a name="maps-transforms"></a>Mapas (transformaciones)

Mapas en BizTalk Server se denominan transformaciones en servicios de BizTalk. Migración de asignaciones de BizTalk Server a BizTalk Services puede ser una de las tareas más complejas para lograr (dependiendo de la complejidad de asignación). La herramienta de asignación que se utiliza para los servicios de BizTalk es diferente de BizTalk mapper. Aunque el asignador principalmente tenga el mismo aspecto, el formato de mapa subyacente es diferente. Las funciones (llamado **Operaciones de mapa** en servicios de BizTalk) disponibles para los usuarios también son diferentes.  De hecho, no puede usar una asignación de BizTalk directamente en servicios de BizTalk. Además, no todas las funciones disponibles en BizTalk Server están disponibles como operaciones de mapa en servicios de BizTalk.

### <a name="new-transform-operations"></a>Operaciones de transformación de nuevo

Mientras que la lista de operaciones de transformación de mapa disponibles puede resultar bastante diferente desde el asignador de BizTalk Server, las transformaciones de servicios de BizTalk tiene nuevas formas de realizar las tareas. Por ejemplo, las transformaciones de servicios de BizTalk tiene **La lista de operaciones** disponibles. Esto no estaba disponible en el asignador de BizTalk.  La **Lista de operaciones** le permiten crear y trabajar con una "lista", donde una lista es un conjunto de elementos (también conocido como "filas") y cada elemento puede tener varios miembros (también conocidos como "columnas").  Puede ordenar la lista, seleccione los elementos en función de una condición, etcetera.

Otro ejemplo de nueva funcionalidad de las transformaciones de servicios de BizTalk son las **Operaciones de bucle**.  Es difícil crear bucles anidados en el asignador de BizTalk Server.  Por lo tanto, se agregan las operaciones de mapa de bucle para los servicios de BizTalk las transformaciones.

Otro ejemplo es la operación de asignación de expresión **If-Then-Else** .  Realizar una operación if-then-else era posible en el asignador de BizTalk, pero requiere varias funciones para llevar a cabo una tarea aparentemente sencilla.

### <a name="migrating-biztalk-server-maps"></a>Mapas de migración de BizTalk Server

Servicios de Microsoft Azure BizTalk proporciona una herramienta para migrar BizTalk Server asigna a las transformaciones de servicios de BizTalk. La **BTMMigrationTool** está disponible como parte del paquete de **Herramientas** proporcionado con el [SDK de servicios de BizTalk descargar](http://go.microsoft.com/fwlink/p/?LinkId=235057). Para obtener más información acerca de la herramienta, vea [convertir una asignación de BizTalk a una transformación de servicios de BizTalk](https://msdn.microsoft.com/library/windowsazure/hh949812.aspx).

También puede mirar una muestra por Sandro Pereira, MVP de BizTalk, sobre cómo [migrar BizTalk Server se asigna a las transformaciones de servicios de BizTalk](http://social.technet.microsoft.com/wiki/contents/articles/23220.migrating-biztalk-server-maps-to-windows-azure-biztalk-services-wabs-maps.aspx).

## <a name="orchestrations"></a>Orquestaciones

Si necesita migrar orquestación BizTalk Server procesamiento de Microsoft Azure, las orquestaciones necesarias para volver a escribir porque Microsoft Azure no admite ejecución orquestaciones BizTalk Server.  Podría volver a escribir la funcionalidad de orquestación en un servicio de Windows Workflow Foundation 4.0 (WF4).  Esto es una reescritura completa porque no hay actualmente ningún migración de orquestaciones BizTalk Server a WF4. Estos son algunos recursos para el flujo de trabajo de Windows:

- [*Cómo integrar un servicio de flujo de trabajo de WCF con colas de Bus de servicio y temas*](https://msdn.microsoft.com/library/azure/hh709041.aspx) por Paolo Salvatori. 

- [ *Creación de aplicaciones con Windows Workflow Foundation y Azure* sesión](http://go.microsoft.com/fwlink/p/?LinkId=237314) de la conferencia de compilación de 2011.

- [*Centro para desarrolladores de Windows Workflow Foundation*](http://go.microsoft.com/fwlink/p/?LinkId=237315) en MSDN.

- [*Documentación de Windows Workflow Foundation 4 (WF4)*](https://msdn.microsoft.com/library/dd489441.aspx) en MSDN.

## <a name="other-considerations"></a>Otras consideraciones

Los siguientes son algunas consideraciones que debe realizar al usar los servicios de BizTalk.

### <a name="fallback-agreements"></a>Contratos de reserva

Procesamiento de EDI de BizTalk Server tiene el concepto de "Reserva Agreements".  Qué servicios de BizTalk **no** tiene un concepto de reserva contrato hasta ese momento.  Vea los temas de documentación BizTalk [Rol de contratos de procesamiento de EDI](http://go.microsoft.com/fwlink/p/?LinkId=237317) y [Configuración Global o propiedades del acuerdo de reserva](https://msdn.microsoft.com/library/bb245981.aspx) para obtener información sobre cómo se usan los contratos de reserva en BizTalk Server.

### <a name="routing-to-multiple-destinations"></a>Enrutamiento a varios destinos

Puentes de servicios de BizTalk, en su estado actual no admite enrutamiento de mensajes a varios destinos mediante un publicar-suscribirse modelo. En su lugar puede enrutar mensajes de un puente de servicios de BizTalk a un tema de Bus de servicio, que, a continuación, puede tener varias suscripciones que reciba el mensaje en más de un extremo.

## <a name="conclusion"></a>Conclusión

Servicios de Microsoft Azure BizTalk se actualiza en hitos normales para agregar más características y funciones. Con cada actualización, esperamos auxiliares mayor funcionalidad para facilitar la creación de soluciones de llevar a cabo con servicios de BizTalk y otras tecnologías de Azure.

## <a name="see-also"></a>Vea también

[Desarrollo de aplicaciones empresariales con Azure](https://msdn.microsoft.com/library/azure/hh674490.aspx)

[EDImessageflow]: ./media/biztalk-migrating-to-edi-guide/IC719455.png

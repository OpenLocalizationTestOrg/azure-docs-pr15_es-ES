<properties
    pageTitle="Obtenga más información sobre las características de servicios de BizTalk ediciones | Microsoft Azure"
    description="Comparar las capacidades de las ediciones de servicios de BizTalk: libre, Developer, Basic, estándar y Premium. MABS, WABS."
    services="biztalk-services"
    documentationCenter=""
    authors="MandiOhlinger"
    manager="erikre"
    editor=""/>

<tags
    ms.service="biztalk-services"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/15/2016"
    ms.author="mandia"/>


# <a name="biztalk-services-editions-chart"></a>Servicios de BizTalk: Gráfico de ediciones

Servicios de BizTalk Azure ofrece varias ediciones. Use este artículo para determinar qué edición es la adecuada para su situación y las necesidades empresariales.


## <a name="compare-the-editions"></a>Compare las ediciones

**Liberar (vista preliminar)**

Puede crear y administrar conexiones híbrido. Una conexión híbrido es una forma sencilla para conectarse a un sitio Web de Azure a un sistema local, como SQL Server.

**Programador**

Incluye conexiones híbrido, EAI y EDI procesamiento de mensaje con un portal de administración de socios comerciales fácil de usar y la compatibilidad con los esquemas EDI comunes y enriquecidos EDI sobre X12 y AS2. Puede crear escenarios comunes de EAI conectar servicios en la nube con cualquier protocolo HTTP/S, resto, FTP, WCF y SFTP para leer y escribir mensajes.  Utilizar la conectividad con sistemas LOB local con adaptadores listos para usar SAP, Oracle comercio, DB de Oracle, Siebel y SQL Server. Use un entorno de desarrollador centrado en con Visual Studio tools e implementación de desarrollo sencilla. Se limita a fines de pruebas y desarrollo solo con ningún contrato de nivel de servicio (SLA).

**Básico**

Incluye la mayoría de las funciones de programador con aumenta en las conexiones de conexiones de híbridos, puentes EAI, EDI Agreements y BizTalk adaptador Pack. También ofrece alta disponibilidad y la opción para ajustar la escala con un contrato de nivel de servicio (SLA).

**Estándar**

Incluye todas las capacidades básicas con aumenta en las conexiones de conexiones de híbridos, puentes EAI, EDI Agreements y BizTalk adaptador Pack. También ofrece alta disponibilidad y la opción para ajustar la escala con un contrato de nivel de servicio (SLA).

**Premium**

Incluye todas las capacidades estándares con aumenta en las conexiones de conexiones de híbridos, puentes EAI, EDI Agreements y BizTalk adaptador Pack. También incluye archivado, alta disponibilidad y la opción para ajustar la escala con un contrato de nivel de servicio (SLA).


## <a name="editions-chart"></a>Gráfico de ediciones
La siguiente tabla enumeran las diferencias.

<table border="1">
<tr bgcolor="FAF9F9">
        <th></th>
        <th>Liberar (vista preliminar)</th>
        <th>Programador</th>
        <th>Básico</th>
        <th>Estándar</th>
        <th>Premium</th>
</tr>

<tr>
<td><strong>Precio de salida</strong></td>
<td colspan="5"><a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011"> Servicios de Azure BizTalk precios</a> <br/><br/> <a HREF="http://azure.microsoft.com/pricing/calculator/?scenario=full">Calculadora de precios de Azure</a></td>
</tr>
<tr>
<td><strong>Configuración mínima de forma predeterminada</strong></td>
<td>1 unidad libre</td>
<td>Unidad de 1 desarrollo</td>
<td>1 unidad básica</td>
<td>1 unidad estándar</td>
<td>Unidad de 1 prima</td>
</tr>
<tr>
<td><strong>Escala</strong></td>
<td>Sin escala</td>
<td>Sin escala</td>
<td>Sí, en incrementos de 1 unidad básica</td>
<td>Sí, en incrementos de 1 unidad estándar</td>
<td>Sí, en incrementos de 1 unidad Premium</td>
</tr>
<tr>
<td><strong>Máximo permitido de escala horizontal</strong></td>
<td>Sin escala</td>
<td>Sin escala</td>
<td>Unidades de hasta 8</td>
<td>Unidades de hasta 8</td>
<td>Unidades de hasta 8</td>
</tr>
<tr>
<td><strong>Puentes de EAI por unidad</strong></td>
<td>No incluida</td>
<td>25</td>
<td>25</td>
<td>125</td>
<td>500</td>
</tr>
<tr>
<td><strong>EDI, AS2</strong>
<br/><br/>
Incluye contratos de TPM</td>
<td>No incluida</td>
<td>Incluye. 10 agreements por unidad.</td>
<td>Incluye. 50 agreements por unidad.</td>
<td>Incluye. 250 agreements por unidad.</td>
<td>Incluye. contratos de 1000 por unidad.</td>
</tr>
<tr>
<td><strong>Conexiones de híbrido por unidad</strong></td>
<td>5</td>
<td>5</td>
<td>10</td>
<td>50</td>
<td>100</td>
</tr>
<tr>
<td><strong>Transferencia de datos de conexión híbrido (GB) por unidad</strong></td>
<td>5</td>
<td>5</td>
<td>50</td>
<td>250</td>
<td>500</td>
</tr>
<tr>
<td><strong>Conexiones de servicio de adaptador BizTalk a sistemas LOB local</strong></td>
<td>No incluida</td>
<td>1 conexión</td>
<td>conexiones de 2</td>
<td>5 conexiones</td>
<td>25 conexiones</td>
</tr>
<tr>
<td align="left"><strong>Sistemas/protocolos compatibles:</strong>
<ul>
<li>HTTP</li>
<li>HTTPS</li>
<li>FTP</li>
<li>SFTP</li>
<li>WCF</li>
<li>Bus de servicio (SB)</li>
<li>Blobs de Windows Azure</li>
<li>API REST</li>
</ul>
</td>
<td>No incluida</td>
<td>Incluido</td>
<td>Incluido</td>
<td>Incluido</td>
<td>Incluido</td>
</tr>
<tr>
<td><strong>Alta disponibilidad</strong>
<br/><br/>
Contrato de nivel de servicio (SLA), vea <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011">Servicios de precios de Azure BizTalk</a>.
</td>
<td>No incluida</td>
<td>No incluida</td>
<td>Incluido</td>
<td>Incluido</td>
<td>Incluido</td>
</tr>
<tr>
<td><strong>Copia de seguridad y restauración</strong></td>
<td>No incluida</td>
<td>Incluido</td>
<td>Incluido</td>
<td>Incluido</td>
<td>Incluido</td>
</tr>
<tr>
<td><strong>Seguimiento</strong></td>
<td>No incluida</td>
<td>Incluido</td>
<td>Incluido</td>
<td>Incluido</td>
<td>Incluido</td>
</tr>
<tr>
<td><strong>Archivado</strong><br/><br/>
Incluye no renuncia de recepción (NRR) y descargar los mensajes marcados</td>
<td>No incluida</td>
<td>Incluido</td>
<td>No incluida</td>
<td>No incluida</td>
<td>Incluido</td>
</tr>
<tr>
<td><strong>Uso del código personalizado</strong></td>
<td>No incluida</td>
<td>Incluido</td>
<td>Incluido</td>
<td>Incluido</td>
<td>Incluido</td>
</tr>
<tr>
<td><strong>Uso de las transformaciones, incluidos XSLT personalizado</strong></td>
<td>No incluida</td>
<td>Incluido</td>
<td>Incluido</td>
<td>Incluido</td>
<td>Incluido</td>
</tr>
</table>

> [AZURE.NOTE] La resistencia frente a errores de hardware, de alta disponibilidad implica tener varias máquinas virtuales dentro de una sola unidad de BizTalk.


## <a name="faqs"></a>Preguntas más frecuentes

#### <a name="what-is-a-biztalk-unit"></a>¿Qué es una unidad de BizTalk?
Una unidad de"" es el nivel de una implementación de Azure BizTalk Services atómico. Cada edición se suministra con una unidad que tiene la memoria y la capacidad de cálculo diferentes. Por ejemplo, una unidad básica tiene más de cálculo que Programador, estándar tiene más de cálculo que básica y así sucesivamente. Al escalar un BizTalk Service, escalar en términos de unidades.

#### <a name="what-is-the-difference-between-biztalk-services-and-azure-biztalk-vm"></a>¿Cuál es la diferencia entre los servicios de BizTalk y BizTalk VM de Azure?
Servicios de BizTalk proporciona una arquitectura de plataforma como-servicio (PaaS) es true para crear soluciones de integración en la nube. Con el modelo de PaaS que centrarse completamente en la aplicación lógica y dejar todo de la administración de la infraestructura a Microsoft, incluidos:

- No es necesario para administrar o máquinas virtuales de revisiones.
- Microsoft garantiza la disponibilidad.
- Controlar la escala a petición simplemente solicitando más o menos capacidad a través del portal de Azure.

BizTalk Server en Azure máquinas virtuales de Windows proporciona una arquitectura de infraestructura como-servicio (IaaS). Crear equipos virtuales y configurar exactamente como su entorno local, lo que facilita más ejecutar aplicaciones existentes en la nube sin cambios de código. Con IaaS, está siendo responsable de configurar las máquinas virtuales, administrar las máquinas virtuales (por ejemplo, instalar software y correcciones de sistemas operativos) y arquitectura de la aplicación para alta disponibilidad.

Si está buscando en nuevas soluciones de integración que minimizar el esfuerzo de administración de infraestructura, usar los servicios de BizTalk. Si está buscando para migrar rápidamente las soluciones existentes de BizTalk o buscando un entorno a petición desarrollar y probar aplicaciones de BizTalk Server, use BizTalk Server en una máquina Virtual de Azure.

#### <a name="what-is-the-difference-between-biztalk-adapter-service-and-hybrid-connections"></a>¿Cuál es la diferencia entre el servicio de adaptador de BizTalk y conexiones híbrido?
El servicio de adaptador BizTalk utiliza un servicio de BizTalk de Azure. El servicio de adaptador BizTalk utiliza el módulo de adaptador BizTalk para conectarse a un sistema de línea de negocio (LOB) local. Una conexión híbrido proporciona una forma fácil y cómoda conectar aplicaciones Azure, como la característica de aplicaciones Web de servicio de aplicación de Azure y Azure Servicios para dispositivos móviles, a un recurso local.

#### <a name="what-does-hybrid-connection-data-transfer-gb-per-unit-mean-is-this-per-minutehourdayweekmonth-what-happens-when-the-limit-is-reached"></a>¿Qué significa "Híbrido conexión transferencia de datos (GB) por unidad"? ¿Es este por minuto, hora, día, semana y mes? ¿Qué ocurre cuando se alcanza el límite?

El coste de conexión híbrido por unidad depende de la edición de servicios de BizTalk. En pocas palabras, dependen de costos en la cantidad de datos que transferir. Por ejemplo, transferir datos de 10 GB diariamente cuesta menos que transferir diaria de 100 GB. Utilice la [Calculadora de precios](https://azure.microsoft.com/pricing/calculator/?scenario=full) para los servicios de BizTalk para determinar los costes específicos. Normalmente, los límites se aplican diariamente. Si supera el límite, cualquier cobertura se cargará a la tasa de 1 $ por GB.

#### <a name="when-i-create-an-agreement-in-biztalk-services-why-does-the-number-of-bridges-go-up-by-two-instead-of-just-one"></a>Al crear un contrato de servicios de BizTalk, ¿por qué el número de puentes ir hacia arriba por dos en lugar de una sola?

Cada contrato se compone de dos puentes diferentes: un puente de comunicación de lado de envío y un puente de comunicación de lado de recepción.

####  <a name="what-happens-when-i-hit-the-quota-limit-on-the-number-of-bridges-or-agreements"></a>¿Qué ocurre cuando alcanza el límite de cuota del número de puentes o contratos?

No es posible implementar ningún puente de nuevo o crear los contratos de nuevos. Para implementar más, debe cambiar la escala a más unidades de servicio de BizTalk o actualizar a una edición superior.

#### <a name="how-do-i-migrate-from-one-tier-of-biztalk-services-to-another"></a>¿Cómo migrar desde un nivel de servicios de BizTalk a otro?

La edición gratuita no se puede migrar o 'escalar' a otro nivel y no se copia de seguridad y restaurar a otro nivel. Si necesita otro nivel, cree un BizTalk Service nuevo usando el nuevo nivel. Cualquier artefacto creado con la edición gratuita, incluidas las conexiones híbrido, necesita volver a crear en el nuevo BizTalk Service. 

Para las ediciones restantes, utilice la copia de seguridad y restauración para migrar su artefactos desde un nivel a otro. Por ejemplo, su defectos en el nivel de estándar de copia de seguridad y, a continuación, restaurarlas al nivel Premium. [Servicios BizTalk: copia de seguridad y restaurar](biztalk-backup-restore.md) describe las rutas de acceso de migración compatibles y muestra qué artefactos se realizan copias de seguridad. Tenga en cuenta que no se realizan copias de seguridad de conexiones de híbrido. Después de realizar copias de seguridad y restaurar a un nuevo nivel, a continuación, volver a crear las conexiones híbrido.  


#### <a name="is-the-biztalk-adapter-service-included-in-the-service-how-do-i-receive-the-software"></a>¿Es el servicio de adaptador BizTalk incluido en el servicio? ¿Cómo se puede recibir el software?

Sí, el servicio de adaptador BizTalk con el paquete de adaptador BizTalk se incluyen en el SDK de servicios de Azure BizTalk [Descargar](http://www.microsoft.com/download/details.aspx?id=39087).

## <a name="next-steps"></a>Pasos siguientes

Para crear los servicios de BizTalk de Azure en el portal de Azure, vaya a [servicios BizTalk: aprovisionamiento con el portal de Azure](biztalk-provision-services.md). Para empezar a crear aplicaciones, vaya a [Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=235197).

## <a name="additional-resources"></a>Recursos adicionales
- [Servicios de BizTalk: Aprovisionamiento con el portal de Azure](biztalk-provision-services.md)<br/>
- [Servicios de BizTalk: Gráfico de estado de aprovisionamiento](biztalk-service-state-chart.md)<br/>
- [Servicios de BizTalk: Fichas de paneles, el Monitor y la escala](biztalk-dashboard-monitor-scale-tabs.md)<br/>
- [Servicios de BizTalk: Copia de seguridad y restauración](biztalk-backup-restore.md)<br/>
- [Servicios de BizTalk: límite](biztalk-throttling-thresholds.md)<br/>
- [Servicios de BizTalk: Nombre del emisor y la clave del emisor](biztalk-issuer-name-issuer-key.md)<br/>
- [¿Cómo puedo comenzar a utilizar el SDK de servicios de BizTalk de Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>

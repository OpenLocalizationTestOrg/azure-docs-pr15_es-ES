<properties
    pageTitle="Plataformas de análisis: comparación de tormenta Apache para el análisis de secuencia | Microsoft Azure"
    description="Obtenga orientación eligiendo una plataforma de análisis de nube usando una comparación de tormenta Apache para el análisis de secuencia. Comprender las diferencias y características."
    keywords="plataforma de análisis, plataformas de análisis, plataforma de análisis de la nube, comparación de tormenta"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>

# <a name="help-choosing-a-streaming-analytics-platform-apache-storm-comparison-to-azure-stream-analytics"></a>Ayuda para seleccionar una plataforma de análisis streaming: comparación de tormenta Apache para el análisis de secuencia de Azure

Obtenga orientación eligiendo una plataforma de análisis de la nube mediante el uso de esta comparación de tormenta Apache para el análisis de secuencia de Azure. Comprender los casos de uso de las propuestas de valor de análisis de secuencia frente a tormenta Apache como un servicio administrado en HDInsight de Azure, para poder elegir la solución adecuada para su empresa.

Ambas plataformas analytics proporcionan ventajas de una solución PaaS, hay algunas capacidades distintivas principales que diferencian. Capacidades, así como a continuación se muestran las limitaciones de estos servicios para ayudar a comenzar en la solución que necesita para alcanzar los objetivos.

## <a name="storm-comparison-to-stream-analytics-general-features"></a>Storm comparación de análisis de secuencia: funciones generales ##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Análisis de secuencia de Azure</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Tormenta Apache en HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Abrir origen</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
No, el análisis de secuencia de Azure es una propiedad de Microsoft que ofrece.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Sí, tormenta Apache es una tecnología de Apache con licencia.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Microsoft admitidos</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Sí </p>
            </td>
            <td width="246" valign="top">
                <p>
Sí </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Requisitos de hardware</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
No hay ningún requisitos de hardware. Análisis de secuencia de Azure es un servicio de Azure.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
No hay ningún requisitos de hardware. Tormenta Apache es un servicio de Azure.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Unidad de nivel superior</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Con los clientes de análisis de secuencia de Azure implementar y supervisar transmisiones de trabajos.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Con Apache tormenta en clientes de HDInsight implementar y supervisar todo un clúster, que puede hospedar varios trabajos de tormenta, así como otras cargas de trabajo (lote incluido).
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Precio</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
El precio del análisis de secuencia es el volumen de datos que procesa y el número de unidades streaming (por hora que el trabajo en ejecución) necesarios.
                </p>
                <p>
                    <a href="http://azure.microsoft.com/en-us/pricing/details/stream-analytics/">Obtener más información sobre precios pueden encontrarse aquí.</a>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Para Apache tormenta en HDInsight, la unidad de compra está basado en el clúster y se cargará en función del momento en que se está ejecutando el clúster, independiente de trabajos implementados.
                </p>
                <p>
                    <a href="http://azure.microsoft.com/en-us/pricing/details/hdinsight/">Obtener más información sobre precios pueden encontrarse aquí.</a>
                </p>
            </td>
        </tr>
    </tbody>
</table>
##En cada plataforma de análisis de creación##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Análisis de secuencia de Azure</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Tormenta Apache en HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Capacidades: SQL DSL</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Sí, una compatibilidad con idiomas de fácil de usar SQL está disponible.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
No, los usuarios deben escribir código en Java C# o use las API de Trident.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Capacidades: Operadores Temporal</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Son compatibles con ventanas agregados y combinaciones temporales fuera del cuadro.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Operadores temporales deben ser implementado por el usuario.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Experiencia de desarrollo</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Experimentan interactiva de creación y depuración a través del Portal de Azure en datos de ejemplo.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Desarrollo, depuración y supervisión experiencia son siempre a través de Visual Studio experiencia para usuarios de .NET, mientras que para Java y otros desarrolladores de idiomas puede utilizar el IDE de su elección.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Soporte de depuración</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Análisis de secuencia ofrece el estado del trabajo básico y los registros de operaciones como una manera de depuración, pero actualmente no ofrece una flexibilidad ¿y cómo mucho se incluye en los registros de la ie: modo detallado.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Registros detallados están disponibles con fines de depuración. Hay dos formas de superficies registros de usuario, a través de visual Studio o usuario puede RDP en el clúster para tener acceso a los registros.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Soporte técnico para UDF (funciones definidas por el usuario)</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Actualmente no hay ninguna compatibilidad para UDF.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
UDF se pueden escribir en C#, Java o el idioma de su elección.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Extensible - código personalizado</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
No existe compatibilidad con extensible código en el análisis de secuencia de.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Sí, hay disponibilidad para escribir código personalizado en C#, Java u otros idiomas compatibles de tormenta.
                </p>
            </td>
        </tr>
    </tbody>
</table>
##Orígenes de datos y resultados##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Análisis de secuencia de Azure</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Tormenta Apache en HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                 <strong>Orígenes de datos de entrada</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>Las fuentes de entrada compatibles son Hubs de evento de Azure y Azure BLOB.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Hay conectores para Hubs de evento, Bus de servicio, Kafka, etcetera. Conectores incompatibles podrían aplicarse a través de código personalizado.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Formatos de datos de entrada</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Formatos de entrada compatibles son Avro, JSON, CSV.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Cualquier formato se puede implementar a través de código personalizado.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Resultados</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Un trabajo streaming puede tener varias salidas. Salidas compatibles: Evento Azure Hubs, almacenamiento de blobs de Windows Azure, tablas de Azure, DB de SQL Azure y PowerBI.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Soporte técnico para muchas de las salidas en una topología, cada salidas pueden tener lógica personalizada para el procesamiento de nivel inferior. Cerrar el cuadro de tormenta incluye conectores para PowerBI, Hubs de evento de Azure, almacenamiento de blobs de Windows Azure, DocumentDB de Azure, SQL y HBase. Conectores incompatibles podrían aplicarse a través de código personalizado.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Formatos de codificación de datos</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Análisis de secuencia requiere el formato de datos de UTF-8 para usarse.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Cualquier formato de codificación de datos se puede implementar a través de código personalizado.
                </p>
            </td>
        </tr>
    </tbody>
</table>
##Administración y operaciones##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Análisis de secuencia de Azure</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Tormenta Apache en HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Modelo de implementación de trabajo</strong>
                </p>
                <p>
                    - <strong>Portal de Azure</strong>
                </p>
                <p>
                    - <strong>Visual Studio</strong>
                </p>
                <p>
                    - <strong>PowerShell</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Implementación se implementa a través del Portal de Azure, PowerShell y las API de REST.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Depolyment se implementa a través del Portal de Azure, PowerShell, Visual Studio y las API de REST.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Supervisión (estadísticas, contadores, etcetera).</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Supervisión se implementa a través del Portal de Azure y las API de REST.
                </p>
                <p>
El usuario también puede configurar las alertas de Azure.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Supervisión se implementa a través de la interfaz de usuario de tormenta y las API de REST.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Escalabilidad</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Número de unidades de transmisión para cada tarea. Cada unidad de transmisión procesa hasta 1 MB/s. Número máximo de 50 unidades de forma predeterminada. Llamada para aumentar el límite.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Número de nodos en el clúster de tormenta HDI. Sin límite en el número de nodos (límite superior definida por la cuota de Azure). Llamada para aumentar el límite.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Límites de procesamiento de datos</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Los usuarios pueden escalar hacia arriba o hacia abajo el número de unidades de transmisión para aumentar el procesamiento de datos u optimizar los costos.
                </p>
                <p>
Ajustar el tamaño de un máximo de 1 GB/s </p>
            </td>
            <td width="246" valign="top">
                <p>
Usuario puede escalar hacia arriba o hacia abajo el tamaño de clúster para satisfacer necesidades.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Detener o reanudar</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Detener y reanudar desde el último lugar detenido.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Detener y reanudar el último colocar detenido en función de la marca de agua.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Actualización de servicio y framework</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Revisiones automáticas sin tiempo de inactividad.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Revisiones automáticas sin tiempo de inactividad.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Continuidad empresarial a través del servicio altamente disponibles con garantía SLA</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
SLA de tiempo de actividad del 99,9% </p>
                <p>
Recuperación automática de errores </p>
                <p>
Recuperación de operadores temporales con estado está integrada.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
SLA de tiempo de actividad de 99,9% del clúster tormenta. Tormenta Apache es una plataforma de streaming tolerancia a errores de errores, aunque es responsabilidad de los clientes para asegurarse de que su trabajo streaming ejecutarse sin interrupciones.
                </p>
            </td>
        </tr>
    </tbody>
</table>
##Características avanzadas##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Análisis de secuencia de Azure</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Tormenta Apache en HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Control de eventos en el orden correcto y llegada tardía</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Directivas configurables integradas para reordenar, eliminar eventos o ajustar la hora del evento.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Usuario debe implementar lógica para controlar este escenario.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Datos de referencia</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Datos de referencia disponibles de Blobs de Azure con el tamaño máximo de 100 MB de caché de búsqueda en memoria. Actualización de datos de la referencia es administrada por el servicio.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Sin límites de tamaño de los datos. Conectores disponibles para HBase, DocumentDB, SQL Server y Azure. Conectores incompatibles podrían aplicarse a través de código personalizado.
                </p>
                <p>
Actualización de datos de referencia debe corregirse por el código personalizado.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Integración con el aprendizaje</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Configurando publicar modelos de aprendizaje del equipo de Azure como funciones durante la creación de trabajo ASA <a href="http://blogs.msdn.com/b/streamanalytics/archive/2015/05/24/real-time-scoring-of-streaming-data-using-machine-learning-models.aspx">(vista previa privada)</a>.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Disponible a través de tormenta pernos.
                </p>
            </td>
        </tr>
    </tbody>
</table>

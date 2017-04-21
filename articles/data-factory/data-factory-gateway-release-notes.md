<properties 
    pageTitle="Notas de la versión de Data Management Gateway | Generador de datos de Azure" 
    description="Notas de la versión de artículo de datos Management Gateway" 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/26/2016" 
    ms.author="spelluru"/>

# <a name="release-notes-for-data-management-gateway"></a>Notas de la versión de Data Management Gateway
Uno de los desafíos de integración de datos moderno es perfecta mover datos hacia y desde local en la nube. Generador de datos hace que esta integración perfecta con Data Management Gateway, que es un agente que puede instalar en local para habilitar el movimiento de datos híbrido.

Consulte los artículos siguientes para obtener información detallada sobre Data Management Gateway y cómo usarlo: 

- [Data Management Gateway](data-factory-data-management-gateway.md)
- [Mover datos entre local y la nube con el generador de datos de Azure](data-factory-move-data-between-onprem-and-cloud.md) 

## <a name="current-version-2260721"></a>Versión actual (2.2.6072.1)

- Es compatible con la configuración de proxy HTTP para la puerta de enlace con el Administrador de configuración de puerta de enlace. Si ha configurado, blobs de Windows Azure, tabla de Azure, lago de datos de Azure y documento DB se tiene acceso a través de proxy HTTP.
- Encabezado es compatible con tratamiento de TextFormat al copiar datos de o para blobs de Windows Azure, almacén de lago de datos de Azure, sistema de archivos local y HDFS local.
- Es compatible con copiar datos de anexar Blob y Blob de página junto con el Blob bloque ya admitidos.
- Presenta un nuevo estado de la puerta de enlace **en línea (limitado)**, lo que indica que la funcionalidad principal de la puerta de enlace funciona excepto la compatibilidad con la operación interactiva del Asistente para copiar.
- Mejora la eficacia de registro de la puerta de enlace con la clave de registro.

## <a name="earlier-versions"></a>Versiones anteriores

## <a name="2160401"></a>2.1.6040.1

- Controlador DB2 ahora se incluye en el paquete de instalación de puerta de enlace. No es necesario instalarlo por separado. 
- Controlador DB2 admite z/OS y DB2 para (AS / 400) junto con las plataformas compatibles ya (Linux, Unix y Windows). 
- Admite el uso de DocumentDB como origen o destino para comercios de datos local
- Es compatible con copiar datos desde/hacia fría o caliente blob almacenamiento junto con la cuenta de almacenamiento general ya admitidos. 
- Le permite conectarse a local de SQL Server a través de la puerta de enlace con privilegios de inicio de sesión remota.  

## <a name="2060131"></a>2.0.6013.1

- Puede seleccionar el idioma o referencia cultural para usarlo con una puerta de enlace durante la instalación manual.
- Cuando la puerta de enlace no funciona según lo esperado, puede elegir enviar registros de puerta de enlace de los siete últimos días a Microsoft para facilitar la solución de problemas del problema. Si la puerta de enlace no está conectada al servicio de nube, puede elegir guardar y archivar los registros de la puerta de enlace.  
- Mejoras de la interfaz de usuario para el Administrador de configuración de puerta de enlace:
    - Que el estado de la puerta de enlace más visibles en la pestaña Inicio.
    - Controles simplificados y reorganizados.
- Puede copiar datos de un almacenamiento mediante la [herramienta de vista previa de la copia de sin código](data-factory-copy-data-wizard-tutorial.md). Ver [Copia por fases](data-factory-copy-activity-performance.md#staged-copy) para obtener más información acerca de esta característica en general. 
- Puede utilizar Data Management Gateway a los datos de entrada directamente desde una base de datos de SQL Server local en Azure el aprendizaje.
- Mejoras de rendimiento
    - Mejorar el rendimiento en Ver esquema y la vista previa en SQL Server en la herramienta de vista previa de la copia sin código.



## <a name="11259531"></a>1.12.5953.1
- Correcciones de errores

## <a name="11159181"></a>1.11.5918.1

- Tamaño máximo del registro de eventos de puerta de enlace se ha aumentado de 1 MB a 40 MB.
- En caso de que es necesario reiniciar durante la actualización automática de puerta de enlace, se muestra un cuadro de diálogo de advertencia. Puede elegir reiniciar hacia la derecha, a continuación, o una versión posterior. 
- En caso de que la actualización automática no funciona, el instalador de puerta de enlace reintentos de actualización automática tres veces como máximo.
- Mejoras de rendimiento
    - Mejorar el rendimiento de tablas de gran tamaño de carga del servidor local en caso de copia sin código.
- Correcciones de errores

## <a name="11058921"></a>1.10.5892.1

- Mejoras de rendimiento
- Correcciones de errores

## <a name="1958652"></a>1.9.5865.2

- Cero capacidad de actualización automática de táctil
- Icono de la Bandeja de nuevo con indicadores de estado de la puerta de enlace
- Capacidad de "Actualizar ahora" del cliente
- Capacidad para establecer el tiempo de programación de actualización
- Script de PowerShell para alternar activar o desactivar la actualización automática
- Compatibilidad con formato JSON  
- Mejoras de rendimiento
- Correcciones de errores

## <a name="1858221"></a>1.8.5822.1

- Mejorar la experiencia de solución de problemas
- Mejoras de rendimiento
- Correcciones de errores

### <a name="1757951"></a>1.7.5795.1

- Mejoras de rendimiento
- Correcciones de errores

### <a name="1757641"></a>1.7.5764.1

- Mejoras de rendimiento
- Correcciones de errores

### <a name="1657351"></a>1.6.5735.1

- Soporte técnico HDFS origen/receptor local
- Mejoras de rendimiento
- Correcciones de errores

### <a name="1656961"></a>1.6.5696.1

- Mejoras de rendimiento
- Correcciones de errores

### <a name="1656761"></a>1.6.5676.1

- Herramientas de diagnóstico de soporte técnico en el Administrador de configuración
- Columnas de la tabla de soporte técnico para orígenes de datos tabulares de generador de datos de Azure
- Compatibilidad con DW SQL Azure datos Factory
- Compatibilidad con Reclusive en BlobSource y FileSource de fábrica de datos de Azure
- Soporte técnico CopyBehavior: MergeFiles, PreserveHierarchy y FlattenHierarchy en BlobSink y FileSink con copia binario de generador de datos de Azure
- Soporte técnico de actividad de copia informar del progreso de generador de datos de Azure
- Validación de conectividad de origen de datos de soporte técnico para generador de datos de Azure
- Correcciones de errores


### <a name="1656721"></a>1.6.5672.1

- Nombre de la tabla de soporte técnico para el origen de datos ODBC para el generador de datos de Azure
- Mejoras de rendimiento
- Correcciones de errores

### <a name="1656581"></a>1.6.5658.1

- Archivo de soporte receptor de generador de datos de Azure
- Soporte técnico perder la jerarquía de copia binario de generador de datos de Azure
- Copiar actividad idempotencia de soporte técnico para el generador de datos de Azure
- Correcciones de errores

### <a name="1656401"></a>1.6.5640.1

- Soporte técnico 3 más orígenes de datos de Azure generador de datos (ODBC, OData, HDFS)
- Compatibilidad con oferta carácter en el analizador de csv generador de datos de Azure
- Compatibilidad con la compresión (BZip2)
- Correcciones de errores

### <a name="1556121"></a>1.5.5612.1

- Compatibilidad con cinco bases de datos relacionales generador de datos de Azure (MySQL, PostgreSQL, DB2, Teradata y Sybase)
- Compatibilidad con la compresión (Gzip y Deflate)
- Mejoras de rendimiento
- Correcciones de errores


### <a name="1455491"></a>1.4.5549.1

- Agregar compatibilidad de origen de datos de Oracle para el generador de datos de Azure
- Mejoras de rendimiento
- Correcciones de errores

### <a name="1454921"></a>1.4.5492.1

- Binario unificada que es compatible con servicios de Microsoft Azure datos fábrica y Power BI para Office 365
- Refinar el proceso de registro y la interfaz de usuario de configuración
- Generador de datos de Azure: Azure de entrada y salida de soporte técnico para el origen de datos de SQL Server

### <a name="1253031"></a>1.2.5303.1

-   Solucionar el problema de tiempo de espera para admitir más conexiones de origen de datos que lleva mucho tiempo. 
    
### <a name="1155268"></a>1.1.5526.8

- Requiere .NET Framework 4.5.1 como requisito previo durante la instalación.

### <a name="1051442"></a>1.0.5144.2

- No hay cambios que afectan a los escenarios de fábrica de datos de Azure. 

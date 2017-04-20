<properties
   pageTitle="Orígenes de datos compatibles con el catálogo de datos de Azure | Microsoft Azure"
   description="Especificación de los orígenes de datos compatibles actualmente."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="jstrauss"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="09/15/2016"
   ms.author="maroche"/>

# <a name="azure-data-catalog-supported-data-sources"></a>Orígenes de datos compatibles con el catálogo de datos de Azure

Los usuarios del catálogo de datos de Azure pueden publicar metadatos mediante una API pública, un clic-registro una vez, o bien introduciendo manualmente información directamente en el catálogo de datos web de portal. La siguiente cuadrícula resume todas las fuentes compatibles con el catálogo de hoy y las capacidades de publicación para cada uno.  También aparecen las herramientas de datos externos que puede iniciar cada origen de nuestra experiencia portal "abrir en". La segunda cuadrícula en el artículo tiene una especificación más información técnica de cada propiedades de conexión de orígenes de datos.


## <a name="list-of-supported-data-sources"></a>Lista de orígenes de datos compatibles

<table>

    <tr>
       <td><b>Objeto de origen de datos</b></td>
       <td><b>API</b></td>
       <td><b>Entrada manual</b></td>
       <td><b>Herramienta de registro</b></td>
       <td><b>Herramientas de abrir</b></td>
       <td><b>Notas</b></td>
    </tr>

    <tr>
      <td>Directorio de almacén de lago de datos de Azure</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Archivo de almacén de lago de datos de Azure</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Almacenamiento de Azure Blob</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Directorio de almacenamiento de Azure</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabla de almacenamiento de Azure</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td>
        <font size="2"></font>
      </td>
      <td>
        <font size="2"></font>
      </td>
    </tr>

    <tr>
      <td>Directorio HDFS</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Archivo HDFS</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabla subárbol</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Vista de sección</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabla de MySQL</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Vista de MySQL</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabla de base de datos de Oracle</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Vista de base de datos de Oracle</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Sí (genérico activo)</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabla de almacén de datos SQL</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Herramientas de datos de Excel, PowerBI, SQL Server</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Vista de almacén de datos SQL</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Herramientas de datos de Excel, PowerBI, SQL Server</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Dimensión SQL Server Analysis Services</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>KPI de SQL Server Analysis Services</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Medida SQL Server Analysis Services</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabla de SQL Server Analysis Services</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Informe de Reporting Services SQL Server</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Explorador</font></td>
      <td><font size=2>Servidores de modo nativo. No se admite el modo de SharePoint.</font></td>
    </tr>

    <tr>
      <td>Tabla de SQL Server</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Herramientas de datos de Excel, PowerBI, SQL Server</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Vista de SQL Server</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Herramientas de datos de Excel, PowerBI, SQL Server</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabla de Teradata</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Vista de Teradata</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Vista de Hana SAP</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>PowerBI</font></td>
      <td><font size=2>Vistas de cálculo y vistas analíticas Vistas del atributo no son compatibles.</font></td>
    </tr>

    <tr>
      <td>Tabla de Db2</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Vista de Db2</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Archivo de sistema</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Directorio FTP</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Archivo FTP</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Informe de http</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Punto final de http</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Archivo de http</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Conjunto de entidades de OData</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Función de OData</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabla de PostgreSQL</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Vista de PostgreSQL</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Vista de Hana SAP</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td> Objeto de Salesforce</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Lista de SharePoint </td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

</table>

Si necesita soporte técnico para fuentes adicionales, envíe una solicitud de característica mediante el [foro de catálogo de datos de Azure](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).


<br>
<br>
## <a name="data-source-reference-specification"></a>Especificación de referencia de origen de datos
> [AZURE.NOTE] Columna de "Estructura DSL" en la siguiente tabla solo listas propiedades de conexión de bolsa de propiedades de "dirección" que se utilizan en el catálogo de datos de Azure (es decir, bolsa de propiedades de "dirección" puede contener otras propiedades de conexión del origen de datos que el catálogo de datos de Azure persiste, pero no utiliza).
<table>
    <tr>
       <td><b>Tipo de origen</b></td>
       <td><b>Tipo de activos</b></td>
       <td><b>Tipos de objeto</b></td>
       <td><b>Estructura DSL<b></td>
    </tr>
    <tr>
      <td>Almacén de lago de datos de Azure</td>
      <td>Contenedor</td>
      <td>Lago de datos</td>
      <td>
        <font size=2>Protocolo: webhdfs <br>autenticación: {básica, oauth} <br>dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;dirección URL</font>
      </td>
    </tr>
    <tr>
      <td>Almacén de lago de datos de Azure</td>
      <td>Tabla</td>
      <td>Directorio de archivo</td>
      <td>
        <font size=2>Protocolo: webhdfs <br>autenticación: {básica, oauth} <br>dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;dirección URL</font>
      </td>
    </tr>
    <tr>
      <td>Almacenamiento de Azure</td>
      <td>Contenedor</td>
      <td>Contenedor</td>
      <td>
        <font size=2>Protocolo: blobs de azure <br>autenticación: {tecla de acceso de azure} <br>dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;dominio <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;cuenta <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;contenedor</font>
      </td>
    </tr>
    <tr>
      <td>Almacenamiento de Azure</td>
      <td>Tabla</td>
      <td>BLOB, directorio</td>
      <td>
        <font size=2>Protocolo: blobs de azure <br>autenticación: {tecla de acceso de azure} <br>dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;dominio <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;cuenta <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;contenedor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;nombre</font>
      </td>
    </tr>
    <tr>
      <td>Almacenamiento de Azure</td>
      <td>Contenedor</td>
      <td>Contenedor</td>
      <td>
        <font size=2>Protocolo: tablas de azure <br>autenticación: {tecla de acceso de azure} <br>dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;dominio <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;cuenta</font>
      </td>
    </tr>
    <tr>
      <td>Almacenamiento de Azure</td>
      <td>Tabla</td>
      <td>Tabla</td>
      <td>
        <font size=2>Protocolo: tablas de azure <br>autenticación: {tecla de acceso de azure} <br>dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;dominio <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;cuenta <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;nombre</font>
      </td>
    </tr>
    <tr>
      <td>COSMOS</td>
      <td>Contenedor</td>
      <td>Clúster virtual</td>
      <td>
        <font size=2>Protocolo: cosmos <br>autenticación: {básicas, windows} <br>dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;dirección URL</font>
      </td>
    </tr>
    <tr>
      <td>COSMOS</td>
      <td>Tabla</td>
      <td>Vista de secuencia, conjunto de secuencias</td>
      <td>
        <font size=2>Protocolo: cosmos <br>autenticación: {básicas, windows} <br>dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;dirección URL</font>
      </td>
    </tr>
    <tr>
      <td>DataZen</td>
      <td>Contenedor</td>
      <td>Sitio</td>
      <td>
        <font size=2>Protocolo: http <br>autenticación: {none, basic, windows, oauth} <br>dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;dirección URL</font>
      </td>
    </tr>
    <tr>
      <td>DataZen</td>
      <td>Informe</td>
      <td>Informe de panel</td>
      <td>
        <font size=2>Protocolo: http <br>autenticación: {none, basic, windows, oauth} <br>dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;dirección URL</font>
      </td>
    </tr>
    <tr>
      <td>Db2</td>
      <td>Contenedor</td>
      <td>Base de datos</td>
      <td>
        <font size=2>Protocolo: db2 <br>autenticación: {básicas, windows} <br>dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de datos</font>
      </td>
    </tr>
    <tr>
      <td>Db2</td>
      <td>Tabla</td>
      <td>Vista de la tabla</td>
      <td>
        <font size=2>Protocolo: db2 <br>autenticación: {básicas, windows} <br>dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de datos <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objeto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;esquema</font>
      </td>
    </tr>
    <tr>
      <td>Sistema de archivos</td>
      <td>Tabla</td>
      <td>Archivo</td>
      <td>
        <font size=2>Protocolo: archivo <br>autenticación: {ninguna, básicas, windows} <br>dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ruta de acceso</font>
      </td>
    </tr>
    <tr>
      <td>FTP</td>
      <td>Tabla</td>
      <td>Directorio de archivo</td>
      <td>
        <font size=2>Protocolo: ftp <br>autenticación: {ninguna, básicas, windows} <br>dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;dirección URL</font>
      </td>
    </tr>
    <tr>
      <td>Sistema de archivos distribuido Hadoop</td>
      <td>Contenedor</td>
      <td>Clúster</td>
      <td>
        <font size=2>Protocolo: webhdfs <br>autenticación: {básica, oauth} <br>dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;dirección URL</font>
      </td>
    </tr>
    <tr>
      <td>Sistema de archivos distribuido Hadoop</td>
      <td>Tabla</td>
      <td>Directorio de archivo</td>
      <td>
        <font size=2>Protocolo: webhdfs <br>autenticación: {básica, oauth} <br>dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;dirección URL</font>
      </td>
    </tr>
    <tr>
      <td>Sección</td>
      <td>Contenedor</td>
      <td>Base de datos</td>
      <td>
        <font size=2>Protocolo: subárbol <br>autenticación: {hdinsight, básica, nombre de usuario, ninguno} <br>dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de datos <br>connectionProperties: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;serverProtocol: {hive2}</font>
      </td>
    </tr>
    <tr>
      <td>Sección</td>
      <td>Tabla</td>
      <td>Vista de la tabla</td>
      <td>
        <font size=2>Protocolo: subárbol <br>autenticación: {hdinsight, básica, nombre de usuario, ninguno} <br>dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de datos <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objeto <br>connectionProperties: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;serverProtocol: {hive2}</font>
      </td>
    </tr>
    <tr>
      <td>Http</td>
      <td>Contenedor</td>
      <td>Sitio</td>
      <td>
        <font size=2>Protocolo: http <br>autenticación: {none, basic, windows, oauth} <br>dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;dirección URL</font>
      </td>
    </tr>
    <tr>
      <td>Http</td>
      <td>Informe</td>
      <td>Informe de panel</td>
      <td>
        <font size=2>Protocolo: http <br>autenticación: {none, basic, windows, oauth} <br>dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;dirección URL</font>
      </td>
    </tr>
    <tr>
      <td>Http</td>
      <td>Tabla</td>
      <td>Punto final, archivo</td>
      <td>
        <font size=2>Protocolo: http <br>autenticación: {none, basic, windows, oauth} <br>dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;dirección URL</font>
      </td>
    </tr>
    <tr>
      <td>MySQL</td>
      <td>Contenedor</td>
      <td>Base de datos</td>
      <td>
        <font size=2>Protocolo: mysql <br>autenticación: {protocolo, windows} <br>dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de datos</font>
      </td>
    </tr>
    <tr>
      <td>MySQL</td>
      <td>Tabla</td>
      <td>Vista de la tabla</td>
      <td>
        <font size=2>Protocolo: mysql <br>autenticación: {protocolo, windows} <br>dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de datos <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objeto</font>
      </td>
    </tr>
    <tr>
      <td>OData</td>
      <td>Contenedor</td>
      <td>Contenedor de entidades</td>
      <td>
        <font size=2>Protocolo: odata <br>autenticación: {ninguna, básicas, windows} <br>dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;dirección URL</font>
      </td>
    </tr>
    <tr>
      <td>OData</td>
      <td>Tabla</td>
      <td>Conjunto de entidades, función</td>
      <td>
        <font size=2>Protocolo: odata <br>autenticación: {ninguna, básicas, windows} <br>dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;dirección URL <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;recursos</font>
      </td>
    </tr>
    <tr>
      <td>Base de datos de Oracle</td>
      <td>Contenedor</td>
      <td>Base de datos</td>
      <td>
        <font size=2>Protocolo: oracle <br>autenticación: {protocolo, windows} <br>dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de datos</font>
      </td>
    </tr>
    <tr>
      <td>Base de datos de Oracle</td>
      <td>Tabla</td>
      <td>Vista de la tabla</td>
      <td>
        <font size=2>Protocolo: oracle <br>autenticación: {protocolo, windows} <br>dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de datos <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objeto</font>
      </td>
    </tr>
    <tr>
      <td>PostgreSQL</td>
      <td>Contenedor</td>
      <td>Base de datos</td>
      <td>
        <font size=2>Protocolo: postgresql <br>autenticación: {básicas, windows} <br>dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de datos</font>
      </td>
    </tr>
    <tr>
      <td>PostgreSQL</td>
      <td>Tabla</td>
      <td>Vista de la tabla</td>
      <td>
        <font size=2>Protocolo: postgresql <br>autenticación: {básicas, windows} <br>dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de datos <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objeto</font>
      </td>
    </tr>
    <tr>
      <td>Power BI</td>
      <td>Contenedor</td>
      <td>Sitio</td>
      <td>
        <font size=2>Protocolo: http <br>autenticación: {none, basic, windows, oauth} <br>dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;dirección URL</font>
      </td>
    </tr>
    <tr>
      <td>Power BI</td>
      <td>Informe</td>
      <td>Informe de panel</td>
      <td>
        <font size=2>Protocolo: http <br>autenticación: {none, basic, windows, oauth} <br>dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;dirección URL</font>
      </td>
    </tr>
    <tr>
      <td>Power Query</td>
      <td>Tabla</td>
      <td>Combinación de datos</td>
      <td>
        <font size=2>Protocolo: power query <br>autenticación: {oauth} <br>dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;dirección URL</font>
      </td>
    </tr>
    <tr>
      <td>Salesforce</td>
      <td>Tabla</td>
      <td>Objeto</td>
      <td>
        <font size=2>Protocolo: salesforce com <br>autenticación: {básicas, windows} <br>dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;loginServer <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;clase <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;itemName</font>
      </td>
    </tr>
    <tr>
      <td>Hana SAP</td>
      <td>Contenedor</td>
      <td>Servidor</td>
      <td>
        <font size=2>Protocolo: hana de sap de sql <br>autenticación: {protocolo, windows} <br>dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor</font>
      </td>
    </tr>
    <tr>
      <td>Hana SAP</td>
      <td>Tabla</td>
      <td>Vista</td>
      <td>
        <font size=2>Protocolo: hana de sap de sql <br>autenticación: {protocolo, windows} <br>dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objeto</font>
      </td>
    </tr>
    <tr>
      <td>SharePoint</td>
      <td>Tabla</td>
      <td>Lista</td>
      <td>
        <font size=2>Protocolo: lista de sharepoint <br>autenticación: {básicas, windows} <br>dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;dirección URL</font>
      </td>
    </tr>
    <tr>
      <td>Almacén de datos SQL</td>
      <td>Comando</td>
      <td>Procedimiento almacenado</td>
      <td>
        <font size=2>Protocolo: tds <br>autenticación: {protocolo, windows} <br>dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de datos <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objeto</font>
      </td>
    </tr>
    <tr>
      <td>Almacén de datos SQL</td>
      <td>TableValuedFunction</td>
      <td>Función con valores de tabla</td>
      <td>
        <font size=2>Protocolo: tds <br>autenticación: {protocolo, windows} <br>dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de datos <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objeto</font>
      </td>
    </tr>
    <tr>
      <td>Almacén de datos SQL</td>
      <td>Contenedor</td>
      <td>Base de datos</td>
      <td>
        <font size=2>Protocolo: tds <br>autenticación: {protocolo, windows} <br>dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de datos</font>
      </td>
    </tr>
    <tr>
      <td>Almacén de datos SQL</td>
      <td>Tabla</td>
      <td>Vista de la tabla</td>
      <td>
        <font size=2>Protocolo: tds <br>autenticación: {protocolo, windows} <br>dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de datos <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objeto</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>Comando</td>
      <td>Procedimiento almacenado</td>
      <td>
        <font size=2>Protocolo: tds <br>autenticación: {protocolo, windows} <br>dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de datos <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objeto</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>TableValuedFunction</td>
      <td>Función con valores de tabla</td>
      <td>
        <font size=2>Protocolo: tds <br>autenticación: {protocolo, windows} <br>dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de datos <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objeto</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>Contenedor</td>
      <td>Base de datos</td>
      <td>
        <font size=2>Protocolo: tds <br>autenticación: {protocolo, windows} <br>dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de datos</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>Tabla</td>
      <td>Vista de la tabla</td>
      <td>
        <font size=2>Protocolo: tds <br>autenticación: {protocolo, windows} <br>dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de datos <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objeto</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services multidimensionales</td>
      <td>Contenedor</td>
      <td>Modelo</td>
      <td>
        <font size=2>Protocolo: servicios de análisis <br>autenticación: {windows, basic, anónimo, ninguno} <br>dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de datos <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modelo</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services multidimensionales</td>
      <td>KPI</td>
      <td>KPI</td>
      <td>
        <font size=2>Protocolo: servicios de análisis <br>autenticación: {windows, basic, anónimo, ninguno} <br>dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de datos <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modelo <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objeto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objectType: {KPI}</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services multidimensionales</td>
      <td>Medida</td>
      <td>Medida</td>
      <td>
        <font size=2>Protocolo: servicios de análisis <br>autenticación: {windows, basic, anónimo, ninguno} <br>dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de datos <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modelo <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objeto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objectType: {medida}</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services multidimensionales</td>
      <td>Tabla</td>
      <td>Dimensión</td>
      <td>
        <font size=2>Protocolo: servicios de análisis <br>autenticación: {windows, basic, anónimo, ninguno} <br>dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de datos <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modelo <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objeto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objectType: {dimensión}</font>
      </td>
    </tr>
    <tr>
      <td>Tabulares de SQL Server Analysis Services</td>
      <td>Contenedor</td>
      <td>Modelo</td>
      <td>
        <font size=2>Protocolo: servicios de análisis <br>autenticación: {windows, basic, anónimo, ninguno} <br>dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de datos <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modelo</font>
      </td>
    </tr>
    <tr>
      <td>Tabulares de SQL Server Analysis Services</td>
      <td>KPI</td>
      <td>KPI</td>
      <td>
        <font size=2>Protocolo: servicios de análisis <br>autenticación: {windows, basic, anónimo, ninguno} <br>dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de datos <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modelo <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objeto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objectType: {KPI}</font>
      </td>
    </tr>
    <tr>
      <td>Tabulares de SQL Server Analysis Services</td>
      <td>Medida</td>
      <td>Medida</td>
      <td>
        <font size=2>Protocolo: servicios de análisis <br>autenticación: {windows, basic, anónimo, ninguno} <br>dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de datos <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modelo <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objeto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objectType: {medida}</font>
      </td>
    </tr>
    <tr>
      <td>Tabulares de SQL Server Analysis Services</td>
      <td>Tabla</td>
      <td>Tabla</td>
      <td>
        <font size=2>Protocolo: servicios de análisis <br>autenticación: {windows, basic, anónimo, ninguno} <br>dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de datos <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modelo <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objeto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objectType: {tabla}</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Reporting Services</td>
      <td>Contenedor</td>
      <td>Servidor</td>
      <td>
        <font size=2>Protocolo: servicios de informes <br>autenticación: {windows} <br>dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;versión: {ReportingService2010}</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Reporting Services</td>
      <td>Informe</td>
      <td>Informe</td>
      <td>
        <font size=2>Protocolo: servicios de informes <br>autenticación: {windows} <br>dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ruta de acceso <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;versión: {ReportingService2010}</font>
      </td>
    </tr>
    <tr>
      <td>Teradata</td>
      <td>Contenedor</td>
      <td>Base de datos</td>
      <td>
        <font size=2>Protocolo: teradata <br>autenticación: {protocolo, windows} <br>dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de datos</font>
      </td>
    </tr>
    <tr>
      <td>Teradata</td>
      <td>Tabla</td>
      <td>Vista de la tabla</td>
      <td>
        <font size=2>Protocolo: teradata <br>autenticación: {protocolo, windows} <br>dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de datos <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objeto</font>
      </td>
    </tr>
    <tr>
      <td>Servicios de datos SQL Server patrón</td>
      <td>Contenedor</td>
      <td>Modelo</td>
      <td>
        <font size="2">Protocolo: mssql mds <br>autenticación: {windows} <br>dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;dirección URL <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modelo <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Versión</font>
      </td>
    </tr>
    <tr>
      <td>Servicios de datos SQL Server patrón</td>
      <td>Tabla</td>
      <td>Entidad</td>
      <td>
        <font size="2">Protocolo: mssql mds <br>autenticación: {windows} <br>dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;dirección URL <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modelo <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Versión <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;entidad</font>
      </td>
    </tr>
    <tr>
      <td>Sí (no uno de los anteriores)</td>
      <td>\*</td>
      <td>\*</td>
      <td>
        <font size=2>Protocolo: activo genérico <br>dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;idtema</font>
      </td>
    </tr>
</table>

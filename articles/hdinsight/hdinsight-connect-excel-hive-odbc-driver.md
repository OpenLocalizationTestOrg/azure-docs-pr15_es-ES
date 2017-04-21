<properties
   pageTitle="Conectar Excel a Hadoop con el controlador ODBC subárbol | Microsoft Azure"
   description="Obtenga información sobre cómo configurar y usar el controlador ODBC subárbol de Microsoft para Excel para consultar los datos en un clúster de HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="mumian"
   manager="jhubbard"
   tags="azure-portal"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/19/2016"
   ms.author="jgao"/>

#<a name="connect-excel-to-hadoop-with-the-microsoft-hive-odbc-driver"></a>Conectar Excel a Hadoop con el controlador ODBC subárbol de Microsoft

[AZURE.INCLUDE [ODBC-JDBC-selector](../../includes/hdinsight-selector-odbc-jdbc.md)]

Solución de datos grande de Microsoft integra componentes de inteligencia empresarial (BI) de Microsoft con clústeres Apache Hadoop que se han implementado HDInsight de Azure. Un ejemplo de esta integración es la capacidad de conectar Excel con el almacén de datos de la sección de un clúster de Hadoop en HDInsight con el controlador de Microsoft subárbol Open Database Connectivity (ODBC).

También es posible conectar los datos asociados a un clúster de HDInsight y otros orígenes de datos, como otros clústeres de Hadoop (no-HDInsight), de Excel mediante el complemento de Microsoft Power Query para Excel. Para obtener información sobre cómo instalar y usar Power Query, vea [Conectar Excel a HDInsight con Power Query][hdinsight-power-query].

> [AZURE.NOTE] Aunque los pasos de este artículo se pueden utilizar con clúster Linux o de HDInsight de Windows, Windows se requiere para la estación de trabajo de cliente.

**Requisitos previos**:

Antes de comenzar este artículo, debe tener el siguiente:

- **HDInsight un clúster**. Para crear uno, consulte [Introducción a Azure HDInsight][hdinsight-get-started].
- **A estación de trabajo** con Office 2013 Professional Plus, Office 365 Pro Plus, Excel 2013 independiente o Office 2010 Professional Plus.


##<a name="install-microsoft-hive-odbc-driver"></a>Instalar a controlador ODBC subárbol de Microsoft

Descargar e instalar controlador ODBC subárbol de Microsoft desde el [Centro de descarga de][hive-odbc-driver-download].

Este controlador se puede instalar en versiones de 32 bits o 64 bits de Windows 7, Windows 8, Windows 10, Windows Server 2008 R2 y Windows Server 2012 y permitir la conexión a HDInsight de Azure (1,6 y versiones posteriores) y Azure HDInsight emulador (v.1.0.0.0 y posteriores). Debe instalar la versión que coincida con la versión de la aplicación en el que va a utilizar el controlador ODBC. En este tutorial, se usará el controlador de Office Excel.

##<a name="create-hive-odbc-data-source"></a>Crear origen de datos ODBC subárbol

Los pasos siguientes muestran cómo crear un origen de datos ODBC subárbol.

1. Desde Windows 8 o Windows 10, presione la tecla Windows para abrir la pantalla de inicio y, a continuación, escriba **los orígenes de datos**.
2. Haga clic en **Configurar datos ODBC orígenes (32 bits)** o **Configurar orígenes de datos ODBC (64 bits)** , según la versión de Office. Si usa Windows 7, elija **Orígenes de datos ODBC (32 bits)** u **Orígenes de datos de ODBC (64 bits)** de **Herramientas administrativas**. Esto inicia el cuadro de diálogo **Administrador de origen de datos ODBC** .

    ![Administrador de origen de datos ODBC][img-hdi-simbahiveodbc-datasource-admin]

3. Desde el servidor DNS de usuario, haga clic en **Agregar** para abrir al asistente **Crear nuevo origen de datos** .
4. Seleccione **Controlador ODBC subárbol de Microsoft**y, a continuación, haga clic en **Finalizar**. Esto inicia el cuadro de diálogo de **Configuración de DNS de controlador de ODBC de subárbol de Microsoft** .

5. Escriba o seleccione los valores siguientes:

    (Propiedad)|Descripción
    ---|---
    Nombre de origen de datos|Asigne un nombre a su origen de datos
    Host|Escriba &lt;HDInsightClusterName >. azurehdinsight.net. Por ejemplo, myHDICluster.azurehdinsight.net
    Puerto|Use <strong>443</strong>. (Este puerto se ha cambiado de 563 443.)
    Base de datos|Use el <strong>valor predeterminado</strong>.
    Tipo de servidor de sección|Seleccione la <strong>sección servidor 2</strong>
    Mecanismo|Seleccione el <strong>Servicio de HDInsight de Azure</strong>
    Ruta de acceso HTTP|Dejar en blanco.
    Nombre de usuario|Escriba el nombre de usuario de clúster HDInsight. Este es el nombre de usuario creado durante el proceso de aprovisionamiento de clúster. Si utiliza la opción crear rápido, el nombre de usuario predeterminado es <strong>admin</strong>.
    Contraseña|Escriba la contraseña de usuario de clúster HDInsight.
    </table>

    Hay algunos parámetros importantes a tener en cuenta al hacer clic en **Opciones avanzadas**:

    Parámetro|Descripción
    ---|---
    Usar consulta nativa|Cuando se selecciona, el controlador ODBC no intenta convertir TSQL en HiveQL. Deberá usarlo únicamente si está seguro de que envío de instrucciones de HiveQL puros al 100%. Al conectarse a SQL Server o base de datos de SQL Azure, debe dejar desactivada.
    Filas encontradas por bloque|Al recuperar una gran cantidad de registros, ajuste este parámetro puede necesaria para garantizar un rendimiento óptimo.
    Longitud de la columna cadena de forma predeterminada, longitud de la columna binaria, escala de la columna Decimal|El tipo de datos longitudes y precisiones pueden afectar a cómo se devuelven datos. Hará información incorrecto devolverán debido a la pérdida de precisión o truncamiento.


    ![Opciones avanzadas][img-HiveOdbc-DataSource-AdvancedOptions]

6. Haga clic en **probar** para probar el origen de datos. Cuando el origen de datos está correctamente configurado, se muestran *pruebas FINALIZÓ correctamente!*.
7. Haga clic en **Aceptar** para cerrar el cuadro de diálogo de prueba. Ahora debe aparecer el nuevo origen de datos en el **Administrador de origen de datos ODBC**.
8. Haga clic en **Aceptar** para salir del asistente.

##<a name="import-data-into-excel-from-hdinsight"></a>Importar datos a Excel desde HDInsight

Los pasos siguientes describen la manera de importar datos desde una tabla subárbol en un libro de Excel con el origen de datos ODBC que creó en los pasos anteriores.

1. Abra un libro nuevo o existente en Excel.
2. En la pestaña **datos** , haga clic en **Desde otros orígenes de datos**y, a continuación, haga clic en **Desde el Asistente para la conexión de datos** para iniciar el **Asistente para la conexión de datos**.

    ![Asistente para la conexión de datos abierta][img-hdi-simbahiveodbc.excel.dataconnection]

3. Seleccione **DSN ODBC** como origen de datos y, a continuación, haga clic en **siguiente**.
4. Orígenes de datos ODBC, seleccione el nombre del origen de datos que creó en el paso anterior y, a continuación, haga clic en **siguiente**.
5. Vuelva a escribir la contraseña para el clúster en el asistente y, a continuación, haga clic en **prueba** para comprobar la configuración una vez más, si es necesario.
6. Haga clic en **Aceptar** para cerrar el cuadro de diálogo de prueba.
7. Haga clic en **Aceptar**. Espere a que el cuadro de diálogo **Seleccionar base de datos y tabla** abrir. Esto puede tardar unos segundos.
8. Seleccione la tabla que desea importar y, a continuación, haga clic en **siguiente**. El *hivesampletable* es una tabla subárbol de ejemplo que se incluye con HDInsight clústeres.  Puede elegir, si todavía no lo ha creado uno. Para más información sobre cómo ejecutar subárbol consultas y crear tablas de sección, consulte [Usar subárbol con HDInsight][hdinsight-use-hive].
8. Haga clic en **Finalizar**.
9. En el cuadro de diálogo **Importar datos** , puede cambiar o especificar la consulta. Para ello, haga clic en **Propiedades**. Esto puede tardar unos segundos.
10. Haga clic en la pestaña **definición** y, a continuación, anexar **200 límite** a la instrucción select de sección en el cuadro de texto de **texto del comando** . La modificación limita el conjunto de registros devueltos a 200.

    ![Propiedades de conexión][img-hdi-simbahiveodbc-excel-connectionproperties]

11. Haga clic en **Aceptar** para cerrar el cuadro de diálogo Propiedades de conexión.
12. Haga clic en **Aceptar** para cerrar el cuadro de diálogo **Importar datos** .  
13. Vuelva a escribir la contraseña y, a continuación, haga clic en **Aceptar**. Se necesitan unos segundos antes de que obtienen importar datos a Excel.

##<a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido cómo usar el controlador ODBC subárbol de Microsoft para recuperar datos de la HDInsight Service en Excel. Asimismo, puede recuperar datos de la HDInsight Service a base de datos de SQL. También es posible cargar los datos en un HDInsight Service. Para obtener más información, consulte:

- [Analizar datos de vuelo retraso usando HDInsight][hdinsight-analyze-flight-data]
- [Cargar datos a HDInsight][hdinsight-upload-data]
- [Usar Sqoop con HDInsight] [hdinsight-use-sqoop]


[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-get-started]: hdinsight-hadoop-tutorial-get-started-windows.md

[hive-odbc-driver-download]: http://go.microsoft.com/fwlink/?LinkID=286698

[img-hdi-simbahiveodbc-datasource-admin]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveOdbc.DataSourceAdmin1.png
[img-HiveOdbc-DataSource-AdvancedOptions]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.HiveOdbc.DataSource.AdvancedOptions1.png
[img-hdi-simbahiveodbc-excel-connectionproperties]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveODBC.Excel.ConnectionProperties1.png
[img-hdi-simbahiveodbc.excel.dataconnection]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveOdbc.Excel.DataConnection1.png

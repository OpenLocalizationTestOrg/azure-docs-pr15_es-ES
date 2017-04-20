<properties 
    pageTitle="Supervisar las dependencias, excepciones y tiempos de ejecución en aplicaciones web de Java" 
    description="Supervisión de su sitio Web de Java con recomendaciones de aplicación extendida" 
    services="application-insights" 
    documentationCenter="java"
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/24/2016" 
    ms.author="awills"/>
 
# <a name="monitor-dependencies-exceptions-and-execution-times-in-java-web-apps"></a>Supervisar las dependencias, excepciones y tiempos de ejecución en aplicaciones web de Java

*Información de la aplicación está en vista previa.*

Si tiene [instrumentar la aplicación web de Java con recomendaciones de aplicación][java], puede utilizar el agente de Java para obtener recomendaciones más profundos, sin ningún cambio de código:


* **Dependencias:** Datos acerca de las llamadas que realiza la aplicación a otros componentes, incluidos:
 * **Resto llamadas** realizadas mediante HttpClient, OkHttp y RestTemplate (resorte).
 * **Redis** llamadas realizadas mediante el cliente de Jedis. Si la llamada tarda más de 10s, el agente recopila también los argumentos de la llamada.
 * **[Llamadas JDBC](http://docs.oracle.com/javase/7/docs/technotes/guides/jdbc/)** - MySQL, SQL Server, PostgreSQL, SQLite, DB de Oracle o Apache azules DB. llamadas "executeBatch" son compatibles. Para MySQL y PostgreSQL, si la llamada tarda más de 10s, el agente informa el plan de consulta. 
* **Detecta excepciones:** Datos sobre las excepciones que se controlan mediante su código.
* **Tiempo de ejecución del método:** Datos sobre el tiempo que se tarda en ejecutar métodos específicos.

Para utilizar al agente de Java, instalarlo en el servidor. Las aplicaciones web deben instalarse con la [Aplicación perspectivas Java SDK][java].

## <a name="install-the-application-insights-agent-for-java"></a>Instalar al agente de aplicación perspectivas para Java

1. En el equipo está ejecutando el servidor de Java, [Descargue al agente](https://aka.ms/aijavasdk).
2. Editar el script de inicio del servidor de aplicaciones y agregar JVM siguiente:

    `javaagent:`*ruta de acceso completa al archivo JAR agente*

    Por ejemplo, en Tomcat en un equipo Linux:

    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`


3. Reinicie el servidor de aplicaciones.

## <a name="configure-the-agent"></a>Configurar el agente

Cree un archivo llamado `AI-Agent.xml` y colóquelo en la misma carpeta que el archivo JAR de agente.

Establecer el contenido del archivo xml. Editar en el siguiente ejemplo para incluir u omitir las características que desee. 

```XML

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsightsAgent>
      <Instrumentation>
        
        <!-- Collect remote dependency data -->
        <BuiltIn enabled="true">
           <!-- Disable Redis or alter threshold call duration above which arguments are sent.
               Defaults: enabled, 10000 ms -->
           <Jedis enabled="true" thresholdInMS="1000"/>
           
           <!-- Set SQL query duration above which query plan is reported (MySQL, PostgreSQL). Default is 10000 ms. -->
           <MaxStatementQueryLimitInMS>1000</MaxStatementQueryLimitInMS>
        </BuiltIn>

        <!-- Collect data about caught exceptions 
             and method execution times -->

        <Class name="com.myCompany.MyClass">
           <Method name="methodOne" 
               reportCaughtExceptions="true"
               reportExecutionTime="true"
               />

           <!-- Report on the particular signature
                void methodTwo(String, int) -->
           <Method name="methodTwo"
              reportExecutionTime="true"
              signature="(Ljava/lang/String;I)V" />
        </Class>
        
      </Instrumentation>
    </ApplicationInsightsAgent>

```

Debe habilitar la excepción de informes y los intervalos de método para métodos individuales.

De forma predeterminada, `reportExecutionTime` es true y `reportCaughtExceptions` es false.

## <a name="view-the-data"></a>Ver los datos

En el recurso de aplicación perspectivas agregados tiempos de ejecución remotos dependencia y método aparece [en el mosaico de rendimiento][metrics]. 

Para buscar instancias individuales de los informes de dependencia, excepción y método, abrir [búsqueda][diagnostic]. 

[Diagnosticar problemas de dependencia: más información](app-insights-dependencies.md#diagnosis).



## <a name="questions-problems"></a>¿Preguntas? ¿Tiene problemas?

* ¿Sin datos? [Establecer excepciones de firewall](app-insights-ip-addresses.md)
* [Solución de problemas de Java](app-insights-java-troubleshoot.md)



<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiexceptions]: app-insights-api-custom-events-metrics.md#track-exception
[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
[usage]: app-insights-web-track-usage.md

 

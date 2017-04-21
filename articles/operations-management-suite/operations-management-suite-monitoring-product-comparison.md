<properties 
   pageTitle="Comparación de productos de supervisión de Microsoft | Microsoft Azure"
   description="Suite de administración de operaciones de Microsoft (OMS) es la nube de Microsoft basado en solución de administración de TI que le ayuda a administrar y proteger su local y la infraestructura de nube.  En este artículo se identifica los distintos servicios que se incluyen en OMS y se proporciona vínculos a su contenido detallada."
   services="operations-management-suite"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/27/2016"
   ms.author="bwren" />

# <a name="microsoft-monitoring-product-comparison"></a>Comparación de los productos de supervisión de Microsoft

En este artículo se proporciona una comparación entre System Center Operations Manager (SCOM) y el análisis de registro en conjunto de aplicaciones de administración de operaciones (OMS) en términos de su arquitectura, la lógica de cómo supervisar los recursos y cómo realizar análisis de los datos recopilados.  Esto es para dar a conocer sus diferencias y puntos fuertes.  

## <a name="basic-architecture"></a>Arquitectura básica
### <a name="system-center-operations-manager"></a>System Center Operations Manager

Todos los componentes SCOM están instalados en el centro de datos.  [Agentes están instalados](http://technet.microsoft.com/library/hh551142.aspx) en equipos de Windows y Linux administrados por SCOM.  Agentes de conectan a [Los servidores de administración](https://technet.microsoft.com/library/hh301922.aspx) que comunicarse con el almacén de datos y la base de datos SCOM.  Agentes dependen de la autenticación de dominio para conectarse a los servidores de administración.  Aquellos fuera de un dominio de confianza pueden realizar la autenticación de certificado o conectarse a un [Servidor de puerta de enlace](https://technet.microsoft.com/library/hh212823.aspx).

SCOM requiere dos bases de datos SQL, uno para datos operativos y otro almacén de datos para admitir informes y análisis de datos.  Un [Servidor de informes](https://technet.microsoft.com/library/hh298611.aspx) ejecuta SQL Reporting Services para informar de los datos del almacén de datos. 

SCOM puede supervisar los recursos de nube con los paquetes de administración para productos como [Azure](https://www.microsoft.com/download/details.aspx?id=38414), [Office 365](https://www.microsoft.com/download/details.aspx?id=43708)y [AWS](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/AWSManagementPack.html).  Estos paquetes de administración usar a uno o más agentes locales como servidores proxy para descubrir recursos de la nube y flujos de trabajo ejecutando para medir el rendimiento y la disponibilidad.  Agentes de proxy también se usan para [supervisar los dispositivos de red](https://technet.microsoft.com/library/hh212935.aspx) y otros recursos externos.

La consola de operaciones es una aplicación de Windows que se conecta a uno de los servidores de administración y permite que el administrador ver y analizar los datos recopilados y configurar el entorno de SCOM.  Una consola basada en web puede estar alojada en cualquier servidor IIS y proporciona análisis de datos a través de un explorador.

![Arquitectura SCOM](media/operations-management-suite-monitoring-product-comparison/scom-architecture.png)

### <a name="log-analytics"></a>Análisis de registro

La mayoría de los componentes OMS se encuentran en la nube de Azure para que pueda implementar y administrar con mínimo costo y esfuerzo administrativo.  Todos los datos recopilados por el análisis de registro se almacena en el repositorio OMS.

Análisis de registro puede recopilar datos de uno de los tres orígenes:

- Física y máquinas virtuales que ejecutan Windows y el [Agente de supervisión de Microsoft (MMA)](https://technet.microsoft.com/library/mt484108.aspx) o Linux y el [Agente de conjunto de aplicaciones de administración de operaciones para Linux](https://technet.microsoft.com/library/mt622052.aspx).  Estos equipos pueden ser local o máquinas virtuales de Azure u otra nube.
- Una cuenta de almacenamiento de Azure con datos de [Diagnósticos de Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) recopilados por trabajo Azure, función, web o máquina virtual.
- [Conexión a un grupo de administración de SCOM](https://technet.microsoft.com/library/mt484104.aspx).  En esta configuración, los agentes de comunican con los servidores de administración de SCOM que entregan los datos a la base de datos SCOM donde se entrega a continuación, en el almacén de datos OMS.
Los administradores analizar los datos recopilados y configuración el análisis de registro con el portal OMS que está hospedado en Azure y puede tener acceso desde cualquier explorador.  Aplicaciones móviles para tener acceso a estos datos están disponibles para las plataformas estándares.

![Arquitectura de análisis de registro](media/operations-management-suite-monitoring-product-comparison/log-analytics-architecture.png)

### <a name="integrating-scom-and-log-analytics"></a>Integración de SCOM y análisis de registro

Cuando se utiliza SCOM como origen de datos para el análisis de registro puede aprovechar las características de ambos productos en un híbrido supervisión entorno.  Puede configurar a agentes SCOM existentes mediante la consola de operaciones administrarse OMS, además de seguir administrando los paquetes de administración de SCOM.  
Datos de un grupo de administración de SCOM conectado se ha entregado al análisis de registro con uno de los cuatro métodos:

- Datos de rendimiento y eventos son recopilados por el agente de y entregados al SCOM.  Servidores de administración de SCOM entregan los datos para el análisis de registro.
- Algunos eventos, como los registros IIS y seguridad seguirán se entregarán directamente al registro de análisis desde el agente.
- Algunas soluciones se entregue el software adicional en el agente o requieren la instalación de software para recopilar datos adicionales.  Estos datos normalmente se enviará directamente al análisis de registro.
- Algunas soluciones recopilar datos directamente de los servidores de administración de SCOM que no se origine desde el agente.  Por ejemplo, la [solución de administración de alertas](https://technet.microsoft.com/library/mt484092.aspx) recopila alertas de SCOM después de haber creado.

## <a name="monitoring-logic"></a>Lógica de supervisión
SCOM y análisis de registro de trabajan con datos similares recopilados agentes pero tienen diferencias fundamentales en cómo definir e implementar su lógica de recopilación de datos y cómo analizan los datos que recopilan.

### <a name="operations-manager"></a>Operations Manager
Supervisión de lógica de SCOM se implementa en [los paquetes de administración](https://technet.microsoft.com/library/hh457558.aspx) que contienen lógica para detectar los componentes para supervisar, medir el mantenimiento de los componentes y para recopilar datos de análisis.  Supervisar datos podría ser tan sencillo como un evento o rendimiento contador de recopilación o podría usar lógica compleja que se ha implementado en una secuencia de comandos.  Paquetes de administración que incluyen la supervisión completa están disponibles para una variedad de [Microsoft y aplicaciones de terceros](http://go.microsoft.com/fwlink/?LinkId=82105) además de los dispositivos de hardware y de red.  Puede [crear sus propios módulos de administración](http://aka.ms/mpauthor) para las aplicaciones personalizadas.

Paquetes de administración contienen varios flujos de trabajo que cada una realiza alguna función distinct supervisión como muestreo un contador de rendimiento, comprobar el estado de un servicio o ejecutar una secuencia de comandos.  Cada flujo de trabajo se ejecuta de forma independiente y define sus propios resultados como base de datos que se escribe y si generará una alerta. 

Puede reemplazar los detalles del flujo de trabajo como la frecuencia que se ejecutan, el umbral donde tenga en cuenta un error y la gravedad de la alerta que generan.  También puede proporcionar funciones adicionales agregando sus propios flujos de trabajo.

![Reemplaza](media/operations-management-suite-monitoring-product-comparison/scom-overrides.png)

Paquetes de administración se instala en la base de datos de Operations Manager y a distribuir automáticamente a agentes de servidores de administración.  Cada agente automáticamente descargar paquetes de administración y cargar los flujos de trabajo relevantes para las aplicaciones que se han instalado.  Los datos recopilados por el agente se ha entregado al servidor de administración de inserción en el almacén de datos y la base de datos SCOM.  La consola de operaciones le permite ver y analizar los datos a través de vistas personalizadas, incluidos en el módulo de administración de informes y paneles.

En el siguiente diagrama se muestra la distribución de paquetes de administración.

![Flujo de paquete de administración](media/operations-management-suite-monitoring-product-comparison/scom-mpflow.png)

### <a name="log-analytics"></a>Análisis de registro
#### <a name="event-and-performance-collection"></a>Evento y recopilación de rendimiento
Análisis de registro recopila eventos y contadores de rendimiento de los sistemas de agente con orígenes de registro de eventos de Windows, los registros IIS y registro del sistema.  Puede definir los criterios para que los datos se recopilan a través del portal de análisis de registro y, a continuación, crear consultas de registro para analizar los datos recopilados.  Al crear el área de trabajo OMS y, a continuación, puede definir datos adicionales para aplicaciones concretas, se define un conjunto de criterios estándar. 

![Definir registros de eventos de análisis de registro](media/operations-management-suite-monitoring-product-comparison/log-analytics-definedata.png)

Aunque SCOM tiene varios flujos de trabajo detallados que suelen definen criterios específicos para los datos y la acción que debe realizarse en respuesta, análisis de registro tiene criterios más generales de recopilación de datos.  Las consultas de registro y soluciones proporcionan más criterios específicos para analizar y actuar en datos específicos en la nube después de que se han recopilado.

#### <a name="solutions"></a>Soluciones
Soluciones proporcionan lógica adicional para análisis y recopilación de datos.  Puede seleccionar soluciones para agregar a su suscripción de OMS desde la Galería de soluciones.

![Galería de soluciones](media/operations-management-suite-monitoring-product-comparison/log-analytics-solutiongallery.png)

Las soluciones principalmente se ejecutan en la nube que proporciona análisis de eventos y contadores de rendimiento recopilados en el repositorio OMS.  También pueden definir adicionales recopilar datos que se pueden analizar con el registro de consultas o por la interfaz de usuario adicionales proporcionada por la solución en el panel OMS. 

Por ejemplo, la [solución de seguimiento de cambios](https://technet.microsoft.com/library/mt484099.aspx) detecta los cambios de configuración en los sistemas de agente y escribe eventos en el repositorio OMS que se pueden analizar con varias vistas gráficos que resumen detectan cambios.  Puede desglosar desde la vista resumida en consultas de registro que se muestran los datos detallados recopilados por la solución.


Mientras que puede seleccionar las soluciones que se agrega a su suscripción, actualmente no tiene la capacidad para crear sus propias soluciones.  Puede seleccionar los eventos y contadores de rendimiento para recopilar y crear vistas personalizadas en función de sus propias consultas de registro.

La lógica de análisis de registro de la supervisión se resume en el diagrama siguiente.

![Flujo de solución de análisis de registro](media/operations-management-suite-monitoring-product-comparison/log-analytics-solution-flow.png)

## <a name="health-monitoring"></a>Seguimiento de estado
### <a name="operations-manager"></a>Operations Manager
SCOM puede modelar los distintos componentes de una aplicación y proporcionar un estado en tiempo real para cada uno.  Esto le permite no sólo ver detectado errores y rendimiento a lo largo del tiempo, sino también para validar el estado actual de una aplicación o sistema y cada uno de sus componentes en cualquier momento.  Porque comprende los períodos de tiempo que una aplicación está disponible, el motor de estado en SCOM también admite contratos de nivel de servicio (SLA) que analizar e informar sobre la disponibilidad de una aplicación a lo largo del tiempo.

Por ejemplo, la vista siguiente muestra el estado en tiempo real de motores de base de datos SQL supervisado por SCOM.  El estado de cada una de las bases de datos para uno de los motores de base de datos se muestra en la parte inferior la mitad de la vista.

![Vista de estado](media/operations-management-suite-monitoring-product-comparison/scom-state-view.png)

A continuación, se muestra el Explorador de estado para uno de los motores de base de datos con los monitores que se usan para determinar el estado general.  Estos monitores se definen en el módulo de administración de SQL y ejecutar en todos los motores de base de datos SQL descubiertos SCOM.

![Explorador de estado](media/operations-management-suite-monitoring-product-comparison/scom-health-explorer.png)

Componentes en varios sistemas pueden combinarse para medir el estado de una aplicación distribuida.  Esto puede ser especialmente útil para aplicaciones empresariales que incluyen varios componentes distribuidos.  Puede crear un modelo que mide el estado de cada componente que resumen en la disponibilidad de la aplicación.

Active Directory es un ejemplo de un módulo de administración que proporciona un modelo para analizar sus componentes distribuidos.  El diagrama de ejemplo siguiente muestra el mantenimiento del entorno general y la relación entre bosques, dominios y controladores de dominio.  Cada uno de estos componentes incluye subcomponentes y varios monitores similares al ejemplo SQL anterior.

![Vista de diagrama SCOM](media/operations-management-suite-monitoring-product-comparison/scom-diagram-view.png)

### <a name="log-analytics"></a>Análisis de registro
OMS no incluir un motor común para las aplicaciones de modelo o medir su estado en tiempo real.  Soluciones individuales pueden evaluar el estado general de los servicios determinados basándose en los datos recopilados y, a continuación, puede instalar lógica personalizada en el agente para realizar análisis en tiempo real.  Dado que las soluciones que se ejecutan en la nube con acceso al repositorio OMS, a menudo pueden proporcionar un análisis más profundo que normalmente es realizado por los paquetes de administración. 

Por ejemplo, las [soluciones de evaluación de AD y evaluación de SQL](https://technet.microsoft.com/library/mt484102.aspx) analizar los datos recopilados y facilitar una clasificación de distintos aspectos del entorno.  Incluye recomendaciones para mejoras que se pueden realizar para mejorar la disponibilidad y el rendimiento del entorno.

![Solución de evaluación de AD](media/operations-management-suite-monitoring-product-comparison/log-analytics-ad-assessment.png)

## <a name="data-analysis"></a>Análisis de datos
SCOM y análisis de registro proporcionan diferentes características para analizar los datos recopilados.  SCOM tiene vistas y paneles en la consola de operaciones de análisis de datos recientes en una gran variedad de formatos e informes para presentar los datos del almacén de datos en formato tabular.  Análisis de registro proporciona una interfaz y el lenguaje de consulta de registro completo para analizar datos en el repositorio OMS.  Cuando SCOM se usa como origen de datos para el análisis de registro, el repositorio incluye los datos recopilados por SCOM para que las herramientas de análisis de registro pueden usarse para analizar los datos de ambos sistemas.

### <a name="operations-manager"></a>Operations Manager

#### <a name="views"></a>Vistas
Vistas en la consola de operaciones le permiten ver diferentes tipos de datos recopilados por SCOM en diferentes formatos, normalmente tabulares de eventos, alertas y datos de estado y los gráficos de datos de rendimiento de línea.  Vistas de realizan análisis mínimo o consolidación de los datos, pero le permiten filtrar según un criterio determinado. 

![Vistas](media/operations-management-suite-monitoring-product-comparison/scom-views.png)

Paquetes de administración le proporcionará normalmente varias vistas compatible con la aplicación o el sistema que supervisa.  Esto puede incluir vistas de estado para los distintos objetos que descubre el módulo de administración, vistas de alertas para los problemas detectados y vistas de rendimiento para contadores.

Las vistas son especialmente adecuadas para analizar el estado actual del entorno incluidos alertas abiertas y el estado de mantenimiento de los sistemas supervisados y objetos.  Puede desglosar evento detallada o auxiliares una alerta determinada para diagnosticar la causa de origen de datos de rendimiento. Asimismo, puede ver el rendimiento y la salud de los diferentes componentes de una aplicación para evaluar su estado actual.

#### <a name="dashboards"></a>Paneles
Paneles en la consola de operaciones principalmente trabajar con los mismos datos como vistas pero son más personalizables y pueden incluir visualizaciones más rica.  Un conjunto de paneles estándares están disponibles que puede personalizar con facilidad para sus propios fines.  También puede utilizar un widget de PowerShell que se puede mostrar los datos devueltos por una consulta de PowerShell.

![Panel](media/operations-management-suite-monitoring-product-comparison/scom-dashboard.png)

Los desarrolladores tienen la capacidad de agregar componentes personalizados a los paneles que se incluyen en los paquetes de administración.  Estos pueden especializados altamente en una aplicación concreta, como el escritorio en el módulo de administración de SQL que se muestra a continuación.  Este panel puede utilizarse también como una plantilla para versiones personalizadas.

![Panel SQL](media/operations-management-suite-monitoring-product-comparison/scom-sql-dashboard.png)

#### <a name="reports"></a>Informes
Informes en SCOM analizar los datos del almacén de datos en formato tabular.  Puede imprimir y programados para entrega automatizada en distintos formatos de archivo como PDF, CSV y Word.  Informes de trabajan con datos del almacén de datos para que sean especialmente adecuados para el análisis de tendencias a largo plazo.

Paquetes de administración le proporcionará normalmente los informes personalizados para una aplicación concreta.  También puede seleccionar la biblioteca de informes genéricos que puede personalizar para sus propias aplicaciones o para realizar análisis ad hoc.

A continuación se muestra un informe de rendimiento de ejemplo que muestra los datos recopilados por el paquete de administración de Active Directory.

![Informe](media/operations-management-suite-monitoring-product-comparison/scom-report.png)

### <a name="log-analytics"></a>Análisis de registro
Análisis de registro tiene un [lenguaje de consulta](https://technet.microsoft.com/library/mt484120.aspx) que puede usar para realizar análisis a través de los datos de varias aplicaciones sin necesidad de crear una vista personalizada o un informe.  Como OMS se implementa en la nube, rendimiento de consultas y análisis de datos no están sujetos a las limitaciones de hardware y puede analizar rápidamente las consultas millones de registros. 

Las consultas de análisis de registro también son la base de otras funciones.  Puede guardar una consulta, exportar a Excel o que se ejecute a intervalos regulares automáticamente y generar una alerta si sus resultados que coincidan con determinados criterios.  

![Flujo de consulta de registro](media/operations-management-suite-monitoring-product-comparison/log-analytics-query-flow.png)

A continuación se muestra un ejemplo de una consulta de análisis de registro.  En este ejemplo se devuelve y agrupados por evento todos los eventos con "iniciado" en el nombre del Id.  El usuario simplemente proporciona la consulta y el análisis de registro genera dinámicamente la interfaz de usuario para realizar el análisis.  Selección de un elemento en la lista devolverá los datos del evento detallada.

![Consulta de registro](media/operations-management-suite-monitoring-product-comparison/log-analytics-query.png)

Además de proporcionar análisis ad hoc, las consultas de análisis de registro se pueden guardar para usos futuros y también se agrega al [panel OMS](http://technet.microsoft.com/library/mt484090.aspx) tal como se muestra en el ejemplo siguiente.

![Panel OMS](media/operations-management-suite-monitoring-product-comparison/log-analytics-dashboard.png)

## <a name="next-steps"></a>Pasos siguientes

- Implemente [System Center Operations Manager (SCOM)](https://technet.microsoft.com/library/hh205987.aspx).
- Registrarse para [El análisis de registro](https://azure.microsoft.com/documentation/services/log-analytics).  

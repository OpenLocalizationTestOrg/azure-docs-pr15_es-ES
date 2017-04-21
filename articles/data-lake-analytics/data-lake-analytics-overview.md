<properties 
   pageTitle="Información general de análisis de datos lago de Microsoft Azure | Azure" 
   description="Análisis de datos lago es un servicio de cálculo de datos de Azure grande que le permite utilizar datos para dirigir su negocio con la información obtenida de los datos en la nube, independientemente de dónde se e independientemente de su tamaño. Análisis de datos lago habilita esto en la forma más sencilla, mayor escalabilidad y más económico forma posible. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="05/16/2016"
   ms.author="edmaca"/>

# <a name="overview-of-microsoft-azure-data-lake-analytics"></a>Información general de análisis de datos lago de Microsoft Azure

## <a name="what-is-azure-data-lake-analytics"></a>¿Qué es el análisis de lago de datos de Azure?

Análisis de lago datos Azure es un nuevo servicio, integrado para facilitar el análisis de datos grande. Este servicio le permite centrarse en escribir, ejecutar y administrar los trabajos, en lugar de operativo infraestructura distribuida. En lugar de implementar, configurar y ajuste del hardware, escribir consultas para transformar los datos y extraer valiosa información. El servicio de análisis puede controlar los trabajos de cualquier escala al instante estableciendo simplemente el acceso telefónico para cuánto power necesita. Solo paga para su trabajo cuando se ejecuta realizar rentables. El servicio de análisis admite Azure Active Directory simplemente administrar acceso y roles, integradas con su sistema de identidad local. También incluye U-SQL, un idioma que unifica las ventajas de SQL con la potencia expresiva del código de usuario. Scalable distribuido runtime de SQL U le permite eficazmente analizar datos en el almacén y entre servidores SQL Azure, base de datos de SQL Azure y almacenamiento de datos de SQL Azure.


## <a name="key-capabilities"></a>Capacidades clave

- **Ajuste de escala dinámicos** 

    Análisis de datos lago está diseñado desde el principio para la escala de la nube y el rendimiento.  Dinámicamente aprovisiona recursos y le permite hacer el análisis en terabytes o incluso exabytes de datos. Cuando finalice el trabajo, finaliza recursos automáticamente y pagar solo para la potencia de procesamiento que se utiliza. Mientras que aumente o disminuya el tamaño de los datos almacenados o la cantidad de cálculo utilizado, no tiene a escribir código. Esto permite centrarse en la lógica empresarial únicamente y no en cómo procesar y almacenar grandes conjuntos de datos. 

- **Desarrollar con mayor rapidez, depurar y optimizar mejor mediante herramientas familiares**

    Análisis de datos lago tiene integración con Visual Studio, de modo que puede usar herramientas familiares para ejecutar, depurar y ajustar el código. Visualizaciones de los trabajos de SQL U le permiten ver cómo se ejecuta el código en escala, para que pueda identificar fácilmente rendimiento cuellos y optimizar los costos. 

- **U-SQL: simples y familiares, eficaz y extensible**

    Análisis de datos lago incluye U-SQL, un lenguaje de consulta que se extiende la naturaleza familiar, simple descriptiva de SQL con la potencia expresiva de C#. El idioma de SQL U se basa en el mismo runtime distribuido que potencia de los sistemas de datos grande dentro de Microsoft. Millones de desarrolladores SQL y .NET ahora pueden procesar y analizar todos sus datos con las habilidades que ya tienen.

- **Se integra con sus inversiones en TI**

    Análisis de datos lago puede usar las inversiones de TI existentes de identidad, administración, seguridad y almacenamiento de datos. Esto simplifica la gobernanza de datos y hace que sea fácil ampliar las aplicaciones de datos actual. Análisis de datos lago se integra con Active Directory para permisos y administración de usuarios y se suministra con de supervisión y auditoría.

- **Asequible y rentable**

    Análisis de datos lago es una solución rentable para ejecutar las cargas de trabajo de datos grande. Pagar de forma por trabajo cuando se procesan. Hardware, licencias o contratos de soporte técnico específicos del servicio no son necesarios. El sistema automáticamente escala hacia arriba o hacia abajo, según el trabajo se inicia y finaliza, lo que significa que nunca paga por más de lo que necesita. 

- **Funciona con los datos de Azure**

    Análisis de datos lago puede trabajar con un número de orígenes de datos de Azure: el almacenamiento de blobs de Windows Azure, base de datos de SQL Azure y, por supuesto, el análisis de datos lago es especialmente optimizada para trabajar con Azure datos lago Store - proporcionar el máximo nivel de rendimiento, el rendimiento y la puesta en paralelo para las cargas de trabajo de datos grande.

## <a name="see-also"></a>Vea también

- Introducción
    - [Introducción a análisis de lago de datos con el Portal de Azure](data-lake-analytics-get-started-portal.md)
    - [Introducción a análisis de datos lago con PowerShell de Azure](data-lake-analytics-get-started-powershell.md)
    - [Introducción a análisis de lago de datos con Azure .NET SDK](data-lake-analytics-get-started-net-sdk.md)
    - [Desarrollar secuencias de comandos de SQL U con datos lago Tools para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
    - [Empezar a trabajar con el lenguaje de datos de Azure lago análisis U-SQL](data-lake-analytics-u-sql-get-started.md)
    
- U SQL y desarrollo
    - [Empezar a trabajar con el lenguaje de datos de Azure lago análisis U-SQL](data-lake-analytics-u-sql-get-started.md)
    - [Usar funciones de la ventana de SQL U para trabajos de análisis de lago de datos de Azure](data-lake-analytics-use-window-functions.md)
    - [Desarrollar operadores de SQL U definidas por el usuario para las tareas de análisis de datos lago](data-lake-analytics-u-sql-develop-user-defined-operators.md)
    
- Administración
    - [Administrar el análisis de lago de datos de Azure con el Portal de Azure](data-lake-analytics-manage-use-portal.md)
    - [Administrar el análisis de lago de datos de Azure con PowerShell de Azure](data-lake-analytics-manage-use-powershell.md)
    - [Supervisar y solucionar problemas de trabajos de análisis de lago de datos de Azure con el Portal de Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
    - [Acceso a registros de diagnóstico para el análisis de lago de datos de Azure](data-lake-analytics-diagnostic-logs.md)

- Tutorial de llevar a cabo
    - [Usar tutoriales interactivas de análisis de lago de datos de Azure](data-lake-analytics-use-interactive-tutorials.md)
    - [Analizar los registros de sitio Web mediante el análisis de lago de datos de Azure](data-lake-analytics-analyze-weblogs.md)

- Díganos qué opina
    - [Comentar pendientes documentación](data-lake-analytics-documentation-backlog.md)
    - [Enviar una solicitud de característica](http://aka.ms/adlafeedback)
    - [Obtenga ayuda en los foros](http://aka.ms/adlaforums)



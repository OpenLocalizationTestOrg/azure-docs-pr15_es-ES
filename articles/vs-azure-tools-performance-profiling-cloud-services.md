<properties 
   pageTitle="Probar el rendimiento de un servicio de nube | Microsoft Azure"
   description="Probar el rendimiento de un servicio de nube mediante el analizador de Visual Studio"
   services="visual-studio-online"
   documentationCenter="n/a"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="visual-studio-online"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />


# <a name="testing-the-performance-of-a-cloud-service"></a>Probar el rendimiento de un servicio de nube 

##<a name="overview"></a>Información general

Puede probar el rendimiento de un servicio de nube de las siguientes maneras:

- Use diagnósticos de Azure para recopilar información acerca de las convocatorias y las conexiones y para revisar las estadísticas del sitio que muestran cómo el servicio se realiza desde una perspectiva de cliente. Para empezar a usar, consulte [configuración de diagnósticos para servicios de nube de Azure y máquinas virtuales]( http://go.microsoft.com/fwlink/p/?LinkId=623009).

- Utilice al analizador de Visual Studio para obtener un análisis detallado de los aspectos de cómo se ejecuta el servicio de cálculo. Como se describe en este tema, puede usar el generador de perfiles para medir el rendimiento de un servicio se ejecuta en Azure. Para obtener información acerca de cómo usar el generador de perfiles para medir el rendimiento de un servicio se ejecuta localmente en un emulador de cálculo, vea [probar el rendimiento de un servicio nube Azure de forma local en el emulador calcular con el analizador de Visual Studio](http://go.microsoft.com/fwlink/p/?LinkId=262845).



## <a name="choosing-a-performance-testing-method"></a>Elegir un método de prueba de rendimiento

###<a name="use-azure-diagnostics-to-collect"></a>Use diagnósticos de Azure para recopilar:###

- Estadísticas de páginas web o los servicios, como las convocatorias y las conexiones.

- Estadísticas sobre funciones, como la frecuencia con la que se reinicia un rol.

- General sobre el uso de memoria, como el porcentaje de tiempo que tarda la recolección o la memoria del conjunto de información de una función de ejecución.

###<a name="use-the-visual-studio-profiler-to"></a>Usar el generador de perfiles de Visual Studio para:###

- Determinar qué funciones llevar más tiempo.

- Medir cuánto tiempo tarda cada parte de un programa de cálculo intensivo.

- Comparar informes de rendimiento detallada dos versiones de un servicio.

- Analizar la asignación de memoria en más detalle que el nivel de las asignaciones de memoria individuales.

- Analizar los problemas de simultaneidad del código multiproceso.

Al usar el generador de perfiles, puede recopilar datos cuando se ejecuta un servicio de nube localmente o en Azure.

###<a name="collect-profiling-data-locally-to"></a>Recopilar datos de perfiles localmente para:###

- Probar el rendimiento de una parte de un servicio de nube, como la ejecución de la función de trabajo específicos, que no requiere una carga simulada realista.

- Probar el rendimiento de un servicio de nube de aislamiento en condiciones controladas.

- Probar el rendimiento de un servicio de nube antes de implementarlo en Azure.

- Probar el rendimiento de un servicio de nube privada, sin afectar a las implementaciones existentes.

- Probar el rendimiento del servicio evitando cargos para ejecutar en Azure.

###<a name="collect-profiling-data-in-azure-to"></a>Recopilar datos de perfiles en Azure para:###

- Probar el rendimiento de un servicio de nube en una carga simulada o real.

- Use el método de instrumentación de recopilación de datos de perfiles, como se describe más adelante en este tema.

- Probar el rendimiento del servicio en el mismo entorno que cuando se ejecuta el servicio de producción.

Normalmente simular una carga en servicios de nube de prueba en normal o condiciones de carga.

## <a name="profiling-a-cloud-service-in-azure"></a>Generación de perfiles de un servicio de nube de Azure

Cuando se publica su servicio de nube de Visual Studio, puede el servicio de perfil y especifique la configuración perfiles que proporcionan la información que desee. Se inicia una sesión para cada instancia de una función. Para obtener más información acerca de cómo publicar su servicio de Visual Studio, vea [publicación a un servicio de nube de Azure de Visual Studio](https://msdn.microsoft.com/library/azure/ee460772.aspx).

Para conocer más acerca de los perfiles de rendimiento en Visual Studio, vea [Guía básica para la generación de perfiles de rendimiento](https://msdn.microsoft.com/library/azure/ms182372.aspx) y [Analizar el rendimiento de la aplicación utilizando las herramientas de generación de perfiles](https://msdn.microsoft.com/library/azure/z9z62c29.aspx).

>[AZURE.NOTE] Puede habilitar IntelliTrace o al publicar su servicio de nube de generación de perfiles. No se puede habilitar ambas.

###<a name="profiler-collection-methods"></a>Métodos de colección de generador de perfiles

Puede usar diferentes métodos para la generación de perfiles, en función de los problemas de rendimiento:

- **Muestreo de la CPU** - este método recopila estadísticas de la aplicación que son útiles para el análisis inicial de los problemas de uso de CPU. Muestreo de la CPU es el método sugerido para iniciar la mayoría de las investigaciones de rendimiento. Hay bajo impacto en la aplicación que genera perfiles al recopilar datos de muestreo de la CPU.

- **Instrumentación** -este método recopila datos detallados de tiempo que es útiles para un análisis enfocado y para analizar los problemas de rendimiento de entrada y salida. El método de instrumentación registros cada entrada y salida llamada a la función de las funciones en un módulo durante una generación de perfiles. Este método es útil para recopilar información de tiempo detallada sobre una sección del código y para comprender el impacto de las operaciones de entrada y salidas de rendimiento de la aplicación. Este método está deshabilitado para un equipo que ejecuta un sistema operativo de 32 bits. Esta opción está disponible solo cuando se ejecuta el servicio de nube en Azure, no localmente en el emulador de cálculo.

- **Asignación de memoria .NET** : este método recopila datos de asignación de memoria de .NET Framework utilizando el método de generación de perfiles de muestreo. Los datos recopilados incluyen el número y el tamaño de los objetos asignados.

- **Simultaneidad** - este método recopila datos de conflicto de recursos y datos de ejecución de proceso y subproceso que resultan útiles para analizar aplicaciones multiproceso y multiproceso. El método de simultaneidad recopila datos para cada evento que bloquea la ejecución del código, por ejemplo, cuando un subproceso espera para bloquea el acceso a un recurso de aplicación para liberar. Este método es útil para analizar aplicaciones multiproceso.

- También puede habilitar **La generación de perfiles de interacción de capas**, que proporciona información adicional sobre los tiempos de ejecución de ADO.NET sincrónico llamadas en funciones de aplicaciones de múltiples niveles que comunican con uno o más bases de datos. Puede recopilar datos de interacción de capas con cualquiera de los métodos de generación de perfiles. Para obtener más información sobre la generación de perfiles de interacción de capas, consulte [Vista de interacciones de nivel](https://msdn.microsoft.com/library/azure/dd557764.aspx).

## <a name="configuring-profiling-settings"></a>Configuración de perfiles

La siguiente ilustración muestra cómo configurar la configuración de perfiles en el cuadro de diálogo Publicar aplicaciones de Azure.

![Configurar opciones de generación de perfiles](./media/vs-azure-tools-performance-profiling-cloud-services/IC526984.png)

>[AZURE.NOTE] Para habilitar la casilla de verificación **Habilitar perfiles** , debe tener el analizador instalado en el equipo local que se va a usar para publicar su servicio de nube. De forma predeterminada, el generador de perfiles se instala cuando se instala Visual Studio.

### <a name="to-configure-profiling-settings"></a>Para establecer la configuración perfiles

1. En el Explorador de soluciones, abra el menú contextual para el proyecto de Azure y, a continuación, elija **Publicar**. Para conocer los pasos detallados publicar un servicio de nube, consulte [publicar una nube de servicio con las herramientas de Azure](http://go.microsoft.com/fwlink/p?LinkId=623012).

1. En el cuadro de diálogo **Publicar aplicaciones de Azure** , elija la ficha **Configuración avanzada** .

1. Para habilitar los perfiles, active la casilla de verificación **Habilitar perfiles** .

1. Para configurar la configuración de perfiles, seleccione el hipervínculo de la **configuración** . Aparece el cuadro de diálogo Configuración de perfiles.

1. Desde los botones de opción **¿qué método de generación de perfiles desea usar** , elija el tipo de generación de perfiles que necesita.

1. Para recopilar los datos de perfiles de interacción de nivel, active la casilla de verificación **Habilitar generación de perfiles de interacción de capas** .

1. Para guardar la configuración, elija el botón **Aceptar** .

    Cuando se publica esta aplicación, estos valores se usan para crear la sesión de generación de perfiles de cada rol.

## <a name="viewing-profiling-reports"></a>Ver informes de generación de perfiles

Se crea una sesión para cada instancia de una función en el servicio de nube. Para ver los informes de generación de perfiles de cada sesión de Visual Studio, puede ver la ventana Explorador de servidor y, a continuación, elija el nodo Azure calcular para seleccionar una instancia de una función. A continuación, puede ver el informe perfiles tal como se muestra en la siguiente ilustración.

![Ver el informe de generación de perfiles de Azure](./media/vs-azure-tools-performance-profiling-cloud-services/IC748914.png)

### <a name="to-view-profiling-reports"></a>Para ver los informes de generación de perfiles

1. Para ver la ventana del explorador de servidores de Visual Studio, en la barra de menús elija Ver, Explorador de servidores.

1. Elegir el nodo Azure calcular y, a continuación, elija el nodo de implementación de Azure para el servicio de nube seleccionada al perfil al publicar desde Visual Studio.

1. Para ver informes de generación de perfiles de una instancia, elija el rol en el servicio, abra el menú contextual para una instancia específica y, a continuación, elija **Ver el informe de generación de perfiles**.

    El informe, un archivo .vsp, ahora se descarga de Azure y el estado de la descarga aparece en el registro de actividad de Azure. Cuando finalice la descarga, el informe de generación de perfiles aparece en una pestaña en el editor de Visual Studio denominada <Role name> _<Instance Number>_ <identifier>.vsp. Aparecen los datos de resumen para el informe.

1. Para mostrar diferentes vistas del informe, en la lista Vista actual, elija el tipo de vista que desee. Para obtener más información, vea [Vistas de informes de herramientas de generación de perfiles](https://msdn.microsoft.com/library/azure/bb385755.aspx).

## <a name="next-steps"></a>Pasos siguientes

[Servicios de nube de depuración](https://msdn.microsoft.com/library/azure/ee405479.aspx)

[Publicación en un servicio de nube Azure desde Visual Studio](https://msdn.microsoft.com/library/azure/ee460772.aspx)


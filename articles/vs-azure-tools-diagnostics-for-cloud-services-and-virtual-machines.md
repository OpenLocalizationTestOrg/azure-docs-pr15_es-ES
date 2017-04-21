<properties
   pageTitle="Configuración de diagnósticos para máquinas virtuales y servicios de nube de Azure | Microsoft Azure"
   description="Describe cómo configurar la información de diagnóstico para depurar servicios de Azure cloude y máquinas virtuales (VM) en Visual Studio."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="configuring-diagnostics-for-azure-cloud-services-and-virtual-machines"></a>Configuración de diagnósticos para servicios de nube de Azure y máquinas virtuales

Si necesita solucionar problemas de un servicio de nube Azure o una máquina virtual de Azure, puede configurar diagnóstico de Azure más fácilmente mediante Visual Studio. Diagnóstico de Azure captura los datos del sistema y datos de registro en los equipos virtuales e instancias de máquina virtual que ejecutan el servicio de nube y que los datos transfiere a una cuenta de almacenamiento de su elección. Para obtener más información acerca de diagnósticos de registro en Azure, vea [Habilitar el registro de diagnósticos para aplicaciones web de servicio de aplicaciones de Azure](./app-service-web/web-sites-enable-diagnostic-log.md) .

En este tema se muestra cómo habilitar y configurar el diagnóstico de Azure en Visual Studio, ambos antes y después de la implementación, así como en máquinas virtuales de Windows Azure. También muestra cómo seleccionar los tipos de información de diagnóstico para recopilar y ver la información que se recopila.

Puede configurar Azure diagnósticos de las siguientes maneras:

- Puede cambiar las opciones de configuración de diagnósticos mediante el cuadro de diálogo **Configuración de diagnósticos** en Visual Studio. La configuración se guarda en un archivo llamado diagnostics.wadcfgx (diagnostics.wadcfg en Azure SDK 2,4 o una versión anterior). Como alternativa, puede modificar directamente el archivo de configuración. Si actualiza manualmente el archivo, los cambios de configuración surtirá efecto la próxima vez que se implementa en la nube del servicio a Azure o ejecutar el servicio en el emulador.

- Usar **Explorer nube** o en **El Explorador de servidor** en Visual Studio para cambiar la configuración de diagnósticos de un servicio de nube de ejecución o una máquina virtual.

## <a name="azure-26-diagnostics-changes"></a>Cambios de diagnósticos de Azure 2.6

Para los proyectos de Azure SDK 2.6 en Visual Studio, se han realizado los cambios siguientes. (Estos cambios también se aplican a las versiones posteriores de Azure SDK.)

- El emulador local admite ahora diagnósticos. Esto significa que puede recopilar datos de diagnóstico y asegúrese de que la aplicación está creando los seguimientos derecha mientras está desarrollar y probar en Visual Studio. La cadena de conexión `UseDevelopmentStorage=true` permite la recopilación de datos de diagnóstico mientras se ejecuta el proyecto de servicio de nube en Visual Studio por uso el almacenamiento de Azure. Todos los datos de diagnóstico está almacenada en la cuenta de almacenamiento (almacenamiento de desarrollo).

- La cadena de conexión de cuenta de almacenamiento de información de diagnóstico (Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString) se almacena de nuevo en el archivo de configuración (.cscfg) de servicio. En Azure SDK 2,5 la cuenta de almacenamiento de diagnósticos se especificó en el archivo diagnostics.wadcfgx.

Existen algunas diferencias importantes entre cómo ha funcionado la cadena de conexión en Azure SDK 2,4 y versiones anteriores y cómo funciona en Azure SDK 2.6 y versiones posteriores.

- En Azure SDK 2,4 y anteriores, la cadena de conexión utilizó como un tiempo de ejecución el complemento de diagnóstico para obtener la información de cuenta de almacenamiento para la transferencia de registros de diagnósticos.

- En Azure SDK 2.6 y versiones posteriores, se utiliza la cadena de conexión de diagnósticos Visual Studio para configurar la extensión de diagnósticos con la información de cuenta de almacenamiento apropiado durante la publicación. La cadena de conexión le permite definir las cuentas de almacenamiento diferentes configuraciones de servicio diferente que usará Visual Studio al publicar. Sin embargo, porque el complemento de diagnósticos ya no está disponible (después de Azure SDK 2.5), el archivo .cscfg por sí mismo no puede habilitar la extensión de diagnósticos. Debe habilitar la extensión por separado por medio de herramientas como Visual Studio o PowerShell.

- Para simplificar el proceso de configuración de la extensión de diagnósticos con PowerShell, el resultado de paquete de Visual Studio también contiene el público XML de configuración para la extensión de diagnósticos de cada rol. Visual Studio utiliza la cadena de conexión de diagnóstico para rellenar la información de cuenta de almacenamiento presente en la configuración del pública. Los archivos de configuración público se crean en la carpeta Extensiones y sigan el patrón PaaSDiagnostics. &lt;Nombre de rol >. PubConfig.xml. Las implementaciones de PowerShell según pueden usar este patrón para asignar cada configuración a un rol.

- La cadena de conexión en el archivo .cscfg también se utiliza el [portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040) accediendo a los datos de diagnóstico para que aparezca en la ficha **supervisión** . La cadena de conexión es necesaria para configurar el servicio para mostrar los datos detallados de supervisión en el portal.

## <a name="migrating-projects-to-azure-sdk-26-and-later"></a>Migración de proyectos a Azure SDK 2.6 y versiones posteriores

Cuando se migra de Azure SDK 2,5 a Azure SDK 2.6 o versiones posteriores, si tiene una cuenta de almacenamiento de diagnósticos especificada en el archivo .wadcfgx, a continuación, permanecerá allí. Para aprovechar las ventajas de la flexibilidad de usar cuentas de almacenamiento diferentes configuraciones de almacenamiento diferentes, debe agregar manualmente la cadena de conexión a su proyecto. Si está migrando un proyecto de Azure SDK 2,4 o una versión anterior a Azure SDK 2.6, se conservan las cadenas de conexión de diagnósticos. Sin embargo, tenga en cuenta los cambios en cómo se tratan las cadenas de conexión en Azure SDK 2.6 tal como se especifica en la sección anterior.

### <a name="how-visual-studio-determines-the-diagnostics-storage-account"></a>¿Cómo Visual Studio determina la cuenta de almacenamiento de información de diagnóstico

- Si se especifica una cadena de conexión de diagnóstico en el archivo .cscfg, en Visual Studio se utiliza para configurar la extensión de diagnósticos al publicar y al generar los archivos xml de configuración de pública durante el embalaje.

- Si no se especifica ninguna cadena de conexión de diagnóstico en el archivo .cscfg, a continuación, Visual Studio utiliza con la cuenta de almacenamiento especificada en el archivo .wadcfgx para configurar la extensión de diagnósticos al publicar y generar los archivos xml de configuración público al empaquetar.

- La cadena de conexión de diagnóstico en el archivo .cscfg tiene prioridad sobre la cuenta de almacenamiento en el archivo .wadcfgx. Si se especifica una cadena de conexión de diagnóstico en el archivo .cscfg, usa Visual Studio y pasa por alto la cuenta de almacenamiento en .wadcfgx.

### <a name="what-does-the-update-development-storage-connection-strings-checkbox-do"></a>¿Qué significa la "actualización desarrollo almacenamiento cadenas de conexión..." ¿casilla de verificación hacer?

La casilla de verificación **actualizar cadenas de conexión de almacenamiento de desarrollo de diagnósticos y almacenamiento en caché con credenciales de cuenta de Microsoft Azure almacenamiento cuando se publica en Microsoft Azure** proporciona una manera conveniente actualizar las cadenas de conexión de cuenta de almacenamiento de desarrollo con la cuenta de almacenamiento de Azure especificada durante la publicación.

Por ejemplo, supongamos que selecciona esta casilla de verificación y especifica la cadena de conexión de diagnósticos `UseDevelopmentStorage=true`. Cuando se publique el proyecto en Azure, Visual Studio se actualizará automáticamente la cadena de conexión de diagnósticos con la cuenta de almacenamiento que especificó en el Asistente para la publicación. Sin embargo, si se ha especificado una cuenta de almacenamiento real como la cadena de conexión de diagnósticos, se utiliza en su lugar la cuenta.

## <a name="diagnostics-functionality-differences-between-azure-sdk-24-and-earlier-and-azure-sdk-25-and-later"></a>Diferencias de funcionalidad de diagnósticos entre Azure SDK 2,4 y anterior y Azure SDK 2.5 y posterior

Si está actualizando el proyecto de Azure SDK 2,4 a Azure SDK 2.5 o posterior, debe tener en cuenta las siguientes diferencias funcionalidad de diagnósticos.

- **API de configuración están obsoletos** : configuración mediante programación de diagnósticos está disponible en Azure SDK 2,4 o versiones anteriores, pero es obsoleto en Azure SDK 2,5 y versiones posteriores. Si la configuración de diagnósticos está definida actualmente en el código, deberá configurar desde cero en el proyecto migrado para diagnósticos para seguir trabajando. El archivo de configuración de diagnósticos de Azure SDK 2,4 es diagnostics.wadcfg y diagnostics.wadcfgx Azure SDK 2,5 y versiones posteriores.

- **Diagnósticos de aplicaciones de servicio de nube sólo se puede configurar en el nivel de la función, no en el nivel de instancia.**

- **Cada vez implemente la aplicación, se actualiza la configuración de diagnósticos** : Esto puede provocar problemas de paridad si cambia la configuración de diagnósticos desde el Explorador de servidor y, a continuación, volver a instalar la aplicación.

- **En Azure SDK 2,5 y posteriores, colapsar volcados están configurados en el archivo de configuración de diagnósticos, no en el código** : si tiene volcados configurados en el código, deberá transferir manualmente la configuración de código al archivo de configuración, porque la volcados no se transfieren durante la migración a Azure SDK 2.6.

## <a name="enable-diagnostics-in-cloud-service-projects-before-deploying-them"></a>Habilitar diagnósticos en proyectos de servicio de nube antes de implementar ellos

En Visual Studio, puede recopilar datos de diagnóstico para funciones que se ejecutan en Azure, cuando se ejecuta el servicio en el emulador antes de distribuirlo. Todos los cambios en la configuración de diagnósticos de Visual Studio se guardan en el archivo de configuración de diagnostics.wadcfgx. Estas opciones de configuración especifican la cuenta de almacenamiento donde está guardados los datos de diagnóstico cuando se implementa el servicio de nube.

### <a name="to-enable-diagnostics-in-visual-studio-before-deployment"></a>Para habilitar diagnósticos en Visual Studio antes de la implementación

1. En el menú contextual para la función que le interese, elija **Propiedades**y, a continuación, elija la pestaña **configuración** en la ventana de **Propiedades** de la función.

1. En la sección **diagnóstico** , asegúrese de que está seleccionada la casilla de verificación **Habilitar diagnósticos** .

    ![Obtener acceso a la opción Habilitar diagnósticos](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796660.png)

1. Seleccione el botón de puntos suspensivos (...) para especificar la cuenta de almacenamiento donde desea que los datos de diagnósticos. Elegir la cuenta de almacenamiento será la ubicación donde se almacenan los datos de diagnóstico.

    ![Especificar la cuenta de almacenamiento para usar](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796661.png)

1. En el cuadro de diálogo **Crear cadena de conexión de almacenamiento** , especifique si desea conectar el uso de la Azure almacenamiento, una suscripción a Azure o indique manualmente las credenciales.

    ![Cuadro de diálogo cuenta de almacenamiento](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796662.png)

  - Si elige la opción de Azure emulador de almacenamiento, la cadena de conexión está establecida en UseDevelopmentStorage de Microsoft = true.

  - Si elige la su opción de suscripción, puede elegir la suscripción de Azure que desea usar y el nombre de cuenta. Puede elegir el botón Administrar cuentas para administrar las suscripciones de Azure.

  - Si elige la opción de credenciales introducidas manualmente, le solicitará que escriba el nombre y la clave de la cuenta de Azure que desea usar.

1. Elija el botón **Configurar** para ver el cuadro de diálogo **configuración de diagnósticos** . Cada pestaña (excepto **General** y **Directorios de registro**) representa un origen de datos de diagnóstico puede recopilar. La pestaña de forma predeterminada, **General**, le ofrece las siguientes opciones de recopilación de datos de diagnósticos: **sólo errores**, **toda la información**y **plan personalizado**. La opción predeterminada, **sólo errores**, tiene la menor cantidad de almacenamiento porque no transferencia de advertencias o mensajes de seguimiento. La opción información todo transfiere la mayoría de la información y, por tanto, es la opción más costosa en términos de almacenamiento.

    ![Habilitar la configuración y diagnóstico de Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758144.png)

1. En este ejemplo, seleccione la opción de **plan personalizado** para que pueda personalizar los datos recopilados.

1. El cuadro de **Cuota de disco en MB** especifica la cantidad de espacio que desea asignar en su cuenta de almacenamiento de datos de diagnóstico. Puede cambiar el valor predeterminado si lo desea.

1. En cada ficha de datos de diagnósticos que desea recopilar, seleccione su **Habilitar la transferencia de <log type> ** casilla de verificación. Por ejemplo, si desea recopilar registros de aplicaciones, seleccione la casilla de verificación **Habilitar la transferencia de registros de aplicaciones** en la pestaña de **Registros de la aplicación** . Además, especificar cualquier otra información que necesita para cada tipo de datos de diagnóstico. Consulte la sección **Configurar orígenes de datos de diagnósticos** más adelante en este tema para obtener información de configuración en cada ficha.

1. Una vez que se ha habilitado la colección de todos los datos de diagnóstico que desee, elija el botón **Aceptar** .

1. Ejecute el proyecto de servicio de nube de Azure en Visual Studio como de costumbre. Si usa la aplicación, se guarda la información del registro habilitado a la cuenta de almacenamiento de Azure que especificó.

## <a name="enable-diagnostics-in-azure-virtual-machines"></a>Habilitar diagnósticos en máquinas virtuales de Windows Azure

En Visual Studio, puede recopilar datos de diagnóstico para máquinas virtuales de Windows Azure.

### <a name="to-enable-diagnostics-in-azure-virtual-machines"></a>Para habilitar diagnósticos en máquinas virtuales de Windows Azure

1. En el **Explorador de servidores**, elija el nodo de Azure y, a continuación, conectarse a su suscripción de Azure, si aún no está conectado.

1. Expanda el nodo de **máquinas virtuales de Windows** . Puede crear una nueva máquina virtual o seleccione una que ya exista.

1. En el menú contextual para la máquina virtual que le interese, elija **Configurar**. Muestra el cuadro de diálogo de configuración de máquina virtual.

    ![Configurar una máquina virtual Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796663.png)

1. Si aún no está instalado, agregue la extensión de diagnósticos de agente de supervisión de Microsoft. Esta extensión le permite recopilar datos de diagnóstico para la máquina virtual Azure. En la lista de extensiones instaladas, elija la selección de un menú desplegable de extensión disponibles y luego elija diagnósticos de agente de supervisión de Microsoft.

    ![Instalar una extensión de Azure máquina virtual](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766024.png)

    >[AZURE.NOTE] Otras extensiones de diagnósticos están disponibles para las máquinas virtuales. Para obtener más información, vea características y extensiones de máquina virtual de Azure.

1. Elija el botón **Agregar** para agregar la extensión y ver su cuadro de diálogo **configuración de diagnósticos** .

1. Elija el botón **Configurar** para especificar una cuenta de almacenamiento y, a continuación, elija el botón **Aceptar** .

    Cada pestaña (excepto **General** y **Directorios de registro**) representa un origen de datos de diagnóstico puede recopilar.

    ![Habilitar la configuración y diagnóstico de Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758144.png)

    La pestaña de forma predeterminada, **General**, le ofrece las siguientes opciones de recopilación de datos de diagnósticos: **sólo errores**, **toda la información**y **plan personalizado**. La opción predeterminada, **sólo errores**, tiene la menor cantidad de almacenamiento porque no transferencia de advertencias o mensajes de seguimiento. La opción de **toda la información** transfiere la mayoría de la información y, por tanto, es la opción más costosa en términos de almacenamiento.

1. En este ejemplo, seleccione la opción de **plan personalizado** para que pueda personalizar los datos recopilados.

1. El cuadro de **Cuota de disco en MB** especifica la cantidad de espacio que desea asignar en su cuenta de almacenamiento de datos de diagnóstico. Puede cambiar el valor predeterminado si lo desea.

1. En cada ficha de datos de diagnósticos que desea recopilar, seleccione su **Habilitar la transferencia de <log type> ** casilla de verificación.

    Por ejemplo, si desea recopilar registros de aplicaciones, seleccione la casilla de verificación **Habilitar la transferencia de registros de aplicaciones** en la pestaña de **Registros de la aplicación** . Además, especificar cualquier otra información que necesita para cada tipo de datos de diagnóstico. Consulte la sección **Configurar orígenes de datos de diagnósticos** más adelante en este tema para obtener información de configuración en cada ficha.

1. Una vez que se ha habilitado la colección de todos los datos de diagnóstico que desee, elija el botón **Aceptar** .

1. Guarde el proyecto actualizado.

    Verá un mensaje en la ventana de **Registro de actividad de Microsoft Azure** que se ha actualizado la máquina virtual.

## <a name="configure-diagnostics-data-sources"></a>Configurar orígenes de datos de diagnóstico

Después de habilitar la recopilación de datos de diagnóstico, puede elegir exactamente qué orígenes de datos que desea recopilar y qué información se recopila. A continuación se muestra una lista de pestañas en el cuadro de diálogo **configuración de diagnósticos** y qué cada opción de configuración.

### <a name="application-logs"></a>Registros de aplicación

**Registros de aplicación** contienen información de diagnóstico creado mediante una aplicación web. Si quiere capturar registros de aplicaciones, seleccione la casilla de verificación **Habilitar la transferencia de registros de aplicaciones** . Puede aumentar o disminuir el número de minutos cuando se transfieren los registros de aplicación a su cuenta de almacenamiento, cambie el valor de **Período transferir (min.)** . También puede cambiar la cantidad de información que se capturan en el registro, establezca el valor de nivel de registro. Por ejemplo, puede elegir **detallado** para obtener más información o elija **tareas críticas** para capturar sólo los errores críticos. Si tiene un proveedor de diagnósticos específicos que emite registros de aplicación, puede capturar Agregar GUID del proveedor al cuadro **GUID de proveedor** .

  ![Registros de aplicación](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758145.png)

  Para obtener más información acerca de los registros de aplicación, vea [Habilitar el registro de diagnósticos para aplicaciones web de servicio de aplicaciones de Azure](./app-service-web/web-sites-enable-diagnostic-log.md) .

### <a name="windows-event-logs"></a>Registros de eventos de Windows

Si quiere capturar registros de eventos de Windows, active la casilla de verificación **Habilitar la transferencia de registros de eventos de Windows** . Puede aumentar o disminuir el número de minutos cuando se transfieren los registros de eventos a su cuenta de almacenamiento, cambie el valor de **Período transferir (min.)** . Seleccione las casillas de verificación de los tipos de eventos que desee hacer un seguimiento.

  ![Registros de eventos](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796664.png)

Si está usando Azure SDK 2.6 o posterior y desea especificar un origen de datos personalizados, escríbalo en el **<Data source name>** texto cuadro y, a continuación, elija el botón **Agregar** junto a ella. El origen de datos se agrega al archivo diagnostics.cfcfg.

Si está usando Azure SDK 2,5 y desea especificar un origen de datos personalizado, puede agregarlo a la `WindowsEventLog` sección de la diagnostics.wadcfgx de archivos, como en el ejemplo siguiente.

```
<WindowsEventLog scheduledTransferPeriod="PT1M">
   <DataSource name="Application!*" />
   <DataSource name="CustomDataSource!*" />
</WindowsEventLog>
```
### <a name="performance-counters"></a>Contadores de rendimiento

Información de contador de rendimiento puede ayudarle a buscar botella de sistema y ajustar el rendimiento del sistema y de aplicación. Para obtener más información, vea [crear y utilizar contadores de rendimiento en una aplicación de Azure](https://msdn.microsoft.com/library/azure/hh411542.aspx) . Si quiere capturar contadores de rendimiento, active la casilla de verificación **Habilitar la transferencia de rendimiento** . Puede aumentar o disminuir el número de minutos cuando se transfieren los registros de eventos a su cuenta de almacenamiento, cambie el valor de **Período transferir (min.)** . Seleccione las casillas de los contadores de rendimiento que desee hacer un seguimiento.

  ![Contadores de rendimiento](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758147.png)

Para realizar un seguimiento de un contador de rendimiento que no se incluye, escriba mediante la sintaxis sugerida y, a continuación, seleccione el botón **Agregar** . El sistema operativo en la máquina virtual determina qué contadores de rendimiento que puede realizar un seguimiento. Para obtener más información sobre la sintaxis, vea [especificar una ruta de acceso de contador](https://msdn.microsoft.com/library/windows/desktop/aa373193.aspx).

### <a name="infrastructure-logs"></a>Registros de infraestructura

Si quiere capturar registros de infraestructura, que contienen información sobre la infraestructura de diagnóstico de Azure, el módulo de acceso remoto y el módulo RemoteForwarder, seleccione la casilla de verificación **Permitir la transferencia de registros de infraestructura** . Puede aumentar o disminuir el número de minutos cuando se transfieren los registros a su cuenta de almacenamiento, cambie el valor de período transferir (min.).

  ![Registros de diagnósticos de infraestructura](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758148.png)

  Para obtener más información, vea [Recopilar datos de registro mediante el uso de diagnósticos de Azure](https://msdn.microsoft.com/library/azure/gg433048.aspx) .

### <a name="log-directories"></a>Directorios de registro

Si quiere capturar directorios de registro que contienen los datos recopilados de directorios de registro para las solicitudes de Internet Information Services (IIS), solicitudes erróneas o carpetas que elija, seleccione la casilla de verificación **Permitir la transferencia de directorios de registro** . Puede aumentar o disminuir el número de minutos cuando se transfieren los registros a su cuenta de almacenamiento, cambie el valor de **Período transferir (min.)** .

Puede seleccionar las casillas de los registros que desean recopilar, como **Los registros de IIS** y **Solicitar el error** . Se proporcionan nombres de contenedor de almacenamiento de forma predeterminada, pero puede cambiar los nombres si lo desea.

Además, puede capturar registros desde cualquier carpeta. Especifique la ruta de acceso en la sección **registro de directorio absoluta** y, a continuación, elija el botón **Agregar directorio** . Los registros se capturará a los contenedores especificados.

  ![Directorios de registro](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796665.png)

### <a name="etw-logs"></a>ETW registros

Si utiliza el [Seguimiento de eventos de Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803(v=vs.85).aspx) (ETW) y desea capturar registros ETW, seleccione la casilla de verificación **Permitir la transferencia de registros de ETW** . Puede aumentar o disminuir el número de minutos cuando se transfieren los registros a su cuenta de almacenamiento, cambie el valor de **Período transferir (min.)** .

Los eventos se capturan desde orígenes de eventos y manifiestos de eventos que especifique. Para especificar un origen de evento, escriba un nombre en la sección de **Orígenes de eventos** y, a continuación, elija el botón **Agregar origen de evento** . Asimismo, puede especificar un manifiesto de eventos en la sección **Evento manifiestos** y, a continuación, elija el botón **Agregar evento manifiestos** .

  ![ETW registros](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766025.png)

  El marco de trabajo ETW es compatible con ASP.NET a través de clases en [System.Diagnostics.aspx] (espacio de nombres de https://msdn.microsoft.com/library/system.diagnostics (v=vs.110). El espacio de nombres Microsoft.WindowsAzure.Diagnostics, que se deriva y extiende estándar [System.Diagnostics.aspx] (https://msdn.microsoft.com/library/system.diagnostics (v=vs.110) clases, habilita el uso de [System.Diagnostics.aspx] (https://msdn.microsoft.com/library/system.diagnostics (v=vs.110) como un marco de registro en el entorno de Azure. Para obtener más información, vea [tomar el Control de registro y seguimiento en Microsoft Azure](https://msdn.microsoft.com/magazine/ff714589.aspx) y [Diagnósticos de habilitación de servicios de nube de Azure y máquinas virtuales de Windows](./cloud-services/cloud-services-dotnet-diagnostics.md).

### <a name="crash-dumps"></a>Volcados

Si quiere capturar información sobre cuándo se bloquea una instancia de la función, seleccione la casilla de verificación **Permitir la transferencia de bloquearse vuelca** . (Como ASP.NET administra la mayoría de las excepciones, esto es suele ser útil sólo para funciones de trabajo). Puede aumentar o disminuir el porcentaje de espacio de almacenamiento dedicado a la volcados cambiando el valor de la **Cuota de directorio (%)** . Puede cambiar el contenedor de almacenamiento donde se almacenan los volcados y puede seleccionar si desea capturar una descarga **completa** o **Mini** .

Se muestran los procesos que está realizando el seguimiento. Seleccione las casillas de los procesos que desea capturar. Para agregar otro proceso a la lista, escriba el nombre del proceso y, a continuación, elija el botón **Agregar proceso** .

  ![Volcados](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766026.png)

  Consulte [tomar el Control de registro y seguimiento en Microsoft Azure](https://msdn.microsoft.com/magazine/ff714589.aspx) y [Diagnósticos de Microsoft Azure parte 4: componentes de registro personalizados y los cambios de 1.3 de diagnósticos de Azure](http://justazure.com/microsoft-azure-diagnostics-part-4-custom-logging-components-azure-diagnostics-1-3-changes/) para obtener más información.

## <a name="view-the-diagnostics-data"></a>Ver los datos de diagnóstico

Después de que ha recopilado los datos de diagnósticos de un servicio de nube o una máquina virtual, puede verla.

### <a name="to-view-cloud-service-diagnostics-data"></a>Para ver los datos de diagnósticos de servicio de nube

1. Implementar el servicio de nube como de costumbre y ejecútelo.

1. Puede ver los datos de diagnóstico en un informe que Visual Studio genera o tablas en su cuenta de almacenamiento. Para ver los datos en un informe, abra **El Explorador de nube** o el **Explorador de servidores**, abra el menú contextual del nodo de la función que le interese y, a continuación, elija **Ver datos de diagnóstico**.

    ![Ver datos de diagnóstico](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC748912.png)

    Aparece un informe que muestra los datos disponibles.

    ![Informe de diagnóstico de Microsoft Azure en Visual Studio](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796666.png)

    Si los datos más recientes no aparecen, es posible que tenga que esperar a que transcurra el tiempo de transferencia.

    Seleccione el vínculo **Actualizar** para actualizar inmediatamente los datos o elegir un intervalo en el cuadro de lista desplegable de **Actualización automática** para que los datos que se actualizan automáticamente. Para exportar los datos de error, elija el botón **Exportar a CSV** para crear un archivo de valores separados por comas que se puede abrir en una hoja de cálculo.

    En **El Explorador de nube** o el **Explorador de servidores**, abra la cuenta de almacenamiento que está asociada a la implementación.

1. Abra las tablas de diagnósticos en el Visor de la tabla y, a continuación, revise los datos que recopilan. Para los registros de IIS y personalizado, puede abrir un contenedor de blob. Al revisar la siguiente tabla, puede encontrar el contenedor de blob o tabla que contiene los datos que le interese. Además de los datos de ese archivo de registro, las entradas de la tabla contienen EventTickCount, DeploymentId, función y RoleInstance para ayudarle a identificar qué máquina virtual y función generan los datos y cuándo. 

  	|Datos de diagnóstico|Descripción|Ubicación|
  	|---|---|---|
  	|Registros de aplicación|Registros de su código genera llamando métodos de la clase System.Diagnostics.Trace.|WADLogsTable|
  	|Registros de eventos|Estos datos son de los registros de eventos de Windows en los equipos virtuales. Windows almacena información en estos registros, pero las aplicaciones y servicios también usan para informar de errores o registran información.|WADWindowsEventLogsTable|
  	|Contadores de rendimiento|Puede recopilar datos de cualquier contador de rendimiento que está disponible en la máquina virtual. El sistema operativo proporciona contadores de rendimiento, que incluyen las estadísticas como el tiempo de procesador y el uso de memoria.|WADPerformanceCountersTable|
  	|Registros de infraestructura|Estos registros se generan desde la propia infraestructura de diagnóstico.|WADDiagnosticInfrastructureLogsTable|
  	|Registros de IIS|Estos registros graban solicitudes web. Si su servicio de nube Obtiene una gran cantidad de tráfico, estos registros pueden resultar bastante largos, por lo que debe recopilar y almacenar los datos solo cuando lo necesite.|Puede encontrar registros de solicitudes de error en el contenedor de blob en iis-torunda-failedreqlogs en una trayectoria para que su implementación, la función y la instancia. Puede encontrar registros completados en archivos de registro torunda-iis. En la tabla WADDirectories realizadas entradas para cada archivo.|
  	|Volcados|Esta información proporciona imágenes binarias del proceso de su servicio nube (generalmente una función de trabajo).|contenedor de volcados de aplastamiento torunda blob|
  	|Archivos de registro personalizados|Registros de datos predefinida.|Puede especificar en código de la ubicación de archivos de registro personalizados en su cuenta de almacenamiento. Por ejemplo, puede especificar un contenedor de blob personalizado.|

1. Si los datos de cualquier tipo se truncan, puede intentar aumentar el búfer de datos tipo o reducir el intervalo entre las transferencias de datos de la máquina virtual a su cuenta de almacenamiento.

1. (opcional) Purgar los datos de la cuenta de almacenamiento en ocasiones para reducir los costos totales de almacenamiento.

1. Al realizar una implementación completa, se actualiza el archivo de diagnostics.cscfg (.wadcfgx de Azure SDK 2.5) en Azure y su servicio de nube recoge los cambios a la configuración de diagnósticos. Si, en su lugar, actualiza una implementación existente, no se actualiza el archivo .cscfg en Azure. Aún puede cambiar la configuración de diagnósticos, no obstante, siguiendo los pasos de la siguiente sección. Para obtener más información sobre cómo realizar una implementación completa y actualizar una implementación existente, vea [Asistente para publicar aplicaciones de Azure](vs-azure-tools-publish-azure-application-wizard.md).

### <a name="to-view-virtual-machine-diagnostics-data"></a>Para ver los datos de diagnóstico de máquina virtual

1. En el menú contextual para la máquina virtual, elija **Ver diagnósticos de datos**.

    ![Ver datos de diagnóstico en una máquina virtual Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766027.png)

    Se abrirá la ventana de **Diagnósticos de resumen** .

    ![Resumen de diagnósticos de Azure máquina virtual](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796667.png)  

    Si los datos más recientes no aparecen, es posible que tenga que esperar a que transcurra el tiempo de transferencia.

    Seleccione el vínculo **Actualizar** para actualizar inmediatamente los datos o elegir un intervalo en el cuadro de lista desplegable de **Actualización automática** para que los datos que se actualizan automáticamente. Para exportar los datos de error, elija el botón **Exportar a CSV** para crear un archivo de valores separados por comas que se puede abrir en una hoja de cálculo.

## <a name="configure-cloud-service-diagnostics-after-deployment"></a>Configurar diagnósticos de servicio de nube después de la implementación

Si está investigando un problema con una nube servicio que ya se está ejecutando, puede recopilar datos que no ha especificado antes de implementar la función por originalmente. En este caso, puede comenzar a recopilar datos mediante la configuración del explorador de servidores. Puede configurar diagnósticos para una sola instancia o todas las instancias de una función, dependiendo de si se abre el cuadro de diálogo Configuración de diagnósticos en el menú contextual para la instancia o la función. Si configura el nodo de función, los cambios se aplican a todas las instancias. Si configura el nodo de la instancia, los cambios se aplican a esa instancia solo.

### <a name="to-configure-diagnostics-for-a-running-cloud-service"></a>Para configurar los diagnósticos de ejecución de un servicio de nube

1. En el Explorador de servidores, expanda el nodo de **Servicios en la nube** y, a continuación, expanda los nodos para localizar la función o instancia que desea investigar o ambos.

    ![Configuración de diagnósticos](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC748913.png)

1. En el menú contextual para una instancia de nodo o rol, elija **Configuración de diagnósticos de actualización**y, a continuación, elija la configuración de diagnóstico que se desea recopilar.

    Para obtener información acerca de las opciones de configuración, vea **Configurar orígenes de datos de diagnóstico** en este tema. Para obtener información sobre cómo ver los datos de diagnóstico, consulte **Ver los datos de diagnóstico** en este tema.

    Si cambia la recopilación de datos en el **Explorador de servidores**, estos cambios permanecen activas hasta totalmente implementar su servicio de nube. Si usa el valor predeterminado la configuración de publicación, no se sobrescriben los cambios, ya que el valor predeterminado publicar configuración es actualizar la implementación existente, en lugar de realizar una redistribución completa. Para asegurarse de que la configuración borra durante la implementación, vaya a la pestaña **Configuración avanzada** en el Asistente para la publicación y desactive la casilla de verificación **de implementación de actualización** . Al implementar de nuevo con esa casilla de verificación desactivada, la configuración de volver a ellos en el archivo .wadcfgx (o .wadcfg) como conjunto mediante el editor de propiedades de la función. Si actualiza su implementación, Azure mantiene la configuración anterior.

## <a name="troubleshoot-azure-cloud-service-issues"></a>Solucionar problemas de servicio de nube de Azure

Si experimenta problemas con sus proyectos de servicio de nube, como una función que no sale de un estado de "ocupado" varias veces recicla o produce un error de servidor interno, existen herramientas y técnicas que puede usar para diagnosticar y solucionar estos problemas. Ejemplos específicos de los problemas más comunes y soluciones, así como información general sobre los conceptos y las herramientas que se utiliza para diagnosticar y solucionar estos errores, vea [Datos de Azure PaaS calcular diagnósticos](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

## <a name="q--a"></a>Preguntas y respuestas

**¿Qué es el tamaño del búfer y cuánto debería?**

En cada instancia de máquina virtual, las cuotas de limitan la cantidad de datos diagnóstico puede almacenarse en el sistema de archivos local. Además, especificar un tamaño de búfer para cada tipo de datos de diagnóstico están disponibles. Tamaño del búfer actúa como una cuota individual para ese tipo de datos. Activando la parte inferior del cuadro de diálogo, puede determinar la cuota general y la cantidad de memoria que permanece. Si especifica búferes más grandes o más tipos de datos, deberá enfocar la cuota general. Puede cambiar la cuota general modificando el archivo de configuración de diagnostics.wadcfg/.wadcfgx. Los datos de diagnósticos se almacenan en el mismo sistema de archivos como datos de la aplicación, por lo que si la aplicación utiliza una gran cantidad de espacio en disco, no debería aumentar la cuota de diagnósticos general.

**¿Qué es el período de transferencia y cuánto tiempo debe?**

El período de transferencia es la cantidad de tiempo que transcurre entre datos captura. Después de cada período de transferencia, se mueven datos desde el sistema de archivos local en una máquina virtual a tablas en su cuenta de almacenamiento. Si la cantidad de datos que se recopilan supera la cuota antes del final de un período de transferencia, se descartan los datos más antiguos. Desea disminuir el período de transferencia si está perder los datos porque los datos superan el tamaño del búfer o la cuota de general.

**¿Qué zona horaria son las marcas de tiempo en?**

Las marcas de tiempo se encuentran en la zona horaria local del centro de datos que hospeda el servicio de nube. Se usan las siguientes tres columnas de marca de tiempo en las tablas de registro.

  - **PreciseTimeStamp** es la marca de tiempo ETW del evento. Es decir, el tiempo que se registra el evento del cliente.

  - **Marca de tiempo** es PreciseTimeStamp redondeada hacia abajo hasta el límite de la frecuencia de carga. Por lo tanto, si la frecuencia de carga es 5 minutos y el evento tiempo 00:17:12, marca de tiempo será 15:00:00.

  - **Marca de tiempo** es la marca de hora en que se creó la entidad de la tabla de Azure.

**¿Cómo administrar los costos al recopilar información de diagnóstico?**

La configuración predeterminada (el**nivel de registro de** **Error** y **transferir período** establecido en **1 minuto**) está diseñada para minimizar los costos. Los costos del cálculo aumentará si recopilar más datos de diagnósticos o disminuir el período de transferencia. No recopilar más datos que tenga y no se olvide de deshabilitar la recopilación de datos cuando ya no necesite. Puede siempre habilitarlo de nuevo, incluso en tiempo de ejecución, como se muestra en la sección anterior.

**¿Cómo recopilar los registros de error de solicitudes de IIS?**

De forma predeterminada, IIS no recopilar registros de solicitudes de error. Puede configurar IIS para recopilarlos si modifica el archivo web.config para su rol de web.

**No aparece información de seguimiento de los métodos de RoleEntryPoint como OnStart. ¿Qué pasa?**

En el contexto de WAIISHost.exe, IIS no se llama a los métodos de RoleEntryPoint. Por lo tanto, la información de configuración de web.config que normalmente no se aplica habilita el seguimiento. Para resolver este problema, agregue un archivo de configuración a su proyecto de rol web y nombre del archivo para que coincida con el conjunto de resultados que contiene el código de RoleEntryPoint. En el proyecto de rol de web de forma predeterminada, el nombre del archivo config sería WAIISHost.exe.config. A continuación, agregue las siguientes líneas a este archivo:

```
<system.diagnostics>
  <trace>
      <listeners>
          <add name “AzureDiagnostics” type=”Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitorTraceListener”>
              <filter type=”” />
          </add>
      </listeners>
  </trace>
</system.diagnostics>
```

Ahora, en la ventana de **Propiedades** , establezca la propiedad de **Copiar en el directorio de salida** **siempre**copia.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información acerca de diagnósticos de registro en Azure, vea [Habilitar diagnósticos en servicios de nube de Azure y máquinas virtuales de Windows](./cloud-services/cloud-services-dotnet-diagnostics.md) y [Habilitar el registro de diagnósticos para aplicaciones web de servicio de aplicaciones de Azure](./app-service-web/web-sites-enable-diagnostic-log.md).

<properties
    pageTitle="Supervisar Hubs superficies con el análisis de registro | Microsoft Azure"
    description="Utilice la solución de superficie concentrador para realizar un seguimiento del estado de su Hubs de superficie y comprender cómo se utilizan."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="banders"/>

# <a name="monitor-surface-hubs-with-log-analytics"></a>Monitor Hubs superficie con el análisis de registro

Este artículo describe cómo puede usar la solución de concentrador de superficie en el análisis de registro para supervisar los dispositivos de Microsoft Surface concentrador con conjunto de aplicaciones de administración de operaciones de Microsoft (OMS). Análisis de registro le ayuda a realizar un seguimiento del estado de su Hubs de superficie así comprender cómo se utilizan.

Cada concentrador de superficie tenga instalado Agente de supervisión de Microsoft. Su a través del agente que se pueden enviar datos desde el Hub de superficie a OMS. Archivos de registro se leen desde la superficie Hubs y, a continuación, se envían al servicio de OMS. Problemas como servidores está sin conexión, el calendario no se sincroniza, o si no puede iniciar sesión en la cuenta del dispositivo Skype se muestran en OMS en el panel de concentrador de superficie. Usando los datos en el panel, puede identificar los dispositivos que no se ejecuta, o que tienen otros problemas y potencialmente aplicar correcciones para los problemas detectados.


## <a name="installing-and-configuring-the-solution"></a>Instalar y configurar la solución

Use la información siguiente para instalar y configurar la solución. Para administrar su Hubs de superficie de conjunto de aplicaciones de administración de operaciones de Microsoft (OMS), necesitará la siguiente:

- Una suscripción válida a [OMS](http://www.microsoft.com/oms).
- Nivel de [suscripción de OMS](https://azure.microsoft.com/pricing/details/log-analytics/) que admita el número de dispositivos que desea supervisar. Precios de OMS varían dependiendo de cuántos dispositivos se ha inscrito y la cantidad de datos se procesos. Desea tener esto en cuenta al planear el lanzamiento de concentrador de superficie.

A continuación, se agrega una suscripción de OMS a su suscripción de Microsoft Azure existente o crear un área de trabajo directamente a través del portal OMS. Instrucciones detalladas para usar cualquiera de los métodos es en [Introducción a análisis de registro](log-analytics-get-started.md). Una vez configurada la suscripción de OMS, hay dos formas para inscribir a los dispositivos de concentrador de superficie:

- Automáticamente mediante InTune
- Manualmente mediante la **configuración** en el dispositivo de concentrador de superficie.

## <a name="set-up-monitoring"></a>Configurar la supervisión

Puede supervisar el mantenimiento y la actividad de su centro de superficie mediante el análisis de registro de OMS. Puede inscribir el Hub de superficie en OMS mediante InTune o localmente mediante **configuración** en el Hub de superficie.

## <a name="connect-surface-hubs-to-oms-through-intune"></a>Conectar Hubs superficies a OMS mediante InTune

Necesitará el identificador del área de trabajo y la clave de área de trabajo para el área de trabajo OMS que va a administrar su Hubs de superficie. Puede obtenerlas desde el portal OMS.

InTune es un producto de Microsoft que le permite administrar la configuración de OMS que se aplica a uno o varios de los dispositivos de forma centralizada. Siga estos pasos para configurar los dispositivos a través de InTune:

1. Inicie sesión en InTune.
2. Vaya a **Configuración del** > **conectado orígenes**.
3. Crear o editar una directiva basada en la plantilla de concentrador de superficie.
4. Vaya a la sección OMS (perspectivas operativas de Azure) de la directiva y agregue el *Identificador del área de trabajo* y la *Clave de área de trabajo* en la directiva.
5. Guardar la directiva.
6. Asociar la directiva de grupo apropiado de dispositivos.

  ![Directiva de InTune](./media/log-analytics-surface-hubs/intune.png)

InTune, a continuación, sincroniza la configuración de OMS con los dispositivos en el grupo de destino, realizar una inscripción en el área de trabajo OMS.

## <a name="connect-surface-hubs-to-oms-using-the-settings-app"></a>Conectar Hubs de superficie a OMS mediante la aplicación de configuración

Necesitará el identificador del área de trabajo y la clave de área de trabajo para el área de trabajo OMS que va a administrar su Hubs de superficie. Puede obtenerlas desde el portal OMS.

Si no usa InTune para administrar su entorno, puede inscribir dispositivos manualmente a través de la **configuración** de cada concentrador de superficie:

1. Desde su centro de superficie, abre **configuración**.
2. Escriba las credenciales de administrador de dispositivos cuando se le solicite.
3. Haga clic en **este dispositivo**y debajo de **supervisión**, haga clic en **Configurar opciones de OMS**.
4. Seleccione **Habilitar la supervisión**.
6. En el cuadro de diálogo de configuración de OMS, escriba el **Identificador del área de trabajo** y escriba la **Clave del área de trabajo**.  
  ![Configuración](./media/log-analytics-surface-hubs/settings.png)
7. Haga clic en **Aceptar** para completar la configuración.

Aparecerá una confirmación que indica si la configuración de OMS aplicó correctamente el dispositivo. Si es así, aparecerá un mensaje que indica que el agente se ha conectado correctamente al servicio de OMS. El dispositivo comienza a enviar datos a OMS, donde puede ver y trabajar con ellos.

## <a name="monitor-surface-hubs"></a>Monitor superficie Hubs

Supervisar su Hubs superficie usar OMS es muy similar a cualquier otro dispositivo inscrito de supervisión.

1. Inicie sesión en el portal OMS.
2. Vaya a Panel de paquete de solución de concentrador de superficie.
3. Se muestra el estado de su dispositivo.

  ![Panel de concentrador de superficie](./media/log-analytics-surface-hubs/surface-hub-dashboard.png)

Puede crear [alertas](log-analytics-alerts.md) basadas en búsquedas de registro existente o personalizado. Utilizando los datos de que la OMS recopila de su Hubs de superficie, puede buscar problemas y de alerta en las condiciones que defina para los dispositivos.


## <a name="next-steps"></a>Pasos siguientes

- Usar [búsquedas de registro de análisis de registro](log-analytics-log-searches.md) para ver datos detallados de concentrador de superficie.
- Crear [alertas](log-analytics-alerts.md) para que le notifique cuando se producen problemas con su Hubs de superficie.

<properties 
   pageTitle="Azure compromiso móvil guía - análisis de solución de problemas" 
   description="Solución de problemas de análisis, supervisión, segmentación y paneles en Azure Mobile compromiso" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="08/19/2016"
   ms.author="piyushjo"/>

# <a name="troubleshooting-guide-for-analytics-monitoring-segmentation-and-dashboard-issues"></a>Guía para solucionar problemas de análisis, supervisión, segmentación y paneles

Los siguientes son posibles problemas que puede encontrarse con cómo Azure Mobile compromiso recopila información sobre sus aplicaciones, dispositivos y usuarios.

## <a name="missingdelayed-information"></a>Información que faltan o demorada

### <a name="issue"></a>Problema
- Información se retrasa en que aparecen en análisis, segmentación o paneles.
- Falta información de supervisión.
- Falta información de análisis, segmentación o paneles.
- Alcance segmentación limita.

### <a name="causes"></a>Causas

- Puede usar la API de análisis de API Monitor, y segmentos API para ver si los datos faltan en la interfaz de usuario es visible a través de la API.
- Si el SDK de compromiso de Azure móvil no está integrado correctamente en la aplicación no podrá ver la información en el análisis, segmentación, supervisión o paneles.
- Segmentos no se pueden cambiar una vez que se crean, segmentos pueden ser (copiados) "clonar" o "destruida" (eliminar). Segmentos sólo pueden contener 10 criterios.
- Configurar un dispositivo de prueba, desinstalar y volver a instalar la aplicación en el dispositivo de prueba es la mejor manera de comprobar la información que falta de supervisión.
- Información se actualiza cada 24 horas para análisis, segmentación o paneles.
- No se puede mostrar la información en segmentos nuevos hasta 24 horas después de que se crean incluso si el segmento se basa en la información anterior.
- Filtrar los datos de análisis en la interfaz de usuario mostrará todos los ejemplos de este tipo independientemente de la versión de la aplicación (por ejemplo, "se bloquea" filtrada por nombre mostrará de versión 1 y 2 de la aplicación).
- El período de tiempo para análisis se basa en la fecha de la configuración de dispositivo de los usuarios, por lo que un usuario cuyo teléfono tiene la fecha establecida correctamente podría mostrarse en el período de tiempo incorrecta.
- No inserta ningún lado del servidor se registran los datos cuando use el botón "probar", solo se registran los datos para las campañas de inserción real.

## <a name="cant-locate-items-in-ui"></a>No se puede buscar elementos de interfaz de usuario

### <a name="issue"></a>Problema
- No puede crear segmentos basándose en determinados integrada o criterios de la etiqueta de información de la aplicación personalizada.
- No puede encontrar determinados integrada o criterios en análisis, supervisión o paneles de etiqueta de la información de la aplicación personalizada.
- No puede interpretar los datos de análisis, supervisión, segmentación o paneles.

### <a name="causes"></a>Causas

- Algunas integradas en elementos y etiquetas de información de aplicación solo están disponibles como criterios de inserción, pero no pueden ser agregado a un segmento o visible de análisis, supervisión o paneles. 
- Para de elementos y etiquetas de información de aplicación que no se pueden agregar a un segmento, necesitará lista de configuración de identificación de criterios en cada campaña para realizar la misma función como destino según un segmento.
- Ver los menús de contexto en las secciones de análisis, supervisión, segmentación y paneles de la interfaz de usuario de Azure Mobile compromiso para obtener más ayuda y cómo la información.

## <a name="crash-troubleshooting"></a>Bloquee la solución de problemas

### <a name="issue"></a>Problema
- Aplicación se bloquea aparecen en análisis, supervisión o paneles.

### <a name="causes"></a>Causas

- Para solucionar problemas de aplicación se bloquea visto en análisis, supervisión o paneles Asegúrese de que las notas para los problemas conocidos con versiones anteriores de SDK.
- Para solucionar errores de aplicación realizan un evento desde un dispositivo de prueba con la aplicación instalada y busque el identificador de dispositivo en la sección "Eventos de – Monitor" de la interfaz de usuario de compromiso de Azure Mobile. A continuación, realice el evento que causa la aplicación bloquearse y buscar información adicional en la sección "Monitor – bloqueo" de la interfaz de usuario de compromiso de Azure Mobile. 

 

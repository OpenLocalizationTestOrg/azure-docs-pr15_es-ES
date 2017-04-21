<properties
   pageTitle="Introducción a la solución de auditoría y de seguridad del conjunto de aplicaciones de administración de operaciones | Microsoft Azure"
   description="Este documento le ayudará a empezar a usar seguridad del conjunto de aplicaciones de administración de operaciones y capacidades de la solución de auditoría para supervisar la nube híbrida."
   services="operations-management-suite"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="operations-management-suite"
   ms.topic="get-started-article" 
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/20/2016"
   ms.author="yurid"/>
 
# <a name="getting-started-with-operations-management-suite-security-and-audit-solution"></a>Introducción a la seguridad del conjunto de aplicaciones de administración de operaciones y solución de auditoría
Este documento le ayudará a empezar rápidamente con las capacidades de solución de auditoría y seguridad (OMS) del conjunto de aplicaciones de administración de las operaciones al guiarle a través de cada opción.

## <a name="what-is-oms"></a>¿Qué es OMS?
Suite de administración de operaciones de Microsoft (OMS) es basado en la nube administración solución de Microsoft que le ayuda a administrar y proteger su local y la infraestructura de nube. Para obtener más información acerca de OMS, lea el artículo de la [Serie de administración de operaciones](https://technet.microsoft.com/library/mt484091.aspx).

## <a name="oms-security-and-audit-dashboard"></a>Panel de seguridad de OMS y auditoría

La solución de seguridad de OMS y auditoría proporciona una vista completa de su organización posición de seguridad de TI con consultas de búsqueda integrada para problemas importantes que requieren su atención. El panel de **seguridad y auditoría** es la pantalla principal para todos los elementos relacionados con la seguridad en OMS. Proporciona información de alto nivel sobre el estado de seguridad de sus equipos. También incluye la capacidad para ver todos los eventos de las últimas 24 horas, 7 días, o cualquier otro período de tiempo personalizado. Para tener acceso al panel de **seguridad y auditoría** , siga estos pasos:

1. En el panel principal del **Conjunto de aplicaciones de administración de operaciones de Microsoft** , haga clic en el icono de **configuración** de la izquierda.
2. En el módulo de **configuración** , en **soluciones** , haga clic en la opción de **seguridad y auditoría** .
3. Aparecerá el panel de **seguridad y auditoría** :

    ![Panel de seguridad de OMS y auditoría](./media/oms-security-getting-started/oms-getting-started-fig1-ga.png)

Si tiene acceso a este panel por primera vez y no tiene dispositivos supervisados por OMS, los mosaicos no se rellenará con datos obtenidos desde el agente. Una vez instalado el agente, puede tardar algún tiempo para rellenar, por lo tanto, lo que ve inicialmente que falte algunos datos mientras aún está cargando en la nube.  En este caso, es normal para ver algunas mosaicos sin información de material. Lea los [equipos con Windows conectarse directamente a OMS](https://technet.microsoft.com/library/mt484108.aspx) para obtener más información sobre cómo instalar a agente OMS en un sistema de Windows y [equipos Linux conectarse a OMS](https://technet.microsoft.com/library/mt622052.aspx) para obtener más información sobre cómo llevar a cabo esta tarea en un sistema Linux.

> [AZURE.NOTE] El agente de recopilar la información basándose en los eventos actuales que están habilitados, por ejemplo el nombre del equipo, el nombre de usuario y dirección IP. Sin embargo se recopilan sin documentos o archivos, el nombre de la base de datos o datos privados.   

Las soluciones son una colección de reglas de adquisición de lógica, visualización y datos que tratar los desafíos clave del cliente. Seguridad y auditoría es una solución, otros usuarios pueden agregarse por separado. Lea el artículo [Agregar soluciones](https://technet.microsoft.com/library/mt674635.aspx) para obtener más información sobre cómo agregar una nueva solución.

El panel de auditoría y seguridad OMS se organiza en cuatro categorías principales:

- **Dominios de seguridad**: en esta área podrán más explorar los registros de seguridad con el tiempo, acceder a la evaluación de malware, actualice la evaluación, seguridad de red, información de identidad y acceso, equipos con los eventos de seguridad y acceder rápidamente al panel Centro de seguridad de Azure.
- **Problemas notables**: esta opción permite identificar rápidamente el número de problemas activos y la gravedad de estos problemas.
- **Detecciones (Preview)**: le permite identificar patrones de ataque visualizar las alertas de seguridad como producirá frente a los recursos.
- **Información sobre amenazas**: le permite identificar patrones de ataque al visualizar el número total de servidores con el tráfico saliente de IP malintencionado, el tipo de amenaza malintencionados y un mapa que muestra dónde proceden estas direcciones IP. 
- **Consultas comunes de seguridad**: esta opción proporciona una lista de las consultas de seguridad más comunes que puede usar para supervisar su entorno. Al hacer clic en una de las consultas, se abre el módulo de **búsqueda** con los resultados de esa consulta.

> [AZURE.NOTE] Para obtener más información sobre cómo OMS conserva los datos seguros, lea que cómo OMS protege los datos.

## <a name="security-domains"></a>Dominios de seguridad

Al supervisar recursos, es importante poder acceder rápidamente el estado actual de su entorno. Sin embargo también es importante poder realizar un seguimiento posterior eventos que ocurrieron en el pasado que puede dar lugar a comprender mejor de lo que sucede en su entorno en determinado punto en el tiempo. 

> [AZURE.NOTE] retención de datos está de acuerdo con la OMS plan de precios. Para obtener más información, visite el [Conjunto de aplicaciones de administración de operaciones de Microsoft](https://www.microsoft.com/server-cloud/operations-management-suite/pricing.aspx) página de precios.

Escenarios de investigación de respuesta y análisis incidentes le vendrá directamente desde los resultados disponibles en el mosaico de **Registros de seguridad con el tiempo** .

![Registros de seguridad con el tiempo](./media/oms-security-getting-started/oms-getting-started-fig2.JPG)

Al hacer clic en este mosaico, se abrirá el módulo de **búsqueda** , que muestra el resultado de una consulta de **Eventos de seguridad** (tipo = SecurityEvents) con los datos en función de los siete últimos días, como se muestra a continuación:

![Registros de seguridad con el tiempo](./media/oms-security-getting-started/oms-getting-started-fig3.JPG)

El resultado de la búsqueda se divide en dos paneles: el panel izquierdo le ofrece un desglose del número de eventos de seguridad que se encontraron los equipos en los que se encontraron estos eventos, el número de cuentas encontrados en estos equipos y los tipos de actividades. El panel derecho proporciona los resultados totales y una vista cronológica de los eventos de seguridad con actividad de nombre y los eventos del equipo. También puede hacer clic en **Mostrar más** para ver más detalles sobre este evento, como los datos del evento, el identificador de evento y el origen del evento.

> [AZURE.NOTE] Para obtener más información acerca de la consulta de búsqueda OMS, lea [OMS Buscar referencia](https://technet.microsoft.com/library/mt450427.aspx).

### <a name="antimalware-assessment"></a>Evaluación de antimalware

Esta opción permite identificar rápidamente equipos con protección insuficiente y equipos que se han visto afectados por un fragmento de código malintencionado. Estado de la evaluación de malware y amenazas detectadas en los servidores supervisados se leen y, a continuación, los datos se envían al servicio de OMS en la nube para el procesamiento. Servidores con detectan amenazas y servidores con protección insuficiente se muestran en el panel de evaluación de malware, que es accesible después de hacer clic en el mosaico de la **Evaluación de Antimalware** . 

![evaluación de malware](./media/oms-security-getting-started/oms-getting-started-fig4-ga.png)

Al igual que cualquier otro icono disponible en el panel de OMS, al hacer clic en él, se abrirá el módulo de **búsqueda** con el resultado de la consulta. Para esta opción, si hace clic en la opción **No informes** de **Estado de protección**, tendrá el resultado de la consulta que muestra esta entrada que contiene el nombre del equipo y su jerarquía, tal como se muestra a continuación:

![resultado de la búsqueda](./media/oms-security-getting-started/oms-getting-started-fig5.png)

> [AZURE.NOTE] *rango* es una calificación ofreciendo para reflejar el estado de la protección (activado, desactivado, actualizado, etcetera) y amenazas que se encuentran. Tiene como un número ayuda a realizar agregaciones.

Si hace clic en el nombre del equipo, tendrá la vista cronológica del estado de protección de este equipo. Esto es muy útil para escenarios en los que debe conocer si una vez se ha instalado el antimalware y en algún momento en que se quitó.   

### <a name="update-assessment"></a>Evaluación de actualización 

Esta opción permite determinar rápidamente la exposición general a posibles problemas de seguridad y si o la importancia de estas actualizaciones son para su entorno. OMS solución de seguridad y auditoría proporcionan solo la visualización de las actualizaciones, los datos reales proceden de [Soluciones de actualizaciones de sistema](https://technet.microsoft.com/library/mt484096.aspx), que es un módulo diferente dentro de OMS. He aquí un ejemplo de las actualizaciones de:

![actualizaciones del sistema](./media/oms-security-getting-started/oms-getting-started-fig6.png)

> [AZURE.NOTE] Para obtener más información sobre la solución de actualizaciones, lea [actualizar servidores con la solución de actualizaciones del sistema](https://technet.microsoft.com/library/mt484096.aspx).

### <a name="identity-and-access"></a>Acceso e identidad

Identidad debe ser el plano de control para su empresa, proteger su identidad debe ser la máxima prioridad. Mientras que en el pasado había perímetros alrededor de las organizaciones y los perímetros fueron uno de los límites defensivos principales, en la actualidad con más datos y más aplicaciones moverse a la nube la identidad se convierte en el perímetro nuevo. 

> [AZURE.NOTE] actualmente, los datos se basan en datos de inicio de sesión de eventos de seguridad (evento 4624 ID) en los futuros inicios de sesión de Office 365 y también se incluyen datos de Azure AD.

Mediante la supervisión de sus actividades de identidad podrá tomar medidas proactivas antes de que un incidente tiene lugar o reactivas acciones para dejar de un intento de ataque. El panel de **acceso e identidad** proporciona información general sobre el estado de identidad, incluidos el número de intentos de inicio de sesión, la cuenta del usuario que se utilizaron durante los intentos, las cuentas que se hayan bloqueado, cuentas con cambia o restablece la contraseña y actualmente en número de cuentas que se ha iniciado sesión en. 

Al hacer clic en el mosaico de **identidad y acceso** se muestra el panel siguiente:

![acceso e identidad](./media/oms-security-getting-started/oms-getting-started-fig7-ga.png)

La información disponible en este panel inmediatamente puede ayudarle a identificar una actividad sospechosa posible. Por ejemplo, hay 338 intenta iniciar sesión como **Administrador** y 100% de estos intentos de error. Esto puede deberse a un ataque de fuerza bruta contra esta cuenta. Si hace clic en esta cuenta obtendrá más información que puede ayudarle a determinar el recurso de destino para que este ataque posible:

![resultados de búsqueda](./media/oms-security-getting-started/oms-getting-started-fig8.JPG)

El informe detallado ofrece información importante sobre este evento, incluidos: el equipo de destino, el tipo de inicio de sesión (en este caso inicio de sesión de red), la actividad (en este caso evento 4625) y una escala de tiempo completa de cada intento. 

### <a name="computers"></a>Equipos

Este mosaico puede utilizarse para tener acceso a todos los equipos que tienen activa eventos de seguridad. Al hacer clic en este mosaico verá la lista de equipos con el número de eventos y eventos de seguridad en cada equipo:

![Equipos](./media/oms-security-getting-started/oms-getting-started-fig9.JPG)

Puede continuar con su investigación haciendo clic en cada equipo y revisar los eventos de seguridad que se han marcado.

### <a name="azure-security-center"></a>Centro de seguridad de Azure

Este mosaico básicamente es un acceso directo para tener acceso a paneles de centro de seguridad de Azure. Para obtener más información sobre esta solución, lea [Introducción al centro de seguridad de Azure](../security-center/security-center-get-started.md) .

## <a name="notable-issues"></a>Problemas importantes

La finalidad principal de este grupo de opciones es proporcionar una vista rápida de los problemas que tiene en su entorno, su clasificación en tareas críticas, advertencia e informativo. El mosaico de tipo de problema activo es una visualización de estos problemas, pero no le permite explorar más detalles acerca de ellos, para que necesita usar la parte inferior de este mosaico que tiene el nombre del problema (nombre), el número de objetos tenía esto suceda (número) y lo importante que es (gravedad).

![Problemas importantes](./media/oms-security-getting-started/oms-getting-started-fig10.JPG)

Puede ver que estos problemas ya se tratan en distintas áreas del grupo de **Seguridad de dominios** , que refuerza la finalidad de esta vista: visualizar los problemas más importantes en su entorno desde un único lugar.

## <a name="detections-preview"></a>Detecciones (vista preliminar)

La finalidad principal de esta opción es permitir que TI para identificar rápidamente las posibles amenazas a su entorno mediante y la gravedad de esta amenaza.

![Amenaza Intel](./media/oms-security-getting-started/oms-getting-started-fig12.png)

Esta opción puede utilizarse también durante una investigación de respuesta a incidencias para realizar la evaluación y obtener más información sobre el ataque.

> [AZURE.NOTE] Para obtener más información sobre cómo usar OMS incidente de respuesta, vea [cómo aprovechar el centro de seguridad de Azure & serie de administración de operaciones de Microsoft para una respuesta de incidente](https://channel9.msdn.com/Blogs/Taste-of-Premier/ToP1703).

## <a name="threat-intelligence"></a>Información sobre amenazas

La nueva sección de inteligencia de amenaza de la solución de seguridad y auditoría visualiza los patrones de ataque posibles de varias formas: el número total de servidores con el tráfico saliente de IP malintencionado, el tipo de amenaza malintencionados y un mapa que muestra dónde proceden estas direcciones IP. Puede interactuar con el mapa y haga clic en las direcciones IP para obtener más información.

Peonzas amarillos en el mapa indican el tráfico entrante de direcciones IP malintencionado. No es extraño para los servidores que se exponen en internet para ver el tráfico malintencionado entrante, pero se recomienda revisar estos intentos para asegurarse de que ninguna de ellas fue correcta. Estos indicadores se basan en los registros de IIS, WireData y registros de Firewall de Windows.  

![Amenaza Intel](./media/oms-security-getting-started/oms-getting-started-fig11-ga.png)

## <a name="common-security-queries"></a>Consultas comunes de seguridad

La lista de consultas comunes de seguridad disponibles puede ser útil para acceder rápidamente a información del recurso y personalizarlo según las necesidades de su entorno. Estas consultas comunes son:

- Todas las actividades de seguridad
- Actividades de seguridad en el equipo "computer01.contoso.com" (reemplazar con su propio nombre de equipo)
- Actividades de seguridad en el equipo "computer01.contoso.com" para la cuenta "Administrador" (reemplazar con sus propios nombres de equipo y cuenta)
- Inicie sesión en la actividad de equipo
- Cuentas que ha finalizado antimalware de Microsoft en cualquier equipo
- Equipos donde se ha finalizado el proceso de antimalware de Microsoft
- Equipos donde estaba "hash.exe" ejecutan (reemplazar con el nombre de otro proceso)
- Todos los nombres de procesos que se han ejecutado
- Actividad de cuenta de inicio de sesión
- Cuentas que remotamente ha iniciado sesión en el equipo "computer01.contoso.com" (reemplazar con su propio nombre de equipo)

## <a name="see-also"></a>Vea también

En este documento, se han introducido para la solución de seguridad de OMS y auditoría. Para obtener más información sobre la seguridad de OMS, consulte los artículos siguientes:

- [Información general de las operaciones Management Suite (OMS)](operations-management-suite-overview.md)
- [Supervisar y responder a las alertas de seguridad de seguridad del conjunto de aplicaciones de administración de operaciones y solución de auditoría](oms-security-responding-alerts.md)
- [Supervisar los recursos de seguridad del conjunto de aplicaciones de administración de operaciones y solución de auditoría](oms-security-monitoring-resources.md)

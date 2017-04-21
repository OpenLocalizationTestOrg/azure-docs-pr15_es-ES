<properties
   pageTitle="Supervisar y responder a las alertas de seguridad de seguridad del conjunto de aplicaciones de administración de operaciones y solución de auditoría | Microsoft Azure"
   description="Este documento le ayuda a utilizar la opción de inteligencia de amenaza disponible en seguridad de OMS y auditoría para supervisar y responder a las alertas de seguridad."
   services="operations-management-suite"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="operations-management-suite"
   ms.topic="article" 
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/18/2016"
   ms.author="yurid"/>

# <a name="monitoring-and-responding-to-security-alerts-in-operations-management-suite-security-and-audit-solution"></a>Supervisar y responder a alertas de seguridad de seguridad del conjunto de aplicaciones de administración de operaciones y solución de auditoría

Este documento le ayuda a utilizar la opción de inteligencia de amenaza disponible en seguridad de OMS y auditoría para supervisar y responder a las alertas de seguridad.

## <a name="what-is-oms"></a>¿Qué es OMS?

Suite de administración de operaciones de Microsoft (OMS) es la nube de Microsoft basado en solución de administración de TI que le ayuda a administrar y proteger su local y la infraestructura de nube. Para obtener más información acerca de OMS, lea el artículo de la [Serie de administración de operaciones](https://technet.microsoft.com/library/mt484091.aspx).

## <a name="threat-intelligence"></a>Información sobre amenazas

En un entorno donde los usuarios tienen amplio acceso a la red y usar una gran variedad de dispositivos para conectarse a datos corporativos de empresa, es fundamental que puede supervisar los recursos de forma activa y responder rápidamente a las incidencias de seguridad. Esto es especialmente importante desde la perspectiva del ciclo de vida de seguridad porque algunas la ciberseguridad no pueden provocar amenazas alertas o sospechosas actividades que pueden identificarse mediante controles técnicos de seguridad tradicionales. 

Con la opción de **Inteligencia de amenaza** disponible en seguridad de OMS y auditoría, los administradores de TI puede identificar amenazas de seguridad contra el entorno, por ejemplo, identifique si un determinado equipo es parte de una [red de zombis](https://www.microsoft.com/security/sir/story/default.aspx#!botnetsection). Equipos pueden resultar nodos en una red de zombis cuando atacantes forma ilegal instalación malware que secreto conecta este equipo con el comando y control. También puede identificar posibles amenazas procedentes de canales de comunicación bajo tierra, como [darknet](https://www.microsoft.com/security/sir/story/default.aspx#!botnetsection_honeypots_darkents). 

Para generar esta inteligencia amenaza, auditoría y seguridad OMS usan datos procedentes de varios orígenes de Microsoft. Seguridad de OMS y auditoría aprovecharán estos datos para identificar posibles amenazas para su entorno.

El panel de información sobre amenazas está compuesto por tres opciones principales:
- Servidores con el tráfico saliente malintencionado
- Tipos de amenazas detectados
- Mapa de inteligencia de amenaza

> [AZURE.NOTE] Para obtener información general de todas estas opciones, lea la [Introducción a la solución de auditoría y de seguridad del conjunto de aplicaciones de administración de operaciones](oms-security-getting-started.md).

### <a name="responding-to-security-alerts"></a>Responder a las alertas de seguridad

Uno de los pasos del proceso de [respuesta a incidencias seguridad](https://technet.microsoft.com/library/cc512623.aspx) es identificar la gravedad de los sistemas de compromiso. En esta fase debe realizar las siguientes tareas:

- Determinar la naturaleza del ataque
- Determinar el punto de ataque de origen
- Determinar la intención del ataque. Ha sido un ataque dirigido específicamente a su organización para conseguir información concreta o ha sido aleatorio?
- Identificar los sistemas que se han comprometido
- Identificar los archivos que se ha tenido acceso y determinan la sensibilidad de los archivos

Puede aprovechar la información **Sobre amenazas** de seguridad de OMS y solución de auditoría para ayudarle con estas tareas. Siga los pasos siguientes para tener acceso a estas opciones de **Sobre amenazas** :

1. En el panel principal del **Conjunto de aplicaciones de administración de operaciones de Microsoft** , haga clic en mosaico de **seguridad y auditoría** .

    ![Seguridad y auditoría](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig1.png)

2. En el panel de **seguridad y auditoría** , verá las opciones de **Sobre amenazas** en la derecha, como se muestra a continuación:

    ![Amenaza intel](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig2-ga.png)

Estos tres mosaicos le proporcionará información general sobre las amenazas actuales. En el **servidor con el tráfico saliente malintencionado** que podrá identificar si hay cualquier equipo que supervisa (dentro o fuera de la red) está enviando el tráfico malintencionado a Internet. 

El mosaico de **los tipos de amenazas detectados** muestra un resumen de las amenazas actual "en la naturaleza", si hace clic en este mosaico podrá ver más detalles acerca de estas amenazas tal como se muestra a continuación:

![Tipos de amenaza detectada](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig3.png)

Más información sobre cada amenaza puede extraer haciendo clic en él. El ejemplo siguiente muestra más detalles sobre Botnet:

![más detalles sobre una amenaza](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig4.png)

Como se describe en el principio de esta sección, esta información puede ser muy útil durante un caso de respuesta a incidencias. También puede ser importante durante una investigación detallada, donde debe buscar el origen del ataque, qué sistema se ha comprometido y la escala de tiempo. En este informe se pueden identificar fácilmente algunos detalles sobre el ataque, como por ejemplo: el origen del ataque, la IP local que se ha comprometido y el estado de la sesión actual de la conexión. 

El **mapa de inteligencia de amenazas** le ayudará a identificar las ubicaciones del mundo actuales que tienen el tráfico malintencionado. Hay naranja (entrante) y flechas (salientes) rojo en esta asignación para identificar la dirección del tráfico, si hace clic en una de estas flechas, se muestran el tipo de amenaza y la dirección del tráfico tal como se muestra a continuación:

![mapa de intel amenazas](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig5.png)

> [AZURE.NOTE] Puede ver una demostración sobre cómo usar esta capacidad durante un incidente de proceso de respuesta observando la presentación [mitigar amenazas de seguridad de centro de datos con guiado investigación con el conjunto de aplicaciones de administración de las operaciones de](https://myignite.microsoft.com/videos/5000) entrega en Microsoft Ignite.

## <a name="see-also"></a>Vea también

En este documento, ha aprendido cómo utilizar la opción de **Amenaza inteligencia** en seguridad de OMS y solución de auditoría para responder a las alertas de seguridad. Para obtener más información sobre la seguridad de OMS, consulte los artículos siguientes:

- [Información general de las operaciones Management Suite (OMS)](operations-management-suite-overview.md)
- [Introducción a la seguridad del conjunto de aplicaciones de administración de operaciones y solución de auditoría](oms-security-getting-started.md)
- [Supervisar los recursos de seguridad del conjunto de aplicaciones de administración de operaciones y solución de auditoría](oms-security-monitoring-resources.md)

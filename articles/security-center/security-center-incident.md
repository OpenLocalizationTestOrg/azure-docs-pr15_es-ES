<properties
   pageTitle="Controlar el incidente de seguridad en el centro de seguridad de Azure | Microsoft Azure"
   description="Este documento le ayuda a utilizar las capacidades del centro de seguridad de Azure para controlar incidencias de seguridad."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.topic="hero-article"
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/18/2016"
   ms.author="yurid"/>

# <a name="handling-security-incident-in-azure-security-center"></a>Control el incidente de seguridad en el centro de seguridad de Azure 
Clasificación e investigar las alertas de seguridad pueden llevar mucho tiempo para incluso los analistas de seguridad más calificados y para muchos es difícil saber dónde comenzar. Mediante [el análisis](security-center-detection-capabilities.md) para conectar la información entre distintas de [las alertas de seguridad](security-center-managing-and-responding-alerts.md), el centro de seguridad puede proporcionar una vista única de una campaña de ataque y todas las alertas relacionadas: puede saber rápidamente las acciones que tomó el intruso y los recursos que se ven afectados.

Este documento describe cómo usar las capacidades de alerta de seguridad en el centro de seguridad para ayudarle a controlar las incidencias de seguridad.


## <a name="what-is-a-security-incident"></a>¿Qué es un incidente de seguridad?

En el centro de seguridad, un incidente de seguridad es una agregación de todas las alertas para un recurso que se alinean con patrones de [eliminar la cadena](https://blogs.technet.microsoft.com/office365security/addressing-your-cxos-top-five-cloud-security-concerns/) . Incidencias aparecen en el mosaico de [Las alertas de seguridad](security-center-managing-and-responding-alerts.md) y el módulo. Un incidente mostrará la lista de alertas relacionadas, que permite obtener más información sobre cada aparición.

## <a name="managing-security-incidents"></a>Administrar las incidencias de seguridad

Puede revisar su incidencias de seguridad actual consultando el mosaico de alertas de seguridad. Acceder al Portal de Azure y siga los pasos siguientes para ver más detalles sobre cada incidente de seguridad:

1. En el panel Centro de seguridad, verá el mosaico de **las alertas de seguridad** .

    ![Mosaico de las alertas de seguridad en el centro de seguridad](./media/security-center-incident/security-center-incident-fig1.png)

2.  Haga clic en este mosaico para expandir y si se detecta un incidente de seguridad, aparecerá en el gráfico de alertas de seguridad, tal como se muestra a continuación:

    ![Incidente de seguridad](./media/security-center-incident/security-center-incident-fig2.png)

3.  Observe que la descripción de incidencias de seguridad tiene un icono diferente en comparación con las demás alertas. Haga clic en él para ver más detalles sobre este incidente.

    ![Incidente de seguridad](./media/security-center-incident/security-center-incident-fig3.png)

4.  Sobre el **incidente** módulo podrá ver más detalles sobre este incidente de seguridad, que incluye su descripción completa, su gravedad (que en este caso es alta), su estado actual (en este caso todavía está *activa*, lo que implica que el usuario no ha realizado una acción para *Descartar* - Esto puede hacerse haciendo clic en el incidente en el módulo de **las alertas de seguridad** ) , el recurso han (en este caso *VM1*), los pasos de la corrección para el incidente y en el panel inferior tiene las alertas que se incluyen en este incidente. Si desea obtener más información sobre cada alerta, haga clic en ella y otro módulo se abrirá, tal como se muestra a continuación:

    ![Incidente de seguridad](./media/security-center-incident/security-center-incident-fig4.png)

La información de este módulo varían según la alerta. Para obtener más información sobre cómo administrar las alertas, lea [administrar y responder a las alertas de seguridad en el centro de seguridad de Azure](security-center-managing-and-responding-alerts.md) . Aspectos importantes sobre esta función:

- Un nuevo filtro le permite personalizar la vista solo incidente, solo alertas o ambos. 
- La misma alerta puede existir como parte de un incidente (si procede), así como sea visible como una alerta independiente. 
- Descartar un incidente, no se cerrará la alertas relacionadas.

## <a name="see-also"></a>Vea también

En este documento, ha aprendido cómo utilizar la capacidad de incidencias de seguridad en el centro de seguridad. Para obtener más información sobre el centro de seguridad, consulte lo siguiente:

- [Administrar y responder a las alertas de seguridad en el centro de seguridad de Azure](security-center-managing-and-responding-alerts.md)
- [Capacidades de detección de centro de seguridad de Azure](security-center-detection-capabilities.md)
- [Centro de seguridad de Azure Guía de planeación y operaciones](security-center-planning-and-operations-guide.md)
- [Administrar y responder a las alertas de seguridad en el centro de seguridad de Azure](security-center-managing-and-responding-alerts.md)
- [Preguntas más frecuentes sobre centro de seguridad de azure](security-center-faq.md)--buscar con frecuencia preguntas más frecuentes sobre el uso del servicio.
- Entradas de [blog de seguridad de azure](http://blogs.msdn.com/b/azuresecurity/)--buscar blog sobre cumplimiento y la seguridad de Azure.

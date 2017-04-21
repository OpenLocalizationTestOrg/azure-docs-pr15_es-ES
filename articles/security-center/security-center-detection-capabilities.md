<properties
   pageTitle="Capacidades de detección del centro de seguridad de Azure | Microsoft Azure"
   description="Este documento le ayuda a comprender cómo funcionan las capacidades de detección de centro de seguridad de Azure."
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
   ms.date="09/22/2016"
   ms.author="yurid"/>

# <a name="azure-security-center-detection-capabilities"></a>Capacidades de detección de centro de seguridad de Azure
Este documento trata las capacidades avanzadas de detección del centro de seguridad de Azure, que ayuda a identificar amenazas activas dirige a los recursos de Microsoft Azure y le ofrece la información necesaria para responder rápidamente.

> [AZURE.NOTE] Detecciones avanzadas están disponibles en el estándar nivel de Azure centro de seguridad. Una prueba gratuita de 90 días está disponible. Puede actualizar desde la selección de nivel de precios en la [Directiva de seguridad](security-center-policies.md). Visite la [página Centro de seguridad](https://azure.microsoft.com/pricing/details/security-center/) para obtener más información sobre precios. 


## <a name="responding-to-todays-threats"></a>Responder a amenazas
Ha habido cambios significativos en el panorama de amenazas durante los últimos años 20. En el pasado, las empresas normalmente sólo tenían que preocuparse desfiguración de sitios web por atacantes individuales que eran interesado en ver "¿qué puede hacer". Atacantes de hoy son mucho más sofisticados; organizados A menudo tienen objetivos financieros y estratégicos específicos. También tienen más recursos disponibles, como puede financian Estados de todo el país o la delincuencia organizada.

Este enfoque ha llevado a un nivel de profesionalidad en los rangos de intruso sin precedentes. ¿Ya no interesa desfiguración de web. Ahora está interesado en robe información, cuentas financieras y los datos privados, que pueden usar para generar efectivo en el mercado abierto o para aprovechar un empresarial concreto, política o military posición. Aún más relativa a dichos atacantes con un objetivo financiero son los atacantes que viola redes para dañar infraestructura y de otras personas.

En la respuesta, las organizaciones a menudo implementar diversas soluciones de punto centran en defensa del perímetro de la empresa o el extremos buscando firmas de ataque conocidos. Estas soluciones suelen generar un gran volumen de alertas de baja fidelidad, que requieren un analista de seguridad evaluar e investigar. La mayoría de las organizaciones carecen de la hora y la experiencia necesarios para responder a estas alertas: ir tantos sin destinatario.  Mientras tanto, los atacantes evolución sus métodos para dañar muchos defensa basada en firmas y [adaptarse a entornos de nube](https://azure.microsoft.com/blog/detecting-threats-with-azure-security-center/). Nuevos enfoques son necesarios para identificar amenazas más rápidamente y agilizar la detección y la respuesta. 

## <a name="how-azure-security-center-detects-and-responds-to-threats"></a>Cómo el centro de seguridad de Azure detecta y responde a amenazas

Investigadores de seguridad de Microsoft están constantemente en busca de amenazas. Tienen acceso a un amplio conjunto de telemetría obtenida de presencia global de Microsoft en la nube y locales. Esta colección de gran alcance y diversa de conjuntos de datos permite a Microsoft detectar patrones de ataque nuevo y tendencias a través de sus productos en local de consumidor y empresa, así como sus servicios en línea. Como resultado, centro de seguridad pueden actualizar rápidamente sus algoritmos de detección como nuevas y cada vez más sofisticados ataques de lanzamiento de atacantes. Este enfoque le ayuda a mantener el ritmo con un entorno de amenaza de movimiento rápido. 

Detección de amenazas del centro de seguridad funciona automáticamente recopilar información de seguridad de los recursos de Azure, la red y soluciones de socios conectados. Analiza esta información, a menudo correlación información de varios orígenes, identificar amenazas. Se asignan prioridades a las alertas de seguridad en el centro de seguridad junto con recomendaciones sobre cómo solucionar la amenaza.

![Presentación y recopilación de datos del centro de seguridad](./media/security-center-detection-capabilities/security-center-detection-capabilities-fig1.png)

Centro de seguridad se emplea el análisis de seguridad avanzada, que van más allá de los enfoques basados en la firma. Avances en big data y [aprendizaje de máquina](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) tecnologías están aprovechar para evaluar eventos a través de la estructura de la nube todo: detección de amenazas que serían posible identificar utilizando enfoques manuales y las predicciones la evolución de ataques. Incluir el análisis de seguridad: 

- **Información sobre amenazas integrado**: busca conocidos actores malas aprovechando inteligencia amenaza global de productos de Microsoft y servicios, Digital delitos unidad (DCU) de Microsoft, el centro de respuesta de seguridad de Microsoft (MSRC) y fuentes externas.
- **Análisis de comportamiento**: aplica conocidos modelos para descubrir un comportamiento malintencionado. 
- **Detección de anomalías**: utiliza perfiles estadístico para crear una línea de base histórica. Alerta de las desviaciones de las referencias establecidas que se ajustan a un tipo de ataque posibles.


### <a name="threat-intelligence"></a>Información sobre amenazas
Microsoft tiene una gran cantidad de inteligencia amenaza global. Telemetría fluye en de varios orígenes, como Azure, Office 365, Microsoft CRM online, Microsoft Dynamics AX, outlook.com, MSN.com, la unidad de delitos Digital (DCU) de Microsoft y el centro de respuesta de seguridad de Microsoft (MSRC). Investigadores también reciban información de inteligencia de amenazas que se comparte entre proveedores de servicios de nube principales y se suscribe a amenaza inteligencia fuentes de terceros. Centro de seguridad de Azure puede usar esta información para alertarle de amenazas conocidos actores incorrecto. Algunos ejemplos:

- **Comunicaciones salientes a la dirección IP malintencionada**: el tráfico saliente a un botnet conocido o darknet es muy probable que indica que el recurso se ha comprometido y un intruso que intenta ejecutar comandos en esos datos sistema o exfiltrate. Centro de seguridad de Azure compara el tráfico de red a base de datos de amenazas globales de Microsoft y le avisa si detecta comunicación a una dirección IP malintencionada.

## <a name="behavioral-analytics"></a>Análisis de comportamiento

Análisis de comportamiento es una técnica que analiza y compara los datos a una colección de modelos conocidos. No obstante, estos modelos no son firmas simples. Se determinan a través de máquina complejos algoritmos que se aplican a grandes conjuntos de datos de aprendizaje. También se determinan mediante análisis cuidado de comportamientos malintencionados analistas expertos. Centro de seguridad de Azure puede usar el análisis de comportamiento para identificar recursos comprometidos en función de análisis de registros de máquina virtual, registros de dispositivos de red virtual, tela registros, volcados y otros orígenes. 

Además, hay correlación con otras señales para comprobar datos de una campaña generalizada complementarios. Esta correlación ayuda a identificar los eventos que son coherentes con indicadores establecidos de compromiso. Algunos ejemplos:

- **Ejecución de procesos Suspicious**: atacantes emplean varias técnicas para ejecutar software malintencionado sin detección. Por ejemplo, un intruso podría dar malware los mismos nombres de archivos de sistema legítimos pero coloque estos archivos en una ubicación alternativa, utilice un nombre que es muy similar a un archivo favorable o máscara la extensión del archivo es true. Centro de seguridad de modelos comportamientos de procesos y monitores procesan ejecuciones para detectar a valores atípicos como estos.  
- **Intentos de malware oculto y explotación**: malware sofisticado es capaz de eludir productos antimalware tradicional nunca escribir en el disco o componentes de software que se almacena en el disco de cifrado.  Sin embargo, como malware puede detectar mediante análisis de memoria, como el malware debe dejar trazas en la memoria para funcionar. Cuando se bloquea el software, una descarga de bloqueo captura una parte de la memoria en el momento del bloqueo.  Mediante el análisis de la memoria en la descarga de bloqueo, centro de seguridad de Azure puede detectar técnicas utilizadas para aprovechar la vulnerabilidad del software, tener acceso a datos confidenciales y conservar clandestinamente con en un equipo comprometido sin afectar al rendimiento de su equipo.
- **Lateral movimiento y reconocimiento interno**: para almacenar en una red y busque/recopilación valiosa de información confidencial, los atacantes suelen intentan desplazarse lateralmente desde el equipo comprometido a otras personas dentro de la misma red. Centro de seguridad supervisa las actividades de proceso e inicio de sesión para detectar intentos de expandir establecerse intruso dentro de la red, como comando remoto ejecución red sondeo y enumeración de cuenta.
- **Secuencias de comandos de PowerShell malintencionados**: se está usando PowerShell atacantes ejecutar código malintencionado en máquinas virtuales de destino para una gran variedad de efectos. Centro de seguridad inspecciona actividad de PowerShell para pruebas de actividad sospechosa. 
- **Saliente ataques**: intrusos suelen buscar recursos de la nube con el objetivo de utilizar estos recursos para preparar más ataques. Comprometido máquinas virtuales de Windows, por ejemplo, podría estar acostumbrado a lanzar ataques de fuerza bruta contra otros equipos virtuales, enviar correo no deseado o examinar los puertos abiertos y otros dispositivos de internet. Aplicando el aprendizaje para el tráfico de red, centro de seguridad puede detectar cuando las comunicaciones salientes superan la norma. En el caso de correo no deseado, el centro de seguridad también establece una correlación entre el tráfico de correo electrónico inusual con inteligencia de Office 365 para determinar si el correo es probable que malintencionadas o el resultado de una campaña de correo legítimos.  

### <a name="anomaly-detection"></a>Detección de anomalías

Centro de seguridad de Azure usa también detección de anomalías para identificar amenazas. A diferencia de análisis de comportamiento (que depende de patrones conocidos derivados de grandes conjuntos de datos), detección de anomalías más "personalizada" y se centra en previsiones específicos para las implementaciones. Se aplica el aprendizaje para determinar la actividad normal para las implementaciones y, a continuación, se generan reglas para definir las condiciones de valores atípicos que podrían representar un evento de seguridad. Aquí tenemos un ejemplo:

- **Entrada bruta de RDP/SSH forzar ataques**: su implementaciones pueden tener ocupados máquinas virtuales con una gran cantidad de inicios de sesión cada día y otras máquinas virtuales de Windows que tienen muy pocos o cualquier inicios de sesión. Centro de seguridad de Azure puede determinar la actividad de inicio de sesión de línea base para estas máquinas virtuales y usar de aprendizaje definir qué está fuera de la actividad de inicio de sesión normal. Si el número de inicios de sesión o la hora del día de los inicios de sesión o la ubicación desde la que se solicitan los inicios de sesión u otras características relacionadas con el inicio de sesión es muy distinta de la línea base, puede generar una alerta. De nuevo, el aprendizaje determina qué es lo importante.

## <a name="continuous-threat-intelligence-monitoring"></a>Supervisión de inteligencia de amenaza continuo

Centro de seguridad de Azure funciona investigación y datos ciencias equipos de seguridad que continuamente supervisión los cambios en el panorama de amenazas. Esto incluye las siguientes iniciativas:

- **Supervisión de inteligencia de amenaza**: amenaza inteligencia incluye mecanismos, indicadores, implicaciones y consejos útiles sobre nuevas amenazas o existente. Esta información se comparte en la Comunidad seguridad y Microsoft supervisa continuamente amenaza inteligencia fuentes de orígenes internos y externos.
- **Uso compartido de señal**: comparten y analizan perspectivas de equipos de seguridad de la nube local servicios, servidores y cliente extremo dispositivos de la cartera amplia de Microsoft. 
- **Especialistas en seguridad de Microsoft**: compromiso continuo con equipos a través de Microsoft que funcionan en campos de seguridad especializadas, como análisis y detección de ataques de web.
- **Ajuste de detección**: algoritmos se ejecutan en conjuntos de datos de clientes reales e investigadores de seguridad trabajan con los clientes para validar los resultados. True y false positivos se utilizan para refinar algoritmos de aprendizaje del equipo.

Estos esfuerzos combinados culminan en detecciones nuevas y mejoradas, que puede beneficiarse al instante: no hay ninguna acción para poder realizar.

## <a name="see-also"></a>Vea también
En este documento, ha aprendido a detección de centro de seguridad de Azure capacidades de funcionan. Para obtener más información sobre el centro de seguridad, consulte lo siguiente:

- [Centro de seguridad de Azure Guía de planeación y operaciones](security-center-planning-and-operations-guide.md)
- [Administrar y responder a las alertas de seguridad en el centro de seguridad de Azure](security-center-managing-and-responding-alerts.md)
- [Alertas de seguridad por tipo en el centro de seguridad de Azure](security-center-alerts-type.md)
- [Supervisión en el centro de seguridad de Azure de estado de seguridad](security-center-monitoring.md) : Obtenga información sobre cómo supervisar el estado de los recursos de Azure.
- [Supervisar las soluciones de asociados con el centro de seguridad de Azure](security-center-partner-solutions.md) : Obtenga información sobre cómo supervisar el estado de las soluciones de socios.
- [Preguntas más frecuentes sobre centro de seguridad de Azure](security-center-faq.md) : preguntas más frecuentes sobre el uso del servicio de búsqueda.
- [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/) : buscar entradas de blog sobre seguridad de Azure y cumplimiento.

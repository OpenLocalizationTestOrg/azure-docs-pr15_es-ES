<properties
   pageTitle="Seguridad de datos del centro de seguridad de Azure | Microsoft Azure"
   description="Este documento explica cómo administrar y protegido en el centro de seguridad de Azure."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/25/2016"
   ms.author="yurid"/>

# <a name="azure-security-center-data-security"></a>Seguridad de datos del centro de seguridad de Azure
Para ayudar a los clientes evitar, detectar y responder a amenazas, centro de seguridad de Azure recopila y procesa datos sobre los recursos de Azure, incluida la información de configuración, metadatos, registros de eventos, bloquee descargar archivos y mucho más. Asegúrese de compromisos seguros para proteger la privacidad y seguridad de datos. Microsoft se adhiere a las directrices de seguridad y cumplimiento estrictas: desde la codificación a un servicio de funcionamiento. 

En este artículo se explica cómo administrar y protegido en el centro de seguridad de Azure.

## <a name="data-sources"></a>Orígenes de datos

Centro de seguridad de Azure analizan los datos de los siguientes orígenes:

- Servicios de Azure: Lee información sobre la configuración de servicios de Azure ha implementado mediante la comunicación con el proveedor de recursos del servicio.
- Tráfico de red: Lecturas muestreadas los metadatos de tráfico de infraestructura de Microsoft, como origen o destino IP/puerto, tamaño de paquete de red y protocolo de red.
- Soluciones de socios: Recopila las alertas de seguridad de soluciones integradas de socios, como firewalls y soluciones contra el malware. Estos datos se almacenan en el almacenamiento de centro de seguridad de Azure, que se encuentra actualmente en los Estados Unidos.
- Sus máquinas virtuales: Centro de seguridad de Azure puede recopilar información de configuración e información sobre los eventos de seguridad, como los eventos de Windows y registros, registros IIS, mensajes de registro del sistema y archivos de descarga de bloqueo de los equipos virtuales con agentes de recopilación de datos de auditoría. Consulte la sección "Administrar la recopilación de datos" a continuación para obtener más información.  

Además, se almacena información sobre las alertas de seguridad, recomendaciones y estado de salud de seguridad en el almacenamiento de centro de seguridad de Azure, que se encuentra actualmente en los Estados Unidos. Esta información puede incluir información de configuración relacionada y eventos de seguridad recopilados desde su máquinas virtuales según sea necesario para proporcionarle la alerta de seguridad, la recomendación o el estado de seguridad.

## <a name="data-protection"></a>Protección de datos

**Separación de datos**: los datos se mantienen separadas de forma lógica en cada componente en todo el servicio. Etiquetado todos los datos por la organización. Este etiquetado continúa durante el ciclo de vida de datos y, a continuación, se aplica en cada nivel del servicio. Además, los datos recopilados desde sus máquinas virtuales se almacenan en sus cuentas de almacenamiento.

**Acceso a datos**: para proporcionar recomendaciones de seguridad e investigar posibles amenazas de seguridad, personal de Microsoft puede tener acceso a los datos recopilados o analizar Azure Services, incluidos los archivos de descarga de bloqueo. Archivos de descarga de bloqueo y eventos de creación de proceso sin querer pueden incluir datos del cliente o datos personales de sus máquinas virtuales. Se cumplen las [Condiciones de Microsoft Online Services](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) y la [Declaración de privacidad](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx), qué estado que Microsoft no puede usar los datos del cliente o derivan información para ningún propósito comercial publicidad o similar. Sólo usamos datos del cliente según sea necesario para proporcionar servicios de Azure, incluidos fines compatibles con proporcionar estos servicios. Conservar todos los derechos de los datos del cliente.

**Uso de datos**: Microsoft usa patrones y sobre amenazas aparecer en varios inquilinos para mejorar nuestras capacidades de detección y prevención; Para hacerlo conforme a los compromisos de privacidad que se describe en la [Declaración de privacidad](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx).

**Ubicación de datos**: no se especifica una cuenta de almacenamiento para cada región donde se están ejecutando máquinas virtuales de Windows. Esto le permite almacenar datos en la misma región como la máquina virtual desde el que se recopilan los datos. Estos datos, incluidos los archivos de descarga de bloqueo, se almacenarán en su cuenta de almacenamiento persistente. El servicio también almacena información acerca de las alertas de seguridad, incluidas las alertas de soluciones integradas de socios, recomendaciones y estado de salud de seguridad en el almacenamiento de centro de seguridad de Azure, que se encuentra actualmente en los Estados Unidos.

## <a name="managing-data-collection-from-virtual-machines"></a>Administrar la recopilación de datos desde máquinas virtuales de Windows

Al elegir habilitar el centro de seguridad de Azure, recopilación de datos se activa para cada uno de sus suscripciones. Puede desactivar la recopilación de datos en la sección "Directiva de seguridad" del panel del centro de seguridad de Azure. Cuando está activada la recopilación de datos, el agente de supervisión de Azure en todos los archivos del centro de seguridad de Azure disposiciones compatible máquinas virtuales y los nuevos que se crean. La extensión de supervisión de seguridad de Azure busca relacionados con la seguridad varias configuraciones y eventos rastrea en [Seguimiento de eventos de Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW). Además, el sistema operativo provocará eventos de registro de eventos durante el curso de la ejecución del equipo. Ejemplos de este tipo de datos son: tipo de sistema operativo y la versión, operativo ejecuta procesos, nombre del equipo, direcciones IP, los registros del sistema (registros de eventos de Windows), se registran en usuario y el identificador de inquilinos. El agente de supervisión de Azure lee las entradas de registro de eventos y realiza un seguimiento de ETW y los copia a su cuenta de almacenamiento para el análisis. 

Se especifica una cuenta de almacenamiento para cada región en la que tiene máquinas virtuales que ejecutan, donde datos recopilan desde máquinas virtuales que se almacena la misma región. Esto facilita la mantener datos en la misma área geográfica con fines de soberanía de datos y privacidad. Puede configurar cuentas de almacenamiento para cada región en la sección "Directiva de seguridad" del panel del centro de seguridad de Azure.

El agente de supervisión de Azure, también se copiarán los archivos de descarga de bloqueo a su cuenta de almacenamiento.  Centro de seguridad de Azure efímeros copias de sus archivos de descarga de bloqueo de recopila y analiza para pruebas de intentos de explotación y compromisos correcta.  Centro de seguridad de Azure realiza este análisis dentro de la misma región geográfica como la cuenta de almacenamiento y elimina las copias efímeras cuando análisis esté completa.

Puede deshabilitar la recopilación de datos de máquinas virtuales en cualquier momento, por lo que se quitará a cualquier agentes supervisión previamente instalado por el centro de seguridad de Azure.


## <a name="see-also"></a>Vea también

En este documento, ha aprendido cómo administra y protegido en el centro de seguridad de Azure. Para obtener más información sobre el centro de seguridad de Azure, consulte:

- [Guía de operaciones y planificación de centro de seguridad de Azure](security-center-planning-and-operations-guide.md) : Obtenga información sobre cómo planificar y comprender las consideraciones de diseño para la adopción de centro de seguridad de Azure.
- [Supervisión en el centro de seguridad de Azure de estado de seguridad](security-center-monitoring.md) : Obtenga información sobre cómo supervisar el estado de los recursos de Azure
- [Administrar y responder a las alertas de seguridad en el centro de seguridad de Azure](security-center-managing-and-responding-alerts.md) : Obtenga información sobre cómo administrar y responder a las alertas de seguridad
- [Supervisar las soluciones de asociados con el centro de seguridad de Azure](security-center-partner-solutions.md) : Obtenga información sobre cómo supervisar el estado de las soluciones de socios.
- [Preguntas más frecuentes sobre centro de seguridad de Azure](security-center-faq.md) : preguntas más frecuentes sobre el uso del servicio de búsqueda
- [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/) : buscar entradas de blog sobre seguridad de Azure y cumplimiento

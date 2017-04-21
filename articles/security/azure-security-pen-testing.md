<properties
   pageTitle="Pruebas de penetración | Microsoft Azure"
   description="El artículo proporciona una introducción a la de pruebas de penetración proceso (pentest) y cómo realizar pentest frente a las aplicaciones que se ejecutan en infraestructura de Azure."
   services="security"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/25/2016"
   ms.author="yurid"/>

# <a name="pen-testing"></a>Pruebas de penetración

Una de las ventajas de usar Microsoft Azure para pruebas de aplicaciones y la implementación es que no es necesario crear una infraestructura local para desarrollar, probar e implementar aplicaciones. Toda la infraestructura se ocupa de los servicios de plataforma de Microsoft Azure. No tiene que preocuparse de aprovisionamiento, adquirir y "instalación en rack y apilar" su propio hardware local.

Esto es genial, pero todavía necesita para asegurarse de que realiza la seguridad normal diligencia. Una de las cosas que debe hacer es penetración probar las aplicaciones que se implementa en Azure.

Ya puede saber que Microsoft realiza [pruebas de penetración de nuestro entorno de Azure](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e). Esto nos ayuda a mejorar nuestra plataforma y le orientará nuestras acciones en términos de mejora de controles de seguridad, presentación de los nuevos controles de seguridad y mejorar nuestros procesos de seguridad.

Nos no pluma probar la aplicación para usted, pero comprendemos que se desea y necesita realizar pruebas en sus propias aplicaciones de pluma. Que es una buena, porque cuando mejorar la seguridad de sus aplicaciones, ayuda a proteger todo el ecosistema de Azure.

Cuando el lápiz probar las aplicaciones, podría aspecto ataque nos. Nos [supervisar continuamente](http://blogs.msdn.com/b/azuresecurity/archive/2015/07/05/best-practices-to-protect-your-azure-deployment-against-cloud-drive-by-attacks.aspx) para enfrentarse tramas y se iniciará un proceso de respuesta a incidencias si es necesario. No ayudarle y no ayudar a si se desencadena una respuesta a incidencias debido a su vencimiento pluma diligencia pruebas.

¿Qué hacer?

Cuando esté listo para pluma probar las aplicaciones hospedadas en Azure, debe comunicarnos. Una vez que se sabe que va a realizar pruebas específicas, nos accidentalmente no apagar (como el bloqueo de la dirección IP que está probando desde), como las pruebas se ajustan a la Azure pluma pruebas términos y condiciones.
Puede realizar las pruebas estándar incluyen:

- Pruebas en los extremos para descubrir la [10 vulnerabilidad principales de proyecto de seguridad de aplicaciones de Web abrir (OWASP)](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
- [Las pruebas](https://blogs.microsoft.com/cybertrust/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/) de los extremos
- [Análisis de puerto](https://en.wikipedia.org/wiki/Port_scanner) de los extremos

Un tipo de prueba que no puede realizar es cualquier tipo de ataque de [Rechazo de servicio (DoS)](https://en.wikipedia.org/wiki/Denial-of-service_attack) . Esto incluye iniciar un ataque DoS propio o realizar pruebas relacionadas que pueden determinar, demostrar o simular cualquier tipo de ataque DoS.

¿Está listo empezar a trabajar con el lápiz está probando aplicaciones alojadas en Microsoft Azure? Si es así, cabezal en sobre la [Visión general de pruebas de penetración](https://security-forms.azure.com/penetration-testing/terms) de página () y haga clic en el botón Crear una solicitud de prueba en la parte inferior de la página. También encontrará más información sobre el lápiz pruebas términos y condiciones y vínculos útiles en cómo puede informar de problemas de seguridad relacionados con Azure o cualquier otro servicio de Microsoft.

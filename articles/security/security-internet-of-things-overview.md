<properties
   pageTitle="Información general de seguridad de Internet de cosas | Microsoft Azure"
   description=" Azure internet de servicios de cosas (IoT) ofrecen una amplia gama de capacidades. Este artículo le ayudará a comprender cómo proteger sus soluciones IoT en Azure. "
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="MBaldwin"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/09/2016"
   ms.author="terrylan"/>

# <a name="internet-of-things-security-overview"></a>Información general de seguridad de Internet de cosas

Azure internet de servicios de cosas (IoT) ofrecen una amplia gama de capacidades. Estos servicios de nivel empresarial le permiten:

- Recopilar datos de dispositivos
- Analizar secuencias de datos en movimiento
- Almacenar y grandes conjuntos de datos de la consulta
- Visualizar datos históricos y en tiempo real
- Integrar con sistemas internos

Ofrecer estas capacidades, los paquetes de Azure IoT Suite juntos varios servicios Azure con extensiones personalizadas como soluciones preconfiguradas. Estas soluciones preconfiguradas son implementaciones bases de los modelos de solución IoT comunes que ayudan a reducir el tiempo que seguir para ofrecer su soluciones IoT. El Kit de desarrollo de software de IoT puede personalizar y ampliar estas soluciones para satisfacer sus propios requisitos. También puede usar estas soluciones como ejemplos o plantillas al desarrollar soluciones de IoT nuevas.

El conjunto de Azure IoT es una solución eficaz para sus necesidades IoT. Sin embargo, es de importancia upmost que las soluciones IoT están diseñadas con la seguridad en mente desde el principio. Debido al gran número de dispositivos IoT, cualquier incidente de seguridad rápidamente puede convertirse en un evento generalizado con consecuencias significativas.

Para ayudarle a entender cómo proteger sus soluciones IoT, tenemos la siguiente información.

## <a name="security-architecture"></a>Arquitectura de seguridad

Al diseñar un sistema, es importante conocer las posibles amenazas a ese sistema y agregar defensa adecuada según corresponda, tal como se ha diseñado y diseñado el sistema. Es especialmente importante diseñar el producto desde el principio con la seguridad en cuenta porque están comprender cómo un intruso podrían peligro un sistema le ayuda a realizar reducciones que corresponda en lugar desde el principio.

Puede obtener la información acerca de la arquitectura de seguridad IoT lea la [Arquitectura de seguridad de Internet de las cosas](../iot-suite/iot-security-architecture.md).

En este artículo se trata los siguientes temas:

- [La seguridad comienza con un modelo de amenazas](../iot-suite/iot-security-architecture.md#security-starts-with-a-threat-model)
- [Seguridad en IoT](../iot-suite/iot-security-architecture.md#security-in-iot)
- [La arquitectura de referencia de Azure IoT amenazas.](../iot-suite/iot-security-architecture.md#threat-modeling-the-azure-iot-reference-architecture)

## <a name="security-from-the-ground-up"></a>Seguridad conceptos básicos

La IoT presenta desafíos únicos de seguridad, privacidad y cumplimiento a en todo el mundo. A diferencia de tecnología de cibernético tradicional donde estos problemas giran en torno a software y cómo se implementa, IoT preocupaciones lo que sucede cuando universos físicos y la cibernético convergen. Proteger soluciones IoT requiere garantizar segura de aprovisionamiento de dispositivos, conectividad segura entre estos dispositivos y la nube y protección de datos seguro en la nube durante el procesamiento y almacenamiento. Sin embargo, se trabaja con esta funcionalidad, son dispositivos de recursos restringidos, distribución geográfica de implementaciones y una gran cantidad de dispositivos dentro de una solución.

Puede aprender a controlar la seguridad en estas áreas mediante la lectura de [seguridad de Internet de cosas conceptos básicos](../iot-suite/securing-iot-ground-up.md).

El artículo trata los siguientes temas:

- [Infraestructura segura conceptos básicos](../iot-suite/securing-iot-ground-up.md#secure-infrastructure-from-the-ground-up)
- [Microsoft Azure: infraestructura IoT segura para su empresa](../iot-suite/securing-iot-ground-up.md#microsoft-azure---secure-iot-infrastructure-for-your-business)

## <a name="best-practices"></a>Prácticas recomendadas

Proteger una infraestructura IoT requiere una estrategia de seguridad en profundidad rigurosa. A partir de proteger los datos en la nube, para proteger la integridad de datos mientras está en tránsito sobre internet público y a continuación, proporcionar la capacidad de forma segura aprovisionar dispositivos, cada capa crea mayor garantía de seguridad en toda la infraestructura.

Puede obtener acerca de la seguridad de Internet de cosas prácticas recomendadas lea [prácticas recomendadas de seguridad de Internet de las cosas](../iot-suite/iot-security-best-practices.md).

El artículo trata los siguientes temas:

- [Fabricante del hardware IoT/integración](../iot-suite/iot-security-best-practices.md#iot-hardware-manufacturerintegrator)
- [Programador de solución IoT](../iot-suite/iot-security-best-practices.md#iot-solution-developer)
- [Implementador de solución IoT](../iot-suite/iot-security-best-practices.md#iot-solution-deployer)
- [Operador de solución IoT](../iot-suite/iot-security-best-practices.md#iot-solution-operator)

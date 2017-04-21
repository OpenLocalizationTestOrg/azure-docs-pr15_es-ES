<properties 
    pageTitle="Implementación de Azure compromiso Mobile aplicación de juegos"
    description="Escenario de aplicación de juegos para implementar el compromiso de móvil de Azure" 
    services="mobile-engagement" 
    documentationCenter="mobile" 
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

#<a name="implement-mobile-engagement-with-gaming-app"></a>Implementar móvil compromiso con la aplicación de juegos

## <a name="overview"></a>Información general

Un inicio de juegos inicia una nueva aplicación de juegos de role play y estrategia de pesca según. El juego ha sido marcha para 6 meses. Este juego es un gran éxito y tiene millones de descargas y la retención es muy alta en comparación con otras aplicaciones de juegos de inicio. En la reunión de revisión trimestral, las partes interesadas acepta que necesitan aumentar los ingresos promedio por usuario (ARPU). Premium del juego paquetes está disponibles como ofertas especiales. Estos paquetes juego permiten a los usuarios actualizar la apariencia y rendimiento de sus líneas de pesca y cebos para o enfrenta en el juego. Sin embargo, las ventas de paquete son muy bajas. Así que decide en primer lugar analizar la experiencia del cliente con una herramienta de análisis y, a continuación, para desarrollar un compromiso programa para aumentar el uso de ventas avanzadas segmentación.

Basada en el [Compromiso de Azure Mobile - Guía de introducción con las mejores prácticas](mobile-engagement-getting-started-best-practices.md) crean una estrategia de contratación.

##<a name="objectives-and-kpis"></a>Objetivos y KPI

Cumplir con las partes interesadas clave para el juego. Acordar un objetivo principal - para aumentar las ventas de paquete premium por 15%. Crean indicadores clave de rendimiento (KPI) empresarial a medida y unidad este objetivo

* ¿En qué nivel del juego se adquieren estos paquetes?
* ¿Qué es el ingresos por usuario, por sesión, semana y mes?
* ¿Cuáles son los tipos de compra favoritas?

Parte 1 de la [Guía de introducción](mobile-engagement-getting-started-best-practices.md) explica cómo definir los objetivos y KPI. 

Con los KPI de negocio definido ahora, el administrador del producto Mobile crea KPI de contratación para determinar la retención y tendencias de usuario nuevo.

* Supervisar la retención y usar en los siguientes intervalos: diariamente, cada 2 días, semanales, mensuales y cada tres meses
* Cuenta de usuario activo
* La clasificación de aplicación de la tienda

Según las recomendaciones del equipo de TI, se agregaron los siguientes KPI técnicos para responder a las preguntas siguientes:

* ¿Qué es la ruta de acceso de usuario (se visite la página, ¿cuánto tiempo que los usuarios dedican a él)
* Número de bloqueos o errores que puedan surgir por sesión
* ¿Qué versiones de sistema operativo ejecutan Mis usuarios?
* ¿Qué es el tamaño medio de la pantalla para Mis usuarios?
* ¿Qué tipo de conexión a internet tienen Mis usuarios?

Para cada KPI el jefe de producto Mobile especifica los datos que necesita y donde se encuentra en su libro de estrategias.

## <a name="engagement-program-and-integration"></a>Programa de contratación y la integración

Antes de generar un programa de contratación avanzadas, Director de proyecto móvil cargo del proyecto debería tener un profundo conocimiento de cómo y cuándo se consumen productos por los usuarios.

Después de tres meses, el Director de proyecto móvil ha recopilado datos suficientes para mejorar sus ventas de notificación de inserción de la aplicación. Descubre:

* La primera compra generalmente sucede en el nivel de 14. 90% de los casos, la compra es nuevas armas legendario para $3.
* 80% de los casos, los usuarios que han realizado una compra, continúe con el producto y realizar más compras.
* Los usuarios que han pasado el nivel de 20, iniciar gastar más de 10 dólares por semana.
* Los usuarios suelen comprar paquetes de premium en el nivel de 16, 24 y 32.

Gracias a este análisis el Director de proyecto móvil decide crear inserción específicos secuencias de notificación para aumentar las ventas de aplicación. El administrador crea tres secuencias de inserción que llama: Bienvenido programa, el programa de ventas y el programa inactivo. Para obtener más información, consulte las [guías](https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks)
    ![][1]

<!--Image references-->

[1]: ./media/mobile-engagement-game-scenario/notification-scenario.png

<!--Link references-->

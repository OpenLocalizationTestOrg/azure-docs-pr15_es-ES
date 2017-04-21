<properties 
    pageTitle="Implementación de Azure Mobile compromiso aplicación multimedia"
    description="Escenario de aplicación de medios para implementar el compromiso de móvil de Azure" 
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

#<a name="implement-mobile-engagement-with-media-app"></a>Implementar móvil compromiso con la aplicación de medios

## <a name="overview"></a>Información general

Juan es un jefe de proyecto móvil para una empresa grande multimedia. Recientemente, inicia una nueva aplicación que tiene un recuento de descarga muy alta. Ha llegado a sus objetivos para su descarga, pero todavía su Investment(ROI) devuelto en por usuario no cumple sus requisitos. 

John ya ha identificado por qué su ROI es demasiado bajo. Los usuarios con frecuencia dejar de usar su aplicación sólo 2 semanas y la mayoría de ellas nunca que vuelva. Desea aumentar la retención de su aplicación.

Después de algunos inicial pruebas, que ha aprendido cuando contrata a sus usuarios con las notificaciones de inserción, suelen seguir utilizando su aplicación. También los usuarios que estaban inactivos a menudo volverá a la aplicación de la función de las notificaciones que envía. John decide invertir en algún tipo de compromiso programa para su aplicación que utiliza la identificación avanzada con las notificaciones de inserción.

John recientemente ha leído el [Compromiso de Azure Mobile - Guía de introducción con las mejores prácticas](mobile-engagement-getting-started-best-practices.md) y ha decidido implementar las recomendaciones de la guía.

##<a name="objectives-and-kpis"></a>Objetivos y KPI

Cumplir con las partes interesadas clave para la aplicación de John. Empresa se genera desde anuncios como usuarios consuman su media. Aumentar contenido consumido por usuario, John aumenta sus ingresos. Acordar un objetivo principal: para aumentar las ventas de anuncios en un 25%. Crean indicadores clave de rendimiento (KPI) empresarial a medida y unidad este objetivo

* Número de anuncios hizo clic por usuario
* El número de páginas de artículo visitado (por usuario / por sesión / semana / mes...)
* ¿Qué categorías favoritas

En función de la reunión de John con los participantes clave ha definido los KPI de su empresa. Sigue a parte 1 [Azure Mobile compromiso - Guía de introducción con las mejores prácticas](mobile-engagement-getting-started-best-practices.md). 

A continuación, el administrador crea los siguientes KPI de contratación para asegurarse de que se ha alcanzado objetivos:

* Supervisar la retención a través de los siguientes intervalos: diaria, semanal, quincenal y mensuales.
* Recuentos de usuarios activos
* Almacena la clasificación de aplicación de la aplicación

Según las recomendaciones del equipo de TI, se agregaron los siguientes KPI técnica para responder a las preguntas siguientes:

* ¿Qué es la ruta de acceso de usuario (página que se visita, ¿cuántos usuarios de tiempo dedican a él)
* ¿Número de bloqueos o errores que puedan surgir por sesión?
* ¿Qué versiones de sistema operativo ejecutan Mis usuarios?
* ¿Qué es el tamaño medio de la pantalla para Mis usuarios?
* ¿Qué tipo de conexiones a internet tienen Mis usuarios?

Para cada KPI clasifica los datos necesarios y los registros en la ubicación correcta de su guía.

## <a name="engagement-program-and-integration"></a>Programa de contratación y la integración

Ahora que John ha terminado de definir su KPI, empieza la fase de su estrategia de contratación definiendo 4 programas de contratación y sus objetivos:    ![][1]

A continuación, John va más profundo, que detalla las notificaciones de inserción para cada programa. Notificación de inserción se definen por cinco elementos:

1. Objetivo: ¿cuál es el objetivo de la notificación
2. ¿Cómo se llega el objetivo
3. Destino: quién recibirá la notificación?
4. Content: ¿Qué es el texto y el formato de la notificación (en App/Out de aplicación)
5. Cuándo: ¿cuál es el mejor momento para enviar esta notificación de inserción

    ![][2]

Para obtener más información, consulte las [guías](https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks).

Según el elemento 2 de las notas del producto John usa la sección de destino para definir qué datos tiene para recopilar y escribe su etiqueta planear conjuntamente con el equipo de TI para implementar la solución. Después de una semana de implementación y las pruebas de aceptación de usuario, John finalmente puede iniciar los programas.

##<a name="program-results"></a>Resultados del programa

4 meses posterior, John revisa prestaciones de programas. El programa de bienvenida y semanal son lograr sus objetivos. Disminuye el número de usuarios con solo una sesión, se utilizan más características de la aplicación y el número de conexiones por semana ha duplicado.

El **Programa inactivo** ayuda John comprender las tendencias de usuario. Parece que 15% de los usuarios inactivos que vuelva a la aplicación. Sin embargo la mayoría de ellas no estar activada más de 1 mes. John prevé una optimización posible de esta secuencia con notificaciones adicionales y expandir sus opciones de contenido.

El **Programa Descubra** no funciona bien. Aumenta cruzada pero no suficiente alcance sus objetivos de ventas. John identifica que no tenga datos suficientes para que relevantes identificación y proponer contenido adecuado. Detiene este programa y se centra en Enviar "notificaciones de inserción editorial" con Azure Mobile compromiso. Sus periodistas ya tienen una solución CMS para enviar notificaciones de inserción y no desea cambiar.

John decide usar la API de llegar a que es una API de REST de HTTP que permite administrar las campañas de alcance sin tener que utilizar interfaz AZME Web. Con este enfoque John puede recopilar los datos que necesita y permite sus autores para seguir usando la solución CMS.

Para asegurarse de que dicha característica funciona correctamente, John le pide el equipo de TI estar atento en los siguientes puntos:

1. **Sistemas operativos** : tienen sus propias reglas para administrar las notificaciones de inserción, por lo que John decide que todos los casos de la lista y comprueba si las API de controlen.
Por ejemplo: Sistema de inserción Android permite panorama general que no es el caso con iOS.

2. **Período de tiempo**: Jesús desea una API, que establece el período de tiempo y un final en campañas. Quiere conservar a los usuarios desde cualquier notificación interrupciones masivo.

3. **Categorías**: equipo Marketing prepara la plantilla para cada tipo de alerta. Juan solicita el equipo de TI para establecer categorías dentro de la API.

Después de algunas pruebas John está satisfecho. Gracias a esta API periodistas aún pueden enviar notificaciones de inserción con sus CMS y compromiso de Azure Mobile recopila todos los datos de comportamiento para ellos

Después de estos 4 primero meses, resultados reflejan una buena general rendimiento y da la confianza para Juan y su sala, retorno de la inversión por usuario aumenta por 15% y ventas móviles representan 17,5% del total de ventas, un aumento de 7,5% en sólo cuatro meses.

<!--Image references-->
[1]: ./media/mobile-engagement-media-scenario/engagement-strategy.png
[2]: ./media/mobile-engagement-media-scenario/push-scenarios.png

<!--Link references-->
[Media Playbook link]: https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks

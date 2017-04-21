<properties
   pageTitle="Probar la oferta de la plantilla de solución de Marketplace | Microsoft Azure"
   description="Comprender cómo probar la oferta de la plantilla de solución de Azure Marketplace."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/04/2015"
   ms.author="hascipio; v-divte" />

# <a name="test-your-solution-template-offer-in-staging"></a>Probar la oferta de la plantilla de solución de ensayo
Ensayo significa implementar la oferta en privadas "recinto" donde puede probar y comprobar su funcionalidad antes de enviar a producción. Aparece la oferta de ensayo tal como lo haría para un cliente que ha implementado. Su oferta debe estar certificada enviarse a en prueba.

Después de que se traslada la oferta, puede ver y probar la oferta en el [Portal de Azure](https://portal.azure.com/).

Siga los pasos siguientes para insertar la oferta de prueba y probar en el [Portal de Azure](https://portal.azure.com/):

1.  Vaya al [Portal de publicación](https://publish.windowsazure.com) > ficha**Plantillas de solución** > su oferta > **Publicar** > **ensayo de inserción**.
2.  Proporcionar la lista de suscripciones de Azure que usará para obtener una vista previa y probar su oferta.
3.  Inicie sesión en el portal de vista previa de Azure mediante el identificador de la suscripción que utilizó en el paso anterior.
4.  Llevar a cabo redondear al menos una de las pruebas en el portal de vista previa de Azure en los puntos que se indican a continuación:
  - Asegúrese de que contenido de marketing aparece correctamente en Azure Marketplace.
  - Llevar a cabo la implementación de la topología.
  - Realizar pruebas de rendimiento y esfuerzo.
  - Asegúrese de que la topología cumple con los procedimientos recomendados.

## <a name="next-steps"></a>Pasos siguientes
Si está satisfecho con los resultados, puede continuar con la fase de publicación de oferta final, el **paso 4**: [implementar la oferta al catálogo de soluciones](marketplace-publishing-push-to-production.md). En caso contrario, realice cambios en su oferta y solicitud de certificación de nuevo.

> [AZURE.NOTE] Para los cambios de contenido de marketing, no se requiere una certificación.

Consulte [Introducción: cómo publicar una oferta en Azure Marketplace](marketplace-publishing-getting-started.md) para obtener una guía para todas las tareas de publisher.

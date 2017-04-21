<properties
   pageTitle="Guía para crear un servicio de datos para el catálogo de soluciones | Microsoft Azure"
   description="Instrucciones detalladas sobre cómo crear, certificar e implementar un servicio de datos para comprar en Azure Marketplace."
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
      ms.date="08/26/2016"
      ms.author="hascipio; avikova" />

# <a name="data-service-publishing-guide-for-the-azure-marketplace"></a>Servicio de datos de publicación de la Guía de Azure Marketplace

>[AZURE.IMPORTANT] **En este momento estamos ya no integrado cualquier nuevas publicaciones de servicio de datos. No se obtener aprobado dataservices nueva lista.** Si tiene una aplicación de empresa SaaS que desea publicar en AppSource puede encontrar más información [aquí](https://appsource.microsoft.com/partners). Si tiene un aplicaciones IaaS o servicio de programador que desea publicar en Azure Marketplace puede encontrar más información [aquí](https://azure.microsoft.com/marketplace/programs/certified/).

Después de completar el paso 1, [registro y la creación de cuentas](marketplace-publishing-accounts-creation-registration.md), se le han guiado a través de los [Requisitos técnicos](marketplace-publishing-data-service-creation-prerequisites.md) de una oferta de servicio de datos y [General técnica](marketplace-publishing-pre-requisites.md) en Azure Marketplace. Ahora le guiaremos a través de los pasos para crear una oferta de servicio de datos en el [Portal de publicación] [ link-pubportal] de Azure Marketplace.

## <a name="1---login-to-the-publishing-portal"></a>1. inicio de sesión a la publicación en el Portal.

Vaya a [https://publish.windowsazure.com](https://publish.windowsazure.com. )

**Para primer inicio de sesión al Portal de publicación, use la misma cuenta con la que se registró perfil de vendedor de la empresa en el Centro para desarrolladores.**  (Más adelante puede agregar a cualquier empleado de su empresa como un administrador de co en el Portal de publicación).

Haga clic en el mosaico de **publicar los servicios de datos** si este es el primer inicio de sesión en el portal de publicación.

## <a name="2---choose-data-services-in-the-navigation-menu-on-the-left-side"></a>2. elija **Servicios de datos** en el menú de navegación en el lado izquierdo.

  ![dibujo](media/marketplace-publishing-data-service-creation/pubportal-main-nav.png)

## <a name="3---create-a-new-data-service"></a>3. crear un nuevo servicio de datos

Rellene el título para la oferta de servicio de datos nueva y haga clic en "+" a la derecha.

  ![dibujo](media/marketplace-publishing-data-service-creation/step-3.png)

## <a name="4---review-the-sub-menu-under-the-newly-created-data-service-in-the-navigation-menu"></a>4. revisar el submenú en el servicio de datos recién creado en el menú de navegación.

Haga clic en la pestaña **tutorial** y revisar todos los pasos necesarios para publicar correctamente el servicio de datos de Azure Marketplace.

> [AZURE.TIP] Siempre puede haga clic en los vínculos de la página "Tutorial" o use las pestañas en el menú en el lado izquierdo de la oferta de servicio de datos.

## <a name="5---create-a-new-plan"></a>5. crear un nuevo Plan.

### <a name="offers-plans-transactions"></a>Ofrece planes, transacciones.

Cada oferta puede tener varios planes, pero debe tener al menos uno (1) Plan. Cuando los usuarios finales suscripción a su oferta se suscriben por uno del Plan de la oferta. Cada plan define cómo los usuarios finales podrán usar el servicio.

Azure Marketplace admite actualmente transacción de suscripción mensual según modelo solo para los servicios de datos, es decir, los usuarios finales pagarán cuota mensual según el precio del plan específico que está suscrito a y podrán consumir cada número de mes de transacción definida por el plan.

Cada transacción que normalmente se define como el número de registros que devolverá el servicio de datos en función de la consulta enviada al servicio. El valor predeterminado es 100. Número de transacciones que se devuelven a cada consulta será el número de registros divididos por 100 y redondeado hacia arriba al entero más cercano.

Es responsable de la capa de servicio de Azure Marketplace para supervisar (medidor) número de transacciones consumido por cada consulta.

> [AZURE.IMPORTANT] Los usuarios finales que ha alcanzado el límite de transacción durante el mes se bloqueará continuar utilizar el servicio hasta el final de su ciclo de suscripción mensual.

> El plan o uno de los planes puede (pero no debe) incluir número ilimitado de transacciones.

### <a name="create-a-plan"></a>Crear un plan.
1. Haga clic en **"+"** junto a la opción "Agregar un nuevo plan".

2. Elija una de las opciones: uso **ilimitado** o **limitado** para este plan.  Si limitado a continuación, proporcione el número de transacción el plan le permitirá utilizar en un mes.

    ![dibujo](media/marketplace-publishing-data-service-creation/step-5.1.png)  

    Portal de publicación también sugerirá "Plan identificador", que se usa para comunicar a los usuarios finales el nombre del plan de la interfaz de usuario y también se usa el servicio de mercado para identificar el Plan. Puede cambiar el valor de "identificador planear" Si lo desea.

    > [AZURE.NOTE] "Plan identificador" debe ser único dentro del ámbito de cada oferta. Después de la primera publicación en producción y no pueda cambiar este identificador, se bloqueará como muchos otros identificadores utilizan el identificador del Plan de Portal de publicación.

3. Haga clic en Aceptar de su elección.

4. A continuación, se le solicitará algunas preguntas adicionales sobre su Plan recién creado.

    ![dibujo](media/marketplace-publishing-data-service-creation/step-5.2.png)


|Pregunta|Importancia|
|----|----|
|**¿Este Plan es gratuita y está disponible en todo el mundo?**|Puede crear un plan de completamente libre de-cargo. Si es el único plan para esta oferta: significa que está publicando "Oferta gratuita" en el catálogo de soluciones. Si es solo para una (de pocos) Plan, la TI le ofrece una opción para ofrecer a los usuarios finales para obtener más información sobre el servicio con un pequeño número de transacciones por mes.  Si la respuesta es "Sí", no se le solicitará más preguntas.|

> [AZURE.NOTE] Los usuarios finales siempre puede actualizar a los planes de pagados.

|Pregunta|Importancia|
|----|----|
|**¿Es la versión de prueba gratuita disponible?**|Puede elegir entre "Sin prueba" en absoluto o dar una opción para usar su Plan para "Un mes". Editores como usar esta opción para proporcionar a los usuarios finales la posibilidad de comprender las ventajas de la oferta de forma gratuita para un mes.|

> [AZURE.IMPORTANT] Los usuarios finales solo podrá adquirir una prueba gratuita si han establecido instrumento de pago, por ejemplo, tarjeta de crédito, contrato enterprise.

> Después de la versión de prueba gratuita de un mes, Azure Marketplace iniciará cobrar a los clientes el precio en la fecha de la suscripción, a menos que el cliente iniciada la cancelación de suscripción. No se proporcionará ninguna notificación especial para los usuarios finales.

|Pregunta|Importancia|
|----|----|
|**¿Este plan requiere un código de promoción para comprar?**| Los editores tienen una opción para limitar el acceso a sus planes de servicio al proporcionar un código especial, denominado "A Promocode" a clientes específicos. Solo los usuarios finales que tendrán este Promocode podrán suscribirse al Plan. Si elige "No", a continuación, acepta que todos los usuarios de la región donde la oferta está disponible (consulte la [Guía de contenido de Marketing del catálogo de soluciones](marketplace-publishing-push-to-staging.md) para obtener más detalles) podrán suscribirse a este plan. No se le solicitará más preguntas.|
|**¿Ocultar también este plan de cualquier persona que no tiene un código de promoción válido?**|Si la respuesta a la pregunta anterior es "Sí" el publicador tiene una opción para eliminar completamente este plan aparezca en la interfaz de usuario del catálogo de soluciones. Significa, los clientes no verán este plan de la página de detalles de la oferta. Los usuarios finales que recibirán un promocode para comprar, podrá suscribirse a ella usando este promocode.|

## <a name="6---create-your-marketplace-marketing-content"></a>6. crear el catálogo de soluciones de contenido de marketing
Para saber cómo proporcionar la información necesaria en las fichas de **Marketing, precios, soporte técnico y categorías** , visite [Guía de contenido de catálogo de soluciones de Marketing](marketplace-publishing-push-to-staging.md) que es común para todos los artefactos publicados en Azure Marketplace.  

## <a name="7---connect-your-offer-to-your-service-sql-azure-based-or-web-service-based"></a>7. Conecte su oferta en el servicio (en función de SQL Azure o servicio Web).

Haga clic en el menú **Servicios de datos** .

En la parte superior de la página, se le pedirá que proporcione de la oferta **Namespace**.  

  ![dibujo](media/marketplace-publishing-data-service-creation/step-7.png)

La debajo de pregunta definirá cómo será el publicador oferta exponer recién creado a Azure Marketplace. (Para obtener más detalles, consulte la [Guía de requisitos previos técnica de servicios de datos](marketplace-publishing-data-service-creation-prerequisites.md)).

  ![dibujo](media/marketplace-publishing-data-service-creation/step-7.2.png)

**El servicio de base de datos de publicación**

Haga clic en **base de datos**. Aparecerá la página siguiente:

  ![dibujo](media/marketplace-publishing-data-service-creation/step-7.3.png)

Para crear una asignación de CSDL para el conjunto de datos en función de la base de datos de SQL Azure:

  ![dibujo](media/marketplace-publishing-data-service-creation/step-7.4.png)

A continuación para cada tabla

  ![dibujo](media/marketplace-publishing-data-service-creation/step-7.5.png)

  ![dibujo](media/marketplace-publishing-data-service-creation/step-7.6.png)

Si servicio Web

  ![dibujo](media/marketplace-publishing-data-service-creation/step-7.7.png)

> [AZURE.IMPORTANT] Para obtener instrucciones detalladas y ejemplos para la creación de un servicio Web de CSDL, lea la [asignación de un servicio web existente a OData a través de CSDL](marketplace-publishing-data-service-creation-odata-mapping.md) .

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha creado la oferta de servicio de datos, asegúrese de que complete las instrucciones de la [Guía de catálogo de soluciones de Marketing contenido](marketplace-publishing-push-to-staging.md) antes de avanzar a la [prueba de su servicio de datos de ensayo](marketplace-publishing-data-service-test-in-staging.md).

## <a name="see-also"></a>Vea también
- [Introducción: Cómo publicar una oferta en Azure Marketplace](marketplace-publishing-getting-started.md)
- Si le interesa conocer el proceso de asignación de OData y propósito general, lea este artículo [Asignación de OData de servicio de datos](marketplace-publishing-data-service-creation-odata-mapping.md) para revisar las definiciones, estructuras e instrucciones.
- Si le interesa aprender y comprender los nodos específicos y sus parámetros, lea este artículo [OData asignación nodos de servicio de datos](marketplace-publishing-data-service-creation-odata-mapping-nodes.md) para definiciones y explicaciones, ejemplos y usar contexto mayúsculas y minúsculas.
- Si está interesado en ejemplos de revisiones, lea este artículo [Ejemplos de asignación de datos servicios OData](marketplace-publishing-data-service-creation-odata-mapping-examples.md) para ver código de ejemplo y comprender el contexto y la sintaxis de código.


[link-pubportal]:https://publish.windowsazure.com

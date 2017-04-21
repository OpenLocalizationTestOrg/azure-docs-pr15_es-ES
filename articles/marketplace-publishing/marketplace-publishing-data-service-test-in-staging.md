<properties
   pageTitle="Probar la oferta de servicio de datos para el catálogo de soluciones | Microsoft Azure"
   description="Comprender cómo probar su oferta de servicio de datos de Azure Marketplace."
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

# <a name="testing-your-data-service-offer-in-staging"></a>Probar la oferta de servicio de datos de ensayo

>[AZURE.IMPORTANT] **En este momento estamos ya no integrado cualquier nuevas publicaciones de servicio de datos. No se obtener aprobado dataservices nueva lista.** Si tiene una aplicación de empresa SaaS que desea publicar en AppSource puede encontrar más información [aquí](https://appsource.microsoft.com/partners). Si tiene un aplicaciones IaaS o servicio de programador que desea publicar en Azure Marketplace puede encontrar más información [aquí](https://azure.microsoft.com/marketplace/programs/certified/).

Después de completar los dos primeros pasos de [la cuenta de Microsoft Developer](marketplace-publishing-accounts-creation-registration.md) y la [creación](marketplace-publishing-data-service-creation.md) y la oferta de servicio de datos en el Portal de publicación ya está listo para poner a disposición de la oferta de Azure Marketplace. Este tema le guiará a través de la primero, intermedio, paso denominado "Ensayo"

Ensayo significa implementar la oferta en privadas "recinto" donde puede probar y comprobar su funcionalidad antes de enviar a producción. La oferta aparecerán en ensayo tal como lo haría para un cliente que ha implementado.

## <a name="step-1-pushing-your-offer-to-staging"></a>Paso 1. Inserción de la oferta de ensayo
Presión de la oferta de prueba le permite probar la oferta antes de que esté disponible para los suscriptores futuros.  Puede ver cómo aparecerán y de función para aquellos suscribirse a los datos de la oferta.  

  ![dibujo](media/marketplace-publishing-data-service-test-in-staging/step-1.1.png)

1.  Inicio de sesión en el [Portal de publicación](https://publish.windowsazure.com)
2.  Seleccione **Servicios de datos** en la ventana de navegación izquierda
3.  Seleccione la oferta que desea insertar en ensayo. Se muestra la pantalla anterior.
4.  Haga clic en el botón de **Inserción a la prueba** .  
5.  Si hay problemas con la oferta que necesita para completar antes de presión a ensayo, verá que muestra una lista.  Corrija estos elementos haciendo clic en cada elemento de la lista. Cuando todas las correcciones realizan, haga clic en botón **de inserción a ensayo** nuevamente.

Si no existen problemas con la oferta se muestra la ventana emergente a continuación.  

Si está planificación y no aprobado para exponer la oferta en el Portal de Azure (actualmente ha limitado la capacidad), a continuación, simplemente cierre la ventana emergente.

Para probar el servicio de datos en el Portal de Azure (además de portal de DataMarket), necesitará un identificador de suscripción de Azure para probar con.  Este ID de suscripción se identificar la cuenta que se le permitirá probar su oferta.  

Cortar y pegar su identificador de suscripción y haga clic en la marca de verificación para continuar.

  ![dibujo](media/marketplace-publishing-data-service-test-in-staging/step-1.2.png)

> [AZURE.NOTE] Estas suscripciones Azure identificadores sólo son necesarios para probar y provisional en el [Portal de administración de Azure](https://manage.windowsazure.com). No son necesarias para probar en Azure Marketplace.

La siguiente pantalla que aparece muestra que la publicación celebra mostrando el icono "en curso" resaltado amarillo abajo. Presión a ensayo dura entre 10 y 15 minutos.  Si tarda más, primero actualice su explorador (presione F5 en Internet Explorer).  En los casos donde la oferta todavía está llevando a ensayo después de una hora, haga clic en el contacto que nos vincular a háganoslo saber que hay un problema.

  ![dibujo](media/marketplace-publishing-data-service-test-in-staging/step-1.3.png)

Cuando la inserción a en prueba finaliza el icono "en curso" Detener mover y el estado se actualizará a "Etapas".  Ya está listo para probar su oferta.  

## <a name="step-2-test-your-staged-offer-in-datamarket"></a>Paso 2. Probar su oferta preconfigurada de DataMarket

Haga clic en el vínculo detrás del texto **"Ver su servicio ofrecen en..."** para mostrar la pantalla que el suscriptor verá cuando su oferta va a producción y aparecerá en DataMarket.

  ![dibujo](media/marketplace-publishing-data-service-test-in-staging/step-2.2.png)

Probar o comprobar cada uno de los 12 elementos marcados encima de garantizar que todos los logotipos, precios/transacciones, texto, imágenes, documentación y vínculos son correctas y funcionen correctamente.  Este es un buen momento para asegurarse de que los valores de prueba que especificó al crear la oferta se han reemplazado con valores reales.

1. Logotipo de oferta
2. Nombre de la oferta
3. Nombre de Publisher o vínculo al sitio Web de su empresa
4. Categorías de búsqueda para la oferta
5. Vínculo a soporte técnico de la oferta para ayudar a los suscriptores
6. Descripción contextual para la oferta
7. Plan de oferta ilustra los detalles de la facturación
8. Vincular a código de implementación
9. Usan imágenes de ejemplo que ilustran de oferta de datos
10. Metadatos de entrada y salida para cada servicio de la oferta
11. Términos de oferta de uso
12. Vista previa de los datos de la oferta


Por último, compruebe que el servicio funcionará a través de la Datamarket haciendo clic en el vínculo "Explorar este conjunto de datos".  Se abrirá una nueva ventana en la herramienta llamamos "Servicio explorador" para que pueda ver los resultados de una consulta con el servicio.  En esta ventana, puede especificar los parámetros necesarios y ver los resultados mostrados desde una consulta en el servicio.   Además, la muestra es la dirección URL para la consulta.  

> [AZURE.NOTE] Asegúrese de revisar la descripción textual del servicio aparece en la parte superior.  Y si su oferta formado por más de un servicio de llamadas, haga clic en las pestañas en la parte inferior para cambiar a la siguiente servicio revisar y volver a probar.



## <a name="next-step"></a>Siguiente paso
Si está teniendo problemas y necesita ayuda para resolverlos póngase en contacto con [Soporte técnico de Publisher de Azure]( http://go.microsoft.com/fwlink/?LinkId=272975).

Si está satisfecho y listo para publicar su oferta lea la documentación de [Solicitar la aprobación de inserción a producción](marketplace-publishing-push-to-production.md) .

## <a name="see-also"></a>Vea también
- [Introducción: Cómo publicar una oferta en Azure Marketplace](marketplace-publishing-getting-started.md)

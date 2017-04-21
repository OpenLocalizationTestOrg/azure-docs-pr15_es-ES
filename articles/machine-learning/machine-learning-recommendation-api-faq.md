<properties 
    pageTitle="Configurar y usar la API de recomendaciones de aprendizaje de equipo | Microsoft Azure" 
    description="API de recomendaciones de Microsoft integrado con preguntas más frecuentes sobre aprendizaje del equipo de Azure" 
    services="machine-learning" 
    documentationCenter="" 
    authors="LuisCabrer" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/08/2016" 
    ms.author="luisca"/> 

#<a name="setting-up-and-using-machine-learning-recommendations-api-faq"></a>Configurar y utilizar máquina aprendizaje recomendaciones API preguntas más frecuentes


**¿Qué es recomendaciones?**

>[AZURE.NOTE]Debe comenzar a usar el servicio cognitivas API de recomendaciones en lugar de esta versión. El servicio de recomendaciones cognitivas se van a reemplazar este servicio y las nuevas características se desarrollará allí. Tiene nuevas capacidades como lotes de soporte técnico, una mejor API Explorer, una experiencia de suscripción y facturación de superficie, más coherente API más limpio, etcetera.
> Más información acerca de la [migración al nuevo servicio cognitivas](http://aka.ms/recomigrate)

Para las organizaciones y empresas que se basan en recomendaciones para cruzadas y arriba vender productos y servicios a sus clientes, recomendaciones de aprendizaje del equipo de Azure proporciona un motor de autoservicio recomendaciones. Es una implementación de filtrado de colaboración que utiliza la descomposición en factores matriz como su algoritmo principal. Los desarrolladores de aplicaciones pueden tener acceso a recomendaciones mediante las API de REST. 

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

**¿Qué puedo hacer con recomendaciones?**

RECOMENDACIONES toma como entrada un elemento o un conjunto de elementos y devuelve una lista de recomendaciones pertinentes. Por ejemplo: un cliente de un proveedor en línea, haga clic en un producto. El distribuidor online envía ese producto como entrada de recomendaciones, obtiene una lista de productos de y decide cuál de estos productos se muestran al cliente. Desea utilizar recomendaciones para optimizar la tienda en línea o incluso para informar a su interior centro de llamada o el departamento de ventas.

**¿Existe alguna limitación de uso?**

Recomendaciones tiene las siguientes limitaciones de uso:
* Número máximo de modelos por suscripción: 10
* Número máximo de elementos que puede contener un catálogo: 100,000
* El número máximo de puntos de uso que se conservan es ~ 5.000.000. El más antiguo se eliminarán si se cargó o notificadas nuevos.
* Tamaño máximo de datos que pueden enviar por correo electrónico (por ejemplo, importar datos de catálogo, importar datos de uso) es 200 MB
* Número de transacciones por segundo (TPS) para una versión de modelo de recomendaciones no está activa es TPS ~ 2. Una compilación de modelo de recomendaciones está activa puede contener hasta 20 TPS.

##<a name="purchase-and-billing"></a>Compra y facturación 


**¿Cuánto cuesta de costo de recomendaciones durante el período de inicio?**

Recomendaciones es un servicio basado en suscripción. Carga se basa en el volumen de transacciones por mes. Puede comprobar la [página de oferta] (https://datamarket.azure.com/dataset/amla/recommendations) en Microsoft Azure Marketplace para obtener información sobre precios.

**¿Hay realizar un seguimiento de los costos asociados con tener recomendaciones y almacenan la actividad de usuario para mí?**

No en este momento.

**¿Recomendaciones tiene una prueba gratuita?**

Hay una prueba gratuita que están limitado a 10.000 transacciones por mes.

**¿Cuándo se cargará recomendaciones?**

Una suscripción de pago es cualquier suscripción cuya es una cuota mensual. Cuando compra una suscripción de pago, le cobrarán inmediatamente para su uso del primer mes. Se cargará la cantidad que está asociada con la oferta en la página de suscripción (además de los impuestos aplicables). Este cargo mensual se realiza cada mes en la misma fecha de calendario como la compra original hasta que cancele la suscripción. 

**¿Cómo actualizar a un servicio de nivel superior?**

Puede comprar o actualizar la suscripción desde la [página de oferta] página (https://datamarket.azure.com/dataset/amla/recommendations) en Microsoft Azure Marketplace.

Cuando se actualiza una suscripción:

* Las transacciones que se quedan en la antigua suscripción no se agregan a la nueva suscripción. 
* Pagar precio total para la nueva suscripción, aunque tenga transacciones sin usar de su suscripción antigua.

Proceso de actualización de una suscripción:

* Nevigate a [página de oferta] (https://datamarket.azure.com/dataset/amla/recommendations).
* Inicie sesión en el catálogo de soluciones si ya no haya iniciado sesión.
* En el panel derecho, se muestran todos los planes disponibles. Haga clic en el botón de radio para el plan que desea actualizar a.
* Si desea actualizar, haga clic en **Aceptar**. Si no desea actualizar, haga clic en **Cancelar**.

**Importante** Lea detenidamente el cuadro de diálogo antes de actualizar porque hay implicaciones de facturación y uso.

**¿Cuándo finalizará mi suscripción recomendaciones?**

Su suscripción finalizará cuando se cancela. Si desea cancelar las suscripciones, vea las instrucciones siguientes.

**¿Cómo puedo cancelar mi suscripción de recomendaciones?**

Para cancelar la suscripción, realice los siguientes pasos. Si su suscripción actual es una suscripción de pago, la suscripción sigue en vigor hasta el final del período de facturación actual. Si necesita la cancelación sea efectiva inmediatamente, póngase en contacto con nosotros en [Soporte técnico de Microsoft](https://support.microsoft.com/oas/default.aspx?gprid=17024&st=1&wfxredirect=1&sd=gn).

**Nota** Reembolso no se expresa si cancela antes del final de un período de facturación o para las transacciones sin usar en un período de facturación.

* Vaya a la sección [oferta página] (https://datamarket.azure.com/dataset/amla/recommendations).
* Inicie sesión en el catálogo de soluciones si ya no haya iniciado sesión.
* Haga clic en **Cancelar** a la derecha del nombre del conjunto de datos y el estado. Puede usar esta suscripción hasta el final del período de facturación actual o se alcanza el límite de transacción (suceda primero).

Si desea cancelar la suscripción inmediatamente, así que puede adquirir una nueva suscripción, archivo un vale al [Soporte técnico de Microsoft](https://support.microsoft.com/oas/default.aspx?gprid=17024&st=1&wfxredirect=1&sd=gn).

##<a name="getting-started-with-recommendations"></a>Introducción a las recomendaciones

**¿Es recomendaciones para mí?** 

Recomendaciones de aprendizaje del equipo es para las organizaciones y empresas que se basan en recomendaciones para cruzadas y arriba vender productos o servicios a sus clientes. Si tiene un sitio Web de cliente, un equipo de ventas, interior equipo de ventas o un centro de llamadas, y si ofrece un catálogo de más de unos pocos docenas de productos o servicios, la línea de la parte inferior pueden beneficiarse de recomendaciones. 

Experimentar con recomendaciones está diseñada para ser muy sencillos. La versión actual de API requiere conocimientos básicos de programación. Si necesita ayuda, póngase en contacto con el proveedor que ha había desarrollado su sitio Web. Si tiene un departamento de TI interno o un desarrollador interno, podrá obtener recomendaciones para que trabaje para usted. 

**¿Cuáles son los requisitos previos para configurar recomendaciones?**

Recomendaciones requiere que tenga un registro de opciones de usuario en relación con el catálogo. Si no t tiene un registro de este tipo y tiene un sitio Web de cliente hacia, recomendaciones pueden recopilar la actividad de usuario para usted. 

Recomendaciones también requiere un catálogo de productos o servicios. Si no t tiene el catálogo, pueden usar los datos de uso de cliente real y desglosar un catálogo recomendaciones. Un catálogo implícito no incluye los elementos que no se han registrado como parte de transacciones de usuario.

**¿Cómo configurar recomendaciones por primera vez?**

Después [suscribirse] (https://datamarket.azure.com/dataset/amla/recommendations) para obtener recomendaciones, debe usar la documentación de la API en [Azure máquina aprendizaje recomendaciones Guía de inicio rápido](machine-learning-recommendation-api-quick-start-guide.md) para configurar el servicio.

**¿Dónde se puede encontrar la documentación de la API?** 

Documentación de la API es [Azure máquina aprendizaje recomendaciones Guía de inicio rápido](machine-learning-recommendation-api-quick-start-guide.md).

**¿Qué opciones tengo que cargar los datos de catálogo y el uso en recomendaciones?**

Tiene dos opciones para cargar los datos de catálogo y el uso: puede exportar los datos de su sistema CRM o en otros registros y cargar las recomendaciones, o puede agregar etiquetas a su sitio Web que realizar un seguimiento de las actividades de usuario. Si utiliza el segundo método, los datos se almacenarán en Azure.

##<a name="maintenance-and-support"></a>Mantenimiento y soporte técnico

**¿Cómo grandes pueden mi conjunto de datos?**

Cada conjunto de datos puede contener hasta 100000 elementos de catálogo y hasta 2048 MB de datos de uso.
Además, una suscripción puede contener un máximo de 10 conjuntos de datos (modelos).

**¿Dónde puedo obtener soporte técnico para obtener recomendaciones?**

Soporte técnico está disponible en el sitio de [Soporte de Microsoft Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=MachineLearning) .

**¿Dónde se puede encontrar los términos de uso?**

[Equipo de Microsoft Azure recomendaciones API términos de servicio de aprendizaje](https://datamarket.azure.com/dataset/amla/recommendations#terms).



 

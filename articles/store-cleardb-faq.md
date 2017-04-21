<properties
    pageTitle="Preguntas más frecuentes sobre las bases de datos ClearDB MySql con el servicio de aplicación de Azure | Microsoft Azure"
    description="Respuestas a preguntas habituales sobre el uso de las bases de datos ClearDB MySQL con el servicio de aplicación de Azure."
    documentationCenter="php"
    services=""
    authors="sunbuild"
    manager="yochayk"
    editor=""
    tags="mysql"/>

<tags
    ms.service="multiple"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/27/2016"
    ms.author="sumuth"/>

# <a name="faq-for-cleardb-mysql-databases-with-azure-app-service"></a>Preguntas más frecuentes sobre las bases de datos ClearDB MySql con el servicio de aplicación de Azure

Este artículo responde preguntas habituales sobre el uso y compras ClearDB MySQL bases de datos para aplicaciones Web de Azure.

## <a name="what-options-do-i-have-for-mysql-on-azure"></a>¿Qué opciones tengo para MySQL en Azure?

Tiene varias opciones:

* [Base de datos MySQL de ClearDB compartido](/marketplace/partners/cleardb/databases/)

* [Clústeres ClearDB MySQL Premium](/marketplace/partners/cleardb-clusters/cluster/)

* [Clúster de MySQL que se ejecuta en una máquina virtual de Azure](https://github.com/azure/azure-quickstart-templates/tree/master/mysql-replication)

* [Instancia de MySQL que se ejecuta en una máquina virtual de Azure](virtual-machines/virtual-machines-windows-classic-mysql-2008r2.md)

ClearDB es un servicio de hospedaje de MySQL y administre la infraestructura de MySQL por usted. Cuando se ejecuta su propio clúster de MySQL o base de datos en una máquina Virtual de Azure, debe configurar el servidor MySQL y mantenerlo actualizado con las revisiones.

## <a name="do-i-need-a-credit-card-for-the-web-app--mysql-template-in-the-azure-marketplace"></a>¿Necesita una tarjeta de crédito para la aplicación Web + MySQL plantilla en el catálogo de soluciones de Azure?

Esto depende del tipo de suscripción que está utilizando. Estos son algunos tipos de suscripción utilizados con frecuencia:

* [Pago](/offers/ms-azr-0003p/): requiere una tarjeta de crédito y comprar una base de datos MySQL pago se cargará su tarjeta de crédito.

* [Prueba gratuita](https://azure.microsoft.com/pricing/free-trial/): incluye créditos para usar con Microsoft Azure de servicios, pero no permite la compra de recursos de terceros. Para comprar servicios de terceros o una base de datos MySQL pago que debe usar una tarjeta de crédito habilitada la suscripción. Para las aplicaciones Web, puede crear una base de datos ClearDB MySQL gratuita.

* **Prueba de desarrollo de MSDN pagar medida que vaya**y la [suscripción de MSDN](/pricing/member-offers/msdn-benefits/) : Similar a la versión de prueba gratuita, una suscripción a MSDN requiere que tenga una tarjeta de crédito para comprar una solución MySQL pagada de ClearDB.

* [Contrato Enterprise (EA)](/pricing/enterprise-agreement/): se facturación a los clientes EA contra su EA cada trimestre para todas sus compras de Azure Marketplace (terceros) en una factura independiente, consolidada. Se cargarán fuera el compromiso monetario para cualquier compras del catálogo de soluciones. Tenga en cuenta que, en este momento, no está disponible para los clientes que se ha inscritos en Azerbaiyán, Croacia, Noruega y Puerto Rico almacén de Azure. 

*  [DreamSpark](https://www.dreamspark.com/Product/Product.aspx?productid=99): puede crear solo ClearDB gratuita bases de datos para aplicaciones Web. No hay ningún límite del número de bases de datos MySQL gratuita de ClearDB que puede crear. Tenga en cuenta que gratuita bases de datos no se pueden usar para las aplicaciones web de producción, como este servicio sólo está pensado para prueba.

## <a name="why-was-i-charged-350-for-a-web-app--mysql-from-the-azure-marketplace"></a>¿Por qué me cobrará 3,50 USD para una aplicación Web + MySQL de Azure Marketplace?

La opción de base de datos predeterminada es Titan, que es 3,50 USD. No va a mostrar el costo durante la creación de la base de datos y se puede comprar por error una base de datos que no desea. Estamos intentando encontrar una manera para mejorar la experiencia pero hasta entonces debe comprobar todos los niveles de precios seleccionados para la aplicación web y base de datos antes de hacer clic en **crear** e iniciar la implementación de los recursos.

## <a name="i-am-running-mysql-on-my-own-azure-virtual-machine-can-i-connect-my-azure-web-app-to-my-database"></a>Estoy ejecutando MySQL en mi propia máquina virtual Azure. ¿Puedo conectar mi aplicación web Azure a la base de datos?

Sí. Puede conectar la aplicación web de a la base de datos mientras la máquina virtual de Azure ha proporcionado acceso remoto a su aplicación web. Para obtener más información, vea [Instalar MySQL en una máquina virtual](virtual-machines/virtual-machines-windows-classic-mysql-2008r2.md).

## <a name="in-which-countries-are-cleardb-premium-mysql-clusters-supported"></a>¿En la que países son clústeres ClearDB Premium MySQL compatibles?

[ClearDB Premium MySQL clústeres](/marketplace/partners/cleardb-clusters/cluster/) están disponibles en todas las regiones de Azure en todo el mundo con la excepción de India, Australia, Brasil sur y China.

## <a name="can-i-create-a-new-cluster-prior-to-creating-a-database-with-cleardb-premium-cluster-solution"></a>¿Puedo crear un nuevo clúster antes de crear una base de datos con la solución de clúster de ClearDB premium?

No, no se admite la creación de clústeres de ClearDB vacíos. El portal de Azure le permite crear bases de datos en un clúster, que puede crear un nuevo clúster al mismo tiempo.

## <a name="will-i-be-warned-if-i-try-to-delete-a-cleardb-mysql-database-that-is-in-use-by-one-of-my-applications"></a>¿Avisará si se intenta eliminar una base de datos ClearDB MySQL que está en uso por una de las aplicaciones?

No, Azure no le avisará si elimina una compra de marketplace depende de la aplicación.

## <a name="which-regions-can-i-create-cleardb-databases-in"></a>¿Qué regiones puedo crear bases de datos de ClearDB en?

No está disponible para los clientes que se ha inscritos en Azerbaiyán, Croacia, Noruega o Puerto Rico Azure Marketplace. ClearDB no está disponible en estas regiones.

## <a name="what-pricing-tier-should-i-choose-for-a-production-web-app-and-database"></a>¿Qué nivel de precios debo elegir para una aplicación web de producción y una base de datos?

Usar Basic o un nivel superior de precios para aplicaciones Web. Para ClearDB, se recomienda plan Saturno o de Júpiter. Revise las características y limitaciones de cada nivel de precios para [Aplicaciones Web](/pricing/details/app-service/) y [las bases de datos ClearDB MySQL](/marketplace/partners/cleardb/databases/) elegir el que se adapte a sus necesidades.

## <a name="how-do-i-upgrade-my-cleardb-database-from-one-plan-to-another"></a>¿Cómo puedo actualizar mi base de datos de ClearDB de un plan a otro?

En el [portal de Azure](https://portal.azure.com), puede escalar ClearDB hospedaje base de datos compartida. Lea este [artículo](https://blogs.msdn.microsoft.com/appserviceteam/2016/10/06/upgrade-your-cleardb-mysql-database-in-azure-portal/) para obtener más información. Actualmente no se admite actualización para clústeres ClearDB Premium en el portal de Azure.

## <a name="i-cant-see-my-cleardb-database-in-azure-portal"></a>¿No puedo ver mi base de datos de ClearDB en el portal de Azure?

Si creamos ClearDB base de datos con el Administrador de recursos de Azure o [Nuevo Portal de Azure](https://portal.azure.com), no estarán visible en el [Portal de Azure antiguo](https://manage.windowsazure.com). Para evitar esto es vincular la base de datos manualmente a la aplicación web. De forma similar si crear base de datos de ClearDB en el [portal de antiguo](https://manage.windowsazure.com) no podrá ver la base de datos en el [Nuevo Portal de Azure](https://portal.azure.com). No hay ninguna solución alternativa para el último escenario.

## <a name="who-do-i-contact-for-support-when-my-database-is-down"></a>¿Quién ¿contacto de soporte técnico cuando la base de datos?

Problemas relacionados con contactos [ClearDB de soporte técnico](https://www.cleardb.com/developers/help/support) para cualquier base de datos. Prepararse para proporcionar información de su suscripción de Azure.

## <a name="can-i-create-additional-users-for-my-cleardb-mysql-database-cluster-solution"></a>¿Puedo crear usuarios adicionales para la solución de clúster de base de datos ClearDB MySQL? 

No. No puede crear usuarios adicionales, pero puede crear bases de datos adicionales en el clúster de base de datos de ClearDB.  

## <a name="can-basicpro-series-databases-be-upgraded-in-place-similar-to-planetary-plans-today-on-cleardb-portal"></a>¿Puede Basic/Pro series las bases de datos actualizados en el lugar similar a planes planetarios hoy en el portal de ClearDB?

Sí, serie básica bases de datos se pueden actualiza (60 básica a través de 500 básico) en lugar. Serie Pro pueden actualizarse in situ (125 Pro a 1000 Pro) excepto 60 Pro. No se admite la actualización de base de datos de 60 Pro actualmente. 

## <a name="when-i-migrate-my-resources-from-one-subscription-to-another-does-my-cleardb-mysql-database-get-migrated-as-well"></a>¿Cuando migrar Mis recursos de una suscripción a otro, la base de datos ClearDB MySQL obtener migrarán también? 

Al realizar la migración de recursos a través de suscripciones, aplicar algunas [limitaciones](./app-service-web/app-service-move-resources.md) . Una base de datos ClearDB MySQL es un servicio de terceros y, por tanto, no se migran los durante la migración de suscripción de Azure. Si no administra la migración de la base de datos MySQL antes de migrar recursos Azure, se pueden deshabilitar las bases de datos ClearDB MySQL. Migrar manualmente las bases de datos en primer lugar y, a continuación, realizar migración de suscripción de Azure para la aplicación web. 

## <a name="can-i-transfer-a-cleardb-database-from-a-credit-card-subscription-to-an-ea-subscription"></a>¿Puedo transferir una base de datos de ClearDB de una suscripción de tarjeta de crédito una suscripción EA?

Bases de datos existentes ClearDB usar la tarjeta de crédito asociada a la suscripción existente. Para usar una suscripción de EA que necesita migrar los datos a una nueva base de datos:

* Adquiera una nueva base de datos de ClearDB con su suscripción de Asia Oriental.
* Migrar los datos a la nueva base de datos.
* Actualizar la aplicación para usar la nueva base de datos.
* Eliminar la base de datos de ClearDB antiguo.

Al crear una nueva aplicación web con MySQL (ClearDB) o crear una base de datos MySQL (ClearDB), la suscripción que elija determina cómo pagará al servicio. Con una suscripción de EA, no se bloqueará el aprovisionamiento de los servicios de terceros como ClearDB en el portal de Azure. Suscripciones de EA se cargarán fuera de compromiso monetarios y facturación trimestral y retrasen. El cliente EA tendría que configurar un método de pago, como una tarjeta de crédito para pagar los servicios del catálogo de soluciones de terceros.

## <a name="where-can-i-see-the-charges-for-cleardb-resources-in-an-ea-subscription"></a>¿Dónde se puede ver los cargos para recursos ClearDB en una suscripción de EA?

Los clientes de EA directo, cargos de Azure Marketplace están visibles en el Portal de empresa. Observe que todas las compras de marketplace y consumo se cargarán fuera de compromiso monetarios y facturación trimestral y retrasen. Los clientes EA tienen que pagar directamente a los proveedores de servicios de terceros y pueden hacerlo activando un método de pago, como una tarjeta de crédito con su cuenta de Asia Oriental.

Los clientes EA indirectos pueden encontrar sus suscripciones de Azure Marketplace en la página **Administrar suscripciones** de Enterprise Portal, pero precios está oculta. Los clientes deben ponerse en contacto con su proveedor de servicios Lingüísticos para obtener información sobre los cargos del catálogo de soluciones.

Acceso a Azure Marketplace para servicios de terceros puede administrarse por los administradores de inscripción EA Azure. Se puede deshabilitar o volver a habilitar acceso a 3ª compras de fiesta de la tienda en administrar cuentas y suscripciones en la sección de cuentas en el Portal de empresa.

## <a name="who-do-i-contact-for-questions-about-my-bill-for-cleardb-services-in-my-ea-subscription"></a>¿Quién ¿contacto para preguntas sobre mi factura para servicios de ClearDB en mi suscripción EA?

Póngase en contacto con [Soporte al cliente empresa](http://aka.ms/AzureEntSupport) con respecto a la facturación en su inscripción EA. El equipo de soporte técnico de EA Portal se sus preguntas o resolver el problema.

 



## <a name="more-information"></a>Más información

[Preguntas más frecuentes de Azure Marketplace](/marketplace/faq/)

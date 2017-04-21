<properties
   pageTitle="Requisitos previos técnicos para crear una oferta de Azure Marketplace | Microsoft Azure"
   description="Comprender los requisitos para crear e implementar una oferta a Azure Marketplace para que otras personas para comprar."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

<tags
  ms.service="marketplace"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="Azure"
  ms.workload="na"
  ms.date="08/18/2016"
  ms.author="hascipio"/>

# <a name="general-prerequisites-for-creating-an-offer-for-the-azure-marketplace"></a>Requisitos previos para crear una oferta de Azure Marketplace
Comprender los requisitos previos generales, business-centrado en los procesos necesarios para llevar a cabo el proceso de creación de la oferta.

## <a name="ensure-that-you-are-registered-as-a-seller-with-microsoft"></a>Asegúrese de que está registrado como un vendedor con Microsoft
Para obtener instrucciones detalladas sobre cómo registrar una cuenta de vendedor con Microsoft, vaya a la [creación de la cuenta y el registro](marketplace-publishing-accounts-creation-registration.md).

- **Si su empresa ya está registrada como un vendedor en el centro de desarrollo y que desea crear una oferta de nueva** , a continuación, inicie sesión en la publicación de portal con el mismo id de correo electrónico con el centro de desarrollo se realiza el registro. Este paso es necesario para que el portal de publicación y el centro de desarrollo están vinculadas entre sí.
- **Si su empresa ya está registrada como un vendedor en el centro de desarrollo y que desea editar una oferta existente,** a continuación, iniciar sesión en la publicación de portal con la cuenta de administrador o con una cuenta que se agrega como un administrador de co en la publicación portal. Pasos para agregar una cuenta de administrador de co se muestra a continuación.

## <a name="steps-to-add-a-co-admin-in-the-publishing-portal"></a>Pasos para agregar un administrador de co en el Portal de publicación
Los administradores de la publicación portal puede agregar otros miembros de la compañía, quién están trabajando en la aplicación, como un administrador de co en la publicación portal. **Suponiendo que es el administrador,** a continuación encontrará los pasos para agregar un administrador de co-.

>[AZURE.NOTE] Para los nuevos usuarios, antes de agregar un administrador de co en la publicación portal, asegúrese de que ha creado al menos una aplicación en la publicación portal. Esto es necesario, en la pestaña **EDITORES** aparecen solo después de crear al menos una aplicación en la publicación de portal.

1. Asegúrese de que el identificador de correo electrónico de administración de co es un account(MSA) de Microsoft. Si no es así, registrar como un MSA con este [vínculo](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1).
2. Asegúrese de que hay al menos una aplicación en la cuenta de administrador antes de agregar un administrador de co-.
3. Después de que haya terminado los pasos anteriores, inicie sesión en la publicación de portal con el id de correo electrónico de co-administrador y, a continuación, cierre de sesión.
4. Ahora inicie sesión en la publicación de portal con el id de correo electrónico del administrador.
5. Vaya a editores -> seleccionar su cuenta -> administradores -> agregar el Administrador de co (captura de pantalla muestra a continuación)

    ![dibujo](media/marketplace-publishing-pre-requisites/imgAddAdmin_05.png)

6. Asegúrese de que están supervisados identificadores de correo electrónico proporcionadas en las distintas etapas del proceso de publicación (por ejemplo, centro de desarrollo, portal de publicación) para las comunicaciones de Microsoft.
7. Para el registro del centro de desarrollo, evitar el uso de una cuenta asociada a una única persona. Esto se sugiere para quitar la dependencia de un individuo.
8. Si se enfrentan a problemas con el registro en el centro de desarrollo, por favor elevar un vale con este [vínculo](https://developer.microsoft.com/en-us/windows/support).

## <a name="steps-to-delete-a-co-admin-in-the-publishing-portal"></a>Pasos para eliminar un administrador de co en la publicación de portal
**Suponiendo que es el administrador,** a continuación encontrará los pasos para eliminar un co-administrador.

1. Inicie sesión en la publicación de portal con el id de correo electrónico del administrador.
2. Vaya a **editores** -> seleccionar su cuenta -> **administradores** -> **- Coadministradores**.
3. Haga clic en el botón **X** junto a la administración de co que desee eliminar total (captura de pantalla muestra a continuación).

    ![dibujo](media/marketplace-publishing-pre-requisites/imgDeleteAdmin_03.png)

> [AZURE.IMPORTANT] No es necesario que completar la información de impuestos y banco de empresa si va a publicar solo gratuitas ofertas (o Traer su propia licencia).

> Debe llevar a cabo el registro de la empresa para empezar. Sin embargo, mientras trabaja su empresa en la información de impuestos y banco de la cuenta de Microsoft Developer, los desarrolladores pueden comenzar a trabajar en la creación de la imagen de la máquina virtual en el [Portal de publicación](https://publish.windowsazure.com), obtenerlo certificados y las pruebas en el entorno de ensayo Azure. Necesitará la aprobación de la cuenta de vendedor completa solo para el paso final de la publicación de la oferta a Azure Marketplace.

## <a name="acquire-an-azure-pay-as-you-go-subscription"></a>Adquirir una suscripción de "pago" Azure
Esta es la suscripción que se va a utilizar para crear las imágenes de máquina virtual y entregar las imágenes a la [Azure Marketplace](https://azure.microsoft.com/marketplace/). Si no tiene una suscripción existente, a continuación, suscríbase en https://account.windowsazure.com/signup?offer=ms-azr-0003p.

## <a name="sell-from-countries"></a>"Vender de" países
> [AZURE.WARNING]
Para vender sus servicios en el catálogo de soluciones de Azure, debe asegurarse de que la entidad registrada es uno de los países aprobados "vender-de". Esta restricción es por razones de pago y fiscales. Se buscan conocidas para expandir la lista de países en un futuro próximo, así que esté atento. Para obtener la lista completa, vea 1b de la sección de las [directivas de participación de Azure Marketplace](http://go.microsoft.com/fwlink/?LinkID=526833).

## <a name="next-steps"></a>Pasos siguientes
Una vez que se cumplen los requisitos previos técnicos, es siguiente la oferta requisitos previos técnicos específicos. Haga clic en el vínculo del artículo para el tipo de oferta respectivos que le gustaría crear de Azure Marketplace.

- [Requisitos previos técnicos de VM](marketplace-publishing-vm-image-creation-prerequisites.md)
- [Solución plantilla técnica requisitos previos](marketplace-publishing-solution-template-creation-prerequisites.md)

## <a name="see-also"></a>Vea también
- [Introducción: cómo publicar una oferta en Azure Marketplace](marketplace-publishing-getting-started.md)

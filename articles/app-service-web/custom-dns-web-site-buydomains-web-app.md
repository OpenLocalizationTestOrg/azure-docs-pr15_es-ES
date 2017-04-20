<properties
    pageTitle="Cómo comprar un nombre de dominio personalizado en las aplicaciones de Azure aplicación de servicio Web"
    description="Aprenda cómo comprar un nombre de dominio personalizado con una aplicación web de servicio de aplicaciones de Azure."
    services="app-service\web"
    documentationCenter=""
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="buy-and-configure-a-custom-domain-name-in-azure-app-service"></a>Comprar y configurar un nombre de dominio personalizado en la aplicación de servicio de Azure

[AZURE.INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

Cuando se crea una aplicación web, Azure asigna a un subdominio del azurewebsites.net. Por ejemplo, si la aplicación se denomina **contoso**, la dirección URL es **contoso.azurewebsites.net**. Azure también asigna una dirección IP virtual.

Para una aplicación web de producción, probablemente desea que los usuarios para ver un nombre de dominio personalizado. En este artículo se explica cómo comprar y configurar un dominio personalizado con la [Aplicación de servicio Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714). 

[AZURE.INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]


## <a name="overview"></a>Información general

Si no tiene un nombre de dominio para la aplicación web, se puede comprar uno fácilmente en [El Portal de Azure](https://portal.azure.com/). Durante el proceso de compra puede elegir tener WWW y raíz registros DNS se asigna automáticamente a su aplicación web. También puede administrar su dominio directamente en el Portal de Azure.


Realice los pasos siguientes para comprar nombres de dominio y asignar a la aplicación web.

1. En el explorador, abra el [Portal de Azure](https://portal.azure.com/).

2. En la pestaña de **Aplicaciones Web** , haga clic en el nombre de la aplicación web, seleccione **configuración**y, a continuación, seleccione **dominios personalizados**

    ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

3. En el módulo de **dominios personalizados** , haga clic en **comprar dominios**.

    ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-1.png)

4. En el módulo de **Dominios comprar** , use el cuadro de texto para escribir el nombre de dominio que desea comprar y pulse ENTRAR. Se muestran los dominios disponibles sugeridos justo debajo del cuadro de texto. Seleccione el dominio que desea comprar. Puede adquirir varios dominios a la vez. 

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.png)

5. Haga clic en la **Información de contacto** y rellene el formulario de información de contacto del dominio.

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-3.png)

    > [AZURE.NOTE] Es muy importante que rellenar todos los campos requeridos con mayor precisión como sea posible, especialmente la dirección de correo electrónico. En caso de adquirir el dominio sin "Protección de privacidad", puede que se le pida para comprobar el correo electrónico antes de que el dominio esté activo. En algunos casos, incorrecta de datos para la información de contacto da error de dominios de compra. 

6. Ahora puede elegir,

    un) "renovación automática" su dominio cada año
    
    b) participación en de "Protección de privacidad" que se incluye en el precio de compra de forma gratuita (excepto para dominios de nivel superior que registro no admite la declaración de privacidad. Por ejemplo:. co.in,. co.uk etcetera.)  
    
    c) "asignar nombres de host predeterminado" para WWW y raíz de dominio a la aplicación Web actual. 

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.5.png)
  
    > [AZURE.NOTE] Opción C configura enlaces DNS y Hostname enlaces automáticamente.  De este modo, la aplicación Web se puede acceder con el dominio personalizado tan pronto como la compra está completa (baring demoras de propagación de DNS en algunos casos). En caso de que la aplicación Web está detrás del Administrador de tráfico de Azure, no verá una opción para asignar el dominio raíz, como registros a no funcionan con el Administrador de tráfico. Puede siempre asignar los dominios/sub-domains adquiridos a través de una aplicación Web a otro Web App y viceversa. Vea el paso 8 para obtener más detalles. 
    
7. Haga clic en **Seleccionar** en el módulo de **Comprar dominios** , verá la información de compra en el módulo de **confirmación de compra** . Si acepta las condiciones legales y haga clic en **comprar**, se enviará el pedido y puede supervisar el proceso de compra en la **notificación**. Compra de dominio puede tardar varios minutos en completarse. 

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-4.png)

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-5.png)

8. Si ha encargado correctamente un dominio, puede administrar el dominio y asignar a la aplicación web. Haga clic en el **"botón..."** en el lado derecho de su dominio. A continuación, puede **Cancelar la compra** o **Administrar dominio**. Haga clic en **Administrar dominio**, a continuación, podemos enlazar **subdominio** a nuestra aplicación web en el módulo de **Administrar dominios** . Si desea enlazar un **subdominio** a una aplicación Web diferente realizar este paso desde dentro del contexto de la aplicación Web correspondiente. Aquí puede elegir para asignar el dominio al extremo del Administrador de tráfico (si es de aplicación Web detrás TM) simplemente seleccionando Administrador de tráfico nombre en el menú desplegable. Al hacer esto, dominio o subdominio se asignará automáticamente a todas las aplicaciones Web detrás de ese extremo del Administrador de tráfico. 

    ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-6.png)

    > [AZURE.NOTE] Puede "Cancelar compra" dentro de 5 días reembolso completo. Después de 5 días, podrá no pueda "Cancelar compra", en lugar de ello verá una opción para el dominio "eliminar". Eliminar el dominio da de lanzamiento de la suscripción sin reembolso y no estará disponible dominio. 

Una vez completada la configuración, se mostrará el nombre de dominio personalizado en la sección de **enlaces de nombre de host** de la aplicación web.

En este momento, debería poder escribir el nombre de dominio personalizado en el explorador y ver que se tarda correctamente a su aplicación web.
 
## <a name="what-happens-to-the-custom-domain-you-bought"></a>¿Qué sucede con el dominio personalizado que se ha adquirido

El dominio personalizado que se compró en el módulo de **dominios personalizados y SSL** está vinculado a la suscripción de Azure. Como un recurso de Azure, este dominio personalizado es diferente e independiente de la aplicación de servicio de la aplicación que ha comprado primero el dominio para. Esto significa que:

- En el portal de Azure, puede usar el dominio personalizado que ha comprado más de una aplicación de servicio de aplicaciones y no solo para la aplicación que ha comprado primero el dominio personalizado para. 
- Puede administrar todos los dominios personalizados que compró en la suscripción de Azure, vaya a la hoja de **dominios personalizados y SSL** de *cualquier* aplicación de servicio de aplicaciones de suscripción.
- Puede asignar cualquier aplicación de servicio de aplicación de la misma suscripción Azure a un subdominio dentro de ese dominio personalizado.
- Si decide eliminar una aplicación de servicio de la aplicación, puede elegir no eliminar el dominio personalizado que depende de si desea seguir usándolo para otras aplicaciones.

## <a name="if-you-cant-see-the-custom-domain-you-bought"></a>Si no ve el dominio personalizado compró

Si ha adquirido el dominio personalizado desde el módulo de **dominios personalizados y SSL** , pero no puede ver el dominio personalizado en **dominios administrado**, compruebe lo siguiente:

- Es posible que no haya terminado la creación de dominio personalizado. Compruebe la campana de notificación en la parte superior del portal de Azure para el progreso.
- Posible error de la creación de dominio personalizado por algún motivo. Compruebe la campana de notificación en la parte superior del portal de Azure para el progreso.
- Puede haber sido correcta del dominio personalizado, pero es posible que el módulo no actualizado todavía. Intente volver a abrir el módulo de **dominios personalizados y SSL** .
- Se ha eliminado el dominio personalizado en algún momento. Compruebe los registros de auditoría haciendo clic en **configuración de** > **Registros de auditoría** del módulo principal de la aplicación. 
- Busca en el módulo de **dominios personalizados y SSL** puede pertenecer a una aplicación que se crea en una suscripción de Azure diferente. Cambiar a otra aplicación en una suscripción diferente y comprobar su placa **dominios personalizados y SSL** .  
  En el portal, no podrá ver o administrar los dominios personalizados creados en una suscripción de Azure diferente de la aplicación. Sin embargo, si hace clic en **Administración avanzada** en el módulo de **Administrar dominio** del dominio, se le redirigirá al sitio Web del proveedor de dominio, donde podrá   [configurar manualmente el dominio personalizado como cualquier dominio personalizado externo](web-sites-custom-domain-name.md)  
   para aplicaciones creadas en una suscripción de Azure diferente. 



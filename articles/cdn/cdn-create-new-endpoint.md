<properties
     pageTitle="Usar CDN Azure | Microsoft Azure"
     description="Este tema muestra cómo habilitar la red de entrega de contenido (CDN) para Azure. El tutorial le guía a través de la creación de un nuevo perfil CDN y final."
     services="cdn"
     documentationCenter=""
     authors="camsoper"
     manager="erikre"
     editor=""/>
<tags
     ms.service="cdn"
     ms.workload="media"
     ms.tgt_pltfrm="na"
     ms.devlang="na"
     ms.topic="get-started-article"
     ms.date="07/28/2016" 
     ms.author="casoper"/>

# <a name="using-azure-cdn"></a>Uso de Azure CDN  

En este tema se recorre habilitar Azure CDN mediante la creación de un nuevo perfil CDN y final.

>[AZURE.IMPORTANT] Para obtener una introducción a cómo funciona CDN, así como una lista de características, vea la [Información general de CDN](./cdn-overview.md).

## <a name="create-a-new-cdn-profile"></a>Crear un nuevo perfil CDN

Un perfil CDN es una colección de extremos CDN.  Cada perfil contiene uno o más extremos CDN.  Puede usar varios perfiles para organizar sus extremos CDN por dominio de internet, aplicación web o cualquier otro criterio.

> [AZURE.NOTE] De forma predeterminada, una sola suscripción Azure se limita a los perfiles de ocho CDN. Cada perfil CDN se limita a los extremos de diez CDN.
>
> CDN de precios se aplican en el nivel de perfil CDN. Si desea usar una combinación de niveles de precios de Azure CDN, deberá varios perfiles CDN.

[AZURE.INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## <a name="create-a-new-cdn-endpoint"></a>Crear un nuevo extremo CDN

**Para crear un nuevo extremo CDN**

1. En el [Portal de Azure](https://portal.azure.com), vaya a su perfil CDN.  Se puede ha anclado se al panel en el paso anterior.  Si no es así, puede encontrar, haciendo clic en **Examinar**y luego **perfiles CDN**y en el perfil que se va a agregar el extremo.

    Aparece el módulo de perfil CDN.

    ![Perfil CDN][cdn-profile-settings]

2. Haga clic en el botón **Agregar extremo** .

    ![Agregar botón de punto final][cdn-new-endpoint-button]

    Aparece el módulo de **Agregar un extremo** .

    ![Agregar módulo de punto final][cdn-add-endpoint]

3. Escriba un **nombre** para este extremo CDN.  Este nombre se usará para tener acceso a los recursos almacenados en caché en el dominio `<endpointname>.azureedge.net`.

4. En la lista desplegable **tipo de origen** , seleccione el tipo de origen.  Seleccione **almacenamiento** para una cuenta de almacenamiento de Azure, el **servicio de nube** para un servicio de nube de Azure **Web App** para una aplicación Web de Azure o **origin personalizada** para cualquier otro origen del servidor web públicamente accesibles (alojado en Azure o en otro lugar).

    ![Tipo de origen CDN](./media/cdn-create-new-endpoint/cdn-origin-type.png)
        
5. En la lista desplegable de **nombre de host de origen** , seleccione o escriba el dominio de origen.  La lista desplegable mostrará una lista de todos los orígenes disponibles del tipo especificado en el paso 4.  Si selecciona *origen personalizado* como el **tipo de origen**, se escribe en el dominio de su origen personalizado.

6. En el cuadro de texto de la **ruta de acceso de origen** , escriba la ruta de acceso a los recursos que desea almacenar en caché, o deje en blanco para permitir la caché de los recursos en el dominio especificado en el paso 5.

7. En el **encabezado de host de origen**, escriba el encabezado de host que desee la CDN para enviar con cada solicitud o deje el valor predeterminado.

    > [AZURE.WARNING] Algunos tipos de orígenes, como el almacenamiento de Azure y aplicaciones Web, requieren el encabezado de host para que coincida con el dominio de origen. A menos que tenga un origen que requiere un encabezado de host diferente de su dominio, debe dejar el valor predeterminado.

8. Para **protocolo** y **puerto de origen**, especificar los protocolos y puertos utilizados para tener acceso a los recursos en el origen.  Debe seleccionar al menos un protocolo (HTTP o HTTPS).
    
    > [AZURE.NOTE] El **puerto de origen** sólo afecta a qué puerto el extremo que utiliza para recuperar información desde el origen.  El propio extremo solo estarán disponible para los clientes de extremo de HTTP predeterminado y puertos HTTPS (80 y 443), independientemente del **puerto de origen**.  
    >
    > Extremos de **Azure CDN de Akamai** no permitir el intervalo de puertos TCP completo para orígenes distintos.  Para obtener una lista de puertos de origen no permitidos, vea [Azure CDN de puertos de origen de Akamai permitidos](https://msdn.microsoft.com/library/mt757337.aspx).  
    >
    > Obtener acceso a CDN contenido con HTTPS tiene las limitaciones siguientes:
    > 
    > - Deberá usar el certificado SSL proporcionado por la CDN. No se admiten certificados de terceros.
    > - Debe usar el dominio siempre CDN (`<endpointname>.azureedge.net`) para obtener acceso al contenido HTTPS. Soporte técnico HTTPS no está disponible para nombres de dominio personalizado (CNAME) desde la CDN no admite certificados personalizados en este momento.

9. Haga clic en el botón **Agregar** para crear el extremo de nuevo.

10. Una vez creado el extremo, aparece en una lista de los extremos del perfil. La vista de lista muestra la dirección URL para tener acceso a contenido de la caché, así como el dominio de origen.

    ![Extremo CDN][cdn-endpoint-success]

    > [AZURE.IMPORTANT] El extremo no inmediatamente estará disponible para su uso, como el tiempo de registro para propagarse a través de la CDN.  Perfiles de <b>Azure CDN de Akamai</b> , propagación normalmente se completará dentro de un minuto.  Perfiles de <b>Azure CDN de Verizon</b> , propagación normalmente se completará en 90 minutos, pero en algunos casos puede tardar más.
    >    
    > Los usuarios que intenten utilizar el nombre de dominio CDN antes de la configuración de extremo haya propagado a los POP recibirán códigos de respuesta de HTTP 404.  Si ha sido varias horas ya que creó el extremo y aún se están recibiendo 404 respuestas, vea [extremos de solución de problemas de CDN devolver 404 Estados](cdn-troubleshoot-endpoint.md).


##<a name="see-also"></a>Vea también
- [Controlar el comportamiento de la caché de solicitudes de cadenas de consulta](cdn-query-string.md)
- [Cómo asignar CDN contenido a un dominio personalizado](cdn-map-content-to-custom-domain.md)
- [Cargar previamente activos en un extremo de CDN de Azure](cdn-preload-endpoint.md)
- [Purgar un extremo de Azure CDN](cdn-purge-endpoint.md)
- [Solución de problemas de los extremos CDN devolver 404 Estados](cdn-troubleshoot-endpoint.md)

[cdn-profile-settings]: ./media/cdn-create-new-endpoint/cdn-profile-settings.png
[cdn-new-endpoint-button]: ./media/cdn-create-new-endpoint/cdn-new-endpoint-button.png
[cdn-add-endpoint]: ./media/cdn-create-new-endpoint/cdn-add-endpoint.png
[cdn-endpoint-success]: ./media/cdn-create-new-endpoint/cdn-endpoint-success.png
